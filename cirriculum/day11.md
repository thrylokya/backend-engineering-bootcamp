# Day 11 — Phase-1 Consolidation, Prefix-Sum Transfer, Memory Bridge & Bounded HLD

> **Duration:** ~3 hours  
> **Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory  
> **Primary DSA Theme:** Transfer array/hash reasoning to prefix-sum + frequency state  
> **Primary Systems Theme:** Finish the virtual-vs-physical-memory bridge  
> **Primary HLD Theme:** Bounded end-to-end design — URL Shortener V1  
> **Engineering Theme:** Close the Fixed-Window Rate Limiter test quality gate

---

# Why Day 11 Looks Like This

Day 10 completed substantial Phase-1 material:

- lower-bound consolidation
- first/last occurrence
- count occurrences
- Search Insert Position
- First Bad Version
- primitive vs reference semantics
- aliasing
- stack frames vs heap objects
- primitive arrays vs reference arrays
- object headers and fields
- padding/alignment
- CPU-cache locality
- Fixed-Window Rate Limiter V1 implementation

The registry explicitly says the Rate Limiter implementation is complete but automated tests remain pending. It also asks Day 11 to briefly close those tests, continue progression, and increase HLD depth.

---

# Schedule Overview

```text
Session 1 — Rate Limiter Test Closure          20 min
Session 2 — DSA Transfer: Subarray Sum = K     45 min
Session 3 — Virtual vs Physical Memory          30 min
Session 4 — HLD: URL Shortener V1               50 min
Session 5 — Production Debugging                20 min
Session 6 — Phase-1 Retrieval / Exit Check       15 min
                                                ------
                                                180 min
```

---

# Session 1 — Fixed-Window Rate Limiter Quality Gate

**20 minutes**

Do not re-teach the Rate Limiter.

Use:

```text
limit = 3
windowSize = 10 seconds
```

Required tests:

1. first request allowed
2. requests up to threshold allowed
3. threshold + 1 rejected
4. rejected request does not increase accepted count
5. new fixed window resets state
6. per-customer isolation
7. invalid configuration rejected

Boundary:

```text
[0,10)
[10,20)
```

Verify:

```text
t = 9  → old window
t = 10 → new window
```

**Exit:** when these pass, Fixed-Window Rate Limiter V1 is complete at the Phase-1 LLD quality-gate level.

---

# Session 2 — DSA Transfer: Subarray Sum Equals K

**45 minutes**

Problem:

> Given an integer array and integer `k`, return the number of contiguous subarrays whose sum equals `k`.

Numbers may be negative.

Do not name a pattern initially.

## Abstraction-First Sequence

```text
1. Objective
2. Relevant information
3. Discardable information
4. Brute force
5. Repeated work
6. Safe reuse
7. Minimum state
8. Invariant
9. Implementation
10. Tests
```

## Brute Force

For every start:

```text
extend every end
maintain running sum
```

Target:

```text
O(n²)
```

## Derive Prefix Reuse

Let current prefix sum be:

```text
prefix
```

A subarray ending now sums to `k` when an earlier prefix satisfies:

```text
earlierPrefix = prefix - k
```

Therefore the repeated question becomes:

> How many earlier prefix sums equal `prefix - k`?

## Minimum State

```text
currentPrefix
HashMap<prefixSum, frequency>
answer
```

Why a **Map**, not Set?

Because multiplicity matters.

Example:

```text
[0,0]
k = 0
```

## Critical Initialization

```text
frequency[0] = 1
```

This represents the empty prefix before the array and allows ranges beginning at index 0 to be counted.

## Invariant

> Before recording the current prefix, the frequency map contains counts of prefix sums produced by all earlier positions.

Correct order:

```text
update prefix
↓
count frequency[prefix - k]
↓
record current prefix
```

## Why Not Sliding Window?

Negative values destroy the monotonicity used by the positive-number window:

```text
expand right → sum may rise or fall
shrink left  → sum may rise or fall
```

Same contiguous-subarray story does **not** imply the same algorithm.

## Required Tests

```text
[1,1,1], k=2       → 2
[1,2,3], k=3       → 2
[1,-1,0], k=0      → 3
[0,0,0], k=0       → 6
[], k=0            → 0
[3], k=3           → 1
[-1,-1,1], k=0     → 1
```

Complexity:

```text
Time  → O(n) expected
Space → O(n)
```

---

# Session 3 — Virtual Memory vs Physical Memory

**30 minutes**

Day 10 separated JVM heap/stack from CPU cache. Today add the OS layer.

## Physical Memory

```text
actual RAM
```

managed by the OS/hardware.

## Virtual Address Space

Each process sees its own logical virtual address space:

```text
process virtual address
↓
OS + hardware translation
↓
physical memory
```

The same virtual address value in two processes does not imply the same physical location.

## Pages

Virtual memory is managed in chunks:

```text
virtual pages
```

mapped toward physical frames.

Conceptually:

```text
virtual page
↓
translation/page-table structures
↓
physical frame
```

## Why Virtual Memory Exists

```text
process isolation
memory protection
controlled sharing
simpler address-space abstraction
flexible mappings
```

## JVM Connection

Layer the concepts:

```text
Java objects/references
↓
JVM heap / stack frames
↓
process virtual address space
↓
virtual pages
↓
physical memory
↓
CPU cache hierarchy
```

Do not collapse these layers.

## Page Fault — Introduction Only

A page fault occurs when a virtual-memory access requires OS handling because the needed mapping/content is not immediately available in the required physical-memory state.

Do not deep-dive into swapping or page replacement yet.

---

# Session 4 — HLD: URL Shortener V1

**50 minutes**

This is a proper but bounded HLD.

Do not jump into multi-region, consensus, or global sharding.

## Problem

Design:

```text
short.ly/abc123
```

that redirects to a long URL.

## Step 1 — Requirements

Functional V1:

```text
create short URL
redirect short URL
optional expiration
```

Explicitly decide whether to defer:

```text
custom aliases
analytics
ownership
deletion
```

## Step 2 — Non-Functional

Clarify:

```text
read/write ratio
redirect latency
availability expectation
durability
short-code stability
```

Useful invariant:

> Once a short code is successfully created, it continues resolving to the same destination unless an explicit mutation or expiry policy says otherwise.

## Step 3 — API

Create:

```text
POST /urls
```

Request:

```json
{
  "longUrl": "https://example.com/very/long/path"
}
```

Redirect:

```text
GET /{shortCode}
```

## Step 4 — Data Model

Minimal state:

```text
short_code
long_url
created_at
expires_at?
```

Primary access path:

```text
short_code → long_url
```

Therefore `short_code` must support efficient lookup and uniqueness.

## Step 5 — Code Generation

Required properties:

```text
unique
compact
URL-safe
cheap to generate
```

Compare:

### Numeric ID + Base62

```text
DB id
↓
Base62
↓
short code
```

### Random Code

```text
generate
↓
insert under UNIQUE constraint
↓
collision? retry
```

Do not deep-dive into distributed ID generation today.

## Step 6 — Write Path

```text
receive long URL
↓
generate code
↓
persist mapping
↓
commit
↓
return short URL
```

Do not return success before durable persistence if the returned code is expected to survive process failure.

## Step 7 — Read Path

```text
GET /abc123
↓
cache lookup
├── hit → redirect
└── miss → DB → populate cache → redirect
```

Do not introduce cache before explaining the repeated-read requirement it solves.

## Step 8 — Basic Scale

Use rough numbers only:

```text
100M stored URLs
10M redirects/day
1M creates/day
```

Derive:

```text
read-heavy workload
```

Do not overdo capacity math.

## Step 9 — Failure Scenarios

Cover:

```text
DB unavailable
cache unavailable
short-code collision
expired/not-found code
```

## Step 10 — Observability

At minimum:

```text
create success/error
redirect success/error
redirect latency
DB latency
cache hit rate
404/not-found rate
code-collision retries
```

---

# Session 5 — Production Debugging: URL Shortener

**20 minutes**

Scenario:

```text
redirect traffic unchanged
create traffic unchanged

cache hit rate: 92% → 35%
DB read QPS:    4k → 28k
DB CPU:         35% → 82%
redirect p95:   40ms → 190ms
app CPU:        normal
```

Reason:

```text
Observation
↓
behavioral condition
↓
hypotheses
↓
discriminating evidence
↓
mitigation
```

Questions:

1. What is the immediate effect of lower hit rate?
2. Why did DB QPS increase?
3. Does normal app CPU mean the system is healthy?
4. Consider:
   - cold cache
   - capacity reduction
   - key-generation change
   - TTL/invalidation change
   - cache bypass/failure
   - working-set shift
5. What evidence separates cold-cache behavior from a persistent key bug?
6. Why is increasing DB capacity not necessarily the first fix?
7. Which metric would you watch during recovery?

Key lesson:

> DB saturation may be a downstream symptom of a cache miss-rate problem.

---

# Session 6 — Phase-1 Retrieval / Exit Check

**15 minutes**

No code.

## DSA

1. What makes binary search valid?
2. What makes sliding window valid?
3. Why does Subarray Sum Equals K use prefix frequencies instead?
4. Why does multiplicity require a Map instead of Set?
5. Why initialize prefix `0 → 1`?

## Structures

6. Stack vs Queue vs Deque?
7. Why did LRU need two data structures?
8. State the LRU agreement invariant.

## Java / Memory

9. Primitive vs reference?
10. Does a reference array contain its objects inline?
11. JVM heap vs virtual memory?
12. Virtual vs physical memory?
13. CPU cache vs virtual memory?

## LLD / HLD

14. State the Fixed-Window Rate Limiter invariant.
15. Why is a local limiter not globally correct across multiple nodes?
16. State one URL Shortener invariant.
17. Why must create persist before returning success?

---

# Deliverables

## Engineering Closure

- [ ] FixedWindowRateLimiter automated tests pass
- [ ] threshold behavior verified
- [ ] rejection does not corrupt count
- [ ] exact new-window boundary verified
- [ ] per-customer isolation verified
- [ ] invalid configuration verified

## DSA

- [ ] brute force derived
- [ ] prefix equation derived
- [ ] frequency Map requirement derived
- [ ] `0 → 1` initialization explained
- [ ] implementation completed
- [ ] negative-number cases tested
- [ ] O(n) expected complexity explained
- [ ] sliding-window rejection explained

## Memory / OS Bridge

- [ ] virtual vs physical memory explained
- [ ] per-process virtual address space explained
- [ ] pages introduced
- [ ] JVM heap layered on virtual memory correctly
- [ ] CPU cache kept separate
- [ ] page-fault concept introduced

## HLD

- [ ] requirements clarified
- [ ] API defined
- [ ] data model derived
- [ ] short-code uniqueness stated
- [ ] code-generation options compared
- [ ] create path explained
- [ ] redirect path explained
- [ ] cache role explained
- [ ] failures reasoned
- [ ] observability identified

---

# End-of-Day Exit Criteria

Day 11 is complete when:

## Rate Limiter

Automated tests prove the Fixed-Window V1 contract.

## DSA

You derive:

```text
subarray sum
↓
prefix difference
↓
prior-prefix lookup
↓
frequency map
```

without pattern prompting.

## Memory

You can layer:

```text
Java object model
↓
JVM memory regions
↓
process virtual memory
↓
physical memory
↓
CPU cache
```

without mixing abstraction levels.

## HLD

You can drive a bounded URL Shortener through:

```text
requirements
API
data
write path
read path
basic scale
failure
observability
```

without premature advanced mechanisms.

---

# Intentionally Deferred

Do not add today:

- trees/heaps
- graph algorithms
- binary search on answer
- rotated-array search
- rolling-window limiter
- token bucket implementation
- Redis-backed limiter
- Java Memory Model
- GC deep dive
- page-replacement algorithms
- TLB internals
- multi-region URL Shortener
- distributed ID generation deep dive
- URL analytics pipeline

---

# Day 11 Exact Starting Action

Start with:

> **For a fixed-window limiter with limit 3 and a 10-second window, what tests prove the contract rather than only the happy path?**

Complete those tests first.

Then:

> **Given an integer array containing positive and negative numbers, how many contiguous subarrays sum to `k`? Start with brute force. Do not name a pattern.**

---

# Registry Update Requirements

At the end of Day 11 record only demonstrated evidence:

1. FixedWindowRateLimiter tests passing
2. Rate Limiter bugs discovered/fixed
3. Subarray Sum Equals K reasoning
4. prefix/frequency invariant quality
5. DSA implementation/tests
6. abstraction-first behavior
7. virtual vs physical memory explanation
8. JVM/OS terminology corrections
9. URL Shortener requirements/API/data model
10. HLD strengths/gaps
11. production cache-debugging reasoning
12. remaining Phase-1 exit gaps
13. whether Phase 2 is ready
14. exact Day 12 starting action

Do not advance Phase solely because Day 11 is complete.
Advance only if Phase-1 evidence is strong enough.
