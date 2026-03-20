---
layout: post
title: "The Unsolvability of the Halting Problem"
date: 2016-05-01
last_modified_at: 2017-06-28
categories: [Misc]
tags: [algorithms, computability, halting-problem, turing, misc]
migrated_from: https://ahmedhanibrahim.wordpress.com/2016/05/01/the-unsolvability-halting-problem/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2016/05/01/the-unsolvability-halting-problem/) on May 1, 2016, and has been migrated here.

## Definition

The Halting Problem is one of the most famous problems in computer science. The question is simple: given a program and an input, can we determine whether the program will eventually terminate, or will it run forever?

Alan Turing proved that **no such general algorithm exists** — it is impossible to decide this for all programs.

The proof uses **proof by contradiction**.

---

## The Proof

1. Assume we have a program **P(X)** that takes a program X as input and returns:
   - `1` if X terminates
   - `0` if X runs forever

2. Define another program **Q** that takes P as input:
   - **Q(P(X))** = `1` if P(X) runs forever
   - **Q(P(X))** = `0` if P(X) halts

3. Now pass **Q to itself**: evaluate **Q(Q)**.

4. Two cases arise:
   - **Case 1:** If Q(Q) halts → P(X) returns 1 → Q(P(X)) returns 1 → P(X) runs forever. **Contradiction.**
   - **Case 2:** If Q(Q) runs forever → P(X) returns 0 → Q(P(X)) returns 0 → P(X) halts. **Contradiction.**

In both cases we reach a contradiction, proving that no program P can decide the halting problem for all inputs.

---

## References

- [Wikipedia: Halting Problem](https://en.wikipedia.org/wiki/Halting_problem)
- [NUS CS5234 FAQ](http://www.comp.nus.edu.sg/~cs5234/FAQ/halt.html)
