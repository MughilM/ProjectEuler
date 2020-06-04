---
layout: post
title: "Problem 121 - Disc game prize fund"
date: 2017-07-06 10:16
mathjax: true
number: 121
---

> A bag contains one red disc and one blue disc. In a game of chance a player takes a disc at random and its colour is noted. After each turn the disc is returned to the bag, an extra red disc is added, and another disc is taken at random.
>
> The player pays £1 to play and wins if they have taken more blue discs than red discs at the end of the game.
>
> If the game is played for four turns, the probability of a player winning is exactly 11/120, and so the maximum prize fund the banker should allocate for winning in this game would be £10 before they would expect to incur a loss. Note that any payout will be a whole number of pounds and also includes the original £1 paid to play the game, so in the example given the player actually wins £9.
>
> Find the maximum prize fund that should be allocated to a single game in which fifteen turns are played.

<!--more-->

## Answer

To reiterate the key takeaway, in case it isn't obvious, is that **the probability of drawing a blue disc changes each turn**. Specifically, it decreases since you add a red disc each time.

To find the maximum prize fund, we need to find the probability of winning game, or the probability of drawing 8 or more blue discs in the 15 turns. If the probability stayed constant each turn, then is basically a binomial distribution with a constant probability of "success" (i.e. drawing a blue disc). To review, if the probability of drawing a blue disc was $$p$$ at each turn, then the probability of drawing 8 blue discs out of 15 tries is


$$
P(8\text{ blue discs}) = \binom{15}{8}p^8(1-p)^7
$$


The binomial coefficient arises because you can draw the 8 discs in different ways i.e. drawing them in your first 8 tries vs. drawing them in your last 8 tries (and everything in between). Since each turn is independent, we multiply $$p$$ 8 times, and $$1-p$$ (probability of drawing a red disc) 7 times.

However, in this problem, $$p$$ is different depending on the turn. Thus, drawing a blue disc in your first 8 turns have a different probability than drawing them in the last 8 turns. This means we have to explicitly loop through each of the $$\binom{15}{8}$$ possibilities and calculate the probability of drawing 8 blue discs **in that specific order**. Because each progression is mutually exclusive, we can simply add them in the end.

Once we're done and we have the probability of winning game, we need to find the greatest amount of money that can be allocated to be still seen as a win from the casino's eyes, or a loser from the player's eyes. Since the player pays £1 to play, the max amount $$M$$ such that $$MP<1 \Rightarrow M < \frac{1}{P}$$, where $$P$$ is the probability of winning the game.

## Code

To loop through each blue disc drawing progression, we can use `itertools.combinations`. Given a list and a parameter $$k$$, in returns each group of choosing $$k$$ items from the list. In our problem, we can use the list to represent turns 1-15, while $$k$$ would be how many blue discs we draw. Each configuration will tell us *when* we draw the blue discs. Then, by using the rule of adding one red disc each turn, we can calculate the probability of drawing a blue disc **on that specific turn**. Then, calculating the max amount to be charged is a simple division.

```python
from itertools import combinations

def kDiscsFromN(n, k):
    for blues in combinations(range(n), k):
        order = ['R'] * n
        for blue in blues:
            order[blue] = 'B'
        yield ''.join(order)

N = 15
bluesToWin = N // 2 + 1
# Calculate the win probability
winProb = 0
for blues in range(bluesToWin, N + 1):
    for progression in kDiscsFromN(N, blues):
        progProb = 1
        numOfDiscs = 2
        for disc in progression:
            if disc == 'B':
                progProb *= (1 / numOfDiscs)
            else:
                progProb *= ((numOfDiscs - 1) / numOfDiscs)
            numOfDiscs += 1
        winProb += progProb

print('Win Probability:', winProb)
print('Max win amount possible before loss:', int(1 / winProb))
```

Running this code gives us,

```
Win Probability: 0.00044063946502124476
Max win amount possible before loss: 2269
0.146565 seconds.
```

Thus, the maximum amount that can be allocated for a 15-turn game is **£2269,** which is a pretty expensive game.