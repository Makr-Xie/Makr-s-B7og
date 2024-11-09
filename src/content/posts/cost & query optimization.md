---
  title: Cost & Query Optimization (DB System)
  pubDate: 2024-11-8
  categories: ["笔记"]
  description: 'A note of Cost and Query Optimization of UIUC CS 411, using for homeworrk & exam review.'
---
- [Basic Cost](#basic-cost)
- [Binary Operation Costs](#binary-operation-costs)
  - [One-pass Algorithms](#one-pass-algorithms)
  - [Nested Loop Join](#nested-loop-join)
  - [Two-Pass Algorithms](#two-pass-algorithms)
  - [Index Based](#index-based)
- [Rule-Based Optimization](#rule-based-optimization)
- [Size Estimation](#size-estimation)
  - [Cost-Based Optimization](#cost-based-optimization)
- [DP for Multiple JOINs](#dp-for-multiple-joins)


## Basic Cost

Assume we have **M** main memory blocks available for use

**B(R)**: Represents the **number of blocks (or pages)** that store the relation R

**T(R)**: Represents the **number of tuples (or records)** in relation R.

**V(R, a):**  Defined as the **number of distinct values** of the attribute a.

Usually T(S) = B(S) / V(R,A)

**Sorting with Scan**

- Cost: B(R) if B(R) ≤ M

**Multi-Way Merge Sort Scan**

- Cost: 3B(R) → (1B(R) for scan, 2B(R) for sort).
- Assumption: B(R)< M^2

**Index Scan**

- Cost: B(R) + #block of index $\approx$  B(R)
- Sort-Scan: B(R)

**Unclustered Relation**

- Scan: T(R)
- Sort: T(R) + 2B(R)

## Binary Operation Costs 
**$\bowtie$**
**(JOIN, UNION,  INTERSECT, EXCEPT)**

### One-pass Algorithms

- Memory Requirement: **M ≥ min(B(R), B(S)) + 2**. (+2 means 1 for input and 1 for output)
- **Cost: B(R) + B(S)**

### Nested Loop Join

- Block-based Nested Loop Join.
- Memory Requirement: Whenever **M ≥ 3**
- Cost:
    - Read R once → B(R)
    - Outer loop runs B(R)/(M-2) times, and each time need to read S → B(S)B(R)/(M-2)
    - **Total Cost: B(R) + B(S)B(R)/(M-2).   (Approximatly B(R) B(S)/M)**

### Two-Pass Algorithms

**1.Based On Sorting** 

- Sort R and Sort S → 2B(R) + 2B(S)
- then Join → B(R) + B(S)
- Total Cost: 3B(R) + 3B(S)
- **Assumption: B(R) + B(S) ≤ M(M-1) < M^2**

**2.Based On Hash**

- Step 1:
    - Hash S into (M-1) buckets, using join attribute(s) as hash key
    - Send all buckets to disk
- Step 2:
    - Hash R into (M-1) buckets, using join attribute(s) as hash key
    - Send all buckets to disk
- Step 3
    - Join every pair of buckets with the same bucket number. Use the one pass algorithm for this.
    - Works when for each bucket no. i, either Ri or Si fits in memory.
    - **Min(B(R), B(S)) / (M-1) ≤ (M-2) $\iff$Min(B(R), B(S)) < M^2**
- **Cost: 3B(R) + 3B(S)**

*The two have similar cost, but have differences in difference scenarios*

So far:

- Min(B(R), B(S)) ≤ M - 2
    - One-Pass: B(R) + B(S)
- Min(B(R), B(S)) < M^2
    - Two-Pass: 3B(R) + 3B(S)
- Otherwise
    - Nested Join: B(R) + B(S)B(R)/(M-2)

### Index Based

**Selection** 

- Clustered Index:  B(R) / V(R,a)
    - V(R, a) was defined as the number of distinct values of the attribute a.
- Unclustered Index: T(R) / V(R,a)

(B for clustered, T for unclustered)

We can reduce cost of Binary Opertions, by reducing the cost of the selection.

**Index Based (Nested-Loop) Join**

Assume R is clustered. Cost:

- If index (on S) is clustered: B(R) + T(R)B(S)/V(S,a)
- If index (on S) is unclustered: B(R) + T(R)T(S)/V(S,a)

**Index Based (Sort-Merge or Zig-zag) Join**

- Assume both R and S have a sorted index (B+ tree) on the join attribute. (A clustering index.
- Then perform a merge join (called zig-zag join)
    - This is only the last step of the “two pass sorting-based join” algorithm we saw previously.
    - “bring all relevant tuples into memory”
- **Cost: B(R) + B(S)**

## Rule-Based Optimization

**Heuristic 1**

- Push selections down
- The earlier we process selections, less tuples we need to manipulatehigher up in the tree

**Heuristic 2**

- (In some cases), push selections up, then down
- But ultimately push down
- Typically: apply selections as close to the relations as possible so as to reduce size of intermediate results

## Size Estimation

**Estimate the size of a selection**

- T(S) = B(S) / V(R,A)

**Estimate the size of a join**

- T(R $\bowtie_A$ S) = T(R) T(S) / max(V(R,A),V(S,A))
- T(R $\bowtie_{A,B}$S) = T(R) T(S)/[max(V(R,A),V(S,A)) * max(V(R,B),V(S,B))]
- T(R X S) = T(R) T(S)

After the join, suppose we have table N → **V(N, A) = Min(V(R, A), V(S,A))**

### Cost-Based Optimization

**Main idea**: given a “partial query”: apply algebraic laws, until estimated cost of partial plan is minimal

## DP for Multiple JOINs

**Problem**

- **Given:** a query $R_1 \bowtie R_2 \bowtie \cdots \bowtie R_n$
- Assume we have a function **cost()** that gives us the cost of every join tree
- Find the best join tree for the query

For each subset $Q\{ R_1, ..., R_n\}$ compute

- **Size(Q)**: estimated size of the join of the relations in Q
- **Plan(Q)**: a best plan for Q – a particular join tree.
- **Cost(Q)**: the least cost of that plan: the cost is going to be **the size of intermediate tables** used by the plan.

**Step 1: For each $R_i$ Do**

- Size(Ri) = B(Ri)
- Plan(Ri) = Ri
- Cost(Ri) = 0

**Step 2: For each pair of $\{R_i, R_j \}$**) **Do**

- Size = T(R) T(S) / max(V(R,A),V(S,A))
- Smaller table on the left, eg. RiRj
- Cost = 0 (the size of intermediate tables = 0)

**DP process:**

- Compute Size: Q
- Cost of pairs Ri and Rj = Size(Ri) + Size(Rj) + Cost(Ri) + Cost(Rj)
- Cost = the smallest of such sum
- Plan = the corresponding Plan
- `Careful`:
    - ABC → need to consider: (AB + C), (A + BC), and (AC + B).
    - Let N = R1 $\bowtie_A$ R2, we have V(N, A) = Min(V(R1, A), V(R2, A))