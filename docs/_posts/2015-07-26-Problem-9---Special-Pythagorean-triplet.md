---
layout: post
title: "Problem 9 - Special Pythagorean triplet"
date: 2015-07-26 19:24
mathjax: true
number: 9
---

## Question

A Pythagorean triplet is a set of three natural numbers, $$a<b<c$$, for which,
$$
a^2+b^2=c^2
$$
For example, $$3^2+4^2=9+16=25=5^2$$.

There exists exactly one Pythagorean triplet for which $$a+b+c=1000$$. Find the product $$abc$$.

## Answer

The brute force method would simply loop through all $$a, b, c$$ values from 1 to 999. However, as there are three different variables, this would take a substantial amount time. But we can use our given relationships to reduce the search space to just two of the variables: Note that


$$
(a+b+c)^2 = a^2+2ab+b^2+2ac+2bc+c^2
$$



From $$a+b+c = 1000$$, we have $$c = 1000 - a - b$$ and of course $$a^2 + b^2 = c^2$$. Using these three facts, we can simplify the above expression:


$$
\begin{align*}
	1000^2 &= 2ab + 2ac + 2bc + 2c^2
	\\
	\frac{1000^2}{2} &= ab + a(1000 - a - b) + b(1000 - a - b) + (1000 - a - b)^2
	\\
	\frac{1000^2}{2} &= ab + 1000a - a^2 - ab + 1000b - ab - b^2 - 1000^2 - 2(1000)(a+b) + (a+b)^2
	\\
	\frac{1000^2}{2} &= 1000a-a^2+1000b-ab-b^2-1000^2-2000a-2000b+a^2+2ab+b^2
	\\
	\frac{1000^2}{2} &= 1000^2-1000a-1000b+ab
	\\
	500 &= 1000-a-b+\frac{ab}{1000}
	\\
	a+b-500 &= \frac{ab}{1000}
\end{align*}
$$


That last statement is the condition we need to check for each pair $$a,b$$. Therefore, our code has reduced to a double for loop:

```python
import sys

for b in range(1, 999):
    for a in range(1, b):
        if a + b - 500 == a * b / 1000:
            c = int((a ** 2 + b ** 2) ** 0.5)
            print('(a, b, c)', '=', a, b, c)
            print('abc', '=', a * b * c)
            end = time.perf_counter()
            print(end - start, 'seconds.')
            sys.exit(0)
```

Running the above code results in the output of,

```python
(a, b, c) = 200 375 425
abc = 31875000
0.033183526281024125 seconds.
```

Therefore, **31875000** is our answer.