# Day 5 — Engineering Evidence, Abstraction-First DSA & First Real HLD

> **Duration:** ~2 hr 50 min  
> **Primary Theme:** Prove HashMap correctness, strengthen abstraction-first problem solving, then begin real HLD without overloading the day.  
> **Current Phase:** Phase 1 — Foundations  
> **HLD Level:** Introductory but real — one narrow system, failure mode by failure mode.

The current registry says Day 4's core hashing work and HashMap V1 are complete, while tests/refactoring and the request-idempotency discussion remain unfinished.

The master curriculum says HLD/LLD should start early and continuously include storage, state, failure handling, observability, APIs, and trade-offs.

---

## Today's Objectives

By the end of Day 5, you should be able to:

- prove HashMap V1 through automated tests rather than reasoning alone
- clean up its design without premature optimization
- solve 2–3 DSA problems by stripping away the story **before** choosing a data structure
- reason correctly about linked-node traversal
- understand why linked structures behave differently from arrays in memory
- drive your first proper HLD discussion around request idempotency
- reason through crashes, retries, and transaction boundaries without jumping to buzzwords

---

# Session 1 — Retrieval Warm-Up

**10 minutes**

No notes.

### Question 1

Why is HashMap lookup only **expected O(1)** rather than guaranteed O(1)?

Your answer should involve:

```text
number of entries = n
number of buckets = m
load factor α = n / m
```

and collision-chain length.

### Question 2

For your HashMap V1, state these three invariants:

- key uniqueness
- bucket-placement correctness
- meaning of `size`

### Question 3

You encounter a DSA problem wrapped in a long story.

Before deciding "HashMap", "array", "queue", etc., what are the first four things you should identify?

Expected framework:

```text
objective
relevant information
discardable information
minimum sufficient representation
```

This becomes mandatory from today onward.

---

# Session 2 — HashMap V1 Engineering Quality Gate

**25 minutes**

Do not add functionality.

Today is:

```text
implemented
→ tested
→ cleaned
```

## Required JUnit Tests

### Basic mapping

```text
put(1, 100)
get(1) → 100
size() → 1
```

### Intentional collisions

With capacity `10`:

```text
1
11
21
```

Force them into the same chain.

Verify all three remain retrievable.

### Update existing key

```text
put(1, 100)
put(1, 500)
```

Verify:

```text
get(1) == 500
size() == 1
```

### Missing key

Verify:

```text
get(missing)
```

throws the intended exception.

And:

```text
containsKey(missing)
```

returns false.

### Negative key

Verify your `Math.floorMod` behavior.

### Same value, different keys

```text
put(1, 100)
put(2, 100)
```

Must produce two mappings.

---

## Cleanup

Only after tests pass:

- remove redundant `capacity` if `buckets.length` is sufficient
- remove unused `grow()` until resizing is deliberately introduced
- remove `setKey()`; key identity should remain stable
- rename `map` → `buckets` if that improves meaning
- optionally extract `findEntry(key)` **only if duplication justifies it**

Do not refactor first and then discover you changed behavior.

---

# Session 3 — DSA: Abstraction Before Data Structure

**50–55 minutes**

This is the most important training block today.

Do **not** identify the pattern from the problem title.

For every problem, follow:

```text
1. What output is required?
2. What information affects the output?
3. What story details are irrelevant?
4. What is the smallest sufficient representation?
5. Brute force
6. Complexity
7. Repeated work
8. Invariant
9. Better solution
10. Tiny dry run
11. Code
```

---

## Problem 1 — Ransom Note

Do **not** think about ransom notes.

Strip the problem down mathematically.

Ask:

> What information about the characters actually determines whether the answer is possible?

I want you to discover the minimum state before choosing its representation.

### Challenge

Can you solve it with:

```text
O(1)
```

auxiliary space under the constraint of lowercase English letters?

Be precise about why that is considered O(1).

---

## Problem 2 — First Unique Character in a String

Start with the brute force.

For every character, what would the naive algorithm repeatedly ask?

Then identify what can be precomputed.

### Important design question

Why may one pass be insufficient if you must return the **first** unique character?

Think about:

```text
frequency information
+
original ordering
```

---

## Problem 3 — Intersection of Two Arrays

Do this only if the first two go well.

Important requirements:

- intersection
- unique output values

Before using any set, explain why uniqueness changes the representation you need.

---

# DSA Success Criterion

I care less about getting all three problems finished than whether you consistently do:

```text
story
↓
objective
↓
relevant information
↓
minimum state
↓
algorithm
```

instead of:

```text
story
↓
classes / arrays / HashMaps immediately
```

That is the behavior we're deliberately retraining.

---

# Session 4 — Core Data Structure: Linked Structures

**25 minutes**

HashMap separate chaining has already exposed you to:

```text
Entry
 ├── value
 └── next
```

Now formalize that mental model.

## Understand

For:

```text
A → B → C → null
```

`A`, `B`, and `C` are separate objects.

Unlike an array, they are not logically dependent on contiguous placement.

---

## Traversal Invariant

Prefer reasoning:

> `current` is the node I am currently inspecting.

Then:

```text
while current != null
    inspect current
    current = current.next
```

This reinforces the HashMap-chain bug you encountered on Day 4.

---

## Compare Array vs Linked List

Derive rather than memorize.

### Array

```text
arr[i]
```

can derive an address from:

```text
base + offset
```

Therefore indexed access is O(1).

### Linked list

To reach node `i`:

```text
head
→ next
→ next
→ ...
```

Therefore lookup is O(n).

---

## Java/JVM Connection

Explain why a linked structure may have:

- more object allocations
- object headers
- references
- pointer chasing
- worse cache locality

than a primitive contiguous array.

Don't go into GC internals yet.

---

# Session 5 — First Proper HLD: Request Idempotency

**40 minutes**

This is where **actual HLD starts**.

But we are keeping the scope narrow so you are not juggling 15 distributed-systems concepts at once.

We resume exactly where Day 4 stopped.

```text
request_id = stable identity

status =
    PROCESSING
    COMPLETED
    FAILED

result = replayable completed result
```

and a shared store plus atomic claim/unique constraint is needed across nodes.

Today you drive the design.

I should interview you one question at a time when we actually study it.

---

## Part 1 — Retry State Machine

Derive what a retry should do when it finds:

### `COMPLETED`

Question:

> Should the business operation execute again?

What should the API return?

---

### `PROCESSING`

Harder.

Suppose the first request is genuinely still running.

What should a retry do?

Possible dimensions to reason about:

- wait?
- reject?
- tell caller to retry later?
- return known state?

Do not choose until requirements are clear.

---

### `FAILED`

Ask:

> Does FAILED mean safe to retry?

Not necessarily.

You must distinguish:

```text
business action definitely never happened
```

from:

```text
we don't know whether it happened
```

That distinction becomes important.

---

# Part 2 — Worker Crash

Scenario:

```text
Node A claims request R
↓
status = PROCESSING
↓
Node A dies
```

Now the database may permanently say:

```text
PROCESSING
```

Question:

> How does another worker distinguish "still working" from "dead forever"?

Derive the need for some concept of:

```text
ownership
+
time
```

Only then introduce ideas such as:

```text
lease
expires_at
heartbeat
reclaim
```

Do not memorize "use distributed lock."

---

# Part 3 — Transaction Boundary

Easy case:

```text
idempotency record
+
business update
```

are in the **same database**.

Question:

> Can one transaction make them succeed/fail together?

Reason through it.

---

## Harder Case

Business effect is external:

```text
our DB
↓
payment provider / another service / Kafka
```

Now one local DB transaction cannot atomically encompass both systems.

Do **not** solve the whole distributed transaction problem today.

Just identify the failure window.

Example:

```text
external action succeeds
↓
service crashes
↓
local COMPLETED state never written
```

What happens when the client retries?

That is enough for Day 5.

---

# HLD Exit Criteria

You do **not** need to design a global payment platform today.

Success means you can reason about:

```text
identity
state
shared storage
atomic claim
retry behavior
worker crash
lease
transaction boundary
cross-system uncertainty
```

without reaching immediately for named distributed-systems patterns.

That is real HLD foundation.

---

# Session 6 — Production Engineering Scenario

**10 minutes**

Your idempotency table suddenly shows:

```text
PROCESSING: 47,000
COMPLETED: 2.3M
FAILED: 1,200
```

Normally `PROCESSING` is under 200.

At the same time:

```text
request traffic = normal
DB latency = normal
CPU = normal
application restart occurred 20 minutes ago
```

Diagnose.

Answer in order:

1. What changed?
2. What hypothesis does the restart suggest?
3. What might have happened to request ownership?
4. What metric would you inspect next?
5. What operational mechanism is missing if records can remain `PROCESSING` forever?

---

# End-of-Day Reflection

**10 minutes**

Answer without notes.

1. Why should HashMap tests intentionally force collisions?
2. What is the difference between logical map size and bucket capacity?
3. Why shouldn't you choose a HashMap before defining the minimum state required by a DSA problem?
4. Why is linked-list indexed lookup O(n)?
5. Why can linked structures have worse cache behavior than arrays?
6. Why doesn't a per-node HashMap provide global idempotency?
7. Why is `request_id` identity while `status` is state?
8. Why can `PROCESSING` require lease/recovery semantics?
9. Why is cross-system idempotency harder than same-database idempotency?
10. What is the difference between preventing duplicate **requests** and guaranteeing exactly-once **effects**?

---

# Deliverables

- [ ] HashMap V1 JUnit suite passes
- [ ] HashMap V1 cleanup completed
- [ ] Ransom Note reasoned + implemented
- [ ] First Unique Character reasoned + implemented
- [ ] optional third DSA transfer problem
- [ ] linked-node traversal mental model explained
- [ ] array vs linked-list memory/performance comparison explained
- [ ] request-idempotency state machine discussed
- [ ] worker-crash failure derived
- [ ] lease/reclaim motivation derived
- [ ] same-DB transaction boundary explained
- [ ] cross-system failure window identified
- [ ] production `PROCESSING` buildup scenario diagnosed

---

# Intentionally Deferred

We are **not** stuffing these into Day 5:

- HashMap resizing/treeification
- ConcurrentHashMap
- full linked-list implementation
- distributed transactions
- Kafka exactly-once semantics
- consensus
- CAP
- full URL Shortener HLD
- JMH implementation

Also still tracked, but deliberately not crammed into today:

- Dynamic Array JUnit quality gate
- Product Except Self JUnit tests
- JMH benchmark

---

# Day 5 Exact Starting Action

Start with:

> **For HashMap V1, what test would prove that collision handling works rather than merely proving that ordinary insertion works?**

Then write that collision test **before changing the implementation**.

This Day 5 is intentionally balanced: one engineering cleanup, one substantial DSA block, one new foundational data-structure concept, and **one narrow real-HLD problem**. That is enough progression without turning the day into six parallel courses.

---

# Registry Update Requirements

At the end of Day 5, update `cirriculum/registry.md` based only on work actually completed.

Record:

1. HashMap tests actually passing
2. HashMap cleanup/refactors actually performed
3. DSA problems actually solved
4. Evidence of abstraction-first reasoning
5. Linked-structure understanding demonstrated
6. Idempotency/HLD concepts actually reasoned through
7. New misconceptions/gaps
8. Pending engineering debt
9. Exact next action for Day 6

Do not mark a topic mastered merely because it was discussed once.
