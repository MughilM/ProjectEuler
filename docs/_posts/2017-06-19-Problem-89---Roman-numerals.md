---
layout: post
title: "Problem 89 - Roman numerals"
date: 2017-06-19 14:07
number: 89
mathjax: true
---

## Question

For a number written in Roman numerals to be considered valid there are basic rules which must be followed. Even though the rules allow some numbers to be expressed in more than one way there is always a "best" way of writing a particular number.

For example, it would appear that there are at least six ways of writing the number sixteen:

<pre style="text-align:left">
IIIIIIIIIIIIIIII
VIIIIIIIIIII
VVIIIIII
XIIIIII
VVVI
XVI
</pre>

However, according to the rules only `XIIIIII` and `XVI` are valid, and the last example is considered to be the most efficient, as it uses the least number of numerals.

The 11K text file, [roman.txt](https://projecteuler.net/project/resources/p089_roman.txt) (right click and 'Save Link/Target As...'), contains one thousand numbers written in valid, but not necessarily minimal, Roman numerals; see [About... Roman Numerals](https://projecteuler.net/about=roman_numerals) for the definitive rules for this problem.

Find the number of characters saved by writing each of these in their minimal form.

Note: You can assume that all the Roman numerals in the file contain no more than four consecutive identical units.

## Answer

Another problem which goes into the bucket of "easy-but-tedious", as we're converting rules into code. For conciseness, I'm not going to list all the rules, as there are a bunch of them (I suggest you look at the link). However, the rule boils down to: **From left to right, the numeral must NOT increase, UNLESS it is following the rule of subtraction.** 

The crux of this problem is writing functions that convert back and forth between roman numerals and numbers. Going from roman to numbers is straightforward; just look at each pair of numerals and add them to a running total. Make sure to take into account any subtraction rules and we're done. Going the other way to result in the minimal string is a bit more tricky, but can still be done. Looking at the rules, we can deduce that there are really **13 possible denominations**, including the basic ones AND the ones which follow subtraction rules. These are below:

- 1000 - M
- 900 - CM
- 500 - D
- 400 - CD
- 100 - C
- 90 - XC
- 50 - L
- 40 - XL
- 10 - X
- 9 - IX
- 5 - V
- 4 - IV
- 1 - I

When given a number, all we need to check is what is the biggest denomination that is less than or equal to our number. We add that roman string, and subtract off the value. We continue until our value is 0. Simple enough!

To recap, for each roman string in the file, we convert it to a number, then convert it back to a minimal string, and add the difference in length to a running total.

```python
def romanToNum(romanStr):
    romanVal = {'M':1000, 'D':500, 'C':100,
                'L':50, 'X':10, 'V':5, 'I':1}
    value = 0
    i = 0
    while i < len(romanStr):
        r = romanStr[i]
        if i < len(romanStr) - 1 and romanVal[r] < romanVal[romanStr[i+1]]:
            value += romanVal[romanStr[i+1]] - romanVal[r]
            i += 2
        else:
            value += romanVal[r]
            i += 1
    return value
def numToRoman(n):
    roman = ''
    # The values that can be encoded are
    # 1000, 900, 500, 400, 100, 90, 50, 40,
    # 10, 9, 5, 4, and 1 with one or two numerals
    # So count down
    values = [1000, 900, 500, 400,
              100, 90, 50, 40, 10,
              9, 5, 4, 1]
    codings = ['M', 'CM', 'D', 'CD',
               'C', 'XC', 'L', 'XL',
               'X', 'IX', 'V', 'IV', 'I']
    mappings = {value: code for value, code in zip(values, codings)}
    while n > 0:
        # Find the greatest denomination that
        # is less than n...
        i = 0
        while values[i] > n:
            i += 1
        val = values[i]
        # Get the mapping, and subtract it off...
        roman += mappings[val]
        n -= val
    return roman

lettersSaved = 0
with open('p089_roman.txt') as f:
    romans = f.read().splitlines()
for roman in romans:
    lettersSaved += len(roman) - len(numToRoman(romanToNum(roman)))

print(lettersSaved)
```

Running this fairly simple code gets us an output of,

```
743
0.022997900000000016 seconds.
```

Thus, we save **743** letters if we write each roman numeral in its minimal form. 