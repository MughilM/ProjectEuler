---
layout: post
title: "Problem 110 - Diophantine reciprocals II"
date: 2019-08-21 15:17
mathjax: true
number: 110
---

> In the following equation, $$x$$, $$y$$, and $$n$$ are positive integers.
>
> 
> $$
> \frac{1}{x} + \frac{1}{y} = \frac{1}{n}
> $$
> 
>
> It can be verified that when $$n=1260$$ there are 113 distinct solutions and this is the least value of $$n$$ for which the total number of distinct solutions exceeds one hundred.
>
> What is the least value of $$n$$ for which the number of distinct solutions exceeds four million?
>
> NOTE: This problem is a much more difficult version of [Problem 108]({{ site.url }}{{ site.baseurl }}{% post_url 2019-08-20-Problem-108---Diophantine-reciprocals-I %}) and as it is well beyond the limitations of a brute force approach it requires a clever implementation.

<!--more-->

## Answer

All right, this is the big brother of [Problem 108]({{ site.url }}{{ site.baseurl }}{% post_url 2019-08-20-Problem-108---Diophantine-reciprocals-I %})! Please read the solution to that problem first as I'll be referencing it throughout.

To recap, we are looking for the least value $$n$$ such that $$n^2$$ has at least $$2\times 4000000 = 8000000$$ factors. Can we use the same method in this problem? Not quite, because in 108, I made an important assumption about the exponents of the prime factorization of $$n^2$$. **I assumed they were either 2 or 4**. Here, since our threshold is much larger, I can't say anything about the size of the exponents.

However, I *can* say something about the maximum prime factor in the solution. Since 3 is the minimum exponent we would multiply in $$n^2$$, this means that if $$L=\lceil\log_3 8\,000\,000\rceil$$, then the maximum prime factor is the $$L$$th one. In this case, $$L=15$$, and the 15th prime number is **47**.

So starting from 2, can we generate numbers with prime factors 2 through 47 until we reach the first one that exceeds 8 million? The answer is **no**, or rather, we can, it would just take insanely long. The problem is that with the presence of 15 prime factors, we would need 15 pointers pointing to locations in a potentially really long list. Depending on how large the final solution is, this could end up being a very unwieldy list.

The main bottleneck of the method is that we need to keep a list going. Even if we were able to prune the beginning of the list (once the pointers have past it), the operations in calculating the values, seeing the minimum and appending is essentially quadratic in the number of prime factors we have.

**Is there a method where we don't need to keep a list, and not be constrained in generating numbers in order?** Well, if there was a **limit** that we know a solution has to exist below, then we could use recursion to generate numbers! 

## Finding an upper bound

Now then, we need a large-ish value that *might* have at least 4 million solutions, but something below it *definitely* does. How about the factorial function? It's one of the fastest growing functions, so surely a solution should exist beneath it. We can find the least $$K$$ such that $$K!$$ has at least 4 million solutions. Recall we need the prime factorization of $$K!$$ to compute the number of solutions. Because this is a factorial, there is an algorithm we can use called [Legendre's formula](https://en.wikipedia.org/wiki/Legendre's_formula). Essentially, the formula counts how many of each prime factor there are in the factorial by using divisibility.

Okay, so once we've found the prime factorization of a $$K!$$, we know that **whatever the minimum solution is, the exponents should NOT exceed those of the factorial**. Because we've already computed the maximum prime number possible (47), and we have limits on the exponents, we can easily loop through all possible numbers by incrementing the exponents!

This is most easily done by recursion (rather than a 15-depth for loop). With recursion, we can also decrease the upper bound as we go, due to already multiplied by previous factors i.e. if we have $$2^8$$ so far, then the rest of the prime factors together shouldn't exceed that $$\frac{K!}{2^8}$$.

Okay, so we have a roadmap!

1. Find the least integer $$K$$ such that $$K!$$ has at least 4 million solutions, or 8 million factors.
2. Of the prime factors in that factorization of $$K!$$, loop through all integers that are less than $$K!$$.
3. Any integers that have at least 8 million factors get added to a running list.
4. The minimum value of this list is our answer.

## Legendre's formula

For conciseness, please see [this link](https://en.wikipedia.org/wiki/Legendre's_formula) for a detailed statement of the formula, as well as an example of it in action. Below is the method I coded up for calculating the prime factorization of a factorial.

```python
def primeFactorFactorial(n):
    # Use Legendre's formula.
    # We check until the greatest prime less than
    # n.
    primes = primesieve.primes(n)
    powers = []
    # For each prime...until halfway
    i = 0
    while primes[i] <= n/2:
        prime = primes[i]
        # Find largest power of prime
        # less than n...
        stopInt = int(math.log(n, prime))
        padic = sum(n // (prime ** np.arange(1, stopInt+1, dtype=int)))
        powers.append(padic)
        i += 1
    # All primes that are more than
    # half are guaranteed to only have
    # one power. So extend the powers
    # array by however many 1s...
    powers.extend([1] * (len(primes) - i))
    return np.array(primes, dtype=object), np.array(powers, dtype=object)
```

I've utilized `numpy` to make the calculations more concise. When we run this with our target of 8 million factors, we get that $$K = 34$$, so $$34!$$ is an upper bound that at least 8 million factors.

## Recursive function

In a call of the function, we keep track of which prime we've chosen, the list of all prime factors, the current prime divisors that are part of our product, and the remaining limit.

- If our location goes off the end of the list, then we yield the number through the current prime divisors.
- Otherwise, we loop all possible exponents of the current prime, and recurse with each chosen power. The limit of the power is based on the logarithm of the limit, whose base is the current prime. For example, with our original limit of $$34!$$, say we had $$2^2\times 3^2$$ as our current running total. The power limit for 5 would be $$\lceil \log_5\left(\frac{34!}{2^2\times3^2}\right)\rceil + 1$$. In this way, our limit actually decreases fairly quickly, and the recursion depth isn't as deep as it would be.
- An additional point: Since we're only worried about squares, the exponents will only be **even**, so we'll increment by 2  up till the loop limit.

```python
def loopThroughNums(currDivLoc, currDivPowers, divisors, limit):
    # Base case is when we're over
    # the current divisor location.
    # In this case, we can yield a value.
    if currDivLoc >= len(currDivPowers):
        yield currDivPowers, np.prod(divisors ** currDivPowers)

    # For each possible divisor value
    # in the current location, yield all
    # possible values with that power...
    else:
        if currDivLoc == 0:
            loopLimit = math.ceil(math.log(limit, divisors[currDivLoc]))
        else:
            loopLimit = min(currDivPowers[currDivLoc - 1] + 1, math.ceil(math.log(limit, divisors[currDivLoc])))
        powerCopy = np.array(currDivPowers)
        # Only doing square numbers, so go by 2s
        for powerVal in range(0, loopLimit, 2):
            powerCopy[currDivLoc] = powerVal
            for product in loopThroughNums(currDivLoc + 1, powerCopy, divisors,
                                           limit / np.prod(divisors[currDivLoc] ** powerCopy[currDivLoc])):
                yield product
```

## Putting it all together

Now we have our two functions, it's straightforward to combine them. We first run the first function to find the factorial upper bound. Then, we give our recursive function the list of primes (which remember the maximum possible is 47), collect the numbers which have at least 8 million factors into a list, and take the minimum of that list.

```python
solsRequired = 4000000
factLimit = (solsRequired + 1) * 2 - 1
# Get the first n primes where n is ceil(log_2(factLimit))
primes = np.array(primesieve.n_primes(math.ceil(math.log(factLimit, 2))), dtype=object)
# Get the factorial amount which has at least factLimit factors...
n = 2
_, powers = primeFactorFactorial(2)
while np.prod(powers + 1) < factLimit:
    n += 1
    _, powers = primeFactorFactorial(n)

print('{}! has at least {} solutions.'.format(n, solsRequired))

# Now we loop through all SQUARE numbers
# meaning prime factorizations with even numbers
# that are less than n! Any number with at least
# factLimit factors, we yield and collect the
# integer value to a list. Finally,
# we take the minimum of that list, which becomes
# our answer.
possibilities = []
count = 0
for powers, value in loopThroughNums(0, np.zeros(len(primes), dtype=object), primes, math.factorial(n)):
    count += 1
    if np.prod(powers + 1) > factLimit:
        possibilities.append(int(value ** 0.5))

print('Checked', count, 'values.')
print('Minimum value is', min(possibilities))
```

Running this code results in an output of,

```
34! has at least 4000000 solutions.
Checked 47204 values.
Minimum value is 9350130049860600
8.538059700000002 seconds.
```

We see that our loop had to check 47204 numbers below 34! that ended up having at least 8 million factors. The minimum of these, namely the smallest integer that has at least 4 million solutions is **9350130049860600**. What a massive number...