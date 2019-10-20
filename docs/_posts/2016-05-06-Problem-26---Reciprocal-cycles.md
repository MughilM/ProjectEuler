---
layout: post
title: "Problem 26 - Reciprocal cycles"
date: 2016-05-06 13:09
mathjax: true
number: 26
---

## Question

A unit fraction contains 1 in the numerator. The decimal representation of the unit fractions with denominators 2 to 10 are given:


$$
\begin{aligned}
\frac{1}{2} &= 0.5 \\
\frac{1}{3} &= 0.\overline{3} \\
\frac{1}{4} &= 0.25 \\
\frac{1}{5} &= 0.2 \\
\frac{1}{6} &= 0.1\overline{6} \\
\frac{1}{7} &= 0.\overline{142857} \\
\frac{1}{8} &= 0.125 \\
\frac{1}{9} &= 0.\overline{1} \\
\frac{1}{10} &= 0.1
\end{aligned}
$$


where $$0.1\overline{6}$$ means 0.166666..., and has a 1-digit recurring cycle. It can be seen that $$\frac{1}{7}$$ has a 6-digit recurring cycle.

Find the value of $$d<1000$$ for which $$\frac{1}{d}$$ contains the longest recurring cycle in its decimal fraction part.

## Answer

Ok, so there are definitely algorithms present to determine if a cycle is present in a string of letters/digits. However, the only upper bound we have is that the recurring cycle has to be at most $$d-1$$, which means we have at least generate at least twice that many digits to notice a cycle. Doing that manually will be difficult, and with the accuracy of floating-point precision makes it unreliable to simply divide. So is there a way to find the length of the recurring cycle just from $$d$$?

There is. [This answer on Quora](https://www.quora.com/What-determines-the-number-of-digits-for-recurring-decimals) and the [Wikipedia](https://en.wikipedia.org/wiki/Repeating_decimal) article on the subject gives us insight to how we can determine the denominator with the longest recurring cycle. Essentially, given our fraction $$\frac{1}{d}$$, we keep multiplying this fraction by 10 until $$gcd(10,d) = 1$$. We also need to keep reducing the fraction to lowest terms each time. The Quora answer then explains that since we kept multiplying by 10 (moving the decimal point), eventually the original fraction and the multiplied fraction should have the same decimal repeating cycle after $$k$$ steps. This $$k$$ value is given by the least $$k$$ that satisfies $$10^k\equiv1\mod d$$. Notice that $$k$$ must be positive, as it doesn't make sense for a decimal to have a 0-digit recurring cycle. Let's show an example with $$\displaystyle \frac{1}{55}$$.

First, observe that $$gcd(10, 55) = 5\neq 1$$. Thus, multiply the fraction to get $$\displaystyle \frac{10}{55} = \frac{2}{11}$$. Now $$gcd(10,11) = 1$$, and we can move to the next step. Now, we are looking for the least $$k$$ that satisfies $$10^k\equiv 1\mod 11$$.


$$
\begin{aligned}
10^1 &\equiv 10\mod 11 = 10 \\
10^2 &\equiv 100\mod 11 = 1
\end{aligned}
$$


Therefore, $$k = 2$$ and we conclude that $$\displaystyle \frac{1}{55}$$ has a 2-digit recurring cycle, and indeed it does as the decimal expansion is $$0.0\overline{18}$$. Notice that the numerator itself did not matter, as any fraction in its reduced form with a denominator of 55 will have a 2-digit recurring cycle. 

What if after multiplying by 10's until the condition results in $$d = 1$$? This just means that the decimal will not repeat, as the denominator only consisted of 2's and 5's. All fractions whose denominators with only these two prime factors will eventually terminate. This is partly due to the fact that $$\frac{1}{2}$$ and $$\frac{1}{5}$$ are the only fractions with prime denominators that don't repeat. 

Which brings me to the Wikipedia article. The article states a prime denominator $$p$$ will have a recurring cycle length of $$p-1$$ *unless* the number 10 is a so-called multiplicative root of $$p$$, in which case the length will be something smaller. For the purposes of this problem, this extra check is not necessary, and may actually involve additional computations. However, due to this fact, all we need to do check the prime numbers under 1000! A great excuse to use `primesieve` :). Finally, to efficiently find the greatest common divisor, we can use the [Euclidean algorithm](https://en.wikipedia.org/wiki/Euclidean_algorithm), where we decrement the larger value by the smaller value, until one is 0. At that point, the non-zero value is the greatest common divisor.

So in the end, we test using the procedure above with all of the prime numbers less than 1000. The code for this, along with calculating GCD is,

```python
def euclidGCD(a, b):
    if a == 0:
        return b
    if a < b:
        return euclidGCD(b, a)
    return euclidGCD(a - b * (a//b), b)

maxK = 1
maxN = 11
for n in primesieve.primes(1000):
    numerator = 1
    denominator = n
    # While the gcd of the denominator
    # and 10 isn't 1...keep multiplying
    # by 10.
    while euclidGCD(denominator, 10) != 1:
        # Multiply numerator by 10,
        # and reduce.
        numerator *= 10
        gcd = euclidGCD(numerator, denominator)
        numerator //= gcd
        denominator //= gcd
    # If we ever encounter a denominator
    # of 1, that means the decimal doesn't
    # repeat, and we can skip this. Fun Fact,
    # this happens with numbers with only
    # 2 and 5 as their prime factors.
    if denominator == 1:
        continue
    # Once 10 is co-prime, find when
    # 10^k mod denominator is 1.
    # This k is the length of the recurrence.
    k = 1
    while (10 ** k) % denominator != 1:
        k += 1
    # Update if it's the biggest we found.
    if maxK < k:
        maxK = k
        maxN = n

print('d =', maxN, 'with', maxK, 'recurring digits.')
```

Notice the GCD method is a relatively simple recursion. The recursion is made faster by guaranteeing one value will be lower than the error. This results in the recursion depth being as small as possible. Running the code above results in an output of,

```
d = 983 with 982 recurring digits.
0.08353135778999571 seconds.
```

Thus, $$\displaystyle \frac{1}{983}$$ has the longest recurring decimal cycle at 982 digits. I really like this problem because two seemingly unrelated concepts (prime numbers and decimal recurrence) come together to solve this problem.