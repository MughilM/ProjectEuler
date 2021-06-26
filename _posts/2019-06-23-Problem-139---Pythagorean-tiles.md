---
layout: post
title: "Problem 139 - Pythagorean tiles"
date: 2019-06-23 18:54
number: 139
mathjax: true
---

> Let $$(a,b,c)$$ represent the three sides of a right angle triangle with integral length sides. It is possible to place four such triangles together to form a square with length $$c$$.
>
> For example, (3, 4, 5) triangles can be placed together to form a 5 by 5 square with a 1 by 1 hole in the middle and it can be seen that the 5 by 5 square can be tiled with twenty-five 1 by 1 squares.
>
> ![p139]({{ site.url }}{{ site.baseurl }}/assets/Images/p139.png)
>
> However, if (5, 12, 13) triangles were used, then the hole would measure 7 by 7 and these could not be used to tile the 13 by 13 square.
>
> Given that the perimeter of the right triangle is less than one-hundred million, how many Pythagorean triangles would allow such a tiling to take place?

<!--more-->

## Answer

Eventually, it should be clear that we will use the recursive process to generate triples. However, we need to be careful of which ones we should check. Let's see how we can calculate the size of the hole. Notice that we have a square of side length $$c$$ with the four triangles. Thus, we can subtract the area of the 4 triangles from the area of the larger square:

$$
c^2-4\left(\frac{1}{2}ab\right) = c^2-2ab = a^2+b^2-2ab = (a-b)^2
$$
Square rooting this expression gets us the side length of $$a-b$$. Since we don't know which variable is bigger, we can take the absolute value. **Therefore, this means the triples we are interested are those where $$\mathbf{|a-b|=1}$$**. 

According to the [tree Wiki](https://en.wikipedia.org/wiki/Tree_of_primitive_Pythagorean_triples), repeatedly applying the $$B$$ matrix preserves **exactly this property.** So actually, we don't even need to run our recursive function; we just keep multiplying $$B$$ until we surpass the perimeter threshold!

```python
B = np.array([
    [1, 2, 2],
    [2, 1, 2],
    [2, 2, 3]
], dtype=object)

tilings = 0
limit = 100000000

# Only of square 1 can divide...
triple = np.array([3, 4, 5])
while sum(triple) < limit:
    print(tilings)
    # This has a square of 1.
    tilings += limit // sum(triple)
    triple = np.dot(B, triple)

print(tilings)
```

Running this short loop results in an output of,

```
10057761
0.00013170000000001236 seconds.
```

Therefore, our final answer to this problem is **10057761**.