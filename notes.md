
## Required Components for Level 1 Civboot

The following is an (extremely rough) list of what is required to produce a
Level 1 Civboot. The purpose of this section is to organize thoughts about next
steps

**Village Toolset a.k.a. [Open Source
Ecology](https://www.opensourceecology.org/gvcs/)**



*   Human Tools and Equipment: 
    *   Hand tools: saw, hammer, axe, shovel, pickaxe, etc
    *   Powered hand tools: drill, rotary saw, soldering iron, etc
    *   Vehicles: tractor, bulldozer, etc
*   Manufacturing:
    *   Materials creation: blast furnace, forge, brick builder, plastic (like) material synthesis, etc 
    *   Fabrication: CNC machine, 3D printer (multi-material), etc
*   Energy:
    *   Power Usage: electric motor (+generator)
    *   Power Generation: Steam generator, wind, solar (?)
    *   Power Storage: battery, steam storage, gravity storage
*   See **[Open Source Ecology](https://www.opensourceecology.org/gvcs/)** for current work on many of these designs

**Computer Hardware**: the computer hardware manufactured will likely be extremely limited compared to modern computer hardware.



*   Basically wikipedia [list of steps](https://en.wikipedia.org/wiki/Semiconductor_device_fabrication#List_of_steps)
*   Manufacturing: wafer, circuit board printing, chemical production, electrical wire, electrical and data connectors, display manufacturing (simple display, possibly pixel-based), input devices (aka keyboard)
*   Electronics manufacturing: photolithography, silicone doping, Gate production, CPU, RAM, FPGA, Disk Storage, etc
*   Inkjet printed transistors
    *   Radically reduce cost+complexity? Literally print whole circuit (including processor, ram, busses) on a single substrate?
*   Processor architecture: RISC-V, FPGA(?)
*   ... incomplete

**Computer Software**: must be simplified to be runnable on the hardware available in previous steps, but must be powerful enough to model and build the hardware in previous process.



*   Operating system: linux-like
*   Compiler toolchain
*   Text editor: vim, emacs, other?
*   Documentation viewer: simplified (text-based) browser (?)
*   Version control: git (?)
*   Documentation language: wiki viewed in text-viewer
*   Physical design: 3D (simple graphics) CAD tool, 2D circuit board design, chip design, electronics design
*   Networking: IPFS over internet+non-internet
*   ... incomplete


## References


### Village Toolset
*   [Open Source Ecology](https://www.opensourceecology.org/gvcs/)


### Computer Hardware



*   [microprocessor chronology](https://en.wikipedia.org/wiki/Microprocessor_chronology) and [RAM timeline](https://en.wikipedia.org/wiki/Random-access_memory#Timeline) gives a basic idea of circuit sizes through time
    *   3MHz at 3um (1983 RISC-II), 40k transistors
    *   1GHz possible at 250 nm (1999 AMD Athalon), 22M transistors
    *   The [Commodore 64](https://en.wikipedia.org/wiki/Commodore_64) (8bit 1MHz 64KB RAM 20KB ROM) 
        *   Made in 1983 and is still the most widely sold single computer of all time. 
        *   10,000+ commercial software titles were made for it.
        *   Capable of 16 bit color graphics, as well as sound
        *   It was made **almost entirely in house**, including custom
            integrated circuit chips from MOS Technology
* [Semiconductor_fabrication_plant](https://en.wikipedia.org/wiki/Semiconductor_fabrication_plant)
    (note to self: update this wikipedia entry, it is terrible)
 * [Seeking Alpha Article with helpful graph](https://seekingalpha.com/article/3518016-dissecting-complex-semiconductor-industry-where-is-heading-over-next-5-years)
   [graph](https://static.seekingalpha.com/uploads/2015/9/15027822_14424109464539_rId9.png)
 * [1980's clean room Intel fab](https://www.chiphistory.org/128-an-intel-wafer-fab-cleanroom-circa-1980)
 * [paper](https://www.princeton.edu/~ota/disk1/1993/9315/931505.PDF) page
   26 says that a fab in 1980 cost ~$100 million (whew, that's more than
   expected)
 * [National Systems of Innovation: Creating High Technology Industries By S. Peters](https://books.google.com/books?id=LJ59DAAAQBAJ&pg=PA80&lpg=PA80&dq=1980+semiconductor+fabrication+plant+cost&source=bl&ots=6nKKg7t0MC&sig=ACfU3U0CBhs9zsxoKKFe6c3AXCBHjtCLfg&hl=en&sa=X&ved=2ahUKEwjvqO_1jO3lAhXW7Z4KHRoACr44ChDoATADegQICRAB#v=onepage&q=1980%20semiconductor%20fabrication%20plant%20cost&f=false)
   *   "In 1970 the cost of building a fabrication plant was $3 million,
       but by 1980 the cost had reached approximately $75 million.
       Throughout the 1980s costs carried on rising, by 1985 the cost of a
       fabrication plant had risen to around $150 million"
 * Note: I wonder if these costs are reflective of if the technology was
   recreated in the modern day. Clearly achieving anything under 100nm
   would be near impossible without $1billion+, but what nm-wafer could we
   achieve for < $10 million? Could we get down to 1000nm? 500nm? 500nm can
   go as fast as 370MHz (IBM S/390 G4), which could get a LOT done,
   especially if we brought forth multiple cores. However, the above paper
   says that by the late 1990s the cost of a fab was > $1 billion, so it
   feels unlikely that we could produce anything like that.
 * Look at it this way, 3MHz is 1000x less than modern CPU speed. In
   addition, if our fab size is that large it will severely limit how much
   RAM and other resources we have. What is the minimum we _need_?
 * Note: Personally I think we want at least 500 MiB of RAM and 500MHz
   multi-core processor. It should also be possible to store at least 1GiB
   on some kind of storage medium. These numbers seem impossible though,
   as even 256MiB of RAM required 100nm processes (at least).
* From what I've seen of semiconductor fabrication, in order to create a
  self-replicating machine which is simple we likely need to _invent_ a
  different material/process which is specifically for this purpose.
  Semiconductor production requires complex machinery, optics, dangerous
  chemicals, safety precautions, etc.
* Inkjet printed transistors
    *   [Inkjet Printing of High Performance Transistors with Micron Order Chemically Set Gaps](https://www.nature.com/articles/s41598-017-01391-2)
    *   It is entirely possible that over the next decade it will be possible to _print_ circuits of the needed size. In addition, these transistors demonstrate speeds >18GHz. Perhaps this problem is not so insurmountable.
    *   [Jan 2019 Wiley Online Library overview of inkjet printing transistors](https://onlinelibrary.wiley.com/doi/full/10.1002/advs.201801445)
    *   2014 [A thin-film microprocessor with inkjet print-programmable memory](https://www.nature.com/articles/srep07398): 2.1kHz basic microprocessor
    *   Assuming these nanoparticles can be manufactured within the CivBoot, this is a realistic approach to micro processors!


### Computer Software
*   https://bootstrappable.org/
*   [Rust layouts and ABIs](https://gankra.github.io/blah/rust-layouts-and-abis/): has some interesting details on what is required for rust to work at all
*   [Adventures in Motion Control](http://adventures.michaelfbryan.com/posts/announcing-adventures-in-motion-control/): guide on creating a CNC simulator to understand how they work better
*   NixOS is a bootstrapping operating system and build system where every component can be built from near scratch
*   

### Composting
*   [Composting human waste][Composting human waste]: it seems that industrial
    facilities pre-heat the input to kill any bacteria.
*   [Anerobic space food][Anerobic space food]
*   [Bugs in composting](http://compost.css.cornell.edu/invertebrates.html)
*   [Aenerobic digestion][Aenerobic digestion] converts waste into usable
    fertilizer without the need for oxygen or heat-treatment
*   [Aenerobic FAQs UoMch][Aenerobic FAQs UoMch]: kills bacteria, although not
    100%. Separates liquids from solids. Creates 
*   [Pyrolosis of biosolids][Pyrolosis of biosolids]: effective method for
    removing waste from biosolids... but also completely destroys it's use for
    farming.
*   [Antibiotic eating bacteria][Antibiotic eating bacteria]
*   [Guidelines on feces][Guidelines on feces]

[BBVA Open Mind]: https://www.bbvaopenmind.com/en/about-us/
[Composting human waste]: https://www.gardeningknowhow.com/composting/ingredients/composting-human-waste.htm#:~:text=Composting%20human%20waste%20is%20risky,humanure%20systems%20are%20rarely%20approved.
[Anerobic space food]: https://www.independent.co.uk/news/science/astronauts-food-human-waste-marmite-iss-international-space-station-nasa-a8179451.html
[Aenerobic digestion]: https://www.epa.gov/agstar/how-does-anaerobic-digestion-work
[Aenerobic FAQs UoMch]: https://www.michigan.gov/documents/anaerobic_digester_FAQs_2005_137431_7.pdf
[Methanotroph]: https://en.wikipedia.org/wiki/Methanotroph
[Pyrolosis of biosolids]: https://www.sciencedirect.com/science/article/pii/S0956053X18303313
[Antibiotic eating bacteria]: https://medicine.wustl.edu/news/bacterias-appetite-may-be-key-to-cleaning-up-antibiotic-contaminated-environments/
[Guidelines on feces]: http://www.ecosanres.org/pdf_files/ESR_Publications_2004/ESR2web.pdf
