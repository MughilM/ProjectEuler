---
layout: post
title: "Problem 75 - Singular integer right triangles"
date: 2018-06-03 16:16
mathjax: true
number: 75
---

## Question

It turns out that 12 cm is the smallest length of wire that can be bent to form an integer sided right angle triangle in exactly one way, but there are many more examples.


$$
\mathbf{12}\textbf{ cm}: (3,4,5)
\\
\mathbf{24}\textbf{ cm}: (6,8,10)
\\
\mathbf{30}\textbf{ cm}: (5,12,13)
\\
\mathbf{36}\textbf{ cm}: (9,12,15)
\\
\mathbf{40}\textbf{ cm}: (8,15,17)
\\
\mathbf{48}\textbf{ cm}: (12,16,20)
$$


In contrast, some lengths of wire, like 20 cm, cannot be bent to form an integer sided right angle triangle, and other lengths allow more than one solution to be found; for example, using 120 cm it is possible to form exactly three different integer sided right angle triangles.


$$
\mathbf{120}\textbf{ cm}: (30,40,50), (20,48,52), (24,45,51)
$$


Given that $$L$$ is the length of the wire, for how many values of $$L\leq 1\,500\,000$$ can exactly one integer sided right angle triangle be formed?

## Answer

There is a problem, namely [Problem 39]({{ site.url }}{% post_url 2016-05-17-Problem-39---Integer-right-triangles %}), that is extremely similar to this, in that we essentially have a bound on the perimeter of the triangle. Like that problem, it's easier to generate the triangles instead of checking each possible perimeter and see if a triangle exists. The method of generating the triples is the same; please consult the write-up of that problem for the details on we generate them. Here, I've modified it slightly to include the matrices in the method itself.

The method only generates primitive triples, however, so we have to take into multiples of it i.e. the (30,40,50) is a multiple of the (3,4,5) triangle. Once the perimeter of the primitive triangle has been computed, we just add all multiples of it until the limit. As we go along, we place on regard on duplicates yet.

Next, we use `np.unique` along with the `return_counts` keyword to get the number of times each perimeter shows up in the list. Anything that has shown up exactly once we include. This method is in fact faster than using Python's built-in structure of `set` due to how many elements there are.

```python
maxi = 1500000
sums = []
for triple in genTriples(np.array([3, 4, 5]), maxi=maxi):
    primPerim = np.sum(triple)
    sums.extend(range(primPerim, maxi + 1, primPerim))
uniques, counts = np.unique(sums, return_counts=True)
exactlyOnce = uniques[np.where(counts == 1)]
print(exactlyOnce.size)
```

Running the above results in an output of,

```
161667
2.8955301999999996 seconds.
```

Thus, there are **161667** values under 1.5 million such that there exists exactly one integer sided right angle triangle.