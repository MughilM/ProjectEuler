---
layout: post
title: "Problem 19 - Counting Sundays"
date: 2015-08-07 20:43
mathjax: true
excerpt: Finding the number of Sundays that fall on the first of months in a given range.
number: 19
---

## Question

You are given the following information, but you may prefer to do some research yourself.

- 1 Jan 1900 was a Monday.

- Thirty days has September,

  April, June and November.

  All the rest have thirty-one,

  Saving February alone,

  Which has twenty-eight, rain or shine.

  And on leap years, twenty-nine.

- A leap year occurs on any year evenly divisible by 4, but not on a century unless it is divisible by 400.

How many Sundays fell on the first of the month during the twentieth century (1 Jan 1901 to 31 Dec 2000)?

## Answer

This problem is slightly made easier by the fact that we only have to keep track of the firsts of the months. There are 7 days per week, which rotate on a cycle. This is similar to a clock where the numbers 1-12 constantly, and the number 1 follows 12. "Clock arithmetic" brings to mind modular arithmetic. To make things easier, we can use the numbers 0-6 to represent days of the week from Sunday to Saturday. Using the number of days there are per month, we can figure out how many whole weeks there are, and how many days are left over using modular arithmetic.

For example, 1 Jan 1900 was a Monday, as stated in the problem. This equates to the number 1 in our numbering system. January has 31 days, which is 4 full weeks, plus 3 extra days. Another way of saying this is $$31 \equiv 3 \mod7$$. With these 3 extra days, this means that 1 Feb 1900 starts on $$1+3=4$$, or a Thursday. We can do this for each month in our range. Anytime the number is a 0 means it's a Sunday, and we have to count it. We'll have to hardcore the number of days per month in an array, and carefully check for leap years as well. It's also possible that after the addition, we obtain a number bigger than 6. In this case, we'll need to take mod 7 once again. Please see the code below:

```python
# Days will be numbered 0, 1, 2, ... -> Sunday, Monday, Tuesday, ...
# Months will be numbered 0-11.
# Given that 1 Jan 1990 is Monday
firstDay = 1
# We need to start counting from Jan 1 1901 though.
# 1900 was NOT a leap year, so there were 365 days...
firstDay += 365 % 7
# Array to hold number of days
# for each month from Jan to Dec
monthDays = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31]
numOfSundays = 0
for year in range(1901, 2001):
    for month in range(12):
        # Check if it's a sunday...
        if firstDay == 0:
            numOfSundays += 1
        # First check if it's a leap year.
        # We need to mod afterwards as well
        # Because there is a chance the addition
        # will take it over 7...
        if (month == 1) and ((year % 100 != 0 and year % 4 == 0) or (year % 400 == 0)):
            firstDay = (firstDay + 29 % 7) % 7
        else:
            firstDay = (firstDay + monthDays[month] % 7) % 7

print(numOfSundays)
```

Running the above code generates the output of,

```python
171
0.0008150123456790123 seconds.
```

  Thus, there are **171** Sundays that fall on the first of the month from January 1, 1901 to December 31, 2000.