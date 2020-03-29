---
layout: post
title: "Problem 99 - Largest exponential"
date: 2017-08-18 01:40
mathjax: true
number: 99
---

## Question

Comparing the two numbres written in index form like $$2^{11}$$ and $$3^7$$ is not difficult, as any calculator would confirm that $$2^{11}=2048<3^7=2187$$.

However, confirming that $$632382^{518061}>519432^{525806}$$ would be much more difficult, as both numbers contain over three million digits.

Using [base_exp.txt](https://projecteuler.net/project/resources/p099_base_exp.txt) (right click and 'Save Link/Target As...'), a 22K text file containing one thousand lines with a base/exponent pair on each line, determine which line number has the greatest numerical value.

NOTE: The first two lines in the file represent the numbers in the example given above.

## Answer

Of course, even Python can't handle that many ridiculous computations very quickly. We need a way to decrease the size of our computations. 

One really good way is that we can take the **logarithm** of both sides. If a function is always increasing or always decreasing, as exponentials are, then taking the algorithm will **always** preserve that property. So if beforehand, $$a^b>c^d$$, then $$\log a^b > \log c^d$$. However, we can use an additional property of bringing down that power in the front: $$b\log a>d\log c$$. **This is how we can decrease the size of our computations!**

Additionally, the **base** of the logarithm does **not** matter. This will work no matter the base. Below  the code uses the `numpy` package to calculate $$b\log a$$ for all of our pairs in a single line. The function `np.argmax` will get the index of the maximum value. Of course, we'll have to add one since these indices are zero-indexed.

```python
exponents = np.genfromtxt(
    'p099_base_exp.txt', delimiter=',', dtype=np.int64)
logs = exponents[:, 1] * np.log10(exponents[:, 0])
# Now find the maximum argument and print its line
index = np.argmax(logs)
print(index + 1)
print(exponents[index].tolist())
```

Running this short code results in an output,

```
709
[895447 504922]
0.011759600000000037 seconds.
```

Thus, $$895447^{504922}$$ is the largest number in our list, and it happens at line **709**.