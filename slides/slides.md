<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" -->
# CMPT231
## Lecture 8: ch15
### Dynamic Programming

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" -->
## Devotional

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" -->
## Outline for today
+ **Dynamic programming**
  + **Rod-cutting** problem
  + Optimal **substructure**
  + Recursive, top-down, **bottom-up** solutions
+ **Fibonacci** sequence
+ **Matrix-chain** multiplication
+ Longest common **subsequence**
+ Shortest unweighted **path**
+ Optimal **binary search tree**

---
## Optimisation
+ Large class of **real-world** problems consisting of
  + Finding the **max** (or min) value of some **goal** (cost) function
    <br/> over some **search space**
+ **Search space**: may be *discrete* or *continuous*
  + **Dimension** of space may be *low* or very *high* (\`10^6\` or more)
+ **Goal** function: may be *analytic* or a *black-box*
  + Can we compute its *derivatives*?

<div class="imgbox"><div style="flex:3">
<strong>Exhaustive</strong> search usually
way too <em>slow</em>
</div><div>
![Saddle point between maxima, [Wikimedia](https://commons.wikimedia.org/wiki/File%3ASaddle_Point_between_maxima.svg)](static/img/Saddle_Point_between_maxima.svg)
</div></div>

---
## Dynamic programming
+ "Programming" here means **tables**
  + (e.g., linear "programming")
+ A form of **divide-and-conquer**, but
  + Store solutions to **sub-problems** for reuse
+ Efficiency depends on:
  + Optimal **substructure**
  + **Overlapping** sub-problems
+ Thought **process** to design solution:
  + **Recurrence** yields a recursive **top-down** solution (*inefficient*)
  + Top-down with **memoisation** (save *sub-results*)
  + **Bottom-up** (solve *smaller* sub-problems first, eliminate recursion)

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" -->
## Outline for today
+ Dynamic programming
  + **Rod-cutting problem**
  + **Optimal substructure**
  + Recursive, top-down, bottom-up
+ Fibonacci sequence
+ Matrix-chain multiplication
+ Longest common subsequence
+ Shortest unweighted path
+ Optimal binary search tree

---
## Rod-cutting problem
+ Steel **rods** of length *i* sell for $\`p\_i\` each (1&le;i&le;n)
+ **Cut** a rod of length *n* so as to maximise **revenue**
  + Assume **cuts** are free
+ Input: **price table** p = *[ 1, 5, 8, 9 ]*
  + So in this case rod **length** n = *4*
  + **Exhaustive**:
    $9, 8+1, 1+8, 5+5, 5+1+1, 1+5+1, 1+1+5, 1+1+1+1
  + **Optimal** solution: two pieces of length 2: $5+5
+ For fixed *p*, let \`r\_n\` be optimal **revenue** for length *n*
  + In this case, \`r\_n = 10\`

---
## Rod-cut: example

| i: |  1 |  2 |  3 |  4 |  5 |  6 |  7 |  8 |
|----|----|----|----|----|----|----|----|----|
| p: |  1 |  5 |  8 |  9 | 10 | 17 | 17 | 20 |

|   n:  |    0   | 1 | 2 | 3 |  4  |  5  |  6 |      7     |  8  |
|-------|--------|---|---|---|-----|-----|----|------------|-----|
| r[n]: |    0   | 1 | 5 | 8 |  10 |  13 | 17 |     18     |  22 |
| cuts: | (base) | 1 | 2 | 3 | 2+2 | 2+3 |  6 | 1+6, 2+2+3 | 2+6 |

May have **multiple** optimal solutions, with **same** \`r\_n\`

---
## Rod-cut: substructure
+ Optimise **one cut** at a time, left to right
  + Assume the first piece **won't** be cut again
+ Optimise **revenue** \`r\_n\` by considering possible cuts:
  + cut of length *1*: \`r\_n = p\_1 + r\_(n-1)\`
  + cut of length *2*: \`r\_n = p\_2 + r\_(n-2)\`
  + ...
  + cut of length *n*, i.e., **no** cuts: \`r\_n = p\_n\`
+ **Recurrence** relation: \`r\_n = max\_(1<=i<=n)( p\_i + r\_(n-i) )\`
+ Decomposes overall task into **subproblems** \`r\_i\`
+ Translates directly into a **recursive** solution

---
## Requirements for dyn prog
+ To use **dynamic programming**, we need two properties:
+ **Optimal substructure**:
  + Optimal solution to **subproblem** <br/>
    results in optimal solution to **overall** problem
  + I.e., any optimal solution can be <br/>
    **composed** of solutions to subproblems
+ **Overlapping subproblems**:
  + Subproblems appear in **multiple** branches of recursion tree
  + Allows **reuse** of solutions, giving us efficiency

---
## Rod-cut: optim substruct
+ Let \`A\_n\` be an optimal solution for **entire** length *n*
  + Let *i* be location of **first** cut in \`A\_n\`
  + Let \`A\_(n-i)\` be the **remaining** cuts in \`A\_n\`
+ **Claim**: \`A\_(n-i)\` is optimal for length *n-i*
  + Assume **not**: let \`B\_(n-i)\` be a **better** solution for *n-i*
    + revenue( \`B\_(n-i)\` ) &gt; revenue( \`A\_(n-i)\` )
  + Then we can **improve** on \`A\_n\` by combining this with *i*:
    + Let \`B\_n = [i, B\_(n-i)]\`, then
    + rev( \`B\_n\` ) = p[i] + rev( \`B\_(n-i)\` )
      &gt; p[i] + rev( \`A\_(n-i)\` ) = rev( \`A\_n\` )
  + This **contradicts** that \`A\_n\` was optimal for length *n*.
+ This proves **optimal substructure** for rod-cutting

---
## Overlapping subproblems
+ Optimal substructure shows recursive solution is **correct**
+ To get **efficiency** of dynamic programming, <br/>
  we also need to **reuse** subproblems
+ **Taxonomy** of subproblems:
  + Index subproblems by **length** of rod (*n*)
+ **Reuse** solutions to subproblems:
  + A solution for length *5* works **anywhere** within longer rods
    + Only depends on **length**, not **location**
  + So solutions to **small** rods like *n=2* can be reused many times
    + Saves a **lot** of computation!

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" -->
## Outline for today
+ Dynamic programming
  + Rod-cutting problem
  + Optimal substructure
  + **Recursive, top-down, bottom-up solutions**
+ Fibonacci sequence
+ Matrix-chain multiplication
+ Longest common subsequence
+ Shortest unweighted path
+ Optimal binary search tree

---
## (1) Recursive top-down
```
def cutRod( p, n ):
  if ( n < 1 ): return 0
  q = -infinity
  for i = 1 to n:
    q = max( q, p[ i ] + cutRod( p, n-i ) )
  return q
```

+ **Naive** implementation of recurrence
+ Recursion **tree**?
+ T(n) = \`2^n\` (#15.1-1)
  + Increase *n* by 1 &rArr; **double** run time!
+ E.g., *cutRod( p, 2 )* run many times

---
## (2) Top-down w/memoisation
```
revenue = array[ 0 .. n ] of -infinity
revenue [ 0 ] = 0
def cutRod( p, n ):
  if revenue[ n ] != -infinity:
    return revenue[ n ]
  for i = 1 to n:
    revenue[ n ] = max( revenue[ n ], p[ i ] + cutRod( p, n-i ) )
  return revenue[ n ]
```

+ **Memoisation**: cache previously-computed results
  + Need to **reset** cache for each new price table *p*
+ *cutRod( p, n )* only computed **once** for each *n*
  + if result not in cache, takes *&Theta;(n)* to compute
  + Complexity: \`sum\_i Theta(i) = Theta(n^2)\`
+ Can we eliminate the **recursion**?

---
## (3) Bottom-up (dyn prog)
```
def cutRod( p, n ):
  revenue = array[ 0 .. n ] of -infinity
  revenue[ 0 ] = 0
  for j = 1 to n:
    for i = 1 to j:
      revenue[ j ] = max( revenue[ j ], p[ i ] + revenue[ j-i ] )
  return revenue[ n ]
```

+ Start from **smaller** subproblems, caching as we go
+ Doubly-nested **for** loop computes each *cutRod( j )*
+ **Sequence** subproblems to satisfy dependencies
+ Complexity: \`sum\_i Theta(j) = Theta(n^2)\`

---
## Subproblem graph
<div class="imgbox"><div style="flex:4"><ul>
<li> <strong>Nodes</strong> are the subproblems (e.g., <em>cutRod( j )</em>)</li>
<li> <strong>Arrows</strong> show <em>dependencies</em>: <ul>
  <li>Which other nodes are needed to compute each node</li>
  <li> Like recursion <strong>tree</strong>, but collapsing reused nodes </li>
  </ul></li>
<li> <strong>Top-down</strong>: performs a <em>depth-first</em> search down to leaves</li>
<li> <strong>Bottom-up</strong>: must <em>sequence</em> nodes
  to resolve dependencies before reaching a node</li>
<li> <strong>Complexity</strong> is generally &Theta;( <em>#nodes</em> + <em>#arrows</em> )</li>
</ul></div><div>
![node graph](static/img/Fig-15-4.svg)
</div></div>

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" -->
## Outline for today
+ Dynamic programming
  + Rod-cutting problem
  + Optimal substructure
  + Recursive, top-down, bottom-up
+ **Fibonacci sequence**
+ Matrix-chain multiplication
+ Longest common subsequence
+ Shortest unweighted path
+ Optimal binary search tree

---
## Fibonacci sequence
Recall: \`F\_n = F\_(n-1) + F\_(n-2)\`, with \`F\_0 = F\_1 = 1\`

**Closed-form** solution: &Theta;(1)

```
def fib( n ):
  return round( pow( phi, n ) )
```

**Naive** top-down: \`Theta(2^n)\`

```
def fib( n ):
  if ( n < 2 ): return 1
  return fib( n-1 ) + fib( n-2 )
```

---
## Fibonacci: dynamic prog
**Top-down** with memo: &Theta;(n)

```
c = array[ 0 .. n ] of -1
c[ 0 ] = c[ 1 ] = 1
def fib( n ):
  if ( c[ n ] > 0 ): return c[ n ]
  c[ n ] = fib( n-1 ) + fib( n-2 )
  return c[ n ]
```

**Bottom-up** (dynamic programming): &Theta;(n)

```
def fib( n ):
  c = array[ 0 .. n ] of -1
  c[ 0 ] = c[ 1 ] = 1
  for j = 2 to n:
    c[ j ] = c[ j-1 ] + c[ j-2 ]
  return c[ n ]
```

Subproblem **graph**?

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" -->
## Outline for today
+ Dynamic programming
  + Rod-cutting problem
  + Optimal substructure
  + Recursive, top-down, bottom-up
+ Fibonacci sequence
+ **Matrix-chain multiplication**
+ Longest common subsequence
+ Shortest unweighted path
+ Optimal binary search tree

---
## Matrix-chain multiplication
+ Given a **chain** of *n* matrices to multiply:
  + \`A\_1 \*\* A\_2 \*\* A\_3 \*\* ... \*\* A\_n\`
  + num **columns** of *left* matrix = num **rows** of *right* matrix
  + \`(p\_0 xx p\_1)(p\_1 xx p\_2) ... (p\_(n-1) xx p\_n)\`
+ All **parenthesisations** are equivalent, <br/>
  but which **minimises** number of operations?
  + Recall dimensions of product matrix: \`(p xx q)(q xx r) = (p xx r)\`
+ **Input** is a list of matrix dimensions: \`{p\_i}\_0^n\`
+ e.g.: (*5 x 500*) (*500 x 2*) (*2 x 50*):
  + \`(A\_1 A\_2)A\_3\`: (5)(500)(2) + (5)(2)(50) = *5500* ops
  + \`A\_1(A\_2 A\_3)\`: (500)(2)(50) + (5)(500)(50) = *175000* ops
+ **Exhaustive** search of parenthesisations takes \`Theta(2^n)\`

---
## Optimal substructure
+ Let *c(i, j)* be min **cost** to multiply \`A\_i, ..., A\_j\`
+ Consider one **split** at a time (like *rod-cut*)
+ If the chain from *i* to *j* is split at *k*, the cost is:
  + \`c(i,j) = c(i,k) + c(k+1,j) + p\_(i-1) p\_k p\_j\` (matrix mult)
+ **Naive** solution uses *2n* recursive calls per loop: \`Theta(2^n)\`

```
def matChain( p, i, j ):
  if (i == j): return 0
  cost = infinity
  for k = i to j-1:
    cost = min( cost,
      matChain( p, i, k ) + matChain( p, k+1, j ) + p[ i-1 ] * p[ k ] * p[ j ] )
  return cost
```

---
## Bottom-up solution
+ Index subproblems by both **start** (*i*) and **end** (*j*):
  + Taxonomy is a 2D **grid** of nodes, not 1D line
+ Save minimal **cost** in matrix *c[i, j]*
+ Save **split** point *k* in matrix *s[i, j]*
+ p = *[ 30, 35, 15, 5, 10, 20, 25 ]* (n=7), at (i, j) = *(2, 5)*, k = *3*:
  + c[2, 5] = *c[2, 3]* + *c[4, 5]* + *35x5x20* = 7125

![matrix-chain](static/img/Fig-15-5.svg)

---
```
def matChain( p ):
  n = length(p) - 1
  c = array[ 1 .. n ][ 1 .. n ] of 0
  s = array[ 1 .. n-1 ][ 2 .. n ]
  for len = 2 to n:
    for i = 1 to n - len + 1:
      j = i + len - 1
      c[ i, j ] = infinity
      for k = i .. j-1:
        q = c[ i, k ] + c[ k+1, j ] + p[ i-1 ] * p[ k ] * p[ j ]
        if q < c[ i, j ]:
          c[ i, j ] = q
          s[ i, j ] = k
```

![matrix-chain](static/img/Fig-15-5.svg)

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" -->
## Outline for today
+ Dynamic programming
  + Rod-cutting problem
  + Optimal substructure
  + Recursive, top-down, bottom-up
+ Fibonacci sequence
+ Matrix-chain multiplication
+ **Longest common subsequence**
+ Shortest unweighted path
+ Optimal binary search tree

---
## Longest common subsequence
+ Given two **sequences**: \`X={x\_i}\_1^m, Y={y\_i}\_1^n\`
  + Find longest **subsequence** common to both X and Y
  + Need not be **consecutive**, but must be in **order**
+ E.g.: LCS("*springtime*", "*pioneer*") = "*pine*"
  + LCS("*horseback*", "*snowflake*") = "*oak*"
  + LCS("*heroically*", "*scholarly*") = "*holly*"
+ **Exhaustive** check:
  + for each of the \`2^m\` **subsequences** of *X*,
  + **check** (in &Theta;(n) time) if it's a subsequence of *Y*

---
## Optimal substructure
+ Let \`X\_k = {x\_i}\_1^k\` represent a **prefix** of *X*
+ Let \`Z = {z\_i}\_1^k\` be any LCS of *X* and *Y*
+ **Theorem (part 1)**: If \`x\_m = y\_n\`, then <br/> \`z\_k = x\_m = y\_n\`
  and \`Z\_(k-1)\` is an **LCS** of \`X\_(m-1)\` and \`Y\_(n-1)\`
+ **(part 2)**: If \`x\_m != y\_n\` and \`z\_k != x\_m\`, then <br/>
  *Z* is an **LCS** of \`X\_(m-1)\` and Y
+ **(part 3)**: If \`x\_m != y\_n\` and \`z\_k != y\_n\`, then <br/>
  *Z* is an **LCS** of X and \`Y\_(n-1)\`
+ This theorem says that an LCS of two sequences contains
  (as prefix) an LCS of prefixes of the two sequences

---
## Proof of optimal substruct (part 1)
+ Assume *Z* is an LCS of *X* and *Y*, and \`x\_m = y\_n\`
+ **Part 1a**: Show \`z\_k = x\_m = y\_n\`:
  + Assume **not**: then create *Z'* by **appending** \`x\_m\` to *Z*:
    + i.e., let \`Z' = (z\_1, ..., z\_k, x\_m)\`
  + *Z'* is also a subsequence of *X* and *Y*, and it's **longer** than *Z*
  + This **contradicts** assumption that *Z* was an LCS of *X* and *Y*
+ **Part 1b**: Show \`Z\_(k-1)\` is an LCS of \`X\_(m-1)\` and \`Y\_(n-1)\`:
  + It's certainly a common subsequence (it just drops \`z\_k\`)
  + If there existed a **longer** subseq *W* (length &gt; *k-1*), <br/>
    then we could create *W'* by **appending** \`x\_m\` to *W*
  + Now *W'* is a subseq of *X* and *Y*, and it's **longer** than *Z*
    (length &gt; *k*)
  + This **contradicts** assumption that *Z* was an LCS of *X* and *Y*

---
## Proof of opt substruct (parts 2-3)
+ Assume *Z* is an LCS of *X* and *Y*, and \`x\_m != y\_n\`
+ **Part 2** (\`z\_k != x\_m\`): Show Z is an LCS of of \`X\_(m-1)\` and Y.
  + Let *W* be a subseq of \`X\_(m-1)\` and Y, with length &gt; *k*
  + Then *W* is also a subseq of *X* and *Y*, **longer** than *Z*
  + This **contradicts** assumption that *Z* was an LCS of *X* and *Y*
+ **Part 3** (\`z\_k != y\_n\`) is **symmetric**
+ This concludes proof of **optimal substructure** for LCS

---
## LCS recurrence
+ Let *c[i, j]* = length of LCS of *X* and *Y*.  Then
+ \`c[i,j] = { (0, if i=0 or j=0), (c[i-1,j-1]+1, if (i,j)>0 and x\_i = y\_j),
  (max(c[i-1,j],c[j,i-1]), if (i,j)>0 and x\_i != y\_j) :} \`
+ LCS only gets **extended** by a character in case *2*
+ e.g., LCS("bozo", "bat")

![LCS example: bozo, bat](static/img/LCS-bozo-bat.png)

---
## LCS solution
```
def LCSLength( x, y ):
  ( m, n ) = ( length( x ), length( y ) )
  b[ 1 .. m ][ 1 .. n ] = new array
  c[ 0 .. m ][ 0 .. n ] = 0             # case 1 (init)

  for i = 1 to m:
    for j = 1 to n:
      if x[ i ] == y[ j ]:              # case 2: add a letter
        c[ i, j ] = c[ i-1, j-1 ] + 1
        b[ i, j ] = "UL"
      elif c[ i-1, j ] >= c[ i, j-1 ]:  # case 3a: go up a row
        c[ i, j ] = c[ i-1, j ]
        b[ i, j ] = "U"
      else:                             # case 3b: go left a col
        c[ i, j ] = c[ i, j-1 ]
        b[ i, j ] = "L"
```

**Complexity**: *&Theta;(mn)*

---
## LCS example
LCS( "*spanking*", "*amputation*" ):

![LCS example: spanking, amputation](static/img/LCS-spanking-amputation.png)
<!-- .element: style="width: 50%" -->

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" -->
## Outline for today
+ Dynamic programming
  + Rod-cutting problem
  + Optimal substructure
  + Recursive, top-down, bottom-up
+ Fibonacci sequence
+ Longest common subsequence
+ Matrix-chain multiplication
+ **Shortest unweighted path**
+ **Optimal binary search tree**

---
## Shortest/longest path
+ Given an unweighted **graph** (*nodes* + *edges*)
  + Find **shortest** path between given nodes *u* and *v*
+ Optimal **substructure**:
  + If path is **split** at node *w*, then
  + **Concatenating** shortest paths *u* &rarr; *w* and *w* &rarr; *v*
  + Yields a **shortest** path from *u* to *v* through *w*

<div class="imgbox"><div style="flex:3"><ul>
<li> What about <strong>longest</strong> path <em>u</em> &rarr; <em>v</em>?</li>
  <li> Obviously need to rule out <strong>cycles</strong></li>
  <li> <strong>Concatenate</strong> <em>longest(u, w)</em> + <em>longest(w, v)</em>?</li>
  <li> <strong>Doesn't</strong> work!  Might not be <strong>longest</strong> <em>u</em> &rarr; <em>v</em>,</li>
  <li> and might have <strong>cycles</strong></li>
</ul></div><div>
![Counter-example for longest path](static/img/Fig-15-6.svg)
</div></div>

---
## Optimal BST
+ Given sorted **keys** \`{k\_i}\_1^n\` and **probabilities** \`{p\_i}\_1^n\`
  + Build tree to minimise expected **search cost**
+ Recall cost for **successful** search is *&Theta;(h(k))* (depth of key)
+ To handle **unsuccessful** searches:
  + Add **dummy** keys \`{d\_i}\_0^n\` as leaves
  + Dummy key \`d\_i\` represents entire **interval** \`(k\_(i-1), k\_i)\`
  + Input \`q\_i\` as **probability** of \`d\_i\`
+ Probabilities over all search keys: \`sum p\_i + sum q\_i = 1\`
+ Expected **search cost** =
  \` sum (h(k\_i)+1)p\_i + sum(h(d\_i)+1)q\_i\`

---
## Optimal substructure
+ Consider one **split** at a time: choice of **root**
+ Given keys \`k\_i, ..., k\_j\`:
  + Consider making \`k\_r\` the **root** (*i* &le; *r* &le; *j*)
  + Recurse on **left** subtree: \`k\_i, ..., k\_(r-1)\`
  + Recurse on **right** subtree: \`k\_(r+1), ..., k\_j\`
+ **Demoting** a subtree increments **depth** to each node
  + Increases **search cost** by
    \`w(i,j) = sum\_(m=i)^j p\_m + sum\_(m=i-1)^j q\_m\`
+ So **cost** is \`e(i,j) = min\_(r=i...j) [ e(i, r-1) + e(r+1, j) + w(i,j) ]\`

---
## Optimal BST example

| i |   0  |   1  |   2  |   3  |   4  |   5  |
|---|------|------|------|------|------|------|
| p |   -  | 0.15 | 0.10 | 0.05 | 0.10 | 0.20 |
| q | 0.05 | 0.10 | 0.05 | 0.05 | 0.05 | 0.10 |

![Optimal BST example](static/img/Fig-15-10.svg)

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" -->
## Outline for today
+ **Dynamic programming**
  + **Rod-cutting** problem
  + Optimal **substructure**
  + Recursive, top-down, **bottom-up** solutions
+ **Fibonacci** sequence
+ **Matrix-chain** multiplication
+ Longest common **subsequence**
+ Shortest unweighted **path**
+ Optimal **binary search tree**

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" class="empty" -->
