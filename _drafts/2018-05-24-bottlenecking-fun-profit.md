---
layout: post
title:  Bottlenecking for Fun & Profit
date:   2018-05-24
categories: machine-learning, writing
---

There is a phenomenon/concept/strategy that shows up in optics, data analysis,
machine-learning, and---I argue---writing.

[comment]: # (This might be way too formal)
In data science, a technique called principal component analysis
is often used to reduce the dimensionality of data.
It works by finding the best way to express the data
as the sum of several components,
so that the most variation is captured by the first component,
the next component captures as much of the remaining variation as possible,
and so on.
Once the components are determined,
the first several capture most of the variation of the original data,
allowing it to be reconstructed using fewer components but minimal error.

One way to consider this transformation is as a projection into
a low-dimensional space followed by re-expansion into the ambient space
in which the data lives.
The projection squeezes the data down to a small representation,
and the reconstruction re-inflates it to the same 'size' it was before.

This same analysis can be accomplished by a neural net
by constructing an hourglass-shaped network
which narrows down to a single neuron,
then expands back out to the same size as the input.
The neural net is trained to reproduce the input as accurately as possible.
Since everything goes through a single neuron,
only one component of the data will be captured,
and the component which produces the highest accuracy
will be the one that captures the most variation in the data.
To determine the second most-important component,
a second neuron is added, and so on.

[comment]: # (This transition suuuucks)
This concept is formally and rigorously defined in data science
and machine learning, but less so in other areas.
[comment]: # (It's totally rigorously defined in optics though)
In optics and microscopy, one often desires a beam with a nice, clean, Gaussian
intensity profile that is free of ripples and bright or dim areas.
To clean up a beam's profile, or _spatial mode_,
the beam is focused tightly onto a pinhole using a lens,
then re-collimated on the other side by another lens.
Any light that doesn't fall in the exact center of the 
