# Civboot: a civilizational bootstrapper

_What I cannot create, I do not understand_ - on the blackboard of Richard P.
Feynman at the time of his death.

_Everything should be made as simple as possible, but no simpler._ -- Albert
Einstein

This is the repository for Civboot: a Civilizational Bootstrapper. It's purpose
is to act as a version-controlled wiki for all efforts related to Civboot.

This effort was started on 2019-11-14 and is still very early stage. This
github repo should be used for discussion (through [opening an issue][issue]).

I have also started a podcast. View it on [youtube][CB youtube] or listen on
[anchor][CB anchor] or wherever you get your podcasts.

[issue]: https://github.com/civboot/civboot/issues
[CB youtube]: https://www.youtube.com/channel/UCCYYcyLG3MAZahnoKLx3Aaw?view_as=subscriber
[CB anchor]: https://anchor.fm/civboot

## Introduction

How large would a warehouse have to be in order to both:

1. Build simple computers in a semi-autonomous way from scratch using only
   tools and knowledge within the warehouse.
2. Build another such warehouse and make improvements to it.

If such a warehouse existed, it would be a Civboot. Civboot aims to simplify
technology by simplifying technology's requirements. Fundamentally Civboot is
an educational tool: it can provide a working understanding of the tools and
processes that underpin our technology. Putting the question another way, if
there were a high-school & college curriculum with the sole goal of
accomploshing "project based learning" of having the entire class (400+
students) understand and reconstruct a Civboot using only the tools within the
Civboot; how many people would that be, how large would the warehouse be and
how many instructors would be required?

This is the fundamental question of Civboot, a project which aims to reduce the
tools and knowledge necessary to bootstrap modern civilization as much as
possible. It is important to point out early that a Civboot does _not_ contain
everything required to build or support Civilization such as food, clothing or
a bed; in the same way as a bootstrapper for an operating system does not
contain all the bells and whistles of the operating system itself. A Civboot
only contains the tools and knowledge necessary to build computers and itself.

In terms of supporting a community with things like food, clothing and shelter,
a Civboot has one additional goal: be able to provide these things within three
degrees. For instance, a Civboot cannot construct a tractor directly, but you
can use tooling within a Civboot to build a factory (the 1st degree), then you
can use the factory to build gears and other tractor components and assemble a
tractor (the 2nd degree).

Another point: a Civboot only contains tutorials to provide a _working
knowledge_ of how to reconstruct and improve the Civboot. Deeper understanding
of things like transistors, hardware architecture, software architecture,
chemistry, mechanical engineering, etc can take potentially a lifetime of
learning. It is hoped that Civboot can be a foundation for that learning, but
Civboot itself aims to provide only a working understanding.

This project is NOT an attempt to "do this at home." Creating the first Civboot
will no doubt require millions of dollars and the efforts of thousands of
individuals to design, experiment, document and simplify a huge range of
technologies which cross an extremely broad range of disciplines. The first
Civboot will likely be made "distributed" (if it is ever made at all) -- with
teams from all over the world sending their products to each other,
collaborating on multiple different approaches. The hope is to get hackers,
professionals, retired experts, universities and organizational support to help
distill civilization's knowledge into a stack that can be understood and self
replicated.

Even if we fail, it will be worth the effort. Having a simplified
working-understanding of technology has many advantages other than the
construction of a Civboot.

## Philosphy of Civboot

Civboot is not a criticism of the current complexity of technology. Technology
is (largely) as complex as we want it to be for the requirements we have:
hiding internal complexity, simplified and beautiful user interfaces, and
scalability of more engineers working on large-scale problems.

Civboot is founded on a philosophical idea: modern civilization would be better
off if it owned the means to produce it's essential components. This is not
to say that proprietary pieces of the tech stack are somehow _wrong_ (although
some believe that). Civboot is not an economic philosophy, it is a scientific
one. Humanity is better off when it can understand the technology it depends on,
and we can only understand what we can create.

You are probably thinking the _real_ goal of Civboot is to be able to survive
an apocalypse-type scenario. This was indeed one of the inspirations for Civboot,
and the project [CollapseOS](CollapseOS.org) was a motivation for the current
technology stack that Civboot is pursuing. I believe that thinking along
"negative" outcomes such as "what happens if civilization collapses" is highly
productive to creativity, growth and learning in much the same way as death and
rot are essential for the growth of plants and sustenence of all life. Having a
noble goal of being educational and a motivational goal of concern for the
future provides a good balance from which I hope Civboot and its community can
thrive.

Civboot is and must remain completely free and open. In order to fulfill its
mission, it must be possible to have a working-understanding of every level of
its design -- from materials science, to manufacturing process, to mechanics,
to the computer hardware and software it is running. Included information and
processes should be as simple as possible -- these concepts should be easy to
learn and replicate. This constraint will force simplification of the designs,
ideas and software being included. We must take our complicated technology
stack and work on distilling it to its essentials, then rebuild upon those
essentials to be even more efficient and simple.

## Goals
The first goal of Civboot is to be able to create a technology stack which can
self-replicate with 500 motivated students in 8 years, starting with a
US-equivalent 8th grade education. In addition, the goal is to be able to
rebuild a Civboot (with a Civboot) for less than 25 million dollars --
or $50,000 per student, less than the typical cost of 8 years of education in
the United States.

In addition, there are (aspirationally) 5 Civboot levels. These are not part
of the core spec, but may help get creative juices flowing. They are documented
[here](thoughts/levels.md)

## Constraints

The intent of these constraints is to limit the amount of tools and information
included in a Civboot into a manageable amount. These constraints (whether
included or excluded) make no judgement about the importance of said items
except as to apply to the goal of creating the first Civboot.

A Civboot...

- **Must** be entirely free and open.
  - **Must not** contain proprietary or hidden designs, i.e. no "binary blobs"
- **Must** be possible to build itself from itself
  - Requires no outside knowledge besides the language which the guides are
    written in
  - Requires no outside tooling besides commonly found materials (rocks, clay,
    wood, dirt, sand, ore, etc)
- **Must** include all working knowledge necessary to understand every
  component and their interactions.
  - **Must** include the theory and practice of math, physics, chemistry,
    materials science, computer design and programming, etc necessary to
    explain the design of every component in the system such that it can be
    replicated and have improvements made upon it.
  - **Must not** include information not necessary to understand or begin
    improvements upon the system. Aka must not include politics, history,
    medicine, philosophy, religion, art, psychology, sociology or any branch of
    human knowledge or endeavor outside of the sciences and engineering
    necessary to understand and improve the system. These are important to
    preserve, but it is not the goal of Civboot to preserve them.
    - Civboot **should** aid in providing the technology to store and view such
      knowledge where possible.
  - **May** include some amount of history of
    math/science/engineering/philosophy which is required to properly
    understand a component.
- **Should** be as simple as possible but no simpler.
  - Comfort has almost no importance, unless it hinders use excessively
  - Easy as possible to re-learn and re-create from near-scratch
- **Should** be as efficient as possible
  - Require as few materials / space / energy necessary to achieve its purpose.
    Improvements to these areas without increasing complexity should be
    accepted.
- **Should** focus on the "single city" design
  - Local communication protocols acceptable
  - Globalized transportation / communication not required (but allowed)
- **Should** be composed of modifiable and swappable designs
  - Stack will continue to improve in both simplicity and effectiveness -- no
    stage in stack should block this improvement
  - Stack should be upgradeable as components become simpler and more efficient
  - May include multiple (swappable) alternatives to perform same role with
    different tradeoffs
- **Should not** include knowledge or tools just for the sake of those who
  build it
  - For example, although the people working on a new Civboot need to eat,
    farming equipment and practices should not be included.
  - Some equipment, like a bulldozer/truck, have multiple purposes but may be
    required to build the Civboot itself so should be included.
  - Civboot **should** enable the storage/retrieval of such knowledge and
    construction of such tools

## Progress

The following is an effort to compile what is needed to construct a Level 1
Civboot and the current status.

**General Tools and Equipment (non computer)**: See
[Open Source Ecology](https://www.opensourceecology.org/gvcs/) for current work on many of
these designs. These two movements share a lot in common.

- Human Tools and Equipment:
 - Hand tools: saw, hammer, axe, shovel, pickaxe, etc
 - Powered hand tools: drill, rotary saw, soldering iron, etc
 - Vehicles: tractor, bulldozer, etc
- Manufacturing:
  - Materials creation: blast furnace, forge, brick builder, plastic (like)
    material synthesis, etc 
  - Fabrication: CNC machine, 3D printer (multi-material), lathe, etc
- Energy:
  - Power Usage: electric motor (+generator)
  - Power Generation: Steam generator, wind
  - Power Storage: battery, steam storage, gravity storage

**Computer Hardware**: the computer hardware manufactured will likely be
extremely limited compared to modern computer hardware.
- [podcast 0002][podcast 0002] ([show notes](podcasts/0002-semiconductor_fabrication.md))
  focused on whether semiconductors can be built.
- Basic requirements are to either simply the processing of Silicone (i.e.
  [silicone grains][silicone grains]) or a different semiconductor material
  (like Tin(II) Sulfide) and/or find a simpler process such as [nanotube
  CPU][nanotube CPU]. See also [this][DARPA 3DSoC CNFET] and [this][CNFET in
  factories].
- This is the hardest piece of a civboot.
- Once circuits can be constructed, a simple stack-based CPU like the [J1](J1)
  is likely the best target for Civboot, as such CPUs can be produced with only
  a few thousand transistors.

[podcast 0002]: https://anchor.fm/civboot/episodes/0002---Could-a-community-produce-their-own-computer-chips-ejmum0)
[silicone grains]: https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4640800/
[Impurity doping]: https://en.wikipedia.org/wiki/Wafer_(electronics)#Impurity_doping
[nanotube CPU]: https://www.sciencenews.org/article/chip-carbon-nanotubes-not-silicon-marks-computing-milestone
[DARPA 3DSoC CNFET]: https://www.electronicsweekly.com/news/business/darpa-3dsoc-cnfet-project-moves-commercialisation-phase-2020-08/
[CNFET in factories]: https://news.mit.edu/2020/carbon-nanotube-transistors-factory-0601
[J1]: https://github.com/jamesbowman/j1


**Computer Software**: [CollapseOS](CollapseOS.org) gives a pretty good idea of
how to get software bootstrapped on basic hardware using only a few hundred
lines of assembly and forth code. That OS fits on less than 5k, requires no
binary blobs, and can cross compile to several other architectures. The Forth
programming language is a primary driver of this simplicity. A (forth)
compiler, basic file system, text editor, assembler and cross compiler was
written largely by a single individual and runs on a computer chip with <9000
transistors from the 1970's.

The basic language stack for software is currently imagined to be: Forth OS /
bootloader largely paralleling the design of CollapseOS, but probably using
[typeforth][typeforth]. This work is [in
progress](https://github.com/civboot/triforth), with a minimal forth language
working, but it will undergo several redesigns before the next phase. The
CivbootOS will use forth for all core software including filesystem, memory
manager, (forth) shell, text editor, etc.  Very likely, severe restrictions
will be put in place, like only being able to allocate up to 1KiB contiguous
memory for most data structures.

On top of this OS, software should be written including CAD, board-layout and VLSI
software similar to [ImplicitCAD][ImplicitCAD], i.e. does not require a GUI. In addition
communication protocols will likely be similar (or identical) to the [PJON
1-wire protocol][PJON] and Ham Radio, with minimal support for TCP/IP. Finally,
a site-database can hold tutorials and educational software similar to [Khan
Academy](https://www.khanacademy.org/), which can aid the self-learning of
Civboot students.

[ImplicitCAD]: https://github.com/colah/ImplicitCAD
[typeforth]: https://typeforth.org
[PJON]: https://github.com/gioblu/PJON
