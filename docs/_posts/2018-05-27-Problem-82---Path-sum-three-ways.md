---
layout: post
title: "Problem 82 - Path sum: three ways"
date: 2018-05-27 09:30
number: 82
mathjax: true
---

## Question

The minimal path sum in the 5 by 5 matrix below, by starting in any cell in the left column and finishing in any cell in the right column, and only moving up, down, and right, is indicated in red and bold; the sum is equal to 994.


$$
\begin{pmatrix}
	131 & 673 & \color{red}{\mathbf{234}} & \color{red}{\mathbf{103}} & \color{red}{\mathbf{18}}
	\\
	\color{red}{\mathbf{201}} & \color{red}{\mathbf{96}} & 
		\color{red}{\mathbf{342}} & 965 & 150
	\\
	630 & 803 & 746 & 422 & 111
	\\
	537 & 699 & 497 & 121 & 956
	\\
	805 & 732 & 524 & 37 & 331
\end{pmatrix}
$$


Find the minimal path sum from the left column to the right column in [matrix.txt](https://projecteuler.net/project/resources/p082_matrix.txt) (right click and "Save Link/Target As..."), a 31K text file containing an 80 by 80 matrix.

## Answer

This is a proper step up from Problem 81, where we only had 2 directions to worry about. However, we can still adapt the same method of updating the minimum sum as we go along, albeit a bit more complicated.

In the previous problem, we realized that in each cell, the minimum path to the cell can **only** come from above or from the left. Here, **it can also come from below**.

Since we're going from column to column, we would update the cells in each column to reflect the minimum cell ending **in that cell**, originating from **any cell in the leftmost column**. Then, when we reach the second to last column, we just add the rightmost column directly, and take the minimum sum.

To figure out how to compute the minimum sums in a column, given the sums in the previous column, the following graphic would help, with the "803" in the 2nd column as an example:

![sumImage]({{site.url}}{{site.baseurl}}\assets\Images\p082.png)

You can see that for the "803" cell, we need to calculate the sums of 5 possible paths. Above, the orange path sum totaling 1100 is the least, and so we save that number in our sum matrix. We then repeat this process for each cell in the column. 

Because we are taking into account an extra direction, we are spending a quadratic amount of time in each column. This coupled with having $n$ columns, leads to a cubic algorithm. Not the best, but much better than brute force.

In terms of code, I utilize the `np.cumsum` method to quickly calculate those vertical sums. Otherwise, it's a straightforward double for loop.

```python
import time
import numpy as np

start = time.perf_counter()

matrix = np.loadtxt('./p082_matrix.txt', dtype='int32', delimiter=',')

# We start on the second column and find the minimum
# sum going from any cell in the previous column to
# that cell. We keep doing this for each column,
# and finally simply add the number directly to
# the right for the second to last column and take the min.

for j in range(1, matrix.shape[1] - 1):
    minSums = np.zeros(matrix.shape[0], dtype='int32')
    for i in range(matrix.shape[0]):
        # Find min of all possible sums coming from the previous column
        # When we come from the bottom (matrix[:i+1,j]) we need to reverse the array...
        minSums[i] = np.min(np.append(np.cumsum(matrix[:i+1, j][::-1]) + matrix[:i+1, j-1][::-1],
                                         np.cumsum(matrix[i:, j]) + matrix[i:, j-1]))

    matrix[:, j] = minSums
# Now just add the last two columns together and take
# the minimum
print(np.min(matrix[:, -2] + matrix[:, -1]))


end = time.perf_counter()
print(end - start, 'seconds.')
```

Running this code results in an output of,

```
260324
0.6649046999999999 seconds.
```

And so, our minimum sum from the left column to the right column is **260324**.