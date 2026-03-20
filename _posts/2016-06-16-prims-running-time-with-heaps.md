---
layout: post
title: "Prim's Running Time With Heaps"
date: 2016-06-16
categories: [Misc]
tags: [algorithms, graphs, mst, prim, heap, complexity, proof]
migrated_from: https://ahmedhanibrahim.wordpress.com/2016/06/16/prims-running-time-with-heaps/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2016/06/16/prims-running-time-with-heaps/) on June 16, 2016, and has been migrated here.

**Input:** A graph with N nodes and M weighted edges.
**Output:** The Minimum Spanning Tree (MST) — a tree connecting all nodes with minimum total edge weight, no cycles allowed.

![MST example](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/06/kruskalll.png)

---

## Naive Algorithm: O(NM)

**Steps (from Wikipedia):**

1. Initialize a tree with a single arbitrarily chosen vertex.
2. Repeatedly find the minimum-weight edge connecting the tree to a vertex not yet in the tree, and add it.
3. Repeat until all vertices are in the tree.

**Cost analysis:**
- Iterating all N nodes: **O(N)**
- Finding the minimum-weight edge per iteration (scanning M edges): **O(M)**
- Combined: **O(NM)**

---

## Improved Algorithm with Heaps: O(M log N)

A **Min Heap** is a binary tree where every parent node is ≤ its children, so the minimum is always at the root. Nearly all languages include a heap in their standard collections.

![Heap structure](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/06/h.png)
![Min-heap](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/06/min-heap.png)

Key heap operation costs:

![Heap operation costs](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/06/bh.png)

**Improved Prim's steps:**

1. Create a Min Heap of size N — **O(N)**
2. Initialize with a root node at cost 0
3. While the heap is not empty:
   - Extract the minimum node *u* — **O(1)**
   - Delete the extracted node (mark as visited) — **O(log N)**
   - Get all adjacent nodes *v* of *u* — **O(M)** total across all iterations
   - For each adjacent *v* still in the heap: if the u-v edge weight is lower than v's current key, relax it and re-insert — **O(log N)**
   - Add *u* to the MST

**Final running time: O(M log N)**

---

## References

- [Wikipedia: Binary Heap](https://en.wikipedia.org/wiki/Binary_heap)
- [Wikipedia: Heap (data structure)](https://en.wikipedia.org/wiki/Heap_(data_structure))
- [Wikipedia: Prim's Algorithm](https://en.wikipedia.org/wiki/Prim%27s_algorithm)
