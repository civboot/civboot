# Civboot OS Thoughts

Unix is based on sockets and pipes. CivbootOS is based on arenas and bvalues.

The exchange between programs consists of both:
- A memory arena, which is used to allocate/dealocate 4k blocks of memory. The
  entire arena can also be dropped and replaced.
- A bvalue which is either raw bytes or a list of bvalues.

Processes are run and start with the following global values:
- arenaProc: the arena owned by the process.
- bvalueProc: the bvalue for the process, called "configuration". This is owned
  by arenaProc (NOT arenaIO).
- arenaIO: input/output arena. This changes after each call to cont or exit.
- bvalueIO: the input/ouput bvalue, or "root" value. This is owned by arenaIO
  and may change after each call to `cont(rc)` or `exit(rc)`

> Note that if bvalueProc or bvalueIO is null then their corresponding arena
> will also be null. The process can request an arena from the system if it
> needs one, or it can use arenaIO if it only uses `exit` anyway.

Processes may call one of the following methods to allow the next process in a
stream to handle data:
- `cont(rc)`: this will "continue" execution, giving ownership of arenaIO and
  valueIO to the next process. The arenaProc and bvalueProc, along with the
  locals and call stacks, will be preserved until the parent in the stream is
  finished. Once the parent is finished, `cont(0)` is equivalent to `exit(0)`.
- `exit(rc)`: like `cont(rc)` but will also tear down the arenaProc and
  bvalueProc, as well as the locals stack.  Note that the process may be
  re-created if an upstream item is passing more bvalues.

The `rc` in both cont and exit are to notify the scheduler of an error. The
scheduler may choose the proper behavior in this situation (the default is to
stop the stream's execution).

Through the above meachanisms, processes can be structured as streams where a
single arena/bvalue pair undergo various data processing steps to perform some
action or do some calculation. This is similar but quite distinct from the unix
protocol of using binary streams and pipes, which will always require lots of
copying of data, re-interpretation, complexity around buffers, non-conformity
of real hardware or processes with binary streams and other concerns.

## Civboot Permissions Bits
Civboot has a few "permissions" bits which are set on programs which allow
restricting what an executable process can do. These are implemented directly in
the sporeASM bytecode interpreter at the compiler level. Essentially spore (or
another language) can do any setup it deems fit and then switch into a
"restricted" mode where it permits only certain filesystem actions, memory
actions, etc.

By default files have none of these permissions.

- exec-base: can execute "base" processes, i.e. those defined by the co
  language. Even a fully-deterministic pure-data file can have this, but
  disallowing it prevents even CPU usage beyond reading a file's bytes.
- exec-read: can execute any "read" processes the user is allowed to execute.
  These always have reduced restrictions to not be allowed to update the system.
- exec-write: can execute any processes the user is allowed to execute.
  These may modify the system.
- fs-read: permits reading the filesystem but not writing. Also uses read-prefix
  list to restrict what can be read.
- fs-write: permits writing to the filesystem but not reading. Also uses
  write-prefix to restrict what can be written.
- mem-read: permits any reading of memory in user-defined programs. Also uses
  mem-read-regions to restrict what memory can be read.
- mem-write: permits any writing of memory in user-defined programs. Also uses
  mem-write-regions to restirct what memory can be written.

## co: the container language and format of civboot
co is the primary text format of Civboot, and compiles transparently to a single
bvalue, although it can emulate multiple bvalues as well. A typical
configuration file might look like:

example.co
```
{name; $getEnv USER}
{age; 33}
{occupation; Software Developer}
{favorite programming languages;
  rust; python;
  java; (TODO: should java really be here?)
  (TODO: learn C#)
  C; C#; fngi;
}
```

Core ideas:
- Only two core data types: blist (block list) and bdata (block data, aka
  binary bytes). bvalue or alternatively "item" means one of these two data
  types.
- By default, everything is bdata (bytes) with the exception of the following
  special characters:
  - `;` ends an item and starts a new one.
  - `{ }`: start/end a nested-list. This also works as a `;`
    - i.e. `{nested; list} other; items;` in json `[["nested", "list"], "other",
      "items"]`
  - `\` is used to escape the next character or create special characters like
    `\{` for inline blist, `\n` for newline, `\;` for semicolon, `\ ` for
    space, etc.
- The start of an item (including the start of the document) can be one of:
  - `$`: execute a token as an arbitrary command. These are defined by co or
    defined by the user or an import.  Every possible command will create
    garbage-collected values and cannot mutate any state (it is pure).
  - `(`: start a comment. Comment is ended (and next item started) with `)`


Note a few things in the above:
- Functions can be called with `$`, i.e. `$getenv user`. This only works as the
  first non-whitespace character in an item.
- Comments (`#`) can be immediately at the start of any item but do not require
  special syntax.  You can write `C#` since the `#` doesn't appear at the
  beginning of the item. (if it did, you could write `\#something`).
- co does not have "maps" because spore does not have maps. Lists are fine for
  key/value stores in small configuration files. The application is free to use
  a hashmap or BST if desired for large key/value configurations.
- On the same point, the standard practice for a key with a list value is to
  just continue the list, i.e. `{key; value1; value2; value3; ...}` This makes
  refactoring easier down the road.

In json, the above might translate to:
```
[
  ["name", "Rett Berg"],
  ["age", "33"],
  ["occupation", "Software Developer"],
  [ "favorite programming languages",
    "rust", "python",
    "java", "C", "C#", "fngi"
  ]
]
```

The goal of co is not to replace json though. It is a fully-featured
configuration language since it can run arbitrary built-in or user-defined
functions. It has comments and compiles directly to the default serialized
data format, bvalue (`.bv`).

Not only that, but it can be extended through nearly standard XXX-lang (some
future language yet to be developed for Civboot). This makes it a deterministic
and fully featured configuration language.

## Co language
Co is a full-featured scripting language, meaning it calls user-defined programs
which communicate via bvalue. 


In the future there may be a fully-featured user-defineable "co language" that
is nearly turing complete and has first-class functions/etc.

In the future.

For the present, co will ship with a small number of native functions, things
like `join(list, sep)` for joining a list of strings with a separator,
`split(bytes, regex)` for splitting bytes into a list using a regex. There will
also be a query language similar to XPath for extracting values from keys, the
ability to define global variables to use throughout the configuration, and
the ability to import other co files.

## A few notes
- Multiple "ends" will not add an empty item, i.e. `{foo};;;bar` is the same as
  `{foo} bar`. The only way to insert an empty item is with the "empty" command `$empty;`,
  i.e. `{foo}; $empty; $empty; $empty; bar` would translate to json `[["foo"], "", "", "",
  "bar"]`. You might use this for empty configurations, i.e. `{security; $empty}`


