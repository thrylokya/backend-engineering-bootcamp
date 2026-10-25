# Day 21 — Phase 2 Java/JVM Depth + HLD/LLD Foundations

**Duration:** ~3 hours  
**Phase:** Phase 2 — Trees, Heaps, Graphs, Collections & JVM Execution  
**DSA:** Explicitly excluded from Day 21; open Day-20 DSA items remain open for the separate DSA session.  
**Primary language:** Java

## Why this is Day 21

`registry.md` shows strong recent work on:

- graph foundations, BFS/DFS, topological sort, Union-Find concepts
- weighted shortest-path reasoning in Network Delay Time
- Java collections: HashMap/HashSet, TreeMap, PriorityQueue, ArrayList vs LinkedList
- ConcurrentHashMap surface-level atomic update reasoning
- JVM class loading / class identity / static initialization

But the current registry does **not** yet provide completion evidence for several non-DSA Phase-2 topics required by `MASTER_CURRICULUM.md`:

- generics
- type erasure
- reflection basics
- deeper JIT execution: hot methods, method inlining, escape analysis, code cache
- B-tree / B+ tree database connection
- continuous HLD/LLD practice

Therefore Day 21 stays inside Phase 2 and covers those gaps.

---

# Schedule

```text
1. Java/JVM retrieval                         15 min
2. Generics + Type Erasure                   35 min
3. Reflection Basics                         25 min
4. JVM Execution: JIT Deep Dive              40 min
5. B-Tree / B+ Tree Engineering Connection   25 min
6. HLD Foundations — Level 1                 20 min
7. LLD Foundations — Responsibility Design   20 min
                                              -------
                                              180 min
```

No LeetCode or algorithm-pattern work belongs in this session.

---

# 1. Java/JVM Retrieval — 15 min

No new DSA questions.

Answer without notes:

1. `HashMap` — what is the relationship between `hashCode()` and `equals()`?
2. Why can a mutable key become practically unreachable inside a `HashMap`?
3. `TreeMap` — what does `Comparator.compare(a, b) == 0` mean for key identity?
4. Why is `ArrayList` often better than `LinkedList` for iteration?
5. What determines Java class identity?
6. Loading vs linking vs initialization — distinguish them.
7. Why can `Child.parentField` initialize only the declaring parent class?

Correct weak answers briefly and move on.

The purpose is retention, not reopening Day 20.

---

# 2. Generics + Type Erasure — 35 min

This is explicitly part of Phase 2 in the master curriculum.

## Part A — Why Generics Exist

Start with the problem:

```java
List values = new ArrayList();
values.add("hello");
values.add(42);
```

What failure has been deferred until runtime?

Then move to:

```java
List<String> values = new ArrayList<>();
```

Derive:

```text
compile-time type safety
+
fewer explicit casts
+
reusable abstractions
```

## Part B — Generic Classes and Methods

Understand and implement a tiny generic container:

```java
class Box<T> {
    private T value;

    void set(T value) { ... }
    T get() { ... }
}
```

Then distinguish:

```java
class Box<T>
```

from:

```java
static <T> T first(List<T> values)
```

Questions:

- Where is `T` declared?
- Who chooses the concrete type?
- Why is `List<String>` not a subtype of `List<Object>`?

## Part C — Bounds

Cover interview-level usage:

```java
<T extends Comparable<T>>
```

and:

```java
? extends T
? super T
```

Use PECS only after deriving the behavior:

```text
Producer → extends
Consumer → super
```

Do not memorize the acronym without explaining why reads/writes differ.

## Part D — Type Erasure

Build the mental model:

```text
Java generics mostly provide compile-time type safety.
Generic type parameters are largely erased from runtime class representation.
```

Reason through:

```java
List<String>
List<Integer>
```

Questions:

1. Are they separate runtime classes?
2. Why can't you normally write `new T()`?
3. Why can't you overload solely on `List<String>` vs `List<Integer>`?
4. Where do compiler-inserted casts appear conceptually?
5. What are bridge methods at a high level?

### Exit Bar

Explain without notes:

```text
generic type parameter
raw type
wildcard
bounded generic
type erasure
```

---

# 3. Reflection Basics — 25 min

Reflection is in Phase 2; keep it at interview/backend depth.

## Start From Class Metadata

Explore:

```java
Class<?> clazz = SomeService.class;
```

Know how to inspect:

```text
class name
fields
methods
constructors
annotations
```

Typical APIs:

```java
clazz.getDeclaredFields()
clazz.getDeclaredMethods()
clazz.getDeclaredConstructors()
clazz.getAnnotations()
```

## Core Questions

1. What is a `Class<?>` object?
2. How is reflection different from ordinary compile-time method invocation?
3. Why do frameworks use reflection?
4. What trade-offs does reflection introduce?
5. Why can reflection weaken normal encapsulation assumptions?
6. Why should reflection not be the default application design mechanism?

## Production Connection

Connect to Java frameworks conceptually:

```text
dependency injection
annotation processing at runtime
serialization/deserialization
ORM object mapping
test frameworks
plugin discovery
```

Do not deep-dive Spring yet; that belongs later in Phase 5.

### Small Exercise

Given:

```java
class User {
    private String name;
    public String getName() { ... }
}
```

Use reflection conceptually to:

```text
discover field
discover getter
inspect modifiers
```

The goal is understanding metadata/introspection, not building a framework.

---

# 4. JVM Execution — JIT Deep Dive — 40 min

The master curriculum requires more than source → bytecode → interpreter.

Today cover:

```text
interpreter
profiling
hot methods
JIT compilation
method inlining
escape analysis
code cache
```

## Part A — Hot Code

Mental model:

```text
.class bytecode
↓
execution begins
↓
interpreter executes
↓
JVM profiles runtime behavior
↓
frequently executed code becomes hot
↓
JIT compiles optimized machine code
```

Questions:

- Why not compile every method aggressively at startup?
- Why can warmup affect benchmarks?
- Why can production behavior differ from a cold local run?

## Part B — Method Inlining

Suppose:

```java
int calculate() {
    return add(10, 20);
}
```

Inlining can conceptually turn:

```text
call add()
```

into:

```text
place add's useful work directly into the caller
```

Discuss why this can:

```text
remove call overhead
enable additional optimizations
```

Do not reduce it to "method calls become faster."

## Part C — Escape Analysis

Object:

```java
Point p = new Point(1, 2);
```

Ask:

> Can this object reference escape the method/thread?

If not, the JVM may enable optimizations such as:

```text
scalar replacement
eliminating some allocations
```

Important precision:

Do **not** memorize the oversimplification:

```text
escape analysis means object goes on stack
```

The useful interview statement is:

```text
escape analysis lets the JIT reason about whether an object escapes and may enable allocation/elision or scalar-replacement optimizations.
```

## Part D — Code Cache

JIT-generated native code must live somewhere.

Discuss:

```text
code cache
compiled methods
finite capacity
```

At interview depth, understand why:

```text
bytecode metadata
heap objects
JIT native code
```

are different categories.

## Part E — Benchmark Connection

Explain why this is bad benchmarking:

```java
long start = System.nanoTime();
runMethodOnce();
long end = System.nanoTime();
```

Connect:

```text
warmup
JIT
dead-code elimination
constant folding
measurement noise
```

and why JMH exists.

### Exit Bar

Explain:

```text
Why can the 10,000th execution of a Java method behave differently from the first?
```

without using vague "JVM optimizes it" language.

---

# 5. B-Tree / B+ Tree Engineering Connection — 25 min

These are Phase-2 core topics and directly support the Phase-2 exit criterion:

> explain why databases prefer B/B+ trees over ordinary BSTs.

Do not implement a full B+ tree today.

## Start From the Constraint

A balanced binary tree may be:

```text
O(log n)
```

but database indexes live on:

```text
pages / disk / SSD
```

The important cost is not only comparison count.

Ask:

> What happens if every tree level requires another page read?

## B-Tree Intuition

Instead of two children per node:

```text
many keys
many children
```

High branching factor means:

```text
fewer levels
→ fewer page accesses
```

## B+ Tree Distinction

At interview depth:

```text
internal nodes → navigation keys
leaf nodes     → actual indexed entries / record references
leaf nodes commonly linked for ordered range scanning
```

Reason about why this is excellent for:

```text
point lookup
range scan
ordered traversal
```

## Required Comparison

Explain:

```text
BST / AVL / Red-Black
vs
B-Tree
vs
B+ Tree
```

from the perspective of:

```text
branching factor
tree height
page locality
range scans
database storage
```

### Exit Bar

Answer:

> Why doesn't a database simply use a Java-style Red-Black Tree for a large disk-backed index?

---

# 6. HLD Foundations — Level 1 — 20 min

This comes directly from the master curriculum's continuous HLD track.

Do **not** design a giant distributed system.

Level 1 focus:

```text
API
storage
caching
simple scaling
```

Today's purpose is to establish the interview sequence.

## HLD Reasoning Skeleton

For a small service, practice:

```text
1. What does the system need to do?
2. What does it explicitly not need to do?
3. What scale matters?
4. What APIs are required?
5. What data must persist?
6. What are the main read/write access patterns?
7. What component is actually the current bottleneck?
```

## Micro-Exercise

Use a deliberately small example:

```text
Store a short text snippet and retrieve it by ID.
```

Do not build Pastebin fully.

Derive only:

```text
POST /snippets
GET /snippets/{id}

Snippet:
- id
- content
- createdAt
```

Then reason:

- relational vs key/value storage?
- what is the primary access pattern?
- when would caching actually help?
- when would adding a load balancer become justified?

### Rule

No component may be added because "system designs usually have one."

Every component must solve a stated problem.

---

# 7. LLD Foundations — Responsibility Design — 20 min

This uses the master curriculum's LLD evaluation criteria:

```text
requirements
responsibilities
abstractions
state ownership
invariants
API design
SOLID
extensibility
error handling
testability
```

Do not start with design-pattern names.

## Micro-Exercise — Simple Logger

The master LLD problem bank includes a Logging Framework.

For today, only model the smallest version:

```text
log(INFO, "started")
log(ERROR, "failed")
```

Start with responsibilities:

```text
What owns log creation?
What owns formatting?
What owns output/destination?
```

Possible vocabulary may emerge:

```text
LogLevel
LogEvent
Formatter
Appender / Sink
Logger
```

But do not hand out the final class diagram first.

## Questions

1. Which state belongs to `Logger`?
2. Should formatting and output be the same responsibility?
3. How would console output and file output differ?
4. What is the smallest useful interface?
5. How would you test formatting without writing a file?
6. What changes if a new destination is added?

### Exit Bar

Produce:

```text
small set of classes/interfaces
clear responsibility per type
one invariant
basic method signatures
testability explanation
```

No concurrency yet. That belongs to Phase 3.

---

# Day 21 Exit Criteria

Day 21 can be considered complete only if the following are demonstrated.

## Java

- [ ] explain generic classes vs generic methods
- [ ] explain invariance of Java generics
- [ ] reason about `extends` vs `super`
- [ ] explain type erasure
- [ ] explain important runtime limitations caused by erasure

## Reflection

- [ ] explain what `Class<?>` represents
- [ ] inspect fields/methods/constructors conceptually
- [ ] explain why frameworks use reflection
- [ ] state trade-offs

## JVM

- [ ] explain interpreter → profiling → JIT
- [ ] explain hot methods
- [ ] explain method inlining
- [ ] explain escape analysis without the "everything goes to stack" myth
- [ ] explain code cache
- [ ] connect JIT behavior to benchmarking/warmup

## B/B+ Tree

- [ ] explain high branching factor
- [ ] connect height to page IO
- [ ] distinguish B-Tree from B+ Tree
- [ ] explain why B+ trees are useful for range scans
- [ ] explain why database indexes do not normally use ordinary binary trees

## HLD

- [ ] clarify requirements before architecture
- [ ] derive a minimal API
- [ ] identify persistence based on access pattern
- [ ] add cache/load balancing only when a reason exists

## LLD

- [ ] assign responsibilities cleanly
- [ ] identify state owner
- [ ] derive abstractions from change points
- [ ] keep the design testable
- [ ] avoid unnecessary patterns

---

# Explicitly Not In Day 21

Because the master curriculum places these later, do not introduce them here:

```text
Phase 3:
- synchronized / locks
- JMM
- volatile
- CAS
- executors
- ConcurrentHashMap internals
- virtual threads

Phase 4:
- transactions / isolation
- MVCC
- LSM / SSTable deep dive
- cache strategies

Phase 5:
- TCP / HTTP deep dive
- Spring
- Kafka
- microservices
- gRPC-style service communication
- security

Phase 6:
- sagas
- consensus
- distributed locks
- distributed-system HLD depth
```

Likewise, no DSA problem-solving is scheduled in Day 21.

---

# Exact Day 21 Starting Action

Start with one Java retrieval question:

> `List<String>` and `List<Integer>` look like different parameterized types in source code. At runtime, do you expect Java to create two completely separate `ArrayList` classes for them? Why or why not?

Use the answer to enter generics and type erasure.

---

# Registry Handling After Day 21

Do **not** mark Day 20 complete.

Update the registry with a separate Day-21 evidence section covering:

```text
Generics / type erasure
Reflection
JIT / inlining / escape analysis / code cache
B/B+ tree reasoning
HLD Level-1 fundamentals
LLD responsibility design
```

Keep Day-20 DSA blockers open for the dedicated DSA session.
