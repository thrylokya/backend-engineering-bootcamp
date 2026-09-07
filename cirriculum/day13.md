# Day 13 — Phase 2 Begins: Binary Trees, Recursive Reasoning & BST Foundations

> **Duration:** ~3 hours  
> **Phase:** Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution  
> **Primary DSA Theme:** Learn to reason recursively over hierarchical search spaces rather than memorize traversal templates.  
> **Primary Engineering Theme:** Build a small Binary Search Tree V1 with explicit invariants and tests.  
> **Primary Java/JVM Theme:** Connect recursive tree algorithms to stack frames and call depth.  
> **Production Theme:** Understand why ordinary BSTs are useful conceptually but insufficient for many production indexing workloads.  
> **Phase-1 Maintenance:** Short spaced retrieval only; do not re-teach completed material.

---

# Why Day 13 Looks Like This

The Day 12 registry explicitly records:

```text
Phase 1 exit criteria sufficiently demonstrated
```

and directs:

```text
Begin Day 13 — Phase 2
Primary direction:
Trees / Binary Trees / BST foundations
```

The registry also specifies four Day-13 priorities:

```text
1. Establish tree vocabulary and structural invariants.
2. Understand recursive tree reasoning before memorizing traversal templates.
3. Build binary-tree/BST intuition from first principles.
4. Introduce traversal patterns incrementally.
```

The Phase-2 master curriculum includes:

```text
binary tree
BST
AVL tree
red-black tree
TreeMap
trie
radix tree
B-tree
B+ tree
binary heap
priority queue
graphs
BFS / DFS
topological sort
union-find
JVM execution / JIT / class loading
```

Day 13 therefore begins at the dependency root:

```text
tree structure
↓
recursive reasoning
↓
traversal
↓
BST ordering invariant
↓
search / insert
```

Do not jump to balancing yet.

---

# Schedule Overview

```text
Session 1 — Phase-1 Spaced Retrieval               15 min
Session 2 — Tree Mental Model & Vocabulary          25 min
Session 3 — Recursive Traversals From First Principles
                                                    35 min
Session 4 — BST V1: Derive, Implement, Test         45 min
Session 5 — DSA Transfer: Maximum Depth             30 min
Session 6 — Java/JVM: Recursion & Stack Frames      15 min
Session 7 — Production Connection                   10 min
Session 8 — Retrieval / Registry Evidence            5 min
                                                   ------
                                                   180 min
```

---

# Session 1 — Phase-1 Spaced Retrieval

**15 minutes**

No notes.

Do not solve full problems unless retrieval is weak.

## A. Prefix-Sum Transfer

Question:

> For Subarray Sum Equals K, why do we look for `currentPrefix - k` among earlier prefix sums?

Expected reasoning:

```text
currentPrefix - earlierPrefix = k
↓
earlierPrefix = currentPrefix - k
```

## B. Binary Search

Question:

> In lower bound, why do we use `right = mid` when `nums[mid] >= target`?

Expected:

> `mid` is already a valid candidate, so discarding it could discard the answer.

## C. LRU

State the invariant:

> HashMap and DLL represent exactly the same logical cache entries.

## D. Rate Limiter

State the fixed-window invariant.

Keep this block short.

---

# Session 2 — Tree Mental Model & Vocabulary

**25 minutes**

Start from structure, not algorithms.

Example:

```text
        8
       / \
      3   10
     / \    \
    1   6    14
```

Be able to identify:

```text
root
node
edge
parent
child
left child
right child
leaf
internal node
subtree
ancestor
descendant
depth
height
```

Important distinction:

```text
Binary Tree
→ structural constraint

Binary Search Tree
→ structural constraint + ordering invariant
```

A binary tree does **not** automatically imply:

```text
left < parent < right
```

Suggested node representation:

```java
class Node {
    int value;
    Node left;
    Node right;
}
```

Smallest sufficient node state:

```text
value
left reference
right reference
```

No parent pointer unless an operation requires it.

---

# Session 3 — Recursive Traversals From First Principles

**35 minutes**

Do not memorize traversal names first.

For any node:

```text
current node
left subtree
right subtree
```

Each subtree is itself a tree.

That self-similarity is why recursion fits naturally.

Base case:

```text
node == null
→ nothing to process
```

## Preorder

```text
node
left
right
```

## Inorder

```text
left
node
right
```

For a BST this later produces sorted values.

## Postorder

```text
left
right
node
```

Useful mental model:

> Children are handled before the parent.

Given:

```text
        5
       / \
      2   8
     / \   \
    1   3   9
```

Produce:

```text
preorder
inorder
postorder
```

Then implement traversal methods.

Complexity:

```text
Time → O(n)
```

Recursive auxiliary space:

```text
O(h)
```

Balanced:

```text
h ≈ log n
```

Worst-case skewed:

```text
h = n
```

---

# Session 4 — BST V1: Derive, Implement, Test

**45 minutes**

Choose duplicate policy first.

For Day 13:

```text
no duplicate keys
```

BST invariant:

> For every node, all values in its left subtree are smaller than the node value, and all values in its right subtree are greater.

This applies to the **entire subtree**, not just immediate children.

## Search

If:

```text
target < node.value
```

then by the BST invariant the right subtree is impossible.

So search left.

Likewise:

```text
target > node.value
→ left subtree impossible
→ search right
```

Explain the elimination proof.

## Insert

Preserve the same invariant:

```text
newValue < node.value
→ recurse/go left

newValue > node.value
→ recurse/go right
```

Eventually a `null` child becomes the insertion position.

Suggested API:

```java
class IntBinarySearchTree {
    void insert(int value);
    boolean contains(int value);
    int size();
}
```

Optional:

```java
List<Integer> inorder();
List<Integer> preorder();
List<Integer> postorder();
```

Do not implement deletion today.

Required tests:

```text
insert into empty
search root
search left descendant
search right descendant
search missing value
inorder sorted
duplicate behavior
size correctness
skewed insertion sequence
```

Example:

```text
insert 8,3,10,1,6,14
```

Expected inorder:

```text
1,3,6,8,10,14
```

Complexity:

```text
search / insert = O(h)
```

Balanced:

```text
O(log n)
```

Skewed:

```text
O(n)
```

Do not say ordinary BST is always `O(log n)`.

---

# Session 5 — DSA Transfer: Maximum Depth of Binary Tree

**30 minutes**

Problem:

> Given the root of a binary tree, return its maximum depth.

Use:

```text
empty tree depth = 0
single node depth = 1
```

Ask:

> If I already knew the left-subtree depth and right-subtree depth, how would I compute the current tree depth?

Answer:

```text
1 + max(leftDepth, rightDepth)
```

Recurrence:

```text
depth(node)
=
1 + max(depth(node.left), depth(node.right))
```

Base:

```text
null → 0
```

Why correct?

Every root-to-leaf path must continue through either the left or right subtree, so the deepest valid path uses the deeper subtree.

Tests:

```text
empty tree           → 0
single node          → 1
balanced 3 levels    → 3
left-skewed 5 nodes  → 5
right-skewed 5 nodes → 5
```

Complexity:

```text
Time  → O(n)
Stack → O(h)
```

---

# Session 6 — Java / JVM: Recursion & Stack Frames

**15 minutes**

For a recursive call such as:

```java
depth(node)
```

each active invocation conceptually has its own stack frame containing things such as:

```text
node reference
local/temporary state
return information
```

Maximum active recursive calls are proportional to tree height:

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

Preview:

Recursive traversal uses the:

```text
implicit call stack
```

Iterative traversal later uses an:

```text
explicit Stack<Node>
```

Do not implement iterative DFS today.

---

# Session 7 — Production Connection

**10 minutes**

Insert sorted values:

```text
1,2,3,4,5,6,7
```

A naive BST degenerates into:

```text
1
 \
  2
   \
    3
     \
      ...
```

Height becomes:

```text
n
```

and search becomes:

```text
O(n)
```

This motivates later balanced trees:

```text
AVL
Red-Black Tree
```

Java connection:

```text
TreeMap
```

uses a balanced tree rather than a naive BST.

Database preview:

```text
B-tree
B+ tree
```

exist because storage/page-access constraints are different from ordinary in-memory pointer trees.

Do not study those details today.

---

# Session 8 — End-of-Day Retrieval

**5 minutes**

Answer quickly:

1. Binary tree vs BST?
2. State the BST invariant.
3. Why can BST search eliminate an entire subtree?
4. Why is ordinary BST search `O(h)` rather than always `O(log n)`?
5. What tree shape gives worst-case `h = n`?
6. Preorder/inorder/postorder?
7. Why does inorder on a BST produce sorted output?
8. Maximum-depth recurrence?
9. Why does recursive auxiliary space depend on height?
10. Why do balanced trees exist?

---

# Day 13 Deliverables

## Tree Foundations

- [ ] vocabulary understood
- [ ] binary tree vs BST distinction precise
- [ ] node representation derived
- [ ] subtree mental model understood
- [ ] height/depth distinction usable

## Traversal

- [ ] preorder derived
- [ ] inorder derived
- [ ] postorder derived
- [ ] traversals implemented
- [ ] traversal tests pass
- [ ] O(n) reasoning explained
- [ ] recursive stack O(h) explained

## BST V1

- [ ] duplicate policy defined
- [ ] BST invariant stated
- [ ] `insert()` implemented
- [ ] `contains()` implemented
- [ ] `size()` implemented
- [ ] inorder validates sorted order
- [ ] missing-value test
- [ ] duplicate behavior test
- [ ] skewed-tree example understood

## DSA

- [ ] Maximum Depth recurrence derived
- [ ] implementation completed
- [ ] empty/single/balanced/skewed tests completed
- [ ] O(n) time / O(h) stack explained

## Java / JVM

- [ ] recursive stack-frame model explained
- [ ] height-to-stack-depth connection understood
- [ ] implicit vs explicit stack distinction introduced

## Production

- [ ] naive BST degeneration explained
- [ ] motivation for AVL/RB trees understood
- [ ] TreeMap connection introduced
- [ ] B/B+ tree motivation previewed

---

# End-of-Day Exit Criteria

Day 13 is complete when:

## Tree reasoning

You see a tree as:

```text
node
+
left subtree
+
right subtree
```

and can reason recursively from that decomposition.

## BST

You can explain:

> Because of the BST ordering invariant, one comparison proves one entire subtree cannot contain the target.

## Complexity

Correct:

```text
search / insert = O(h)
balanced h = O(log n)
worst-case h = O(n)
```

## Recursion

You can connect:

```text
tree height
→ recursive call depth
→ auxiliary stack space
```

---

# Intentionally Deferred

Do not add today:

- BST deletion
- iterative DFS
- BFS / level order
- AVL rotations
- red-black tree rules
- TreeMap internals
- trie
- radix tree
- B-tree / B+ tree details
- heap
- PriorityQueue
- graph traversal
- Java class loading
- JIT deep dive

---

# Day 13 Exact Starting Action

Begin with:

> **Without using notes: what is the difference between a binary tree and a binary search tree? What extra invariant does a BST impose?**

Then draw:

```text
        8
       / \
      3   10
     / \    \
    1   6    14
```

and identify:

```text
root
leaves
parent/children
subtree
depth
height
```

Only after that begin traversal reasoning.

---

# Registry Update Requirements

At the end of Day 13, update `cirriculum/registry.md` using demonstrated evidence only.

Capture:

1. Phase-1 spaced-retrieval quality
2. tree vocabulary precision
3. binary-tree vs BST distinction
4. recursive subtree mental model
5. traversal implementation status
6. traversal mistakes / ordering confusion
7. BST invariant quality
8. BST insert/search implementation
9. duplicate policy
10. BST tests actually passing
11. maximum-depth reasoning and implementation
12. recursion / stack-space explanation
13. balanced-vs-skewed complexity precision
14. production motivation for balanced trees
15. exact Day-14 starting action
