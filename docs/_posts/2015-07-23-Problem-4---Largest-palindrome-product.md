---
layout: post
title: "Problem 4 - Largest palindrome product"
date: 2015-07-23 12:06
number: 4
---

## Question

A palindromic number reads the same both ways. The largest palindrome made from the product of two 2-digit numbers is 9009 = 91 x 99.

Find the largest palindrome made from the product of two 3-digit numbers.

## Answer

First need a method to see if a number is a palindrome or not. The following method converts the number into a string, goes through each digit, and compares it to the digit opposite to it. The first mismatch results in a number not being a palindrome.

```python
def isPalindrome(n):
    num = str(n)
    for i in range(0, len(num) // 2):
        if (str(n)[i] != str(n)[len(num) - 1 - i]):
            return False
    return True
```

Next, the built-in package `itertools` has a method called `combinations` along with a parameter `r` which will make a generator containing all the ways of selecting `r` items from that list. With that generator, we can multiply each pair of numbers whilst removing duplicate products, and simply start at the end of the list, ending as soon as we find our first palindromic number.

```python
numbers = sorted(set([a * b for a, b in
                      combinations(range(100, 1000), r=2)]))

for num in numbers[::-1]:
    if isPalindrome(num):
        print(num)
        break
```

Running this gives us the output of

```python
906609
0.13311525925925927 seconds.
```

Therefore, **906609** is our answer.