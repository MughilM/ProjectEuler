---
layout: post
title: "Problem 41 - Pandigital prime"
date: 2016-05-18 10:26
mathjax: true
number: 41
---

## Question

We shall say that an $$n$$-digit number is pandigital if it makes use of all the digits 1 to $$n$$ exactly once. For example, 2143 is a 4-digit pandigital and is also prime.

What is the largest $$n$$-digit pandigital prime that exists?

## Answer

So the crux of this problem is reducing the number of pandigital numbers or the number of primes we need to check, because there is a vast number of primes less than $$10^9$$.

Can we put a restriction on $$n$$? Well, remember first that an $$n$$-digit pandigital number has all the numbers from 1 to $$n$$. Additionally, recall that if you add the digits of a number and their sum is divisible by 3 or 9, then the number itself is divisible by 3 or 9. Now, If $$n=9$$, then it has 1,2,...,9. If you add these digits together, we get 45. However, since 45 is divisible by 9, every 9-digit pandigital number is divisible by 9! So a pandigital prime can't be 9 digits.

We can do the same for an 8-digit pandigital number. The sum of the digits is 1 + 2 + 3 + ... + 8 = 36. Hence, all 8-digit pandigital numbers are also divisible by 9, and thus can't be prime.

For 7-digit and 6-digit pandigital numbers however, the sums are 28 and 21 respectively, and thus we can't make any conclusions. However, the sum of the digits of all 5-digit pandigital numbers are 15, and so those are all divisible by 3. Therefore, we will generate all primes between 100000 and 7654321 (since that is the largest 7-digit pandigital number), and subsequently check each prime from the largest on down. The first prime we encounter that is pandigital will be our answer. Technically, I probably should also generate all 4-digit primes, but I was betting on the fact that the largest prime is either 6 or 7 digits long.

```python
# Can't be 8 or 9 digits, so check 6 and 7 digit numbers.
primes = primesieve.primes(100000, 7654321)[::-1]
# Find the first prime (going from largest to smallest)
# that is pandigital.
sevenDigitPan = set('1234567')
sixDigitPan = set('123456')
for p in primes:
    if set(str(p)) == sevenDigitPan or set(str(p)) == sixDigitPan:
        print(p)
        break
```

Running the code results in an output of,

```
7652413
0.048801859630159886 seconds.
```

Therefore, the largest pandigital prime is **7652413**.