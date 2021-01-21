---
layout: post
title: "Problem 187 - Semiprimes"
date: 2020-01-02 14:05
number: 187
mathjax: true
---

> A composite is a number containing at least two prime factors. For example, $$15=3\times5$$; $$9=3\times 3$$; $$12=2\times 2\times 3$$.
>
> There are ten composites below thirty containing precisely two, not necessarily distinct, prime factors: 4, 6, 9, 10, 14, 15, 21, 22, 25, 26.
>
> How many composite integers, $$n<10^8$$, have precisely two, not necessarily distinct, prime factors?

<!--more-->

## Answer

First, because we are only concerned about numbers with two prime factors, the larger of the two **must not be larger than half the limit**. This decreases the number of primes we need to check. 

If we have a prime flag array, where each element is a 0 or a 1, corresponding to whether the position index is a prime, then we can essentially solve this problem with a simple double for loop.

The larger loop will loop through all possible primes the **smaller** of the two factors can be. Since this needs to be smaller than the other factor, we only need to check primes up to the square root of the limit. Next, once we have this factor, we count all the primes that are larger than this prime (including this one) and add it to our running total. By looping through the smaller of the factors, this avoids counting products like $$2\times 3$$ and $$3\times 2$$ twice over.

### Implementation

To get the primes, I use the `numpy` version of `primesieve`. The algorithm is more important here, not finding the primes themselves. Though if you wanted, you can write a sieve function for it. The reason I use the `numpy` version is so that I can easily flags as 1 in a single line. Since these are binary flags, counting the number of primes is the same as computing the sum. This is what you see in the loop I mentioned above.

```python
import primesieve.numpy as pnp

limit = 10 ** 8
# Only need primes halfway
primes = pnp.primes(limit / 2)
# Take as little space as possible with
# np.uint8
primeFlags = np.zeros(limit // 2, dtype=np.uint8)
primeFlags[primes] = 1
del primes  # Get rid of it, we don't need it anymore!

total = 0
i = 0
# The smaller of the two can't exceed the 
# square root of the limit...
while i <= limit ** 0.5 + 1:
    if primeFlags[i]:
        # Count primes up till limit // i to ensure
        # the resulting number does not exceed
        # the limit.
        total += np.sum(primeFlags[i: limit // i + 1])
    i += 1
print(total)
```

Running this short loop,

```
17427258
0.22744560000000003 seconds.
```

Thus, there are **17427258** composite integers below $$10^8$$ with exactly two, not necessarily distinct, prime factors.