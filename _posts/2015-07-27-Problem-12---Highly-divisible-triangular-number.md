---
layout: post
title: "Problem 12 - Highly divisible triangular number"
date: 2015-07-27 10:30
mathjax: true
number: 12
---

## Question

The sequence of triangle numbers is generated by adding the natural numbers. So the 7<sup>th</sup> triangle number would be 1 + 2 + 3 + 4 + 5 + 6 + 7 = 28. The first ten terms would be:

<p align="center">1, 3, 6, 10, 15, 21, 28, 36, 45, 55, ...</p>
Let us list the factors of the first seven triangle numbers:

- **1**: 1
- **3**: 1, 3
- **6**: 1, 2, 3, 6
- **10**: 1, 2, 5, 10
- **15**: 1, 3, 5, 15
- **21**: 1, 3, 7, 21
- **28**: 1, 2, 4, 7, 14, 28

We can see that 28 is the first triangular number to have over five divisors.

What is the value of the first triangle number to have over five hundred divisors?

## Answer

A basic solution is test every triangle number, using the formula above, and find its factors by testing every number up to it. However, there are a couple of optimizations we can make. First, we note that the $$n^{th}$$ triangular number $$T_n = \sum_{i=1}^n i = \frac{n(n+1)}{2}$$. So that is one loop that disappears almost immediately. Additionally, for finding factors, we only have to test numbers up until $$\sqrt{T_n}$$, since we can add pairs of factors. If $$T_n$$ turns out to be a square number, however, then we need to add the extra factor. In code, this is:

```python
def factors(n):
    facts = []
    for i in range(1, int(n ** 0.5)):
        if n % i == 0:
            facts.extend([i, n // i])
    # See if the number is a perfect square
    if n ** 0.5 == int(n ** 0.5):
        facts.append(n ** 0.5)
    return facts

n = 1
while True:
    num = n * (n + 1) // 2
    f = factors(num)
    if len(f) > 500:
        print(num)
        break
    n += 1
```

Running the code gives us,

```
76576500
3.971642074074074 seconds.
```

Thus, **76576500** is our answer. However, the program is slightly slow, evidenced by the time taken. Can we make this faster? Well, notice that the problem says we have to find the *number* of divisors, not the divisors themselves. In fact, if we have the prime factorization of a number, then it is extremely simple to find the number of divisors of the number. 

For example, $$48 = 2^4\times 3$$. We take each exponent, add one to it, and multiply them together to get the number of divisors of 48. In this case, the number of divisors should be $$(4+1)(1+1) = 5(2) = 10$$. And indeed, the 10 divisors of 48 are 1, 2, 3, 4, 6, 8, 12, 16, 24, 48. Using `primesieve` we can grab the primes which divide into each number, and calculate the number of divisors as just described. The code now is,

```python
def numOfFactors(n):
    # Get prime numbers less
    # than square root of n
    primes = primesieve.primes(n ** 0.5)
    # Filter...
    primes = [p for p in primes if n % p == 0]
    # Now we need to find powers...
    prod = 1
    for p in primes:
        count = 0
        while n % p == 0:
            count += 1
            n //= p
        # Multiply by one more
        # than the number of powers
        prod *= (count + 1)
    return prod
    
n = 8
while True:
    num = n * (n + 1) // 2
    f = numOfFactors(num)
    if f > 500:
        print(num)
        break
    n += 1
```

Running this now gives,

```
76576500
0.7850484938271605 seconds.
```

Notice that the time is much faster.