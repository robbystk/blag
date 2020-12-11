---
layout: post
title: Advent of Code 2020 Day 10 Part 2 Generalization
date: 2020-12-10 13:45 -0700
comments: true
---

I fell down a bit of a rabbit hole after I finished
[part 2 of day 10 of Advent of Code][1].
My solution broke the problem into several instances
of a slightly different sub-problem.
These were simple enough to reason about by hand,
which let me come up with a formula for the smallest few
(\\(n < 4\\)).
I knew the formula didn't generalize to bigger examples,
but the puzzle didn't require any that weren't covered by my formula.
It bugged me that my solution wasn't general,
so after solving the puzzle,
I set out to determine a solution to the sub-problem for all \\(n\\).

[1]: https://adventofcode.com/2020/day/10#part2

The original puzzle boils down to determining how many ways there are
to skip some numbers in a strictly increasing sequence of positive integers
so that the gap (difference) between any two consecutive numbers
is no greater than 3.

For example, given the sequence 0, 1, 4, 5, 6, 7, 10, 11, 12, 15, 16, 19, 22,
there are eight ways to skip some of the numbers
and keep the largest gap no bigger than 3.
This is easier to think about when looking at the gaps themselves.
For the above sequence, the differences between each consecutive number
are 1, 3, 1, 1, 1, 3, 1, 1, 3, 1, 3, 3.
Skipping a number (such as the five) means the gaps on either side of it
combine into a wider gap.
When the five is skipped, the second and third ones in the gap sequence
merge into a two, producing 1, 3, 2, 1, 3, 1, 1, 3, 1, 3, 3.

Some things to notice:
1. In the original sequence, there are only gaps of 1 and 3, but none of 2
2. The numbers on either side of a gap of three can't be skipped,
since that would produce a gap larger than three, which isn't allowed

Because of these two things, a string of \\(n\\) ones in the sequence of gaps
indicates \\(n-1\\) numbers that are candidates for skipping.
The original sequence will have several stretches
of consecutive skippable numbers
(corresponding to stretches of 2 or more ones in the sequence of gaps).
The number of ways to skip numbers in the original sequence
is the product of the number of ways to skip numbers
in each of these stretches of consecutive skippable numbers.

Based on this we can reduce the problem
to determining how many ways there are to skip numbers other than the first and last
in a sequence of \\(n+1\\) _consecutive_ integers
so that the largest gap is no bigger than 3.

For example, if \\(n = 4\\), we have (0), 1, 2, 3, (4).
We can skip 1, 2, or 3, and there are seven ways to do that ensuring no gap larger than three:
- we can skip none of the numbers---there's one way to do that (\\(3 \choose 0\\))
- we can skip one, and there are three ways to do that (\\(3 \choose 1\\))
- we can skip two numbers in three different ways (\\(3 \choose 2\\))
- we can't skip all three because that would leave a gap of 4

I noticed the presence of \\(n-1 \choose k\\) for \\(k = 0, 1, 2\\)
which let me write a formula for \\(n\\) between 1 and 4:
\\[
    \sum_{k=0}^2 {n-1 \choose k}
    = {n-1 \choose 0} + {n-1 \choose 1} + {n-1 \choose 2}
    \quad 1 \leq n \leq 4
\\]

It doesn't work for larger \\(n\\) though,
for example with \\(n = 5\\) we have (0), 1, 2, 3, 4, (5).
The formula above predicts 11 ways to skip numbers, but there are 13.
The reason is that we can now skip 3 numbers,
but we have to be careful to avoid producing a gap of 4.
We can skip 1, 2 and 4, leaving 0, 3, 5; or skip 1, 3 and 4, leaving 0, 2, 5.
There are only two ways to skip 3 numbers
rather than the four given by \\(4 \choose 3\\).

I don't know of a formula for the number of ways
to choose three numbers to skip out of four given the gap constraint.
The situation gets even more complicated as \\(n\\) increases,
for example for \\(n = 6\\), we could skip 5 numbers but only one way (0, 3, 6),
and skip four numbers 12 ways instead of \\({6 \choose 4} = 15\\).

Alan Kay said that "Point of view is worth eighty IQ points",
and it turns out that there is an easier way of looking at this problem.
If we take \\(n = 3\\), we have (0), 1, 2, (3),
and it's easy to see that there are four ways to skip 1 and 2.
The sequences of gaps for each case are: [1,1,1], [1,2], [2,1] and [3].
In all cases the sequence of gaps sums to 3.
This problem is thus equivalent to determining
how many ways there are to add 1s, 2s, and 3s to get \\(n\\).

Once the problem is cast that way,
it's easy to come up with a recursive formula
for the number of ways to skip numbers.
Let's call the number of ways to add 1s, 2s, and 3s to get \\(n\\) \\(f(n)\\).
Now consider starting the sum with a 1.
The rest of the sum needs to be \\(n-1\\), so there are \\(f(n-1)\\)
ways to construct it.
If the sum starts with a 2, there are \\(f(n-2)\\),
and if it starts with a 3, there are \\(f(n-3)\).
Therefore \\(f(n) = f(n-1) + f(n-2) + f(n-3)\\).
This is similar to the Fibonacci sequence,
except that we sum the previous three numbers instead of the previous two.

It's easy to see that \\(f(0) = 1\\), \\(f(1) = 1\\), and \\(f(2) = 2\\),
so we can compute \\(f(n)\\) for all \\(n\\).
The first several values of \\(f(n)\\) are 1, 1, 2, 4, 7, 13, 24, 44 . . .
Punching these into [The OEIS][2] reveals the name for this sequence:
[The Tribonacci numbers (OEIS A000073)][3].
The connection to sums of 1s, 2s, and 3s is pointed out by Emeric Deutsch
in the second comment.

[2]: https://oeis.org/
[3]: https://oeis.org/A000073

From the original problem in Advent of Code day 10 part 2
we get a family of sub-problems
whose solutions turn out to be Tribonacci numbers.
Finally, my curiosity is satisfied.
