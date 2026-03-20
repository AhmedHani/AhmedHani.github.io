---
layout: post
title: "Overview: Generative Adversarial Networks – When Deep Learning Meets Game Theory"
date: 2017-01-17
last_modified_at: 2017-01-17
categories: [Artificial Intelligence, Deep Learning, Machine Learning, Reinforcement Learning]
tags: [gan, generative-models, deep-learning, game-theory, nash-equilibrium, minimax]
migrated_from: https://ahmedhanibrahim.wordpress.com/2017/01/17/generative-adversarial-networks-when-deep-learning-meets-game-theory/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2017/01/17/generative-adversarial-networks-when-deep-learning-meets-game-theory/) on January 17, 2017, and has been migrated here.

Before diving into Generative Adversarial Networks (GANs), a few foundational concepts are worth establishing.

---

## Key Concepts

**Discriminative Models** predict a hidden class given observed features. They model the conditional probability **P(y | x₁, x₂, …, xₙ)**. Examples: SVMs, Feedforward Neural Networks.

**Generative Models** learn the joint distribution of features and classes — **P(x₁, x₂, …, xₙ, y)** — enabling them to generate new samples from the learned distribution. Examples: Restricted Boltzmann Machines (RBMs), HMMs. Note: Vanilla Auto-encoders are *not* generative models (they reconstruct); Variational Auto-encoders (VAEs) are.

**Nash Equilibrium** (Game Theory): A stable game state where no player has an incentive to change their strategy after knowing the other players' strategies. Each player is satisfied with their outcome given the others' choices.

**Minimax**: An algorithm for two-player games where each player tries to minimize the maximum possible loss the opponent can inflict. Used in Chess, Tic-Tac-Toe, Connect-4, and other rule-based decision games.

---

## Generative Adversarial Networks (GANs)

![GAN architecture](https://i0.wp.com/www.kdnuggets.com/wp-content/uploads/generative-adversarial-network.png)

A GAN consists of two models competing during training:

- **Generator (G):** Produces fake samples intended to match the distribution of real data.
- **Discriminator (D):** Learns to distinguish real samples from the Generator's fakes.

The dynamic is adversarial — G tries to fool D; D tries to catch G. This is precisely the Minimax setup: each player attempts to minimize the worst outcome the other can produce.

Training continues iteratively until both models become experts: G generates samples indistinguishable from real data, and D becomes highly accurate at classification. When neither model can improve by changing its strategy unilaterally, the system reaches **Nash Equilibrium**.

During training, a shared loss function updates each model's parameters independently via backpropagation — neither model can directly modify the other's weights.

---

## Status

This was an overview written while still learning GANs. The follow-up post applies the concepts in more detail: [GANs Part 2 — Camouflage your Predator!](/2017/02/17/generative-adversarial-networks-2-camouflage-your-predator/)

---

## References

- [Goodfellow et al., NIPS 2016 Tutorial on GANs](https://arxiv.org/pdf/1701.00160v1.pdf)
- [KDnuggets: GANs Overview](http://www.kdnuggets.com/2017/01/generative-adversarial-networks-hot-topic-machine-learning.html)
- [Wikipedia: Generative Adversarial Networks](https://en.wikipedia.org/wiki/Generative_adversarial_networks)
- [Wikipedia: Minimax](https://en.wikipedia.org/wiki/Minimax)
- [Wikipedia: Nash Equilibrium](https://en.wikipedia.org/wiki/Nash_equilibrium)
- Stuart Russell and Peter Norvig, *Artificial Intelligence: A Modern Approach*
