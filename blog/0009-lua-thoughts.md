# Scripting Language: How Lua can work

I've been thinking a bit about where I'm going with fngi and the Operating
system I will eventually build for Civboot in general (I'm thinking of calling
it MushROS (aka mushroom real-time operating system).

The "next stage" for the language on this path of building an operating system
is: what should the primary shell/scripting/config language?

I've come to realize that a shell language has unique requirements when compared
to other language use-cases. This is because the shell is _itself_ a utility,
making the programmer a user. Normally a language can treat the source code as
infallible, and if it fails it will apropriately stop the world and not worry
about state anymore -- this is absolutely untrue for a shell language.

Shell language requirements:

1. Must NEVER crash because of user input.

  - The user can have bad syntax, wrong types, etc -- these should cause an
    exception that gets handled at the top level and doesn't cause memory
    corruption.
  - Poorly written (native) libraries might crash or cause corruption, but
    high quality libraries won't.

2. Must be data safe. This doesn't just refer to garbage collection (although
   that is part of it), it also refers to not being able to change internals.
   This is a primary _feature_ of fngi (where the programmer has near-complete
   control of internals) that cannot be given to a script.

3. Must be able to ferry small-ish amounts of data to/from more performant
   code (such as C or fngi).

In thinking about these requirements I've started to realize why so many
scripting languages are string based (bash, tcl) and even more "complex" ones
have "completely owned types" (Python, javascript, Lua... even Java) -- fiddling
with pointers is always _inherently dangerous_ and letting any pointers "leak"
to your scripting language will always violate requirements 1 and 2.

At the same time, I've always been resistive of this:

 1. Isn't converting values to/from the script types going to make it too slow?
 2. Don't most of these languages rely on hash tables for their reasonable
    performance? This doesn't work in Civboot (which won't guarantee large blocks
    of contiguous memory).

To answer the first concern: it will be _slower_, but unless you are processing
a large amount of data it will not be "too slow".

This article exists to answer the second question.

## Why Lua

Civboot and Lua philosophies align.

Lua is a lightweight scripting language, one of the only ones that I know of
which prides itself on how many lines of code it is: 30,000 lines of C, which
is a bit heavy for Civboot, but we can freely implement only the pieces we need.

In particular, Lua's interpreter is extremely lightweight: the syntax is very
minimal and clean, and I think implementing a parser would be near-trivial. Lua
also has a lot of fans when it comes to embedded technology -- many of which
explicitly try to run on embedded targets.

Whether we re-use an existing Lua library or create our own, I think there would
be a lot of potential interest in a Lua created in fngi to run on a Civboot OS.
It sure as hell beats bash, and may be competitive with Python (and especially
javascript) for productivity. It also seems better than TCL in that it has more
structured types.

Also, I like the terse function call syntax for single argument functions, it
makes it a more reasonable scripting language.

## Lua Data Representation (in fngi)

Let's talk a bit about data representation in fngi: both for implementing Lua
and for fngi/Lua interop. The most important requirements are:

  1. Must be reasonably fast: we are going for O(1) lookups, but we will
     tolerate O(log n) for large data structures.

  2. The user must be able to call (appropriate) fngi functions

To prove we can do the fist one, let's define our types!

The types we are supporting (which are all of Lua's types) are:

  * Boolean: true or false
  * Number: 64 bit floating point number
  * String: bunch of binary data
  * Table: Map (key/value) of data. Used by Lua for every complex datatype.
  * Function: a Lua function with argument types and a return type
  * Userdata: application-specific data. Must be a Resource type which can be
    dropped and not cause fragmentation.

```
filemod Lua;

\ The base Lua type
struct Ty [
  \ linked list of all types to clear values after mark/sweep
  parent: Sll
  \ Slot sized pre-hash. Storing this radically reduces lookup time.
  \ The upper 4 bits are:
  \  1b: mark bit for mark/sweep garbage collection
  \  3b: the Lua type (Boolean, Number, etc), used as part of the hash.
  hash: S
]

struct Boolean  [ parent:Ty; value:U1 ]
struct Number   [ parent:Ty; value:F64 ]
struct String   [ parent:Ty; value:LuaString ]
struct Table    [ parent:Ty; value:&BTable; da:&SlabAllocator ]
struct Function [ parent:Ty; value:LuaFunction \Role]
struct UserData [ parent:Ty; value:UserData    \Role]
```

The first thing to note is that all Lua types are of similar size. Therefore a
Slab Allocator can be used to allocate all of them, completely eliminating
fragmentation (of the types themselves).

The defragmentation strategy for Strings is: data inside Strings are all from a
single global arena allocator. The amount of used/stale data is tracked and Lua
migrates string data to a new arena in pieces when certain thresholds are met,
eventually dropping the old arena.

Tables are a bit more tricky. There are actually 3 types of Table, all of
which are a BST of hash tables of different sizes. Regarldess, an item in a
table looks like:

```
struct BTableItem [
  key: &Ty
  value: &Ty
]

\ the base type
struct BTable [
  parent: Bst
  \ the hash of the first item inserted, used as the "value"
  \ for BST algorithm
  value: S
  meta: S  \ red/black bit, table type, node length used
]

\ Note: on 32 bit systems this is 16 slots (64 bytes)
\ and max lookups is 2.
struct BTableSmall [
  const CAP: U2 = 6
  parent: BTable
  items: Arr[CAP BTableItem]
]

fn luaTableCap[memSize: S -> U2] do (
  (memSize - tySz(BTable)) / tySz(BTableItem)
)

struct BTableBlock [
  const CAP: U2 = luaTableCap(Block.SIZE)
  parent: BTable
  items: Arr[CAP BTableItem]
]
imm#tAssert(sizeof(BTableBlock) <= Block.SIZE)

struct BTableSector [
  const CAP: U2 = luaTableCap(Sector.SIZE)
  parent: BTable
  items: Arr[CAP BTableItem]
]
imm#tAssert(sizeof(BTableSector) <= Sector.SIZE)
```

The Allocation strategy is:

 * BTableSmall contains a small number of slots per node (6 for 32 bit systems),
   This is used when a table is composed of half or fewer of the values required
   to fit in a BTableBlock (len 32 max for 32 bit systems). BTableSmall nodes
   are allocated from their own slab allocator to avoid fragmentation.
   * Since table data can be freely moved, this slab allocator can use the same
     defrag strategy as strings if fragmentation gets too large, but this is
     only required for large amounts of allocs followed by large amounts of
     de-allocs.
 * BTableBlock uses entire memory blocks (4KiB) for each node, completely
   eliminating fragmentation.
   * items per block: ~512 on 32bit systems
 * BTableSector uses entire memory sector (64KiB) for each node on systems
   that support it.
   * items per sector: ~8192 on 32bit systems

This means that even though fngi does not support arbitrary contiguous memory,
Lua table lookup time is essentially O(3) or better if you have less than 10,000
items -- and O(log(n / 8192)) for large N. As you might know, this technically
reduces to O(log n) -- but just don't use Lua for processing large amounts of
data and/or doing large amounts of lookups -- it's not well designed for that
anyway!


### Calling Fngi from Lua

Fngi is technically a statically compiled language, but unlike most such
languages it permits the programmer to inspect, modify and generate pretty much
anything inline (including types, functions, code, etc).

Therefore the "glue" needed for lots of other languages can be eliminated with
fngi. All that is required for Lua interop is:

- Fngi functions used for interop must accept only types that can be easily
  converted to Lua which are floats, ints, DllSlc (string), BTable, the
  UserData role and POD (plain-old-data aka structs) composed of those.

- Fngi will preserve the type information of both functions and structs. When
  Lua wants to call a fngi function it will simply walk the fngi type and
  extract the needed information to recursively set the fngi function.

  - If the fngi type accepts a direct Lua type, no conversion will happen.

- When the fngi function is done, the resulting types will be converted back to
  Lua (and will be handled by Lua's memory management).

- if Lua code wants to create a (typechecked) fngi type, that will also be
  supported. No "glue" code will be generated, Lua will simply be able to
  read the fngi type information to do the type checking.


## Conclusion

Lua is far from a perfect language... but then again no language is perfect. For
instance, I don't particularly like default nulls, but I also understand that it
helps make the language more performant and smaller, as anything assigned to
null is simply deleted.

I think Lua makes approximately the right tradeoffs to be used as a scripting
language and I think it would work very well to write small apps and scripts
for Civboot. It was also developed to be used by non-tech folks, and I think
it's use in the game industry (and players) reflects its success at achieving
that goal. Civboot's OS will be largely driven by the command-line and scripts,
so it is especially important that the layman be able to read and write the
language used.

I also think there are worse things than _trying_ to target something other
people are comfortable with. If Lua turns out to be a bad fit in practice,
I think lessons learned from it will be valuable anyway.

