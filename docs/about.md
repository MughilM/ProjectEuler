---
layout: post
title: About
permalink: /about/
---

Project Euler is a vast collection of problems ranging in all areas of mathematics, from number theory to probability. All problems are designed to be solved with programming, with the only submission being the answer. To see all problems, please visit [the archives](https://projecteuler.net/archives). In this website, I will list all of the problems that I have solved. 

### Format

So, each problem is a blog post, with the date being the date that I solved it. Generally, I try to solve the problems in numerical order, but due to difficulty of some problems, I may jump back and forth. Links to all problems in order are listed [here](/All-Problems.md).

Additionally, each program has been written in this format:

```python
import numpy as np
import np

# FUNCTIONS GO HERE

start = time.perf_counter()

# CONTENT

end = time.perf_counter()
print(end - start, 'seconds.')
```

For brevity, I will only show the code that goes in `CONTENT`. Any output shown should assume that timings have been implemented. The `numpy` package is an extremely useful Python package that implements a number of advanced linear algebra and other mathematical functions. Additionally, it is useful for implementing element-by-element operations in an array. 

The programs are run on my personal laptop, which is running an Intel i7-6500U processor @ 2.50GHz, along with 8GB RAM. Timings may vary across systems.