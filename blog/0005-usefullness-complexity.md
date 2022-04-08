# The Usefulness of Complexity
> Author: Rett Berg (vitiral)

One of the core goals of Civboot is to reduce the complexity of technology
enough that a small group of people (say 50-500) can learn and rebuild a new
Civboot from scratch within ~8 years (high school through college).

Complexity is therefore a problem I've been thinking about a lot. I've just
finished creating a working programming language called [fngi]. It is still in
the early stages, but I've started being able to write a primitive memory
allocator in it. The language is designed to be simple but understandable. In
about two thousand lines of C it bootstraps a virtual machine and assembly
language (called spor), which then bootstraps fngi in less than two thousand
lines, including documentation.

For programmers, there are many concepts thrown around regarding complexity.
These include ideas like:
* Lines of code. Bloat of software is often expressed this way, such as "did you
  know the linux kernel is 20 million lines of code?". Dan Simon wrote an
  excellent [paper][OS Complexity] on this subject.
* Memory model and language internals. How much does your programming language
  "hide" from you? How "close to the metal" are you? Do you depend on a GC and
  reflection or are you wiggling bits in assembly?
* Language design. How "safe" is your language? What about when you use
  multi-threading? How much undefined behavior is there? I've come across [QBE],
  an optimizing backed for C written in ~15,000 lines. It's bibliography
  referenced an entertaining paper on the unnecessary [complexity of
  optimizations][optimizations].

Type systems, optimizations, safety checks, linters, sanitizers, tests, APIs,
documentation, serialization, macros, build systems, libraries. To an
experienced programmer there is a cloud of tools, concepts and processes that
their entire profession is based upon.

I've been enjoying reading writings from [the convivial society][convivial]
lately, although the writing is often too dense for me to fully follow. One of
the core points that speaks to me is the references to Ivan Illich's idea that
modern technology is really a pile of tools to build a new pile of tools. For a
concrete example: we make a complex set of lathes, CNC machines, precision
lasers and metallurgy to create slightly more exact machining tools -- which
themselves are used to make more exact tools. Not only that, but only specific
experts know how to use and design each new layer of tools. We create minimal
viable products (MVP) in house using custom and off-the-shelf components, then
order custom parts from all over the world, created using tools and processes we
ourselves are blind to. When this supply chain is disrupted, we are left in the
dark with nobody to give us a lamp.

As someone embedded in technology, this feeling can be very visceral; like a mud
you are trudging though. You feel as though all you can ever do is live on the
surface, like skimming a junk heap for the valuable pieces poking through, but
you're never allowed to dig deeper for fear of collapsing the whole pile. I
think it is important to reframe these feelings to avoid life and work becoming
a drag. I've summarized my reframing as three points:

  1. Complexity always serves a purpose
  2. Growth of complexity is a natural part of all processes.
  3. You have no control over any of this.

Reframing 1: Complexity always serves a purpose, even if it's not a purpose you
think is valuable. Organic life became fantastically large and complex during
the prehistoric era, only to radically die off and simplify after calamity. This
complexity was neither good or bad, it simply was. Technology is undergoing a
radical expansion of complexity; it's primary purpose being to allow millions of
engineers to work together on similar problems. Eventually this edifice will
become too complex for it's own weight and will collapse; unless technology can
continuously create stronger and stronger clutches to shore itself up. The
current hope is that AI can write and test our own increasingly messy software.
We will just have to see how that goes.

Reframing 2: Growth of complexity is a natural part of all processes. Life
(including your life and cycles within your life) is a process which starts
small and simple, grows and evolves in complexity, then dies and is reborn for
the next generation of complexity.  This describes the cycles of the season,
with plants and animals growing to fantastic interconnected webs, then
withering, dying and sleeping. This describes evolution and the inevitable
creation and extinction of species. This equally describes the growth and death
of complexity in technology. At the small scale, software developers constantly
see new libraries and languages arrive, become popular, then die. We haven't
experienced a large-scale die-off but it will inevitably happen; just as all
processes inevitably end and are reborn.

Reframing 3: you have no control over any of this, so don't try. Fighting
the growth of complexity directly is to fight against life. Nature doesn't
fight complexity, it finds niches where simplicity can start anew. Also,
the Stoic principle of letting go of that which you have no control will
allow you to rest and put your focus on what actually matters.

I hope these can help you when you feel that life and work has become a drag,
weighed down by complexity.

[fngi]: http://github.com/civboot/fngi
[OS Complexity]: https://pspodcasting.net/dan/blog/2020/complexity.pdf
[QBE]: https://c9x.me/compile/
[optimizations]: https://c9x.me/compile/bib/ubc.pdf
[convivial]: https://theconvivialsociety.substack.com/

