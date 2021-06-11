
## References
Shell:
- http://www.linusakesson.net/programming/tty/

hashing:
- https://www.cs.hmc.edu/~geoff/classes/hmc.cs070.200101/homework10/hashfuncs.html


## Basic Design
I don't want to be beholden to the complexity of the linux system with syscalls
involving complex binary interfaces practically requiring C header files to
standardize. Heck `ioctl` is not standardized and instead you must use C.

Instead, the system is defined using _only forth_. Data is passed in memory.
Each CPU executes independently in single-threaded mode (with interrupts). All
single-threaded multitasking is cooperatively scheduled. Multiple CPUs
communicate with eachother using centralized memory. They read/write on the
data bus to this centralized memory, which also handles locking/atomics/etc.

Further, this is trivial to emulate and will be. I will write the initial emulator
in rust. The forth will use it's own 16bit assembly based on an extended J1 CPU
architecture. The interprer will provide:

- Virtualized interpretation/execution of the assembly on configurable number
  of independent CPUs, each with 128k of memory running at set speed.
- Virtualized "shared" memory extension of 2MiB, addressible via assembly
  registers.
- Memory API region for serial IO backed by a simplified "terminal emulator",
  behaving similar to UART.
- Memory API region for setting rasterized image blocks to the screen.
  Basically, an array of `(&start, &end, rows)` which points to memory
  containing a bitmap of black/white pixels.

The "tiling window manager" will then handle passing out the address of
`(&start, &end, rows)` blocks and the process can freely update the system in
this way.

## Serial IO design
I think it makes sense to walk before we can run. In particualr, while I
DON'T think we should reimplement every feature of how a terminal typically
works, I DO think we should support serial IO for updating a text-buffer.

https://viewsourcecode.org/snaptoken/kilo/02.enteringRawMode.html

## Thoughts regarding just another programming language

I went on an on/off love afair with Forth. The language is so clean, simple,
and easy to implement. I strongly believe it is a solid foundation for a
Civboot OS, at the very least bootloader. However, I remain unconvinced
that it is the final stage of any successful system.

The major problem lies in the ability to read the code. Even though I've
written greather than 30 hours of forth code, I still can't easily read code
of even minimal complexity. For instance, what does this code do?

```
: some_func     dup @ 1+ swap ! ;
```

The lack of variables makes it hard to really read. If you documented
each line perhaps it would be easier, but it's not. It might help to
know that `@` consumes an address from the stack to fetch it and `!`
consumes a value and address from the stack to store it.

This is the function `+1!` or equivalently `incAddr`. What if I wrote the
function like this? Here we assign a local variable `addr` using `=:`. This
at least makes things a _bit_ more clear, as at least we can see where the
addr is being used without having to keep a mental model of the stack.

```
: incAddr  =: addr
  addr @ 1+   addr ! ;
```

However, this is still not as clear as the equivalent C code IMO.

```
void incAddr(uint32* addr) {
  uint32 v = *addr;
  *addr = v + 1;
}
```

I remain unsure whether it is better to try to and tack on readability to a
forth-like language or instead simply abandon the stack-based approach entirely
in favor of a new language. The problem is that the difficulties for a new
language mount incredibly quickly. An approach like Forth can solve these issues
with relatively few resources.
