---
layout: post
title: "Problem 77 - Prime summations"
date: 2018-06-05 12:18
mathjax: true
number: 77
---

## Question

It is possible to write ten as the sum of primes in exactly five different ways:


$$
\begin{aligned}
&7+3
\\
&5+5
\\
&5+3+2
\\
&3+3+2+2
\\
&2+2+2+2+2
\end{aligned}
$$


What is the first value which can be written as the sum of primes in over five thousand different ways?

## Answer

Like with [previous problem]({{ site.baseurl }}{% post_url 2017-06-21-Problem-76---Counting-summations %}), I researched for a possible recursive definition when using only prime numbers. Thankfully, [this Math StackExchange post](https://math.stackexchange.com/a/89661) shows a way to do it. The reason why it works is based on Euler transformations, which are an advanced math topic that is too out of scope to cover here. Please read the post if you are interested!

Essentially, the number of partitions $$\kappa(n)$$ for a number $$n$$ where each partition consists of only prime numbers is given by


$$
\kappa(n) = \frac{1}{n}\left(\text{sopf}(n) + \sum_{j=1}^{n-1}\text{sopf}(n)\kappa(n-j)\right)
$$


where $$\kappa(1) = 0$$. The function $$\text{sopf}(n)$$ is the **sum of prime factors** function, which is the sum of the **distinct** prime factors of $$n$$. 

Due to the explosion in the answer to the regular partitions, I wager $$n$$ would not get too big, and we don't need to do anything fancy. Instead, we could just loop through each number $$i$$ from $$2$$ to $$n$$. If we encounter an $$i$$ that divides into $$n$$, then $$i$$ is included in the sum of $$\text{sopf}(n)$$, and then we **completely** divide it out. This is so the number gets as small as possible as quickly as possible.

```python
def sopf(n):
    s = 0
    i = 2
    while i <= n:
        if n % i == 0:
            s += i
            # Completely divide out the factor
            while n % i == 0:
                n //= i
        else:
            i += 1
    return s
```

As for calculating $$\kappa(n)$$ itself, we can use `numpy` to reverse the array and multiply them in a single line. In the end, the code looks like:

```python
sopfs = [0, 0]
kappas = [0, 0]
n = 2
while kappas[-1] <= 5000:
    sopfs.append(sopf(n))
    kappas.append(1/n * (sopfs[n] + sum(sopfs[1:n] * np.array(kappas[1:][::-1]))))
    n += 1
print(len(kappas) - 1, 'is the first value which can be written in 5000+ ways.')
```

The extra 0 in the beginning is so that the index matches the value of $$n$$. Running this results in,

```
71 is the first value which can be written in 5000+ ways.
0.0018762999999999974 seconds.
```

Thus, as the message says, **71** is the first integer such that there are at least 5000 different ways to write sums.