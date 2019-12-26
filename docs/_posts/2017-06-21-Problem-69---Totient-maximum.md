---
layout: post
title: "Problem 69 - Totient maximum"
date: 2017-06-21 11:37
number: 69
mathjax: true
---

## Question

Euler's Totient function, $$\phi(n)$$ [sometimes called the phi function], is used to determine the number of numbers less than $$n$$ which are relatively prime to $$n$$. For example, as 1, 2, 4, 5, 7, and 8, are all less than nine and relatively prime to nine, $$\phi(9)=6$$.

| $$n$$ | Relatively Prime | $$\phi(n)$$ | $$n/\phi(n)$$ |
| ----- | ---------------- | ----------- | ------------- |
| 2     | 1                | 1           | 2             |
| 3     | 1,2              | 2           | 1.5           |
| 4     | 1,3              | 2           | 2             |
| 5     | 1,2,3,4          | 4           | 1.25          |
| 6     | 1,5              | 2           | 3             |
| 7     | 1,2,3,4,5,6      | 6           | 1.1666...     |
| 8     | 1,3,5,7          | 4           | 2             |
| 9     | 1,2,4,5,7,8      | 6           | 1.5           |
| 10    | 1,3,7,9          | 4           | 2.5           |

It can be seen that $$n=6$$ produces a maximum $$n/\phi(n)$$ for $$n\leq 10$$.

Find the value of $$n\leq 1\,000\,000$$ for which $$n/\phi(n)$$ is a maximum.

## Answer

Okay, so the first order of business is to find out more about this function. [This](https://en.wikipedia.org/wiki/Euler's_totient_function) Wikipedia article states that the actual formula for this is


$$
\phi(n) = n\prod_{p|n}\left(1-\frac{1}{p}\right)
$$


Essentially, we take all the distinct prime numbers that divide $$n$$ and multiply according to the above. For example, 9 has only prime factor of 3. Thus, $$\phi(n) = 9\left(1-\frac{1}{3}\right) = 9\left(\frac{2}{3}\right) = 6$$, as the table shows.

But notice, to calculate for large $$n$$, we can keep a Sieve going. Instead of marking off whether numbers are prime or not, we store a running value at each position. Furthermore, since we need the maximum value of $$n/\phi(n)$$, we'll actually need the **least** value of $$\prod_{p\vert n}\left(1 - \frac{1}{p}\right)$$. To keep track of which numbers are primes or not, we initialize the array with 1s. That way, if an element isn't 1, then it was multiplied by some other number, and so isn't a prime. I use the `numpy` package as it makes element-wise marking simple.

```python
# Basically make a sieve,
# since totient of a number
# is (1 - 1/p) for each distinct
# prime factor p...
limit = 1000000
totient = np.ones(limit + 1, dtype=float)
# For each number starting at 2...
for n in range(2, limit + 1):
    # If the value isn't 1, then it
    # was a multiple of a prime factor...
    if totient[n] != 1:
        continue
    # Otherwise, mark ell multiples
    totient[np.arange(n, limit + 1, n)] *= (1 - 1/n)
# Minimum value of prod(1 - 1/p) will produce
# maximum n/totient(n)
print(np.argmin(totient))
```

Running the above results in an output of,

```
510510
1.3519872 seconds.
```

Thus, $$n=\boxed{510\,510}$$ produces the largest value of $$n/\phi(n)$$.