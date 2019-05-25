---
layout: post
title: "Problem 37 - Truncatable primes"
date: 2016-05-10 16:39
mathjax: true
number: 37
---

## Question

The number 3797 has an interesting property. Being prime itself, it is possible to continuously remove digits from left to right, and remain prime at each stage: 3797, 797, 97, and 7. Similarly we can work from right to left: 3797, 379, 37, 3.

Find the sum of the only eleven primes that are both truncatable from left to right and right to left.

NOTE: 2, 3, 5, and 7 are not considered to be truncatable primes.

## Answer

For terminology, I'll call a prime where you can chop off digits from left to right a **left truncatable prime**. Similarly, being able to chop off digits right to left will be called a **right truncatable prime**.

One way to do this is to generate primes which satisfy one of the two properties, and then check to see whether it satisfies the other property. Left truncatable primes are easier to generate, while right truncatable primes are simpler to check for validity.

Now, when you chop off digits left to right, the single digit you will end up at is the ones digit in the original number. This **must** be prime, otherwise the prime will never be left truncatable. The one-digit prime numbers are 2, 3, 5, and 7. However, any number ending in 2 (other than 2 itself), isn't prime. Therefore, all **left truncatable primes will end in 3, 5, or 7**.

We can do a similar analysis for right-truncatable primes. Chopping digits from right to left will mean the final digit you check will be the digit all the way on the left. This number is only allowed to be 2, 3, 5, or 7 because those are our one-digit primes. However, because we are adding digits to the left of numbers each time, we have to watch out for numbers which have non-prime digits in the middle of the number.

For example, the number **313** is one of the eleven described, but notice that 1 isn't prime. But since, it's in the middle of the number, the number will never right-truncate to 1.

So....do we have to add all digits 1-9 to see if we get a right truncatable prime? Fortunately, there are some numbers we can eliminate. A number containing a **4, 6, or 8** can't be right truncatable, because at some point, our candidate prime will end in that number, demoting it from prime status.

So we finally have our methodology. Until we find 11 primes:

- Start with the primes 3, 5, 7.
- Each time, add all numbers except for 0, 4, 6, and 8 to the **left** of each number to make a new list.
- Remove any numbers which aren't prime from the list. The new list now only has numbers which are left truncatable.
- Test each left truncatable prime to see if it's right truncatable. If it is, mark it down and move on.
- Repeat steps 2 through 4, each time adding digits to the left until all 11 primes are found.

For our prime list, I generate a sufficiently large set of primes that I use to check. Additionally, I've made a generator to give me the number each time it's been right truncated, so we can test these on the fly.

```python
def rightTrunkNums(x):
    while x > 10:
        x //= 10
        yield x


primes = primesieve.primes(1000000)
# Keep adding digits to the right
# of a number which is left to right
# truncatable. We check these primes
# to see if they are left to right
# truncatable. Don't add 4, 6, 8
# because then it won't be truncatable
# the other direction
count = 0
# Our starting set are the one digit
# primes...
primeSet = [3, 5, 7]
# The numbers we can add to the left...
addingNumbers = [1, 2, 3, 5, 7, 9]
# While we haven't found all of them.
# We can deal with pure numbers, because
# each time, the length of the numbers
# goes up by one.
s = 0
tenPower = 1
while count < 11:
    # Add all of the possible digits to each
    # number in our list.
    primeSet = [[p + digit * (10 ** tenPower) for digit in addingNumbers]
                    for p in primeSet]
    # Flatten it.
    primeSet = [p for digitSet in primeSet for p in digitSet]
    # Go through, remove each number that isn't prime.
    primeSet = [p for p in primeSet if p in primes]
    # We are left with left truncatable primes by definition.
    # Now go through each prime and check if it's right
    # truncatable
    for p in primeSet:
        for num in rightTrunkNums(p):
            if num not in primes:
                break
        # Check if num is less than 10 and a prime...
        if num < 10 and num in primes:
            # We found one...
            print(p)
            count += 1
            s += p
        # else:
        #     primeSet.remove(p)
    # Increase power of ten...
    tenPower += 1

print()
print(s)
```

Running the code mass above results in an output of,

```python
23
53
73
37
313
373
317
797
3137
3797
739397

748317
0.4748998125831603 seconds.
```

Therefore, the 11 primes we are looking for are 23, 53, 73, 37, 313, 373, 317, 797, 3137, 3797, and 739397, and consequently, their sum is **748317**.