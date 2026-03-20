---
layout: post
title: "[Week 2] Signals and Systems"
date: 2015-04-22
last_modified_at: 2015-04-22
categories: [Digital Image and Video Processing, Documentations]
tags: [image-processing, signals, convolution, digital-systems]
migrated_from: https://ahmedhanibrahim.wordpress.com/2015/04/22/week-2-signals-and-systems/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2015/04/22/week-2-signals-and-systems/) on April 22, 2015, and has been migrated here.

*Part of the [Digital Image and Video Processing](https://class.coursera.org/digital-002/) Coursera course documentation series.*

This week covered three segments: 2D/3D discrete signals, 2D complex exponential signals, and 2D convolution examples.

---

## 2D and 3D Discrete Signals

- **2D discrete signals** depend on 2 variables, each with a defined range.
  - Example: Images — a 2D grid of pixels, each with an (x, y) coordinate.
  - Each pixel stores 3 combined values: Red, Green, and Blue.
  - Grayscale images have RED = GREEN = BLUE.
- **3D discrete signals** add a third variable.
  - Videos are 3D: dimensions are (x, y, z) where z is the frame number.

![2D signal](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/04/2d.png)
![3D signal](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/04/3d.png)

---

## Discrete Unit Impulse

- An impulse signal is zero everywhere except at zero.
- The 2D discrete unit impulse of signals n₁, n₂ is 1 only when both n₁ = 0 and n₂ = 0.

![Unit Impulse formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/04/eq.png)

- Two signals are called **separable** if:

![Separable condition](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/04/eq2.png)

---

## Discrete Unit Step

- The 2D discrete unit step is 1 when both n₁ ≥ 0 and n₂ ≥ 0.

![Unit Step formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/04/eq3.png)

---

## Complex Exponential Signals

- The 2D complex exponential signal is defined as:

![Complex exponential](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/04/eq4.png)

- Where the periodicity terms satisfy:

![Periodicity](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/04/eq5.png)

- By Euler's formula:

![Euler formula](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/04/eq7.png)

---

## 2D Systems

A 2D system takes an input signal and applies a transformation **T[·]** to produce an output. There are three main types:

**Linear System**

Given input with weights, a system is linear if the output scales linearly with the input weights:

![Linear system output](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/04/eq15.png)

**Spatially Invariant System**

A system is spatially invariant (shift-invariant) if a spatial shift in the input produces the same shift in the output:

![Spatially invariant](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/04/eq17.png)

**Linear and Spatially Invariant (LSI) Systems**

LSI systems are fully characterized by their **impulse response** h(n₁, n₂). The output for any input x(n₁, n₂) is the 2D discrete convolution:

![LSI convolution](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2015/04/eq21.png)

---

## Applications of 2D Convolution

- Noise Reduction
- Edge Detection
- Sharpening
- Blurring

---

## References

- [Digital Image and Video Processing — Coursera](https://class.coursera.org/digital-002/)
- Wikipedia
