---
layout: post
title: "Problem 72 - Counting fractions"
date: 2017-08-04, 12:13
number: 72
mathjax: true
---

## Question

Consider the fraction, $$n/d$$, where $$n$$ and $$d$$ are positive integers. If $$n<d$$ and $$HCF(n,d)=1$$, it is called a reduced proper fraction.

If we list the set of reduced proper fractions for $$d\leq 8$$ in ascending order of size, we get:


$$
\frac{1}{8},\frac{1}{7},\frac{1}{6},\frac{1}{5},\frac{1}{4},\frac{2}{7},\frac{1}{3},\frac{3}{8},\mathbf{\frac{2}{5}}, \frac{3}{7},\frac{1}{2},\frac{4}{7},\frac{3}{5},\frac{5}{8},\frac{2}{3},\frac{5}{7},\frac{3}{4},\frac{4}{5},\frac{5}{6},\frac{6}{7},\frac{7}{8}
$$


It can be seen that there are 21 elements in this set.

How many elements would be contained in the set of reduced proper functions for $$d\leq 1\,000\,000$$?

## Answer

This is the same setup as [Problem 71]({{ site.url }}{% post_url 2017-06-21-Problem-71---Ordered-fractions %}), but now we have to count the fractions. When is a fraction in the set? It's when the fraction is reduced, and that is when the numerator and denominator **do not share any common factors**. Another word for this is that $$n$$ and $$d$$ are **coprime**. So now we need to count how many numbers are coprime to a given denominator $$d$$. But wait a moment, this is just $$\phi(d)$$, introduced in [Problem 69]({{ site.url }}{% post_url 2017-06-21-Problem-69---Totient-maximum %})! So we can use the same exact moment of calculating the totients, and finally simply sum them all. 

```python
limit = 10 ** 6
totients = np.ones((limit + 1, 2), dtype=object)
for n in range(2, limit + 1):
    if totients[n, 0] and totients[n, 1] != 1:
        continue
    totients[np.arange(n, limit + 1, n)] *= [n - 1, n]

totientValues = np.arange(limit + 1) * totients[:, 0] // totients[:, 1]
print(np.sum(totientValues[2:]))
```

Running this gives an output of,

```
303963552391
2.6249155 seconds.
```

Thus, there are a total of **303963552391** fractions in the set.