# Civboot OS Thoughts

Unix is based on sockets and pipes. CivbootOS is based on arenas and bvalues.

The exchange between programs consists of both:
- A memory arena, which is used to allocate/dealocate 4k blocks of memory. The
  entire arena can also be dropped.
- A bvalue which is either raw bytes or a list of bvalues.

Processes are run and start with the following global values:
- arenaProc: the arena owned by the process.
- bvalueProc: the bvalue for the process, called "configuration". This is owned
  by arenaProc (NOT arenaIO).
- arenaIO: input/output arena. This changes after each call to cont or exit.
- bvalueIO: the input/ouput bvalue, or "root" value. This is owned by arenaIO
  and may change after each call to `cont()` or `exit()`

> Note that if bvalueProc or bvalueIO is null then their corresponding arena
> will also be null. The process can request an arena from the system if it
> needs one.

Processes may call one of the following methods to allow the next process in a
stream to handle data:
- `cont(rc)`: this will "continue" execution, passing the arenaIO and valueIO to
  the next item. The arenaProc and bvalueProc, along with the locals and call
  stacks, will be preserved until the parent in the stream is finished. Once the
  parent is finished, `cont()` is equivalent to `exit(0)`.
- `exit(rc)`: like `cont()` but will also tear down the arenaProc and
  bvalueProc, as well as the locals stack.  Note that the process may be
  restarted if an upstream item is passing more bvalues.

The `rc` in both cont and exit are to notify the scheduler of an error. The
scheduler may choose the proper behavior in this situation (the default is to
stop the stream's execution).

Through the above meachanism, processes can be structured as streams where a
single arena/bvalue pair undergo various data processing steps to perform some
action or do some calculation. This is similar but quite distinct from the unix
protocol of using binary streams and pipes, which will always require lots of
copying of data and re-interpretation.

## co: the container language and format of civboot
This is represented in the primary text form of Civboot, (unnamed)

Core ideas:
- Only two data types: blist (block list) and bdata (block data, aka bytes).
- By default, everything is bdata with the exception of the following special
  characters.
- The start of an item (including the start of the document) can be one of:
  - `$`: execute a token as an arbitrary command. These are defined by co or
    defined by the user or an import.  Every possible command will create
    garbage-collected values and cannot mutate any state (it is pure).
  - `/`: start a line-comment.
    i.e. `{nested; list;} other; items;`
- `$[ ` is used for raw blocks. If a space follows it it will be ignored. The
  more `[` that are added the more `]` must be used to escape. This is used for
  code blocks where semi-colons can be left un-escaped.
- `\` is used to escape the next character or create special characters like
  `\n` for newline, `\;` for a semicolon.

The special characters are:
- `{`: start a nested-list. This is only valid at the start of an item.
- `}`: end a nested-list, which also ends the current item.
- `;` ends a bvalue item. The only time it is not special is if a `$` command is
  ignoring it.

A typical configuration file might look like:

```
{name; Rett Berg}
{age; 33}
{occupation; Software Developer}
{
  favorite programming languages; {
    // Note to self: remove java since nobody likes java
    rust; python; java; C;
    fngi;
  }
}
```

In json, the above would translate to:
```
[
  ["name", "Rett Berg"],
  ["age", "33"],
  ["occupation", "Software Developer"],
  ["favorite programming languages",
    "rust", "python", "fngi", "java", "C"]
]
```

The goal of co is not to replace json though. It is a fully-featured
configuration language since it can run arbitrary built-in or user-defined
commands. In addition to being Civboots core config language, it also serves at
it's markdown.  Several commands are included which expand to items understood
by the `vdoc` documentation program.

```
`$* this is italicized;

which expands to;

{{type; italic} this is italicized}

;# This is a header. More examples below;
This is some text with a random semicolon ;
{this; is an embedded; list of; items}

The document creator will simply join each of these elements (applying
formatting to some with a "type"). It does not add newlines except for when
there are two or more newlines separating text (like markdown). So you could
write FOO as:

;F;O;O

If you wanted to italicize the middle O you could do:

F;*O;O

That is extremely unusual obviously. You would more typically write
something like:

I like to use ;*italicized ;words, ;**bold ;words. Sometimes even
;_underlined;\ words.

The above can look clunky, so you can enter "markup" mode to do a little
cleaner:

;up I like to use *italicized* words, **bold** words. Sometimes even
_underlined_ words.

The previous does what might be expected. We now have to exit markup.
;\
Note: The `{` or `}` must be at the beginning of the item, otherwise it is just
part of the item. This entire note is itself still a single item.;

   Arbitrary leading whitespace (like before this) is ignored.;

If you need to include whitespace, you can do so with \ , i.e;
\     this whitespace was not ignored.
;

Code blocks are started with "code" and remain code via indentation, i.e.:
;code <optional language>
  this is code;
  as long as I'm indented, semicolons are ignored.
  It's really code.

This is no longer code but rather a new item of regular text.
