---
layout: post
title:  Bottlenecking for Fun & Profit
date:   2018-05-24
categories: [machine-learning, writing]
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
Any light that doesn't fall in the exact center of the pinhole is blocked,
and only the light in the center passes through the pinhole.
Conveniently, the light in the center corresponds to a clean Gaussian mode,
while the light outside corresponds to deviations from this ideal.
With the extra light blocked, a clean spatial mode is created after
being re-collimated by the second lens.
This procedure is not entirely analogous,
but it is interesting that it also involves filtering the beam
through a single point, like the single neuron or single dimension above.

Lastly I mentioned that this concept applies to writing as well.
I've found that when revising a paper or article or post,
one tends to focus in on a single sentence or two and modify them
without consideration for how they fit into the rest of the paragraph.
Too much of this leaves little discontinuities between parts
that interfere with reading and comprehension of the whole piece.

Reading the work out-loud is very helpful for finding these sharp edges,
but an even more powerful and drastic technique is
to read through and produce an outline which very briefly captures
the main ideas of each paragraph or section in just a few sentences.
Once that's done, the outline may be rearranged if necessary
to produce a nice organizational flow of ideas,
and then the entire thing should be rewritten from scratch
based only on the outline.

[comment]: # (This transition is shit)
It's even better if you wait a few days between making the outline and re-writing,
to avoid re-using the same phrases again.
Some things will likely come out the same phrasing---which is fine---but
most things will change to simpler, more-concise language.
Another benefit is that rewriting the work in a single pass from top to bottom
will produce a smooth final product with good transitions
and a logical ordering of ideas.
It will be completely free of those jagged edges that piecewise revision produces.

[comment]: # (I should probably apply this to this very post)
This technique doesn't take as long as you might expect,
because writing and revising the initial version will have allowed you to think through
all the arguments, points, and supporting evidence necessary to convey each idea,
and the outline re-ordering allows high-level organization.
Since you've already gone through the process once,
the second round of translating ideas into words will be much easier.

Notice that this strategy is similar to the above techniques---data
are filtered through a small, incomplete representation
and then reassembled from there in order to extract their essential characteristics.
Doing this not only extracts the most-important aspects,
but also smooths out many imperfections, producing a pure sample.

I hope this technique may be useful to you.
I would be interested to hear if this concept of `bottlenecking'
shows up in any other areas.

[comment]: # (This brings to mind Ben Franklin's writing practice also)
