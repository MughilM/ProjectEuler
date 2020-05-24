---
layout: post
title: "Problem 71 - Ordered fractions"
date: 2017-06-21 10:12
number: 71
mathjax: true
---

## Question

Consider the fraction, $$n/d$$, where $$n$$ and $$d$$ are positive integers. If $$n<d$$ and $$HCF(n,d)=1$$, it is called a reduced proper fraction.

If we list the set of reduced proper fractions for $$d\leq 8$$ in ascending order of size, we get:


$$
\frac{1}{8},\frac{1}{7},\frac{1}{6},\frac{1}{5},\frac{1}{4},\frac{2}{7},\frac{1}{3},\frac{3}{8},\mathbf{\frac{2}{5}}, \frac{3}{7},\frac{1}{2},\frac{4}{7},\frac{3}{5},\frac{5}{8},\frac{2}{3},\frac{5}{7},\frac{3}{4},\frac{4}{5},\frac{5}{6},\frac{6}{7},\frac{7}{8}
$$


It can be seen that $$2/5$$ is the fraction immediately to the left of $$3/7$$.

By listing the set of reduced proper fractions for $$d\leq 1\,000\,000$$ in ascending order of size, find the numerator of the fraction immediately to the left of $$3/7$$.

## Answer

A direct brute force solution would be to loop through all denominators, and go through all reduced proper fractions with that denominator. However, there are simply too many fractions to check, and attempting to reduce will also take some time.

To make the looping faster, we can ask a related question: **Given** a denominator $$d$$, what is the closest fraction to the left of $$3/7$$? For example, if $$d=8$$, we can see that $$3/8$$ is the closest fraction to the left of $$3/7$$. If we have fixed a denominator, it is simple to find the numerator:


$$
\frac{n}{d} < \frac{3}{7} \Rightarrow n < \frac{3d}{7}
$$


Now, $$3d/7$$ may not be an integer, in which case we take the floor function to get the greatest integer for it. Additionally, we also have to be careful to not check denominators which are divisible by 7, since instead we will get the fraction itself. Cool, so then this is just a simple loop!

```python
# Basically find the reduced fraction that is
# closest to 3/7 on the left
minDist = float('inf')
bestNum = 0
bestDenom = 1

# Get the reduced fraction with
# the corresponding denominator
# that is closest to the left of 3/7.
# Calculate distance for each one...
for denom in range(3, 1000001):
    # Skip multiples of 7
    if denom % 7 == 0:
        continue
    # Calculate closest numerator
    num = 3 * denom // 7
    # Calculate distance
    dist = 3/7 - num/denom
    if dist < minDist:
        minDist = dist
        bestNum = num
        bestDenom = denom
print(bestNum, '/', bestDenom)
```

Running this gives an output of,

```
428570 / 999997
0.3996735 seconds.
```

Thus, the fraction that is immediately to the left of $$3/7$$ is $$\boxed{428\,570/999\,997}$$, and hence the numerator is **428570**.

### Another Way to get the "right" answer

The following way will get you the right number, but only through coincidence. The method is that you find the greatest multiple of 7 less than one million, and subtract one from the resulting numerator. With 3/7, this fraction is 428571/999999. As you see, subtracting one from the numerator gets you the correct answer. However, the fraction itself is very wrong, and indeed, through a calculator:


$$
\begin{aligned}
	\frac{3}{7} &\approx 0.428571429
	\\
	\frac{428\,570}{999\,999} &\approx 0.428570429
	\\
	\frac{428\,570}{999\,997} &\approx 0.428571286
\end{aligned}
$$
