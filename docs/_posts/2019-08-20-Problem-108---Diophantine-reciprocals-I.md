---
layout: post
title: "Problem 108 - Diophantine reciprocals I"
date: 2019-08-20 17:36
mathjax: true
number: 108
---

## Question

In the following equation, $$x$$, $$y$$, and $$n$$ are positive integers.
$$
\frac{1}{x}+\frac{1}{y}=\frac{1}{n}
$$
For $$n=4$$ there are exactly three distinct solutions:
$$
\begin{aligned}
	\frac{1}{5}+\frac{1}{20}&=\frac{1}{4}
	\\
	\frac{1}{6}+\frac{1}{12}&=\frac{1}{4}
	\\
	\frac{1}{8}+\frac{1}{8}&=\frac{1}{4}
\end{aligned}
$$
What is the least value of $$n$$ for which the number of distinct solutions exceeds one-thousand?

NOTE: This problem is an easier version of Problem 110; it is strongly advised that you solve this one first.

## Answer

Woah. How do we begin? A good first thing to do is grab some pen and paper and try to work out some properties of this problem. I can see two off the bat:

- $$x=y=2n$$ is guaranteed to be solution.
- Both $$x$$ and $$y$$ have to be greater than $$n$$, because all variables are positive.

Can we work off of the second point? Since both variables are greater than $$n$$, we can say that $$x=a+n$$ and $$y=b+n$$, where $$a$$ and $$b$$ are some other positive integers. Let's plug these in and do some algebra and see where that gets us:
$$
\begin{aligned}
	\frac{1}{a+n}+\frac{1}{b+n} &= \frac{1}{n}
	\\
	\frac{b+n}{(a+n)(b+n)}+\frac{a+n}{(a+n)(b+n)} &= 
		\frac{1}{n}
	\\
	n(a+b+2n)&=(a+n)(b+n)
	\\
	an+bn+2n^2 &= ab+an+bn+n^2
	\\
	n^2 &= ab
\end{aligned}
$$
What is that last statement telling us? It means that **if we find two integers that when multiplied together give $$\mathbf{n^2}$$, then that corresponds to a solution!** But hold on, the only integers that satisfy this property are **factors of $$\mathbf{n^2}$$**!

For more clarity, I'll demonstrate using the example in the problem. Here, $$n=4$$, so $$n^2=16$$. The **factors of 16** are 1, 2, 4, 8, and 16. We can group these to **3** distinct pairs of integers, corresponding to the three solutions given above. We can work out the original solutions by using $$x=a+n$$ and $$y=b+n$$:

- (1, 16) $$\rightarrow (x,y)=(5,20)$$
- (2, 8) $$\rightarrow (x,y)=(6,12)$$
- (4, 4) $$\rightarrow (8, 8)$$

Notice that how we're using $$4$$ twice, because well, we squared it so of course we'll have that pair.

### Change in Problem Statement...

Look at what we just did. We transformed a problem dealing in fractions to dealing with integers. Specifically, **for a given $$\mathbf{n}$$, we have to find the number of distinct pairs of integers that multiply to $$\mathbf{n^2}$$**.

For **square** numbers, the **number of factors are always odd**. This is because of the repeated factor of the square root itself. Additionally, each factor can only pair with one other factor to produce the original number. Therefore, if we have the number of factors, we can add one and divide by 2 to get the number of solutions. For example, 16 has 5 factors, so $$n=4$$ has (5+1)/2 = **3 solutions**.

Thus, we want an $$n$$ with more than 1000 solutions, then $$n^2$$ better have more than 2000 factors.

### Finding Number of Factors of a Number

How do we find the number of factors of a number without going for a loop? If we have the prime factorization of the number, then it's simple. If $$n=p_1^{e_1}p_2^{e_2}\cdots p_k^{e_k}$$, then the number of factors is
$$
F(n) = \prod_{i=1}^k (e_i+1)
$$
Essentially, we add 1 to all the exponents, and we multiply them together. That's it! 

### Algorithm

Now, ultimately we want the number of factors of $$n^2$$. Because it's a square, the prime factorization will have **all even powers.** Thus, when we add 1 and multiply, we'll get an odd number, which makes sense.

To add more details to our problem, we would want the smallest number $$n$$ such that $$n^2$$ has at least 2000 factors. Coming back to the exponents of the factorization of $$n^2$$, it's highly unlikely that **one of these exponents is 6.** Why? Because that would mean the original number has a cube in it, and that increases the size immensely. Sticking with exponents of 2 and exponents of 4 (correspondingly squares and singleton prime factors in $$n$$) is our best bet.

If we assume that all of the $$e_i$$'s in $$n^2$$ are either 2 or 4, then all of the numbers we would multiply together would either be **3 or 5**. Thus, our problem is rephrased again: **What is the smallest integer greater than 2000 with only 3 and 5 as factors?** 

We can write a simple loop to keep multiplying 3 or 5 until we get past 2000. To make sure we do not skip any numbers, we'll keep a pointer as to the last time we multiplied by 3 or 5 in a running list. Each pointer will be multiplied by 3 or 5 respectively, and whichever multiplication is less, we increment that pointer.

For example, let $$t_3$$ be the number with the 3-pointer, and $$t_5$$ be the number with the 5-pointer. At the beginning, $$t_3=t_5=3^0\times5^0 = 1$$ and our list holds just {1}.

- Multiplying 3 by 1 is smaller than 5 by 1, so we multiply by 3 and move the 3-pointer. Thus our list is now {1, 3}, $$t_3=3$$, and $$t_5=1$$.
- We compare $$3t_3$$ and $$5t_5$$. Here, $$5\times1 < 3\times3$$, so we add 5 to the list and increment the 5-pointer **by 1 to the next location in the list.** Afterwards, both $$t_3$$ and $$t_5$$ point to **3**.
- We compare $$3t_3$$ and $$5t_5$$. Here, $$9<15$$, so $$t_3=5$$. Our sequence now reads 1, 3, 5, 9

