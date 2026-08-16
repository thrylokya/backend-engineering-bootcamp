# Day 6 — Ownership vs Business State, Two-Pointer Reasoning & Linked List V1

> **Duration:** ~2 hr 15 min  
> **Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory  
> **Primary Theme:** Stabilize task-ownership reasoning, then move forward into two-pointer thinking and Linked List V1.  
> **Design Constraint:** Do not add more distributed-systems vocabulary until ownership vs business state is clear.

---

# Why Day 6 Looks Like This

Day 5 produced strong evidence in:

- HashMap V1 testing/refactoring
- abstraction-first DSA
- linked-structure/cache-locality reasoning
- first real HLD around payment idempotency and failure recovery

One gap remains:

```text
worker/task ownership
!=
business-operation state
!=
external-system outcome
```

Day 6 reinforces that distinction briefly, then moves forward in Phase 1.

---

# Learning Outcomes

By the end of Day 6, you should be able to:

- distinguish job state from worker ownership without prompting
- derive a two-pointer solution from repeated work rather than pattern memorization
- state linked-list invariants before implementation
- implement a small singly linked list correctly
- test boundary cases around head/tail/size
- explain when linked lists are useful despite worse cache locality than arrays
- write HLD invariants without prematurely naming mechanisms

---

# Session 1 — Retrieval: Ownership vs Business State

**10 minutes**

Use a non-payment example.

Scenario:

```text
ImageJob J1
status = RUNNING

Worker A claimed J1.
Worker A disappears.
```

Answer:

1. What does `status = RUNNING` tell us?
2. What does it *not* tell us?
3. What additional information would help decide whether the work is abandoned?
4. What does expiry of ownership mean?
5. Why does stale ownership not automatically tell us the business outcome?

Target distinction:

```text
job/business state
→ where the logical operation is in its lifecycle

worker ownership
→ who currently has permission to perform/recover a piece of work

lease expiry
→ ownership became stale

lease expiry
!=
business operation failed
```

Do not proceed until this distinction can be explained cleanly.

---

# Session 2 — DSA Abstraction-First: Derive Two-Pointer Thinking

**50 minutes**

Do not announce the pattern before deriving it.

For every problem:

```text
1. Objective/output
2. Relevant information
3. Discardable information
4. Minimum sufficient state
5. Brute force
6. Repeated work
7. Invariant
8. Better representation/algorithm
9. Tiny dry run
10. Code
```

## Problem 1 — Valid Palindrome

Given a string, determine whether it is a palindrome after ignoring non-alphanumeric characters and case.

Do not start with "two pointers."

First derive:

- what comparisons determine the answer?
- which characters are irrelevant?
- do we need to build a completely new string?
- what work would brute force or copying introduce?
- can the original string itself preserve the information we need?

Target invariant to eventually derive:

```text
Everything strictly outside [left, right]
has already been validated as matching.
```

Complexity target:

```text
Time: O(n)
Auxiliary space: O(1)
```

if solved without constructing a normalized copy.

---

## Problem 2 — Two Sum II / Sorted Pair Sum

Given a sorted integer array and a target, find two values/indices whose sum equals the target.

Do not begin with a HashMap.

Ask:

- what information does sorted order give us?
- if current sum is too small, which direction can possibly improve it?
- if current sum is too large, which direction can possibly improve it?
- what search space can be permanently discarded after each comparison?

Target invariant:

```text
If the current sum is too small,
all pairs using the current left element with an index <= right
that cannot reach the target may be discarded by advancing left.

If the current sum is too large,
the right boundary can move left.
```

The important learning is not the exact wording; it is deriving monotonic elimination from sorted order.

Complexity target:

```text
Time: O(n)
Space: O(1)
```

---

# Session 3 — Linked List V1: Invariants Before Code

**35 minutes**

We already understand:

```text
Array
→ contiguous storage
→ direct index calculation

Linked List
→ nodes + references
→ traversal required
```

Today we implement a deliberately small singly linked list.

## V1 State

Start by deciding the minimum fields.

Possible model:

```text
IntLinkedList
├── Node head
├── Node tail
└── int size
```

Do not add fields unless they protect a useful invariant or operation.

## Required invariants

State these before implementation:

```text
size == 0
→ head == null
→ tail == null
```

```text
size == 1
→ head == tail
```

```text
size > 0
→ tail.next == null
```

```text
size
= number of nodes reachable by following next from head
```

## Implement only

- `addFirst(int value)`
- `addLast(int value)`
- `get(int index)`
- `size()`

Do **not** implement remove, reverse, iterator, generics, or doubly-linked behavior today.

### Complexity reasoning

Derive:

```text
addFirst → O(1)
addLast  → O(1) if tail is maintained
get(i)   → O(i), O(n) worst case
size     → O(1) if explicitly maintained
```

Then ask:

> What additional invariant cost do we take on by maintaining `tail` and `size`?

This is the LLD trade-off: faster operations in exchange for more mutable state that must remain consistent.

---

# Session 4 — Linked List V1 Tests

**20 minutes**

Write JUnit 5 tests before adding more functionality.

Required tests:

- empty list has size 0
- addFirst into empty list
- addFirst twice preserves order
- addLast into empty list
- addLast after existing nodes
- get first index
- get middle/last index
- invalid negative index
- index == size is invalid
- size remains correct after mixed addFirst/addLast

Testing principle:

> Test invariants through externally observable behavior. Do not assert private `Node` fields directly.

---

# Session 5 — Narrow HLD/LLD Reinforcement

**15 minutes**

Return to the image-job scenario, not payments.

Scenario:

```text
J1 = RUNNING
owner = Worker-A
ownership expired
```

Before mechanisms, derive:

## Bad outcomes

- job remains stuck forever
- two workers perform the same non-idempotent effect concurrently

## Invariants

Write only two:

1. a claimed unit of work must eventually become recoverable if ownership disappears
2. at most one valid owner may hold permission to perform the protected operation at a time

Then identify minimum durable information required.

Only after that may words such as:

```text
owner
claimedAt
expiresAt
heartbeat
lease
```

be introduced.

The objective is to make:

```text
bad outcome
↓
invariant
↓
minimum state
↓
mechanism
```

feel natural.

---

# Session 6 — End-of-Day Retrieval

**5 minutes**

Without notes:

1. What is the difference between business state and worker ownership?
2. What does lease expiry prove, and what does it not prove?
3. Why can sorted input sometimes eliminate the need for a HashMap?
4. State a two-pointer invariant in your own words.
5. Why is linked-list indexed access O(n)?
6. Why can `addLast` be O(1) if we maintain `tail`?
7. What correctness burden does maintaining `tail` introduce?
8. Why should `index == size` fail for `get(index)`?

---

# Deliverables

- [ ] ownership-vs-business-state explanation without prompting
- [ ] Valid Palindrome reasoned and implemented
- [ ] Sorted Pair Sum reasoned and implemented
- [ ] `IntLinkedList` V1 implemented
- [ ] Linked List V1 JUnit suite passes
- [ ] linked-list invariants explained
- [ ] two invariant drills completed without mechanism-first answers

---

# Intentionally Deferred

Do **not** add these to Day 6:

- linked-list removal
- reverse linked list
- fast/slow pointers
- stack implementation
- queue implementation
- LRU cache
- HashMap resizing
- distributed locks
- payment reconciliation internals
- Kafka
- JMH benchmark

Pending engineering debt remains tracked:

- Dynamic Array implementation/tests quality gate
- Product Except Self JUnit tests
- JMH benchmark implementation

These should be scheduled deliberately rather than dumped into Day 6.

---

# Day 6 Exact Starting Action

Start with this question:

> An image-processing job says `RUNNING`, but the worker that claimed it stopped renewing ownership. What does that tell us about **ownership**, and what does it tell us about the **actual image-processing outcome**?

Do not answer using the word `lease` initially.

---

# Registry Update Requirements

At the end of Day 6 record only demonstrated evidence:

1. ownership-vs-business-state distinction
2. DSA problems actually solved
3. two-pointer reasoning evidence
4. Linked List methods actually implemented
5. tests actually passing
6. invariant mistakes or boundary bugs discovered
7. exact pending work
8. exact Day 7 starting action
