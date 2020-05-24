---
layout: post
title: "Problem 56 - Powerful digit sum"
date: 2017-06-19 19:55
mathjax: true
number: 56
---

## Question

A googol $$\left(10^{100}\right)$$ is a massive number: one followed by one-hundred zeros; $$100^{100}$$ is almost unimaginably large: one followed by two-hundred zeros. Despite their size, the sum of the digits in each number is only 1.

Considering natural numbers of the form, $$a^b$$, where $$a, b < 100$$, what is the maximum digital sum?

## Answer

Another straightforward problem. As said before, Python is pretty good at handling large numbers such as these. Thus, all we need to loop through all $$a$$ and $$b$$, compute $$a^b$$, and add the digits together, keeping track of the maximum sum:

```python
maxSum = 0
for a in range(1, 100):
    for b in range(1, 100):
        s = sum(int(x) for x in str(a ** b))
        if s > maxSum:
            maxSum = s

print(maxSum)
```

Output of the above is,

```
Max digital sum at 99^95 with a sum of 972
0.2502726912502948 seconds.
```

Thus, $$99^{95}$$ has the maximum digital sum of **972**.