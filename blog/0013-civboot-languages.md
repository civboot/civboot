# Civboot Programming Languages

As I've continued to play with Lua I've become less and less interested in my
own language, [fngi]. While it was a fantastic experience to implement a C-like
(but much more powerful!) language from the ground up in only 3k LoC, I don't
think fngi can be the base language for Civboot. The primary issues include:

* No matter now "low level" the langauge is, there will always need to be an
  assembler for platform-specific operations (especially in the kernel).
* I've been glazing over converting spor (the bytecode and VM with C-like memory
  model) into the target platform. The more I think about it, the more I wonder
  if this complexity is justified.
* Lua is just so damn _simple_. I feel the call to implement a Lua in as little
  C as possible.

The last point is the focus of Part 1 of this post. Once that is done Part 2
coveres a conceptual new language named SPASM which stands for "structured
portable assembly".


## Stack Lua
Traditional Lua is implemented using a register VM snd it's reference
implementation is only 24k LoC, absurdly small compared to many languages.

However, I think I can do better if my only focus is on size of implementation.

First of all, the parser and AST compiler (compiling lua syntax to lua bytecode)
could easily be written in Lua. I have almost completed the MVP of a pretty
great parsing library (pegl) in the Civboot libraries and am not far from
parsing Lua syntax. After having built fngi from scratch in C I don't think
generating Lua bytecode would be terribly difficult.

As for the VM and runtime, I believe I could make them very lean and mean AND I
believe with the homogenious data model (Lua has only 5 types) I could use
only slab allocators for all of Lua's data types. This rest of this post will
discuss my current thinking on Lua's data structures.

### Lua Data Structures
Lua has only 5 types: `nil`, `boolean`, `number`, `string` and `node`

Internally there are 8 types: `false true uint str2 number string table tnode`
* nil is represented by a `NULL` pointer and cannot be used as a key in a table
* uint: an unsigned integer that fit in the hash value of the metadata (it's
  value IS it's hash, which we'll discuss)
* str2: 2 slot str, max size of `slotsize - 1` where the first byte stores the
  length.
  * So is maxlen=3 on 32bit systems, maxlen=7 on 64bit systems
* `tnode` is part of the table representation. `table` is the root of the
  table.

Every internal Lua type must have the following metadata as it's first slot:
* a bit for GC status (to implement a mark-and-sweep garbage collector)
* 3 bits for the type
* the remaining bits store the value itself (for uint) or the value's hash

Type sizes:
* 1 slots: false, true, uint
* 2 slots: number, str2
* 9 slots: string, table, node

I believe that **all internal Lua Data an be represented as some version of
these types**.  Even the function local variables and "return stack" can be
nodes (technically a SIT, see below). It won't necessarily be as performant as
possible but it will be as simple as possible!

### Table representation

The table root node looks like:

```
struct table [
  S              meta    -- Lua metadata (gc, ty=table, hash)
  S              ilen    -- length of indexes
  &table         mt      -- metatable
  [2; &Value]    indexed -- index-keyed values (2 slots)
  [2; &KeyValue] hashed  -- hash-keyed values  (4 slots)
]
```

Before we look at the root though, let's look at the node

```
struct node [
  S              meta -- Lua metadata (gc, ty=node, hash=shift)
  [8; S]              -- slots for indexed or hashed values
]
```

The node can act as either storage for indexed values (if it's root is
`table.indexed`) or hashed key/values (if it's root is `table.hashed`).
Let's investigate each independently.

All of these act as what I'm calling a **Shifted Search Tree**. The basic
algorithm of an SST is that each "level" is shifted by a specific value
which either increases or decreases as you descend the tree and then masked
by it's power of 2 size.

The data strcuture is pretty cool. Basically it functions how a normal "array"
would except
* Does not require alloacating arbitrary blocks of memory. Can be implemented
  with a slab allocator!
* Linear growth: adding new items always requires `O(1)` time which is different
  from typicall list/map implementations that sometimes take `O(N)` (but which
  amatorize to `O(1)` through the trick of always doubling their allocation
  size).
* `O(log(S))` lookup: with 8 slots per node it takes log(8) to lookup a value.
  It's like a beefier version of a binary search tree!

There is very little value to a SST if you can allocate arbitrarily large blocks
of memory. However, for resource-constraint or simple systems with small data
sizes I think it can be extremely useful.


#### Shifted Index Tree (SIT)

> Note: We are using 5 slots for demo purposes. The Lua SIT uses 9 slots.

An indexed node has the following properties:
* A size of 5 slots, causing bitsize=2 and mask = `0b11`. Therefore it requires
  2 bits to store 4 indexes.
* The first slot contains the shiftvalue (amount to shift) and a bitmap for
  sparse type. If shiftvalue=0 then this is a leaf node (contains only values).
* The remaining 4 slots contain:
  * NULL indicating empty
  * a pointer to a Lua value (only if shift=0)
  * a pointer to a sub-node (sparse=false), who's shift value is decreased by bitsize
  * a pointer to a 2S space (sparse=true), which stores the index and a pointer to a Lua value.
    This is an optimization for more sparse indexed data.


Say you have a full node, each with a value:

```
[1, 2, 3, 4]  shift=0, each index has a value
```

 Now we want to add index `5`. To do so we create a root and set it's shift = 2

```
    [A, 5, -, -]      shift=2
    /
A[1, 2, 3, 4]         shift = 0
```

The root node has shift=2. The first slot (who's value == 0 after shift+mask)
points to our original root. The second slot points to the "sparse value" 5
(index=5, value=whatever value was insert)

Now we want to add index `6`
```
             [A, B, -, -]       shift=2
              |  |
 /-----------/   |
A[1, 2, 3, 4]    B[5, 6, -, -]  shift = 0
```

There was already a sparse value at 5. Therefore the subnode was allocated and
index 5 and 6 were moved into it.

Now let's say we want to add index 1,000,000 (a 20 bit value)

```
           [C, -, -, 1e6]          shift=18
            |
            C[A, B, -, -]          shift=2
 /-----------/   |
A[1, 2, 3, 4]    B[5, 6, -, -]     shift = 0
```

The root takes on an extremely large shift value. However, the sub-nodes remain
sparse.

#### Shifted Hash Tree (SHT)

The shifted hash tree works in somewhat the opposite direction, which makes it
much simpler:
* The root is always shift=0
* Collissions cause sub-nodes to be created

Say you have the following hash values:

```
    [0b1000, -, 0b1011, -]    shift=0
```

Now you add `0b1100`, this clashes with the current value.  Therefore you
allocate a new sub-node who's shift value is increased by bitsize

```
    [A, -, 0b1011, -]   shift = 0
    /
A[0b1000, -, -, 0b1100] shift = 2
```

The sparsity is very reliant on the hash being sufficiently distributed, but
since this is a feature of hashes we can rely on it.

### String representation
For strings larger than `str2` they must use an index tree:

```
struct string [
  S                meta (including hash of THIS node)
  U8               bitmap
  (slot remainder) nodelen
  [8; Data]        slots
]
```

The root contains the total string length of this node and sub-nodes (in bytes).
The contents of each slot are determined programatically:
* The bitmap contains whether each slot contains string data or a pointer to
  another string node.
* The data is split into 8ths, each slot containing one part
* The first slot contains the first 1/8 of the data, second 2/8, etc.

Indexing is done by:
* Divding the value by 8 (>>3) and checking the bitmap whether the slot is data
  or a sub-node
* If data then the value can be used directly
* Else the process is repeated recursively with the sub-node

## SPASM: structured portable assembly

Once that is done, I have a nagging desire to see a "better assembly": an
assembly with cross-platform support for structs (and types including function
signatures and memory locations (named local addresses and blocks).

The basic idea is to have an assembler implemented as a "script" in lua. When
you declare a function implementation, calls to your backend causes the
assembler instructions to be "written" to the current (global) instruction list
of the function. It might look something like:

```
local B = require'x86'
local AX, BX = B.AX, B.BX

-- Add ax + bx. Return (in Ax) 0 if < 10, else bx
B.fn{'fnName', regs='AX, const BX'}
    B.add(AX, BX) -- add and accumulate into AX
    B.lt(AX, 10)
    B.jfz(AX, 'next', 'end') -- jump to end of next block if flag=0
    B.blk''
        B.mv(Ax, 0)
        B.ret''
    B.done''
    B.mv(AX, BX)
    B.ret''
B.done''
```

The idea struck me because cross-platform **code** is hard, but cross-platform
**data or structure** is easy. Also, no matter what I choose I will _want_ some
kind of assembler for the backends Civboot wants to target. Therefore it behooves
me to start small and be able to write that assembly by hand.

The primary goals are:

* small and simple assembler
* representation of structs and function signatures
* C interop (including linker) of data and function calls

If I'm right and the Lua VM interpreter will be extremely small, then I could
pretty quickly spin up a Lua kernel using this VM. From there, Civboot would be
entirely self-hosting!

[fngi]: http://github.com/civboot/fngi
