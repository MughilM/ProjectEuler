---
layout: post
title: "Problem 140 - Modified Fibonacci golden nuggets"
date: 2019-06-23 16:06
number: 140
mathjax: true
---

> Consider the infinite polynomial series $$A_G(x) = xG_1+x^2G_2+x^3G_3+\cdots$$, where $$G_k$$ is the $$k$$th term of the second order recurrence relation $$G_k=G_{k-1}+G_{k-2}$$, $$G_1=1$$ and $$G_2=4$$; that is, $$1,4,5,9,14,23, \dots$$.
>
> For this problem we shall be concerned with values of $$x$$ for which $$A_G(x)$$ is a positive integer.
>
> The corresponding values of $$x$$ for the first five natural numbers are shown below.
>
> | $$x$$                       | $$A_G(x)$$ |
> | --------------------------- | ---------- |
> | $$\frac{\sqrt{5}-1}{4}$$    | 1          |
> | $$\frac{2}{5}$$             | 2          |
> | $$\frac{\sqrt{22}-2}{6}$$   | 3          |
> | $$\frac{\sqrt{137}-5}{14}$$ | 4          |
> | $$\frac{1}{2}$$             | 5          |
>
> We shall call $$A_G(x)$$ a golden nugget if $$x$$ is rational, because they become increasingly rarer; for example, the 20th golden nugget is 211345365.
>
> Find the sum of the first thirty golden nuggets.

## Answer

This problem is very similar to [Problem 137]({{ site.baseurl }}{% post_url 2019-06-21-Problem-137---Fibonacci-golden-nuggets %}) where we dealt with purely Fibonacci numbers. Here, the sequence is slightly different, but hopefully the same pattern (if not a variation) should stand. We will follow the same steps. First, using the sequence definition we need to come up with a closed form formula for $$A_G(x)$$:


$$
\begin{aligned}
	A_G(x) &= \sum_{n=1}^\infty G_n x^n
	\\ &=
	G_1x + G_2x^2 + \sum_{n=3}^\infty G_n x^n
	\\ &=
	x + 4x^2 + \sum_{n=3}^\infty \left(G_{n-1} + G_{n-2}\right)x^n
	\\ &=
	x + 4x^2 + x\sum_{n=3}^\infty G_{n-1}x^{n-1} + x^2\sum_{n=3}^\infty G_{n-2}x^{n-2}
	\\ &=
	x + 4x^2 + x\sum_{n=2}^\infty G_n x^n + x^2\sum_{n=1}^\infty G_n x^n
	\\ &=
	x + 4x^2 + x\left(A_G(x) - x\right) + x^2A_G(x)
	\\ &=
	x + 3x^2 + xA_G(x) + x^2A_G(x)
	\\
	A_G(x) &= \frac{x+3x^2}{1-x-x^2}
\end{aligned}
$$


Like before, now we'll find when $$A_G(x)$$ is an integer. Let's suppose that $$A_G(x) = C$$ and use the quadratic formula to find what $$x$$ should be:


$$
\begin{aligned}
	C &= \frac{x+3x^2}{1-x-x^2}
	\\
	x^2(C+3)+x(C+1)-C &= 0
	\\
	x &= \frac{-(C+1)\pm \sqrt{(C+1)^2-4(C+3)(-C)}}{2(C+3)}
	\\ &=
	\frac{-(C+1)\pm\sqrt{(C+1)^2+4C^2+12C}}{2(C+3)}
	\\ &=
	\frac{-(C+1)\pm\sqrt{5C^2+14C+1}}{2(C+3)}
\end{aligned}
$$


## Finding when the discriminant is a perfect square

The discriminant is different than in [Problem 137]({{ site.baseurl }}{% post_url 2019-06-21-Problem-137---Fibonacci-golden-nuggets %}), but the same steps hold. We found the first few values of $$C$$ that results in a perfect square under the square root, devised a rule, and proved it works.

First we find a few values where we get a perfect square discriminant. Below are the values of $$C$$ which result in a rational $$x$$. 

| $$C$$ | Root of Discriminant | $$x$$             |
| ----- | -------------------- | ----------------- |
| 2     | 7                    | $$\frac{2}{5}$$   |
| 5     | 14                   | $$\frac{1}{2}$$   |
| 21    | 50                   | $$\frac{7}{12}$$  |
| 42    | 97                   | $$\frac{3}{5}$$   |
| 152   | 343                  | $$\frac{19}{31}$$ |
| 296   | 665                  | $$\frac{8}{13}$$  |
| 1050  | 2351                 | $$\frac{50}{81}$$ |
| 2037  | 4558                 | $$\frac{21}{34}$$ |

Like [Problem 137]({{ site.baseurl }}{% post_url 2019-06-21-Problem-137---Fibonacci-golden-nuggets %}), we see the Fibonacci fractions, but then also see other fractions with seemingly random numbers. Taking just these non-Fibonacci fractions, they do seem to satisfy the addition property (2, 5, 7, 12, ...). Where did these numbers come from? Well, watch what happens when we add the Fibonacci numbers to the $$G$$ sequence:


$$
\{1,1,2,3,5,8,\dots\}\,+\,\{1,4,5,9,14,23,\dots\} = \{2,5,7,12,19,31,\dots\}
$$


We get our sequence from above! Following the alternating pattern between the pure Fibonacci numbers and the numbers from the sum of the two sequences, we can conclude that the $$x$$-value associated with the $$n$$th golden nugget is


$$
x_n=\begin{cases}
	\frac{F_n+G_n}{F_{n+1}+G_{n+1}} \qquad &n\text{ is odd}
	\\
	\frac{F_n}{F_{n+1}} \qquad &n\text{ is even}
\end{cases}
$$


We can call the $$F_n+G_n$$ sequence as $$H_n$$. We can plug each of these into the function and get formulas for the $$n$$th golden nugget $$C_n$$:


$$
C_n=\begin{cases}
	\frac{H_nH_{n+1}+3H_n^2}{11} \qquad &n\text{ is odd}
	\\
	F_nF_{n+1}+3F_n^2 \qquad &n\text{ is even}
\end{cases}
$$


It shouldn't be surprising that the $$H_n$$ sequence has its own "Cassini's Identity". In this case, $$H_{n+1}H_{n-1}-H_n^2=11(-1)^{n+1}$$. We can prove this identity by induction, just like the original Cassini's Identity. Additionally, for total completeness, you can also prove that the numerator of the top expression must be divisible by 11, so that the whole thing evaluates to an integer. This is much more involved, and it is left to the reader.

Now that we have a direct formula, it is simple to code a loop to calculate the sum of the first 30 golden nuggets. First we calculate the values of the Fibonacci sequence and the $$H$$ sequence up $$n=31$$.

```python
# Compute the Fibonacci and H sequence up till n = 31
H = [3, 2, 5]
F = [0, 1, 1]
for _ in range(3, 32):
    H.append(H[-2] + H[-1])
    F.append(F[-2] + F[-1])

# Now compute the sum of the first 30 thirty nuggets
goldenNuggetSum = 0
for n in range(1, 31):
    if n % 2 == 1:
        goldenNuggetSum += (H[n] * H[n + 1] + 3 * H[n] ** 2) // 11
    else:
        goldenNuggetSum += F[n] * F[n + 1] + 3 * F[n] ** 2

print(f'The sum of the first 30 golden nuggets is {goldenNuggetSum}.')
```

Running this short code, we get

```
The sum of the first 30 golden nuggets is 5673835352990.
5.6199999999950734e-05 seconds.
```

Thus, our golden nugget sum is **5673835352990**.