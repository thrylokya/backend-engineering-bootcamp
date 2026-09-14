# Day 15 — AVL Balancing, Rotations & Full Job Scheduler HLD

> **Duration:** ~3 hours  
> **Phase:** Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution  
> **Primary DSA Theme:** Derive AVL balancing from invariants, not memorized rotation cases.  
> **Primary Engineering Theme:** Brief BST deletion maintenance, then balanced-tree implementation.  
> **Primary HLD Theme:** Full one-time Job Scheduler design.  
> **Production Theme:** Failure diagnosis around leases, stuck jobs, and worker execution.

---

# Why Day 15 Looks Like This

Day 14 demonstrated:

```text
Validate BST ✅
BFS / level order ✅
ArrayDeque reasoning ✅
Job Scheduler requirements/invariants ✅
```

One item remains partially incomplete:

```text
BST deletion implementation + mutation tests
```

The registry explicitly says not to block progress on it.

So Day 15 is:

```text
short deletion maintenance
+
AVL balancing
+
full Job Scheduler HLD
+
production failure drill
```

---

# Schedule Overview

```text
Session 1 — BST Deletion Maintenance               20 min
Session 2 — AVL Motivation & Balance Factor        25 min
Session 3 — AVL Rotations From First Principles    45 min
Session 4 — HLD: Job Scheduler V1                  55 min
Session 5 — Production Failure Drill               20 min
Session 6 — Retrieval / Registry Evidence          15 min
                                                  ------
                                                  180 min
```

---

# Session 1 — BST Deletion Maintenance

**20 minutes**

Do not re-teach deletion from scratch.

Preferred recursive contract:

```java
Node delete(Node node, int key)
```

Meaning:

> Return the root of this subtree after deletion.

Cases:

```text
0 children → return null
1 child    → return only child
2 children → copy inorder successor value
              then delete successor from right subtree
```

Required minimal tests:

```text
delete leaf
delete one-child node
delete two-child node
delete root
```

After each:

```text
isValidBST(root) == true
```

and:

```text
inorder remains sorted
```

If implementation is still sticky after 20 minutes:

```text
park again
```

Do not sacrifice AVL/HLD time.

---

# Session 2 — Why AVL Trees Exist

**25 minutes**

A plain BST can remain perfectly valid and still degrade badly.

Insert:

```text
1,2,3,4,5,6,7
```

Result:

```text
height = O(n)
search = O(n)
insert = O(n)
```

The BST invariant is not broken.

The problem is shape.

## AVL Invariant

For every node:

```text
|height(left) - height(right)| <= 1
```

Define:

```text
balanceFactor
=
height(left) - height(right)
```

Valid:

```text
-1
0
+1
```

Invalid:

```text
<= -2
>= +2
```

Important distinction:

```text
BST invariant
→ ordering correctness

AVL invariant
→ balance / height correctness
```

For today:

```text
null height = 0
leaf height = 1
```

and:

```text
height(node)
=
1 + max(height(left), height(right))
```

---

# Session 3 — AVL Rotations From First Principles

**45 minutes**

Do not memorize LL/RR/LR/RL as arbitrary labels.

Ask:

> Where did the extra height appear?

## Left-Left

Insert:

```text
30,20,10
```

Before:

```text
    30
   /
  20
 /
10
```

Right rotation around 30:

```text
    20
   /  \
 10    30
```

## Right-Right

Insert:

```text
10,20,30
```

Left rotation around 10:

```text
    20
   /  \
 10    30
```

## Left-Right

Insert:

```text
30,10,20
```

Repair:

```text
left rotate 10
then
right rotate 30
```

## Right-Left

Insert:

```text
10,30,20
```

Repair:

```text
right rotate 30
then
left rotate 10
```

## Rotation Invariant

A rotation may change shape, but must preserve:

```text
inorder sequence
```

So ordering remains valid.

## Implement

```java
Node rotateRight(Node y)
Node rotateLeft(Node x)
```

Take care not to lose the middle subtree (`T2`).

After rewiring, recompute heights:

```text
lower node first
new root second
```

## AVL Insert Flow

```text
1. normal BST insert
2. update height
3. compute balance factor
4. identify heavy direction
5. rotate if required
6. return new subtree root
```

Required tests:

```text
30,20,10 → LL
10,20,30 → RR
30,10,20 → LR
10,30,20 → RL
```

For each verify:

```text
root == 20
inorder sorted
isValidBST == true
AVL invariant valid
```

Then try:

```text
10,20,30,40,50,25
```

Complexity:

```text
rotation → O(1)
AVL height → O(log n)
search / insert → O(log n)
```

---

# Session 4 — Full HLD: One-Time Job Scheduler V1

**55 minutes**

Continue from Day 14.

Already established:

```text
create scheduled job
cancel scheduled job
execute at/after scheduledAt
inspect status
```

and invariants:

```text
API success → job survives restart
job must not execute early
dead worker must not leave job stuck forever
ownership must be controlled
```

Now complete the architecture.

## API

Possible shape:

```text
POST /jobs
GET  /jobs/{jobId}
POST /jobs/{jobId}/cancel
```

Create request:

```text
taskType
payload / task reference
scheduledAt
```

Do not return success before durable persistence.

## Durable State

Minimum:

```text
jobId
taskType
payload / reference
scheduledAt
status
createdAt
updatedAt
attemptCount
leaseOwner?
leaseUntil?
fencingToken?
```

Statuses:

```text
SCHEDULED
RUNNING
SUCCEEDED
FAILED
CANCELLED
```

## Access Pattern

Workers need:

> Which jobs are runnable now?

Conceptually:

```text
status = SCHEDULED
and
scheduledAt <= now
```

Storage must support this efficiently.

## Worker Discovery

Simplest V1:

```text
workers poll durable storage
for eligible jobs
```

Problem:

```text
multiple workers may observe the same job
```

So eligibility read is not enough.

## Atomic Claim

Claim only if the job is still runnable and unowned.

Conceptually:

```text
SCHEDULED
→ RUNNING
```

plus:

```text
leaseOwner
leaseUntil
```

in one atomic state transition.

## Lease

Lease gives temporary ownership.

If worker dies:

```text
lease expires
→ job can be reclaimed
```

This solves stuck ownership.

But lease alone is not sufficient.

## Stale Worker Scenario

```text
worker A gets lease
A pauses
lease expires
worker B claims job
A wakes up
A still thinks it owns job
```

This creates stale-owner risk.

## Fencing Token

Every successful claim gets increasing token:

```text
41
42
43
```

Newer owner has higher token.

A protected downstream resource must reject stale tokens for fencing to work.

Important:

> A fencing number by itself does nothing unless the receiving boundary validates it.

## Execution Semantics

Assume:

```text
at-least-once execution
```

Why?

If worker crashes:

```text
after side effect
but
before recording success
```

the scheduler may retry.

So:

```text
duplicate execution attempt is possible
```

## Idempotency

Ownership and business effect are different concerns.

Possible:

```text
idempotencyKey = jobId
```

If downstream supports idempotency:

```text
same jobId
→ same business effect
```

Do not claim exactly-once because only one worker owns the job.

## Worker Death Recovery

If:

```text
status = RUNNING
leaseUntil < now
```

job is abandoned.

Recovery transitions it back to:

```text
SCHEDULED
```

or a retry state.

This satisfies liveness.

## Retry Policy

Need:

```text
attemptCount
maxAttempts
backoff
nextAttemptAt
```

Transient failures:

```text
retry with backoff
```

Exhausted/permanent:

```text
FAILED
```

## Cancellation

If:

```text
SCHEDULED
```

transition atomically to:

```text
CANCELLED
```

If already RUNNING, choose an explicit policy:

```text
reject cancellation
```

or:

```text
best effort
```

Do not pretend arbitrary running work can always be stopped instantly.

## Failure Scenarios

Walk through:

```text
API crashes after DB commit before response
worker crashes before side effect
worker crashes after side effect before success update
storage unavailable
worker paused beyond lease expiry
```

For each identify:

```text
safety risk
liveness risk
recovery
```

## Observability

Metrics:

```text
scheduled count
runnable backlog
oldest runnable age
schedule delay
execution latency
success rate
failure rate
retry rate
lease expirations
stuck RUNNING count
claim conflicts
```

Key:

```text
schedule delay
=
actualStartTime - scheduledAt
```

Logs/traces:

```text
jobId
attempt
workerId
fencingToken
state transition
failure reason
```

## HLD Exit

Be able to explain:

```text
durable acceptance
↓
eligible lookup
↓
atomic claim
↓
lease
↓
fencing
↓
execution
↓
idempotency
↓
retry / recovery
↓
terminal state
```

without jumping immediately to Kafka/Redis.

---

# Session 5 — Production Failure Drill

**20 minutes**

Scenario:

```text
job arrival rate normal

RUNNING jobs ↑
SUCCEEDED jobs ↓
oldest runnable age ↑
lease expirations ↑

CPU normal
DB latency normal
```

Workers appear healthy at process level.

Use:

```text
Observation
↓
Hypothesis
↓
Supporting evidence
↓
Rejecting evidence
↓
Mitigation
```

Consider at least three:

```text
task dependency slowdown
worker thread starvation
lease duration too short
lease-renewal failure
missing task timeout
external service degradation
```

Do not declare a root cause without evidence.

---

# Session 6 — Retrieval / Registry Evidence

**15 minutes**

No notes.

## AVL

1. What problem does AVL solve that ordinary BST does not?
2. State the AVL invariant.
3. What must rotation preserve?
4. Derive LL repair.
5. Derive LR repair.
6. Why is rotation O(1)?
7. Why does balancing give O(log n) search?

## Job Scheduler

8. Why is durable create a safety requirement?
9. Why are leases needed?
10. Why are leases alone insufficient?
11. What does fencing protect against?
12. Why does at-least-once execution create idempotency concerns?
13. What happens when a RUNNING lease expires?
14. Difference between ownership and exactly-once business effect?
15. Name two scheduler liveness metrics.

---

# Day 15 Deliverables

## BST Maintenance

- [ ] deletion revisited
- [ ] core deletion cases tested or explicitly re-parked
- [ ] `isValidBST()` used as a correctness oracle

## AVL

- [ ] height convention fixed
- [ ] balance factor understood
- [ ] LL derived
- [ ] RR derived
- [ ] LR derived
- [ ] RL derived
- [ ] left/right rotations implemented
- [ ] heights updated correctly
- [ ] AVL insert implemented or substantially completed
- [ ] inorder preserved
- [ ] balance invariant tested

## HLD

- [ ] API
- [ ] durable data model
- [ ] runnable-job access pattern
- [ ] worker discovery
- [ ] atomic claim
- [ ] lease semantics
- [ ] stale-worker scenario
- [ ] fencing token
- [ ] at-least-once semantics
- [ ] idempotency boundary
- [ ] worker-death recovery
- [ ] retries/backoff
- [ ] cancellation semantics
- [ ] failure scenarios
- [ ] observability

## Production Debugging

- [ ] 3+ hypotheses
- [ ] support evidence for each
- [ ] reject evidence for each
- [ ] no unsupported root-cause jump

---

# End-of-Day Exit Criteria

## Trees

You can explain:

```text
BST ordering may remain valid
while performance becomes O(n)
```

and:

```text
AVL adds a balance invariant
```

You derive rotations from where excess height appeared instead of recalling labels mechanically.

## HLD

You understand that:

```text
durable storage
lease
fencing
retry
idempotency
```

solve different problems.

You do **not** equate single ownership with exactly-once business execution.

---

# Intentionally Deferred

Do not add today:

- AVL deletion
- Red-Black Tree rules
- TreeMap internals
- B/B+ tree details
- binary heap
- trie
- graph algorithms
- Kafka-based scheduler
- leader election
- sharded scheduler
- recurring cron semantics
- multi-region scheduler
- workflow DAGs

---

# Day 15 Exact Starting Action

Start with the parked item for no more than 20 minutes:

> **Implement BST deletion using `Node delete(Node node, int key)`, then verify it using `isValidBST()` and inorder traversal.**

Then:

> **If a BST can remain valid and still become O(n), what additional invariant would keep its height under control?**

For HLD, continue from Day 14:

> **Multiple workers can see the same runnable job. What state transition must be atomic so only one worker acquires valid ownership?**

---

# Registry Update Requirements

At the end of Day 15, capture demonstrated evidence only:

1. BST deletion status
2. deletion bugs/corrections
3. AVL balance-factor reasoning
4. rotation derivation quality
5. rotation implementation
6. AVL insert status/tests
7. height-update mistakes
8. Job Scheduler API/data model
9. runnable-job access pattern
10. atomic claim reasoning
11. lease understanding
12. fencing understanding
13. stale-worker reasoning
14. at-least-once vs exactly-once precision
15. idempotency reasoning
16. retry/recovery design
17. cancellation policy
18. scheduler observability
19. production-debugging discipline
20. exact Day-16 starting action

Recommended Day-16 direction if Day 15 is strong:

```text
Red-Black Tree intuition / TreeMap internals
+
binary heap / PriorityQueue foundations
+
tree DSA transfer
+
short production-debugging block
```
