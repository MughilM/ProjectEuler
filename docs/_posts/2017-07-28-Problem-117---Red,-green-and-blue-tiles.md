---
layout: post
title: "Problem 117 - Red, green and blue tiles"
date: 2017-07-28 10:36
mathjax: true
number: 117
---

> Using a combination of grey square tiles and oblong tiles chosen from: red tiles (measuring two units), green tiles (measuring three tiles), and blue tiles (measuring four tiles), it is possible to tile a row measuring five units in length in exactly fifteen different ways.
>
> ![p117]({{ site.url }}{{ site.baseurl }}/assets/Images/p117.png)
>
> How many ways can a row measuring fifty units in length be tiled?
>
> NOTE: This is related to [Problem 116]({{ site.url }}{{ site.baseurl }}{% post_url 2017-07-28-Problem-116---Red,-green-or-blue-tiles %}).

<!--more-->

## Answer

And now they've started mingling. Man it's tough to deal with blocks...It seems as if this problem needs a quite different from the previous three. Surely we can't simply copy-paste our code and simply change a couple lines?

....Actually we can (surprise surprise). Since the red block is the smallest we have at 2 units, and the largest is the blue tile at 4 units, it is basically like [Problem 114]({{ site.url }}{{ site.baseurl }}{% post_url 2017-07-27-Problem-114---Counting-block-combinations-I %}), where the minimum tile length is 2 and the maximum tile length is 4. The fact that there is no gap between the tiles doesn't change much, other than how many black tiles are available for placement. In the end, the same concept of dynamic programming applies.

For the code, notice the `blackSize = np.arange(n - blockSize + 1)` line. Previously, we had it start at -1, because of how the problem of "one gap required" was set up. Now, our array actually starts at 0.

```python
# Same as 114 except the maximum block size is now 4
# and there is no minimum gap of 1
targetN = 50
nVals = np.zeros(targetN+1, dtype=np.int64)
# 0 is 1
nVals[0] = 1
# Start from n = 0, and find values up to target
for n in range(0, targetN+1):
    configs = 0
    # From the smallest red block (2) to the largest
    # (4), move it across and find the number of
    # configurations you can put in the remaining
    # usable black blocks
    for blockSize in range(2, 5):
        blackSizes = np.arange(n - blockSize + 1)
        # Sum the values at those locations
        # Add 1
        configs += np.sum(nVals[blackSizes])
    # Add 1 to final count because of all black
    nVals[n] = configs + 1

print(nVals[-1])
```

Running this code (which you've probably seen at least 3 times before...), we get,

```
100808458960497
0.0025583000000000133 seconds.
```

Thus, with the problem set up this way, we have **100808458960497** ways of tiling a row 50 units in length. This is the last problem that deals with blocks...so hope you enjoyed it while it lasted :)