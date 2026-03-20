---
layout: post
title: "[Python] 3 Ways of Multi-threaded Matrix Multiplication"
date: 2016-04-29
categories: [Source Code]
tags: [python, parallel-computing, multi-threading, matrix, source-code]
migrated_from: https://ahmedhanibrahim.wordpress.com/2016/04/29/python-3-ways-of-multi-threaded-matrix-multiplication/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2016/04/29/python-3-ways-of-multi-threaded-matrix-multiplication/) on April 29, 2016, and has been migrated here.

A parallel programming implementation of 3×3 matrix multiplication using three different decomposition strategies.

**Input:** Matrix A (3×3) and Matrix B (3×3)  
**Output:** Matrix C = A × B

```
A = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
B = [[9, 8, 7], [6, 5, 4], [3, 2, 1]]
```

---

## Three Decomposition Strategies

### 1. Input Decomposition

Divide both matrices into sub-matrices using [Block Matrix](https://en.wikipedia.org/wiki/Block_matrix) partitioning. For 3×3 matrices:

```
A = [A1, A2, A3]ᵀ  where each Ai is 3×1
B = [B1, B2, B3]   where each Bi is 1×3
```

Each thread computes one outer product: A1×B1, A2×B2, A3×B3. Each result is a 3×3 matrix. Sum all three to get C.

### 2. Output Decomposition

Each thread is responsible for computing one **row** of the output matrix C. The thread receives a row from A and the entire matrix B.

![Output decomposition diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/04/od.png)

### 3. Intermediate Decomposition

Similar to Input Decomposition, but each thread produces a complete intermediate 3×3 matrix. The final result is the element-wise sum of all three intermediate matrices:

```
C = thread_1_C + thread_2_C + thread_3_C
```

![Intermediate decomposition diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/04/inter.png)

---

**Source code:** [GitHub — CS617-HPC / parallel_matrix_multiplication](https://github.com/AhmedHani/CS617-HPC/tree/master/Assignments/parallel_matrix_multiplication/multi-threaded_matrix_multiplication)

## References

- Fayez Gebali, *Algorithms and Parallel Computing*
- Ananth Grama et al., *Introduction to Parallel Computing* (2nd Ed.)
