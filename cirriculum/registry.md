## Day 12 — Phase-1 Exit Evidence: Prefix Transfer, Rate Limiter, Memory Fluency, Independent HLD & Debugging

**Status:** Completed
**Phase status:** Phase 1 exit criteria sufficiently demonstrated.
**Next phase:** Begin Phase 2 with Trees / BST foundations while continuing spaced retrieval of Phase-1 concepts.

---

### Rate Limiter — Fixed Window Automated Evidence

Revisited the Fixed-Window Rate Limiter with emphasis on proving invariants rather than redesigning the algorithm.

#### Core behavior demonstrated

For:

```text
limit = 3
windowSize = fixed interval
```

Correctly reasoned:

```text
request 1 → accepted
request 2 → accepted
request 3 → accepted
request 4 → rejected
```

Important invariant retained:

> The stored count represents accepted requests in the current window and must never exceed the configured threshold.

Therefore:

```text
rejected request
→ count remains unchanged
```

#### Window boundary reasoning

Correctly reasoned that fixed windows are identified through integer division:

```text
windowId = epochTime / windowSize
```

For a 10-second conceptual window:

```text
t = 0  → window 0
t = 9  → window 0
t = 10 → window 1
t = 19 → window 1
t = 20 → window 2
```

Understood that an exact boundary starts a fresh window and resets the accepted count for that customer.

#### Per-customer isolation

Correctly established that state must be scoped using a rate-limit key such as:

```text
userId
tenantId
orgId
API key
```

Different customers can be in the same `windowId` while maintaining independent counts.

Preferred state shape:

```text
rateLimitKey
→ currentWindowId
→ acceptedCount
```

rather than storing historical counts for every window.

#### Invalid configuration

Established fail-fast behavior:

```text
limit <= 0
windowSize <= 0
→ reject during construction/configuration
```

unless zero-limit semantics are explicitly part of the product requirement.

#### Testing observations

Discussed deterministic testing of time-dependent logic.

Learned that:

```java
Instant.now().plus(...)
```

does not mutate the system clock because `Instant` is immutable.

Explored:

* static mocking of `Instant.now()`
* injectable `Clock`
* exposing a controllable time seam for tests

Representative automated tests were implemented/exercised for the limiter, including threshold behavior, rejection count preservation, and window reset behavior.

Full exhaustive test-suite proof was intentionally not required before proceeding.

**Current limiter evidence:** Fixed-window invariants are understood and representative automated evidence exists. Phase-1 limiter quality gate is sufficiently closed.

---

### DSA — Count Subarrays Whose Sum Is Divisible by K

Problem used for spaced transfer:

```text
nums = [4,5,0,-2,-3,1]
k = 5

answer = 7
```

#### Brute force

Derived independently:

```text
for each start
    runningSum = 0
    for each end >= start
        runningSum += nums[end]

        if runningSum % k == 0
            count++
```

Complexity:

```text
Time  → O(n²)
Space → O(1)
```

#### Prefix-sum transfer

For subarray:

```text
(j + 1) ... i
```

the sum is:

```text
prefix[i] - prefix[j]
```

Requirement:

```text
(prefix[i] - prefix[j]) % k == 0
```

Derived:

```text
prefix[i] % k == prefix[j] % k
```

Therefore the repeated question becomes:

> How many earlier prefix boundaries had the same normalized remainder as the current prefix?

This successfully transferred the earlier Subarray Sum Equals K reasoning.

Comparison:

```text
Subarray Sum Equals K
→ lookup currentPrefix - target

Subarrays Divisible by K
→ lookup frequency of current normalized remainder
```

#### Why frequency map rather than Set

Multiplicity matters.

If the current remainder has appeared at three earlier prefix boundaries:

```text
3 earlier matching boundaries
→ 3 different valid subarrays ending here
```

Therefore required state:

```text
remainder → occurrence count
```

#### Empty-prefix boundary

Understood:

```text
0 → 1
```

as the empty prefix before index `0`.

This enables counting subarrays beginning at the first array element whenever:

```text
currentPrefix % k == 0
```

Clarified that the prefix sum itself does not have to be zero; its remainder must be zero.

#### Zero is divisible by K

Explicitly reinforced:

```text
0 % k = 0
```

Therefore zero-sum subarrays are valid.

For the example, valid subarrays included:

```text
[5]
[5,0]
[0]
[-2,-3]
[0,-2,-3]
[5,0,-2,-3]
[4,5,0,-2,-3,1]
```

#### Negative remainder normalization

Identified a correctness issue with Java `%`.

For example:

```text
-2 % 5 = -2
```

but mathematically:

```text
-2 ≡ 3 (mod 5)
```

`Math.abs()` was correctly rejected as a normalization strategy.

Preferred Java solution:

```java
Math.floorMod(prefixSum, k)
```

Equivalent conceptually to:

```text
((prefix % k) + k) % k
```

#### Smallest sufficient representation

Initial implementation built a complete `prefixSum[]`.

Recognized that this is unnecessary because only the current prefix sum and historical remainder frequencies are required.

Preferred state:

```text
currentPrefix
currentRemainder
remainderFrequency
answer
```

Time:

```text
O(n) expected
```

Space:

```text
O(min(n, k))
```

conceptually when normalized remainder classes are used.

#### Sliding-window insight

Initially recalled that negative values break monotonic sum behavior.

Then independently identified the stronger insight:

> Divisibility itself is not a monotonic predicate, even when all input numbers are positive.

Example:

```text
[2]          → sum 2  → invalid
[2,3]        → sum 5  → valid
[2,3,1]      → sum 6  → invalid
[2,3,1,4]    → sum 10 → valid
```

Therefore:

```text
invalid → valid → invalid → valid
```

can occur as the window expands.

A conventional sliding-window decision rule is therefore unavailable.

**Current DSA evidence:** Prefix-sum + frequency reasoning successfully transferred to a materially different condition without being handed the implementation. Phase-1 transfer requirement satisfied.

---

### Java / OS Memory — Arithmetic Fluency

Used:

```text
pageSize = 4096 bytes
```

Correctly applied:

```text
virtualPageNumber = address / pageSize
offset            = address % pageSize
```

Examples:

```text
address 4095
→ page 0
→ offset 4095

address 4096
→ page 1
→ offset 0

address 8200
→ page 2
→ offset 8

address 16383
→ page 3
→ offset 4095

address 16384
→ page 4
→ offset 0
```

#### Translation exercise

Given:

```text
virtual page 2
→ physical frame 17
offset = 8
pageSize = 4096
```

Derived:

```text
physicalAddress
= 17 × 4096 + 8
= 69640
```

Minor arithmetic slip occurred initially by mixing frame number and offset, then was corrected immediately.

#### Offset preservation

Correctly explained:

* virtual pages and physical frames are the same size
* translation changes which physical frame backs a virtual page
* the position of the byte within the page does not change

Invariant:

> Virtual-to-physical translation changes the page/frame number while preserving the page offset.

#### Why virtual memory exists

Independently identified:

* process isolation
* protection/security
* abstraction from physical-memory placement
* non-contiguous physical allocation
* ability to move/map pages independently
* support for pages that are not currently resident in RAM

Precision correction:

* Java GC compaction and OS physical-page remapping are different abstraction layers.
* GC may move Java objects within the JVM/process address space.
* OS/MMU manages virtual-page-to-physical-frame translation.

#### Cache miss vs page fault

Correct conceptual distinction retained:

```text
CPU cache miss
→ CPU/cache-hierarchy concern

page fault
→ virtual-memory / OS concern
```

Precision refinements:

* CPU cache lookup/refill is primarily hardware-managed, not performed by the OS.
* A cache miss may be satisfied by L2/L3 or RAM.
* A page fault does not always imply disk access.
* Minor faults may be resolved without storage I/O.
* Major faults may require reading from backing storage.

#### Multiple abstraction layers

Correctly explained that the same Java-heap byte can simultaneously be:

```text
part of a JVM heap object
part of a process virtual page
backed by a physical RAM frame
present in a CPU cache line
```

These are not mutually exclusive states; they are different abstraction layers.

**Current memory evidence:** Page-number/offset arithmetic is now fluent enough for Phase-1 exit. Continue tightening terminology around JVM vs OS/MMU vs CPU hardware during spaced retrieval.

---

### HLD — Feature Flag / Kill Switch Service V1

Designed a materially different HLD with substantially less prompting than URL Shortener.

#### Functional requirements identified

Feature evaluation may depend on:

* environment
* org / tenant
* pod
* application/version
* effective time
* specific customers
* optional percentage rollout
* global kill switch

Core operation:

```text
flag + evaluation context
→ true / false
```

Also identified administrative configuration/update behavior.

#### Non-functional requirements

Identified:

* low evaluation latency
* high availability
* bounded configuration staleness
* visibility from all pods
* scalable high-read workload

Refined the distinction between:

```text
normal rollout freshness
vs
emergency kill-switch freshness
```

and recognized that the architecture should be driven by the freshness SLO.

#### Rule engine

Proposed a rule/formula engine supporting a constrained DSL such as:

```text
EQUALS
NOT_EQUALS
IN
NOT_IN
AND
OR
comparison operators
```

Configuration should be validated before becoming active.

Write path concept:

```text
configuration
→ syntax validation
→ semantic validation
→ normalize/compile
→ durable persistence
→ expose/publish new version
```

#### Data model

Considered:

```text
FeatureFlag
→ one-to-many Rules
```

and rejected a schema such as:

```text
rule1
rule2
...
rule50
```

as rigid and poorly extensible.

Preferred approaches:

* normalized rule table
* validated structured JSON for hierarchical expressions

Possible flag state:

```text
flagKey
environment
enabled
version
updatedAt
compiledRules
fallbackPolicy
```

#### Cache architecture

Clarified two separate cache concerns:

```text
Application-side cache
→ avoid repeated network calls

Feature Flag Service cache
→ avoid repeated DB/rule reconstruction
```

Initially considered caching final boolean decisions.

Then independently identified cache-cardinality explosion when the decision depends on:

```text
flagKey
orgId
pod
environment
version
other context
```

Preferred design:

> Cache compiled feature configuration/rules and evaluate against runtime context rather than caching every contextual true/false decision.

This avoids:

```text
(flag × org × pod × version × environment ...)
```

cache explosion.

#### Runtime evaluation

Preferred flow:

```text
Application
→ cached compiled flag configuration
→ local/in-memory rule evaluation
→ true / false
```

or equivalent short-lived application-side caching depending on system boundaries.

The key architectural insight was:

> Avoid repeated database access while preventing contextual decision-cache cardinality explosion.

#### Staleness / propagation trade-off

Explicitly reasoned about:

```text
long TTL
→ simpler architecture
→ greater bounded staleness

push invalidation
→ faster propagation
→ additional distributed-system complexity
```

Defended TTL-based convergence when the product SLO allows it rather than introducing messaging technology prematurely.

This was a strong example of architecture being driven by requirements rather than by technology selection.

#### Failure behavior

Correctly rejected a universal:

```text
service unavailable → false
```

policy.

Different flags may require different behavior.

Preferred configurable failure policy:

```text
FAIL_OPEN
FAIL_CLOSED
USE_LAST_KNOWN_GOOD
```

Recognized that a previously valid cached configuration should not automatically be discarded during a temporary control-plane outage.

#### Percentage rollout

Discussed deterministic rollout:

```text
hash(stableEntityId + flagKey)
→ bucket
→ compare with rollout percentage
```

ensuring the same entity does not randomly alternate between enabled and disabled.

#### Observability

Useful evaluation context identified:

```text
flagKey
orgId
pod
environment
configVersion
evaluationResult
matchedRule
fallbackUsed
evaluationLatency
```

Recognized that `userId` / `orgId` should be used carefully in logs and generally avoided as high-cardinality metric dimensions.

Aggregate metrics include:

```text
evaluation latency
evaluation count
evaluation errors
fallback count
cache hit rate
refresh failures
stale-config age
```

Important distinction:

> Request latency does not measure configuration propagation latency.

Propagation should instead compare:

```text
configuration committedAt
vs
first observation of new version by evaluators/pods
```

#### HLD independence assessment

**Level:** Strong / Medium-high independence.

Driven mostly independently:

* functional requirements
* contextual rules
* data-model alternatives
* formula engine
* validation
* caching strategy
* cardinality concerns
* failure behavior
* observability

Prompting was mainly needed around:

* freshness vs caching
* last-known-good semantics
* cache cardinality
* propagation-specific metrics

**Current HLD evidence:** Additional bounded HLD successfully derived with materially less prompting. Phase-1 HLD exit evidence satisfied.

---

### Production Debugging — Evidence Before Root Cause

Incident characteristics:

```text
traffic unchanged
CPU nearly unchanged
memory normal
errors increased
p50 nearly unchanged
p95 moderately worse
p99 dramatically worse
```

Correctly recognized that:

```text
normal p50 + severe p99 increase
→ tail/minority path is degraded
```

Precision correction:

`p99 = 2.4s` does not mean 99% of requests are failing.

It means approximately 99% complete at or below that latency threshold, while the slowest tail is significantly degraded.

Actual failures are represented separately by the error-rate metric.

#### Investigation sequence

Improved debugging flow:

```text
Observation
↓
segment endpoint / cohort
↓
identify downstream span consuming time
↓
form hypothesis
↓
collect supporting evidence
↓
collect rejecting evidence
↓
declare root cause only when validated
↓
mitigate
```

#### Redis / Postgres example

Given:

```text
fast request:
Redis    → 3 ms
Postgres → not called

slow request:
Redis    → 3 ms
Postgres → 1.8–2.1 sec

Redis hit rate:
96% → 91%
```

Correctly localized the slow path to Postgres fallback.

Initially moved toward an index hypothesis.

Refined reasoning:

> Postgres is where time is being spent, but this does not yet prove an index regression.

Required further evidence:

```text
specific query latency
execution plan
rows scanned
index usage
DB CPU / IOPS
locks
connection wait
pool utilization
recent deployment/query/schema change
```

#### Cache-hit-rate interpretation

Important quantitative insight:

```text
96% hit rate → 4% misses
91% hit rate → 9% misses
```

Therefore DB-bound requests changed by:

```text
9 / 4 = 2.25×
```

A 5-percentage-point reduction in hit rate therefore more than doubled the cache-miss traffic.

#### Connection-pool diagnosis

Scenario:

```text
query execution ≈ 60 ms
connection acquisition ≈ 1.8 sec
```

Correctly concluded that query execution is not the bottleneck.

Likely area:

```text
connection-pool saturation / connection availability
```

Next evidence identified:

* active connections
* idle connections
* waiting threads
* connection acquisition latency
* connection hold time
* long transactions
* connection leaks
* applications/endpoints monopolizing connections

Correctly considered increasing pool size only as a possibility, not an immediate fix.

Refined principle:

> Increasing the application connection pool without checking database capacity can worsen the incident.

Potential causes distinguished:

* increased cache misses
* long-held transactions
* leaked connections
* downstream DB slowdown
* legitimately undersized pool

**Current debugging evidence:** Meaningful improvement from plausible-bottleneck guessing toward evidence-driven narrowing. Phase-1 debugging exit evidence satisfied.

---

## Day 12 Overall Assessment

### Demonstrated strengths

* Prefix-sum reasoning transferred successfully to a different algebraic condition.
* Strong ability to reason about frequency/multiplicity rather than memorize implementations.
* Caught an additional algorithmic insight independently: divisibility is non-monotonic even with positive inputs.
* Virtual-memory page/offset arithmetic improved materially.
* HLD reasoning increasingly starts from requirements and trade-offs rather than technologies.
* Strong instinct around cache cardinality and local rule evaluation.
* Production-debugging flow became substantially more evidence-driven.
* Comfortable challenging assumptions and defending simpler designs when the SLO supports them.

### Precision areas to continue improving

#### 1. Mathematical wording

Preserve exact equations when speaking:

```text
Subarray Sum Equals K:
earlierPrefix = currentPrefix - target
```

Avoid verbally reversing the subtraction even when the mental model is correct.

#### 2. JVM / OS / Hardware ownership

Continue distinguishing:

```text
JVM / GC
OS / virtual memory
MMU / page tables
CPU cache hierarchy
```

In particular:

* CPU cache misses are primarily hardware-managed.
* Not every page fault requires disk access.

#### 3. Root-cause language

Continue enforcing:

```text
hypothesis ≠ root cause
```

until validating evidence exists.

#### 4. Testing time-dependent code

Continue using deterministic time seams when exact boundary behavior matters.

---

## Phase-1 Exit Decision

### Evidence

```text
Fixed-Window Rate Limiter reasoning/testing   ✅
Prefix/frequency spaced transfer              ✅
Binary-search boundary retrieval              ✅
Array/hash foundational transfer              ✅
Linked structures/LRU retrieval               ✅
Java reference semantics                      ✅
Virtual-memory model + arithmetic              ✅
Additional bounded HLD with less prompting    ✅
Evidence-driven production debugging          ✅
```

### Decision

**Phase 1 is complete.**

Minor wording/precision imperfections remain, but the core reasoning can now be reconstructed with limited prompting.

Do not spend another full day polishing Phase-1 topics.

Continue them through spaced retrieval while advancing the curriculum.

---

# Next Action

Begin **Day 13 — Phase 2**.

Primary direction:

```text
Trees / Binary Trees / BST foundations
```

Continue Phase-1 concepts only as short spaced-retrieval exercises.

Priorities for Day 13:

1. Establish tree vocabulary and structural invariants.
2. Understand recursive tree reasoning before memorizing traversal templates.
3. Build binary-tree/BST intuition from first principles.
4. Introduce traversal patterns incrementally.
5. Continue short DSA retrieval from Phase 1.
6. Preserve ongoing HLD and production-debugging practice.

Do not yet jump ahead into:

```text
graphs
advanced DP
distributed rate limiter
token bucket
JMM
GC deep dive
advanced page replacement
```

unless explicitly scheduled by the master curriculum.
