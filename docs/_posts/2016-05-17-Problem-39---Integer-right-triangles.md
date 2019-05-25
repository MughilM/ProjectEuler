---
layout: post
title: "Problem 39 - Integer right triangles"
date: 2016-05-17 10:50
mathjax: true
number: 39
---

## Question

If $$p$$ is the perimeter of a right angle triangle with integral length sides, $$\{a,b,c\}$$, there are exactly three solutions for $$p = 120$$.


$$
\begin{align*}
\{20,48,52\},\{24,45,51\},\{30,40,50\}
\end{align*}
$$


For which value of $$p\leq1000$$, is the number of solutions maximised?

## Answer

There are two possible we could go about this: Either we test each $$p$$ and see which right triangles exist, or we take all right angles with perimeters below 1000 and see which perimeter occurs the most. Personally, I think the latter is the easier way to go, as not every $$p$$ will have an integer right triangle associated with it.

So then how do we find all Pythagorean triples with perimeters less than or equal 1000? After some research on methods of generating triples, I came across [this Wikipedia page](https://en.wikipedia.org/wiki/Tree_of_primitive_Pythagorean_triples), which details 3 matrices which can be used to find all triples. I have reproduced the matrices below.


$$
\begin{align*}
A &= \begin{bmatrix}
	1 & -2 & 2 \\
	2 & -1 & 2 \\
	2 & -2 & 3
\end{bmatrix} \\
B &= \begin{bmatrix}
	1 & 2 & 2 \\
	2 & 1 & 2 \\
	2 & 2 & 3
\end{bmatrix} \\
C &= \begin{bmatrix}
	-1 & 2 & 2 \\
	-2 & 1 & 2 \\
	-2 & 2 & 3
\end{bmatrix}
\end{align*}
$$


To generate triples, start with the most basic of them: (3,4,5). Convert this to a vector $$\mathbf{v}=\langle 3,4,5 \rangle$$. Now multiply this vector by one of the three matrices above on the left i.e. $$A\mathbf{v}$$. The result of this is another Pythagorean triple! What's more, each of the three matrices will always get you 3 distinct triples. For example, $$A\mathbf{v}$$ will get you the triple $$\langle 5,12,13 \rangle$$, while $$B\mathbf{v}=\langle 21,20,29\rangle$$ and $$C\mathbf{v}=\langle 15,8,17\rangle$$.

Because you get three distinct vectors, you can form almost a kind of tree of Pythagorean triples, depending on the order you multiply matrices. For example, multiplying $$C$$, then $$B$$, then $$A$$ gets you $$ABC\mathbf{v}=\langle 115,252,277\rangle$$.

One note I should make is that this method will only generate **primitive** Pythagorean triples, meaning those that are in their reduced form. The triple $$\langle 10,24,26\rangle$$ will not be generated in this process, because it is a multiple of $$\langle 5,12,13\rangle$$. However, this problem is easily worked around as I will explain later.

Because this is a tree structure, recursion is the perfect method to write a function that generates triples for us. The only thing we need to keep passing is the current triple, and the maximum perimeter we want. If we encounter a triple that is larger than the maximum perimeter allowed, we simply cut the tree at that point. Using `numpy` will allow us to easily multiply matrices, and putting it in generator format will mean we won't have to store all the triples at one time. The recursion step is simple: We simply multiply each matrix by the current triple and start a new branch. See the code below:

```python
def genPythagoreanTriples(triple, maxP, A, B, C):
    # Base case...
    if sum(triple) > maxP:
        return
    yield triple
    # Multiply each matrix
    for matrix in [A, B, C]:
        for multTriple in genPythagoreanTriples(np.dot(matrix, triple), maxP, A, B, C):
            yield multTriple
```

At this point, we simply define our $$A,B,$$ and $$C$$ in the main program, and then calculate the perimeter of each triangle. Recall that the generation method only generates primitive triples. In order to get multiples of triples, we need to loop through all multiples that don't exceed the perimeter once more. For example, since the (3,4,5) triangle has perimeter 12, and $$\lfloor 1000/12 \rfloor = 83$$, we need to go through (3,4,5), (6,8,10), ..., (249,332,415). That last triangle has a perimeter of 996, just below our bound. We do this for each primitive triple generated using the function above. We also add each perimeter as it comes along, without worrying about duplicates for the moment.

After we are done going through all triples, we can call `numpy`'s `unique` function to gather us the unique perimeter values as well as how many times each perimeter was encountered. We can then take the maximum and we're done!

```python
# Make the matrices
A = np.array([
    [1, -2, 2],
    [2, -1, 2],
    [2, -2, 3]
])
B = np.array([
    [1, 2, 2],
    [2, 1, 2],
    [2, 2, 3]
])
C = np.array([
    [-1, 2, 2],
    [-2, 1, 2],
    [-2, 2, 3]
])

maxP = 1000
trianglePerims = []
for triple in genPythagoreanTriples(np.array([3,4,5]), maxP, A, B, C):
    perimTriple = sum(triple)
    # Generate all triple multiples that don't exceed maxP
    # and add it to the list
    for i in range(1, maxP // perimTriple + 1):
        trianglePerims.append(i * perimTriple)

# Find the unique solutions
unique, count = np.unique(trianglePerims, return_counts=True)
# Find the p with the most solutions
print('p =', unique[np.argmax(count)], 'has', np.max(count), 'solutions.')
```

Notice I used `np.argmax()` to get the *location* of the maximum solutions. Running the code results in an output of,

```python
p = 840 has 8 solutions.
0.0012768364795974804 seconds.
```

Therefore, $$p=\boxed{840}$$ is the perimeter with the most solutions (8). It took almost no time at all since we only had to look through all triples whose perimeter was at most 1000 (~350 of them).