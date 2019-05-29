---
layout: post
title: "Problem 43 - Sub-string divisibility"
date: 2016-05-20 10:33
mathjax: true
number: 43
---

## Question

The number, 1406357289, is a 0 to 9 pandigital number because it is made up of each of the digits 0 to 9 in some order, but it also has a rather interesting sub-string divisibility property.

Let $$d_1$$ be the 1st digit, $$d_2$$ be the 2nd digit, and so on. In this way, we note the following:

- $$d_2d_3d_4=406$$ is divisible by 2
- $$d_3d_4d_5=063$$ is divisible by 3
- $$d_4d_5d_6=635$$ is divisible by 5
- $$d_5d_6d_7=357$$ is divisible by 7
- $$d_6d_7d_8=572$$ is divisible by 11
- $$d_7d_8d_9=728$$ is divisible by 13
- $$d_8d_9d_{10}=289$$ is divisible by 17

Find the sum of all 0 to 9 pandigital numbers with this property.

## Answer

There are $$10!=3\,628\,800$$ 0 to 9 pandigital numbers. We need to find the quickest way to check each number to see whether it satisfies the conditions. The order in which we check is also important, as some conditions are more restrictive than others.

We first note that the number can't start with a 0, because then it is not considered a 10 digit number anymore. Additionally, since $$d_4d_5d_6$$ has to be divisible 5, that forces $$d_6$$ to either be 0 or 5. By the same reason, $$d_4$$ has to be an even digit. 

After these three conditions are met, we can start checking divisibility. Since a number being divisible by 17 is much less likely than a number being divisible by 3, we check the divisibility rules on the biggest factor on down. 

For looping through all pandigital numbers, we can utilize the `permutations` method of the `itertools` package. In the end, it is a rather simple-looking loop, with all of the conditions listed above.

```python
s = 0
for perm in permutations('0123456789'):
    if perm[0] != '0' and \
            (perm[5] == '0' or perm[5] == '5') and \
            perm[3] in '02468':
        perm = ''.join(perm)
        if int(perm[7:]) % 17 == 0 and \
                int(perm[6:9]) % 13 == 0 and \
                int(perm[5:8]) % 11 == 0 and \
                int(perm[4:7]) % 7 == 0 and \
                int(perm[2:5]) % 3 == 0:
            print(perm)
            s += int(perm)

print('-----------')
print(s)
```

The output of the above is,

```python
1406357289
1430952867
1460357289
4106357289
4130952867
4160357289
-----------
16695334890
1.2334722367077846 seconds.
```

We see that there are only 6 numbers that satisfy the properties, and the sum of them is **16695334890**.