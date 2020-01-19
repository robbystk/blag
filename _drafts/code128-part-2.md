---
title: "Code 128 Barcode Optimization, Part 2"
layout: post
date: 2020-01-18
---

My [last post](2019/10/24/code128-barcode-optimization.html) described the code128 barcode format
and how to optimize the encoding of a barcode payload for minimum barcode length.
This post describes how I implemented the described algorithm in rust.
Please read the previous post to understand the problem and context of this solution.

In order to decide whether to switch code subsets at a given point in the payload,
you need to look ahead several characters to see if there are enough
of the same character in a row to make the switch worth it.
Looking ahead several characters at each point in the payload seemed wasteful to me,
especially since you have to go all the way to the end of the payload (?).
This turns a problem that should be $O(n)$ into $O(n^2)$, making it expensive for large payloads.
(Do we expect 1000-character barcodes? No, but I'm not letting that keep me from optimizing prematurely.)

Rather than trying to do the encoding all in one pass through the string
and requiring potentially expensive lookahead,
I had the idea of making two passes.
The first pass breaks the string into chunks that are all the same character type.
The three types are numbers (which code subset C is great for),
control characters (which can only be encoded in subeset A),
and all other characters including lowercase letters (which are only found in subset B).

Once the string has been broken into chunks, a second pass over the chunks is made.
The length of each chunk has already been determined,
so there is no need to step through to the end of the string to determine whether to switch encodings.
In addition, the number of potential decision points is reduced.
Making only one pass would require deciding whether to switch encodings
at every position in the entire payload.
When the string is pre-chunked, the boundaries between the chunks
are the only places where an encoding switch needs to be decided on.

With the overall strategy in mind,  I needed another way to make my life difficult.
The easy way to implement this would be to go through the chunking step first
and store the chunks off for later.
Then once that's done, go through the chunks to do the actual encoding.
However, for each encoding decision, the only information necessary is the current encoding,
the character type of the next chunk, and its length.
It isn't necessary to have all the chunks at once, so we can operate on one at a time!
This means we can (and therefor should, obviously) break a chunk of the string,
then send it to the encoder which makes a decision and outputs the encoded string.
Meanwhile, another chunk has been created, and can be encoded.
In other words, instead of doing all the chunking then all the encoding,
we alternate between chunking and encoding.
Chunks are encoded as soon as they're produced.

This seems harder to implement, and it is, but it is made possible by Rust's `iterator` trait.
I can define a type that does the chunking and implement the iterator trait for it
so that calling `next` on it will compute and deliver the next chunk of the string.
The chunks aren't computed until they are needed.
These chunks are consumed by the encoder which asks for the next chunk,
then makes an encoding decision based on that,
and writes the next part of the encoded string to the output.
So the computation timeline goes break off a chunk, encode chunk, break off a chunk, encode chunk, etc.

Implementing this required some careful typing and stuff. 
