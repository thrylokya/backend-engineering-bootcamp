# Current Position

* **Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory
* **Latest Curriculum Worked:** Day 8 — Deque, LRU Cache LLD & Constraint-Driven Sliding Window
* **Day 8 Date:** 2026-08-25
* **Status:** Day 8 completed at the learning/evidence level. Deque behavior was derived from requirements, doubly linked-list mechanics and invariants were implemented and tested, and Longest Substring Without Repeating Characters was derived and implemented using a Set-based sliding window. LRU Cache was derived from independent O(1) lookup and recency-mutation requirements into a `HashMap + Doubly Linked List` composite representation. LRU invariants, helper contracts, `get`/`put` behavior, eviction flow, and a capacity-2 dry run were completed. Production cache reasoning covered hit rate, cold-cache behavior, working-set pressure, cache-key cardinality, capacity decisions, source-of-truth boundaries, LRU policy limitations, and the distinction between eviction and freshness. Full LRU implementation and tests remain intentionally deferred.
* **Next Curriculum:** Day 9
* **Primary Language:** Java
* **Target Level:** Strong Senior / Lead / Staff-level backend engineering capability
* **Primary Goal:** Production engineering excellence + top-tier interview readiness

---

# Day 8 — Completed Learning & Evidence

## 1. Day 7 Closure

### Queue Final-Element Transition

Reinforced the Queue empty-state invariant:

```text
size == 0
head == null
tail == null
```

For the final element:

```text
head
 ↓
 A
 ↑
tail
```

`dequeue()` must leave:

```text
head = null
tail = null
size = 0
```

Important correction:

> Normal dequeue moves `head`; `tail` is explicitly cleared only when removing the final node.

Existing Queue tests cover FIFO behavior, empty exceptions, size transitions, full drain, and enqueue-after-drain behavior.

### Valid Parentheses

Reinforced the smallest sufficient representation:

> The stack contains exactly the unmatched opening brackets, with the most recent unmatched opener on top.

Only the standard bracket symbols affect the problem:

```text
()
[]
{}
```

Important abstraction lesson:

> Do not model extra parsing concepts or retain input history that does not affect the answer.

---

## 2. Deque — Behavioral Contract

Derived Deque from required operations:

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

Important distinction:

```text
Deque
→ behavioral contract / ADT

Doubly Linked List
→ one possible implementation
```

Deque and Doubly Linked List are not synonyms.

---

## 3. Why Singly Linked List Is Insufficient

With both:

```text
head
tail
```

a singly linked list supports:

```text
addFirst    → O(1)
addLast     → O(1)
removeFirst → O(1)
removeLast  → O(n)
```

`removeLast()` remains `O(n)` because `tail` identifies the final node but does not identify its predecessor.

Example:

```text
A → B → C → D
            ↑
           tail
```

Removing `D` requires reaching `C`.

This requirement motivated adding:

```text
prev
```

to each node.

---

## 4. Doubly Linked List Invariants

Node representation:

```text
value
prev
next
```

Core invariants:

```text
size == 0
→ head == null
→ tail == null
```

```text
size == 1
→ head == tail
```

For non-empty structures:

```text
head.prev == null
tail.next == null
```

For adjacent nodes:

```text
A.next == B
B.prev == A
```

Key engineering lesson:

> Additional state provides faster operations but introduces additional consistency obligations.

---

## 5. `IntDeque` Implementation

Implemented:

```java
addFirst(int value)
addLast(int value)
removeFirst()
removeLast()
peekFirst()
peekLast()
getSize()
isEmpty()
```

Correctly handled:

```text
empty → single
single → multiple
multiple → single
single → empty
empty → reusable again
```

Important singleton-removal behavior:

```text
head = null
tail = null
size = 0
```

---

## 6. `IntDeque` Testing Evidence

JUnit tests cover:

* `addFirst` on empty, single, and multiple elements
* `addLast` on empty, single, and multiple elements
* `removeFirst` on empty, single, and multiple elements
* `removeLast` on empty, single, and multiple elements
* empty and non-empty `peekFirst`
* empty and non-empty `peekLast`
* size transitions
* `isEmpty`
* full drain
* reuse after drain

A test-modeling mistake was discovered and corrected.

Example:

```text
addFirst(5)
addFirst(6)
addFirst(7)
```

produces:

```text
7 <-> 6 <-> 5
```

Important testing lesson:

> Derive or draw pointer state before asserting expected linked-list order.

Deque is complete at the Day 8 learning/evidence level.

---

## 7. Longest Substring Without Repeating Characters

Objective:

> Find the maximum length contiguous substring containing no duplicate characters.

Brute force was derived before optimization.

Repeated work identified:

> Neighboring start positions repeatedly rebuild membership information for heavily overlapping substrings.

Smallest sufficient state for the first optimized solution:

```text
left
right
HashSet<Character>
bestLength
```

Global character frequencies are unnecessary.

---

## 8. Sliding-Window Invariant

Core invariant:

> The current window contains no duplicate characters and the Set contains exactly the characters in that window.

When the incoming character already exists:

```text
while incoming character is in Set
    remove s[left]
    left++
```

Then add the incoming character.

Important transfer insight:

> The optimization works because an invalid window can be restored by monotonically moving `left` forward. Neither boundary needs to move backward.

This differs from Minimum Size Subarray Sum:

```text
Minimum Size Subarray Sum
→ relies on positivity and numeric monotonicity

Longest Substring
→ relies on monotonic restoration of a validity constraint
```

---

## 9. Longest Substring Complexity

Even with:

```text
for right
    while duplicate
        left++
```

the total complexity is:

```text
O(n)
```

because:

```text
right moves forward at most n times
left moves forward at most n times
```

Each character enters the window at most once and leaves it at most once.

Therefore:

```text
Time  → O(n) average
Space → O(k)
```

where `k` is the maximum number of distinct characters in the active window.

---

## 10. Longest Substring Implementation & Tests

Implemented the Set-based sliding-window solution.

Tested cases include:

```text
"abcabcbb" → 3
"bbbbb"    → 1
"pwwkew"   → 3
""         → 0
"a"        → 1
"abba"     → 2
"dvdf"     → 3
```

Implementation feedback:

> Restore the validity invariant first, add the current character, then compute and update the current maximum. This keeps the code aligned directly with the reasoning.

DSA portion is complete at the Day 8 learning/evidence level.

---

# LRU Cache LLD

## 11. Requirements Derived Before Data Structures

Required API:

```text
get(key)
put(key, value)
```

Target:

```text
get → O(1) expected
put → O(1) expected
```

Two independent requirements were identified:

```text
fast key lookup
+
fast recency mutation
```

---

## 12. Why One Structure Is Insufficient

### HashMap Alone

Provides:

```text
key lookup → O(1) expected
```

but cannot directly maintain:

```text
MRU ... LRU
```

or identify/update recency in `O(1)`.

### Doubly Linked List Alone

Provides:

```text
insert MRU      → O(1)
remove LRU      → O(1)
move known node → O(1)
```

but:

```text
get(key)
```

requires scanning:

```text
O(n)
```

---

## 13. Composite LRU Representation

Derived:

```text
HashMap
+
Doubly Linked List
```

Representation:

```text
Map:
key → Node

DLL:
MRU <-> ... <-> LRU
```

Chosen convention:

```text
head → MRU
tail → LRU
```

Important milestone:

> `HashMap + DLL` was derived from operation requirements rather than memorized as the standard LRU answer.

---

## 14. LRU Node Representation

Node requires:

```text
key
value
prev
next
```

Why the map stores:

```text
key → Node
```

instead of:

```text
key → value
```

Because a successful access must locate the exact list node and move it to MRU in `O(1)`.

Why Node stores `key`:

```text
tail
→ LRU Node
→ node.key
→ map.remove(node.key)
```

This allows eviction to update both representations in `O(1)`.

---

## 15. LRU Helper Contracts

Derived helper operations:

```text
removeNode(node)
addFirst(node)
moveToFront(node)
removeLast()
```

Conceptually:

```text
moveToFront(node)
→ removeNode(node)
→ addFirst(node)
```

Important LLD principle:

> Public methods express cache behavior; helper methods encapsulate pointer mutation.

---

## 16. LRU `get` / `put` Behavior

### `get(key)`

```text
find node in map
↓
missing → cache miss
↓
move node to MRU
↓
return node.value
```

Important insight:

> A successful LRU `get()` is not structurally read-only because it changes recency.

### `put(existingKey, value)`

```text
find existing node
↓
update value
↓
move to MRU
```

Size remains unchanged.

### `put(newKey, value)` With Space

```text
create node
↓
add to map
↓
add as MRU
```

### `put(newKey, value)` When Full

```text
identify LRU from tail
↓
remove LRU from DLL
↓
remove lru.key from map
↓
create new node
↓
add to map
↓
add as MRU
```

---

## 17. LRU Core Invariants

Capacity:

```text
0 <= size <= capacity
```

Map/list agreement:

> Every logical cache entry exists exactly once in the HashMap and exactly once in the linked list.

Therefore:

```text
map.size() == number of DLL nodes
```

Identity invariant:

```text
map.get(k)
```

must point to the exact list node representing `k`.

Recency:

```text
head = MRU
tail = LRU
```

Boundary:

```text
head.prev == null
tail.next == null
```

Important LLD lesson:

> Two individually valid data structures can still form an invalid composite structure if they disagree about their logical contents.

---

## 18. LRU Capacity-2 Dry Run

For:

```text
capacity = 2
```

operations:

```text
put(1,10)
put(2,20)
get(1)
put(3,30)
```

state evolves to:

```text
after put(1):
1

after put(2):
2 <-> 1

after get(1):
1 <-> 2

put(3):
evict 2

final:
3 <-> 1
```

Then:

```text
get(2) → miss
get(3) → 30
```

Required Day 8 LRU dry run completed.

Full LRU implementation and tests remain intentionally deferred.

Do not mark LRU as mastered yet.

---

# Java / JVM Connection

## 19. Linked Node Trade-Off

LRU requires:

```text
access arbitrary key
↓
obtain exact node
↓
move arbitrary node to MRU
```

A DLL allows a known node to be detached in `O(1)` using:

```text
prev
next
```

This does not imply linked structures are universally faster than array-backed structures.

Composite LRU adds:

```text
HashMap state
+
Node allocations
+
key/value
+
prev/next references
```

Potential costs:

* extra memory
* more allocations
* pointer chasing
* weaker locality
* additional GC pressure

Key principle:

> Improved operation complexity often costs additional state and stronger invariants.

---

# HLD / Production Cache Reasoning

## 20. Local Cache as Optimization

Typical flow:

```text
request
↓
local cache
├── hit  → return cached value
└── miss → authoritative store
           ↓
           populate cache
           ↓
           return
```

A local in-memory cache should normally be reconstructible.

If the JVM disappears:

```text
cache disappears
```

but durable business data should remain available from the authoritative database/service.

Therefore:

```text
cache = optimization
```

not:

```text
system of record
```

---

## 21. Working Set, Capacity & LRU Limitations

LRU assumes:

> Recently accessed data is more likely to be accessed again.

This is a workload assumption, not a universal truth.

If:

```text
working set >> capacity
```

the cache can thrash:

```text
insert
evict
insert
evict
```

with little useful reuse.

LRU tracks:

```text
recency
```

not:

```text
historical frequency
```

Therefore a historically very hot item can still eventually be evicted if it has not been accessed recently.

---

## 22. Cache Capacity Reasoning

Increasing capacity is not automatically a fix.

Example:

```text
capacity = 10,000
hit rate ≈ 90–94%
```

Increasing capacity dramatically may provide little incremental value while increasing:

* memory usage
* per-instance duplication
* warm-up cost
* GC pressure

Low hit rate also does not automatically imply insufficient capacity.

Investigate:

* working-set size
* cache-key cardinality
* TTL
* invalidation
* cache bypass
* changed access locality
* failed cache population

---

## 23. Cache Hit-Rate Collapse

After deployment, first determine whether the cache is temporarily cold.

Typical local-cache lifecycle:

```text
deployment
↓
JVM restart
↓
empty cache
↓
misses
↓
cache warms
↓
hit rate recovers
```

A persistent low hit rate after the expected warm-up period requires investigation.

Potential causes:

* reduced capacity
* changed TTL
* aggressive invalidation
* cache bypass
* cache population failure
* changed access pattern
* increased service-instance count
* metric/instrumentation changes
* bad cache-key construction

Important example:

```text
before:
customer:123

after:
customer:123:requestId:<unique>
```

A unique request ID creates extremely high cache-key cardinality and can collapse hit rate even when the LRU implementation itself is correct.

---

## 24. Hit Rate & Dependency Load

When:

```text
cache hit rate ↓
```

then:

```text
cache misses ↑
↓
database/downstream requests ↑
↓
dependency load ↑
↓
latency may ↑
```

Application CPU may remain moderate while the dependency experiences significant pressure.

---

## 25. Eviction vs Freshness

Important distinction:

```text
LRU
→ eviction policy

TTL / invalidation / refresh
→ freshness policy
```

Stale data is not inherently an LRU implementation problem.

Staleness occurs when:

```text
source of truth changes
↓
cached copy remains old
↓
application continues reading cached value
```

LRU answers:

> Which entry should be removed because capacity is needed?

Freshness logic answers:

> When should an existing cached entry no longer be trusted?

These concerns must not be conflated.

---

# Day 8 — Gaps / Corrections to Continue Reinforcing

## DSA Abstraction

Continue enforcing:

```text
objective
↓
information that affects answer
↓
discardable details
↓
smallest sufficient representation
↓
brute force
↓
repeated work
↓
optimization
```

There is still a tendency to initially retain more state than required, such as global character frequencies for a problem that only requires current-window membership.

## Precision

Continue correcting:

* Deque behavior vs DLL representation
* `tail` existence vs predecessor availability
* active-list invariant vs detached-node cleanup
* exact sliding-window enabling property
* `Map<K, Node>` vs `Map<K, V>`
* worst-case complexity proof vs approximate runtime intuition

These are refinement gaps, not foundational blockers.

---

# Day 8 Completion Status

```text
Day 7 Queue closure                  ✅
Valid Parentheses closure            ✅

Deque contract                       ✅
DLL derivation                       ✅
DLL invariants                       ✅
IntDeque implementation              ✅
IntDeque tests                       ✅

Longest Substring derivation         ✅
Set-based implementation             ✅
required edge cases                  ✅
O(n) proof                           ✅
pattern-transfer explanation         ✅

LRU requirements                     ✅
HashMap limitation                   ✅
DLL limitation                       ✅
composite representation             ✅
Node fields                          ✅
helper contracts                     ✅
get/put flows                        ✅
LRU invariants                       ✅
capacity-2 dry run                   ✅

LRU full implementation              ⏳ Day 9
LRU implementation tests             ⏳ Day 9

Local cache production reasoning     ✅
hit-rate diagnosis                   ✅
capacity/working-set reasoning       ✅
eviction vs freshness distinction    ✅
```

---

# Exact Next Action — Day 9

Begin with a short retrieval drill only.

Do not re-teach the LRU architecture.

Retrieve:

```text
Why Map<K, Node>?
Why does Node contain key?
What does head represent?
What does tail represent?
What invariant must hold between the map and DLL?
```

Then implement the LRU structural helpers:

```text
removeNode(node)
addFirst(node)
moveToFront(node)
removeLast()
```

Verify them across:

```text
single node
head
tail
middle node
```

Then implement:

```text
get(key)
put(key, value)
```

and add tests for:

```text
existing-key update
access changes recency
capacity-1 behavior
eviction
evicted-key miss
repeated get
repeated put
map/list agreement
```

Primary Day 9 engineering invariant:

> The HashMap and Doubly Linked List must remain two synchronized representations of exactly the same logical cache contents after every operation.

After the LRU implementation quality gate, continue Day 9 using:

```text
MASTER_CURRICULUM.md
+
registry.md
+
Day 8 evidence
```

without re-teaching completed Day 8 material.
