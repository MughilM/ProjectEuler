---
layout: post
title: "Problem 193 - Squarefree Numbers"
date: 2019-07-11 12:47
number: 193
mathjax: true
---

> A positive integer $$n$$ is called squarefree, if no square of a prime divides $$n$$, thus 1, 2, 3, 5, 6, 7, 10, 11 are squarefree, but not 4, 8, 9, 12.
>
> How many squarefree numbers are there below $$2^{50}$$?

<!--more-->

## Answer

The opposite of squarefree is *squareful*. From the problem, you might guess that there should be fewer squareful numbers than squarefree, and you would be right. Therefore, it would make sense to count all the squraeful numbers and subtract the amount from the limit.

Speaking of the limit, that limit is huge. We can't simply create a bit array to mark all the primes below, because it would take too much space than any modern laptop can handle. However, since we are concerned of numbers which contain squares of primes, we only need to primes up to the square root of the limit, namely $$2^{25}$$ which is doable.

### Counting the Squareful Numbers

How do we count the number of squareful numbers? We can use the principle of inclusion/exclusion. First, we include all the numbers that contain a single square prime e.g. multiples of 4, 9, 16, etc. However, when we do this, we included numbers that had **two** square prime factors twice (multiples of 36, 64, etc.). So we have to *subtract* the amount of numbers that are like these. However, when we do this subtraction, we subtract the numbers that **three** square primes (900 is the smallest such number) one too many times. So we have to add these back in.

In conclusion, if a number has an **odd** number of square primes, we **add** it to our count, and if it has **even** number of square primes, we **subtract** it from our count. 

When we go to implementing, we will actually loop through the distinct products instead of all the multiples. After all, if we know the upper limit $$L$$ and the product $$P$$, then the number of multiples is simply $$\lfloor L/P^2 \rfloor$$.

### Implementation

As stated before, we only need a list of primes up till $$\sqrt{L}$$. Then, we have to find the maximum number of primes that can be part of a product. Here we just a loop on the first couple of primes until the square of the product exceeds the limit. For our limit is 8, meaning the square of the product of the first 9 primes exceeds $$2^{50}$$.

To find all possible products with the given number of prime factors, we can use a recursive function. The function will keep track of the limit, the number of factors left $$x$$, the current product $$P$$, and the list of primes $$p_1,p_2,\dots$$ to check through. The base case is when the number of factors is 1. In this case, we yield the current product multiplied with each prime factor.

For when the number of factors $$x$$ is greater than 1, we loop through each prime in the function call and do the following steps:

1. We keep looping until the current product multiplied by the next $$x$$ factors exceeds the limit.
2. For each chosen prime $$p_i$$, we recursive call is as follows: The recursive prime list is cut-down to be in the range $$[p+1,L/(P\times p_i)]$$. The limit stays the same, since this is needed by each recursive call. The number of factors goes down by 1, the current product is updated through the multiplication with $$p_i$$.

The `yield`s you see in the code is so that we receive the product immediately instead of perhaps only returning a massive list of all the products. It's a useful Python structure for when we only need each value individually.

As stated before, for each product, we add the multiples $$x$$ is odd, and subtract if $$x$$ is even. This can be done quickly using $$(-1)^{x+1}$$. As a final step, since we want the squarefree numbers and not the squareful ones, we subtract the number of squareful numbers from the limit. The addition of 1 is needed because 1 is considered a squarefree number.

```python
def genProducts(primes, limit, factors=1, currProd=1):
    if factors == 1:
        for prime in primes:
            yield currProd * prime
        return
    i = 0
    while currProd * np.prod(primes[i:i + factors]) < limit:
        prime = primes[i]
        for product in genProducts(primesieve.primes(prime + 1, limit / (currProd * prime)), limit=limit,
                                   factors=factors - 1,
                                   currProd=currProd * prime):
            yield product
        i += 1


limit = 2 ** 50
primes = primesieve.primes(limit ** 0.5)
squareFulNums = 0
# Find maximum number of factors...
i = 1
prod = 2
while prod <= limit ** 0.5:
    prod *= primes[i]
    i += 1
maxFactors = i - 1

for numOfFactors in range(1, maxFactors + 1):
    totalProdCount = 0
    for product in genProducts(primes=primes, limit=limit ** 0.5, factors=numOfFactors, currProd=1):
        totalProdCount += (limit // product ** 2)
    squareFulNums += (-1) ** (numOfFactors + 1) * totalProdCount

print(limit - squareFulNums + 1)
```

Our output is thus,

```
684465067343070
12.7108755 seconds.
```

Therefore, the number of squarefree numbers under $$2^{50}$$ is **684465067343070**. It's one of the larger answers we've had, and the time taken supports that. As some extra research, the Mobius function is closely related to this topic.