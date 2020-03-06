---
title: "Code 128 Barcode Optimization, Part 2"
layout: post
date: 2020-01-18
---

My [last post](/2019/10/24/code128-barcode-optimization.html) described the code128 barcode format
and how to optimize the encoding of a barcode payload for minimum barcode length.
This post describes how I implemented the described algorithm in rust.
Please read the previous post to understand the problem and context of this solution.

In order to decide whether to switch code subsets at a given point in the payload,
you need to look ahead several characters to see if there are enough
of the same character in a row to make the switch worth it.
To decide whether to switch from code B to code C
requires scanning all the way to the end of the string
to see if there are an even or odd number of characters.
Looking ahead to the end of the string
at each point in the payload seemed wasteful to me.
It turns a problem that should be \\(O(n)\\) into \\(O(n^2)\\),
making it inefficient for large payloads.
(Do we expect 1000-character barcodes?
No, but I'm not letting that keep me from optimizing prematurely.)

Rather than trying to do the encoding all in one pass through the string
and requiring inefficient lookahead,
I had the idea of making two passes.
The first pass breaks the string into chunks
that are all the same character type.
The three types are numbers (which code subset C is great for),
control characters (which can only be encoded in subeset A),
and all other characters including lowercase letters
(which are only found in subset B).

Once the string has been broken into chunks,
a second pass over the chunks is made.
The length of each chunk has already been determined and is easy to check,
so there is no need to step through to the end of the string
to determine whether to switch encodings.
In addition, the number of potential decision points is reduced.
Making only one pass would require deciding whether to switch encodings
at every position in the entire payload.
When the string is pre-chunked,
the only places where switching encodings needs to be considered
are the boundaries between the chunks.

With the overall strategy in mind,
I searched for more ways to make my life difficult.
The easy way to implement this would be to go through the chunking step first
and store the chunks off for later.
Then once that's done, go through the chunks to do the actual encoding.
However, for each encoding decision,
the only information necessary is the current encoding,
the character type of the next chunk, and its length.
It isn't necessary to have all the chunks at once,
so we can operate on one at a time!
This means we can (and therefor should, obviously) break a chunk of the string,
then send it to the encoder which makes a decision
and outputs the encoded string.
Meanwhile, another chunk has been created, and can be encoded.
In other words, instead of doing all the chunking then all the encoding,
we alternate between chunking and encoding.
Chunks are encoded as they're produced.

This seems harder to implement, and it is,
but it is made easier by Rust's `iterator` trait.
I can define a type that does the chunking
and implement the iterator trait for it
so that calling `next` on it will compute
and deliver the next chunk of the string.
Rust's iterators are lazy, so
the chunks aren't computed until they are needed.
These chunks are consumed by the encoder which asks for the next chunk
(via the `next` method),
then makes an encoding decision based on that,
and writes the next part of the encoded string to the output.
So the computation timeline goes break off a chunk, encode that chunk,
break off another chunk, encode it, etc.

This back-and-forth can be seen by adding some ~~`printf`~~`println!` debugging
and observing the output for a string with several sequences
of letters and digits.
Below is the output for the payload
`1000ml water, 6 eggs, 25ml salt, and 1298426 grains of flour`

```
parsed token: Digits[1, 0, 0, 0]
adding 1 to payload
adding 0 to payload
adding 0 to payload
adding 0 to payload
parsed token: Chars[m, l,  , w, a, t, e, r, ,,  ]
adding m to payload
adding l to payload
adding   to payload
adding w to payload
adding a to payload
adding t to payload
adding e to payload
adding r to payload
adding , to payload
adding   to payload
parsed token: Digits[6]
adding 6 to payload
parsed token: Chars[ , e, g, g, s, ,,  ]
adding   to payload
adding e to payload
adding g to payload
adding g to payload
adding s to payload
adding , to payload
adding   to payload
parsed token: Digits[2, 5]
adding 2 to payload
adding 5 to payload
parsed token: Chars[m, l,  , s, a, l, t, ,,  , a, n, d,  ]
adding m to payload
adding l to payload
adding   to payload
adding s to payload
adding a to payload
adding l to payload
adding t to payload
adding , to payload
adding   to payload
adding a to payload
adding n to payload
adding d to payload
adding   to payload
parsed token: Digits[1, 2, 9, 8, 4, 2, 6]
adding 1 to payload
adding 2 to payload
adding 9 to payload
adding 8 to payload
adding 4 to payload
adding 2 to payload
adding 6 to payload
parsed token: Chars[ , g, r, a, i, n, s,  , o, f,  , f, l, o, u, r]
adding   to payload
adding g to payload
adding r to payload
adding a to payload
adding i to payload
adding n to payload
adding s to payload
adding   to payload
adding o to payload
adding f to payload
adding   to payload
adding f to payload
adding l to payload
adding o to payload
adding u to payload
adding r to payload
```

And the resulting payload is `>;`, `10`, `00`, `>6`, `m`, `l`, `` ``, `w`, `a`, `t`, `e`, `r`, `,`, `` ``, `6`, `` ``, `e`, `g`, `g`, `s`, `,`, `` ``, `2`, `5`, `m`, `l`, `` ``, `s`, `a`, `l`, `t`, `,`, `` ``, `a`, `n`, `d`, `` ``, `1`, `>5`, `29`, `84`, `26`, `>6`, `` ``, `g`, `r`, `a`, `i`, `n`, `s`, `` ``, `o`, `f`, `` ``, `f`, `l`, `o`, `u`, `r`,

The code is [available on github](https://github.com/robbystk/code128-encoder),
go check it out!
