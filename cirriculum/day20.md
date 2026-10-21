# Day 20 — Dijkstra Transfer, Union-Find Composition, Monotonic Stack & Phase-2 Interview Integration

**Duration:** ~3 hours  
**Phase:** Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution  
**Mode:** Interview-first  
**DSA rule:** Do not reveal the pattern before deriving it from the problem.

## Schedule

```text
Session 1 — Precision Retrieval / Day-19 Check        10 min
Session 2 — Unknown Graph Problem: Network Delay      50 min
Session 3 — Union-Find Transfer: Accounts Merge       40 min
Session 4 — Unknown DSA Transfer: Daily Temperatures  35 min
Session 5 — Java Collections Interview Drill          25 min
Session 6 — JVM Spaced Challenge                      15 min
Session 7 — Phase-2 Exit Check                         5 min
                                                     -------
                                                      180 min
```

## 1. Precision Retrieval — 10 min

Without notes:

1. When does multi-source BFS differ from ordinary BFS?
2. In Kahn's algorithm, what does `indegree == 0` mean?
3. Why does `processedCount < V` imply a directed cycle?
4. BFS/DFS vs Union-Find — what problem does each solve?
5. What do path compression and union-by-size/rank accomplish?
6. What determines JVM class identity?
7. Why is a dependency cycle not automatically a deadlock?

Correct only what is wrong; do not reopen Day 19.

## 2. Network Delay Time — 50 min

**LeetCode 743**

Do not name Dijkstra first.

Given directed weighted edges and a source, determine when every node receives the signal.

Start with:

```text
What are the nodes?
What do edge weights mean?
What exactly is the output?
Why is ordinary BFS insufficient when weights differ?
```

Derive the required state:

```text
best known distance to each node
+
a structure that exposes the smallest-distance candidate
```

The priority queue should emerge from:

> Which unsettled node should I process next?

Key invariant:

> With non-negative weights, when the smallest-distance unprocessed node is finalized, no later path can improve it.

Relaxation:

```text
candidate = dist[current] + weight
if candidate < dist[neighbor]:
    update dist[neighbor]
```

Allow stale PQ entries and skip when:

```text
polledDistance > bestKnownDistance[node]
```

Final answer:

```text
max shortest distance from source
```

If any node is unreachable:

```text
-1
```

Target complexity:

```text
O((V + E) log V)
```

Exit bar: explain the reasoning without relying on the phrase “because this is Dijkstra.”

## 3. Accounts Merge — 40 min

**LeetCode 721**

Do not start with Union-Find.

Derive the relationship:

```text
accounts sharing an email belong to one connected component
```

Transitive sharing matters.

Choose what DSU represents. A practical approach:

```text
DSU over account indices
email → first account index seen
```

For each email:

```text
unseen → record account
seen   → union current account with previous owner
```

After unions, rebuild:

```text
root account → all emails
```

Then sort emails and prepend the name.

Required interview questions:

1. Why is pairwise account comparison wasteful?
2. Why is email-sharing connectivity transitive?
3. What exactly is the DSU element?
4. What map bridges emails to DSU elements?
5. How are components reconstructed?
6. Which cost comes from sorting?

Goal: compose `HashMap + Union-Find` without being handed the full solution.

## 4. Daily Temperatures — 35 min

**LeetCode 739**

This is intentionally not a graph problem.

Start with brute force:

```text
for each day, scan right for first warmer day
→ O(n²)
```

Ask:

> What work is repeated, and which earlier days remain unresolved?

Derive a structure that retains unresolved indices.

Invariant to reach:

> The retained indices are unresolved, and their temperatures are monotonic so the current temperature can resolve candidates from one end.

Then derive the monotonic stack.

Core operation:

```text
while stack not empty
and currentTemp > temp[stack.peek()]:

    prev = stack.pop()
    answer[prev] = i - prev

push i
```

Complexity proof:

```text
each index pushed once
each index popped at most once
→ O(n)
```

Exit bar: explain why popping an index is permanent and safe.

## 5. Java Collections Interview Drill — 25 min

No long implementation.

For each scenario, choose the collection and defend it.

### Scenario A
Fast expected key/value lookup, no ordering.

Discuss:

```text
HashMap
```

At interview depth explain hash, bucket, collision, equality, resize/load factor.

### Scenario B
Sorted keys, floor/ceiling, ordered navigation.

Discuss:

```text
TreeMap
```

Typical operations:

```text
O(log n)
```

### Scenario C
Repeated highest/lowest priority extraction.

Discuss:

```text
PriorityQueue
```

Distinguish from FIFO Queue.

### Scenario D
Frequent append, indexed access, iteration.

Discuss why `ArrayList` is often preferable to `LinkedList` in real Java workloads:

```text
locality
allocation overhead
index access
```

### Scenario E
Thread-safe key/value access.

Identify:

```text
ConcurrentHashMap
```

Park internals for Phase 3.

## 6. JVM Spaced Challenge — 15 min

Predict before running:

```java
class Parent {
    static {
        System.out.println("Parent initialized");
    }

    static int value = 10;
}

class Child extends Parent {
    static {
        System.out.println("Child initialized");
    }
}

public class Main {
    public static void main(String[] args) {
        System.out.println(Child.value);
    }
}
```

Answer:

1. Which class declares `value`?
2. Which class is actively used by `getstatic`?
3. Does writing `Child.value` necessarily initialize `Child`?
4. Predict exact output.
5. Explain the rule.

Record prediction vs actual result.

Do not close the JVM initialization track.

## 7. Phase-2 Exit Check — 5 min

Without notes:

```text
Tree/BST        → what invariant/problem?
Heap            → what problem shape?
Trie            → what problem shape?
BFS             → when?
DFS             → when?
Topological     → when?
Union-Find      → when?
Dijkstra        → when?
```

The goal is to recognize these from problem semantics, not names.

## Day 20 Deliverables

### Weighted graph
- [ ] explain why ordinary BFS fails
- [ ] derive shortest-distance state
- [ ] derive min-priority frontier
- [ ] explain relaxation
- [ ] handle stale PQ entries
- [ ] implement Network Delay Time
- [ ] explain complexity

### Union-Find composition
- [ ] derive Accounts Merge connectivity
- [ ] choose DSU identity
- [ ] combine email map + DSU
- [ ] reconstruct components
- [ ] explain sorting cost

### Monotonic stack
- [ ] derive from brute force
- [ ] identify unresolved candidates
- [ ] state monotonic invariant
- [ ] implement Daily Temperatures
- [ ] prove O(n)

### Collections
- [ ] HashMap
- [ ] TreeMap
- [ ] PriorityQueue
- [ ] ArrayList vs LinkedList
- [ ] ConcurrentHashMap recognized as Phase-3 topic

### JVM
- [ ] predict inherited static-field initialization case
- [ ] distinguish source syntax from declaring class
- [ ] record actual output
- [ ] keep module open

## Exit Criteria

Day 20 succeeds when DSA increasingly feels like:

```text
unknown problem
→ derive structure
→ solve
```

rather than:

```text
learn structure
→ solve a labeled problem
```

You should be able to distinguish:

```text
unweighted shortest path     → BFS
weighted non-negative path   → Dijkstra-style reasoning
dependency ordering          → topological sort
dynamic connectivity         → Union-Find
next-greater unresolved item → monotonic stack
```

## Intentionally Deferred

- Bellman-Ford
- Floyd-Warshall
- negative-weight shortest paths
- SCC
- MST
- advanced monotonic-stack problems
- DP
- backtracking
- ConcurrentHashMap internals
- custom class loaders
- GC

## Exact Starting Action

After retrieval, begin with:

> A signal starts at node K. Directed edges have different travel times. We need to know when every node receives the signal. Before choosing any algorithm, what information must we know for each node while exploring the graph?

Force:

```text
objective
→ state
→ safe next choice
→ data structure
```

## Registry Update Requirements

Capture demonstrated evidence only:

1. Day-19 retrieval quality
2. Network Delay abstraction
3. why BFS is insufficient
4. shortest-distance state
5. PQ derivation
6. relaxation reasoning
7. stale-entry handling
8. implementation bugs
9. Accounts Merge abstraction
10. DSU identity choice
11. HashMap + DSU composition
12. reconstruction quality
13. Daily Temperatures brute-force analysis
14. monotonic invariant
15. amortized O(n) explanation
16. Collections scenario reasoning
17. JVM inherited-static-field prediction
18. remaining Phase-2 blockers
19. whether Phase-2 exit can begin
20. exact Day-21 starting action

Recommended Day-21 direction if Day 20 is strong:

```text
mixed LeetCode interview set
+
Java Collections/JVM integration
+
Phase-2 exit test
+
begin Phase-3 concurrency bridge
```
