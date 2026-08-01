# Day 1 — Thinking Like an Interviewer

> **Duration:** 5–6 Hours  
> **Theme:** Learn to analyze problems before writing code.  
> **Objective:** Build the foundation of array-based problem solving by understanding how arrays work in memory, recognizing common interview patterns, and implementing a Dynamic Array from scratch.

---

# Learning Outcomes

By the end of Day 1, you should be able to:

- Explain how arrays are stored in memory.
- Describe why arrays provide O(1) random access.
- Recognize common array problem patterns.
- Solve simple array traversal problems.
- Explain the difference between arrays and `ArrayList`.
- Understand how Java's `ArrayList` grows internally.
- Build your own Dynamic Array implementation.

---

# Session 1 — Understanding Arrays (45–60 min)

## Goal

Understand what an array actually is.

Most people know how to use arrays.
Few understand how they work.

---

## Topics

### Memory Layout

- Contiguous memory
- Fixed-size allocation
- Index calculation
- Random access

### Why Arrays Are Fast

Understand why

```text
arr[100]
```

takes constant time.

Learn:

- Base Address
- Offset calculation
- CPU cache friendliness

---

## Complexity

| Operation | Complexity |
|-----------|------------|
| Access | O(1) |
| Update | O(1) |
| Search | O(n) |
| Insert | O(n) |
| Delete | O(n) |

---

## Read

- Arrays vs Linked Lists
- Static Arrays vs Dynamic Arrays

---

# Session 2 — Pattern Recognition (45 min)

> **Do NOT solve these problems yet.**

The objective is to classify the problem before thinking about code.

For each problem, answer:

- What is the input?
- What is the expected output?
- Can one traversal solve it?
- Does it require extra memory?
- Which pattern does it belong to?

---

## Problems

- Two Sum
- Contains Duplicate
- Running Sum
- Move Zeroes
- Rotate Array
- Product of Array Except Self
- Majority Element
- Best Time to Buy and Sell Stock

---

## Deliverable

Create a markdown document containing:

- Pattern
- Reasoning
- Complexity guess

---

# Session 3 — Coding Practice (90 min)

Solve the following problems.

## Problem 1

### Build Array from Permutation

Focus:

- Array indexing
- Traversal

---

## Problem 2

### Running Sum of 1D Array

Focus:

- Prefix accumulation
- Sequential traversal

---

## Problem 3

### Concatenation of Array

Focus:

- Copying arrays
- Index manipulation

---

## Rules

Before coding:

Write down

- Brute force idea
- Better approach
- Time Complexity
- Space Complexity

Only then start coding.

---

# Session 4 — Java Deep Dive (60 min)

## Read ArrayList Source Code

Focus on understanding—not memorizing.

Study:

- Internal array
- Constructor
- add()
- grow()
- ensureCapacity()
- resize()

---

## Questions to Answer

Why:

- is the default capacity 10?
- does Java grow by 1.5x?
- doesn't Java double the capacity?
- doesn't Java shrink automatically?
- is ArrayList faster than LinkedList for most workloads?

---

## Deliverable

Write a one-page summary in your own words.

---

# Session 5 — JVM Fundamentals (45 min)

Understand how arrays exist inside the JVM.

Topics:

- Object Header
- Class Metadata
- References
- Primitive Arrays
- Wrapper Objects

---

## Learn the Difference

```java
int[]
```

vs

```java
Integer[]
```

Explain:

- Memory consumption
- Cache locality
- Performance
- Boxing
- Unboxing

---

# Session 6 — Engineering Lab (90 min)

## Build Your Own Dynamic Array

Restrictions

- No ArrayList
- No Collections
- No Generics

---

## Required API

```java
add(int value)

get(int index)

set(int index, int value)

remove(int index)

size()

isEmpty()

resize()
```

---

## Stretch Goals

Implement

```java
contains()

clear()

print()

capacity()
```

---

## Think About

When should resizing happen?

How much should capacity grow?

---

# Session 7 — Low-Level Design (30 min)

Design your Dynamic Array before implementing it.

Consider:

- Public API
- Internal data members
- Responsibilities
- Error handling
- Complexity of each operation

Draw a simple class diagram if possible.

---

# Session 8 — Real-World Engineering (20 min)

Discuss why contiguous memory matters.

Examples:

- Java ArrayList
- Apache Arrow
- ClickHouse
- Columnar databases
- Analytics engines

Key takeaway:

Modern high-performance systems are built around contiguous memory because it maximizes CPU cache utilization.

---

# Deliverables

- [ ] Pattern recognition notes
- [ ] Build Array from Permutation solved
- [ ] Running Sum solved
- [ ] Concatenation of Array solved
- [ ] Dynamic Array implemented
- [ ] Java ArrayList notes
- [ ] JVM memory notes
- [ ] Dynamic Array design document

---

# Reflection Questions

Answer these before ending the day.

1. Why are arrays O(1) for random access?
2. Why are inserts O(n)?
3. Why is contiguous memory important?
4. Why does Java use Dynamic Arrays instead of Linked Lists?
5. What is the biggest difference between `int[]` and `Integer[]`?
6. If you were designing `ArrayList`, what growth factor would you choose and why?

---

# End-of-Day Success Criteria

You are ready to move to Day 2 if you can:

- Explain array memory layout without notes.
- Solve simple traversal problems confidently.
- Describe `ArrayList` internals.
- Implement a working Dynamic Array from scratch.
- Explain every operation's time complexity without memorization.
