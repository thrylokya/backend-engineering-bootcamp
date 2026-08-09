# Backend Engineering Bootcamp Registry

> **Purpose:** This file is the continuity/state document for the bootcamp.
>
> It records:
>
> * where the curriculum currently stands
> * what has actually been learned
> * what has only been discussed but not implemented
> * observed strengths
> * misconceptions and growth areas
> * incomplete engineering deliverables
> * the exact next action
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

Daily progression should follow:

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
* **Last Completed Curriculum:** Day 3 — Prefix/Suffix Thinking & Reusing Computed Work
* **Day 3 Date:** 2026-08-09
* **Status:** Day 3 conceptual objectives completed
* **Next Curriculum:** Day 4
* **Primary Language:** Java
* **Target Level:** Strong Senior / Lead / Staff-level backend engineering capability
* **Primary Goal:** Production engineering excellence + top-tier interview readiness

---

# Progress Summary

## Day 1 — Arrays, Memory & Pattern Foundations

Core areas covered:

* array memory layout
* contiguous storage
* indexed access
* insertion/deletion shifting
* primitive versus reference arrays
* cache locality
* virtual memory versus CPU-cache concepts
* boxing/unboxing
* Integer caching
* `==` versus `.equals()`
* basic array pattern recognition
* Dynamic Array design foundations

Problems/patterns discussed include:

* Two Sum
* Contains Duplicate
* Running Sum
* Move Zeroes
* Best Time to Buy and Sell Stock
* Build Array from Permutation
* array traversal and state-maintenance problems

Primary growth areas discovered:

* derive brute force before optimization
* exact index arithmetic
* stronger invariants
* distinguish abstraction layers precisely

---

## Day 2 — Complexity & Linear Scan Thinking

Core areas covered:

* deriving complexity from work performed
* linear scan reasoning
* maintaining running state
* Dynamic Array mechanics
* resizing/copying
* sequential versus random memory access
* CPU cache locality
* Big-O versus actual runtime
* basic JIT mental model
* hot-code detection
* code cache introduction

Strong evidence:

* understands why sequential array access is cache-friendly
* understands why random/scattered access can be slower while remaining `O(n)`
* understands that Big-O does not predict exact execution time
* understands why benchmarks are necessary for real performance claims

Carry-forward gaps identified:

* method inlining
* JMH
* exact index reasoning
* distinguishing JIT/compiler effects from CPU-cache effects

These were partially closed during Day 3.

---

# Day 3 — Completed Learning

## 1. Big-O vs Actual Runtime

Understands:

```text
Big-O
=
how work grows as input grows
```

It does not directly describe:

```text
CPU instructions
latency
cache misses
allocation cost
branch prediction
GC pressure
object indirection
```

Two algorithms can both be:

```text
O(n)
```

while having significantly different real-world performance.

Reasons identified include:

* sequential vs random access
* cache locality
* primitive versus object representation
* object indirection
* allocation
* branch predictability
* JVM/JIT optimization

### Status

**Conceptually understood.**

Continue requiring precise separation of:

```text
asymptotic complexity
vs
actual runtime cost
```

---

# 2. JIT Method Inlining

Current mental model:

```text
frequently executed code
        ↓
JVM profiles execution
        ↓
JIT optimizes hot paths
        ↓
optimized machine code
        ↓
code cache
```

Method inlining is understood as a separate optimization:

```text
caller
  ↓
small/hot callee
  ↓
callee body becomes visible at call site
```

Primary insight:

> The major benefit of inlining is not merely avoiding method-call overhead. It exposes a larger optimization region to the compiler.

Possible follow-on optimizations include:

* constant propagation
* dead-code elimination
* redundant-load elimination
* devirtualization
* bounds/range-check elimination in applicable situations

### Terminology to reinforce

Do not mix:

* hot code
* HotSwap
* JIT
* code cache
* CPU cache
* method inlining

### Status

**Interview-level mental model established; terminology still requires reinforcement.**

---

# 3. JMH Mental Model

Understands why this is unreliable for serious Java microbenchmarking:

```java
long start = System.nanoTime();
someMethod();
long end = System.nanoTime();
```

Important effects discussed:

* JVM warm-up
* profiling
* tiered compilation
* JIT optimization
* class loading
* dead-code elimination
* measurement noise
* benchmark contamination
* shared warmed code
* forks/isolation

Current mental model:

```text
cold JVM
   ↓
warm-up iterations
   ↓
profiling / JIT optimization
   ↓
steady-state-ish execution
   ↓
measurement iterations
```

Also understood:

> Benchmark A can warm shared JVM code/state and make Benchmark B artificially appear faster.

### Status

**Mental model understood.**

### Pending

* No actual JMH benchmark implemented yet.
* JMH annotations/API do not need deep study yet.

---

# 4. Prefix Sum / Precomputation Pattern

Primary Day 3 insight:

> Repeated overlapping computation + many reads + stable data should trigger consideration of precomputation.

The pattern should **not** be recognized merely as:

```text
"range sum → prefix sum"
```

The desired reasoning is:

```text
What work is repeated?
        ↓
Will it be queried repeatedly?
        ↓
Can useful state be computed once?
        ↓
How mutable is the source data?
        ↓
What does the stored state represent?
        ↓
What do updates now cost?
```

---

# Prefix Convention

Preferred convention:

```text
prefix[i]
=
sum of the first i elements
```

Example:

```text
nums   = [2, 4, 6, 8, 10]
prefix = [0, 2, 6, 12, 20, 30]
```

Invariant:

> `prefix[i]` contains the sum of `nums[0]` through `nums[i - 1]`.

Range `[left, right]`, inclusive:

```text
sum(left, right)
=
prefix[right + 1] - prefix[left]
```

Important conceptual derivation:

```text
everything through right
-
everything before left
=
desired range
```

### Status

**Understood after working through competing prefix conventions.**

### Important carry-forward gap

There was initial confusion between:

```text
prefix[i] = sum through index i
```

and:

```text
prefix[i] = sum of first i elements
```

Do not allow formulas to be written until the invariant is explicitly stated.

---

# 5. Prefix Sum Complexity

Naive repeated range queries:

```text
q queries
×
up to n work/query
=
O(nq)
```

Prefix approach:

```text
preprocessing = O(n)
q queries     = O(q)

total = O(n + q)
```

Important correction learned:

> When `n` and `q` are independent inputs, `O(n + q)` must not casually be simplified to `O(n)`.

### Status

**Understood.**

---

# 6. Prefix-Sum Trade-offs

Prefix/precomputed structures work particularly well for:

```text
read-heavy
+
mostly immutable
+
repeated overlapping queries
```

Weakness:

If:

```text
nums[i]
```

changes, many later cumulative values may become stale.

An arbitrary historical update can require:

```text
O(n)
```

repair in a simple prefix structure.

Engineering connection understood:

```text
precomputation
=
faster reads
in exchange for
storage + maintenance cost
```

Connections discussed:

* caches
* database indexes
* materialized views
* metrics rollups
* analytics aggregation

### Status

**Strong conceptual understanding.**

---

# 7. Product of Array Except Self

Problem reasoning completed.

Brute force:

```text
for every index
    multiply every other value
```

Complexity:

```text
O(n²)
```

Repeated-work insight:

```text
answer[i]
=
product of everything strictly before i
×
product of everything strictly after i
```

---

## First Optimized Version

Use:

```text
prefix[]
suffix[]
```

with:

```text
prefix[i]
=
product strictly before i

suffix[i]
=
product strictly after i
```

For:

```text
nums = [1,2,3,4]
```

derived:

```text
prefix = [1,1,2,6]
suffix = [24,12,4,1]
```

Then:

```text
answer[i] = prefix[i] * suffix[i]
```

Result:

```text
[24,12,8,6]
```

Complexity:

```text
Time:  O(n)
Space: O(n)
```

---

## Space-Optimized Version

Insight:

> The complete suffix array does not need to be retained.

Reuse output array for prefix products:

```text
answer[i]
=
product strictly left of i
```

Then traverse right → left using:

```text
suffixProduct
```

Invariant before processing `i`:

> `suffixProduct` contains the product of everything strictly after `i`.

Correct update order:

```text
answer[i] *= suffixProduct
suffixProduct *= nums[i]
```

Final complexity:

```text
Time: O(n)
Auxiliary Space: O(1)
```

excluding the required result array.

### Status

**Reasoning completed.**

### Pending

* Java implementation not yet verified.
* Unit tests not yet written as part of Day 3.
* Should eventually test normal + zero cases.

---

# 8. Product Except Self — Zero Cases

Understood:

```text
[0,1,2,3]
→
[6,0,0,0]
```

because excluding the zero at index `0` leaves:

```text
1 * 2 * 3
```

Two zeros:

```text
[0,0,2,3]
→
[0,0,0,0]
```

Prefix/suffix solution naturally handles these cases without division or explicit zero-count branches.

### Status

**Understood after one correction.**

---

# 9. Pivot Index — Transfer Problem

Used to test whether prefix thinking transfers beyond explicit range queries.

Brute force:

```text
for every candidate i
    calculate left sum
    calculate right sum
```

Complexity:

```text
O(n²)
```

Optimized insight:

Calculate:

```text
totalSum
```

once.

Maintain:

```text
leftSum
```

Invariant before processing index `i`:

> `leftSum` contains the sum of everything strictly before `i`.

Then:

```text
rightSum
=
totalSum - leftSum - nums[i]
```

Check:

```text
leftSum == rightSum
```

Then update:

```text
leftSum += nums[i]
```

Complexity:

```text
Time: O(n)
Space: O(1)
```

Successfully dry-ran:

```text
[1,7,3,6,5,6]
```

and found:

```text
pivot index = 3
```

### Status

**Reasoning and pseudocode completed successfully.**

### Pending

* Java implementation optional/not yet completed.

---

# 10. Minimum Necessary State

Important Day 3 optimization principle learned:

> Do not allocate prefix and suffix arrays automatically.

Ask:

```text
What minimum state must survive between iterations or queries?
```

Examples:

### Pivot Index

Needs only:

```text
totalSum
leftSum
```

No prefix/suffix arrays required.

### Product Except Self

Needs:

```text
output array for prefix state
suffixProduct variable
```

No explicit suffix array required.

### Arbitrary future left/right lookup

May justify storing:

```text
prefix[]
+
totalSum
```

and deriving suffix values when needed.

### Status

**Good emerging optimization instinct.**

---

# 11. Dynamic Array Quality Gate

Core invariants reinforced:

```text
0 <= size <= capacity
```

Valid element index:

```text
0 <= index < size
```

Next append location:

```text
elements[size]
```

Critical distinction:

```text
index == size
```

is:

* valid as the next append position
* invalid for `get`
* invalid for `set`
* invalid for `remove`

---

## Add Invariant

Correct conceptual ordering:

```text
if full
    grow

elements[size] = value
size++
```

Do not:

```text
elements[++size] = value
```

because when `size == 0`, the first value belongs at index `0`.

---

## Remove Invariant

For:

```text
[10,20,30,40]
remove(1)
```

must become logically:

```text
[10,30,40]
```

Elements after the removed position shift exactly one position left.

Number of shifted elements:

```text
size - index - 1
```

Removing the last element requires zero shifts.

Size decreases exactly once.

### Object-array note

For `Object[]`, the unused final reference should normally be cleared after removal to avoid unnecessarily retaining objects.

### Status

**Boundary reasoning improved.**

### Pending

Existing Dynamic Array implementation must still be verified/fixed against these invariants and tested.

---

# 12. JIT Bounds / Range-Check Elimination

Discussed:

```java
for (int i = 0; i < arr.length; i++) {
    sum += arr[i];
}
```

Java semantics require array accesses to be in bounds.

For sufficiently predictable loops, the JIT may prove the accesses safe and eliminate redundant checks.

Important distinction:

> Bounds-check elimination is compiler/JIT reasoning, not CPU caching.

Less predictable indexing such as:

```java
arr[indexes[i]]
```

may still require runtime bounds checks.

### Status

**Concept introduced and corrected.**

---

# 13. Backend / HLD Connection — Metrics Range Queries

Scenario:

```text
request count per completed minute
```

with large numbers of dashboard range queries.

Design instinct correctly identified:

```text
completed immutable buckets
→ preaggregate / cumulative representation
→ cheap range reads
```

Plain prefix/cumulative structures become problematic when historical buckets can be corrected because later cumulative values become stale.

If a historical bucket changes by:

```text
delta
```

all later cumulative entries may require that delta.

Naive repair:

```text
O(n)
```

---

# Mutable vs Immutable Data Architecture

Strong system-design insight established:

```text
Recent window
→ mutable
→ late events/corrections allowed

lateness window expires
        ↓

Finalize bucket
        ↓

Historical representation
→ immutable
→ preaggregated
→ read optimized
```

Important architecture principle:

> Different lifecycle stages of the same logical dataset can and often should use different data structures.

For workloads requiring frequent arbitrary updates and range queries, simple prefix sums may no longer be appropriate.

Future structures that can address this include:

* Fenwick Tree
* Segment Tree

These have **not yet been studied** and should not be pulled forward prematurely.

### Status

**Strong backend/system-design connection made.**

---

# Current Strengths

Evidence from Days 1–3:

## 1. Strong engineering intuition

Naturally connects algorithmic ideas to:

* caching
* backend data access
* immutable data
* streaming/late events
* metrics systems
* production trade-offs

## 2. Good cache-locality intuition

Understands:

* sequential memory access
* cache-line benefit
* random/scattered access penalties

## 3. Good ability to derive optimizations

Frequently identifies reusable state once the repeated work is made explicit.

## 4. Strong practical design thinking

Correctly identified mutable recent data versus immutable historical data as separate workload classes.

## 5. Improving invariant-based reasoning

Successfully used:

```text
leftSum = everything strictly before i
```

and:

```text
suffixProduct = everything strictly after i
```

to reason about correctness.

## 6. Complexity reasoning improving

Can derive:

```text
O(n²)
O(nq)
O(n + q)
O(n)
```

from the work being performed rather than pure memorization.

---

# Growth Areas

## 1. Exact Index Semantics — HIGH PRIORITY

Conceptual reasoning is often correct before the index arithmetic is.

There is still a tendency to mix:

```text
i
i - 1
i + 1
```

or switch between competing prefix definitions.

Required discipline:

Before writing any index formula, explicitly state:

```text
What exactly does this variable/index represent?
What elements are included?
What elements are excluded?
What is true before processing i?
What becomes true after processing i?
```

Do not memorize formulas before defining the invariant.

---

## 2. Optimization Progression — HIGH PRIORITY

There is sometimes a tendency to jump directly to a shortcut.

Required interview sequence:

```text
1. Brute force
2. Derive complexity
3. Identify repeated work
4. Introduce reusable state
5. Establish correctness/invariant
6. Optimize memory/state
7. Discuss trade-offs
```

Example from Day 3:

Do not immediately jump to:

```text
totalProduct / nums[i]
```

for Product Except Self.

Derive prefix/suffix first.

---

## 3. JVM Terminology Precision

Continue separating:

```text
CPU cache
code cache
hot code
HotSwap
JIT compilation
method inlining
bounds-check elimination
```

The performance intuition is generally good, but terminology must become Staff/interview precise.

---

## 4. Complexity With Multiple Independent Variables

Do not reduce:

```text
O(n + q)
```

to:

```text
O(n)
```

unless a relationship between `n` and `q` is established.

Always identify independent input dimensions.

---

## 5. Implementation Must Follow Reasoning

Conceptual solutions are progressing faster than implementation evidence.

The curriculum must continue requiring:

```text
reason
→ implement
→ test
→ edge cases
```

Do not mark an engineering lab complete based only on discussion.

---

# Engineering Deliverables — Current State

## Dynamic Array

### Reasoning

* [x] `add()` mechanics understood
* [x] growth boundary understood
* [x] valid-index invariant understood
* [x] remove shifting understood
* [x] remove-last optimization understood
* [x] size/capacity invariant understood

### Implementation / Verification

* [ ] Fix/verify append uses `elements[size]` before incrementing size
* [ ] Verify negative indexes fail
* [ ] Verify `index == size` fails for get/set/remove
* [ ] Verify remove shift boundaries
* [ ] Verify grow preserves all existing elements
* [ ] Verify size changes exactly once per operation
* [ ] Add first-insertion test
* [ ] Add growth-boundary test
* [ ] Add invalid-access tests
* [ ] Add remove-first test
* [ ] Add remove-middle test
* [ ] Add remove-last test
* [ ] Commit verified implementation/tests

Dynamic Array must **not** be considered fully complete until implementation and tests pass.

---

## Product of Array Except Self

* [x] Brute-force reasoning
* [x] `O(n²)` derivation
* [x] Prefix/suffix solution
* [x] `O(n)` derivation
* [x] Space optimization
* [x] Zero-case reasoning
* [ ] Implement in Java
* [ ] Add tests if selected as coding deliverable

---

## Pivot Index

* [x] Brute-force reasoning
* [x] Running-state optimization
* [x] Invariant
* [x] Correct dry run
* [x] Pseudocode
* [ ] Java implementation optional/pending

---

## JMH

* [x] Warm-up mental model
* [x] Measurement mental model
* [x] Benchmark contamination understood
* [x] Fork purpose introduced
* [ ] Actual JMH benchmark not yet created

---

# Patterns Learned So Far

## Complement Lookup

Question:

> What value do I need, and have I already seen it?

Typical structure:

```text
required = target - current
```

Typical data structure:

```text
HashMap
```

Example:

* Two Sum

---

## Membership Lookup

Question:

> Have I seen this value before?

Typical data structure:

```text
HashSet
```

Example:

* Contains Duplicate

---

## Running State

Question:

> Can everything needed for the next iteration be summarized in a small amount of state?

Examples:

* Running Sum
* Best Time to Buy and Sell Stock
* Pivot Index

---

## Stable Compaction

Question:

> Where should the next valid element be written?

Typical state:

```text
writeIndex
```

Example:

* Move Zeroes

---

## Prefix / Precomputation

Question:

> Am I repeatedly recalculating overlapping information that can be computed once?

Best suited for:

```text
many reads
+
stable/immutable source data
```

Examples:

* Range Sum Query
* cumulative metrics

---

## Prefix + Suffix Decomposition

Question:

> Can the result for index `i` be expressed using everything before `i` and everything after `i`?

Example:

* Product of Array Except Self

---

## Global Aggregate + Running Prefix

Question:

> If I know the total and everything before `i`, can I derive everything after `i`?

Example:

* Pivot Index

Typical relation:

```text
rightAggregate
=
totalAggregate
-
leftAggregate
-
current
```

when the aggregate operation permits this transformation.

---

# Core Invariants Learned

## Dynamic Array

```text
0 <= size <= capacity
```

Valid indexes:

```text
0 <= index < size
```

Append location:

```text
elements[size]
```

---

## Prefix Sum

```text
prefix[i]
=
aggregate of first i elements
```

---

## Product Except Self — Forward Pass

Before processing `i`:

```text
prefixProduct
=
product of everything strictly before i
```

---

## Product Except Self — Reverse Pass

Before processing `i`:

```text
suffixProduct
=
product of everything strictly after i
```

---

## Pivot Index

Before processing `i`:

```text
leftSum
=
sum of everything strictly before i
```

---

# Interview Response Framework

For every coding problem:

## 1. Requirements

What exactly is requested?

## 2. Clarifications

Ask only questions that could change the solution.

## 3. Assumptions

State confirmed guarantees.

## 4. Brute Force

Give the simplest correct solution.

## 5. Complexity

Derive complexity from actual work.

## 6. Bottleneck

Identify repeated computation or unnecessary state.

## 7. Better Solution

Derive the optimization.

## 8. Invariant

State precisely what remains true.

## 9. Dry Run

Use:

* one normal case
* one meaningful edge case

## 10. Final Complexity

State time and auxiliary-space complexity.

## 11. Trade-offs

Discuss when relevant:

* memory
* mutability
* preprocessing
* data movement
* cache behavior
* update cost
* maintainability
* production workload shape

---

# Day 3 Exit Assessment

## Achieved

* [x] Distinguish Big-O from actual runtime
* [x] Explain basic JIT method inlining
* [x] Explain why JMH needs warm-up
* [x] Recognize repeated range work
* [x] Derive prefix sums rather than only memorize formula
* [x] Explain preprocessing/query trade-off
* [x] Derive Product of Array Except Self using prefix/suffix
* [x] Reduce Product Except Self auxiliary storage
* [x] Reason about zero cases
* [x] Use invariants for left/right state
* [x] Solve Pivot Index using total + running state
* [x] Explain mutable-vs-immutable implications of precomputation
* [x] Connect cumulative aggregation to backend metrics systems
* [x] Reinforce Dynamic Array boundary invariants

## Not Yet Proven Through Implementation

* [ ] Dynamic Array quality-gate tests
* [ ] Product Except Self Java implementation
* [ ] JMH benchmark implementation

These should be carried forward as engineering evidence, not necessarily block Day 4 unless the next curriculum requires them.

---

# Next Action

Generate **Day 4** using:

```text
cirriculum/MASTER_CURRICULUM.md
+
registry.md
+
Day 3 evidence
```

Day 4 should continue Phase 1 rather than restarting or repeating Days 1–3.

It should preserve the master-curriculum philosophy:

```text
Learn
→ Explain
→ Implement
→ Test
→ Benchmark where meaningful
→ LLD/HLD connection
→ Production connection
```

## Mandatory Day 4 Carry-Forward

Day 4 should continue reinforcing:

1. exact index/invariant reasoning
2. brute-force-before-optimization discipline
3. independent-variable complexity reasoning
4. JVM terminology precision
5. implementation/test evidence

Do **not** spend another full session reteaching prefix sums unless Day 4 exposes a concrete gap.

Prefix/suffix concepts should instead be reinforced through spaced retrieval or one transfer problem.

---

# Immediate Engineering Follow-up

Before Dynamic Array is declared complete, verify the implementation against:

```text
0 <= size <= capacity
0 <= index < size
append at elements[size]
remove shifts size-index-1 elements
size changes exactly once
```

Required tests:

```text
first insertion
growth boundary
get(-1)
get(size)
remove first
remove middle
remove last
```

---

# Registry Update Rule

At the end of every future day, update only state that changed.

Do not turn this file into a transcript.

For each day capture:

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
