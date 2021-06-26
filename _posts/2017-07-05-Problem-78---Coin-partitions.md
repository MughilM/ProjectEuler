---
layout: post
title: "Problem 78 - Coin partitions"
date: 2017-07-05 14:32
mathjax: true
number: 78
---

## Question

Let $$p(n)$$ represent the number of different ways in which $$n$$ coins can be separated into piles. for example, five coins can be separated into piles in exactly seven different ways, so $$p(5)=7$$.


$$
\bigcirc\bigcirc\bigcirc\bigcirc\bigcirc
\\
\bigcirc\bigcirc\bigcirc\bigcirc\quad\bigcirc
\\
\bigcirc\bigcirc\bigcirc\quad\bigcirc\bigcirc
\\
\bigcirc\bigcirc\bigcirc\quad\bigcirc\quad\bigcirc
\\
\bigcirc\bigcirc\quad\bigcirc\bigcirc\quad\bigcirc
\\
\bigcirc\bigcirc\quad\bigcirc\quad\bigcirc\quad\bigcirc
\\
\bigcirc\quad\bigcirc\quad\bigcirc\quad\bigcirc\quad\bigcirc
$$


Find the least value of $$n$$ for which $$p(n)$$ is divisible by one million.

## Answer

This is exactly the same problem as [Problem 76]({{ site.url }}{%  post_url 2017-06-21-Problem-76---Counting-summations %}), just worded differently. Notice that since we are counting all the coins as "one pile", there is no more subtracting one from the answer. Regardless, we can use the same method for generating $$p(n)$$. Only difference now is that we append to our partition list as we go along, and our stopping condition is different. Additionally, I have done minor optimizations, such as reducing the number of computations we need to do per loop to try and make it as fast as possible.

One key optimization is that since $$p(n)$$ is calculated though a summation, we are checking to see if $$p(n)\mod 1000000 \equiv 0$$, and modulus distributes through sums, we don't have to store massive values of $$p(n)$$ and instead can store $$p(n)\mod 1000000$$. This will reduce the size of the numbers we add.

```python
partitions = [1]
pent = lambda x: x * (3 * x - 1) // 2
n = 1
while partitions[-1] != 0:
    k = 1
    currP = 0
    pentk = pent(k)
    while pentk <= n:
        currP = (currP + partitions[n - pentk] * (-1) ** (k + 1)) % 1000000
        # If k is positive, then it turns into
        # its negative counterpart,
        # Otherwise, it goes to the next number
        if k > 0:
            k *= -1
        else:
            k = k * -1 + 1
        pentk = pent(k)
    # Append...
    partitions.append(currP)
    n += 1

print('n =', len(partitions) - 1, 'is when p(n) is divisible by 1000000.')
```

Again, due to storing $$p(0)$$, we subtract one from the length of our list. The output of the above is,

```
n = 55374 is when p(n) is divisible by 1000000.
16.1984474 seconds.
```

Thus, **55374** coins is the fewest number needed such that the number of ways of splitting into piles is divisible by one million. The time is satisfactory, given the size of the problem (I don't want to think about the *actual* size of $$p(55374)$$). There could be additional steps we can take to minimize the size of the list we are storing.