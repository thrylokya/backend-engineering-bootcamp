# Day 9 — LRU Cache Implementation Quality Gate & Binary-Search Boundary Reasoning

> **Duration:** ~2 hr 50 min  
> **Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory  
> **Primary Engineering Theme:** Convert the Day 8 LRU design into correct, tested code while preserving map ↔ doubly-linked-list agreement.  
> **Primary DSA Theme:** Derive binary search from safe elimination, then strengthen exact boundary reasoning with a lower-bound transfer problem.  
> **LLD/HLD Scope:** Keep system-design exposure narrow; introduce only the requirements/invariants of a simple Rate Limiter. Do not expand into distributed rate limiting yet.

---

# Why Day 9 Looks Like This

Day 8 completed the conceptual work for:

- Deque
- Doubly Linked List invariants
- `IntDeque` implementation and tests
- Longest Substring Without Repeating Characters
- variable-size sliding-window reasoning
- LRU Cache requirements
- `HashMap + Doubly Linked List` derivation
- LRU node representation
- helper contracts
- `get` / `put` flows
- map/list invariants
- capacity-2 dry run
- local-cache production reasoning

The registry explicitly leaves:

```text
LRU full implementation  → Day 9
LRU implementation tests → Day 9
```

Day 9 therefore begins with an **engineering quality gate**, not another LRU lecture.

The primary invariant is:

> The HashMap and Doubly Linked List must remain two synchronized representations of exactly the same logical cache contents after every public operation.

After the LRU implementation is proven, the next Phase-1 DSA topic is **binary search**.

Binary search is particularly important now because it trains:

```text
search-space definition
+
safe elimination
+
exact boundary semantics
```

and boundary precision remains a recurring growth area.

---

# Today's Objectives

By the end of Day 9, you should be able to:

- retrieve the LRU representation/invariants without re-teaching
- implement the four LRU structural helper operations
- implement `get()` and `put()` correctly
- prove eviction and recency behavior through automated tests
- explain why a successful LRU `get()` mutates structure
- avoid redundant LRU state where it is unnecessary
- derive ordinary binary search from sorted-order elimination
- state a precise binary-search loop invariant
- implement binary search without off-by-one errors
- derive a lower-bound / search-insert style solution rather than memorizing a formula
- distinguish "find target" from "find boundary"
- explain why binary search is O(log n)
- compare asymptotic binary-search benefit with real memory/cache behavior
- derive the minimal requirements of a simple rate limiter without jumping to Redis/distributed locking

---

# Session 1 — LRU Retrieval Only

**10 minutes**

Do not inspect Day 8 notes initially.

Answer:

1. Why is the map:

```text
key → Node
```

instead of:

```text
key → value
```

2. Why does each Node retain its `key`?

3. In our chosen convention, what does:

```text
head
```

represent?

4. What does:

```text
tail
```

represent?

5. State the map/list agreement invariant.

6. Why is a successful:

```text
get(key)
```

not structurally read-only?

7. What are the four pointer helpers we derived?

Expected:

```text
removeNode(node)
addFirst(node)
moveToFront(node)
removeLast()
```

If these can be explained clearly, move directly into implementation.

Do not re-derive `HashMap + DLL`.

---

# Session 2 — LRU Structural Helper Implementation

**30 minutes**

Implement pointer mutation first.

Do not start with `get()` / `put()`.

Assume the convention:

```text
head = MRU
tail = LRU
```

and a non-sentinel implementation unless you deliberately choose otherwise.

## Helper 1 — `removeNode(node)`

Must correctly handle a node that is:

```text
only node
head
tail
middle
```

Postconditions:

- node is no longer part of the active recency list
- remaining adjacent nodes agree in both directions
- `head.prev == null` when non-empty
- `tail.next == null` when non-empty
- empty-list boundaries are correct

Optional cleanup:

```text
node.prev = null
node.next = null
```

may make detached state easier to reason about.

## Helper 2 — `addFirst(node)`

Postcondition:

```text
head == node
node.prev == null
```

If empty:

```text
head == tail == node
```

Otherwise:

```text
node.next == oldHead
oldHead.prev == node
```

## Helper 3 — `moveToFront(node)`

Derive:

```text
removeNode(node)
↓
addFirst(node)
```

If `node` is already the head, choose either a no-op or a correct remove/add flow. Keep the contract consistent.

## Helper 4 — `removeLast()`

Purpose:

```text
remove current LRU
```

Return the removed node so `put()` can also do:

```text
map.remove(removed.key)
```

Prefer responsibility separation:

```text
list helper → changes list
cache operation → coordinates map + list
```

Before public operations, manually dry-run:

```text
single node
two nodes
remove head
remove tail
remove middle
move middle to front
move tail to front
```

---

# Session 3 — LRU `get` / `put` + Automated Quality Gate

**40 minutes**

Now compose public behavior.

Example state:

```text
Map<Integer, Node> map
Node head
Node tail
int capacity
```

Question:

> Do you actually need a separate mutable `size` field?

If `map.size()` already represents logical size, another size field is redundant state. Choose intentionally.

## `get(key)`

```text
lookup
↓
missing → return miss
↓
existing → move exact node to MRU
↓
return value
```

Target:

```text
O(1) expected
```

## `put(existingKey, value)`

```text
lookup existing node
↓
update value
↓
move to MRU
```

Size must not increase.

## `put(newKey, value)` With Space

```text
create node
↓
map.put(key, node)
↓
addFirst(node)
```

## `put(newKey, value)` When Full

```text
removeLast()
↓
obtain LRU node
↓
map.remove(lru.key)
↓
create/insert new node
↓
add new node as MRU
```

The operation must never finish with map/list disagreement.

---

# Required LRU Tests

- basic put/get
- existing-key update
- access changes recency
- eviction
- evicted-key miss
- capacity = 1
- repeated get
- repeated put on same key
- head/tail/middle movement
- explicit capacity contract (`capacity <= 0`)

Critical test:

```text
put(1,10)
put(2,20)
get(1)
put(3,30)
```

Expected:

```text
2 evicted
1 retained
3 retained
```

For capacity 1:

```text
put(1,10)
put(2,20)

get(1) → miss
get(2) → 20
```

LRU is complete only when:

- implementation compiles
- tests pass
- eviction is correct
- access updates recency
- same-key update does not duplicate state
- capacity-one works
- map and list never logically disagree after a public operation

At this point the LRU engineering lab may be marked complete.

---

# Session 4 — Binary Search From First Principles

**25 minutes**

Problem:

> Given a sorted array and a target, determine whether the target exists.

Do not start by writing `mid`.

## Brute Force

Scan left to right:

```text
O(n)
```

## What Does Sorted Order Buy Us?

If:

```text
nums[mid] < target
```

then all indices:

```text
<= mid
```

can be discarded.

If:

```text
nums[mid] > target
```

then all indices:

```text
>= mid
```

can be discarded.

Core mechanism:

> One comparison proves that part of the remaining candidate region cannot contain the answer.

## Search-Space Invariant

Use closed interval:

```text
[left, right]
```

Invariant:

> If the target exists and has not already been returned, it must lie inside `[left, right]`.

Initial:

```text
left = 0
right = n - 1
```

Loop:

```text
left <= right
```

Midpoint:

```text
mid = left + (right - left) / 2
```

Updates:

```text
nums[mid] < target → left = mid + 1
nums[mid] > target → right = mid - 1
```

Be able to explain why `mid` itself is removed from the search space.

## Dry Runs

```text
[1,3,5,7,9], target = 7
[1,3,5,7,9], target = 1
[1,3,5,7,9], target = 9
[1,3,5,7,9], target = 4
[], target = 4
[5], target = 5
[5], target = 3
```

## Complexity

```text
n
n/2
n/4
n/8
...
1
```

Therefore:

```text
Time: O(log n)
Space: O(1) iterative
```

---

# Session 5 — DSA Transfer: First Position Where `value >= target`

**30 minutes**

Problem:

> Given a sorted integer array and a target, return the first index whose value is greater than or equal to the target. If none exists, return `n`.

Examples:

```text
[1,3,5,7], target = 4 → 2
[1,3,5,7], target = 5 → 2
[1,3,5,7], target = 0 → 0
[1,3,5,7], target = 9 → 4
[1,3,3,3,7], target = 3 → 1
```

This is not ordinary exact-match search.

## Define the Answer

We want the first index where:

```text
nums[i] >= target
```

Think of a monotonic predicate:

```text
false false false true true true
```

The task is to find the first `true`.

## Brute Force

Scan until predicate becomes true:

```text
O(n)
```

## Safe Elimination

If:

```text
nums[mid] < target
```

then `mid` and everything left cannot be the answer.

If:

```text
nums[mid] >= target
```

then `mid` may be the answer, but an earlier valid index may exist.

Therefore equality is not enough to return immediately.

## Required Explanation

Be able to state:

- what `left` means
- what `right` means
- which region is known invalid
- which region may still contain the first valid index
- why the loop terminates
- why the returned boundary is correct

## Edge Cases

```text
empty array
target smaller than all
target larger than all
target equals first
target equals last
duplicates around answer
single element
```

## Transfer Questions

1. How is this different from ordinary binary search?
2. Why do duplicates make immediate return on equality incorrect?
3. What property of the predicate makes binary search possible?
4. What other problems could look like:

```text
false ... false true ... true
```

Do not expand into binary-search-on-answer yet.

---

# Session 6 — Java / JVM / Performance Connection

**15 minutes**

Compare:

```text
linear scan over sorted int[]
```

with:

```text
binary search over sorted int[]
```

Big-O:

```text
linear → O(n)
binary → O(log n)
```

But real runtime also depends on memory behavior.

Linear scan is sequential and cache-friendly.

Binary search jumps around the array.

For tiny arrays, constants and locality may matter.

Correct conclusion:

> Big-O describes growth; cache behavior and constant factors affect actual runtime.

Also connect back to LRU:

```text
HashMap state
+
Node objects
+
prev/next references
```

The extra state exists because it buys:

```text
O(1) expected lookup
+
O(1) recency mutation
```

---

# Session 7 — Narrow LLD/HLD: Simple Rate Limiter Requirements

**15 minutes**

Do not implement a production rate limiter today.

Scenario:

> Allow at most 100 requests per customer per minute.

Before choosing Redis, token bucket, sliding window, or locks, clarify the requirement.

## Entities

```text
client/customer identity
request
time
limit
```

## Bad Outcome

A client exceeds the intended rate and all requests are accepted.

## Safety Invariant

> Accepted requests for a client must not exceed the configured allowance under the chosen time semantics.

But "per minute" is ambiguous.

Clarify:

```text
fixed wall-clock minute?
rolling 60-second window?
average rate with bursts?
```

Different semantics imply different algorithms.

Minimal API:

```text
allow(clientId, now) → true / false
```

Day 9 scope:

- identity
- limit
- time semantics
- burst behavior
- API contract
- one or two invariants

No distributed implementation yet.

---

# Session 8 — Production Scenario + Retrieval

**10 minutes**

Cache capacity:

```text
10,000
```

Traffic changes from repeatedly accessing:

```text
5,000 hot keys
```

to nearly uniform access across:

```text
2,000,000 keys
```

Observed:

```text
hit rate ↓
eviction rate ↑
DB reads ↑
CPU moderate
```

Answer:

1. Is the LRU implementation necessarily broken?
2. What changed about the working set/locality?
3. Why can eviction spike?
4. Why might increasing capacity help only partially?
5. What evidence distinguishes workload change from a cache-key bug?

Final retrieval:

1. Why `Map<K, Node>`?
2. Why does Node contain key?
3. State the map/list agreement invariant.
4. Why is successful LRU `get()` a structural mutation?
5. Why is binary search O(log n)?
6. State the exact-match search-space invariant.
7. Why `left = mid + 1` rather than `left = mid`?
8. Why can't lower-bound search immediately return on equality?
9. What does `false ... false true ... true` represent?
10. Why must Rate Limiter time semantics be clarified before choosing an algorithm?

---

# Deliverables

## LRU Engineering

- [ ] retrieval drill completed
- [ ] `removeNode(node)` implemented
- [ ] `addFirst(node)` implemented
- [ ] `moveToFront(node)` implemented
- [ ] `removeLast()` implemented
- [ ] `get(key)` implemented
- [ ] `put(key, value)` implemented
- [ ] capacity contract defined
- [ ] basic put/get test passes
- [ ] same-key update test passes
- [ ] recency-change test passes
- [ ] eviction test passes
- [ ] evicted-key miss test passes
- [ ] capacity-one test passes
- [ ] repeated-get test passes
- [ ] repeated-put test passes
- [ ] head/tail/middle movement tested
- [ ] map/list agreement preserved

## Binary Search

- [ ] ordinary membership search derived
- [ ] invariant stated
- [ ] iterative binary search implemented
- [ ] boundary cases tested
- [ ] O(log n) derived from halving
- [ ] off-by-one updates explained

## Boundary Transfer

- [ ] first `value >= target` problem brute-forced first
- [ ] monotonic predicate identified
- [ ] boundary solution derived
- [ ] duplicates handled
- [ ] empty/before-all/after-all tested
- [ ] distinction from exact-match search explained

## Java/JVM

- [ ] linear vs binary complexity/runtime distinction explained
- [ ] locality distinction explained
- [ ] LRU memory trade-off explained

## LLD/HLD

- [ ] Rate Limiter entities identified
- [ ] "100 requests/minute" ambiguity recognized
- [ ] time semantics clarified
- [ ] `allow(clientId, now)` contract described
- [ ] safety invariant stated
- [ ] no premature distributed mechanism selected

---

# End-of-Day Exit Criteria

## LRU Cache

You can now move through:

```text
requirements
→ representation
→ invariants
→ helpers
→ implementation
→ tests
```

LRU may be considered complete at the **Phase-1 engineering-lab level**, not at production-concurrent/distributed level.

## Binary Search

You can explain:

```text
ordered search space
↓
comparison
↓
prove one region impossible
↓
discard permanently
```

rather than merely "take mid."

## Boundary Precision

You can distinguish:

```text
find exact value
```

from:

```text
find first boundary satisfying a monotonic predicate
```

and explain why updates differ.

## Abstraction-First DSA

Before coding:

```text
objective
search space
relevant information
safe elimination
enabling property
invariant
minimum state
```

comes before pattern naming.

## LLD

For Rate Limiter, you can state:

```text
what is limited
who is limited
over what time semantics
what burst behavior is allowed
what invariant is protected
```

before mentioning a technology.

---

# Intentionally Deferred

Do not add:

- thread-safe LRU
- locks / `ConcurrentHashMap`
- Java Memory Model
- distributed cache
- LFU
- TTL cache implementation
- full Rate Limiter implementation
- distributed Rate Limiter
- token bucket deep dive
- heap / priority queue
- trees
- binary search on answer
- rotated-array binary search
- Kafka/SQS internals

Older engineering debt remains tracked separately:

- Dynamic Array quality-gate tests if still incomplete
- Product Except Self JUnit tests if still incomplete
- JMH benchmark implementation

---

# Day 9 Exact Starting Action

Start with:

> **Why does the LRU HashMap store `key → Node`, and what must be true between the HashMap and the doubly linked list after every completed `get()` or `put()` operation?**

Then immediately implement:

```text
removeNode
addFirst
moveToFront
removeLast
```

Do not re-teach the LRU architecture.

---

# Registry Update Requirements

At the end of Day 9, update `cirriculum/registry.md` using demonstrated evidence only.

Capture:

1. LRU helpers actually implemented
2. `get` / `put` implementation status
3. LRU tests actually passing
4. pointer or map/list disagreement bugs discovered
5. whether the Phase-1 LRU quality gate is complete
6. binary-search invariant understanding
7. exact-match binary-search implementation/tests
8. lower-bound reasoning evidence
9. boundary/off-by-one mistakes observed
10. Java/JVM performance distinctions demonstrated
11. Rate Limiter requirements/invariants actually derived
12. remaining engineering debt
13. exact Day 10 starting action

Do not mark a concept mastered because it was merely discussed once.
