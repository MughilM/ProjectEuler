---
layout: post
title: "Problem 144 - Investigating multiple reflections of a laser beam"
date: 2017-08-17 03:38
number: 144
mathjax: true
---

> In laser physics, a "white cell" is a mirror system that acts as a delay line for the laser beam. The beam enters the cell, bounces around on the mirrors, and eventually works its way back out.
>
> - The specific white cell we will be considering is an ellipse with the equation $$4x^2+y^2=100$$.
>
>
> The section corresponding to $$-0.01\leq x\leq 0.01$$ at the top is missing, allowing the light to enter and exit through the hole.
>
> ![ellipse]({{site.url}}{{site.baseurl}}\assets\Images\p144_1.png) ![ellipseGIF]({{site.url}}{{site.baseurl}}\assets\Images\p144_2.gif)
>
> The light beam in this problem starts at the point (0.0, 10.1) just outside the white cell, and the beam first impacts the mirror at (1.4, -9.6).
>
> Each time the laser beam hits the surface of the ellipse, it follows the usual law of reflection "angle of incidence equals angle of reflection." That is, both the incident and reflected beams make the same angle with the normal line at the point of incidence.
>
> In the figure on the left, the red line shows the first two points of contact between the laser beam and the wall of the white cell; the blue line shows the line tangent to the ellipse at the point of incidence of the first bounce.
>
> The slope $$m$$ of the tangent line at any point $$(x,y)$$ of the given ellipse is: $$m=-4x/y$$.
>
> The normal line is perpendicular to this tangent line at the point of incidence.
>
> The animation on the right shows the first 10 reflections of the beam.
>
> How many times does the beam hit the internal surface of the white cell before exiting?

<!--more-->

## Answer

As you know, two points make a line. A vector and a point also makes a line. Thus, given that we have the first two points, and we know the slope of the **normal** line, we essentially need to take the vector and **reflect it across the normal line**. The reflected vector will be pointing in the direction of the next point. Then, we can solve for the intersection of the ellipse and the line. Laid out more explicitly:

- Find vector associated with incident line.
- Find the **normal** vector using the slope of the normal line and the reflection point.
- Reflect the incident vector across the normal vector to get the **reflected vector**.
- Find the equation of the reflected line, and find the other intersection point with the ellipse.

In terms of how to reflect a vector across another vector, I found this [Math StackExchange](https://math.stackexchange.com/questions/13261/how-to-get-a-reflection-vector) answer to be quite useful in explaining the simple formula. If $$\mathbf{d}$$ is the incident vector and $$\hat{\mathbf{n}}$$ is the *normalized* normal vector, then the reflection vector $$\mathbf{r}$$ is


$$
\mathbf{r} = \mathbf{d} - 2(\mathbf{d}\cdot\hat{\mathbf{n}})\hat{\mathbf{n}}
$$


where we use the dot product. Now that we know how to reflect, we have all the pieces to find the next point after the laser bounces off the inside surface.

## Finding the Next Point after Reflecting

For this to work, we need the previous two reflection points. We will call this $$(x_0, y_0)$$ and $$(x_1, y_1)$$. At the beginning in our case, we have $$(x_0, y_0) = (0.0, 10.1)$$ and $$(x_1, y_1) = (1.4, -9.6)$$. In any case, the vector associated with this line is simply $$\mathbf{v} = \langle x_1-x_0, y_1-y_0 \rangle$$.

### The normal vector

Next, we need the vector that is normal to the tangent line. The slopes of two perpendicular lines are negative reciprocals of each other. Since the slope of the tangent line is $$-4x/y$$, this means the slope of the normal line is $$y/4x$$. Remember a slope is "rise over run". Therefore, we can quickly convert this to a vector by $$\mathbf{n} = \langle 4x,y\rangle$$. For reflection purposes, we need to normalize this: $$\displaystyle \hat{\mathbf{n}}=\frac{\mathbf{n}}{\left\lVert \mathbf{n} \right\rVert}$$.

### The reflected vector

Using our formula from the stack exchange, the reflected vector $$\mathbf{r}$$ is


$$
\mathbf{r} = \mathbf{v} - 2(\mathbf{v}\cdot\hat{\mathbf{n}})\hat{\mathbf{n}}
$$


If you looked closely at the stack exchange, the incident vector was pointing towards the reflection point, and the normal vector was pointing away. When we calculate our normal vector, it may not always be pointing away due to the nature of the ellipse. However, this doesn't pose a problem, because we eventually convert these vectors into line equations, so the pointing direction doesn't matter.

### The intersection point

Here is where the bulk of the math takes place. We now convert the reflected vector into its corresponding line equation. Then we solve both the given ellipse equation and the line to get 2 intersection points. One of these will be the point we reflected off of, and the other will be the next reflection point we need. If we say $$\mathbf{r} = \langle r_1, r_2\rangle$$, and since we know a point on the line (the reflection point $$(x_1,y_1)$$) we can write the equation of a line in point-slope form, like so:


$$
y=m_r(x-x_1)+y_1
$$


where $$m_r=\frac{r_2}{r_1}$$.

We can plug this into the ellipse equation directly:


$$
\begin{aligned}
	4x^2+y^2 &= 100
	\\
	4x^2 + \left(m_r(x-x_1) + y_1\right)^2 &= 100
	\\
	4x^2 + m_r^2(x-x_1)^2 + 2m_r(x-x_1)y_1 + y_1^2 &= 100
	\\
	4x^2 + m_r^2x^2-2m_r^2xx_1+m_r^2x_1^2+2m_rxy_1-2m_rx_1y_1+y_1^2-100 &= 0
	\\
	x^2\left[4 + m_r^2\right] + x\left[2m_ry_1-2m_r^2x_1\right] + \left[m_r^2x_1^2 - 2m_rx_1y_1+y_1^2-100\right] &= 0
	\\
	x_2\big[ 4+m_r^2 \big] + x\big[ 2m_r(y_1-m_rx_1) \big] + \big[ (m_rx_1-y_1)^2-100 \big] &= 0
\end{aligned}
$$


Although messy, we get a nice quadratic of the form $$ax^2+bx+c=0$$ at the end, where


$$
\begin{aligned}
	a &= 4 + m_r^2
	\\
	b &= 2m_r\left(y_1-m_rx_1\right)
	\\
	c &= (m_r x_1 - y_1)^2 - 100
\end{aligned}
$$


All that's left is to do the well-known quadratic formula


$$
x = \frac{-b\pm\sqrt{b^2-4ac}}{2a}
$$


This will get us two $$x$$ values. One of these will be the point we just reflected off of, and so we need the other. There is no way to tell which sign we should use, as it depends on which side of the ellipse we are on. For the time being, we can calculate both and pick whichever is not our reflection point. Once we've picked our $$x$$ value, we just plug it back into our $$\mathbf{r}$$ line from above to get the corresponding $$y$$ value.

## Implementation

Now that we know how to get the next point, all that's left is to write the code for it. It is essentially just all the steps I have lined out in code form. We keep getting the next point, until the $$x$$ value of the point is between $$-0.1$$ and $$0.1$$, as this means it has escaped back into the hole. We keep track of the number of bounces. In order to see which $$x$$ value we pick, I used the `np.isclose` function. This function allows us to take into account floating point errors from the square roots, divisions, etc.

```python
def getNextPoint(p0, p1):
    v = p1 - p0
    n = np.array([4 * p1[0], p1[1]])
    n /= np.linalg.norm(n)
    r = v - 2 * np.dot(v, n) * n
    # Slope of the reflection line
    m = r[1] / r[0]
    # Quadratic coefficients
    a = 4 + m ** 2
    b = 2 * m * (p1[1] - m * p1[0])
    c = (p1[0] * m - p1[1]) ** 2 - 100
    # Calculate the two solutions...
    sols = [(-b - (b * b - 4 * a * c) ** 0.5) / (2 * a), (-b + (b * b - 4 * a * c) ** 0.5) / (2 * a)]
    # One of these is original point...
    if np.isclose(sols[0], p1[0]):
        xNew = sols[1]
    else:
        xNew = sols[0]
    yNew = m * (xNew - p1[0]) + p1[1]
    return np.array([xNew, yNew])


p0 = np.array([0, 10.1])
p1 = np.array([1.4, -9.6])
pNew = getNextPoint(p0, p1)
bounces = 1
while np.abs(pNew[0]) > 0.01 or np.abs(10 - pNew[1]) > 0.00002:
    pNew = getNextPoint(p0, p1)
    bounces += 1
    p0 = p1
    p1 = pNew

# Due to the while statement, it counted the exit as a "bounce",
# so subtract one...
print(f'The total number bounces inside the cell is {bounces - 1}')
```

Running this medium-sized code gets us an output of,

```
The total number bounces inside the cell is 354.
0.046985 seconds.
```

Therefore, the laser beam bounces off the wall **354** times before it exits back through the hole at the top.

