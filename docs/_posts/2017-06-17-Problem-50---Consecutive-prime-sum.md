---
layout: post
title: "Problem 50 - Consecutive prime sum"
date: 2017-06-17 11:43
mathjax: true
number: 50
---

## Question

The prime 41, can be written as the sum of six consecutive primes:


$$
41=2+3+5+7+11+13
$$


This is the longest sum of consecutive primes that adds to a prime below one-hundred.

The longest sum of consecutive primes below one-thousand that adds to a prime, contains 21 terms, and is equal to 953.

Which prime, below one-million, can be written as the sum of the most consecutive primes?

## Answers

Note that we are looking for the **longest** sum, not the largest sum. Therefore, since we can generate a list of primes, we will start at 2, and keep adding terms consecutively. If we reach a prime, then we update the length of the sum. We keep going until our sum exceeds 1 million. Once we're done starting our sum from 2, we then move to starting the sum with 3. However, since we have saved the longest sum length so far, we have to add that many terms i.e. If the longest sum starting with 2 is 200 digits long, then we start checking sums starting from 3 from 201 digits onward. Like this, we check through all the primes.

```python
primes = primesieve.primes(1000000)
# Start from 2, and keeping adding on up until
# sum exceeds 1 million.
# If we come across a prime, then update the
# chain length. Once 2s are done, move on to 3,
# but now start from the longest chain length.
chainLength = 1
maxPrime = 2
i = 0
j = i + chainLength + 1
while j < len(primes):
    j = i + chainLength + 1
    s = sum(primes[i:j])
    while s < 1000000:
        if s in primes:
            chainLength = j - i
            maxPrime = s
        j += 1
        s += primes[j - 1]
    i += 1

print(maxPrime, 'with', chainLength, 'consecutive primes.')
```

Running the above results in an output of,

```
997651 with 543 consecutive primes.
1.3174206796941914 seconds.
```

Therefore, the longest sum has 543 terms and evaluates to **997561**.