---
layout: post
title: "Problem 22 - Names scores"
date: 2015-08-14 04:04
mathjax: true
number: 22
---

## Question

Using [names.txt](https://projecteuler.net/project/resources/p022_names.txt) (right click and 'Save Link/Target As...'), a 46k text file containing over five-thousand first names, begin by sorting it into alphabetical order. Then working out the alphabetical value for each name, multiply this value by its alphabetical position in the list to obtain a name score.

For example, when the list is sorted into alphabetical order, COLIN, which is worth 3 + 15 + 12 + 9 + 14 = 53, is the 938th name in the list. So, COLIN would obtain a score of 938 x 53 = 49714.

What is the total of all the name scores in the file?

## Answer

The problem itself tells us exactly what to do. The process is simple enough that there is no need for anything overly fancy. In Python, there is convenient function called `ord()`, which returns the ASCII integer code for the given character. This works perfectly when we need to convert each letter to its numerical counterpart. Additionally, since the character 'A' has a value of 65, we'll need to subtract 64 from the value of `ord()`, so that 'A' properly maps to 1. Below is the simple code to solve the problem:

```python
# Each name is in double quotes, separated
# by commas. So remove the quotes and split on
# the comma.
with open("problem022.txt") as f:
    names = f.readline().replace('"', '').split(',')
names.sort() # sort alphabetically

total = 0

for i, name in enumerate(names, 1):
    score = 0
    # For each letter, grab the ASCII code integer,
    # and subtract 64, because 'A' is 64.
    for chara in name:
        score += ord(chara) - 64
    score *= i + 1
    total += score
print(total)
```

Running the above gives an output of,

```python
871522818
0.011386455640743932 seconds.
```

Thus, **871522818** is our answer.