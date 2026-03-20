---
layout: post
title: "A Study on CoVe, Context2Vec, ELMo, ULMFiT and BERT"
date: 2019-07-01
last_modified_at: 2019-07-01
categories: [Artificial Intelligence, Deep Learning, Machine Learning, Natural Language Processing, Neural Network]
tags: [bert, elmo, ulmfit, cove, context2vec, transfer-learning, nlp, language-models, word-embeddings]
migrated_from: https://ahmedhanibrahim.wordpress.com/2019/07/01/a-study-on-cove-context2vec-elmo-ulmfit-and-bert/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2019/07/01/a-study-on-cove-context2vec-elmo-ulmfit-and-bert/) on July 1, 2019, and has been migrated here.

A research study on the models that revolutionized NLP through Transfer Learning — covering architecture, key ideas, and personal notes from implementation experience.

---

## Key Terminology

**Vector Space Models (VSMs):** Words as unique vectors, feeding downstream ML models.

**Word Embedding:** Fixed-size vectors where semantically similar words have small Euclidean distance. Foundation for Language Modeling and Machine Translation.

**Sentence Embedding:** Same idea applied to full sentences.

**Language Model:** Models a statistical distribution over sentences to predict the next word given context.

**Transfer Learning:** Store knowledge learned on one task; reuse and optionally fine-tune it for another task.

**Multi-Task Learning:** Train simultaneously on multiple subtasks; the shared representation captures generalizable knowledge.

**Domain Adaptation:** A Transfer Learning subfield — adapt a model trained on a source distribution to perform well on a different target distribution.

---

## Context Vectors (CoVe)

**Paper:** [arxiv.org/pdf/1708.00107.pdf](https://arxiv.org/pdf/1708.00107.pdf)

CoVe vectors are learned on top of existing word vectors (GloVe, Word2Vec, FastText) using the **encoder** of a Neural Machine Translation (NMT) seq2seq model trained on German→English translation. The encoder learns complex semantic relations between words in order to translate, making its hidden representations richer than static embeddings.

**Usage:**
```
CoVe = MT-LSTM(GloVe(sentence))
```

Inspired by the success of pre-trained CNNs on ImageNet, CoVe applies the same transfer idea to NLP: train on a large task (NMT), then use the encoder as an initialization layer for downstream tasks.

The paper introduced **Bi-attentive Classification Network (BCN)** to validate CoVe quality on tasks like Sentiment Analysis and Paraphrase Detection. BCN accepts two inputs (or duplicates one), passes them through the MT-LSTM encoder, then uses a Bi-LSTM + bi-attention architecture ending in a maxout classifier.

![BCN architecture](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2019/06/screen-shot-2019-06-29-at-3.20.03-pm.png)

![BCN results](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2019/06/screen-shot-2019-06-29-at-4.39.59-pm.png)

**Personal notes:**
- You don't need BCN — just prepend the frozen (or fine-tuned) encoder to your own model.
- Fine-tuning is generally better than freezing to allow slight task-specific adaptation.
- Use FastText over GloVe when character-level distinctions matter (e.g., named entities).

---

## Context to Embeddings (Context2Vec)

**Paper:** [aclweb.org/anthology/K16-1006](https://www.aclweb.org/anthology/K16-1006)

Consider the sentence "I can't find **April**." Without context, "April" could be a month or a person. Context2Vec extends CBOW Word2Vec by replacing the simple average-of-context-vectors with a richer parametric model — a **Bi-LSTM + feedforward network**.

**Three-stage architecture:**
1. Bi-LSTM processes left-to-right and right-to-left context.
2. Feedforward network learns from the concatenated Bi-LSTM hidden states.
3. Objective function (with Word2Vec negative sampling) compares output to target word embedding.

![Context2Vec vs CBOW](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2019/06/screen-shot-2019-06-29-at-5.43.01-pm.png)

![Context2Vec architecture](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2019/06/screen-shot-2019-06-29-at-5.53.11-pm.png)

![Context2Vec closest words sample](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2019/06/screen-shot-2019-06-29-at-6.05.36-pm.png)

**Personal note:** Similar to Doc2Vec, but uses Bi-LSTM instead of a plain projection layer for deeper contextual representation.

---

## Embeddings from Language Models (ELMo)

**Paper:** [arxiv.org/pdf/1802.05365.pdf](https://arxiv.org/pdf/1802.05365.pdf)

ELMo addresses the same polysemy problem (a word's meaning depends on context) by learning embeddings from a **Bi-directional Language Model (BiLM)**:

- **Forward LM:** Predict word given previous words — P(word | left context)
- **Backward LM:** Predict word given following words — P(word | right context)

![Bidirectional language model](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2019/07/52861-1pb5hxsxogjrnda_si4nj9q.png)

Each word's final ELMo representation is the **weighted element-wise sum** of:
1. Original word embedding (GloVe/Word2Vec/FastText)
2. Forward LSTM hidden state
3. Backward LSTM hidden state

Weights can be task-specific (learned during fine-tuning).

**Usage steps:**
1. Train BiLM on a large corpus.
2. Freeze the BiLM encoders and attach them at the bottom of your model.
3. Replace raw word indices with their ELMo representations.

![ELMo benchmark results](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2019/06/screen-shot-2019-06-29-at-10.31.49-pm.png)

**Personal notes:**
- Train the LM on domain-specific data for best downstream results.
- Deeper models or CNN character features can improve the LM quality.

---

## Universal Language Model Fine-tuning (ULMFiT)

**Paper:** [arxiv.org/pdf/1801.06146.pdf](https://arxiv.org/pdf/1801.06146.pdf)

ULMFiT's goal: one universal language model that can be fine-tuned for any classification task. The base model is **AWD-LSTM** — a heavily regularized LSTM targeting generalization on long sequences.

**AWD-LSTM regularization techniques:**
- **DropConnect Mask:** Randomly zeroes weight connections (not activations).
- **Variational Dropout:** Same dropout mask applied at every time step within a sequence.
- **ASGD (Average SGD):** Averages weights over multiple steps for more stable convergence.
- **Variable Length BPTT:** Randomizes truncation length during training.

**ULMFiT introduces two fine-tuning innovations:**

**Discriminative Fine-tuning (Discr):** Different layers use different learning rates, since lower layers capture more general features (should change slowly) while upper layers capture task-specific features (can change faster).

**Slanted Triangular Learning Rates (STLR):** The learning rate increases quickly then decreases slowly — a specific schedule designed for fine-tuning pre-trained models.

---

## BERT

**Paper:** [arxiv.org/abs/1810.04805](https://arxiv.org/abs/1810.04805)

BERT (Bidirectional Encoder Representations from Transformers) is a pre-trained deep Transformer encoder that redefined the state of the art across NLP benchmarks. Unlike the models above which use LSTMs, BERT uses a multi-layer Transformer architecture with self-attention.

**Two novel pre-training objectives:**

**1. Masked Language Model (MLM):** Randomly mask 15% of tokens in the input; train the model to predict those masked tokens. This allows truly bidirectional context — both left and right — unlike unidirectional LMs.

**2. Next Sentence Prediction (NSP):** Given two sentences, predict whether sentence B actually follows sentence A in the original document. This captures inter-sentence relationships useful for QA and inference tasks.

**Two model sizes:**
- BERT-Base: 12 Transformer layers, 768 hidden units, 12 attention heads (110M parameters)
- BERT-Large: 24 layers, 1024 hidden units, 16 attention heads (340M parameters)

**Fine-tuning:** Add a task-specific output layer on top of BERT and fine-tune end-to-end. BERT achieved state-of-the-art on 11 NLP tasks including SQuAD, MNLI, and CoLA at time of publication.

---

## Summary

| Model | Core Idea | Architecture | Key Innovation |
|---|---|---|---|
| CoVe | NMT encoder as feature extractor | Bi-LSTM | Transfer from MT task |
| Context2Vec | BiLM-style context modeling | Bi-LSTM + FF | Richer CBOW context |
| ELMo | Contextual word embeddings from BiLM | Stacked Bi-LSTM | Per-layer weighted sum |
| ULMFiT | Universal LM fine-tuning | AWD-LSTM | Discr LR + STLR |
| BERT | Masked LM + NSP pre-training | Transformer | True bidirectionality via masking |

The trajectory is clear: from static word vectors → context-dependent LSTMs → attention-based Transformers. Each step brought deeper, more context-aware representations that better model language semantics.

---

## References

- [CoVe paper](https://arxiv.org/pdf/1708.00107.pdf)
- [Context2Vec paper](https://www.aclweb.org/anthology/K16-1006)
- [ELMo paper](https://arxiv.org/pdf/1802.05365.pdf)
- [ULMFiT paper](https://arxiv.org/pdf/1801.06146.pdf)
- [BERT paper](https://arxiv.org/abs/1810.04805)
- [AWD-LSTM paper](https://arxiv.org/pdf/1708.02182.pdf)
- [BiLM explanation — Medium](https://medium.com/@plusepsilon/the-bidirectional-language-model-1f3961d1fb27)
- [AWD-LSTM explanation — Yash Seth](https://yashuseth.blog/2018/09/12/awd-lstm-explanation-understanding-language-model/)
