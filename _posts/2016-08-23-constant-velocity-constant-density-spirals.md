---
layout:     post
title:      "Constant-Velocity, Constant-Density Spirals"
date:       2015-08-23 23:08
---

In confocal microscopy, a tightly focused illumination beam 
is scanned rapidly across the sample.  
For each illuminated point, the amount of scattered, transmitted, 
or fluorescent light is detected, 
and the image is reconstructed by mapping each light measurement
to the corresponding pixel.  

The scanning is usually done by mechanically actuated mirrors, 
which are limited in how quickly they can change positions by inertia.  
This limitation makes raster scanning, which is traditionally used for imaging,
extremely non-ideal.  

Better would be a scan pattern that moves the beam at a constant velocity, 
so we can move the mirrors at their maximum speed the entire time.  
It would also be nice to maintain a constant pixel density across the image.  

One way to achieve this would be to simply take a raster scan 
but reverse every other line so that the beam travels down one row of pixels, 
and then back down the next in the opposite direction 
without jumping to the beginning after every row.  
A different and novel way to achieve this would be a spiral scanning pattern.  

I set out to derive a constant-velocity, constant density spiral.  
I started with a simple Archimedian spiral \\( r = a + b\theta \\), 
or in parametric, \\( x = (a + bt)\cos{\omega t} \\), 
\\( y = (a + bt)\cos{\omega t} \\).  
