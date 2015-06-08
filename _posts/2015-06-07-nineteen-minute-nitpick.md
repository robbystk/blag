---
layout:     post
title:      "Nineteen-Minute Nitpick"
date:       2015-06-07 21:40
---

I recently finished [Nineteen Minutes by Jodi 
Picoult](https://www.goodreads.com/book/show/14866.Nineteen_Minutes).  I really 
enjoyed it, especially for the way it examined conformity, fitting in, and life 
in general from many different angles and perspectives.  The human aspect is 
great, but there are some distracting technical errors that I'm surprised made 
it through the proofreading and editing process.  For example, the following 
(apparently) C++ program on page 314 (of my edition):

{% highlight C++ %}
# include <stdio.h>
main ( )
{
    int time;
    for (time=0; time<infinity (1) ; time ++)
    { printf ("I love you|n"); }
}
{% endhighlight %}

Even I spotted a few problems with this, despite having minimal experience 
with C++.  `infinity()` may be defined outside this excerpt, but it probably 
isn't.  There's an `infinity()` function in the `numeric_limits` class template,
but `# include <limits>` is conspicuously missing.  And I don't have any idea 
why a function to produce infinity would need 1 as an argument.  Also, (this 
is probably a typo) `|n` at the end of the printed string is not going to have 
the desired effect.  

These are minor, and most readers won't notice, but I'm surprised that nobody
thought to throw this into a compiler during the entire process of preparing
the book for publication.  
