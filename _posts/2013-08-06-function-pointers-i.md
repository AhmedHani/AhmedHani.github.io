---
layout: post
title: "Function Pointers I"
date: 2013-08-06
last_modified_at: 2014-05-18
categories: [C/C++]
tags: [c++, pointers, function-pointers]
migrated_from: https://ahmedhanibrahim.wordpress.com/2013/08/06/function-pointers-2/
---

> **Note:** This post was originally published on [AH's Blog (WordPress)](https://ahmedhanibrahim.wordpress.com/2013/08/06/function-pointers-2/) on August 6, 2013, and has been migrated here.

It is a long time since I have written C++ code — I admit it is too hard to leave this language and continue coding with C#, Java, or Python. C++ has its own magic 😄

In this post I'm refreshing my C++ memory by playing with some pointers. Specifically: **Function Pointers**. We don't use them that often in day-to-day projects, but they are worth understanding when talking about pointers in depth.

We all know that a pointer is a variable that holds the address of another variable. A function pointer is the same concept, but instead of pointing to a variable, it points to a function.

---

## Declaration

```cpp
int (*func_ptr)(); // points to a function that takes no arguments and returns int
```

The parentheses around `*func_ptr` are essential. Without them:

```cpp
int *func_ptr(); // this is a function declaration that returns int*
```

Function pointers can point to functions of any return type:

```cpp
long   (*func_ptr_long)();  // points to a long-returning function
double (*func_ptr_doub)();  // points to a double-returning function
```

The declaration must match the target function's return type **and** argument list:

```cpp
int (*func_ptr)();          // no arguments
int (*func_ptr1)(int);      // one int argument
int (*func_ptr2)(int, int); // two int arguments
```

---

## Assignment

```cpp
#include <stdio.h>

int func() {}

int main() {
    int (*func_ptr)() = func; // assign func to the pointer
    return 0;
}
```

The return type and argument count must match — mismatches are compile errors:

```cpp
#include <stdio.h>

int func() {}
int func2(int x) {}

int main() {
    double (*func_ptr)()  = func;  // Error: return type mismatch
    int    (*func_ptr2)() = func2; // Error: argument count mismatch
    return 0;
}
```

---

## Calling a Function via a Pointer

**Explicit dereference:**

```cpp
#include <stdio.h>

int func(int a) {}

int main() {
    int (*func_ptr)(int) = func;
    (*func_ptr)(9); // explicit dereference call
    return 0;
}
```

**Implicit dereference** (reads like a normal function call):

```cpp
#include <stdio.h>

int func(int a) {}

int main() {
    int (*func_ptr)(int) = func;
    func_ptr(9); // implicit dereference call
    return 0;
}
```

Full example — storing and printing a return value:

```cpp
#include <stdio.h>

int Sum(int a, int b) {
    return a + b;
}

int main() {
    int (*func_ptr)(int, int) = Sum;
    int Result = func_ptr(3, 4);
    printf("%d\n", Result); // output: 7
    return 0;
}
```

---

## Function Pointers and Overloading

Function pointers work correctly with overloaded functions — the compiler resolves which overload to bind based on the pointer's signature:

```cpp
#include <stdio.h>

int func(int a)        { return a + 1; }
int func(int a, int b) { return a + b; }

int main() {
    int (*func_ptr1)(int);       // binds to func(int)
    int (*func_ptr2)(int, int);  // binds to func(int, int)
    func_ptr1 = func;
    func_ptr2 = func;
    return 0;
}
```

---

## Function Pointers in Memory

Like a variable pointer, a function pointer holds a memory address. The distinction is that variable pointers point to a data location, while function pointers point to executable code — specifically to the entry point of the function after compilation.

---

## Practical Use Case: Parameterizing Behavior

Function pointers shine when you want to change the behavior of a function at call time without duplicating logic. Here's an example using Bubble Sort — the comparison strategy is injected via a function pointer:

```cpp
#include <stdio.h>

int Arr[] = {2, 3, 1, 6, 2, 0, 0, 1};

void BubbleSort(int Length, bool (*func_ptr)(int, int)) {
    for (int i = 0; i < Length - 1; i++) {
        for (int j = 0; j < Length - i - 1; j++) {
            if (func_ptr(Arr[j], Arr[j + 1])) {
                int Tmp   = Arr[j];
                Arr[j]    = Arr[j + 1];
                Arr[j + 1] = Tmp;
            }
        }
    }
}

inline bool Increasing(int First, int Second) { return First > Second; }
inline bool Decreasing(int First, int Second) { return First < Second; }

int main() {
    BubbleSort(8, Increasing);
    for (int i = 0; i < 8; i++) printf("%d\n", Arr[i]);

    puts("----");

    BubbleSort(8, Decreasing);
    for (int i = 0; i < 8; i++) printf("%d\n", Arr[i]);

    return 0;
}
```

The same `BubbleSort` function sorts ascending or descending simply by swapping the comparator — no code duplication needed.

> **Note:** Virtual functions and polymorphism can accomplish similar goals in an object-oriented style. In modern C++, **lambda functions** (C++11) offer an even more ergonomic alternative — a topic worth exploring next.
