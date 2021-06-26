---
layout: post
title: "Problem 16 - Power digit sum"
date: 2015-08-07 20:45
mathjax: true
number: 16
---

## Question

$$2^{15} = 32768$$ and the sum of its digits is $$3 + 2 + 7 + 6 + 8 = 26$$.

What is the sum of the digits of the number $$2^{1000}$$?

## Answer

As said in [a previous problem]({{ site.url }}{% post_url 2015-07-27-Problem-13---Large-sum %}), Python is very good at computing and handling large numbers. As a result, this problem can be done in a paltry two lines, as shown below.

```python
# Create array of digits in 2 ^ 1000
num = [int(i) for i in str(2 ** 1000)]
# Add them up
print(sum(num))
```

Running gives,

```
1366
0.00012523456790123458 seconds.
```

Thus, our answer is **1366** and we're done in a jiff.