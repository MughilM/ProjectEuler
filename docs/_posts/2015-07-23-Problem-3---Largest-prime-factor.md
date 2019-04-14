---
layout: post
title: "Problem 3 - Largest prime factor"
date: 2015-07-23 11:50
mathjax: true
---

## Question

The prime factors of 13195 are 5, 7, 13 and 29.

What is the largest prime factor of the number 600851475143?

## Answer

Now, there are a lot of complicated algorithms out there that can quickly prime factorize composite numbers. However, for the purposes of this problem, the number is not extremely large. Therefore, we can take a simple approach. There is a Python package known as `primesieve` that can handle finding prime numbers themselves. Documentation is listed [here](https://pypi.org/project/primesieve/).

The upper bound for the largest prime factor of a number $n$ is in fact $\sqrt{n}$. Using `primesieve` we can quickly generate prime factors and check the largest one on down:

```python
import primesieve

n = 600851475143
primesToCheck = primesieve.primes(n ** 0.5)

for prime in primesToCheck[::-1]:
    if n % prime == 0:
        print(prime)
        break
```

Running this code gives us

```
6857
0.01296553086419753 seconds.
```

Thus, **6857** is our answer.