---
layout: post
title: "Problem 63 - Powerful digit counts"
date: 2017-08-08 12:09
mathjax: true
number: 63
---

## Question

The 5-digit number, $$16807=7^5$$, is also a fifth power. Similarly, the 9-digit number, $$134\,217\,728=8^9$$, is a ninth power.

How many $$n$$-digit positive integers exist which are also an $$n$$th power?

## Answer

First, since there are no bounds given in the problem, we need to work out bounds ourselves. An $$n$$th power $$a^n$$ can only be an $$n$$-digit number if it is between $$10^{n-1}$$ and $$10^n$$. Writing it out, this is,


$$
10^{n-1}<a^n<10^n
$$


From the right half of the inequality, we can immediately notice that $$a<10$$. What about $$n$$? Well, from the left half of the inequality, we can solve for $$n$$:


$$
\begin{align*}
10^{n-1} &< a^n
\\
\log_{10}\left(10^{n-1}\right) &< \log_{10}\left(a^n\right)
\\
n-1 &< n\log_{10}a
\\
n-n\log_{10}a &< 1
\\
n &< \frac{1}{1-\log_{10}a}
\end{align*}
$$


That last expression is what we will loop until for each fixed $$a$$. So we have our double loop to check all combinations of $$a^n$$. To check the length, the easiest way is to convert to a string. Other ways include comparing the integer part of $$\log_{10}$$ of each number. I'll be using the former.

```python
# Bound for a is 10
count = 0
for a in range(2, 10):
    n = 1
    # Bound for the power
    while n <= 1/(1 - math.log10(a)):
        if len(str(a ** n)) == n:
            count += 1
        n += 1

print(count + 1) # for 1 ^ 1 = 1
```

Running the short loop gets us the output of,

```
49
9.204934635087552e-05 seconds.
```

Thus, there are **49** $$n$$-digit integers that are also $$n$$th powers (and it did in quicktime).