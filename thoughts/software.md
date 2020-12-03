
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
