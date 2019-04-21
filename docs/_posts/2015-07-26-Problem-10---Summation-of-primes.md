---
layout: post
title: "Problem 10 - Summation of primes"
date: 2015-07-26 19:29
mathjax: true
number: 10
---

## Question

The sum of the primes below 10 is 2 + 3 + 5 + 7 = 17.

Find the sum of all primes below two million.

## Answer

Since finding primes is the main crux of this problem, I will refrain from using `primesieve` in the problem. In that case, we can turn to our trusty [Sieve of Eratosthenes](https://en.wikipedia.org/wiki/Sieve_of_Eratosthenes). Essentially, this sieve goes through each number, and crosses off all multiples of that number, excluding itself. The consequence, is that numbers that never get crossed off had no factors, and therefore, are prime. We can easily adapt it to this problem using `numpy` functions, and using a `boolean` array to represent the sieve:

```python
n = 2000000
primes = np.ones(n, dtype=bool)
# Assume all numbers are composite,
# except for 0 and 1
primes[0] = False
primes[1] = False
i = 2
# Only need to check until sqrt(n)
while i ** 2 <= n:
    # Check to see if it's prime.
    # If it is, then update multiples
    # until the end
    if primes[i]:
        p = 2 * i
        while p < n:
            primes[p] = False
            p += i
    i += 1
# Doing np.where will
# get the index values
primeValues = np.where(primes)[0]
print(np.sum(primeValues))
```

Running the above results in,

```python
142913828922
1.0929866437847455 seconds.
```

Thus, **142913828922** is our answer. The timing is not that bad either, considering the large bound of two million.