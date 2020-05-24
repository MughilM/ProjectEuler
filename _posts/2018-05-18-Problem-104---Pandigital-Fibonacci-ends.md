---
layout: post
title: "Problem 104 - Pandigital Fibonacci ends"
date: 2018-05-18 16:51
number: 104
mathjax: true
---

## Question

The Fibonacci sequence is defined by the recurrence relation:


$$
F_n=F_{n-1}+F_{n-2}
\\
F_1=1
\\
F_2=1
$$


It turns out that $$F_{541}$$, which contains 113 digits, is the first Fibonacci number for which the last nine digits are 1-9 pandigital (contain all the digits 1 to 9, but not necessarily in order). And $$F_{2749}$$, which contains 575 digits, is the first Fibonacci number for which the first nine digits are 1-9 pandigital.

Given the $$F_k$$ is the first Fibonacci number for which the first nine digits AND the last nine digits are 1-9 pandigital, find $$k$$.

## Answer

To find the last $$d$$ digits of a number $$n$$, we do $$n\mod 10^d$$. However, if we instead do $$\lfloor n/10^d\rfloor$$, we get all the digits **except for the first one.** Since we are in base 10, doing a logarithm base 10 will get us **one less than** the number of digits in a number. Subtract this by 10, put it as the power, and we have a way to get the first $$d$$ digits of a number.

Finding the leading digits of a number is generally a bit more expensive, so we'll only perform that calculation if we have a 1-9 pandigital at the end. At each step, we perform $$F_k\mod 10^9$$, and if we see a pandigital, we then perform $$\lfloor F_k/10^{\lfloor \log_{10}F_k\rfloor - 8}\rfloor$$. To test whether a number is pandigital, we convert to a string, and the convert it to a set. We then compare it to the set of the digits 1-9. If they're the same, then it's pandigital.

```python
a = 1
b = 1
k = 3
notFound = True
pandigital19 = set('123456789')
while notFound:
    c = a + b
    lastNine = c % 1000000000
    if set(str(lastNine)) == pandigital19:
        # Only check the first 9 digits if the last 9 are okay.
        # Check first 9 is more expensive.
        firstNine = c // 10 ** (int(math.log(c, 10)) - 8)
        if set(str(firstNine)) == pandigital19:
            print('k = ' + str(k) + ' with ' + str(int(math.log(c, 10) + 1)) + ' digits.')
            notFound = False
    a = b
    b = c
    k += 1
```

Running this short loop,

```
k = 329468 with 68855 digits.
11.4568057 seconds.
```

Thus, the **329468th** Fibonacci number, with 68855 digits, has both a 1-9 digital at the beginning and at the end.