---
layout: post
title: "Problem 20 - Factorial digit sum"
date: 2015-08-07 20:51
mathjax: true
number: 20
---

## Question

$$n!$$ means $$n\times(n-1)\times\cdots\times 3\times 2\times 1$$.

For example, $$10! = 10\times 9\times\cdots\times 3\times 2\times 1 = 3628800$$, and the sum of the digits in the number $$10!$$ is $$3+6+2+8+8+0+0 = 27$$.

Find the sum of the digits in the number $$100!$$.

## Answer

As said multiple times before, Python can easily handle very large numbers. In this case, we can write a simple loop to multiply all numbers from $$n$$ on down, like so.

```python
def factorial(n):
    product = 1
    for i in range(2, n + 1):
        product *= i
    return product
```

At this point, we can simply call this function to retrieve $$100!$$. After that, we can extract the digits and sum them together, all in one line:

```python
n = 100
fact = factorial(n)
s = sum([int(chara) for chara in str(fact)])
print(s)
```

At the end, the output is,

```
648
9.362962962962964e-05 seconds.
```

Therefore, our sum is **648**, and all done is negligible time :).