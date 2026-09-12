# Day 14 — BST Correctness, Deletion, Level-Order Traversal & HLD Bridge

> **Duration:** ~3 hours  
> **Phase:** Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution  
> **Primary DSA Theme:** Global tree invariants and structural mutation.  
> **Primary Engineering Theme:** Extend BST V1 with validation and deletion.  
> **Traversal Theme:** Introduce BFS / level-order traversal using a queue.  
> **HLD Theme:** Short Job Scheduler design bridge; full HLD resumes Day 15.  
> **Phase-1 Maintenance:** Small spaced retrieval only.

---

# Why Day 14 Looks Like This

Day 13 established:

```text
binary tree vs BST
tree vocabulary
recursive subtree decomposition
preorder / inorder / postorder
BST search
BST insert
tree size
maximum depth
recursive stack O(h)
balanced vs skewed complexity
```

The next logical progression is:

```text
validate a global BST invariant
↓
mutate a BST while preserving correctness
↓
traverse by breadth instead of depth
↓
maintain HLD continuity
```

Do not start AVL rotations yet.

---

# Schedule Overview

```text
Session 1 — Retrieval & Precision Gate             15 min
Session 2 — DSA: Validate Binary Search Tree       35 min
Session 3 — Engineering: BST Delete                45 min
Session 4 — BFS / Level-Order Traversal            30 min
Session 5 — Java / Collections: Queue Choice       15 min
Session 6 — HLD Bridge: Job Scheduler              25 min
Session 7 — Retrieval / Production Connection      15 min
                                                  ------
                                                  180 min
```

---

# Session 1 — Retrieval & Precision Gate

**15 minutes**

No notes.

## A. Recursive Tree Model

Use:

```text
base case
↓
solve left subtree
solve right subtree
↓
combine
```

## B. Traversal Retrieval

For:

```text
        8
       / \
      3   10
     / \    \
    1   6    14
```

Produce:

```text
preorder
inorder
postorder
```

Pay special attention to:

```text
postorder = left, right, node
```

## C. BST Invariant

State it precisely:

> For every node, every key in the left subtree is smaller and every key in the right subtree is greater, under the no-duplicates policy.

Important:

```text
entire subtree
not only immediate children
```

## D. Complexity

Why is BST search:

```text
O(h)
```

rather than automatically:

```text
O(log n)
```

---

# Session 2 — DSA: Validate Binary Search Tree

**35 minutes**

Problem:

> Given the root of a binary tree, determine whether it satisfies the BST invariant.

Do not begin with code.

## Why Local Child Checks Are Insufficient

Consider:

```text
        10
       /  \
      5    15
          /  \
         6    20
```

Locally:

```text
6 < 15
20 > 15
```

But the tree is invalid because:

```text
6
```

is in the right subtree of `10` while:

```text
6 < 10
```

Therefore the problem needs constraints inherited from ancestors.

## Smallest Sufficient State

Each subtree needs:

```text
lower bound
upper bound
```

At root:

```text
(-∞, +∞)
```

Moving left from value `v`:

```text
upper = v
```

Moving right:

```text
lower = v
```

## Recursive Contract

Conceptually:

```text
isValid(node, lower, upper)
```

Invariant:

> Every node handled by this call must lie strictly inside the legal range inherited from its ancestors.

Base:

```text
null → true
```

Reject:

```text
node.value <= lower
or
node.value >= upper
```

Then:

```text
left  → (lower, node.value)
right → (node.value, upper)
```

## Alternative Reasoning

For a no-duplicate BST:

```text
inorder traversal
→ strictly increasing
```

Compare:

```text
bounds approach → directly encodes invariant
inorder approach → verifies a consequence of invariant
```

## Required Tests

```text
empty tree              → true
single node             → true
valid balanced BST      → true
invalid immediate child → false
deep ancestor violation → false
left-skewed valid BST   → true
right-skewed valid BST  → true
duplicate key           → false
```

Complexity:

```text
Time  → O(n)
Space → O(h)
```

---

# Session 3 — Engineering: BST Delete

**45 minutes**

Requirement:

> Remove one key while preserving the BST invariant.

## Step 1 — Locate

```text
target < node.value → delete left
target > node.value → delete right
target == node.value → remove current node
```

## Case 1 — Leaf

```text
delete leaf
→ subtree becomes null
```

## Case 2 — One Child

Replace the deleted subtree root with its only child.

Conceptually:

```text
return non-null child
```

## Case 3 — Two Children

Need a replacement key that preserves:

```text
all left values < replacement < all right values
```

Use:

```text
inorder successor
→ minimum value in right subtree
```

Alternative:

```text
inorder predecessor
→ maximum value in left subtree
```

For Day 14 use the successor.

## Why Successor Works

The minimum value in the right subtree:

```text
is greater than every allowed left-side value
and
has no smaller value remaining in the right subtree
```

So it can replace the deleted node while preserving ordering.

Then remove the successor from its original position.

## Recursive Contract

Prefer:

```java
Node delete(Node node, int value)
```

Meaning:

> Return the root of this subtree after deletion.

That allows:

```java
node.left = delete(node.left, value);
node.right = delete(node.right, value);
```

and naturally handles subtree-root replacement.

## Required Tests

Start with:

```text
8, 3, 10, 1, 6, 14, 4, 7, 13
```

Test:

```text
delete missing value
delete leaf
delete one-child node
delete two-child node
delete root
delete until empty
size correctness
contains(deleted) == false
unrelated keys remain present
inorder remains sorted
```

Critical engineering assertion after each mutation:

```text
isValidBST(root) == true
```

Complexity:

```text
O(h)
```

Balanced:

```text
O(log n)
```

Skewed:

```text
O(n)
```

---

# Session 4 — BFS / Level-Order Traversal

**30 minutes**

Problem:

> Visit nodes level by level from top to bottom.

Example:

```text
        8
       / \
      3   10
     / \    \
    1   6    14
```

Output:

```text
8, 3, 10, 1, 6, 14
```

## Derive the Data Structure

Ask:

> In what order should discovered nodes be processed?

Need:

```text
first discovered
→ first processed
```

Therefore:

```text
FIFO
→ Queue
```

Do not memorize “BFS uses queue” without deriving FIFO.

## Core Algorithm

```text
enqueue root

while queue not empty:
    dequeue front
    process node

    enqueue left child
    enqueue right child
```

## Invariant

> The queue contains discovered but unprocessed nodes in the order required by breadth-first traversal.

## Complexity

Every node is:

```text
enqueued once
dequeued once
```

Therefore:

```text
Time → O(n)
```

Auxiliary space:

```text
O(w)
```

where:

```text
w = maximum frontier / tree width
```

Worst case:

```text
O(n)
```

## Per-Level Grouping

For:

```text
[[8], [3,10], [1,6,14]]
```

snapshot:

```text
levelSize = queue.size()
```

before processing the level.

Then process exactly that many nodes.

Understand:

> The queue size at the start of a level is the current frontier.

Required:

```text
flat level order
values grouped per level
number of levels
```

No zigzag today.

---

# Session 5 — Java / Collections: Queue Choice

**15 minutes**

For BFS in Java:

```java
Deque<Node> queue = new ArrayDeque<>();
```

FIFO operations conceptually:

```text
offerLast / addLast
pollFirst / removeFirst
```

Why not Stack?

```text
Stack → LIFO
BFS   → FIFO
```

Why not automatically `LinkedList`?

`LinkedList` works as a Queue, but `ArrayDeque` often offers:

```text
less per-element object overhead
fewer allocations
better locality
```

This connects directly to Phase-1 memory/cache-locality reasoning.

---

# Session 6 — HLD Bridge: Job Scheduler

**25 minutes**

This is **not** the full HLD.

Day 15 should contain the full 40–50 minute design block.

Today derive only:

```text
requirements
entities
bad outcomes
invariants
minimum durable state
```

No full architecture diagram.

## Problem

Design a backend system where a client can schedule a job to run:

```text
once at a future time
```

Examples:

```text
send notification at 10:00
generate report at midnight
run cleanup at 02:00
```

Do not include recurring jobs yet.

## Functional Requirements

At minimum:

```text
create scheduled job
cancel scheduled job
execute at/after scheduled time
inspect job status
```

## Entities

Likely:

```text
Job
Execution attempt
Worker
```

Possible Job state:

```text
jobId
task/payload reference
scheduledAt
status
createdAt
version?
```

## Bad Outcomes

Derive before mechanisms:

```text
job disappears after create success
job executes before scheduled time
duplicate business effect
job remains stuck forever
two workers both believe they own execution
cancelled job still runs
```

## Safety / Liveness Invariants

### Durable acceptance

> Once create returns success, the scheduled job must survive application restart.

### Scheduling

> A job must not start before its allowed scheduled time.

### Ownership

> At most one worker should hold valid ownership for a particular execution attempt at a time.

### Progress

> An accepted runnable job should eventually complete or enter an explicit retry/terminal state.

## Minimum Durable State

Ask:

> What must survive process failure?

At least:

```text
job identity
scheduled time
task reference/payload
execution state
ownership/retry metadata as needed
```

Do **not** choose:

```text
Kafka
Redis
Postgres
```

yet.

Day 15 continues with:

```text
API
storage/access pattern
worker discovery
leases
fencing
retries
idempotency
failure handling
observability
```

---

# Session 7 — Retrieval & Production Connection

**15 minutes**

Answer without notes:

1. Why are parent/child checks insufficient for BST validation?
2. What does bounds-based validation carry?
3. Why does inorder successor preserve ordering during deletion?
4. What does returning `Node` from recursive delete represent?
5. Why does BFS require FIFO?
6. What determines BFS auxiliary space?
7. Why can naive BST tail latency become unpredictable?
8. State one Job Scheduler safety invariant.
9. State one Job Scheduler liveness invariant.

## Production Connection

If insert order creates a skewed BST:

```text
height ↑
comparison count ↑
recursive depth ↑
latency ↑
```

This affects:

```text
CPU work
tail latency
stack depth
predictability
```

That motivates balanced trees next.

---

# Day 14 Deliverables

## Retrieval

- [ ] traversals recalled correctly
- [ ] postorder precision clean
- [ ] BST invariant stated globally
- [ ] O(h) search reasoning retained

## Validate BST

- [ ] local-check failure understood
- [ ] bounds state derived
- [ ] recursive contract stated
- [ ] implementation completed
- [ ] deep ancestor violation tested
- [ ] O(n) / O(h) explained

## BST Delete

- [ ] leaf deletion derived
- [ ] one-child deletion derived
- [ ] two-child deletion derived
- [ ] successor reasoning understood
- [ ] recursive-return contract understood
- [ ] root deletion tested
- [ ] size remains correct
- [ ] inorder remains sorted
- [ ] `isValidBST()` passes after mutation

## BFS

- [ ] FIFO requirement derived
- [ ] flat level order implemented
- [ ] per-level grouping implemented
- [ ] queue invariant stated
- [ ] O(n) time explained
- [ ] O(width) space explained

## Java

- [ ] `ArrayDeque` choice explained
- [ ] FIFO operations understood
- [ ] locality/allocation connection recalled

## HLD

- [ ] Job Scheduler requirements
- [ ] core entities
- [ ] bad outcomes
- [ ] safety/liveness invariants
- [ ] minimum durable state
- [ ] no premature technology choice

---

# End-of-Day Exit Criteria

Day 14 is complete when:

## BST correctness

You distinguish:

```text
local ordering
```

from:

```text
global BST invariant
```

## Structural mutation

You can delete:

```text
leaf
one-child node
two-child node
root
```

while preserving ordering.

## Traversal

You understand:

```text
DFS
→ follow subtree depth

BFS
→ process frontier in FIFO order
```

## HLD

You can reason:

```text
bad outcome
→ invariant
→ required durable state
```

before selecting infrastructure.

---

# Intentionally Deferred

Do not add today:

- AVL rotations
- Red-Black Tree rules
- TreeMap internals
- Lowest Common Ancestor
- diameter
- path sum
- iterative DFS
- zigzag traversal
- binary heap
- PriorityQueue internals
- trie
- B/B+ tree details
- complete Job Scheduler architecture
- distributed scheduling implementation
- leader election

---

# Day 14 Exact Starting Action

Begin with:

> **Can a binary tree satisfy `left child < parent < right child` at every node and still NOT be a valid BST? Construct an example and explain why.**

Do not code immediately.

Once clear, derive what information must flow from ancestors to validate the entire tree.

---

# Registry Update Requirements

At the end of Day 14, update `cirriculum/registry.md` with demonstrated evidence only.

Capture:

1. traversal retrieval quality
2. postorder precision
3. Validate-BST abstraction quality
4. bounds/invariant reasoning
5. Validate-BST implementation/tests
6. BST deletion reasoning by case
7. deletion implementation/tests
8. mutation bugs encountered
9. `isValidBST()` after mutations
10. BFS queue derivation
11. level-order implementation
12. O(width) reasoning
13. `ArrayDeque` reasoning
14. Job Scheduler requirements/entities
15. Job Scheduler safety/liveness invariants
16. premature-technology tendency, if any
17. exact Day-15 starting action

Recommended Day-15 direction if Day 14 is strong:

```text
AVL balancing motivation
+
height / balance-factor reasoning
+
rotations from invariant repair
+
proper 40–50 minute Job Scheduler HLD
```
