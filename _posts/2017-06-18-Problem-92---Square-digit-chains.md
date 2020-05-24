---
layout: post
title: "Problem 92 - Square digit chains"
date: 2017-06-18 10:18
mathjax: true
number: 92
---

## Question

A number chain is created by continuously adding the square of the digits in a number to form a new number until it has been seen before.

For example,


$$
44\rightarrow 32\rightarrow 13\rightarrow 10\rightarrow\mathbf{1}\rightarrow\mathbf{1}
\\
85\rightarrow\mathbf{89}\rightarrow145\rightarrow 42\rightarrow20 \rightarrow4\rightarrow16\rightarrow37\rightarrow58\rightarrow\mathbf{89}
$$


Therefore any chain that arrives at 1 or 89 will become stuck in an endless loop. What is most amazing is that EVERY starting number will eventually arrive at 1 or 89.

How many starting numbers below ten million will arrive at 89?

## Answer

### First try

From the reading of the problem, this seems like a classic dynamic programming problem. We keep updating a list of numbers that end in 89 or 1. For each **new number we haven't seen**, we calculate the chain. As soon as we encounter a number that we have seen, we immediately set **all numbers in the chain** to either 89 or 1.

On the surface, this seems like an efficient solution. In fact, I thought so, and so I coded it up. However, I was wrong. When I ran it for the limit of ten million, I barely managed to make it under one minute, the recommended limit.

### Second try

So, clearly, there should be a much faster solution. When enhancements can we make? For one thing, **the largest sum of squares possible results** from having seven 9s (9,999,999), and is $$7\times9^2=567$$, or seven 9s. So, in fact, we can do the same algorithm, **but only up to 567**. For all numbers above that, we only calculate the sum of squares of the digits **just once**, and simply look up the eventual ending.

This second algorithm managed to shave off about 18-20 seconds off the initial algorithm, but it was still over half a minute. Can we do even better?

### Third (and final) try

The key observation to make is that **the order of the digits do not matter**. For example, the numbers 5332, 5323, 5233, 3532, 3352, 3325, 3235, 3253, 3523, 2335, 2353, and 2533 all compute to a digit squared sum of $$25+9+9+4=\mathbf{47}$$. Can we take advantage of this?

Indeed we can :) To go through all of our numbers, without actually visiting each number, I'll first show an example where we are searching up to 10000 (so each number is a maximum of **4** digits).

#### Example

Our search space will eventually consist of numbers of the form $$abcd$$, where the digits are in **nonincreasing order.** In other words, $$a\geq b\geq c\geq d$$. Our number 5332 from above would be part of this set, **but none of its permutations**, and that's what we want. 

Once we get this set (details to come), are we done? No! Remember 5332 had **11 other permutations** we had to worry about, which **all end at 89** and need to be accounted for. So, for each number in our set, we have to calculate the **number of distinct permutations it has.** It is not too complicated to calculate, as the only thing we have to watch out for is repeated digits. 

If all our digits are different, then there are exactly $$4! = 24$$ ways of ordering them. However, there are only 12 permutations of 5332, because there are 2 3s! We have to divide by the number of ways these two 3s can be arranged, which is just 2, and so $$24/2 = 12$$. In general, if $$c_d$$ is the number of times the digit $$d$$ appears in the number, then **the number of permutations of our 4-digit number is**


$$
Perm(abcd) = \frac{24}{c_0!c_1!\dots c_9!} = 
	\frac{24}{\prod_{d=0}^9 c_d!}
$$


**What about leading 0s?** If our original number was 8400, then the above formula also counts "numbers" such as 0480 and 0084. But this actually works in our favor because we are counting numbers with less than 4 digits! So no external correction is needed here.

### Final Result

Once we have our number set, and the number of times it happens, then all we need to do is calculate the chain for each number, making sure we are saving as we go. If we encounter an 89 chain, then we add the number of permutations to a running total.

To generate the number set, I use `yield` and create a generator. That way, the entire list isn't stored in memory. The actual algorithm is a recursive one, which keeps track of the remaining length of the number. If the length left is 0, then we return the number. Otherwise, we append each possible digit, recurse with each of them, and decrement the length by 1. For the initial loop, I do not include 0, since we can't have leading zeros.

Notice the three functions that calculate the sum of the squares of the digits, loop through all possible numbers, and count the number of permutations. A quick way to get each digit, is to get the remainder when the number is divided by 10. To go to the next digit, we integer divide by 10, which throws away the last digit.

```python
def sumOfSquareOfDigits(num):
    s = 0
    while num != 0:
        s += (num % 10) ** 2
        num = num // 10
    return s


def loopNumbers(length, currNum=None):
    if length == 0:
        yield currNum
        return
    # Top level
    if currNum is None:
        # Don't start with a 0
        for n in range(1, 10):
            for number in loopNumbers(length - 1, n):
                yield number
    # Any other level...
    else:
        for n in range(currNum % 10 + 1):  # <-- Zero allowed
            for number in loopNumbers(length - 1, currNum * 10 + n):
                yield number

def countPermutations(n):
    nstr = str(n)
    digits = '0123456789'
    return math.factorial(len(nstr)) // \
        np.prod([math.factorial(nstr.count(digit)) for digit in digits])


is89 = []
is1 = []
total89 = 0
for number in loopNumbers(7):
    count = countPermutations(number)
    i = number
    startingSquare = sumOfSquareOfDigits(i)
    # Check to see if it's in the 89 list...
    if startingSquare in is89:
        total89 += count
    elif startingSquare in is1:
        continue
    else:
        while i != 89 and i != 1:
            i = sumOfSquareOfDigits(i)
        if i == 89:
            is89.append(startingSquare)
            total89 += count
        else:
            is1.append(startingSquare)
print(total89)
```

Running this long code gets us an output of,

```
8581146
0.2859901000000001 seconds.
```

Thus, we have **8581146** numbers below 10 million that end in 89, in almost a quarter of a second! Much better than the near-minute times I was getting earlier. 