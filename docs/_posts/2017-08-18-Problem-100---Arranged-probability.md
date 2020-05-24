---
layout: post
title: "Problem 100 - Arranged probability"
date: 2017-08-18 01:05
number: 100
mathjax: true
---

## Question

If a box contains twenty-one coloured discs, composed of fifteen blue discs and six red discs, and two discs were taken at random, it can be seen that the probability of taking two blue discs $$P(BB)=(15/21)\times(14/20)=1/2$$.

The next such arrangement, for which there is exactly 50% chance of taking two blue discs at random, is a box containing eighty-five blue discs and thirty-five red discs.

By finding the first arrangement to contain over $$10^{12}=1\,000\,000\,000\,000$$ discs in total, determine the number of blue discs that the box would contain.

## Answer

The right "first step" to solving the problem is to see generally what the probability, given that we $$n$$ total discs and $$b$$ blue discs in the box. We do not replace the first disc once we've taken it out, so the probability is
$$
P(BB)=\left(\frac{b}{n}\right)\left(\frac{b-1}{n-1}\right)
$$
We want this to be a half, so let's see what happens if we set this equal to 1/2 and try to simplify...


$$
\begin{aligned}
	\left(\frac{b}{n}\right)\left(\frac{b-1}{n-1}\right) &=
		\frac{1}{2}
	\\
	\frac{b^2-b}{n^2-n} &= \frac{1}{2}
	\\
	2b^2-2b &= n^2-n
	\\
	2b^2-2b+n-n^2 &= 0
\end{aligned}
$$


It appears we have a quadratic in $$b$$. So let's try and solve it!


$$
\begin{aligned}
	b &= \frac{2\pm\sqrt{4-4(2)(n-n^2)}}{4}
	\\ &=
	\frac{2\pm2\sqrt{1-2n+2n^2}}{4}
	\\ &=
	\frac{1}{2}\left(1 + \sqrt{2n^2-2n+1}\right)
\end{aligned}
$$


Okay, so it looks like the number of blue discs will only be an integer if $$2n^2-2n+1$$ is a perfect square. When does this happen? Well, notice that we can rewrite the expression under the square root as $$n^2+(n-1)^2$$. Therefore, a **sum of two squares has to equal a third square**. In other words, if we let $$z=\sqrt{2n^2-2n+1}$$, then the set $$\{n, n-1, z\}$$ form a Pythagorean triple. Looks like it's a job for the matrices :)

The triple has the property that the two smaller sides are within one unit of each other. From the [Wikipedia page](https://en.wikipedia.org/wiki/Tree_of_primitive_Pythagorean_triples) for the tree of triples, repeatedly multiplying the $$B$$ matrix (shown below) will preserve that exact property.


$$
B=\begin{bmatrix}
	1 & 2 & 2 \\
	2 & 1 & 2 \\
	2 & 2 & 3
\end{bmatrix}
$$


We necessarily do not need all three values, as having $$n$$ will allow us to find the other values. However, there is one thing we must be careful of. 

The value of $$n$$ we want will always be the larger of the two smaller sides. From looking at the tree, the **order of the two values actually alternates each time we multiply.** For example, if we start with $$\{3,4,5\}$$, $$n=4$$, the second value. But if we multiply $$B$$ with this vector, the vector becomes $$\{21,20,29\}$$. Now $$n=21$$, the first value. The next vector, $$\{119,120,169\}$$, it is back to being the second value.

Once we've implemented this switch, it's simple to code. After we've calculated $$n$$, we calculate $$b$$, which is used to calculate the next $$n$$.

```python
b = 15
n = 21
switch = 0
while n < 10 ** 12:
    n = 3 * n - 1 + 2 * (2 * b - 1)
    if switch:
        n -= 1
    b = (int((2 * n ** 2 - 2 * n + 1) ** 0.5) + 1) // 2

print('b:', b)
print('n:', n)
```

Running this short code results in an output of,

```
b: 756872327473
n: 1070379110497
0.00012419999999999792 seconds.
```

Thus, we have **756872327473** blue discs in the box when the number of total discs first surpasses $$10^{12}$$.