---
layout: post
title: "Problem 68 - Magic 5-gon ring"
date: 2019-08-13 17:40
number: 68
mathjax: true
---

## Question

Consider the following "magic" 3-gon ring, filled with numbers 1 to 6, and each line adding to nine.

![p068_1]({{site.url}}{{site.baseurl}}\images\PE_Imgs\p068_1.png)

Working **clockwise**, and starting from the group of three with the numerically lowest external node (4,3,2 in this example), each solution can be described uniquely. For example, the above solution can be described by the set: 4,3,2; 6,2,1; 5,1,3.

It is possible to complete the ring with four different totals: 9, 10, 11, and 12. There are eight solutions in total.

| Total | Solution Set        |
| ----- | ------------------- |
| 9     | 4,2,3; 5,3,1; 6,1,2 |
| 9     | 4,3,2; 6,2,1; 5,1,3 |
| 10    | 2,3,5; 4,5,1; 6,1,3 |
| 10    | 2,5,3; 6,3,1; 4,1,5 |
| 11    | 1,4,6; 3,6,2; 5,2,4 |
| 11    | 1,6,4; 5,4,2; 3,2,6 |
| 12    | 1,5,6; 2,6,4; 3,4,5 |
| 12    | 1,6,5; 3,5,4; 2,4,6 |

By concatenating each group it is possible to form 9-digit strings; the maximum string for a 3-gon ring is 432621513.

Using the numbers 1 to 10, and depending on arrangements, it is possible to form 16- and 17-digit strings. What is the maximum **16-digit** string for a "magic" 5-gon ring?

![p068_2]({{site.url}}{{site.baseurl}}\images\PE_Imgs\p068_2.png)

## Answer

From the way how the strings are formed, we can deduce that **the number 10 has to be on the outer ring**. Why? Well, the string is going to concatenate 15 numbers, 3 for each of the 5 arms. Numbers on the inner ring will be repeated twice, since they are part of two separate arms. Thus, if "10" is on the outer ring, then it will be present *once* in the string, which means the length of the string will 14 (one digit for 14 circles), plus 2 for the single "10", for a grand total of 16 characters.

So we know that "10" is on the outer ring. Now, we need a way to quickly go through all possible starting configurations of the ring, without falling to brute force. Note that we also shouldn't take into account two rings that differ by a rotation, as these are the same ring. 

We can loop through all sets of 5 unique numbers, that contain "10", to place in the outer ring. Remember that the ring strings are reported starting with the smallest number. We can then actually have this fact enforce our ordering of the number placements.

Once we placed 5 numbers on the outer ring, we can fill in the first arm using 2 numbers. This sets in stone the sum that must be fulfilled with the other 4 arms. Thus, using the remaining available numbers, we can test numbers to see if a solution is possible.

To recap, our methodology:

- Generate all distinct sorted sets of 5 distinct numbers that contain the number 10. These are placed on the outer ring.
- For each set, place 2 of the remaining 5 numbers onto the arm with the smallest number. This sets the required sum.
- See if it's possible to satisfy the sum on the other 4 arms with the 3 remaining numbers. If it is possible, output the configuration. Otherwise, move on to the next set of 5.

For technalities, I put the numbers of each arm as a row in a table, so I can quickly calculate the sums. We use `itertools.permutations` to loop through the groups of 5 numbers.

```python
def filterOut(toRemove, origList):
    return [ele for ele in origList if ele not in toRemove]

# Okay, so basically, we have to
# loop through all possible locations
# for numbers in the outer circle and
# pair of numbers for the first petal.
maxNum = 10
numList = list(range(1, maxNum + 1))
c = 0
allPerms = []
for i in range(1, maxNum + 1):
    for perm in permutations(list(range(i + 1, maxNum + 1)), maxNum // 2 - 1):
        if 10 in perm:
            allPerms.append((i,) + perm)
            c += 1
for perm in allPerms:
    # Get numbers that are remaining
    copied = filterOut(perm, numList)
    # print(perm, '==> ', end='')
    # All possible pairs of numbers that can be placed
    # into the 2 remaining circles.
    for pair in permutations(copied, 2):
        # print(pair, ' ', end='')
        # Make a small 2d array of the sums...
        ring = np.zeros((maxNum // 2, 3), dtype=int)
        ring[:, 0] = perm
        # Assign the pair...
        ring[0, 1:] = pair
        # The second number in the pair is also the
        # second number in the second leg...
        ring[1, 1] = pair[1]
        # The first number in the pair is also
        # the third number in the last leg
        ring[-1, 2] = pair[0]
        # Calculate the required sum
        sumToMeet = sum(ring[0])
        # Filter out the pair...
        innerNumsLeft = filterOut(pair, copied)
        # Okay, we have our sum and starting
        # numbers. Now we can keep putting
        # required numbers until it's impossible
        # to put anymore. We keep removing from the list.
        # If we finish the loop, and the list is empty,
        # then we've placed everything..
        i = 1  # Current sum row.
        while len(innerNumsLeft) > 0 and i < maxNum // 2 - 1:
            # Calculate number that should go here...
            requiredNum = sumToMeet - sum(ring[i])
            # If the required number is not in the list,
            # then it's impossible, and we break and go
            # to the next setup.
            if requiredNum not in innerNumsLeft:
                break
            # If this is the last number
            # placed, we also need to check
            # the other leg that has been filled.

            # Otherwise remove it, add it to
            # the ring, and go to the next leg.
            ring[i, 2] = requiredNum
            ring[i + 1, 1] = requiredNum
            innerNumsLeft.remove(requiredNum)
            i += 1
        # Check to see if we placed all the
        # numbers, and that the last leg is
        # the correct sum
        if len(innerNumsLeft) == 0 and sum(ring[-1]) == sumToMeet:
            print(''.join(map(str, np.ravel(ring))))
```

Running this outputs all 16-digit configurations:

```
2594936378711015
2951051817673439
6357528249411013
6531031914842725
0.8801705000000002 seconds.
```

Therefore, we can see that the maximum 16-digit string is **6531031914842725**.