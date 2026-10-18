# Backend Engineering Bootcamp Registry

**Last updated:** 2026-10-18  
**Primary language:** Java  
**Target:** Senior / Lead / Staff backend engineering interviews  
**Phase:** Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution  
**Current curriculum:** Day 18 — Graph Foundations, BFS/DFS & JVM Class Initialization  
**Current status:** IN PROGRESS  
**Last completed day:** Day 17  
**Next direction:** Close Day 18 gaps, then start Day 19

> This registry records **demonstrated understanding**, not just topics discussed. A concept remains open when implementation, edge-case reasoning, or independent retrieval is incomplete.
>
> **Explicit continuity rule (2026-10-18): Do not mark the JVM class-initialization unit complete.** Revisit it in small, spaced challenges until the edge cases are independently explained.

---

## 1. Current Position at a Glance

| Workstream | Status | Evidence / blocker |
|---|---|---|
| Day 17 — Heaps, sliding windows, Trie, JVM execution | Completed at Day-17 scope; targeted retrieval outstanding | Day-17 registry has detailed evidence |
| Day 18 — Search in Rotated Sorted Array (LC 33) | Reviewed; verification pending | Sorted-half/safe-elimination reasoning discussed; independent final test suite not confirmed |
| Day 18 — Graph representations | Foundations covered | Directed edges, adjacency list/matrix, degrees, sparse-vs-dense trade-offs |
| Day 18 — BFS / DFS | Foundations covered; practice pending | Queue vs stack/recursion, visited state, shortest unweighted distance, O(V+E) |
| Day 18 — Number of Islands (LC 200) | IN PROGRESS | Component/DFS idea understood; fix guards/bounds and run tests |
| Day 18 — JVM loading, linking, initialization | IN PROGRESS | Correct parent/child output trace; nuanced trigger and edge cases not yet mastered |
| Day 19 | NOT STARTED | Begin after bounded Day-18 close-out; carry JVM revision forward |

**Important:** Completion of one JVM output-tracing exercise does **not** close the broader JVM topic.

---

## 2. Day 18 — Evidence and Progress

### 2.1 Precision retrieval (from Day 17)

- [ ] Independently recheck Kth Largest stream invariant.
- [ ] Directly explain `Comparable` vs `Comparator`.
- [ ] Explain Java `-1 % 5` and `Math.floorMod(-1, 5)`.
- [ ] State fixed-window invariant for Permutation in String.
- [ ] Explain Trie `search()` vs `startsWith()`.
- [ ] Retrieve the source → bytecode → interpreter/profile → JIT chain.
- [ ] Explain why `PriorityQueue` fixes ordering, **not** capacity/backpressure.

**Status:** Retrieval checklist is not fully verified in the available session record. Do not silently mark these items complete.

### 2.2 Mixed algorithm transfer — Search in Rotated Sorted Array (LeetCode 33)

Key model:

```text
The array is not globally sorted.
At least one half around the midpoint remains sorted
(under the distinct-values assumption).
```

Reasoning protocol:

```text
1. Identify which half is sorted.
2. Check whether the target lies inside that half's value range.
3. Discard only a half proved not to contain the target.
4. Preserve inclusive/exclusive boundaries correctly.
```

- [x] Topic and sorted-half/safe-elimination reasoning discussed.
- [ ] Independently verify final Java implementation.
- [ ] Test singleton, missing target, two elements, pivot endpoints and ordinary rotations.
- [ ] Give a complete correctness and O(log n) time / O(1) space explanation without hints.

**Status:** Reviewed; do not claim implementation mastery until independent verification.

### 2.3 Graph representations

Covered:

- [x] Vertex/node and directed edge.
- [x] Adjacency list as `Map<Node, List<Node>>` / `List<List<Integer>>`.
- [x] Adjacency matrix as a `V × V` structure.
- [x] List storage: O(V + E).
- [x] Matrix storage: O(V²).
- [x] Matrix edge lookup: O(1).
- [x] Matrix neighbor scan: O(V).
- [x] Adjacency list neighbor scan: O(out-degree).
- [x] In-degree vs out-degree.
- [x] Why sparse, large service-dependency graphs favor adjacency lists.
- [x] Recognized a directed cycle, e.g. `A → C → D → A`.

**Precision correction to retain:**

```text
A directed cycle is not automatically a deadlock.
Deadlock requires additional resource/wait conditions.
A dependency cycle may be invalid or dangerous, but is not
sufficient evidence of a runtime deadlock.
```

- [ ] Independently explain graph cycle vs deadlock in one production example.
- [ ] Revisit disconnected vertices and traversal across components.

### 2.4 BFS foundations

- [x] FIFO queue defines breadth-first exploration.
- [x] `visited` is required when graph nodes may be reachable by multiple paths/cycles.
- [x] Mark visited **when enqueuing** to prevent duplicate entries.
- [x] BFS finds minimum edge-count distance in an unweighted graph.
- [x] With adjacency lists, traversal is O(V + E) time; O(V) auxiliary space.
- [ ] Independently code BFS from a blank editor.
- [ ] Test a diamond graph where two paths reach the same vertex.
- [ ] Verify disconnected-graph traversal by starting from each unvisited vertex.

Queue invariant:

```text
Every queued vertex has already been discovered/marked,
but has not yet been fully processed.
```

### 2.5 DFS foundations

- [x] Depth-first exploration via recursion or an explicit stack.
- [x] Backtracking after exploring a path.
- [x] Need to mark visited to handle cycles.
- [x] With adjacency lists, O(V + E) traversal time and O(V) auxiliary space.
- [ ] Independently implement recursive DFS and iterative DFS.
- [ ] Test directed cycle and disconnected graph.
- [ ] Explain why DFS does not guarantee shortest unweighted distance.

Model:

```text
DFS = go deep, then backtrack.
BFS = explore in increasing distance from the source.
```

### 2.6 Graph LeetCode — Number of Islands (LeetCode 200)

**Abstraction understood:**

```text
One island = one connected component of land cells
using four-directional adjacency.
```

Plan:

```text
For every grid cell:
  if it is unvisited land:
    islands++
    traverse all connected land cells
    mark them visited / mutate them to water
```

- [x] Connected-component abstraction understood.
- [x] Outer grid scan + DFS approach discussed.
- [x] O(R × C) time goal understood.
- [ ] Fix empty/null grid guard as appropriate to the method contract.
- [ ] Fix row/column bounds (`>=` upper-bound checks, not just `>`).
- [ ] Verify visited or in-place mutation is applied exactly once.
- [ ] Compile and run corrected Java code.
- [ ] Test empty grid, one land cell, one water cell, separate islands, full-land grid and thin rows/columns.
- [ ] State worst-case auxiliary space, including the DFS recursion stack: O(R × C).

**Status:** **IN PROGRESS.** A correct idea is not the same as a tested implementation.

---

## 3. JVM — Class Loading, Initialization & Inheritance

**Status:** **IN PROGRESS — explicitly not completed.**

### 3.1 Understanding demonstrated

- [x] `.java` source is compiled by `javac` to `.class` bytecode.
- [x] Bytecode is not CPU-native machine code.
- [x] Loading, linking and initialization are distinct lifecycle concerns.
- [x] Linking includes verification, preparation and resolution.
- [x] Static variables get preparation/default-state treatment before user-written initialization.
- [x] Static field initializers and static blocks execute in textual order during class initialization.
- [x] A superclass is initialized before a subclass that requires initialization.
- [x] Class initialization normally occurs once per class per defining class loader.
- [x] Object constructors execute for each object construction.
- [x] Correctly reasoned through a combined parent/child static-initialization and constructor output exercise.

Example of the correctly analyzed trace:

```text
Start
Parent static: 15
Child static: 40
Parent constructor
Child constructor
Parent constructor
Child constructor
End
```

What the exercise established:

```text
Static initialization occurs once for the relevant initialized classes.
Instance construction occurs each time an object is created.
Superclass construction precedes subclass construction.
```

**Assessment:** Progress on one multi-part scenario is evidence of improvement, not broad coverage of JVM corner cases.

### 3.2 JVM edge-case coverage matrix — still open

#### A. Initialization triggers

- [ ] `new`, `getstatic`, `putstatic`, `invokestatic`.
- [ ] Access through a subclass name to a field declared in its parent.
- [ ] Compile-time constant (`static final`) vs runtime-computed `static final`.
- [ ] `SomeClass.class` vs `Class.forName(...)` vs `ClassLoader.loadClass(...)`.
- [ ] Creating an array of a class type without initializing its component class.
- [ ] Static methods, instance methods and inherited static members.

#### B. Initialization ordering and Java syntax

- [ ] Multiple static fields and static blocks in declaration order.
- [ ] Forward references and illegal forward references.
- [ ] Default-value observations during initialization.
- [ ] Circular/cross-class initialization and partially initialized state.
- [ ] Static hiding vs instance overriding; static method resolution.
- [ ] Interfaces, constant access, and initialization of implementing classes.
- [ ] Default methods and interface initialization nuances.

#### C. Object-construction ordering

- [ ] Instance field initializers and instance initializer blocks.
- [ ] `super()` vs `this()` constructor chaining.
- [ ] Parent and child constructor order, including inherited state.
- [ ] Dynamic dispatch from constructors and partially initialized subclass fields.

#### D. Failures, concurrency and class loaders

- [ ] Exception during `<clinit>` and `ExceptionInInitializerError`.
- [ ] Subsequent initialization attempts and `NoClassDefFoundError`.
- [ ] Two threads racing to initialize the same class.
- [ ] Distinguish loading, linking and initialization triggers.
- [ ] Parent-delegation class-loader model.
- [ ] Same class name loaded by different defining class loaders.
- [ ] Metaspace vs heap at interview depth.

**Rule:** Do not convert any checkbox above to `[x]` merely because the answer has been explained. Require an independent output prediction, explanation, and/or executable test.

### 3.3 JVM spaced-retrieval protocol

At the beginning of selected future sessions:

```text
1. Show one small Java code snippet (without its output).
2. User predicts exact output or compile-time failure.
3. User explains which classes initialize and why.
4. Test the snippet in Java when useful.
5. Record error category and corrected rule.
```

Rotate cases across sessions rather than treating the entire matrix as one long lecture.

Suggested tracking fields:

```text
Date | Scenario | Initial prediction | Correct? | Misconception | Retry date
```

**Exit bar for this JVM module:**

- [ ] Predict representative superclass/subclass scenarios correctly.
- [ ] Explain active use and non-initialization cases.
- [ ] Identify default vs explicit static initialization.
- [ ] Explain interface and constant nuances.
- [ ] Handle instance initializer and constructor interactions.
- [ ] Recognize initialization failure and concurrency behavior.
- [ ] Pass a mixed, unprompted JVM-output quiz on separate days.

No fixed date is assigned for closing this module.

---

## 4. Strengths Observed in Day 18

1. Can reason about data-structure memory scaling rather than just memorize complexity.
2. Understands why `visited` is essential once graphs can contain cycles.
3. Grasps BFS/DFS in terms of the exploration objective, not only queue vs stack.
4. Recognizes connected components as an abstraction for grid problems.
5. Improved in JVM superclass/subclass static and constructor ordering after dry runs.
6. Pushes to retain unmastered topics instead of accepting a premature completion label.

## 5. Priority Growth Areas

### Algorithm implementation precision

Correct conceptual reasoning is frequently ahead of exact Java code. Focus on:

```text
empty input
upper/lower bounds
last possible index/window
correct state mutation
recursion termination
duplicate visits
explicit edge-case tests
```

### Graph correctness explanations

Be able to articulate:

```text
representation
+ exploration frontier
+ visited state
+ correctness invariant
+ O(V + E) cost accounting
```

Do not equate a graph cycle with deadlock.

### JVM conceptual boundaries

Keep distinct:

```text
loading ≠ linking ≠ initialization ≠ object construction
```

and:

```text
declaring/inheriting a static member
≠ always initializing the class whose name appears in source
```

Practice compile-time behavior as well as runtime output.

---

## 6. Pending Deliverables

### Day 17 carry-over

- [ ] Direct retrieval: `Comparable` vs `Comparator`.
- [ ] Negative modulo vs `Math.floorMod`.
- [ ] Independently rewrite optimized `formed/required` Minimum Window Substring.
- [ ] Explain backpressure without prompts.
- [ ] Independently rewrite full Kth Largest stream class after correction.

### Day 18 closure

- [ ] Test Search in Rotated Sorted Array with a complete edge-case suite.
- [ ] Produce verified Number of Islands solution and test output.
- [ ] Independently write BFS and DFS.
- [ ] Demonstrate whole-graph traversal for disconnected components.
- [ ] Explain cycle vs deadlock.
- [ ] Continue JVM class-initialization corner-case practice.

### Phase 2 next work

- [ ] Rotting Oranges — multi-source BFS.
- [ ] Directed-cycle detection.
- [ ] Topological sort / Kahn's algorithm.
- [ ] Course Schedule.
- [ ] Union-Find fundamentals.
- [ ] JVM class-loader delegation, metaspace and further retrieval.

---

## 7. Exact Next Action

**Do not restart Day 18 from the beginning.** Resume the smallest unfinished, high-value pieces.

1. **Number of Islands:** correct guard/boundary conditions, compile, and pass edge-case tests.
2. **BFS/DFS retrieval:** independently trace disconnected and cyclic graphs; then implement from scratch.
3. **JVM micro-drill:** run one unprompted class-initialization corner case; record the outcome without closing the topic.
4. **Day 17 retrieval:** revisit `Comparable`/`Comparator`, `floorMod`, heap invariant and backpressure briefly.
5. **Begin Day 19:** Rotting Oranges → topological ordering/Course Schedule → Union-Find foundations. Introduce JVM class loaders/metaspace as a separate thread.

**Working principle:** Continue new material while regularly revisiting JVM; do not let one unfinished JVM module block all graph progress.

---

## 8. Completion Rules

Use exactly these meanings:

- **COMPLETED:** Independently explained and, where coding is required, implemented/tested.
- **FOUNDATIONS COVERED:** Concept explained; independent practice still due.
- **IN PROGRESS:** Corrective work or significant corner-case coverage remains.
- **NOT STARTED:** Not yet meaningfully taught/tested.
- **NEEDS RETRIEVAL:** Previously covered, but not independently retested.

**Day 18 remains IN PROGRESS.**  
**JVM class initialization remains IN PROGRESS.**  
**Day 19 remains NOT STARTED.**

---

## 9. Previous Day Baseline — Day 17

The prior `cirriculum/registry.md` contains detailed Day-17 implementation observations. The essential exit status is retained here:

| Topic | Day-17 result | Remaining |
|---|---|---|
| Kth Largest in Stream (LC 703) | Bounded min-heap invariant understood | Full independent stream-class rewrite |
| Top K Frequent Elements (LC 347) | HashMap + bounded heap composition implemented | Retrieval on comparator details |
| Permutation in String | Fixed window, outgoing/incoming updates understood | Implementation boundary repetition |
| Minimum Window Substring (LC 76) | Working map-scanning version; optimization understood | Independently implement `formed/required` |
| Trie | `insert`, `search`, `startsWith` implemented | Occasional retrieval |
| JVM source/JIT | Bytecode, interpreter, profiling/JIT and warmup understood | Extend into class initialization |
| Production drill | Priority ordering vs backpressure distinguished | Unprompted backpressure explanation |

**Source of truth:** `cirriculum/MASTER_CURRICULUM.md`, `cirriculum/day18.md`, and the Day-17 registry previously committed in GitHub.

**Registry maintenance rule:** Update only after observed evidence. Preserve open tasks and original misconceptions so they can be retested.
