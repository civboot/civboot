# Episode 4: Designing and Fabricating a Civboot Integrated Circuit

Download [here](./0004-chip_design_and_fabrication.mp3), watch on [youtube][youtube] or listen on
[anchor][anchor] or wherever you get your podcasts.

> This is the podcast component to the [blog](../blog/0004-chip_design_and_fabrication.md). It is
> not respoken word-for-word writeup, but rather in the same spirit.

A bit of introduction and explanation of why the gap between episodes
(FPGA tangent, baby) and likely future direction.

- Episode is about chip manufacturing after semiconductor manufacturing.
- Very high level. Knowing in depth requires graduate level work, something
  I intend to do.
- See show notes for a few excellent links about the details.

Basics:
- Start with pure silicone wafer (see episode 2).
- Oxidize it at fairly high temperature. Creates thin layer of protection.
- Apply a photo resist and mask -- areas light touches (for positive
  photoresist) can be disolved by applying a solvent.
- Then apply various processes to the wafer such as:
  - "Doping" the pure semiconductor to make it either p (positive) or n
    (negative) type, which will determine the characteristics of how the
    semiconductor is conducting.
  - Depositing metal then disolving the photoresist -- areas with no photo
    resist will have metal.
- Once the chips are manufactured they are cut or broken off (score and cleave
  is very effective) and wirebonded into an IC so it can be soldered.

That's "it", but the processes to achieve many of these steps require extreme
precision for even the density of transistors that a Civboot would need.
Basically, while this process is likely easier to accomplish than the growth of
a pure semiconductor, it is far from easy. However, it is "easy" enough that a
teenager managed to make a simple IC in his garage, albeit with expensive legacy
tooling he acquired from ebay and other means (see [link][Sam]).

That's about all I'm going to cover this material for now. I'll leave you with
this [13 minute overview][13 minute overview], which is far better than I could
ever do. Hopefully I will be able to do a more in-depth episode in the future.

Before building your chip though, you must know what transistors you want to
put on it and in what configuration.

- FPGA: Field Programmable Gate Array. Explain what it is, why it is useful.
- Open source tools can now program it. [IceStorm][IceStorm] toolchain. [FPGA
  tutorial][FPGA tutorial] in spanish and english.
- [J1][J1] Forth CPU.
- Also worth mentiniong [homebrew computers](https://www.homebrewcpuring.org/),
  people building their own computers for fun, some from very minimal
  components. The [megaprocessor](http://www.megaprocessor.com/homebrew.html)
  might be a good place to start down the rabbit hole.

Also worth mentioning that open silicon is starting to become more common,
meaning some of these processes may end up in the public domain in the future.
- https://www.lowrisc.org/open-silicon/
- https://github.com/google/skywater-pdk: open sourced 130nm process
- https://theopenroadproject.org/ EDA toolkit
  - https://github.com/efabless/openlane: builds on top for optimization.

[anchor]: TODO
[youtube]: https://youtu.be/TDKvXsTQvq4
[Sam]: http://sam.zeloof.xyz/first-ic/
[13 minute overview]: https://youtu.be/bor0qLifjz4
[IceStorm]: http://www.clifford.at/icestorm/
[FPGA tutorial]: https://github.com/Obijuan/open-fpga-verilog-tutorial/wiki
[J1]: https://www.excamera.com/sphinx/fpga-j1.html
