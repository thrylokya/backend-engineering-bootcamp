# Current Position

* **Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory
* **Latest Curriculum Worked:** Day 10 — Intensive: Binary-Search Transfer, Java Memory Foundations & Fixed-Window Rate Limiter V1
* **Day 10 Date:** 2026-08-30
* **Status:** Day 10 completed at the conceptual and implementation-learning level. Binary-search boundary reasoning was transferred across first/last occurrence, count occurrences, Search Insert Position, and First Bad Version. Java memory foundations covered primitive/reference semantics, aliasing, stack frames vs heap objects, primitive arrays vs reference arrays, object headers, field layout, padding/alignment, references inside objects, and CPU-cache locality. A single-node Fixed-Window Rate Limiter V1 was designed and implemented with per-user `WindowState`. Rate-limiter implementation tests remain deliberately pending and should be completed as a short engineering-quality gate rather than re-teaching the design.
* **Next Curriculum:** Day 11 — generate from `MASTER_CURRICULUM.md + registry.md + Day 10 evidence`
* **Primary Language:** Java
* **Target Level:** Strong Senior / Lead / Staff-level backend engineering capability
* **Primary Goal:** Production engineering excellence + top-tier interview readiness

---

# Day 10 — Completed Learning & Evidence

## 1. Invariant Precision

Working definition consolidated:

> An invariant is a property that must remain true throughout relevant state transitions while an algorithm/system remains correct.

Important distinction reinforced:

```text
Invariant
→ what must remain true

Algorithm / implementation
→ what we do to preserve it
```

Examples retrieved:

```text
Binary search:
if target exists, its candidate index remains inside the search region

LRU:
map and DLL contain the same logical cache entries

Fixed-window Rate Limiter:
for the customer's current fixed window,
acceptedCount equals accepted requests in that window
and never exceeds the configured limit
```

Initial tendency to mix boundary movement with invariant definition continues to improve.

Carry forward:

> Ask "what property must remain true?" before describing implementation steps.

---

# Binary Search Transfer

## 2. Canonical Lower Bound — `[0, n]`

Requirement:

```text
first index i such that nums[i] >= target
```

If no such element exists:

```text
return n
```

Canonical search space:

```text
[0, n]
```

Important correction from initial implementation:

```java
right = nums.length - 1;
```

cannot represent answer:

```text
n
```

Example:

```text
[1,3,5], target = 10
→ lower bound = 3
```

Correct initialization:

```java
int left = 0;
int right = nums.length;
```

Boundary movement:

```text
nums[mid] < target
→ mid is definitely invalid
→ left = mid + 1
```

```text
nums[mid] >= target
→ mid is a valid candidate
→ an earlier valid candidate may exist
→ preserve mid
→ right = mid
```

Canonical implementation was subsequently reproduced correctly from memory.

---

## 3. Exact Search vs Boundary Search

Distinction consolidated.

Exact binary search:

```text
nums[mid] > target
→ mid is proven not to be answer
→ right = mid - 1
```

Lower bound:

```text
nums[mid] >= target
→ mid may itself be answer
→ right = mid
```

Core transfer:

> Exact search discards `mid` after proving it wrong. Boundary search may need to preserve `mid` after proving it valid.

---

## 4. First and Last Position of Target

Problem:

```text
sorted array
+
duplicates
+
return first and last target indices
```

Initial solution direction:

```text
find first index separately
+
find last index separately
```

was correct.

An initial recursive formulation was produced and then simplified into iterative binary searches.

### First occurrence

Normal binary-search comparison behavior remains unchanged:

```text
nums[mid] < target
→ left = mid + 1

nums[mid] > target
→ right = mid - 1
```

Equality changes:

```text
nums[mid] == target
→ remember mid
→ continue searching left
→ right = mid - 1
```

Invariant-like reasoning:

> The remembered index is the earliest matching index found so far; if a better answer exists, it must be to the left.

### Last occurrence

Equality:

```text
nums[mid] == target
→ remember mid
→ continue searching right
→ left = mid + 1
```

Reasoning:

> The remembered index is the latest matching index found so far; if a better answer exists, it must be to the right.

Complexity:

```text
Time  = O(log n)
Space = O(1)
```

for the iterative implementation.

---

## 5. Boundary-Abstraction Learning Note

The initial:

```text
false false false true true
```

predicate framing created unnecessary cognitive overhead during first/last-occurrence derivation.

Concrete reasoning proved more effective:

```text
found target
+
first occurrence?
→ remember candidate and continue left

last occurrence?
→ remember candidate and continue right
```

After the concrete algorithm was understood, the equivalent boundary interpretation became clearer.

Important coaching rule going forward:

> For DSA, derive the concrete safe-elimination behavior first. Introduce monotonic-predicate terminology only when it improves the reasoning rather than obscuring it.

---

## 6. Count Occurrences in Sorted Array

Derived from previously solved boundaries:

```text
count
=
lastIndex - firstIndex + 1
```

If:

```text
firstIndex == -1
```

return:

```text
0
```

Example:

```text
[1,2,2,2,3,4], target = 2

first = 1
last  = 3

count = 3
```

Important simplification identified:

The first version performed an additional midpoint-based narrowing in the caller before invoking the first/last helpers.

This was logically valid but redundant.

Cleaner composition:

```text
findFirst()
↓
absent? → 0
↓
findLast()
↓
last - first + 1
```

Engineering lesson:

> If a helper completely owns an operation, avoid partially reimplementing that operation in the caller.

Complexity:

```text
O(log n)
```

because matching duplicates are never enumerated.

---

## 7. Search Insert Position

Correctly recognized as the same problem as lower bound:

```text
first index where nums[i] >= target
```

Implementation reproduced correctly using:

```java
left = 0;
right = numbers.length;

while (left < right) {
    int mid = left + (right - left) / 2;

    if (numbers[mid] >= target) {
        right = mid;
    } else {
        left = mid + 1;
    }
}
```

Correctly handles:

```text
target present
target between elements
target before all elements
target after all elements
empty array
```

No special empty-array branch is required.

---

## 8. First Bad Version

Binary search successfully transferred away from arrays.

Search space:

```text
versions 1 ... n
```

Provided API:

```java
boolean isBad(int version)
```

Contract:

```text
once a version becomes bad,
all later versions are also bad
```

Example:

```text
F F F T T T T
      ^
   first bad
```

Derived:

```text
isBad(mid) == true
→ mid may be first bad
→ preserve mid
→ right = mid
```

```text
isBad(mid) == false
→ mid and everything before it are good
→ left = mid + 1
```

Implementation produced correctly.

Key transfer:

> Binary search fundamentally requires an ordered candidate space, a monotonic decision property, and safe elimination. It does not fundamentally require an array.

Complexity:

```text
O(log n)
```

API calls.

---

# Java Memory Foundations

## 9. Primitive vs Reference

Primitive:

```java
int x = 42;
```

Mental model:

```text
x contains primitive value 42
```

Reference:

```java
Person p = new Person();
```

Mental model:

```text
p
→ reference

Person object
→ separate heap-managed object
```

Important statement:

> A reference is not the object.

---

## 10. Aliasing

Example:

```java
Person a = new Person();
Person b = a;
```

Correctly identified:

```text
1 Person object
2 reference variables
```

Both:

```text
a
b
```

refer to the same object.

Therefore:

```java
b.name = "TG";
```

is visible through:

```java
a.name
```

because the shared object was mutated.

---

## 11. Reference Reassignment

Example:

```java
Person a = new Person("A");
Person b = a;

b = new Person("B");
```

Correctly reasoned:

```text
a → Person("A")
b → Person("B")
```

There are now:

```text
2 objects
2 references
```

Key distinction:

```text
b.name = ...
→ mutate referenced object

b = new Person(...)
→ reassign reference
```

---

## 12. Stack Frames vs Heap Objects

For:

```java
void process(Person p, int count) {
    int local = count + 1;
}
```

conceptual stack frame contains:

```text
reference p
primitive count
primitive local
execution/bookkeeping state
```

The `Person` object itself is separate.

Important refinement:

> References are not universally "on the stack."

A local reference may conceptually reside in a stack frame.

A reference field inside another heap object is stored as part of that object.

---

## 13. Primitive Arrays vs Reference Arrays

Primitive:

```java
int[] numbers = new int[3];
```

contains:

```text
[0 | 0 | 0]
```

Reference array:

```java
Person[] people = new Person[3];
```

contains:

```text
[null | null | null]
```

Important statement consolidated:

> `new Person[3]` creates one array object containing three reference slots. It does not create three Person objects.

After:

```java
people[0] = new Person("A");
people[1] = new Person("B");
```

conceptually:

```text
people array
[refA | refB | null]
   ↓      ↓
   A      B
```

Total heap-managed objects in this example:

```text
1 array
+
2 Person objects
=
3 objects
```

---

# Object Layout

## 14. Object Fields Are Stored Inline

Example:

```java
class Person {
    int age;
    int id;
    String name;
}
```

Conceptual object layout:

```text
object header
int age
int id
reference name
possible padding
```

Primitive fields:

```text
age
id
```

are stored inline as part of the object's memory representation.

The reference field:

```text
name
```

is also stored inline, but it contains only a reference.

The actual `String` object is separate.

Therefore:

```text
person.age
```

does not require following another Java object reference.

But:

```text
person.name
```

requires:

```text
read reference from Person object
↓
follow reference
↓
access String object
```

This may involve additional pointer chasing/cache behavior.

---

## 15. Object Header Mental Model

Traditional HotSpot object-header concepts introduced:

```text
Mark Word
+
class/Klass information
```

Object header contains JVM runtime metadata rather than application-domain fields.

### Mark Word

Conceptually associated with runtime information such as:

```text
locking/synchronization state
identity-hash information
GC age
runtime/status bits
```

GC age was understood as conceptually tracking object survival across young-generation collection activity.

### Class / Klass Information

Allows the JVM to identify:

```text
what runtime class is this object?
```

and reason about:

```text
field layout
type checks
runtime dispatch
GC reference layout
```

Important precision:

> Do not memorize exact header bit layouts because HotSpot implementation details vary by JDK/configuration.

---

## 16. Padding / Alignment

Padding clarified as:

> Unused bytes inserted to satisfy memory-layout/alignment requirements.

Padding is not:

```text
additional Mark Word space
```

and is not application data.

Conceptual total object size:

```text
object header
+
instance primitive fields
+
instance reference fields
+
alignment/padding
```

Exact byte counts are JVM/configuration dependent.

---

## 17. 64-bit HotSpot / Compressed References — Introduction

Introduced conceptually:

```text
64-bit JVM/process
```

does not imply:

```text
every Java reference must physically consume 8 bytes
```

HotSpot can use compressed object references.

Directional understanding:

```text
smaller references
→ lower memory footprint
→ potentially better cache density
```

Exact implementation details deliberately remain JVM-internals material and should not become memorization burden during Phase 1.

---

# JVM Heap vs CPU Cache

## 18. Separate Abstraction Layers

JVM-level concepts:

```text
heap
stack frames
objects
references
arrays
```

Hardware-level concepts:

```text
CPU registers
L1/L2/L3 cache
cache lines
RAM
```

Important correction:

Do not say:

```text
stack = CPU cache
heap = RAM
```

A logically heap-resident Java object's bytes may currently be present in CPU cache.

---

## 19. Primitive Array Locality

Primitive arrays:

```text
contiguous primitive payload
```

provide strong spatial locality.

Reading one part of an array may cause nearby data to enter the same or nearby cache lines.

Therefore sequential traversal can benefit from:

```text
cache-line reuse
hardware prefetch
spatial locality
```

---

## 20. Reference Array Locality

For:

```java
Person[] people;
```

the array stores contiguous:

```text
references
```

Therefore iterating over the reference slots has good locality.

However:

```text
people[0] → Person object A
people[1] → Person object B
people[2] → Person object C
```

the actual Person objects may reside in unrelated heap locations.

Therefore:

> An array of object references gives spatial locality for the reference array itself, but not necessarily for the referenced objects.

Following the references may still create pointer chasing/cache misses.

---

## 21. Object Field Locality

Within a single object:

```text
header
+
inline fields
```

form one object layout.

Therefore accessing nearby primitive fields may benefit from locality if the relevant object data is already cache-resident.

Reference fields differ because the referenced object's data is elsewhere.

This creates the conceptual access chain:

```text
load containing object
↓
read reference field
↓
dereference another object
↓
potential additional cache access/miss
```

---

# Rate Limiter V1 — Fixed Window

## 22. Requirement Clarification

Day 10 implementation selected:

```text
Fixed Window
```

not:

```text
Rolling Window
Token Bucket
```

Fixed-window semantics:

```text
12:00:00–12:00:59
12:01:00–12:01:59
12:02:00–12:02:59
```

Each customer receives a fresh allowance in every predefined window.

Example:

```text
limit = 100/minute

100 requests at 12:00:59
+
100 requests at 12:01:01
```

may both be valid under fixed-window semantics.

This is a:

```text
boundary burst
```

and is a policy consequence, not necessarily an implementation bug.

---

## 23. Window Identity

Unix epoch seconds introduced:

```java
Instant.now().getEpochSecond()
```

Conceptually:

> Number of whole seconds since 1970-01-01T00:00:00Z.

Fixed-window identity:

```java
windowId = epochSeconds / windowSizeSeconds;
```

For:

```text
windowSize = 60 seconds
```

all timestamps in the same 60-second bucket produce the same logical `windowId`.

Important naming improvement:

Do not call:

```java
Instant.now().getEpochSecond() / 60
```

`epochSeconds`.

It is now:

```text
windowId
```

---

## 24. Initial Rate-Limiter State Model

Initial implementation used:

```java
HashMap<String, HashMap<Long, Integer>>
```

conceptually:

```text
user
→ timestamp/window
→ count
```

This retained historical buckets and was more state than fixed-window semantics require.

Key abstraction correction:

> Fixed window does not require storing every request or every historical bucket.

Only the customer's current fixed-window state matters.

---

## 25. Minimal Fixed-Window State

State model simplified to:

```java
Map<String, WindowState>
```

where:

```text
WindowState
├── windowId
└── requestProcessedCount
```

Conceptually:

```text
abc → {windowId=12345, count=7}
def → {windowId=12345, count=2}
ghi → {windowId=12346, count=1}
```

Important insight:

> The map is per user; the value object contains that user's current fixed-window state.

No nested map is required.

---

## 26. Fixed-Window Decision Flow

For every request:

```text
compute currentWindowId
↓
lookup user
```

### New user

```text
create WindowState
windowId = currentWindowId
count = 1
allow
```

### Existing user — same window

```text
count >= threshold
→ reject

count < threshold
→ count++
→ allow
```

### Existing user — new window

```text
windowId = currentWindowId
count = 1
allow
```

No global/background "reset every minute" mechanism is required.

Reset happens lazily when that customer's next request enters a different window.

---

## 27. Rate-Limit Off-by-One Bug

Initial implementation checked:

```java
if (totalRequests > threshold)
```

which allows:

```text
threshold + 1
```

requests.

Correct condition:

```java
if (count >= threshold)
```

before accepting the next request.

This prevents accepted count from exceeding the configured limit.

---

## 28. Fixed-Window Invariant

Current invariant:

> For each customer's current fixed window, `requestProcessedCount` equals the number of accepted requests represented by that state and never exceeds the configured threshold.

Rejected requests must not increment the accepted count.

---

# Rate Limiter LLD Structure

## 29. RateLimiter Abstraction

Created:

```java
interface RateLimiter
```

with current conceptual API:

```java
boolean shouldProcessRequest(String user);
```

Request payload was removed from the rate-limiter API because the current policy does not use request contents.

Future policies may evolve identity/scope requirements separately.

---

## 30. FixedRateLimiter

Implemented a single-node:

```java
FixedRateLimiter
```

using:

```java
HashMap<String, WindowState>
```

Current implementation behavior is correct for the intended:

```text
single-threaded
single-process
fixed one-minute window
```

learning model.

Potential naming improvement:

```text
FixedWindowRateLimiter
```

is more precise than:

```text
FixedRateLimiter
```

because "fixed" describes the time-window algorithm.

---

## 31. RateLimiterFactory

A factory was introduced to create limiter implementations.

Current selection resembles:

```java
RateLimiterFactory.getRateLimiter(
    "FixedRateLimiter",
    limit
)
```

Current implementation works as an extensibility exercise.

Future cleanup:

```text
raw String policy selector
→ enum/configuration/policy object
```

and:

```text
unknown limiter
→ throw explicit exception
```

rather than returning:

```text
null
```

Do not over-engineer this yet.

---

## 32. Dependency Injection / Application Ownership

Initial application created its own limiter internally:

```text
RateLimiterApplication
→ calls RateLimiterFactory itself
```

Refactored to constructor injection:

```java
RateLimiterApplication(RateLimiter rateLimiter) {
    this.rateLimiter = rateLimiter;
}
```

Bootstrap/main now decides which implementation to create:

```text
main/configuration
↓
factory creates RateLimiter
↓
RateLimiter injected into application
```

Important design principle:

> The component using a dependency should not necessarily also own the dependency's construction/configuration.

`RateLimiterApplication` now depends only on:

```java
RateLimiter
```

and does not need to understand:

```text
Fixed Window
Token Bucket
factory selection
threshold construction
```

Runtime mutation/setters were deliberately avoided for V1.

If rate-limit algorithms must truly change dynamically later, state migration and configuration semantics should be designed explicitly rather than introducing an ad-hoc setter.

---

# Rate Limiter HLD Bridge

## 33. Local vs Global Rate Limiting

Current implementation stores state in:

```text
in-process HashMap
```

Therefore each application instance has independent state.

Suppose:

```text
3 service instances
limit = 100/customer/minute
```

Each instance could independently accept:

```text
instance A → 100
instance B → 100
instance C → 100
```

Potential global acceptance:

```text
300
```

Therefore:

> Correct local enforcement does not imply correct global enforcement.

This limitation was identified correctly.

---

## 34. Distributed Direction — Deferred

A distributed system needs some mechanism for coordinating rate-limit state.

Possible future directions include:

```text
shared centralized state
Redis-style atomic operations
gateway-level enforcement
partitioned/distributed enforcement
```

These were recognized conceptually but not designed/implemented on Day 10.

Do not prematurely jump into:

```text
Redis Lua
distributed locking
consensus
```

until the distributed Rate Limiter/HLD curriculum explicitly reaches that stage.

---

# Rate Limiter Production Concerns

## 35. Fixed-Window Boundary Burst

Fixed-window semantics allow traffic concentration around boundaries.

Example:

```text
limit = 100/min

100 requests just before boundary
+
100 requests immediately after boundary
```

can produce approximately:

```text
200 requests in a very short real-time interval
```

while still satisfying each fixed bucket independently.

This is a semantic limitation of Fixed Window.

---

## 36. State Growth

Current:

```java
Map<String, WindowState>
```

retains one state entry for every customer that has ever been observed.

If customer cardinality continually grows:

```text
map size ↑
heap usage ↑
GC activity ↑
```

Old/stale users eventually need a cleanup/expiry strategy in a production implementation.

This remains intentionally unimplemented in V1.

---

## 37. Time Dependency / Testability

Current implementation internally uses:

```java
Instant.now()
```

This works for a demonstration but makes exact window-boundary tests harder.

Preferred testable design:

```java
boolean allow(String customerId, Instant now)
```

or inject a:

```java
Clock
```

later.

Reason:

```text
deterministic tests
```

Example tests should be able to explicitly exercise:

```text
t = 59 sec
t = 60 sec
```

without sleeping.

Carry forward as implementation-quality improvement.

---

# Day 10 — Tests / Engineering Evidence Pending

## 38. Fixed-Window Rate Limiter Tests

Implementation was manually smoke-tested through `RateLimiterApplication`.

Formal unit tests remain pending.

Required regression tests:

```text
1. first request allowed

2. requests up to threshold allowed

3. threshold + 1 request rejected

4. rejected request does not increase count

5. next fixed window resets allowance

6. per-user isolation

7. exact window-boundary behavior

8. multiple windows

9. null/invalid user behavior

10. invalid limit configuration
```

Testability improvement needed before exact boundary tests:

```text
control time explicitly
```

rather than relying entirely on:

```java
Instant.now()
```

Carry these tests into the next engineering-quality warm-up rather than re-teaching Fixed Window.

---

# Day 10 — Strong Areas

## 39. DSA

Improved substantially:

```text
exact binary search
first occurrence
last occurrence
count occurrences
Search Insert Position / lower bound
First Bad Version
safe elimination
candidate preservation
```

First/last occurrence was solved independently after an initial recursive formulation and simplified into a clean iterative solution.

Binary search successfully transferred from:

```text
array-value comparison
```

to:

```text
boolean monotonic API
```

through First Bad Version.

---

## 40. Java / JVM

Strong conceptual understanding established for:

```text
primitive vs reference
aliasing
reference reassignment
stack frame vs heap object
primitive arrays
reference arrays
inline primitive fields
reference fields
object headers
Mark Word
class/Klass information
padding/alignment
pointer chasing
cache locality
```

The user actively asked implementation-level JVM questions and connected object/reference layout to CPU-cache behavior.

Do not re-teach these foundations from scratch.

Use retrieval and later deepen them during dedicated JVM/GC sessions.

---

## 41. LLD

Good improvement in reducing unnecessary state.

Rate Limiter evolution:

```text
nested historical map
↓
identify actual policy requirement
↓
one WindowState per customer
```

This demonstrated the abstraction goal:

> Store the minimum state required to answer the decision.

Constructor injection was also adopted after discussing dependency ownership.

---

# Day 10 — Active Gaps / Corrections

Continue reinforcing:

```text
1. Binary-search boundary abstraction should follow concrete reasoning rather than precede it when terminology becomes distracting.

2. Keep index and value terminology precise:
   lower bound returns an index, not the target value.

3. Avoid redundant work in DSA callers once helpers already completely own the search operation.

4. State invariants as properties rather than procedural boundary updates.

5. FixedWindowRateLimiter needs deterministic unit tests.

6. Time should eventually be injectable/controllable rather than hard-coded through Instant.now().

7. Current Rate Limiter implementation is single-threaded only.
   HashMap + mutable WindowState is not thread-safe.

8. Rate-limiter stale-user state cleanup remains unresolved.

9. Local in-memory rate limiting does not provide a distributed/global guarantee.

10. Factory string-based selection is acceptable for the exercise but should eventually become typed/config-driven.
```

---

# Deferred / Do Not Re-Teach Yet

Do not restart from scratch:

```text
LRU Cache
basic exact binary search
first/last occurrence
count occurrences
Search Insert Position
First Bad Version
primitive/reference basics
aliasing
stack vs heap basics
primitive vs reference arrays
basic object-header concept
fixed-window semantics
basic local-vs-global Rate Limiter limitation
```

Use retrieval instead.

---

# Explicit Carry-Forward Items

## Immediate

```text
1. Add deterministic tests for FixedWindowRateLimiter.

2. Refactor time dependency when tests require exact window control.

3. Preserve single-node V1; do not over-engineer distributed enforcement prematurely.
```

## Java / JVM Later

Deepen during dedicated JVM sessions:

```text
GC roots
young/old generations
allocation paths
TLAB
minor/major/full GC terminology
promotion
collectors
escape analysis
JIT
compressed oops/class pointers
Metaspace
actual object layout inspection with JOL
```

Day 10 introduced supporting concepts but did not replace the dedicated JVM/GC curriculum.

## Rate Limiter Later

Future HLD progression should cover:

```text
centralized/shared state
atomicity
concurrent requests
Redis-style counters
TTL/state cleanup
clock semantics
failure modes
hot keys
partitioning
fixed-window vs sliding-window vs token-bucket trade-offs
gateway vs application-layer enforcement
global vs regional limits
```

---

# Interview Readiness Signal After Day 10

Current trend:

```text
DSA mechanics:
improving

binary-search transfer:
good progress

boundary terminology:
still becoming natural

Java memory fundamentals:
strong conceptual progress

LLD state modeling:
improving

Rate Limiter V1:
conceptually implemented

engineering evidence:
tests still required

distributed/HLD depth:
next-stage work
```

Important observation:

> When reasoning remains concrete and centered on what information is actually necessary, solutions become significantly simpler.

Day 10 examples:

```text
Count Occurrences
→ compose first + last instead of adding another search layer

Fixed Window
→ user → WindowState instead of user → map of historical windows

RateLimiterApplication
→ depend on RateLimiter rather than constructing policy internally
```

Continue reinforcing:

```text
requirement
↓
minimum information needed
↓
state representation
↓
invariant
↓
implementation
↓
tests
```

---

# Next-Day Starting Point

Do not regenerate Day 11 blindly.

Generate Day 11 using:

```text
MASTER_CURRICULUM.md
+
this registry
+
Day 10 evidence
```

Day 11 should:

```text
briefly close FixedWindowRateLimiter tests
+
continue the planned curriculum progression
+
increase HLD depth according to MASTER_CURRICULUM
```

without spending another full session re-deriving Day 10 material.
