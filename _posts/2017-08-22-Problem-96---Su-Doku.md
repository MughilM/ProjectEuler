---
layout: post
title: "Problem 96 - Su Doku"
date: 2017-08-22 05:03
mathjax: true
number: 96
---

## Question

Su Doku (Japanese meaning *number place*) is the name given to a popular puzzle concept. Its origin is unclear, but credit must be attributed to Leonhard Euler who invented a similar, and much more difficult, puzzle idea called Latin Squares. The objective of Su Doku puzzles, however, is to replace the blanks (or zeros) in a 9 by 9 grid in such that each row, column, and 3 by 3 box contains each of the digits 1 to 9. Below is an example of a typical starting puzzle grid and its solution grid.

<div style="text-align:center;">
<table border="0" cellpadding="0" cellspacing="0" align="center"><tbody><tr><td>
<table cellpadding="5" cellspacing="0" border="1"><tbody><tr><td style="font-family:&#39;courier new&#39;;font-size:14pt;">0 0 3<br>9 0 0<br>0 0 1</td>
<td style="font-family:&#39;courier new&#39;;font-size:14pt;">0 2 0<br>3 0 5<br>8 0 6</td>
<td style="font-family:&#39;courier new&#39;;font-size:14pt;">6 0 0<br>0 0 1<br>4 0 0</td>
</tr><tr><td style="font-family:&#39;courier new&#39;;font-size:14pt;">0 0 8<br>7 0 0<br>0 0 6</td>
<td style="font-family:&#39;courier new&#39;;font-size:14pt;">1 0 2<br>0 0 0<br>7 0 8</td>
<td style="font-family:&#39;courier new&#39;;font-size:14pt;">9 0 0<br>0 0 8<br>2 0 0</td>
</tr><tr><td style="font-family:&#39;courier new&#39;;font-size:14pt;">0 0 2<br>8 0 0<br>0 0 5</td>
<td style="font-family:&#39;courier new&#39;;font-size:14pt;">6 0 9<br>2 0 3<br>0 1 0</td>
<td style="font-family:&#39;courier new&#39;;font-size:14pt;">5 0 0<br>0 0 9<br>3 0 0</td>
</tr></tbody></table></td>
<td width="50"><img src="./Problem 96 - Project Euler_files/spacer.gif" width="50" height="1" alt=""><br></td>
<td>
<table cellpadding="5" cellspacing="0" border="1"><tbody><tr><td style="font-family:&#39;courier new&#39;;font-size:14pt;">4 8 3<br>9 6 7<br>2 5 1</td>
<td style="font-family:&#39;courier new&#39;;font-size:14pt;">9 2 1<br>3 4 5<br>8 7 6</td>
<td style="font-family:&#39;courier new&#39;;font-size:14pt;">6 5 7<br>8 2 1<br>4 9 3</td>
</tr><tr><td style="font-family:&#39;courier new&#39;;font-size:14pt;">5 4 8<br>7 2 9<br>1 3 6</td>
<td style="font-family:&#39;courier new&#39;;font-size:14pt;">1 3 2<br>5 6 4<br>7 9 8</td>
<td style="font-family:&#39;courier new&#39;;font-size:14pt;">9 7 6<br>1 3 8<br>2 4 5</td>
</tr><tr><td style="font-family:&#39;courier new&#39;;font-size:14pt;">3 7 2<br>8 1 4<br>6 9 5</td>
<td style="font-family:&#39;courier new&#39;;font-size:14pt;">6 8 9<br>2 5 3<br>4 1 7</td>
<td style="font-family:&#39;courier new&#39;;font-size:14pt;">5 1 4<br>7 6 9<br>3 8 2</td>
</tr></tbody></table></td>
</tr></tbody></table></div>

A well constructed Su Doku puzzle has a unique solution and can be solved by logic, although it may necessary to employ "guess and test" methods in order to eliminate options (there is much contested opinion over this). The complexity of the search determines the difficulty of the puzzle; the example above is considered *easy* because it can be solved by straight forward direct deduction.

The 6K text file, [sudoku.txt](https://projecteuler.net/project/resources/p096_sudoku.txt) (right click and 'Save Link/Target As...'), contains fifty different Su Doku puzzles ranging in difficulty, but all with unique solutions (the first puzzle in the file is the example above).

By solving all fifty puzzles find the sum of the 3-digit numbers found in the top left corner of each solution grid; for example, 483 is the 3-digit number found in the top left corner of the solution grid above.

## Answer

Okay, so we are just making a sudoku solver. To recap, each row, column and 3 by 3 box must contain all the digits 1-9 **exactly once**. How do we begin to code up a solution?

The second paragraph provides a good clue. Most puzzles can be solved by logic. I suggest you take a pen and paper and attempt to solve the example above. Pay close attention to your thinking process as you solve it. Chances are, you'll think something similar to "This cell can't have a 2 because there's a 2 already in the row", or "This cell needs to be a 5, which makes this other cell an 8, and etc." *That thought process is what we want to capture!*

First, we would need for each cell, which other cells in the grid constrain the result of the cell. In this case, it would always be, the 8 other cells in its row, the 8 cells in its column, and the remaining 4 cells in the box the cell is part of. As our program evolves, we keep updating of the possible numbers that can go in each cell. We also need methods that update the possibilities through pure logic, and also ones that brute force checks the remaining cells which haven't been narrowed down (what the problem calls "guess and test"). So our steps are:

- **Initially, each 0 cell will have all possibilities.** The cells that have been filled in are already solved.
- **Reduce possibilities through logic.** This is analogous to the thought process "This cell can't be an 8 because there's an 8 in the column." Any cell which has been narrowed down completely to only one option **gets filled in.** This prior step actually trims down our search space tremendously, as the easy puzzles are usually solved by this point actually.
- **Of those cells which still have more than one possibility, brute force them**. Simply what you would do in real life: Fill in an option, and try to solve the puzzle from there. If we reach an impossibility (a cell has 0 options as to what number it could contain), then the choice was wrong, and so we erase and fill in our next choice. **Step 2 takes place once we've filled in an option as well**.
- Keep going, until each cell has only one option, in which case the puzzle is solved.

The structure I'm using to store the possibilities is a three dimensional list. We have a 9 by 9 grid, where each element is a list of the numbers the cell can be. We assume the board itself is also a two dimensional list. As you might guess from the methodology, the main solver method is **recursive**. With recursion involving lists, we have to be extra careful. Python does shallow copies of lists if we assign 2 separate variables to it, so when you change one it changes the other. This causes problems when we backtrack because we'll lose the original board. To solve it, I use the `deepcopy` method from the `copy` package, which completely creates a new object. Additionally, to iterate through the indices, I use `itertools.product`, which conveniently returns tuples `(0,0), (0,1), ...` given a `repeat` parameter.

The code is longer than most due to all the methods, but it is still digestable.

```python
def getConstraintSets(N):
    blockSize = int(N ** 0.5)
    constraintSet = {}
    for r, c in product(range(N), repeat=2):
        s = []
        # Row set
        s.extend((r, column) for column in range(N))
        # Column set
        s.extend((row, c) for row in range(N))
        # Block set
        # Get top corner of block (0-index)
        blockRow = r // blockSize * blockSize
        blockColumn = c // blockSize * blockSize
        # Iterate through the block...
        for row in range(blockRow, blockRow + blockSize):
            for column in range(blockColumn, blockColumn + blockSize):
                s.append((row, column))
        # Get rid of duplicates
        s = list(set(s))
        # Remove the cell we're at...
        s.remove((r, c))
        constraintSet[(r, c)] = s
    return constraintSet


def initialPossibilities(board, N):
    poss = range(1, N + 1)
    # Need to make a new list object each
    # time.
    return [[list(poss) if board[r][c] == 0 else [board[r][c]] 
        for c in range(N)] for r in range(N)]


# Method to directly deduce the number of possibilities
# as much as possible without resorting to checking
# multiple values for a single cell i.e. PURE
# DEDUCTION
def reducePossibilities(posses, constraintSets, N):
    possibilities = copy.deepcopy(posses)
    solvedCells = [(r, c) for r, c in product(range(N), repeat=2)
                   if len(possibilities[r][c]) == 1]
    while len(solvedCells) != 0:
        r, c = solvedCells.pop(0)
        # Get the value
        # If the list is empty,
        # that means there are no possibilities
        # in this cell...
        if len(possibilities[r][c]) == 0:
            return None
        value = possibilities[r][c][0]
        # For each cell in the constrained set,
        # go to that cell, and remove 'value' from
        # that cell's list of possibilities.
        for consR, consC in constraintSets[(r, c)]:
            if value in possibilities[consR][consC]:
                possibilities[consR][consC].remove(value)
                # If it resulted in one possibility, then
                # it's "solved". Add it to the list to check
                # later.
                if len(possibilities[consR][consC]) == 1:
                    solvedCells.append((consR, consC))
    return possibilities


# Recursive function to handle
# nonpure deductions...
def solveBoard(possibilities, constraintSets, N):
    # Board is solved if we have exactly
    # one possibility in each cell.
    if isSolved(possibilities):
        return [[possibilities[r][c][0] for c in range(N)] for r in range(N)]
    # Get the next cell with a 0
    # in it a.k.a has more than one possibility
    r, c = getNext0(possibilities)
    # For each choice we have,
    # try it, reduce the possibilities
    # and see if it will work...
    for choice in possibilities[r][c]:
        # Copy the possibilities list
        testPoss = copy.deepcopy(possibilities)
        testPoss[r][c] = [choice]
        # Reduction...
        reduction = reducePossibilities(testPoss, constraintSets, N)
        # If it's a failed reduction, throw it away,
        # Otherwise, assume it's right and try to
        # solve the board from that spot.
        if reduction is not None:
            result = solveBoard(reduction, constraintSets, N)
            # If something came back other than None,
            # then it was solved!
            if result is not None:
                return result
    return


def getNext0(possibilities):
    for r, c in product(range(len(possibilities)), repeat=2):
        if len(possibilities[r][c]) > 1:
            return r, c


def isSolved(posses):
    for r, c in product(range(len(posses)), repeat=2):
        if len(posses[r][c]) > 1:
            return False
    return True


N = 9
with open('p096_sudoku.txt') as f:
    boardText = f.read().splitlines()
boards = []
for i in range(0, len(boardText), 10):
    # Read the next 9 lines into the board...
    boards.append([list(map(int, line)) for line in boardText[i + 1: i + 10]])

# Split each line into the individual numbers and apply a cast
# There are 50 boards so reshape as well
s = 0
constSets = getConstraintSets(N)
for board in boards:
    # Get initial possibilities and
    # reduce them.
    initial = initialPossibilities(board, N)
    # prettyPrintBoard(initial)
    reduced = reducePossibilities(initial, constSets, N)
    solved = solveBoard(reduced, constSets, N)
    # Add the top 3 corner numbers...
    s += 100 * solved[0][0] + 10 * solved[0][1] + solved[0][2]

print(s)
```

Running this very long code gets a final sum of,

```
24702
1.5998253 seconds.
```

Thus, our sum is **24702**. The time is not bad at all, especially considering that the final puzzles in the list have the bare minimum of filled-in squares (17) necessary to solve.