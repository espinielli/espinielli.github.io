---
layout: post
title: Learning Vega 2.0 a.k.a. Reactive Vega
date: '2015-08-08T18:38:52+02:00'
author: enrico_spinielli
comments: true
tags:
- vega
- Grammar of graphics
- D3.js
- Grammar of interaction
modified_time: '2015-08-08T18:39:13+02:00'
---
<style>
h1 ~ aside {
  font-size: small;
  right: 0;
  position: absolute;
  width: 180px;
}
</style>
# Learning Vega 2.0 #

## A Grammar of Graphics ##
Vega 2.0 adds a _grammar of interaction_ to the _grammar of graphics_ implemented in Vega 1.0.

When you say _grammar of graphics_ all roads bring you to

> Leland Wilkinson
> [The Grammar of Graphics](http://books.google.com/books/about/The_Grammar_of_Graphics.html?id=_kRX4LoFfGQC)
> Springer Science & Business Media, Jul 15, 2005

But given [the price of 100+ USD](http://www.amazon.com/Grammar-Graphics-Statistics-Computing/dp/0387245448/),
I am not even thinking of getting my hands on it.

So the next thing you can look at is who has build upon this conceptual framework.
And then we bump into

> Hadley Wickham.
> A layered grammar of graphics.
> Journal of Computational and Graphical Statistics, vol. 19, no. 1, pp. 3–28, 2010.
> http://vita.had.co.nz/papers/layered-grammar.html

which implements a (layered) grammar of graphics as a package for the excellent
[R language](https://www.r-project.org/).
The article in the "Conclusions" section raises the question of how to add interaction to the `ggplot2` framework.

## ... and a Grammar of Interaction ##
Moving to Vega, from its own site

> Vega is a visualization grammar, a declarative format for creating, saving, and sharing interactive visualization designs


The _interactive_ was not present in the first implementation as of Apr 2014, it has been added and publicly described in

> Arvind Satyanarayan, Kanit Wongsuphasawat, Jeffrey Heer
> Declarative Interaction Design for Data Visualization
> ACM User Interface Software & Technology (UIST), 2014,
http://idl.cs.washington.edu/papers/reactive-vega

The companion video explains things further:

<iframe class="player" src="//player.vimeo.com/video/100936827" width="630" height="354" frameborder="0" webkitallowfullscreen="1" mozallowfullscreen="1" allowfullscreen="1"></iframe>

## And now let's use it ##
All that reading is ok but without doing I do not fully get it (and even then ... ;-)
I need [examples](http://bost.ocks.org/mike/example/)

So the [Vega tutorial](https://github.com/vega/vega/wiki/Tutorial) is a very good starting point
(and like all tutorials [it can be perfected](https://github.com/vega/vega/issues/308) by
[inspirational minds](https://github.com/vega/vega/issues/308#issuecomment-125266356)),
as well as the interactive and non examples in the [Vega Editor](http://vega.github.io/vega-editor/?spec=bar).

Here of course you have less textual description about the what's and why's.

The tutorial shows you how to build a (vertical) bar chart. Here it is:

<iframe src="https://cdn.rawgit.com/espinielli/358d490182efc1beace5/raw/87428be8d9493ae67c1909162a4a5bf2fd05d063/index.html"
	width="250px" height="450px" marginwidth="0px" marginheight="0px" scrolling="no"></iframe>

So as my homework I implemented what requested as en exercise at the end of the tutorial.
That is

"convert the (vertical) bar plot
to an horizontal one"

Here it is:

<iframe src="https://cdn.rawgit.com/espinielli/64b0be9bc33d1405bc92/raw/ab9d7be1b4fa9ab72d40555803579896bf7815fc/index.html"
	width="450px" height="250px" border="1px solid #DEDEDE" marginwidth="0px" marginheight="0px" scrolling="no"></iframe>

The interactive bit in both blocks is evident when you hover your mouse on the bars and the relevant amount is textually shown.
Simple and it renders the concept.

And finally a little example with yearly number of flight in Europe:

<iframe src="https://cdn.rawgit.com/espinielli/87047968c67c02dbcb8d/raw/a570dc7500c87f3fc6cba48a62d12725bfc2e4c5/index.html"
	width="480px" height="180px" border="1px solid #DEDEDE" marginwidth="0" marginheight="0" scrolling="no"></iframe>

The following questions remain for me:

1. How to I pad the graph so that my boundary point are not so close to the axes?
   This [issue](https://github.com/vega/vega/issues/104) calls for an offset to be added to the spec.

2. How do I link events from widgets external to the graph to interactions in the graph?
   (Here I think the [Reactive Vega](http://idl.cs.washington.edu/papers/reactive-vega)
   supplemental material, i.e. `ex_jobs.json`, watch at min 3:19 of reactive vega video above, will help,
   as well as [CSS selector for Signals](https://github.com/vega/vega/wiki/Signals#user-content-event-stream-selectors)
   in the Vega documentation)

If you want to daydream and see the potentials of Vega watch the recent conference presentation from Prof. Jeffrey Heer:

<iframe width="560" height="315" src="https://www.youtube.com/embed/GdoDLuPe-Wg" frameborder="0" allowfullscreen></iframe>
