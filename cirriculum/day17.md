# Day 17 — Heap Transfer, Sliding Window LeetCode, Trie Foundations & JVM Execution

> **Duration:** ~3 hours  
> **Phase:** Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution  
> **Interview Track:** Continuous LeetCode / algorithm practice  
> **Primary LeetCode Themes:** Heap transfer + Sliding Window  
> **Phase-2 Concept:** Trie foundations  
> **Java/JVM Theme:** Source → bytecode → interpreter → JIT  
> **Weekday Revival Rule:** ~10 minutes only

---

# Schedule Overview

```text
Session 1 — Precision Retrieval                    10 min
Session 2 — LeetCode: Heap Transfer                65 min
Session 3 — LeetCode: Sliding Window Transfer      45 min
Session 4 — Trie Foundations                       30 min
Session 5 — JVM: Source → Execution                20 min
Session 6 — Production / Exit Drill                10 min
                                                  ------
                                                   180 min
```

---

# Session 1 — Precision Retrieval

**10 minutes**

Only revisit Day-16 weak spots.

## Heap

Recall:

```text
parent(i) = (i - 1) / 2
left(i)   = 2*i + 1
right(i)  = 2*i + 2
```

Explain why the root is globally minimum even though a heap is not globally sorted.

## Comparable vs Comparator

```text
Comparable
→ natural/default ordering belonging to the type

Comparator
→ external/use-case-specific ordering
```

For a `Job` that can be ordered by scheduled time, priority, or creation time, explain why `Comparator` is usually more appropriate.

## Modulo

In Java:

```java
-1 % 5 == -1
```

Normalize with:

```java
Math.floorMod(value, k)
```

Stop after 10 minutes.

---

# Session 2 — LeetCode: Heap Transfer

**65 minutes**

## Problem 1 — Kth Largest Element in a Stream

**LeetCode 703 — 30 minutes**

Do not start by naming a heap.

Ask:

> What is the smallest set of values that must remain in memory?

If `k = 3`, retain only the largest three values seen so far.

Among those three, the answer is:

```text
the smallest
```

Therefore use:

```text
min-heap of size k
```

### Invariant

> After every processed value, the heap contains exactly the largest `k` values seen so far, or all values if fewer than `k` have arrived.

When:

```text
heap.size() > k
```

remove the smallest retained value.

### Safe-elimination proof

Among `k + 1` retained candidates, one value must fall outside the top `k`.

The smallest is that value, so removing it cannot remove a required top-k element.

### Complexity

```text
add/remove → O(log k)
peek       → O(1)
space      → O(k)
```

Be precise: this is `log k`, not automatically `log n`.

---

## Problem 2 — Top K Frequent Elements

**LeetCode 347 — 35 minutes**

This combines:

```text
HashMap frequencies
+
Heap priority
```

### Step 1 — Transform the problem

Build:

```text
value → frequency
```

using:

```java
Map<Integer, Integer>
```

### Step 2 — Baseline

Sort all distinct values by frequency:

```text
O(n + m log m)
```

where:

```text
m = number of distinct values
```

But we only need `k`.

### Step 3 — Bounded heap

Maintain:

```text
min-heap of size k
```

ordered by frequency.

For each distinct value:

```text
push candidate

if heap.size() > k:
    remove least-frequent retained candidate
```

### Invariant

> The heap contains the `k` highest-frequency candidates seen so far, or all candidates if fewer than `k` have been processed.

### Why min-heap?

Among retained top-k candidates, the least frequent is the one we need to replace most cheaply.

### Complexity

```text
frequency map → O(n)
heap work     → O(m log k)

total         → O(n + m log k)
```

Use `Comparator` intentionally.

---

# Session 3 — LeetCode: Sliding Window Transfer

**45 minutes**

## Problem — Permutation in String

**LeetCode 567**

Given:

```text
s1
s2
```

determine whether `s2` contains a permutation of `s1`.

Example:

```text
s1 = "ab"
s2 = "eidbaooo"

→ true
```

because `"ba"` is a permutation of `"ab"`.

### Step 1 — Derive the window

A valid substring must have:

```text
length == s1.length()
```

So the window size is fixed.

### Step 2 — Identify repeated work

Brute force could rebuild character counts for every substring.

But when the window advances one position, only:

```text
one character leaves
one character enters
```

So maintain state incrementally.

### Possible state

```text
target frequency
window frequency
```

Use arrays of size 26 if inputs are lowercase English.

### Invariant

> At each comparison point, the window-frequency state represents exactly the current `s1.length()` characters in `s2`.

When advancing:

```text
remove outgoing character
add incoming character
```

### Complexity

With 26-character arrays:

```text
Time  → O(|s1| + |s2|)
Space → O(1)
```

### Interview requirement

Before coding, explain:

1. Why this is a window problem.
2. Why the window size is fixed.
3. What brute force repeats.
4. What changes when the window moves.
5. What invariant must remain true.

Do not answer merely:

```text
"Sliding window because substring."
```

---

## Optional Hard Decomposition

If time remains, spend **5–10 minutes only** on:

**LeetCode 76 — Minimum Window Substring**

Do not require a full implementation.

Reason about:

```text
what makes a window valid?
what state tells us validity?
when can left move safely?
why is this variable-size?
```

This is Hard exposure, not a new rabbit hole engineered by people who clearly had too much free time.

---

# Session 4 — Trie Foundations

**30 minutes**

A Trie is useful when:

```text
prefix
```

matters.

Suppose we store:

```text
cat
car
care
dog
```

Common prefixes share structure.

## Trie Node

Conceptually:

```text
children
isWord
```

Possible Java representation:

```java
Map<Character, TrieNode> children;
boolean isWord;
```

For lowercase English only:

```java
TrieNode[26]
```

is another choice.

### Trade-off

Array children:

```text
fast direct indexing
more unused memory
```

Map children:

```text
sparse storage
hashing/object overhead
```

## Required operations

Understand and preferably implement:

```text
insert(word)
search(word)
startsWith(prefix)
```

## Complexity

For word length `L`:

```text
insert     → O(L)
search     → O(L)
startsWith → O(L)
```

assuming constant-ish child lookup.

Important:

```text
cost depends mainly on key length,
not directly on number of stored words
```

## Trie vs HashSet

HashSet:

```text
exact membership
```

Trie:

```text
prefix queries
```

Production connections:

```text
autocomplete
dictionary lookup
prefix routing
search suggestions
```

---

# Session 5 — JVM: Source → Execution

**20 minutes**

Build this mental chain:

```text
.java source
↓
javac
↓
.class bytecode
↓
class loading
↓
verification / linking / initialization
↓
JVM execution
↓
interpreter
↓
hot-code detection
↓
JIT compilation
↓
machine code
```

## Interpreter vs JIT

Initial execution may be interpreted.

Frequently executed methods/loops become:

```text
hot
```

The JVM can compile hot code to optimized machine instructions.

Runtime profile therefore affects performance.

## Why warmup matters

A naive benchmark measured immediately may include:

```text
class loading
interpretation
JIT activity
```

Later execution may behave differently.

This is one reason:

```java
System.nanoTime()
```

around a tiny method is not a trustworthy microbenchmark by itself.

JMH handles warmup and measurement more carefully.

Do not deep-dive tiered compilation, inlining, escape analysis, or code cache today.

---

# Session 6 — Production / Exit Drill

**10 minutes**

Scenario:

```text
incoming tasks = 20,000/sec
worker capacity = 5,000/sec
```

Question:

> Does using a better heap solve this?

No.

Heap solves:

```text
ordering
```

The production problem is:

```text
arrival rate > service rate
```

leading to:

```text
queue growth
latency growth
memory pressure
```

Potential controls:

```text
bounded queues
backpressure
load shedding
capacity scaling
```

Remember:

```text
priority != capacity
```

---

# End-of-Day Retrieval

Without notes:

1. Why does Kth Largest use a **min-heap of size k**?
2. State its invariant.
3. Why is removing the smallest retained value safe?
4. Why does Top K Frequent need a frequency map first?
5. Why does Top K Frequent also use a min-heap of size k?
6. What makes Permutation in String a fixed-size window?
7. What exactly changes when the window advances one position?
8. Trie vs HashSet: when does Trie win?
9. What does JIT do at a high level?
10. Why does Java benchmark warmup matter?
11. Why doesn't a PriorityQueue solve backpressure?

---

# Day 17 Deliverables

## Heap LeetCode

- [ ] Kth Largest derived without premature pattern naming
- [ ] min-heap-of-size-k reasoning
- [ ] invariant stated
- [ ] safe elimination proved
- [ ] complexity `O(log k)` per update
- [ ] Top K Frequent frequency map derived
- [ ] bounded heap derived
- [ ] comparator used intentionally

## Sliding Window

- [ ] fixed window derived
- [ ] repeated brute-force work identified
- [ ] incremental add/remove implemented
- [ ] invariant stated
- [ ] complexity explained
- [ ] Hard decomposition attempted only if time permits

## Trie

- [ ] Trie mental model
- [ ] children + `isWord`
- [ ] insert
- [ ] search
- [ ] startsWith
- [ ] array-vs-map trade-off
- [ ] O(L) reasoning

## JVM

- [ ] source → bytecode → JVM chain
- [ ] interpreter vs JIT
- [ ] hot-code concept
- [ ] warmup significance
- [ ] JMH connection

## Production

- [ ] ordering vs backpressure distinction retained

---

# End-of-Day Exit Criteria

Day 17 is complete when:

## Heap Transfer

You think of a heap as:

```text
maintaining an extreme over a changing candidate set
```

rather than merely a structure with `peek()` and `poll()`.

## Sliding Window

You can derive:

```text
window size
state
incremental update
validity condition
```

without being told the pattern.

## Trie

You can explain why prefix queries change the data-structure choice compared with exact membership.

## JVM

You understand:

```text
interpret
→ profile
→ identify hot code
→ JIT compile
→ optimized machine code
```

---

# Intentionally Deferred

- Merge K Sorted Lists
- heapify O(n) proof
- heap sort
- Minimum Window Substring full solution
- radix tree
- compressed trie
- graph curriculum
- class-loader delegation
- JIT tiers
- method inlining
- escape analysis
- code cache
- GC
- full HLD

---

# Day 17 Exact Starting Action

Start with:

> **A stream of integers arrives continuously. After every insertion, you must return the kth largest value seen so far. What is the smallest set of values you actually need to retain?**

Do not name the data structure yet.

Once you derive:

```text
retain only the largest k values
```

ask:

> **Among those k values, which one is the answer?**

That should lead naturally to:

```text
minimum of top-k
→ min-heap
```

Then state the invariant before writing code.

---

# Registry Update Requirements

At the end of Day 17 capture demonstrated evidence only:

1. heap invariant retrieval
2. Comparable vs Comparator retrieval
3. modulo/floorMod retrieval
4. Kth Largest abstraction quality
5. min-heap vs max-heap reasoning
6. Kth Largest implementation/bugs
7. Top K Frequent abstraction
8. HashMap + heap composition
9. comparator reasoning
10. sliding-window derivation quality
11. Permutation in String implementation/bugs
12. fixed-window invariant
13. Hard decomposition if attempted
14. Trie mental model
15. Trie implementation status
16. array-vs-map child trade-off
17. JVM interpreter/JIT understanding
18. warmup/JMH reasoning
19. backpressure-vs-ordering precision
20. exact Day-18 starting action

Recommended Day-18 direction if Day 17 is strong:

```text
LeetCode:
Binary Search / mixed unknown transfer

+
Graphs:
representation + BFS/DFS foundations

+
JVM:
class loading / bytecode / JIT continuation

+
short HLD / production-design block
```
