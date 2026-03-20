---
layout: post
title: "[Python] U-V Decomposition using Swarm Optimization"
date: 2016-05-07
last_modified_at: 2016-05-07
categories: [Artificial Intelligence, Machine Learning, Source Code, Swarm Intelligence]
tags: [swarm-intelligence, pso, recommendation-systems, matrix-factorization, python, source-code]
migrated_from: https://ahmedhanibrahim.wordpress.com/2016/05/07/u-v-decomposition-using-swarm-optimization/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2016/05/07/u-v-decomposition-using-swarm-optimization/) on May 7, 2016, and has been migrated here.

An implementation of **U-V Matrix Decomposition** for Recommendation Systems, optimized using **Particle Swarm Optimization (PSO)**.

---

## Problem Setup

U-V Decomposition is a matrix optimization technique. Here the matrix represents user ratings for movies — a classic collaborative filtering scenario.

![User-Movie rating matrix](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/05/m.png)

The matrix has 5 users and 5 movies. Each row is a user's ratings. **Missing values** indicate unrated movies — these are the values we want to predict.

---

## Algorithm Summary

**Input:** A 5×5 matrix M with missing entries.

**Intermediate Output:** Factor matrices **U** and **V** such that U × V approximates M (without missing values).

**Output:** A complete 5×5 matrix with predicted values for all missing entries.

PSO is used to find the optimal U and V matrices by minimizing the reconstruction error over the known entries.

---

**Source code:** [GitHub — CS624-ML / U-V Decomposition](https://github.com/AhmedHani/CS624-ML/tree/master/Implementations/U-V%20Decomposition)

*(A detailed code walkthrough post is planned as a follow-up.)*

## References

- Leskovec, Rajaraman, Ullman — [*Mining of Massive Datasets*](http://infolab.stanford.edu/~ullman/mmds/book.pdf), Chapter 9 ([direct link](http://infolab.stanford.edu/~ullman/mmds/ch9.pdf))
