# Day 13 — Binary Trees, Recursive Reasoning & BST Foundations

## Status

**Completed**

## Phase

Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution

## Concepts Demonstrated

### Binary Tree / BST

* Correctly distinguished binary tree from binary search tree.
* Binary tree: every node has at most two children.
* BST invariant understood:

  * all values in left subtree `< node.value`
  * all values in right subtree `> node.value`
* Understood that BST invariant applies to the entire subtree, not only immediate children.

### Tree Vocabulary

Demonstrated understanding of:

* root
* parent / child
* leaf
* subtree
* depth
* height

Clarified:

* depth → distance from root
* height → longest downward path to a leaf

### Recursive Tree Mental Model

Understood the core decomposition:

```text
tree
=
current node
+
left subtree
+
right subtree
```

Recognized `null` as the natural recursion base case.

Important improvement:

* Initially added explicit child-null branching.
* Progressed toward letting the recursive base case absorb null handling.

Preferred pattern:

```text
if node == null → base result

solve left subtree
solve right subtree
combine results
```

## Traversals

Successfully derived and implemented:

```text
Preorder  → node, left, right
Inorder   → left, node, right
Postorder → left, right, node
```

### Traversal Evidence

Correctly produced traversal sequences on multiple trees.

Minor mistake:

* During one postorder example, processed parent `10` before child `14`.
* Corrected after identifying that postorder requires both children before parent.

Understands why inorder traversal of a BST returns sorted values:

* left subtree contains smaller values
* current node follows
* right subtree contains larger values
* recursion preserves the same invariant within each subtree

### Traversal Complexity

```text
Time = O(n)
Auxiliary recursion space = O(h)
```

Understood distinction between:

* total calls → O(n)
* simultaneously active stack frames → O(h)

Balanced tree:

```text
O(log n) recursive stack
```

Skewed tree:

```text
O(n) recursive stack
```

## BST Search

Implemented recursive `contains()` correctly.

Reasoning demonstrated:

```text
target < node
→ right subtree impossible
→ search left

target > node
→ left subtree impossible
→ search right
```

Complexity stated correctly after correction:

```text
search = O(h)

balanced → O(log n)
skewed   → O(n)
```

Important precision:

* One comparison eliminates one subtree, not necessarily half the nodes unless the tree is balanced.

## BST Insert

Derived insertion using a helper-based approach first:

* locate insertion parent
* attach new node

Discussion surfaced important API-contract reasoning:

* returning insertion parent is valid if explicitly intended
* `Node insert(root, value)` conventionally returns updated tree root
* returning insertion parent can be misleading if caller performs:

```java
root = insert(root, value);
```

Duplicate policy for Day 13:

```text
No duplicate keys
```

Recursive insertion abstraction understood conceptually:

```text
null → create node

value < node → insert left
value > node → insert right
value == node → do nothing
```

## Tree Size

Initially implemented size using explicit child-null checks.

Successfully simplified to:

```text
size(node)
=
1 + size(left) + size(right)
```

Base case:

```text
null → 0
```

This reinforced the recursive subtree abstraction.

## Maximum Depth

Derived:

```text
maxDepth(node)
=
1 + max(
    maxDepth(node.left),
    maxDepth(node.right)
)
```

Base case:

```text
null → 0
```

Convention used for this problem:

```text
empty tree  → 0
single node → 1
```

Important distinction clarified:

* depth of a specific node
* maximum depth of an entire tree
* height
* subtree size

Maximum-depth complexity:

```text
Time  = O(n)
Space = O(h)
```

## JVM / Recursion

Understood that recursive tree traversal
