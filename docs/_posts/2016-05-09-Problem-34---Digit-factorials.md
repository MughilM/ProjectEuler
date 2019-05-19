---
layout: post
title: "Problem 34 - Digit factorials"
date: 2016-05-09 17:27
mathjax: true
number: 34
---

## Question

145 is a curious number, as 1! + 4! + 5! = 1 + 24 + 120 = 145.

Find the sum of all numbers which are equal to the sum of the factorial of their digits.

Note: as 1! = 1 and 2! = 2 are not sums they are not included.

## Answer

Firstly, there is no upper bound given in the problem, implying we need to find it. Say our test number has $$k$$ digits. The largest this number can be is a set of $$k$$ 9's. In this case, the largest this number can be is $$k\times9!$$. If $$k=8$$, then this value is $$8\times9! = 2\,903\,040$$, only 7 digits. Thus, we only need to check numbers up to 7 digits long. Additionally, since $$7\times9! = 2\,540\,160$$, we only need to check up till this number.

Below is the simple for loop used to calculate the sum of all numbers which satisfy the property.

```python
def factSum(n):
    s = 0
    for d in str(n):
        s += math.factorial(int(d))
    return s

s = 0

for i in range(10, 2540161):
    if factSum(i) == i:
        s = s + i
print(s)
```

The output of the above is,

```python
40730
8.536330512194187 seconds.
```

Therefore, our sum is **40730**. From the output, we can see that we could have safely ignored all 6 and 7 digit numbers and still have gotten the same answer!