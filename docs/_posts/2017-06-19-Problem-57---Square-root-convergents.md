---
layout: post
title: "Problem 57 - Square root convergents"
date: 2017-06-19 20:11
mathjax: true
number: 57
---

## Question

Is it possible to show that the square root of two can be expressed as an infinite continued fraction.


$$
\sqrt{2} = 1 + \frac{1}{2+\frac{1}{2 + \frac{1}{2+\dots}}} = 1.414213\dots
$$


By expanding this for the first four iterations, we get:


$$
\begin{align*}
1 + \frac{1}{2} &= \frac{3}{2} = 1.5
\\
1 + \frac{1}{2+\frac{1}{2}} &= \frac{7}{5} = 1.4
\\
1 + \frac{1}{2+\frac{1}{2+\frac{1}{2}}} &= \frac{17}{12} = 1.41666\dots
\\
1 + \frac{1}{2+\frac{1}{2+\frac{1}{2+\frac{1}{2}}}} &= \frac{41}{29} = 1.41379\dots
\end{align*}
$$


The next three expansions are, $$\displaystyle\frac{99}{70},\frac{239}{169},\frac{577}{408}$$, but the eight expansion, $$\displaystyle\frac{1393}{895}$$, is the first example where the number of digits in the numerator exceeds the number of digits in the denominator.

In the first one-thousand expansions, how many fractions contain a numerator with more digits than denominator?

## Answer

The crux of this problem involve finding how to get from one expansion to the next. We can't recalculate every single expansion each time, as that is inefficient.

Now, there is a recurring constant "1" which we add at the end. I will subtract this out because this is not part of the expansion. It isn't difficult to add 1 to a fraction, as we just the denominator to the numerator i.e. $$\displaystyle 1+\frac{a}{b} = \frac{a+b}{b}$$. The problem then boils to, if the fractional-only expansion is $$\displaystyle\frac{p}{q}$$, then what is the value of the next fractional-only expansion?

From our problem example, the fractional-only expansion of the 2nd and 3rd step is $$\displaystyle\frac{2}{5}$$ and $$\displaystyle\frac{5}{12}$$ respectively (remember I am subtracting 1). However, with the 3rd expansion, notice the expression in red:


$$
\frac{1}{2 + \color{red}\frac{1}{2 + \frac{1}{2}}}
$$


That is exactly the expression used for the 2nd expansion of $$\displaystyle\frac{2}{5}$$! In that case, we can plug this in directly, and end up computing $$\displaystyle\frac{1}{2+\frac{2}{5}} = \frac{1}{\frac{12}{5}} = \frac{5}{12}$$. In general, this pattern will follow each pair of expansion. As an extra example, the 3rd expansion can be plugged into the 4th in the same way: $$\displaystyle \frac{1}{2 + \frac{5}{12}} = \frac{1}{\frac{29}{12}} = \frac{12}{29}$$.

With $$\displaystyle\frac{p}{q}$$, the next expansion becomes $$\displaystyle \frac{1}{2+\frac{p}{q}} = \frac{1}{\frac{2q+p}{q}} = \frac{q}{2q+p}$$. Therefore, the new denominator is 2 times the old one plus the old numerator, while the new numerator is the old denominator. This calculation can easily be done in code quickly, as we only need the previous expansion. In the end, it's a simple loop. See below:

```python
exceeding = 0
# Expansion of sqrt(2) is all 2s
num = 0
denom = 1
for _ in range(1000):
    num += 2 * denom
    # Flip
    temp = num
    num = denom
    denom = temp
    # Check if the length f num exceeds denom
    # ADD ONE!
    if len(str(num + denom)) > len(str(denom)):
        exceeding += 1
print(exceeding)
```

Running the above results in,

```
153
0.004323158785912579 seconds.
```

Therefore, there are **153** expansions within the first one-thousand that have more digits in the numerator than that of the denominator.