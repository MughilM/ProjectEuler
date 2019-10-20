---
layout: post
title: "Problem 46 - Goldbach's other conjecture"
date: 2017-01-31 19:47
mathjax: true
number: 46
---

## Question

It was proposed by Christian Goldbach that every odd composite number that can be written as the sum of a prime and twice a square.


$$
\begin{aligned}
9 &= 7 + 2\times 1^2
\\
15 &= 7 + 2\times 2^2
\\
21 &= 3 + 2\times 3^2
\\
25 &= 7 + 2\times 3^2
\\
27 &= 19 + 2\times 2^2
\\
33 &= 31 + 2\times 1^2
\end{aligned}
$$


It turns out that the conjecture was false.

What is the smallest odd composite that cannot be written as the sum of a prime and twice a square?

## Answer

We can do this problem with a double loop. First we keep looping through all odd composite numbers. Then, we keep subtracting twices of squares from it until the max we can subtract, and see if it ever results in a prime. For example, with $$21$$, we start with $$21 - 2\times 1^2$$. This is $$20$$, which isn't prime, so we move to the next. $$21-2\times2^2=13$$, which is prime, so this odd number can be written this way. If we ever find a number which exhausted all twices of squares, then that is our solution.

First, we go to each odd number $$x$$ in general. We need a small check to see if it's prime before we start checking sums. If it's not prime, then we start from 1 and go to $$\sqrt{x/2}$$ and check if doubling it and subtracting it from $$x$$ gives us a prime.

```python
n = 5
primes = []

while True:
    # Check if the number is prime
    if all(n % p for p in primes):
        primes.append(n)
    # Check if twice a square being subtracted
    # from this number results in a prime.
    else:
        if not any((n - 2 * k * k) in primes for k in range(1, int((n / 2) ** 0.5) + 1)):
            break
    n += 2

print(n)
```

Notice the prime check is not something overly complicated. Running the program results in an output of,

```
5777
0.08932956603362126 seconds.
```

Therefore, **5777** is the first odd composite number that can't be written as the sum of a prime and twice a square.