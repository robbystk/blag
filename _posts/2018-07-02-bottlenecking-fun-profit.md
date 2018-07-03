---
layout: post
title:  Bottlenecking for Fun & Profit
date:   2018-07-02
---

I have recently noticed that wildly different-seeming problems
in the areas of data analysis, optics, and writing
all have similar-looking solutions.
They each involve squishing something through a tiny passage (bottleneck)
to clean and purify it by removing all of the unnecessary parts.

In data analysis, one often encounters high-dimensional data
which is difficult to work with due to its complexity.
Sometimes, the data cannot even be represented
due to computational resource limits (not enough RAM).
A technique called principal component analysis (PCA)
is frequently used to reduce the dimensionality of a data set
so that it can be represented by fewer measurements.
It works by projecting the data into a lower-dimensional space
consisting of the first \\(N\\) principal components.
These components are constructed such that
the first component captures as much as possible of the variation in the data,
the second captures as much of the remaining variation as possible, and so on.
The result is that the data can be represented much more compactly
while maintaining as much accuracy as possible.
By squeezing the data into a small subspace,
its essence can be maintained while improving its amenability to analysis.

In optics, diffraction is the enemy.
To minimize the diffraction of a beam,
its intensity profile or _spatial mode_ should be a pure Gaussian
without any bumps, fringes, or other abnormalities.
An imperfect beam can be cleaned up by passing it through a _spatial filter_,
in which a lens focuses the light through a tiny pinhole
which blocks everything except the light in the very center.
The light that makes it through is re-collimated by a second lens
into a nice clean Gaussian profile,
resulting in a beam that can travel long distances without widening significantly.
The pinhole actually removes whatever ripples or anomalies are present,
leaving only the pure Gaussian mode.

Lastly, in writing, many revisions are usually necessary before a piece is finished,
but revision of small parts without sufficient regard for their context
results in discontinuities between parts.
Also, one often wants to re-order whole sections for organization,
but the sections were originally written in a specific order,
and if they are rearranged, the transitions from one to the next
become jarring or at least more-noticeable.
A remedy for both of these problems is to condense the entire article
into a very brief outline.
Once the outline is complete, it can be rearranged as necessary
for better logical organization.
Then, using only the outline, the piece can be rewritten entirely from beginning to end,
producing a coherent, smooth, _organized_ work.
Since the piece has already been written once,
the second round doesn't take as long as you might expect,
because all the questions about phrasing, organization,
and what the important points are have already been considered
and answered previously during the first draft.

I suspect that this process may be easier
than trying to reorganize an already-written piece
and constantly fighting with rough transitions between displaced sections.
I also think it produces a fresher result, because the baggage
of the first draft is discarded so word and phrase choices can be made anew.
By forcing the ideas through a minimal representation,
they come out better-organized, better-written, and perhaps more-understandable.

Does this method of squeezing things through a tight space
appear in any other fields?
I am curious to find out.

---

P.S. I used this method on this very post, and it shrunk by over 30%.
