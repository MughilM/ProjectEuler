---
layout: post
title: "Problem 76 - Counting summations"
date: 2017-06-21, 12:35
mathjax: true
number: 76
---

## Question

It is possible to write five as a sum in exactly six different ways:


$$
\begin{aligned}
&4 + 1
\\
&3 + 2
\\
&3 + 1 + 1
\\
&2 + 2 + 1
\\
&2 + 1 + 1 + 1
\\
&1 + 1 + 1 + 1 + 1
\end{aligned}
$$


How many different ways can one hundred be written as a sum of at least two positive integers?

## Answer

Breaking up a number into sets of sums like this are called finding the **partitions** of a number. It is an ancient concept and hence the area of extensive research, which is good for us because chances are someone has found a way to calculate these. 

The actual definition of a partition also includes the number itself (in this case it would include "5" as a "sum"). The number of partitions of a number $$n$$ is $$p(n)$$. In our example, $$p(5)=7$$. The smart thing to do is to calculate the partitions this way and simply subtract one from the final answer.

### Recurrence Relation

It isn't difficult to get some intuitive sense that the number of partitions of $$n$$ would somehow depend on the values of $$p(n)$$ before it. The [Wikipedia](https://en.wikipedia.org/wiki/Partition_function_(number_theory)#Recurrence_relations) article shows that the following recursive definition exists for $$p(n)$$:


$$
p(n) = \sum_{k\neq 0}^\infty (-1)^{k+1}p\left(n - \frac{k(3k-1)}{2}\right)
$$


where $$p(0)=1$$ and $$p(n) = 0$$ if $$n<0$$. Thus, even though the sum goes to infinity, it will consist of finitely many non-zero terms. Additionally, $$\frac{k(3k-1)}{2}$$ steadily increases as $$k$$ goes $$1, -1, 2, -2, 3, -3, 4, \dots$$. In fact, these are the pentagonal numbers, which have other uses. Thus, this is a perfect application of a while loop to properly add all the non-zero terms.

This problem is pretty simple to code, with setting our array and our base case, and simply looping up to the limit, applying the rule above. Remember, $$p(n)$$ includes $$n$$ itself as a "sum", so when we report our answer, we subtract one.

```python
limit = 100

p = [0] * (limit + 1)
# Base case
p[0] = 1
for i in range(1, len(p)):
    k = 1
    # lambda function of pentagonal number
    pent = lambda x: x * (3 * x - 1) // 2
    while pent(k) <= i:
        p[i] += p[i - pent(k)] * int((-1) ** (k + 1))
        # If k is positive, then it turns into
        # its negative counterpart,
        # Otherwise, it goes to the next number
        if k > 0:
            k *= -1
        else:
            k = k * -1 + 1
print(p[limit] - 1)
```

The output after running is,

```
190569291
0.0013518999999999615 seconds.
```

Therefore, 100 can be written as a sum of positive integers in **190569291** ways.