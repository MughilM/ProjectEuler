---
layout: post
title: "Problem 108 - Diophantine reciprocals I"
date: 2019-08-20 17:36
mathjax: true
number: 108
---

> In the following equation $$x$$, $$y$$, and $$n$$ are positive integers.
>
> 
> $$
> \frac{1}{x} + \frac{1}{y} = \frac{1}{n}
> $$
> 
>
> For $$n=4$$ there are exactly three distinct solutions:
>
> 
> $$
> \begin{aligned}
> 	\frac{1}{5} + \frac{1}{20} &= \frac{1}{4}
> 	\\
> 	\frac{1}{6} + \frac{1}{12} &= \frac{1}{4}
> 	\\
> 	\frac{1}{8} + \frac{1}{8} &= \frac{1}{4}
> \end{aligned}
> $$
> 
>
> What is the least value of $$n$$ for which the number of distinct solutions exceeds one-thousand?
>
> NOTE: This problem is an easier version of [Problem 110]({{ site.url }}{{ site.baseurl }}{% post_url 2019-08-21-Problem-110---Diophantine-reciprocals-II %}); it is strongly advised that you solve this one first.
>

<!--more-->

## Answer

First, because we are dealing with fractions with positive denominators, one thing you should realize is that $$x$$ and $$y$$ should both be strictly greater than $$n$$. Let's call the amount by which $$x$$ and $$y$$ exceed $$n$$, $$a$$ and $$b$$ respectively. So $$x=n+a$$ and $$y=n+b$$. Then, using some algebra, we can rewrite the equation as follows:


$$
\begin{aligned}
	\frac{1}{a+n} + \frac{1}{b+n} &= \frac{1}{n}
	\\
	\frac{a+b+2n}{(a+n)(b+n)} &= \frac{1}{n}
	\\
	ab + an + bn + n^2 &= an + bn + 2n^2
	\\
	ab &= n^2
\end{aligned}
$$


What is the last line telling us? It is essentially saying that any values $$a$$ and $$b$$ which multiply together to get $$n^2$$ are valid solutions to the original problem.

From the example given in the problem, we have $$n=4$$, so $$n^2=16$$. There are exactly 3 pairs of numbers which multiply to 16: {1, 16}, {2, 8}, and {4, 4}. Add $$n=4$$ to each one and we get the 3 solutions in the problem ({2, 8} corresponds to {6, 12}, etc.)

So now, the problem has been reframed: **How many pairs of integers are there that multiply together to get** $$\mathbf{n^2}$$? We can get this value if we how many factors $$n^2$$ have. For square numbers, the number of factors is odd, because the square root is duplicated. To include the square root i.e. $$n$$ as part of the solution, we **add 1 and divide 2 to the number of factors**.

For example, for $$n=4$$, we have $$n^2=16$$, and there are 5 factors of 16: {1, 2, 4, 8, 16}. This corresponds to (5 + 1) / 2 = 3 solutions, as we see above. Another example is $$n=15$$. Here, $$n^2=225$$, 9 factors: {1, 3, 5, 9, 15, 25, 45, 75, 225}. This corresponds to (9 + 1) / 2 = 5 solutions:


$$
\begin{aligned}
	\frac{1}{16} + \frac{1}{240} &= \frac{1}{15}
	\\
	\frac{1}{18} + \frac{1}{90} &= \frac{1}{15}
	\\
	\frac{1}{20} + \frac{1}{60} &= \frac{1}{15}
	\\
	\frac{1}{24} + \frac{1}{40} &= \frac{1}{15}
	\\
	\frac{1}{30} + \frac{1}{30} &= \frac{1}{15}
\end{aligned}
$$


**To find the number of factors of a number, first find the prime factorization of the number. Then, take the exponents, add one to each, and multiply them together**. For example, $$225 = 3^2\times5^2$$, so 225 has (2 + 1) x (2 + 1) = 3 x 3 = **9** factors.

## Solving the problem...

Now that we know how to find the number of solutions for a given $$n$$ (find the number of factors of $$n^2$$, add one, then divide by 2), we can turn our attention to answering the question.

Our logic will be working backwards. If the number of solutions exceeds 1000, then worst-case, we have 1001 solutions. This means the number of factors of $$n^2$$ is at least 2001.

A brief side: Since we're squaring $$n$$, the prime factorization will have only even exponents, which means when we calculate the number of factors, we will be multiplying odd numbers together, such as 3 and 5. **Remember this is telling us the size of the exponent. A 3 means a prime was squared, while a 5 means a prime was raised to the fourth power.** We want to find the **least** value of $$n$$, so if possible we only want to deal with 3s and 5s.

Going off of this assumption, we would want the **least number greater than 2001 that only has 3 and 5 as prime factors.** To generate them in order, we will keep a running list of numbers satisfying this property, with two pointers $$p_3$$ and $$p_5$$ pointing to the **smallest** number that can be multiplied by 3 or 5. Let's show an example:

- First, start with $$A=[1]$$. Here, $$p_3 = p_5 = 0$$.
- We have two choices: we either multiply 1 by 3 or 1 by 5. We choose the smaller one, 3. Now, since we can't multiply 1 by 3 anymore, we move to the next number, which is 3. At the end of this iteration, the list is [1, 3].
- Next, we compare multiplying 3 by 3 and 1 by 5. The latter is smaller, so we increment the 5-pointer. The list is [1, 3, 5].
- Since we've already multiplied 1 by 5, we move to the next smallest number. We compare 3 x 3 and 3 x 5. The former is smaller, so we add 9: [1, 3, 5, 9].
- Now comes a special case. The 3-pointer is on 5, but the 5-pointer is on 3. In this case, we've double counted the product, so we add 15 as usual, but now we increment **both** pointers. Now, the 3-pointer is on 9, while the 5-pointer is on 5. The list is [1, 3, 5, 9, 15].

In this fashion, we can generate the numbers **in order**. Eventually, we see that the smallest number greater than 2001 is **2025**, which is $$3^4 \times 5^2$$.

## Translating the product into a factorization

What is $$3^4\times 5^2$$ telling us? It means that when we calculate the number of factors, we multiply by 3 4 times, and by 5 twice. This could mean 4 separate instances of 3, or two instances 9. **However, remember these numbers correspond to the exponent, and we want to keep things as small as possible.** A factor of 3 means the prime was squared, while a factor of 5 means the prime was raised to the fourth power.

As an example, a number with 2025 factors is $$2^8\times3^8\times5^4\times7^4$$. However, **we want the smallest number with this property.** One way we can force the number to be the small is **to keep the exponents small.** We need to put the fourth powers on 2 and 3, while the squares go on the primes after those.

The result is $$2^4\times 3^4\times 5^2\times 7^2\times 11^2\times 13^2$$. However, remember this is $$n^2$$. Thus, all we have to do is square root and we have the answer! The square root is consequently is $$2^2\times 3^2\times 5\times7\times 11\times 13 = \boxed{180180}$$.

The code for this exercise is just for finding the least number greater than 2001 that had only 3 and 5 as prime factors.

```python
factLimit = 2001
# Find smallest integer greater than
# factLimit that only has 3 and 5 as prime factors
# Two pointers to the current multiplicand
# for either 3 or 5.
threePoint = 0
fivePoint = 0
integers = [(1, [])]  # Start with 1 = 3^0 x 5^0
while integers[-1][0] < factLimit:
    # Find the minimum value of 3 multiplied
    # by the value at threePoint and 5 with
    # value at fivePoint. Increment the pointer
    # depending on which was chosen.
    # If they're both equal, then we add one of them
    # and increment both.
    if 3 * integers[threePoint][0] < 5 * integers[fivePoint][0]:
        integers.append((3 * integers[threePoint][0], integers[threePoint][1] + [3]))
        threePoint += 1
    elif 5 * integers[fivePoint][0] < 3 * integers[threePoint][0]:
        integers.append((5 * integers[fivePoint][0], integers[fivePoint][1] + [5]))
        fivePoint += 1
    else:
        integers.append((3 * integers[threePoint][0], integers[threePoint][1] + [3]))
        threePoint += 1
        fivePoint += 1

# Calculate value.
primes = [2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31]
prod = 1
# Sort the powers greatest to least
for i, power in enumerate(sorted(integers[-1][1], key=lambda x: -x)):
    prod *= primes[i] ** (power - 1)
print(int(prod ** 0.5), 'with', (integers[-1][0] + 1) // 2, 'solutions.')
```

Running the code results in the correct output of

```
180180 with 1013 solutions.
5.779999999999674e-05 seconds.
```

Thus, our final answer is **180180**.