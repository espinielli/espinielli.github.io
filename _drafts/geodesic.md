---
layout: post
title: Folding Geodesic in D3.js
date: '2014-11-29T15:46:00.001+02:00'
author: enrico_spinielli
comments: true
tags:
- Geodedic
- Spherical geometry
- D3.js
- Geometry
modified_time: '2014-11-29T15:46:00.001+02:00'
---

# Geodesic

This is an attempt to understand and maybe reproduce part of what has beed described
in [Unfolding the Earth: Myriahedral Projections](http://www.win.tue.nl/~vanwijk/myriahedral/)
by [Prof. Jack van Wijk](http://www.win.tue.nl/~vanwijk/).

I start from the D3.js examples from [Mike Bostock](http://bost.ocks.org/)

* [the icosahedron mesh](http://bl.ocks.org/mbostock/3061181)
* [the geodesic grid](http://bl.ocks.org/mbostock/3058685)

and from [Jason Davies](http://www.jasondavies.com/):

* [Waterman Butterfly Map](http://www.jasondavies.com/maps/waterman-butterfly/)


## The Icosahedron Mesh

In his example Mike Bostock starts from a regular icosahedron with coordinates

$$
\begin{alignat}{8}
&(& 0        ,&& \pm 1    ,&& \pm \phi &)& \\
&(& \pm 1    ,&& \pm \phi ,&& 0        &)& \\
&(& \pm \phi ,&&        0 ,&& \pm 1    &)&
\end{alignat}
$$

where $\phi$ is the [golden ration](http://en.wikipedia.org/wiki/Golden_ratio)

$$ \phi = \frac{1 + \sqrt{5}}{2} $$

The details of coordinates and faces can be found on [Paul Bourke's site][platonic].
In particular [this file][coords]



## [Tetrahedron](http://en.wikipedia.org/wiki/Tetrahedron) coordinates

The spherical coordinates for the [regular tethraedron][tethraedron] are:

$$
\begin{alignat}{8}
&(& 0,&& 0,&& \sqrt{\frac{2}{3}} - \frac{1}{2 \sqrt{6}} &)& \\
&(& -\frac{1}{2 \sqrt{3}},&& -\frac{1}{2},&& -\frac{1}{2 \sqrt{6}} &)& \\
&(& -\frac{1}{2 \sqrt{3}},&& \frac{1}{2},&& -\frac{1}{2 \sqrt{6}} &)& \\
&(& \frac{1}{\sqrt{3}},&& 0,&& -\frac{1}{2 \sqrt{6}} &)&
\end{alignat}
$$


## D3's geodesic.js


## References

[platonic]: http://paulbourke.net/geometry/platonic/ "platonic solids"
[coords]: http://paulbourke.net/geometry/platonic/icosahedron.vf "icosahedron vertices and faces coordinates"
[tethraedron]: <http://www.wolframalpha.com/input/?i=vertex+coordinates+of+regular+tetrahedron> "reguran tethraedron"
