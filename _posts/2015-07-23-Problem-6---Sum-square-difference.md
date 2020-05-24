---
layout: post
title: "Problem 6 - Sum square difference"
date: 2015-07-23 19:23
mathjax: true
number: 6
---

## Question

The sum of the squares of the first ten natural numbers is,


$$
1^2+2^2+\cdots+10^2 = 385
$$


The square of the sum of the first ten natural numbers is,


$$
(1+2+\cdots+10)^2 = 55^2 = 3025
$$


Hence the difference between the sum of the squares of the first ten natural numbers and the square of the sum is 3025 - 385 = **2640**.

Find the difference between the sum of the squares of the first one hundred natural numbers and the square of the sum.

## Answer

Nothing too fancy to do here. The bound is not that large, and so we can directly run a loop up to 100, compute the two values necessary, and find the difference. Although `numpy` can simplify the number of lines we write, it may not be the quickest solution, since we are calling an external package. Unless we absolutely have to use it, I will not.

```python
n = 100
sumsquare = 0
squaresum = 0
for i in range(1, n + 1):
    sumsquare += i ** 2
    squaresum += i
squaresum **= 2

print(squaresum - sumsquare)
```

Running this gives an output of,

```
25164150
4.4641975308641976e-05 seconds.
```

Thus, **25164150** is our answer. 

But of course, we can do this analytically. The question is basically asking us to compute two separate summations, and subtract the two, as shown below:


$$
S = \left(\sum_{i=1}^{100}i\right)^2 - \sum_{i=1}^{100}i^2
$$


Formulas exist for both, and can be proven using induction. Recall that $\sum_{i=1}^n i = \frac{n(n+1)}{2}$ and $\sum_{i=1}^n i^2 = \frac{n(n+1)(2n+1)}{6}$. Plugging these two in directly,


$$
\begin{aligned}
S &= \left(\frac{100(100+1)}{2}\right)^2 - \frac{100(100+1)(2(100)+1)}{6}
\\ &= 5050^2 - 338350
\\ &= \boxed{25164150}
\end{aligned}
$$
