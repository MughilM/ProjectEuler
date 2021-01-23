---
layout: post
title: "Problem 206 - Concealed Square"
date: 2019-03-24 11:17
number: 206
mathjax: true
---

> Find the unique positive integer whose square has the form 1\_2\_3\_4\_5\_6\_7\_8\_9\_0, where each "_" is a single digit.

<!--more-->

## Answer

An extremely short problem description. Remember we are looking for a square. The first thing to note is that the square ends in a 0. Normally you multiply by 10 to get a number ending in 0. However, 10 isn't a square....100 is though. But if we multiply by 100, we end with 2 zeroes instead. Thus, the number actually looks like 1\_2\_3\_4\_5\_6\_7\_8\_900. We can further cut off the two zeroes and instead look for a square that looks like 1\_2\_3\_4\_5\_6\_7\_8\_9.

This is a smaller number than before. We want a square that ends in 9. Only $$3^2=9$$ and $$7^2=49$$. So the square root of this number must end in a 3 or 7. At this point, we can write a brute-force loop to test numbers.

### Implementation

The largest possible number is where only 9s are filled in i.e. 19293949596979899. The largest square less than this is $$138\,902\,662^2=19\,293\,949\,510\,686\,244$$. You can see the pattern breaks after 5. We start with 138902657, since we want a number ending in 3 or 7. Then we alternatively subtract 4 and 6 to keep the last digit 3 or 7. A short function to test if the square follows the pattern is also needed.

```python
# Function to determine if the number
# follows the pattern
def isValid(n):
    # Only need to check until the "8"
    # because our number ends in "900".
    for k in range(1, 9):
        if int(str(n)[2 * (k - 1)]) != k:
            return False
    return True

# Start with the root of the largest number
# 19293949596979899 -> 138,902,662
n = 1389026657
subtracting = 4
while not isValid(n ** 2):
    n -= subtracting
    # Alternate subtracting 4 and 6
    # to get numbers ending in 3 and 7
    if subtracting == 4:
        subtracting = 6
    else:
        subtracting = 4
print(f'{n * 10}^2 = {n * n * 100}')
```

Running this `while` loop, we get

```
1389019170^2 = 1929374254627488900
0.0008701999999999877 seconds.
```

Thus, our unique number whose square follows the above pattern is **1389019170**. We did this in quick time because the answer is actually fairly close to the upper limit. We probably would've been in trouble if we started at the bottom and counted up.