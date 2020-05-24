---
layout: post
title: "Problem 27 - Quadratic primes"
date: 2016-05-06 20:01
mathjax: true
number: 27
---

## Question

Euler discovered the remarkable quadratic formula:


$$
n^2+n+41
$$


It turns out that the formula will produce 40 primes for the consecutive integer values $$0\leq n\leq 39$$. However, when $$n = 40, 40^2+40+41=40(40+1)+41$$ is divisible by 41, and certainly when $$n=41,41^2+41+41$$ is clearly divisible by 41.

The incredible formula $$n^2-79n+1601$$ was discovered, which produces 80 primes for the consecutive values $$0\leq n\leq 79$$. The product of the coefficients, -79 and 1601, is -126479.

Considering quadratics of the form:


$$
n^2+an+b,\text{ where } |a|<1000\text{ and } |b|\leq 1000
$$


where the bars indicate absolute value.

Find the product of the coefficients, $$a$$ and $$b$$, for the quadratic expression that produces the maximum number of primes for consecutive values of $$n$$, starting $$n=0$$.

## Answer

At first glance, it seems like we have to all possible values $$a$$ and $$b$$ satisfying the condition above. However, there are a couple of observations we can make.

Let $$f(n) = n^2+an+b$$. The problem says that $$f(0)$$ should be prime. However, plugging in $$n=0$$, we get that $$f(0) = b$$. Therefore, $$\mathbf{b}$$ **must be prime**. Additionally, it must also be positive. There are a bit more than 150 primes below 1000, so that drastically reduces the search space. Additionally, $$b\neq 2$$, because if $$n$$ is even, then $$n^2 + an$$ will be even, and the resultant $$f(n)$$ will also be even. There is only one even prime (2) so it is impossible to have more than 2 consecutive primes.

In addition to saying $$b$$ is prime (and consequently odd, since $$b\neq 2$$), we can also say that $$a$$ is odd. Observe what happens when $$a$$ is even and $$n$$ is odd:


$$
\begin{aligned}
f(Odd) &= Odd^2 + Even\times Odd+Odd \\
&= Odd + Even + Odd
\\ &=
Odd + Odd \\
&= Even
\end{aligned}
$$


So every other $$n$$, $$f(n)$$ will turn out to be even, which leads to non-prime integers. On the other hand, if $$a$$ is odd, then $$n^2+an$$ will always to turn out to be even, and consequently $$f(n)$$ will be odd, which allows for possibilities for prime numbers.

Finally, $$a>-b$$, because otherwise, that allows $$f(n)$$ to be negative. In the end, we need a double for loop that loops our constrained search space. For testing if $$f(n)$$ is prime, we can do the generic loop until $$\sqrt{f(n)}$$. The `primesieve` package once again makes it appearance to find all prime numbers under 1000.

```python
def isPrime(p):
    if p <= 1:
        return False
    if p == 2:
        return True
    if p % 2 == 0:
        return False
    for i in range(3, int(p ** 0.5) + 1):
        if p % i == 0:
            return False
    return True

limit = 1000
Bs = primesieve.primes(limit)[1:]
maxChain = 40
maxA = 1
maxB = 41
for b in Bs:
    for a in range(-b + 2, limit):
        # Make the function
        f = lambda x: x ** 2 + a * x + b
        # We know f(0) = b is prime, so start
        # from n = 1.
        n = 1
        while isPrime(f(n)):
            n += 1
        # Check to see if chain length is bigger...
        if n > maxChain:
            maxChain = n
            maxA = a
            maxB = b

print(maxA, maxB, maxChain, maxA * maxB)
```

Running the code above results in an output of,

```
-61 971 71 -59231
0.33305679045121134 seconds.
```

Thus, $$f(n) = n^2 - 61n+971$$ produces a chain of 71 consecutive primes from $$0\leq n\leq 70$$. The product of $$a$$ and $$b$$ is thus **-59231**.