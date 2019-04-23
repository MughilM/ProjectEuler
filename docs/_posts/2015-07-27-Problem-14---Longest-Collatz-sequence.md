---
layout: post
title: "Problem 14 - Longest Collatz sequence"
date: 2015-07-27 11:07
mathjax: true
number: 14
---

## Question

The following iterative sequence is defined for the set of positive integers:


$$
n = \begin{cases}
	\frac{n}{2},\quad &n\text{ is even}
	\\
	3n + 1, \quad &n\text{ is odd}
\end{cases}
$$


Using the rule above and starting with 13, we generate the following sequence


$$
13 \rightarrow 40 \rightarrow 20 \rightarrow 10 \rightarrow 5 \rightarrow 16 \rightarrow 8 \rightarrow 4 \rightarrow 2 \rightarrow 1
$$


It can be seen that this sequence (starting at 13 and finishing at 1) contains 10 terms. Although it has not been proved yet (Collatz Problem), it is thought that all starting numbers finish at 1.

Which starting number, under one million, produces the longest chain?

**NOTE**: Once the chain starts, the terms are allowed to go over one million.

## Answer

So, it is unfeasible to go through every number and wait until we get to 1, especially if the longest sequence is unreasonably long. We can be a bit smarter about it. For example, in the above example sequence (starting at 13), we can place 26 at the start of the sequence, and have it still be valid (half of 26 is 13). Since we know the sequence starting at 13 has 10 terms, the sequence starting at 26 must have **11** terms.

In terms of code, we can keep a dictionary whose keys are the starting numbers, and whose values are the lengths of the sequence. By definition, the sequence starting at has 1 term (technically, it's a cycle going 1, 4, 2, 1, 4, 2, ...). The reason that I'm using a dictionary is because I do not know the maximum number that might be reached during a sequence. It might unreasonably high, and having a simple list that many elements long will surely take up much more space than a dictionary at intermediate values. We can use `defaultdict` from the `collections` package to initialize our values to 0 automatically.

```python
limit = 1000000
seqLengths = defaultdict(int)
# Starting at 1 has 1 term
seqLengths[1] = 1
# Go through each starting number until
# the limit.
for i in range(2, limit):
    # If the number is 0, then we
    # haven't calculated it yet.
    if seqLengths[i] == 0:
        # Apply the rule
        if i % 2 == 0:
            j = i // 2
        else:
            j = 3 * i + 1
        # Keep a list of numbers visited.
        numVisited = [i, j]
        # While we haven't reached a number
        # that has been calculated...
        while seqLengths[j] == 0:
            # Repeatedly apply rule...
            if j % 2 == 0:
                j //= 2
            else:
                j = 3 * j + 1
            numVisited.append(j)
        # Once we've reached a value that
        # has been calculated, set all the
        # numbers we've visited with the right
        # value
        val = seqLengths[j]
        # Reverse the visited list,
        # and set all of them, adding 1
        # as we go.
        numVisited = numVisited[::-1]
        for num in numVisited:
            seqLengths[num] = val
            val += 1

# We're done calculating,
# now find the max value.
print(max([(k, v) for k, v in seqLengths.items()], key=lambda x: x[1]))
```

Running the above results in,

```python
(837799, 525)
3.123749135802469 seconds.
```

Therefore, the number with the longest sequence is **837799**, with a grand total of 525 elements.