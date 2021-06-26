---
layout: post
title: "Problem 125 - Palindromic sums"
date: 2018-06-01 09:51
mathjax: true
number: 125
---

> The palindromic number 595 is interesting because it can be written as the sum of consecutive squares: $$6^2+7^2+8^2+9^2+10^2+11^2+12^2$$.
>
> There are exactly eleven palindromes below one-thousand that can be written as consecutive square sums, and the sum of these palindromes is 4164. Note that $$1^2=0^2+1^2$$ has not been included as this problem is concerned with the squares of positive integers.
>
> Find the sum of all the numbers less than $$10^8$$ that are both palindromic and can be written as the sum of consecutive squares.

<!--more-->

## Answer

Essentially, we would have to loop through all possible consecutive square sums whose sums are less than the limit. The limit of a hundred million is not too big so we can do a brute force solution.

Since we are doing a sum of squares, the maximum number in the sum is $$\left(10^4\right)^2$$, so we only have to loop up until this number. For every starting number, we add each sum possible to an array. If we encounter a palindrome we add it to a running list, and if at any point we exceed the limit, then we cut off the sum and move onto the next starting number. To check if a number is a palindrome, I convert it to a string and see if the string is equal to its reverse. It's unclear whether we can have a single palindromic number that can be written as consecutive sum squares in two different ways. Thus, before we sum everything, we call `set()` to remove duplicates.

```python
limit = 10 ** 8
squareLim = int(limit ** 0.5)
cumsumSquares = []
for i in range(1, squareLim + 1):
    s = i ** 2
    for j in range(i+1, squareLim + 1):
        s += j ** 2
        if s > limit:
            break
        if str(s) == str(s)[::-1] and s not in cumsumSquares:
            cumsumSquares.append(s)
print(sum(set(cumsumSquares)))
```

Running this short double loop results in an output of,

```
2906969179
0.49573599999999995 seconds.
```

Thus, the sum of all palindromes that can be written as a consecutive square sum is **2906969179**.