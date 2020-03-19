---
layout: post
title: "Problem 83 - Path sum: four ways"
date: 2019-01-19 12:19
number: 83
mathjax: true
---

## Question

In the 5 by 5 matrix below, the minimal path sum from the top left to the bottom right, by moving left, right, up, and down, is indicated in bold red and is equal to 2297.


$$
\begin{pmatrix}
	\color{red}{\mathbf{131}} & 673 & \color{red}{\mathbf{234}} & \color{red}{\mathbf{103}} & \color{red}{\mathbf{18}}
	\\
	\color{red}{\mathbf{201}} & \color{red}{\mathbf{96}} & 
		\color{red}{\mathbf{342}} & 965 & \color{red}{\mathbf{150}}
	\\
	630 & 803 & 746 & \color{red}{\mathbf{422}} & \color{red}{\mathbf{111}}
	\\
	537 & 699 & 497 & \color{red}{\mathbf{121}} & 956
	\\
	805 & 732 & 524 & \color{red}{\mathbf{37}} & \color{red}{\mathbf{331}}
\end{pmatrix}
$$


Find the minimal path sum from the top left to the bottom right by moving left, right, up, and down in [matrix.txt](https://projecteuler.net/project/resources/p083_matrix.txt) (right click and "Save Link/Target As..."), a 31K text file containing an 80 by 80 matrix.

## Answer

Woah. So what do we do? Unlike the previous two path sum problems, any cell can have its path coming from **any direction**. This creates confusion in that we don't know which path to calculate first. 

Instead, we can take a different approach. We can think of the matrix of numbers as a topological **graph**. Each vertex represent a matrix entry, and each **incoming edge to that vertex** represents how much you need to add to get there.

To clarify, the vertices will not have numbers themselves, only the edges will. To designate, I'll put subscript to signify the row and column, for example $$v_{22}$$ is the vertex in the 2nd row and 2nd column. This vertex, will have incoming edges from $$v_{12}, v_{21}, v_{23}$$ and $$v_{32}$$ with edge weights, 673, 201, 342, and 803 respectively. 

Once we convert the problem representation, the question now turns into: "What is the path with the shortest weight from $$v_{11}$$ to $$v_{nn}$$. A shortest path problem! There has been a lot of research in shortest path problems, most famously by Edsger Dijkstra, who created [Dijkstra's Algorithm](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm). If you haven't heard of it, I strongly encourage you to visit the link and especially look at the animation, as it provides the best explanation. Below I simply perform the conversion I stated earlier, and implement Dijkstra's Algorithm on the resulting graph.

One small implementation detail, I leave out the top left corner value from the graph, because it is an initial value which is not originating from anywhere. Instead, I just add it at the very end.

```python
# Function to determine the row-column neighbors
# of an index in the matrix
def getNeighbors(index, width, height):
    # Grab the row and column (0-indexed)
    row = index // height
    column = index % width
    # Calculate the 4 neighbors in
    # each direction
    RCNeighs = [
        (row, column - 1),  # Left
        (row - 1, column),  # Up
        (row, column + 1),  # Right
        (row + 1, column)   # Down
    ]
    # Filter out locations that are
    # outside the range. These would be
    # negatives or numbers bigger than width/height
    RCNeighs = list(filter(lambda loc: (0 <= loc[0] < height) and (0 <= loc[1] < width), RCNeighs))
    return RCNeighs

start = time.perf_counter()

matrix = np.loadtxt('./p083_matrix.txt', dtype='int32', delimiter=',')

height, width = matrix.shape

# We are going to take Dijkstra's approach and treat
# the digits as edge weights. The graph will be
# directed because going from backwards adds a different number.
# The nodes will numbered 0 to whatever number, across the columns
# then down the rows. Due to this, when we find the minimum
# distance, we must ADD THE TOP LEFT.
edgeWeights = {}

for i in range(width * height):
    neighbors = getNeighbors(i, width, height)
    weights = []
    # For each neighbor, calculate index value
    # and grab the value...
    for row, col in neighbors:
        indexVal = row * width + col
        weights.append((indexVal, matrix[row, col]))
    # Assign the weights for this index...
    edgeWeights[i] = weights

# Now we can do Dijkstra's algorithm. The start index
# is the node value 0 (top left).
# We need a unvisited array and an array to hold the minimum
# values.
minValues = np.ones(len(edgeWeights.keys())) * float('inf')
unvisited = list(edgeWeights.keys())

# As a first step, the start node will have distance 0.
minValues[0] = 0

# While we still have vertices to visit.
while len(unvisited) > 0:
    # Choose the index with the lowest distance which
    # hasn't been visited yet.
    nextIndex = unvisited[np.argmin(minValues[unvisited])]
    # Grab the unvisited neighbors
    unvisitedNeighs = list(filter(lambda x: x[0] in unvisited, edgeWeights[nextIndex]))
    # For each unvisited neighbor, add the weight to
    # the current min value of nextIndex and see if it should
    # be updated
    for neigh, weight in unvisitedNeighs:
        if minValues[nextIndex] + weight < minValues[neigh]:
            minValues[neigh] = minValues[nextIndex] + weight
    # The index is now visited, so remove it...
    unvisited.remove(nextIndex)

# The value in the bottom right is the last value
# in the minValues array. Add this to the value in
# the top left to get our minimum sum.
print(int(minValues[-1] + matrix[0, 0]))

end = time.perf_counter()
print(end - start, 'seconds.')
```

Running the algorithm gets us an output of

```
425185
4.2442820999999995 seconds.
```

Thus, the smallest corner to corner sum is **425185**.