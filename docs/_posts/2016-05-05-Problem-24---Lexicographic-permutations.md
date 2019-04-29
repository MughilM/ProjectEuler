---
layout: post
title: "Problem 24 - Lexicographic permutations"
date: 2016-05-05 11:41
mathjax: true
number: 24
---

## Question

A permutation is an ordered arrangement of objects. For example, 3124 is one possible permutation of the digits 1, 2, 3, and 4. If all the permutations are listed numerically or alphabetically, we call it lexicographic order. The lexicographic permutations of 0, 1 and 2 are:

<p align="center">
    012	021	102	120	201	210
</p>

What is the millionth lexicographic permutation of the digits 0, 1, 2, 3, 4, 5, 6, 7, 8 and 9?

## Answer

As there are $$10! = 3\,628\,800$$, it is clearly unfeasible to try and find all permutations and sort them all. The first permutation in the list is simply all the numbers in ascending order: 0123456789. We need to way to quickly find the next permutation that comes lexicographically after this one. Fortunately, this is an extremely common problem and many people have written about it. I will be using the algorithm used in this [link](https://www.nayuki.io/page/next-lexicographical-permutation-algorithm).

The gist of the algorithm is that we find the first element from the right that does not follow non-increasing order. Then, we swap this element with the least element in the non-increasing part of the list. Finally, we reverse the non-increasing part. That's it!

It's relatively simple to implement the 3 steps outlined above. Additionally, using Python's generator structures, we can iterate through and generate the permutations on the fly. Below is the function to find the next permutation. I also use a small helper function to find the index of the first element in the index which does not follow non-increasing order from the *right*.

```python
# Returns index of first element
# from RIGHT which isn't in
# non-increasing order.
def indexOfNonIncreasing(list_):
    j = len(list_) - 1
    while j > 0 and list_[j] <= list_[j-1]:
        j -= 1
    # If i is 0, then the whole list is
    # in non-increasing order.
    return j - 1

def genPermutations(charaSet):
    # While we haven't gotten to the
    # last permutation
    index = indexOfNonIncreasing(charaSet)
    while index != -1:
        # Find the least element greater
        # than what's at index...
        leastEle = index + 1
        while leastEle < len(charaSet) and charaSet[leastEle] > charaSet[index]:
            leastEle += 1
        # At this point we'll be one over, so subtract one.
        leastEle -= 1
        # Swap the numbers at the two locations
        temp = charaSet[index]
        charaSet[index] = charaSet[leastEle]
        charaSet[leastEle] = temp
        # Reverse the portion of non-increasingness
        charaSet[index + 1:] = charaSet[index + 1:][::-1]
        # Yield it.
        yield charaSet
        # Find the next index
        index = indexOfNonIncreasing(charaSet)
```

Now, we can run a loop through the permutations until we reach the one millionth one. However, there is a slight change. A generator runs once before it even enters the for loop, so in fact we need to find when our iterator variable is $$999\,999$$, as below.

```python
digits = list(range(10))
for i, perm in enumerate(genPermutations(digits), 1):
    # The generator runs once before it enters the loop,
    # so we need the permutation at 999999
    if i == 999999:
        print(''.join(map(str, perm)))
        break
```

After running, the output is,

```python
2783915460
1.479740270925111 seconds.
```

Thus, **2783915460** is the one millionth permutation.

### Bonus!

It's actually possible to solve this problem using a basic calculator to find each digit from left to right. 

Notice that with the 10 digits we have on hand, there are exactly $$9! = 362\,880$$ permutations that start with each digit. Additionally, since we know that the permutations need to be in lexicographic order, the first $$362\,880$$ permutations will start with $$0$$, while the next $$362\,880$$ will start with $$1$$, and so on. Because $$3\times362\,880>1\,000\,000$$, the one millionth iteration starts with $$2$$. Additionally, the first permutation that starts with $$2$$ is $$2013456789$$, the $$725\,761^{\text{th}}$$ one. 

We can do the same analysis for the second digit. There are $$1\,000\,000-725\,760 = 274\,240$$ more permutations left. This time, there are $$8! = 40\,320$$ permutations that start with each of the digits, except for $$2$$, since we already used that. $$\lceil274\,240 / 40\,320\rceil = 7$$, so the seventh digit in our list, or $$7$$ will be the second digit in the one millionth digit.

So, in each step, we divide the remaining permutations to go by the number of ways to arrange the remaining digits, and take the ceiling to find which digit to use. In code, we do integer division i.e. the floor function, because we are dealing with 0-indexing. In the same manner, we decrement the number of permutations left by 1 before we divide. See below:

```python
digits = list('0123456789')
permNum = 1000000
perm = ''
for i in range(1, 11):
    ways = math.factorial(10 - i)
    # It's permNum - 1 because integer
    # division is 0-index like.
    # If ways is 2, then the 4th permutation should
    # still be the 2nd digit, not the 3rd digit.
    loc = (permNum - 1) // ways
    digit = digits[loc]
    # Remove the element at loc.
    del digits[loc]
    perm += digit
    # Subtract
    permNum -= loc * ways

print(perm)
```

Running the above results in an output of,

```python
2783915460
7.94073132950361e-05 seconds.
```

Notice the time is much faster, almost instantaneously.