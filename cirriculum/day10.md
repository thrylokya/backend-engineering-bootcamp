# Day 10 — Binary-Search Boundary Transfer, Rate Limiter V1 & Java Memory Foundations

> **Duration:** ~2 hr 50 min  
> **Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory  
> **Primary DSA Theme:** Consolidate exact-search vs boundary-search reasoning through a fresh duplicate-heavy transfer problem.  
> **Primary LLD Theme:** Convert the Rate Limiter requirements from Day 9 into a small, correct single-node implementation.  
> **Primary Java Theme:** Build a precise heap/stack/reference/object-layout mental model without jumping ahead into GC internals.  
> **HLD Scope:** Narrow multi-node Rate Limiter limitation only. Do not implement a distributed Rate Limiter yet.

---

# Why Day 10 Looks Like This

Day 9 completed:

- LRU Cache implementation and testing
- map ↔ DLL agreement reasoning
- exact binary search
- lower-bound / first-true reasoning
- `[0, n]` answer-space reasoning
- Big-O vs cache-locality discussion
- Rate Limiter requirement clarification
- fixed-window vs rolling-window semantics
- token-bucket distinction
- rolling-window safety invariant

The registry still identifies one active precision skill:

```text
invariant formulation
```

and one direct retrieval task:

```text
canonical lower-bound rewrite from memory
```

The Phase-1 master curriculum still includes:

- binary search
- primitive vs reference types
- object headers
- array layout
- heap vs stack
- virtual vs physical memory
- CPU cache
- JIT introduction
- simple Rate Limiter LLD

Therefore Day 10 should not start trees/heaps yet.

It should close the remaining Phase-1 foundation loops first.

---

# Today's Objectives

By the end of Day 10, you should be able to:

- state an invariant as a property, not as an algorithm
- rewrite canonical lower bound from memory
- distinguish exact search, lower bound, and upper bound
- solve a fresh duplicate-heavy binary-search problem
- derive first/last occurrence from boundary search
- implement and test a simple fixed-window Rate Limiter
- state the Rate Limiter invariant precisely
- explain why time semantics are part of correctness
- distinguish primitive values from object references in Java
- explain stack frames vs heap objects at interview depth
- explain what an array of references actually stores
- understand object-header/reference overhead conceptually
- distinguish JVM heap/stack abstractions from CPU cache
- identify why a local in-memory Rate Limiter is insufficient across multiple service instances
- diagnose memory-growth and boundary-burst behavior in a production-style Rate Limiter

---

# Session 1 — Retrieval Gate

**15 minutes**

No notes initially.

## Part A — State an Invariant

Question:

> What is an invariant?

Do **not** answer with implementation steps.

Use the structure:

```text
While the algorithm/system is correct,
what property must remain true?
```

Examples from previous days:

```text
LRU:
map and DLL represent the same logical cache entries

Exact binary search:
if target exists, its index remains inside the current candidate interval

Rolling Rate Limiter:
accepted requests in the preceding 60 seconds <= limit
```

## Part B — Rewrite Lower Bound From Memory

Requirement:

> Return the first index `i` such that `nums[i] >= target`. Return `n` if no such index exists.

Use answer space:

```text
[0, n]
```

Before coding, explain:

```text
nums[mid] < target
→ mid is impossible
→ left = mid + 1
```

and:

```text
nums[mid] >= target
→ mid is valid
→ but an earlier valid answer may exist
→ right = mid
```

## Part C — Exact Search vs Boundary Search

Explain:

```text
Exact search
→ find a value

Lower bound
→ find first position satisfying value >= target
```

Then answer:

> Why can exact search discard `mid` after proving it wrong, while lower bound sometimes has to preserve `mid`?

---

# Session 2 — DSA Transfer: Find First and Last Position of Target

**40 minutes**

Problem:

> Given a sorted array that may contain duplicates, return the first and last index of `target`. If absent, return `[-1, -1]`.

Examples:

```text
[5,7,7,8,8,10], target = 8 → [3,4]
[5,7,7,8,8,10], target = 6 → [-1,-1]
[2,2,2,2], target = 2       → [0,3]
[], target = 4              → [-1,-1]
```

Do not start with "two binary searches."

## Step 1 — Objective

We need:

```text
first occurrence
+
last occurrence
```

## Step 2 — Brute Force

Scan:

```text
O(n)
```

Then ask:

> What property lets us do better?

Answer:

```text
sorted order
+
duplicates are contiguous
```

## Step 3 — Left Boundary

First occurrence:

```text
first index where nums[i] >= target
```

Then verify:

```text
nums[first] == target
```

If not:

```text
target absent
```

## Step 4 — Right Boundary

Find:

```text
first index where nums[i] > target
```

Then:

```text
last occurrence = firstGreaterThanTarget - 1
```

## Predicate View

Lower bound:

```text
nums[i] >= target
false false true true true
```

Upper bound:

```text
nums[i] > target
false false false true true
```

Both are first-true searches.

## Required Invariants

Lower bound:

> All indices strictly before `left` are proven to contain values `< target`.

Upper bound:

> All indices strictly before `left` are proven to contain values `<= target`.

## Edge Cases

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

iterative auxiliary space:

```text
O(1)
```

---

# Session 3 — Java Memory Foundations: Primitive, Reference, Stack, Heap

**35 minutes**

No GC deep dive.

## Primitive Value

```java
int x = 42;
```

Conceptually:

```text
x contains primitive value 42
```

## Reference Variable

```java
Person p = new Person();
```

Conceptually:

```text
p → reference
Person object → separate object
```

The reference is not the object.

## Stack Frame

A method frame conceptually contains:

```text
parameters
local variables
intermediate execution state
return information
```

This is a JVM-level mental model, not an exact physical-layout promise.

## Heap

Objects and arrays are generally heap-managed allocations:

```java
new Person()
new int[100]
new Object[100]
```

---

# Critical Array Distinction

Compare:

```java
int[] numbers = new int[100];
Person[] people = new Person[100];
```

`int[]` stores:

```text
100 primitive int values
```

`Person[]` stores:

```text
100 reference slots
```

It does **not** create 100 `Person` objects.

Draw:

```text
stack frame
│
└── people reference
       ↓
heap
Person[] array
[ refA | refB | null ]
    ↓      ↓
 PersonA  PersonB
```

---

# Session 4 — Object Overhead & Layout Mental Model

**20 minutes**

Conceptually, an object contains:

```text
object header
+
instance fields
+
possible padding/alignment
```

Example:

```java
class Node {
    int value;
    Node next;
}
```

Mental model:

```text
object header
int value
reference next
possible padding
```

The next Node is a separate object.

Primitive array:

```text
header
+
length metadata
+
contiguous primitive elements
```

Reference array:

```text
header
+
length metadata
+
contiguous reference slots
```

Do not memorize exact byte sizes yet.

---

# Session 5 — JVM Heap/Stack vs CPU Cache

**15 minutes**

Keep abstraction layers separate.

JVM-level:

```text
heap
stack frames
objects
references
arrays
```

Hardware-level:

```text
registers
CPU caches
RAM
cache lines
```

Do not say:

> JVM stack = CPU cache.

An array may be a heap object while recently accessed portions of it are present in CPU cache.

Arrays often benefit from:

```text
contiguous elements
→ spatial locality
→ cache-line reuse
→ hardware prefetch
```

Linked nodes may involve:

```text
pointer chasing
→ scattered accesses
→ more cache misses
```

Memory behavior is separate from Big-O.

---

# Session 6 — Rate Limiter LLD: Implement Fixed Window V1

**40 minutes**

Day 9 clarified several policies.

Today implement exactly one:

```text
Fixed Window
```

Do not combine fixed window, rolling window, and token bucket.

## Requirement

For each customer:

> Allow at most `limit` accepted requests during each fixed window of `windowSize`.

Example:

```text
limit = 100
windowSize = 60 seconds
```

## API

```java
boolean allow(String customerId, Instant now)
```

Pass time in so tests are deterministic.

## Minimum State

Per customer:

```text
windowStart/windowId
acceptedCount
```

Possible representation:

```text
Map<CustomerId, WindowState>
```

Do not store every request timestamp. That would be solving a different policy.

## Invariant

> For the customer's current fixed window, `acceptedCount` equals the number of accepted requests in that window and never exceeds `limit`.

## Decision Flow

No state:

```text
create current window
count = 1
allow
```

Same window:

```text
count < limit
→ increment and allow

count == limit
→ reject
```

New window:

```text
reset to current window
count = 1
allow
```

## Window Identity

Use deterministic window identity, e.g.:

```text
windowId = epochSeconds / windowSizeSeconds
```

Do not use "60 seconds since this customer's first request" unless that is explicitly the policy.

## Required Tests

Use:

```text
limit = 3
window = 10 seconds
```

Test:

- first request
- requests within limit
- fourth request rejected
- next fixed window resets
- per-customer isolation
- exact window boundary
- invalid `limit`
- invalid `windowSize`

---

# Session 7 — HLD/Production: Local vs Global Rate Limiting

**15 minutes**

Suppose:

```text
3 application nodes
```

Each independently allows:

```text
100 requests/min/customer
```

A customer's traffic is spread across all nodes.

Potential accepted total:

```text
node A → 100
node B → 100
node C → 100

global → 300
```

Therefore:

```text
local limiter correctness
!=
global distributed limit
```

Do not implement the distributed version today.

## Production Concerns

### State Growth

Millions of one-time customer IDs can grow:

```text
Map<CustomerId, WindowState>
```

indefinitely unless stale state is cleaned up.

### Boundary Burst

Fixed windows can allow:

```text
100 near end of one window
+
100 near start of next
```

in a short real-time interval.

That is a semantic trade-off, not necessarily an implementation bug.

### Clock Dependence

Ask:

- what clock is used?
- can time move backward?
- how do tests control time?
- how would multi-node clock skew matter later?

---

# Session 8 — Production Debugging Scenario

**10 minutes**

Configuration:

```text
limit = 100/min/customer
single-node fixed-window limiter
```

Metrics:

```text
unique customers/hour ↑ 20x
request rate ↑ 1.5x
heap usage steadily ↑
GC frequency ↑
CPU moderately ↑
rate-limit rejects normal
```

Reason:

```text
Observation
↓
what retained state grows?
↓
why?
↓
what evidence confirms it?
↓
mitigation direction
```

Inspect:

```text
number of limiter-map entries
entry age
unique customer cardinality
cleanup behavior
allocation/GC metrics
```

Do not jump directly to increasing heap size.

---

# Session 9 — End-of-Day Retrieval

**10 minutes**

Answer without notes.

1. What is an invariant?
2. Why does lower bound preserve `mid` when `nums[mid] >= target`?
3. How can first/last occurrence be expressed using boundaries?
4. What is the difference between a primitive value and a reference?
5. Does `new Person[100]` create 100 Person objects?
6. What conceptually exists inside a Node object?
7. What is the difference between JVM heap and CPU cache?
8. Why can arrays have better locality than linked nodes?
9. State the fixed-window Rate Limiter invariant.
10. Why is time passed into the limiter useful for tests?
11. Why does a local limiter not guarantee a global multi-node limit?
12. Why can fixed-window semantics permit a boundary burst?
13. Why can limiter state cause memory growth?

---

# Deliverables

## Binary Search

- [ ] invariant stated as a property
- [ ] lower bound rewritten from memory
- [ ] exact vs boundary search explained
- [ ] first/last occurrence derived
- [ ] lower-bound reuse derived
- [ ] first-greater-than boundary derived
- [ ] duplicate-heavy tests pass
- [ ] O(log n) explained

## Java Memory

- [ ] primitive vs reference explained
- [ ] stack frame vs heap object explained
- [ ] `int[]` vs `Person[]` explained
- [ ] reference-array diagram correct
- [ ] Node object layout explained conceptually
- [ ] object header/fields/padding introduced
- [ ] JVM heap vs CPU cache distinction clear
- [ ] array locality vs pointer chasing explained

## Rate Limiter V1

- [ ] fixed-window policy chosen
- [ ] `allow(customerId, now)` API defined
- [ ] minimum state derived
- [ ] invariant stated
- [ ] implementation completed
- [ ] deterministic tests completed
- [ ] per-customer isolation tested
- [ ] boundary behavior tested
- [ ] invalid configuration defined

## HLD / Production

- [ ] local-vs-global enforcement limitation explained
- [ ] state-growth risk identified
- [ ] boundary-burst trade-off explained
- [ ] clock dependency identified
- [ ] production memory-growth scenario diagnosed

---

# End-of-Day Exit Criteria

## Binary Search

You can derive:

```text
first >= target
first > target
first occurrence
last occurrence
```

from monotonic predicates without memorizing independent templates.

## Invariants

You state:

```text
what must remain true
```

not:

```text
what the algorithm does next
```

## Java Memory

You can explain:

```text
local reference
↓
array object
↓
reference slots
↓
separate objects
```

and keep:

```text
JVM heap/stack
```

separate from:

```text
CPU cache/RAM
```

## Rate Limiter

You can move through:

```text
requirement
↓
time semantics
↓
minimum state
↓
invariant
↓
implementation
↓
tests
```

The fixed-window limiter may be considered complete at the **Phase-1 LLD level**.

It is not yet a distributed production Rate Limiter.

---

# Intentionally Deferred

Do not add:

- distributed Rate Limiter implementation
- Redis-backed limiter
- Lua
- token-bucket implementation
- rolling-window-log implementation
- thread safety
- Java Memory Model
- `volatile`
- CAS
- GC algorithms
- exact object-byte-size memorization
- virtual-memory/page-fault deep dive
- trees/heaps
- binary search on answer
- rotated-array search

---

# Day 10 Exact Starting Action

Start with:

> **State one invariant from any problem we have solved, but state only the property that must remain true — do not describe the implementation.**

Then:

> **Rewrite lower bound from memory using answer space `[0, n]`, and explain why `right = mid` preserves a valid candidate.**

If clean, immediately move into the first/last-position transfer problem.

---

# Registry Update Requirements

At the end of Day 10, update `cirriculum/registry.md` using evidence only.

Capture:

1. invariant-retrieval quality
2. canonical lower-bound rewrite status
3. first/last-position reasoning and implementation
4. boundary/off-by-one mistakes observed
5. primitive/reference evidence
6. stack/heap explanation quality
7. array/reference-array understanding
8. object-layout terminology precision
9. JVM heap vs CPU-cache distinction
10. Rate Limiter V1 implementation status
11. Rate Limiter tests passing
12. local-vs-global reasoning
13. production memory-growth diagnosis
14. remaining Phase-1 gaps
15. exact Day 11 starting action

Do not advance to Phase 2 merely because the calendar says so.

Advance when the remaining Phase-1 exit criteria are evidenced.
