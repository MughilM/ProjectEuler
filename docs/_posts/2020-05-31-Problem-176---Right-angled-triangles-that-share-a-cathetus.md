---
layout: post
title: "Problem 176 - Right-angled triangles that share a cathetus"
date: 2020-05-31 19:59
number: 176
mathjax: true
---

> The four right-angled triangles with sides (9, 12, 15), (12, 16, 20), (5, 12, 13) and (12, 35, 37) all have one of the shorter sides (catheti) equal to 12. It can be shown that no other integer sided right-angled triangle exists with one of the catheti equal to 12.
>
> Find the smallest integer that can be the length of a cathetus of exactly 47547 different integer sided right-angled triangles.

<!--more-->

## Answer

I needed to do some research on this one to see if a formula exists to find the number of triangles with a specific length. In fact, on the Wolfram Mathworld page for [Pythagorean Triples](https://mathworld.wolfram.com/PythagoreanTriple.html), we find the following formula for the number of triangles with a leg length of $$s$$.


$$
L(s) = \begin{cases}
	\frac{1}{2}[(2a_1+1)(2a_2+1)\cdots(2a_n+1)-1] \qquad &\text{for }a_0=0
	\\
	\frac{1}{2}[(2a_0-1)(2a_1+1)(2a_2+1)\cdots(2a_n+1)-1] \qquad &\text{for }a_0\geq 1
\end{cases}
$$


where


$$
s=2^{a_0}p_1^{a_1}\cdots p_n^{a_n}
$$


We can verify this using the example given in the problem. $$s=12=2^2\times 3$$. Therefore, since $$2\geq 1$$, we use the second case, and $$L(12)=\frac{1}{2}[(2(2)-1)(2(1)+1)-1] = \frac{1}{2}(3(3)-1) = 4$$.

But one moment. We're not asked to find $$L(s)$$. Instead we are given what $$L(s)$$ is, and asked to find the minimum value of $$s$$. Essentially, we want a way to compute $$L^{-1}(s)$$.

The first thing we can do is change the formula a bit to accommodate the inverse. Assuming $$s$$ is broken-down into its prime factorization as above, we can multiply by 2 and add 1, and leave the product on the left-hand side. Additionally, because we want the **smallest** $$s$$, we would want $$s$$ to have powers of 2, and not potentially only contain something larger than that (12 has a power of 2, 15 does not, but both evaluate to 4 triangles).


$$
(2a_0-1)(2a_1+1)(2a_2+1)\cdots(2a_n+1)=2L(s)+1
$$


On the left, we are multiplying a bunch of numbers together....just like a prime factorization. So maybe, if we find the prime factorization of $$2L(s)+1$$ we can be a step closer to the answer.

The prime factorization of $$2(47547)+1=95095$$ is exactly $$5\times 7\times 11\times 13\times 19$$. We have 5 factors to split across 5 expressions above. This means that each of the $$2a_0-1$$ and $$2a_i+1$$ will evaluate to one of these 5 factors.

So then how do we split up. Which factor goes where? Well remember we want the smallest product that works. The first $$a_0$$ is mapped to the power of 2 in the product, $$a_1$$ is mapped to the power of 3, and so on. To keep the number as small as possible, we want to put the **largest exponents on the smallest powers.** So essentially, we want the first factor to evaluate to 19, the second to 13, the third to 11, the fourth to 7, and the final one to just 5. Put together, we need to solve


$$
\begin{cases}
	2a_0-1 &= 19
	\\
	2a_1+1 &= 13
	\\
	2a_2+1 &= 11
	\\
	2a_3+1 &= 7
	\\
	2a_4+1 &= 5
\end{cases}
$$


Well each of these on its own is just a simple equation in one variable! Solving them, we get that our exponents should be $$\{a_0,a_1,a_2,a_3,a_4\}=\{10,6,5,3,2\}$$. Therefore the smallest $$s$$ which makes $$L(s)$$ be 47547 is


$$
s=2^{10}\times 3^6\times 5^5\times 7^3\times 11^2 = \boxed{96818198400000}
$$


Our rather large answer is **96818198400000**. How about that? A complete solution done entirely by hand. This method is entirely generalizable to any $$L(s)$$. 

