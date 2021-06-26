---
layout: post
title: "Problem 162 - Hexadecimal numbers"
date: 2019-08-23 15:58
number: 162
mathjax: true
---

> In the hexadecimal number system numbers are represented using 16 different digits:
>
> 
> $$
> 0,1,2,3,4,5,6,7,8,9,A,B,C,D,E,F
> $$
> 
>
> The hexadecimal number AF when written in the decimal number system equals $$10\times 16+15=175$$.
>
> In the 3-digit hexadecimal numbers 10A, 1A0, A10, and A01 the digits 0, 1, and A are all present. Like numbers written in base ten we write hexadecimal numbers without leading zeroes.
>
> How many decimal numbers containing at most sixteen hexadecimal digits exist with all of the digits 0, 1, and A present at least once? Give your answer as a hexadecimal number.
>
> (A,B,C,D,E and F in upper case, without any leading or trailing code that marks the number as hexadecimal and without leading zeroes, e.g. 1A3F and not: 1a3f and not 0x1a3f and not $1A3F and not #1A3F and not 0000001A3F)

<!--more-->

## Answer

A counting problem! Whenever we see the phrase "at least" in a counting problem, we usually think to count the complement and subtract it from the whole universe of numbers. The complement to the above is "hexadecimal numbers that are *missing* at least one of 0, 1, or A."

As before, we start with the entire universe of numbers. With $$n$$ digits, and none starting with $$0$$, we can choose any of the 15 non-zero numbers for the first digit, and then anything for the rest of the $$n-1$$ digits. Thus, in total there are $$15\times16^{n-1}$$ $$n$$-digit hexadecimal numbers. From this, we subtract the number of hexadecimal numbers that are missing 0, 1, or A:

- Missing 0: With 0 removed, we have 15 choices for each digit, so in total $$15^n$$.
- Missing 1: We have 15 choices, but only 14 for the first digit (0 is still there), so in total $$14\times 15^{n-1}$$.
- Missing A: Same as the above case, $$14\times 15^{n-1}$$.

So in total, there are $$15^n+2\times14\times15^{n-1}$$ digits. We subtract this from the total and we are done....

....Wait a minute! We over counted in the last part! A number like "BA3" is missing 0, but it's also missing 1. That means it fell into both groups and we subtracted this instance twice. We thus need add all numbers that fall into 2 groups back:

- Missing 0 and 1: We have 14 choices for each digit, so in total $$14^n$$.
- Missing 0 and A: Same as above case, $$14^n$$.
- Missing 1 and A: The 0 is still in this group, so we only have 13 choices for the first digit. In total, there are $$13\times 14^{n-1}$$.

The total we need to add back (for missing 2 out of the 3 required numbers) is thus $$2\times14^n + 13\times14^{n-1}$$.

We still aren't done. A number like "56C2" is missing all of 0, 1, and A, and thus it falls into 2 of the above groups. It's the same problem as the previous part. We added one instance too many of these types of numbers. Thus, we subtract numbers that are missing *all of 0, 1, and A*. Well, this is actually simple. With these 3 numbers gone, we have 13 choices for each digit, and thus it is $$13^n$$.

### Final Formula

In total, the number of $$n$$-digit numbers that have at least one 0, one 1, and A is


$$
\begin{aligned}
	c(n) &= 
		\underbrace{15\times16^{n-1}}_\text{all numbers} -
		\underbrace{\left( 15^n+2\times14\times15^{n-1} \right)}_\text{missing one of 0, 1, A} + 
		\underbrace{\left( 2\times14^n+13\times14^{n-1} \right)}_\text{missing two of 0, 1, A} - 
		\underbrace{13^n}_\text{missing all 3}
	\\ &=
	15\times 16^{n-1} - 15^{n-1}(15+28) + 14^{n-1}(28+13)-13^n
	\\ &=
	\boxed{15\times 16^{n-1} - 43\times15^{n-1} + 41\times 14^{n-1} - 13^n}
\end{aligned}
$$


Conveniently, this formula also evaluates to 0 for $$n=1$$ and $$n=2$$.

### Implementation

Since we have an expression for $$c(n)$$, all we have to is sum of the values up to $$n=16$$. The `hex()` function converts to hexadecimal. It contains the "0x" artifact at the beginning so we index out of it. Since the output is a string, we can call `.upper()` to convert everything to uppercase. In the end, we can do this in 2 lines (even 1 if you want to put the whole expression in the `sum()`).

```python
countNums = lambda n: 15 * 16 ** (n-1) - 43 * 15 ** (n-1) + 41 * 14 ** (n-1) - 13 ** n
print(hex(sum(countNums(i) for i in range(3, 17)))[2:].upper())
```

Running one of the shortest programs we have, we get

```
3D58725572C62302
4.590000000004313e-05 seconds.
```

Therefore, our answer for the total hexadecimal numbers up to 16 digits is **3D58725572C62302**.

## Bonus - A Closed Form

Because we are dealing with a sum of a powers, I was pretty confident it was possible to find a closed form solution. We will primarily use the fact that $$\sum_{n=0}^N a^n = \frac{a^{N+1}-1}{a-1}$$. In our case, you might think we are starting from $$n=3$$, but since our formula evaluates to 0 for $$n=1$$ and $$n=2$$, we can start our summation from $$n=1$$, which means we have to remember to subtract 1 ($$a^0=1$$). Therefore,


$$
\begin{aligned}
\sum_{n=1}^N c(n) &= 15\sum_{n=1}^N 16^{n-1} -
	43\sum_{n=1}^N 15^{n-1} + 
	41\sum_{n=1}^N 14^{n-1} -
	\sum_{n=1}^N 13^n
\\ &=
	15\left( \frac{16^N-1}{16-1}-1 \right) -
	43\left( \frac{15^N-1}{15-1}-1 \right) +
	41\left( \frac{14^N-1}{14-1}-1 \right) -
	\left( \frac{13^{N+1}-1}{13-1}-1 \right)
\\ &=
	16^N-1-15 - 
	\frac{43}{14}\left(15^N-1\right)+43 +
	\frac{41}{13}\left(14^N-1\right)-41 -
	\frac{1}{12}\left(13^{N+1}-1\right)+1
\\ &=
	16^N - \frac{43}{14}15^N + \frac{41}{13}14^N - \frac{1}{12}13^{N+1} - 13 + \frac{1}{1092}
\end{aligned}
$$


Plugging in $$N=16$$ and converting to hexadecimal gets us the same answer. If you are worried about the weird fraction at the end, it is possible to put the entire expression as something 1092. Additionally, the prime factorization of 1092 is $$2^2\times3\times7\times 13$$. You can notice that each factor is part of an exponential, and so cancelling will definitely occur.

