---
layout: post
title: "Problem 158 - Exploring strings for which only one character comes lexicographically after its neighbour to the left"
date: 2017-08-01 15:31
number: 158
mathjax: true
---

> Taking three different letters from the 26 letters of the alphabet, character strings of length three can be formed.
>
> Examples are 'abc', 'hat' and 'zyx'.
>
> When we study these three examples we see that for 'abc' two characters come lexicographically after its neighbour to the left. For 'hat' there is exactly one character that comes lexicographically after its neighbour to the left. For 'zyx' there are zero characters that come lexicographically after its neighbour to the left. 
>
> In all there are 10400 strings of length 3 for which exactly one character comes lexicographically after its neighbour to the left.
>
> We now consider strings of $$n\leq 26$$ different characters from the alphabet. For every $$n$$, $$p(n)$$ is the number of strings of length of $$n$$ for which exactly one character comes lexicographically after its neighbour to the left.
>
> What is the maximum value of $$p(n)$$?

<!--more-->

## Answer

This is essentially a counting problem. It's good to first find some properties of a string where exactly one character is lexicographically after the one to its left. This phrase is a fancy way of saying, "there is only one pair of letters which are in order." Well, what else does this tell us? **It tells us that the substrings on either side of the pair are in decreasing order**.

For example, suppose we had 5 letters: c, t, m, r, d. The string "tmcrd". The satisfying pair is "cr", and if you notice "tmc" and "rd" are both in decreasing order. This ensures there is exactly one pair of letters in order. Here's another thing we can note: **It does NOT matter what the initial group of letters are. The number of strings with one pair of ascending letters will be the same regardless.** Based on the descending property on either side, this should make sense since this only has to do with the ordering of letters, which stays constant with each group.

Given a group of $$n$$ letters, we can find a formula for $$c(n)$$, which is the number of strings that contain one pair of letters in ascending order. Note this isn't $$p(n)$$ which take **all possible groups**. We will just focus on one group for the moment, to keep things simple.

### Finding a formula for $$c(n)$$

To make it easier in finding a formula, our group of $$n$$ letters will always be the first $$n$$ letters of the alphabet.

There are some obvious values for the first couple of values of $$n$$. $$c(0) = c(1) = 0$$, because it is impossible to order anything with less than 2 letters. With 2 letters, they are either in order or out of order, so $$c(2) = 1$$ ("ab"). Now, when we add "c", because this is *after* both "a" and "b", we can place it at the beginning of "ab" and preserve the property. If the "c" comes to the middle, then both configurations work. If "c" is at the end, then the first two characters must be in reverse order i.e. "ba". In total, we have $$c(3) = 4$$, with the four strings being "abc", "acb", "bca", "bac". 

I'll do one more example for $$c(4)$$. Now we have a "d". As with $$n=3$$, we can prepend the "d" to each of the 4 strings previously, since this letter is greater than all 3. I'll go through each positional case. Remember that substrings on either need to be **decreasing**.

- *"d" in the 2nd position:* The 1st character needs to be less than "d". Well each of the other three is less, so this gives 3 strings.
- *"d" in the 3rd position:* The first 2 characters need to be in reverse order. We can **choose 2 letters from 3** a total of 3 ways. So this case also gives us 3 letters.
- *"d" in the last position*: The first 3 characters need to be reversed. This only gives us one string: "cbad".

In total, we have 11 strings: "dabc", "dacb", "dbca", "dbac", "adcb", "bdca", "cdba", "badc", "cadb", "cbda", "cbad". If you are still confused, I suggest you work out the value of $$c(5)$$.

Hopefully you see the pattern by now. I'll now keep things abstract. For a given $$n$$:

- Prepend the $$n$$th letter to each of the strings previously. This is $$c(n-1)$$.
- For the $$i$$th position in the string ($$i\geq 2$$), the $$i-1$$ letters before it need to be reversed. We can choose the letters that go here from the remaining group a total of $$\binom{n - 1}{i - 1}$$ ways.
- Add up the number of strings for each position of the $$n$$th letter from 2 up to $$n$$.

In conclusion, 


$$
\begin{aligned}
c(n) &= \binom{n-1}{1}+\binom{n-1}{2}+\cdots+\binom{n-1}{n}+c(n-1) 
\\ &= \sum_{i=1}^n\binom{n-1}{i}+c(n-1) 
\\ &= \boxed{2^{n-1}-1+c(n-1)}
\end{aligned}
$$


The last expression follows from a well-known formula that $$\sum_{i=0}^m\binom{m}{i} = 2^m$$ and that anything choose 0 is 1, so we subtract off 1.

### Finding a formula for $$p(n)$$

Once we have $$c(n)$$, finding a formula for $$p(n)$$ is actually simple. The second observation we made was that **$$c(n)$$ only depends on the number of letters, and not the letters themselves**. This means that two groups of 4 letters e.g. "abcd" and "wots" have the same value of $$c(4)=11$$.

Thus, the value of $$p(n)$$ takes into account the value of $$c(n)$$ for **all** groups of $$n$$ letters. Well then, how many distinct groups of $$n$$ letters are there? $$\binom{26}{n}$$! Therefore,


$$
p(n)=\binom{26}{n}c(n)
$$

## Implementation

The recursive nature of $$c(n)$$ and the simple formula for $$p(n)$$ makes calculating the maximum value really simple. We just create an array of the values of $$c(n)$$ and multiply each by $$\binom{26}{n}$$ to get the values of $$p(n)$$. We just take the max of this array and we are done! To calculate $$\binom{26}{n}$$, I use the `comb` function from `scipy.special`:

```python
from scipy.special import comb

# For a given group of letters, the number
# of configurations to get exactly one pair
# is constant between groups. Thus, all we
# have to do is find the number of
# configurations for one group and then
# multiply by 26 choose n.

# For the number of configurations, the formula
# is c(n) = 2^(n-1) - 1 + c(n - 1) where c(0) = 0
# c(1) = 2^0 - 1 + c(0) = 0

values = [0]
for n in range(1, 27):
    values.append(2 ** (n - 1) - 1 + values[-1])
# Now multiply each by 26 choose n
values = [int(value * comb(26, i)) for i, value in enumerate(values)]
print('Max value of', np.max(values), 'at n =', np.argmax(values))
```

Running this short loop gets us,

```
Max value of 409511334375 at n = 18
0.005198499999999995 seconds.
```

Thus, the maximum value of $$p(n)$$ is **409511334375**.