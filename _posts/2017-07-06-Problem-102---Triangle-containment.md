---
layout: post
title: "Problem 102 - Triangle containment"
date: 2017-07-06 14:47
mathjax: true
number: 102
---

## Question

Three distinct points are plotted at random on a Cartesian plane, for which $$-1000\leq x,y\leq 1000$$, such that a triangle is formed.

Consider the following two triangles:


$$
A(-340, 495), B(-153, -910), C(835, -947)
\\
X(-175, 41), Y(-421, -714), Z(574, -645)
$$


It can be verified that triangle $$ABC$$ contains the origin, whereas triangle $$XYZ$$ does not.

Using [triangles.txt](https://projecteuler.net/project/resources/p102_triangles.txt) (right click and 'Save Link/Target As...'), a 27K text file containing the coordinates of one thousand "random" triangles, find the number of triangles for which the interior contains the origin.

NOTE: The first two examples in the file represent the triangles in the example given above.

## Answer

The core question is: **"Given a triangle, is the origin contained inside of it?"** Some googling of this problem leads to the structure of **barycentric coordinates**.

The [Wikipedia page](https://en.wikipedia.org/wiki/Barycentric_coordinate_system) provides a good overview. Essentially, we can put a **weight** $$\lambda_i$$ for each point $$i$$ of the triangle. Thus, we'll have $$\lambda_1, \lambda_2, \lambda_3$$. Essentially, each point $$(x,y)$$ in the Cartesian is a weighted average of the three points of the triangle.

Intuitively, you would think that for a point $$(x,y)$$, if these weights **are all between 0 and 1**, then $$(x,y)$$ would be inside the triangle. And you would be right! Conversely, if *any* of these weights are greater than 1 or negative, then the point lies outside the triangle.

How do we compute the weights though? The [conversion section](https://en.wikipedia.org/wiki/Barycentric_coordinate_system#Conversion_between_barycentric_and_Cartesian_coordinates) gives a quick formula for calculating two of the weights; since the weights have to sum to 1, the third can be calculated easily once you know two of them.

If our triangle points are $$\mathbf{r_1}=\langle x_1,y_1\rangle, \mathbf{r_2}=\langle x_2,y_2\rangle, \mathbf{r_3}=\langle x_3, y_3\rangle$$, the point we are testing is $$\mathbf{r}=\langle x, y\rangle$$, then we can create a 2 by 2 matrix 


$$
\mathbf{T}=\begin{bmatrix}
	x_1-x_3 & x_2-x_3 \\
	y_1-y_3 & y_2-y_3
\end{bmatrix}
$$


and then, by taking the inverse, we can find two of the weights:


$$
\begin{bmatrix}
	\lambda_1 \\ \lambda_2
\end{bmatrix} = \mathbf{T}^{-1}(\mathbf{r}-\mathbf{r_3})
$$


The last weight is $$\lambda_3 = 1-\lambda_1-\lambda_2$$. With the help of the `numpy` package, doing vector-matrix multiplications are a cinch. Of course, in our case, the test point is $$\mathbf{r}=\langle 0, 0\rangle$$, so we end up just multiplying by the negative of $$\mathbf{r_3}$$.

```python
with open('p102_triangles.txt') as f:
    triangles = np.reshape([line.split(',')
                            for line in f.read().splitlines()],
                           (-1, 3, 2)).astype(int)

numOfTriangles = 0
r = np.zeros(2, dtype=int)
for triangle in triangles:
    r1 = triangle[0]
    r2 = triangle[1]
    r3 = triangle[2]
    T = np.array([[r1[0] - r3[0], r2[0] - r3[0]],
                  [r1[1] - r3[1], r2[1] - r3[1]]])
    Lam = np.dot(np.linalg.inv(T), r - r3)
    l1 = Lam[0]
    l2 = Lam[1]
    l3 = 1 - l1 - l2
    if l1 > 0 and l2 > 0 and l3 > 0:
        numOfTriangles += 1

print(numOfTriangles)
```

Running this short code gets us a number of,

```
228
0.04480099999999998 seconds.
```

Thus, there are **228** triangles that contain the origin.