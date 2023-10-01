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
Traditional Lua is implemented using a register VM and its reference
implementation is only 24k LoC, absurdly small compared to many languages.
It works by compiling Lua syntax into a custom VM bytecode, which is then
executed natively (and is extremely fast).

However, I think I can do better if my only focus is on simplicity/readability
and size of implementation.

First of all, the parser and AST compiler (compiling lua syntax to lua bytecode)
could easily be written in Lua. I have almost completed the MVP of a pretty
great parsing library (pegl) and am not far from parsing Lua syntax. After
having built fngi from scratch in C I don't think generating Lua VM bytecode
would be terribly difficult.

As for the VM and runtime, I believe I could make them very lean and mean AND I
believe because of the homogenious data model (Lua has only 5 types) I could use
only slab allocators for all of Lua's data. This rest of this post will discuss
my current thinking on Lua's data structures.

### Slab Allocator
A slab allocator is the simplest possible allocator. Fundamentally it is just:

```
struct SlabT [
  &SLL             free -- singly-linked list of free T
  &[SLAB_SIZE; T]  data -- reference to array of T
]
```

When an item is allocated, it is popped from free. When it is free'd, it is
added to free (corrupting any data inside). The slab allocator therefore takes
no extra space to track its free blocks. Because all blocks are the same size,
there will never be memory fragmentation.

> In practice `data` is probably a SLL of allocated sectors, allowing the slab
> to grow. Otherwise this really is all there is to a slab allocator.


### Lua Data Structures
Lua has only 8 basic types: `nil`, `boolean`, `number`, `string`, `table`,
`function`, `userdata` and `thread`

Internally I will use 10 types:
`nil false true uint str2 number string table function thread userdata`
* nil is represented by a `NULL` pointer and cannot be used as a key in a table.
  This also means it is not included in the type enum.
* uint: an unsigned integer that fits in the hash value of the metadata (its
  value IS its "hash", which we'll discuss)
* str2: 2 slot str, max size `sizeof(S) * 2 - 2`
  * So up to len 6 on 32bit systems, len 14 on 64bit systems
* `false and true` are separate types so that conditional checks can be done by
  only masking the type field: both `nil` (all 0's) and `false` will return `0`.

Every internal Lua type must have the following metadata as its first byte:
* a bit for ALLOC (allocated vs free) for the slab allocator and GC
* a bit for MARK to implement a mark-and-sweep garbage collector
* 4 bits for the type
* the remaining bits store the value itself (for uint and boolean) or the
  value's hash

Type sizes:
* 0 slots: nil (is a NULL pointer)
* 1 slots: boolean, uint
* 2 slots: number, str2
* 9 slots: string, table, node
* other (not yet decided): function, thread, userdata

I believe that **all internal Lua Data an be represented as some version of
these slot sizes**. Even the function local variables and "return stack" can be
9S nodes (technically a SIT, see below). It won't necessarily be as performant
as possible but it will be as simple as possible!

#### Tagged Pointers
A tagged pointer is simply a pointer value which uses 2 bits that are unused by
the rest of the system. To get the pointer you simply mask or shift (depending
on the endian-ness). These bits are never used because all data structures are
slot-aligned so there are 2 or 3 wasted bits.

There are two cases where tagged pointers will be used:
* The free singly-linked-list in the 1U slab allocator (used to track free
  slabs)
* The first slot (value) of value/key pairs for tables.

Without tagged pointers it would be impossible to have a 1U slab allocator at
all and we wouldn't be able to use the 2U slab allocator for key/value pairs.

### Table representation

> Note: something very similar to the Shifted Search Tree presented here was
> discussed in [an artcle][1] I found shortly after writing it. You may prefer
> that article's descriptions and diagrams.

The table root node looks like:

```
struct table [
  S              meta    -- Lua metadata (gc, ty=table, hash)
  S              ilen    -- length of indexes
  &table         mt      -- metatable pointer
  [2; &Value]    indexed -- index-keyed root (2 slots)
  [4; &KeyValue] hashed  -- hash-keyed root  (4 slots)
]
```

Before we look at the table though, let's look at the node

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

The data strcuture is pretty cool. Basically it performs the same tasks that a
normal "array" would except:
* Does not require alloacating arbitrary blocks of memory. Can be implemented
  with a slab allocator!
* Linear growth: adding new items always requires `O(1)` time which is different
  from most list/map implementations which sometimes take `O(N)` (but which
  amatorize to `O(1)` through the trick of always doubling their allocation
  size).
* `O(log(S))` lookup: with 8 slots per node it takes log(8) to lookup a value.
  It's like a beefier version of a binary search tree!

There is very little value to a SST if you can allocate arbitrarily large blocks
of memory (like you normally can in most OS's). However, for
resource-constrained or simple systems like Civboot I think it can be extremely
useful.


#### Shifted Index Tree (SIT)

> Note: We are using 5 slots for demo purposes. The Lua SIT uses 9 slots.

Say you have a full node, each with a value:

```
[1, 2, 3, 4]  shift=0, each index has a value
```

An indexed node has the following properties:
* A size of 5 slots, causing bitsize=2 and mask = `0b11`. Therefore it requires
  2 bits to store 4 indexes.
* The first slot contains the shiftvalue (amount to shift) and a bitmap for
  which slots are children. If shiftvalue=0 then this is a leaf node (contains
  only values).
* The remaining 4 slots contain:
  * always a pointer to a Lua value or NULL (only if shift=0)
  * a pointer to NULL or a 2S space (only if child=false), which stores the
    `index` and a lua value reference.
  * a pointer to a sub-node (only if child=true), who's shift value is decreased
    by bitsize

Now we want to add index `5`. To do so we create a root and set it's `shift=2`

```
    [A, 5, -, -]      shift=2
    /
A[1, 2, 3, 4]         shift=0
```

The root node has `shift=2`. The first slot (who's value == 0 after shift+mask)
points to our original root. The second slot points to the "sparse value" 5
(index=5, value=whatever value was insert)

Now we want to add index `6`
```
             [A, B, -, -]       shift=2
              |  |
 /-----------/   |
A[1, 2, 3, 4]    B[5, 6, -, -]  shift=0
```

There was already a sparse value at 5. Therefore the subnode is allocated and
index 5 and 6 are moved into it.

Now let's say we want to add index 1,000,000 (a 20 bit value)

```
           [C, -, -, 1e6]          shift=18
            |
            C[A, B, -, -]          shift=2
 /-----------/   |
A[1, 2, 3, 4]    B[5, 6, -, -]     shift=0
```

The root takes on an extremely large shift value. However, the sub-nodes remain
sparse.

#### Shifted Hash Tree (SHT)

The shifted hash tree works in somewhat the opposite direction, which makes it
much simpler to implement:
* The root is always shift=0
* the Children bitmap still works the same. If child=true it is a sub-node. If
  child=false it is a pointer to 2U (value/key) references, where value is a
  tagged pointer.
* Collissions cause sub-nodes to be created

Say you have the following hash values:

```
    [0b1000, -, 0b1011, -]    shift=0
```

Now you add `0b1100`, this clashes with the current index=0 value. Therefore
you allocate a new child who's shift value is increased by bitsize

```
    [A, -, 0b1011, -]   shift=0
    /
A[0b1000, -, -, 0b1100] shift=2
```

There is another possible approach, which solves the issue of what happens
when two hashes are completely identical: When two keys first colide, a new
sub-table is reserved. Instead of being a shift-tree this is simply an "array"
of up to 4 key/value pairs that must be walked to find a match. This reduces the
number of 2S items we use and works even if the the keys are identical. If the
number grows to more than 4 then a shift sub-node is reserved.

Something like that. I think the implementation of this could have some
complexity for the edge-cases where hashes are identical or near-identical but
at the end of the day will be very performant for most real workflows.

### String representation
For `str2` the str stores `maxlen = sizeof(S) * 2 - 7`, so 8 bytes for 32 bit
systems and 15 bytes for 64 bit systems. This is accomplished because the
first byte has the normal memory and ty bits and the second byte has the string
length. The rest of the slot is the string data. No hash value is stored (since
hashing is fast for so little data).

For `string` the root is stored in a 2S space. This primarily has the type and
precomputed hash, with a pointer to the actual string.

The actual string is stored in a 9S space (the same slab as tables). The first
slot contains the `length` of the node (+ all its sub-nodes). The remaining 8
slots contain either the raw string data or pointers to sub-nodes. 

```
struct string [
  S                  meta (including hash of THIS node)
  S                  nodelen
  [8; Data]          slots
]
```

We now need to decide how we determine how many sub-nodes we need.  We want to
store strings as compactly as possible while still being able to quickly index
them. Ideally we would put as much data as possible into the "root" node itself
and any overflow would go into sub-nodes -- which themselves would store as much
data as possible.

Following this logic:

* A node needs zero subnodes (only data) if the data can fit entirely inside the
  8 slots. This is `8 * sizeof(S)` bytes. So 32 bytes for 32bit systems and 64
  bytes for 64bit systems.
* One subnode lets us store that `sizeof(S) * 8` in the subnode. However, we
  can now only store `sizeof(S) * (8 - 1)` in our own data (as one S is for the
  sub-node).

This is generalizable (using `N` as numSubnodes) to the following algebraic
reductions to:

```
maxlen = 8*sizeof(S)*N + sizeof(S) * (8 - N)
maxlen = 8*sizeof(S)*N + 8*sizeof(S) - sizeof(S)*N
maxlen = 7*sizeof(S)*N + 8*sizeof(S)

Solving for N
numsubs = (maxlen - 8*sizeof(S)) / (7*sizeof(S))
```

What we want is the number of subnodes we _need_ given a certain length. Using a
few test cases and a bit of equation twidling I came upon the solution:

```
subnodes = (len - (8*sizeof(S) + 1))/(7 * sizeof(S)) + 1
```

> Of course `8` is just `data_slots` and `7` is just `data_slots - 1` if you
> wanted to generalize the equation for 4 slots or 16 slots or whatever.

This works for all `subnodes<=8` (aka depth=1 strings where sub-nodes contain
only raw data). Therefore this works up to a length of `8 * 8 * sizeof(S)`
bytes: 256 bytes on 32bit system and 512 bytes on 64bit systems.

Larger strings require a modified strategy. The simplest one I can think of is
to store `len // 8` bytes in each sub-node. This makes finding the index
extremely fast but I'm slightly worried it is very non-optimal for storage
space.

An optimal solution would be something like:
* Compute how many depth1 subnodes would be needed for the string. This is
  just `len / (8 * 8 * sizeof(S))`
* If this number is less than 8 (which it is <= 2048 bytes on 32 bit systems)
  then that is how many sub-nodes are depth1 subnodes, the remaining slots are
  depth0 subnodes or raw data.
* If this number is more than 8 then do the computation again but for how many
  depth2 subnodes are needed, etc.

The problem is that I doubt the above generalizes into a neat linear equation
like our depth1 subnodes, meaning that finding an index becomes slow. Also, I
somewhat doubt that the compactness is _that_ much better than the extremely
simple solution I outlined. I'd love to actually compare the two approaches
though, as well as hear thoughts on more optimal solutions. It _might_ be
possible to use some of the `len` space to keep track of some part of the
above algorithm to make indexing faster, but I can't think of it at this time.

I'd love other's thoughts on the proper string storage algorithms.

> There is a third solution which is that string indexing isn't important, only
> string iteration (by native code like pattern matching) is important. I would
> prefer this wasn't the solution we went with.

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
pretty interatively port my Lua kernel to SPASM. From there, Civboot could be
entirely self-hosting!

[1]: https://nullprogram.com/blog/2023/09/30/
[fngi]: http://github.com/civboot/fngi
