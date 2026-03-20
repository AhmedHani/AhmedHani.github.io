---
layout: post
title: "Feedback Sequence-to-Sequence Model – Gonna Reverse Them All!"
date: 2017-06-25
last_modified_at: 2017-06-26
categories: [Artificial Intelligence, Deep Learning, Machine Learning, Natural Language Processing, Neural Network, Source Code]
tags: [seq2seq, encoder-decoder, rnn, lstm, nlp, deep-learning, tutorial]
migrated_from: https://ahmedhanibrahim.wordpress.com/2017/06/25/feedback-sequence-to-sequence-model-gonna-reverse-them-all/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2017/06/25/feedback-sequence-to-sequence-model-gonna-reverse-them-all/) on June 25, 2017, and has been migrated here.

*This tutorial assumes familiarity with Recurrent Neural Networks and Backpropagation Through Time (BPTT).*

---

## Terminology

**One-to-one:** One input word → one output word (e.g., semantic synonyms like "like" → "love").

**One-to-many:** One input → multiple outputs (e.g., hypernym relations: "vehicle" → ["car", "bike", "boat"]).

**Many-to-one:** Multiple inputs → one output (e.g., Sentiment Analysis: sentence → polarity label).

**Many-to-many:** Multiple inputs → multiple outputs (e.g., Machine Translation: English → French sentence).

**Word Embedding:** Fixed-size semantic vectors for words — similar words have similar vectors.

**One-hot Encoding:** Naive sparse representation — a vector of zeros with a single 1 at the word's index. No semantic content; used here for simplicity.

---

## Dataset

Characters: `'a'`, `'b'`, `'c'` only. Task: reverse a string (e.g., "abc" → "cba").

One-hot encodings:
```
[1, 0, 0] = 'a'
[0, 1, 0] = 'b'
[0, 0, 1] = 'c'
```

String "abc" is represented as the concatenated vectors [1,0,0, 0,1,0, 0,0,1].

---

## Encoder-Decoder Architecture

![Encoder-Decoder overview](https://camo.githubusercontent.com/097ae56dffeca6fb58767a8829d313e4c5fb69c1/687474703a2f2f7777312e73696e61696d672e636e2f6d773639302f36393762303730666a77316632377232346f3263746a3230656130636f3075382e6a7067)

Any seq2seq model has two components:

- **Encoder:** Processes the input sequence step by step, updating its hidden state. The final hidden state — the **Thought Vector** — is a fixed-size representation of the entire input.
- **Decoder:** Initialized with the Thought Vector, generates output tokens one at a time until an END token is produced or max length is reached.

This post demonstrates the **Feedback Encoder-Decoder** variant: the decoder's output at time *t* becomes its input at time *t+1*.

---

## The Encoder

![Encoder diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2017/06/encoder.png)

**Parameters:**
- Batch size = 1, Input shape = 1×3
- Hidden layer size S(t) = 5
- Input-to-hidden weights **Wh** (3×5), Hidden-to-hidden weights **Ws** (5×5)
- Activation: ReLU — f(x) = max(0, x)

Hidden state recurrence: **S(t) = x(t) · Wh + S(t-1) · Ws**

Initial state S(0) = zeros (no prior memory).

**Processing "abc":**

**S(1)** = x(1) · Wh + 0 · Ws = **[0.1, 0.2, 0.3, 0.4, 0.5]**

![S1 matrices](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2017/06/mat1.png)
![S1 result](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2017/06/mat11.png)

**S(2)** = x(2) · Wh + S(1) · Ws = **[0.98, 1.28, 1.65, 1.74, 0.58]**

![S2 matrices](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2017/06/mat2.png)
![S2 intermediate](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2017/06/mat22.png)
![S2 result](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2017/06/mat222.png)

**S(3)** = x(3) · Wh + S(2) · Ws = **[1.74, 2.16, 4.56, 4.62, 3.29]**

![S3 matrices](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2017/06/mat3.png)
![S3 intermediate](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2017/06/mat33.png)
![S3 result](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2017/06/mat333.png)

**Thought Vector = [1.74, 2.16, 4.56, 4.62, 3.29]** — the encoded representation of "abc".

> Note: With all-positive weights, ReLU has no effect in this toy example. In practice, weights will be mixed-sign.

---

## The Decoder

![Decoder diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2017/06/decoder.png)

Two key differences from the encoder:

1. **Initial state** = Thought Vector (the encoder's final state).
2. **Input at t+1** = output produced at time t (feedback loop).

**Additional parameters:**
- Hidden-to-output weights **_Wo** (5×3)
- Output activation: Softmax

The decoder runs for the desired output length, producing one character per step. The character with the highest Softmax probability is selected (argmax), fed back as the next input, and this repeats until the reversed string is complete.

> This is a mapping problem solvable by an MLP if max string length is fixed. The seq2seq framing here is pedagogical — the real power of these models is in variable-length sequences like Machine Translation.

---

## References

- [Source code on GitHub](https://github.com/AhmedHani/nlpeast) (Thesis project)
- [Data Normalization post](/2014/10/10/data-normalization-and-standardization-for-neural-networks/) — for softmax background
