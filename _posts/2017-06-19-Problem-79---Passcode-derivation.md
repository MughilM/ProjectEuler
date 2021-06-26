---
layout: post
title: "Problem 79 - Passcode derivation"
date: 2017-06-19 11:19
mathjax: true
number: 79
---

## Question

A common security method used for online banking is to ask the user for three random characters from a passcode. For example, if the passcode was 531278, they may ask for the 2nd, 3rd, and 5th characters; the expected reply would be: 317.

The text file, [keylog.txt](https://projecteuler.net/project/resources/p079_keylog.txt), contains fifty successful login attempts.

Given that the three characters are always asked for in order, analyze the file so as to determine the shortest possible secret passcode of unknown length.

## Answer

A different sort of problem this time with the password breaking. In hindsight, I felt that this problem could actually be solved by hand, by looking at the login attempts. One thing that might jump out to you is that if there's a 7 in the login attempt, it's *always* at the beginning. Similarly, if there's a 0 in the login, it's *always* at the end. These two facts immediately imply that the passcode starts and ends with a 7 and a 0 respectively.

With these observations as to what numbers appear before and after others, you could easily decode what the passcode should be. However, I still wanted to see if I can code this up.

Some assumptions. Since we are looking for the shortest possible keycode, and no digit appears twice within each login attempt, each digit appears only once in the full passcode. Additionally, there should be no 4s or 5s in the passcode, since no login attempt had one.

In terms of how we store which digits before and after each digit, we can keep a dictionary, which maps each digit to a 2-tuple of lists. The first holds the possible digits that can come before, and the second holds those that can come after.

In order to build the full passcode, we see which digit has **no** possible digits that come before it. After we've selected it, we remove that number from all other instances. We then repeat this process. The string we have in the end is the full passcode.

```python
# Set a bunch of rules, like digit x must come before y.
rules = {str(digit): (set(), set()) for digit in range(10) if digit != 4 and digit != 5}

with open('./p079_keylog.txt', 'r') as f:
    keylogs = f.read().splitlines()

for log in keylogs:
    for i in range(len(log)):
        if i == 0:
            rules[log[i]][1].add(log[i+1])
        elif i == len(log) - 1:
            rules[log[i]][0].add(log[i-1])
        else:
            rules[log[i]][0].add(log[i-1])
            rules[log[i]][1].add(log[i+1])
# Now we see which digit has nothing
# come before it. Once we've found it,
# we remove that from all other lists,
# and perform the search again...
password = ''
while len(password) < 8:
    nextDigit = None
    for key, values in rules.items():
        if len(values[0]) == 0:
            nextDigit = key
            break
    password += nextDigit
    del rules[nextDigit]
    # Remove it from the rest...
    for key, values in rules.items():
        if nextDigit in values[0]:
            values[0].remove(nextDigit)

print('The password is', password)
```

Running the above code results in,

```
The password is 73162890
0.0008719999999999839 seconds.
```

Thus, our full password is **73162890**. Notice there are no 4s or 5s in this passcode.