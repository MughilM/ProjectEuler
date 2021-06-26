---
layout: post
title: "Problem 65 - Convergents of e"
date: 2017-06-19 11:11
mathjax: true
number: 65
---

## Question

The square root of 2 can be written as an infinite continued fraction.


$$
\sqrt{2} = 1 + \frac{1}{2 + \frac{1}{2 + \frac{1}{2 + \frac{1}{2+\dots}}}}
$$


The infinite continued fraction can be written, $$\sqrt{2}=[1;(2)]$$, $$(2)$$ indicates that 2 repeats *ad infinitum*. In a similar way, $$\sqrt{23}=[4;(1,3,1,8)].$$

It turns out that the sequence of partial values of continued fractions for square roots provide the best rational approximations. Let us consider the convergents for $$\sqrt{2}$$.


$$
\begin{align*}
1 + \frac{1}{2} &= \frac{3}{2}
\\
1 + \frac{1}{2 + \frac{1}{2}} &= \frac{7}{5}
\\
1 + \frac{1}{2 + \frac{1}{2 + \frac{1}{2}}} &= \frac{17}{12}
\\
1 + \frac{1}{2 + \frac{1}{2 + \frac{1}{2 + \frac{1}{2}}}} &= \frac{41}{29}
\end{align*}
$$


Hence the sequence of the first ten convergents for $$\sqrt{2}$$ are:


$$
1,\frac{3}{2}, \frac{7}{5}, \frac{17}{12}, \frac{41}{29}, \frac{99}{70}, \frac{239}{169}, \frac{577}{408}, \frac{1393}{985}, \frac{3363}{2378}, \dots
$$


What is most surprising is that the important mathematical constant,


$$
e=[2;1,2,1,1,4,1,1,6,1,\dots,1,2k,1,\dots]
$$


The first ten terms in the sequence of convergents for $$e$$ are:


$$
2,3,\frac{8}{3},\frac{11}{4},\frac{19}{7}, \frac{87}{32}, \frac{106}{39}, \frac{193}{71}, \frac{1264}{465}, \frac{1457}{536},\dots
$$


The sum of digits in the numerator of the $$\text{10}^\text{th}$$ convergent is $$1+4+5+7=17$$.

Find the sum of digits in the numerator of the $$100^\text{th}$$ convergent of the continued fraction for $$e$$.

## Answer

Continued fractions just won't let us go will they? In any case, I think this problem is slightly easier than the other continued fraction problems, because we are simply computing a fraction. 

To be clear, in order to evaluate any continued fraction, we'll need to work from the bottom up. An example for the fourth convergent of $$e$$:


$$
2+\frac{1}{1 + \frac{1}{2+\frac{1}{1}}} = 2 + \frac{1}{1+\frac{1}{\color{red}{\frac{3}{1}}}}
= 2 + \frac{1}{1 + \frac{1}{3}} = 2 + \frac{1}{\color{red}{\frac{4}{3}}} = 2 + \frac{3}{4} =
\boxed{\frac{11}{4}}
$$


Just doing one example allows us to see what the actual algorithm is. We start from the lowest fraction, add the constant to the current fraction we have (in the denominator of the 1 fraction), and then flip it, because it is 1 over. In symbols, it is:


$$
\frac{1}{a_i + \frac{N}{D}} = \frac{1}{\frac{Da_i+N}{D}} = \frac{D}{Da_i+N}
$$


where $$a_i$$ is the current constant, $$N$$ is the current numerator and $$D$$ is the current denominator. After the above iteration, our new $$N = D$$ and our new $$D = Da_i+N$$. Therefore, we keep repeating this until we add the final constant (in this case 2) but *don't* flip it (since it's not under a 1 in a fraction). 

Cool so we can code this fairly easily! The first step is generating all of our $$a_i$$'s. Since we need the 100th convergent, we generate 99 numbers, since the constant all the way out in front is considered the first convergent. Note that the constants which aren't 1 are every 3rd number, and is of the form $$2k$$. We can easily keep track of this as well. Afterwards, we can do the adding and flipping I mentioned earlier, until we've exhausted our list. Remember we start from the end.

```python
coeffs = []
n = 1
while len(coeffs) < 99:
    if len(coeffs) % 3 == 1:
        coeffs.append(n * 2)
        n += 1
    else:
        coeffs.append(1)
# Add and flip over and over again
num = 1
denom = coeffs[-1]
for i in range(len(coeffs) - 2, -1, -1):
    # Add
    num += coeffs[i] * denom
    # Flip
    temp = num
    num = denom
    denom = temp
# Add the final two
num += 2 * denom
print(sum([int(x) for x in str(num)]))
```

Running the code results in an output of,

```
272
0.00021688880320442342 seconds.
```

Therefore, the sum of the digits in the numerator of the 100th convergent of $$e$$ is **272** (which implies the numerator is something massive).