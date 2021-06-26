---
layout: post
title: "Problem 73 - Counting fractions in a range"
date: 2017-08-04 14:52
number: 73
mathjax: true
---

## Question

Consider the fraction, $$n/d$$, where $$n$$ and $$d$$ are positive integers. If $$n<d$$ and $$HCF(n,d)=1$$, it is called a reduced proper fraction.

If we list the set of reduced proper fractions for $$d\leq 8$$ in ascending order of size, we get:


$$
\frac{1}{8},\frac{1}{7},\frac{1}{6},\frac{1}{5},\frac{1}{4},\frac{2}{7},\frac{1}{3},\mathbf{\frac{3}{8}},\mathbf{\frac{2}{5}}, \mathbf{\frac{3}{7}},\frac{1}{2},\frac{4}{7},\frac{3}{5},\frac{5}{8},\frac{2}{3},\frac{5}{7},\frac{3}{4},\frac{4}{5},\frac{5}{6},\frac{6}{7},\frac{7}{8}
$$


It can be seen that there are 3 fractions between $$1/3$$ and $$1/2$$.

How many fractions lie between $$1/3$$ and $$1/2$$ in the sorted set of reduced proper fractions for $$d\leq12000$$?

## Answer

Seeing as it's the third question in a row after Problems [71]({{ site.url}}{% post_url 2017-06-21-Problem-71---Ordered-fractions %}) and [72]({{ site.url }}{% post_url 2017-08-04-Problem-72---Counting-fractions %}), I decided to research to see if this "set of reduced proper fractions" had a name. To my delight, this set is actually called a **Farey sequence**, and of course, there is a Wikipedia article on it. More importantly, the article also shows how to generate the fractions **in order** given two consecutive fractions in the sequence. 

Paraphrasing from the article, if we have two consecutive fractions $$a/b$$ and $$c/d$$, then the next fraction in the sequence is  $$p/q$$ where


$$
\begin{aligned}
	p &= \lfloor \frac{n + b}{d} \rfloor c - a
	\\
	q &= \lfloor \frac{n + b}{d} \rfloor d - b
\end{aligned}
$$


Now, we have one of the fractions we start from, namely $$1/3$$, but we need the fraction immediately to its left in order to start the sequence. But we've done that before in [Problem 71]({{ site.url }}{% post_url 2017-06-21-Problem-71---Ordered-fractions %})! So, all we need to do slightly adapt it, and then continue generating the next fraction until we hit $$1/2$$.

I've created a general function following #71's logic to find the fraction immediately to the left of $$a/b$$, given the maximum denominator.

```python
def findClosestFraction(n, a, b):
    minDist = float('inf')
    bestNum = 0
    bestDenom = 1
    for denom in range(3, n):
        # Skip multiples of b
        if denom % b == 0:
            continue
        # Calculate closest
        num = a * denom // b
        dist = a/b - num/denom
        if dist < minDist:
            minDist = dist
            bestNum = num
            bestDenom = denom
    return bestNum, bestDenom
```

Now it's just a matter of simple while loop. Since we are not including $$1/2$$ from the list, we subtract one from our count.

```python
n = 12000
a, b = findClosestFraction(n, 1, 3)

count = 0
c, d = 1, 3
while c/d != 1/2:
    k = (n + b) // d
    a, b, c, d = c, d, k * c - a, k * d - b
    count += 1

print(count - 1)
```

Running the above results in,

```
7295372
5.6877309 seconds.
```

Thus, when $$d=12000$$, we have **7295372** fractions between $$1/3$$ and $$1/2$$. The time is not terrible, but there could be additional ways to count the fractions without actually calculating the fractions themselves.