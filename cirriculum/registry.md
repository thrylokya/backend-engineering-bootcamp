# Backend Engineering Bootcamp Registry — Day 17 Update

# Current Position

- **Curriculum:** Day 17 — Heap Transfer, Sliding Window LeetCode, Trie Foundations & JVM Execution
- **Status:** Completed
- **Phase:** Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution
- **Primary Language:** Java
- **Next Direction:** Day 18 — mixed algorithm transfer + Graph foundations + JVM continuation

---

# Day 17 — Evidence & Progress

## 1. Heap Retrieval

### Heap invariant
- [x] Understood that a heap is not globally sorted.
- [x] Understood that a min-heap exposes the minimum retained candidate at the root.
- [x] Used heap as a bounded candidate-set structure rather than only as a `peek()/poll()` API.

### Comparable vs Comparator
- [x] Demonstrated understanding that `PriorityQueue` uses natural ordering by default.
- [x] Understood that a custom `Comparator` changes the definition of priority.
- [x] Used a frequency-based comparator for Top K Frequent Elements.
- [ ] Direct `Comparable` vs `Comparator` retrieval was not explicitly tested today.

### Modulo / `floorMod`
- [ ] Not retrieved during this session.
- [ ] Revisit briefly in next retrieval block.

---

# 2. Kth Largest Element in a Stream — LeetCode 703

## Abstraction

Initial reasoning correctly identified:

```text
Only the largest k values need to remain in memory.
```

Then derived:

```text
Among the retained top-k values,
the kth largest is the smallest.
```

Therefore:

```text
min-heap of size k
```

## Invariant

```text
After processing each value,
the heap contains the largest k values seen so far,
or all values if fewer than k have been seen.
```

## Safe elimination

Correctly understood:

```text
When there are k + 1 candidates,
the smallest candidate cannot belong to the largest k.
```

Therefore removing the heap root is safe.

## Implementation evidence

Initial implementation used:

```text
array of size k
+
PriorityQueue
+
heap reconstruction
```

Issues discovered:

- array was not actually maintained in sorted order;
- default zero values could pollute the heap;
- heap was unnecessarily rebuilt;
- duplicated state existed between array and heap.

Improved abstraction:

```text
The heap itself is sufficient state.
```

Canonical operation:

```java
heap.offer(value);

if (heap.size() > k) {
    heap.poll();
}
```

## Complexity

```text
per update → O(log k)
peek       → O(1)
space      → O(k)
```

### Status

- [x] top-k state derived
- [x] min-heap reasoning
- [x] invariant understood
- [x] safe-elimination reasoning
- [x] complexity understood
- [x] redundant array state identified
- [ ] Full LeetCode stream class was not independently reimplemented after correction

---

# 3. Top K Frequent Elements — LeetCode 347

## Abstraction

Correctly independently identified composition:

```text
frequency map
+
heap
```

Frequency state:

```text
value → number of occurrences
```

Initial solution:

```text
max-heap containing all distinct elements
→ poll k times
```

This was logically correct but used:

```text
O(m)
```

heap space where `m` is the number of distinct values.

Then transferred bounded-heap reasoning from Kth Largest:

```text
min-heap of size k
ordered by frequency
```

## Invariant

```text
After processing each distinct value,
the heap contains the k highest-frequency candidates seen so far,
or all candidates if fewer than k have been processed.
```

## Comparator understanding

Correctly changed from:

```java
Integer.compare(b.frequency, a.frequency)
```

for max-frequency-first ordering

to:

```java
Integer.compare(a.frequency, b.frequency)
```

for bounded min-heap elimination.

Also identified and understood that:

```java
priorityQueue.comparator().reversed();
```

does not mutate an existing `PriorityQueue`.

## Tie reasoning

Asked an important edge case:

```text
k = 1
all elements have the same frequency
```

Understood that equal-frequency candidates are tied and that deterministic behavior requires an explicit secondary comparator if the specification demands one.

## Complexity

Let:

```text
n = total elements
m = distinct elements
```

Then:

```text
frequency map    → O(n)
bounded heap     → O(m log k)
total            → O(n + m log k)
space            → O(m + k)
```

### Status

- [x] frequency-map abstraction
- [x] HashMap + heap composition
- [x] max-heap baseline
- [x] bounded min-heap optimization
- [x] comparator reasoning
- [x] tie behavior considered
- [x] implementation completed

---

# 4. Sliding Window — Permutation in String

## Derivation

Correctly identified:

```text
window size = s1.length()
```

because any permutation of `s1` must contain exactly the same number of characters.

State identified:

```text
target character frequencies
current-window character frequencies
```

Incremental update identified:

```text
one outgoing character is removed
one incoming character is added
```

## Invariant

```text
At every comparison point,
the window-frequency state represents exactly
s1.length() consecutive characters from s2.
```

## Initial implementation issues

The first implementation exposed several useful precision gaps:

1. Used:

```java
map.size()
```

as window length.

But:

```text
Map.size() = number of distinct characters
```

not total characters.

2. Initially failed to compare the final possible window.

3. Later version could call:

```java
s2.charAt(s2.length())
```

after checking the final window.

4. Used:

```java
Integer != Integer
```

instead of value equality.

These were identified and corrected.

## Current understanding

- [x] fixed-size window derived
- [x] state identified
- [x] outgoing/incoming incremental update understood
- [x] final-window boundary issue understood
- [x] exact invariant understood
- [x] Java boxed-equality issue identified

---

# 5. Minimum Window Substring — LeetCode 76 Exposure

Hard decomposition was attempted beyond the minimum Day-17 requirement.

## Variable-window reasoning

Correctly independently reasoned:

```text
if window is invalid:
    expand right

if window is valid:
    shrink left
```

Useful mental model:

```text
right → tries to achieve validity
left  → tries to achieve minimality
```

## Important distinction learned

For:

```text
target = "ABC"
```

validity does NOT mean:

```text
window frequencies == target frequencies
```

Instead:

```text
window[A] >= required[A]
window[B] >= required[B]
window[C] >= required[C]
```

Extra characters and extra copies are allowed.

## Implementation

Implemented a working map-scanning version using:

```text
target frequency map
window frequency map
left/right pointers
minimum valid window tracking
```

## Complexity analysis

Initial implementation:

```text
O(n × d)
```

where:

```text
d = number of distinct characters in target
```

because validity was recomputed by scanning target frequencies.

Worst-case description:

```text
O(n × m)
```

where `m = target.length()`.

## Optimization learned

Instead of rescanning target state, maintain:

```text
required = number of distinct target requirements
formed   = number currently satisfied
```

Then validity becomes:

```java
formed == required
```

in O(1).

Optimized overall complexity:

```text
O(n + m)
```

### Status

- [x] variable-size window derived
- [x] expand/shrink condition understood
- [x] coverage vs exact-equality distinction
- [x] working initial implementation
- [x] initial complexity derived
- [x] `formed/required` optimization understood
- [ ] Optimized `formed/required` implementation not independently rewritten yet

---

# 6. Trie Foundations

## Mental model

Understood:

```text
each word = path through the Trie
common prefixes = shared paths
```

Example:

```text
car
care
```

share:

```text
c → a → r
```

## Node state

Understood need for:

```text
children
isWord
```

`isWord` distinguishes:

```text
prefix exists
```

from:

```text
complete inserted word exists
```

Example:

```text
only "care" inserted

search("car")      → false
startsWith("car")  → true
```

---

# 7. Trie `insert`

Implemented Trie insertion iteratively.

Several useful bugs were found during development:

### Bug 1

After creating a node, traversal initially did not descend into the newly created node.

### Bug 2

`isWord` was initially set only when a node was created.

This failed for:

```text
insert("care")
insert("car")
```

because the `r` node already existed when `"car"` was inserted.

Final implementation correctly tracks the current node whether existing or newly created and marks:

```java
node.setWord(true);
```

after full traversal.

## Insert invariant

```text
After processing character i,
the current node represents word[0..i].
```

---

# 8. Trie `search`

Implemented correctly.

Logic:

```text
traverse every character

if any path is missing:
    false

after full traversal:
    return finalNode.isWord
```

Correct distinction:

```text
path existence alone is insufficient for exact search.
```

---

# 9. Trie `startsWith`

Initially assumed that the final prefix node must have children.

Corrected understanding:

```text
startsWith(prefix)
```

only asks:

```text
Does the complete prefix path exist?
```

Therefore:

```text
insert("apple")

startsWith("apple") → true
```

even if the final `e` node has no children.

Correct final behavior:

```text
search()
→ path exists + final isWord

startsWith()
→ path exists
```

Also correctly reasoned that BFS/DFS would be needed for a different requirement:

```text
getAllWordsStartingWith(prefix)
```

but not for boolean `startsWith()`.

---

# 10. Trie Representation Trade-off

Reviewed two child representations.

## `HashMap<Character, Node>`

Advantages:

```text
sparse allocation
flexible character domain
```

Costs:

```text
hashing overhead
extra objects / memory overhead
```

## `Node[26]`

Useful for:

```text
lowercase English alphabet only
```

Advantages:

```text
direct indexing
low lookup overhead
```

Cost:

```text
unused slots when branching is sparse
```

## Complexity

For key length `L`:

```text
insert     → O(L)
search     → O(L)
startsWith → O(L)
```

assuming constant-average child lookup.

### Status

- [x] Trie mental model
- [x] children + `isWord`
- [x] insert implemented
- [x] search implemented
- [x] startsWith implemented
- [x] prefix vs exact lookup understood
- [x] BFS/DFS role for autocomplete clarified
- [x] array-vs-map trade-off reviewed

---

# 11. JVM — Source to Execution

## Correct execution chain

Retrieved:

```text
.java source
↓
javac
↓
.class bytecode
```

Initially assumed JVM bytecode was directly understood by the processor.

Corrected mental model:

```text
.java
↓
javac
↓
.class bytecode
↓
class loading
↓
JVM execution
↓
interpreter / lower-tier execution
↓
runtime profiling
↓
hot-code detection
↓
JIT compilation
↓
native machine code
↓
CPU
```

Important distinction retained:

```text
CPU understands native machine instructions,
not JVM bytecode.
```

## Portability

Understood that `.class` bytecode is platform-independent while the JVM implementation is platform-specific.

---

# 12. Interpreter vs JIT

Learned why JVM does not eagerly compile all bytecode to optimized native code.

Reasons:

```text
JIT compilation itself costs CPU/time
many methods may never execute
runtime profiling can identify actually hot paths
```

Mental model:

```text
execute
→ profile
→ identify hot code
→ compile optimized native code
```

---

# 13. JVM Warmup / JMH

Understood that:

```java
System.nanoTime()
```

around one execution is not sufficient evidence of steady-state performance.

First-call latency may include:

```text
class loading
static initialization
lazy initialization
cache coldness
JIT activity
I/O or connection setup
```

Therefore:

```text
first call slower
```

does not automatically prove:

```text
JIT caused the difference.
```

JMH connection reviewed:

```text
warmup iterations
measurement iterations
better microbenchmark isolation
```

### Status

- [x] source → bytecode understood
- [x] bytecode vs machine-code distinction corrected
- [x] interpreter/JIT purpose understood
- [x] hot-code concept understood
- [x] warmup significance
- [x] JMH purpose understood

---

# 14. Production Drill — Ordering vs Capacity

Scenario:

```text
incoming rate = 20,000 tasks/sec
worker capacity = 5,000 tasks/sec
```

Correctly answered that a better heap / `PriorityQueue` does not solve the system problem.

Backlog growth:

```text
20,000 - 5,000
= 15,000 tasks/sec
```

Key distinction:

```text
priority → determines ordering

capacity/backpressure
→ determines whether the system can keep up
```

Relevant production controls reviewed:

```text
bounded queues
backpressure
load shedding
capacity scaling
```

### Retrieval status

- [x] correctly rejected heap as the solution
- [ ] Full backpressure explanation should be retrieved once without prompting

---

# Day 17 Observed Strengths

1. Increasing ability to derive structures rather than immediately pattern-match.
2. Strong transfer from Kth Largest to Top K Frequent.
3. Good instinct for challenging edge cases, especially ties.
4. Variable sliding-window reasoning was derived with limited prompting.
5. Trie mental model was absorbed quickly after implementation feedback.
6. Comfortable composing structures such as:

```text
HashMap + Heap
```

7. Increasing awareness of invariants and incremental state maintenance.

---

# Day 17 Growth Areas

## 1. Implementation precision

Conceptual approach is frequently correct before implementation.

Current recurring bugs involve:

```text
loop boundaries
last-window handling
state meaning
boxed equality
tracking current object/node
```

Required pre-code checklist:

```text
1. What exactly is my state?
2. What invariant must remain true?
3. What changes in one iteration?
4. What boundary ends the loop?
5. What input breaks this code?
```

## 2. Avoid duplicate state

Example from Kth Largest:

```text
array + heap
```

was unnecessary.

Prefer the smallest state that fully represents the invariant.

## 3. Separate path existence from semantic completion

Trie lesson:

```text
path exists
≠
word exists
```

`isWord` carries semantic completion.

## 4. Maintain derived state incrementally

Minimum Window optimization:

```text
recompute validity every time
```

can often become:

```text
update one counter when state changes
```

This is an important transferable optimization pattern.

---

# Day 17 Deferred / Needs Retrieval

- [ ] Comparable vs Comparator direct retrieval
- [ ] Java `%` negative-value behavior
- [ ] `Math.floorMod`
- [ ] optimized Minimum Window implementation
- [ ] backpressure explanation without prompting

These should be brief retrieval items, not another full Day-17 session.

---

# Day 17 Exit Assessment

## Heap Transfer

**Passed.**

Current mental model:

```text
heap =
maintain an extreme over a changing candidate set
```

not merely:

```text
peek / poll
```

## Sliding Window

**Passed conceptually; implementation precision still needs repetition.**

Can derive:

```text
window size
state
incremental update
validity condition
```

Fixed and variable windows both understood.

## Trie

**Passed.**

Can explain:

```text
exact membership
vs
prefix existence
```

and implement the three fundamental operations.

## JVM

**Passed at Day-17 depth.**

Current model:

```text
source
→ bytecode
→ JVM execution/profile
→ hot code
→ JIT
→ native machine code
```

---

# Exact Day 18 Starting Action

Before new material, do a **10-minute retrieval only**:

```text
1. State Kth Largest heap invariant.
2. Explain Comparable vs Comparator.
3. Explain -1 % 5 in Java and Math.floorMod.
4. State fixed-window invariant for Permutation in String.
5. Explain search() vs startsWith() in Trie.
6. Explain interpreter → profile → JIT.
7. Explain why PriorityQueue does not solve backpressure.
```

Then begin Day 18 with:

```text
Mixed / unknown-pattern algorithm transfer
+
Graph representation
+
BFS / DFS foundations
+
JVM continuation
```

First graph question:

> Given a set of services and directed dependencies between them, how would you represent the graph in memory, and what information must each representation preserve?
