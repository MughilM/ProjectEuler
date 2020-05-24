---
layout: post
title: "Problem 17 - Number letter counts"
date: 2015-08-07 20:46
mathjax: true
number: 17
---

## Question

If the numbers 1 to 5 are written out in words: one, two, three, four, five, then there are 3 + 3 + 5 + 4 + 4 = 19 letters used in total.

If all the numbers from 1 to 1000 (one thousand) inclusive were written out in words, how many letters would be used?

**NOTE**: Do not count spaces or hyphens. For example, 342 (three hundred and forty-two) contains 23 letters and 115 (one hundred and fifteen) contains 20 letters. The use of "and" when writing out numbers is in compliance with British usage.

## Answer

A slightly different problem that doesn't relate to math computation directly, but instead language. We need to break up this problem into cases, as all numbers from 1-19 follows no pattern. The tens also follow their own pattern. However, if the number is big enough, we can extract the hundreds place digit and recurse downwards. We can integer divide and do modular arithmetic to extract the digit and whatever is left over, respectively. The following recursive would return the number of letters for the numbers we care about (< 1000).

```python
def letter_count(n):
    ones = ['one', 'two', 'three', 'four', 'five',
              'six', 'seven', 'eight', 'nine']
    teens = ['ten', 'eleven', 'twelve', 'thirteen', 'fourteen', 'fifteen',
             'sixteen', 'seventeen', 'eighteen', 'nineteen']
    tens = [None, 'twenty', 'thirty', 'forty', 'fifty',
            'sixty', 'seventy', 'eighty', 'ninety']
    # Ultimate base case is if
    # our number is 0. This means we've
    # exhausted the digits
    if n == 0:
        return 0
    # Base case is if our number
    # is less than 20. There is a separate
    # word for each number from 1-19
    if n < 20:
        if n < 10:
            return len(ones[n - 1])
        else:
            return len(teens[n - 10])
    # If we're between 20 and 100,
    # then we need to extract the tens and
    # the ones
    elif 20 <= n < 100:
        return len(tens[n // 10 - 1]) + letter_count(n % 10)
    # If we're at least one hundred, then extract
    # the hundred's place and call the count again.
    else:
        total = len(ones[n // 100 - 1]) + len('hundred')
        # If we're a multiple of hundred, then we
        # don't need the "and".
        # Otherwise, add 3 to the length for "and" and
        # recurse.
        if n % 100 != 0:
            return total + 3 + letter_count(n % 100)
    return total
```

Now, we can iterate through each number, and add the letters for "one thousand" at the end.

```python
total = 0
for n in range(1, 1000):
    total += letter_count(n)
total += len('one') + len('thousand')
print(total)
```

Running the above gives an output of,

```
21124
0.002460049382716049 seconds.
```

Thus, **21124** is our answer.