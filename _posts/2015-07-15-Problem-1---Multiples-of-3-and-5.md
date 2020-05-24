---
layout: post
title: "Problem 1 - Multiples of 3 and 5"
date: 2015-07-15 17:41:00
mathjax: true
number: 1
---

## Question

If we list all the natural numbers below 10 that are multiples of 3  or 5, we get 3, 5, 6 and 9. The sum of these multiples is 23.

Find the sum of all the multiples of 3 or 5 below 1000.

## Answer

Nothing fancy to do here. We can simply look through each natural number and add it to a rolling sum if it's a multiple of 3 or 5:

```python
s = 0
for n in range(1, 1000):
    if n % 3 == 0 or n % 5 == 0:
        s += n
print(n)
```

Running the code results in,

```
233168
0.00019780000000002573 seconds.
```

Therefore, **233168** is our answer to the first problem.

### Bonus

We can solve this problem completely analytically. For example, $$\sum_{i=1}^{333}3i$$ will get us all multiples of 3. We can do the same summation for 5. However, we have to be a bit careful. Adding these two summations will lead us to double count all numbers that are both multiples of 3 and 5 i.e. 15. Thus, we need to subtract these multiples. The full sum is therefore:

$$
\begin{aligned}
S &= \sum_{i=1}^{333}3i + \sum_{i=1}^{199}5i - \sum_{i=1}^{66}15i
\\
&= 3\left(\frac{333(334)}{2}\right) + 5\left(\frac{199(200)}{2}\right)-15\left(\frac{66(67)}{2}\right)
\\ &=
3(55611) + 5(19900) - 15(2211)
\\ &=
\boxed{233168}
\end{aligned}
$$
