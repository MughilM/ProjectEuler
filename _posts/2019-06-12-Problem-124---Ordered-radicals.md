---
layout: post
title: "Problem 124 - Ordered radicals"
date: 2019-06-12 18:45
mathjax: true
number: 124
---

> The radical of $$n$$, $$\text{rad}(n)$$, is the product of the distinct prime factors of $$n$$. For example, $$504=2^3\times3^2\times7$$, so $$\text{rad}(504)=2\times3\times7=42$$.
>
> If we calculate $$\text{rad}(n)$$ for $$1\leq n\leq 10$$, then sort them on $$\text{rad}(n)$$, and sorting on $$n$$ if the radical values are equal we get:
>
> | Unsorted       |                              |      | Sorted         |                              |                |
> | -------------- | ---------------------------- | ---- | -------------- | ---------------------------- | -------------- |
> | $$\mathbf{n}$$ | $$\textbf{rad}\mathbf{(n)}$$ |      | $$\mathbf{n}$$ | $$\textbf{rad}\mathbf{(n)}$$ | $$\mathbf{k}$$ |
> | 1              | 1                            |      | 1              | 1                            | 1              |
> | 2              | 2                            |      | 2              | 2                            | 2              |
> | 3              | 3                            |      | 4              | 2                            | 3              |
> | 4              | 2                            |      | 8              | 2                            | 4              |
> | 5              | 5                            |      | 3              | 3                            | 5              |
> | 6              | 6                            |      | 9              | 3                            | 6              |
> | 7              | 7                            |      | 5              | 5                            | 7              |
> | 8              | 2                            |      | 6              | 6                            | 8              |
> | 9              | 3                            |      | 7              | 7                            | 9              |
> | 10             | 10                           |      | 10             | 10                           | 10             |
>
> Let $$E(k)$$ be the $$k^{\text{th}}$$ element in the sorted $$n$$ column; for example, $$E(4) = 8$$ and $$E(6) = 9$$.
>
> If $$\text{rad}(n)$$ is sorted for $$1\leq n \leq 100000$$, find $$E(10000)$$.

<!--more-->

## Answer

There's no way around it because we are sorting. We are going to calculate the radical for each $$n$$ up to 100000. This limit isn't very large, so we could actually just calculate each number's prime factorization directly and sort it and the program wouldn't be hopelessly slow.

However, there is a much faster way which can scale much easier than the brute force calculation. The key point to realize is that **the radical stays constant as long as the distinct prime factors are the same.** For example, using the example in the problem, *any* number with 2, 3, and 7 as its distinct prime factors (and nothing else), will have a radical of 42. 

Since each integer has a unique prime factorization, we can loop through *all* possible distinct prime factor sets, then add all numbers stemming from those factor sets into an array (along with its constant radical) and finally sort it.

## Looping through factor sets

How do we make sure we do exhaustive search of all possible sets of distinct prime factors? We first define the set size, then using a recursive algorithm, we can loop through all possible numbers such that the product doesn't exceed the limit. 

For example, let's say the set size was 3 and our limit was 100. The set with the smallest product is {2, 3, 5}, which is 30.  Now, keeping 2 and 3 constant, this means $$2\times3\times p \leq 100 \rightarrow p \leq 16.67$$. Thus, the only possibilities for the last factor is 5, 7, 11, and 13. Then, in the next loop, the second factor becomes 5, which leads to the last factor being no more than 10 (7 being the only satisfactory factor). To avoid duplicating the sets, we'll make sure the primes are increasing when going left to right.

Once this set size is finished, we move on to a set size of 4. We keep going until multiplying the smallest $$t$$ prime numbers leads to a number which is larger than our limit. In the 100 case, it is 3. For 1000, it is 4 (2, 3, 5, and 7).

```python
def loopThroughFactorizations(numOfFacts, primes, limit):
    if numOfFacts == 1:
        i = 0
        while (i < len(primes)) and (primes[i] < limit):
            yield [primes[i]]
            i += 1
    else:
        upperBound = limit ** (1 / numOfFacts)
        i = 0
        while (i < len(primes)) and (primes[i] < upperBound):
            for subFactorization in loopThroughFactorizations(numOfFacts - 1, primes[i + 1:], limit / primes[i]):
                yield [primes[i]] + subFactorization
            i += 1
```

Next, for each set we get, we have to adjust the powers of those factors so that we get all integers with those prime factors.

## Looping through all integers with specific prime factors

A similar recursive definition, where we are now adjusting incrementing powers for each. Each time we increment to the next power, we do a recursive call on the remaining prime factors, while simultaneously adjusting the limit. Take the set {2, 3, 7}, with a limit of 1000. First, we put an exponent of 1. Then, whatever powers are on 3 and 7, the product **shouldn't exceed 1000/2 = 500**. To get the maximum power, we simply do the logarithm, with base equal to the prime we are at.

For 2, we have $$\log_2 1000 = 9.97$$, so we loop the power from 1 to 9. Each time we recurse on the {3, 7} set. For a 2-power equal to 3, we have the remaining product not exceeding 1000/8 = 125. Thus, the maximum power on 3 would be $$\log_3 125 = 4.39$$. Like this, we keep recursing until we have one power in which case we just yield the number.

```python
def loopThroughFactorPowers(factorization, limit):
    if len(factorization) == 1:
        base = factorization[0]
        powerLimit = math.log(limit, base)
        for power in range(1, math.ceil(powerLimit)):
            yield [power]
    else:
        prodOfRemaining = 1
        for number in factorization[1:]:
            prodOfRemaining *= number
        base = factorization[0]
        powerLimit = math.log(limit / prodOfRemaining, base)
        for power in range(1, math.ceil(powerLimit)):
            for remPowers in loopThroughFactorPowers(factorization[1:], limit / base ** power):
                yield [power] + remPowers
```

## Putting it together

If you notice the previous two functions, I could've defined a base case at a value of 0, and returned an empty list. However, the problem with that is that it unnecessarily puts an additional call on the stack that doesn't really do anything, and through my experience I've seen it hampers performance just enough so that it adds up over thousands of calls. I use `primesieve.primes` to get my primes, and initialize my array with (1, 1). The cool thing about Python's `sorted()` function, is that if the list contains tuples, it will sort them on the columns left to right. So it will sort on the first value, then the second, and so on. Thus, if we put the radical value first, then the integer, we get the same sorted functionality the problem requires, where we sort on the integer if the radical values are equal.

```python
limit = 100001
primes = primesieve.primes(limit)
rads = [(1, 1)]
# Find the maximum number of primes we can 
# multiply together
maxPrimes = 0
prod = 1
while prod < limit:
    prod *= primes[maxPrimes]
    maxPrimes += 1

for numOfFacts in range(1, maxPrimes):
    for factorization in loopThroughFactorizations(numOfFacts, primes, limit):
        rad = 1
        for factor in factorization:
            rad *= factor
        for factorPowers in loopThroughFactorPowers(factorization, limit):
            number = 1
            for factor, power in zip(factorization, factorPowers):
                number *= factor ** power
            rads.append((rad, number))
E = sorted(rads)
print(E[9999])
```

 Running this medium-size code leads to an output of'

```
(1947, 21417)
0.5366485 seconds.
```

Thus, we have $$E(10000)$$ being the number **21417**, whose radical is 1947.