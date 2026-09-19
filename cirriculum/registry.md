# Day 15 — AVL Balancing, Rotations & Full Job Scheduler HLD

## Status

**Completed conceptually with AVL implementation intentionally not over-invested**

Day 15 closed the parked BST deletion implementation, established the AVL balancing model and rotation intuition, and completed the one-time Job Scheduler HLD with failure handling, retries, cancellation, idempotency, leases, fencing, observability, and production-debugging reasoning.

The AVL section was deliberately kept to interview-relevant depth. Full AVL insertion implementation and exhaustive rotation tests were not completed and are not blocking curriculum progress.

## Phase

Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution

---

## BST Deletion Maintenance

### Recursive Contract

The deletion contract was clarified and locked in:

```java
Node delete(Node root, int value)
```

means:

> Return the root of this subtree after deletion.

Important distinction:

```text
return value != deleted node
return value != "replacement value"

return value = new root of the affected subtree
```

This explains why recursive reassignment is required:

```java
root.left = delete(root.left, value);
root.right = delete(root.right, value);
```

The recursive call may return:

```text
same subtree root
different subtree root
null
```

and the parent reconnects to that returned root.

### Implementation Demonstrated

```java
public Node delete(Node root, int value){
    if (root == null) return root;

    if (root.value > value) {
        root.left = delete(root.left, value);
    } else if (root.value < value) {
        root.right = delete(root.right, value);
    } else {
        if (root.left == null) return root.right;
        if (root.right == null) return root.left;

        Node succ = getSuccessor(root);
        root.value = succ.value;
        root.right = delete(root.right, succ.value);
    }
    return root;
}

static Node getSuccessor(Node curr) {
    curr = curr.right;
    while (curr != null && curr.left != null) {
        curr = curr.left;
    }
    return curr;
}
```

### Two-Child Case

Correctly used the inorder successor:

```text
minimum node in right subtree
```

Flow:

```text
copy successor value into target
↓
delete original successor from right subtree
↓
return resulting subtree root
```

Understood that the temporary duplicate value is intentional and is removed by the recursive delete.

### Correctness Status

Conceptual and implementation-level deletion understanding is sufficient.

Not demonstrated during this session:

```text
full mutation test suite
delete-until-empty test
size verification
systematic isValidBST() after every mutation
```

These remain useful spaced-retrieval tests, but BST deletion is no longer considered a blocking gap.

---

## AVL Trees — Motivation & Invariant

### Why AVL Exists

Correctly connected the problem with an ordinary BST:

```text
BST ordering can remain valid
while shape degrades to a linked list
```

Example:

```text
1,2,3,4,5,6,7
```

can produce:

```text
height = O(n)
search = O(n)
insert = O(n)
```

So:

```text
BST invariant
→ ordering correctness

AVL invariant
→ height/balance correctness
```

### Height Convention

Day-15 convention:

```text
height(null) = 0
height(leaf) = 1
```

Initial off-by-one misunderstanding was corrected.

Final recurrence:

```text
height(node)
=
1 + max(height(left), height(right))
```

### Balance Factor

Understood:

```text
balanceFactor
=
height(left) - height(right)
```

Valid values:

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

Important correction:

```text
balanceFactor does NOT have to equal 1
```

The invariant is:

```text
|height(left) - height(right)| <= 1
```

---

## AVL Rotations

### Rotation Derivation

For:

```text
    30
   /
  20
 /
10
```

correctly identified that:

```text
20 must become the new subtree root
```

giving:

```text
    20
   /  \
 10    30
```

This was derived structurally rather than by memorizing the label `LL`.

### Rotation Invariant

Understood that a rotation changes tree shape while preserving BST ordering.

Key invariant:

```text
inorder sequence before rotation
=
inorder sequence after rotation
```

### Middle Subtree / T2

For:

```text
        30
       /
      20
     /  \
   10    25
```

correctly identified that after right rotation, `25` must become the left child of `30`:

```text
        20
       /  \
     10    30
          /
         25
```

Reason:

```text
20 < 25 < 30
```

This demonstrated correct understanding of the middle subtree that must not be lost during pointer rewiring.

### Height Metadata After Rotation

Important correction:

> Structural rewiring alone is not enough in an AVL implementation.

Stored height metadata must also be recomputed.

Correct update order:

```text
lower node first
new subtree root second
```

because the new root's height depends on the already-updated child height.

### Left Rotation

For:

```text
10
  \
   20
     \
      30
```

correctly identified:

```text
20 becomes the new subtree root
```

### Double Rotations

Conceptually covered:

```text
LR:
left rotate child
then right rotate parent

RL:
right rotate child
then left rotate parent
```

Mental model:

```text
find unbalanced node
↓
find where excess height appeared
↓
same direction      → single rotation
different direction → double rotation
```

### AVL Scope Decision

For target Senior/Staff backend interviews, required depth retained:

```text
why ordinary BST can degrade
AVL balance invariant
height / balance factor
purpose of rotations
LL/RR/LR/RL intuition
rotation preserves inorder
```

Not completed:

```text
full rotateLeft/rotateRight implementation by user
full AVL insertion implementation
systematic AVL test suite
AVL deletion
```

These are not considered blocking for current interview ROI.

---

## HLD — One-Time Job Scheduler

### Scope

System scope:

```text
one-time scheduler
```

Therefore the core scheduling field is:

```text
scheduledAt
```

not a recurring cron expression.

Possible create model:

```text
jobId
taskType
payload
scheduledAt
```

### Core Functional Requirements

Derived and reasoned through:

```text
create scheduled job
inspect job status
execute at/after scheduledAt
cancel before execution begins
retry according to configured policy
recover from worker death
```

### State Model

Core states:

```text
SCHEDULED
RUNNING
SUCCEEDED
FAILED
CANCELLED
```

Optional retry representation discussed:

```text
RETRY_WAIT
```

### Durable Acceptance

Requirement retained:

> Once job creation returns success, the job must survive process restart.

The create path must persist durably before acknowledging success.

---

## Create API Idempotency

Identified failure:

```text
DB commit succeeds
↓
API server crashes before response
↓
client retries
↓
duplicate jobs may be created
```

Initial idea considered deriving deduplication from business fields such as:

```text
application
scheduledAt
createdBy
taskType
```

This was refined because two legitimate jobs can have identical business fields.

Preferred contract:

```text
clientId + idempotencyKey
```

with a unique constraint.

Semantics:

```text
same key + same request
→ return existing job

same key + different request
→ reject conflict
```

Important distinction:

```text
jobId
→ resource identity

idempotencyKey
→ logical create-request identity
```

Also distinguished:

```text
CREATE idempotency
vs
EXECUTION idempotency
```

---

## Runnable-Job Access Pattern

Workers need:

```text
status = SCHEDULED
AND scheduledAt <= now
```

Corrected misconception that millions of future jobs would all match this condition.

With an index such as:

```text
(status, scheduledAt)
```

the database can efficiently perform a range scan for currently due jobs instead of scanning unrelated future jobs.

Recommended access pattern:

```sql
WHERE status = 'SCHEDULED'
  AND scheduled_at <= now()
ORDER BY scheduled_at
LIMIT batchSize
```

Important concepts:

```text
scheduledAt <= now
→ eligibility

LIMIT
→ bounds work per poll

index(status, scheduledAt)
→ avoids scanning unrelated future rows
```

---

## Worker Contention & Claiming

User independently identified that many workers repeatedly selecting the same rows can waste time on locking/contention.

A hash-based worker assignment idea was proposed:

```text
hash(jobId) % workerCount
```

Trade-offs explored:

```text
worker failure
worker-count changes
rebalancing
membership management
```

For V1, the simpler approach was preferred:

```text
indexed lookup
+
small batches
+
short transactional claim
+
SKIP LOCKED / conditional update
```

### Atomic Claim

Correctly identified the critical transition:

```text
SCHEDULED → RUNNING
```

must be atomic.

Conceptually:

```sql
UPDATE jobs
SET status = 'RUNNING',
    lease_owner = ?,
    lease_until = ?
WHERE job_id = ?
  AND status = 'SCHEDULED';
```

Interpretation:

```text
1 row updated
→ ownership acquired

0 rows updated
→ another worker/state transition won
```

### DB Lock vs Logical Ownership

Important distinction:

```text
database row lock
→ short-lived claim coordination

lease
→ longer-lived execution ownership
```

Database locks must not be held for the full duration of a long-running job.

---

## Dispatcher / Ready Queue Evolution

Proposed architecture improvement:

```text
single master/dispatcher discovers due jobs
workers consume dispatched jobs
```

Refined into:

```text
Dispatcher
→ discovers eligible jobs

Workers
→ execute ready jobs
```

Advantages:

```text
reduces repeated DB polling by all workers
separates scheduling from execution
```

Risks identified:

```text
single dispatcher = single point of failure
DB + external queue introduces dual-write consistency
```

Possible evolution:

```text
SCHEDULED
→ READY
→ RUNNING
→ terminal state
```

V1 decision remains simpler:

```text
direct indexed DB polling
+
atomic claims
+
leases
```

Dispatcher + durable queue is an optimization/evolution when polling or claim contention becomes a real bottleneck.

---

## Lease Semantics

Correctly identified:

```text
worker claims job
↓
worker dies / stops reporting
↓
job must not remain RUNNING forever
```

Lease metadata:

```text
leaseOwner
leaseUntil
```

allows abandoned ownership to expire.

Important nuance:

> Lease expiry does not prove the original worker stopped executing.

It only means the scheduler no longer trusts that worker as the current owner.

Long-running jobs may renew leases periodically while ownership remains valid.

---

## Fencing Tokens

User independently proposed a monotonic counter/version model:

```text
worker A → token 1
worker B → token 2
...
```

This maps directly to a fencing-token design.

Example:

```text
A owns token 41
lease expires

B reclaims with token 42

A later wakes up
→ token 41 is stale
```

Scheduler state updates from the stale owner must be rejected.

Critical distinction:

```text
fencing prevents stale ownership actions
ONLY where the protected boundary validates the token
```

A fencing number by itself does not prevent arbitrary downstream side effects.

---

## Execution Semantics & Idempotency

Strong distinction established:

```text
single valid owner
!=
exactly-once business execution
```

Failure case:

```text
worker performs side effect
↓
worker crashes before marking success
↓
lease expires
↓
another worker retries
↓
duplicate execution attempt occurs
```

Therefore the scheduler should be described as:

```text
at-least-once execution
```

unless a stronger transactional mechanism exists.

Downstream/business effect should ideally use:

```text
idempotencyKey = jobId
```

Important separation:

```text
Lease
→ temporary ownership

Fencing token
→ protects against stale ownership

Idempotency
→ protects business effect from duplicate attempts
```

---

## Retry Policy

Correctly insisted on maintaining a clear scheduler/application boundary.

### Application/task adapter owns

```text
whether a specific failure is retryable
```

Examples may map into generic outcomes:

```text
SUCCESS
RETRYABLE_FAILURE
NON_RETRYABLE_FAILURE
```

### Scheduler owns

```text
attemptCount
maxAttempts
backoff
nextAttemptAt
terminal FAILED transition
```

Possible fields:

```text
attemptCount
maxAttempts
nextAttemptAt
lastFailureReason
```

Important reasoning:

> The scheduler should not embed domain-specific HTTP/business semantics, but it should provide generic retry machinery so every caller does not reinvent orchestration.

Retries stop when:

```text
non-retryable failure
or
maxAttempts exhausted
```

---

## Cancellation Policy

A deliberate V1 boundary was chosen:

```text
SCHEDULED → cancellable
RUNNING   → not cancellable
```

Reasoning:

Once a job is RUNNING, the scheduler may not know whether execution is:

```text
1% complete
99% complete
blocked
already side-effected but not yet acknowledged
```

Therefore V1 avoids pretending arbitrary running work can be safely stopped.

Cancellation must be atomic:

```text
SCHEDULED → CANCELLED
```

If worker claim and cancellation race, only one conditional state transition should win.

---

## Durable Data Model

By the end of the design, the job record conceptually included:

```text
jobId
clientId
idempotencyKey

taskType
payload

scheduledAt
status

attemptCount
maxAttempts
nextAttemptAt

leaseOwner
leaseUntil
fencingToken

createdAt
updatedAt
lastFailureReason
```

Not every field is mandatory in every implementation, but each discussed field maps to a specific correctness or operational requirement.

---

## Observability

Relevant scheduler metrics:

```text
scheduled count
runnable backlog
oldest runnable age
RUNNING count
success rate
failure rate
retry rate
lease expirations
task execution latency
schedule delay
worker utilization
claim conflicts
```

Important derived metric:

```text
scheduleDelay
=
actualStartTime - scheduledAt
```

Useful dimensions:

```text
taskType
workerId
attempt
downstream dependency
fencingToken
failure reason
```

---

## Production Failure Drill

Scenario:

```text
RUNNING ↑
SUCCEEDED ↓
oldest runnable age ↑
lease expirations ↑

CPU normal
DB latency normal
```

### Hypothesis 1 — Lease Duration Too Short / Lease Renewal Failure

User correctly correlated increasing RUNNING jobs and lease expirations with possible lease-policy problems.

Evidence needed:

```text
task execution P50/P95/P99
vs
lease duration

lease-renewal success/failure
```

Important discipline:

Do not immediately increase lease duration.

If workers are actually dead, a longer lease simply delays recovery.

### Hypothesis 2 — Downstream Dependency Slowdown

Correctly identified that:

```text
CPU normal
DB normal
```

can coexist with workers blocked on external I/O.

Evidence:

```text
latency/error rate by downstream dependency
task duration by taskType
thread states
timeouts
```

A downstream outage may not be owned by the scheduler team, but the scheduler must still handle it safely through:

```text
timeouts
retry/backoff
telemetry
bounded execution
```

### Hypothesis 3 — Worker Saturation / Thread Starvation

Increasing worker count was proposed as a possible mitigation, but refined to require evidence first.

Evidence:

```text
activeWorkers / maxWorkers
queue depth
thread dumps
jobs exceeding expected duration
```

Important caution:

```text
more workers
+
slow downstream
→ may amplify the incident
```

Therefore capacity should be increased only after identifying where the bottleneck actually sits.

### Debugging Discipline

Strong improvement demonstrated:

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

Correlation was correctly treated as a hypothesis generator, not proof of root cause.

---

## Day 15 Assessment

### Strong

* BST deletion recursive-return contract
* BST two-child deletion implementation
* AVL motivation and balance-factor reasoning
* understanding that rotation preserves inorder
* T2/middle-subtree reasoning
* distinction between structural rotation and height bookkeeping
* one-time scheduler requirements
* durable acceptance
* create API idempotency
* indexed runnable-job access pattern
* contention awareness
* atomic claim reasoning
* lease semantics
* stale-worker reasoning
* fencing-token intuition
* at-least-once vs exactly-once distinction
* downstream idempotency boundary
* retry ownership split between scheduler and task implementation
* cancellation-state boundary
* dispatcher/queue trade-off reasoning
* production-debugging discipline

### Needs Reinforcement

AVL coding fluency:

```text
rotateLeft
rotateRight
height updates
full AVL insert
rotation test cases
```

This is intentionally lower priority and should be revisited through spaced retrieval rather than blocking progress.

Also worth revisiting later:

```text
BST mutation test suite
scheduler DB/queue dual-write patterns such as outbox
large-scale scheduler partitioning only when scale requires it
```

---

## Exact Day 16 Starting Direction

Proceed to:

```text
Red-Black Tree intuition / TreeMap internals
+
binary heap / PriorityQueue foundations
+
tree DSA transfer
+
short production-debugging block
```

Start with the conceptual bridge:

> AVL and Red-Black trees both preserve logarithmic height, but they make different trade-offs in how strictly they balance the tree.

Then move quickly into heaps/PriorityQueue, which has higher direct interview ROI.
