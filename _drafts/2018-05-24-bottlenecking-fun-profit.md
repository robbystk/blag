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
by
