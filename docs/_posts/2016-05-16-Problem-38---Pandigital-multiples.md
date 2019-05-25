---
layout: post
title: "Problem 38 - Pandigital multiples"
date: 2016-05-16 17:56
mathjax: true
number: 38
---

## Question

Take the number 192 and multiply it by each of 1, 2, and 3:


$$
192\times1=192 \\
192\times2=384 \\
192\times3=576
$$


By concatenating each product we get the 1 to 9 pandigital, 192384576. We will call 192384576 the concatenated product of 192 and (1,2,3).

The same can be achieved by starting with 9 and multiplying by 1, 2, 3, 4, and 5, giving the pandigital, 918273645, which is the concatenated product of 9 and (1,2,3,4,5).

What is the largest 1 to 9 pandigital 9-digit number that can be formed as the concatenated product of an integer with (1,2,...,n) where n > 1?

## Answer

This may seem like such a specific question that it may be hard to start, but noticing a small bit in the problem and some trial and error can help write a simple loop to solve this problem.

Firstly, notice that the problem already gave us a fairly large pandigital, namely 918273645. Therefore, in order for our pandigital to be bigger, our number **must start with a 9**.

Okay that's settled. But...how long is the number. Well imagine our test number $$x$$ is a two-digit number starting with 9. Multiplying by 1 will get us two digits, while multiplying by 2, 3, 4, ..., 9, 10, 11 will get us three digits. However, a **1-9 pandigital is exactly 9 digits long**. Knowing this, can we ever get exactly 9 digits from the concatenated product? The answer is a resounding no, because the concatenated product of $$x$$ and (1,2,3) will have 8 digits, while $$x$$ with (1,2,3,4) will have 11 digits, and so we're over. So it can't have 2 digits.

What about 3 digits? With the same analysis, you can see that $$x$$ with (1,2) has 7 digits, but $$x$$ with (1,2,3) has 11 digits.

4 digits? **Yes**! The concatenated product of $$x$$ with (1,2) will have exactly 9 digits, so it's possible that it might be a candidate.

Can $$x$$ be more than 5 digits? Well, if $$x$$ is $$k$$ digits long, then $$2x$$ is at least $$k+1$$ digits. Therefore, any $$k>4$$ will result in a concatenated product that exceeds 9 digits, and thus cannot be pandigital.

So our candidate $$x$$ is a 4-digit number that starts with 9! Easy enough to write a loop around! To test if a number is 1-9 pandigital, we can call `set()` on it so that only duplicates are saved, and compare that with a set with 1-9. We don't have to test the length of our product because by our analysis, it will always be 9 digits. Additionally, since we are looking for the largest, the first $$x$$ we find coming down from 10000 will be the answer, since our product starts with $$x$$.

```python
for i in range(10000, 9000, -1):
    result = int(str(i) + str(2 * i))
    if set(str(result)) == set('123456789'):
        print(result)
        break
```

Running this short loop takes no time at all, and the output is:

```python
932718654
0.001404046054606883 seconds.
```

Therefore, the largest 1-9 pandigital concatenated product is **932718654**.