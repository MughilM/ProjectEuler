---
layout: post
title: "Problem 5 - Smallest multiple"
date: 2015-07-23 12:36
mathjax: true
number: 5
---

## Question

2520 is the smallest number that can be divided by each of the numbers by 1 to 10 without any remainder. 

What is the smallest positive number that is evenly divisible by all of the numbers from 1 to 20?

## Answer

Essentially, the question is asking us to find the Least Common Multiple (LCM) of all the numbers from 1 to 20. If we have the prime factorization of each number, then finding the LCM is simple. 

To find the LCM of a list of numbers, you would see the maximum number of times each prime factor appears in the prime factorization of each number. Then, you will multiply each factor this many times, and the result is the LCM. Here is an example using the example given in the question:

- 2 = 2
- 3 = 3
- 4 = 2 x 2
- 5 = 5
- 6 = 2 x 3
- 7 = 7
- 8 = 2 x 2 x 2
- 9 = 3 x 3
- 10 = 2 x 5

Above, we have 4 unique factors appearing: 2, 3, 5, and 7. I have ignored 1 because it is the multiplicative identity. The factor of 2 appears a maximum of **3** times, with the number 8. The factor of 3 appears a maximum of **2** times, with 9. Likewise, 5 and 7 appear a maximum of **1** time each. Therefore, $$LCM(1,2,3,...,10) = 2\times 2\times 2\times 3\times 3\times 5\times 7 = \mathbf{2520}$$.

We can write code for our bound of 20. To find prime factorization, we divide by the lowest prime factor, until we reach the number 1. Other data structures we can use is the `defaultdict`, part of the `collections` package, which hold default values for keys not explicitly set in a hash map. Putting `int` as the argument initializes all dictionary values to 0. Additionally, the `np.unique` method can be used to return the unique values, and the counts of which each occurs.

```python
import primesieve
from collections import defaultdict

def primeFactor(n, primes, factors):
    # Base case:
    if n == 1:
        return factors
    for prime in primes:
        if n % prime == 0:
            factors.append(prime)
            return primeFactor(n // prime, primes, factors)

n = 20
primes = primesieve.primes(n)
maxTimes = defaultdict(int)
# Find factorization for each number up to 20.
allFactors = [primeFactor(i, primes, []) for i in range(2,n+1)]

for factorization in allFactors:
    unique, counts = np.unique(factorization, return_counts=True)
    # Check if the number of times each factor appears is more
    # than the maximum saved.
    for factor, amount in zip(unique, counts):
        maxTimes[factor] = max(maxTimes[factor], amount)

# Go through each factor, and multiply
prod = 1
for factor in maxTimes.keys():
    # We need to multiply the factor maxTimes[factor]
    # number of times....or raise it to the power.
    prod *= factor ** maxTimes[factor]

print(prod)
```

Running the code gives the output of:

```
232792560
0.004698469135802469 seconds.
```

Thus, **232792560** is our answer. With this specific problem, it might have been faster from the onset to do it by hand. However, if our bound was something much larger, such as 100 or 200, then the code generalizes. Though, with those large values, you would have to worry about overflowing.