# Backend Engineering Bootcamp — Registry

> This file is the operational source of truth for where the bootcamp currently stands.
> Daily curricula should be generated from `MASTER_CURRICULUM.md` + this registry + previous-session evidence.

---

# Current Position

* **Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory
* **Latest Curriculum Worked:** Day 6 — Job Ownership Reliability, Two-Pointer Reasoning & Linked List V1
* **Day 6 Date:** 2026-08-22
* **Status:** Day 6 completed at the learning/evidence level. The Day 5 worker-ownership gap was revisited and materially improved through a small in-memory job-processing implementation. Two-pointer reasoning was introduced through safe-elimination thinking. Linked List V1 was implemented and tested, followed by reversal, fast/slow middle detection, cycle reasoning, and fixed-gap reasoning for removing the Nth node from the end.
* **Next Curriculum:** Day 7
* **Primary Language:** Java
* **Target Level:** Strong Senior / Lead / Staff-level backend engineering capability
* **Primary Goal:** Production engineering excellence + top-tier interview readiness

---

# Day 5 — Completed Learning & Evidence

## HashMap V1 Engineering Quality Gate

Completed JUnit 5 coverage for:

* basic put/get
* logical size
* same-key update without increasing size
* negative keys
* collision handling
* missing-key `get()` exception
* `containsKey()` true/false behavior
* different keys with the same value

Refactoring evidence:

* redundant capacity removed
* backing array renamed conceptually to buckets
* unnecessary `Entry` getters/setters removed
* nested `Entry` made static
* key identity treated as stable
* shared `findEntry`-style traversal abstraction derived

Key lesson:

> Tests prove behavioral contracts and protect refactoring; they should not assert implementation details.

## DSA Abstraction-First Evidence

Completed:

* Ransom Note
* First Unique Character
* Intersection of Two Arrays

Reasoning sequence established:

```text
objective
→ relevant information
→ discardable story/noise
→ minimum sufficient state
→ brute force
→ repeated work
→ invariant
→ optimized representation
→ code
```

Continue enforcing abstraction before naming a data structure.

## Linked Structures & Memory

Demonstrated:

* array indexed access derives from base + offset
* linked-list indexed access requires reference traversal
* cache locality affects real runtime separately from Big-O
* linked nodes introduce pointer chasing and Java object/reference overhead

## First Real HLD — Payment Idempotency

Core distinctions established:

```text
duplicate delivery != duplicate business effect
request-level idempotency != business-level uniqueness
UNKNOWN != FAILED
```

A local DB transaction cannot atomically include an independent external payment gateway. Temporary inconsistency may occur; reconciliation/eventual convergence is required.

Safety vs liveness introduced:

* **Safety:** something bad must never happen.
* **Liveness:** useful progress must eventually happen.

---

# Day 6 — Completed Learning & Evidence

## 1. Worker Ownership vs Job State

The Day 5 high-priority gap was revisited using a non-payment `ImageJob` example.

Key distinction now understood:

```text
job/business state
!=
worker state
!=
ownership state
```

`RUNNING` means only that the latest durable state says the job entered an in-progress state and no later terminal state was recorded.

It does **not** prove:

* the worker is alive
* the worker is dead
* the worker is still executing
* the business operation failed
* no side effect occurred
* retrying the external effect is safe

Important mental model:

> Failure to communicate is not the same thing as failure of execution.

## 2. Lease Semantics

Lease understood as **time-bounded ownership**, not worker-liveness detection.

A lease expiry means:

> The previous worker's ownership is no longer valid.

It does not mean:

> The previous worker is dead or has stopped executing.

Lease purpose:

> Solve liveness/recovery by allowing another worker to reclaim work when the current owner fails to renew its authority.

## 3. Fencing Tokens

Fencing introduced to protect against stale owners after lease expiry/reassignment.

```text
Worker A claims J1 → token 10
lease expires
Worker B claims J1 → token 11
```

If A later attempts completion with token 10 while authoritative state has token 11, A must be rejected.

Core distinction:

```text
Lease
→ When may another worker take ownership?

Fencing token
→ Is this execution still authorized to commit?
```

Important modeling lesson:

```text
Job
→ current authoritative mutable state

JobClaim
→ immutable authority granted to one particular execution attempt
```

`ThreadLocal` was rejected for fencing state because the token belongs to a **claim/execution attempt**, not to a reusable executor thread.

## 4. At-Least-Once Execution vs Duplicate Business Effects

Failure scenario understood:

```text
receive work
→ perform business effect
→ crash before ACK/completion update
→ infrastructure redelivers/reclaims
```

Important distinction:

```text
duplicate execution != duplicate business effect
```

For externally visible effects such as payment, email, shipment, or loyalty credit, downstream protection may require:

* idempotency keys
* deduplication
* uniqueness constraints
* reconciliation

Job infrastructure delivery semantics do not automatically guarantee exactly-once business effects.

## 5. In-Memory Job Processor MVP

Implemented a deliberately small Java model using:

* one `ArrayList<Job>` as the in-memory authoritative store
* `ExecutorService`
* multiple worker tasks
* synchronized claim transition
* processing outside the synchronization boundary
* lease expiry
* owner identity
* fencing token
* immutable `JobClaim`

Critical concurrency lesson:

> Synchronize the state transition, not the long-running business work.

Correct shape:

```text
LOCK
claim
UNLOCK

process concurrently

LOCK
validate ownership/token and complete
UNLOCK
```

Full Java concurrency/JMM remains scheduled for Phase 3.

## 6. Executor/Worker Lifecycle Mental Model

Clarified:

```text
ExecutorService
→ manages threads and Runnable/Callable tasks

Worker loop
→ application logic that repeatedly asks JobStore for work
```

An executor does not automatically monitor a business job store.

Avoid:

```text
while(true) {
    executor.submit(...)
}
```

For a continuously running polling worker:

```text
submit worker once
→ worker loops
→ claim/process or sleep
→ repeat until shutdown
```

## 7. Pattern Recall / Abstraction Drill

Refreshed:

* linear scan + minimal state
* frequency counting
* two-pass counting while preserving original ordering
* membership lookup
* uniqueness through `Set`
* two pointers on sorted data
* sliding-window intuition
* binary-search elimination

Primary DSA protocol reinforced:

```text
Objective
↓
Search space
↓
Relevant information
↓
Repeated work
↓
What can be safely eliminated?
↓
What property makes that elimination valid?
↓
Only then choose the technique
```

Key lesson:

> Do not recognize a pattern from superficial story cues. Derive the technique from the property that lets work be avoided or candidates be safely eliminated.

## 8. Sorted Pair Sum — Two Pointers

Derived an `O(n)` two-pointer solution from sorted-order monotonicity.

```text
sum < target  → move left rightward
sum > target  → move right leftward
sum == target → success
```

Why elimination is safe:

* if smallest + largest is too small, the smallest cannot work with any remaining partner
* if smallest + largest is too large, the largest cannot work with any remaining partner

## 9. Sliding Window Intuition

Problem used: minimum-length contiguous subarray with sum at least target.

Enabling property:

> All numbers are positive.

Therefore:

```text
move right boundary → sum can only increase
move left boundary  → sum can only decrease
```

Sliding-window intuition still needs future repetition before being considered fully internalized.

## 10. Binary Search Recall

Sorted-order property used to eliminate half the search space after one comparison.

```text
n
n/2
n/4
n/8
...
```

Result: `O(log n)`.

## 11. Linked List V1 — Implementation

Implemented a singly linked list maintaining:

* `head`
* `tail`
* `size`
* `Node(value, next)`

Implemented operations:

* `addFirst(int)`
* `addLast(int)`
* `get(int)`
* `removeFirst()`
* `removeAfter(Node)`
* `size()`
* `reverse()`
* `findMiddle()`
* remove-Nth-from-end exercise at algorithm-refinement level

Core invariants:

```text
empty:
head == null
tail == null
size == 0

non-empty:
head != null
tail != null
tail.next == null
```

Complexities understood:

| Operation | Complexity |
|---|---:|
| addFirst | O(1) |
| addLast with tail | O(1) |
| get(index) | O(n) worst case |
| removeFirst | O(1) |
| removeAfter(knownNode) | O(1) |
| delete tail in singly linked list | O(n) even with tail |
| size with stored counter | O(1) |

## 12. Linked List Memory / CPU Reasoning

Array indexed access:

```text
address = base + index × elementSize
```

Linked-list indexed access:

* nodes need not be contiguous
* each next address is obtained by dereferencing the current node
* reaching index `k` requires following prior references
* worst-case indexed access is `O(n)`

Real-runtime distinction:

* arrays tend to have strong cache locality
* linked lists introduce pointer chasing
* scattered nodes can cause additional cache misses

Use **cache miss / memory fetch**, not “memory scan.”

## 13. Linked List Testing Evidence

JUnit coverage written for:

* addFirst on empty list
* repeated addFirst
* negative value insertion
* addLast on empty/non-empty list
* repeated addLast
* get valid/invalid indices
* removeFirst
* repeated removeFirst
* removeFirst on empty list
* removeFirst on one-node list
* removeAfter null
* removeAfter middle / node before tail
* removeAfter tail as no-op
* reverse multi-node/single-node/empty
* findMiddle empty/single/odd/even cases

Testing lesson:

> Draw the linked-list state before and after each mutation before writing expected assertions.

Several early assertions were reversed because repeated `addFirst()` ordering was not mentally tracked. This was corrected.

## 14. Reverse Linked List

Implemented a valid iterative reversal using repeated front insertion.

Invariant:

> Keep the original head/current node fixed, repeatedly detach `current.next`, and move that node to the front.

Required final invariants:

* old head becomes tail
* new head is old tail
* `tail.next == null`
* size unchanged

Complexity:

* `O(n)` time
* `O(1)` auxiliary space

## 15. Fast/Slow Pointer — Find Middle

Implemented one-pass middle detection.

```text
slow moves 1
fast moves 2
```

Loop safety condition:

```java
fast != null && fast.next != null
```

Behavior:

* odd length → exact middle
* even length → second middle

Complexity:

* `O(n)` time
* `O(1)` space
* one pass

Key abstraction:

> Relative speed encodes position information without first computing list length.

## 16. Cycle Detection Reasoning

Brute-force approach:

* maintain `HashSet<Node>` of visited node identities
* repeated node identity implies a cycle
* repeated node value does not imply a cycle

Floyd fast/slow reasoning:

* slow moves 1
* fast moves 2
* inside a finite cycle, fast gains one node per iteration relative to slow
* if a cycle exists, references eventually meet

Complexity:

* `O(n)` time
* `O(1)` auxiliary space

Production-vs-interview trade-off discussed: a `HashSet` may be perfectly acceptable when simplicity is worth the memory; Floyd is attractive when auxiliary memory/allocation matters.

## 17. Remove Nth Node From End — Fixed Gap

Two-pass baseline derived:

```text
length L
node index = L - n
predecessor index = L - n - 1
```

One-pass fixed-gap reasoning introduced:

> Move fast pointer ahead once to establish a fixed distance, then move fast and slow at the same speed.

Important implementation correction:

> Do not repeatedly jump fast by `n`; establish the gap once, then advance both by one.

Dummy-node technique introduced:

```text
dummy → head → ...
```

Purpose:

> Give the real head an artificial predecessor so head deletion no longer needs a structurally special case.

Important helper-contract lesson:

> A synthetic dummy node does not necessarily satisfy assumptions of helper methods written only for real list nodes.

This problem was understood conceptually; implementation should be finalized/cleaned in the next short review if needed rather than expanded into more linked-list problems.

---

# Day 6 — Improvement Findings / Remaining Gaps

## DSA Pattern Intuition

Improving, but not yet automatic.

Continue short pattern-recall drills and do not name the pattern until objective, repeated work, invariant/property, and safe-elimination rule are stated.

Sliding-window intuition requires more repetition.

## Testing Discipline

For pointer-heavy structures, explicitly track:

```text
Before mutation
→ operation
→ after mutation
```

before writing assertions.

## Concurrency Scope

The job-processing MVP successfully exposed atomic claim, shared mutable state, processing outside locks, stale workers, leases, and fencing.

Do not expand Phase 1 into full Java concurrency.

Formal concurrency remains for Phase 3:

* Java Memory Model
* happens-before
* visibility
* `volatile`
* CAS/atomics
* locks
* executor internals
* deadlocks/starvation/contention
* virtual threads

## Terminology Precision

Prefer:

* worker instead of “another job” when referring to the execution actor
* cache miss / memory fetch instead of memory scan
* lease expiry means ownership invalid, not worker death
* fencing token means ownership generation/authority, not worker identity
* at-least-once execution does not equal exactly-once business effect

---

# Ongoing HLD Practice Protocol

Before architecture:

1. Entities
2. Bad outcomes
3. Invariants
4. Failure scenarios
5. Minimum durable state
6. Mechanisms

If an answer starts with a mechanism such as lock, queue, retry, cache, or poll, ask:

> What truth/invariant is this mechanism protecting?

---

# Ongoing DSA Practice Protocol

Before coding:

1. What is the objective?
2. What is the brute-force search space?
3. What information actually affects the answer?
4. What details can be discarded?
5. What work is repeated?
6. What can be safely eliminated?
7. What property/invariant makes that elimination safe?
8. What is the smallest sufficient representation/state?
9. Only then choose the pattern/data structure.
10. Code after the reasoning is stable.

---

# Exact Next Action — Day 7

Generate Day 7 from:

```text
MASTER_CURRICULUM.md
+
this registry
+
Day 6 evidence
```

Day 7 should:

1. Start with a short mixed DSA pattern-recall drill covering linear scan, hashing/membership, frequency, two pointers, and fast/slow reasoning.
2. Briefly verify/finalize `removeNthFromEnd` dummy-node implementation if it remains incomplete; do not spend substantial session time on it.
3. Continue the next Phase-1 data-structure/pattern topic from the master curriculum.
4. Preserve abstraction-first coaching before every DSA implementation.
5. Keep HLD exposure active, but do not expand the Day 6 job-processing MVP into advanced concurrency.
6. Add tests/evidence for the next structure before marking the day complete.
