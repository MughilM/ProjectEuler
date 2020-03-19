---
layout: post
title: "Problem 84 - Monopoly odds"
date: 2017-06-25, 08:44
number: 84
mathjax: true
---

## Question

In the game, *Monopoly*, the standard board is set up in the following way:

![monopolyImg]({{site.url}}{{site.baseurl}}\assets\Images\p084.png)

A player starts on the GO square and adds the scores on two 6-sided dice to determine the number of squares they advance in a clockwise direction. Without any further rules we would expect to visit each square with equal probability: 2.5%. However, landing on G2J (Go To Jail), CC (community chest), and CH (chance) changes this distribution.

In addition to G2J, and one card from each of CC and CH , that orders the player to go directly to jail, if a player rolls three consecutive doubles, they do not advance the result of their 3rd roll. Instead they proceed directly to jail.

At the beginning of the game,  the CC and CH cards are randomly shuffled. When a player lands on CC or CH they take a card from the top of the respective pile and, after following the instructions, it is returned to the bottom of the pile. There are sixteen cards in each pile, but for the purpose of this problem we are only concerned with cards that order a movement; any instruction not concerned with movement will be ignored and the player will remain on the CC/CH square.

- Community Chest (2/16 cards):
  - Advance to GO
  - Go to JAIL
- Chance (10/16 cards):
  - Advance to GO
  - Go to JAIL
  - Go to C1
  - Go to E3
  - Go to H2
  - Go to R1
  - Go to next R (railway company)
  - Go to next R
  - Go to next U (utility company)
  - Go back 3 squares

The heart of this problem concerns the likelihood of visiting a particular square. That is, the probability of finishing at that square after a roll. For this reason it should be clear that, with the exception of G2J for which the probability of finishing is zero, the CH squares will have the lowest probabilities, as 5/8 request a movement to another square, and it is the final square that the player finishes on that we are interested in. We shall make no distinction between "Just Visiting" and being to sent to JAIL, and we shall also ignore the rule about requiring a double to "get out of jail", assuming they pay to get out on their next turn.

By starting on GO and numbering the squares sequentially from 00 to 39 we can concatenate these two-digit numbers to produce strings that correspond with sets of squares.

Statistically it can be shown that the three most popular squares, in order, are JAIL (6.24%) = Square 10, E3 (3.18%) = Square 24, and GO (3.09%) = Square 00. So these three most popular squares can be listed with the six-digit modal string: 102400.

If, instead of using two 6-sided dice, two 4-sided dice are used, find the six-digit modal string.

## Answer

Quite frankly, this is one of those problems that is "tedious", as we are just programming the rules of the game. There isn't much to explain from me here, other than just showing the code. To calculate something like this, we can simply run a simulation for a very large number of turns, and see the long-term probability. Another way to do this would be to calculate conditional probabilities to land on a particular square, given that **were** on a square previously. Due to the large number of squares, it gets pretty messy, and so we'll stick with the first method. I've chosen to do 500 thousand rolls, but it's also possible it will converge earlier.

```python
start = time.perf_counter()

spaces = np.array(['GO', 'A1', 'CC1', 'A2', 'T1', 'R1', 'B1', 'CH1',
          'B2', 'B3', 'JAIL', 'C1', 'U1', 'C2', 'C3', 'R2',
          'D1', 'CC2', 'D2', 'D3', 'FP', 'E1', 'CH2', 'E2',
          'E3', 'R3', 'F1', 'F2', 'U2', 'F3', 'G2J', 'G1',
          'G2', 'CC3', 'G3', 'R4', 'CH3', 'H1', 'T2', 'H2'])

# Methods to find distance to nearest railroad and utility
# For chance and community chest, I'm not rotating the deck
# I'm simply taking a random but even then the distribution
# is still similar
def nearestRail(currSquare, spaces=spaces):
    # Get the railroad spaces
    RRspaces, = np.where((spaces == 'R1') |
                        (spaces == 'R2') |
                         (spaces == 'R3') |
                          (spaces == 'R4'))
    # Add the index of the square if it
    # wrapped around
    RRspaces = np.append(RRspaces, len(spaces) + np.min(RRspaces))
    # Subtract each square from current,
    # smallest positive is the closest
    dists = RRspaces - currSquare
    return np.min(dists[dists > 0])
def nearestUtility(currSquare, spaces=spaces):
    # Get the utility spaces
    Uspaces, = np.where((spaces == 'U1') |
                        (spaces == 'U2'))
    Uspaces = np.append(Uspaces, len(spaces) + np.min(Uspaces))
    dists = Uspaces - currSquare
    return np.min(dists[dists > 0])
def drawCC(currSquare, spaces=spaces):
    cardNum = np.random.randint(1, 17)
    if cardNum == 1: # Advance to GO
        return len(spaces) - currSquare
    elif cardNum == 2: # Go to JAIL
        jail = np.where(spaces == 'JAIL')[0][0]
        dist = jail - currSquare
        if dist > 0:
            return dist
        return len(spaces) + dist
    else: # Don't move from current
        return 0
def drawCH(currSquare, spaces=spaces):
    cardNum = np.random.randint(1, 17)
    dests = ['GO', 'JAIL', 'C1', 'E3', 'H2', 'R1',
             'RR', 'RR', 'U']
    if cardNum < 10:
        spaceToGo = dests[cardNum - 1]
        # Railroad or Utility
        if cardNum == 7 or cardNum == 8:
            return nearestRail(currSquare)
        elif cardNum == 9:
            return nearestUtility(currSquare)
        spaceNum = np.where(spaces == spaceToGo)[0][0]
        dist = spaceNum - currSquare
        if dist > 0:
            return dist
        return len(spaces) + dist
    elif cardNum == 10: # Go back 3 spaces
        return len(spaces) - 3
    else:
        return 0
counts = np.zeros(len(spaces))
diceSize = 4
currSpace = 0
doublesInARow = 0
rounds = 500000
for _ in range(rounds):
    roll1 = np.random.randint(1, diceSize+1)
    roll2 = np.random.randint(1, diceSize+1)
    if roll1 == roll2:
        doublesInARow += 1
        if doublesInARow == 3: # Go straight to JAIL
            currSpace = 10
            doublesInARow = 0
            continue
    else:
        doublesInARow = 0
    toMove = roll1 + roll2
    currSpace = (currSpace + toMove) % len(spaces)
    # Check if you're on Chance or Community Chest or Go to Jail
    if spaces[currSpace] == 'CH1' or spaces[currSpace] == 'CH2' or spaces[currSpace] == 'CH3':
        currSpace = (currSpace + drawCH(currSpace)) % len(spaces)
    elif spaces[currSpace] == 'CC1' or spaces[currSpace] == 'CC2' or spaces[currSpace] == 'CC3':
        currSpace = (currSpace + drawCC(currSpace)) % len(spaces)
    elif spaces[currSpace] == 'G2J': # JAIL = 10
        currSpace = 10
    # Add to a count
    counts[currSpace] += 1
# Get the 3 largest probabilities
first = np.argmax(counts)
# Set that to negative infinity
counts[first] = float('-inf')
second = np.argmax(counts)
counts[second] = float('-inf')
third = np.argmax(counts)

print(str(first).zfill(2) + str(second).zfill(2) + str(third).zfill(2))



end = time.perf_counter()
print(end - start, 'seconds.')
```

 Running this long code, we get as our modal string,

```
101524
31.810585500000002 seconds.
```

Thus, JAIL is still the most popular, but now Pennsylvania Railroad and Illinois Avenue come in 2nd and 3rd respectively.