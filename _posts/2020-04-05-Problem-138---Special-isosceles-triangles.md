---
layout: post
title: "Problem 138 - Special isosceles triangles"
date: 2020-04-05 09:15
mathjax: true
number: 138
---

> Consider the isosceles triangle with base length, $$b=16$$, and legs, $$L=17$$.
>
> ![p138]({{ site.url }}{{ site.baseurl }}/assets/Images/p138.png)
>
> By using the Pythagorean theorem it can be seen that the height of the triangle, $$h=\sqrt{17^2-8^2}=15$$, which is one less than the base length.
>
> With $$b=272$$ and $$L=305$$, we get $$h=273$$, which is one more than the base length, and this is the second smallest isosceles triangle with the property that $$h=b\pm1$$.
>
> Find $$\sum L$$ for the twelve smallest isosceles triangles for which $$h=b\pm1$$ and $$b,L$$ are positive integers.

<!--more-->

## Answer

A property of isosceles triangles is that whenever you drop the height down, it cuts the base cleanly in two, and it produces two right triangles, whose leg lengths are $$b/2$$ and $$h$$, and hypotenuse $$L$$. It should be clear that since we want $$h=b\pm1$$, then that means $$b/2<h$$.

Let's say that $$\alpha=h$$, $$\beta=b/2$$, and $$\gamma=L$$. To have our original property hold, we need to find Pythagorean triples such that $$2\beta=\alpha\pm1$$. Additionally, this also means the triples have to be **primitive**, because if they weren't, the difference would be greater than 1.

How do we cycle through primitive triples again? The [Pythaogrean tree!](https://en.wikipedia.org/wiki/Tree_of_primitive_Pythagorean_triples)

![tree]({{ site.url }}{{ site.baseurl }}/assets/Images/pyth_tree.svg)

Looking at the tree, the two solutions given in the problem correspond to the (15, 8, 17) and (273, 136, 305) triangles. It seems as if we multiplied by $$C$$ to get to the first one, then multiplied by $$B$$, and $$C$$ again to get the second one. Does this pattern hold? If we multiply by $$B$$, and $$C$$ again do we get another solution? Let's check! Remember we start at $$\overrightarrow{v}^T=[3, 4, 5]$$ and that we multiply on the left. For what the matrices are, please check the Wikipedia link.
$$
(CB)^2C\overrightarrow{v} =
	\begin{bmatrix}
		-545 & 610 & 818 \\
		-274 & 305 & 410 \\
		-610 & 682 & 915
	\end{bmatrix}
	\begin{bmatrix}
		3 \\ 4 \\ 5
	\end{bmatrix}
= \begin{bmatrix}
	4895 \\ 2448 \\ 5473
\end{bmatrix}
$$
Indeed, we have 2(2448) = 4896, which is one more than 4895. It seems that this pattern works, where we multiply by $$CB$$ again and again, and we get each satisfying triangle in order. Plus, since we are only interested in multiplying, this is all we have to do. Simply collect the hypotenuses and add them up.

## Why? (Optional Section)

Simply recognizing a pattern exists wasn't too satisfying to me. I was hoping to see why this pattern exists, and more importantly, why it **doesn't** happen anywhere else. The best analysis I could achieve is going through the matrix multiplication with arbitrary values $$\alpha$$, $$\beta$$, and $$\gamma$$. Once you do this, you will see that when you multiply through, and do subtract the first element from twice the second, you will achieve a difference of $$-3\alpha + 2\gamma$$. To generate the tree, we always start with $$[\alpha, \beta, \gamma] = [3, 4, 5]$$. Plugging these in, you see the difference is 1. So this tells us it will always work with the multiplications.

As for other possible multiplications, checking the differences with those do not yield the same. Some intuition as to why has to do with how the matrices act on the triple. For example, from the Wikipedia page, repeatedly taking the top branch means the hypotenuse will always one more than the leg. The middle branch sees the two legs being one within each other, while the bottom branch gets the difference between the hypotenuse and the longer leg to be 2.

## Code

The only code here has to do with defining the matrices and multiplying them, which is quick and easy with `numpy`. I keep what the matrix power of $$CB$$ is throughout the process. 

```python
B = np.array([
    [1, 2, 2],
    [2, 1, 2],
    [2, 2, 3]
], dtype=object)
C = np.array([
    [-1, 2, 2],
    [-2, 1, 2],
    [-2, 2, 3]
], dtype=object)

baseTriple = np.array([15, 8, 17], dtype=object)
CBprod = np.dot(C, B)
sumL = 17
limit = 12
for _ in range(limit - 2):
    CBprod = np.dot(np.dot(C, B), CBprod)
    triple = np.dot(CBprod, baseTriple)
    sumL += triple[-1]
print(sumL)
```

Running this short loop gets us,

```
1118049290473627
0.00019860000000002098 seconds.
```

Thus, the sum of all the hypotenuses is **1118049290473627**. Not a hairy problem at all :)