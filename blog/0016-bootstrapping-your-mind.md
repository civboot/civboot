# Bootstrap Your Mind

I want to start a new movement in both software development and in education.  I
want to enable motivated and curious people to be able to bootstrap their own
mind: to start from small and simple beginnings and work their way up to a
complete understanding of the tools they depend on.

The tools we (software developers) currently have don't permit us to do this. We
depend on operating systems, compilers, text editors and build systems that span
millions of lines of code. You will never fully bootstrap yourself to understand
them, they are outside the grasp of a single human. With LLMs "helping" us code,
I believe this problem will only get worse: as code is supposedly "easier to
write" we will end up with more and more of it. Why do we need all this
complexity to drive our own development and learning environment? Why does even
most popular "simple" editor (vim) require [944k lines of code][vim]?

Software cannot be truly free if you can never truly own it. Regardless of
what the LICENSE file says, it is impossible to own something you can never
hope to understand and "what I cannot create, I do not understand" (Feynman).
Yes, these monoliths of computational efficiency and power DO have value! They
allow millions of people to build and change tools that are backwards-compatible
and interoperable with eachother for decades or more. But do we really NEED a
tower of babel to just make and track edits to a text file or build a small
library on your personal computer? The tower of Babel will eventually fall, let's
not let the basic tools of software crumble with it.

For over a year I've been working on a [complete dev stack implemented in
Lua][civlua] ([why lua]). It currently totals 9,500 lines of (non-test) code and
has been going swimmingly. We'll see how much I eat my own words, but I believe
the entire dev stack should be less than 20,000 lines of code; including simple
software such as: vim/emacs-like editor with built-in lua shell, version control
system, unix-like utilities, grep-like tool and others. This software will (at
first) exist on top of a unix-like OS but my plan is to bootstrap even the
compiler and OS on the same stack in similarily little code.

Other attempts like this exist: Project Oberon and CollapseOS/DuskOS are the two
I'm most familiar with; but there are others like me (dozens!). We have a hunger
for _real ownership_ of the tools we depend on by being able to understand and
hack them with minimal interference. For me, Lua brings a pythonic taste to the
endeavor -- despite being only 15,000 lines of C Lua is an extremely
full-featured language, even supporting powerful features like coroutines.

Understanding and building your own toolbox is, in my opinion, one of the most
joyful ways to perform a craft. I hope you will join me in this approach of
combining work and play.

[vim]: https://github.com/vim/vim
[tokei]: https://github.com/XAMPPRocky/tokei
[civlua]: https://github.com/civboot/civlua
[why lua]: https://github.com/civboot/civboot/blob/main/blog/0009-lua-thoughts.md#why-lua
