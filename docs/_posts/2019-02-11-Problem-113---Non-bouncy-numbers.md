---
layout: post
title: "Problem 113 - Non-bouncy numbers"
date: 2019-02-11 13:46
mathjax: true
number: 113
---

> Working from left-to-right if no digit is exceeded by the digit to its left it is called an increasing number; for example, 134468.
>
> Similarly if no digit is exceeded by the digit to its right it is called a decreasing number; for example, 66420.
>
> We shall call a positive integer that is neither increasing nor decreasing a "bouncy" number; for example, 155349.
>
> As $$n$$ increases, the proportion of bouncy numbers below $$n$$ increases such that there are only 12951 numbers below one-million that are not bouncy and only 277032 non-bouncy numbers below $$10^{10}$$.
>
> How many numbers below a googol ($$10^{100}$$) are not bouncy?

<!--more-->

## Answer

With [Problem 112]({{ site.url }}{{ site.baseurl }}{% post_url 2019-02-11-Problem-112---Bouncy-numbers %}), we explicitly tested whether each number was bouncy. However, in this case, limit is absolutely massive, which means we must take a clever counting approach.

From the problem, a number will fall into 3 distinct groups (there are a couple of exceptions which I'll massive later): an increasing number, a decreasing number, or neither. The former two collectively refer to non-bouncy numbers. 

One thing you might realize is that the number of increasing numbers differs depending on **what digit the number starts with.** For example, there are 5 two-digit increasing numbers that start with 5 (55-59), but only 2 that start with 8 (88 and 89). It appears, at least in the two-digit case, that the number of increasing numbers starting with digit $$d$$ is $$10-d$$.

What happens when we move to the 3-digit case? Something interesting happens. Let's say our 3-digit number starts with **4**. Going off the two-digit case, this means the next digit can be anything between 4-9. However, doing this logic means **we are essentially finding all two-digit increasing numbers that start with 4-9**. The sum of these gives us how many three-digit increasing numbers there are that start with 4. The same logic can be used to see how many there are that start with each digit, using the results from the previous length.

## A quick example

In case it hasn't set in yet, I'll show a quick example of how we can calculate the number of 4-digit increasing numbers there are. In other words, anything less than $$10^5$$. We keep track of the number of increasing numbers per starting digit, except 0 because 1) numbers can't start with 0 and 2) an increasing number can't contain a 0.

- Our array looks like [1, 1, 1, 1, 1, 1, 1, 1, 1], because well, there's 1 number for each starting digit when you only have...one digit.
- For two digits, the array now looks like [9, 8, 7, 6, 5, 4, 3, 2, 1] i.e. there are 9 2-digit numbers that start with 1 (11-19), 8 that start with 2 (22-29), ...
- For three digits, the array looks like [45, 36, 28, 21, 15, 10, 6, 3, 1]. The first element is calculated by 9 + 8 + 7 + ... + 1 = 45, the second element is calculated by 8 + 7 + 6 + ...., because 3-digit increasing numbers that start with 2 can't have 1 as a second digit. You can see that is like a cumulative sum, where the left boundary is traveling to the right. In fact, this is exactly the method that will happen at each step.
- For four digits, the array looks like [165, 120, 84, 56, 35, 20, 10, 4, 1]. It should make sense that the right-most number is 1, since there's always only 1 increasing number that starts with 9, and that is 99...99.

This is extremely easy to code, and with `numpy` it can even be done in one line, with `np.cumsum`.

## Decreasing numbers

For decreasing numbers, the method is exactly the same, just that the cumulative sum travels in the other direction. Additionally, there is one more thing: We need to include 0 in our calculations. This is because it is possible for decreasing numbers to have zeroes in them i.e. 2100 is a decreasing number.

## Double-counting

So once we've calculated the increasing and decreasing numbers we add them up right? Well almost. Remember how I said there were exceptions to how each number only falls into one group? The exception is a number with **all repeated digits**. A number like 5555 is both increasing and decreasing. Thus we would've double-counted it in our calculations, so we need to subtract them out. Additionally, the decreasing number algorithm is also counting repeated zeroes as well, which is flat out not a number. Therefore, we subtract 10 off each time, one for the repeated zeroes, and 9 for the nine numbers with all repeated digits (111, 222, ..., 999).

## Code

The code is actually a simple loop, as `numpy` makes things concise. Notice I subtract 10 in each loop from our total.

```python
incrNums = np.ones(9, object)
decrNums = np.ones(10, object)  # Count 0 as a decreasing digit
# All one digit #s are not bouncy
total = 9
digitMax = 100

for digit in range(2, digitMax+1):
    # Calculate number of increasing
    # and decreasing numbers for the current
    # digit length
    incrNums = np.cumsum(incrNums)
    decrNums = np.cumsum(decrNums)
    # Add the total of each of these to the total.
    # However, we have to subtract 10, because one
    # we counted is 00...0, which isn't a number.
    # We also double-counted numbers that go like
    # 1...1, 22...2, ..., 99...9; there are nine of them.
    total += np.sum(incrNums) + np.sum(decrNums) - 10

print('There are', total, 'non-bouncy numbers below 10 ^', digitMax)
```

Running the loop gets us,

```
There are 51161058134250 non-bouncy numbers below 10 ^ 100
0.0030246999999999913 seconds.
```

Thus, there are **51161058134250** (that is 51 trillion....which pales in comparison to a googol) non-bouncy numbers below a googol. The main reason the resulting code was as simple as it was was that they provided a hard number of digits to count until. If they needed a specific number as an upper bound (like 562,828,495), things would've been a tad hairier.