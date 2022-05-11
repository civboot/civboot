## Basic Concepts
[Good quick video overview](https://www.youtube.com/watch?v=NGFhc8R_uO4),
discusses some of the complexities with lithography at the tiny processor size
in current use. This brings up _some_ of the kinds of issues a Civboot would
have to face.


## Hardware Cost and Ability Over Time
[microprocessor chronology](https://en.wikipedia.org/wiki/Microprocessor_chronology) and [RAM timeline](https://en.wikipedia.org/wiki/Random-access_memory#Timeline) gives a basic idea of circuit sizes through time
* [paper](https://www.princeton.edu/~ota/disk1/1993/9315/931505.PDF) page
  26 says that a fab in 1980 cost ~$100 million (whew, that's more than
  expected)
 * [Seeking Alpha Article with helpful graph](https://seekingalpha.com/article/3518016-dissecting-complex-semiconductor-industry-where-is-heading-over-next-5-years)
   [graph](https://static.seekingalpha.com/uploads/2015/9/15027822_14424109464539_rId9.png)
* [1980's clean room Intel fab](https://www.chiphistory.org/128-an-intel-wafer-fab-cleanroom-circa-1980)
* [National Systems of Innovation: Creating High Technology Industries By S. Peters](https://books.google.com/books?id=LJ59DAAAQBAJ&pg=PA80&lpg=PA80&dq=1980+semiconductor+fabrication+plant+cost&source=bl&ots=6nKKg7t0MC&sig=ACfU3U0CBhs9zsxoKKFe6c3AXCBHjtCLfg&hl=en&sa=X&ved=2ahUKEwjvqO_1jO3lAhXW7Z4KHRoACr44ChDoATADegQICRAB#v=onepage&q=1980%20semiconductor%20fabrication%20plant%20cost&f=false)
* "In 1970 the cost of building a fabrication plant was $3 million,
  but by 1980 the cost had reached approximately $75 million.
  Throughout the 1980s costs carried on rising, by 1985 the cost of a
  fabrication plant had risen to around $150 million"
* 3MHz at 3um (1983 RISC-II), 40k transistors
* 1GHz possible at 250 nm (1999 AMD Athalon), 22M transistors
* The [Commodore 64](https://en.wikipedia.org/wiki/Commodore_64) (8bit 1MHz 64KB RAM 20KB ROM)
  * Made in 1983 and is still the most widely sold single computer of all time.
  * 10,000+ commercial software titles were made for it.
  * Capable of 16 bit color graphics, as well as sound
  * It was made **almost entirely in house**, including custom
    integrated circuit chips from MOS Technology


## Thin Thin Film Transistors
[Tin Thin Film Transistors][Tin TFT] made directly on glass using only low
temperature processes, which _may_ be replicatabeable by a Civboot at first
glance (PECVD and RF sputtering). It looks like the characteristics are
_extremely_ good as well. [This book][Tin Book] seems like a decent reference.


[Tin TFT]: https://www.nature.com/articles/s41598-019-53766-2
[Tin Book]: https://www.amazon.com/Tin-Oxide-Materials-Properties-Applications/dp/0128159243/ref=sr_1_1?dchild=1&keywords=Tin+Oxide+Materials+book&qid=1634793705&sr=8-1
