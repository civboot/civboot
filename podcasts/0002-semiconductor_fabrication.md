# Episode 2: Could a community produce their own computer chips?

Watch on [youtube][youtube] or listen on [anchor][anchor] or wherever you get your podcasts.

[anchor]: https://anchor.fm/civboot/episodes/0002---Could-a-community-produce-their-own-computer-chips-ejmum0
[youtube]: https://www.youtube.com/watch?v=2pJjzKlYI8g

Show notes

First a little groundwork
 - What is the timeframe of civboot?
 - What are the minimum requirements? 
   - Forth operating system CollapseOS on z80 9000 transistors greenarray chips
     http://www.greenarraychips.com/ with full chip design software in forth,
     and apparently minimal resource usage. ([forth experience blog])


- What is silicone used for? Why is pure silicone needed?
  - We'll get more into how to manufacture computer chips later, although there
    has been some movement in that space recently.
- Cost of silicone fabs has been skyrocketing, over 10B in 2013 ([fab costs][fab costs]).
  -  "In 1970 the cost of building a fabrication plant was $3 million,
     but by 1980 the cost had reached approximately $75 million.
     Throughout the 1980s costs carried on rising, by 1985 the cost of a
     fabrication plant had risen to around $150 million" [source][SPeters]
  -  The [Commodore 64](https://en.wikipedia.org/wiki/Commodore_64) (8bit 1MHz
     64KB RAM 20KB ROM) 
    - Made in 1983 and is still the most widely sold single computer of all time. 
- Personally I think we want at least 500 MiB of RAM and 500MHz multi-core
  processor. It should also be possible to store at least a few GiB on some
  kind of storage medium. 
- However, from my limited research These numbers seem impossible for a
  Civboot, as even 256MiB of RAM required 100nm processes (at least),
  which puts us in the 90nm process area of the early 2000's, where
  silicone fabrication facilities cost between 1.7 and 2.3 billion dollars
  [source][fab costs] (see also: [microprocessor chronology] [RAM timeline])
- Possibilities of reducing cost? How is pure silicone manufactured
- Purifying the silicone (source [silicone springer])
  - Purified from sand via melting (~2000C) in submerged arc furnace, manufactured via steel
    and refactory metals. Requires extremely high electric power, makes MG-Si (98-99% pure),
    called metallurgical-grade silicon.
  - Powderized MG-Si has several chemical reactions and distilations at low
    temperature to produce polysilicone (pure silicone)
  - [silicone grains]: Recent developments here might be helpful. One study
    showed purification of silicone (5 9's) with cheap and simple process of
    crushing it into nano-scale particles and using just simple acids with very
    little heat

- Growing pure crystal 
  - Even after the silicone is purified, a pure crystal must be grown
  - Two types: Floating-Zone and Czochralski. FZ is primarily used for
    ultra-pure silicone (5% of Si produced) and has issues with wafer
    processing due to being weaker to temperature changes, however CZ is
    unsuitable because 
    - "In the conventional CZ process, called a batch process, a crystal is
      pulled from a single crucible charge, and the quartz crucible is used
      only once and is then discarded. This is because the small amount of
      remaining silicon cracks the crucible as it cools from a high temperature
      during each growth run."
  - Basics of CZ: silicone is melted in a quartz crucible and slowly pulled, forming
    a large cylinder of single-crystal silicone. Therefore the crystal is grown down.
  - Basics of FZ: "a polysilicon rod is converted into a single-crystal ingot
    by passing a molten zone heated by a needle-eye coil from one end of the
    rod to the other".
  - Basically, the bottom of the rod is melted and has a pure-crystal seed inserted
    into it, which then cools to also become pure-crystal (this is how crystal
    growth work). The polysilicone rod is then slowly melted from bottom to top,
    the crystal growing upwards.

Finally: the silicone has to be cut into wafers in a very precise fashion
(perfectly along the latice) using diamond saws, which are highly precise
and expensive instruments. Processing the wafer to produce transistors is a topic
for the next episode.

- Done with basic silicone process, thoughts:
  - Cheaper, or at least simpler, methods are definitely desired. However,
    using the nano-particles to obtain fairly pure silicone could possibly be
    the perfect scale-down breakthrough, although melting into rods is still
    necessary.
  - Regardless of whether these process can be understood and replicated they
    are extremely complex, labor intensive and dangerous. It would be very
    difficult for anything smaller than a university or large company to build.
  - Don't need large wafers or silicone which is as pure as modern wafers
    - need to only match the early 2000's, hundreds of MHz CPUs and hundreds of
      Mb RAM, for computers to be usable to learn how to rebuild Civboot.
      However, this may still cost billions of dollars
      [source][fab costs] (see also: [microprocessor chronology] [RAM timeline])
  - Regarldess, silicone manufacturing is going to be difficult to make
    smaller. I highly doubt anyone will even try.

So we are left with two options: reduce the cost of silicone and computer chip
manufacturing or find a different process.
- There may actually be hope in reducing costs of silicone processes --
  previous quoted prices were for enourmourmous fabrication facilities which by
  necessity produce massive scale. A Civboot does not need such scale, and it
  can take advantage of knowledge gained in the present to produce chips of
  significantly less complexity.
- However, we don't actually want to produce silicone computer chips -- we just
  want to produce computer chips. Are there other options?

yes
- [nanomagnetic logic] seems interesting. It uses extremely small magnets and
  a magnetic clock to create a computer. Logic gates can be constructed by
  carefully arranging the magnets. This has several advantages, however
  I can not see any recent updates which demonstrates this outside of a few
  simple gates. The future may tell whether this is an option.
- [nanotube CPU] was demonstrated at a low speed. This uses carbon nanotubes
  instead of silicone to construct transistors and can theoretically achieve
  better compute efficiency. According to the DARPA press release
  ([source][DARPA 3DSoC CNFET]) this process does not require a pure-silicone
  backing and requires only low temperature processes, which to me indicates it
  _might_ scale down. It has also been demonstrated to work using existing
  semiconductor fabrication methods ([source][CNFET in factories])

Worth noting now the timescale Civboot is operating under. It is _very_
unlikely that we can produce a full Civboot in less than 30 years, which means
that if this technology matures in ~10 years the patents will be availble for
open use. Also, creating our own chips from scratch is the most pie-in-the-sky
piece of Civboot. I think it is important -- but it is also the most difficult
regarding what modern technology can give us.

Perhaps most importantly, universities and industry are already working on
improving the current processes -- which may open up this technology in the
future.



# Old notes

How can we build computer chips since silicone is so expensive to purify?
- Go back to first principles, what do we want?
- A bit of a crash course in computer chip physics
 - A computer processor is composed of transistors, which form logic gates.
 - Take electrical signals at inputs, and generate outputs based on logic gates.
 - Outputs control other circuits such as memory to affect later execution.
 - That's all on computer chips, we want to talk about how they are made.
 - How transitors are made
 - Extremely pure silicone which is grown as a single crystal and adding a
   small concentration of boron, phosphorus, arsenic or antimony is added. The
   silicone is still 99.9999% pure [source][Impurity doping].
 - Silicone is sliced by dimond saws into thin wafers.
 - The silicone is doped to create MOSFET or similar transistors. These use
   the physics of semiconductors to create gates which can allow or disallow
   current to flow between the drain and the source. [more information][mosfet
   physics]
 - Transistor phsics may be discussed more in a later episode.

First, a few details about the cost of silicone purificaiton

So we are left with two options: reduce the cost of silicone and computer chip
manufacturing or find a different process.
- There may actually be hope in reducing costs of silicone processes --
  previous quoted prices were for enourmourmous fabrication facilities which by
  necessity produce massive scale. A Civboot does not need such scale, and it
  can take advantage of knowledge gained in the present to produce chips of
  significantly less complexity.
- However, we don't actually want to produce silicone computer chips -- we just
  want to produce computer chips. Are there other options?


Other links:
- [Decent Quora answer](https://www.quora.com/What-are-some-alternatives-to-silicon-for-making-transistors-Ive-read-about-gallium-nitride-and-graphene-and-I-assume-other-possibilities-must-exist-What-are-the-trade-offs-e-g-yield-power-requirements-thermal-conductivity-cost-etc)

[SPeters]: https://books.google.com/books?id=LJ59DAAAQBAJ&pg=PA80&lpg=PA80&dq=1980+semiconductor+fabrication+plant+cost&source=bl&ots=6nKKg7t0MC&sig=ACfU3U0CBhs9zsxoKKFe6c3AXCBHjtCLfg&hl=en&sa=X&ved=2ahUKEwjvqO_1jO3lAhXW7Z4KHRoACr44ChDoATADegQICRAB#v=onepage&q=1980%20semiconductor%20fabrication%20plant%20cost&f=false
[Impurity doping]: https://en.wikipedia.org/wiki/Wafer_(electronics)#Impurity_doping
[fab costs]: https://static.seekingalpha.com/uploads/2015/9/15027822_14424109464539_rId9.png
[mosfet physics]: http://www.onmyphd.com/?p=mosfet.transistor
[nanomagnetic logic]: https://www.advancedsciencenews.com/energy-efficient-computing-via-nanomagnetic-logic-architectures/
[nml simulator]: https://link.springer.com/article/10.1007/s10825-018-1215-8?shared-article-renderer
[nml fabrication]: https://www.researchgate.net/publication/269328479_Nanomagnetic_logic_devices_fabrication_using_nanoimprint_lithography
[nanotube CPU]: https://www.sciencenews.org/article/chip-carbon-nanotubes-not-silicon-marks-computing-milestone
[DARPA 3DSoC CNFET]: https://www.electronicsweekly.com/news/business/darpa-3dsoc-cnfet-project-moves-commercialisation-phase-2020-08/
[CNFET in factories]: https://news.mit.edu/2020/carbon-nanotube-transistors-factory-0601
[silicone springer]: https://link.springer.com/chapter/10.1007/978-3-319-48933-9_13
[silicone grains]: https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4640800/
[microprocessor chronology]: https://en.wikipedia.org/wiki/Microprocessor_chronology
[RAM timeline]: https://en.wikipedia.org/wiki/Random-access_memory#Timeline
[forth experience blog]: http://yosefk.com/blog/my-history-with-forth-stack-machines.html
