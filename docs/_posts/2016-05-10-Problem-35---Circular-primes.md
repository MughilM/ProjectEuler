---
layout: post
title: "Problem 35 - Circular primes"
date: 2016-05-10 11:34
mathjax: true
number: 35
---

## Question

The number, 197, is called a circular prime because all rotations of the digits: 197, 971, 719, are themselves prime.

There are thirteen such primes below 100: 2, 3, 5, 7, 11, 13, 17, 31, 37, 71, 73, 79, and 97.

How many circular primes are there below one million?

## Answer

We will obviously need to use our `primesieve` package to generate primes from 100 to one million. Additionally, we will need a function which takes in an integer, and generates its circular numbers. The methodology can go like this: Convert the integer to a list of digits, copy the first digit into some external variable, then shift the rest of digits to the left, and finally set the last digit to our copied first digit. In Python, this process is extremely simple. Below is the function:

```python
# Gets all the values of x that
# are obtained from rotating the
# digits, not including x.
def circulate(x):
    x = list(str(x))
    numOfCircles = len(x) - 1
    pList = []
    for _ in range(numOfCircles):
        # Copy the first digit.
        firstDigit = x[0]
        # Set all digits except
        # the last to the remaining
        x[:-1] = x[1:]
        # Set the last digit to the first
        # digit saved earlier
        x[-1] = firstDigit
        # Add to list
        pList.append(int(''.join(x)))
    return pList
```

Notice that the returned list doesn't contain $$x$$ itself. Additionally, the list is actually the digits converted to characters, which has no bearing on the methodology, other than converting back to an integer. In fact, with a few tweaks, this can be used to rotate any `iterable`.

Anyways, the next issue we must tackle is how we can reduce the number of primes we need to check. After all, there are over 78 thousand primes below 1,000,000. There are two things we can observe:

- If we find a set of circular primes, each prime's last digit is actually each digit of one of the primes. For this reason, our candidate circular prime **should not contain any even digits**. For example, 23 is not a circular prime, because its rotation 32, ends in an even number, and therefore cannot be prime. The number 2 is an exception, but since the first prime we check is 101, we are fine.
- If we find a set of circular primes, we can remove the entire set from the primes list, as we don't need to check them anymore.

Implementing these two things can drastically reduce the number of primes we need to check. In fact, just implementing the first bullet point reduces the size from ~78k to only 3k! Below is the loop to solve this problem, along with the function to check if a number has only odd digits.

```python
def containsAllOdd(x):
    return all([int(d) % 2 for d in str(x)])

limit = 1000000
# Generate primes between 100 and 1000000
primes = primesieve.primes(100, limit)
# Filter out all primes that have even digits.
primes = [p for p in primes if containsAllOdd(p)]
count = 13  # 13 primes that follow the property below 100.
for prime in primes:
    # Circulate, and see if each one
    # is in the list. If they are, then
    # save all of them and then
    # delete them, as we don't need to check them again.
    circlePrimes = circulate(prime)
    if all([p in primes for p in circlePrimes]):
        count += len(circlePrimes) + 1 # Plus 1 for current.
        # Delete.
        primes.remove(prime)
        for p in circlePrimes:
            primes.remove(p)
            
print(count)
```

The output after running everything is,

```python
55
0.8581394861900083 seconds.
```

Thus, there are **55** circular primes below one million, all this in less than a second. If you are curious what they are, see below :)

- 2​
- 3
- 5
- 7
- 11
- 13, 31
- 17, 71
- 37, 73
- 79, 97
- 113, 131, 311
- 197, 971, 719
- 337, 373, 733
- 919, 199, 991
- 1193, 1931, 9311, 3119
- 3779, 7793, 7937, 9377
- 11939, 19391, 93911, 39119, 91193
- 19937, 99371, 93719, 37199, 71993
- 193939, 939391, 393919, 939193, 391939, 919393
- 199933, 999331, 993319, 933199, 331999, 319993