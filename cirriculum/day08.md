# Day 8 — Deque, LRU Cache LLD & Constraint-Driven Sliding Window

> **Duration:** ~2 hr 45 min  
> **Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory  
> **Primary Theme:** Move from single-ended Stack/Queue behavior to double-ended access, then derive the composite design behind an O(1) LRU Cache.  
> **DSA Theme:** Transfer sliding-window reasoning to a new constraint rather than repeating the positive-sum problem.  
> **HLD Scope:** Narrow cache/production connection only. No distributed-cache deep dive yet.

---

# Why Day 8 Looks Like This

Day 7 established:

- Stack as a LIFO behavioral contract
- Queue as a FIFO behavioral contract
- linked implementations of Stack and Queue
- Stack/Queue invariant-driven testing
- Valid Parentheses from unresolved-state/LIFO reasoning
- stronger sliding-window complexity reasoning
- `ArrayDeque` vs linked-memory/runtime trade-offs
- durable acceptance vs in-memory queue semantics
- production backlog diagnosis
- leases, fencing, idempotency, and reconciliation in the Notification Service discussion

Two tiny Day 7 implementation cleanups remain:

1. verify `IntQueue` clears `tail` when the final element is dequeued
2. verify the standard Valid Parentheses solution models only `()[]{}`

These are **closure checks**, not teaching sessions.

The next Phase-1 progression is:

```text
Stack / Queue
     ↓
Deque
     ↓
Doubly Linked List mechanics
     ↓
LRU Cache
```

The LRU Cache is important because it is the first structure in this bootcamp where one data structure is intentionally insufficient.

The goal is to derive:

```text
requirement
↓
required operations
↓
required complexity
↓
what each candidate structure can/cannot provide
↓
minimum composite representation
```

Do not memorize:

```text
LRU = HashMap + DoublyLinkedList
```

Derive why that combination is necessary.

---

# Today's Objectives

By the end of Day 8, you should be able to:

- close the two remaining Day 7 implementation checks quickly
- explain Deque as an access contract before choosing an implementation
- derive why efficient operations at both linked-list ends benefit from `prev` and `next`
- implement a small `IntDeque`
- test Deque boundary transitions thoroughly
- explain doubly linked-list invariants
- solve a fresh variable-size sliding-window problem without being told the pattern
- derive the LRU Cache API and O(1) complexity target
- prove why a HashMap alone is insufficient for LRU
- prove why a linked list alone is insufficient for LRU
- derive `HashMap + Doubly Linked List` as the smallest useful composite representation
- define LRU Cache invariants before implementation
- understand why LRU is a good early LLD problem
- connect local LRU caching to production hit rate, stale data, capacity, and source-of-truth boundaries

---

# Session 1 — Very Short Day 7 Closure

**10 minutes maximum**

Do not spend teaching time here.

## Check 1 — Queue Final-Element Transition

Verify committed `IntQueue` behavior:

```text
before:

head
 ↓
 A
 ↑
tail

size = 1
```

After:

```text
dequeue()
```

must become:

```text
head = null
tail = null
size = 0
```

Then verify:

```text
enqueue(B)
peek()    → B
dequeue() → B
isEmpty() → true
```

If this works, Queue is closed.

## Check 2 — Valid Parentheses Cleanup

For the standard problem, relevant characters are only:

```text
( )
[ ]
{ }
```

Verify the final implementation does not introduce unnecessary parsing state such as:

```text
quotes
angle brackets
arbitrary syntax rules
```

The important lesson remains:

> Do not expand the input model unless the problem statement requires it.

If both checks pass, move on immediately.

---

# Session 2 — Retrieval Drill: Choose From Requirements, Not Story

**10 minutes**

For each scenario, do **not** name the structure first.

Answer:

```text
1. What operations are required?
2. From which end/location?
3. What ordering guarantee is required?
4. What complexity is required?
5. What minimum state would support those operations?
```

## A

Add and remove only the most recently added element.

## B

Add at one end and remove the oldest element from the other.

## C

Add and remove efficiently from **both ends**.

## D

Find a key quickly **and** remove the least recently used item quickly.

The fourth question intentionally leads toward today's LRU design.

Do not jump to the final structure until the requirements force it.

---

# Session 3 — Core Concept: Derive Deque

**20 minutes**

Deque means:

```text
Double-Ended Queue
```

But do not memorize the name as the concept.

Derive the required behavior:

```text
addFirst
addLast
removeFirst
removeLast
peekFirst
peekLast
```

Target:

```text
all end operations → O(1)
```

## Why a Singly Linked List Becomes Awkward

With:

```text
head
tail
```

a singly linked list can do:

```text
addFirst    O(1)
addLast     O(1)
removeFirst O(1)
```

But consider:

```text
removeLast
```

Even with `tail`, what do we need?

We need the predecessor of `tail`.

In a singly linked list:

```text
head → ... → predecessor → tail
```

There is no backward link.

Therefore finding the predecessor requires traversal:

```text
O(n)
```

## What Information Is Missing?

If every node also knows:

```text
prev
```

then the tail can directly identify its predecessor.

This motivates a doubly linked node:

```text
Node
├── value
├── prev
└── next
```

Do not treat `prev` as free convenience.

It improves operations but adds another invariant that every mutation must preserve.

---

# Session 4 — Doubly Linked List Invariants + `IntDeque`

**30 minutes**

Implement only the operations needed for a Deque.

## State

```text
head
tail
size
```

Node:

```text
value
prev
next
```

## Empty Invariant

```text
size == 0
head == null
tail == null
```

## Single-Node Invariant

```text
size == 1
head == tail
head.prev == null
tail.next == null
```

## Non-Empty Boundary Invariants

```text
head.prev == null
tail.next == null
```

## Bidirectional-Link Invariant

For adjacent nodes:

```text
A <-> B
```

both relationships must agree:

```text
A.next == B
B.prev == A
```

This is the central new correctness burden.

## Required API

```java
addFirst(int value)
addLast(int value)
removeFirst()
removeLast()
peekFirst()
peekLast()
size()
isEmpty()
```

## Mutation Discipline

For every method, draw:

```text
before
↓
references being changed
↓
after
```

Do not code pointer mutations from memory.

## Required Tests

- empty size/isEmpty
- removeFirst on empty
- removeLast on empty
- peekFirst on empty
- peekLast on empty
- addFirst single element
- addLast single element
- remove first from single element
- remove last from single element
- mixed addFirst/addLast
- mixed removeFirst/removeLast
- transition back to empty
- add again after empty
- size transitions

Transition test:

```text
addLast(1)
addLast(2)
removeFirst() → 1
removeLast()  → 2
isEmpty()     → true
addFirst(3)
peekFirst()   → 3
peekLast()    → 3
```

---

# Session 5 — Fresh DSA Transfer: Longest Substring Without Repeating Characters

**30 minutes**

Do not announce the pattern.

Problem:

> Given a string, return the length of the longest contiguous substring containing no repeated characters.

Before coding:

```text
1. Objective
2. Relevant information
3. Discardable information
4. Brute-force search space
5. Repeated work
6. What condition makes a candidate window valid?
7. When validity breaks, what can be safely discarded?
8. Minimum state
```

## Start With Brute Force

For every start position:

```text
extend right
until duplicate appears
```

Discuss the repeated work across neighboring starts.

Do not optimize yet.

## Key Validity Constraint

A current candidate region is valid when:

```text
all characters in the region are unique
```

When the next character creates a duplicate, ask:

> Do we need to restart from scratch?

No.

The right boundary can continue moving forward.

Advance the left boundary until the current region becomes valid again.

## Minimum State

First derive a simple version using:

```text
left
right
set of characters currently in the window
bestLength
```

Do not jump immediately to a `Map<Character, Integer>` last-seen optimization.

The Set version is enough to establish the invariant.

## Core Invariant

> The current window contains no duplicate characters, and the Set contains exactly the characters in that window.

When duplicate `c` appears:

```text
while c already exists in current window
    remove s[left]
    left++
```

Then include `c`.

## Why This Is Still O(n)

```text
right moves forward at most n times
left moves forward at most n times
```

Each character:

```text
enters the window at most once
leaves the window at most once
```

Therefore:

```text
Time: O(n) average
Space: O(k)
```

where `k` is the number of distinct characters that may exist in the window.

## Test Cases

```text
"abcabcbb" → 3
"bbbbb"    → 1
"pwwkew"   → 3
""         → 0
"a"        → 1
"abba"     → 2
```

## Transfer Question

Minimum Size Subarray Sum relied on numeric positivity.

This problem does not.

So what enables sliding-window behavior here?

Target insight:

> There is a window-validity condition that can be restored by monotonically moving the left boundary forward; neither boundary ever needs to move backward.

---

# Session 6 — LRU Cache LLD: Derive the Requirements

**40 minutes**

This is today's main design exercise.

Do not code immediately.

## Requirement

Design a fixed-capacity cache supporting:

```text
get(key)
put(key, value)
```

Behavior:

- `get(existingKey)` returns the value
- accessing a key makes it **most recently used**
- `put(existingKey, newValue)` updates the value and makes it most recently used
- `put(newKey, value)` inserts the key
- if capacity is exceeded, evict the **least recently used** key

Target complexity:

```text
get → O(1) expected
put → O(1) expected
```

## Step 1 — Two Independent Requirements

### Fast Lookup

Given:

```text
key
```

find its cache entry quickly.

Target:

```text
O(1) expected
```

### Recency Ordering

We must know:

```text
most recently used
...
least recently used
```

and efficiently:

- move an arbitrary accessed item to MRU
- remove LRU
- insert new MRU

Target:

```text
O(1)
```

## Step 2 — Why HashMap Alone Is Insufficient

HashMap gives fast lookup.

But it does not naturally give:

```text
least recently used item
```

or exact recency order with O(1) update.

## Step 3 — Why Linked List Alone Is Insufficient

A recency list can support:

```text
insert MRU
remove LRU
move known node
```

in O(1).

But:

```text
get(K)
```

requires finding K.

Without another index:

```text
scan → O(n)
```

## Step 4 — Derive the Composite Representation

```text
HashMap
+
Doubly Linked List
```

Conceptually:

```text
Map:
key → Node

List:
MRU <-> ... <-> LRU
```

Why map to a **Node**, not merely a value?

Because after lookup we need to move the exact list node in O(1).

## Step 5 — Node State

Minimum useful node:

```text
key
value
prev
next
```

Why retain `key` in the node?

When evicting the LRU node:

```text
map.remove(node.key)
```

The list gives us the node; the key lets us remove the corresponding map entry.

## Step 6 — Choose One Recency Convention

Recommended:

```text
head → MRU
tail → LRU
```

Then:

```text
access node → move to head
insert node → add to head
evict       → remove tail
```

Do not switch conventions mid-implementation.

---

# LRU Core Invariants

## Capacity

```text
0 <= size <= capacity
```

## Map/List Agreement

Every logical cache entry exists:

```text
exactly once in map
and
exactly once in list
```

Therefore:

```text
map.size()
=
number of list nodes
```

## Map Node Identity

For every cached key `k`:

```text
map.get(k)
```

points to the exact list node representing `k`.

## Recency

```text
head = most recently used
tail = least recently used
```

## Boundaries

```text
head.prev == null
tail.next == null
```

for a non-empty non-sentinel implementation.

---

# LRU Helper Operations to Design

Before public `get()` / `put()`:

```text
removeNode(node)
addFirst(node)
moveToFront(node)
removeLast()
```

Each should be O(1).

## `get(key)`

```text
find node in map
↓
missing? return miss
↓
move node to MRU
↓
return value
```

## `put(existingKey, value)`

```text
find node
↓
update value
↓
move node to MRU
```

Size unchanged.

## `put(newKey, value)` With Space

```text
create node
↓
map insert
↓
add as MRU
```

## `put(newKey, value)` When Full

```text
identify LRU
↓
remove LRU from list
↓
remove LRU key from map
↓
insert new node
↓
mark new node MRU
```

The operation must never finish with map/list disagreement.

---

# Day 8 LRU Scope

Required today:

- requirements
- API
- complexity target
- composite representation
- node fields
- helper contracts
- invariants
- dry run

Optional if time remains:

- class skeleton
- helper-method implementation

Do **not** rush full `get/put` implementation merely to say LRU was completed.

Full implementation + tests can become the Day 9 engineering quality gate.

---

# Required LRU Dry Run

Capacity:

```text
2
```

Operations:

```text
put(1, 10)
put(2, 20)
get(1)
put(3, 30)
```

Track after every operation:

```text
map keys
MRU
LRU
list order
size
```

Then:

```text
get(2)
get(3)
```

Explain the results.

---

# Session 7 — Java/JVM Connection: Why LRU Wants a Linked Node

**15 minutes**

`ArrayDeque` is excellent when operations occur at the ends.

But LRU requires:

```text
access arbitrary key K
↓
locate exact entry
↓
move K from its current position to MRU
```

With:

```text
key → exact Node
```

a doubly linked list can detach that node using:

```text
node.prev
node.next
```

without traversing from the head.

This is why the linked structure is valuable for this access pattern.

## Memory Trade-Off

The design buys O(1) recency updates with more memory:

```text
HashMap state
+
Node object
+
key/value
+
prev
+
next
```

Key principle:

> Better operation complexity often requires additional state and stronger invariants.

Do not claim linked structures are generally faster than arrays.

---

# Session 8 — Narrow HLD / Production: Local LRU Cache

**15 minutes**

Scenario:

```text
request
↓
local LRU cache
├── hit  → cached value
└── miss → database → cache → return
```

## Source of Truth

If the process disappears:

> Is the product data lost?

No, if the database is authoritative.

Therefore:

```text
cache = optimization
```

not:

```text
system of record
```

## Why Capacity?

Without a bound:

```text
new keys
→ memory growth
```

LRU decides what to discard when capacity is full.

## LRU Assumption

LRU assumes:

> Recently used data is more likely to be used again than data that has been idle longer.

That is a workload assumption, not a universal truth.

## Stale Data

If the database changes while the cache still contains the old value:

```text
cache value != source-of-truth value
```

Do not solve all invalidation today.

Just identify the freshness trade-off.

Future mechanisms may include TTL/invalidation/versioning, but they are deferred.

---

# Production Scenario — Cache Hit Rate Collapse

Before:

```text
cache hit rate: 92%
DB reads:        4k/sec
p95 latency:     35 ms
```

After:

```text
cache hit rate: 28%
DB reads:        31k/sec
p95 latency:     180 ms
CPU:             moderate
cache capacity:  unchanged
```

Diagnose in this order:

```text
Observation
↓
behavioral condition
↓
likely causes
↓
discriminating evidence
↓
possible mitigation
```

Questions:

1. What changed?
2. Why did DB traffic rise?
3. Does moderate CPU rule out cache pressure?
4. Could the working set now exceed cache capacity?
5. Could access locality have changed?
6. What metrics distinguish capacity pressure from a bug?
7. Why is increasing cache size a hypothesis to test, not an automatic fix?

---

# Deliverables

## Day 7 Closure

- [ ] `IntQueue` final-element dequeue clears both logical ends
- [ ] Queue works after drain + re-enqueue
- [ ] Valid Parentheses models only required bracket syntax

## Deque

- [ ] Deque behavioral contract derived
- [ ] need for doubly linked nodes derived
- [ ] `IntDeque` implemented
- [ ] empty/single/multi-node invariants documented
- [ ] JUnit tests pass
- [ ] mixed-end transition tests pass

## DSA

- [ ] Longest Substring brute force derived
- [ ] smallest sufficient state identified before data structure
- [ ] sliding-window invariant stated
- [ ] Set-based O(n) solution implemented
- [ ] edge cases tested
- [ ] O(n) derived from total boundary movement
- [ ] difference from positive-sum sliding window explained

## LRU LLD

- [ ] requirements clarified
- [ ] `get` / `put` API defined
- [ ] O(1) expected target stated
- [ ] HashMap-only failure explained
- [ ] linked-list-only failure explained
- [ ] composite representation derived
- [ ] Node fields justified
- [ ] map/list invariants stated
- [ ] helper operations designed
- [ ] capacity-2 dry run completed

### Optional

- [ ] LRU class skeleton created
- [ ] linked-list helper methods implemented

## Java/JVM

- [ ] explain arbitrary known-node removal
- [ ] explain extra memory cost of composite LRU
- [ ] distinguish asymptotic benefit from locality benefit

## HLD / Production

- [ ] cache identified as optimization, not source of truth
- [ ] bounded-capacity rationale explained
- [ ] LRU workload assumption explained
- [ ] stale-data problem identified
- [ ] hit-rate-collapse scenario diagnosed

---

# End-of-Day Reflection

Answer without notes.

1. What makes a Deque different from a Queue?
2. Why does a singly linked list struggle with O(1) `removeLast()`?
3. What invariant must hold between `A.next` and `B.prev`?
4. What extra correctness burden does `prev` introduce?
5. What property enables Longest Substring's window to move only forward?
6. Why is its nested-looking loop still O(n)?
7. Why is this sliding-window justification different from Minimum Size Subarray Sum?
8. Why can't a HashMap alone implement O(1) LRU behavior?
9. Why can't a linked list alone meet the lookup target?
10. Why should the map store `key → Node`?
11. Why does an LRU node need its key?
12. State the map/list agreement invariant.
13. What does `head` represent in the chosen LRU convention?
14. What does `tail` represent?
15. Why is an LRU cache normally not the source of truth?
16. What does falling hit rate do to dependency load?
17. Why is LRU a workload assumption rather than a universal best policy?

---

# End-of-Day Exit Criteria

## Deque

You can derive:

```text
operations at both ends
↓
singly linked limitation
↓
need for backward relationship
↓
doubly linked representation
```

without memorization.

The `IntDeque` tests must prove:

```text
empty
↔
single element
↔
multiple elements
```

## DSA

You solve Longest Substring using:

```text
objective
↓
validity constraint
↓
brute force
↓
repeated work
↓
restore validity by moving left
↓
minimum state
↓
implementation
```

rather than simply naming sliding window.

## LLD

You can derive:

```text
fast key lookup
+
fast recency mutation
=
HashMap + Doubly Linked List
```

and justify every field.

Do **not** mark LRU mastered yet.

Day 8 establishes design and invariants.

## Production

You can explain:

```text
cache hit
cache miss
capacity
eviction
source of truth
staleness
working set
```

at a practical backend-engineering level without expanding into distributed-cache internals.

---

# Intentionally Deferred

Do **not** add these to Day 8:

- full LRU implementation if design is not stable
- thread-safe LRU
- `ConcurrentHashMap`
- Java Memory Model
- locks
- distributed cache design
- Redis internals
- LFU / ARC
- heap / priority queue
- monotonic queue
- Kafka/SQS internals
- full cache invalidation architecture
- full Notification Service redesign

Older engineering debt remains tracked separately:

- Dynamic Array quality-gate tests if still incomplete
- Product Except Self JUnit tests if still incomplete
- JMH benchmark implementation

Do not dump all old debt into Day 8.

---

# Day 8 Exact Starting Action

Start with:

> **When a linked Queue containing exactly one node dequeues that node, which fields must change for the empty invariant to become true again?**

Then:

> **For standard Valid Parentheses, which input symbols actually affect the answer, and which extra parsing concepts should be discarded?**

After those two checks, move immediately into the Deque requirements drill.

---

# Registry Update Requirements

At the end of Day 8, update `cirriculum/registry.md` using demonstrated evidence only.

Capture:

1. Day 7 closure verification
2. Deque contract understanding
3. `IntDeque` methods actually implemented
4. Deque tests actually passing
5. doubly linked-list invariant mistakes discovered
6. Longest Substring reasoning and implementation evidence
7. evidence of abstraction-first behavior
8. LRU requirements derived
9. LRU representation/invariants derived
10. LRU code actually written, if any
11. cache/HLD production insights demonstrated
12. new misconceptions/gaps
13. exact Day 9 starting action

Do not mark LRU complete because the architecture was discussed once.
