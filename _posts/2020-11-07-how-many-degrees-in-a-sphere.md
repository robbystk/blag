---
layout: post
title: How Many Degrees in a Sphere?
date: 2020-11-07 18:41 -0700
comments: true
---

I was listening to something that talked about,
"looking in every direction---360 degrees around".
This bothered me, because 360 degrees isn't every direction.
Up and down are missed, for example.
This got me wondering:
if there are 360 degrees in a circle, how many are in a sphere?
Of course, for a sphere, they will have to be [square degrees] instead.
\\( \def\dd{\mathrm{d}} \\)

[square degrees]: https://en.wikipedia.org/wiki/Square_degrees

Imagine a small square patch on the surface of a sphere, one degree wide,
and one degree tall.
The angle taken up by the patch is a square degree.
If the sphere has a radius of 1 unit,
then each side of the square will be
\\( \frac{\pi}{180} \approx 0.0174 \\) units,
giving an area of
\\( \left(\frac{\pi}{180}\right)^2 \approx 304\times 10^{-6} \\) square units.
However many times that area fits into the sphere's total surface area
is the number of square degrees in a sphere.
The full sphere has an area of \\( 4\pi \\) square units,
so there are
\\( 4\pi / \left(\frac{\pi}{180}\right)^2 = \frac{4\cdot180^2}{\pi}
= \frac{360^2}{\pi} \approx 41\,253 \\)
square degrees in a sphere.

I was really surprised to see \\( \pi \\) in that result.
There are exactly 360 degrees in a circle,
why should \\( \pi \\) show up for a sphere?
Perhaps finding the answer a different way
will explain where the \\( \pi \\) comes from.

Now imagine a differential rectangular patch of the sphere,
located at \\( (\theta,\phi) \\) in spherical coordinates
(with \\( \theta \\) being the azimuthal angle
and \\( \phi \\) being the polar angle).
Its vertical dimension is \\( r\,\dd\phi \\),
and its horizontal dimension is \\( r\,\sin\phi\,\dd\theta \\).
Those measurements are lengths,
but we are interested in the angular size in square degrees.
We can divide by \\( r \\) to get the angles in radians,
convert to degrees, and then multiply the angles together to get square degrees.
In this way, the area of the rectangular patch is
\\( \frac{180}{\pi}\sin\phi\,\dd\theta \times \frac{180}{\pi}\,\dd\phi
= \left(\frac{180}{\pi}\right)^2\sin\phi\,\dd\theta\,\dd\phi \\) square degrees.

To get the total number of square degrees,
we integrate all the patches in the whole sphere:
\\[
    \int_{\theta=0}^{2\pi} \int_{\phi=0}^\pi
        \left(\frac{180}{\pi}\right)^2\sin\phi\,\dd\phi\,\dd\theta
    = \left(\frac{180}{\pi}\right)^2\int_{\theta=0}^{2\pi} \int_{\phi=0}^\pi
        \sin\phi\,\dd\phi\,\dd\theta.
\\]

The integrals separate, and the result is the same as above.
\\[
    = \left(\frac{180}{\pi}\right)^2\int_{\theta=0}^{2\pi} \dd\theta
        \int_{\phi=0}^\pi \sin\phi\,\dd\phi
    = \left(\frac{180}{\pi}\right)^2 2\pi \left[-\cos\phi\right]\_{\phi=0}^\pi
    = \left(\frac{180}{\pi}\right)^2 2\pi\, 2 = \frac{360^2}{\pi}
\\]

Note that before integrating,
we had two \\( \pi \\)s in the denominator to get rid of.
The integral over \\( \theta \\) comes out to \\( 2\pi \\)
and cancels one of them,
but the integral over \\( \phi \\) has the \\( \sin \\) in it,
and does not produce a \\( \pi \\).
The result is that only one of the \\( \pi \\)s gets cancelled,
leaving a \\( \pi \\) in the denominator.
