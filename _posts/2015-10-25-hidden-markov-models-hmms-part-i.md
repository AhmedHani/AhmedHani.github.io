---
layout: post
title: "Hidden Markov Models (HMMs) Part I"
date: 2015-10-25
last_modified_at: 2017-11-03
categories: [Machine Learning, Natural Language Processing]
tags: [hmm, markov-chains, nlp, machine-learning, forward-backward, viterbi, statistics]
migrated_from: https://ahmedhanibrahim.wordpress.com/2015/10/25/hidden-markov-models-hmms-part-i/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2015/10/25/hidden-markov-models-hmms-part-i/) on October 25, 2015, and has been migrated here.

*This article assumes familiarity with Naive Bayes, Recursion, and Dynamic Programming.*

---

## Agenda

- Markov Chains (Structure, 1st Order, Higher Order)
- Hidden Markov Model (Structure, Applications)
- Forward-Backward Algorithm
- Viterbi Algorithm *(Part II)*
- Baum-Welch Algorithm *(Part II)*

---

## Markov Chains

A Markov Chain is a probabilistic model consisting of finite states connected by edges with associated transition probabilities.

### Structure

![Markov Chain weather model](https://upload.wikimedia.org/wikipedia/commons/7/7a/Markov_Chain_weather_model_matrix_as_a_graph.png)

The above diagram has two states: **Sunny** and **Rainy**. The probability of transitioning from Sunny to Rainy is 0.1, and from Sunny to Sunny again is 0.9. The sum of outgoing probabilities from any state must equal 1.

This can be represented as a **Right Stochastic Transition Matrix** where each row sums to 1 [[Side Note #1]](#side-note-1):

![Transition matrix](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/08/t-matrix.png)

### Predicting the Next State

To predict the next state at time *t+1* given the current state, multiply the current state vector by the transition matrix:

![Next state prediction](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/08/t-matrix1.png)

This is **1st Order Markov Chain** — the next state depends only on the current state.

### Higher Order Markov Chains

To predict the state *m* steps ahead, solve recursively bottom-up:

![Higher order prediction](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/08/t-matrix2.png)

After substitution:

![Higher order substitution](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/08/t-matrix3.png)

Real-world examples include multi-day weather forecasting.

---

## Hidden Markov Models

In practice, the states we want to predict may not be directly observable. Consider a friend whose health is visibly affected by the weather (visible states: Flu, Fever, Dry) but the weather itself (hidden states: Sunny, Cloudy, Rainy) is not directly accessible. We want to infer weather from health observations.

Plain Markov Chains can't handle this — they require direct access to the predictable states. HMMs extend Markov Chains to handle this case.

### Structure

![HMM model diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/08/hmm1.png)

The model λ = (A, B, π) where:
- **A** = Transition probability matrix between hidden states
- **B** = Emission probability matrix (hidden state → observable state)
- **π** = Initial state distribution

**Transition Probabilities** define how likely the hidden state changes over time (e.g., P(Rainy | Sunny) = 0.3).

**Emission Probabilities** define the likelihood of observing a visible state given a hidden state (e.g., P(Dry | Sunny) = 0.6).

### Why Use HMMs?

HMMs excel at sequential prediction tasks: weather forecasting, speech recognition, handwriting recognition, machine translation, and language detection. They solve three key problems:

1. **Observation Probability Estimation** — Given a model, estimate the probability of an observation sequence.
2. **Optimal Hidden State Sequence** — Find the most likely hidden state path for a given observation sequence.
3. **Parameter Estimation** — Learn model parameters that maximize the probability of observations.

These are solved by the Forward-Backward, Viterbi, and Baum-Welch algorithms respectively.

---

## Observations Probability Estimation

We want to compute **P(O | λ)** where O = {o₁, o₂, …, oT} is an observation sequence.

### Brute Force

For every possible state sequence Q:

```
P(O, Q | λ) = πq₁ · ∏ P(qt | qt-1) · ∏ bqt,ot
```

This requires O(2T · N^T) computations — exponential and intractable for long sequences.

### Forward-Backward Algorithm (Dynamic Programming)

Using Figure 2 above. States: **S** = Sunny, **R** = Rainy, **C** = Cloudy. Observations: **D** = Dry, **F** = Fever, **U** = Flu.

**Initial probabilities:**

![Initial probabilities](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/10/equ.png)

**Transition probabilities:**

![Transition probabilities](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/10/equ1.png)

**Emission probabilities:**

![Emission probabilities](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/10/equ2.png)

#### Step 1: Forward Probabilities

αt(i) = probability the model outputs observations o₁…ot and ends in state i at time t.

**Base case (t=1), observation = D:**
```
α₁(R) = P(R) × P(D|R) = 0.3 × 0.1 = 0.03
α₁(S) = P(S) × P(D|S) = 0.4 × 0.6 = 0.24
α₁(C) = P(C) × P(D|C) = 0.3 × 0.4 = 0.12
```

**Recurrence:**
```
αt(i) = (Σj αt-1(j) × P(Qt=i | Qt-1=j)) × P(Ot | Qt=i)
```

**t=2, observation = F:**
```
α₂(R) = (0.03×0.2 + 0.24×0.3 + 0.12×0.1) × 0.4 = 0.49
α₂(S) = (0.03×0.1 + 0.24×0.4 + 0.12×0.4) × 0.4 = 0.547
α₂(C) = (0.03×0.7 + 0.24×0.3 + 0.12×0.5) × 0.4 = 0.553
```

![Forward probabilities continued](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/10/equ3.png)

#### Step 2: Backward Probabilities

βt(i) = probability of generating observations ot+1…oT starting from state i at time t.

**Base case:**
```
βT(i) = 1  (for all states)
```

**Recurrence:**
```
βt(i) = Σj P(Qt=i | Qt-1=j) × P(ot+1 | j) × βt+1(j)
```

**t=3, observation = D:**
```
β₃(R) = (0.2×0.1×1) + (0.3×0.6×1) + (0.1×0.4×1) = 0.04
β₃(S) = (0.1×0.1×1) + (0.4×0.6×1) + (0.4×0.4×1) = 0.16
β₃(C) = (0.7×0.1×1) + (0.3×0.6×1) + (0.5×0.4×1) = 0.20
```

![Backward probabilities continued](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/10/equ4.png)

#### Step 3: Combine Forward and Backward

```
P(o₁, o₂, …, oT | qt=i) = αt(i) × βt(i)
```

The forward pass covers time 1→t; the backward pass covers t+1→T. Together they give the full probability.

---

## Side Notes

### Side Note #1 {#side-note-1}

A [Stochastic Transition Matrix](https://en.wikipedia.org/wiki/Stochastic_matrix) has three variants: Right Stochastic (each **row** sums to 1), Left Stochastic (each **column** sums to 1), and Doubly Stochastic (both rows and columns sum to 1).

### Side Note #2

[N-grams](https://en.wikipedia.org/wiki/N-gram) are a language modelling approach from NLP. They apply the Markov property to estimate word co-occurrence probabilities. A Bigram (N=2) models P(word₂ | word₁); a Trigram (N=3) models P(word₃ | word₁, word₂).

---

## References

- Christopher D. Manning and Hinrich Schütze, *Foundations of Statistical Natural Language Processing*
- [TAMU PRISM HMM Lecture](http://research.cs.tamu.edu/prism/lectures/pr/pr_l23.pdf)
- [Leeds HMM Tutorial](http://www.comp.leeds.ac.uk/roger/HiddenMarkovModels/html_dev/hmms/s2_pg2.html)
