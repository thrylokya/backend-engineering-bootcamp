# Day 16 — Weekend Consolidation, Two Pointers & Binary Heap Foundations

## Status

**Completed**

Day 16 completed the weekend cumulative-retrieval block, established two-pointer elimination reasoning through Two Sum II, Container With Most Water, and 3Sum, and introduced binary heaps from first principles through a working `IntMinHeap` implementation.

Red-Black Tree / TreeMap internals were intentionally deprioritized because their immediate interview ROI is lower than heaps, graphs, JVM, databases, concurrency, and system design.

The strongest new Phase-2 result is that heap mechanics were derived rather than memorized:

```text
complete binary tree
+
local heap-order invariant
+
array representation
+
sift up / sift down
```

---

## Phase

Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution

---

# Weekend Cumulative Revival

## Arrays / Hashing

### Two Sum

Correctly retrieved the complement-lookup abstraction:

```text
current value = x

needed value
=
target - x
```

Rather than comparing every pair:

```text
for each x
→ ask whether target - x has already been seen
```

Complexity understood:

```text
brute force
→ O(n²)

HashMap / HashSet
→ expected O(n)
```

Also understood the distinction between:

```text
HashSet
→ membership

frequency map
→ membership + count
```

and correctly identified that frequency information is required when duplicate occurrences contribute independently to the answer.

### HashMap Complexity

Correctly recalled:

```text
expected lookup → O(1)
```

and identified pathological collision behavior as the reason lookup can degrade.

Java-specific treeified-bucket behavior was discussed as an implementation nuance.

### Array Locality

Strong retrieval.

Correctly connected:

```text
array
→ contiguous memory
→ spatial locality
→ cache-line utilization
→ CPU prefetch friendliness
```

versus:

```text
linked structure
→ pointer chasing
→ nodes may be scattered
→ increased cache misses
```

Also correctly distinguished:

```text
array index access → O(1)
linked-list nth access → O(n)
```

from the separate hardware-locality advantage during traversal.

---

# Prefix Sum Revival

## Subarray Sum Equals K

Correctly recalled the basic relationship:

```text
currentPrefix - earlierPrefix = k

therefore:

earlierPrefix = currentPrefix - k
```

Understood that the algorithm can run as a single left-to-right traversal.

Important frequency insight retrieved:

```text
if the required earlier prefix occurred N times
→ there are N valid subarrays ending at the current position
```

Therefore:

```text
frequency map
```

is required rather than simple membership.

Correctly understood initialization:

```text
0 → 1
```

as representing the empty prefix before index `0`.

---

## Subarrays Divisible by K

The pattern was recognized, but the modulo derivation initially required reinforcement.

Correct derivation established:

```text
(prefix[i] - prefix[j]) % k == 0

iff

prefix[i] % k == prefix[j] % k
```

Reason:

```text
prefix[i] = a*k + r1
prefix[j] = b*k + r2

difference
=
(a-b)k + (r1-r2)

for difference to be divisible by k:
r1 = r2
```

Correctly retained:

```text
0 → 1
```

for the empty-prefix remainder.

### Java Negative Remainders

This was the weakest revived Prefix Sum detail.

Initial reasoning incorrectly treated Java negative modulo as if it were already normalized.

Correct behavior established:

```java
-1 % 5 == -1
```

Normalization:

```java
((prefixSum % k) + k) % k
```

or preferably:

```java
Math.floorMod(prefixSum, k)
```

Important correction:

```text
do NOT use abs(remainder)
```

because absolute value can map values into the wrong equivalence class.

### Prefix-Sum Revival Assessment

```text
core abstraction       → strong
frequency reasoning    → strong
empty-prefix reasoning → strong
modulo derivation      → needs spaced reinforcement
negative remainder     → needs spaced reinforcement
```

---

# Binary Search Revival

Correctly distinguished:

```text
exact search
→ find any matching value

lower bound
→ first index with nums[i] >= target
```

For:

```text
[1,3,3,3,7]
```

and target `3`:

```text
exact search
→ may return any of indices 1,2,3

lower bound
→ must return index 1
```

### Candidate Preservation

Correctly reasoned that:

```text
nums[mid] >= target
```

means `mid` is already a valid lower-bound candidate.

Therefore:

```java
right = mid;
```

is required rather than:

```java
right = mid - 1;
```

because `mid` itself may be the answer.

### General Binary-Search Abstraction

Initially answered:

```text
array must be sorted
```

then generalized correctly to:

```text
monotonic predicate / ordered eliminability
```

Example lower-bound predicate:

```text
false false false true true true
```

Binary search works because one observation allows an entire region to be discarded safely.

Binary-search revival status:

```text
strong
```

---

# Sliding Window Revival

Strong retrieval.

Correctly identified why positive-only sum windows support pointer movement:

```text
move right
→ sum cannot decrease

move left
→ sum cannot increase
```

For minimum-length subarray with:

```text
sum >= target
```

correct invariant:

```text
while current window remains valid:
    record answer
    shrink from left
```

Correctly identified why negative numbers break the argument:

```text
window sum is no longer monotonic
```

and why divisibility is not monotonic:

```text
(sum + x) % k
```

can move arbitrarily among remainder classes.

Sliding-window revival status:

```text
strong
```

---

# Stack / Queue / LRU Revival

## BFS

Correctly identified FIFO as necessary for preserving level-order processing.

Refinement established:

```text
FIFO
→ nodes already discovered at current depth
   execute before newly discovered deeper nodes
```

Using a stack instead naturally changes the traversal toward DFS.

## LRU

Correctly explained why:

```java
get(key)
```

is logically a mutation:

```text
successful access
→ entry becomes most recently used
→ move node in DLL
```

Correctly retained the dual-structure responsibility:

```text
HashMap
→ key → node lookup

DLL
→ recency ordering
```

and the consistency invariant:

```text
map entries
==
DLL logical cache entries
```

A map-only entry would be retrievable but not correctly represented in recency/eviction state.

A DLL-only entry would become an unreachable ghost entry.

LRU revival status:

```text
strong
```

---

# Tree / BST / AVL Revival

## Binary Tree vs BST

Correctly stated:

```text
binary tree
→ at most two children

BST
→ binary tree + ordering invariant
```

Refinement reinforced:

```text
all values in left subtree < node
all values in right subtree > node
```

not merely immediate-child comparison.

## Validate BST

Correctly explained why parent-only comparison is insufficient.

Ancestor constraints must be propagated:

```text
root
→ (-∞, +∞)

left
→ (lower, root.value)

right
→ (root.value, upper)
```

## BST Complexity

Correctly explained:

```text
BST search = O(h)
```

because ordinary BSTs may become skewed.

```text
balanced tree
h = O(log n)

skewed tree
h = O(n)
```

Minor terminology correction:

```text
skewed BST
```

rather than skewed AVL.

## AVL

Correctly retained:

```text
balanceFactor
=
height(left) - height(right)
```

with:

```text
-1, 0, +1
```

allowed.

Correctly understood that rotations must preserve:

```text
BST ordering
+
AVL balance
```

Tree revival status:

```text
strong
```

---

# Two-Pointer Interview Track

## Core Abstraction

Correctly generalized two pointers beyond having two variables.

Required property:

```text
ordering / monotonicity
+
safe elimination
```

A pointer may move only when doing so provably discards states that cannot contain the required answer.

---

# Two Sum II

Given sorted data:

```text
numbers[left] + numbers[right]
```

correct pointer movement retained:

```text
sum < target
→ left++

sum > target
→ right--
```

Pointer-elimination proof initially needed prompting, then was understood.

For:

```text
sum < target
```

because every smaller right value is:

```text
<= current right
```

every pair using the current left remains:

```text
<= current sum < target
```

therefore current `left` can be discarded safely.

Symmetric argument applies for `sum > target`.

Complexity:

```text
Time  → O(n)
Space → O(1)
```

Status:

```text
solution mechanics          → strong
elimination proof           → understood after prompting
```

---

# Container With Most Water

## Formula

Derived:

```text
width
=
right - left

usableHeight
=
min(height[left], height[right])

area
=
width * usableHeight
```

Initially expected height behavior to provide monotonicity.

Important correction established:

> Heights themselves need not be monotonic.

The elimination proof instead uses the limiting wall.

If:

```text
height[left] < height[right]
```

then current area is limited by `height[left]`.

Keeping the same left while moving right inward gives:

```text
smaller width
+
height still <= height[left]
```

so no improved solution can retain that left boundary.

Therefore:

```text
left++
```

is safe.

Key understanding:

> Moving the shorter wall does not guarantee improvement. It is simply the only move that still has a chance of improvement.

### Implementation Demonstrated

```java
public static int maxArea(int[] heightArray) {
    int leftIndex = 0;
    int rightIndex = heightArray.length - 1;
    int maxArea = 0;

    while (rightIndex > leftIndex) {
        int height =
            Math.min(
                heightArray[rightIndex],
                heightArray[leftIndex]
            );

        int width = rightIndex - leftIndex;

        int area = width * height;

        maxArea = Math.max(area, maxArea);

        if (heightArray[rightIndex] > heightArray[leftIndex]) {
            leftIndex++;
        } else {
            rightIndex--;
        }
    }

    return maxArea;
}
```

Complexity:

```text
Time  → O(n)
Space → O(1)
```

Equal-height case understood:

```text
either pointer may move
```

Status:

```text
implementation       → correct
pointer mechanics    → correct
elimination proof    → required prompting, then understood
```

---

# 3Sum

## Reduction

Correctly identified:

```text
brute force
→ O(n³)
```

and then derived:

```text
sort
fix nums[i]
solve remaining pair using two pointers
```

leading to:

```text
O(n²)
```

Target complexity correctly understood as the expected interview solution.

Sorting enables:

```text
directional elimination
```

because increasing `left` cannot reduce the value and decreasing `right` cannot increase it.

---

## Initial Implementation Issue

Initial version failed to reset:

```text
rightPointer
```

for each fixed `i`.

This was identified and corrected.

---

## Duplicate Handling

Pointer-level duplicate skipping was discussed:

```text
skip duplicate nums[i]

after finding triplet:
    move left
    move right
    skip equal left values
    skip equal right values
```

Final user implementation instead used:

```java
Set<ArrayList<Integer>>
```

to deduplicate generated triplets.

This is functionally valid.

Pointer-level duplicate prevention was understood but not implemented.

### Final Demonstrated Approach

```java
public static Set findPairs(int nums[]) {
    Set<ArrayList<Integer>> pairs = new HashSet<>();

    Arrays.sort(nums);

    for (int i = 0; i < nums.length; i++) {
        int candidate = nums[i];

        int leftPointer = i + 1;
        int rightPointer = nums.length - 1;

        while (rightPointer > leftPointer) {
            ArrayList<Integer> pair = new ArrayList<>();

            if (nums[leftPointer] + nums[rightPointer]
                    == candidate * -1) {

                pair.add(candidate);
                pair.add(nums[leftPointer]);
                pair.add(nums[rightPointer]);

                pairs.add(pair);
            }

            if (nums[leftPointer] + nums[rightPointer]
                    > candidate * -1) {
                rightPointer--;
            } else {
                leftPointer++;
            }
        }
    }

    return pairs;
}
```

Additional refinement discussed:

```text
avoid candidate * -1 overflow edge case
```

by evaluating three-number sum using `long`.

### 3Sum Status

```text
O(n³) brute force             → understood
O(n²) reduction               → understood
sort + two pointers           → implemented
right reset bug               → identified and fixed
HashSet deduplication         → implemented
pointer duplicate skipping    → understood, not implemented
```

---

# Red-Black Tree / TreeMap

Detailed Red-Black Tree material was intentionally deferred.

Current retained interview-level knowledge:

```text
TreeMap
→ ordered map
→ balanced-tree implementation
→ O(log n) get/put/remove
→ supports ordered navigation
   floor / ceiling / ranges
```

Red-Black insertion/deletion/color-fixup mechanics are intentionally not blocking current progress.

---

# Binary Heap Foundations

## Why Heap

Compared alternatives.

### Unsorted Array

```text
add
→ O(1) amortized

peek/find min
→ O(n)

remove min
→ O(n)
```

### Sorted Array

```text
peek min
→ O(1)

insert
→ O(n)
```

even when insertion position is found in `O(log n)` because array elements must be shifted.

### Heap

Target operations:

```text
peek min
→ O(1)

insert
→ O(log n)

remove min
→ O(log n)
```

---

# Heap vs Balanced BST

User independently proposed using a BST for min/max extraction.

Correct trade-off discussion established:

```text
balanced BST
→ richer ordering semantics
→ arbitrary search
→ predecessor/successor
→ floor/ceiling
→ sorted traversal

heap
→ specialized extreme-priority access
→ compact array representation
→ less metadata
→ better locality
```

Core distinction retained:

> Balanced BST provides rich global ordering. Heap maintains only enough ordering to expose one extreme efficiently.

---

# Binary Heap Representation

Important mental model established:

```text
Heap
=
tree logically
+
array physically
```

A binary heap is a complete binary tree, typically represented level-by-level in an array.

Zero-based index formulas correctly retrieved:

```text
parent(i)
=
(i - 1) / 2

left(i)
=
2*i + 1

right(i)
=
2*i + 2
```

Example reasoning for index `1` was correct:

```text
parent → 0
left   → 3
right  → 4
```

---

# Heap Ordering Invariant

For min-heap:

```text
parent <= children
```

Important distinction understood:

```text
heap is NOT globally sorted
```

Sibling ordering is irrelevant.

Example:

```text
        1
      /   \
     4     3
```

is valid even though:

```text
4 > 3
```

because both children satisfy:

```text
>= parent
```

---

# Local Invariant → Global Root Minimum

An important concern was raised:

> Could a smaller value be hidden somewhere deeper in the tree because heap ordering is only local?

This was resolved through transitivity.

If every edge satisfies:

```text
parent <= child
```

then along any root-to-descendant path:

```text
root <= ... <= descendant
```

Therefore the root is globally minimum even though the rest of the heap is not globally sorted.

Also understood:

> After removing the root, the next-smallest value must be one of the two root children because each child is already the minimum of its subtree.

This was an important conceptual milestone.

---

# Heap Insert — Sift Up

Correct model established:

```text
append at next free array index
↓
compare with parent
↓
if child < parent:
    swap
↓
continue upward
↓
stop when invariant holds
```

Insertion does not search for the globally smallest element.

Only the newly inserted node's ancestor path can violate the invariant.

Example insertion of `2` into:

```text
[1,4,3,10,8,7,6]
```

was walked through:

```text
append at index 7

2 < 10
→ swap

2 < 4
→ swap

2 >= 1
→ stop
```

Final:

```text
[1,2,3,4,8,7,6,10]
```

Complexity:

```text
O(log n)
```

---

# Heap Poll — Sift Down

Correct model established:

```text
save root
↓
move last element to root
↓
remove last slot
↓
compare moved element with children
↓
swap with smaller child
↓
continue downward
```

Important correction:

Heap removal does not rebalance the entire heap.

Only one root-to-leaf path needs repair.

Therefore:

```text
poll
→ O(log n)
```

rather than `O(n)`.

Correctly understood that choosing the smaller child is necessary to restore:

```text
parent <= both children
```

---

# IntMinHeap Engineering Lab

A working `IntMinHeap` was implemented using:

```java
ArrayList<Integer>
```

backing storage.

### Demonstrated API

```java
add(...)
poll()
peek()
size()
isEmpty()
```

---

## add()

Implementation correctly used:

```text
append
+
sift up
```

with:

```java
parent = (index - 1) / 2
```

and termination:

```java
if (heap.get(parent) <= heap.get(index)) {
    break;
}
```

Status:

```text
correct
```

---

## poll()

Implementation correctly used:

```text
save root
move last element to root
remove last slot
sift down
```

Smaller-child selection correctly implemented by comparing:

```text
current
left
right
```

and choosing the smallest index.

Termination:

```java
if (smallest == index) {
    break;
}
```

correctly means the local heap invariant has been restored.

Status:

```text
correct
```

---

## Empty Contract

Initial contract was inconsistent:

```text
poll()
→ null

peek()
→ indirect IndexOutOfBoundsException
```

This was corrected to explicitly throw:

```java
NoSuchElementException("Heap is empty")
```

for empty access.

`peek()` correction demonstrated.

`poll()` was instructed to use the same contract.

---

## Implementation Status

```text
IntMinHeap             → implemented
add                    → implemented
sift up                → implemented
poll                   → implemented
sift down              → implemented
peek                   → implemented
size                   → implemented
isEmpty                → implemented
empty contract         → corrected
duplicate support      → structurally supported
```

### Test Status

The required repeated-poll sorted-output test was discussed:

```text
insert N values
poll until empty
→ output must be non-decreasing
```

but actual test execution/output was not demonstrated during the session.

Therefore:

```text
implementation complete
tests passing → not yet evidenced in session
```

---

# Java PriorityQueue

Correctly connected:

```java
PriorityQueue<Integer>
```

with:

```text
min-priority behavior under natural Integer ordering
```

Important distinction retained:

```text
Queue
→ FIFO

PriorityQueue
→ priority-based removal
```

PriorityQueue behavior now maps directly to the manually implemented heap mechanics rather than being treated as an opaque Java collection.

---

# Comparable vs Comparator

Initial recall:

```text
Comparator
→ functional interface
```

was correct, but the full distinction required reinforcement.

Final model established:

```text
Comparable
→ ordering belongs to the type
→ natural/default ordering
→ compareTo(...)

Comparator
→ external/use-case-specific ordering
→ compare(...)
→ multiple legitimate orderings possible
```

For scheduler jobs:

```text
scheduledAt
priority
createdAt
jobId
```

may all provide meaningful orderings.

Therefore scheduler priority is better represented using an external:

```text
Comparator<Job>
```

unless one ordering is truly universal for the domain type.

Initial preference for `Comparable` in the scheduler example was corrected.

---

# Production Connection — Heap + Scheduler

Strong connection made to Day-15 Job Scheduler.

Correctly rejected:

```text
load all 5 million future jobs
into one process heap
```

User independently proposed bounded retrieval such as:

```text
take first N
process/refill
```

This was refined into:

```text
durable DB
=
source of truth

scheduler:
query near-term jobs
ORDER BY scheduledAt
LIMIT batchSize
↓
put bounded subset into local heap
↓
refill periodically / as capacity becomes available
```

Important production boundary understood:

```text
heap
→ local ordering optimization

DB
→ durability / correctness
```

A scheduler must remain correct after process restart.

---

# Burst / Backpressure Reasoning

Scenario:

```text
100,000 jobs
scheduled for same second
```

User independently identified:

```text
memory pressure
tie-breaking / equal priority question
```

The more important system-level issue was then introduced:

```text
burst execution pressure
```

Potential overload targets:

```text
worker pool
DB connections
downstream APIs
queues
retry system
```

Correct production distinction established:

```text
priority
→ which eligible job executes next

backpressure / bounded concurrency
→ how many may execute at once
```

Recommended architecture:

```text
durable DB
↓
bounded batch claim
↓
local priority queue
↓
bounded worker pool
↓
execution
```

Important insight:

> Being due means a job becomes eligible for execution. It does not imply that 100,000 jobs must begin in the same millisecond.

Relevant metrics carried forward:

```text
runnable backlog
oldest runnable age
worker utilization
queue depth
execution latency
claim latency
retry/failure rate
```

---

# Day 16 Assessment

## Strong

* array/hash retrieval
* membership vs frequency distinction
* array locality / CPU cache reasoning
* Subarray Sum Equals K abstraction
* lower-bound binary-search reasoning
* binary-search monotonic-predicate abstraction
* sliding-window monotonicity reasoning
* BFS / FIFO reasoning
* LRU map + DLL invariant
* BST global invariant
* ancestor-bound validation
* AVL motivation
* two-pointer monotonic-elimination abstraction
* Container With Most Water implementation
* 3Sum O(n³) → O(n²) reduction
* heap-vs-array trade-off reasoning
* heap-vs-BST trade-off reasoning
* complete-tree array representation
* heap parent/child formulas
* local heap invariant
* local-invariant-to-global-min reasoning
* sift-up reasoning
* sift-down reasoning
* `IntMinHeap` implementation
* heap vs durable scheduler-state boundary
* bounded near-term loading intuition

---

## Needs Reinforcement

### Prefix Sum / Modulo

Revisit:

```text
Java negative remainder
modulo equivalence classes
Math.floorMod
why abs(remainder) is incorrect
```

### Two-Pointer Proof Discipline

For problems such as:

```text
Two Sum II
Container With Most Water
```

pointer movement was sometimes recognized before the elimination proof was articulated.

Continue forcing the structure:

```text
Because ______,
I know ______.
Therefore discarding ______ is safe.
```

### 3Sum Duplicate Handling

Current implementation uses:

```text
HashSet
```

successfully for deduplication.

Still reinforce pointer-level duplicate skipping:

```text
skip duplicate i
skip duplicate left
skip duplicate right
```

### Heap Testing

Implementation exists, but actual test evidence should still be produced:

```text
ascending inserts
descending inserts
random inserts
duplicates
repeated poll sorted-output
empty peek
empty poll
size correctness
```

### Comparable vs Comparator

Concept understood after correction.

Needs one spaced-retrieval check to ensure:

```text
natural ordering
vs
use-case-specific ordering
```

is automatic.

### Backpressure

Heap-memory concerns were identified independently.

System-wide burst/backpressure reasoning required prompting.

Reinforce distinction:

```text
ordering problem
!=
capacity-control problem
```

---

# Deferred

Intentionally deferred:

```text
Red-Black insertion/fixup
Red-Black deletion
TreeMap implementation deep dive
AVL implementation completion
bottom-up heapify O(n) proof
heap sort
Merge K Sorted Lists
full graph curriculum
```

These are not blocking Day-16 completion.

---

# Exact Day 17 Starting Direction

Proceed with:

```text
LeetCode:
Sliding Window transfer problems

+

Heap transfer:
Kth Largest
Top K Frequent

+

Trie foundations

+

Java/JVM execution block
```

## Exact Starting Action

Start Day 17 with a short heap retrieval:

> You receive a stream of numbers and must continuously know the kth largest value seen so far. Why is a heap useful, and should it be a min-heap or max-heap?

Require derivation before implementation.

Then move into:

```text
Kth Largest
→ bounded heap reasoning
```

followed by:

```text
Top K Frequent
→ frequency map + heap
```

This deliberately combines previously learned:

```text
HashMap frequencies
+
heap priority
```

and should expose whether heap understanding transfers beyond direct `peekMin()` / `poll()` mechanics.
