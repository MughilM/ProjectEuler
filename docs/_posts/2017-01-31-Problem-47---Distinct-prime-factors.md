---
layout: post
title: "Problem 47 - Distinct prime factors"
date: 2017-01-31 20:00
mathjax: true
number: 47
---

## Question

The first two consecutive numbers to have two distinct prime factors are:


$$
\begin{aligned}
14 &= 2\times 7
\\
15 &= 3\times 5
\end{aligned}
$$


The first three consecutive numbers to have three distinct prime factors are:


$$
\begin{aligned}
644 &= 2^2\times 7\times 23
\\
645 &= 3\times 5\times 43
\\
646 &= 2\times 17\times 19
\end{aligned}
$$


Find the first four consecutive integers to have four distinct prime factors each. What is the first of these numbers?

## Answer

The first order of business is to try and perform prime factorization, and quickly. It's been proven that the largest prime factor of a number $$n$$ will not exceed $$\sqrt{n}$$. Therefore, we only need to check until this value. Additionally, once we find a prime factor, we can fully divide it out before moving on to the next one. This reduces the ongoing value as much as possible. The function is below, where we are given $$n$$ and a sufficiently large set of `primes`, generated through a sieve or external package.

```python
def distinctPrimeFacts(n, primes):
    facts = []
    i = 0
    while n != 1 and primes[i] ** 2 <= n:
        # Divide out the entire prime
        p = primes[i]
        while n % p == 0:
            facts.append(p)
            n //= p
        i += 1
    facts.append(n)
    return facts
```

Now, all we have to do is start from $$n=647$$, and loop until we find a string of 4 integers that each have exactly 4 distinct prime factors. However, there are a couple of tweaks we can do to make this slightly faster. Say that the four integers starting from $$n$$ i.e. $$n, n+1, n+2, n+3$$ have 4, 4, 4, and **3** distinct prime factors respectively. Obviously, this $$n$$ is not a candidate. However, we don't have to check the 4 integers from $$n+1$$, because we know $$n+3$$ has only 3 distinct prime factors. In fact, the next number we check can be $$n+4$$, since we have no information on that yet. 

In this way, we can skip a lot of integers. Below is the code to find the starting number $$n$$, and the prime factors each of $$n,n+1,n+2$$ and $$n+3$$. For the sufficiently large set of primes, I chose a limit of 500 thousand.

```python
# Start from 647...
primes = primesieve.primes(500000)
n = 647
while True:
    # Calculate starting from this number
    # the number of distinct prime factors.
    count = 0
    while len(set(distinctPrimeFacts(n, primes))) == 4:
        count += 1
        n += 1
    if count == 4:
        for i in range(n - 4, n):
            print(i, '==>', distinctPrimeFacts(i, primes))
        break
    n += 1
```

The output is,

```python
134043 ==> [3, 7, 13, 491]
134044 ==> [2, 2, 23, 31, 47]
134045 ==> [5, 17, 19, 83]
134046 ==> [2, 3, 3, 11, 677]
1.6933869983913123 seconds.
```

Therefore, the set of 4 integers we are looking for are,


$$
\begin{aligned}
134043 &= 3\times 7\times 13\times 491
\\
134044 &= 2^2\times 23\times 31\times 47
\\
134045 &= 5\times 17\times 19\times 83
\\
134046 &= 2\times 3^2\times 11\times 677
\end{aligned}
$$


and our starting integer is **134043**.

