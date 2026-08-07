# Day 3 — Prefix Sum & Reusing Computation

> **Duration:** 5–6 Hours  
> **Theme:** Stop recomputing work. Learn to build on previous computations.  
> **Objective:** Understand the Prefix Sum pattern, recognize cumulative computation problems, and appreciate why precomputation is a fundamental optimization technique used in production systems.

---

# Learning Outcomes

By the end of Day 3, you should be able to:

- Explain what a Prefix Sum is and when to use it.
- Identify problems that benefit from precomputation.
- Convert repeated O(n) computations into O(1) lookups.
- Implement a reusable Prefix Sum utility.
- Explain the tradeoff between preprocessing time and query performance.
- Relate Prefix Sums to real-world systems like dashboards and analytics databases.

---

# Session 1 — Understanding Prefix Sum (60 min)

## Goal

Learn one of the simplest but most powerful optimization techniques.

Instead of repeatedly calculating the same information,
calculate it **once** and reuse it.

---

## Problem

Suppose you have the array:

```text
[2, 5, 1, 7, 3]
```

Find the sum of:

- index 0–3
- index 1–4
- index 2–3
- index 0–4

How much work are we repeating?

---

## Build the Prefix Array

```text
Original:
[2, 5, 1, 7, 3]

Prefix:
[2, 7, 8, 15, 18]
```

Observe:

```
prefix[i]
=
sum of elements from 0...i
```

---

## Complexity Comparison

| Approach | Preprocessing | Query |
|-----------|--------------|-------|
| Iterate every time | O(0) | O(n) |
| Prefix Sum | O(n) | O(1) |

---

## Key Insight

Sometimes we intentionally spend time **once**
to make future operations much faster.

---

# Session 2 — Pattern Recognition (45 min)

## Goal

Learn to recognize Prefix Sum problems.

---

## Recognition Clues

Look for phrases like:

- Sum between two indices
- Range Sum
- Multiple queries
- Continuous subarray
- Running total
- Cumulative value

---

## Questions to Ask

Before coding:

- Am I repeatedly calculating the same values?
- Can previous work be reused?
- Would precomputation help?
- Is the array immutable?

---

## Practice Classification

Without coding, identify whether Prefix Sum is useful.

- Running Sum
- Pivot Index
- Find Middle Index
- Range Sum Query
- Product Except Self
- Maximum Subarray
- Moving Average
- Sliding Window Maximum

Explain your reasoning.

---

# Session 3 — Coding Practice (90 min)

Solve the following problems.

---

## Problem 1

### LC 724 — Find Pivot Index

Focus:

- Prefix Sum
- Left Sum
- Right Sum

---

## Problem 2

### LC 1991 — Find the Middle Index

Focus:

- Reusing cumulative information
- Avoiding repeated calculations

---

## Problem 3

### LC 303 — Range Sum Query (Immutable)

Focus:

- Designing a reusable data structure
- Preprocessing
- O(1) query performance

---

## Before Coding

Write:

- Brute-force solution
- Better solution
- Complexity analysis
- Why Prefix Sum helps

---

## After Coding

Ask yourself:

- What work did I eliminate?
- Could I reduce memory?
- Would this work for mutable arrays?

---

# Session 4 — Java Deep Dive (45 min)

## Goal

Explore how Java supports cumulative operations.

---

## Study

```java
Arrays.parallelPrefix()
```

Understand:

- What it does
- When it should be used
- Sequential vs Parallel computation

---

## Read About

- ForkJoinPool
- Parallel Arrays
- Parallel Streams

Questions:

- Why isn't parallel always faster?
- What is the overhead?
- When does parallelism become beneficial?

---

# Session 5 — JVM & Memory Thinking (45 min)

## Goal

Understand why sequential computation is efficient.

---

## Topics

- Sequential traversal
- Cache locality
- Memory bandwidth
- CPU prefetching

---

## Learn

Why building a Prefix Sum array is extremely cache friendly.

---

## Explain

- Why scanning an array once is efficient
- Why repeated scans become expensive
- How modern CPUs optimize sequential access

---

# Session 6 — Engineering Lab (90 min)

## Build a Prefix Sum Library

Implement:

```java
class PrefixSum
```

---

## Required APIs

```java
build(int[] arr)

rangeSum(int left, int right)
```

---

## Stretch Goals

Implement:

```java
average(left, right)

prefixAt(index)

printPrefix()
```

---

## Write Tests

Cover:

- Empty array
- Single element
- Entire range
- Middle range
- Invalid indices
- Negative numbers
- Large values

---

# Session 7 — Low-Level Design (30 min)

## Design a Range Query Service

Think about:

- Public API
- Constructor
- Internal storage
- Validation
- Error handling

---

## Discuss

Would you:

- Compute every query?
- Store Prefix Sums?
- Cache previous queries?

Why?

---

# Session 8 — High-Level Design (30 min)

## Real-World Engineering

Where are Prefix Sums used?

Examples:

### Analytics Dashboards

"What were today's sales?"

---

### Banking Systems

Daily balances

Monthly spending

---

### Time-Series Databases

Sensor data

Metrics

Monitoring

---

### Gaming

Health bars

Score accumulation

Experience points

---

### Database Systems

Materialized Views

Pre-aggregated tables

OLAP Cubes

---

## Key Takeaway

Precomputation is everywhere.

Many production systems intentionally perform expensive work once so future queries become extremely fast.

---

# Deliverables

- [ ] Prefix Sum notes
- [ ] Pattern recognition notes
- [ ] LC 724 solved
- [ ] LC 1991 solved
- [ ] LC 303 solved
- [ ] Prefix Sum utility implemented
- [ ] Unit tests completed
- [ ] Java parallelPrefix notes
- [ ] Range Query design document

---

# Reflection Questions

Answer these before ending the day.

1. What problem does Prefix Sum solve?
2. When is preprocessing worth the extra memory?
3. Why are repeated scans inefficient?
4. Why is Prefix Sum not useful for frequently changing arrays?
5. What is the tradeoff between preprocessing time and query time?
6. Where have you seen this optimization in real systems?

---

# End-of-Day Success Criteria

You are ready to move to Day 4 if you can:

- Recognize Prefix Sum problems without hints.
- Explain the preprocessing vs query-time tradeoff.
- Solve range sum problems confidently.
- Implement a reusable Prefix Sum utility.
- Explain why immutable data benefits from precomputation.
- Relate Prefix Sum concepts to real-world backend systems.
