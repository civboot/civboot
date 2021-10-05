# Episode 5: delays and moving

I have a few updates to make to the Civboot project. As I've said in the past, this is
a lifetime aspirational goal for me, not something I'm going to complete in a year.
Over the past several months my family and I have made several steps towards both
that long-term goal and our other values.

- I am now working remotely at my same company. With that freedom we are able
  to make other choices. In about a month, we will be moving to a small seven
  acre farm where we can try permaculture and agroforestry. This has long been
  something we wanted to do, but which the response to COVID has made possible
  since I can now work remotely.

- I've been building a new language, [fngi](https://github.com/vitiral/fngi). It is
  progressing nicely. I won't discuss much here since it isn't complete. What I will
  say is that I've decided _not_ to use web assembly for the Intermediate
  Representation.

## Fngi and WASM

I have previously made comments, here and elsewhere, that web assembly seems like
a decent language to bootstrap from. After implementing an interpreter for a
subset of the language (including i32 bit integers, function calls with inputs
and locals and many other features) I now think I was wrong. Here are the
majori issues:

1. wasm function inputs are part of the locals. This means that for
   _each function call_ a naive interpreter must first pop values from the
   stack and populate the inputs. This is the opposite of "inline". Clearly
   a complicated enough compiler can reason about these things, but this kind
   of complicaiton is exactly the kind of thing that Civboot wants to avoid.
2. WASM's core type system is I32. This was obvious from the beginning,
   but I now believe that being able to support 16bit environments is important
   to Civboot, not least of all because it is simpler and lighter-weight for
   the initial language.
3. I've been inspired by conversations in the
   [CollapseOS](https://collapseos.org/) community to build my own IR/assembly
   language, which is the direction I'm pursuing (but has been put on hold
   while we move our entire lives to live on a farm). Shout out to
   [uxn](https://100r.co/site/uxn.html), you can ready some of my thoughts
   about it [here][uxnThoughts]

## Permaculture and Agro-Forestry

First it should be said that I've never been a farmer. My wife has done some
gardening. We are naive and way out of our depth... isn't it fun!

Agro-forestry is not directly tied to Civboot, at least not until a Level-2
Civboot. However, I believe that understanding how to grow food, use
sustainable energy and use and build tools for farming will help me build a
Civboot.

I promise that this site/blog will not turn into some kind of journal on
permaculture and agro-forestry. If I wanted to do that I would start a new
channel!

## Verbal Outline
- A few updates, again note that this will go slowly.
- I have transistioned to work remotely.
- Now able to live on a small seven acre farm. Permaculture, agroforestry and
  raising animals.
- I've been building a new language called fngi, it isn't complete.
- Talk a little about wasm and why it hasn't worked. I'm building my own IR
- Talk a little about farming and living self-sufficiently.



[uxnThoughts]: https://github.com/vitiral/fngi/blob/main/notes/fu.md


