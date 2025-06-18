+++
title = "Graphs, Git, and Havel-Hakimi"
description = "Realizing degree sequences"
date = 2025-06-17
updated = 2025-06-17
draft = false

[taxonomies]
categories = ["GSoC"]
# tags = ["one", "two", "three"]

[extra]
lang = "en"
toc = true
comment = false
copy = true
outdate_alert = false
outdate_alert_days = 120
math = true
mermaid = false
featured = false
reaction = false
+++

## Havel-Hakimi

We're given a degree sequence — that is, the degrees of a set of nodes — and we want to build a graph out of it. How do we do that?

The [Havel-Hakimi algorithm](https://en.wikipedia.org/wiki/Havel%E2%80%93Hakimi_algorithm) gives a way to construct a *simple graph* from the degree sequence if one exists. 
> Say we are given the degree sequence `[3, 2, 3, 1, 1]`.
> 1. Find the highest degree $d$ in the sequence (in this case, $3$), and remove it or zero it out -> `[0, 2, 3, 1, 1]`
> 2. Subtract $1$ from the next $d$ largest degrees in the remaining list. This is akin to "satisfying" the largest degree by connecting each of its allotted edges to other nodes with the most remaining degree requirements -> `[0, 1, 2, 0, 1]`
> 3. Repeat until either: 
>    * One gets all zeros, indicating that a graph exists for that degree sequence. This is the case here: 
>       * `[0, 1, 2, 0, 1]` continuing from step 2 -> `[0, 1, 0, 0, 1]` zeroing out the highest degree -> `[0, 0, 0, 0, 0]` "satisfying" the largest degree
>    * One gets a negative number, or there are not enough numbers to subtract from, indicating that the degree sequence is not graphical

### Naïve Approach

I was told that the best way to learn is to *first* code up the most straightforward version of a program, and *then* sequentially improving upon it. So I did just that. A naïve Havel-Hakimi might look something like this:

```
while True:
    max = find max(sequence)
    sequence[max index] = 0

    if (max > len(sequence)):          return False    # not enough nodes
    if (any elements in sequence < 0): return False    # negative degrees
    if (all elements in sequence = 0): return True     # we're done

    for i in range(max):
        next largest deg = find max(sequence)
        next largest deg -= 1
```
It's a surprisingly simple algorithm. But there is one issue: **time complexity.** 

For a worst-case analysis of the time complexity, assume that the degree sequence is graphical (this means the program will not return `False` "early"). Let $N$ be the number of nodes and $E$ the number of edges in the resulting graph. We have that $N$ is the length of our list, and $E$ the sum of our list divided by two (as each edge contributes to two degrees). 

This double loop ...
```
while True:
    ...
    for i in range(max):
        next largest deg = find max(sequence)
        ...
```
... is the main bottle neck of this implementation. Conceptually, each iteration of the inner loop creates an edge, and the outer loop runs until all possible edges are created, so **the total number of iterations is exactly $E$.** Then, within each iteration, `find max(sequence)` must loop through the degrees of all nodes, and is thus an $\mathcal{O}(N)$ operation. Together, that makes an $\mathcal{O}(N\cdot E)$ implementation, which is slow for large graphs.

> Note that in simple graphs, we have $E \leq \frac{N(N-1)}{2}$. Thus, in the true worst case, we obtain a time complexity of $\mathcal{O}(N\cdot \frac{N(N-1)}{2})$ or $\mathcal{O}(N^3)$ (slow!)

### Slightly Better

```
while True:
    sort(sequence)
    max = sequence[0] 
    sequence[0] = 0                             # set highest degree to 0

    if (max > len(sequence)):          return False    # not enough nodes
    if (any elements in sequence < 0): return False    # negative degrees
    if (all elements in sequence = 0): return True 

    for i in range(max):
        degree seq[i+1] -= 1                       # next d largest degrees
```