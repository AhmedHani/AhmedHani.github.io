---
layout: post
title: "Data Normalization and Standardization for Neural Networks Output Classification"
date: 2014-10-10
last_modified_at: 2014-11-15
categories: [Machine Learning, Neural Network, Data Mining]
tags: [neural-networks, normalization, standardization, data-encoding, information-theory, python, machine-learning]
migrated_from: https://ahmedhanibrahim.wordpress.com/2014/10/10/data-normalization-and-standardization-for-neural-networks-output-classification/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2014/10/10/data-normalization-and-standardization-for-neural-networks-output-classification/) on October 10, 2014, and has been migrated here.

*This article assumes familiarity with Neural Networks and algorithms like Backpropagation.*

---

## Agenda

- Data encoding for input
  - Binary
  - Positive-Negative
  - Manhattan & Euclidean
  - Category encoding
- Normalization vs. Standardization
  - Min-Max Normalization
  - Gaussian Normalization
- Data Decoding
  - Softmax activation function
  - Mean Squared Error (MSE)
  - Entropy (Information Theory)
  - Mean Cross Entropy (MCE)
- MSE vs. MCE

---

## Why Encode Data?

Neural networks operate exclusively on numeric data. Algorithms like backpropagation rely on matrix operations, weight updates, and error functions — all of which require numbers. But real-world datasets often include non-numeric features: names, nationalities, gender, food preferences.

The solution is **data encoding**: transforming categorical and string values into numeric representations that the network can process. This is sometimes called *data standardization*.

Consider the following training set:

| ID | Name  | Gender | Nationality | Preferred Food | Wage |
|----|-------|--------|-------------|----------------|------|
| 1  | Hani  | Male   | French      | Rice           | 30   |
| 2  | Saad  | Male   | Italian     | Pizza          | 40   |
| 3  | Sofia | Female | Russian     | Spaghetti      | 15   |

![Data matrix](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat30.png)

We process each column:

- **ID**: Unique identifier only — excluded from encoding.
- **Name**: Not involved; map each ID to its name externally to avoid duplicate handling.
- **Gender, Nationality, Preferred Food**: Categorical — require encoding.
- **Wage**: Numeric — requires normalization.

---

## Encoding Categorical Data

### Gender (Binary / 2-value categories)

Three approaches:

**0-1 Encoding (Binary):** Assign `Male = 0`, `Female = 1` (or vice versa).

**+1/-1 Encoding:** Use `+1` and `-1` instead of `0` and `1` when a zero value in the input would cause problems (e.g., in multiplicative operations).

**Manhattan Encoding (vector pair):** Represent each value as a pair: `Male = [0, 1]`, `Female = [1, 0]`. This generalizes naturally to more than two categories — add `-1` when there are more than four values (Euclidean encoding).

### Nationality / Preferred Food (Multi-value categories)

For categories with many possible values, use an **identity matrix encoding**:

1. Count the distinct values in the column. Say there are 3 nationalities: *m* = 3.
2. Create a matrix *N* of size *m × 1*.

![N matrix](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat31.png)

3. Create a corresponding identity matrix *A* of size *m × m*.

![Identity matrix](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat2.png)

4. Set *N = A* — each category maps to a distinct one-hot row:

![N = A assignment](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat32.png)

```
French  = [1, 0, 0]
Italian = [0, 1, 0]
Russian = [0, 0, 1]
```

**Pros:** Dynamic, easy to implement, easy to understand.

**Cons:** Memory-intensive for large cardinalities; performance degrades with very large datasets.

Replace each string value in the dataset with its corresponding vector.

---

## Normalization vs. Standardization

The Wage column is already numeric, but we still need to normalize it. Raw numeric values may dominate other features in scale, hurting training quality.

The relationship between normalization and standardization mirrors that of recursion and backtracking: every standardization is a normalization, but not every normalization is a standardization.

### Min-Max Normalization

Scales values to the **[0, 1]** range:

![Min-Max formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat4.png)

To center around zero in **[-1, 1]**:

![Min-Max [-1,1] formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat5.png)

### Gaussian Normalization (Standardization)

Computes the **Z-score**, transforming data to have **mean = 0** and **variance = 1**:

![Gaussian formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat6.png)

![Gaussian distribution](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat7.png)

Values close to the mean normalize to near zero; values above the mean are positive, below are negative. This retains directional information that Min-Max normalization discards.

> Experience shows that Gaussian normalization generally produces better training outcomes than Min-Max.

**Applying Gaussian normalization to the Wage column:**

Step 1 — Compute the mean:

![Step 1](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat8.png)

Step 2 — Compute the standard deviation:

![Step 2](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat9.png)

Step 3 — Apply the Z-score formula to each wage value:

![Step 3](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat10.png)

The wage of `30` normalizes to `0.09` — close to zero because `30 ≈ 28.33` (the mean), and positive because it is above the mean.

**Final encoded input matrix:**

![Final matrix](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat11.png)

---

## Data Decoding

After encoding, the neural network's output is also in encoded form. To interpret predictions, we need to decode them.

### Decoding as Probability

Given the one-hot encoding:
```
French  = [1, 0, 0]
Italian = [0, 1, 0]
Russian = [0, 0, 1]
```

A sample output like `[0.6, 0.3, 0.1]` can be interpreted as a probability distribution. The highest value indicates the predicted class. This works when values are in **[0, 1]** and sum to 1.

### Softmax Activation

When output values exceed 1 (e.g., `[3.0, 2.5, 7.0]`), use the **softmax function** to map them to valid probabilities:

![Softmax formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat12.png)

**Example calculation:**

Step 1:

![Softmax step 1](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat13.png)

Step 2:

![Softmax step 2](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat14.png)

Result: `[0.017, 0.010, 0.971]` — now usable for classification.

```python
def softMax(self, output):
    newOutput = [0.0 for i in range(0, self.numberOfOutputs)]
    total = 0.0

    for i in range(0, self.numberOfOutputs):
        total += round(output[i], 2)

    for i in range(0, self.numberOfOutputs):
        newOutput.append(output[i] / total)

    return newOutput
```

---

## Error Measurement

In practice, the network output won't always match the target. Consider:

```
Output           Target
[0.3, 0.3, 0.4]  [1, 0, 0]   ← Wrong prediction
```

We need to quantify how wrong the prediction is. Two common approaches:

### Mean Squared Error (MSE)

MSE measures the average squared difference between output and target vectors.

Given:
```
Output           Target
[0.3, 0.3, 0.4]  [1, 0, 0]
[0.2, 0.3, 0.5]  [0, 1, 0]
```

Training item 1:

![MSE item 1](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat16.png)

Training item 2:

![MSE item 2](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat17.png)

Average:

![MSE average](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat18.png)

A high MSE indicates the network needs more training.

```python
def getMeanSquaredError(self, trueTheta, output):
    sumOfSum = 0.0

    for i in range(0, self.numberOfOutputs):
        sumOfSum += pow((trueTheta[i] - output[i]), 2)

    return sumOfSum / self.numberOfOutputs
```

---

### Entropy and Cross Entropy

**Entropy** from Information Theory measures the average number of bits needed to encode an event — i.e., the randomness in a probability distribution.

For a variable **X** with distribution `[0.3, 0.2, 0.1, 0.4]`:

![Entropy formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat19.png)

![Entropy calculation](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat20.png)

**Cross Entropy (CE)** estimates how close probability model **B** is to model **A**:

![CE formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat22.png)

Given a target distribution **T = [0.2, 0.1, 0.7]** and two models:
- **X = [0.3, 0.1, 0.6]**
- **Y = [0.3, 0.3, 0.4]**

CE(T, X):

![CE T,X](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat23.png)

CE(T, Y):

![CE T,Y](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat24.png)

Model **X** is much closer to **T** than **Y**.

---

### Mean Cross Entropy (MCE)

**MCE** is the preferred approach for evaluating classification output. It measures the average cross entropy between the network output and target across all training items.

MCE formula:

![MCE formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat25.png)

Using the same example as MSE:

Training item 1:

![MCE item 1](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat26.png)

Training item 2:

![MCE item 2](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat27.png)

MCE result:

![MCE result](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/mat29.png)

Target is 1; result is 1.7, indicating a 0.7 error.

```python
def getMeanCrossEntropy(self, trueTheta, output):
    total = 0.0

    for i in range(0, self.numberOfOutputs):
        total += (math.log2(trueTheta[i]) * output[i])

    return -1.0 * total / self.numberOfOutputs
```

---

## MSE vs. MCE

As with most things in machine learning: *it depends on the problem*. Both affect the gradient computation in backpropagation differently. In practice, MCE tends to work better for classification tasks with softmax output, while MSE is more common for regression.

---

## References

- Stanford Machine Learning Course
- [Wikipedia: Entropy (Information Theory)](http://en.wikipedia.org/wiki/Entropy_(information_theory))
- [Rochester CSC248 Lecture Notes](http://www.cs.rochester.edu/u/james/CSC248/Lec6.pdf)
- [AI FAQ — Neural Nets Part 2](http://www.faqs.org/faqs/ai-faq/neural-nets/part2/section-7.html)
- James McCaffrey, *Neural Networks*
