---
layout: post
title: "Problem 81 - Path sum: two ways"
date: 2017-06-23 10:24
number: 81
mathjax: true
---

## Question

In the 5 by 5 matrix below, the minimal path sum from the top left to the bottom right, by **only moving to the right and down**, is indicated in bold red and is equal to 2427.


$$
\begin{pmatrix}
	\color{red}{\mathbf{131}} & 673 & 234 & 103 & 18
	\\
	\color{red}{\mathbf{201}} & \color{red}{\mathbf{96}} & 
		\color{red}{\mathbf{342}} & 965 & 150
	\\
	630 & 803 & \color{red}{\mathbf{746}} & \color{red}{\mathbf{422}} & 111
	\\
	537 & 699 & 497 & \color{red}{\mathbf{121}} & 956
	\\
	805 & 732 & 524 & \color{red}{\mathbf{37}} & \color{red}{\mathbf{331}}
\end{pmatrix}
$$


Find the minimal path sum from the top left to the bottom right by only moving right and down in [matrix.txt](https://projecteuler.net/project/resources/p081_matrix.txt) (right click and "Save Link/Target As..."), a 31K text file containing an 80 by 80 matrix.

## Answer

From the looks of things, it appears that it is not a good idea to brute force every single path in the giant matrix. Is there a faster method?

Well, notice that addition is like one-way street. Once you add something, it will always get bigger (we're only dealing with positive numbers). We can then save the **minimum path sum** for *any* path ending in a cell by comparing adding the number to its right and below it. If that's confusing, I'll show an example using a smaller matrix. Suppose our matrix $$M$$ is just the top 3 by 3 corner of the example:


$$
M = \begin{pmatrix}
	131 & 673 & 234
	\\
	201 & 96 & 342
	\\
	630 & 803 & 746
\end{pmatrix}
$$


We will also have a new matrix $$P$$, where each element $$p_{ij}$$ is the minimum path sum of all paths in $$M$$ ending at the cell in row $$i$$ and column $$j$$. Hopefully, you can easily see that the top corner of both matrices will be the same i.e. $$p_{ij} = m_{ij}$$. Since we can only move right or down, the only paths that end in the top row, are the ones that consistently move across. Thus,


$$
\begin{aligned}
	p_{12} &= m_{11} + m_{12} = 131 + 673 = 804
	\\
	p_{13} &= m_{11} + m_{12} + m_{13} = 131 + 673 + 234 = 1038
\end{aligned}
$$


Similarly, we can make the same deduction about the first column of $$P$$. So from the start, we can initialize our $$P$$ matrix as so:


$$
P = \begin{pmatrix}
	131 & 804 & 1038 \\
	332 & * & * \\
	962 & * & *
\end{pmatrix}
$$


I've put stars in the places where we haven't calculated the minimum path sum yet. Now then, what is $$p_{22}$$, that is, the minimum path sum for all paths ending in the middle cell? Well, since all paths only travel right or down, **all paths ending in the middle cell had to have come from the cell above or the cell to the left**. But wait, we already calculated the minimum path sums for those cells! In that case, we just need add those cells ($$p_{12}$$ and $$p_{21}$$) to $$m_{22}$$ (96), and save whichever is smaller! In this case, $$332+96<804+96$$, so $$p_{22} = 332+96 = 428$$. In general,


$$
p_{ij} = \min\begin{cases}p_{i-1,j}+m_{ij} \\ p_{i,j-1}+m_{ij}\end{cases}
$$


Following this rule, we can quickly fill in $$P$$:


$$
P = \begin{pmatrix}
	131 & 804 & 1038 \\
	332 & 428 & 770 \\
	962 & 1231 & 1516
\end{pmatrix}
$$


The question asks for the minimum path sum ending in the bottom corner. Well, this is just the value in the bottom corner of $$P$$. Notice, we are not saving the actual path as we go along. We could if we want to, by signaling which term was saved, but that is not needed in this problem.

When coding the solution, I was overwriting the original array itself, since we visit each cell only once and don't need that cell's value anymore. A single double for loop does the job, after we've initialized the first row and column.

```python
with open('p081_matrix.txt') as f:
    matrix = [list(map(int, line.split(','))) for line in f.read().splitlines()]

n = 80
# First fill in cumulative sums on first row
# and first column
for i in range(1, n):
    matrix[0][i] += matrix[0][i - 1]
for i in range(1, n):
    matrix[i][0] += matrix[i - 1][0]
# Now update all the cells with the minimum sum
for i in range(1, len(matrix)):
    for j in range(1, len(matrix[i])):
        matrix[i][j] += min(matrix[i - 1][j], matrix[i][j - 1])

print(matrix[-1][-1])
```

Running this results in an output of,

```
427337
0.005567600000000006 seconds.
```

Thus, the minimum path sum in our 80 by 80 matrix is **427337**.