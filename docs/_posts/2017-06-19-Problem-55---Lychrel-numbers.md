---
layout: post
title: "Problem 55 - Lychrel numbers"
date: 2017-06-19, 19:51
mathjax: true
number: 55
---

## Question

If we take 47, reverse and add, 47 + 74 = 121, which is palindromic.

Not all numbers produce palindromes so quickly. For example,


$$
\begin{aligned}
349+943 &= 1292
\\
1292+2921 &= 4213
\\
4213+3124 &= 7337
\end{aligned}
$$


That is, 349 took three iterations to arrive at a palindrome.

Although no one has proved it yet, it is thought that some numbers, like 196, never produce a palindrome. A number that never forms a palindrome through the reverse and add process is called a Lychrel number. Due to the theoretical nature of these numbers, and for the purpose of this problem, we shall assume that a number is Lychrel until proven otherwise. In addition you are given that for every number below ten-thousand, it will either (i) become a palindrome in less than fifty iterations, or, (ii) no one, with all the computing power that exists, has managed so far to map it to a palindrome. In fact, 10677 is the first number to be shown to require over fifty iterations before producing a palindrome: 4668731596684224866951378664 (53 iterations, 28-digits).

Surprisingly, there are palindromic numbers that are themselves Lychrel numbers; the first example is 4994.

How many Lychrel numbers are there below ten-thousand?

## Answer

This is a pretty straightforward problem. Since the bound is relatively low, we can do this directly. We keep adding the reverse of the number until a palindrome is reached. We keep track of the number of iterations. If we pass 50, then we break immediately, and add to the count, and continue to the next number. Like this, we loop through all integers below ten-thousand.

The `isPalindrome` function is very basic, as it checks if it's equal to the reverse of the integer. To make things slightly faster, I explicitly check one-digit and two-digit numbers. All one-digit numbers are palindromes, and a two-digit number is a palindrome if it's divisible by 11.

```python
def isPalindrome(n):
    # One digit number are
    # automatically palindromes.
    # If it's two digits, then it
    # should be divisible by 11.
    if n < 10 or (n < 100 and n % 11 == 0):
        return True
    n = str(n)
    return n == n[::-1]

isLychrel = 0
for n in range(1, 10001):
    i = n + int(str(n)[::-1])
    iters = 2
    # Keep going until iterations are more than 50
    while iters <= 50 and not isPalindrome(i):
        i += int(str(i)[::-1])
        iters += 1
    # If the first condition failed,
    # then we have 51 iterations....
    if iters == 51:
        isLychrel += 1
    print('\r' + str(n) + ' / 10000 numbers finished.', end='')
print()
print(isLychrel)
```

Running the code results in an output of,

```
10000 / 10000 numbers finished.
249
1.2541913069861503 seconds.
```

Thus, there are **249** Lychrel numbers below ten-thousand.