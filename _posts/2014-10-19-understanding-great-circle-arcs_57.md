---
layout: post
title: Understanding Great Circle Arcs Intersection Algorithm
date: '2014-10-19T15:51:00.001+02:00'
author: enrico_spinielli
comments: true
tags:
- Spherical
- D3.js
- Geometry
modified_time: '2014-11-07T15:14:14.879+01:00'
blogger_id: tag:blogger.com,1999:blog-1947405997418753543.post-4563888999264398405
blogger_orig_url: http://ongiantsshoulders.blogspot.com/2014/10/understanding-great-circle-arcs_57.html
---

# Understanding Great Circle Arcs Intersection Algorithm #
The following material is the result of my attempt to understand the [nice example][intersect] from Jason Davies.
I was puzzled about the origin of the algorithm used to find the intersection of two great circle arcs.
Google helped and I discovered [Roger Stafford's post][roger] in Matlab newsgroup and the relevant Python's implementation in the [Spherical Geometry Toolkit][geotoolkitintersect].
 

## The algorithm ##
You have two great circle arcs on a sphere, \\(a\\) from point \\(\mathbf{a_0}\\) to \\(\mathbf{a_1}\\), and \\(b\\) from \\(\mathbf{b_0}\\) to \\(\mathbf{b_1}\\), whose coordinates are expressed as longitude \\(\theta\\) (positive going East of Greenwich) and latitude \\(\phi\\) (positive going North).
Transform theses coordinates over to Cartesian coordinates using the equations:


\\begin{aligned}
x  & = cos(\theta) cos(\phi) \\\\
y  & = sin(\theta) cos(\phi) \\\\
z  & = sin(\phi)
\\end{aligned}


where
\\begin{equation} 
-\pi  \le  \theta  \le \pi \\\\
-\frac{\pi}{2} \le \phi \le \frac{\pi}{2}. 
\\end{equation}

These Cartesian coordinates correspond to a hypothetical spherical "earth" of unit radius, but that does not interfere in the following computations.

Let \\(\mathbf{a_0}\\), \\(\mathbf{a_1}\\), \\(\mathbf{b_0}\\), and \\(\mathbf{b_1}\\) be vectors of the Cartesian coordinate endpoints for the two arcs \\(a\\) (\\(\mathbf{a_0} \leftrightarrow \mathbf{a_1}\\) and \\(b\\)(\\(\mathbf{b_0} \leftrightarrow \mathbf{b_1}\\) obtained in this way. Carry out the following computations:

 \\(\mathbf{p} = \mathbf{a_0} \times \mathbf{a_1}\\) is the vector normal to the plane going through the arc \\(a\\) and the center of the Earth.

 \\(\mathbf{q} = \mathbf{b_0} \times \mathbf{b_1}\\)  is the vector normal to the plane going through the arc \\(b\\) and the center of the Earth.

\\(\mathbf{t} = \mathrm{normalized}(\mathbf{p} \times \mathbf{q})\\) is along the line of intersection of the planes above. (The normalization was not mentioned in [Roger's post][roger] but it is implemented in the [Spherical Geometry Toolkit][geotoolkitintersect] and by [Jason's example][intersect].)

Then, let's define the following quantities:
\\begin{aligned} 
 s_1 & =  (\mathbf{a_0} \times \mathbf{p})  \cdot \mathbf{t} \\\\
 s_2 & = (\mathbf{a_1} \times \mathbf{p}) \cdot \mathbf{t} \\\\
 s_3 & = (\mathbf{b_0} \times \mathbf{q}) \cdot \mathbf{t} \\\\
 s_4 & = (\mathbf{b_1} \times \mathbf{q}) \cdot \mathbf{t}
\\end{aligned} 

(These quantities are crucial: they represent the projection of \\(\mathbf{t}\\) along the arcs \\(a\\) and \\(b\\).)
 The arcs \\(a\\) and \\(b\\) will intersect \\(\iff\\) \\(-s_1\\), \\(s_2\\), \\(-s_3\\), and \\(s_4\\) are all of the same sign. In that case they intersect along \\(+\mathbf{t}\\) if they are all positive or along \\(-\mathbf{t}\\) if all are negative.
(Jason tests against \\(\epsilon = 10^{-6}\\), I implemented the test against the sign.)

If they do intersect, you can transform the corresponding vector, \\(\mathbf{t}\\) or \\(-\mathbf{t}\\) back into longitude and latitude (without worrying about its length.) Letting \\(x\\), \\(y\\), \\(z\\) be \\(\mathbf{t}\\)'s Cartesian coordinates this reverse transformation can be accomplished this way:

\\begin{aligned} 
\theta & = {\mathrm arctan2} (y,x) \\\\
\phi    & = {\mathrm arctan2}(z, \sqrt{x^2+y^2})
\\end{aligned} 

<iframe src="http://bl.ocks.org/espinielli/raw/00f6062b0324eac6f882/" marginwidth="0" marginheight="0" scrolling="no" width="800px" height="500px"></iframe>


### References ###
* [Roger Stafford's post][roger] on Matlab newsgroup  (but it lacks the normalization step which is instead used in Jason Davies code, and in Spherical Geometry Toolkit)
* [implementation in Python][geotoolkitintersect] as part of the [Spherical Geometry Toolkit][geotoolkit]
* Jason Davies [implementation][intersect] in D3.js


[roger]: http://www.mathworks.com/matlabcentral/newsreader/view_thread/276271 "Roger Stafford's on Matlab newsgroup"
[intersect]: http://www.jasondavies.com/maps/intersect/ "great circle arcs intersection"
[geotoolkitintersect]: http://ssb.stsci.edu/doc/stsci_python_x/stsci.sphere.doc/html/_modules/stsci/sphere/great_circle_arc.html "intersection"
[geotoolkit]: http://ssb.stsci.edu/doc/stsci_python_x/stsci.sphere.doc/html/ "spherical geometry toolkit"

> Written and published with [StackEdit](https://stackedit.io/).
