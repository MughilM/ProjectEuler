---
layout: post
title: "Problem 36 - Double-base palindromes"
date: 2016-05-10 11:56
mathjax: true
number: 36
---

## Question

The decimal number, $$585=1001001001_2$$ (binary), is palindromic in both bases.

Find the sum of all numbers, less than one million, which are palindromic in base 10 and base 2.

(Please note that the palindromic number, in either base, may not include leading zeros.)

## Answer

Nothing fancy to do here other than run a simple for loop. For binary conversion, Python has a convenient built-in function called `bin`. Only problem is that there is a leading `'0b'` in the output to signify binary. For example, `bin(25) = '0b11001'`. But this is easily handled because it is a string, so we take the rest of the string starting with the 3rd character. A quick and dirty way to check if a string is a palindrome is simply to reverse the string and check if the original is equal to the reversed version. You can see the for loop below which adds all such numbers.

```python
s = 0
isPalindrome = lambda x: x == x[::-1]
for i in range(1, 1000000):
    if isPalindrome(str(i)) and isPalindrome(bin(i)[2:]):
        s += i
print(s)
```

Notice the `isPalindrome` function is written using lambda calculus for a one-liner :). The output of the above program is,

```
872187
0.49978133307417255 seconds.
```

Thus, the sum of all numbers below one million which are palindromes in both base 10 and base 2 is **872187**.