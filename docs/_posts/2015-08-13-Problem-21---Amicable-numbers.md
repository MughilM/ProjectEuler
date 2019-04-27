---
layout: post
title: "Problem 21 - Amicable numbers"
date: 2015-08-13 10:21
mathjax: true
number: 21
---

## Question

Let $$d(n)$$ be defined as the sum of proper divisors of $$n$$ (numbers less than $$n$$ which divide evenly into $$n$$). If $$d(a) = b$$ and $$d(b) = a$$, where $$a\neq b$$, then $$a$$ and $$b$$ are an amicable pair and each of $$a$$ and $$b$$ are called amicable numbers.

For example, the proper divisors of $$220$$ are $$1,2,4,5,10,11,20,22,44,55$$ and $$110$$; therefore $$d(220) = 284$$. The proper divisors of $$284$$ are $$1,2,4,71$$ and $$142$$; so $$d(284) = 220$$.

Evaluate the sum of all the amicable numbers under 10000.

## Answer

No matter what happens, we are going to have to find the sum of the proper divisors for every number up to 10000 to see if it's part of a pair. So the main focus should be aiming to make the process of finding the sum as efficient as possible.

For starters, we could test every number up until $$\frac{n}{2}$$. However, this will cause slowdowns for large numbers. However, what if we had the prime factorization of $$n$$? We can certainly work out the divisors of $$n$$ if we had the prime factorization. For example, $$220 = 2^2\times 5\times 11$$. Every possible number that can be made given these integers and powers i.e. two 2's, one 5, and 11, is a divisor of $$n$$. For example, one 2 and one 5 gives us 10, while two 2's and one 11 gives us 44. In this way, the divisors are:


$$
1 \\ 2 \\ 2^2 \\ 5 \\ 2\times5 \\ 11 \\ 2^2\times 5 \\ 2\times11 \\ 2^2\times11 \\ 5\times11 \\ 2\times5\times11 \\ 2^2\times5\times11
$$


Written another way, this is


$$
2^0 5^0 11^0 \\
2^1 5^0 11^0 \\
2^2 5^0 11^0 \\
2^0 5^1 11^0 \\
2^1 5^1 11^0 \\
2^0 5^0 11^1 \\
2^2 5^1 11^0 \\
2^1 5^0 11^1 \\
2^2 5^0 11^1 \\
2^0 5^1 11^1 \\
2^1 5^1 11^1 \\
2^2 5^1 11^1
$$


Notice we are just going through all possibilities of allocating the maximum number of exponents allowed for each factor. Now, watch how things simplify if we try to add them all:
$$
\begin{align*}
2^0 5^0 11^0 + 2^1 5^0 11^0 + \cdots + 2^2 5^1 11^1 &=
	(2^0 + 2^1 + 2^1)(5^0 11^0 + 5^1 11^0 + 5^0 11^1 + 5^1 11^1)
\\ &=
(2^0 + 2^1 + 2^2)(5^0 + 5^1)(11^0 + 11^1)
\end{align*}
$$
So it turns out we can just sum all the powers of each prime factor and multiply them to get the sum of the divisors! Of course, we'll have to remember to subtract $$n$$ at the end because we want *proper* divisors. There is one more simplification we can do here. Notice each sum in the parentheses is a geometric sum with the common ratio being the respective prime factor. Recall that if $$a$$ is the first term and $$r$$ is the common ratio, then $$\sum_{i=0}^n ar^i = a\left(\frac{1-r^{n+1}}{1-r}\right)$$. In our case, the first term is 1, and we can also switch the subtraction in both the numerator and denominator. So, for each prime factor $$p_i$$, with exponent $$k$$, the sum of $$1 + p_i^1 + p_i^2 + \cdots + p_i^k = \frac{p_i^{k+1} - 1}{p_i - 1}$$. Thus, we can simply evaluate this and multiply them together. Don't forget to subtract $$n$$ at the end due to wanting proper divisors. In the end, if we have $$m$$ prime factors $$p_1,p_2,\cdots,p_m$$ with exponents $$k_1,k_2,\cdots,k_m$$ respectively, then


$$
d(n) = \prod_{i=1}^m\frac{p_i^{k_i+1}-1}{p_i-1}-n
$$


Below is the code for evaluating $$d(n)$$, given a list of prime numbers up to our limit. This was generated using the `primesieve` package.

```python
def d(n, primes):
    # 0 and 1 have no proper divisors, so return 0.
    if n <= 1:
        return 0
    # If n is a prime, then by definition d(n) = 1
    if n in primes:
        return 1
    # underPrimes = primes[primes <= n ** 0.5]
    primeFacts = []
    powers = []
    ### PRIME FACTORIZATION
    # Test each prime to see if it divides
    num = n
    for p in primes:
        # Keep dividing until it can't.
        # Keep a counter for the number of times.
        power = 0
        while num % p == 0:
            num //= p
            power += 1
        # If we've divided, then this is a factor.
        if power > 0:
            primeFacts.append(p)
            powers.append(power)
        # If num became 1, then we've exhausted all
        # factors, no need to check.
        if num == 1:
            break
    ### CALCULATING SUM OF PROPER DIVISORS
    # Take each prime and respective power,
    # and do (p^(k+1) - 1)/(p-1)
    s = 1
    for prime, power in zip(primeFacts, powers):
        s *= (prime ** (power + 1) - 1) // (prime - 1)
    # The product includes n, which isn't a
    # PROPER divisor, so subtract.
    s -= n
    return s
```

Once we have this, we can make a list of all the sums of proper divisors for each number up to our limit of 10000. Afterwards, we run through the list, seeing if we can find amicable pairs.

```python
limit = 10000
primes = primesieve.primes(limit)
# For each number from 0 to 9999, find
# d(n).
Ds = [d(i, primes) for i in range(limit)]
# Now go through the list and search
# for amicable numbers.
s = 0
for n, propSum in enumerate(Ds):
    # The first check is necessary because
    # we don't want perfect numbers.
    if n != propSum and propSum < limit and Ds[propSum] == n:
        s += n
        print(n, end=' ')
print()
print(s)
```

In the end, running the code above results in the output of,

```python
220 284 1184 1210 2620 2924 5020 5564 6232 6368 
31626
0.26177382716049385 seconds.
```

Thus, **31626** is the sum we are looking for. As a bonus, the code also prints out all amicable numbers below 10000 on the first line. Finally, we will most likely use some variation of the methods outlined above in future problems that deal with sums of divisors.