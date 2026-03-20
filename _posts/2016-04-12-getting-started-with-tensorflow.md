---
layout: post
title: "Getting Started with TensorFlow"
date: 2016-04-12
last_modified_at: 2016-04-18
categories: [Python Notebook]
tags: [tensorflow, deep-learning, python, xor, machine-learning]
migrated_from: https://ahmedhanibrahim.wordpress.com/2016/04/12/getting-started-with-tensorflow/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2016/04/12/getting-started-with-tensorflow/) on April 12, 2016, and has been migrated here.

TensorFlow is an open-source library created by Google for deep learning tasks. It represents matrix operations and data dependencies as a computation graph: edges (called *tensors*) carry data between operation nodes.

Key advantages of TensorFlow include reducing development time by abstracting mathematical operations and offering GPU support.

As part of a machine learning master's course assignment, I implemented a solution for the XOR learning problem using TensorFlow — my first time using the library. The model structure is shown below:

![XOR model](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2016/04/xor.png)

**Notebook:** [xor_learning.ipynb on GitHub](https://github.com/AhmedHani/CS624-ML/blob/master/Assignments/xor_learning.ipynb)
