---
layout: post
title: "Facade Pattern"
date: 2014-10-05
last_modified_at: 2014-10-05
categories: [OOP, Design Patterns, Structural Patterns]
tags: [design-patterns, csharp, oop, structural-patterns]
migrated_from: https://ahmedhanibrahim.wordpress.com/2014/10/05/facade-pattern/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2014/10/05/facade-pattern/) on October 5, 2014, and has been migrated here.

The Facade pattern is one of the most important patterns to know when working on large systems with many interacting objects.

---

## Concept

When working with a complex system, you may find yourself needing to manage many individual objects directly. The Facade pattern solves this by introducing a single simplified entry point — a *facade* — that sits between the programmer and the system's subsystems.

As the name suggests, the facade is the front face: the programmer interacts with one object, and that object orchestrates the rest.

![Facade diagram](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/facade3.png)

---

## Scenario

You want to build a form application with `TextBox`, `Label`, and `Button` controls. Each control has configurable properties (position, width, height, color). Rather than managing each control individually, you expose them through a single `GraphicalUserInterface` facade class.

---

## Implementation

Define an abstract base class for all UI handlers:

```csharp
public abstract class Design
{
    public abstract void setPosition(double x, double y);
    public abstract void setWidth(double x);
    public abstract void setHeight(double y);
    public abstract void setColor(int r, int g, int b);
}
```

Implement concrete controls:

```csharp
public class TextBox : Design
{
    public override void setPosition(double x, double y)
        => Console.WriteLine("TextBox has been set at " + x + " " + y);

    public override void setWidth(double x)
        => Console.WriteLine("TextBox width is " + x + " now");

    public override void setHeight(double y)
        => Console.WriteLine("TextBox Height is " + y + " now");

    public override void setColor(int r, int g, int b) { }
}
```

```csharp
public class Button : Design
{
    public override void setPosition(double x, double y)
        => Console.WriteLine("Button has been set at " + x + " " + y);

    public override void setWidth(double x)
        => Console.WriteLine("Button width is " + x + " now");

    public override void setHeight(double y)
        => Console.WriteLine("Button Height is " + y + " now");

    public override void setColor(int r, int g, int b)
        => Console.WriteLine("Button Color is set!");
}
```

**UML structure:**

![UML Facade](https://ahmedhanibrahim.wordpress.com/wp-content/uploads/2014/10/umlfacade.png)

Now build the facade class — it owns all the controls and exposes simple factory methods:

```csharp
public class GraphicalUserInterface
{
    private Design textBox;
    private Design button;
    private Design label;

    public GraphicalUserInterface()
    {
        this.textBox = new TextBox();
        this.label   = new Label();
        this.button  = new Button();
    }

    public void createTextBox()
    {
        this.textBox.setPosition(1.0, 1.0);
        this.textBox.setWidth(1.0);
        this.textBox.setHeight(1.0);
    }

    public void createLabel()
    {
        this.label.setPosition(2.0, 2.0);
        this.label.setWidth(2.0);
        this.label.setHeight(2.0);
    }

    public void createButton()
    {
        this.button.setPosition(3.0, 3.0);
        this.button.setWidth(3.0);
        this.button.setHeight(3.0);
    }
}
```

The client interacts only with the facade:

```csharp
static void Main(string[] args)
{
    GraphicalUserInterface buildGUI = new GraphicalUserInterface();

    buildGUI.createButton();
    buildGUI.createLabel();
    buildGUI.createTextBox();
}
```

---

## Advantages

- Reduces the number of objects the client needs to interact with directly.
- Simplifies complex systems into manageable subsystems.

Full implementation: [GitHub — Design-Patterns/Facade](https://github.com/AhmedHani/Design-Patterns/tree/master/Facade)

---

## References

- *Design Patterns Explained* (Shalloway & Trott)
