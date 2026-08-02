# Backend Engineering Bootcamp Registry

This registry tracks curriculum progress, observed growth areas, completed work, pending deliverables, and the exact next step.

It is the continuity document for the bootcamp.

At the start of every new session:

1. Read the relevant curriculum file.
2. Read this registry.
3. Resume from `Next Action`.
4. Update this file at the end of the session.

---

# Current Position

* **Curriculum:** Day 1 — Thinking Like an Interviewer
* **Status:** In Progress
* **Target Level:** LMTS / Staff-level backend engineering interviews
* **Primary Language:** Java
* **Current Section:** Session 2 — Pattern Recognition
* **Next Problem:** Rotate Array

---

# Day 1 Curriculum Progress

## Session 1 — Understanding Arrays

* [x] Contiguous memory
* [x] Fixed-size allocation
* [x] Base address and offset calculation
* [x] Why indexed access is O(1)
* [x] Why unsorted search is O(n)
* [x] Why insertion and deletion are O(n)
* [x] Insert shifting direction
* [x] Remove shifting direction
* [x] Virtual memory versus physical memory
* [x] Memory pages versus CPU cache lines
* [x] Spatial locality
* [x] Array versus linked-list cache behaviour
* [x] Static arrays versus dynamic arrays

## Session 2 — Pattern Recognition

* [x] Two Sum
* [x] Contains Duplicate
* [x] Running Sum
* [x] Move Zeroes
* [ ] Rotate Array
* [ ] Product of Array Except Self
* [ ] Majority Element
* [x] Best Time to Buy and Sell Stock

For every remaining problem, capture:

* Input
* Expected output
* Constraints and assumptions
* Brute-force approach
* Better approach
* Repeated question or useful state
* Pattern
* Invariant
* Time complexity
* Space complexity
* Trade-offs

## Session 3 — Coding Practice

* [x] Build Array from Permutation — reasoning completed
* [x] Running Sum of 1D Array — reasoning completed
* [x] Concatenation of Array — reasoning completed
* [ ] Implement and commit Build Array from Permutation
* [ ] Implement and commit Running Sum
* [ ] Implement and commit Concatenation of Array
* [ ] Add tests and edge cases

## Session 4 — Java ArrayList Deep Dive

* [ ] Internal backing array
* [ ] Constructor behaviour
* [ ] Default capacity
* [ ] `add()`
* [ ] `grow()`
* [ ] `ensureCapacity()`
* [ ] Resize behaviour
* [ ] Why growth is approximately 1.5x
* [ ] Why capacity is not doubled
* [ ] Why `ArrayList` does not shrink automatically
* [ ] Why `ArrayList` generally outperforms `LinkedList`
* [ ] Write one-page ArrayList summary

## Session 5 — JVM Fundamentals

* [x] Primitive arrays
* [x] Reference arrays
* [x] `int[]` versus `Integer[]`
* [x] Default primitive and reference values
* [x] Wrapper-object memory overhead
* [x] Cache-locality implications
* [x] Boxing
* [x] Unboxing
* [x] Null unboxing and `NullPointerException`
* [x] Integer cache
* [x] `==` versus `equals()`
* [ ] Object header
* [ ] Class metadata
* [ ] Array object layout
* [ ] Write JVM memory notes

## Session 6 — Engineering Lab

Build a Dynamic Array without `ArrayList`, Collections, or generics.

Required API:

* [ ] `add(int value)`
* [ ] `get(int index)`
* [ ] `set(int index, int value)`
* [ ] `remove(int index)`
* [ ] `size()`
* [ ] `isEmpty()`
* [ ] `resize()`

Stretch goals:

* [ ] `contains()`
* [ ] `clear()`
* [ ] `print()`
* [ ] `capacity()`

## Session 7 — Low-Level Design

* [ ] Define public API
* [ ] Define internal fields
* [ ] Define responsibilities
* [ ] Define validation and error handling
* [ ] Document operation complexities
* [ ] Draw a class diagram
* [ ] Write Dynamic Array design document

## Session 8 — Real-World Engineering

* [ ] Java `ArrayList`
* [ ] Apache Arrow
* [ ] ClickHouse
* [ ] Columnar databases
* [ ] Analytics engines
* [ ] Explain why contiguous memory improves throughput

---

# Patterns Learned

## Complement Lookup

**Repeated question:** What value do I need, and have I seen it?

Typical structure:

```text
requiredValue = target - currentValue
```

Typical data structure:

```text
HashMap
```

Example:

* Two Sum

## Membership Test

**Repeated question:** Have I seen this exact value before?

Typical data structure:

```text
HashSet
```

Examples:

* Contains Duplicate
* First Duplicate

## Prefix Accumulation

**Repeated question:** Can I reuse the result computed at the previous index?

Typical state:

```text
previous prefix result
```

Example:

* Running Sum

## Index Mapping

**Repeated question:** Which position does this value point to?

Typical operation:

```text
nums[nums[i]]
```

Example:

* Build Array from Permutation

## Stable Compaction

**Repeated question:** Where should the next valid element be written?

Typical state:

```text
writeIndex
```

Example:

* Move Zeroes

## Running Optimum

**Repeated question:** What is the best value observed so far?

Typical state:

```text
minimum so far
maximum result so far
```

Example:

* Best Time to Buy and Sell Stock

---

# Concepts Learned

## Array Address Calculation

```text
elementAddress = baseAddress + index × elementSize
```

This explains O(1) indexed access.

## Cache Locality

Arrays store elements contiguously.

Sequential traversal benefits from CPU cache lines because one memory fetch brings several nearby elements into cache.

Linked structures commonly require pointer chasing and have weaker cache locality.

## Dynamic Array Insertion

To insert while preserving existing values:

* Shift elements right.
* Iterate from right to left.
* Move the current element using:

```text
array[i + 1] = array[i]
```

## Dynamic Array Removal

To remove an element:

* Shift subsequent elements left.
* Iterate from left to right.
* Move the next element using:

```text
array[i] = array[i + 1]
```

## Invariant

An invariant is a condition that remains true at a defined point during every iteration of an algorithm.

A useful invariant must be strong enough to support a correctness argument.

Example for Move Zeroes:

> Everything before `writeIndex` contains all non-zero elements processed so far, in their original relative order.

---

# Observed Strengths

* Strong backend and Java intuition
* Naturally reasons in terms of data movement
* Identifies missing constraints
* Connects algorithms with streaming systems
* Understands cache locality once the abstraction layers are separated
* Can derive optimal approaches rather than only recall them
* Communicates practical implementation concerns
* Recognizes nullability and ORM implications of wrapper types
* Responds well to dry runs and concrete examples

---

# Growth Areas

## 1. Establish the Baseline Before Optimising

There is a tendency to jump directly to sorting, hashing, or another optimisation.

Required interview sequence:

1. State the simplest correct approach.
2. Analyse its time and space complexity.
3. Identify repeated work.
4. Derive the better solution.

## 2. Convert the Mental Model into Exact Index Logic

The conceptual direction is usually correct, but `+1`, `-1`, loop boundaries, and iteration direction require more discipline.

Required practice:

* State the movement in English.
* Dry-run a small example.
* Then write the assignment and loop bounds.

## 3. State Strong Invariants

Initial invariants may describe only part of correctness.

Example:

Weak:

> Everything before `writeIndex` is non-zero.

Strong:

> Everything before `writeIndex` contains all non-zero values processed so far in their original relative order.

Each invariant should cover:

* correctness
* completeness
* ordering, when required
* absence of invalid or duplicated state

## 4. Separate Abstraction Layers Precisely

Continue strengthening the distinction among:

* JVM heap objects
* process virtual memory
* OS memory pages
* MMU and page tables
* physical memory
* CPU cache lines

Avoid using page and cache line interchangeably.

## 5. Explain Why the Complexity Is Correct

Do not stop at `O(n)` or `O(n²)`.

Explain:

* what operations are performed
* how often they are performed
* whether elements are copied, compared, or allocated
* whether the output size creates a lower bound
* why the result cannot be asymptotically improved

## 6. Improve Java Reference Semantics Precision

Continue practising:

* primitive value versus object reference
* reference identity versus value equality
* boxing versus object creation
* unboxing and null safety
* wrapper caching
* arrays of primitives versus arrays of references

## 7. Use Precise Variable Names

Prefer state-revealing names:

```text
writeIndex
minPriceSoFar
maxProfitSoFar
currentCustomerWealth
```

Avoid generic names such as:

```text
pointer
value
profit
counter
```

when a stronger domain-specific name is available.

## 8. Maintain Curriculum Discipline

Do not introduce unrelated problems before completing the current curriculum section.

Useful detours may be included, but they must be labelled as bonus material and must not replace pending curriculum work.

---

# Interview Response Template

Use this structure for every coding problem:

## 1. Requirements

What is being requested?

## 2. Clarifications

Ask only questions that may change the algorithm.

## 3. Assumptions

State confirmed guarantees.

## 4. Brute Force

Give the simplest correct solution.

## 5. Complexity

Explain the actual work performed.

## 6. Bottleneck

Identify repeated work or unnecessary state.

## 7. Better Solution

Derive the optimisation.

## 8. Invariant

State what remains true after every iteration.

## 9. Dry Run

Trace one normal case and one edge case.

## 10. Final Complexity

State time and space complexity.

## 11. Trade-offs

Discuss memory, data movement, cache behaviour, scalability, and maintainability when relevant.

---

# Pending Deliverables

* [ ] Pattern-recognition notes
* [ ] Build Array from Permutation implementation
* [ ] Running Sum implementation
* [ ] Concatenation of Array implementation
* [ ] Dynamic Array implementation
* [ ] ArrayList internals summary
* [ ] JVM memory notes
* [ ] Dynamic Array design document
* [ ] Day 1 reflection answers

---

# Next Action

Continue **Day 1, Session 2 — Pattern Recognition**.

Next problem:

```text
Rotate Array
```

Required discussion:

1. Define right rotation precisely.
2. Handle `k >= n`.
3. Clarify whether extra space is allowed.
4. Present the repeated one-step rotation baseline.
5. Analyse its complexity.
6. Derive the extra-array approach.
7. Derive the in-place reversal approach.
8. State the invariant or correctness argument.
9. Compare data movement and trade-offs.

After Rotate Array:

1. Product of Array Except Self
2. Majority Element
3. ArrayList source-code deep dive
4. Dynamic Array design
5. Dynamic Array implementation

---

# Day 1 Exit Criteria

Day 1 is complete only when all of the following are true:

* [ ] Array memory layout can be explained without notes.
* [ ] O(1) access can be derived using address calculation.
* [ ] Insert and remove shifting can be implemented without memorisation.
* [ ] All eight pattern-recognition problems are classified.
* [ ] The three coding-practice problems are implemented and tested.
* [ ] ArrayList growth behaviour can be explained.
* [ ] `int[]` versus `Integer[]` can be explained in JVM and performance terms.
* [ ] A working Dynamic Array is implemented.
* [ ] The Dynamic Array design and complexity are documented.
* [ ] All reflection questions are answered.
