> This is **extremely** preliminary.

Based on my current readings, the RISC-V chip design is not well suited for the
civboot software stack. My research with the Forth language and some of the
philosophy of its creator Chuck Moore has opened up some of my assumptions
about what the requirements actually are for a project of this kind.

A Civboot aims to be as simple as humanly possible at _every layer_ of the
stack. Forth and wasm achieve this to a large extent while ALSO being extremely
portable (okay, it's the wasm that's portable not the forth), meaning software
written for a future Civboot can be used _right now_ on your current operating
system, providing it aheres to the WASI standard.

However, in researching current computer chips I came across an article **TODO
link needed** that details the differences between RISC based chips and
x86/TODO-complex-design based chips. In it, it mentioned that RISC based
chips have a reduced instruction set which executes in a single clock cycle
(good! reduced complexity) but that this bloats binary size if the compiler
isn't sufficiently optimized. Another blog post **TODO link** discussing
porting machine forth to RISC gives a more direct comparison of instruction
requirements. The basic problem is that RISC architectures do explicit
load/store instructions whereas TODO-complex-design architectures bundle
instructions together.

One of the things Chuck Moore has focused on is not just compilers, software
and applications but rather designing _hardware_ to meet the requirements
presented. Thinking in these terms, it occurs to me that we could have our
cake and eat it too -- if only the "reduced instructions" mapped very well
to the software these chips will be running -- that software being forth
and/or wasm virtual stack machines.

I know very little assembly, and know even less about chip design. I know
that to dabble here I should probably learn Verilog or VHDL (?is that right?)
and buy an FPGA. However, hypothetically such a chip could have an architecture
something like this:

All instructions would be stack-based. The stack is required to "grow down."
When the processor starts, the stack would be at a hardware defined place
in memory (i.e. 0x100), from which place it should be quickly moved with
some of the following instructions which push/pop some number of values
from the stack in order to function:

- `push cell       ( -- u ) \ push a value onto the stack`
- `setDataStack    ( bottom top -- ) \ sets the stack`
- `setReturnStack  ( bottom top -- ) \ set the return stack`
- `call            ( addr -- ) \ runs code at addr`
- `exit            ( -- ) \ return to where called from`

These would allow the bootloader to quickly reconfigure what memory should be
used for the data and return stacks.

Further operations would directly utilize the stack. Notice how at no point are
registers _ever_ required, drastically improving the simplicity and aiding in
the mental model:

- `swap   ( u1 u0 -- u0 u1 )`
- `dSwap  ( d1 d0 -- d0 d1 )`
- `ds&    ( -- addr )       \ get addr of top item on datastack`
- `ds&0   ( u0 -- u0 u0 )   \ duplicate datastack @ index 0. forth:DUP`
- `ds&1   ( u0 -- u0 u0 )   \ duplicate datastack @ index 1. forth:OVER`
- `ds&i   ( ... u1 u0 i -- ... u1 u0 u&i )  \ duplicate datastack @ index i
- `rotl   ( u2 u1 u0 -- u1 u0 u2 ) \ rotate left. forth:ROT`
- `rotr   ( u2 u1 u0 -- u0 u2 u1 ) \ rotate right. forth:-ROT`
- `add    ( u u -- u )`   \ sum
- `mul    ( u u -- u )`   \ product
- `multd  ( u u -- d )    \ multiply to double`
- `div    ( u1 u0 -- u )  \ divide u1 / u0`
- `mod    ( u1 u0 -- u )  \ modulo u1 % u0`
- `load   ( addr -- u )   \ load directly from memory
- `store  ( u addr -- )   \ store directly into memory
- `dupAdd ( u1 u0 -- u1 u0 sum` \ sum while preserving values
- ... etc

A memory manager could be built-in with indexible "memory regions" and security
could be added by a "secureCall" which only allows the called methods and its
children to:
- access specific areas of memory
- exit back to the root caller
- call a _single specific_ address

Possibly something like this:
- `memNew  ( mid blocks -- flag ) \ creates a memory region`
- `memDrop ( mid -- flag )        \ destroy a memory region`
- `memGrow ( mid blocks -- flag   \ grow memory region`
- `memShrink ( mid blocks -- flag \ shrink memory region`
- `block   ( block mid -- addr )  \ physical address to a block`
- `secureCall    ( addr pid mid callback -- &stackTop )`
  - `{callback call}  ( pid mid &stackTop -- )`

`memNew` creates a new memory region, which is managed by the Memory MxxxTODO
Unit (MMU). The MMU provides a byte-addressable contiguous block of memory to
any CPU using memLoad/memStore with the appropriate mid (memory id) set in
their register.

When secureCall is called it's mid and pid registers gets set, which causes any
attempt to use load/store to return memory from within its mid instead of
on relative to absolute memory. The mid/pid register itself cannot be set
outisde of secureCall, preventing the process from accessing memory outside of
its mid.

When secureCall returns (or the callBack is called) the mid is cleared, allowing
the root process to acccess arbitrary areas of memory, which it can map
to the user process via calls to `block`. It is the root process's job to
maintain security while still allowing for access of allowed resources. The
pid can be used to help a program determine what access restirctions should be
granted/denied. The root process "returns" values by directly modifying the
stack of the user process.

Atomic operations (I know very very little about how a CPU normally manages
atomic operations except that it is extremely complicated) are done via a single
instruction
- `astore  ( u:value u:expected -- flag )`

This tells the MMU to do the store operation _only_ if the current value is the
same as `expected`. The `flag` specifies whether the operation was successful
or not.

The CPU has very few registers:
- pid: the current "process id". Can be used by root software to track process
  access levels.
- mid: the current memory id, see above.
- dataStackBot: the bottom of the data stack. Attempts to push memory lower
  will cause a stackOverflow.
- dataStackTop: the top of the data stack, which contains the count of items
  on the stack.
- returnStackBot: the bottom of the return stack. Attempts to add items will
  cause callDepthExceeded.
- returnSTackTop: the top of the return stack which is also the count of items
  on the stack.
- err: an error code associated when a trap is encountered.
