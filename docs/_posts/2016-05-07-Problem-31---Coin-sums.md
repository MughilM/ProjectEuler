---
layout: post
title: "Problem 31 - Coin sums"
date: 2016-05-07 09:36
mathjax: true
number: 31
---

## Question

In England the currency is made up of pound, £, and pence, p, and there are eight coins in general circulation:

<p align="center">
1p, 2p, 5p, 10p, 20p, 50p, £1 (100p), £2 (200p) 
</p>

It is possible to make £2 in the following way:

<p align="center">
1x£1 + 1x50p + 2x20p + 1x5p + 1x2p + 3x1p 
</p>

How many different ways can £2 be made using any number of coins?

## Answer

This is very similar to finding partitions of a number. However, instead of the set of natural numbers at our disposal, we have the coin values above. To solve this, we note that in a certain set of coin values, we can either use a certain coin or not use it. 

For example, with £2 given above, we can use £2 coin or not. If we use it, then that is obviously one way to make the value. If we don't, our next option is to use the £1 coin. At that point, the problem turns into one of how many ways we can create £2 - £1 = £1 using the coins. In this way, we can recurse down, where each step we either choose the largest value available to us, or not use it. The base case will be when we have exactly £0 left. In this case, we have found a way to make change. Some of the special cases we have to take care of is when we have a negative amount, or when we have no more coins left to choose. Below is the function used to calculate the number of ways.

```python
def makeChange(value, coins):
    # Coins will be in decreasing order i.e.
    # largest is first
    # Can't make change
    if value < 0:
        return 0
    # I can make no change by choosing nothing
    elif value == 0:
        return 1
    # I can't make something out of nothing
    elif value > 0 and len(coins) == 0:
        return 0
    # Recursive call: Have a case where I choose the largest
    # coin while decrementing value and a case where I skip it
    # and don't decrement value
    else:
        return makeChange(value - coins[0], coins) + makeChange(value, coins[1:len(coins)])
```

After this, we can simply make the array of coin values, and call our function:

```python
coins = [200, 100, 50, 20, 10, 5, 2, 1]
print(makeChange(200, coins))
```

Running the code above results in an output of,

```python
73682
1.9159925933914943 seconds.
```

Thus, there are **73682** ways to make £2 from the coins given.