# Day 7 — Stack, Queue & Sliding-Window Reasoning

> **Duration:** ~2 hr 40 min  
> **Phase:** Phase 1 — Foundations: Data Structures, Complexity & Memory  
> **Primary Theme:** Derive LIFO/FIFO structures from access requirements, implement them correctly, and strengthen sliding-window intuition.  
> **HLD Scope:** Narrow production connection only; no more concurrency depth today.

---

# Starting Point

Day 6 already covered:

- linked-list implementation and testing
- reverse linked list
- fast/slow middle detection
- cycle-detection reasoning
- fixed-gap reasoning for remove-Nth-from-end
- sorted two-pointer elimination
- introductory sliding-window intuition
- job ownership, leases, fencing, and at-least-once execution reasoning

So Day 7 should **not** repeat linked-list fundamentals or job ownership.

The two important carry-forwards are:

- sliding-window intuition still needs repetition
- DSA pattern recognition must continue to come from the **property that permits elimination**, not story cues

The next structural progression is:

```text
Linked List
    ↓
Stack
    ↓
Queue
    ↓
Deque
```

Today: **Stack + Queue**.

---

# Today's Objectives

By the end of Day 7, you should be able to:

- identify LIFO vs FIFO requirements before naming a data structure
- explain Stack and Queue as abstract data types
- implement `IntStack`
- implement `IntQueue`
- maintain empty/single/multi-element invariants
- solve a stack problem from first principles
- derive sliding window from positivity/monotonicity
- explain why a nested-looking sliding-window solution can still be O(n)
- compare `ArrayDeque` and linked representations conceptually
- distinguish an in-memory Queue from a durable messaging system

---

# Session 1 — Mixed DSA Pattern Recall

**15 minutes**

No coding.

For each scenario, answer:

```text
Objective
↓
Relevant information
↓
Brute-force search space
↓
Repeated work
↓
What can be eliminated?
↓
What property makes elimination safe?
↓
Minimum necessary state
```

## A — Membership

You receive integers one by one and need to know whether the current value appeared earlier.

Do not say `HashSet` first.

First identify the repeated question.

## B — Sorted Pair

Sorted array + target pair sum.

Explain exactly why moving one boundary permanently eliminates candidates.

## C — Middle of Linked List

No length calculation allowed.

Explain what relative pointer speed gives you.

## D — Minimum Contiguous Sum

Positive integers, target sum, minimum-length contiguous region.

Explain what positivity tells you about expanding and shrinking the region.

**Success criterion:** identify the reasoning property before naming the technique.

---

# Session 2 — `removeNthFromEnd` Closure

**Maximum: 10 minutes**

Only do this if the implementation remains incomplete.

Do not re-study the algorithm.

Use:

```text
dummy → head → ...
```

Then:

```text
establish fixed gap once
↓
move both pointers together
↓
slow reaches predecessor
↓
unlink one node
```

You should answer:

- Why establish the gap only once?
- Why does the dummy simplify head removal?
- Where must `slow` end up?

If already implemented correctly, **skip this session**.

---

# Session 3 — Stack & Queue From First Principles

**20 minutes**

Do not begin with Java APIs.

## Stack

Operations occur at one logical end.

```text
push A
push B
push C

pop → C
```

Therefore:

```text
LIFO
Last In, First Out
```

Required contract:

```text
push
pop
peek
size
isEmpty
```

## Queue

```text
enqueue A
enqueue B
enqueue C

dequeue → A
```

Therefore:

```text
FIFO
First In, First Out
```

Required contract:

```text
enqueue
dequeue
peek
size
isEmpty
```

### Important Mental Model

Stack and Queue are **behavioral contracts**, not physical layouts.

They could be implemented using:

```text
array
dynamic array
linked nodes
circular array
```

Implementation changes performance/memory behavior.

It does not change LIFO/FIFO semantics.

---

# Session 4 — Engineering Lab: `IntStack`

**20 minutes**

No Java Collections.

Before coding, choose between:

```text
linked-list head
```

or:

```text
dynamic-array end
```

and justify the decision.

Target:

```text
push  O(1)
pop   O(1)
peek  O(1)
```

## Required Invariant

If linked:

```text
size == 0
→ top == null
```

and:

```text
top
=
next value pop() must return
```

If array-backed, state the equivalent index invariant yourself.

## Required Tests

- empty
- one push
- multiple pushes
- LIFO pop
- peek does not remove
- repeated pop reaches empty
- empty pop
- empty peek
- correct size transitions

Do not implement extras.

---

# Session 5 — Engineering Lab: `IntQueue`

**25 minutes**

For V1, use linked nodes with:

```text
head
tail
size
```

because Day 6 already established those invariants.

Target:

```text
enqueue O(1)
dequeue O(1)
peek    O(1)
```

## Invariants

Empty:

```text
head == null
tail == null
size == 0
```

Non-empty:

```text
head != null
tail != null
tail.next == null
```

Meaning:

```text
head
→ next node to dequeue

tail
→ most recently enqueued node
```

### Critical Edge Case

Start:

```text
A
head = A
tail = A
```

Then:

```text
dequeue()
```

If `head` becomes null, what must happen to `tail`?

This is the main queue mutation invariant for today.

## Required Tests

- empty
- enqueue into empty
- multiple enqueue
- FIFO order
- peek without removal
- dequeue several
- dequeue final node
- empty dequeue
- empty peek
- enqueue again after becoming empty
- size correctness

Before assertions, explicitly draw:

```text
before
→ operation
→ after
```

---

# Session 6 — DSA Problem 1: Valid Parentheses

**20–25 minutes**

Do **not** start with:

> "This is a Stack problem."

First answer:

## Objective

What exactly makes the string valid?

## Relevant Historical Information

When encountering:

```text
)
```

do you care about the **first** unmatched opening bracket or the **most recent** unmatched opening bracket?

Why?

## Smallest Sufficient State

What unresolved information must survive as you scan?

Only after answering that should LIFO emerge.

## Target Invariant

Before processing the next character:

> The retained state contains exactly the unmatched opening brackets from the processed prefix, in the order required to validate the next closing bracket.

Test:

```text
()
([{}])
(]
([)]
(
)
```

Complexity:

```text
Time: O(n)
Space: O(n) worst case
```

---

# Session 7 — DSA Problem 2: Minimum Size Subarray Sum

**25–30 minutes**

This is today's main DSA reasoning exercise.

Given:

```text
positive integers
+
target
```

find the minimum-length contiguous subarray whose sum is at least the target.

Do not name the technique initially.

## Start With Brute Force

For every start position, try possible endings.

Ask:

> What work is being repeated?

Then inspect the crucial constraint:

```text
every value > 0
```

That gives:

```text
extend right
→ sum cannot decrease
```

and:

```text
move left rightward
→ sum cannot increase
```

That monotonic behavior is what makes the moving window safe.

Eventually derive state:

```text
left
right
windowSum
minLength
```

## Mandatory Complexity Derivation

You may have:

```text
for right ...
    while windowSum >= target ...
```

Do not incorrectly call this O(n²).

Reason:

```text
right moves at most n times
left moves at most n times
```

Total boundary movement:

```text
≤ 2n
```

Therefore:

```text
O(n)
```

### Critical Transfer Question

What breaks if the array contains negative numbers?

If you can answer that clearly, the sliding-window mental model is improving.

---

# Session 8 — Java/JVM Connection

**15 minutes**

Compare conceptually:

```text
Stack
ArrayDeque
LinkedList
```

## Legacy `Stack`

Understand that Java's `Stack` is an older abstraction based on `Vector`.

For normal modern stack usage, think in terms of a `Deque`.

## `ArrayDeque`

Array-backed.

Useful for:

```text
stack semantics
queue semantics
operations at both ends
```

Potential practical advantages:

- fewer node allocations
- less reference indirection
- better locality

## `LinkedList`

Can also implement queue/deque semantics but has:

- node objects
- object headers
- references
- pointer chasing
- weaker locality

### Interview Question

Both can have O(1) end operations.

Why might one still be significantly faster?

Your answer must separate:

```text
algorithmic complexity
```

from:

```text
allocation
memory layout
cache behavior
```

---

# Session 9 — Narrow HLD: Queue vs Durable Messaging

**20 minutes**

Actual HLD exposure continues, but narrowly.

Scenario:

```text
POST /notifications
```

The API receives a request to send email/SMS.

Sending synchronously is slow.

Initial architecture:

```text
API
↓
in-memory Queue
↓
worker
↓
external provider
```

Do **not** say Kafka/SQS immediately.

Reason from requirements.

## Failure

Suppose:

```text
request accepted
↓
API returns success
↓
process crashes
↓
in-memory queue disappears
```

Requirement:

> An accepted notification must not silently disappear.

Ask:

> Which invariant did the design violate?

Target:

> Once durable acceptance is acknowledged, enough state must survive process failure to eventually continue processing.

Only after deriving that should the architecture move toward durable storage/messaging.

### Core HLD Distinction

```text
Queue data structure
!=
Durable message queue
```

Same high-level ordering idea.

Very different system guarantees.

---

# Session 10 — Production Scenario: Growing Queue

**10 minutes**

Metrics:

```text
producer rate:      8,500/sec
consumer rate:      6,000/sec
queue depth:        steadily rising
CPU:                45%
DB latency:         normal
provider latency:   elevated
error rate:         low
```

Diagnose:

1. Is the system keeping up?
2. Why can CPU remain moderate while queue depth grows?
3. Where is the likely bottleneck?
4. What happens to end-to-end latency?
5. Why does simply increasing queue capacity not solve the problem?
6. What should you alert on?

Key reasoning:

```text
arrival rate > service rate
```

for a sustained period means backlog must grow.

A larger queue only delays the consequence.

---

# Deliverables

- [ ] mixed pattern recall completed
- [ ] `removeNthFromEnd` finalized if needed
- [ ] `IntStack` implemented
- [ ] Stack tests pass
- [ ] `IntQueue` implemented
- [ ] Queue tests pass
- [ ] single-element queue transition handled correctly
- [ ] Valid Parentheses reasoned before choosing Stack
- [ ] Valid Parentheses implemented/tested
- [ ] Minimum Size Subarray Sum derived from positivity
- [ ] sliding-window O(n) complexity explained from total pointer movement
- [ ] negative-number limitation explained
- [ ] Stack / ArrayDeque / LinkedList runtime trade-off explained
- [ ] in-memory queue vs durable messaging distinction explained
- [ ] production queue-backlog scenario diagnosed

---

# End-of-Day Reflection

Answer without notes:

1. What makes a Stack a Stack regardless of implementation?
2. What makes a Queue a Queue?
3. Why does maintaining `tail` make queue enqueue O(1)?
4. What new correctness burden does `tail` create?
5. Why does Valid Parentheses require the most recent unmatched opener?
6. What exact property enables today's sliding window?
7. Why is the nested-loop implementation still O(n)?
8. Why do negative values break the same argument?
9. Why can `ArrayDeque` outperform a linked representation despite identical Big-O?
10. Why is an in-memory Queue insufficient once acknowledged work must survive process crashes?
11. If producer throughput permanently exceeds consumer throughput, what must eventually happen?
12. Which metric best reveals that mismatch?

---

# End-of-Day Exit Criteria

Day 7 is complete only if you can demonstrate:

## Data Structures

```text
Stack = LIFO
Queue = FIFO
```

and implement both correctly with automated edge-case tests.

## DSA

For both problems:

```text
objective
→ relevant information
→ brute force
→ property/invariant
→ minimum state
→ solution
```

must come **before pattern naming**.

## Sliding Window

You can explain:

> Positivity creates monotonic changes to the window sum, which makes one-way boundary movement safe.

Not merely:

> "I know this is sliding window."

## Engineering

You can explain why convenience state such as `tail` improves performance while increasing invariant burden.

## HLD

You can derive a durability requirement from a crash scenario without starting with a technology name.

---

# Intentionally Deferred

Do **not** add today:

- circular-array Queue
- Deque implementation
- monotonic Stack
- heap / priority queue
- LRU Cache
- Kafka/SQS internals
- advanced backpressure
- Java Memory Model
- locks/CAS
- more leases/fencing
- full Notification Service design

Those are separate steps.

---

# Exact Starting Action

Start Day 7 with this:

> **Before choosing an array, map, set, stack, queue, or pointer technique for a new DSA problem, what facts must I extract from the problem first?**

Then immediately do the four pattern-recall drills.

---

# Registry Update Requirements

At the end of Day 7, update `cirriculum/registry.md` using evidence only.

Capture:

1. pattern-recall performance
2. `removeNthFromEnd` status
3. Stack implementation/tests
4. Queue implementation/tests
5. Valid Parentheses reasoning/implementation
6. sliding-window reasoning/implementation
7. new boundary/invariant mistakes
8. Java/JVM distinctions demonstrated
9. HLD durability/queue insight demonstrated
10. exact next action for Day 8
