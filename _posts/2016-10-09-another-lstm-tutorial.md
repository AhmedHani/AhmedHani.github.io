---
layout: post
title: "Another LSTM Tutorial"
date: 2016-10-09
categories: [Artificial Intelligence, Deep Learning, Machine Learning, Natural Language Processing, Neural Network]
tags: [lstm, rnn, deep-learning, nlp, neural-network]
migrated_from: https://ahmedhanibrahim.wordpress.com/2016/10/09/another-lstm-tutorial/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2016/10/09/another-lstm-tutorial/) on October 9, 2016, and has been migrated here.

*Figures in this post are taken from Christopher Olah's excellent [Understanding LSTMs](http://colah.github.io/posts/2015-08-Understanding-LSTMs/) blog post.*

---

## Recurrent Neural Networks

Recurrent Neural Networks (RNNs) are designed for sequential data — data where order and dependency between elements matters. Traditional Multi-layer Perceptrons (MLPs) assume independence between inputs, which is inappropriate for text or audio.

RNNs contain **self-loops** that carry the previous hidden state forward, allowing the network to "remember" what it has seen.

![RNN single unit](https://i0.wp.com/colah.github.io/posts/2015-08-Understanding-LSTMs/img/RNN-rolled.png)

Unrolled over time, the RNN resembles a deep feedforward network where each step receives both the current input and the previous hidden state:

![Unrolled RNN](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/1.png)

---

## The Long-term Dependencies Problem

Standard RNNs have no mechanism to selectively forget irrelevant context. For a sentence like:

> "I live in France, I like playing football with my friends and going to the school, **I speak french**"

Predicting "french" requires connecting to "I live in France" — but the two intermediate clauses introduce noise. Regular RNNs struggle to bridge these long-range dependencies, which is the main motivation behind **LSTM**.

---

## What is LSTM?

Long Short-Term Memory (LSTM) is a variant of RNN that controls the memory process through **gates** within each unit. These gates regulate what information to retain, update, or forget, allowing the network to maintain relevant long-range context.

The analogy: when reading a novel, your brain selectively remembers important events (subject, previous action) while discarding irrelevant details. LSTMs simulate this selective memory.

---

## LSTM Unit Structure

![LSTM unit](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/2.png)

A standard LSTM unit contains:
- **2 inputs:** previous cell state C_{t-1} and previous output h_{t-1}
- **4 layers:** 3 sigmoid activations + 1 tanh activation
- **5 point operators:** 3 multiplications, 1 addition, 1 tanh
- **2 outputs:** current cell state C_t and current output h_t

The **cell state** is the memory backbone. It flows through the unit with minimal modification unless the gates decide to change it.

---

## Detailed Processing: 3 Groups

![LSTM overview](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/21.png)

### Group 1.1 — Forget Gate

![Forget gate](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/6.png)

The **forget gate layer** (sigmoid) decides what to discard from the previous cell state. Output of 0 → forget everything; values closer to 1 → retain.

![Forget gate formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/7.png)

### Group 1.2 — Applying Forget to Previous State

![Forget gate × state](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/8.png)

Element-wise multiply the forget gate output with C_{t-1}. A vector of zeros means we wipe all previous memory.

![Forget application formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/9.png)

### Group 2.1 — Input Gate and Candidate State

![Input gate](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/10.png)

The **input gate layer** (sigmoid) decides which state values to update. A **tanh** layer generates the candidate new state values to potentially add.

![Candidate state formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/12.png)

### Group 2.2 — Scaling New State

![Scaling](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/13.png)

Multiply the candidate state by the input gate output to filter which new information actually gets written.

![Scaled formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/15.png)

### Combining Groups 1 + 2 → New Cell State

![New state](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/16.png)

Add the filtered old state (Group 1) and filtered new information (Group 2) to get C_t.

![New state formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/17.png)

### Group 3 — Output Gate

![Output gate](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/19.png)

A sigmoid layer decides which parts of the state to output. The state is passed through tanh (to keep values in [-1, 1]) and multiplied element-wise by the sigmoid output.

![Output formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/10/20.png)

---

## Conclusion

LSTMs have proven themselves across a wide range of tasks: Language Modeling, Sentiment Analysis, Speech Recognition, Text Summarization, and Question Answering. The gating mechanism is what makes them capable of learning which context to carry forward and which to discard.

---

## References

- [Christopher Olah: Understanding LSTMs](http://colah.github.io/posts/2015-08-Understanding-LSTMs/)
- [Wikipedia: LSTM](https://en.wikipedia.org/wiki/Long_short-term_memory)
- [Hochreiter & Schmidhuber, 1997 (original LSTM paper)](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.248.4448&rep=rep1&type=pdf)
- [WildML: RNN Tutorial Part 1](http://www.wildml.com/2015/09/recurrent-neural-networks-tutorial-part-1-introduction-to-rnns/)
