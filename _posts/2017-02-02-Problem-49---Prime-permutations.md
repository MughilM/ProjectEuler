---
layout: post
title: "Problem 49 - Prime permutations"
date: 2017-02-02 09:53
mathjax: true
number: 49
---

## Question

The arithmetic sequence, 1487, 4817, 8147, in which each of the terms increases by 3330, is unusual in two ways: (i) each of the three terms are prime, and, (ii) each of the 4-digit numbers of are permutations of one another.

There are no arithmetic sequences made up 1-, 2-, or 3-digit primes, exhibiting this property, but there is one other 4-digit increasing sequence.

What 12-digit number do you form by concatenating the three terms in this sequence?

## Answer

At first glance, it seems we will need 3 for loops to go through all triplets of prime numbers. However, there is one thing we can do to reduce the time complexity. For any **pair** of primes, the difference between them is already defined. Therefore, we just add this difference to the larger of the two numbers, and see if the resulting number is in our prime list. Of course, we should check if even our pair of primes are permutations of one another, since that is more restrictive.

To find equality of permutations, we can convert each number to a string and sort them, and see if the sorted strings are exactly equal. The double for loop is below. Like with previous problems, I use the `primesieve` package to grab all 4-digit primes:

```python
numbers = {}
primes = primesieve.primes(10 ** 3, 10 ** 4)
for i in range(len(primes)):
    for j in range(i + 1, len(primes)):
        prime1 = primes[i]
        prime2 = primes[j]
        # check if a permutation
        if sorted(str(prime1)) == sorted(str(prime2)):
            diff = prime2 - prime1
            prime3 = prime2 + diff
            if sorted(str(prime2)) == sorted(str(prime3)) and prime3 in primes:
                print(prime1, prime2, prime3)
```

Running it results in the two such triplets the problem is talking about:

```
1487 4817 8147
2969 6299 9629
0.9628449275774557 seconds.
```

Concatenating the triplet that isn't in the problem gives us **296962999629**.