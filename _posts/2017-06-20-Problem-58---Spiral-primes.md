---
layout: post
title: "Problem 58 - Spiral primes"
date: 2017-06-20 09:10
mathjax: true
number: 58
---

## Question

Starting with 1 and spiral anticlockwise in the following way, a square spiral with side length 7 is formed.

<pre style="text-align:center">
<span style="color:red">37</span> 36 35 34 33 32 <span style="color:red">31</span>
38 <span style="color:red">17</span> 16 15 14 <span style="color:red">13</span> 30
39 18  <span style="color:red">5</span>  4  <span style="color:red">3</span> 12 29
40 19  6  1  2 11 28
41 20  <span style="color:red">7</span>  8  9 10 27
42 21 22 23 24 25 26
<span style="color:red">43</span> 44 45 46 47 48 49
</pre>

It is interesting to note that the odd squares lie along the bottom right diagonal, but what is more interesting is that 8 out of the 13 numbers lying along both diagonals are prime; that is, a ratio of 8/13 ~ 62%.

If one complete new layer is wrapped around the spiral above, a square spiral with side length 9 will be formed. If this process is continued, what is the side length of the square spiral for which the ratio of primes along both diagonals first falls below 10%?

## Answer

So, first note that due to the construction of the spiral, a side length can only be odd. Furthermore, to get from one corner number to the other, you have to add one less than the length of the side length you are on. For example, from 13 to 17, you need to add 4, and the side length is 5. 31 to 37 you add 6 while the side length is 7. We know the side length has grown by 2 when we reach an odd square. 

Therefore, our methodology is to keep generating primes from one odd square to the next, calculate the 4 corner numbers, and see how many are primes. We keep track of the total number of diagonal numbers and the total number primes on the diagonals. Once this ratio reaches below 10%, we break out and print the side length.

```python
# Side lengths are always
# odd. Keep track of
# primes on diagonals...
sideLen = 7
primesOnDiags = 8
totalDiags = 13
while primesOnDiags / totalDiags >= 0.1:
    # Put one complete layer.
    diagonalNums = [sideLen ** 2 + (sideLen + 1) * i for i in range(1, 5)]
    # Generate primes in this layer...
    primesInLayer = primesieve.primes(sideLen ** 2, (sideLen + 2) ** 2)
    # Go through each diagonal number, and see if it's
    # in the prime set...increment the primesOnDiags if it is...
    primesOnDiags += sum(diag in primesInLayer for diag in diagonalNums)
    # Number of diagonal numbers increases by 4
    totalDiags += 4
    # Side length increases by 2...
    sideLen += 2

print(sideLen)
```

The output is,

```
26241
3.9868424743338373 seconds.
```

Therefore, a side length of **26241** is needed before the ratio falls below 10%.