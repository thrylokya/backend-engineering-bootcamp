# Day 4 — Implementation Discipline + Hashing Foundations

> **Duration:** 2 hours 45 minutes
> **Primary Theme:** Convert conceptual understanding into implementation evidence, then begin hashing from first principles.
> **Current Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory
> **Why Today Matters:** Your conceptual reasoning is progressing faster than your implementation evidence. Today fixes that imbalance while introducing the next major Phase-1 topic: hashing.

---

# Starting Point

Day 3 established good conceptual understanding of:

* Big-O versus actual runtime
* JIT method inlining
* JMH warm-up and measurement
* prefix sums
* prefix/suffix decomposition
* Product of Array Except Self
* Pivot Index
* precomputation trade-offs
* minimum necessary state
* Dynamic Array invariants
* mutable versus immutable data architectures

Several concepts were demonstrated strongly enough that they should **not be retaught today**.

In particular:

* prefix sums are understood
* prefix/suffix reasoning is understood
* Product Except Self reasoning is understood
* Pivot Index reasoning is understood
* JMH mental model is understood

However, the registry identifies an important imbalance:

```text
reasoning
>
implementation evidence
```

The following remain unproven through code/tests:

* Dynamic Array quality-gate tests
* Product of Array Except Self Java implementation
* actual JMH benchmark

JMH implementation does not need to block today's progression.

Dynamic Array verification and Product Except Self implementation **do need concrete engineering evidence**.

---

# Carry-Forward Growth Areas

Throughout Day 4, continue enforcing:

## 1. Exact Index Semantics

Before writing:

```text
i
i - 1
i + 1
```

state:

```text
What does this index represent?
What is included?
What is excluded?
What is true before processing i?
```

---

## 2. Brute Force Before Optimization

Required sequence:

```text
Correct baseline
↓
Complexity
↓
Repeated work
↓
Better state/data structure
↓
Invariant
↓
Optimization
```

Do not jump directly to HashMap simply because you recognize the problem.

---

## 3. Independent Complexity Variables

Continue distinguishing:

```text
n
q
capacity
number of buckets
chain length
```

when they represent independent dimensions.

---

## 4. JVM Terminology Precision

Keep separate:

```text
CPU cache
code cache
hot code
JIT
method inlining
bounds-check elimination
```

---

## 5. Implementation Must Follow Reasoning

Today's engineering rule:

> A concept is not considered implemented until the code passes meaningful edge-case tests.

---

# Today's Objectives

By the end of Day 4, you should be able to:

* verify your Dynamic Array against its invariants
* implement Product of Array Except Self in Java
* recognize when repeated lookup suggests hashing
* explain hashing without relying on Java `HashMap`
* distinguish a hash value from a bucket index
* explain why collisions are unavoidable
* explain average O(1) versus worst-case O(n)
* design the first version of a HashMap
* implement the core lookup/insert path of HashMap V1
* explain `equals()` / `hashCode()` from the perspective of map correctness
* connect local hash-based lookup to backend deduplication

---

# Session 1 — Spaced Retrieval

> **Time:** 15 minutes

No reteaching.

Answer these from memory.

---

## Question 1 — Prefix/Suffix Transfer

For:

```text
nums = [1, 2, 3, 4]
```

explain the invariant of the reverse pass in Product of Array Except Self.

Do not give code first.

Expected level:

> Before processing `i`, what exactly does `suffixProduct` contain?

---

## Question 2 — Multiple Input Variables

Suppose:

```text
n = number of elements
q = number of queries
```

Prefix preprocessing costs:

```text
O(n)
```

and each query costs:

```text
O(1)
```

What is total complexity for all queries?

Explain why you should not automatically call it `O(n)`.

---

## Question 3 — JVM Precision

Explain the difference between:

```text
CPU cache
```

and:

```text
JVM code cache
```

### Success Criteria

Proceed only if these answers are reasonably precise.

Do **not** spend more than 15 minutes repairing them.

---

# Session 2 — Engineering Quality Gate: Dynamic Array

> **Time:** 35 minutes

This is carry-forward implementation work.

Do not redesign the structure.

Take the existing implementation and verify it against the invariants already established.

---

# Core Invariants

## Size/Capacity

Always:

```text
0 <= size <= capacity
```

---

## Valid Element Index

For:

```text
get
set
remove
```

valid indexes are:

```text
0 <= index < size
```

Therefore:

```text
index == size
```

must fail.

---

## Append Position

The next item belongs at:

```text
elements[size]
```

Then:

```text
size++
```

Correct:

```text
elements[size] = value
size++
```

Incorrect:

```text
elements[++size] = value
```

---

## Remove Shift Count

Removing index `i` requires moving:

```text
size - i - 1
```

elements.

Therefore removing the last element requires:

```text
0 shifts
```

---

# Required Verification

## Test 1 — First Insertion

Start:

```text
size = 0
```

Perform:

```text
add(10)
```

Expected:

```text
elements[0] = 10
size = 1
```

---

## Test 2 — Growth Boundary

If:

```text
size == capacity
```

and another element is added:

Verify:

* capacity increases
* all previous elements survive
* new value is appended at the old `size`
* `size` increases exactly once

---

## Test 3 — Negative Index

Verify failures for:

```text
get(-1)
set(-1, ...)
remove(-1)
```

---

## Test 4 — `index == size`

Verify failures for:

```text
get(size)
set(size, ...)
remove(size)
```

This is one of today's most important boundary tests.

---

## Test 5 — Remove First

```text
[10,20,30,40]

remove(0)
```

Expected logical result:

```text
[20,30,40]
```

---

## Test 6 — Remove Middle

```text
[10,20,30,40]

remove(1)
```

Expected:

```text
[10,30,40]
```

---

## Test 7 — Remove Last

```text
[10,20,30]

remove(2)
```

Expected:

```text
[10,20]
```

No shifting required.

---

# Dynamic Array Exit Criteria

Before leaving the lab:

* [ ] append happens at `elements[size]`
* [ ] negative indexes fail
* [ ] `index == size` fails for access/update/remove
* [ ] grow preserves values
* [ ] remove boundaries are correct
* [ ] size changes exactly once
* [ ] required tests pass

Do not mark Dynamic Array fully complete otherwise.

---

# Session 3 — Coding Evidence: Product of Array Except Self

> **Time:** 20 minutes

The reasoning is already complete.

Today's objective is:

```text
reasoning
→ code
→ tests
```

Do not spend another session deriving the algorithm.

---

# Required Implementation

Implement the O(n) solution using:

```text
output array
+
running suffix product
```

Expected conceptual sequence:

### Forward

```text
answer[i]
=
product strictly before i
```

### Reverse

```text
answer[i] *= suffixProduct
suffixProduct *= nums[i]
```

---

# Required Tests

## Normal Case

```text
[1,2,3,4]

→ [24,12,8,6]
```

---

## One Zero

```text
[0,1,2,3]

→ [6,0,0,0]
```

---

## Two Zeros

```text
[0,0,2,3]

→ [0,0,0,0]
```

---

## Small Valid Input

Use one small legal edge case based on the problem constraints.

---

# Questions Before Completion

Explain:

1. Why does the forward pass write left-product state?
2. Why does the reverse update order matter?
3. What goes wrong if `suffixProduct` is updated before using it?
4. Why is auxiliary space O(1) even though an output array exists?

---

# Session 4 — Core Concept: Why Hashing Exists

> **Time:** 30 minutes

Now begin the new topic.

Do **not** begin with Java `HashMap`.

Begin with the problem hashing solves.

---

# Scenario

Suppose you have:

```text
[10, 42, 91, 17, 63]
```

and receive one lookup:

```text
Does 91 exist?
```

A scan is reasonable:

```text
O(n)
```

Now suppose you receive:

```text
1,000,000 membership queries
```

over largely unchanged data.

Ask:

> What work are we repeating?

Answer:

```text
searching through values repeatedly
```

Now ask:

> What information could we organize ahead of time so that we don't scan everything?

This is the motivation for hashing.

---

# Mental Model

Conceptually:

```text
key
 ↓
hash function
 ↓
hash value
 ↓
bucket mapping
 ↓
bucket index
 ↓
entry
```

---

# Important Distinction

Do not collapse:

```text
hash value
```

and:

```text
bucket index
```

into one concept.

Example:

```text
hash = 928374928
```

but if capacity is:

```text
16
```

then the bucket index must be:

```text
0..15
```

---

# Questions

## Q1

Why can't:

```text
bucketIndex = hash
```

generally work directly?

---

## Q2

Why do we need a finite bucket array?

---

## Q3

What property do we want from a hash function?

Not:

> unique hash for every possible input.

That is impossible for arbitrary key spaces mapped into finite hashes/buckets.

Think instead about **distribution**.

---

# Session 5 — Collisions & Complexity

> **Time:** 20 minutes

Suppose:

```text
many possible keys
```

must map into:

```text
finite number of buckets
```

Eventually:

```text
two different keys
→ same bucket
```

This is a collision.

---

# Important Principle

> A collision is not necessarily evidence of a bad hash table.

Collisions are unavoidable.

The goal is to handle them efficiently and distribute keys reasonably.

---

# Collision Strategy for HashMap V1

Use separate chaining.

Conceptually:

```text
bucket[0] → Entry → Entry
bucket[1] → null
bucket[2] → Entry
bucket[3] → Entry → Entry → Entry
```

---

# Average Lookup

Ideal-ish distribution:

```text
key
↓
bucket
↓
very short chain
↓
match
```

Approximate expected work:

```text
constant
```

Therefore average:

```text
O(1)
```

---

# Worst Case

If every key lands in the same bucket:

```text
bucket
 ↓
Entry
 ↓
Entry
 ↓
Entry
 ↓
...
```

Then lookup may scan:

```text
n entries
```

Worst case:

```text
O(n)
```

---

# Required Explanation

Do not say:

> HashMap is O(1) because hashing is O(1).

Explain the complete path:

```text
calculate hash
→ select bucket
→ inspect expected-small collision structure
```

and why poor distribution breaks that assumption.

---

# Session 6 — DSA Pattern Reinforcement

> **Time:** 20 minutes

Use two familiar problems.

The objective is not solving them again.

The objective is connecting the pattern to the data structure.

---

## Problem 1 — Contains Duplicate

Repeated question:

> Have I already seen this exact value?

Baseline:

```text
compare against previously seen values
```

Potential complexity:

```text
O(n²)
```

Better state:

```text
HashSet
```

---

# Required Invariant

Before processing index `i`:

> The set contains exactly the distinct values from indices strictly before `i`.

If current value is already there:

```text
duplicate found
```

---

## Problem 2 — Two Sum

Repeated question:

> What value do I need, and have I seen it already?

At index `i`:

```text
required = target - nums[i]
```

Store:

```text
value → index
```

---

# Important Edge Case

```text
nums = [3,3]
target = 6
```

Explain why:

```text
lookup required
then insert current
```

is safer than inserting first.

The algorithm must not reuse the same array element.

---

# Session 7 — Engineering Lab: HashMap V1 Design

> **Time:** 30 minutes

The goal today is to **design and start** HashMap V1.

Do not force every feature if correctness quality drops.

---

# Scope

For now:

```java
put(int key, int value)

get(int key)

containsKey(int key)

size()
```

Stretch if time remains:

```java
remove(int key)
```

No:

* generics
* resizing
* treeification
* concurrent support

---

# Internal Model

Reason about:

```text
IntHashMap
 ├── Entry[] buckets
 ├── int size
 └── Entry
      ├── int key
      ├── int value
      └── Entry next
```

Do not write code until you can justify each field.

---

# Design Questions

## 1.

Why is:

```text
buckets
```

an array?

Connect this to O(1) indexed access.

---

## 2.

What does:

```text
buckets[i]
```

represent?

Be precise.

---

## 3.

Why does `Entry` need:

```text
next
```

?

---

## 4.

What should this do?

```text
put(10, 100)
put(10, 200)
```

Expected logical map:

```text
10 → 200
```

and:

```text
size == 1
```

Why should duplicate logical keys not create duplicate mappings?

---

# Bucket Mapping

For integer keys, use a simple initial mechanism.

The invariant must be:

```text
0 <= bucketIndex < capacity
```

Be careful with negative integers.

Ask:

> Can `% capacity` produce a negative value in Java?

If yes, how will the design handle it?

---

# HashMap V1 Invariants

## Invariant 1

Every logical key appears at most once.

---

## Invariant 2

Every stored entry exists in the bucket chosen by the map's bucket function.

---

## Invariant 3

`size` equals the number of distinct logical key/value mappings.

---

## Invariant 4

Updating an existing key does not increase `size`.

---

# Implement First

Prioritize:

```text
put()
get()
```

Then:

```text
containsKey()
```

Only implement `remove()` if there is enough time.

---

# Required Test: Collision

Create two keys that intentionally map to the same bucket.

Then verify:

```text
put(k1,v1)
put(k2,v2)
```

and:

```text
get(k1) == v1
get(k2) == v2
```

This proves collision handling actually works.

---

# Session 8 — Java Deep Dive: `equals()` and `hashCode()`

> **Time:** 15 minutes

Your V1 uses integer keys.

Real Java `HashMap` needs object keys.

Suppose:

```java
class UserKey {
    long userId;
}
```

Two different Java objects might represent the same logical key.

Example:

```text
UserKey(100)
UserKey(100)
```

---

# Equality Contract

If:

```text
a.equals(b)
```

is true,

then:

```text
a.hashCode() == b.hashCode()
```

must also be true.

But:

```text
a.hashCode() == b.hashCode()
```

does **not** imply:

```text
a.equals(b)
```

because collisions exist.

---

# Derive Why

Suppose:

```text
a.equals(b) == true
```

but:

```text
hash(a) != hash(b)
```

Then:

```text
put(a, value)
```

may place `a` into one bucket.

Later:

```text
get(b)
```

may search another bucket.

The logically equal key is never inspected.

That breaks map semantics.

---

# Mutable-Key Problem

Suppose key fields participating in `hashCode()` change after insertion.

Conceptually:

```text
insert key
↓
bucket X
↓
mutate key
↓
new hash
↓
lookup searches bucket Y
```

The entry remains physically in bucket X.

It may effectively become unreachable through normal lookup.

---

# Session 9 — LLD/HLD + Production Connection

> **Time:** 15 minutes

## Scenario — Request Deduplication

An API receives:

```text
requestId
```

Clients retry requests.

Requirement:

> Do not process the same request ID twice.

---

# Version 1

Store processed IDs in:

```text
List
```

Every new request performs:

```text
linear scan
```

At millions of IDs, this becomes expensive.

---

# Version 2

Use:

```text
HashMap / HashSet
```

for fast keyed lookup.

Potential mapping:

```text
requestId
→
processing result/status
```

---

# Now Move to HLD Thinking

Suppose the service runs on:

```text
20 application nodes
```

Each node maintains its own in-memory HashMap.

Question:

> Does this guarantee global deduplication?

No.

Reason from first principles:

```text
request A hits node 1
request retry hits node 7
```

Node 7 does not know node 1's local state.

---

# Architecture Boundary

Today's key insight:

```text
HashMap
=
local data structure
```

It does not automatically provide:

```text
distributed coordination
durability
global uniqueness
cross-node consistency
```

Future system-level mechanisms may use:

* database uniqueness constraints
* Redis
* idempotency stores

Do not go deeper today.

---

# Session 10 — Production Engineering Scenario

> **Time:** 10 minutes

A service uses a hash-based lookup structure.

After a deployment:

```text
request volume = unchanged
memory = normal
GC = normal
DB latency = normal
CPU ↑ sharply
p99 latency ↑ sharply
```

Profiler shows substantial time walking collision chains.

---

# Diagnose

Answer:

1. What does long chain traversal imply?
2. Could the hash function have changed?
3. Could key distribution have changed?
4. Why might average O(1) degrade?
5. What would worst-case complexity approach?
6. What profiling data would confirm the hypothesis?
7. Would adding more heap fix the root cause?

---

# Production Lesson

Hash-table performance depends on:

```text
hash distribution
+
bucket count
+
collision handling
+
load factor
```

Big-O alone does not tell the complete production story.

---

# Deliverables

## Carry-Forward Implementation

* [ ] Dynamic Array first-insertion test
* [ ] Dynamic Array growth-boundary test
* [ ] Dynamic Array negative-index tests
* [ ] Dynamic Array `index == size` tests
* [ ] Dynamic Array remove-first test
* [ ] Dynamic Array remove-middle test
* [ ] Dynamic Array remove-last test
* [ ] Dynamic Array implementation fixed if required

## Product Except Self

* [ ] Java implementation complete
* [ ] normal case test
* [ ] one-zero test
* [ ] two-zero test

## Hashing

* [ ] hashing motivation explained
* [ ] hash value vs bucket index explained
* [ ] collisions explained
* [ ] average vs worst-case lookup explained

## HashMap V1

* [ ] internal data model designed
* [ ] invariants documented
* [ ] `put()` implemented
* [ ] `get()` implemented
* [ ] `containsKey()` implemented
* [ ] collision test written
* [ ] update-existing-key test written

### Stretch

* [ ] `remove()` implemented

## Java

* [ ] `equals()` / `hashCode()` contract explained
* [ ] mutable-key failure explained

## Design

* [ ] local deduplication design explained
* [ ] distributed limitation identified

## Production

* [ ] poor-distribution incident diagnosed

---

# Reflection / Interview Questions

Answer these without notes.

## 1.

Why is repeated lookup over an unsorted array expensive?

---

## 2.

What problem is hashing fundamentally trying to solve?

---

## 3.

What is the difference between:

```text
hash value
```

and:

```text
bucket index
```

?

---

## 4.

Why are collisions mathematically unavoidable?

---

## 5.

Why is HashMap lookup average O(1) rather than guaranteed O(1)?

---

## 6.

What happens if every key maps to one bucket?

---

## 7.

Why must equal Java objects produce equal hash codes?

---

## 8.

Why doesn't equal hash code imply equal objects?

---

## 9.

Why is mutating a HashMap key dangerous?

---

## 10.

Why does a local HashMap solve a different problem from a distributed idempotency store?

---

## 11.

What invariant guarantees that updating an existing map key should not increase `size`?

---

## 12.

Why should implementation tests be considered evidence of understanding rather than just validation after the fact?

---

# End-of-Day Exit Criteria

Day 4 is complete when the following are demonstrated.

---

## Dynamic Array

You have executable evidence that:

```text
0 <= size <= capacity
```

and:

```text
0 <= index < size
```

are respected.

Boundary tests pass.

---

## Product Except Self

You can:

```text
reason
→ implement
→ test
```

the optimized solution without copying a reference.

---

## Hashing

You can derive:

```text
repeated lookup
↓
key organization
↓
hash
↓
bucket
↓
collision handling
```

from first principles.

---

## Complexity

You can explain:

```text
Average O(1)
Worst O(n)
```

without hand-waving.

---

## HashMap V1

At minimum:

```text
put
get
containsKey
```

work correctly, including collisions and existing-key updates.

`remove()` may carry into Day 5 if necessary.

Correctness is more important than forcing completion.

---

## Java

You can explain the `equals()` / `hashCode()` contract through actual HashMap lookup behavior.

---

## Engineering Maturity

You demonstrate today's most important progression:

```text
understand
↓
implement
↓
test
↓
prove with edge cases
```

rather than stopping after conceptual reasoning.

---

# Explicitly Not Part of Day 4

Do not expand into:

* Java HashMap treeification
* red-black trees
* ConcurrentHashMap
* consistent hashing
* Bloom filters
* distributed caches
* lock-free structures
* Fenwick Tree
* Segment Tree

These all have later prerequisite positions.

---

# Exact Starting Action

Begin Day 4 with this retrieval question:

> In Product of Array Except Self, before processing index `i` during the reverse traversal, exactly what does `suffixProduct` represent, and why must `answer[i]` be updated before multiplying `suffixProduct` by `nums[i]`?

After that, move directly into the Dynamic Array quality gate.

---

# Registry Update Requirements

At the end of Day 4, update `cirriculum/registry.md` based only on work actually completed.

Specifically record:

* whether Dynamic Array tests pass
* bugs discovered and fixed
* whether Product Except Self was implemented
* hashing mental-model evidence
* HashMap V1 methods actually implemented
* collision-handling evidence
* `equals/hashCode` understanding
* new misconceptions
* unfinished work
* exact next action

Do not mark HashMap V1 complete merely because its design was discussed.
