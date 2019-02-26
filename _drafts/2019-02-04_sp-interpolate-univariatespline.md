---
title:  "sp.interpolate.UnivariateSpline does not do what I expected"
date:   2019-02-25
layout: "post"
---

Wasted about an hour because I thought a `UnivariateSpline` would follow whatever you gave it nicely, but it just fits a cubic by default to the entire range no matter how many points you give it.
The way you get it to pass through every point is to add `s=0` which uses many splines spliced together until the result passes through every point.
