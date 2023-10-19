# File Size and Indexes
One of the core concerns I have with making an editor for Civboot,
which should target a low amount of memory, is the memory usage of such an editor.

While Civboot will mostly stick to only small source files (so editing them will use
minimal memory), there are definitely use-cases for reading and appending large files:
* logs: the editor will have a "status" buffer with status's appended to the end.
  Logging in general is a boon but it's very difficult to keep these files small.
* history: script execution history _may_ grow in length, although this is perhaps
  a more minor concern.
* human readable data / generated files: things like the VCS will have large
  files and while a human should never be editing these, they might want to read
  them occaisionally!

To accomodate these files without balooning memory usage, Civboot will support an
append-or-read-only IndexedFile

```
ds.file.Idx = metaty.record'file.Idx'
  :field('f',        'userdata') :fdoc'the file, opened in "a+" append update mode'
  :fieldMaybe('idx', 'userdata') :fdoc'the index file, default is io.tmpfile()'
  :field('len',      'number', 0):fdoc'number of lines'
  :field('idxSize',  'number', 3):fdoc'size in bytes of each idx entry'
```

The idx file is simple: it contains a series of 3 byte values (max=16.7 million)
which reference a seek position in file `f`. You can get the position of line `l` via 
`idx.seek('set', l * 3); lpos = bigendian(idx:read(3))`. Of course this also let's
you get the range between two files, etc.

> Note: the first 3 bytes of the index file will store a two-byte magic string and `idxSize`

Any calls to `write` (or `table.insert`) will append to the file and track newlines +
update `idx` and `len` appropriately. Calls to `fidx[line]` will use the index to return the line. Calls to
`fidx:sub(l, l2)` will get the range from the index, read it, and return the lines as
a table. Basically a `file.Idx` type fill look and behave like a `gap.Gap` buffer and
a normal table. Iteration via `ipairs` will also be sped-up as a simple read-lines.

Attempting to `seek` will throw an error.

## Buffer
I've debated with myself how I will handle _editing_ large files. My basic idea went something like this:

* The `Buffer` object doesn't just contain a Gap or `file.Idx` -- instead it contains a list of such objects,
  with a `Range` wrapping any `file.Idx`.
* If a user wants to edit line 1023 in a `file.Idx` the Buffer will simply "split" the `file.Idx` into two
  `Range` objects with a `Gap` in the middle.
* The buffer methods will mediate all getting/setting/etc.

While this would certainly work, and would certainly be performant enough, there are a lot of *cough* edge cases
involved in the implementation. Eventually I may very well do exactly this. In the meantime however, I'm not
convinced of it's need -- rather `Buffer` should have a hard-cap for the number of lines allowed to edit.
Going over this will create an error: do something else. In most cases _you shouldn't have large files_. If you do
need to edit a large file (again, why?) then you could so something like:

* Don't edit large files manually.
* Somehow split the file into chunks, edit those chunks, then stitch it back together (i.e. via helper scripts)
* Build an editing Buffer soley for editing large files. Maybe it won't support all the bells and whistles.
  Ele is extensible, extend it.

**Conclusion** Ele's buffer supports either a small-file (mutable) gap buffer or a large-file append-only `file.Idx`.

## View Caching
There is one more point, which is that screen-redrawing a `file.Idx` could be extremely slow since in theory
it would require several file reads for every lookup (two to get the start/end, 1 to read all the data). I debated caching
recently-read lines directly in `file.Idx` but discarded it: doing so would add needless complexity and memory use.

Instead, `rebuf` (the bufer library Ele rests on) will provide a method and registered set of types. Then you just call
`rebuf.isFast(myTy)` and it tells you whether you should cache a view of the buffer. The cache will be stored in the
ele `Edit` buffer (where it belongs), NOT in a `file.Idx`.
