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
+ Longest common **subsequence**
+ **Matrix-chain** multiplication
+ Optimal **binary search tree**

---
## Optimisation
+ Large class of real-world problems consisting of
  + Find the **max** (or min) value of some **goal** (cost) function
    over some **search space**
+ **Search space**: may be *discrete* or *continuous*
  + **Dimension** of space may be *low* or very *high* (`10^6` or more)
+ **Goal** function: may be *analytic* or a *black-box*
  + Can we compute its *derivatives*?
+ **Exhaustive** search usually way too *slow*

![Saddle point between maxima, [Wikimedia](https://commons.wikimedia.org/wiki/File%3ASaddle_Point_between_maxima.svg)](static/img/Saddle_Point_between_maxima.svg)

---
## Dynamic programming
+ "Programming" here means **tables** (e.g., linear programming)
+ A form of **divide-and-conquer**, but
  + Store solutions to **sub-problems** for reuse
+ Outline for designing an implementation:
  + Recursive **top-down** (*inefficient*)
  + Top-down with **memoisation** (save *sub-results*)
  + **Bottom-up** (solve *smaller* sub-problems first)
+ Efficiency depends on:
  + Optimal **substructure**
  + **Overlapping** sub-problems

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" -->
## Outline for today
+ Dynamic programming
  + **Rod-cutting problem**
  + **Optimal substructure**
  + Recursive, top-down, bottom-up
+ Fibonacci sequence
+ Longest common subsequence
+ Matrix-chain multiplication
+ Optimal binary search tree

---
## Rod-cutting problem
+ Steel **rods** of length *i* can be sold for \`p\_i\` dollars each (1&le;i&le;n)
+ How to **cut** a single rod of length *n* so as to maximise **revenue**?
  + Assume **cuts** are free
+ e.g.: Input **price table** p = *[ 1, 5, 8, 9 ]*, rod **length** n = *4*
  + **Exhaustive** search:
    $9, $8+1, $1+8, $5+5, $5+1+1, $1+5+1, $1+1+5, $1+1+1+1
  + **Optimal** solution: two pieces of length 2: $5+5
+ For a given price table *p*, <br/>
  let \`r\_n\` be optimal **revenue** for length *n*
  + In this case, \`r\_n = 10\`

---
## Rod-cut: example

| i |  1 |  2 |  3 |  4 |  5 |  6 |  7 |  8 |
|---|----|----|----|----|----|----|----|----|
| p |  1 |  5 |  8 |  9 | 10 | 17 | 17 | 20 |

| n | `r\_n` |   Solution  |
|---|--------|-------------|
| 0 |    0   | (base case) |
| 1 |    1   | 1 (no cuts) |
| 2 |    5   | 2 (no cuts) |
| 3 |    8   | 3 (no cuts) |
| 4 |   10   | 2+2         |
| 5 |   13   | 2+3         |
| 6 |   17   | 6 (no cuts) |
| 7 |   18   | 1+6 or 2+2+3|
| 8 |   22   | 2+6         |

May have **multiple** optimal solutions, with **same** \`r\_n\`

Optimise **revenue** \`r\_n\` by considering possibilities for the **leftmost** cut:
+ **no cuts** (i.e., \`r\_n = p\_n\`)
+ cut of length 1: \`r\_n = p\_1 + r\_(n-1)\`
+ cut of length 2: \`r\_n = p\_2 + r\_(n-2)\`
+ etc.

---
## Rod-cut: substructure
+ Optimise **one cut** at a time, left to right
  + Assume the first piece **won't** be cut again
+ **Recurse** (or iterate) on second piece:
  + \`r\_n = max\_(1<=i<=n)( p[i] + r\_(n-i) )\`
+ Decomposes overall task into **subproblems** \`r\_i\`
+ Translates directly into a **recursive** solution

---
## Requirements for dynamic programming
+ To use **dynamic programming**, we need two properties:
+ **Optimal substructure**:
  + Optimal solution to **subproblem** results in optimal solution to **overall** problem
  + I.e., any optimal solution can be **composed** of solutions to subproblems
+ **Overlapping subproblems**:
  + Subproblems appear in **multiple** branches of recursion tree
  + Allows **reuse** of solutions, giving us efficiency

---
## Prove optimal substructure
+ Let \`A\_n\` be an optimal solution for **entire** length *n*
  + Let *i* be location of **first** cut in \`A\_n\`
  + Let \`A\_(n-i)\` be the **remaining** cuts in \`A\_n\`
+ **Claim**: \`A\_(n-i)\` is optimal for length *n-i*
  + Assume **not**: let \`B\_(n-i)\` be a **better** solution for *n-i*
    + revenue( \`B\_(n-i)\` ) &gt; revenue( \`A\_(n-i)\` )
  + Then we can **improve** on \`A\_n\` by combining this with *i*:
    + Let \`B\_n = [i, B\_(n-i)]\`, then
    + revenue( \`B\_n\` ) = p[i] + revenue( \`B\_(n-i)\` ) <br/>
      &gt; p[i] + revenue( \`A\_(n-i)\` ) = revenue( \`A\_n\` )
  + This **contradicts** that \`A\_n\` was optimal for length *n*.
+ This proves **optimal substructure** for rod-cutting

---
## Overlapping subproblems
+ Optimal substructure shows recursive solution is **correct**
+ To get **efficiency** of dynamic programming, we also need to **reuse** subproblems
+ **Taxonomy** of subproblems:
  + Index subproblems by **length** of rod (*n*)
+ **Reuse** solutions to subproblems:
  + A solution for rods of length *5* works **anywhere** within a longer rod
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
+ Longest common subsequence
+ Matrix-chain multiplication
+ Optimal binary search tree

---
## 1 Recursive top-down

---
## 2 Top-down w/memoisation

---
## 3 Bottom-up solution

---
## Subproblem graph

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" -->
## Outline for today
+ Dynamic programming
  + Rod-cutting problem
  + Optimal substructure
  + Recursive, top-down, bottom-up
+ **Fibonacci sequence**
+ **Longest common subsequence**
+ Matrix-chain multiplication
+ Optimal binary search tree

---
## Fibonacci sequence

---
## Fib top-down

---
## Fib bottom-up

---
## Longest common subsequence

---
## Optimal substructure

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" -->
## Outline for today
+ Dynamic programming
  + Rod-cutting problem
  + Optimal substructure
  + Recursive, top-down, bottom-up
+ Fibonacci sequence
+ Longest common subsequence
+ **Matrix-chain multiplication**
+ Optimal binary search tree

---
## Matrix-chain multiplication

---
## Optimal substructure

---
## Bottom-up solution

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
+ **Optimal binary search tree**

---
## Shortest/longest path

---
## Optimal BST

---
## Optimal substructure

---
## Optimal BST example

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" -->
## Outline for today
+ **Dynamic programming**
  + **Rod-cutting** problem
  + Optimal **substructure**
  + Recursive, top-down, **bottom-up** solutions
+ **Fibonacci** sequence
+ Longest common **subsequence**
+ **Matrix-chain** multiplication
+ Optimal **binary search tree**

---
<!-- .slide: data-background-image="https://sermons.seanho.com/img/bg/unsplash-mE5MBZX5sko-leaves.jpg" class="empty" -->
