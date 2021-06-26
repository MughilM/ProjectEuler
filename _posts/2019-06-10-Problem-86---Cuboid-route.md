---
layout: post
title: "Problem 86 - Cuboid route"
date: 2019-06-10 20:25
mathjax: true
number: 86
---

> A spider, S, sits in one corner of a cuboid room, measuring 6 by 5 by 3, and a fly, F, sits in the opposite corner. By travelling on the surfaces of the room the shortest "straight line" distance from S to F is 10 and the path is shown on the diagram.
>
> ![cubeImg]({{site.url}}{{site.baseurl}}/assets/Images/p086.png)
>
> However, there are up to three "shortest" path candidates for any given cuboid and the shortest route doesn't always have integer length.
>
> It can be shown that there are exactly 2060 distinct cuboids, ignoring rotations, with integer dimensions, up to a maximum size of $$M$$ by $$M$$ by $$M$$, for which the shortest route has integer length when $$M=100$$. This is the least value of $$M$$ for which the number of solutions first exceeds two thousand; the number of solutions when $$M=99$$ is 1975.
>
> Find the least value of $$M$$ such that the number of solutions first exceeds one million.
>

<!--more-->

Interesting problem. Firstly, I want to show you that since this is an optimization problem, the most straightforward way is to **single-variable calculus**, and this is the solution I present here. The value of the shortest path shows an interesting property, if it were an integer. Look forward to it :)

Next, I also want to show the three possible candidates for the shortest paths that the problem talked about. Like above, the spider would have to go over an edge, and so, **each path corresponds to each edge spider goes over **. She can either go over the 6 edge, the 5 edge, or the 3 edge. I've displayed them below.

![routeImg]({{site.url}}{{site.baseurl}}/assets/Images/p086-1.png)

First question you might ask, **which edge corresponds to the actual shortest path from S to F?**. Intuitively, it is **the longest edge**, because the spider is cutting across it through both legs (before going over the edge, and after). If the spider cross any of the shorter edges, she would have cover that longest edge in just of the two legs, which would lead a slightly longer distance. **To prove to ourselves that this is indeed correct, let's actually try to compute the shortest path!**

I'll assume a cuboid measuring $$a\times b\times c$$, with $$a\geq b\geq c$$, so $$a$$ is the longest edge. Our assumption is that the spider goes over edge $$a$$, and so the variable we would be optimizing is **where she does it**. Let's call this point $$x$$. The picture below helps with visualizing.

![xpointImg]({{site.url}}{{site.baseurl}}/assets/Images/p086-2.png)

To find our distance function, notice that each of the two parts are hypotenuses of two right triangles. The triangle lying flat (on the $$ab$$ face), has sides $$x$$ and $$b$$, and so has a hypotenuse of $$\sqrt{x^2+b^2}$$. The other triangle has sides $$c$$  and $$\mathbf{a-x}$$. Thus, this hypotenuse has a length of $$\sqrt{(a-x)^2+c^2}$$. Therefore, our distance function $$f(x)$$ is


$$
f(x)=\sqrt{x^2+b^2}+\sqrt{(a-x)^2+c^2}
$$


To minimize this, we have to take the derivative, set it equal to 0, and solve for $$x$$. That tells us the point which the spider has to cross the edge. To find the actual distance, we just plug this back in. The derivative is quite messy in the beginning, but I promise it will simplify to something nice! Let's do it!

### Taking the Derivative...

Lot of algebra involved here, but nothing we can't handle. I'll expand the square in the second square root and then compute the derivative. We then set this to 0 and solve.


$$
\begin{aligned}
	\frac{df}{dx}&=\frac{1}{2}(x^2+b^2)^{-\frac{1}{2}}(2x)+
		\frac{1}{2}(x^2-2ax+a^2+c^2)^{-\frac{1}{2}}
		(2x-2a)
	\\
	0 &= \frac{x}{\sqrt{x^2+b^2}}+\frac{x-a}
		{\sqrt{x^2-2ax+a^2+c^2}}
	\\
	\frac{a-x}{\sqrt{x^2-2ax+a^2+c^2}} &=
		\frac{x}{\sqrt{x^2+b^2}}
	\\
	(a-x)\sqrt{x^2+b^2} &= x\sqrt{x^2-2ax+a^2+c^2}
	\\
	(a-x)^2(x^2+b^2) &= x^2(x^2-2ax+a^2+c^2)
	\\
	(x^2-2ax+a^2)(x^2+b^2) &= x^4-2ax^3+a^2x^2+c^2x^2
	\\
	x^4+b^2x^2-2ax^3-2ab^2x+a^2x^2+a^2b^2 &=
		x^4-2ax^3+a^2x^2+c^2x^2
	\\
	(b^2-c^2)x^2-2ab^2x+a^2b^2 &= 0
\end{aligned}
$$


Notice that last expression is a quadratic in $$x$$. Thus, we can directly employ the quadratic formula:


$$
\begin{aligned}
	x &= \frac{2ab^2\pm\sqrt{4a^2b^4-4(b^2-c^2)a^2b^2}}
		{2(b^2-c^2)}
	\\ &=
	\frac{2ab^2\pm\sqrt{4a^2b^4-4a^2b^4+4a^2b^2c^2}}
		{2b^2-2c^2}
	\\ &=
	\frac{2ab^2\pm\sqrt{4a^2b^2c^2}}{2b^2-2c^2}
	\\ &=
	\frac{2ab^2\pm 2abc}{2b^2-2c^2}
	\\ &=
	\frac{ab^2\pm abc}{b^2-c^2}
\end{aligned}
$$


Now then, **which sign do we choose?** Remember, since we're going over an edge, $$x$$ has to be **less than** $$\mathbf{a}$$. If we choose the positive, then our numerator is $$ab^2+abc = a(b^2+bc)>a$$. So we have to **choose the negative sign**.


$$
\frac{ab^2-abc}{b^2-c^2} = \frac{ab(b-c)}{(b-c)(b+c)}=
	\boxed{\frac{ab}{b+c}}
$$


Cool we have the minimizing point! Now we need to plug this into our distance formula to find the actual distance.

### Finding the Minimizing Distance

Plug $$x=\frac{ab}{b+c}$$ into $$f(x)$$ to get our minimizing distance. More algebra:


$$
\begin{aligned}
	f\left(\frac{ab}{b+c}\right) &=
		\sqrt{\left(\frac{ab}{b+c}\right)^2 + b^2} +
		\sqrt{\left(a-\frac{ab}{b+c}\right)^2+c^2}
	\\ &=
	\sqrt{\frac{(ab)^2}{(b+c)^2}+\frac{b^2(b+c)^2}{(b+c)^2}} +
		\sqrt{\left(\frac{a(b+c)}
		{b+c}-\frac{ab}{b+c}\right)^2 + c^2}
	\\ &=
	\frac{b}{b+c}\sqrt{a^2+(b+c)^2} +
		\frac{c}{b+c}\sqrt{a^2+(b+c)^2}
	\\ &=
	\left(\frac{b}{b+c}+\frac{c}{b+c}\right)
		\sqrt{a^2+(b+c)^2}
	\\ &=
	\boxed{\sqrt{a^2+(b+c)^2}}
\end{aligned}
$$


Told you it would simplify nicely! Now, remember this is assuming that the spider would go over the $$a$$ edge en-route to $$F$$. Looking at the result, we see that the edge that gets passed over is squared on its own, and the other two sides are added together and then squared. **If we took $$\mathbf{b}$$ to be the passed over edge**, then $$a$$ and $$c$$ would be added together and then squared, which would result **in a larger distance**. Hopefully this convinces you that we need the spider to pass over the longest edge to have the shortest distance. If you're still not convinced, you could always go through the derivation assuming $$x$$ is on $$b$$ or $$c$$ :)

### Analyzing the Minimizing Distance

Let's call this minimum distance $$D$$. Thus, we have $$D=\sqrt{a^2+(b+c)^2}$$. Remember, we want $$D$$ **to be an integer**. Hmm, then that means that the thing inside the square root would have to be a perfect square wouldn't it? In fact, let's rewrite this...


$$
a^2+(b+c)^2=D^2
$$


That looks eerily like the Pythagorean formula. The only difference is that we're adding two things together. From all our information thus far, we can get the following conclusion:

**If the length of the shortest path $$\mathbf{D}$$ is an integer, then $$\mathbf{a}$$, $$\mathbf{b+c}$$, and $$\mathbf{D}$$ all have to form a Pythagorean triple.**

To verify, we can use the example. We have $$a=6$$, $$b=5$$, and $$c=3$$. This corresponds to the triple $$\{6,8,10\}$$, and indeed $$10$$ is the shortest path.

But wait, we've dealt with looping through triples before. We use those matrices from [Problem 39]({{ site.baseurl }}{% post_url 2016-05-17-Problem-39---Integer-right-triangles %})! But we have to deal with a little something....

### Looping through all possible triplets

When we loop through triples $$\alpha, \beta, \gamma$$ (with $$\alpha\leq\beta\leq\gamma$$), it is clear that the hypotenuse corresponds to $$D$$ directly i.e. $$\gamma=D$$. What about the other two values? Well we have two choices here: Either our largest side $$a=\alpha$$ or $$a=\beta$$. What are the consequences?

#### $$a=\alpha$$

If we set our largest side to be $$\alpha$$, then we must split up $$\beta$$ into two values $$b$$ and $$c$$ such that $$a$$ are greater than both. **This will only work if $$\mathbf{\beta \leq 2\alpha}$$**. Now then, next question: **Assuming this is true, how many solutions are there for a fixed triple in this case?**

Without any constraints, there are $$\lfloor \beta/2\rfloor$$ distinct pairs of sums. Of these, exactly $$\beta-\alpha$$ of them have a number which is **greater than** $$\alpha$$. Thus, we have to subtract these out. However, we also have to add back in the sum with an operand that is **equal to** $$\alpha$$, since that is also allowed. Therefore, **the total number of solutions in this case are** $$\boxed{\lfloor \beta/2\rfloor - (\beta-\alpha)+1}$$.

#### $$\mathbf{a=\beta}$$

This case is slightly easier. Since $$\beta\geq\alpha$$, all partitions of $$\alpha$$ are valid solutions. Thus, the number of solutions in this case is exactly $$\lfloor \alpha/2\rfloor$$.

### Implementation

Recall our recursive implementation to go through the triples only covers primitive triples. Thus, we count the number of solutions, we have to do so for multiples of the triples as well. Secondly, **when do we stop our recursive search?**

Remember $$a$$ is our biggest side, and this **can't exceed** $$M$$. Based on our two cases above, we stop when either $$\alpha > M$$, or when both $$\beta>M$$ and $$\beta>2\alpha$$, because this prevents us from splitting up $$\beta$$ into partitions.

This method of finding the number of solutions is extremely quick....for a specific value of $$M$$. However, it can get slow if we try to compute it for each value of $$M$$. After all, we are trying to find the smallest $$M$$ such that the number of solutions is greater than 1000000, and I have no idea how large $$M$$ would be. Thus, when we're iterating up, we can **increment by 100**, and then when we overshoot, we can slowly decrement our way down. This is will greatly reduce the number of times we need to compute the number of solutions. 

The two methods for recursively searching triples and computing the number of solutions is below. I use `numpy` to easily do vector-matrix multiplications.

```python
def genPythagoreanTriples(triple, maxSide, A, B, C):
    # Find min and second largest
    # value. Never the last value...
    if triple[0] == min(triple):
        minVal, secVal = triple[0], triple[1]
    else:
        minVal, secVal = triple[1], triple[0]
    # Base case...
    if min(triple) > maxSide or (secVal > maxSide and secVal > 2 * minVal):
        return
    yield np.sort(triple)
    # Multiply each matrix
    for matrix in [A, B, C]:
        for multTriple in genPythagoreanTriples(np.dot(matrix, triple), maxSide, A, B, C):
            yield multTriple
def calculateSols(triple, maxSide, A, B, C):
    s = 0
    for pythTriple in genPythagoreanTriples(triple, maxSide, A, B, C):
        print(pythTriple)
        # Check to see if the second value is
        # less than twice the lowest value.
        # Otherwise, we can't break up the second value
        # into two sides less than the lowest...
        if pythTriple[1] <= 2 * pythTriple[0]:
            # Loop through, keeping the minimum side
            # constant...
            # Number of solutions is secVal // 2 - (secVal - firstVal) + 1
            for i in range(1, maxSide // pythTriple[0] + 1):
                multTriple = i * pythTriple
                v1 = multTriple[1] // 2 - (multTriple[1] - multTriple[0]) + 1
                s += v1
        # Keep the second value constant,
        # breaking the lowest value into all
        # possible unique partitions...
        for i in range(1, maxSide // pythTriple[1] + 1):
            v2 = int((i * pythTriple)[0] // 2)
            s += v2
    return s
```

As for our main loop, we start with $$M=100$$ and work our way up as before.

```python
A = np.array([
    [1, -2, 2],
    [2, -1, 2],
    [2, -2, 3]
], dtype=object)
B = np.array([
    [1, 2, 2],
    [2, 1, 2],
    [2, 2, 3]
], dtype=object)
C = np.array([
    [-1, 2, 2],
    [-2, 1, 2],
    [-2, 2, 3]
], dtype=object)

triple = np.array([3,4,5], dtype=object)
maxSide = 100
maxSolutions = 1000000
numSolutions = 1
while numSolutions < maxSolutions:
    numSolutions = calculateSols(triple, maxSide, A, B, C)
    # Save time by increasing the side by 100
    maxSide += 100
# Now slowly decrease the maxSide until we're less
while numSolutions > maxSolutions:
    maxSide -= 1
    numSolutions = calculateSols(triple, maxSide, A, B, C)
# Add one because we went one
# over with the while loop.
print('M = {} with {} solutions.'.format(maxSide + 1, calculateSols(triple, maxSide + 1, A, B, C)))
```

Running it all together, we get an output of,

```
M = 1818 with 1000457 solutions.
7.8357412 seconds.
```

Thus, the first side length which has the number of solutions a million is **1818**. For me personally, this was an extremely satisfying problem to finish. Hope you had fun!