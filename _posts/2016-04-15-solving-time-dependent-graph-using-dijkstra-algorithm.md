---
layout: post
title: "Solving Time-dependent Graphs Using a Modified Dijkstra Algorithm"
date: 2016-04-15
last_modified_at: 2016-04-18
categories: [Papers Recap]
tags: [algorithms, graph, dijkstra, shortest-path, paper-recap]
migrated_from: https://ahmedhanibrahim.wordpress.com/2016/04/15/solving-time-dependent-graph-using-modified-dijkstra-algorithm/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2016/04/15/solving-time-dependent-graph-using-modified-dijkstra-algorithm/) on April 15, 2016, and has been migrated here.

> Paper: [Shortest Path in Time-Dependent Graphs (Microsoft Research, EDBT 2008)](http://research.microsoft.com/pubs/173806/edbt08tdsp.pdf)

---

## Time-dependent Graph — Definition

A Time-dependent Graph (TDG) is defined as **GT(V, E, W)**, where:
- **V** = vertices
- **E** = directed edges
- **W** = edge weights that *vary with time*

The weight function is called the **edge-delay function**, parameterized by source node, destination node, and departure time. The weight can be constant across all time intervals or change at specific intervals.

![TDG example](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/04/1.png)

Figure 1: (b) shows constant-weight edges; (c) shows a weight that increases from 10 to 25 between time intervals [0,5] and [10,60].

---

## Problem Definition

Given a TDG with edge-delay functions and a time domain T, compute the **Least Total Travel Time (LTT)** from a source node to a destination node:

```
LTT(source, destination, departure_time)
```

Travel time = arrival time − starting time. Waiting at a node is allowed; the departure time of a node = arrival time + wait time.

The solution finds the **Time-Dependent Shortest Path (TDSP)**.

---

## Existing Solutions

### Bellman-Ford Based Algorithm

![Bellman-Ford algorithm](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/04/2.png)

1. Initialize GL(t) = ∞ for all nodes (earliest arrival time from source).
2. Initialize HK,L(t) for all edges (earliest arrival to L from source via edge K→L).
3. Iteratively relax edges — update GL(t) (**path-selection**) and HK,L(t) (**time-refinement**) until convergence.

### A\* Based Algorithm (KDXZ)

Assumes no waiting at nodes. Uses a priority queue where each path Pk has an associated function:

```
FPk(t) = GPk(t) + dk,d(t) − t
```

Where GPk(t) = arrival time at node K via Pk, and dk,d(t) = lower-bound estimate to destination. The algorithm works well on small graphs but struggles at scale because dk,d(t) is hard to estimate accurately for large graphs.

---

## New Dijkstra-Based Algorithm

The key insight is to **decompose** the path-selection and time-refinement steps into a clean two-phase procedure:

![Two-phase algorithm](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/04/3.png)

**Time-refinement** estimates the earliest arrival time function for every node. **Path-selection** then finds the path with the best arrival time for the optimal departure time t\*.

### Path-Selection Algorithm

![Path-selection pseudocode](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/04/4.png)

### Time-Refinement Algorithm (Modified Dijkstra)

![Time-refinement pseudocode](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/04/5.png)

1. Initialize the earliest arrival time function for each node.
2. Use a priority queue; for each node, incrementally expand its time sub-interval until the function is *well-refined* (specifies the true earliest arrival from source).
3. Update the function for every neighbor.
4. Terminate when the destination node is reached — its arrival time function is the answer.
