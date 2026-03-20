---
layout: post
title: "Introduction to Reinforcement Learning"
date: 2015-02-10
last_modified_at: 2015-02-10
categories: [Machine Learning, Reinforcement Learning]
tags: [reinforcement-learning, machine-learning, tic-tac-toe]
migrated_from: https://ahmedhanibrahim.wordpress.com/2015/02/10/introduction-to-reinforcement-learning/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2015/02/10/introduction-to-reinforcement-learning/) on February 10, 2015, and has been migrated here.

This is the first post in a series on Reinforcement Learning — a field I started exploring after realizing that Supervised and Unsupervised Learning weren't the only approaches available. This series documents what I learned, the resources I used, and my code implementations.

---

## Chapter 1: Introduction

### Supervised vs. Unsupervised vs. Reinforcement Learning

In **supervised learning**, a labeled training dataset is used to train a classifier (SVM, KNN, Neural Networks, etc.). The classifier extracts feature vectors from training examples to learn how to identify and classify new data.

In **unsupervised learning**, there is no labeled data. The task is to cluster unlabeled examples into classes based on their features. Common algorithms include K-Means and its variants, and Hidden Markov Models (HMMs).

**Reinforcement learning** is different from both. It's about learning *what to do* in a given situation to maximize a reward — without requiring pre-labeled training data. The agent learns from its own experience through interaction with the environment. This makes it well-suited for interactive problems where the agent must make real-time decisions.

---

## Characteristics of Reinforcement Learning

Three defining aspects:

- **Trials**: The agent performs many trials in the same environment to learn patterns (e.g., predicting opponent behavior in a chess game).
- **Error**: After each state transition, the agent calculates the error and tries to minimize it in similar future states.
- **Reward**: The benefit the agent receives after performing an action.

The agent also faces a fundamental trade-off in action selection:

- **Exploitation**: Using previously successful experiences in the current situation.
- **Exploration**: Trying new actions that might yield better rewards.

---

## Four Elements of a Reinforcement Learning System

Scientists have identified four core elements:

**1. Policy**
Defines the agent's behavior in each state. In Tic-Tac-Toe, the policy might be to always play 'X' and estimate the winning probability for each possible move.

**2. Reward Function**
Defines the desirability of the *immediate* next state after the agent's action. A high reward = a good move; a low reward = a bad move.

**3. Value Function**
Defines the total expected reward over the long run, considering all future states — not just the next one. The Value Function is more important than the Reward Function for long-horizon decisions.

**4. Model** *(optional)*
Predicts the next state of the environment given the current state and action. Used in planning-based agents (e.g., game AI).

> **Note:** A good agent may accept a lower immediate reward if that state leads to a higher Value Function over time.

---

## Example: Tic-Tac-Toe with Reinforcement Learning

![Tic-Tac-Toe RL diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/02/tic.jpg)

- The opponent starts at point **a**, transitioning the game to state **b**.
- At **b**, the agent evaluates all possible moves (dashed lines) and selects one — transitioning to state **c** per its policy.
- The opponent responds, moving the game to state **d**.
- At **d**, the agent tries an exploratory move → state **e\***, which turns out to be suboptimal.
- The agent backtracks and chooses state **e** instead.
- Curved lines indicate *backups* — the agent updates its winning probability estimates after each move to improve future decisions.

---

## References

- Richard S. Sutton and Andrew G. Barto, *Reinforcement Learning: An Introduction*
- [Wikipedia: Reinforcement Learning](http://en.wikipedia.org/wiki/Reinforcement_learning)
