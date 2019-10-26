---
layout: post
title: "Problem 68 - Magic 5-gon ring"
date: 2019-08-13 17:40
number: 68
mathjax: true
---

## Question

Consider the following "magic" 3-gon ring, filled with numbers 1 to 6, and each line adding to nine.

![p068_1]({{site.url}}{{site.baseurl}}\assets\Images\p068_1.png)

Working **clockwise**, and starting from the group of three with the numerically lowest external node (4,3,2 in this example), each solution can be described uniquely. For example, the above solution can be described by the set: 4,3,2; 6,2,1; 5,1,3.

It is possible to complete the ring with four different totals: 9, 10, 11, and 12. There are eight solutions in total.

| Total | Solution Set        |
| ----- | ------------------- |
| 9     | 4,2,3; 5,3,1; 6,1,2 |
| 9     | 4,3,2; 6,2,1; 5,1,3 |
| 10    | 2,3,5; 4,5,1; 6,1,3 |
| 10    | 2,5,3; 6,3,1; 4,1,5 |
| 11    | 1,4,6; 3,6,2; 5,2,4 |
| 11    | 1,6,4; 5,4,2; 3,2,6 |
| 12    | 1,5,6; 2,6,4; 3,4,5 |
| 12    | 1,6,5; 3,5,4; 2,4,6 |

By concatenating each group it is possible to form 9-digit strings; the maximum string for a 3-gon ring is 432621513.

Using the numbers 1 to 10, and depending on arrangements, it is possible to form 16- and 17-digit strings. What is the maximum **16-digit** string for a "magic" 5-gon ring?

![p068_2]({{site.url}}{{site.baseurl}}\assets\Images\p068_2.png)

## Answer

From the way how the strings are formed, we can deduce that **the number 10 has to be on the outer ring**. Why? Well, the string is going to concatenate 15 numbers, 3 for each of the 5 arms. Numbers on the inner ring will be repeated twice, since they are part of two separate arms. Thus, if "10" is on the outer ring, then it will be present *once* in the string, which means the length of the string will 14 (one digit for 14 circles), plus 2 for the single "10", for a grand total of 16 characters.

So we know that "10" is on the outer ring. Now, we need a way to quickly go through all possible starting configurations of the ring, without falling to brute force. Note that we also shouldn't take into account two rings that differ by a rotation, as these are the same ring. 

We can loop through all sets of 5 unique numbers, that contain "10", to place in the outer ring. Remember that the ring strings are reported starting with the smallest number. We can then actually have this fact enforce our ordering of the number placements