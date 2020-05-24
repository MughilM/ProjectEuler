---
layout: post
title: "Problem 85 - Counting rectangles"
date: 2017-06-25 08:54
number: 85
mathjax: true
---

## Question

By counting carefully it can be seen that a rectangular grid measuring 3 by 2 contains eighteen rectangles:

![rectImage]({{site.url}}{{site.baseurl}}/assets/Images/p085.png)

Although there exists no rectangular grid that contains exactly two million rectangles, find the area of the grid with the nearest solution.

## Answer

First, notice how the problem shows counting the rectangles. We are taking each possible size of the "sub-rectangles", and counting how many there are. In this case, a 2 x 1 rectangle is **different** from a 1 x 2 rectangle.

Given a grid size $$m\times n$$, can we calculate how many rectangles there are? Let's go through the same method that the problem goes through. For each possible "sub-rectangle" dimension $$i\times j$$, we'll count how many of these $$i\times j$$ rectangles are contained in the larger $$m\times n$$ grid. 

Let's say $$i\times j = 2\times 1$$. Length-wise, we can fit 2 of these rectangles lengthwise above. Why? Because we'll run out of room. The 2 by 1 has 2 squares. At the right-most position of this 2 by 1, the left square is one away edge. In general, in an $$m\times n$$ grid, we can fit $$m-i+1$$ rectangles **lengthwise**. The $$m-i$$ is how many rectangles before we run out of room, and the extra 1 is to count that last position. By the same logic, we can fit $$n-j+1$$ rectangles **width-wise**. So obviously, for a specific $$i\times j$$ "sub-rectangle", there are $$(m-i+1)(n-j+1)$$ of them in an $$m\times n$$ grid. Summing over all possible values of $$i$$ and $$j$$ will give us the total number of rectangles in an $$m\times n$$ grid.

If $$R(m,n)$$ is the number of rectangles, then


$$
R(m,n) = \sum_{i=1}^m\sum_{j=1}^n (m-i+1)(n-j+1)
$$


Currently, if we were translate this into code, it would be double for loop. Can we simplify the expression so that we can calculate it directly? **Yes!** In a double summation, if we can write argument as a product of two terms, where one is a function of $$i$$, and the other is a function of $$j$$, then we can turn the sums of products into a product of sums! In other words, after a little algebra:


$$
\begin{aligned}
	R(m,n) &= \sum_{i=1}^m\sum_{j=1}^n(m-i+1)(n-j+1)
	\\ &=
	\left(\sum_{i=1}^m (m-i+1)\right)
		\left(\sum_{j=1}^n (n-j+1)\right)
	\\ &=
	\left(\sum_{i=1}^m(m+1) - \sum_{i=1}^m i\right)
		\left(\sum_{j=1}^n(n+1) - \sum_{j=1}^n j\right)
	\\ &=
	\left(m(m+1) - \frac{m(m+1)}{2}\right)
		\left(n(n+1) - \frac{n(n+1)}{2}\right)
	\\ &=
	\left(\frac{m(m+1)}{2}\right)\left(\frac{n(n+1)}{2}\right)
	\\ &=
	\boxed{\frac{mn(m+1)(n+1)}{4}}
\end{aligned}
$$


Great! We have directly formula for the number of rectangles in a grid. Notice that this is quadratic in $$m$$ and $$n$$. So this is going to pretty quickly. I simply did a guess on what max value to test until, and I guessed 100. So we'll have $$m$$ going from 1 to 100, and $$n$$ going from 1 to $$m$$, since we don't want to double test to grids of the same dimension.

```python
import math
start = time.perf_counter()

# Only have to go up to length-1 because
# 3 x 2 = 2 x 3
minDist = float('inf')
target = 2000000
area = 0
bestM = 0
bestN = 0
for m in range(1, 101):
    for n in range(1, m+1):
        rects = m * n * (m + 1) * (n + 1) // 4
        if math.fabs(rects - target) < minDist:
            minDist = math.fabs(rects - target)
            area = m * n
            bestM = m
            bestN = n
rects = bestM * bestN * (bestM + 1) * (bestN + 1) // 4
print('The grid is {} x {} (Area = {}) with {} rectangles.'.format(bestM, bestN, area, rects))
```

Running this code, we get an output of,

```
The grid is 77 x 36 (Area = 2772) with 1999998 rectangles.
0.0024605999999999795 seconds.
```

Thus, a $$77\times 36$$ grid has the closest to 2 million rectangles, and its area is **2772**.