---
layout: post
title: "Adapter Pattern"
date: 2014-10-03
categories: [OOP, Design Patterns, Structural Patterns]
tags: [design-patterns, java, oop, structural-patterns]
migrated_from: https://ahmedhanibrahim.wordpress.com/2014/10/03/adapter-pattern/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2014/10/03/adapter-pattern/) on October 3, 2014, and has been migrated here.

The Adapter pattern is commonly used in applications that expose many functions or operations all serving a specific entity, and you want to make incompatible interfaces work together.

---

## Concept

Think of your computer's calculator app — it has a Standard mode with basic operations (add, subtract), and a Scientific mode that extends those with advanced operations (multiply, divide). The basic operations have been *adapted* into the scientific interface.

The Adapter pattern formalizes this: we have two or more different classes, and we want to make them compatible without modifying either.

---

## Scenario

1. A basic standard calculator provides `add` and `subtract`.
2. We want a unified calculator that also supports `product` and `division` via the same interface.

---

## Implementation

Define the standard operations interface:

```java
public interface Operations {
    public double calculate(double x, double y, char operation);
}
```

Define the advanced operations interface:

```java
public interface AdvancedOperations {
    public double product(double x, double y);
    public double divide(double x, double y);
}
```

Implement the scientific calculator:

```java
public static class Scientific implements AdvancedOperations {
    @Override
    public double product(double x, double y) { return x * y; }

    @Override
    public double divide(double x, double y)  { return x / y; }
}
```

Create the adapter — it implements the standard `Operations` interface but internally delegates to `Scientific` when needed:

```java
public static class CalculatorAdapter implements Operations {
    Scientific scientificMode;

    public CalculatorAdapter(String mode) {
        if (mode.equals("Scientific")) {
            this.scientificMode = new Scientific();
        }
    }

    @Override
    public double calculate(double x, double y, char operation) {
        if (operation == '*') {
            System.out.println("Scientific");
            return this.scientificMode.product(x, y);
        } else if (operation == '/') {
            System.out.println("Scientific");
            return this.scientificMode.divide(x, y);
        }

        if (operation == '+') {
            System.out.println("Standard");
            return x + y;
        } else if (operation == '-') {
            System.out.println("Standard");
            return x - y;
        }

        return 0.0;
    }
}
```

The final unified calculator:

```java
public static class Calculator implements Operations {
    CalculatorAdapter calculatorAdapter;

    @Override
    public double calculate(double x, double y, char operation) {
        this.calculatorAdapter = new CalculatorAdapter("Scientific");
        return this.calculatorAdapter.calculate(x, y, operation);
    }
}
```

Full implementation: [GitHub — Design-Patterns/Adapter](https://github.com/AhmedHani/Design-Patterns/tree/master/Adapter)

---

## References

- GoF, *Design Patterns: Elements of Reusable Object-Oriented Software*
- [Wikipedia: Adapter Pattern](http://en.wikipedia.org/wiki/Adapter_pattern)
