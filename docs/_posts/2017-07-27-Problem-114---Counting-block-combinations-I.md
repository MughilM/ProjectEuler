---
layout: post
title: "Problem 114 - Counting block combinations I"
date: 2017-07-27 16:35
mathjax: true
number: 114 
---

> A row measuring seven units in length has red blocks with a minimum length of three units placed on it, such that any two red blocks (which are allowed to be different lengths) are separated by at least one grey square. There are exactly seventeen ways of doing this.
>
> ![p114]({{ site.url }}{{ site.baseurl }}/assets/Images/p114.png)
>
> How many ways can a row measuring fifty units in length be filled?
>
> NOTE: Although the example does not lend itself to the possibility, in general it is permitted to mix block sizes. For example, on a row measuring eight units in length you could use red (3), grey (1), and red (4).

<!--more-->

## Answer

This type of problem actually lends itself easily to dynamic programming, where we slowly build up the values to past solutions. Why you ask? If you were to go about this normally, imagine you placed exactly one red block somewhere on the row. Of the black units that are remaining, it is natural to ask yourself **Now then, how many ways can I put red blocks on the remaining black tiles?** The consequence of asking such a question is dynamic programming.

Another concern you might have, what about configurations that turn out to be symmetric (much like the red (3), grey (1), and red (3) above)? To mitigate this problem, we will only worry about the number of ways of placing blocks in the black tiles **to the left of the already placed red block.** That way, we will only count each configuration once.

To summarize our steps:

- Choose a red block length $$\ell_r$$. Let the length of the row be $$n$$.
- Slide the red block across the row until the end. This is exactly $$n-\ell_r + 1$$ steps.
- For each position $$i$$ (0-indexed), take the number of **valid** black tiles to the left, and lookup how many ways are there for that amount of tiles to be filled. Under this problem, there needs to be a gap of one, so the number of valid tiles is $$i-1$$.

Now, since the least-case scenario for the number of valid tiles is 0, this means we would have to lookup the number of ways to fill "-1" black tiles. For the purposes of this problem, this will be set to 1.

Therefore, our dynamic programming equation looks like this:


$$
F(n) = \begin{cases}
	1 & n < 3
	\\
	\displaystyle\sum_{r=3}^n\sum_{i=0}^{n-r}F(n-1) & n \geq 3
\end{cases}
$$


A double summation equates to a double for loop, so this is quite simple to code. Once again, I'm using `numpy`'s advanced indexing features, which allow me to index with an array of positions and get the values, which saves me the trouble of writing an extra for loop.

```python
targetN = 50
nVals = np.zeros(targetN+2, dtype=np.int64)
# -1,0,1,2 are all 1
nVals[0] = 1
# Start from n = 3, and find values up to target
for n in range(0, targetN+1):
    configs = 0
    # From the smallest red block (3) to the largest
    # (n), move it across and find the number of
    # configurations you can put in the remaining
    # usable black blocks
    for redSize in range(3, n+1):
        blackSizes = np.arange(-1, n - redSize)
        # Sum the values at those locations
        # Add 1
        configs += np.sum(nVals[blackSizes + 1])
    # Add 1 to final count because of all black
    nVals[n + 1] = configs + 1

print(nVals[-1])
```

Since the start of the array signifies -1, I have to do the same offset adjustment to index it correctly (which in this case is *adding* 1).

Running this short loop results in,

```
16475640049
0.022253200000000084 seconds.
```

Thus, there are **16475640049** ways to fill a row measuring 50 units long.