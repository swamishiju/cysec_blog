+++
title = "Young Cryptography"
date = 2026-08-11
authors = ["Swaminath Shiju", "Sriram V"]
+++

# Young Cryptography UIUCTF26
### Description

The Princess has sent you an encrypted message, but The Enemy has stolen your secret key! Can you recover the message?

### Solution

The provided `chal.py` was a key exchange facilitated using sparse matrices and a crazy product function.

``` py
def my_prod(A, B):
    n = len(A)
    z = (0,) * n

    def f(a, b, c, x):
        return (max(b[0], c[0]) + x,) + tuple(
            min(b[k-1], c[k-1]) + max(b[k], c[k]) - a[k-1]
            for k in range(1, n)
        )

    r = [z] * (n + 1)
    for row in A + B:
        s = [z]
        for j, x in enumerate(row, 1):
            s.append(f(r[j-1], r[j], s[-1], x))
        r = s

    p = [[None] * (n + 1) for _ in range(n + 1)]
    for i in range(n + 1):
        p[i][n] = r[i]

    C = [[0] * n for _ in range(n)]
    for i in range(n-1, -1, -1):
        for j in range(n-1, i-1, -1):
            b = p[i][j+1]
            c = p[i+1][j] if i < j else b
            d = p[i+1][j+1]

            C[i][j] = C[j][i] = d[0] - max(b[0], c[0])
            p[i][j] = tuple(
                min(b[k], c[k]) +
                (max(b[k+1], c[k+1]) - d[k+1] if k+1 < n else 0)
                for k in range(n)
            )

    return C
```

[This challenge was solved organically ✨✨]

Initially we spent some time analyzing the product to see if some trivial
properties like linearity or commutativity were followed. This however didn't lead to anything.
Looking at the name of the challenge
again somewhere in the recesses of my mind I remembered a combinatorial object
I'd seen in a [Numberphile video](https://www.youtube.com/watch?v=vgZhrEs4tuk) called Young Tableau.

The definition of a young tableau (tableau in French means painting) is rather
simple, but like most things in this neck of math it is deceivingly so.

> A Young diagram (also called a Ferrers diagram, particularly when represented
using dots) is a finite collection of boxes, or cells, arranged in
left-justified rows, with the row lengths in non-increasing order. (taken from [Young_tableu](https://en.wikipedia.org/wiki/Young_tableau))

A young tableu is an objected obtained by filling in the boxes of the diagram
with symbols from a totally ordered alphabet (usually taken as some set of
numbers for brevity).

The tableu can further be called semi standard if the entries are non-decreasing along rows and strictly increasing down columns.

> TODO: Explain RSK
> TODO: Explain Plactic Monoids
> TOOD: Link papers


