# Backend Engineering Bootcamp — Registry — Day 7 Update

> Apply the sections below to `cirriculum/registry.md`.
> Keep the existing Day 5 and Day 6 evidence unchanged.
> Replace `Current Position`, append the Day 7 evidence/findings, and replace `Exact Next Action` with the Day 8 section below.

---

# Current Position

* **Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory
* **Latest Curriculum Worked:** Day 7 — Stack, Queue & Sliding-Window Reasoning
* **Day 7 Date:** 2026-08-24
* **Status:** Day 7 completed at the learning/evidence level. Stack and Queue behavioral contracts and linked implementations were exercised with tests. Valid Parentheses was derived from unresolved-state/LIFO reasoning. Sliding-window reasoning was reinforced from positivity and total pointer movement. Java runtime trade-offs between array-backed and linked structures were reviewed. HLD exposure continued through a durable Notification Service flow covering persistence-before-acknowledgement, atomic claiming, leases, fencing, downstream idempotency, reconciliation, and queue-backlog diagnosis. A small amount of implementation cleanup remains to be verified, but the concepts do not need to be re-taught.
* **Next Curriculum:** Day 8
* **Primary Language:** Java
* **Target Level:** Strong Senior / Lead / Staff-level backend engineering capability
* **Primary Goal:** Production engineering excellence + top-tier interview readiness

---

# Day 7 — Completed Learning & Evidence

## 1. Mixed DSA Pattern Recall

Reinforced the abstraction-first protocol through four short drills.

### Membership

Problem:

> For every arriving integer, determine whether that value appeared previously.

Derived:

```text
objective
→ membership in prior observations

brute force
→ scan all prior values for every new value

repeated work
→ repeatedly searching the same history

relevant state
→ distinct values observed so far

discardable information
→ arrival order
→ positions
→ duplicate count
```

Result:

```text
membership requirement
→ HashSet
```

Complexity:

```text
brute force total → O(n²)
HashSet average   → O(n)
space             → O(n)
```

Important lesson:

> Choose `Set` because only membership matters, not because the story “looks like a HashSet problem.”

### Sorted Pair Sum

Re-derived safe two-pointer elimination from sorted order.

```text
sum > target
→ current largest cannot work with any remaining partner
→ move right leftward

sum < target
→ current smallest cannot work with any remaining partner
→ move left rightward
```

Enabling property:

> Sorted order gives monotonic boundary behavior, which makes permanent candidate elimination safe.

A boundary-direction slip occurred during explanation and was corrected.

### Middle of Linked List

Re-derived:

```text
slow moves 1
fast moves 2
```

Key abstraction:

> Relative speed encodes positional information without needing the absolute list length.

Important boundary correction reinforced for:

```java
while (fast != null && fast.next != null)
```

For an even-length list, the usual implementation returns the second middle.

### Minimum Contiguous Sum

Recalled the minimum-length contiguous subarray with positive numbers.

Derived:

```text
sum < target
→ expand right

sum >= target
→ shrink left while still valid
```

Enabling property:

```text
all values > 0
```

Therefore:

```text
expand right → sum cannot decrease
shrink left  → sum cannot increase
```

Negative values break this monotonic guarantee.

---

## 2. `removeNthFromEnd` Closure

The fixed-gap reasoning was closed conceptually.

For deletion:

```text
slow must end on the predecessor
of the node being removed
```

Reason:

> In a singly linked list, deletion is performed by changing the predecessor's `next` reference.

Dummy-node value reinforced:

```text
dummy → head → ...
```

The dummy gives the real head an artificial predecessor, so removing the head uses the same predecessor-based operation as removing any other node.

Core invariant:

> Establish the fast/slow gap once, then move both pointers together so that when `fast` reaches the boundary, `slow` is the predecessor of the deletion target.

---

## 3. Stack Behavioral Contract

Derived Stack from access behavior before naming the structure.

```text
push A
push B
push C

pop → C
pop → B
pop → A
```

Core contract:

```text
LIFO
Last In, First Out
```

Important abstraction:

> Stack is a behavioral contract, not a physical representation.

Possible representations include:

* linked nodes
* dynamic array
* array/deque-style storage

---

## 4. `IntStack` Implementation

Implemented a linked-node Stack maintaining:

```text
top
size
```

Core invariant:

```text
size == 0 ⇔ top == null
```

and:

> `top` always represents the next value that `pop()` must return.

Operations demonstrated:

```text
push  O(1)
pop   O(1)
peek  O(1)
```

Empty `pop()` / `peek()` contract was standardized to:

```text
throw NoSuchElementException
```

Important API/encapsulation feedback:

* do not expose `Node` through the Stack API
* `top` and `size` should be private
* nested `Node` can be `private static`
* internal Node getters/setters are unnecessary
* `peek()` should return primitive `int` when empty state is represented by exception
* `push()` can be reduced to `top = new Node(value, top)`

These are code-hygiene improvements, not conceptual Stack gaps.

---

## 5. `IntStack` Testing Evidence

JUnit tests covered:

* push one integer
* negative integer
* multiple pushes
* empty `pop()`
* one-element `pop()`
* multi-element `pop()`
* push/pop transitions
* `peek()` without removal
* empty `peek()`
* size on empty/non-empty stack
* `isEmpty()`
* explicit LIFO ordering
* final-element removal

LIFO evidence included:

```text
push 2
push 0
push 3

pop → 3
pop → 0
pop → 2
```

Stack implementation is complete at the learning/evidence level.

---

## 6. Queue Behavioral Contract

Derived Queue from access behavior.

```text
enqueue A
enqueue B
enqueue C

dequeue → A
dequeue → B
dequeue → C
```

Core contract:

```text
FIFO
First In, First Out
```

Linked representation chosen with:

```text
head
tail
size
```

Meaning:

```text
head
→ next value to dequeue

tail
→ most recently enqueued value
```

Why `tail` exists:

> Without a stored `tail`, linked-list enqueue requires traversal to the end and becomes `O(n)`. Maintaining `tail` makes enqueue `O(1)`.

---

## 7. `IntQueue` Implementation and Critical Invariant

Implemented linked-node Queue operations:

```text
enqueue
dequeue
peek
getSize
isEmpty
```

Critical empty invariant:

```text
head == null
tail == null
size == 0
```

Non-empty invariant:

```text
head != null
tail != null
tail.next == null
```

A real bug was identified in the first implementation.

For:

```text
head
 ↓
 A
 ↑
tail
```

after removing `A`, the initial code produced:

```text
head = null
tail = old A
size = 0
```

This violates the empty invariant.

Required correction:

```java
head = head.next;
size--;

if (head == null) {
    tail = null;
}
```

Key engineering lesson:

> `tail` is redundant/convenience state introduced for performance. Extra state buys faster operations but creates an additional consistency burden.

Final corrected Queue code should be verified once before considering the implementation fully closed; the invariant itself is understood.

---

## 8. `IntQueue` Testing Evidence

JUnit tests covered:

* enqueue one
* enqueue several
* FIFO dequeue order
* `peek()` without removal
* size transitions
* empty `dequeue()`
* empty `peek()`
* drain entire queue
* enqueue again after becoming empty

Important testing improvement:

> Re-enqueue-after-empty should verify actual behavior (`peek`, `dequeue`, `isEmpty`) rather than only verifying `size`.

Queue reasoning is strong; final code should retain the explicit `tail = null` transition when the final node is removed.

---

## 9. Valid Parentheses — Abstraction & Stack Derivation

Problem objective:

> Validate correct bracket nesting.

Relevant symbols for the standard problem:

```text
( )
[ ]
{ }
```

Historical information required:

> Only unmatched opening brackets, in the order they must be closed.

Key insight:

> A closing bracket must match the **most recent unmatched opening bracket**, not merely any matching opener that appeared previously.

Example:

```text
([)]
```

Processing:

```text
'(' → push
'[' → push
')' → top is '['
```

Mismatch is sufficient to return `false` immediately. The remaining `]` cannot repair an already-invalid prefix.

Core invariant:

> After processing each prefix, the stack contains exactly the unmatched opening brackets, with the most recent unmatched opener at the top.

End condition:

```text
stack must be empty
```

so:

```text
(((
```

is invalid even though no closing mismatch occurred.

---

## 10. Valid Parentheses — Important Modeling Finding

The first implementation expanded the problem to include:

* quotes
* angle brackets
* arbitrary characters
* additional parsing semantics

This was unnecessary for the stated problem and reproduced an existing DSA weakness:

> Modeling the story/language too literally and introducing extra state before proving that it affects the answer.

The correction was to return to the smallest sufficient representation:

```text
objective
→ bracket nesting validity

relevant information
→ only unresolved opening brackets

discard
→ everything that does not affect bracket matching
```

A later implementation correctly added an empty-stack guard before `peek()`:

```java
if (!charStack.isEmpty() && charStack.peek() == expected) {
    charStack.pop();
} else {
    return false;
}
```

Remaining cleanup for the standard interview problem:

* remove `<` / `>`
* ensure the opener set contains exactly `(`, `[`, `{`
* remove unnecessary parsing/generalization

Reasoning is complete; final implementation cleanup should be brief.

---

## 11. Sliding-Window Complexity Closure

The minimum-size-subarray problem was not re-solved from scratch because it was already covered previously.

The critical complexity proof was reinforced.

Even if code has:

```text
for right ...
    while ...
```

it is still `O(n)` because:

```text
right moves forward at most n times
left moves forward at most n times
```

Total boundary movement:

```text
≤ 2n
```

Therefore:

```text
Time  → O(n)
Space → O(1)
```

Preferred interview explanation:

> Each element enters the window at most once and leaves the window at most once.

Avoid reasoning such as:

> “The inner loop usually scans only a few elements.”

That is not a worst-case proof.

Sliding-window reasoning is materially stronger than on Day 6, though future transfer problems should still test whether the pattern is recognized from monotonicity rather than memorization.

---

## 12. Java/JVM — `ArrayDeque` vs Linked Representation

Reinforced that identical Big-O does not imply identical runtime performance.

Linked Stack using the head can still provide:

```text
push → O(1)
pop  → O(1)
```

Linked Queue with `head` + `tail` can provide:

```text
enqueue → O(1)
dequeue → O(1)
```

So `ArrayDeque` performance advantages are not because the linked representation necessarily traverses.

Array-backed advantages discussed:

* stronger cache locality
* fewer per-element node allocations
* less pointer chasing
* lower object/header/reference overhead
* lower GC pressure
* better use of CPU cache lines

Important distinction:

```text
algorithmic complexity
!=
allocation cost
!=
memory layout
!=
cache behavior
!=
GC overhead
```

A mistaken claim that linked-list `pop()` necessarily traverses to the end was corrected.

---

## 13. HLD — Notification Service: Durable Acceptance

Scenario:

```text
POST /notifications
↓
asynchronous processing
↓
email/SMS provider
```

Naive design:

```text
API
↓
in-memory queue
↓
worker
↓
provider
```

Failure:

```text
request accepted
→ 202 returned
→ JVM/process crashes
→ in-memory queue disappears
→ acknowledged notification is lost
```

Derived invariant:

> Once the service acknowledges durable acceptance, enough state must survive process failure to continue processing later.

V1 durable flow:

```text
POST /notifications
↓
INSERT notification(status = PENDING)
↓
DB commit
↓
202 Accepted
```

Important semantic distinction:

```text
202 Accepted
```

means:

> The system durably accepted responsibility for processing.

It does **not** mean:

> The external email/SMS was already sent.

---

## 14. HLD — Atomic Claiming With Multiple Workers

With multiple workers polling `PENDING` notifications, a plain:

```text
SELECT PENDING
→ later UPDATE
```

creates a race because several workers can observe the same pending row.

Derived atomic claim:

```sql
UPDATE notifications
SET status = 'IN_PROGRESS',
    worker_id = ?,
    updated_at = NOW()
WHERE id = ?
  AND status = 'PENDING';
```

Interpret affected-row count:

```text
1 row
→ claim won
→ worker owns processing

0 rows
→ another worker already changed the state
→ skip
```

Important operational rule:

> Do not hold a database transaction/row lock while calling the external provider.

Correct shape:

```text
claim atomically
↓
commit
↓
release DB resources
↓
perform network I/O
```

---

## 15. HLD — Lease Recovery & Fencing

Failure:

```text
PENDING → IN_PROGRESS
↓
worker crashes
↓
job remains stuck forever
```

Recovered using time-bounded ownership:

```text
status
owner
leaseUntil
fencingToken
```

Example:

```text
Worker A claims token 10
↓
lease expires
↓
Worker B claims token 11
```

Lease:

> Determines when another worker may reclaim ownership.

Fencing token:

> Determines whether a particular execution attempt is still authorized to mutate authoritative state.

A stale Worker A carrying token 10 must not overwrite state after token 11 becomes authoritative.

---

## 16. HLD — External Side Effects & Idempotency

Hard failure window:

```text
worker calls provider
↓
provider successfully sends email
↓
worker crashes
↓
before local DB is updated to COMPLETED
```

After lease expiry, another worker may retry.

Fencing alone cannot undo or deduplicate an external side effect that already happened.

Derived requirement:

> The external operation should be identifiable/idempotent when the downstream system supports it.

Example:

```text
notificationId = N123
idempotencyKey = N123
```

Retry:

```text
Worker A → provider(N123) → send succeeds → crash
Worker B → provider(N123)
```

If provider honors idempotency:

```text
same logical request
→ no duplicate external effect
→ return prior result
→ local state converges to COMPLETED
```

Important distinction:

```text
fencing
→ protects authorization to mutate local authoritative state

idempotency
→ protects repeated external business effects
```

If the provider does not support idempotency, the system cannot magically guarantee true exactly-once execution across the external boundary.

Practical guarantee may be:

```text
at-least-once processing
+
best-effort duplicate suppression
+
reconciliation
```

---

## 17. HLD — Reconciliation

Useful durable fields discussed:

```text
notification_id
status
provider_request_id
idempotency_key
owner
lease_until
fencing_token
```

For an ambiguous `IN_PROGRESS` notification:

```text
query provider using stable identity
↓
provider reports SENT
↓
mark local notification COMPLETED
```

This extends the earlier payment-system learning:

> Local database state and external side effects may temporarily disagree; reconciliation is required to converge.

---

## 18. Production Queue Backlog Diagnosis

Observed metrics:

```text
producer rate:      8,500/sec
consumer rate:      6,000/sec
queue depth:        increasing
CPU:                45%
DB latency:         normal
provider latency:   elevated
error rate:         low
```

Immediate throughput equation:

```text
8,500 - 6,000
= 2,500 messages/sec backlog growth
```

Per minute:

```text
2,500 × 60
= 150,000 additional messages
```

Fundamental condition:

```text
arrival rate > service rate
```

Therefore backlog must grow while that condition remains true.

---

## 19. CPU Saturation vs System Saturation

A critical production distinction was reinforced.

Moderate CPU does not imply healthy throughput.

Consumers may be:

```text
call provider
↓
wait on network/downstream
↓
receive response
↓
process next item
```

During I/O wait, worker threads may be occupied while CPU remains moderate.

Given:

```text
CPU normal
DB normal
provider latency elevated
```

the strongest first hypothesis is:

> Elevated downstream latency is reducing effective consumer throughput.

Validate with:

* provider-call latency
* in-flight request count
* worker active/waiting state
* timeout rate
* consumer throughput
* queue age
* queue depth

Do not jump directly to GC/thread-count explanations when existing evidence points more strongly to downstream I/O.

---

## 20. Queue Age, End-to-End Latency & Capacity

If oldest-message age grows:

```text
2 sec
→ 30 sec
→ 3 min
```

then customer-visible end-to-end latency grows even if the provider eventually succeeds.

```text
end-to-end latency
=
queue wait
+
actual processing time
```

Therefore:

> Low error rate does not prove that the system is healthy.

Important operational signal:

```text
oldest message age / queueing delay
```

Increasing queue capacity from:

```text
1 million
→ 10 million
```

does not fix:

```text
arrival rate > service rate
```

It only delays the point at which capacity/resources are exhausted.

Core mental model:

> Queues absorb temporary bursts. They cannot indefinitely compensate for a sustained throughput deficit.

---

# Day 7 — Improvement Findings / Remaining Gaps

## 1. DSA Abstraction Is Improving, but Over-Modeling Still Appears During Coding

During verbal reasoning, objective/relevant-state identification improved significantly.

However, Valid Parentheses initially expanded into:

* quotes
* angle brackets
* arbitrary parsing behavior

This is the same DSA tendency previously identified:

> Story/system modeling begins before proving what information actually affects the answer.

Continue enforcing:

```text
objective
→ relevant information
→ discardable information
→ smallest sufficient state
→ only then implementation
```

The next DSA transfer problem should be unfamiliar enough that superficial pattern recall is insufficient.

## 2. Boundary Precision

Two small reasoning slips appeared and were corrected:

* sorted-pair pointer direction
* even-length fast/slow linked-list termination

Continue forcing explicit boundary walkthroughs for pointer problems.

## 3. Queue Invariant Verification

The single-element Queue transition exposed the exact expected invariant burden:

```text
dequeue final node
→ head = null
→ tail = null
→ size = 0
```

The concept is understood.

Before Day 8 implementation work begins, verify that the committed `IntQueue` code contains the `tail = null` correction.

## 4. Valid Parentheses Final Code Cleanup

Reasoning is complete.

Before using the implementation as evidence, verify:

```text
supported openers = (, [, {
supported closers = ), ], }
```

and remove unnecessary `< >` / parsing generalization.

Do not spend another teaching session on this problem.

## 5. Sliding Window

Reasoning improved materially.

The next exposure should be a transfer problem rather than repeating Minimum Size Subarray Sum.

The expected explanation remains:

> Positivity/monotonicity makes one-way boundary movement safe.

## 6. Production Diagnosis

Initial diagnosis sometimes mixed:

```text
symptom
cause
mechanism
```

Continue separating:

```text
Observation
→ mathematical/behavioral condition
→ likely causes
→ evidence that discriminates causes
→ mitigation
```

Example:

```text
queue age grows
→ service rate < arrival rate
→ investigate why service rate fell
```

---

# Ongoing HLD Practice Protocol

Before architecture:

1. Entities
2. Bad outcomes
3. Invariants
4. Failure scenarios
5. Minimum durable state
6. Mechanisms

If an answer starts with a mechanism such as lock, queue, retry, cache, or poll, ask:

> What truth/invariant is this mechanism protecting?

Additional Day 7 refinement:

```text
Durable acceptance
!=
business completion

Lease
!=
fencing

Fencing
!=
idempotency

Queue semantics
!=
durable messaging guarantees
```

---

# Ongoing DSA Practice Protocol

Before coding:

1. What is the objective?
2. What is the brute-force search space?
3. What information actually affects the answer?
4. What details can be discarded?
5. What work is repeated?
6. What can be safely eliminated?
7. What property/invariant makes that elimination safe?
8. What is the smallest sufficient representation/state?
9. Only then choose the pattern/data structure.
10. Code after the reasoning is stable.

Additional Day 7 enforcement:

> Do not expand the input model or introduce extra syntax/state unless the problem statement requires it.

---

# Exact Next Action — Day 8

Generate Day 8 from:

```text
MASTER_CURRICULUM.md
+
this registry
+
Day 7 evidence
```

Day 8 should:

1. Begin with a very short Day 7 closure:
   * verify `IntQueue` clears `tail` when the final element is dequeued
   * verify standard Valid Parentheses implementation uses only `()[]{}`

2. Continue the Phase-1 linked-structure progression toward:
   ```text
   Deque
   ↓
   LRU Cache
   ```

3. Derive Deque as an access contract before choosing representation.

4. Introduce doubly linked-list invariants only to the depth needed for efficient operations at both ends and for LRU Cache.

5. Begin LRU Cache as an early LLD exercise:
   * requirements
   * API contract
   * required complexities
   * invariants
   * why one structure alone is insufficient
   * derive the minimum composite representation before coding

6. Preserve abstraction-first DSA coaching with a fresh transfer problem rather than repeating Minimum Size Subarray Sum.

7. Continue a narrow production/HLD connection without expanding into full messaging internals or Phase-3 concurrency.

8. Add automated tests for every new mutable structure before marking the day complete.

9. Keep implementation deliberately small. Do not add:
   * heap / priority queue
   * full Kafka/SQS internals
   * Java Memory Model
   * advanced concurrency
   * full Notification Service redesign

10. At the end of Day 8, update this registry using demonstrated evidence only.
