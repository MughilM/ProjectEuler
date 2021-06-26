---
layout: post
title: "Problem 233 - Lattice points on a circle"
date: 2019-07-20 16:10
number: 233
mathjax: true
---

> Let $$f(N)$$ be the number of points with integer coordinates that are on a circle passing through $$(0,0),(N,0),(0,N)$$, and $$(N,N)$$.
>
> It can be done shown that $$f(10000)=36$$.
>
> What is the sum of all positive integers $$N\leq 10^{11}$$ such that $$f(N)=420$$?

<!--more-->

## Answer

One thing I want to get out of the way is the circle we are dealing with. The circle passes through 4 points of a square with side length $$N$$. Because it circumscribes this square, the diagonal of the square is the diameter itself. The length of this diameter is $$N\sqrt{2}$$, and so the radius is $$N\sqrt{2}/2$$. Both the circle and square also have the same center. Therefore, the center is located at $$(N/2, N/2)$$. So, our equation for this circle is


$$
\left(x-\frac{N}{2}\right)^2+\left(y-\frac{N}{2}\right)^2=\frac{N^2}{2}
$$


Now onto the actual solution.

This is, hands down, my favorite problem of the whole set (so far). Before I talk about a solution, I want you to watch the video below. It is by Grant Sanderson of the YouTube channel 3Blue1Brown and in the process of solving one problem, he mentions a couple of important points with regards to this one. It's 30 minutes, but well worth it. Come back here once you've watched it.

[![Circle](https://img.youtube.com/vi/NaL_Cb42WyY/0.jpg)](https://www.youtube.com/watch?v=NaL_Cb42WyY)

Ok, so to summarize the key points in the video:

- The number of lattice points a circle centered at the origin and radius $$\sqrt{N}$$ is dependent on the **prime factorization** of $$N$$.
- Write the factorization of $$N$$ as $$2^{a_0}\left( 5^{b_1} 13^{b_2}\cdots\right)\left( 3^{c_1}7^{c_2}\cdots \right)$$ where the primes associated with the $$b_i$$ exponents are **one above a multiple of 4** i.e. $$1\mod 4$$ and the primes associated with the $$c_i$$ exponents are **one below a multiple of 4** i.e $$3\mod 4$$.
- **The number of lattice points is** $$4(b_1+1)(b_2+1)\cdots=4\prod_{i=1}^\infty(b_i+1)$$.
- If any of the $$c_i$$ exponents are **odd**, then the number of lattice points is 0. Otherwise, they do not affect the count.
- Factors of 2 do not affect the count.

Cool, so using this formula we can directly solve the problem? Well you might have noticed something...

### Video's Circle vs. Our Circle

The circle in the video is centered at the origin and has radius $$\sqrt{N}$$. The equation is $$x^2+y^2=N$$. However, our circle isn't like that! Instead, our circle is centered at $$(N/2,N/2)$$ and has radius $$\sqrt{N}/2$$. If our circle is centered at a lattice point, then we can simply translate it back to the origin. However, it is entirely possible that $$N$$ is odd and it lands at a non-lattice point. In that case, we can't simply translate it. Is this really the same as the circle problem in the video?

Here is where I make a claim and convince you how to transform our existing problem into something like the video. I claim that finding the number lattice points to our circle **is the same as** finding the number of lattice points to a circle centered at the origin with radius $$N$$. In other words, the two circles


$$
\begin{aligned}
	\left(x-\frac{N}{2}\right)^2 + \left(y-\frac{N}{2}\right)^2 &= \frac{N^2}{2}
	\\
	x^2+y^2 &= N^2
\end{aligned}
$$


will cross the **same number of lattice points.** This might seem crazy at first, but I will explain now.

First, we break this up into cases. Our number $$N$$ can either be **even** or **odd**.

#### $$N$$ is even

If $$N$$ is even, then it can be represented by $$2k$$ where $$k$$ is any integer. If we substitute this into our original circle, we get


$$
\begin{aligned}
	\left(x-\frac{2k}{2}\right)^2 + \left(y-\frac{2k}{2}\right)^2 &= \frac{(2k)^2}{2}
	\\
	(x-k)^2+(y-k)^2 &= 2k^2
\end{aligned}
$$


Now, we have a circle that is centered at $$(k,k)$$. This is in effect our origin, so this will cross the same number of lattice points as the same circle centered at the origin. Next, remember the final bullet point, that **factors of 2 do not affect the count**. Thus, a circle with radius $$\sqrt{2\cdot2k^2}$$ will cross the same number of lattice points as one with radius $$\sqrt{2k^2}$$. However, $$\sqrt{2\cdot2k^2} = \sqrt{4k^2}=\sqrt{(2k)^2}=\sqrt{N^2}=N$$. Therefore, we can make the radius of the circle be $$n$$ and not affect the count. Thus, the case where $$N$$ is even is taken care of.

#### $$N$$ is odd

In this case, it's a bit trickier. Here, we won't do something like the previous case e.g. assume $$N$$ is $$2k+1$$ but instead look at what happens to the equation. If $$N$$ is odd, then the right-hand side (RHS) of the equation will stay a fraction. Since the RHS is a fraction, the LHS must also be a fraction, which means each square must also be a fraction. We can assume something like this:


$$
\begin{aligned}
\left( x-\frac{N}{2} \right)^2 &= \frac{a^2}{4}
\\
\left( y-\frac{N}{2} \right)^2 &= \frac{b^2}{4}
\end{aligned}
$$


where $$a$$ and $$b$$ are integers. The denominator is 4 due to the square of a half. Then, adding these, our equation becomes


$$
\begin{aligned}
	\frac{a^2}{4} + \frac{b^2}{4} &= \frac{N^2}{2}
	\\
	a^2+b^2 &= 2N^2
\end{aligned}
$$


Since $$a$$ and $$b$$ were integers, that last equation is basically a circle centered at the origin, which means we can use the same algorithm from the video. Additionally, since factors of 2 don't affect the count, the equation simplifies to


$$
x^2 + y^2=N^2
$$


Here, I just replaced $$a$$ and $$b$$ with $$x$$ and $$y$$. Therefore, this covers the odd case.

### Working Through the Problem's Example

Alright! Now that we have convinced ourselves that our circle will cross the **same number of lattice points** as a circle centered at the origin with radius $$N$$, I'll quickly work through the given example in the problem.

In the problem, $$N=10000$$. The video told us we had to factorize the radius **squared** *(NOTE: Do not get confused. The video was dealing with a circle with radius $$\sqrt{N}$$ and they factorized $$N$$. Here, we have a radius of $$N$$ and so we must factorize $$N^2$$).* The prime factorization of $$N^2=10^8$$ is $$2^8\times5^8$$. Factors of 2 don't affect the count, so we can ignore that exponent. We only have a 5, which can be broken down into complex factors. Add one to the exponent to get 9, then multiply by 4 to cover the 4 rotations it can go. Therefore, the total is 36, just as the problem.

A note about the factorization of $$N^2$$. Since it's a square number, each exponent will be **even**. Remember from the video, that Grant told us that if the factorization had any Gaussian primes, then that wipes out any points. However, this is only the case if the exponent for the Gaussian prime was odd. If it's even, then this doesn't affect the count. Well, since the exponents are all even, we are guaranteed that any Gaussian primes present *will not affect the count.* **This becomes important when we find a way to solve the problem.**

### Solving the Problem

But hold on. Our problem doesn't ask for the number of lattice points given $$N$$. It instead asks for all $$N$$ that pass through a specified number of points -- the inverse of what we just did. How do we solve the inverse? Well, for starters we can basically work backwards.

Remember, to find the number of lattice points, we find the prime factorization of $$N^2$$, add one to all the exponents of the non-Gaussian primes, multiply them together, and multiply by 4. If we have the result, we can divide by 4, and find what numbers multiplied together give it.

Back to the problem's example, the answer is 36. Divide by 4 and we get 9. That means when we add one and multiply the exponents together, we should get 9. Since the product is 9, either we have **one exponent that is 8**, or **two exponents that are 2 each (to get 3 times 3 = 9)**. In our problem example, we had an exponent that was 8. But we could've also had a number like $$2^4\times 5^2\times 13^2=67600=260^2$$. In this case, $$N=260$$, and notably much less than $$N=10000$$ in the problem.  Here's another one: $$2^2\times3^2\times5^2\times7^2\times17^2=12744900=3570^2$$. Even though 3 and 7 are Gaussian primes, because there are two of them, they do not affect the count. Thus, we only worry about the exponents of 5 and 17. To get all values of $$N$$ below a limit, we would need to mix match the exponents of the non-Gaussian primes (5, 13, 17, etc.) with the exponents of 2 and the other Gaussian primes.

Here's another way to look at it. Since we need the exponents in $$N^2$$ to either be 8 or two 2s (to have the circle cross 36 lattice points), the exponents in $$\mathbf{N}$$ need to consequently be either 4 or two 1s, because remember squaring a number doubles the exponents. We're almost at the point where we can get a proper algorithm to search all $$N$$ up to a limit.

#### Example of One Loop for Limit of 1000

Let's assume our limit was 1000. As the previous paragraph states, we have two cases. Either we have something raised to the 4th power, or two separate singleton primes. The smallest prime that can affect the count is 5. Now, only $$5^4=625<1000$$, and so that's the only value below our limit for the 4th power case.

Let's go to the next case. We need two singleton primes. Since the smallest $$1\mod 4$$ prime is 5, the other factor **cannot be bigger than 1000/5 = 200**. The next smallest needed prime is 13. So the smallest $$N$$ that crosses 36 lattice points is actually $$5\times 13=65$$. 

But remember we're not done! We can take any of the other $$3\mod 4$$ primes, in addition 2, and include them in the product as well e.g. $$2\times3\times5\times13=390$$ and $$3^2\times5\times13=585$$ and $$2\times7\times5\times13=910$$ **all cross 36 lattice points**. Essentially, we have a whole set **separate** products we can multiply with the product consisting of only $$1\mod 4$$ primes and **still have a circle crossing 36 points!** This other product only contains, in addition to 2, primes that are $$3\mod 4$$. 

Back to our example, this other product can't exceed $$\lfloor 1000/65 \rfloor=15$$. Our products are thus 2, 3, 4, 6, 7, 8, 9, 11, 12, 13, and 14. Notice multiples of 5 are not present because $$5\equiv 1\mod 4$$ and would change the number of lattice points this crosses. Multiplying 65 by each of these numbers gets us an $$N$$ less than 1000 that crosses 36 points.

Of course, we are not done yet. Once we're done with 65, we move to $$5\times 17=85$$, and **repeat the process.** The separate products this time only go up to 11. 

### Our Algorithm

At this point, we have a sure algorithm we can use if we're given the number of crossed lattice points $$C$$ and the limit $$L$$. I've summarized the algorithm below. If you do not understand the reasoning behind some of the steps, then please re-read the previous 2-3 sections until you do. After this, we will apply this to our number, 420.

1. Divide $$C$$ by 4, then find **all possible products** that will result in $$C/4$$. Subtracting one and halving the values in these products will get you what the exponent values should be for $$N$$ e.g. $$C=36\Rightarrow 9$$, products of 9 are 9 and $$3\times3$$, so possible exponent values are $$\left\{\left\{\frac{9-1}{2}\right\}, \left\{ \frac{3-1}{2}, \frac{3-1}{2} \right\}\right\} = \{ \{4\}, \{1,1\} \}$$.
2. For each set of exponents, grab all possible products **that consist of only $$1\mod 4$$ primes** below the limit that have these specific exponents. Call this set $$P_1$$.
3. For each product $$r$$ in $$P_1$$, generate a *second* set of products $$P_2$$ that only consist of primes that $$3\mod 4$$ and 2. These should be less than $$L/r$$.
4. Multiplying $$r$$ by each product in $$P_2$$ gets you an $$N$$ value less than $$L$$ that crosses exactly $$C$$ lattice points. Add each one to a running sum (since that's what the problem asks for).

Doing this for all cases will get the sum we need. Now, onto the actual problem!

### Implementation

Our number is 420. We first divide it by 4 to get **105**. Now, the factorization of 105 is exactly $$3\times 5\times 7$$. We must now find all possible ways that the exponents can be. Technically, one of them is the number itself minus one and then divided by 2 = 52. However, $$5^{52}$$ is oh so much larger than $$10^{11}$$ so that obviously can't work. With our three distinct numbers, we have 3 different sets of exponents for the prime factorization of $$N$$ we must consider:

- One is cubed, one is squared, and the other just multiplied = $$\{3,2,1\}$$ (Taken directly from the 3, 5, 7. Subtract one and divide each by 2).
- One is raised to the 10th power, and the other is squared = $$\{10, 2\}$$ (This comes from the fact $$21\times 5=105$$).
- One is raised to the 7th power, and the other is cubed = $$\{7,3\}$$ (This stems from $$15\times7=105$$).

We have 2 sets of 2, and one set of 3. We can package the 2 sets of 2 exponents into its own for loop, and the other set of 3 into another.

We also need a list of all the $$1\mod 4$$ primes and products of $$3\mod 4$$ primes (including 2). For the former, we can use `primesieve` and quickly filter. As for the limit, we **only need primes up until** $$\frac{10^{11}}{5^313^2}=4\,733\,727$$. This is because the smallest solution we have is $$5^3\times13^2\times17=359125$$. As for the latter, we can create a sieve where we mark where the multiples are. The size of this sieve only goes up until the smallest solution. I've put the code for these first two preliminary steps below.

```python
limit = 10 ** 11
# Maximum prime list we need
# is up to 10^11 / (5^3 * 13^2)
primeBound = limit / (125 * 169)
primes = primesieve.numpy.primes(primeBound)
# Grab all primes that are of 1 mod 4
primes = primes[primes % 4 == 1]

# Now the sieve of all integers that
# HAVE at least one factor from the prime list.
# The max size possible should be
# 10^11 / (5^3 * 13^2 * 17), since that is our
# smallest solution.
sieveLimit = int(primeBound / 17)
sieve = np.zeros(sieveLimit + 1, dtype=np.uint8)
for prime in primes:
    # Grab multiples
    multiples = np.arange(prime, sieveLimit + 1, prime)
    # Set all locs to one...
    sieve[multiples] = 1
```

The `numpy` package allows for simple assigning in an array. Now for the first two cases where we have 2 exponents.

#### First two cases

Let's take the most constraining case, where one prime is raised to the 10th power, and the other is squared. When we loop through candidate $$1\mod 4$$ primes, we go up until the **10th root of the limit**. In our case, only $$5^{10}<10^{11}$$ and $$13^{10}$$ turns out to be bigger. Once we have the first prime, for the second prime we loop through $$\sqrt{\frac{10^{11}}{5^{10}}}$$ since we want to ensure the product does not exceed $$10^{11}$$. Here, we loop until 101. The first product is $$5^{10}\times 13^2$$. Remember before, now we take all products containing $$3\mod 4$$ primes (and 2), and multiply each one with this number to get an $$N$$ that crosses 420 lattice points. Multiply them all and add them! Then move onto the next prime, and so on. Repeat for the other case.

```python
# Cases 1 and 2, powers of 10 and 2, and powers of 7 and 3
total = 0
for ex1, ex2 in [(10, 2), (7, 3)]:
    for p1 in primes[primes < limit ** (1/ex1)]:
        # Now we need all the different primes
        # that are less than the square/cube root
        # of 10^11 / prime^10 (or prime^7)
        for p2 in primes[(primes < (limit / p1 ** ex1) ** (1/ex2)) & (primes != p1)]:
            value = p1 ** ex1 * p2 ** ex2
            numOfMultiplies = limit // value
            # Get non 1 mod 4 prime multiples, multiply them,
            # and finally sum them...
            # DON'T INCLUDE 0...
            multiples = np.where(sieve[:numOfMultiplies + 1] == 0)[0][1:]
            total += np.sum(value * multiples)
```

#### Other case with 3 primes

This loop is basically the same as the other loop, except we are now choosing 3 primes instead of 2, and so we have an extra for loop.

```python
# Case 3, where we have THREE primes,
# one cubed, squared, and the remaining just multiplied.
for p1 in primes[primes < limit ** (1/3)]:
    # Next is squared...
    for p2 in primes[(primes < (limit / p1 ** 3) ** (1/2)) & (primes != p1)]:
        # Next get all individually...
        for p3 in primes[(primes < limit / (p1 ** 3 * p2 ** 2)) & (primes != p1) &
                         (primes != p2)]:
            value = p1 ** 3 * p2 * p2 * p3
            numOfMultiplies = limit // value
            multiples = np.where(sieve[:numOfMultiplies + 1] == 0)[0][1:]
            total += np.sum(value * multiples)
```

### Final Solution

We are done now! All together, the code in its final form.

```python
# Exponents can 3, 2, 1 (for 6, 4, 2 ==> 7 * 5 * 3 = 105)
# 10, 2 (for 20, 4 ==> 21 * 5 = 105)
# 7, 3 (for 14, 6 ==> 15 * 7 = 105)

limit = 10 ** 11
# Maximum prime list we need
# is up to 10^11 / (5^3 * 13^2)
primeBound = limit / (125 * 169)
primes = primesieve.numpy.primes(primeBound)
# Grab all primes that are of 1 mod 4
primes = primes[primes % 4 == 1]

# Now the sieve of all integers that
# HAVE at least one factor from the prime list.
# The max size possible should be
# 10^11 / (5^3 * 13^2 * 17), since that is our
# smallest solution.
sieveLimit = int(primeBound / 17)
sieve = np.zeros(sieveLimit + 1, dtype=np.uint8)
for prime in primes:
    # Grab multiples
    multiples = np.arange(prime, sieveLimit + 1, prime)
    # Set all locs to one...
    sieve[multiples] = 1

# Cases 1 and 2, powers of 10 and 2, and powers of 7 and 3
total = 0
for ex1, ex2 in [(10, 2), (7, 3)]:
    for p1 in primes[primes < limit ** (1/ex1)]:
        # Now we need all the different primes
        # that are less than the square/cube root
        # of 10^11 / prime^10
        for p2 in primes[(primes < (limit / p1 ** ex1) ** (1/ex2)) & (primes != p1)]:
            value = p1 ** ex1 * p2 ** ex2
            numOfMultiplies = limit // value
            # Get non 1 mod 4 prime multiples, multiply them,
            # and finally sum them...
            # DON'T INCLUDE 0...
            multiples = np.where(sieve[:numOfMultiplies + 1] == 0)[0][1:]
            total += np.sum(value * multiples)

# Case 3, where we have THREE primes,
# one cubed, squared, and the remaining just multiplied.
for p1 in primes[primes < limit ** (1/3)]:
    # Next is squared...
    for p2 in primes[(primes < (limit / p1 ** 3) ** (1/2)) & (primes != p1)]:
        # Next get all individually...
        for p3 in primes[(primes < limit / (p1 ** 3 * p2 ** 2)) & (primes != p1) &
                         (primes != p2)]:
            value = p1 ** 3 * p2 * p2 * p3
            numOfMultiplies = limit // value
            multiples = np.where(sieve[:numOfMultiplies + 1] == 0)[0][1:]
            total += np.sum(value * multiples)

print(total)
```

It looks long, but is really just a couple of for loops one after the other. Finally, running this monster code, our answer is

```
271204031455541309
7.3993970000000004 seconds.
```

Therefore, the sum of all $$N<10^{11}$$ where the circle passes through exactly 420 lattice points is **271204031455541309**. A fittingly massive number for a problem that took a massive amount of analysis and work. I hope you enjoyed through my explanations and learning a lot about circles :) Until the next problem....