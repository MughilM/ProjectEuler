---
layout: post
title: "Problem 54 - Poker hands"
date: 2017-06-18, 15:18
mathjax: true
number: 54
---

## Question

In the card game poker, a hand consists of five cards and are ranked, from lowest to highest, in the following way:

- **High Card**: Highest value card.
- **One Pair**: Two cards of the same value.
- **Two Pairs**: Two different pairs.
- **Three of a Kind**: Three cards of the same value.
- **Straight**: All cards are consecutive values.
- **Flush**: All cards of the same suit.
- **Full House**: Three of a kind and a pair.
- **Four of a Kind**: Four cards of the same value.
- **Straight Flush**: All cards are consecutive values of the same suit.
- **Royal Flush**: Ten, Jack, Queen, King, Ace, in same suit.

The cards are valued in the order: 2, 3, 4, 5, 6, 7, 8, 9, 10, Jack, Queen, King, Ace.

If two players have the same ranked hands then the rank made up of the highest value wins; for example, a pair of eights beats a pair of fives (see example 1 below). But if two ranks tie, for example, both players have a pair of queens, then highest cards in each hand are compared (see example 4 below); if the highest cards tie then the next highest cards are compared, and so on.

Consider the following 5 hands dealt to two players:

| Hand  |                         Player 1                          |                          Player 2                          |  Winner  |
| :---: | :-------------------------------------------------------: | :--------------------------------------------------------: | :------: |
| **1** |             5H 5C 6S 7S KD<br />Pair of Fives             |             2C 3S 8S 8D TD<br />Pair of Eights             | Player 2 |
| **2** |           5D 8C 9S JS AC<br />Highest card Ace            |           2C 5C 7D 8S QH<br />Highest card Queen           | Player 1 |
| **3** |              2D 9C AS AH AC<br />Three Aces               |          3D 6D 7D TD QD<br />Flush with Diamonds           | Player 2 |
| **4** | 4D 6S 9H QH QC<br />Pair of Queens<br />Highest card Nine | 3D 6D 7H QD QS<br />Pair of Queens<br />Highest card Seven | Player 1 |
| **5** |   2H 2D 4C 4D 4S<br />Full House <br />with Three Fours   |   3C 3D 3S 9S 9D<br />Full House <br />with Three Threes   | Player 1 |

The file, [poker.txt](https://projecteuler.net/project/resources/p054_poker.txt), contains one-thousand random hands dealt to two players. Each line of the file contains ten cards (separated by a single space): the first five are Player 1's cards and the last five are Player 2's cards, You can assume that all hands are valid (no invalid characters or repeated cards), each player's hands are in no specific order, and in each hand there is a clear winner.

How many hands does Player 1 win?

## Answer

Okay, so there's a giant wall of text associated with this problem, but it is just explaining the rules of poker. Unfortunately, due to the specific ordering of the hands and the conditions for each one, there is no better way than simply coding up each piece of logic to check. The ordering of the logic is important, as we want to check the best hand first before checking the lower ones. The code mostly consists of parsing the given file, and then sorting each player's hand based on the card value. That, along with grabbing the counts of each card that show up will allow to check the hands such as four of a kind, full house, i.e. the ones that deal with numerical amounts of cards. 

Because there is a chance that the hand ranking will tie, we also need to keep track of the highest card in each ranking for each player. For this, I have a method which returns the ranking of the hand and the highest card in that ranking. Then, we can just run this method for each player, and whichever player has the higher ranking (or higher card) wins. If they're both exactly equal, then we need to keep removing the highest card from each player until we see a difference.

```python
import time
import numpy as np
import sys
start = time.clock()

# Returns the ranking of the hand and the
# highest card in that ranking
def pokerRanking(hand):
    order = '23456789TJQKA'
    # First split the hand into a paired list of sorts
    #hand = list(zip(*[tuple(card) for card in hand]))
    numbers = [card[0] for card in hand]
    suits = [card[1] for card in hand]
    # Go down the list
    # Royal Flush - T, J, Q, K, A all same suit
    if all(card in numbers for card in 'TJQKA') and all(suits[0] == s for s in suits):
        return 'A', 'RF'
    # Straight Flush - consecutive values
    sortedNums = sorted(numbers, key=lambda x: order.index(x))
    if (all(suits[0] == s for s in suits) and
            all(order.index(sortedNums[i+1]) - order.index(sortedNums[i]) == 1 for i in range(4))):
        return sortedNums[-1], 'SF'
    # Four of a kind - only need to look at values
    unique, counts = np.unique(numbers, return_counts=True)
    # Sort unique
    unique = unique[np.argsort(counts)[::-1]]
    counts = np.sort(counts)[::-1]
    if counts[0] == 4:
        return unique[0], 'FK'
    # Full House
    if counts[0] == 3 and counts[1] == 2:
        return unique[0], 'FH'
    # Flush - all same suit
    if all(suits[0] == s for s in suits):
        return max(numbers, key=lambda x: order.index(x)), 'F'
    # Straight
    if all(order.index(sortedNums[i+1]) - order.index(sortedNums[i]) == 1 for i in range(4)):
        return sortedNums[-1], 'S'
    # Three of a kind
    if counts[0] == 3:
        return unique[0], 'TK'
    # Two pairs
    if counts[0] == 2 and counts[1] == 2:
        return max(unique, key=lambda x: order.index(x)), 'TP'
    # One pair
    if counts[0] == 2:
        return unique[0], 'OP'
    # Highest card
    return max(numbers, key=lambda x: order.index(x)), 'H'


with open('p054_poker.txt') as f:
    pokerHands = np.array([[hands[:14].split(' '), hands[-14:].split(' ')] for hands in f.read().splitlines()])
# Codes for each card ranking.
rankings = ['H', 'OP', 'TP', 'TK', 'S', 'F', 'FH', 'FK', 'SF', 'RF']
order = '23456789TJQKA'
p1Wins = 0
for hand1, hand2 in pokerHands:
    hand1Res = pokerRanking(hand1)
    hand2Res = pokerRanking(hand2)
    if rankings.index(hand1Res[1]) > rankings.index(hand2Res[1]):
        p1Wins += 1
    if rankings.index(hand1Res[1]) == rankings.index(hand2Res[1]):
        if order.index(hand1Res[0]) > order.index(hand2Res[0]):
            p1Wins += 1
        elif order.index(hand1Res[0]) == order.index(hand2Res[0]):
            # Keep removing the highest card from both sides until
            # there's a winner
            numbers1 = [card[0] for card in hand1]
            numbers2 = [card[0] for card in hand2]
            while max(numbers1, key=lambda x: order.index(x)) == \
                    max(numbers2, key=lambda x: order.index(x)):
                toRem = max(numbers1, key=lambda x: order.index(x))
                numbers1.remove(toRem)
                numbers2.remove(toRem)
            if (max(numbers1, key=lambda x: order.index(x)) >
                max(numbers2, key=lambda x: order.index(x))):
                p1Wins += 1

print(p1Wins)

end = time.clock()
print(end - start, 'seconds.')
```

Running this large code results in,

```
376
0.15291450749056495 seconds.
```

Personally, I didn't like this problem too much as some of the others beforehand, as it was just equating game rules to code. No mathematical genius required :) In any case, though, it wasn't too difficult.