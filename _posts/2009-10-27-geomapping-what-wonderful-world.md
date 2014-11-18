---
layout: post
title: 'Geomapping: what a wonderful world!'
date: '2009-10-27T23:56:00.003+01:00'
author: enrico_spinielli
comments: true
tags:
- geomapping
- web services
modified_time: '2012-10-02T09:37:56.982+02:00'
blogger_id: tag:blogger.com,1999:blog-1947405997418753543.post-89677465580500019
blogger_orig_url: http://ongiantsshoulders.blogspot.com/2009/10/geomapping-what-wonderful-world.html
---

Suppose you have a set of lat/lon points, where do you find out their elevation?

Simple, let say you have the following coordinates (a cross in concrete on the top of the hill in front of my parents' place):

	45.469678, 10.970527 (I got these from 'Get Directions from here' in google maps)

Then call the relevant [USGS' web service](http://www.blogger.com/getElevation?Y_Value=45.469678&amp;X_Value=10.970527&amp;Elevation_Units=METERS&amp;Source_Layer=-1&amp;Elevation_Only=true)

It says

	getElevation
	   for Latitude 45.46                   --> Y_Value=45.469678
	   for Longitude 10.97                --> X_Value=10.970527
	   return result in meters            --&gt; Elevation_Units=METERS
	   use the best survey available --&gt; Source_Layer=-1
	   not other additional info        --&gt; Elevation_Only=true


Why would you want elevations?

To plot an elevation chart of a bike or hiking tour.

Amazing what you can find around...

(This was inspired by [http://econym.org.uk/gmap/altitude.htm](http://econym.org.uk/gmap/altitude.htm))