---
layout: post
title: "Problem 61 - Cyclical figurate numbers"
date: 2019-06-01 14:26
mathjax: true
number: 61
---

> Triangle, square, pentagonal, hexagonal, heptagonal, and octagonal numbers are all figurate (polygonal) numbers and are generated by the following formulae:
>
> | Type       | Formula                        | Series                |
> | ---------- | ------------------------------ | --------------------- |
> | Triangle   | $$P_{3,n}=\frac{1}{2}n(n+1)$$  | 1, 3, 6, 10, 15, ...  |
> | Square     | $$P_{4,n}=n^2$$                | 1, 4, 9, 16, 25, ...  |
> | Pentagonal | $$P_{5,n}=\frac{1}{2}n(3n-1)$$ | 1, 5, 12, 22, 35, ... |
> | Hexagonal  | $$P_{6,n}=n(2n-1)$$            | 1, 6, 15, 28, 45, ... |
> | Heptagonal | $$P_{7,n}=\frac{1}{2}n(5n-3)$$ | 1, 7, 18, 34, 55, ... |
> | Octagonal  | $$P_{8,n}=n(3n-2)$$            | 1, 8, 21, 40, 65      |
>
> The ordered set of three 4-digit numbers: 8128, 2882, 8281, has three interesting properties.
>
> 1. The set is cyclic, in that the last two digits of each number is the first two digits of the next number (including the last number with first).
> 2. Each polygonal type: triangle ($$P_{3,127}=8128$$), square ($$P_{4,91}=8281$$), and pentagonal ($$P_{5,44}=2882$$), is represented by a different number in the set.
> 3. This is the only set of 4-digit numbers with this property.
>
> Find the sum of the only ordered set of six cyclic 4-digit numbers for which each polygonal type: triangle, square, pentagonal, hexagonal, heptagonal, and octagonal, is represented by a different number in the set.
>

<!--more-->

It is way too inefficient to check every possible set of six of numbers, as there are 6 different types. However, what we can do is create a sort of "tree" of possible paths. For instance, suppose we pick the smallest 4-digit octagonal number $$P_{8,19}=1045$$. If we assume the **next number in the chain** is heptagonal, then the **only** possible value is $$P_{7,43}=4558$$. However, no hexagonal, pentagonal, or square numbers have these last two digits. Only $$P_{3,108}=5886$$ can follow. In this way, we can search the remaining sets for numbers that start with "86". Unfortunately, through eliminating possibilities, you find at that you can add up to two more numbers before the chain breaks and you have to start over again from a different octagonal number.

This continuous eliminating of possibilities is what allows to quickly see if a chain is possible given a starting number. Because this acts like a tree search almost, it lends itself nicely to a **recursive** approach. We can create a list of each type of polygonal numbers, which gets passed into the recursive function. We also have a list of the current chain going as well. Every time we choose a number from a list, we remove it so it doesn't get chosen from again.

We can make one key optimization. If a number has a 0 in the third position, then it's impossible to form a chain, because that would entail the next number start with a 0.

1. **Base case**: If we have zero choices to make for the next number AND our chain length is 6, then we have found a chain, so return it! Otherwise, this chain is invalid so cut off the tree and stop looking.
2. If our chain length is 0, then simply loop through all starting numbers and start the chain up.
3. **Recursive case**: For each polygonal set, grab all numbers that can be tacked on to our ongoing chain. If it's the **last** number, then also look at the first two digits of the first number in the chain list. **Recurse**, with the addition of each valid number, removing the list the number came from in the recursive call.

See the recursive function `findCyclicSets` below. Additionally, I also have a function `genPolyNums` which allows me to generate each set of polygonal numbers.

```python
# Function to generate polygonal
# numbers up to octogonal, given
# bounds.
def genPolyNums(degree, low, high):
    # Generate the lambda function...
    if degree == 3:
        f = lambda x: x * (x + 1) // 2
    elif degree == 4:
        f = lambda x: x ** 2
    elif degree == 5:
        f = lambda x: x * (3 * x - 1) // 2
    elif degree == 6:
        f = lambda x: x * (2 * x - 1)
    elif degree == 7:
        f = lambda x: x * (5 * x - 3) // 2
    else:
        f = lambda x: x * (3 * x - 2)
    # Increment n until our polygonal
    # number is bigger than 'low',
    # then loop until it's bigger than
    # 'high'.
    n = 1
    while f(n) <= low:
        n += 1
    nums = []
    while f(n) < high:
        nums.append(f(n))
        n += 1
    return nums

# Recursive function to generate
# cyclic chains using exactly
# one number from a list of sets.
def findCyclicSets(numSets, currChain, chainLength):
    # Base case is when we
    # have no more numbers to choose from.
    # If our chain isn't long enough, then
    # it's failed branch. Otherwise, we return the
    # chain.
    if len(numSets) == 0:
        if len(currChain) == chainLength:
            return currChain
        else:
            return
    # Recursive cases.
    # If our currChain is length 0,
    # start it up by using each number from
    # the first set.
    if len(currChain) == 0:
        for num in numSets[0]:
            # Skip numbers that have a 0 in
            # the 3rd position
            if num % 100 < 10:
                continue
            result = findCyclicSets(numSets[1:], [num], chainLength)
            if result is not None:
                return result
    # Otherwise, take the last number
    # in the chain, take each set, and
    # recurse with the number that forms
    # a chain. If it's going to be the last
    # number in the chain, then we have to check the
    # first number in the chain as well.
    lastTwoDigits = currChain[-1] % 100
    for i in range(len(numSets)):
        numSet = numSets[i]
        # Filter out numbers that form chain.
        validNums = [n for n in numSet if (n // 100) == lastTwoDigits]
        # Filter out numbers that form chain for
        # the first number IF it's the last number.
        if len(currChain) == chainLength - 1:
            firstNumFirstTwo = currChain[0] // 100
            validNums = [n for n in validNums if firstNumFirstTwo == n % 100]
        # Recurse using each valid number, removing
        # the set to prevent future selections from the
        # set.
        for valid in validNums:
            # Don't bother checking ones that
            # have a zero as the second to last digit.
            if valid % 100 < 10:
                continue
            # Recurse, making sure to not include this set.
            result = findCyclicSets(numSets[:i] + numSets[i+1:], currChain + [valid], chainLength)
            if result is not None:
                return result

```

At this point, we can generate the polygonal numbers and call the recursive function given the starting conditions of an empty list and chain length of 0.

```python
# Generate triangle through octogonal
# numbers...
polyNums = [genPolyNums(i, 1000, 10000) for i in range(8, 2, -1)]
cyclicSet = findCyclicSets(polyNums, [], 6)
print(cyclicSet)
print(sum(cyclicSet))
```

Running the function results in an output of,

```
[1281, 8128, 2882, 8256, 5625, 2512]
28684
0.0008549132425034104 seconds.
```

Therefore, the chain goes 1281, 8128, 2882, 8256, 5625, and 2512. The sum of these numbers is **28684**.