---
layout: post
title: "Problem 94 - Almost equilateral triangles"
date: 2017-06-30 12:58
number: 94
mathjax: true

---

## Question

It is easily proved that no equilateral triangle exists with integral length sides and integral area. However, the *almost equilateral triangle* 5-5-6 has an area of 12 square units.

We shall define an *almost equilateral triangle* to be a triangle for which two sides are equal and the third differs by no more than one unit.

Find the sum of the perimeters of all *almost equilateral triangles* with integral side lengths and area and whose perimeters do not exceed one billion (1,000,000,000).

## Answer

An obvious question to ask first is: When does an almost equilateral triangle have an integral area? Well, from the description, it's clear that almost equilateral triangles are really just isosceles triangles. Finding the area of an isosceles triangle is straightforward. Below is an example, where $$x$$ is the base, and $$y$$ are the two sides that are equal. The main property is that if you drop a straight line to the base, then it is perpendicular to the base.

![triangleImg]({{site.url}}{{site.baseurl}}/assets/Images/p094.png)

Okay, so the area of our isosceles triangle is $$\frac{1}{2}x\sqrt{y^2-(x/2)^2}$$. This an integer when $$x$$ is even and $$y^2-(x/2)^2$$ is a perfect square. Put another way, if $$h$$ is our height, it means that $$\mathbf{(x/2, h, y)}$$ **is a Pythagorean triple!** Here, we assumed that $$x/2$$ is an integer, which would $$x$$ would have to be even anyway.

Including our original condition that $$x-y = \pm1$$, this also means that the triples we are interested in have the property that **twice the smallest side differs from the hypotenuse by one.** Why the smallest side? Because the right triangle that is formed by cutting the isosceles in half has the shorter leg corresponding to our base of the original.

Another important point, is that Pythagorean triples will be **primitive**, meaning they will not have any common factors. Why? Because as we scale up a triple, the difference property will also be scaled i.e. (3,4,5) follows our property (3 x 2 = 6 = 5 + 1), but (6,8,10) will not, as the difference becomes 2.

To loop through primitive triples, we can use matrices. They have been reproduced below:


$$
A=\begin{bmatrix}
	1 & -2 & 2 \\
	2 & -1 & 2 \\
	2 & -2 & 3
\end{bmatrix}
\\
B=\begin{bmatrix}
	1 & 2 & 2 \\
	2 & 1 & 2 \\
	2 & 2 & 3
\end{bmatrix}
\\
C=\begin{bmatrix}
	-1 & 2 & 2 \\
	-2 & 1 & 2 \\
	-2 & 2 & 3
\end{bmatrix}
$$


Assuming the triple is in vector form, at each step, you have a choice of 3 matrices to multiply by, and showing all 3 possibilities results in a tree structure:

![treeImg]({{site.url}}{{site.baseurl}}/assets/Images/tree.png)

Examining this tree, I see only **4 triples** that satisfy our desired property:

- $$(3,4,5)\rightarrow3\times 2-5=6-5=1$$
- $$(15,8,17)\rightarrow 8\times2-17=-1$$
- $$(33,56,65)\rightarrow 33\times2-65=1$$
- $$(209,120,241)\rightarrow 120\times2-241=-1$$

What is common in these 4 triples? Following the tree, it appears we are alternating multiplying by the $$A$$ and $$C$$ matrices, starting with $$C$$! One can prove that this multiplication is the only one that will produce the triples we want. Exercise left to the reader :) *Hint: Represent our triple using only 2 variables based on the desired property, and multiply the matrices and see which conserve the property.*

So, this is simple to code up. The triple always "increases" in size whenever we multiply by a matrix, so the limit is also easy to enforce. As always, I use `numpy` to easily do vector-matrix multiplication.

```python
# The solutions to this problem are any
# pythogorean triples where the 2 * smallest side +/- 1 = hypotenuse.
# By using matrices, this is a form where C(AC)^N * [3, 4, 5] where
# A and C are defined below. The base is 2 times the smallest side,
# The height is the second largest

A = np.array([[1, -2, 2], [2, -1, 2], [2, -2, 3]])
C = np.array([[-1, 2, 2], [-2, 1, 2], [-2, 2, 3]])
triple = np.array([3, 4, 5])
Psums = 0
switch = 0  # We need to alternate multiplying A and C, starting with C
while np.sum(triple) <= 1000000000:
    perim = 2 * np.max(triple) + 2 * np.min(triple)
    # We don't know which one is the minimum,
    # so multiply the two numbers which aren't
    # the hypotenuse. We have x/2 so no need to
    # explicitly divide by 2.
    area = np.prod(triple[triple != np.max(triple)])
    if not switch:
        triple = np.dot(C, triple)  # Multiply by C
        switch = 1
    else:
        triple = np.dot(A, triple)  # Multiply by A
        switch = 0
    Psums += perim
print(Psums)
```

Running this short loop results in an output of,

```
518408346
0.0014765999999999946 seconds.
```

Therefore, our final sum of the perimeters is **518408346**.