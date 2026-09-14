# Day 14 — BST Correctness, Deletion, BFS & Job Scheduler HLD Bridge

## Status

**Completed with one parked implementation item**

BST deletion reasoning was covered, but the full deletion implementation/tests were intentionally parked after the concept became low-value friction. Revisit later during the next tree pass.

## Phase

Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution

## Session 1 — Retrieval

**Skipped intentionally.**

Reason:

* Day 13 had just been completed immediately before starting Day 14.
* No additional traversal/BST refresher was needed.

---

## Validate Binary Search Tree

### Global BST Invariant

Correctly identified that BST validity is **global**, not just a parent-child check.

Example understood:

```text
        10
       /  \
      5    15
          /  \
         6    20
```

Even though:

```text
6 < 15
```

the tree is invalid because `6` is inside the right subtree of `10`, therefore it must also satisfy:

```text
6 > 10
```

### Abstraction Derived

Initially reasoned in terms of ancestor/root relationships and path combinations such as:

```text
left-left
left-right
right-left
right-right
```

Progressed to the smaller sufficient state:

```text
lowerBound
upperBound
```

Core invariant understood:

```text
lowerBound < node.value < upperBound
```

Propagation rule:

```text
left  → (lowerBound, node.value)
right → (node.value, upperBound)
```

Important correction reinforced:

```text
going left  → keep lower, tighten upper
going right → tighten lower, keep upper
```

### Base Case

Corrected initial misconception that:

```text
null → false
```

Final understanding:

```text
null → true
```

because an empty subtree contains no node that can violate the BST invariant.

### Boundary Type

Recognized that using:

```java
Integer.MIN_VALUE
Integer.MAX_VALUE
```

as strict bounds can incorrectly reject valid nodes containing those exact values.

Used wider bounds:

```java
Long.MIN_VALUE
Long.MAX_VALUE
```

for an `int`-valued BST.

### Implementation

Implemented bounds-based validation.

Final corrected form:

```java
public boolean isValidBST(Node root) {
    return isValidBST(root, Long.MIN_VALUE, Long.MAX_VALUE);
}

private boolean isValidBST(Node root, long leftBound, long rightBound) {
    if (root == null) {
        return true;
    }

    if (root.value <= leftBound || root.value >= rightBound) {
        return false;
    }

    return isValidBST(root.left, leftBound, root.value)
        && isValidBST(root.right, root.value, rightBound);
}
```

### Bugs / Corrections Encountered

Initial implementation checked only immediate children:

```java
root.left.value > root.value
root.right.value < root.value
```

This failed to enforce ancestor constraints.

Second implementation initially used:

```java
root.value < leftBound || root.value > rightBound
```

which allowed duplicate values on a boundary.

Corrected to:

```java
root.value <= leftBound || root.value >= rightBound
```

under the no-duplicates policy.

### Complexity

Correctly reasoned:

```text
Time  → O(n)
Space → O(h)
```

Reasoning:

* every node is visited once
* constant work is performed per node
* recursive stack depth is bounded by tree height

Balanced tree:

```text
O(log n) stack
```

Skewed tree:

```text
O(n) stack
```

Important precision retained:

```text
h is not automatically O(log n)
```

---

## BST Deletion

### Case Model

Correctly converged on the standard deletion cases:

```text
0 children → remove leaf
1 child    → promote the only child
2 children → replace using predecessor/successor
```

Important correction:

* deleting the root is not a separate structural case
* the root can itself have 0, 1, or 2 children

### Two-Child Replacement

Correctly derived both valid choices:

```text
inorder predecessor
→ maximum value in left subtree
→ rightmost node of left subtree
```

and:

```text
inorder successor
→ minimum value in right subtree
→ leftmost node of right subtree
```

Day 14 standard:

```text
use inorder successor
```

Correctly explained why the successor preserves the BST invariant.

### Successor Detail

Important subtlety covered:

> The inorder successor is not guaranteed to be a leaf.

It may have a right child.

Therefore removing the successor from its original location must reconnect that right child if present.

### Recursive-Return Contract

Discussed:

```java
Node delete(Node node, int value)
```

with the contract:

> Return the root of this subtree after deletion.

The recursive formulation was explained, including:

```java
node.left = delete(node.left, value);
node.right = delete(node.right, value);
```

However, this abstraction caused unnecessary friction during the session.

### Preferred Mental Model During Session

User reasoned more naturally using explicit parent references:

```text
find target + parent

0 children:
    disconnect target from parent

1 child:
    point parent directly to target's child

2 children:
    find successor + successorParent
    copy successor value into target
    reconnect successorParent to successor.right
```

This iterative/parent-based approach was accepted as a valid implementation strategy.

### Implementation Status

**Parked / incomplete**

An initial implementation was attempted but did not correctly:

* disconnect leaf nodes from their parents
* handle one-child promotion
* remove the original successor after copying its value
* maintain the required subtree-root contract

Rather than over-invest time, implementation was intentionally deferred.

### Revisit Requirement

On the next tree revision pass, implement and test:

```text
delete missing value
delete leaf
delete one-child node
delete two-child node
delete root
delete until empty
size correctness
contains(deleted) == false
inorder remains sorted
isValidBST(root) == true after each mutation
```

---

## BFS / Level-Order Traversal

### Queue Derivation

Correctly derived FIFO from first principles.

Reasoning:

```text
first discovered
→ first processed
```

Therefore:

```text
FIFO
→ Queue
```

No memorized dependency on "BFS uses queue" was needed.

### Flat BFS Implementation

Implemented:

```java
private void printBFS(Node root) {
    Node node = root;
    Queue<Node> bfsQueue = new ArrayDeque<Node>();
    bfsQueue.offer(node);

    while (!bfsQueue.isEmpty()) {
        System.out.println(bfsQueue.peek().value);
        Node topNode = bfsQueue.poll();

        if (topNode.left != null) {
            bfsQueue.offer(topNode.left);
        }

        if (topNode.right != null) {
            bfsQueue.offer(topNode.right);
        }
    }
}
```

Implementation was logically correct for non-null roots.

Refinements identified:

* handle `root == null` before adding to `ArrayDeque`
* `peek()` is redundant because `poll()` already returns the current node

Preferred form:

```java
private void printBFS(Node root) {
    if (root == null) {
        return;
    }

    Queue<Node> bfsQueue = new ArrayDeque<>();
    bfsQueue.offer(root);

    while (!bfsQueue.isEmpty()) {
        Node current = bfsQueue.poll();

        System.out.println(current.value);

        if (current.left != null) {
            bfsQueue.offer(current.left);
        }

        if (current.right != null) {
            bfsQueue.offer(current.right);
        }
    }
}
```

### BFS Invariant

Understood:

> The queue contains discovered but not-yet-processed nodes in BFS order.

### Complexity

Correctly reasoned:

```text
Time  → O(n)
Space → O(w)
```

where:

```text
w = maximum frontier / tree width
```

Reasoning:

* each node is enqueued once
* each node is dequeued once
* maximum queue occupancy determines auxiliary space

Also recognized that a skewed tree can have:

```text
BFS auxiliary space = O(1)
```

despite:

```text
height = O(n)
```

### Level Grouping

Proposed a valid alternative representation:

```java
Queue<List<Node>>
```

where each queue element represents one entire level.

Understood that this works by:

```text
process current List<Node>
build next List<Node>
enqueue next list
```

Also learned the standard interview pattern:

```java
int levelSize = queue.size();
```

with:

```java
Queue<Node>
```

to snapshot the current frontier.

Understood both approaches encode the same level boundary.

---

## Java Collections — Queue Choice

Preferred abstraction:

```java
Queue<Node> queue = new ArrayDeque<>();
```

Correctly distinguished:

```text
Queue<Node>  → behavioral abstraction
ArrayDeque   → concrete implementation
```

Understood why `ArrayDeque` is generally preferred over `LinkedList` for BFS:

```text
fewer per-element allocations
less object overhead
less pointer chasing
better cache locality
```

This successfully connected back to Phase-1 memory/cache-locality reasoning.

---

## HLD Bridge — One-Time Job Scheduler

### Scope Correction

Initially framed the system as a cron scheduler.

Corrected scope:

```text
one-time scheduled job
```

Example:

```text
run once at 2026-09-16 02:00
```

Therefore Day 14 needs:

```text
scheduledAt
```

rather than a recurring cron expression.

Recurring jobs remain out of scope.

### Functional Requirements

Derived minimum requirements:

```text
create scheduled job
cancel scheduled job
execute at/after scheduled time
inspect job status
```

Possible statuses discussed:

```text
SCHEDULED
RUNNING
SUCCEEDED
FAILED
CANCELLED
```

Clarified:

```text
CANCELLED → must not execute
SUCCEEDED → terminal
FAILED    → retry policy dependent
RUNNING   → already owned/processing
```

### Bad Outcomes Identified

Reasoned about:

```text
worker dies while executing
job stays RUNNING forever
job fails without retry or terminal status
two workers execute the same job
cancelled job still executes
job executes before scheduledAt
job disappears after create returned success
```

User independently raised:

* long-running jobs
* worker execution timeout
* worker death
* missing completion/status update
* multiple workers picking jobs

These are strong seeds for Day 15 leases, ownership, retries, and recovery.

### Durable State

Minimum durable state understood:

```text
jobId
task/payload reference
scheduledAt
status
```

Additional execution metadata may include:

```text
retryCount
lastAttemptAt
ownership / lease metadata
version
```

Key reasoning:

> Once create returns success, the job record must survive process failure/restart.

Also recognized that terminal statuses should be durable if history/inspection and duplicate prevention depend on them.

### Safety Invariant

Demonstrated:

> Once job creation returns success, the scheduled job must survive a process restart.

### Liveness Invariant

Demonstrated:

> A runnable job must not remain stuck indefinitely just because the worker that owned it died.

### Technology Selection Discipline

No premature selection of:

```text
Kafka
Redis
Postgres
```

Architecture/mechanism selection was deferred.

---

## Day 14 Assessment

### Strong

* Global-vs-local BST invariant
* Bounds-based BST validation
* Recursive bound propagation
* Validate-BST implementation
* `O(n)` / `O(h)` complexity reasoning
* BFS derivation from FIFO
* BFS implementation
* `O(w)` auxiliary-space reasoning
* Java `Queue<Node>` + `ArrayDeque`
* cache-locality reasoning
* Job Scheduler failure-mode thinking
* safety vs liveness distinction
* durable-state reasoning

### Needs Reinforcement

BST deletion implementation:

```text
recursive subtree-root return contract
or
explicit parent-based pointer rewiring
```

Conceptual deletion understanding is sufficient; implementation fluency is not yet complete.

Do not block curriculum progress on this now.

---

## Exact Day 15 Starting Direction

Continue the Job Scheduler into full HLD:

```text
API
storage/access pattern
worker discovery
ownership
leases
fencing
retries
idempotency
failure recovery
observability
```

Then continue the balanced-tree progression as scheduled.

Carry forward one maintenance item:

```text
BST deletion implementation + mutation tests
```

but treat it as spaced retrieval / reinforcement rather than restarting Day 14.
