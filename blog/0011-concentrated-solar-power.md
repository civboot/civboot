# Concentrated Solar Power

Like normal, I've become distracted from fngi with a new project: writing a text
editor (and ipython-like shell!) in Lua ([ele](https://github.com/civboot/ele)).
That is _almost_ ready for a blog post and announcement, but I'm going to take a
side-track with a (perhaps obvious) realization about generating power for a
Civboot.

I had [previously](../thoughts/levels.md) believed power generation should NOT
be part of a "level 1 Civboot" but the solution I've realized is so simple as to
be absurd to not include it.

## Concentrated Solar Power Plant

Simply put:

1. Use Concentrated Solar Power ([CSP]) and water to create high-quality (high
   temperature and pressure) steam.
2. Store the high quality steam in a Steam Accumulator ([SA])
3. Generate electricity (day and night) from the stored steam using a [Steam
   Electric Power Station]
4. Directly use high-pressure steam for industrial processes that need it, the
   Civboot will have many.
5. Utilize low quality (lower temperature) steam for industrial processes and
   even [Steam Jet Cooling] for refrigeration/air-conditioning (I found [this
   paper][1] interesting).
6. Utilize low quality heat (i.e. hot water) for drinkable hot water, industrial
   processes, and heating of buildings.
7. The waste water is then drinkable, meaning this can serve the dual-purpose of
   desalinating water.

## Takeaways
Concentrated Solar Power is typically not as efficient or cost-effective as
photovoltaic systems. However, it is about the simplest possible renewable
energy source possible to manufacture as it is made entirely of relatively
simple mechanical components: primarily pumps, a steam power generator and a
large reflective dome.

The eureka moment for me was when I realized that a Steam Accumulator could be
used to store the high-quality steam, permitting energy storage. Not only that,
but a steam accumulator is a highly valuable resource for many industrial
processes, meaning the [CSP] and [SA] could be the centerpoint for many
industrial processes.  The final piece of the puzzle was when I looked up
whether you can use steam as a refrigerant. Not only can you, but the steam [can
even be of low quality][1].

This means that Steam can be used for all major energy requirements of a
Civboot: heating, cooling and electricity. Not to mention the inherent value of
the steam itself. Because of it's simplicity and versatility, it may be the
best-possible energy source for Civboot. It also has the benefit that it can
fall-back on organic sources of energy (i.e. wood fire) for emergencies or
in environments where those resources are plentiful.

Civboot could become a modern manifestation of Steampunk fantasy!

[CSP]: https://en.wikipedia.org/wiki/Concentrated_solar_power
[SA]: https://en.wikipedia.org/wiki/Steam_accumulator
[Steam Electric Power Station]: https://en.wikipedia.org/wiki/Steam-electric_power_station
[Steam Jet Cooling]: https://en.wikipedia.org/wiki/Steam_jet_cooling
[1]: https://docs.lib.purdue.edu/cgi/viewcontent.cgi?article=2435&context=iracc
