---
layout: post
title: "Problem 32 - Pandigital products"
date: 2016-05-09 16:53
mathjax: true
number: 32
---

## Question

We shall say that an $$n$$-digit number is pandigital if it makes use of all the digits 1 to $$n$$ exactly once; for example, the 5-digit number, 15234, is 1 through 5 pandigital.

The product 7254 is unusual, as the identity, $$39\times 186=7254$$, containing multiplicand, multiplier, and product is 1 through 9 pandigital.

Find the sum of all products whose multiplicand/multiplier/product identity can be written as a 1 through 9 pandigital.

HINT: Some products can be obtained in more than one way so be sure to only include it once in your sum.

## Answer

We will need to cover all possible equations of multiplicand times multiplier equals product. Python has a convenient package `itertools`, which is home to the `permutations` method, which returns a generator that will go through each permutation of an iterable i.e. a string, list, tuple, etc. Therefore, we can use to loop through all 9 digit arrangements of the digits 1 to 9. Next, we need to place the multiplication sign and equals sign at all valid positions. The multiplication sign can be placed anywhere between the 1st and 8th digit. The reason is that we also need space for the equals sign, which comes after the multiplication sign.

For example, with the product given above, we would start from $$3\times 9=1867254$$ and end with $$3918672\times 5=4$$. However, we don't need to check the last product, because as we loop through locations to place the equals sign, the product will get smaller and smaller. Eventually, it will be less than the actual product, and then we can stop and check the next spot for the multiplication sign. For example, with the multiplication sign being after the 3, the last product we will check is $$3\times 918=67254$$, because the next product $$3\times 9186>7254$$. With this, we can skip a fair number of possibilities. 

Below is the code to run through all permutations and see if a multiplicand times multiplier equaling a product exists.

```python
products = set()
for perm in permutations('123456789'):
    # We place a multiplication and equals
    # sign at all possible places here.
    # Once the product goes above the RHS,
    # we can stop and test putting the
    # multiplication sign in the next spot.
    # The multiplication symbol can be placed
    # after the 1st digit, or before the
    # second to last digit (because we need equals)
    for i in range(1, len(perm) - 2):
        # Now we place an equals sign anywhere
        # from at least one digit after
        # the multiplicand to one before the result.
        # Once the product becomes larger than RHS,
        # we stop, because the RHS will only get smaller.
        for j in range(i + 1, len(perm) - 1):
            mCand = int(''.join(perm[:i]))
            mPlier = int(''.join(perm[i:j]))
            product = mCand * mPlier
            rhs = int(''.join(perm[j:]))
            if product > rhs:
                break
            elif product == rhs:
                products.add(product)

print(sum(products))
```

Notice we are adding the products to the set to avoid duplicates. Running the double for loop above results in an output of,

```python
45228
9.040594220951176 seconds.
```

Therefore, the sum of all products part of a pandigital product is **45228**. It might be possible to make the code a bit faster. We can note that it runs through each product twice, since multiplication is commutative. In that case, we may only need to run through a carefully calculated half of all the permutations.