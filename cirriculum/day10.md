# Day 10 — Intensive: Binary-Search Transfer, Rate Limiter V1, Java Memory & Engineering Closure

> **Duration:** ~4 hr 30 min to 5 hr  
> **Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory  
> **Session Type:** Intensive Block 1 of 3  
> **Primary DSA Theme:** Binary-search boundaries and transfer under unfamiliar framing  
> **Primary LLD Theme:** Implement and test a fixed-window Rate Limiter V1  
> **Primary Java Theme:** Primitive/reference semantics, heap/stack, arrays, object layout, locality  
> **Engineering Theme:** Close one deliberately deferred Phase-1 implementation/testing gap  
> **HLD Scope:** Local vs global Rate Limiter guarantees; do not implement a distributed limiter yet

---

# Why Day 10 Is Expanded

Day 9 completed:

- LRU Cache implementation and tests
- exact binary search
- lower-bound / first-true reasoning
- `[0, n]` answer-space reasoning
- Big-O vs locality discussion
- Rate Limiter requirement clarification
- fixed vs rolling window semantics
- token-bucket distinction
- Rate Limiter safety-invariant formulation

The active gaps are now narrower:

```text
binary-search boundary precision
invariant formulation
Java memory-model foundations
simple Rate Limiter implementation
a small amount of deferred engineering evidence
```

Because this is one of three temporarily longer sessions, Day 10 will use the extra time for:

```text
more transfer problems
+
more implementation
+
more tests
+
more retrieval
```

not for prematurely jumping into trees, heaps, or concurrency.

---

# Today's Outcomes

By the end of Day 10, you should be able to:

- state invariants as properties rather than implementation steps
- reimplement lower bound from memory
- distinguish exact search, lower bound, upper bound, first occurrence, and last occurrence
- solve multiple binary-search transfer problems without being told the pattern
- explain why each boundary update is safe
- implement and test a fixed-window Rate Limiter
- explain fixed-window burst behavior as a semantic consequence
- identify why local Rate Limiting is not global Rate Limiting
- distinguish primitive values from references
- explain stack-frame vs heap-object mental models
- explain `int[]` vs `Person[]`
- explain object/reference overhead conceptually
- separate JVM heap/stack from CPU cache
- close one deferred engineering item with tests

---

# Session 1 — Retrieval & Precision Gate

**20 minutes**

No notes initially.

## 1. Invariant Drill

State one invariant from each category.

### DSA

Binary search:

> If the target exists and has not already been returned, its index remains in the current candidate interval.

### Data Structure

LRU:

> The map and DLL contain exactly the same logical cache entries.

### System

Rate Limiter:

> Accepted requests must not exceed the configured allowance under the chosen time semantics.

Now create **one new invariant in your own words**.

Do not describe an algorithm.

---

## 2. Canonical Lower Bound From Memory

Requirement:

```text
first index i where nums[i] >= target
```

Answer range:

```text
[0, n]
```

Explain before coding:

```text
nums[mid] < target
→ mid is definitely invalid
→ discard through mid
→ left = mid + 1
```

```text
nums[mid] >= target
→ mid is valid
→ but earlier valid candidate may exist
→ preserve mid
→ right = mid
```

Then implement from memory.

---

## 3. Exact vs Boundary Search

Answer quickly:

```text
Exact search:
what are we searching for?

Lower bound:
what boundary are we searching for?

Why can exact search discard mid?

Why can lower bound sometimes not discard mid?
```

---

# Session 2 — DSA Problem 1: First and Last Position of Target

**35 minutes**

Problem:

> Given a sorted array with duplicates, return the first and last positions of a target.

Example:

```text
[5,7,7,8,8,10], target = 8
→ [3,4]
```

Do not start with "two binary searches."

Use the full abstraction-first protocol:

```text
1. Objective
2. Search space
3. Relevant information
4. Discardable information
5. Brute force
6. Repeated work
7. Safe elimination
8. Enabling property
9. Boundary formulation
10. Code
11. Tests
```

---

## Derive the Left Boundary

First occurrence is:

```text
first index where nums[i] >= target
```

Then validate:

```text
nums[first] == target
```

---

## Derive the Right Boundary

Do not memorize "upper bound."

Ask:

> What is the first index that definitely lies after every occurrence of target?

Answer:

```text
first index where nums[i] > target
```

Then:

```text
last = firstGreaterThanTarget - 1
```

---

## Required Tests

```text
[]
[1], target 1
[1], target 2
[2,2,2,2], target 2
[1,2,3], target 1
[1,2,3], target 3
[1,2,3], target 4
[1,1,2,2,2,3,3], target 2
```

Complexity:

```text
O(log n)
```

---

# Session 3 — DSA Problem 2: Count Occurrences in Sorted Array

**25 minutes**

Fresh transfer.

Problem:

> Given a sorted array and a target, return how many times the target occurs.

Do not scan after finding one target.

Reason:

```text
count
=
rightBoundary - leftBoundary
```

More precisely:

```text
firstGreaterThanTarget
-
firstGreaterThanOrEqualToTarget
```

Example:

```text
[1,2,2,2,3,4], target = 2
→ 3
```

---

## Key Question

Why is this still:

```text
O(log n)
```

rather than:

```text
O(k)
```

where `k` is the number of duplicates?

Because we never enumerate all matching elements.

---

## Edge Cases

```text
target absent
all elements same
single element
empty array
target before all
target after all
```

---

# Session 4 — DSA Problem 3: Search Insert Position Under Interview Conditions

**25 minutes**

This is deliberately easier algorithmically.

The purpose is **independence and communication**.

Problem:

> Return the index where target exists or should be inserted to preserve sorted order.

This is effectively lower bound.

But pretend the pattern name is unavailable.

Your interview answer should sound like:

> "I'm looking for the first position whose value is not less than target. Sorted order makes the predicate monotonic, so I can binary-search that boundary."

Then code without assistance.

---

# Session 5 — DSA Problem 4: First Bad Version Style Predicate

**25 minutes**

No array values.

Imagine versions:

```text
1 ... n
```

and an API:

```text
isBad(version)
```

Once versions become bad, all later versions are bad:

```text
false false false true true true
```

Find the first bad version.

---

## Purpose

This removes the visual crutch of a sorted integer array.

You must recognize:

```text
monotonic predicate
```

as the real binary-search requirement.

---

## Questions

1. What is the search space?
2. What is the predicate?
3. Why can one region be eliminated?
4. Which candidate must be preserved?
5. What are the boundary conditions?

Target:

```text
O(log n)
```

API calls.

---

# DSA Block Exit

By now you should be able to articulate:

```text
Binary search does not fundamentally require an array.

It requires:
ordered candidate space
+
monotonic decision/predicate
+
safe elimination
```

Do not yet jump to binary-search-on-answer problems.

---

# Session 6 — Java Memory Foundations: Primitive vs Reference

**30 minutes**

## Primitive

```java
int x = 42;
```

Conceptually:

```text
x contains 42
```

## Reference

```java
Person p = new Person();
```

Conceptually:

```text
p
→ reference

Person object
→ separate object
```

The reference is not the object.

---

## Aliasing

Consider:

```java
Person a = new Person();
Person b = a;
```

Questions:

```text
How many Person objects?
How many reference variables?
Does modifying b's object affect what a observes?
Why?
```

Target:

```text
one object
two references to same object
```

---

## Reference Reassignment

```java
Person a = new Person("A");
Person b = a;

b = new Person("B");
```

Now reason about:

```text
a
b
object A
object B
```

This is foundational for later Java/JVM/concurrency discussions.

---

# Session 7 — Java Memory Foundations: Stack Frames & Heap Objects

**25 minutes**

Use:

```java
void process(Person p, int count) {
    int local = count + 1;
}
```

Conceptually reason about the method frame containing:

```text
reference p
primitive count
primitive local
execution state
```

The `Person` object itself is separate.

Do not over-literalize exact machine layout.

---

## Heap Mental Model

Objects and arrays are normally treated conceptually as heap-managed allocations:

```java
new Person()
new int[10]
new Object[10]
```

Later JVM optimization may alter physical behavior internally, but that does not change the programming mental model required here.

---

# Session 8 — Arrays: Primitive Array vs Reference Array

**25 minutes**

Compare:

```java
int[] numbers = new int[3];
```

and:

```java
Person[] people = new Person[3];
```

Draw both.

Primitive:

```text
numbers
  ↓
[ 0 | 0 | 0 ]
```

Reference:

```text
people
  ↓
[ null | null | null ]
```

Then:

```java
people[0] = new Person("A");
people[1] = new Person("B");
```

becomes:

```text
[ refA | refB | null ]
    ↓      ↓
    A      B
```

Key statement:

> `new Person[3]` creates one array object containing three reference slots; it does not create three Person objects.

---

# Session 9 — Object Layout & Data-Structure Memory Cost

**25 minutes**

Conceptual object components:

```text
object header
instance fields
padding/alignment
```

Example:

```java
class Node {
    int value;
    Node next;
}
```

Think:

```text
Node object
├── header
├── int value
├── reference next
└── possible padding
```

Then compare storing 1,000 integers as:

```text
int[1000]
```

vs:

```text
1,000 linked Node objects
```

Do not calculate exact bytes yet.

Discuss directionally:

```text
primitive array
→ one object + primitive payload

linked list
→ many Node objects
→ many headers
→ references
→ more allocations
→ pointer chasing
→ GC pressure
```

---

# Session 10 — JVM Heap/Stack vs CPU Cache

**20 minutes**

Separate abstraction layers.

JVM concepts:

```text
heap
stack frames
objects
references
arrays
```

Hardware concepts:

```text
CPU registers
L1/L2/L3 cache
RAM
cache lines
```

An object can logically live in the JVM heap while some of its bytes are currently resident in CPU cache.

These are not contradictory.

---

## Spatial Locality

Arrays:

```text
contiguous elements
→ nearby data often fetched together
```

Linked nodes:

```text
node
→ pointer
→ another node somewhere else
```

potentially create more cache misses.

Do not confuse:

```text
algorithmic complexity
```

with:

```text
memory-access behavior
```

---

# Session 11 — Rate Limiter V1: Fixed-Window LLD

**45 minutes**

Day 9 clarified semantics.

Today implement exactly:

```text
Fixed Window
```

Requirement:

> For each customer, allow at most `limit` requests in each fixed interval of length `windowSize`.

---

## API

```java
boolean allow(String customerId, Instant now)
```

Pass `now` in.

Do not hard-code `Instant.now()` throughout the implementation.

Reason:

```text
deterministic tests
```

---

## Minimum State

```text
Map<CustomerId, WindowState>
```

where:

```text
WindowState
├── windowId
└── acceptedCount
```

No request timestamp list.

That would be a rolling-window implementation.

---

## Window Identity

Example:

```text
windowId =
epochSeconds / windowSizeSeconds
```

For:

```text
windowSize = 10 sec
```

windows become conceptually:

```text
[0,10)
[10,20)
[20,30)
```

---

## Invariant

> For each customer's current fixed window, `acceptedCount` equals the number of accepted requests in that window and never exceeds `limit`.

Do not answer:

> "Check timestamp then increment."

That is implementation.

---

## Flow

### No Existing State

```text
create WindowState
count = 1
allow
```

### Same Window

```text
count < limit
→ count++
→ allow

count == limit
→ reject
```

### Different Window

```text
replace/reset state
count = 1
allow
```

---

# Session 12 — Rate Limiter Tests

**30 minutes**

Configuration:

```text
limit = 3
window = 10 sec
```

Required tests:

```text
first request allowed
three requests allowed
fourth rejected
new window resets
per-customer isolation
exact boundary behavior
rejection does not increase acceptedCount
multiple windows
invalid limit
invalid window size
```

---

## Important Regression

After rejection:

```text
count must remain == limit
```

Do not accidentally increment before deciding.

---

## Boundary Test

If:

```text
t = 9
```

belongs to:

```text
window 0
```

then:

```text
t = 10
```

belongs to:

```text
window 1
```

Be explicit.

---

# Session 13 — Rate Limiter HLD: Local vs Global

**20 minutes**

Suppose:

```text
3 service instances
```

Each instance independently enforces:

```text
100 requests/min/customer
```

Traffic is load balanced.

Possible total:

```text
instance A → 100
instance B → 100
instance C → 100
```

Global:

```text
300
```

Therefore:

> A correct local limiter does not imply a correct global distributed limiter.

---

## Do Not Solve Yet

Do not start designing Redis/Lua/consensus.

Just derive the missing requirement:

> All request decisions affecting the same logical limit must coordinate against authoritative shared state or equivalent global partition ownership.

That's enough for today.

---

# Session 14 — Production Reasoning: State Growth & Window Bursts

**20 minutes**

## Scenario A — State Growth

Metrics:

```text
unique customers/hour ↑ 20x
request rate ↑ 1.5x
heap usage steadily ↑
GC frequency ↑
CPU moderately ↑
reject rate normal
```

Reason:

```text
Observation
↓
What retained state grows?
↓
What evidence confirms it?
↓
Mitigation direction
```

Inspect:

```text
limiter map size
entry age
unique key cardinality
cleanup policy
allocation metrics
GC behavior
```

Do not immediately increase heap size.

---

## Scenario B — Fixed-Window Burst

Limit:

```text
100/min
```

Client sends:

```text
100 requests at 10:00:59
100 requests at 10:01:00
```

Both groups may be accepted under fixed-window semantics.

Question:

> Is this an implementation bug?

No, if fixed-window semantics were intentionally selected.

Then ask:

> If product requirements prohibit this behavior, what does that tell you?

Answer:

> The chosen algorithm does not match the required time semantics.

---

# Session 15 — Engineering Debt Closure

**30–40 minutes**

Close **one** old Phase-1 engineering item.

Priority order:

```text
1. Dynamic Array quality-gate tests
2. Product Except Self JUnit tests
3. JMH implementation
```

Pick the highest-priority item that is actually incomplete.

Do not do all three.

---

## Option A — Dynamic Array Quality Gate

Verify:

```text
first insertion
growth
preserve existing values
negative index rejected
index == size rejected
remove first
remove middle
remove last
size updated once
```

Add/repair tests.

---

## Option B — Product Except Self Tests

Test:

```text
[1,2,3,4]
one zero
two zeros
negative values
single-element contract if applicable
```

Confirm:

```text
O(n)
O(1) auxiliary excluding output
```

---

## Option C — JMH

Only if the first two are already fully closed.

Benchmark a simple meaningful comparison such as:

```text
sequential array traversal
vs
linked-node traversal
```

or another already-understood Phase-1 comparison.

Focus on:

```text
warmup
measurement
fork
avoiding dead-code elimination
```

Do not turn this into a benchmarking rabbit hole.

---

# Session 16 — Mixed Interview Drill

**25 minutes**

No pattern labels.

I should give you three short prompts from earlier material.

Examples:

### A

Unsorted numbers + target pair.

### B

Positive array + shortest region meeting a sum.

### C

Linked list + determine whether cycle exists.

For each, answer only:

```text
objective
brute force
waste
enabling property
minimum state
invariant
complexity
```

No code unless reasoning is weak.

Purpose:

> Keep earlier patterns alive while learning new material.

---

# End-of-Day Retrieval

**15 minutes**

Without notes:

1. State an invariant without describing an implementation.
2. Why does lower bound use `right = mid`?
3. What predicate gives the first occurrence of target?
4. What predicate gives the boundary after the last occurrence?
5. What fundamentally makes binary search possible?
6. What is the difference between a primitive and a reference?
7. How many objects does `new Person[100]` create immediately?
8. What does a reference array contain?
9. What conceptually exists in a linked Node?
10. Why are linked structures more allocation-heavy than primitive arrays?
11. JVM heap vs CPU cache?
12. State the fixed-window Rate Limiter invariant.
13. Why pass time into `allow()`?
14. Why can three correct local limiters violate one global limit?
15. Why is a fixed-window boundary burst not automatically a bug?
16. What old engineering debt did you close today?

---

# Deliverables

## DSA

- [ ] lower bound implemented from memory
- [ ] first/last occurrence implemented
- [ ] count-occurrences transfer completed
- [ ] Search Insert solved independently
- [ ] first-bad-version predicate search completed
- [ ] boundary invariants explained
- [ ] all DSA complexity proofs stated clearly

## Java Memory

- [ ] primitive/reference distinction
- [ ] aliasing explained
- [ ] stack frame / heap object explained
- [ ] primitive-array vs reference-array diagram
- [ ] object layout concepts
- [ ] linked-node overhead
- [ ] heap vs CPU-cache distinction
- [ ] locality explanation

## Rate Limiter

- [ ] fixed-window API defined
- [ ] minimum state derived
- [ ] invariant stated
- [ ] implementation completed
- [ ] unit tests pass
- [ ] exact boundary tested
- [ ] per-customer isolation tested
- [ ] rejection-count behavior tested
- [ ] invalid configuration tested

## HLD / Production

- [ ] local-vs-global limitation explained
- [ ] state-growth failure diagnosed
- [ ] boundary-burst trade-off explained
- [ ] global coordination requirement identified

## Engineering Closure

- [ ] one old Phase-1 debt item fully closed
- [ ] tests/evidence added
- [ ] registry no longer carries that item as incomplete

---

# Exit Criteria

Day 10 is complete when:

## Binary Search

You can derive multiple boundary problems from:

```text
monotonic predicate
```

rather than memorizing independent templates.

## Invariants

You consistently state:

```text
what must remain true
```

rather than:

```text
what the implementation does
```

## Java Memory

You can cleanly explain:

```text
primitive value
reference
object
array of primitives
array of references
stack frame
heap object
CPU cache
```

without mixing abstraction layers.

## Rate Limiter

You have a tested, single-node fixed-window implementation whose behavior matches an explicitly defined time policy.

## Production

You can explain why:

```text
correct algorithm locally
```

does not automatically imply:

```text
correct system globally
```

## Engineering Discipline

At least one previously deferred Phase-1 deliverable is now actually closed.

---

# Intentionally Deferred

Do **not** add today:

- Redis-backed Rate Limiter
- distributed Rate Limiter implementation
- rolling-window implementation
- token-bucket implementation
- thread safety
- Java Memory Model
- `volatile`
- CAS
- GC algorithms
- trees/heaps
- binary search on answer
- rotated-array search
- multiple old debt items

The next two intensive sessions can continue accelerating Phase-1 closure and transition readiness.

---

# Day 10 Exact Starting Action

Start with:

> **State one invariant from any earlier problem, but do not describe the algorithm. State only the property that must remain true.**

Then:

> **Implement lower bound from memory using answer space `[0, n]` and explain why a valid `mid` must sometimes remain in the search space.**

Then immediately begin the first/last-position transfer problem.

---

# Registry Update Requirements

At the end of Day 10 record only demonstrated evidence:

1. lower-bound rewrite quality
2. invariant-formulation quality
3. first/last-position implementation
4. additional binary-search transfer performance
5. boundary mistakes observed
6. Java primitive/reference understanding
7. stack/heap explanation
8. array/reference-array reasoning
9. object-layout terminology
10. heap-vs-cache distinction
11. fixed-window Rate Limiter implementation
12. Rate Limiter tests
13. local-vs-global reasoning
14. production state-growth reasoning
15. engineering-debt item closed
16. exact remaining Phase-1 gaps
17. exact Day 11 starting action
