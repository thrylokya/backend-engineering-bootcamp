# Day 2 — Complexity, Linear Scan & Hash-Based Thinking

> **Duration:** 6–7 Hours
> **Theme:** Learn to analyze algorithms from first principles, strengthen engineering intuition, and begin designing hash-based data structures.
>
> **Objective:**
> Move beyond arrays into hashing while building strong intuition about complexity, memory, metadata, and production systems.

---

# Learning Outcomes

By the end of Day 2, you should be able to:

- Explain Big-O by counting work rather than memorizing formulas.
- Distinguish algorithmic complexity from hardware performance.
- Recognize linear scan problems immediately.
- Explain amortized analysis.
- Explain why ArrayList add() is amortized O(1).
- Explain why System.arraycopy() is faster than manual copying.
- Design and implement a basic HashMap.
- Explain hashing, collisions, load factor and resizing.
- Explain equals() vs hashCode().
- Connect HashMap to Redis, Kafka, Cassandra and backend systems.

---

# Session 1 — Complexity From First Principles (60 min)

## Goal

Never memorize Big-O again.

Instead derive it.

---

## Topics

### Time Complexity

Understand:

- O(1)
- O(log n)
- O(n)
- O(n log n)
- O(n²)

---

### Space Complexity

Measure:

- Variables
- Arrays
- HashMaps
- Recursion

---

### Operation Counting

Instead of:

> "This is O(n)"

Ask:

- How many iterations?
- Constant work?
- Nested loops?
- Extra allocations?

---

## Exercise

Analyze:

- Maximum element
- Reverse array
- Copy array
- Remove element
- Dynamic Array add()
- Dynamic Array remove()

---

## NEW

### Amortized Analysis

Derive:

Why:

```
ArrayList.add()
```

is

```
O(1)
```

even though resize is

```
O(n)
```

---

# Session 2 — Linear Scan Pattern (45 min)

## Goal

Identify one-pass problems immediately.

---

## Recognition

Ask:

- Can I solve in one pass?
- What state should I carry?
- What invariant exists?
- What repeated work can I eliminate?

---

## Classification

Without coding:

- Max element
- Second max
- Max consecutive ones
- Third maximum
- Even digit count

---

## Deliverable

Pattern Notes

- Recognition
- Invariant
- Typical mistakes
- Complexity

---

# Session 3 — Coding Practice (75 min)

Follow the interview framework:

- Clarifications
- Brute force
- Better
- Optimal
- Invariant

---

Problems

LC 485

Max Consecutive Ones

---

LC 1295

Find Numbers with Even Digits

---

LC 414

Third Maximum Number

---

Focus

- Running state
- Boundary conditions
- Invariants

---

# Session 4 — Engineering Lab (45 min)

## Finish Dynamic Array

Only remaining improvements:

- indexOf()
- lastIndexOf()
- remove(value)
- toString()

---

## Unit Tests

Write tests for:

- Empty
- Growth
- Shrink
- Remove
- Invalid index
- Capacity
- trimToSize()

---

## Complexity Table

Document

| API | Time | Space | Why |
|-----|------|-------|-----|

---

# Session 5 — JVM & Performance (45 min)

Topics

- System.arraycopy()
- Arrays.copyOf()
- Bounds checking
- JIT optimizations
- Cache locality

---

Explain

Why

```
System.arraycopy()
```

beats

```
manual loop
```

---

Explain

Spatial locality

Temporal locality

Sequential access

---

# Session 6 — HashMap Foundations (90 min)

## Goal

Understand HashMap before coding.

---

Topics

Hash Table

Buckets

Nodes

Memory layout

Hash function

Bucket index

Collision

Separate chaining

---

Explain

Average

```
O(1)
```

Worst

```
O(n)
```

---

# Session 7 — Build HashMap V1 (90 min)

Implement

```
put()

get()

containsKey()

remove()
```

Only

Separate chaining

No resize

No treeification

Focus

Correctness

---

# Session 8 — Java HashMap Deep Dive (45 min)

Topics

equals()

hashCode()

Load factor

Resize

Power-of-two capacity

Why

```
hash & (length-1)
```

instead of

```
%
```

---

Treeification

Threshold

8

Why?

---

# Session 9 — Production Connections (30 min)

Connect today's concepts to:

## Arrays

- Kafka logs
- Apache Arrow
- ClickHouse

---

## HashMap

- Redis
- Cassandra
- Memcached
- Routing tables
- Rate limiting
- Connection pools
- Deduplication
- Caches

---

Discuss

Why HashMap isn't used for databases.

Why B-Trees still dominate storage engines.

---

# Deliverables

## Notes

- Complexity
- Linear Scan
- Hashing
- equals()/hashCode()
- HashMap internals

---

## Coding

- LC 485
- LC 1295
- LC 414
- Dynamic Array complete
- HashMap V1

---

## Engineering

- Complexity table
- Unit tests
- Production connection notes

---

# Reflection Questions

1. Why is amortized O(1) different from O(1)?
2. Why can two O(n) algorithms differ by 5x?
3. What metadata does DynamicArray maintain?
4. Why does HashMap resize?
5. Why must equals() and hashCode() agree?
6. Why are collisions unavoidable?
7. Why doesn't Kafka use LinkedList?
8. Why doesn't Redis use arrays for everything?

---

# End-of-Day Success Criteria

You are ready for Day 3 if you can:

✅ Analyze algorithms without memorizing Big-O.

✅ Recognize linear scan patterns.

✅ Explain amortized complexity.

✅ Explain CPU cache effects.

✅ Implement DynamicArray confidently.

✅ Implement HashMap V1.

✅ Explain equals() vs hashCode().

✅ Explain collisions and load factor.

✅ Connect arrays and hash tables to production backend systems.
