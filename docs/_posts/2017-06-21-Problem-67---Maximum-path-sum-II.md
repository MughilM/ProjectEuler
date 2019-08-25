---
layout: post
title: "Problem 67 - Maximum path sum II"
date: 2017-06-21 09:29
mathjax: true
number: 67
---

## Question

By starting at the top of the triangle below and moving to adjacent numbers on the row below, the maximum total from top to bottom is 23.

<pre style="text-align:center">
<span style="color:red"><b>3</b></span>
<span style="color:red"><b>7</b></span> 4
2 <span style="color:red"><b>4</b></span> 6
8 5 <span style="color:red"><b>9</b></span> 3
</pre>

That is, 3 + 7 + 4 + 9 = 23.

Find the maximum total from top to bottom in [triangle.txt](https://projecteuler.net/project/resources/p067_triangle.txt) (right click and 'Save Link/Target As...') a 15K text file containing a triangle with one-hundred rows.

**NOTE**: This is a much more difficult version of [Problem 18]({{ site.baseurl }}{% post_url 2015-08-07-Problem-18---Maximum-path-sum-I %}). It is not possible to try every route to solve this problem, as there are $$2^{99}$$ altogether! If you could check one trillion $$\left(10^{12}\right)$$ routes every second it would take over twenty billion years to check them all. There is an efficient algorithm to solve it ;o)

## Answer

My answer to [Problem 18]({{ site.baseurl }}{% post_url 2015-08-07-Problem-18---Maximum-path-sum-I %}) has the efficient algorithm in question, as there was a warning for this problem. Please see the answer to that problem for the algorithm. After replacing the file in the code and running it again, we get an output of,

```
7273
0.004878615356596403 seconds.
```

Thus, the max total from top to bottom is **7273**.