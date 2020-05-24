---
layout: post
title: "Problem 18 - Maximum path sum I"
date: 2015-08-07 20:47
mathjax: true
number: 18
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

Find the maximum total from top to bottom of the triangle below:

<pre style="text-align:center">
75
95 64
17 47 82
18 35 87 10
20 04 82 47 65
19 01 23 75 03 34
88 02 77 73 07 63 67
99 65 04 28 06 16 70 92
41 41 26 56 83 40 80 70 33
41 48 72 33 47 32 37 16 94 29
53 71 44 65 25 43 91 52 97 51 14
70 11 33 28 77 73 17 78 39 68 17 57
91 71 52 38 17 14 91 43 58 50 27 29 48
63 66 04 68 89 53 67 30 73 16 69 87 40 31
04 62 98 27 23 09 70 98 73 93 38 53 60 04 23
</pre>

**NOTE**: As there are only 16384 routes, it is possible to solve this problem by trying every route. However, [Problem 67]({{ site.baseurl }}{% post_url 2017-06-21-Problem-67---Maximum-path-sum-II %}), is the same challenge with a triangle containing one-hundred rows; it cannot be solved by brute force, and requires a clever method! ;o)

## Answer

Taking the note on the bottom into account, it is probably smarter to find an algorithm that will let us quickly find the maximum sum, so we can simply re-use the code for the future problem. How do we start? Well, even though the example in the problem lists out the numbers in the sum, the problem itself asks "Find the maximum total" - and that is all. That's key. This means we don't need to find the numbers which make the sum. Similar to the [lattice-paths problem]({{ site.baseurl }}{% post_url 2015-08-07-Problem-15---Lattice-paths %}), we can keep track of the maximum sum.

However, we have to make sure we save the sum for each spot in the row and not simply take the maximum number below us. Why? Imagine the "6" in the example given in the problem was instead "200":

<pre style="text-align:center">
3
7 4
2 4 200
8 5 9 3
</pre>

Now, the maximum sum is clearly 216 = 3 + 4 + 200 + 9. However, if we only take the max number among the two choices below each number, then we follow the path $$3\rightarrow 7\rightarrow 4\rightarrow 9$$ and completely miss the 200. Instead, we keep track of all sums, like so:

<pre style="text-align:center">
10 7
2 4 200
8 5 9 3
</pre>

<pre style="text-align:center">
12 14 207
8 5 9 3
</pre>

Here, notice that we save 14 (10 + 4), because $$10 + 4 > 7 + 4$$. The final row is,

<pre style="text-align:center">
20 19 216 210
</pre>

Now we see that the biggest number now is 216.

We can go in the other direction as well. The only difference is that we don't have to search the top row for the maximum because there is only one number. See below:

<pre style="text-align:center">
3
7 4
10 13 209
</pre>

<pre style="text-align:center">
3
20 213
</pre>

<pre style="text-align:center">
216
</pre>

Therefore, I will be using this method. For simplicity, I have saved the large triangle in the problem into a text file called `problem018.txt`. To make this simpler, I have placed numbers which are below and to the left in the triangle directly below it in the text file, like so:

```
75
95 64
17 47 82
18 35 87 10
...
```

The code is shown below:

```python
# open file and
# convert to integers and make
# 2d array.
with open("problem018.txt") as f:
    triangle = [list(map(int, line.split(' '))) for line in f.read().splitlines()]
# Work bottom to top, starting
# from the second to last
# row so we can numbers below.
for i in range(len(triangle) - 2, -1, -1):
    for j in range(len(triangle[i])):
        # The number to the "bottom left" is really just below,
        # in terms of indices, and "bottom right" is diagonally
        # right.
        triangle[i][j] = triangle[i][j] + max(triangle[i + 1][j], triangle[i + 1][j + 1])
# Grab the top number.
print(triangle[0][0])
```

Running the code above gives an output of,

```
1074
0.0003338271604938272 seconds.
```

Thus, **1074** is our answer. As an extra note, this method of programming where we keep track of intermediate values on the fly is known as *dynamic programming*. It is extremely useful if we only need the result and not the steps we took to get there, like with this problem, and the [lattice-path problem]({{ site.baseurl }}{% post_url 2015-08-07-Problem-15---Lattice-paths %}).