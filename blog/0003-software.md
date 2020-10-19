# Civboot Software Stack
> This is the blog component to the [podcast](../podcasts/0003-software.md). It is
> not a word-for-word writeup, but rather in the same spirit.

I've been very busy lately writing a [forth][triforth] that I feel could properly
underpin the software a [Civboot](Civboot.org) needs to build. Forth the
language has become very exciting to me lately, ever since I saw
[CollapseOS](CollapseOS.org)'s forth implementation which provides a full
operating system, text editor, cross compiler, etc existing on an 8bit 16kB z80
CPU. Maybe more exciting is the [J1][J1] CPU running [swapforth][swapforth].
I'll just quote [the author][J1-blog] "The whole system is: an Open Source
Forth kernel (SwapForth), running on an Open Source CPU (the J1a), built using
an entirely Open Source FPGA toolchain (IceStorm)"

With Forth, we can write a bootstrapper in ~1000 lines of assembly, and because
of the properties of forth the _rest of the language_ can be written in Forth.
This isn't a stage0 vs stage1 compiler -- this is a language "picking itself up
by its bootstraps" and creating itself off a few lines of assembly. In
addition, forth runs most efficiently on stack based CPUs which Wikipedia lists
as one of the [simplest](https://en.wikipedia.org/wiki/Stack_machine) CPU
architectures. If we ever need a more complex language we can have it target
[WASI][WASI], which is also a stack machine so we could run it near-natively.

Now, forth has been criticised as a write-only language and I can somewhat see why.
Therefore, I've been reimagining it, and believe I've figured out how to create
[typeforth][typeforth] using basically zero runtime overhead and _very_ minimal
(like a few Kb) compile-time overhead. I feel this will improve the
readability, but more significantly the _refactorability/hackability_ of the
language. Just like a regular "threaded model" forth, typeforth is written in a
few thousand lines of assembly and bootstraps itself from there. I'll have more
updates in the future.

I mention all of this to help you understand: this stuff is not impossible. The
z80 has only 9000 transistors. Only NINE THOUSAND. Modern cpu's have
[39.5 BILLION transistors](https://en.wikipedia.org/wiki/Transistor_count). When
[episode2](../podcasts/0002-semiconductor_fabrication.md) talked about silicone
wafer fabrication and other possible technologies for producing CPUs I
mentioned that we don't have to reach the complexity of modern processes. This
demonstrates it better than most.

Lest you think we won't be able to design even our simple stack-based CPU using
such minimal hardware (<128K memory), I present to you (the creator of forth)
Chuck Moore's [GreenArray][GreenArray] company, where it is claimed he wrote
[OKAD](http://www.ultratechnology.com/okad.htm), a chip design and emulation
software, in ~12K of code.

[Civboot](https://civboot.org) probably won't exactly replicate Chuck's
approach, but the fact that it has been done is a demonstration that it is
possible. The fact that one person can build a full VLSI suite of tools on only
a few kB of code demonstrates that a Civboot can design and simulate itself on
it's own minimal hardware.

It's worth mentioning that none of this is a criticism of modern software development.
Modern software development (at least the high quality kind) is built on
_encapsulation_ of complexity and then layering it. There are many advantages
to this, one of the main ones being that you can have hundreds of thousands or
even millions of people working on tiny pieces of software towards a very large
goal. I believe that some software simply _must_ be designed in this way.

However, part of the problem with encapsulation of complexity is that each "capsule"
adds complexity in it's inputs and outputs. Every layer must be "wrapped" with
it's APIs, contracts, types, interfaces, dependencies, build system, etc.
What you end up with is a well-defined stack of _thousands_, even hundreds of
thousands, of moving parts. No group of people can understand the operation of
the whole stack. For this reason, modern software methodology does not work for
a Civboot -- where the primary goal is for a single person to be able to
understand at least one component of the whole within ~4years ("component"
being for example software, hardware, material fabrication, etc).

This is why my current thinking on a Civboot's software stack is to support only
two languages: Assembly and TypeForth. However, because forth can modify itself
at runtime (it's one of the pearls of the language), it is nearly trivial to
write domain-specific languages. Need a hermetic configuration language? Simply
create a simple garbage collector and define some immutable datatypes. Then
spin up a new interpreter with an allowlist of which words are okay to be
called (writing to memory addresses won't be one!).  Maybe there will be a few
leaks -- but overall you have basically created a forth-style [bazel][bazel]
using forth instead of python as your config language.  When you've got your
config, simply reset the dictionary to before you imported your config
forth-bazel and drop it's memory arena. Wallah! You have used zero memory and
can continue building.

This allows Civilization's technology stack to be learnable within a short
amount of time by a small number of people -- that is the purpose of a Civboot.
However, it is not saying that encapsulating complexity or building large
software projects is somehow bad. Go ahead, try new complex things. Build
different layers to get there. Work together -- that is what civilization's
tools can offer you. We achieve simplicity by starting out with a compliated
solution and simplifying it. In fact, that's exactly what Civboot is trying to
do to our current technology stack.


[triforth]: https://github.com/civboot/triforth
[typeforth]: https://typeforth.org
[J1]: https://github.com/jamesbowman/j1
[swapforth]: https://github.com/jamesbowman/swapforth
[J1-blog]: https://www.excamera.com/sphinx/article-j1a-swapforth.html
[WASI]: https://hacks.mozilla.org/2019/03/standardizing-wasi-a-webassembly-system-interface/
[GreenArray]: http://www.greenarraychips.com/
[bazel]: https://bazel.build/

