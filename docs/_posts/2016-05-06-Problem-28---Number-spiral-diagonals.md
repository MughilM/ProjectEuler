---
layout: post
title: "Problem 28 - Number spiral diagonals"
date: 2016-05-06 22:38
mathjax: true
number: 28
---

## Question

Starting with the number 1 and moving to the right in a clockwise direction a 5 by 5 spiral is formed as follows::

<pre style="text-align:center">
<span style="color:red">21</span> 22 23 24 <span style="color:red">25</span>
20  <span style="color:red">7</span>  8  <span style="color:red">9</span> 10
19  6  <span style="color:red">1</span>  2 11
18  <span style="color:red">5</span>  4  <span style="color:red">3</span> 12
<span style="color:red">17</span> 16 15 14 <span style="color:red">13</span>
</pre>

It can be verified that the sum of the numbers on the diagonals is 101.

What is the sum of the numbers on the diagonals in a 1001 by 1001 spiral formed in the same way?

## Answer

Let's add another step and make it a 7 by 7 spiral:

<pre style="text-align:center">
<span style="color:red">43</span> 44 45 46 47 48 <span style="color:red">49</span>
42 <span style="color:red">21</span> 22 23 24 <span style="color:red">25</span> 26
41 20  <span style="color:red">7</span>  8  <span style="color:red">9</span> 10 27
40 19  6  <span style="color:red">1</span>  2 11 28
39 18  <span style="color:red">5</span>  4  <span style="color:red">3</span> 12 29
38 <span style="color:red">17</span> 16 15 14 <span style="color:red">13</span> 30
<span style="color:red">37</span> 36 35 34 33 32 <span style="color:red">31</span>
</pre>

Note that the diagonal heading northeast has $$1,9,25,49,\dots$$. These are the squares of the odd numbers i.e. $$1^2,3^2,5^2,7^2,\dots$$. We can get the other 3 numbers at the other corners by subtracting the side length minus one once, twice, and three times. For example, $$25$$ is at the corner of the 5 by 5 circle. If we subtract $$5-1=4$$ from $$25$$, we get $$21$$, the number on the northwest diagonal. Subtracting two times and three times gets us $$17$$ and $$13$$ respectively. 

So our method is loop through each odd number until 1001, and add that number squared, as well as the other 3 numbers described in the preceding paragraph. We will start from 3, as to not triple count the 1 in the center.

```
spiralLim = 1001
s = 0
for n in range(3, spiralLim + 1, 2):
    s += n ** 2
    # Add the other three numbers:
    s += n ** 2 - (n - 1)
    s += n ** 2 - 2 * (n - 1)
    s += n ** 2 - 3 * (n - 1)
   
# Add 1 from center
s += 1

print(s)
```

 Running the above results in,

```python
669171001
0.0023656268259237617 seconds.
```

Therefore, **669171001** is our final sum.

### Bonus!

This is one of the few problems that can be done by hand, although it's a little messy. Let the spiral loop we are on by denoted $$n$$. Recall that for each loop in the spiral, we add $$n^2$$, and the three numbers that have been subtracted by $$n-1$$ once, twice, and three times. Therefore, in each loop, the sum is


$$
\begin{align*}
S_n &= n^2+n^2-(n-1)+n^2-2(n-1)+n^2-3(n-1)
\\ &=
4n^2-6(n-1)
\\ &=
4n^2-6n+6
\end{align*}
$$


So, in actuality, instead of the 4 lines we had in the for loop, we could have had one line where add `s` by this sum. However, there are more steps we could do.

An odd number $$n$$ can be written in the form $$2k+1$$. In our problem, $$1\leq k\leq 500$$. Thus, we can write a summation and evaluate it as follows:


$$
\begin{align*}
S &= \sum_{k=1}^{500}S_{2k+1} +1
\\ &=
\sum_{k=1}^{500}\left( 4(2k+1)^2 - 6(2k+1) + 6 \right) +1
\\ &=
\sum_{k=1}^{500}\left( 4\left( 4k^2+4k+1 \right) - 12k - 6 + 6 \right) +1
\\ &=
\sum_{k=1}^{500}\left( 16k^2+16k+4-12k \right) +1
\\ &=
\sum_{k=1}^{500}\left( 16k^2+4k+4 \right) +1
\\ &=
16\sum_{k=1}^{500}k^2+4\sum_{k=1}^{500}k+\sum_{k=1}^{500}4 +1
\\ &=
16\left( \frac{500(500+1)(2(500)+1)}{6} \right)+4\left( \frac{500(500+1)}{2} \right)
	+ 4(500) +1
\\ &=
16(41\,791\,750)+4(125\,250) + 2000 +1
\\ &=
\boxed{669\,171\,001}
\end{align*}
$$
