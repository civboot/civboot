# Civboot Software Stack

Watch on [youtube][youtube] or listen on
[anchor][anchor] or wherever you get your podcasts.

[anchor]: TODO
[youtube]: TODO
> This is the podcast component to the [blog](../blog/0003-software.md). It is
> not respoken word-for-word writeup, but rather in the same spirit.

Outline:
- Been awhile, been busy working on my own forth. More updates in the future.
- Instead of discussing making a CPU, talk a bit about software.
- What is forth? Why is it important for Civboot?
  - CollapseOS
  - GreenArrays
- Stack based CPUs: only ~9000 transistors
  - Other hardware elements discussed in future episode.
- Software complexity compared to Civboot

References:
- https://fuel.edby.coffee/posts/how-we-ported-xv6-os-to-a-home-built-cpu-with-a-home-built-c-compiler/
  : author built own computer chip (on an FPGA), C compiler and operating
  system for a college course. This stuff is possible!
- https://github.com/jamesbowman/j1 : 200 lines of verlog stack-based forth CPU 
  - https://github.com/jamesbowman/swapforth : ANS forth that runs on 8k of memory.
- https://github.com/cstack/db_tutorial : tutorial re-implementing SQLite
- https://capnproto.org/news/2014-06-17-capnproto-flatbuffers-sbe.html
- https://github.com/cesarblum/sectorforth : forth fitting in 512 bytes
- https://www.forth.com/starting-forth/0-starting-forth/ : forth tutorial
- https://www.complang.tuwien.ac.at/forth/gforth/Docs-html/Introduction.html#Introduction : gforth tutorial
- http://www.complang.tuwien.ac.at/schani/oldstuff/#schemeinforth - scheme interpretrs in many languages, including forth
- http://yosefk.com/blog/my-history-with-forth-stack-machines.html - interesting blog post
- https://el-tramo.be/blog/waforth/ - dynamic forth compiler written in WASM.
  Very intersting to see how wasm can do dynamic execution.
- https://beepb00p.xyz/configs-suck.html : don't use a config language... use a real programming language!
  Personally I feel like it would be _trivial_ to "branch" a forth interpreter that is more limited... only
  has immutable datatypes, etc and so is a _great_ config language.
- https://github.com/carp-lang/Carp - Carp programming language
- https://prog21.dadgum.com/30.html - 2 papers to read about writing a compiler
- https://archiveprogram.github.com/ - open source archive program
- http://www.greenarraychips.com/ - creator of forth's chip factory
- https://kestrelcomputer.github.io/kestrel/ - personal computer project OSS
