---
layout: post
title: "Problem 64 - Odd period square roots"
date: 2017-06-19 10:33
mathjax: true
number: 64
---

## Question

All square roots are periodic when written as continued fractions and can be written in the form:


$$
\sqrt{N}=a_0 + \frac{1}{a_1 + \frac{1}{a_2 + \frac{1}{a_3+\dots}}}
$$


For example, let us consider $$\sqrt{23}$$:


$$
\sqrt{23}=4+\sqrt{23}-4=4 + \frac{1}{\frac{1}{\sqrt{23} - 4}} = 4 + \frac{1}{1 + \frac{\sqrt{23} - 3}{7}}
$$


If we continue we would get the following expansion:


$$
\sqrt{23} = 4 + \frac{1}{1 + \frac{1}{3 + \frac{1}{1 + \frac{1}{8 + \dots}}}}
$$


The process can be summarized as follows:


$$
\begin{aligned}
a_0 &= 4, \frac{1}{\sqrt{23}-4} = \frac{\sqrt{23} + 4}{7} = 1 + \frac{\sqrt{23} - 3}{7}
\\
a_1 &= 1, \frac{7}{\sqrt{23}-3} = \frac{7(\sqrt{23} + 3)}{14} = 3 + \frac{\sqrt{23} - 3}{2}
\\
a_2 &= 3, \frac{1}{\sqrt{23}-4} = \frac{\sqrt{23} + 4}{7} = 1 + \frac{\sqrt{23} - 3}{7}
\\
a_3 &= 1, \frac{7}{\sqrt{23}-4} = \frac{7(\sqrt{23} + 4)}{7} = 8 + \sqrt{23} - 4
\\
a_4 &= 8, \frac{1}{\sqrt{23}-4} = \frac{\sqrt{23} + 4}{7} = 1 + \frac{\sqrt{23} - 3}{7}
\\
a_5 &= 1, \frac{7}{\sqrt{23}-3} = \frac{7(\sqrt{23} + 3)}{14} = 3 + \frac{\sqrt{23} - 3}{2}
\\
a_6 &= 3, \frac{1}{\sqrt{23}-4} = \frac{\sqrt{23} + 4}{7} = 1 + \frac{\sqrt{23} - 3}{7}
\\
a_7 &= 1, \frac{7}{\sqrt{23}-4} = \frac{7(\sqrt{23} + 4)}{7} = 8 + \sqrt{23} - 4
\end{aligned}
$$


It can be seen that the sequence is repeating. For conciseness, we use the notation $$\sqrt{23}=[4;(1,3,1,8)]$$, to indicate that the block (1,3,1,8) repeats indefinitely.

The first ten continued fraction representation of (irrational) square roots are:


$$
\begin{aligned}
\sqrt{2} &= [1;(2)], \text{period=1}
\\
\sqrt{3} &= [1:(1,2)], \text{period=2}
\\
\sqrt{5} &= [2;(4)], \text{period=1}
\\
\sqrt{6} &= [2;(2,4)], \text{period=2}
\\
\sqrt{7} &= [2;(1,1,1,4)], \text{period=4}
\\
\sqrt{8} &= [2;(1,4)], \text{period=2}
\\
\sqrt{10} &= [3;(6)], \text{period=1}
\\
\sqrt{11} &= [3;(3,6)], \text{period=2}
\\
\sqrt{12} &= [3;(2,6)], \text{period=2}
\\
\sqrt{13} &= [3;(1,1,1,1,6)], \text{period=5}
\end{aligned}
$$


Exactly four continued fractions, for $$N\leq 13$$, have an odd period.

How many continued fractions for $$N\leq10\,000$$ have an odd period?

## Answer

The crux of this problem lies in finding the period of the continued fraction of $$\sqrt{N}$$. Whenever we are trying to find cycles, we need a bound to stop generating numbers. Otherwise, we could sit here indefinitely generating the next number in the sequence without knowing when to stop and look for a cycle.

After some research, I found this little tidbit in [this Wikipedia article](https://en.wikipedia.org/wiki/Periodic_continued_fraction#Reduced_surds) that reads:

> If $$r>1$$ is a rational number that is not a perfect square, then
> $$
> \sqrt{r} = [a_0;\overline{a_1,a_2,\dots,a_2,a_1,2a_0}]
> $$

The details of the proof are slightly out of scope for this site, but feel free to read the article if you are interested.

So this gives a way to calculate the length of the period. In these cases, $$a_0$$ corresponds to the integer part of $$\sqrt{N}$$. We keep generating the next number in the sequence until we reach $$2a_0$$.

Now then, how do we keep calculate each number in the sequence? One simple way is to keep reciprocating and taking the integer part. For example:



- $$\sqrt{23}\approx 4.79 \Rightarrow a_0=4$$
- $$\sqrt{23}-\mathbf{a_0}\approx 0.79 \Rightarrow \frac{1}{0.79}\approx 1.256 \Rightarrow a_1 = 1$$
- $$\frac{1}{1.256-\mathbf{a_1}}\approx 3.90 \Rightarrow a_2 = 3$$
- $$\frac{1}{3.90-\mathbf{a_2}}\approx 1.11 \Rightarrow a_3 = 1$$
- $$\frac{1}{1.11-\mathbf{a_3}}\approx 8.79 \Rightarrow a_4=8$$



Since $$a_4 = 8 = 2(4) = 2a_0$$, we are done, and the length is 4. This is a perfectly reasonable way to calculate the sequence. However, in computer terms, it is pretty inefficient, because we have to constantly keep track of floating point numbers. An integer-only way to generate the sequence would be much preferred because it is much faster to do integer arithmetic.

Thankfully, I stumbled upon [this section in a Wiki article](https://en.wikipedia.org/wiki/Methods_of_computing_square_roots#Algorithm) describing how to generate the sequence using only integers. I won't reproduce it here for conciseness, but the algorithm essentially keeps track of the irrational radical fraction that you repeatedly see in the problem example $$\frac{\sqrt{23}-3}{7}, \frac{\sqrt{23}-3}{2}$$, etc. It relies on the guaranteed fact that $$N-m_{n+1}^2$$ always divides $$d_n$$ at each step. The reason why is a bit out of scope for this answer, and I encourage you to read [this math StackExachange question](https://math.stackexchange.com/questions/213683/calculate-the-continued-fraction-of-square-root) if you are curious.

Okay, so the algorithm is simple enough to directly convert into code, since it only uses integers. See the `periodLength` function below.

```python
def periodLength(S):
    m = 0
    d = 1
    # Integer part is a0
    a0 = int(math.sqrt(S))
    # Current a
    a = a0
    count = 0
    # Keep going until we get to 2a_0
    while a != 2 * a0:
        m = d * a - m
        d = (S - m ** 2) // d
        a = int((a0 + m) / d)
        count += 1
    return count
```

Now, all we have to do is loop through each $$N$$ (which aren't square numbers), and see whether the period length is odd. We do this by computing $$N\mod 2$$. If it's 1, then $$N$$ is odd.

```python
oddPeriod = 0
for n in range(2, 10001):
    # Skip square numbers
    if int(math.sqrt(n)) == math.sqrt(n):
        continue
    # Calculate period length and check if odd
    length = periodLength(n)
    if periodLength(n) % 2 == 1:
        oddPeriod += 1

print(oddPeriod)
```

Running this short loop gets us,

```
1322
0.4687670246846322 seconds.
```

Therefore, there are **1322** numbers below 10,000 where the period length of the continued fraction of the square root is odd.