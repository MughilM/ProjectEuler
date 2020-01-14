---
layout: post
title: "Problem 80 - Square root digital"
date: 2017-06-21 11:59
number: 80
mathjax: true
---

## Question

It is well known that if the square root of a natural number is not an integer, then it is irrational. The decimal expansion of such square roots is infinite without any repeating pattern at all.

The square root of two is 1.41421356237309504880..., and the digital sum of the first one hundred decimal digits is 475.

For the first one hundred natural numbers, find the total of the digital sums of the first one hundred decimal digits for all the irrational square roots.

## Answer

If there was a way to increase the precision of floating point numbers in Python, this would be a cinch. In the built-in `decimal` package, there is a variable called `getcontext().prec`. This is where we set how many **total** digits we want. Since we are going up to 100, 105 total digits is a safe bet. After that, it is simply a matter of converting each number to a string, finding the decimal point, then summing the first 100 digits after it.

```python
from decimal import *

# square root of 100 is 10 so set precision to 105
# to cover at least 100 decimal digits
getcontext().prec = 105
s = 0
for n in range(2, 100):
    if n ** 0.5 != int(n ** 0.5):
        sqrt = str(Decimal(str(n)) ** Decimal('0.5'))
        sqrt = sqrt.replace('.', '')
        s += sum(int(val) for val in sqrt[:100])
print(s)
```

Running the short loop above gets us an output of,

```
40886
0.05162940000000038 seconds.
```

Thus, our desired sum is **40886**.