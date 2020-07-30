

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
-  "In 1970 the cost of building a fabrication plant was $3 million,
   but by 1980 the cost had reached approximately $75 million.
   Throughout the 1980s costs carried on rising, by 1985 the cost of a
   fabrication plant had risen to around $150 million" [source][SPeters]
-  The [Commodore 64](https://en.wikipedia.org/wiki/Commodore_64) (8bit 1MHz
   64KB RAM 20KB ROM) 
  - Made in 1983 and is still the most widely sold single computer of all time. 
- Personally I think we want at least 500 MiB of RAM and 500MHz multi-core
  processor. It should also be possible to store at least 1GiB on some kind of
  storage medium. 
- However, from my limited research These numbers seem impossible for a
  Civboot, as even 256MiB of RAM required 100nm processes (at least),
  which puts us in the 90nm process area of the early 2000's, where
  silicone fabrication facilities cost between 1.7 and 2.3 billion dollars
  [source][fab costs]

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

