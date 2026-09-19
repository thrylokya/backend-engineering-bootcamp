# Day 16 — Weekend Consolidation + Two-Pointer LeetCode Track + Heap Foundations

> **Date:** Sunday, September 20, 2026  
> **Duration:** ~6 hours  
> **Phase:** Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution  
> **Interview Track:** LeetCode algorithms now run continuously in parallel with the phase curriculum.  
> **Weekend Rule:** One concentrated ~60-minute cumulative revival block; do not scatter large review blocks throughout the day.  
> **Primary Algorithm Theme:** Two Pointers — derive safe pointer movement rather than memorize templates.  
> **Primary Phase-2 Theme:** Red-Black Tree intuition → Binary Heap / PriorityQueue foundations.  
> **Engineering Lab:** Implement a Binary Min-Heap from scratch.  
> **HLD:** No full HLD today; Day 15 already contained a full Job Scheduler design.

---

# Why Day 16 Looks Like This

Day 15 established:

```text
BST deletion ✅
AVL motivation / balance invariant ✅
rotation intuition ✅
full Job Scheduler HLD ✅
leases / fencing / idempotency ✅
production-debugging discipline ✅
```

The remaining AVL coding work is explicitly **non-blocking**.

The next Phase-2 direction from the registry is:

```text
Red-Black Tree intuition / TreeMap internals
+
Binary Heap / PriorityQueue foundations
```

But from Day 16 onward we also run a permanent interview-algorithm track:

```text
Arrays / Hashing
Two Pointers
Sliding Window
Prefix Sum
Binary Search
Fast/Slow
Stack/Queue
mixed unknown problems
```

The two tracks run in parallel.

---

# Schedule Overview

```text
Session 1 — Weekend Cumulative Revival             60 min
Session 2 — LeetCode Track: Two Pointers          100 min
Session 3 — Red-Black Tree / TreeMap Intuition     30 min
Session 4 — Binary Heap / PriorityQueue Foundations 60 min
Session 5 — Engineering Lab: Build Min-Heap        55 min
Session 6 — Java Collections / Comparator          30 min
Session 7 — Production Connection & Exit Drill     25 min
                                                  -------
                                                   360 min
```

---

# Session 1 — Weekend Cumulative Revival

**60 minutes**

This is the one deliberate weekly revival block.

Do not spend the rest of the day repeatedly revisiting old concepts unless a genuine gap appears.

The objective is:

```text
retrieve
not re-learn
```

Use no notes initially.

## Block A — Arrays / Hashing — 10 min

Rapid questions:

1. Why is Two Sum naturally a complement lookup?
2. Why does a frequency map sometimes matter where a Set is insufficient?
3. Membership lookup vs frequency lookup?
4. Expected HashMap lookup complexity and one pathological case?
5. Why does array locality often beat linked-node traversal?

## Block B — Prefix Sum — 10 min

### Subarray Sum Equals K

Derive:

```text
currentPrefix - earlierPrefix = k
→ earlierPrefix = currentPrefix - k
```

Explain why frequency counts matter.

### Subarrays Divisible by K

Derive:

```text
(prefix[i] - prefix[j]) % k == 0
→ prefix[i] % k == prefix[j] % k
```

Explain:

```text
0 → 1
```

and Java negative-remainder normalization.

## Block C — Binary Search — 10 min

Explain:

```text
exact membership
vs
lower bound
```

Why is:

```text
right = mid
```

safe when `mid` is already a valid lower-bound candidate?

Then answer:

> What ordered/monotonic property must exist before binary-search-style elimination is safe?

## Block D — Sliding Window — 10 min

Answer:

1. What lets a window boundary move without reconsidering all earlier possibilities?
2. Why can negative numbers break many sum-window arguments?
3. Why is divisibility not monotonic as the window expands?
4. State one variable-window invariant.

## Block E — Stack / Queue / LRU — 10 min

Recall:

```text
Stack → LIFO
Queue → FIFO
Deque → both ends
```

Why does BFS require FIFO?

LRU invariant:

> HashMap and DLL represent exactly the same logical cache entries.

Why does successful `get()` mutate recency?

## Block F — Trees / BST — 10 min

Rapid recall:

1. Binary tree vs BST.
2. Global BST invariant.
3. Why Validate BST needs ancestor bounds.
4. Recursive tree model.
5. Search complexity `O(h)`.
6. Why AVL exists.
7. What rotations must preserve.

Stop after 60 minutes.

---

# Session 2 — LeetCode Interview Track: Two Pointers

**100 minutes**

This is the main DSA block today.

The objective is not to memorize a template. The objective is to prove why moving one pointer cannot discard the required/optimal answer.

Use:

```text
Because ______,
I know ______.
Therefore moving/discarding ______ is safe.
```

## Part A — Two-Pointer Mental Model — 15 min

Two pointers become useful when:

```text
two positions summarize the relevant search state
+
an ordering/property gives a safe elimination rule
```

Common forms:

```text
opposite ends
same direction
slow / fast
```

Today focus on opposite ends.

Key question:

> What property makes pointer movement safe?

---

## Problem 1 — Two Sum II — 20 min

**LeetCode 167**

Warm-up / retrieval.

Given sorted `numbers[]` and `target`:

```text
left = 0
right = n - 1
```

Do not simply recite pointer moves.

If:

```text
numbers[left] + numbers[right] < target
```

prove why keeping the same left with any smaller right is useless.

Because every smaller-right value is `<= numbers[right]`, every such pair has sum `<= current sum < target`.

Therefore that `left` can be discarded.

Do the symmetric proof for `sum > target`.

Complexity:

```text
Time  → O(n)
Space → O(1)
```

---

## Problem 2 — Container With Most Water — 40 min

**LeetCode 11**

This is the main new transfer problem.

Start from:

```text
area = width * min(leftHeight, rightHeight)
```

Brute force:

```text
all pairs → O(n²)
```

Now suppose:

```text
height[left] < height[right]
```

The current area is limited by `height[left]`.

If `right` moves inward while `left` stays fixed:

```text
width decreases
```

and limiting height is still at most `height[left]`.

Therefore no narrower container using that same left boundary can improve the area.

So:

```text
left++
```

is safe.

Required explanation:

> Move the shorter wall because keeping it while shrinking width cannot improve area; only replacing the limiting wall offers a chance to improve.

Tests:

```text
[1,8,6,2,5,4,8,3,7] → 49
[1,1]                 → 1
increasing heights
decreasing heights
```

Complexity:

```text
Time  → O(n)
Space → O(1)
```

---

## Problem 3 — 3Sum — 25 min

**LeetCode 15**

If time is tight, derive cleanly even if implementation is unfinished.

Goal:

```text
a + b + c = 0
```

Sort.

Fix one index `i`.

Then solve:

```text
nums[left] + nums[right] = -nums[i]
```

So:

```text
sort
→ directional elimination becomes possible

fix one value
→ reduce 3Sum to sorted 2Sum
```

Discuss duplicate skipping precisely.

Target complexity:

```text
O(n²)
```

because:

```text
n fixed choices × O(n) two-pointer scan
```

---

# Two-Pointer Exit Criterion

You must distinguish:

```text
"there are two indices"
```

from:

```text
"there is a safe elimination property that lets one index move monotonically"
```

---

# Session 3 — Red-Black Tree / TreeMap Intuition

**30 minutes**

Keep this at interview-relevant depth.

Do not implement a Red-Black Tree today.

AVL maintains stricter balance.

Red-Black Trees use looser structural/color invariants while still guaranteeing:

```text
height = O(log n)
```

General trade-off:

```text
AVL
→ stricter balance
→ tighter lookup depth
→ potentially more rebalancing

Red-Black
→ looser balance
→ fewer/milder updates in many workloads
→ still logarithmic operations
```

Do not claim one is universally faster.

High-level Red-Black intuition:

```text
nodes carry red/black metadata
root is black
red nodes cannot have red children
black-height constraints prevent pathological depth
```

No need to memorize every insertion-fixup case.

## Java Connection — TreeMap

Know:

```text
TreeMap
→ ordered map
→ Red-Black Tree based
```

Useful when you need:

```text
sorted keys
floor / ceiling
range navigation
```

HashMap is usually preferred when ordering is irrelevant and expected O(1) lookup is enough.

---

# Session 4 — Binary Heap / PriorityQueue Foundations

**60 minutes**

This is the main new Phase-2 concept today.

## Problem the Heap Solves

Repeatedly need:

```text
smallest element
```

or:

```text
largest element
```

Trade-off comparison:

```text
sorted array:
peek extreme → O(1)
insert       → O(n)

unsorted array:
append       → O(1)
find min     → O(n)

heap:
peek         → O(1)
insert       → O(log n)
remove root  → O(log n)
```

## Binary Heap Structure

```text
complete binary tree
+
heap-order invariant
```

For min-heap:

```text
parent <= children
```

Important:

```text
heap is NOT globally sorted
```

## Array Representation

Because the tree is complete, store level-by-level in an array.

Zero-based:

```text
parent(i) = (i - 1) / 2
left(i)   = 2*i + 1
right(i)  = 2*i + 2
```

Derive with a drawing.

## Insert — Sift Up

Append at end to preserve complete-tree shape.

Repair heap order upward:

```text
while child < parent:
    swap
```

Complexity:

```text
O(log n)
```

## Remove Min — Sift Down

```text
1. save root
2. move last element to root
3. remove last slot
4. restore heap order downward
```

For min-heap choose the smaller child during sift-down.

Complexities:

```text
peek         → O(1)
insert       → O(log n)
remove root  → O(log n)
size         → O(1)
```

Defer bottom-up `buildHeap = O(n)` proof.

---

# Session 5 — Engineering Lab: Build IntMinHeap

**55 minutes**

Suggested API:

```java
class IntMinHeap {
    void add(int value);
    int peek();
    int poll();
    int size();
    boolean isEmpty();
}
```

Backing storage:

```text
int[] / dynamic array
```

or `ArrayList<Integer>` for V1 if you want to focus on heap logic.

Possible helpers:

```text
parentIndex(i)
leftChildIndex(i)
rightChildIndex(i)
swap(i, j)
siftUp(i)
siftDown(i)
```

Invariant:

```text
heap[parent(i)] <= heap[i]
```

for every valid child index `i`.

Required tests:

```text
add into empty
peek one element
poll one element
ascending inserts
descending inserts
random inserts
duplicates
repeated poll returns non-decreasing sequence
empty peek/poll contract
size correctness
```

Powerful end-to-end test:

```text
insert N values
poll until empty
→ output must be sorted non-decreasing
```

The internal heap array itself does not need to be sorted.

---

# Session 6 — Java Collections / Comparator / PriorityQueue

**30 minutes**

Java:

```java
PriorityQueue<Integer>
```

is a priority queue, typically min-priority by natural ordering.

Important:

```text
PriorityQueue != FIFO Queue
```

Removal order is based on priority, not insertion order.

For custom objects:

```java
PriorityQueue<Job> pq =
    new PriorityQueue<>(Comparator.comparing(Job::scheduledAt));
```

Conceptually:

```text
earliest scheduledAt
→ highest removal priority
```

High-level distinction:

```text
Comparable
→ ordering belongs to the type

Comparator
→ ordering supplied externally for a use case
```

Do not deep-dive generics today.

---

# Session 7 — Production Connection & Exit Drill

**25 minutes**

## Scheduler Connection

The Day-15 Job Scheduler repeatedly cares about:

```text
next due job
```

An in-memory heap can efficiently track:

```text
earliest scheduledAt
```

But:

```text
heap / PriorityQueue
→ efficient local ordering

durable DB state
→ survives process failure
```

Do not replace durable scheduling state with only an in-memory heap.

A production scheduler may combine:

```text
durable DB
+
bounded in-memory ready structure
```

but correctness must survive restart.

## Production Questions

Suppose:

```text
heap contains 5 million future jobs
```

Ask:

1. Do all 5 million belong in one process?
2. What happens on restart?
3. How is ordering rebuilt?
4. How do we bound memory?
5. Why might we load only a near-term scheduling window?

No full HLD today.

## End-of-Day Retrieval

### Two Pointers

1. Why does sorted Two Sum allow pointer elimination?
2. Why move the shorter wall in Container With Most Water?
3. What makes two-pointer reasoning valid?
4. How does sorting reduce 3Sum?

### Trees

5. AVL vs Red-Black at high level.
6. Why does TreeMap avoid a naive BST?

### Heap

7. State min-heap invariant.
8. Why isn't the heap array globally sorted?
9. Why append before sift-up?
10. Why move last element to root during poll?
11. Why choose the smaller child in sift-down?
12. Heap operation complexities?
13. Why isn't PriorityQueue FIFO?

---

# Day 16 Deliverables

## Weekend Revival

- [ ] arrays/hash retrieval
- [ ] prefix-sum equations
- [ ] binary-search boundary reasoning
- [ ] sliding-window reasoning
- [ ] stack/queue/LRU invariants
- [ ] tree/BST/AVL retrieval
- [ ] revival limited to ~60 minutes

## LeetCode

- [ ] Two Sum II re-solved
- [ ] pointer-elimination proof stated
- [ ] Container With Most Water solved
- [ ] shorter-wall proof explained
- [ ] 3Sum reduction derived
- [ ] duplicate handling reasoned
- [ ] no blind template memorization

## Red-Black / TreeMap

- [ ] AVL vs Red-Black trade-off understood
- [ ] high-level RB invariants understood
- [ ] TreeMap connection understood
- [ ] ordered-map use cases understood

## Heap

- [ ] complete-tree property understood
- [ ] min-heap invariant stated
- [ ] array-index relationships understood
- [ ] sift-up derived
- [ ] sift-down derived
- [ ] complexities explained

## Engineering Lab

- [ ] `IntMinHeap` implemented
- [ ] add
- [ ] peek
- [ ] poll
- [ ] size / isEmpty
- [ ] duplicate tests
- [ ] repeated-poll sorted-output test
- [ ] empty contract defined

## Java

- [ ] PriorityQueue behavior understood
- [ ] PriorityQueue vs FIFO distinction
- [ ] Comparable vs Comparator distinction
- [ ] scheduled-job comparator understood

## Production

- [ ] heap vs durability boundary understood
- [ ] scheduler connection made
- [ ] memory-bounding discussion completed

---

# End-of-Day Exit Criteria

## LeetCode

You can explain:

> Two pointers are justified by a safe elimination property, not by the mere presence of two indices.

For Container With Most Water, you can prove why moving the shorter boundary is safe.

## Heap

You can derive:

```text
array representation
sift up
sift down
```

from complete-tree shape and heap-order invariants.

You can implement a working min-heap without copying a reference implementation.

## Phase 2

You understand why:

```text
AVL / Red-Black Tree
→ ordered search with bounded height

Heap
→ efficient repeated access to an extreme-priority element
```

---

# Intentionally Deferred

Do not add today:

- Red-Black insertion/fixup implementation
- Red-Black deletion
- AVL implementation completion
- TreeMap source-code deep dive
- bottom-up heapify O(n) proof
- heap sort
- Top K problems
- Merge K Sorted Lists
- trie
- graph representation
- graph BFS/DFS
- another full HLD

---

# Day 16 Exact Starting Action

Because this is the weekend session, start with the 60-minute cumulative revival.

After that begin LeetCode with:

> **Given a sorted array and a target, if the sum at `left` and `right` is too small, prove why advancing `left` cannot discard a valid solution involving that same left value.**

Do not start by writing code.

After the algorithm block, move to:

> **AVL and Red-Black Trees both prevent pathological BST height. Why would Java's TreeMap prefer a Red-Black Tree instead of requiring AVL's stricter balance?**

Then move quickly into heaps, which are the higher-priority implementation topic.

---

# Registry Update Requirements

At the end of Day 16, update `cirriculum/registry.md` using demonstrated evidence only.

Capture:

1. weekend revival retrieval quality
2. weakest revived Phase-1 concept
3. Two Sum II pointer-elimination proof
4. Container With Most Water abstraction quality
5. Container solution/bugs
6. 3Sum reduction quality
7. duplicate-handling precision
8. tendency to recognize pattern too early, if any
9. AVL vs Red-Black distinction
10. TreeMap intuition
11. heap invariant
12. heap array-index reasoning
13. sift-up reasoning
14. sift-down reasoning
15. `IntMinHeap` implementation status
16. heap tests actually passing
17. PriorityQueue / Comparator reasoning
18. heap-vs-durability production distinction
19. exact Day-17 starting action

Recommended Day-17 direction if Day 16 is strong:

```text
LeetCode: Sliding Window
+
Heap transfer problems (Kth Largest / Top K)
+
Trie foundations
+
Java/JVM execution block
```
