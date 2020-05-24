---
layout: post
title: "Problem 97 - Large non-Mersenne prime"
date: 2017-08-18 01:10
number: 97
mathjax: true
---

## Question

The first known prime found to exceed one million digits was discovered in 1999, and is a Mersenne prime of the form $$2^{6972593}-1$$; it contains exactly 2,098,960 digits. Subsequently other Mersenne primes, of the form $$2^p-1$$, have been found which contain more digits.

However, in 2004 there was found a massive non-Mersenne prime which contains 2,357,207 digits: $$28433\times2^{7830457}+1$$.

Find the last ten digits of this prime number.

## Answer

To find the last ten digits of a number, we take the mod $$10^{10}$$. Now then, remember how I said that Python is really good and handling massive numbers? We can actually put this directly like so...

```python
# ONE LINE FOR THE WIN!!
print((28433 * 2 ** 7830457 + 1) % (10 ** 10))
```

And instantly, we get an answer!

```
8739992577
0.064769 seconds.
```

Thus, the last ten digits of this number is **8739992577**.

### How SHOULD we do it?

Well that was a bit anticlimatic. Not very exciting in that we basically used a state-of-the-art calculator. To actually do this question, we utilize a fast way to take the modulus of a very large number.

Repeatedly, we use the property that $$ab$$ mod $$c$$ is $$(a\mod c)\times(b\mod c)\mod c$$. Thus, since we have a power, we can split up our power in multiple smaller powers. As an example, say we have $$5^{214}\mod 7$$. What we do, is we convert the power (214) to binary, which is 11010110. Then, because this is binary, we can say that $$5^{214} = 5^{128}\cdot5^{64}\cdot5^{16}\cdot5^{4}\cdot5^{2}$$. The higher powers of 5 can be calculated with the lower powers, and since we are taking the modulus each time, we will only be dealing with numbers less than the mod!

Multiplication and addition both hold under modulus as well. So that is the general method, and the code....well I'll leave that to you :)