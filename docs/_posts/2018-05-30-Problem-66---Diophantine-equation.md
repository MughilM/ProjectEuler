---
layout: post
title: "Problem 66 - Diophantine equation"
date: 2018-05-30 14:57
number: 66
mathjax: true
---

Consider quadratic Diophantine equations of the form: $$x^2-Dy^2=1$$

For example, when $$D=13$$, the minimal solution in $$x$$ is $$649^2-13\times 180^2 = 1$$.

It can be assumed that there are no solutions in positive integers when $$D$$ is square.

By finding minimal solutions in $$x$$ for $$D = \{2,3,5,6,7\}$$, we obtain the following: 

$$\begin{aligned}
3^2-2\times2^2 &= 1
\cr
2^2-3\times1^2 &= 1
\cr
\color{red}{9}^2-5\times4^2 &= 1
\cr
5^2-6\times2^2 &= 1
\cr
8^2-7\times3^2 &= 1
\end{aligned}$$

Hence, by considering minimal solutions in $$x$$ for $$D\leq 7$$, the largest $$x$$ is obtained when $$D=5$$.

Find the value of $$D\leq 1000$$ in minimal solutions of $$x$$ for which the largest value of $$x$$ is obtained.

## Answer

Okay, so to make things clear after all the largest and minimal terminology, for a **fixed** $$D$$ (non-square), there are an **infinite** number of integer pairs $$(x,y)$$ that satisfy the equation above. This question is asking us to find the solution with the **smallest** $$x$$ for each $$D$$, and then report which $$D$$ has the **largest** "smallest" $$x$$ solution. Hopefully that clears up some confusion.

Now to the problem. A Diophantine equation is any polynomial equation where we only want integer solutions. A plethora of research has gone into these types of equations for so long because neat properties can be discovered. For this problem, I discovered that this Diophantine equation is actually known as Pell's equation. In fact, Pell's equation made its first appearance way back in 400 BC, and as such, simple algorithms are already in place to solve them.

The [Wikipedia article](https://en.wikipedia.org/wiki/Pell%27s_equation) lists many ways to solve it, but given what we have encountered so far, the following is the easiest:

> Let $$\frac{h_i}{k_i}$$ denote the sequence of convergents to the regular continued fraction for $$\sqrt{n}$$. This sequence is unique. Then the pair $$(x_1, y_1)$$ solving Pell's equation and minimizing $$x$$ satisfies $$x_1 = h_i$$ and $$y_1 = k_i$$ for some $$i$$.

The article uses $$n$$ in place of $$D$$. So to solve this, all we have to do is keep calculating the convergents for $$\sqrt{D}$$ until we encounter a pair which solves the equation. This is guaranteed to be the pair that minimizes $$x$$. Both problems [64]({{ site.baseurl }}{% post_url 2017-06-19-Problem-64---Odd-period-square-roots %}) and [65]({{ site.baseurl }}{% post_url 2017-06-19-Problem-65---Convergents-of-e %}) deal with computing convergents of continued fractions. We can use those algorithms in this problem, and test each convergent to see if it solves the equation.

I have one method which returns the periodic coefficients of the continued fraction of $$\sqrt{D}$$, and a loop which continuously calculates $$h_i$$ and $$k_i$$ above for testing the equation. Each solution pair is saved and then the maximum $$x$$ is grabbed later.

```python
def periodCoeffs(S):
    m = 0
    d = 1
    a0 = int(S ** 0.5)
    coeffs = [a0]
    a = a0
    while a != 2 * a0:
        m = d * a - m
        d = (S - m ** 2) // d
        a = int((a0 + m) / d)
        coeffs.append(a)
    return coeffs

# Diophantine equations of the form x^2 - Dy^2 = 1 are
# known as Pell's equations. As long as D is not square,
# a solution always exists. The minimizing x solution
# can be derived by testing successive convergents
# of the continued fraction of sqrt(D). This is
# what I'll be doing here.

maxD = 1000
xySols = np.zeros((maxD, 3), dtype=object)
xySols[:, -1] = np.arange(1, maxD+1)

for D in range(2, maxD + 1):
    # If D is not a perfect square
    sqrtD = D ** 0.5
    if sqrtD != int(sqrtD):
        # Find the coefficients of the
        # continued fraction
        aCo = periodCoeffs(D)
        hP = aCo[0]
        kP = 1
        h = aCo[1] * hP + 1
        k = aCo[1]
        n = 2
        while h ** 2 - D * k ** 2 != 1:
            an = aCo[(n - 1) % (len(aCo) - 1) + 1]
            temphP, tempkP = hP, kP
            hP, kP = h, k
            h = an * h + temphP
            k = an * k + tempkP
            n += 1
        # We're here when a solution has been found.
        # It has been proven that this solution minimizes x
        xySols[D - 1, :2] = [h, k]
        print('\rD: ' + str(D) + '/' + str(maxD), end='')
print()
maxX = np.argmax(xySols[:, 0])
print('The max x occurs when D = {} with (x, y) = ({}, {}).'
      .format(maxX + 1, xySols[maxX, 0], xySols[maxX, 1]))
```

Running the loop above results in an output of,

```
D: 1000/1000
The max x occurs when D = 661 with (x, y) = (16421658242965910275055840472270471049, 638728478116949861246791167518480580).
```

Thus, the largest minimal solution in $$x$$ occurs when $$D = \boxed{661}$$.