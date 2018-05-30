---
layout:     post
title:      "When to use FFTshift in MATLAB"
date:       2017-05-31 20:31
comments:   true
---

After much research and experimentation, I have determined the proper way
to take forward and inverse Fourier transforms in matlab.  The forward
transform is:

    spectrum = fftshift(fft(ifftshift(signal)));

And the inverse transform is:

    signal = fftshift(ifft(ifftshift(spectrum)));

I found [this discussion](https://www.dsprelated.com/showthread/comp.dsp/128035-1.php) the most helpful.

# A thorough explanation #
There are two reasonably sane ways to store real data in an array.  They
only differ in where negative values are placed, so we'll only use
examples where the independent variable (time, frequency, etc.) has
positive and negative values, e.g. integers from -5 to 5.

The first way (and the way I prefer) is to place the value for the
lowest time value in the first element of the array, and continuing in
order.  That is, 

    [-5, -4, -3, -2, -1, 0, 1, 2, 3, 4, 5]

I will use the nomenclature of Chris in the discussion linked above,
and call this scheme 'natural'.
One could also use 'monotonic'.

The second way (and the way used by most implementations of the fft algorithm) is to place the
value for time (or frequency, or whatever) 0 in the first element of the
array.  Then continue to the largest time, and then start at the
smallest time and proceed to the one just before zero.  Using the same
example as above,

    [0, 1, 2, 3, 4, 5, -5, -4, -3, -2, -1]

Again using Chris's nomenclature, I will call this the 'shifted' form.

These methods both contain all the same data, and it is trivial to
convert between them losslessly.  Neither is better--they both have
their strengths and applications.  The only pitfall is using one when
the other is expected, as is commonly encountered when taking Fourier
transforms.

The first time you plotted an FFT in matlab, you might have noticed that
the DC coefficients weren't where you expected them to be.  Instead of
being in the center of the plot, they are at the beginning and end.
This is because matlab's `fft()`, and many other fft implementations,
output data in the shifted form, and we expect to view it in the natural
form with DC (zero frequency) in the center.  Luckily, matlab includes a
helpful function called `fftshift()` to convert shifted data into the
natural format.  It also includes `ifftshift()` to undo that shift and
convert natural data into the shifted format.

That is only half of the story, though.  `fft()` also expects its input to
be in shifted form, with the zero time value in the first element of the
array.  Many people realize this, and so they use
`fftshift(fft(fftshift(signal)))`, but this will cause problems if the
number of points is odd.  (You can try it for yourself on some familiar
function like a rectangle, or a gaussian.)

To see why, remember that `fftshift()` converts from shifted to natural,
and `ifftshift()` converts from natural to shifted.  If we start with
natural-format data, and run `fftshift()` on it, it will be converted from
shifted to natural again!  We actually want to run `ifftshift()` to
convert it from its natural state into the shifted format that `fft()`
wants.

This subtlety is often missed because `fftshift()` and `ifftshift()` appear
very similar, and in fact only behave slightly differently (one point)
on data with an odd number of points, and they are actually equivalent
for even numbers of points.

In the shifted format, the center point (5 above) is the highest time (or
frequency) value (see the examples above).  `fftshift()` will move it to
the last element in the array, so that it is with all the other points
after zero.  `ifftshift()` takes the center point (0 above) and moves it to the
first element, because the center element is the 0 time (or frequency)
value in the natural format, and must be placed at the beginning for the
shifted format.  Running one where the other is needed will put the
center point at the wrong end of the array, and repeated application
will slowly circularly shift the array around.  Thus it is important to
use the correct function depending on what you are trying to do.  This
technically means that `ifftshift()` only needs to be used on data with an
odd number of points, but to maintain generality, it is recommended that
`ifftshift()` is always used.

In order to avoid having to remember all this, and to avoid mistakes,
you may want to define some functions that will take care of all the
shifting for you, such as:

    F2 = @(signal) fftshift(fft2(ifftshift(signal)));
    IF2 = @(spectrum) fftshift(ifft2(ifftshift(spectrum)));

For a two-dimensional Fourier transform.
