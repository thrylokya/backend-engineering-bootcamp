# Current Position

* **Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory
* **Latest Curriculum Worked:** Day 9 — LRU Implementation Quality Gate & Binary-Search Boundary Reasoning
* **Day 9 Date:** 2026-08-28
* **Status:** Day 9 completed at the learning/evidence level. LRU Cache moved from design into implementation: DLL mutation helpers, `get`, `put`, eviction coordination, map/list agreement, recency behavior, and JUnit tests were worked through. Exact-match binary search was derived from safe elimination and implemented. Lower-bound / Search Insert Position was derived as a first-true boundary problem; `[0, n]` answer-space reasoning and `right = mid` candidate preservation were understood. JVM/performance reasoning connected Big-O to cache locality. Rate-limiter requirements covered identity, fixed-window vs rolling-window semantics, token-bucket distinction, and safety invariants. Invariant formulation improved significantly but remains an active precision skill.
* **Next Curriculum:** Day 10 — generate from `MASTER_CURRICULUM.md + registry.md + Day 9 evidence`
* **Primary Language:** Java
* **Target Level:** Strong Senior / Lead / Staff-level backend engineering capability
* **Primary Goal:** Production engineering excellence + top-tier interview readiness

---

# Day 9 — Completed Learning & Evidence

## 1. LRU Cache Implementation

Completed the Day 8 → Day 9 LRU implementation quality gate.

Representation:

```text
HashMap<K, Node>
+
Doubly Linked List

head = MRU
tail = LRU
```

Retrieved why:

```text
Map<K, Node>
```

is required:

> The map returns the exact DLL node in O(1), allowing recency mutation without traversing the linked list.

Retrieved why Node contains its key:

```text
tail
→ LRU Node
→ node.key
→ map.remove(node.key)
```

This allows DLL eviction and HashMap eviction to remain coordinated in O(1) expected time.

---

## 2. LRU Invariants

Primary agreement invariant:

> The HashMap and Doubly Linked List must represent exactly the same logical cache entries after every public operation.

Equivalent checks:

```text
map.size() == number of active DLL nodes
```

and:

```text
map.get(node.key) == node
```

for every active node.

Recency:

```text
head = most recently used
tail = least recently used
```

DLL structural invariants:

```text
head.prev == null
tail.next == null
```

and:

```text
A.next == B
B.prev == A
```

for adjacent nodes.

Important correction reinforced:

> LRU means least **recently** used, not least historically accessed.

---

## 3. LRU Helper Implementation

Implemented/reasoned through:

```text
removeNode(node)
addFirst(node)
moveToFront(node)
removeLast()
```

### `removeNode`

Initial implementation exposed boundary bugs:

```text
remove head
→ new head.prev was not cleared

remove tail
→ new tail.next was not cleared

single node
→ stale tail remained
```

All were identified and corrected.

Required cases:

```text
only node
head
tail
middle
```

### `moveToFront`

Initial implementation duplicated pointer manipulation.

Improved design:

```text
if already head
    no-op

removeNode(node)
addFirst(node)
```

Important engineering lesson:

> Once lower-level mutation helpers have strong contracts, higher-level operations should compose them instead of duplicating pointer logic.

### `removeLast`

Simplified to:

```text
capture tail
removeNode(tail)
return removed node
```

The DLL helper modifies the list; the cache operation coordinates the corresponding HashMap removal.

---

## 4. LRU `get`

Successful flow:

```text
map lookup
↓
obtain exact Node
↓
moveToFront(node)
↓
return value
```

Important insight:

> A successful LRU `get()` is a structural mutation because recency changes.

Potential concurrency transfer:

> Concurrent `get()` calls cannot automatically be treated as harmless readers because both may mutate the recency list.

---

## 5. LRU `put`

Three cases derived correctly.

### Existing key

```text
find existing node
↓
update value
↓
move same node to MRU
```

Do not create another node.

### New key with space

```text
create Node
↓
put into map
↓
addFirst(node)
```

### New key when full

```text
remove LRU from DLL
↓
remove same key from HashMap
↓
insert new entry
↓
new node becomes MRU
```

A correctness bug was caught during implementation:

```text
removeLast()
```

was initially performed without:

```text
map.remove(removed.key)
```

which would have created:

```text
Map contains entry
DLL does not contain entry
```

and violated the central map/list agreement invariant.

This was a strong invariant-driven debugging example.

---

## 6. LRU Tests

JUnit coverage created includes:

```text
invalid/zero capacity
cache miss
basic put/get
existing-key update
repeated update
capacity eviction
get-based recency mutation
eviction after multiple accesses
```

Recency scenario correctly demonstrated:

```text
insertion order != eviction order
```

when entries are subsequently accessed.

A dedicated capacity-1 regression test was discussed but not separately added.

LRU implementation is complete at the Day 9 learning/evidence level.

Do not re-teach LRU from scratch; use retrieval later.

---

# Binary Search

## 7. Exact Search Mental Model

Important improvement:

Binary search is not:

```text
target is probably on this side
```

It is:

> Sorted ordering proves that one part of the remaining search space cannot contain the answer.

Rules:

```text
nums[mid] < target
→ indices <= mid are impossible
→ left = mid + 1
```

```text
nums[mid] > target
→ indices >= mid are impossible
→ right = mid - 1
```

---

## 8. Binary-Search Invariant

Precise invariant:

> If the target exists and has not already been returned, its index must remain inside the current candidate interval `[left, right]`.

Initialization:

```text
left = 0
right = n - 1
```

Loop:

```text
left <= right
```

Interpretation:

```text
left <= right
→ at least one candidate remains

left > right
→ search space is empty
```

Important correction:

`left < right` would skip the valid one-element state:

```text
left == right
```

---

## 9. Safe Midpoint

Learned:

```java
mid = left + (right - left) / 2;
```

instead of:

```java
mid = (left + right) / 2;
```

because:

```text
left + right
```

can overflow an `int`.

---

## 10. Exact Binary Search Implementation

Implemented iterative binary search.

Important implementation correction:

An initial pre-loop:

```text
calculate mid
access numbers[mid]
```

was removed because it:

```text
breaks empty-array behavior
+
duplicates the loop's first comparison
```

Empty arrays are naturally handled by:

```text
left = 0
right = -1

left <= right → false
```

Complexity:

```text
n
n/2
n/4
...
1
```

Therefore:

```text
Time  = O(log n)
Space = O(1)
```

---

# Lower Bound / Search Insert Position

## 11. Definition

Return:

> The first index `i` for which `nums[i] >= target`.

If none exists:

```text
return n
```

Examples:

```text
[1,3,5,7], target 4 → 2
[1,3,5,7], target 5 → 2
[1,3,3,3,7], target 3 → 1
[1,3,5,7], target 9 → 4
[], target 5 → 0
```

---

## 12. First-True Representation

Define:

```text
nums[i] >= target ?
```

Sorted order gives:

```text
false false false true true true
                  ^
             first true
```

Lower-bound search therefore means:

> Find the boundary where a monotonic predicate changes from false to true.

This is the main transfer beyond ordinary exact-match binary search.

---

## 13. Lower-Bound Elimination

When:

```text
nums[mid] < target
```

`mid` is invalid:

```text
left = mid + 1
```

When:

```text
nums[mid] >= target
```

`mid` is already valid, but an earlier valid index may exist:

```text
right = mid
```

Important distinction:

```text
Exact binary search:
mid proven incorrect
→ discard mid

Lower bound:
mid proven valid but maybe not first
→ preserve mid
```

This distinction was explained correctly without memorization.

---

## 14. Why Search Space Includes `n`

Standard lower-bound answer range:

```text
[0, n]
```

not:

```text
[0, n - 1]
```

because:

```text
[] target 5
→ 0
```

and:

```text
[1,3,5,7], target 9
→ 4 == n
```

Therefore:

```text
left = 0
right = n
```

Although `nums[n]` is not a valid array access, `n` is a valid **answer boundary**.

Carry-forward:

> Reimplement the final canonical lower-bound method once from memory before treating the implementation as fully consolidated.

---

# Java / JVM / Performance

## 15. Big-O vs Cache Locality

Linear scan:

```text
O(n)
```

Binary search:

```text
O(log n)
```

But actual runtime also depends on memory behavior.

Sequential array scan:

```text
contiguous access
→ spatial locality
→ cache-line reuse
→ hardware prefetch friendliness
```

Binary search:

```text
jumping access pattern
→ weaker locality
```

For very small arrays, a simple sequential scan may therefore be competitive despite worse asymptotic complexity.

For large arrays, logarithmic scaling dominates.

---

## 16. Array vs Linked-List Locality

Array:

```text
contiguous data
→ nearby elements tend to arrive together
```

Linked list:

```text
separate Node objects
→ pointer chasing
→ potentially scattered memory
→ weaker locality
```

LRU's:

```text
HashMap
+
Node objects
+
prev/next references
```

buy:

```text
O(1) expected lookup
+
O(1) recency mutation
```

at the cost of additional state, pointer chasing, allocations, and potential GC pressure.

---

# Rate Limiter — Narrow LLD/HLD

## 17. Requirement Clarification

Requirement:

```text
100 requests per customer per minute
```

must first clarify:

```text
WHO?
per user?
per org/customer?
per API key?
per IP?
per endpoint?
global?
```

and:

```text
WHAT DOES “PER MINUTE” MEAN?

fixed window?
rolling 60-second window?
average replenish rate with allowed bursts?
```

Important principle:

> Define the product guarantee before choosing the algorithm.

---

## 18. Fixed Window vs Rolling Window

Fixed:

```text
10:00:00–10:00:59 → max 100
10:01:00–10:01:59 → max 100
```

may permit:

```text
100 at 10:00:59
+
100 at 10:01:00
```

Rolling window:

> At every instant, consider requests accepted during the immediately preceding 60 seconds.

Initial confusion:

```text
reset counter every 60 seconds
```

was identified as fixed-window behavior, not rolling-window behavior.

---

## 19. Rate-Limiter Safety Invariant

For:

```text
customer
rolling 60-second window
limit = 100
```

correct invariant was eventually stated:

> After every request decision, a customer must not have more than 100 accepted requests during the preceding 60 seconds.

Conceptually:

```text
acceptedRequests(customer, now - 60s, now) <= 100
```

Minimal conceptual API:

```java
boolean allow(String customerId, Instant now)
```

No distributed implementation was attempted.

---

## 20. Token Bucket Correction

Token bucket is **not** the same thing as a rolling window.

It models:

```text
request consumes token
+
tokens replenish at configured rate
```

and may intentionally permit controlled bursts.

Therefore:

```text
fixed window
rolling window
token bucket
```

represent different guarantees.

---

# Production Cache Reasoning

## 21. Working Set / Locality Scenario

Scenario:

```text
capacity = 10,000

before:
~5,000 repeatedly accessed hot keys

after:
~2,000,000 nearly uniformly accessed keys
```

Observed:

```text
hit rate ↓
evictions ↑
DB reads ↑
CPU moderate
```

Correct conclusion:

> These metrics do not prove the LRU implementation is broken.

Earlier:

```text
hot working set fits cache
→ high reuse
```

Later:

```text
working set >> capacity
+
low temporal locality
→ entries evicted before reuse
```

Important distinction:

```text
LRU correctness
→ are recency/invariants maintained?

LRU effectiveness
→ does the workload contain enough locality for LRU to help?
```

A cache can be correct and still perform poorly for the workload.

---

## 22. Workload Change vs Cache-Key Bug

Metrics expose symptoms:

```text
hit rate ↓
evictions ↑
DB reads ↑
```

but do not prove the cause.

Useful evidence:

```text
logical request
→ generated cache key
→ hit/miss
```

A cache-key bug is strongly indicated when:

```text
same logical request
→ unexpectedly different cache key
```

A workload/locality change instead looks like:

```text
same key-generation logic
+
more distinct keys / less repetition
```

Important nuance:

> Zero or near-zero hit rate is suspicious but is not by itself proof of a key-generation bug.

Logs/traces help establish cause; aggregate metrics primarily expose symptoms.

---

# Active Growth Area — Invariants

## 23. Invariant Definition

Working definition:

> An invariant is a property that must remain true while an algorithm or system is in a correct state.

Practical derivation:

```text
What bad state must never happen?
↓
Negate that bad state
↓
State what must remain true after each meaningful operation
```

Examples:

```text
LRU:
map and DLL contain exactly the same entries

Binary search:
if target exists, it remains in the current candidate region

Rolling rate limiter:
accepted requests during the previous 60 seconds <= limit
```

Important distinction:

```text
Invariant
→ what must remain true

Implementation
→ how we preserve it
```

The initial tendency is still to describe implementation steps when asked for an invariant.

Continue deliberately practicing invariant formulation across DSA, LLD, concurrency, state machines, and distributed systems.

---

# Day 9 — Gaps / Corrections

Continue reinforcing:

```text
1. State invariants as properties, not algorithms.

2. In binary search, use proof language:
   “this region is impossible”
   rather than:
   “the target is more likely on this side.”

3. Distinguish:
   exact search
   vs
   boundary / first-true search.

4. Reimplement canonical lower bound once from memory.

5. Continue DSA abstraction-first:
   objective
   → relevant information
   → discardable details
   → smallest representation
   → brute force
   → repeated work / invariant
   → optimization.

6. Prefer composition of strong helpers over duplicated mutation logic.
```

These are precision/refinement gaps, not foundational blockers.

---

# Day 9 Completion Status

```text
LRU retrieval                                ✅
Map<K, Node> rationale                       ✅
Node-key rationale                           ✅
map/list invariant                           ✅
recency invariant                            ✅

removeNode implementation                    ✅
boundary bug correction                      ✅
addFirst implementation                      ✅
moveToFront composition                      ✅
removeLast implementation                    ✅
get implementation                           ✅
put three-case derivation                    ✅
map + DLL eviction coordination              ✅
core JUnit coverage                          ✅
get-driven recency                           ✅

exact binary-search derivation               ✅
binary-search invariant                      ✅
safe midpoint                                ✅
exact binary-search implementation           ✅
O(log n) reasoning                           ✅

lower-bound definition                       ✅
first-true representation                    ✅
right = mid reasoning                        ✅
[0,n] answer-space reasoning                 ✅
canonical lower-bound rewrite from memory    ⏳

Big-O vs cache locality                      ✅
array vs linked-list locality                ✅

rate-limiter identity clarification          ✅
fixed vs rolling semantics                   ✅
rolling-window invariant                     ✅
token-bucket distinction                     ✅
distributed rate limiter                     ⏳ future scope

invariant formulation                        🟡 improving
```

---

# Exact Next Action — Day 10

Generate Day 10 from:

```text
MASTER_CURRICULUM.md
+
registry.md
+
Day 9 evidence
```

Start with a very short retrieval gate:

```text
1. State an invariant without describing an implementation.

2. Reimplement lower bound from memory:
   answer space = [0, n]

3. Explain:
   exact search:
   why can mid be discarded?

   lower bound:
   why must mid sometimes remain a candidate?
```

If those pass, immediately continue into the next Phase-1 material.

Do not re-teach the LRU implementation.

Continue:

```text
one concept at a time
reason before code
smallest sufficient representation
implementation after mental model
production connection after correctness
```
