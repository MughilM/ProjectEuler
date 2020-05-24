---
layout: post
title: "Problem 74 - Digit factorial chains"
date: 2019-06-01 17:31
number: 74
mathjax: true
---

## Question

The number 145 is well known for the property that the sum of the factorial of its digits is equal to 145:


$$
1! + 4! + 5! = 1 + 24 + 120 = 145
$$


Perhaps less well known is 169, in that it produces the longest chain of numbers that link back to 169; it turns out that there are only three such loops that exist:


$$
\begin{aligned}
	& 169 \rightarrow 363601 \rightarrow 1454 \rightarrow 169
	\\
	& 871 \rightarrow 45361 \rightarrow 871
	\\
	& 872 \rightarrow 45362 \rightarrow 872
\end{aligned}
$$


It is not difficult to prove that EVERY starting number will eventually get stuck in a loop. For example,


$$
\begin{aligned}
	& 69 \rightarrow 363600 \rightarrow 1454 \rightarrow 169 \rightarrow 363601\,(\rightarrow 1454)
	\\
	& 78 \rightarrow 45360 \rightarrow 871 \rightarrow 45361\,(\rightarrow 871)
	\\
	& 540 \rightarrow 145\,(\rightarrow 145)
\end{aligned}
$$


Starting with 69 produces a chain of five non-repeating terms, but the longest non-repeating chain with a starting number below one million is sixty terms.

How many chains, with a starting number below one million, contain exactly sixty non-repeating terms?

## Answer

First, before we do a loop, we need to figure out how to calculate the sum of the factorials of a number's digits. A direct way in Python is to convert the number to a string, and go through each digit (now a character) and casting back to integer. However, repeated string and integer casting will slow things down, so it would be nice if we can work with purely integers.

Notice that the ones digit in a number $$n$$ is the remainder when $$n$$ is divided by 10 i.e. $$n\mod 10$$. This is how we can obtain the ones digit. We can also completely remove if we do $$\lfloor n/10 \rfloor$$, as the ones digit will end up as a decimal. With these two steps, we can keep reducing $$n$$, calculating the factorials and adding them onto a running sum. The written function is below. I use the `factorial` function from the `math` package.

```python
def factorialDigitSum(n):
    sum = 0
    while n >= 10:
        sum += math.factorial(n % 10)
        n //= 10
    sum += math.factorial(n)
    return sum
```

Now, how can we efficiently calculate the length of chains? Say that we've calculated the chain length of a number $$a$$ and we are currently calculating the chain of $$b$$. If we encounter $$a$$ during the chain of $$b$$, then we can stop, and simply add on the chain length of $$a$$ to that of $$b$$! Additionally, we can also do it to numbers that we encountered before $$a$$. This drastically reduces the number of computations we need to do.

However, which numbers do we need to store? We'll obviously need to store at least the chain lengths of numbers up to one million, but chains of numbers less than one million have a good chance of containing terms greater than the limit. To combat this, Python has a data structure called `defaultdict` in the `collections` package. It allows us to set a default value for all keys in a standard dictionary. In our case, it will be 0, as we are storing the chain length.

We can also store the initial loop chain lengths to prevent any other numbers from falling into a loop. **There is actually one more number that equals itself when taking the sum of the factorials of its digits**. That number is **40585**. I encountered this when I first ran my program and noticed it got stuck at this number. Putting together everything we've covered:

```python
chainLengths = defaultdict(int)
# 1! = 1, 2! = 2, 145! ==> 145
# 40585! ==> 40585
# set these to 1
chainLengths[1] = 1
chainLengths[2] = 1
chainLengths[145] = 1
chainLengths[40585] = 1
# 871, 45361, 872, 45362
# have lengths of 2
for num in [871, 872, 45361, 45362]:
    chainLengths[num] = 2
# 169, 363601, 1454 have length 3
for num in [169, 363601, 1454]:
    chainLengths[num] = 3

# Calculate length
# of chains for everything less
# than 1 million
limit = 1000000
for n in range(3, limit):
    # See if it's been calculated
    if chainLengths[n] > 0:
        continue
    # Calculate until we've reached a
    # number that has been calculated...
    num = n
    length = 1
    visited = [num]
    while chainLengths[num] == 0:
        num = factorialDigitSum(num)
        visited.append(num)
        length += 1
    # Now set the visited numbers...
    # l is an offset.
    for l, k in enumerate(visited[::-1]):
        chainLengths[k] = l + chainLengths[num]

# Count how many numbers have chain length 60
countOfLength60 = 0
for num, length in chainLengths.items():
    if num < limit and length == 60:
        countOfLength60 += 1

print(countOfLength60)
```

A 0 means the chain hasn't been calculated yet. Running the code above results in,

```
402
3.9272377 seconds.
```

Thus, there are **402** terms with exactly sixty non-repeating terms in their chain. To confirm my suspicions about how many numbers we need to store, if we check how many keys are in the `chainLengths` dictionary, we see that there are 1000208, so we ended up storing an extra 208 numbers above our limit.