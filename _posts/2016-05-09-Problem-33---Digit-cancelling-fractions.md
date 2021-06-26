---
layout: post
title: "Problem 33 - Digit cancelling fractions"
date: 2016-05-09 17:17
mathjax: true
number: 33
---

## Question

The fraction $$\frac{49}{98}$$ is a curious fraction, as an inexperienced mathematician in attempting to simplify it may incorrectly believe that $$\frac{49}{98} = \frac{4}{8}$$, which is correct, is obtained by cancelling the 9s.

We shall consider fractions like, $$\frac{30}{50} = \frac{3}{5}$$, to be trivial examples.

There are exactly four non-trivial examples of this type of fraction, less than one in value, and containing two digits in the numerator and denominator.

If the product of these four fractions is given in its lowest common terms, find the value of the denominator.

## Answer

This problem is practically begging for us to use a double for loop and look through all fractions. However, there are some enhancements we can make to reduce the number of fractions we look through. Let's say the numerator of our test fraction is 14. In that case, there is no reason to test the denominator of 56, as there are no numbers to cancel! Therefore, for each numerator, we can generate all cancellable denominators, and then see if the property holds. For example, for 14, if we wanted to cancel the 1, we would test 15, 16, 17, 18, 19, 21, 31, 41, 51, 61, 71, 81, and 91, as these are all the numbers greater than 14 with a 1 in them.

If $$d$$ is the digit we want to cancel, then to generate all 2-digit numbers with $$d$$ in them, we can take all the numbers between $$10d+1$$ and $$10d+9$$ inclusive (for $$d$$ in the tens place), and the numbers $$d, 10+d,20+d,\dots,90+d$$ (for $$d$$ in the ones place). Afterwards, we filter out all numbers that are not bigger than our original denominator. Note that the number $$11d$$ is generated twice, so we have to remove one instance. Below is the function I use to generate the denominators necessary.

```python
def getSameDigitNums(initNum, digit):
    # Get all tens digit numbers,
    # excluding multiple of 10
    nums = list(range(digit * 10 + 1, (digit + 1) * 10))
    # Now append all numbers with ones digit.
    nums.extend(list(range(digit, 100 + digit, 10)))
    # Filter out numbers that are less than or equal
    # to digit and remove the double instance of digit * 11
    nums = [n for n in nums if n > initNum]
    if digit * 11 in nums:
        nums.remove(digit * 11)
    return nums
```

Okay, now we have that, we can loop through all of our numerators, and for each of the two digits, grab all valid denominators and start testing. Since we want the result in lowest terms, we also need a GCD function for the end.

```python
def gcd(a, b):
    #the euclid algorithm
    while a:
        a, b = b % a, a
    return b

numProd = 1
denomProd = 1

for a in range(11, 100):
    # Skip if a is a multiple of 10.
    if a % 10 != 0:
        for keep, delete in [(0, 1), (1, 0)]:
            aNew = str(a)[keep]
            deletedDigit = str(a)[delete]
            denominators = getSameDigitNums(a, int(deletedDigit))
            # Now go through each number, remove the digit and see
            # what happens
            for b in denominators:
                b = str(b)
                # See which digit was cancelled.
                if b[0] == deletedDigit:
                    bNew = int(b[1])
                else:
                    bNew = int(b[0])
                # Now see if the old fraction
                # is the same as the new fraction
                if a / int(b) == int(aNew) / bNew:
                    print(a, '/', b)
                    numProd *= a
                    denomProd *= int(b)
# Reduce the multiplied frcation
GCD = gcd(numProd, denomProd)
print('---------')
print(numProd // GCD, '/', denomProd // GCD)
```

Running the above results in an output of,

```
16 / 64
19 / 95
26 / 65
49 / 98
---------
1 / 100
0.0025031140777648326 seconds.
```

Therefore, the four fractions that follow this special property are $$\frac{16}{64},\frac{19}{95},\frac{26}{65}$$, and $$\frac{49}{98}$$. Multiplying these together  results is $$\frac{1}{100}$$ and so the denominator is **100**.