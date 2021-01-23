---
layout: post
title: "Problem 205 - Dice Game"
date: 2017-08-25 01:29
number: 205
mathjax: true
---

> Peter has nine four-sided (pyramidal) dice, each with faces numbered 1, 2, 3, 4. Colin has six six-sided (cubic) dice, each with faces numbered 1, 2, 3, 4, 5, 6.
>
> Peter and Colin roll the dice and compare totals: the highest total wins. The result is a draw if the totals are equal.
>
> What is the probability that Pyramidal Peter beats Cubic Colin? Give your answer rounded to seven decimal places in the form 0.abcdefg

<!--more-->

## Answer

Compared to the other problems in this range, this is relatively simple. So simple, in fact, that we can directly brute force it. The totals range from 9 to 36 for Peter and 6 to 36 for Colin. For each possible total, we can compute the probability of getting that total. Then we loop through each pair of totals for Peter and Colin. If Peter's score is higher than Colin's, then this is a win for Peter. The probability of this happening is the product of the probabilities of obtaining both totals. Because this is exclusive to any other pair of totals, we add this product to a running total. The sum of all probability products is our answer.

### Implementation

We first need to map each total to the probability of getting that total. To calculate probabilities of totals we do not know beforehand, we use the `defaultdict` structure from the `collections` package. This dictionary structure sets a default value for all uninitialized values. In our case, these need to be 0.0. We create one for Peter and one for Colin. Once these are created, we just run a double for-loop on each possible pair of totals and calculate and update probabilities as described before.

```python
pyrProbs = defaultdict(float)
cubeProbs = defaultdict(float)
# There are 9 pyramids. Each configuration has
# probability 1 / (4 ^ 9).
numberOfDice = 9
prob = 1 / (4 ** numberOfDice)
for config in product(np.arange(1, 5), repeat=numberOfDice):
    total = sum(config)
    pyrProbs[total] += prob
# Do the same for the cubes
numberOfDice = 6
prob = 1 / (6 ** numberOfDice)
for config in product(np.arange(1, 7), repeat=numberOfDice):
    total = sum(config)
    cubeProbs[total] += prob

winProb = 0
# Go through each value in the pyramid and the cube and add those
# which win
for pyrSum, pyrProb in pyrProbs.items():
    for cubeSum, cubeProb in cubeProbs.items():
        if pyrSum > cubeSum:
            winProb += pyrProb * cubeProb

print(f'Peter beats Colin with a probability of {winProb:0.7f}.')
```

Running this relatively simple code, we get an output of

```
Peter beats Colin with a probability of 0.5731441.
0.4863471 seconds.
```

Therefore, the probability that Peter beats Colin is **0.5731441**.