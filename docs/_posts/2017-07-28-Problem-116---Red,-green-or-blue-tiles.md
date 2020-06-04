---
layout: post
title: "Problem 116 - Red, green or blue tiles"
date: 2017-07-28 10:20
mathjax: true
number: 116
---

> A row of five grey square tiles is to have a number of its replaced with coloured oblong tiles chosen from red (length two), green (length three), or blue (length four).
>
> If red tiles are chosen there are exactly seven ways this can be done.
>
> ![p116_1]({{ site.url }}{{ site.baseurl }}/assets/Images/p116_1.png)
>
> If green tiles are chosen there are three ways.
>
> ![p116_2]({{ site.url }}{{ site.baseurl }}/assets/Images/p116_2.png)
>
> And if blue tiles are chosen there are two ways.
>
> ![p116_3]({{ site.url }}{{ site.baseurl }}/assets/Images/p116_3.png)
>
> Assuming that colours cannot be mixed there are 7 + 3 + 2 = 12 ways of replacing the grey tiles in a row measuring five units in length.
>
> How many different ways can the grey tiles in a row measuring fifty units in length be replaced if colours cannot be mixed and at least one coloured tile must be used?
>
> NOTE: This is related to [Problem 117]({{ site.url }}{{ site.baseurl }}{% post_url 2017-07-28-Problem-116---Red,-green-or-blue-tiles %}).

<!--more-->

## Answer

We refused to let the blocks go about their business, and now they're changing colours....Anyways, this differs from the two previous problems because, well, there's more than one colour to worry about, and we shouldn't include the configuration of **all black tiles** in the count. Additionally, each coloured tile is a specific length; they don't vary like in Problems [114]({{ site.url }}{{ site.baseurl }}{% post_url 2017-07-27-Problem-114---Counting-block-combinations-I %}) and [115]({{ site.url }}{{ site.baseurl }}{% post_url 2017-07-28-Problem-115---Counting-block-combinations-II %}).

Since the tiles can't mix, this actually makes it easy. Like how we kept an array going for past values, we now keep **three** separate arrays, one for each colour. Also, since the tile is constant length, we can simply slide it along and calculate the ways to fill the remaining black tiles **to the left**, much like the previous two problems. In summary, we will actually use the same code with slight modifications.

```python
targetN = 50
# Red, blue, and green tiles can't be mixed
# so create three separate arrays
# No space of at least one so smallest value is n = 0
# Order is red, green, blue. The size would be the index + 2
coloredTiles = np.zeros((3, targetN+1), dtype=np.uint64)
coloredTiles[:, :2] = 1
# Use dynamic programming to find
# the number for each n of each color
for n in range(2, targetN+1):
    for i in range(len(coloredTiles)):
        size = i + 2
        blackSpaceLeft = np.arange(n - size + 1)
        coloredTiles[i, n] = np.sum(coloredTiles[i, blackSpaceLeft]) + 1

# Subtract 3 to remove the all-black config
# axis=0 means to sum column by column.
totalTiles = np.sum(coloredTiles, axis=0) - 3
print(totalTiles[-1])
```

Running this code results in an output of,

```
20492570929
0.003920800000000002 seconds.
```

Thus, if mixing of colours are prohibited, then there are **20492570929** ways to fill a row that is 50 units long.