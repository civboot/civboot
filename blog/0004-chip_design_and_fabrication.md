# How will a Civboot make it's own computer chips?
> This is the blog component to the
> [podcast](../podcasts/0004-chip_design_and_fabrication.md). It is not a
> word-for-word writeup, but rather in the same spirit.

> Note: If you want to know the absolute basics of how a computer chip works check
> out an excellent [13 minute overview][13 minute overview]. If you want to see
> how they can be manufactured, check out [Sam][Sam]'s work.

Open source silicone has been advancing radically in the last several years, and
Civboot intends to fully ride this trend. CPU designs can be written in Verliog
and flashed to an FPGA using only open source toolchains (see [IceStorm][IceStorm]
and [Symbiflow](https://symbiflow.github.io/)), with awesome reference CPUs
such as the [J1][J1] CPU (also check out [this great
article](https://www.fpgarelated.com/showarticle/790.php) analyzing it). Once
the CPU design is mature enough, we can even get them manufactured for relatively
low cost.

But the point of the Civboot is to eventually manufacture them _ourselves_. This is
clearly a lot harder, and will require complex and high precision tooling and processes.
This is the 30 year arm of the Civboot goal -- for a very very long time, we will have
not have any chips made in-house, and for quite a while afterwards we will have
only a small fraction of chips made in house. If Civboot _does_ end up building
it's own computer chips they will almost certainly have poor efficiency and
reliability, making them not ideal for most applications.

But that's okay. Iteration is the name of the game here. If we can manufacture
even one highly inneficient and unreliable CPU in a few decads then it will be
an incredible achievement. Making it more efficient will be the work of Civboot
students and graduates of hte future, which is the whole point of a Civboot --
creating a generation of people who can build and improve technology from its
fundamentals.

[13 minute overview]: https://youtu.be/bor0qLifjz4
[Sam]: http://sam.zeloof.xyz/first-ic/
[IceStorm]: http://www.clifford.at/icestorm/
[FPGA tutorial]: https://github.com/Obijuan/open-fpga-verilog-tutorial/wiki
[J1]: https://www.excamera.com/sphinx/fpga-j1.html
