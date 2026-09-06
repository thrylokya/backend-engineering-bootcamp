# Day 12 — Phase-1 Exit Evidence: Prefix Transfer, Rate-Limiter Proof, Memory Fluency & Independent HLD

> **Duration:** ~3 hours  
> **Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory  
> **Primary Goal:** Generate the missing evidence required before Phase 2, rather than adding new breadth prematurely.  
> **DSA Theme:** Spaced transfer of prefix-sum/frequency reasoning to a different problem.  
> **Engineering Theme:** Prove the Fixed-Window Rate Limiter with automated tests.  
> **Memory Theme:** Make page-number/offset reasoning fluent.  
> **HLD Theme:** Drive a bounded Feature Flag / Kill Switch Service V1 with materially less prompting.  
> **Production Theme:** Move from plausible hypotheses to explicit evidence chains.

---

# Why Day 12 Looks Like This

Day 11 is complete conceptually.

The registry explicitly recommends four pieces of evidence before Phase 2:

```text
1. Spaced retrieval of prefix-sum/frequency-map reasoning on a different problem.
2. Automated Fixed-Window Rate Limiter tests demonstrated.
3. One additional HLD problem derived with materially less prompting.
4. Production-debugging evidence where hypotheses are validated before fixes are proposed.
```

Day 11 also identified a smaller precision gap:

```text
page-number / page-offset arithmetic
```

Therefore Day 12 is a **Phase-1 evidence day**, not another broad teaching day.

If the exit evidence is strong, Day 13 can begin Phase 2.

---

# Schedule Overview

```text
Session 1 — Rate Limiter Automated Proof           20 min
Session 2 — DSA Transfer: Subarrays Divisible by K 45 min
Session 3 — Memory Arithmetic Fluency              20 min
Session 4 — Independent HLD: Feature Flag V1       50 min
Session 5 — Evidence-Driven Production Debugging   30 min
Session 6 — Phase-1 Exit Retrieval                 15 min
                                                   ------
                                                   180 min
```

---

# Session 1 — Fixed-Window Rate Limiter: Automated Proof

**20 minutes**

Do not re-teach the design. Close the evidence gap.

Use:

```text
limit = 3
windowSize = 10 seconds
```

Required automated tests:

1. first request allowed
2. requests up to limit allowed
3. limit + 1 rejected
4. rejection does not mutate accepted count
5. exact boundary creates a new window
6. per-customer isolation
7. behavior across multiple windows
8. invalid configuration fails fast

For windows:

```text
[0,10)
[10,20)
```

verify:

```text
t = 9  → old window
t = 10 → new window
```

Evidence required:

```text
tests executed
+
tests passing
```

If a test fails:

```text
observed behavior
→ violated invariant
→ fix
→ rerun
```

When green:

```text
Fixed-Window Rate Limiter V1
→ Phase-1 LLD evidence complete
```

---

# Session 2 — DSA Transfer: Count Subarrays Whose Sum Is Divisible by K

**45 minutes**

Problem:

> Given an integer array and positive integer `k`, return the number of contiguous subarrays whose sum is divisible by `k`.

Example:

```text
nums = [4,5,0,-2,-3,1]
k = 5

answer = 7
```

Numbers may be negative.

Do not name the pattern initially.

## Step 1 — Objective

We need:

```text
count of contiguous subarrays
```

where:

```text
subarraySum % k == 0
```

## Step 2 — Brute Force

```text
for each start
    runningSum = 0
    for each end
        runningSum += nums[end]
        if runningSum % k == 0
            count++
```

Complexity:

```text
O(n²)
```

## Step 3 — Algebra

For subarray:

```text
(j + 1) ... i
```

sum is:

```text
prefix[i] - prefix[j]
```

We require:

```text
(prefix[i] - prefix[j]) % k == 0
```

which is true when:

```text
prefix[i] % k == prefix[j] % k
```

So the repeated question becomes:

> How many earlier prefix sums had the same remainder as the current prefix?

## Step 4 — Minimum State

Need:

```text
currentPrefix
currentRemainder
frequency of prior remainders
answer
```

Why frequency state rather than Set?

Because multiplicity creates multiple valid subarrays.

## Step 5 — Initial State

Represent the empty prefix:

```text
remainderFrequency[0] = 1
```

Explain what boundary this represents.

## Step 6 — Negative Remainders

In Java, normalize remainder classes:

```text
((prefix % k) + k) % k
```

or equivalent.

Understand why mathematical remainder classes must map to the same bucket.

## Invariant

> Before recording the current remainder, the frequency structure contains the number of earlier prefix boundaries for each normalized remainder.

Then:

```text
same prior remainder
→ difference divisible by k
→ valid subarray
```

Required tests:

```text
[4,5,0,-2,-3,1], k=5 → 7
[5], k=5              → 1
[1,2,3], k=3          → 3
[0,0], k=5            → 3
[], k=5               → 0
```

Also include one negative-prefix case.

Complexity:

```text
Time  → O(n) expected
Space → O(min(n, k)) conceptually with a map
```

Transfer questions:

1. What changed from Subarray Sum Equals K?
2. What stayed the same?
3. Why is lookup now based on same remainder?
4. Why does multiplicity still matter?
5. Why is sliding window unsafe with negative values?

Success means deriving:

```text
range condition
↓
prefix difference
↓
algebraic transformation
↓
prior-prefix property lookup
↓
frequency state
```

without being handed the technique.

---

# Session 3 — Virtual-Memory Arithmetic Fluency

**20 minutes**

No new OS theory.

Use:

```text
pageSize = 4096 bytes
```

For each address:

```text
virtualPageNumber = address / pageSize
offset            = address % pageSize
```

Drills:

```text
address = 0
address = 4095
address = 4096
address = 8200
address = 16383
address = 16384
```

Translation exercise:

```text
virtual page 2
→ physical frame 17

offset = 8
```

Physical byte address:

```text
17 * 4096 + 8
```

Key invariant:

> Translation changes the page/frame number while preserving the offset within the page.

Explain:

1. Why is the offset preserved?
2. Why do processes use virtual addresses?
3. Cache miss vs page fault?
4. Can Java-heap bytes simultaneously be part of a virtual page, backed by RAM, and present in CPU cache?

Expected:

```text
yes
```

Different abstraction layers.

---

# Session 4 — Independent HLD: Feature Flag / Kill Switch Service V1

**50 minutes**

This HLD should be less guided than URL Shortener.

You drive the first half.

Problem:

> Design a service that allows applications to ask whether feature X is enabled for a given context, while authorized operators can update the configuration.

Typical uses:

```text
feature rollout
kill switch
percentage rollout
environment-specific enablement
```

## Required Opening

Drive these without waiting for prompts:

```text
1. Functional requirements
2. Non-functional requirements
3. API
4. Data model
5. source of truth
6. read path
7. write path
8. freshness/consistency expectation
9. failure behavior
10. observability
```

Do not start with Redis/Kafka.

## Candidate V1 Scope

Decide whether to support:

```text
create/update flag
boolean evaluation
environment
optional percentage rollout
```

Explicitly defer unnecessary complexity.

## Key NFR Tension

Feature evaluation is:

```text
read-heavy
latency-sensitive
```

A kill switch also needs:

```text
fast propagation
```

Therefore a real trade-off exists:

```text
aggressive caching
vs
freshness
```

## Minimal State

Possible model:

```text
flag_key
environment
enabled
version
updated_at
rollout_percentage?
```

## Source of Truth

Choose a durable authoritative store.

Invariant:

> A flag update acknowledged as successful must be durably represented by the source of truth.

## Read Path

Possible V1:

```text
application
↓
cache
↓
authoritative store on miss
```

But justify caching from NFRs.

## Write / Propagation Path

Reason about:

```text
operator update
↓
durable write
↓
readers eventually observe new version
```

Ask:

```text
How stale is acceptable for normal rollout?
How stale is acceptable for emergency kill switch?
```

## Optional Percentage Rollout

If included, use deterministic assignment conceptually:

```text
hash(stableEntityId + flagKey)
→ bucket
→ compare with rollout percentage
```

Same entity should not randomly flip between requests.

## Failure Scenarios

Reason about:

- authoritative DB unavailable
- cache unavailable
- DB update succeeds but propagation is delayed
- bad configuration rollout

## Observability

Identify:

```text
evaluation latency
cache hit rate
config version
propagation delay
update success/failure
fallback rate
stale-read age
flag evaluation volume
```

Useful kill-switch signal:

```text
time from durable update
to readers observing new version
```

## Independence Score

Strong:
- requirements, NFRs, API, data, freshness, failure, observability all driven mostly independently

Medium:
- prompting needed mainly for freshness/failure/observability

Weak:
- technology chosen before product guarantees were defined

Record the actual level.

---

# Session 5 — Production Debugging: Evidence Before Root Cause

**30 minutes**

Incident:

```text
request rate: unchanged
CPU:          45% → 48%
memory:       normal
errors:       0.2% → 1.1%

p50:           45ms → 47ms
p95:          120ms → 170ms
p99:          280ms → 2.4s
```

Dependencies:

```text
Redis
PostgreSQL
external Profile API
```

Required format for each hypothesis:

```text
Observation
↓
Hypothesis
↓
Evidence that supports it
↓
Evidence that rejects it
↓
Only then: mitigation
```

## First Step

Segment before guessing:

```text
which endpoint?
which downstream span?
which AZ/instance group?
which request cohort?
```

## Hypothesis A — Redis

Support:

```text
Redis latency ↑
timeouts ↑
hit-rate change
slow traces involve Redis
```

Reject:

```text
Redis metrics normal
slow traces do not spend time there
```

## Hypothesis B — DB Query Regression

Support:

```text
specific query latency ↑
DB QPS/locks/connection waits ↑
query plan changed
slow traces point to SQL
```

Reject:

```text
DB stable
slow traces do not spend time in DB
```

## Hypothesis C — External API Tail Latency

Support:

```text
external span p99 ↑
timeouts/retries ↑
only endpoints using it affected
```

Reject:

```text
dependency latency stable
affected endpoint does not call it
```

Then inspect deployment changes, but remember:

```text
correlation != causation
```

Preferred phrasing:

> If traces show X, I would mitigate Y. Otherwise I would reject that hypothesis and continue narrowing.

---

# Session 6 — Phase-1 Exit Retrieval

**15 minutes**

No notes.

## DSA

1. Why did Subarray Sum Equals K need prefix frequencies?
2. For divisible-by-K subarrays, what replaces `prefix - target`?
3. Why does `0 → 1` still exist?
4. Why is Set insufficient?
5. When is sliding window unsafe?

## Structures

6. Stack?
7. Queue?
8. Deque?
9. Why does LRU need lookup + recency state?
10. State the LRU agreement invariant.

## Java / Memory

11. Primitive vs reference?
12. What does `Person[]` store?
13. Stack frame vs heap object?
14. JVM heap vs virtual memory?
15. Virtual page vs physical frame?
16. Cache miss vs page fault?

## LLD / HLD

17. State the Fixed-Window Rate Limiter invariant.
18. Why is a local limiter not global?
19. What makes feature-flag reads difficult despite simple key/value data?
20. What freshness tension does a kill switch create?

## Debugging

21. What separates hypothesis from root cause?
22. What evidence is required before blaming a dependency?

---

# Day 12 Deliverables

## Rate Limiter

- [ ] automated tests executed
- [ ] required tests pass
- [ ] bugs, if any, mapped to invariant violations
- [ ] Phase-1 limiter quality gate closed

## DSA

- [ ] brute force derived
- [ ] divisibility algebra derived
- [ ] prior-remainder frequency state derived
- [ ] `0 → 1` explained
- [ ] negative remainder normalization understood
- [ ] implementation completed
- [ ] tests pass
- [ ] O(n) expected reasoning stated
- [ ] transfer achieved without naming pattern first

## Memory

- [ ] page/offset arithmetic correct
- [ ] offset preservation explained
- [ ] cache miss vs page fault explained
- [ ] JVM/OS/hardware layers kept separate

## HLD

- [ ] Feature Flag V1 driven mostly independently
- [ ] requirements and NFRs separated
- [ ] source of truth identified
- [ ] read path derived
- [ ] write/propagation path derived
- [ ] freshness requirement discussed
- [ ] deterministic rollout reasoned if included
- [ ] failures discussed
- [ ] observability discussed
- [ ] prompting level recorded honestly

## Production Debugging

- [ ] endpoint/cohort segmentation first
- [ ] 3+ hypotheses considered
- [ ] supporting evidence stated
- [ ] rejecting evidence stated
- [ ] mitigation proposed conditionally
- [ ] no unsupported root-cause declaration

---

# Phase-1 Exit Decision

Recommend Phase 2 if evidence shows:

```text
Rate Limiter automated evidence     ✅
prefix/frequency spaced transfer    ✅
binary-search boundary retrieval    ✅
array/hash foundational transfer    ✅
linked structures/LRU retrieval     ✅
Java reference semantics            ✅
memory-layer explanation            ✅
bounded HLD with less prompting     ✅
evidence-driven debugging           ✅
```

Minor imperfections are acceptable.

The real question:

> Can the reasoning be reconstructed with limited prompting?

If yes:

```text
Day 13
→ begin Phase 2
→ Trees / BST foundations
```

while continuing Phase-1 spaced retrieval.

If not:

```text
Day 13
→ targeted repair of exact remaining gaps
```

---

# Intentionally Deferred

Do not add today:

- Tree/BST implementation
- heap / PriorityQueue
- graphs
- advanced DP
- Redis/distributed Rate Limiter
- token bucket
- rolling-window implementation
- Java Memory Model
- GC deep dive
- TLB internals
- page replacement
- distributed feature-flag consensus
- multi-region propagation
- Kafka-based config distribution

---

# Day 12 Exact Starting Action

Start with:

> **Run the Fixed-Window Rate Limiter automated tests. Which tests prove the invariant at the threshold, after rejection, at the exact window boundary, and across different customers?**

Once green:

> **Given integers that may be positive, zero, or negative, count contiguous subarrays whose sum is divisible by `k`. Start from brute force. What repeated work do you notice?**

Do not name the optimized technique.

---

# Registry Update Requirements

At the end of Day 12, capture only demonstrated evidence:

1. Rate Limiter automated test output/status
2. Rate Limiter bugs fixed, if any
3. prefix/frequency spaced-transfer performance
4. divisibility/remainder reasoning precision
5. DSA implementation/test evidence
6. memory arithmetic accuracy
7. cache-miss vs page-fault precision
8. Feature Flag HLD prompting level
9. Feature Flag requirements/NFR reasoning
10. freshness/consistency reasoning
11. production-debugging hypothesis discipline
12. exact remaining Phase-1 gaps
13. explicit Phase-2 readiness decision
14. exact Day 13 starting action

Do not mark Phase 1 complete because Day 12 was completed.
Mark it complete only if the exit evidence supports that conclusion.
