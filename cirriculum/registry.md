# Backend Engineering Bootcamp Registry

> **Purpose:** Continuity/state document for the bootcamp.
>
> `MASTER_CURRICULUM.md` is the strategic source of truth.
>
> `registry.md` is the execution-state source of truth.

---

# Session Startup Protocol

At the beginning of every new bootcamp session:

1. Read `cirriculum/MASTER_CURRICULUM.md`.
2. Read this `registry.md`.
3. Read the previous day's curriculum when relevant.
4. Generate or open the current day's curriculum.
5. Resume from `Next Action`.
6. Do not assume a topic is mastered merely because it was discussed.
7. Update this registry at the end of the session based on actual evidence.

Daily progression:

```text
MASTER_CURRICULUM.md
        +
registry.md
        +
previous-day evidence
        ↓
daily curriculum
        ↓
Learn → Explain → Implement → Test → Benchmark → Design
        ↓
registry.md update
```

---

# Current Position

* **Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory
* **Latest Curriculum Worked:** Day 4 — Implementation Discipline + Hashing Foundations
* **Day 4 Date:** 2026-08-13
* **Status:** Day 4 core learning and HashMap V1 implementation completed; engineering tests/refactor still pending. Day 4 distributed-idempotency continuation intentionally moved to Day 5.
* **Next Curriculum:** Day 5
* **Primary Language:** Java
* **Target Level:** Strong Senior / Lead / Staff-level backend engineering capability
* **Primary Goal:** Production engineering excellence + top-tier interview readiness

---

# Progress Summary

## Day 1 — Arrays, Memory & Pattern Foundations

Covered:

* array memory layout and contiguous storage
* O(1) indexed access
* insertion/deletion shifting
* primitive vs reference arrays
* cache locality
* boxing/unboxing and Integer caching
* `==` vs `.equals()`
* Dynamic Array design foundations
* basic array pattern recognition

Patterns/problems introduced:

* Two Sum
* Contains Duplicate
* Running Sum
* Move Zeroes
* Best Time to Buy and Sell Stock
* Build Array from Permutation

Growth areas discovered:

* derive brute force before optimization
* exact index arithmetic
* stronger invariants
* distinguish abstraction layers precisely

---

## Day 2 — Complexity & Linear Scan Thinking

Covered:

* deriving complexity from work performed
* linear scan reasoning
* maintaining running state
* Dynamic Array resizing/copying
* sequential vs random memory access
* CPU cache locality
* Big-O vs actual runtime
* introductory JVM/JIT model
* hot-code detection and code cache

Strong evidence:

* understands why sequential array access is cache-friendly
* understands why random access can be slower while remaining the same Big-O
* understands Big-O does not predict exact runtime
* understands why benchmarking is needed for real performance claims

---

## Day 3 — Prefix/Suffix Thinking & Reusing Computed Work

Completed conceptually:

* Big-O vs actual runtime
* JIT method inlining mental model
* JMH warm-up/measurement/fork mental model
* prefix sums and precomputation
* complexity with independent variables (`O(n + q)`)
* prefix/suffix decomposition
* Product of Array Except Self reasoning
* Pivot Index
* minimum necessary state
* mutable vs immutable data architecture
* metrics/preaggregation HLD connection
* Dynamic Array boundary invariants
* JIT bounds/range-check elimination introduction

Important invariants:

```text
prefix[i] = aggregate of first i elements
```

```text
suffixProduct = product strictly after i
```

```text
leftSum = sum strictly before i
```

Primary Day 3 principle:

> Repeated overlapping computation + many reads + stable data should trigger consideration of precomputation.

Engineering evidence still pending from Day 3:

* Dynamic Array implementation/tests
* JMH benchmark implementation

Product Except Self Java implementation was completed during Day 4; JUnit tests remain pending.

---

# Day 4 — Completed Learning

## 1. Spaced Retrieval

Reinforced:

* Product Except Self reverse-pass invariant
* independent complexity variables (`n` and `q`)
* CPU cache vs JVM code cache
* JIT compilation flow

Important terminology correction:

```text
source code
→ javac bytecode
→ JVM interpretation/profiling
→ JIT native machine code
→ JVM code cache
```

CPU cache and JVM code cache are different concepts.

### Status

**Completed; terminology should continue to be reinforced.**

---

## 2. Dynamic Array Quality Gate

Reinforced:

```text
size = logical number of elements
capacity = physical backing storage length
```

Valid access invariant:

```text
0 <= index < size
```

Append:

```text
elements[size] = value
size++
```

Remove shift count:

```text
size - index - 1
```

Growth principle:

* geometric growth (for example 1.5x or 2x)
* `grow()` changes capacity
* `add()` changes logical size

Amortized analysis correction:

> Amortized O(1) does not ignore expensive resizes. It counts all copying across a sequence of operations and spreads that total cost across the sequence.

Geometric growth gives total copy work O(n) across n appends, so append is amortized O(1).

### Status

**Conceptual quality gate completed. Implementation/test evidence still pending.**

---

## 3. Product of Array Except Self — Java Implementation

Implemented the O(n), O(1)-auxiliary-space version using the result array for prefix state and a scalar `suffixProduct`.

Validated mentally against:

```text
[1,2,3,4]   → [24,12,8,6]
[0,1,2,3]   → [6,0,0,0]
[0,0,2,3]   → [0,0,0,0]
```

Complexity:

```text
Time: O(n)
Auxiliary space: O(1)
```

excluding the output array.

### Status

**Java implementation completed. JUnit tests pending.**

---

## 4. Why Hashing Exists

Derived hashing from the repeated-work problem rather than starting from Java `HashMap`.

Scenario:

```text
many membership queries over largely stable data
```

Repeated work:

```text
scanning the collection again and again
```

Minimum sufficient representation for pure membership:

```text
set of values already present/seen
```

For associated information:

```text
key → value
```

Core path:

```text
key
→ hash/hash value
→ bucket mapping
→ bucket index
→ collision structure
→ exact key comparison
```

Important distinction:

> Hash value and bucket index are not the same concept.

### Status

**Completed.**

---

## 5. Collisions, Load Factor & Complexity

Understood:

* collisions are unavoidable when large key spaces map into finite bucket spaces
* separate chaining resolves collisions using linked entries per bucket
* bucket array provides O(1) indexed access
* chain traversal verifies the actual key

Load factor:

```text
alpha = n / m
```

where:

```text
n = number of entries
m = number of buckets
```

Expected separate-chaining lookup:

```text
O(1 + alpha)
```

If load factor remains bounded, expected lookup is O(1).

Worst case:

```text
all keys collide
→ chain length n
→ O(n)
```

Resizing insight:

> Capacity changes bucket mapping. A stored object's logical hash value does not inherently need to change merely because the table capacity changes.

### Status

**Completed conceptually. Resizing intentionally not implemented in HashMap V1.**

---

## 6. DSA Pattern Reinforcement

### Contains Duplicate

Derived progression:

```text
brute-force repeated membership search
→ O(n²)
→ seen-values HashSet
→ expected O(n)
```

Invariant:

> Before processing index `i`, the set contains exactly the distinct values from indices strictly before `i`.

### Two Sum

Derived progression:

```text
for current value
needed = target - current
```

Minimum state:

```text
value → earlier index
```

Correct one-pass order:

```text
lookup complement
then insert current value/index
```

This prevents reusing the same element, including the `[3,3]`, target `6` case.

### Status

**Transfer reasoning successful. Continue daily problem volume from Day 5 onward.**

---

## 7. HashMap V1 — Design & Implementation

Implemented an integer-key/integer-value HashMap using separate chaining.

Current logical model:

```text
IntHashMap
 ├── Entry[] map/buckets
 ├── int size
 └── Entry
      ├── int key
      ├── int value
      └── Entry next
```

Implemented:

* [x] `put(int key, int value)`
* [x] `get(int key)`
* [x] `containsKey(int key)`
* [x] `size()`
* [x] collision chaining
* [x] update existing key without increasing size
* [x] negative-key bucket mapping using `Math.floorMod`

Important invariants:

```text
Every logical key appears at most once.
```

```text
Every Entry lives in the bucket selected by the bucket function.
```

```text
size = number of distinct logical mappings.
```

```text
Updating an existing key does not increase size.
```

Traversal lesson learned:

> For linked structures, reason about the node currently being inspected (`current != null`) rather than attempting to control traversal through `next`/`next.next` boundary checks.

Current implementation is functionally correct for V1, but cleanup remains.

### Pending cleanup

* [ ] remove redundant `capacity` field and use `map.length`
* [ ] remove unused `grow()` until resizing is deliberately implemented
* [ ] remove/avoid `setKey()`; key identity should remain stable after insertion
* [ ] optionally rename `map` to `buckets`
* [ ] refactor duplicated bucket traversal into `findEntry(int key)` after tests

### Required JUnit tests

* [ ] basic insertion/retrieval
* [ ] intentional collisions (`1`, `11`, `21` with capacity 10)
* [ ] update existing key and verify size unchanged
* [ ] missing `get()` throws `NoSuchElementException`
* [ ] missing `containsKey()` returns false
* [ ] negative key
* [ ] same value under different keys

### Status

**Core implementation complete. Tests and small refactor pending. Do not mark engineering lab fully complete until tests pass.**

---

## 8. Java Deep Dive — `equals()` and `hashCode()`

Understood:

```text
equals()   → logical key identity
hashCode() → hash used to decide where to search
```

Contract:

```text
a.equals(b) == true
→ a.hashCode() == b.hashCode()
```

But:

```text
a.hashCode() == b.hashCode()
```

does not imply equality because collisions are legal.

Derived failure if equal objects return inconsistent hashes:

```text
put(a)
→ bucket X

get(equal b)
→ bucket Y
→ logically equal key never inspected
```

Mutable-key problem understood:

```text
insert key using hash H1
→ entry physically stored in bucket B1
→ mutate field used by equals/hashCode
→ hash becomes H2
→ lookup searches B2
→ entry may appear missing although still physically present
```

Key insight:

> Fields participating in `equals()`/`hashCode()` should generally remain stable while the object is used as a HashMap key.

### Status

**Completed.**

---

## 9. LLD/HLD + Production Connection — Request Deduplication

Session started and intentionally deferred to Day 5 for deeper continuation.

Scenario:

```text
requestId
→ clients may retry
→ same logical request should not execute twice
```

Derived so far:

### Local HashMap limitation

```text
node 1 local HashMap
!=
node 7 local HashMap
```

Therefore per-node in-memory maps cannot guarantee global deduplication across multiple application nodes.

Appending node ID to request ID was considered and rejected because it creates different identities for retries of the same logical request.

### Shared store

Need a shared source of truth keyed by the same stable request/idempotency key.

### Concurrency race

Unsafe:

```text
SELECT request
if absent
    process
    INSERT
```

Two nodes can both observe absence before either inserts.

Derived stronger approach:

```text
request_id UNIQUE / PRIMARY KEY
+
atomic insertion/claim
```

### Identity vs state

Rejected composite primary key:

```text
(request_id, status)
```

because it weakens the invariant and allows multiple logical records for one request.

Preferred conceptual model:

```text
request_id PRIMARY KEY
status     PROCESSING | COMPLETED | FAILED
result
timestamps / optional lease metadata
```

Key principle:

> `requestId` is identity; `status` is mutable lifecycle state.

### Crash/retry reasoning introduced

If processing completes and the node crashes before returning the HTTP response, a retry should be able to find `COMPLETED` and return the stored result rather than execute the business action again.

Also introduced the idea that when the business write and idempotency state live in the same database, a transaction can couple them atomically.

### Status

**Partially completed. Continue on Day 5.**

### Day 5 continuation questions

Continue from:

1. What exactly should happen for `PROCESSING`, `COMPLETED`, and `FAILED` on retry?
2. What happens when a worker crashes while status remains `PROCESSING`?
3. When is a lease/timeout/reclaim mechanism needed?
4. What if the business side effect is in a different system and cannot share the DB transaction?
5. Difference between idempotency, deduplication, exactly-once effects, and at-least-once delivery.

Do not over-expand prematurely; derive each failure mode from first principles.

---

# DSA Abstraction-First Protocol — HIGH PRIORITY

A recurring interview weakness was identified: there is a tendency to model the story/system too literally and introduce arrays/classes/state before identifying the minimal mathematical/computational problem.

For every future DSA problem, enforce this sequence **before coding**:

```text
1. What is the objective/output?
2. What information actually affects the answer?
3. What story details can be discarded?
4. What is the smallest sufficient representation/state?
5. What is the brute-force solution?
6. What work is repeated / what invariant exists?
7. Test the mental model on a tiny case.
8. Only then choose data structures and implement.
```

Do not name the pattern too early. The learner must derive the representation and repeated-work insight first.

From Day 5 onward, target roughly 45–60 minutes/day of DSA problem solving, approximately 2–4 problems depending on difficulty, with transfer problems where the pattern is not announced.

---

# Current Strengths

Evidence through Day 4:

## 1. Strong backend/system intuition

Naturally connects data-structure concepts to:

* caching
* precomputation
* mutable vs immutable data
* metrics systems
* request deduplication
* database uniqueness
* distributed coordination boundaries

## 2. Good performance intuition

Understands:

* sequential vs random memory access
* CPU cache locality
* Big-O vs actual runtime
* JIT/code-cache distinction after correction

## 3. Improving invariant-based reasoning

Successfully reasons with:

```text
leftSum = everything strictly before i
suffixProduct = everything strictly after i
seen = values from strictly earlier indices
current = linked entry currently being inspected
```

## 4. Improving state minimization

Product Except Self and Two Sum showed progress toward asking:

```text
What is the minimum information that must survive?
```

rather than automatically creating multiple structures.

## 5. Good debugging response

HashMap V1 exposed linked-chain boundary bugs; after correction, traversal reasoning improved and the implementation became functionally correct.

---

# Growth Areas

## 1. DSA Abstraction Before Implementation — HIGHEST PRIORITY

Do not model the story literally.

Always identify objective, relevant information, discardable details, and minimum sufficient state before selecting a data structure.

---

## 2. Exact Index / Boundary Semantics — HIGH PRIORITY

Before formulas or loops, state:

```text
What does this index/reference represent?
What is included?
What is excluded?
What is true before processing?
What becomes true after processing?
```

This applies to arrays, prefix structures, and linked structures.

---

## 3. Brute Force Before Optimization — HIGH PRIORITY

Required interview progression:

```text
1. brute force
2. derive complexity
3. identify repeated work
4. choose minimum reusable state
5. state invariant
6. optimize
7. dry run
8. implement
9. test
```

---

## 4. Implementation/Test Evidence — HIGH PRIORITY

Conceptual progress continues to move faster than engineering evidence.

Do not declare implementations complete until:

```text
implemented
→ edge cases tested
→ automated tests pass
→ cleanup/refactor performed where appropriate
```

---

## 5. JVM Terminology Precision

Continue separating:

```text
CPU cache
JVM code cache
hot code
JIT compilation
method inlining
bounds/range-check elimination
```

---

## 6. Complexity With Independent Variables

Preserve independent dimensions such as:

```text
O(n + q)
```

unless a relationship between variables is explicitly established.

---

# Engineering Deliverables — Current State

## Dynamic Array

### Reasoning

* [x] add mechanics understood
* [x] growth boundary understood
* [x] valid-index invariant understood
* [x] remove shifting understood
* [x] size/capacity distinction understood
* [x] amortized geometric-growth reasoning understood

### Implementation / Verification

* [ ] verify append uses `elements[size]` before increment
* [ ] verify negative indexes fail
* [ ] verify `index == size` fails for get/set/remove
* [ ] verify remove shift boundaries
* [ ] verify grow preserves elements
* [ ] verify size changes exactly once
* [ ] JUnit: first insertion
* [ ] JUnit: growth boundary
* [ ] JUnit: invalid accesses
* [ ] JUnit: remove first/middle/last
* [ ] commit verified implementation/tests

**Not fully complete.**

---

## Product of Array Except Self

* [x] brute-force reasoning
* [x] O(n²) derivation
* [x] prefix/suffix solution
* [x] O(n) derivation
* [x] O(1) auxiliary-space optimization
* [x] zero-case reasoning
* [x] Java implementation
* [ ] JUnit tests

---

## HashMap V1

* [x] bucket-array model
* [x] separate chaining
* [x] `put`
* [x] `get`
* [x] `containsKey`
* [x] `size`
* [x] update-existing-key semantics
* [x] negative-key bucket mapping
* [x] equals/hashCode conceptual bridge
* [ ] cleanup redundant `capacity`/`grow`
* [ ] make key effectively immutable / remove `setKey`
* [ ] JUnit basic insertion
* [ ] JUnit collision chain
* [ ] JUnit update without size increase
* [ ] JUnit missing key behavior
* [ ] JUnit negative key
* [ ] JUnit same value/different keys
* [ ] optional `findEntry(key)` refactor after tests

**Core implementation complete; engineering quality gate pending.**

---

## JMH

* [x] warm-up mental model
* [x] measurement mental model
* [x] benchmark contamination understood
* [x] fork purpose introduced
* [ ] actual benchmark

---

# Patterns Learned So Far

## Complement Lookup

```text
What value do I need, and have I seen it?
```

Typical representation:

```text
value → index/info
```

Example: Two Sum.

---

## Membership Lookup

```text
Have I seen this value before?
```

Typical representation:

```text
HashSet
```

Example: Contains Duplicate.

---

## Running State

```text
Can everything needed for the next iteration be summarized in small state?
```

Examples: Running Sum, Best Time to Buy/Sell Stock, Pivot Index.

---

## Stable Compaction

```text
Where should the next valid element be written?
```

Typical state: `writeIndex`.

Example: Move Zeroes.

---

## Prefix / Precomputation

```text
Am I repeatedly recalculating overlapping information that can be computed once?
```

Best for read-heavy, stable/immutable data.

---

## Prefix + Suffix Decomposition

```text
Can result[i] be expressed from everything before i and everything after i?
```

Example: Product of Array Except Self.

---

## Global Aggregate + Running Prefix

```text
If I know the total and everything before i, can I derive everything after i?
```

Example: Pivot Index.

---

## Hash-Based Membership / Association

```text
Can repeated search be replaced by organized keyed lookup?
```

Representation depends on requirement:

```text
membership only      → Set
membership + payload → Map
```

---

# Interview Response Framework

For every coding problem:

1. Objective / required output
2. Relevant information
3. Discardable story details
4. Minimum sufficient representation
5. Clarifications/assumptions
6. Brute force
7. Complexity from actual work
8. Repeated work / bottleneck
9. Better solution
10. Invariant
11. Dry run
12. Implementation
13. Tests/edge cases
14. Final time + auxiliary-space complexity
15. Trade-offs when relevant

Do not jump directly from story to data structure.

---

# Day 4 Exit Assessment

## Achieved

* [x] reinforced prefix/suffix invariants
* [x] reinforced independent-variable complexity reasoning
* [x] clarified CPU cache vs JVM code cache
* [x] completed Dynamic Array conceptual quality gate
* [x] implemented Product Except Self in Java
* [x] derived why hashing exists
* [x] explained hash value vs bucket index
* [x] explained collisions and separate chaining
* [x] derived load factor and expected/worst-case behavior
* [x] solved Contains Duplicate using seen-state reasoning
* [x] derived one-pass Two Sum using value → earlier index
* [x] designed and implemented HashMap V1 core operations
* [x] fixed linked-chain traversal boundary reasoning
* [x] completed `equals()` / `hashCode()` contract
* [x] explained mutable-key failure
* [x] started request-deduplication LLD/HLD connection
* [x] identified local-memory vs distributed-coordination boundary
* [x] derived stable request identity + mutable status model

## Not Yet Proven / Deferred

* [ ] Dynamic Array JUnit quality gate
* [ ] Product Except Self JUnit tests
* [ ] HashMap V1 JUnit tests
* [ ] HashMap V1 cleanup/refactor
* [ ] request-idempotency failure-mode discussion continuation
* [ ] Day 4 later production section(s), intentionally rolled into Day 5 rather than rushed
* [ ] JMH implementation

---

# Next Action — Day 5

Generate/open **Day 5** using:

```text
cirriculum/MASTER_CURRICULUM.md
+
registry.md
+
Day 4 evidence
```

Day 5 must not restart hashing from scratch.

## Mandatory Day 5 opening carry-forward

### Engineering evidence block

Complete the shortest useful quality gate first:

```text
1. HashMap V1 JUnit tests
2. HashMap V1 cleanup
3. optional findEntry(key) refactor
```

Do not let this consume the entire day.

Product Except Self/Dynamic Array tests remain tracked and can be scheduled deliberately rather than repeatedly deferred.

### DSA block

Allocate approximately 45–60 minutes.

Use 2–4 problems depending on difficulty.

Enforce abstraction-first reasoning before pattern/data-structure selection.

Do not announce the pattern in advance.

### LLD/HLD continuation

Resume request idempotency from the exact state reached:

```text
request_id = stable identity / unique key
status = mutable lifecycle state
result = replayable response/result when completed
```

Continue deriving:

```text
PROCESSING retry semantics
worker crash/recovery
leases/timeouts
transaction boundary
cross-system side effects
idempotency vs exactly-once effects
```

Keep the discussion failure-mode driven rather than solution-name driven.

---

# Registry Update Rule

At the end of every future day, update state, not transcript.

Capture:

```text
1. Current Position
2. Topics actually completed
3. Problems actually solved
4. Implementations/tests actually completed
5. New strengths demonstrated
6. Misconceptions/gaps observed
7. Pending deliverables
8. Exact Next Action
```

A topic discussed once is not automatically mastered.

Use evidence such as:

```text
explained without help
derived correctly
implemented
tested
debugged
benchmarked
applied to a transfer problem
connected to a production scenario
```

to decide mastery.
