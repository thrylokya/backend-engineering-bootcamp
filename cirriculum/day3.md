# Day 3 — Prefix/Suffix Thinking & Reusing Computed Work

> **Duration:** 2 hours 45 minutes
> **Primary Theme:** Stop recomputing information that can be carried forward or precomputed.
> **Current Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory
> **Why Today Matters:** Many interview problems, database optimizations, analytics systems, caching strategies, and streaming algorithms reduce to one idea: **compute useful state once and reuse it instead of repeatedly scanning the same data.**

---

# Starting Point

Day 2 established a reasonably strong foundation in:

* deriving complexity from work performed
* linear-scan reasoning
* maintaining running state
* Dynamic Array mechanics
* resizing and copying
* sequential versus random memory access
* CPU cache locality
* JIT fundamentals
* hot-code detection
* the distinction between algorithmic complexity and real-world performance

You demonstrated particularly good intuition around:

* sequential memory access
* cache locality
* why adjacent array elements benefit from cache-line loading
* why benchmarks are necessary to determine actual performance
* JIT compilation of frequently executed code

However, Day 2 exposed several areas that still need tightening.

## Carry-Forward Gaps

### 1. Method Inlining

This was a new concept.

You should understand:

```text
method call
    ↓
JIT decides method is hot/small enough
    ↓
method body may be inserted at call site
    ↓
call overhead disappears
    ↓
further optimizations may become possible
```

The important point is not merely eliminating method-call overhead.

Inlining enables other compiler optimizations.

---

### 2. JMH

JMH was introduced but not completed.

You need to understand:

* why ordinary Java timing is unreliable
* warm-up iterations
* measurement iterations
* JIT tiered compilation
* dead-code elimination
* benchmark isolation

We do **not** need deep JMH expertise today.

We need the correct mental model.

---

### 3. Big-O vs Actual Runtime

Strengthen this distinction:

```text
Big-O
=
how work grows with input size
```

It does **not** mean:

```text
number of CPU instructions
```

Two algorithms can both be:

```text
O(n)
```

and have radically different real-world performance because of:

* cache locality
* allocation
* branch prediction
* vectorization
* bounds checks
* object indirection
* GC pressure

---

### 4. Exact Index Logic

Your high-level reasoning is generally stronger than your low-level index arithmetic.

Continue forcing yourself to derive:

```text
What does this index represent?
What is true before this index?
What is true after this index?
```

before writing `i + 1`, `i - 1`, etc.

---

# Today's Objectives

By the end of Day 3, you should be able to:

* recognize when repeated range work suggests precomputation
* derive prefix accumulation instead of memorizing it
* explain prefix and suffix state precisely
* solve Product of Array Except Self systematically
* distinguish preprocessing cost from query cost
* reason about the time/space trade-off of precomputation
* explain basic JIT method inlining
* explain why JMH requires warm-up
* validate Dynamic Array index invariants with tests
* connect prefix computation to backend analytics and metrics systems

---

# Session 1 — Day 2 Gap Closure

> **Time: 15 minutes**

No lecture first.

You should answer these interactively.

## Question 1

Two implementations both perform a single traversal of one million integers.

Both are:

```text
O(n)
```

One is 5× faster.

How can that happen?

Your explanation should eventually include some combination of:

* memory layout
* cache locality
* object indirection
* allocation
* JIT optimization

### Success Criterion

You must clearly separate:

```text
asymptotic complexity
```

from:

```text
actual execution cost
```

---

## Question 2

Consider:

```java
int getSize() {
    return size;
}
```

Suppose this method is called millions of times.

What might the JIT compiler do?

We will use this to repair the method-inlining mental model.

### Success Criterion

Explain:

* what inlining means
* why small hot methods are good candidates
* why the benefit is larger than simply avoiding a method call

---

## Question 3

Why shouldn't we benchmark Java like this?

```java
long start = System.nanoTime();

someMethod();

long end = System.nanoTime();
```

Expected discussion:

* JVM warm-up
* interpreter
* tiered compilation
* JIT
* dead-code elimination
* measurement noise

This leads directly into JMH.

---

# Session 2 — Core Concept: Prefix & Suffix Thinking

> **Time: 30 minutes**

## Goal

Learn to recognize this smell:

> "I'm calculating almost the same thing repeatedly."

Consider an array:

```text
[2, 4, 6, 8, 10]
```

Suppose I repeatedly ask:

```text
sum(0, 3)
sum(1, 4)
sum(2, 4)
sum(0, 4)
```

The naive approach repeatedly scans overlapping regions.

The question is:

> What work are we repeating?

Do not start with the prefix-sum formula.

Derive it from the repeated work.

---

# Mental Model

Suppose:

```text
prefix[i]
```

means:

> the aggregate of everything up to a precisely defined boundary.

Before coding anything, define what that boundary is.

For example, we could define:

```text
prefix[i] = sum of elements from 0 through i
```

or:

```text
prefix[i] = sum of the first i elements
```

Both are valid.

But they produce different index formulas.

This distinction matters.

---

# Core Principle

Prefix techniques trade:

```text
preprocessing
+
memory
```

for:

```text
cheaper future queries
```

This is an engineering trade-off, not merely a LeetCode pattern.

---

# Questions You Must Be Able to Answer

### Q1

If calculating one range sum requires:

```text
O(n)
```

and there are `q` queries, what is the total complexity?

---

### Q2

If I spend:

```text
O(n)
```

once building auxiliary information and then answer every query in:

```text
O(1)
```

what is the total complexity?

---

### Q3

When would prefix sums be a bad design?

Think about:

* frequent updates
* memory
* mutable datasets
* write-heavy workloads

---

# Prefix vs Suffix

Now generalize the idea.

For an element:

```text
nums[i]
```

we can maintain information about:

```text
everything before i
```

and separately:

```text
everything after i
```

That gives us:

```text
PREFIX | nums[i] | SUFFIX
```

This becomes the key idea behind today's main interview problem.

---

# Session 3 — DSA: Prefix/Suffix Pattern

> **Time: 50 minutes**

Only two problems today.

Depth over volume.

---

## Problem 1 — Range Sum Query — Immutable

### Why This Problem?

Running Sum taught:

```text
carry previous accumulated state forward
```

This problem adds:

```text
precompute once
to answer many future questions cheaply
```

---

## Required Reasoning Sequence

Use the full interview framework.

### 1. Requirements

What operations exist?

What changes?

What remains immutable?

---

### 2. Brute Force

For every query:

```text
left → right
```

calculate the sum.

Derive the complexity.

Do not just state `O(n)`.

---

### 3. Bottleneck

Identify exactly what work gets repeated between queries.

---

### 4. Better Solution

Derive prefix sums.

---

### 5. Invariant

State precisely what `prefix[i]` represents.

This must be mathematically unambiguous.

---

### 6. Trade-off

Discuss:

```text
O(n) preprocessing
O(n) additional memory
O(1) query
```

versus:

```text
O(1) preprocessing
O(1) additional memory
O(n) query
```

---

## Problem 2 — Product of Array Except Self

This is the **main problem of Day 3**.

It was previously pending and is now the natural next step.

Restrictions:

```text
No division.
```

Eventually reach:

```text
O(n) time
```

and:

```text
O(1) auxiliary space
```

excluding the result array.

But do NOT jump directly there.

---

## Required Progression

### Stage 1 — Brute Force

For every index:

```text
multiply every other value
```

Derive why this becomes:

```text
O(n²)
```

---

### Stage 2 — Identify Repeated Work

For index `i`, the answer is:

```text
product before i
×
product after i
```

Think:

```text
PREFIX PRODUCT
       ↓
      [i]
       ↑
SUFFIX PRODUCT
```

---

### Stage 3 — Two Auxiliary Arrays

First derive:

```text
prefix[]
suffix[]
```

Do not prematurely optimize memory.

Make correctness obvious first.

---

### Stage 4 — Remove Unnecessary Storage

Ask:

> Do I really need the entire suffix array?

If traversing right → left, can one variable represent:

```text
product of everything processed to my right
```

?

Derive the optimized version.

---

# Required Invariants

You should be able to state something equivalent to:

### Forward Pass

Before processing index `i`:

> `prefixProduct` contains the product of every element strictly before `i`.

### Reverse Pass

Before processing index `i`:

> `suffixProduct` contains the product of every element strictly after `i`.

These invariants are more important than memorizing the implementation.

---

# Required Edge Cases

Dry-run:

```text
[1, 2, 3, 4]
```

Then:

```text
[0, 1, 2, 3]
```

Then:

```text
[0, 0, 2, 3]
```

Explain why the algorithm naturally handles zeros.

---

# Session 4 — Engineering Lab: Dynamic Array Quality Gate

> **Time: 25 minutes**

Do **not** rebuild Dynamic Array.

Day 3 is about making the existing implementation trustworthy.

The goal is to attack the area that has historically caused mistakes:

```text
index boundaries
```

---

# Invariants

Write these down before touching implementation.

For a Dynamic Array:

```text
0 <= size <= capacity
```

Valid element indexes:

```text
0 <= index < size
```

Next append position:

```text
elements[size]
```

After removal:

```text
size decreases exactly once
```

Elements after the removed position shift exactly one position left.

---

# Required Tests

Add or verify tests for:

### First insertion

```text
[]
add(10)

→ [10]
size = 1
```

---

### Growth boundary

Fill exactly to capacity.

Then add one more item.

Validate:

* old elements preserved
* new item inserted
* size correct
* capacity increased

---

### Invalid access

Test:

```text
get(-1)
get(size)
```

Both must fail.

Remember:

```text
index == size
```

is invalid for `get()`.

---

### Remove first

```text
[10,20,30]

remove(0)

→ [20,30]
```

---

### Remove middle

```text
[10,20,30]

remove(1)

→ [10,30]
```

---

### Remove last

No unnecessary shifting should occur.

---

# Deliverable

Create a small table:

| Operation | Invariant threatened          |
| --------- | ----------------------------- |
| add       | size ≤ capacity               |
| get       | index < size                  |
| set       | index < size                  |
| remove    | ordering + size               |
| resize    | all existing values preserved |

The objective is to start thinking like a data-structure implementer rather than simply making tests green.

---

# Session 5 — Java/JVM: Why Tiny Java Code Can Become Fast

> **Time: 20 minutes**

Today's JVM continuation is intentionally narrow.

## Topic 1 — Method Inlining

Conceptually:

```java
int getSize() {
    return size;
}

if (getSize() == capacity) {
    ...
}
```

may eventually behave more like:

```java
if (size == capacity) {
    ...
}
```

after JIT optimization.

But do not treat that transformation literally or as guaranteed.

Discuss:

* hot call sites
* small methods
* compiler heuristics
* why giant methods are harder to inline

---

## Important Insight

Inlining can expose more optimization opportunities.

For example:

```text
inlining
    ↓
compiler can see surrounding code
    ↓
constant propagation
dead-code removal
bounds-check elimination
other optimizations become possible
```

This is the real reason inlining matters.

---

# Topic 2 — Bounds Checks

Java normally needs array access safety:

```java
arr[i]
```

must satisfy:

```text
0 <= i < arr.length
```

But in sufficiently predictable loops, the JIT may prove some checks unnecessary.

Connect this to:

```text
for (int i = 0; i < arr.length; i++)
```

---

# Topic 3 — JMH Mental Model

JMH exists because microbenchmarking managed/JIT-compiled code is surprisingly difficult.

Understand:

```text
warm-up
↓
allow JVM/JIT to stabilize
↓
measurement
↓
multiple iterations/forks
↓
statistical result
```

Do not memorize annotations yet.

---

# Question

Suppose benchmark A runs first and benchmark B runs second.

B appears much faster.

How could JVM warm-up produce a misleading conclusion?

You should be able to explain this before leaving Day 3.

---

# Session 6 — LLD/HLD: Metrics Range Query Service

> **Time: 20 minutes**

Now connect prefix thinking to backend engineering.

Imagine we collect:

```text
API request count per minute
```

Example:

```text
10:00 → 120 requests
10:01 → 150
10:02 → 180
10:03 → 130
...
```

The dashboard asks:

```text
How many requests happened between T1 and T2?
```

Thousands of times.

---

# Requirements

Assume:

* sequential time buckets
* very high dashboard read volume
* data from completed historical buckets becomes immutable
* range queries are common

---

# Design Discussion

Compare:

## Design A

Scan every bucket for every query.

---

## Design B

Maintain precomputed cumulative information.

---

Discuss:

* query complexity
* storage overhead
* ingestion cost
* mutable current bucket
* historical immutable buckets
* preaggregation

---

# Staff-Level Question

What happens if users can arbitrarily modify historical values?

Your prefix information becomes stale.

How would you redesign the system?

Do not solve this with a buzzword.

Reason from the read/write access pattern.

---

# Session 7 — Production Engineering Scenario

> **Time: 10 minutes**

You own an analytics API.

Originally users could query:

```text
last 60 minutes
```

Latency:

```text
p99 = 80 ms
```

A feature launch allows:

```text
last 90 days
```

Suddenly:

```text
p50 = 90 ms
p95 = 1.2 s
p99 = 4.8 s
CPU = 92%
memory = normal
GC = normal
DB latency = normal
error rate = increasing
```

Every request currently loads buckets and sums them sequentially.

---

# Diagnose

Answer in this order:

1. What does the evidence rule out?
2. What component is probably consuming CPU?
3. How did changing the query range change computational work?
4. What is the complexity relative to number of buckets?
5. Would adding machines solve the architectural problem?
6. What forms of precomputation could reduce query work?
7. What metrics would confirm your hypothesis?

This is the production-engineering connection for today's algorithmic concept.

---

# Deliverables

## DSA

* [ ] Range Sum Query derived from brute force
* [ ] Prefix invariant stated precisely
* [ ] Product of Array Except Self brute force derived
* [ ] Prefix/suffix-array version derived
* [ ] O(1)-auxiliary-space version derived
* [ ] Zero cases dry-run

## Engineering

* [ ] Dynamic Array boundary tests completed
* [ ] Dynamic Array invariants documented

## JVM

* [ ] Method inlining explained without notes
* [ ] JMH warm-up explained
* [ ] Big-O vs measured performance explained clearly

## Design

* [ ] Metrics range-query service trade-offs discussed

## Production

* [ ] Analytics latency incident diagnosed

---

# Reflection / Interview Questions

Do these without notes.

### 1.

Why does preprocessing sometimes improve latency even though it adds additional work?

---

### 2.

Given `q` range queries over `n` elements, compare:

```text
repeated scanning
```

against:

```text
prefix preprocessing
```

Derive both complexities.

---

### 3.

Why isn't a prefix structure automatically the right choice for a heavily mutable dataset?

---

### 4.

For Product of Array Except Self, why is division a weaker general solution even if division were permitted?

Think about zeros.

---

### 5.

State the forward and reverse invariants for Product of Array Except Self.

---

### 6.

Why can:

```text
O(n)
```

array traversal significantly outperform another:

```text
O(n)
```

algorithm operating over objects?

---

### 7.

Why can JVM warm-up invalidate a naive microbenchmark?

---

### 8.

Why does method inlining sometimes unlock optimizations that would otherwise be impossible?

---

### 9.

You have an analytics API where writes are rare but reads are extremely frequent.

How does that influence whether precomputation is attractive?

---

### 10.

Suppose historical metrics suddenly become editable.

Which assumption in our prefix-based design has changed?

---

# End-of-Day Exit Criteria

Day 3 is complete only when you can demonstrate all of the following.

## Algorithms

You can look at a problem and recognize:

> "This is repeatedly recomputing information I could carry or precompute."

You can derive:

```text
Brute force
→ repeated work
→ prefix/suffix state
→ optimized algorithm
```

without memorizing the final solution.

---

## Product Except Self

You can solve it from scratch using:

```text
left product
+
right product
```

and state strong invariants for both passes.

---

## Complexity

You can clearly explain:

```text
O(n)
```

as growth in computational work—not as a literal count of CPU operations.

---

## JVM

You can explain at introductory interview depth:

* hot code
* JIT
* method inlining
* why inlining enables further optimization
* why Java microbenchmarks require warm-up
* what JMH protects us from

---

## Dynamic Array

Your implementation passes boundary-condition tests and you can explain why:

```text
index == size
```

is invalid for access but is conceptually the next append location.

---

## Engineering

You can connect prefix/precomputation thinking to:

* analytics
* metrics aggregation
* read-heavy services
* precomputation
* latency versus write-cost trade-offs

---

# Explicitly Not Part of Day 3

Do **not** expand today's scope into:

* HashMap implementation
* HashMap treeification
* sliding window
* two pointers
* binary search
* concurrency
* GC deep dive

Those are valuable, but today is about building one reusable mental model deeply:

> **When work overlaps, ask whether previous computation can become reusable state.**

---

# Registry Update Requirements

Do **not** update `registry.md` merely because this Day 3 curriculum exists.

After the actual Day 3 session, record evidence from what you really demonstrated.

In particular capture:

* Prefix/suffix understanding
* Product Except Self performance
* strength of invariants
* Dynamic Array boundary-test results
* method-inlining understanding
* JMH understanding
* new misconceptions
* unfinished deliverables
* exact next action

The next curriculum must be generated from that evidence rather than blindly advancing because Day 3 ended.
