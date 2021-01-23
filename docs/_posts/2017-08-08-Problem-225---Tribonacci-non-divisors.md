---
layout: post
title: "Problem 225 - Tribonacci non-divisors"
date: 2017-08-08 09:52
number: 225
mathjax: true
---

> The sequence 1, 1, 1, 3, 5, 9, 17, 31, 57, 105, 193, 355, 653, 1201 ... is defined by $$T_1=1, T_2=1, T_3=1$$ and $$T_n=T_{n-1}+T_{n-2}+T_{n-3}$$.
>
> It can be shown that 27 does not divide any terms of this sequence. In fact, 27 is the first odd number with this property.
>
> Find the 124th odd number that does not divide any number of this sequence.

<!--more-->

## Answer

If $$a$$ is divisible by $$b$$, then you get a remainder of 0 when do $$a/b$$. Put another way, $$a\equiv 0\mod b$$. If it's not divisible, then we get a non-zero answer. As you might guess, the regular Fibonacci sequence is closely related to this. In fact, on the subject of each number's divisibility and modular arithmetic, we have something called the [**Pisano period**](https://en.wikipedia.org/wiki/Pisano_period). If you were to take the regular Fibonacci sequence and divide each number in the sequence by a constant value, then the sequence of **remainders** we get actually repeats! The length of this repeating sequence is called the Pisano period. The Wikipedia page linked above gives numerous examples and what the sequences looks like.

Since the Tribonacci series is built the same way, we will get the same repeating sequence. So when we test to see whether a number divides the sequence, we just need to test the repeating part.

How do we know when the sequence repeats? Notice the first 3 numbers are all 1s. Because these are all less than any number we test, **these will stay three 1s**! So after the first 3 1s, **if we encounter 3 1s again later, then the sequence has repeated!**

### Implementation

Remember, if there's a 0 anymore in the sequence, then we stop checking and move onto the next number. We need to go through the whole sequence without finding a single 0.

```python
oddNums = 0
n = 27
while oddNums < 124:
    vals = [1,1,1,3]
    while vals[-3:] != [1,1,1] and vals[-1] != 0:
        vals.append(sum(vals[-3:]) % n)
    # Check if it's 111 or 0
    if vals[-1] != 0:
        oddNums += 1
    n += 2
# We went two over, so subtract 2...
print(n - 2)
```

Running this short while loop, we get,

```
2009
3.0220346 seconds.
```

Therefore, the 124th odd number is **2009**. If interested, you can do some more research on the types of numbers that will/will not divide the sequence to make the time even faster.