---
title: "Code 128 Barcode Optimization"
layout: post
date: 2019-10-24
---

The [code 128 barcode][wikipedia] allows text to be encoded
in a way that can be easily read and decoded by a barcode scanner.
The code consists of 107 symbols, each of which consists of 3 lines and 3 spaces
and has an overall length of 11 'bits' if the sequence is interpreted
as a binary pattern where the smallest bar or space width corresponds to one bit.

[wikipedia]: https://en.wikipedia.org/wiki/Code_128

Each symbol can be decoded in three different ways
depending on which of the, three start symbols is used,
and the code used can be changed partway through the barcode.
The three codes are called A, B, and C.
B can encode any digit or letter and some symbols.
A can encode uppercase letters, digits, and some ASCII control characters.
C can encode only digits, but it does so at twice the density as A and B
i.e. two digits per symbol instead of one.

If your barcode has many digits, it makes sense to use code C
and switch to code B only when a non-digit is encountered.
If it is mostly letters but has a string of consecutive digits,
it is sometimes still advantageous to switch to code C.
However, switching codes adds a symbol to the barcode,
and this overhead can eliminate the advantage of code C.
Code C also needs pairs of digits to operate on,
and leftover digits are ignored.

This makes deciding how to encode a given string
an interesting optimization problem.

The goal is to process a string into a barcode _payload_,
including symbols to set and change modes.
The barcode can be started in code B with `>:` or code C with `>;`.
In the middle of the barcode, the mode can be switched from B to C with `>6`,
and from C to B with `>5`.

For example, for the string `A123456B`, the `A` and `B` have to be in code B.
If we want the digits to be done in code C, we can switch into it before, 
and then back to B after.
So the resulting barcode will have the following symbol sequence:
`>:`, `A`, `>5`, `12`, `34`, `56`, `>6`, `A`.
There is also a check symbol and a stop symbol at the end,
but they are added automatically during rendering
and don't have to be included in the payload.

The strategy I came up with is to build a finite state machine
with a B state and a C state
that looks at the next chunk of the string,
and decides whether to switch modes or not.
If we need to switch modes, add a mode-switching symbol to the payload
and change to the other state.
If we can stay in the same mode,
just take the next chunk of characters from the string
and add them to the payload.

There are a few cases for different parts of the string.
If the string starts with four or more consecutive digits,
start in code C with `>;`, otherwise, start in code B with `>:`.
When in code C, take pairs of digits from the string and add them to the payload.
If a the next pair of characters contains a non-digit, switch to code B.
When in code B, take single characters from the string
and add them to the payload.
If the next six or more characters are digits, switch to code C.

This works fine for the beginning and middle of the string,
but there are some caveats at the end.
In the middle it's only advantageous to switch to code C
for six or more consecutive digits,
but at the end, there is no need to switch back to code B,
so only four consecutive digits are necessary to make code C more-efficient.

There is also an edge case involving odd numbers of digits.
Consider the string `ABC12345`.
It ends with 5 consecutive digits, so those should be encoded with code C,
but processing the string as outlined above results in a payload of
`>:`, `A`, `B`, `C`, `>5`, `12`, `34` with `5` remaining in the string.
The spare 5 can't be encoded with C because unpaired digits are ignored,
so we have to switch to code B: `>6`, `5`.
The resulting payload is 9 symbols long.

However, this string can be encoded with only 8 symbols
by shifting the digit pairing so that the `1` is included in the code B chunk,
which eliminates the need to switch codes again at the end of the string.
The optimal payload is `>:`, `A`, `B`, `C`, `1`, `>5`, `23`, `45`.

I'm not sure how to best handle this decision.
Making the decision at the point just after the three letters
have been added to the payload requires looking at the entire rest of the string
and seeing that it contains an odd number of digits.
Looking at the entire string at every step seems inefficient though.
There is also the possibility of backtracking.
After the end is reached with the 5 left over, go over the payload in reverse
until a code-change symbol is reached and move it forward one position.
If the beginning of the payload is reached, change the start symbol to code B,
and add a symbol to switch to code C after the first symbol.

I think implementing this solution would be a fun exercise,
but I'll leave that for another post.
