---
layout: post
title: "Problem 48 - Self powers"
date: 2017-02-02 09:10
mathjax: true
number: 48
---

## Question

The series, $$1^1+2^2+3^3+\dots+10^{10}=10405071317$$.

Find the last ten digits of the series, $$1^1+2^2+3^3+\dots+1000^{1000}$$.

## Answer

As said many times previously, Python is extremely good in handling very large numbers. Therefore, we can calculate this sum directly, and then take modulus $$10^{10}$$ to grab the last ten digits. When we do that, it's possible we might get something less than 10 digits, if it had leading zeros, so keep that in mind.

 If we use `numpy`, then we can solve this in two lines :)

```python
nums = np.arange(1, 1001, dtype=object)
print(sum(nums ** nums) % 10 ** 10)
```

Running this short program results in,

```
9110846700
0.010996122083266172 seconds.
```

Therefore, the last ten digits are **9110846700**. This is exactly ten digits, so we have no leading zeros we need to put in. 

### Bonus!

Okay, so obviously if you have Python, then this problem is extremely trivial. However, most other languages have a cap on the size of integers that can be dealt with. Additionally, $$1000^{1000}$$ is a massive number, and it takes space to store it. If this limit is larger, then it can get unruly to store them. Is there a way to quickly calculate this sum?

Well first notice that we took modulus $$10^{10}$$ to get the last ten digits. In fact, modulus is distributive among addition, meaning $$(a+b)\mod c\equiv (a\mod c+b\mod c)\mod c$$. Okay, so this means we can reduce the size of the numbers we are adding.

Now is there a way to not calculate the very large raw values such as $$245^{245}$$ and $$819^{819}$$. Since we would be taking the mod, there is actually a quick way. The addition rule easily translates to the multiplication rule with modular arithmetic: $$(a\times b)\mod c \equiv ((a\mod c)\times(b\mod c))\mod c$$. If $$b=a$$ then we have a formula for $$a^2\mod c$$. This means we can calculate mods such like $$a^k\mod c$$ where $$k$$ is some power of two. We just repeatedly apply the formula i.e. $$3^{16}\mod 15$$:


$$
\begin{aligned}
3^2\equiv 9\mod 15
\\
3^4 = (3^2)^2\equiv ((3^2\mod 15)\times(3^2\mod 15))\mod 15\equiv(9\times9)\mod 15\equiv5\mod15
\\
3^8=(3^4)^2\equiv((3^4\mod 15)\times(3^4\mod 15))\mod 15\equiv (5\times5)\mod 15\equiv10\mod15
\\
3^{16}\equiv((3^8\mod15)\times(3^8\mod15))\mod15\equiv(10\times10)\mod 15\equiv10\mod 15
\end{aligned}
$$


This is all good and all, but most powers aren't powers of 2! What do we do then? This is when binary comes in. Recall the binary expansion of a number $$x$$ essentially tells us what powers of 2 to add together to get $$x$$. However, since this is an exponent, we have to multiply expressions together (so that the exponents get added together). In this case, we can use our modular arithmetic rules.

For example, let's try to do $$4^{49}\mod 11$$. First convert $$60$$ to binary. This is $$110001_2=2^5+2^4+2^0$$. So we are trying to find $$4^{2^5+2^4+2^0}\mod 11$$. However, this is also the same as $$\left(4^{2^5}\times4^{2^4}\times4^{2^0}\right)\mod 11$$. In that case, we can use the same method and find the appropriate powers of two:


$$
\begin{aligned}
4\equiv 4\mod 11
\\
4^2\equiv (4\times4)\mod 11 \equiv16\mod11\equiv 5\mod 11
\\
4^4\equiv (4^2\times4^2)\mod 11\equiv 25\mod 11\equiv 3\mod 11
\\
4^8\equiv (4^4\times4^4)\mod 11\equiv9\mod 11\equiv 9\mod 11
\\
4^{16}\equiv (4^8\times 4^8)\mod 11\equiv 81\mod 11\equiv 4\mod 11
\\
4^{32}\equiv (4^{16}\times 4^{16})\mod 11\equiv 16\mod 11\equiv 5\mod 11
\end{aligned}
$$


Now we pick the powers of two we need, plug them in, and calculate:


$$
\left(4^{2^5}\times4^{2^4}\times4^{2^0}\right)\mod 11\equiv(5\times4\times4)\mod11\equiv80\mod 11\equiv\boxed{3\mod 11}
$$


And that's it! We turned $$4^{49}$$ into $$80$$. The number of times we multiply within the parenthesis is proportional to the log base 2 of the power, since we are writing in binary. 

In terms of code, we first generate all mods of powers of 2, and then we multiply the appropriate results using the binary representation. The function is below:

```python
# Function to calculate a^b mod c for large
# values of b
def largeMod(a, b, c):
    # Calculate mod c for all power of 2 <= b.
    # Index is the power. so the array looks
    # like [a^(2^0) mod c, a^(2^1) mod c, ...]
    largestPower = int(math.log2(b))
    # First element is a^(2^0) = a^1 = a
    modsOfTwo = [a % c]
    for _ in range(largestPower):
        modsOfTwo.append(modsOfTwo[-1] ** 2 % c)
    # Convert the exponent to binary
    # to see which mods to use...
    bbinary = bin(b)[2:][::-1]
    prod = 1
    for i, bit in enumerate(bbinary, 0):
        if bit == '1':
            prod *= modsOfTwo[i]
    return prod % c
```

Notice I reverse the binary representation because the array is in reverse order. Anyways, now it's a simple loop:

```python
limit = 1000
s = 0
for i in range(1, limit + 1):
    s += largeMod(i, i, 10 ** 10)

print(s % 10 ** 10)
```

Running the code above results in,

```
9110846700
0.008301412421343397 seconds.
```

We get the same answer, and in this case it is ever so slightly faster. This function will be extremely useful, as taking a glimpse at the much later problems shows that we would be doing modular arithmetic a lot.