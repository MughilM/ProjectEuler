---
layout: post
title: "Problem 52 - Permuted multiples"
date: 2017-06-19 15:07
mathjax: true
number: 52
---

## Question

It can be seen that the number, 125874, and its double 251748, contain exactly the same digits, but in a different order.

Find the smallest positive integer, $$x$$, such that $$2x,3x,4x,5x$$ and $$6x$$, contain the same digits.

## Answer

Nothing too complicated needed. Just keep looping up, and see if the sorted string of digits for the 6 numbers are exactly the same. However, we can make two slight optimizations. Because we are checking 6 multiples, $$x$$ should have at least 6 digits. Also, we only have to check up until $$\lfloor 1000000/6 \rfloor=166$$, because anything bigger than this will lead to $$x$$ having 6 digits, but at least $$6x$$ having 7 digits, ruling it out of contention.

To check if all the multiples have the same digits, I convert each number to a string, sort each string, and finally call `set()` on the list. If the set has exactly 1 element, then each sorted string is the same, meaning each number had the same digits.

```python
x = 100000
while True:
    if x > 10 ** len(str(x)) / 6:
        x = 10 ** len(str(x))
        continue
    # Test x through 6x...
    multiples = [''.join(sorted(str(x * i))) for i in range(1, 7)]
    # If it has one element after set()...
    # then they all have the same digits in different
    # orders.
    if len(set(multiples)) == 1:
        print(x)
        break
    x += 1
```

Running the loop results in an output of,

```
142857
0.45314348766162477 seconds.
```

Therefore, **142857** has all 6 multiples containing the same digits.