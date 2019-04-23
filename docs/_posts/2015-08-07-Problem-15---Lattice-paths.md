---
layout: post
title: "Problem 15 - Lattice paths"
date: 2015-08-07 20:44
mathjax: true
number: 15
---

## Question

Starting in the top left corner of a 2 x 2 grid, and only being able to move to the right and down, there are exactly 6 routes to the bottom right corner.

![routeImg]({{site.url}}{{site.baseurl}}\assets\Images\p015.png)

How many such routes are there through a 20 x 20 grid?

## Answer

How do we start counting such a massive number? One thing to realize is that we can only move to the right and down. Therefore, for each point, we can only arrive at it if we passed through either the point to the right or to the north. If we had the number of routes to those two points respectively, then we can simply add them. Thus, we can gradually build a grid of route ways to each point, and once we're done, we just grab the number at the bottom right corner. Also, note that there is only 1 way to get to the points along the top row and left column, namely by either travelling only to the right or down, respectively.

```python
size = 20
# It's (size + 1) because a size x size grid
# has (size + 1) intersections across the top and
# down.
grid = np.zeros((size + 1, size + 1), dtype=object)
# The top row and left column only
# have 1 way to get there.
grid[0] = 1
grid[:, 0] = 1
# For each inner grid point, add
# the number to its left and up
for i in range(1, size + 1):
    for j in range(1, size + 1):
        grid[i, j] = grid[i - 1, j] + grid[i, j - 1]
# The number in the bottom right corner is what
# we want.
print(grid[-1, -1])
```

Running the above gives us,

```python
137846528820
0.0002860246913580247 seconds.
```

Thus, **137846528820** is our answer, and in quick time to boot.