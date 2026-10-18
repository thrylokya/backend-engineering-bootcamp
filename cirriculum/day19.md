# Day 19 — Multi-Source BFS, Topological Sort, Course Schedule, Union-Find & JVM Class Loaders

> **Duration:** ~3 hours
> **Phase:** Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution
> **Mode:** Problem-first / interview-oriented
> **Primary DSA Theme:** Graph transfer
> **Primary Concepts:** Multi-source BFS, directed-cycle detection, topological sorting, Union-Find
> **JVM Theme:** Class loaders, parent delegation, class identity, metaspace
> **Important:** Day 18 open items remain in the registry and continue through brief spaced retrieval. They do not replace the Day 19 curriculum.

---

## Day 19 Objective

By the end of today, you should be able to distinguish:

```text
ordinary traversal
vs
wave/level propagation
vs
dependency ordering
vs
dynamic connectivity
```

without needing me to name the algorithm first.

The interview flow is:

```text
problem statement
→ identify graph meaning
→ derive state/invariant
→ choose traversal/structure
→ implement
→ explain correctness
```

---

## Schedule

```text
Session 1 — Short Retrieval                         10 min
Session 2 — Rotting Oranges                         35 min
Session 3 — Topological Sort From First Principles  35 min
Session 4 — Course Schedule                         30 min
Session 5 — Union-Find Foundations + Implementation 40 min
Session 6 — Union-Find Transfer Problem             20 min
Session 7 — JVM Class Loaders + Metaspace           10 min
                                                   -------
                                                    180 min
```

---

# Session 1 — Short Retrieval

**10 minutes maximum**

No long review.

Answer without notes:

1. Why do graph traversals need `visited` state?
2. Why does BFS mark visited when enqueuing?
3. BFS vs DFS: what objective makes BFS preferable?
4. Adjacency-list traversal complexity?
5. Directed cycle vs deadlock — one sentence.
6. JVM: explain briefly why `loading != initialization`.

Stop after 10 minutes.

---

# Session 2 — Rotting Oranges

**35 minutes**

## LeetCode 994

Do not start with:

```text
This is BFS.
```

Start from the process.

Every minute:

```text
all currently rotten oranges
infect adjacent fresh oranges simultaneously
```

### Step 1 — Define the state

Need:

```text
currently active rotten oranges
fresh oranges remaining
minutes elapsed
```

Question:

> If several rotten oranges exist initially, which one do we start from?

Answer:

```text
all of them
```

Therefore the initial frontier is:

```text
all initially rotten oranges
```

This is multi-source propagation.

### Frontier meaning

One complete BFS level corresponds to:

```text
one minute
```

because all oranges already rotten at the start of that minute act before newly infected oranges spread during the next minute.

### Invariant

> At the start of each BFS level, the queue contains exactly the oranges that can spread rot during the current minute.

New infections form the next frontier.

### Typical state

```java
Queue<int[]> queue;
int freshCount;
int minutes;
```

Initial scan:

```text
cell == 2 → enqueue
cell == 1 → freshCount++
```

When a fresh neighbor becomes rotten:

```text
1 → 2
freshCount--
enqueue neighbor
```

### Edge cases

```text
no fresh oranges initially
all fresh and no rotten
unreachable fresh orange
single rotten orange
single fresh orange
multiple rotten sources
```

### Complexity

```text
Time  → O(R × C)
Space → O(R × C)
```

### Exit question

Why would running independent BFS from every rotten orange be wasteful and not match the simultaneous-spread model?

---

# Session 3 — Topological Sort From First Principles

**35 minutes**

Do not begin with Kahn's algorithm.

Start with:

```text
A must happen before B
A must happen before C
B must happen before D
C must happen before D
```

Represent:

```text
A → B
A → C
B → D
C → D
```

Interpret:

```text
u → v
means
u must happen before v
```

### Who can go first?

Ask:

> Which nodes have no unmet prerequisites?

Answer:

```text
in-degree = 0
```

These are safe to process.

### After processing one

If A is done:

```text
A → B
A → C
```

no longer constrain B/C.

Implementation:

```text
decrement neighbors' indegree
```

When a neighbor reaches:

```text
0
```

it becomes eligible.

### Derive Kahn's algorithm

```text
1. compute indegree for every node
2. enqueue all indegree-0 nodes
3. process one
4. decrement indegree of outgoing neighbors
5. enqueue newly-zero neighbors
6. repeat
```

### Queue invariant

> Every node in the queue currently has no unmet prerequisite among unprocessed nodes.

### Cycle detection

Example:

```text
A → B
B → C
C → A
```

No node can reach indegree zero.

General condition:

```text
processedCount < V
```

after queue exhaustion means:

```text
directed cycle exists
```

### Complexity

```text
Time  → O(V + E)
Space → O(V + E)
```

---

# Session 4 — Course Schedule

**30 minutes**

## LeetCode 207

For:

```text
[course, prerequisite]
```

edge is:

```text
prerequisite → course
```

Do not reverse it.

### Abstraction

The real question is:

> Does the prerequisite graph contain a directed cycle?

If yes:

```text
not all courses can be completed
```

If no:

```text
a valid ordering exists
```

### Implementation

Build:

```text
adjacency list
indegree[]
queue
processedCount
```

Return:

```java
processedCount == numCourses;
```

### Required tests

```text
1 course, no prerequisites
simple chain
diamond dependency
2-node cycle
longer cycle
multiple disconnected prerequisite components
```

### Precision

Course Schedule asks whether an ordering exists, not to return the ordering.

So:

```text
processedCount == numCourses
```

is sufficient.

---

# Session 5 — Union-Find Foundations + Implementation

**40 minutes**

Change the graph problem.

Suppose edges arrive over time:

```text
(1,2)
(2,3)
(4,5)
...
```

Repeated questions:

```text
Are 1 and 3 connected?
Are 1 and 5 connected?
```

Repeated BFS/DFS can answer this, but repeated traversal is wasteful.

We want a structure specialized for:

```text
dynamic connectivity
```

## Core model

Each connected component has a representative/root.

Maintain:

```java
int[] parent;
```

Initially:

```text
parent[i] = i
```

Every node starts in its own component.

## find(x)

Return the representative of x's component.

Basic version:

```java
while (x != parent[x]) {
    x = parent[x];
}
return x;
```

## union(a, b)

```text
rootA = find(a)
rootB = find(b)
```

If roots are the same:

```text
already connected
```

Otherwise merge them.

## Problem with naive union

Bad sequences can create:

```text
1 → 2 → 3 → 4 → 5
```

making `find` expensive.

## Optimization 1 — Path compression

```java
int find(int x) {
    if (parent[x] != x) {
        parent[x] = find(parent[x]);
    }
    return parent[x];
}
```

## Optimization 2 — Union by size/rank

Track:

```text
size[]
```

or:

```text
rank[]
```

Attach the smaller/shallower component below the larger/deeper one.

Use one strategy.

## Complexity

With both optimizations:

```text
amortized O(α(n))
```

Effectively constant for practical input sizes.

## Suggested API

```java
class UnionFind {
    int[] parent;
    int[] size;

    UnionFind(int n);

    int find(int x);

    boolean union(int a, int b);

    boolean connected(int a, int b);
}
```

A useful `union` contract:

```text
true  → two separate components were merged
false → already connected
```

## Tests

```text
initially separate
union(1,2)
union(2,3)
connected(1,3) == true
connected(1,4) == false
union(1,3) == false
multiple components
repeated find/path compression
```

---

# Session 6 — Union-Find Transfer Problem

**20 minutes**

## LeetCode 684 — Redundant Connection

An undirected graph was originally a tree, then one extra edge was added.

Need:

```text
edge that creates the cycle
```

Do not begin by saying Union-Find.

For every edge:

```text
u — v
```

ask:

> Were u and v already connected before this edge?

If no:

```text
union them
```

If yes:

```text
this edge closes a cycle
```

### Invariant

> Before processing the next edge, Union-Find represents exactly the connectivity created by previously accepted edges.

If:

```text
find(u) == find(v)
```

then an existing path already connects them, so adding the new edge creates a cycle.

### Complexity

```text
O(E α(V))
```

Effectively near linear.

---

# Session 7 — JVM Class Loaders + Metaspace

**10 minutes**

Keep this compact. JVM class initialization remains a spaced-retrieval thread.

## Class loader purpose

A class loader determines where class bytecode comes from and defines the runtime class.

Common conceptual hierarchy:

```text
Bootstrap ClassLoader
Platform ClassLoader
Application ClassLoader
```

## Parent delegation

Typical behavior:

```text
ask parent first
```

Why?

```text
avoid replacing core platform classes
promote consistent loading/class identity
```

## Class identity

Important interview rule:

```text
class identity
=
fully qualified class name
+
defining class loader
```

Same class name loaded by different defining loaders can be different JVM types.

This helps explain plugin/container `ClassCastException` cases.

## Metaspace

Class metadata lives in:

```text
Metaspace
```

rather than the ordinary Java object heap.

Conceptually includes metadata for:

```text
classes
methods
runtime class structures
```

If old class loaders stay reachable during repeated redeployments, their class metadata may remain reachable and metaspace can grow.

---

# End-of-Day Interview Drill

Without notes:

1. Why is Rotting Oranges multi-source?
2. What does one BFS level represent?
3. What does indegree zero mean?
4. Why does `processedCount < V` imply a cycle?
5. Which direction does Course Schedule's prerequisite edge point?
6. BFS/DFS vs Union-Find: when do you choose each?
7. What does path compression accomplish?
8. What does union-by-size/rank accomplish?
9. Why does `find(u) == find(v)` expose a redundant edge?
10. What determines JVM class identity?

---

# Day 19 Exit Criteria

You should be able to distinguish:

```text
1. Reachability / exploration
   → BFS / DFS

2. Simultaneous propagation
   → multi-source BFS

3. Dependency ordering
   → topological sort / cycle detection

4. Repeated connectivity / component merging
   → Union-Find
```

You should not need the algorithm name first.

The goal is:

```text
problem semantics
→ invariant
→ data structure
```

---

# Intentionally Deferred

- Dijkstra
- Bellman-Ford
- Floyd-Warshall
- SCC
- Kruskal / Prim
- Course Schedule II
- advanced DSU variants
- weighted Union-Find
- custom class loaders
- metaspace tuning flags
- reflection
- escape analysis
- GC

---

# Exact Day 19 Starting Action

Start with:

> Several rotten oranges already exist in different parts of the grid. Every minute they all spread simultaneously. What information must be in memory at the start of minute 0?

Do not say “multi-source BFS” yet.

Derive the state first.

---

# Registry Update Requirements

At Day 19 completion, record demonstrated evidence only:

1. Rotting Oranges abstraction
2. multi-source derivation
3. minute/frontier invariant
4. implementation bugs
5. indegree reasoning
6. Kahn derivation
7. cycle-detection explanation
8. Course Schedule edge direction
9. Course Schedule implementation
10. disconnected dependency handling
11. Union-Find abstraction
12. find implementation
13. path compression
14. union by size/rank
15. Redundant Connection transfer
16. BFS/DFS vs DSU distinction
17. class-loader hierarchy
18. parent delegation
19. class identity
20. metaspace understanding
21. exact Day-20 starting action

Recommended Day-20 direction if Day 19 is strong:

```text
mixed graph / Union-Find LeetCode
+
weighted graph / shortest-path intuition
+
Java Collections internals
+
JVM spaced retrieval
+
Phase-2 exit preparation
```
