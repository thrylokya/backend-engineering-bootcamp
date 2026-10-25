# Current Position

- **Curriculum:** Day 20 — Dijkstra Transfer, Union-Find Composition, Monotonic Stack & Phase-2 Interview Integration
- **Status:** In Progress — paused intentionally; **do not mark Day 20 complete**
- **Current Section:** Session 3 — Accounts Merge (LeetCode 721)
- **Next Action:** Derive DSU union between account indices when a repeated email is encountered
- **Day 21:** Not started
- **Primary language:** Java

---

# Day 20 Checkpoint — 2026-10-26

Source: `cirriculum/day20.md`. These entries reflect demonstrated understanding, not just topics explained by the tutor. The learner asked to stop immediately after deriving transitive connectivity in Accounts Merge.

## Session Status

| Session | Status | Evidence / open work |
|---|---|---|
| 1. Day 19 retrieval | Completed with corrections | All seven retrieval questions answered; precision refinements recorded below |
| 2. Network Delay Time | In progress | Distance state, min-frontier, relaxation, stale entries understood; full Java solution and independent complexity proof pending |
| 3. Accounts Merge | In progress | Transitive email connectivity derived; DSU union composition, reconstruction, sorting, code pending |
| 4. Daily Temperatures | Not started | Brute force, monotonic stack, invariants, implementation, amortized proof pending |
| 5. Java Collections | Day 20 theory covered, mixed retrieval later | HashMap, TreeMap, PriorityQueue, ArrayList vs LinkedList, ConcurrentHashMap |
| 6. JVM spaced challenge | Predicted, not executed | Inherited static-field case and child-declared variation predicted correctly; runtime verification pending |
| 7. Phase 2 exit check | Not started | Do not claim Phase 2 exit while coding/transfer exercises are open |

## 1. Day 19 Retrieval — Evidence

- [x] Multi-source BFS: enqueue all starting points and process fixed-size BFS levels; initial nodes are t=0 and newly discovered nodes are processed at t=1.
- [x] Kahn: `indegree == 0` means no **remaining** prerequisites; initially needed terminology correction.
- [x] Kahn directed-cycle detection: `processedCount < V` means some nodes cannot reach indegree zero, proving a cycle.
- [x] BFS/DFS vs Union-Find: selected DSU for repeated incremental connectivity queries across many users.
- [x] Path compression and union-by-size: understood root flattening and attaching small components under large ones; clarify compression acts on visited path.
- [x] JVM class identity: binary name + **defining** class loader, even if bytecode identical.
- [x] Dependency cycle vs deadlock: distinguished structural dependency cycle from classical runtime lock deadlock; logical deadlock nuance discussed.

## 2. Network Delay Time (LeetCode 743)

**Demonstrated:**

- [x] Differentiated cumulative edge weight from number of hops; ordinary BFS cannot directly minimize unequal weights.
- [x] Maintained best-known `dist[node]`; understood that discovered distances can improve.
- [x] Reasoned toward selecting smallest total-distance frontier candidate using a min-heap, not only the next neighbor.
- [x] Worked through relaxation and independently wrote `dist[4] = Math.min(dist[4], weight + dist[2])` for 10 vs 3+2 → 5.
- [x] Explained stale heap entries: skip outdated `(node=2, distance=10)` if `dist[2]=4`.
- [x] Discussed non-negative-weight justification for finalizing the minimum tentative distance.

**Open:**

- [ ] Build adjacency list, initialize distances, and create min-heap in Java.
- [ ] Implement poll/relax/enqueue loop and stale-entry handling.
- [ ] Implement return value: maximum shortest distance, or -1 if unreachable.
- [ ] Validate edge cases and analyze time/space complexity independently.
- [ ] Demonstrate a full solution with fewer hints.

**Implementation bugs:** None observed — no complete implementation was submitted. Do not treat unimplemented code as failed or complete.

**Observed bridge gap:** Learner follows concrete questions and can write atomic Java operations but is not yet confident composing an entire unfamiliar problem. Use small blocks before requiring complete implementation.

## 3. Accounts Merge (LeetCode 721)

- [x] Derived that Account 1 sharing with Account 3 and Account 3 sharing with Account 2 makes all three a single connected component.
- [x] Identified shared-email transitivity as the relationship to preserve.
- [ ] Explicitly pick **account indices** as Union-Find elements and explain why.
- [ ] Use `email -> firstAccountIndex` map; if seen again, union current account index with first-seen owner index.
- [ ] Rebuild `root -> unique emails`, sort each list, prepend name.
- [ ] Explain pairwise-comparison cost, sorting cost, and complexity.
- [ ] Implement and test Java solution.

**Exact stop:** The teacher asked what DSU operation to perform when Account 3 sees `b@gmail.com` first seen in Account 1. Learner stopped before answering.

## 4. Daily Temperatures (LeetCode 739)

- [ ] Start with O(n²) brute force.
- [ ] Identify unresolved indices and monotonic stack invariant.
- [ ] Implement solution and demonstrate why popping is safe.
- [ ] Explain amortized O(n): each index pushed once, popped at most once.

No evidence collected yet; do not mark covered.

## 5. Java Collections — Scenario Evidence

**HashMap and HashSet**

- [x] Hash/bucket collision vs `equals()` key equivalence, contract between `hashCode()` and `equals()`.
- [x] Load factor and resize: at capacity 32, threshold 24, 25th insertion triggers resize.
- [x] Collision-chain resize vs treeification: minimum capacity 64 and chain threshold explained.
- [x] Resizing 16→32: hashes 5 and 21 split buckets; identical hashes stay together (needed correction).
- [x] Mutable hash-relevant key: map retains old entry but cannot necessarily retrieve it, and reinsertion may produce two entries with the same object reference.
- [x] HashSet internally leverages a HashMap; duplicate equal elements are not added. Needed correction: existing `e1` stays rather than replacement by `e2`.
- [ ] Brief future drill for collision tree caveats and mutable-key retrieval, without prolonged repetition.

**TreeMap**

- [x] Red-Black Tree with O(log n) worst-case search/insert/removal.
- [x] BST navigation, `floorKey`, `ceilingKey`, `lowerKey`, `higherKey` after initial confusion.
- [x] Comparator negative / zero / positive outcomes; compare==0 means equivalent key to TreeMap.
- [x] Two-level string Comparator: primary length, secondary lexical order, including reverse ordering.
- [ ] Spaced retrieval: initially thought iteration preserves insertion order and same-length unequal strings can coexist under a length-only Comparator; both corrected in discussion.

**PriorityQueue**

- [x] Heap array layout; parent/child index formulas; min-heap `poll` sequence.
- [x] Sift-up on insertion and last-element move + sift-down on removal.
- [ ] Bottom-up heapify O(n) reasoning not fully internalized; learner explicitly requested to defer it. Do not mark it complete.

**ArrayList vs LinkedList**

- [x] Chose ArrayList for frequent indexed access and iteration; explained locality and address calculation.
- [x] Clarified adjacent `Object[]` references vs potentially scattered objects.

**ConcurrentHashMap**

- [x] Explained shared-state hazards for concurrent HashMap updates, even with different keys.
- [x] Recognized get-then-put increment is non-atomic even on ConcurrentHashMap.
- [x] Chose `merge("requests", 1, Integer::sum)` for an atomic per-key update.
- [ ] ConcurrentHashMap internals deliberately deferred to Phase 3.

Day 20 Collections theory was covered; do not equate this with complete mastery of all collection internals.

## 6. JVM Inherited-Static-Field Challenge

- [x] Correctly predicted `Child.value` inherited from Parent: output `Parent initialized` then `10`; Child not initialized.
- [x] Correctly predicted Child-owned static field variant: `Parent initialized`, `Child initialized`, `20`.
- [x] Articulated superclass-before-subclass initialization.
- [ ] Run both examples to record prediction vs actual output.
- [ ] Keep broader JVM initialization corner-case module open for spaced challenges.

## 7. Phase-2 Exit Blockers

- [ ] Network Delay complete implementation, tests, complexity.
- [ ] Accounts Merge DSU composition, reconstruction, sorting, complexity, code.
- [ ] Daily Temperatures monotonic stack derivation, code, proof.
- [ ] JVM runtime verification.
- [ ] Phase-2 exit retrieval/mixed transfer check.

**Phase-2 exit decision:** Not ready. Do not advance as if Day 20 were complete.

## Coaching Contract — Going Forward

- Cover the **entire** curriculum, but teach faster and stay within approximate module timeboxes.
- Core explanation + key Java trade-off + 1–2 high-signal checks per concept; no extended loops of near-identical questions.
- Track gaps for spaced retrieval rather than extending a 25-minute module into hours.
- Distinguish **explained**, **reasoned**, **implemented**, and **tested**.
- Scaffold algorithm coding from small operations → partial loop → full problem, then reduce hints progressively.
- Do not abruptly mark sessions complete or drop planned scope to save time.

## Next Action — Exact Resume Point

**Resume Day 20 Session 3: Accounts Merge**, not Day 21.

Ask first:

> Account 3 contains `b@gmail.com`, and `emailToAccount` records that Account 1 contained it earlier. What Union-Find operation should we perform on the two account indices, and why?

Then proceed in order:

1. DSU over account indices; email map; union on previously seen email; reconstruct and sort grouped emails.
2. Daily Temperatures brute force → unresolved indices → monotonic stack → code → O(n) proof.
3. Return to Network Delay Time for incremental Java implementation and complexity.
4. JVM runtime verification and Phase-2 exit check.

**Day 21 recommended direction only after Day 20 exit:** mixed LeetCode interview set, JVM/Collections integration, Phase-3 concurrency bridge.
