---
layout: post
title: "Problem 42 - Coded triangle numbers"
date: 2016-05-18 20:31
mathjax: true
number: 42
---

## Question

The $$n^{\text{th}}$$ term of the sequence of triangle numbers is given by, $$t_n=\frac{1}{2}n(n+1)$$; so the first ten triangle numbers are:


$$
1,3,6,10,15,21,28,36,45,55,\dots
$$


By converting each letter in a word to a number corresponding to its alphabetical position and adding these values we form a word value. For example, the word value for SKY is $$19+11+25=55=t_{10}$$. If the word value is a triangle number then we shall call the word a triangle word.

Using [words.txt](https://projecteuler.net/project/resources/p042_words.txt) (right click and 'Save Link/Target As...'), a 16K text file containing nearly two-thousand common English words, how many are triangle words?

## Answer

Let $$w_{word}$$ by the word value for $$word$$. Essentially, the only thing we need to find is to see whether $$w_{word}$$ is a triangle number. If it is, then by using the quadratic formula,


$$
\begin{align*}
w_{word} &= \frac{1}{2}n(n+1) \\
n^2+n-2w_{word} = 0 \\
n &= \frac{-1 \pm \sqrt{1-4(1)(-2w_{word})}}{2}
\\ &=
\frac{\sqrt{8w_{word} + 1} - 1}{2}
\end{align*}
$$


Therefore, for $$w_{word}$$ to be a triangular number, that last line has to be an integer. Equivalently, that means that $$8w_{word}+1$$ has to be a square, which is simple enough to test. As a side note, the reason why I discarded the minus solution from the quadratic formula is that it would result in a negative number.

So, in the end, it's a simple loop over all the words, and checking if the word value matches our condition. For convenience I've renamed the file from `words.txt` to `problem042.txt`. Also, the file has each word in double quotes, so we'll actually need to remove them before we start looping. See the code below:

```python
with open("problem042.txt") as f:
    words = f.readlines()
# split so it becomes 1D array
words = words[0].split(',')
# Now traverse through the words and remove
# the double quotes on the ends
for i in range(len(words)):
    words[i] = words[i].replace('"', '')

letters = list("ABCDEFGHIJKLMNOPQRSTUVWXYZ")
count = 0
for i in range(len(words)):
    num = 0
    for j in range(len(words[i])):
        num += letters.index(words[i][j:j+1]) + 1
    if (8 * num + 1) ** 0.5 == int((8 * num + 1) ** 0.5):
        count = count + 1

print(count)
```

Running results in an output of,

```
162
0.011115430442436482 seconds.
```

Thus, there are **162** words in the file that are triangle words.