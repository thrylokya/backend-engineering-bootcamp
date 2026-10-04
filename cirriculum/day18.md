# Day 18 — Mixed LeetCode Transfer, Graph Foundations, BFS/DFS & JVM Class Loading

> **Duration:** ~3 hours
> **Phase:** Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution
> **Interview Track:** Continuous LeetCode / algorithm practice
> **Primary LeetCode Theme:** Unknown-pattern transfer — no pattern hint before reasoning
> **Phase-2 Theme:** Graph representation + BFS/DFS foundations
> **JVM Theme:** Class loading, linking, initialization, and JIT continuation
> **Weekday Revival Rule:** ~10 minutes only

---

## Schedule Overview

```text
Session 1 — Precision Retrieval                    10 min
Session 2 — LeetCode: Unknown-Pattern Transfer     55 min
Session 3 — Graph Representation                   30 min
Session 4 — BFS / DFS From First Principles        40 min
Session 5 — Graph LeetCode Transfer                25 min
Session 6 — JVM: Class Loading & Execution         20 min
                                                  ------
                                                   180 min
```

---

# Session 1 — Precision Retrieval

**10 minutes maximum**

No notes initially.

1. State the Kth Largest stream invariant.
2. `Comparable` vs `Comparator` — one sentence each.
3. In Java, what is `-1 % 5`?
4. Why is `Math.floorMod(-1, 5)` useful?
5. State the fixed-window invariant for Permutation in String.
6. Trie: `search()` vs `startsWith()`.
7. Explain `interpret → profile → hot code → JIT`.
8. Why does `PriorityQueue` solve ordering but not backpressure?

Stop after 10 minutes.

---

# Session 2 — LeetCode: Unknown-Pattern Transfer

**55 minutes**

## Problem — Search in Rotated Sorted Array (LeetCode 33)

Treat this as an interview problem. Do not begin by naming the algorithmic pattern.

Use:

```text
1. Objective
2. Relevant information
3. Brute force
4. Exploitable property
5. Invariant
6. Safe elimination
7. Complexity target
8. Implementation
```

Example:

```text
[4,5,6,7,0,1,2]
target = 0
```

Expected index:

```text
4
```

Baseline:

```text
linear scan → O(n)
```

Expected target:

```text
O(log n)
```

Ask:

> What useful ordering survives after rotation?

At every midpoint, at least one half is normally sorted. Derive why.

Then ask:

> If one half is sorted, can the target's value range tell us whether that half can contain the target?

For every boundary move, use:

```text
Because ______,
I know ______.
Therefore ______ can be safely discarded.
```

Test:

```text
[4,5,6,7,0,1,2], target = 0
[4,5,6,7,0,1,2], target = 3
[1], target = 1
[1], target = 0
[3,1], target = 1
[5,1,3], target = 5
```

Assume distinct values.

Exit bar:

```text
Time  → O(log n)
Space → O(1)
```

You should explain the solution in terms of surviving order + safe elimination, not merely say “modified binary search.”

---

# Session 3 — Graph Representation

**30 minutes**

Start with:

```text
A → B
A → C
C → D
```

Interpret this as directed service dependencies.

Learn/retrieve:

```text
vertex / node
edge
directed graph
undirected graph
weighted edge
path
cycle
in-degree
out-degree
connected component
```

## Adjacency List

```text
A → [B, C]
B → []
C → [D]
D → []
```

Java representations:

```java
Map<String, List<String>>
```

or:

```java
List<List<Integer>>
```

Space:

```text
O(V + E)
```

Neighbor iteration:

```text
O(out-degree)
```

## Adjacency Matrix

```text
V × V
```

Space:

```text
O(V²)
```

Edge lookup:

```text
O(1)
```

Neighbor enumeration:

```text
O(V)
```

Reason through:

> 10 million entities, each with ~3 outgoing edges. Which representation is sensible, and why?

Do not answer with a slogan. Use memory scaling.

---

# Session 4 — BFS / DFS From First Principles

**40 minutes**

Trees gave us BFS/DFS without cycles. Graphs add:

```text
cycles
multiple paths to the same node
no single parent
disconnected regions
```

Therefore graph traversal needs:

```text
visited state
```

## BFS

Objective:

```text
explore in increasing edge-distance from source
```

Use:

```text
FIFO queue
```

Mark visited when enqueued.

Why?

Because two neighbors may discover the same vertex before it is dequeued. Marking on enqueue prevents duplicate queue entries.

Invariant:

> The queue contains discovered but unprocessed vertices, and every queued vertex is already marked visited.

For an unweighted graph, BFS gives shortest path length in number of edges.

## DFS

Objective:

```text
follow one path deeply before backing up
```

Use recursion or an explicit stack.

Conceptual form:

```text
dfs(node):
    mark visited
    for neighbor:
        if unseen:
            dfs(neighbor)
```

## BFS vs DFS

Do not reduce this to “queue vs stack.”

Think objective:

```text
BFS
→ unweighted shortest distance / level reachability

DFS
→ deep exploration / cycle reasoning / components / topo-style reasoning
```

With adjacency list:

```text
Time  → O(V + E)
Space → O(V)
```

For whole-graph traversal:

```text
for every vertex:
    if not visited:
        start traversal
```

---

# Session 5 — Graph LeetCode Transfer

**25 minutes**

## Problem — Number of Islands (LeetCode 200)

Do not begin by saying “graph problem.”

Given land/water grid, define what one island means.

Correct abstraction:

```text
one connected component of land cells
```

Scan every cell.

When an unseen land cell is found:

```text
count += 1
```

Then explore the entire connected land region using BFS or DFS and mark it visited.

Invariant:

> Every land cell belonging to a discovered island is marked before scanning continues to count another island.

For `R × C` grid:

```text
Time  → O(R × C)
Space → O(R × C) worst case
```

Pick one traversal implementation. Explain how the other would differ.

If modifying the grid is allowed, discuss using the grid itself as visited state.

---

# Session 6 — JVM: Class Loading & Execution

**20 minutes**

Continue from:

```text
.java
→ javac
→ .class bytecode
→ JVM
→ interpreter/profile
→ JIT
→ native code
```

Now zoom into class loading.

High-level lifecycle:

```text
Loading
↓
Linking
    Verification
    Preparation
    Resolution
↓
Initialization
```

## Loading

JVM obtains class bytecode and creates runtime class metadata.

## Verification

Checks bytecode structural/type-safety constraints.

## Preparation

Sets up class-level runtime state such as static-field storage with default values.

Distinguish this from user-written static initialization.

## Resolution

Symbolic references may be resolved into runtime references.

Interview-depth concept only.

## Initialization

Runs:

```text
static field initializers
static blocks
```

Interview answer:

> `javac` does not produce CPU-native instructions. It produces JVM bytecode. The JVM loads, verifies, links, and initializes classes; bytecode is executed by the JVM, and hot code may later be JIT-compiled into native machine code.

---

# Day 18 Deliverables

## Retrieval
- [ ] Kth Largest invariant
- [ ] Comparable vs Comparator
- [ ] `-1 % 5`
- [ ] `Math.floorMod`
- [ ] fixed-window invariant
- [ ] Trie exact vs prefix
- [ ] JIT chain
- [ ] backpressure explanation
- [ ] retrieval kept under 10 min

## Mixed LeetCode
- [ ] brute force stated
- [ ] surviving order derived
- [ ] sorted-half reasoning
- [ ] safe elimination explained
- [ ] implementation completed
- [ ] edge cases tested
- [ ] O(log n) justified

## Graph Foundations
- [ ] directed vs undirected
- [ ] adjacency list
- [ ] adjacency matrix
- [ ] sparse-vs-dense trade-off
- [ ] O(V + E) vs O(V²)

## BFS / DFS
- [ ] visited-state requirement
- [ ] BFS queue derived
- [ ] mark-on-enqueue reasoning
- [ ] DFS recursion/stack
- [ ] BFS vs DFS objective
- [ ] O(V + E)
- [ ] disconnected traversal

## Number of Islands
- [ ] connected-component abstraction
- [ ] BFS or DFS implementation
- [ ] visited-state correctness
- [ ] O(R×C)

## JVM
- [ ] Loading
- [ ] Verification
- [ ] Preparation
- [ ] Resolution
- [ ] Initialization
- [ ] bytecode vs native code retained

---

# End-of-Day Exit Criteria

## Algorithms

You can handle a problem where familiar structure is partially disrupted by asking:

```text
What property still survives?
What does it let me eliminate safely?
```

## Graphs

You understand graph traversal as:

```text
representation
+
frontier mechanism
+
visited state
```

and can derive complexity from the representation.

## JVM

You can explain how `.class` bytecode becomes a loaded/initialized JVM class and eventually hot native code.

---

# Intentionally Deferred

- Course Schedule
- topological sort
- cycle detection details
- Union-Find
- Dijkstra
- weighted shortest path
- SCC
- Clone Graph
- class-loader delegation hierarchy
- custom class loaders
- metaspace deep dive
- escape analysis
- method inlining
- full HLD

---

# Day 18 Exact Starting Action

Start with the 10-minute precision retrieval.

Then ask:

> **You are given `[4,5,6,7,0,1,2]` and target `0`. A linear scan works, but the interviewer requires O(log n). What useful ordering information still exists even though the array is no longer globally sorted?**

Require reasoning before code.

Then begin graphs with:

> **A service can depend on several other services, and dependencies are directed. What is the minimum information we must preserve in memory so we can answer “which services does A depend on?” and traverse those dependencies?**

---

# Registry Update Requirements

At the end of Day 18 capture demonstrated evidence only:

1. retrieval precision/time
2. Comparable vs Comparator retention
3. modulo/floorMod retention
4. backpressure explanation independence
5. rotated-array abstraction
6. sorted-half derivation
7. elimination proof
8. implementation/boundary bugs
9. graph vocabulary
10. adjacency-list reasoning
11. matrix trade-off
12. BFS derivation
13. mark-on-enqueue understanding
14. DFS understanding
15. O(V+E) reasoning
16. disconnected-graph reasoning
17. Number of Islands abstraction
18. implementation quality
19. JVM class lifecycle understanding
20. exact Day-19 starting action

Recommended Day-19 direction if Day 18 is strong:

```text
LeetCode:
Graph transfer — Clone Graph / Rotting Oranges

+
Graphs:
cycle detection / topological sort

+
Union-Find foundations

+
JVM:
class loaders / metaspace / deeper JIT
```
