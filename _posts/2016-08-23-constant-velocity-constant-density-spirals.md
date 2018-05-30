---
layout:     post
title:      "Constant-Velocity, Constant-Density Spirals"
date:       2016-08-23 23:08
comments:   true
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
I started with a simple Archimedian spiral \\( r = r_0 + b\theta \\), 
or in parametric, \\( x = (r_0 + b\omega t)\cos{\omega t} \\), 
\\( y = (r_0 + b\omega t)\sin{\omega t} \\)
where we have made \\(\theta = \omega t\\). 
For simplicity, we will condense constants and examine 
\\[ x = at \cos{\omega t} \\] \\[ y = at \sin{\omega t} \\]

\\(
    \def\ud{\mathrm{d} }
\\)

The problem with this is that as the radius increases, 
the angular velocity \\(\omega\\) stays the same, 
so that the actual velocity \\(v = \omega r\\) increases with \\(r\\). 
This can be seen even more clearly by finding 
\\[v = \sqrt{ {\left ( \frac{\ud x}{\ud t} \right ) }^2 
\+ {\left ( \frac{\ud x}{\ud t} \right ) }^2 } = a \sqrt{1 + t^2} \\]
Thus velocity increases linearly with \\(t\\) 
and will soon spiral out of control.  

A slightly better result comes by setting 
\\(\theta = \omega\sqrt{t}\\) resulting in 
\\[v = \frac{a}{2} \sqrt{\frac{1}{t} + \omega^2} \\]
The velocity is flatter with respect to time, but starts out high and drops 
so that the majority of the spiral is scanned below the maximum speed. 

Thinking about this problem, I pictured something like 
[this video](https://www.youtube.com/watch?v=dmCQkosIa2k&t=18). 
A self-propelled lawnmower tied to a post traces out a contracting spiral 
as the rope winds around the post. 
the lawnmower maintains constant tangential velocity, 
which makes for changing angular velocity. 
The lawnmower appears to speed up as it reaches the center
as the angular velocity goes through the roof. 

As it turns out, there is a mathematical name for the path traced 
by the end of a string winding or unwinding around another shape: 
[the involute](https://en.wikipedia.org/wiki/Involute). 

For this we want the string to wind around a circle, which is given by
\\[x = a\cos\theta + a\theta\sin\theta\\]
\\[y = a\sin\theta - a\theta\cos\theta\\]
where \\(a\\) is the radius of the circle.  
Using \\(\theta = at\\) will obviously not work as we've already seen, 
but \\(\theta = \omega\sqrt{t}\\) was promising. 

As it turns out, something very interesting happens to the derivative. 
For x: 
\\[\frac{\ud x}{\ud\theta} = -a\sin\theta + a\sin\theta + a\theta\cos\theta 
= a\theta\cos\theta\\]
\\[\dot{x}\equiv\frac{\ud x}{\ud t}
= \frac{\ud x}{\ud\theta}\frac{\ud\theta}{\ud t}
= a\theta\cos\theta\frac{\ud\theta}{\ud t} 
= a\omega\sqrt{t}\cos\omega\sqrt{t} \frac{\omega}{2\sqrt{t} }
= \frac{a\omega^2}{2}\cos\omega\sqrt{t} \\]
Similarly, \\(\dot{y}\\) turns out to be 
\\(\frac{a\omega^2}{2}\sin\omega\sqrt{t}\\)
Combining these gives
\\[v = \sqrt{\dot{x}^2 + \dot{y}^2} 
= \sqrt{\left(\frac{a\omega^2}{2}\right)^2\cos^2\omega\sqrt{t}
\+ \left(\frac{a\omega^2}{2}\right)^2\sin^2\omega\sqrt{t} }
= \frac{a\omega^2}{2} \\]
Which has no time dependence! 

Now what about the density? 
The radial spacing of each turn of the spiral 
is the difference in \\(x\\) or \\(y\\) 
when \\(\theta\\) increases by \\(2\pi\\). 
A little work reveals \\(\Delta r = 2\pi a\\). 
The tangential spacing depends on the sample rate \\(\Delta t\\)
and is simply \\(\Delta s = v\Delta t = \frac{a\omega^2}{2}\Delta t\\). 
With these relationships, it is always possible 
to find a constant density spiral given maximum velocity and sample rate. 
