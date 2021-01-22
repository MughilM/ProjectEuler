---
layout: post
title: "Problem 191 - Prize Strings"
date: 2019-07-13 19:18
number: 191
mathjax: true
---

> A particular school offers cash rewards to children with good attendance and punctuality. If they are absent for three consecutive days or late on more than one occasion then they forfeit their prize.
>
> During the $$n$$-day period a trinary string is formed for each child consisting of L's (late), O's (on time), and A's (absent).
>
> Although there are eight-one trinary strings for a 4-day period that can be formed, exactly forty-three strings would lead to a prize:
>
> <pre>
>     OOOO OOOA OOOL OOAO OOAA OOAL OOLO OOLA OAOO OAOA
> OAOL OAAO OAAL OALO OALA OLOO OLOA OLAO OLAA AOOO
> AOOA AOOL AOAO AOAA AOAL AOLO AOLA AAOO AAOA AAOL
> AALO AALA ALOO ALOA ALAO ALAA LOOO LOOA LOAO LOAA
> LAOO LAOA LAAO
> </pre>
>
> How many "prize" strings exist over a 30-day period?

<!--more-->

## Answer

This is another counting problem. Essentially, we want all strings of length of $$n$$ where we **do not** have 3 consecutive A's **nor** more than one L's. The latter, on its own, is much easier to count. Thus, we can find all the strings that have 0 or 1 L's, and subtract off those with 3 consecutive A's. The function that will be our answer for $$n$$-digits will be $$a(n)$$.

### Less than 2 L's

This is the easiest part to count. We break this up into two cases: strings without any L's, and strings with exactly one L:

- **Zero L's**: Without any L's, our only option is a "O" or "A" for each letter. With $$n$$ letters, there are exactly $$2^n$$ such strings.
- **One L**: We can place the "L" in any one of $$n$$ spots. The other $$n-1$$ letters have to be either "O" or "A". Thus, there are $$n2^{n-1}$$ such strings

Together, we have $$2^n+n2^{n-1} = 2^{n-1}(n+2)$$ strings with less than two L's.

### Three consecutive A's AND less than 2 L's

From the number we calculated in the previous section, we have to subtract the number of strings that contain less than 2 L's AND 3 consecutive A's. Like before, we break it up into 2 cases, those without any L's and those with exactly one. Let $$f(n)$$ be the number of strings with no L's and 3 consecutive A's, and $$g(n)$$ be the number of strings with one L and 3 consecutive A's.

#### Finding $$f(n)$$

Assume we are looking at one such $$n$$-character string. Then it could look like exactly one of the following:

1. **Ends with "AAA"**: The first $$n-3$$ characters can be any of "O" or "A". There are $$2^{n-3}$$ of these.
2. **Ends with "O"**: The first $$n-1$$ characters must contain 3 consecutive A's and no L's. There are exactly $$f(n-1)$$ such strings.
3. **Ends with "OA"**: The first $$n-2$$ characters must contain 3 consecutive A's and no L's. There are $$f(n-2)$$ of these.
4. **Ends with "OAA"**: You probably notice the pattern by now. This set contains $$f(n-3)$$ strings.

You can convince yourself that these 4 groups are mutually exclusive and encompass all possible cases of the $$n$$-character string. Therefore, our formula for the number of strings with 3 consecutive A's and no L's is


$$
f(n)=2^{n-3}+f(n-1)+f(n-2)+f(n-3)=\boxed{2^{n-3}+\sum_{i=1}^3f(n-i)}
$$

#### Finding $$g(n)$$

This case is a little bit trickier because one L to worry about. But we can still break this into similar cases:

1. **Ends with "AAA"**: The first $$n-3$$ characters can be any of "O" or "A", *in addition to the fact that we need one L*. There are $$n-3$$ spots to place the L, and the other $$n-4$$ can be any of the other two. In total, there are $$(n-3)2^{n-4}$$.
2. **Ends with "O"**: Extremely similar to the $$f(n)$$ case. The first $$n-1$$ must be one of $$g(n-1)$$ strings. You can immediately see what the next two cases amount to.
3. **Ends with "OA"**: $$g(n-2)$$.
4. **Ends with "OAA"**: $$g(n-3)$$.
5. **Ends with "L"**: This is one of the extra cases we must consider. The string can also end in L. In this case, the first $$n-1$$ characters *must contain 3 consecutive A's*. How many strings is that? Well that's just the $$f(n-1)$$ we calculated in the previous section! Hopefully you can see where this is going...
6. **Ends with "LA"**: The first $$n-2$$ must have "AAA", which count for $$f(n-2)$$ strings.
7. **Ends with "LAA"**: $$f(n-3)$$.

Adding up all the cases, we get


$$
\begin{aligned}
	g(n) &= (n-3)2^{n-4}+\sum_{i=1}^3(g(n-i)+f(n-i))
	\\ &=
	(n-3)2^{n-4}+\sum_{i=1}^3g(n-i)+\sum_{i=1}^3 f(n-i)
	\\ &=
	(n-3)2^{n-4}+\sum_{i=1}^3 g(n-i)+ f(n)-2^{n-3}
	\\ &=
	\boxed{2^{n-4}(n-5)+\sum_{i=1}^3 g(n-i)}
\end{aligned}
$$

### Final amount

We are essentially done. We take the number of strings which have zero or one L, and subtract off those which contain 3 consecutive A's. In all, this is


$$
a(n)=2^{n-1}(n+2)-f(n)-g(n)
$$

### Implementation

Since $$f(n)$$ and $$g(n)$$ are recursive, we calculate all of these beforehand, and a single line to calculate the final amount is needed. Additionally, since $$g(n)$$ needs the current value of $$f(n)$$, we do that second.

```python
limit = 30
# Make the fn and gn arrays up until limit...
f = [0] * (limit + 1)
g = [0] * (limit + 1)
# fn = 2^(n-3) + (3 previous terms)
# gn = 2^(n-4)*(n-5) + (3 previous terms) + fn
for n in range(3, limit + 1):
    f[n] = 2 ** (n - 3) + sum(f[n - 3:n])
    g[n] = 2 ** (n - 4) * (n - 5) + sum(g[n - 3:n]) + f[n]

# Answer is 2^(n-1) * (n+2) - fn - gn
print(int(2 ** (limit - 1) * (limit + 2) - f[limit] - g[limit]))
```

Running this short code,

```
1918080160
7.449999999997736e-05 seconds.
```

Therefore, there are **1918080160** such prize strings for 30 days.