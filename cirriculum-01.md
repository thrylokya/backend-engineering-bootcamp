# CURRICULUM-01.md (Part 1)
## Backend Engineering Mastery Bootcamp
### Version 1.0
### Days 1–6 (Week 1)

---

# Philosophy

This curriculum is **immutable**.

It defines **what** must be studied.

The Registry defines **where you currently are**.

Daily chats expand each lesson but **never change this curriculum**.

---

# Week 1
## Theme

> Learn **how to solve problems**, not just solve them.

This week is about fixing the single biggest weakness experienced engineers usually have after years in industry:

> **Pattern Recognition**

By the end of Week 1 you should:

- Stop jumping into coding.
- Learn to classify problems.
- Think in constraints.
- Build confidence again.
- Understand arrays deeply.
- Build your first engineering lab.

---

# W01-D01

## Theme

Learning How Interviews Actually Work

---

## Mission

Learn how to analyze a problem before writing code.

---

## DSA

### Mental Model

Arrays are contiguous memory.

Think about:

- Memory
- Cache
- Index
- Traversal

---

### Pattern

Array Traversal

---

### Recognition Drill

Classify (Do NOT solve)

- Two Sum
- Contains Duplicate
- Move Zeroes
- Best Time to Buy Stock
- Running Sum
- Build Array from Permutation
- Concatenation of Array
- Rotate Array
- Product Except Self
- Majority Element

For every problem answer

- What is the input?
- What is the output?
- What is optimized?
- Can input be modified?
- Is ordering important?
- Extra memory allowed?

---

### Problems

Solve

1. LC 1920 - Build Array from Permutation

Focus

- Traversal
- Index Mapping

---

2. LC 1480 - Running Sum

Focus

- Prefix Thinking

---

3. LC 1929 - Concatenation of Array

Focus

- Index Arithmetic

---

### Pattern Notes

Create notes answering

Why are these NOT different patterns?

---

## Java

Topic

Arrays vs ArrayList

Read

OpenJDK

ArrayList.java

Methods

- add()
- grow()
- ensureCapacity()

Questions

Why 1.5x growth?

Why not 2x?

Why not linked list?

---

## JVM

Topic

Object Layout

Read

- Object Header
- References
- Primitive Arrays

Questions

Why is int[] faster than Integer[]?

---

## Engineering Lab

LAB-01

Build

DynamicArray

Features

- add
- get
- set
- remove
- resize

Restrictions

No ArrayList

No Generics

---

## LLD

Design

Dynamic Array

Discuss

Responsibilities

Methods

Complexities

Tradeoffs

---

## HLD

Discussion

Why do databases love contiguous memory?

Examples

- ClickHouse
- Apache Arrow
- Vectorized Execution

---

## Open Source

Read

ArrayList.java

Understand

- resize()
- add()

---

## Revision

None

---

## Deliverables

- Dynamic Array
- 3 LC Problems
- Pattern Notes
- Java Notes

---

## Expected Outcome

Confidence

2/10 → 4/10

---

# W01-D02

## Theme

Complexity & Memory Thinking

---

## Mission

Stop guessing Big-O.

Understand it.

---

## DSA

Pattern

Linear Scan

---

### Theory

Understand

- O(1)
- O(n)
- O(n²)

Visualize operations.

---

### Problems

1. LC 485 - Max Consecutive Ones

Focus

Single traversal

---

2. LC 1295 - Find Numbers with Even Number of Digits

Focus

Traversal

---

3. LC 414 - Third Maximum Number

Focus

Tracking state

---

### Recognition Drill

Given 10 array problems

Predict complexity before solving.

---

## Java

Read

System.arraycopy()

Arrays.copyOf()

Questions

Why is arraycopy native?

---

## JVM

Topic

CPU Cache

Read

- Cache Line
- Spatial Locality

Question

Why are arrays cache-friendly?

---

## Engineering Lab

Extend Dynamic Array

Implement

- insert(index)
- delete(index)

Benchmark

100K inserts

---

## LLD

Resizable Array API

Discuss

Public API

Exceptions

Edge cases

---

## HLD

Memory Locality

Examples

Redis

Column Stores

---

## Open Source

Arrays.java

---

## Revision

Review Day 1

Without code

Explain Dynamic Array.

---

## Deliverables

- Benchmarks
- Updated Dynamic Array
- Notes

---

## Expected Outcome

Confidence

4/10 → 5/10

---

# W01-D03

## Theme

Prefix Thinking

---

## Mission

Learn cumulative computation.

---

## DSA

Pattern

Prefix Sum

Mental Model

Don't recompute.

Reuse previous work.

---

### Problems

1. LC 724 Pivot Index

2. LC 1991 Find Middle Index

3. LC 303 Range Sum Query Immutable

---

Recognition Drill

Identify

When should Prefix Sum be used?

---

## Java

Read

Arrays.parallelPrefix()

Question

Why does Java include this?

---

## JVM

Primitive Arrays

Why int[] is faster than Integer[].

---

## Engineering Lab

Build

Prefix Sum Utility

API

- rangeSum()
- build()

---

## LLD

Range Query Service

---

## HLD

Analytics Engine

How dashboards answer

"Sales last 30 days"

---

## Open Source

Arrays.parallelPrefix()

---

## Revision

Problems from Day 1

Without looking

Explain recognition.

---

## Deliverables

Prefix Utility

---

# W01-D04

## Theme

In-place Algorithms

---

## Mission

Learn memory optimization.

---

## DSA

Pattern

Two Index Manipulation

(Not Two Pointers yet)

---

### Problems

1. LC 27 Remove Element

2. LC 26 Remove Duplicates

3. LC 283 Move Zeroes

---

Recognition Drill

Identify

Can this be solved in-place?

---

## Java

Read

Collections.swap()

---

## JVM

Reference Assignment

Object Copy

Reference Copy

---

## Engineering Lab

Add

reverse()

rotate()

to Dynamic Array

---

## LLD

Mutable Collection

---

## HLD

Large Dataset Processing

Why in-place matters.

---

## Revision

Prefix Sum

---

## Deliverables

Updated Dynamic Array

---

# W01-D05

## Theme

Simulation

---

## Mission

Don't optimize early.

Build correctly first.

---

## DSA

Pattern

Simulation

---

### Problems

1. LC 66 Plus One

2. LC 989 Add to Array Form

3. LC 1089 Duplicate Zeros

---

Recognition Drill

Simulation vs Mathematical Shortcut

---

## Java

Read

StringBuilder

Why mutable?

---

## JVM

Garbage Generation

Object Allocation

---

## Engineering Lab

Dynamic Array Iterator

---

## LLD

Iterator Design

---

## HLD

Batch Processing Pipeline

---

## Revision

Days 1–4

---

## Deliverables

Iterator

---

# W01-D06 (Saturday - 6 Hours)

## Theme

Week 1 Consolidation

---

## Session 1

Re-solve

All 14 problems

Without notes.

---

## Session 2

Engineering Review

Review

Dynamic Array

Refactor

Improve naming

Add tests

---

## Session 3

Benchmark

Using JMH

Compare

- Your Dynamic Array
- ArrayList

Measure

- add()
- get()
- insert()
- remove()

---

## Session 4

Java Deep Dive

Read completely

ArrayList.java

Topics

- resize
- modCount
- fail-fast iterator

---

## Session 5

Mock Interview

45 Minutes

Questions

- Why arrays?
- Why contiguous memory?
- Why resize?
- Why 1.5x?
- Why ArrayList over LinkedList?
- When should you NOT use arrays?

---

## Session 6

Week Review

Write exactly one page answering

- What patterns can I recognize immediately?
- What mistakes do I repeat?
- What confused me?
- What engineering concepts became clearer?

---

## Week 1 Success Criteria

You are ready for Week 2 only if you can:

- ✅ Recognize basic array traversal in under 30 seconds.
- ✅ Explain ArrayList resizing.
- ✅ Implement a Dynamic Array from scratch.
- ✅ Explain why contiguous memory improves cache performance.
- ✅ Solve all Week 1 problems without looking at previous solutions.
- ✅ Connect arrays to real systems such as columnar storage, analytics, or in-memory data structures.

---

**End of CURRICULUM-01.md (Part 1)**

**Next file:** `CURRICULUM-01.md.part2` (Days 7–12 / Week 2: Array Patterns, Prefix Sum Mastery, Difference Arrays, Matrix Traversal, Circular Buffers)
