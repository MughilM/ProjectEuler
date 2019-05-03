---
layout: post
title: "Problem 25 - 1000-digit Fibonacci number"
date: 2016-05-06
mathjax: true
number: 25

---

## Question

The Fibonnaci sequence is defined by the recurrence relation:


$$
F_n = F_{n-1} + F_{n-2}
$$


where $$F_1=1$$ and $$F_2=1$$.

Hence the first 12 terms will be:


$$
\begin{align*}
	F_1 &= 1 \\
	F_2 &= 1 \\
	F_3 &= 2 \\
	F_4 &= 3 \\
	F_5 &= 5 \\
	F_6 &= 8 \\
	F_7 &= 13 \\
	F_8 &= 21 \\
	F_9 &= 34 \\
	F_{10} &= 55 \\
	F_{11} &= 89 \\
	F_{12} &= 144
\end{align*}
$$


The 12th term, $$F_{12}$$, is the first term to contain three digits.

What is the index of the first term in the Fibonacci sequence to contain 1000 digits?

## Answer

There is a simple way to determine the number of digits of an integer. Converting to a string every single time would eventually add up. Instead, we can take the $$\log_{10}$$ of the number, and the integer part plus one is how many digits we have. For example, numbers between 10 and 99 inclusive all have 2 digits, while the $$\log_{10}$$ of these numbers are between 1 and 2.

In this way, we dynamically generate the Fibonacci numbers until the log base 10 is at least 1000.

```python
digitNum = 1000
a = 1
b = 1
n = 2
while math.log10(b) + 1 < digitNum:
    temp = a + b
    a = b
    b = temp
    n += 1

print(n)
```

The output is,

```python
4782
0.0033090331152200118 seconds.
```

Thus, the **4782**nd Fibonacci number has at least 1000 digits.