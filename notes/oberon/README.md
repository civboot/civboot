# Project Oberon

See the main site page: http://projectoberon.com/

The entire source code is ~10,000 lines for a full operating system.
Unfortunately, it does not "self bootstrap" -- Oberon in it's entirety (both the
language the kernel) is written in Oberon. In order to compile Oberon with a
language like C you would have to write the entire language, not just a VM/etc.

However, that asside it appears to be an excellent reference for simple language
and kernel design. This directory will be used to store notes from my reading of
the langauge, along with learnings for Civboot software.

## Chapter 2 (page 14): Basic Concepts
Very much a gui-based system. 

* It uses cooperative multi-tasking or really _no multitasking at all_. The user
  selects the "Viewer" to use via their mouse and that process gets control. The
  keyboard+mouse are polled in a single Oberon loop. Not sure how networking
  works...
  * Oh, it does have interrupts... but only in the "drivers". So it's not
    _really_ a single-thread system.
* Page 21, yes indeed it is <10,000 lines of code. Also the full system may in
  fact use less than 64KiB of memory (although this might just be globals?).

Otherwise this is mostly an overview of what the book will discuss

## Chapter 3 (page 26): The tasking system

Mostly just more information regarding the task scheduler and how it is just a
simple loop. Pretty neato. There are two task types: background and interactive.
The main loop prioritizes interactive. There is a trap handler

## Chapter 4

Wow, way too much focus on UI. Moving to chapter 5.

## Chapter 5: text system

I like this:

> There are many reasons for the popularity of text in general and in connection
> with computers in particular. To name but a few: Text containing any arbitrary
> amount of information can be built from a small alphabet of widely
> standardized elements (characters), their building pattern is extremely simple
> (lining up elements), and the resulting structure is most elementary (a
> sequence). And perhaps most importantly, syntactically structured text can be
> parsed and interpreted by a machine. 


* Conventional operating systems neither feature an integrated management nor an
  abstract programming interface (API) for texts.

How text is in memory:

> Representing characters as pairs (font, ref), where font designates a font and
> ref the character's ASCII-code and adding two more attributes color and
> vertical offset, we get to a quadruple representation (font, ref, col, voff)
> of characters.

**Bookmark page 57**
