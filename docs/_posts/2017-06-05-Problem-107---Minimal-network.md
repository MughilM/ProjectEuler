---
layout: post
title: "Problem 107 - Minimal network"
date: 2017-06-05 10:20
number: 107
mathjax: true
---

## Question

The following undirected network consists of seven vertices and twelve edges with a total weight of 243.

![networkImg]({{site.url}}{{site.baseurl}}/assets/Images/p107_1.png)

The same network can be represented by the matrix below.

|       | A    | B    | C    | D    | E    | F    | G    |
| ----- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| **A** | -    | 16   | 12   | 21   | -    | -    | -    |
| **B** | 16   | -    | -    | 17   | 20   | -    | -    |
| **C** | 12   | -    | -    | 28   | -    | 31   | -    |
| **D** | 21   | 17   | 28   | -    | 18   | 19   | 23   |
| **E** | -    | 20   | -    | 18   | -    | -    | 11   |
| **F** | -    | -    | 31   | 19   | -    | -    | 27   |
| **G** | -    | -    | -    | 23   | 11   | 27   | -    |

However, it is possible to optimise the network by network by removing some edges and still ensure that all points on the network remain connected. The network which achieves the maximum saving is shown below. It has a weight of 93, representing a saving of 243 - 93 = 150 from the original network.

![cutDownImg]({{site.url}}{{site.baseurl}}/assets/Images/p107_2.png)

Using [network.txt](https://projecteuler.net/project/resources/p107_network.txt) (right click and 'Save Link/Target As...'), a 6K text file containing a network with forty vertices, and given in matrix form, find the maximum saving which can be achieved by removing redundant edges whilst ensuring that the network remains connected.

## Answer

The meaning of "connected" is exactly as it sounds like: We don't have a vertex that's floating off in space. More specifically, **starting from any vertex in the graph**, a path exists from it to **every other vertex**.

Since we want the **minimal weight** edges, a good algorithm is: At each step, find the edge with the **biggest weight** that we can remove such that the graph still remains connected.

When do we stop? We stop when **every edge would lead to a disconnected graph once removed.** 

A pretty simple algorithm. With respect to code, there is a useful package called `networkx`, which handles graph structures. It has functions such as `add_edge`, `add_node`, as well as `is_connected`, which will come in handy. As an aside, due to how our input file looks like, I use the `read_csv` function from the `pandas` package.

```python
import networkx as nx
import pandas as pd

df = pd.read_csv('./p107_network.txt', na_values='-', header=None)

# Add all the vertices
G = nx.Graph()
G.add_nodes_from(df.columns)
# Go through the weights and add the edges
for i in range(len(df.columns)):
    for j in range(i):
        if not np.isnan(df.loc[i, j]):
            G.add_edge(i, j, weight=df.loc[i, j])
sortedEdges = sorted(G.edges(data='weight'), key=lambda x: x[2])[::-1]
amountBefore = int(np.sum([x[2] for x in sortedEdges]))
print('Sum of edges before:', amountBefore)
i = 0
for i in range(len(sortedEdges)):
    # Make a copy and delete the deg and check if it's connected
    H = G.copy()
    H.remove_edge(sortedEdges[i][0], sortedEdges[i][1])
    if nx.is_connected(H):
        G = H.copy()

amountAfter = int(np.sum([x[2] for x in G.edges(data='weight')]))
print('Sum of edges after:', amountAfter)

savings = amountBefore - amountAfter

print(savings)
```

Running the code results in an output of,

```
Sum of edges before: 261832
Sum of edges after: 2153
259679
0.9926626999999999 seconds.
```

Thus, we save **259679** after making the minimum weight graph, also known as a **minimum spanning tree**.