---
layout: post
title:  "scipy.interpolate.UnivariateSpline does not do what I expected"
date:   2019-05-19
---

Wasted about an hour because I thought a [`UnivariateSpline`][1]
would follow whatever you gave it nicely,
but it just fits a cubic by default to the entire range
no matter how many points you give it.
The way you get it to pass through every point
is to add `s=0` which uses many splines spliced together
until the result passes through every point.

[1]: https://docs.scipy.org/doc/scipy/reference/generated/scipy.interpolate.UnivariateSpline.html

This image should illustrate the difference in behavior:
![Spline behavior](/assets/splines.png)

This came up because I was trying to compute the [full width at half-maximum][2]
from some samples.
[This answer][3] led me to `UnivariateSpline`, but I missed the `s=0`.

[2]: https://en.wikipedia.org/wiki/Full_width_at_half_maximum
[3]: https://stackoverflow.com/questions/10582795/finding-the-full-width-half-maximum-of-a-peak/10583774#10583774
