# Backend Engineering Bootcamp — Master Curriculum

> **Purpose:** Build an exceptional backend engineer who can design, build, debug, scale, operate, and lead production systems at Senior/Lead/Staff level.
>
> **Primary language:** Java
>
> **Primary stack:** Java, Spring Boot, SQL, Kafka, Redis, AWS, Docker, Kubernetes
>
> **Target roles:** Senior Software Engineer, Lead Engineer, SMTS/Staff Engineer; Principal Engineer as a longer-term trajectory
>
> **Target companies:** Salesforce, Google, Microsoft, Amazon, Uber, Stripe, Airbnb, LinkedIn, Atlassian, Databricks, Snowflake, Netflix, Cloudflare, Confluent
>
> **Expected commitment:** 20–25 hours/week
>
> **Primary optimization target:** Production engineering excellence first, interview success as a consequence

---

# 1. How This Curriculum Is Used

This is the strategic source of truth for the bootcamp.

It deliberately does **not** contain every daily lesson.

Daily curricula are generated using:

```text
MASTER_CURRICULUM.md
        +
registry.md
        +
previous session evidence
        ↓
Today's curriculum
        ↓
Learn → Explain → Implement → Test → Benchmark → Design → Debug
        ↓
Update registry.md
        ↓
Next curriculum
```

The master curriculum answers:

- What must eventually be mastered?
- In what dependency order?
- What production depth is required?
- What interview depth is required?
- What projects demonstrate mastery?
- What evidence is required before progressing?

The registry answers:

- Where are we now?
- What has been completed?
- What misconceptions remain?
- What deliverables are incomplete?
- What should happen next?

Daily curriculum files answer:

- What exactly should be studied today?
- What should be implemented today?
- What should be explained without notes?
- What should be tested or benchmarked?
- What interview questions should be attempted?

---

# 2. Curriculum Philosophy

## Rule 1 — Derive, Do Not Memorize

For every concept, understand the mechanism.

Examples:

- Do not memorize that array access is O(1); derive address calculation.
- Do not memorize that HashMap is O(1); understand hashing, collisions, load factor, resizing, and pathological cases.
- Do not memorize CAP; reason about failure modes and consistency guarantees.
- Do not memorize JVM tuning flags; understand allocation, GC pressure, safepoints, and workload shape.

## Rule 2 — Connect Interview Knowledge to Production Systems

Every major topic must answer:

1. How does this appear in interviews?
2. Where does this appear in real systems?
3. What breaks under scale or failure?
4. How would we observe the failure?
5. How would we mitigate it?

## Rule 3 — Implementation Is Mandatory

```text
Concept
  ↓
Mental model
  ↓
Small implementation
  ↓
Tests
  ↓
Benchmark
  ↓
Production connection
  ↓
Design trade-offs
```

## Rule 4 — HLD and LLD Start Early

System design is not a final-month activity.

Every phase must contain some combination of:

- API design
- class design
- invariants
- state transitions
- storage decisions
- failure handling
- capacity reasoning
- observability
- deployment

## Rule 5 — Production Engineering Is a First-Class Track

The curriculum must continuously develop:

- testing
- observability
- debugging
- performance analysis
- reliability
- rollout safety
- incident response
- operational ownership

## Rule 6 — Staff-Level Growth Requires Leadership Evidence

Staff-level preparation includes:

- architecture decisions
- influencing without authority
- cross-team alignment
- technical strategy
- mentoring
- incident leadership
- reducing organizational risk
- driving ambiguous work

---

# 3. Target Capability Model

By the end of the six-month core program, the engineer should be capable of:

## Algorithms and Data Structures

- Solving common interview problems systematically.
- Recognizing patterns rather than memorizing solutions.
- Explaining invariants and lower bounds.
- Connecting data structures to production systems.
- Implementing core structures from scratch.
- Discussing memory layout and cache behavior.

## Java and JVM

- Explaining JVM execution from source to machine code.
- Reasoning about heap, stack, metaspace, object layout, GC, JIT, and safepoints.
- Writing correct concurrent Java code.
- Diagnosing CPU, memory, lock, thread, and GC issues.
- Choosing appropriate concurrency primitives.

## Backend Engineering

- Designing stable APIs.
- Building maintainable Spring Boot services.
- Designing for retries, timeouts, backpressure, idempotency, and failure.
- Choosing storage systems based on access patterns and consistency needs.

## Distributed Systems

- Reasoning about partial failure.
- Explaining replication and consensus.
- Understanding ordering and delivery semantics.
- Designing distributed caches, queues, schedulers, counters, and locks.

## Databases

- Understanding indexes and query plans.
- Explaining transactions and isolation.
- Designing schemas around access patterns.
- Diagnosing slow queries and hot partitions.

## System Design

- Driving requirement clarification.
- Estimating scale.
- Designing data models and APIs.
- Reasoning about consistency, availability, cost, and failure domains.
- Designing observability and rollout strategy.

## Production Engineering

- Debugging real production symptoms.
- Designing metrics, logs, and traces.
- Handling incidents.
- Conducting root-cause analysis.
- Preventing regressions.

## Leadership

- Communicating architecture clearly.
- Handling disagreement constructively.
- Driving cross-team technical decisions.
- Mentoring engineers.
- Managing technical debt strategically.

---

# 4. Knowledge Dependency Graph

```text
Computer Fundamentals
│
├── Complexity & Algorithms
│   ├── Arrays / Strings
│   ├── Hashing
│   ├── Linked Structures
│   ├── Trees / Heaps
│   ├── Graphs
│   └── Dynamic Programming
│
├── Java Language
│   ├── Object Model
│   ├── Collections
│   ├── Generics
│   ├── Exceptions
│   ├── Streams
│   └── Reflection
│       │
│       └── JVM
│           ├── Class Loading
│           ├── Memory Layout
│           ├── JIT
│           ├── GC
│           ├── Java Memory Model
│           └── Concurrency
│
├── Operating Systems
│   ├── Processes / Threads
│   ├── Scheduling
│   ├── Virtual Memory
│   ├── Filesystems
│   └── Linux Debugging
│
├── Networking
│   ├── TCP/IP
│   ├── DNS
│   ├── HTTP
│   ├── TLS
│   ├── Load Balancing
│   └── Proxies
│
├── Databases
│   ├── Storage Engines
│   ├── B/B+ Trees
│   ├── LSM Trees
│   ├── Indexes
│   ├── Query Planning
│   ├── Transactions
│   ├── Isolation
│   └── Replication
│
├── Backend Architecture
│   ├── REST / GraphQL
│   ├── Spring Boot
│   ├── Security
│   ├── Caching
│   ├── Messaging
│   └── Microservices
│       │
│       └── Distributed Systems
│           ├── Time / Ordering
│           ├── Replication
│           ├── Consensus
│           ├── Partitioning
│           ├── Idempotency
│           ├── Transactions
│           └── Failure Handling
│
├── Infrastructure
│   ├── Docker
│   ├── Kubernetes
│   ├── AWS
│   └── CI/CD
│
├── Production Engineering
│   ├── Testing
│   ├── Observability
│   ├── Reliability
│   ├── Performance
│   ├── Security
│   └── Incident Response
│
└── Staff Engineering
    ├── Architecture
    ├── Technical Strategy
    ├── Cross-Team Influence
    ├── Mentoring
    ├── Technical Debt
    └── Organizational Reliability
```

---

# 5. Six-Month Core Program

The phase boundaries are directional, not rigid. Registry evidence decides when to move forward.

## Phase 1 — Foundations: Data Structures, Complexity, Memory

### Approximate Duration

Weeks 1–4

### Core Topics

- Arrays and dynamic arrays
- Complexity and amortized analysis
- Hashing
- Linked lists, stack, queue, deque
- Linear scan
- complement lookup
- membership lookup
- prefix accumulation
- two pointers
- sliding window
- fast/slow pointer
- binary search
- primitive vs reference types
- equals/hashCode
- boxing/unboxing
- object headers
- array layout
- heap vs stack
- virtual vs physical memory
- CPU cache
- JIT introduction

### Engineering Labs

- Dynamic Array
- HashMap V1
- Linked List
- Stack
- Queue
- LRU Cache

Each important implementation should eventually include:

- unit tests
- edge cases
- complexity notes
- design notes
- JMH benchmark where meaningful

### LLD Track

- Dynamic Array
- LRU Cache
- simple Rate Limiter

### Exit Criteria

- derive common complexities
- explain cache locality
- implement Dynamic Array and HashMap without copying a reference
- solve foundational array/hash problems consistently
- explain strong loop invariants
- explain Java reference semantics precisely

---

## Phase 2 — Trees, Heaps, Graphs, Collections, JVM Execution

### Approximate Duration

Weeks 5–7

### Core Topics

- binary tree
- BST
- AVL tree
- red-black tree
- TreeMap
- trie
- radix tree
- B-tree
- B+ tree
- binary heap
- priority queue
- adjacency list/matrix
- BFS
- DFS
- topological sort
- union-find
- Collections hierarchy
- ArrayList internals
- HashMap internals
- Comparable/Comparator
- generics
- type erasure
- reflection basics
- source → bytecode → execution
- interpreter
- JIT
- hot methods
- method inlining
- escape analysis
- code cache
- class loading
- metaspace

### Engineering Labs

- Binary Search Tree
- Binary Heap
- Trie
- Union-Find
- Dependency Graph / Topological Scheduler

### Exit Criteria

- explain balancing trade-offs
- implement heap and trie
- solve tree traversal problems cleanly
- explain why databases prefer B/B+ trees over ordinary BSTs
- explain JIT and class loading at interview depth

---

## Phase 3 — Concurrency, Java Memory Model, Operating Systems

### Approximate Duration

Weeks 8–10

### Core Topics

- threads
- synchronized
- ReentrantLock
- ReadWriteLock
- StampedLock
- volatile
- atomic classes
- CAS
- VarHandles
- Unsafe concepts
- executors
- thread pools
- CompletableFuture
- ForkJoinPool
- virtual threads
- visibility
- atomicity
- ordering
- happens-before
- safe publication
- final-field semantics
- data races
- memory barriers
- ConcurrentHashMap
- ConcurrentLinkedQueue
- BlockingQueue
- CopyOnWriteArrayList
- DelayQueue
- ring buffer
- process vs thread
- scheduling
- context switching
- system calls
- virtual memory
- page faults
- file descriptors
- sockets
- epoll/select concepts

### Engineering Labs

- bounded blocking queue
- thread pool
- token-bucket rate limiter
- concurrent counter variants
- producer-consumer benchmark

### Production Debugging

- deadlock
- thread starvation
- CPU saturation
- lock contention
- runaway thread creation
- blocked IO
- memory leak

### Exit Criteria

- explain happens-before precisely
- identify unsafe publication
- choose concurrency primitives by workload
- read thread dumps
- reason about virtual vs platform threads

---

## Phase 4 — Databases, Storage Engines, Caching

### Approximate Duration

Weeks 11–13

### Core Topics

- schema design
- normalization/denormalization
- primary/secondary indexes
- clustered/non-clustered indexes
- composite indexes
- covering indexes
- query plans
- join algorithms
- cardinality
- selectivity
- ACID
- isolation levels
- dirty/non-repeatable/phantom reads
- write skew
- MVCC
- locking
- deadlocks
- pages
- WAL
- buffer pool
- B+ trees
- LSM trees
- SSTables
- compaction
- Bloom filters
- cache-aside
- read-through
- write-through
- write-behind
- eviction
- TTL
- stampede
- penetration
- stale data
- hot keys
- Skip List
- HyperLogLog
- Merkle Tree

### Engineering Labs

- mini storage engine
- B+ tree or simplified page index
- WAL
- TTL cache
- Bloom filter
- LSM-style immutable segments

### Production Debugging

- slow query
- missing index
- index not used
- hot partition
- connection pool exhaustion
- DB lock contention
- cache stampede

### Exit Criteria

- design indexes from access patterns
- read an execution plan
- explain MVCC
- explain B+ tree vs LSM tree
- select appropriate cache strategy

---

## Phase 5 — Networking, APIs, Spring, Security, Messaging

### Approximate Duration

Weeks 14–16

### Core Topics

- TCP
- connection establishment
- retransmission
- congestion control concepts
- keep-alive
- DNS
- HTTP/1.1
- HTTP/2
- HTTP/3 concepts
- TLS
- proxies
- reverse proxies
- load balancers
- REST resource modeling
- HTTP semantics
- idempotency
- pagination
- filtering
- versioning
- error models
- retries
- GraphQL schema design
- resolvers
- N+1
- DataLoader
- caching
- authorization
- query complexity
- Spring IoC/DI
- bean lifecycle
- proxies
- AOP
- transactions
- Spring MVC
- validation
- configuration
- Spring Security
- filters/interceptors
- actuator
- testing
- Kafka partitions
- consumer groups
- offsets
- ordering
- delivery semantics
- idempotent consumers
- retries
- DLQ
- backpressure
- authentication/authorization
- sessions
- JWT
- OAuth2/OIDC concepts
- secrets
- OWASP API risks
- SSRF
- injection
- rate limiting

### Engineering Labs

- production-quality REST service
- GraphQL endpoint with N+1 mitigation
- idempotent Kafka consumer
- authentication/authorization module

### Exit Criteria

- reason from protocol behavior instead of framework magic
- design retry-safe APIs
- diagnose N+1 and network latency
- explain Kafka ordering and delivery semantics
- explain Spring proxy limitations

---

## Phase 6 — Distributed Systems and System Design

### Approximate Duration

Weeks 17–20

### Core Topics

- partial failure
- distributed computing fallacies
- clocks
- ordering
- logical clocks
- vector clocks
- consistency models
- quorum
- replication
- partitioning
- sharding
- leader/follower
- leaderless systems
- consensus
- Raft
- leases
- distributed locks
- sagas
- outbox pattern
- idempotency
- deduplication
- consistent hash ring
- distributed hash table
- distributed queue
- distributed cache
- distributed counter
- CRDT
- gossip
- timeout
- retry
- exponential backoff
- jitter
- circuit breaker
- bulkhead
- load shedding
- backpressure
- graceful degradation

### System Design Framework

1. Clarify requirements.
2. Identify scale.
3. Define APIs.
4. Model data.
5. Establish consistency requirements.
6. Build high-level architecture.
7. Identify bottlenecks.
8. Design failure handling.
9. Design observability.
10. Discuss cost.
11. Discuss evolution.

### Core HLD Problem Bank

- URL Shortener
- Rate Limiter
- Notification Service
- Pastebin
- Distributed Cache
- File Storage
- News Feed
- Chat System
- Search Autocomplete
- Metrics Platform
- Logging Platform
- API Gateway
- Authentication Platform
- Feature Flag System
- Job Scheduler
- Workflow Engine
- Distributed Queue
- Event Streaming Platform
- Search Engine
- Object Storage
- Video Streaming
- Ride Sharing
- Payment System
- E-commerce Checkout
- Ad Click Aggregation
- Collaborative Editing
- Web Crawler
- Recommendation Serving
- Distributed Counter
- Distributed Lock Service
- Configuration Service

### Primary Capstone — Distributed Key-Value Store

Evolve through:

1. single-node memory store
2. TTL
3. WAL
4. restart recovery
5. snapshots
6. replication
7. leader/follower
8. follower catch-up
9. partitioning
10. consensus/Raft
11. metrics
12. failure injection
13. benchmarks
14. operational documentation

### Exit Criteria

- reason about partial failure naturally
- explain replication/consensus trade-offs
- design idempotency correctly
- complete medium/high-complexity system designs within interview time
- connect architecture decisions to SLOs and failure modes

---

## Phase 7 — Production Engineering, Observability, Reliability, Performance

### Approximate Duration

Weeks 21–22

### Core Topics

- request rate
- error rate
- latency
- availability
- saturation
- queue depth
- dependency health
- business metrics
- structured logging
- correlation IDs
- tracing
- spans
- critical path
- SLI/SLO/SLA
- error budgets
- alert design
- burn rate
- graceful degradation
- incident detection
- triage
- mitigation
- communication
- root cause
- corrective actions
- latency distributions
- p50/p95/p99
- throughput
- CPU profiling
- allocation profiling
- GC analysis
- lock contention
- IO bottlenecks
- queueing
- benchmarking
- JMH
- load testing
- heap dumps
- thread dumps
- GC logs
- JFR
- jstack/jcmd concepts

### Failure Labs

Inject and diagnose:

- DB slowdown
- Redis outage
- Kafka lag
- high CPU
- memory pressure
- deadlock
- dependency timeout
- packet latency
- partial replica failure

### Exit Criteria

- create useful dashboards
- define actionable alerts
- debug latency from evidence
- distinguish CPU, memory, IO, lock, DB, and network bottlenecks
- lead a structured incident investigation

---

## Phase 8 — Staff Engineering, Leadership, Portfolio, Interview Integration

### Approximate Duration

Weeks 23–24 and ongoing

### Technical Leadership

- architecture decision records
- design reviews
- trade-off communication
- technical strategy
- migration planning
- dependency management
- risk management
- technical debt
- ownership boundaries

### People and Influence

- mentoring
- feedback
- disagreement
- influencing without authority
- cross-team collaboration
- stakeholder alignment
- project leadership

### Behavioral Story Bank

Prepare evidence for:

- ownership
- conflict
- failure
- incident
- mentoring
- architecture decision
- technical debt
- ambiguous project
- cross-team influence
- difficult stakeholder
- scaling a system
- improving quality
- preventing production failure

### Interview Integration

Run realistic loops containing:

- coding
- Java/JVM
- concurrency
- LLD
- HLD
- databases
- distributed systems
- debugging
- behavioral
- leadership

Interview simulation must:

- ask one question at a time
- challenge assumptions
- provide no premature hints
- score clarity, correctness, depth, trade-offs, and seniority signal

---

# 6. Continuous Data Structures Mastery Track

## Tier 1 — Must Implement

- Dynamic Array
- Linked List
- Stack
- Queue
- Deque
- HashMap
- Binary Search Tree
- Binary Heap
- Trie
- Union-Find
- LRU Cache
- Blocking Queue
- Bloom Filter
- Consistent Hash Ring

## Tier 2 — Must Understand Internals

- LinkedHashMap
- ConcurrentHashMap
- WeakHashMap
- IdentityHashMap
- TreeMap
- AVL Tree
- Red-Black Tree
- B Tree
- B+ Tree
- Radix Tree
- Segment Tree
- Fenwick Tree
- Interval Tree
- Indexed Heap
- Skip List
- HyperLogLog
- Merkle Tree
- LSM Tree
- Ring Buffer

## Tier 3 — Interview/Conceptual Awareness

- Fibonacci Heap
- Suffix Array
- Suffix Tree
- CRDT structures
- Distributed Hash Table
- Vector Clock

## Required Analysis Template

For each important structure:

- internal representation
- memory layout
- cache friendliness
- time complexity
- space complexity
- mutation behavior
- thread safety
- Java implementation
- production usage
- systems using it
- failure/pitfall modes
- when not to use it

## Production Mapping

| Data Structure | Example Production Usage |
|---|---|
| Array / Dynamic Array | ArrayList, buffers, analytics engines |
| Trie / Radix Tree | autocomplete, routers, prefix matching |
| Bloom Filter | Cassandra/RocksDB-style lookup avoidance |
| LSM Tree | RocksDB/Cassandra-style storage engines |
| B+ Tree | relational database indexes |
| Consistent Hash Ring | distributed cache/shard placement |
| Skip List | ordered in-memory structures |
| Merkle Tree | Git/content addressing, replica comparison concepts |
| Ring Buffer | LMAX Disruptor-style event processing |
| Heap | schedulers / priority queues |
| Graph | dependency resolution / schedulers |
| HyperLogLog | approximate cardinality |
| Union-Find | connectivity / clustering |

---

# 7. Continuous Coding Interview Track

## Core Categories

- Arrays
- Strings
- Hashing
- Two Pointers
- Sliding Window
- Binary Search
- Linked Lists
- Stack
- Queue
- Trees
- BST
- Trie
- Heap
- Graph
- Dynamic Programming
- Greedy
- Backtracking
- Union Find
- Topological Sort
- Bit Manipulation
- Monotonic Stack
- Segment Tree
- Design-style coding

## Required Interview Response Sequence

1. Requirements
2. Clarifications
3. Assumptions
4. Brute force
5. Complexity
6. Bottleneck
7. Better/optimal approach
8. Invariant
9. Dry run
10. Final complexity
11. Trade-offs

## Recognition Questions

Before coding, ask:

- What repeated work exists?
- What state do I need to carry?
- Is ordering important?
- Is this a membership problem?
- Is this a range problem?
- Is the input sorted?
- Is there monotonic behavior?
- Is this really a graph?
- Can previous computation be reused?

## Seniority Expectations

### Senior Engineer

- solve correctly
- communicate cleanly
- handle edge cases
- analyze complexity

### Lead Engineer

Additionally:

- justify trade-offs
- discuss testability
- discuss maintainability
- identify production implications where relevant

### Staff Engineer

Additionally:

- expose assumptions early
- identify invariant and failure modes
- reason about scale/memory/runtime behavior
- communicate alternatives and decision boundaries

---

# 8. Continuous Low-Level Design Track

LLD should appear every week after the foundation phase.

## Core Problem Bank

- Parking Lot
- Elevator
- ATM
- Vending Machine
- Library
- Chess
- Tic-Tac-Toe
- Snake and Ladder
- Splitwise
- BookMyShow / Seat Reservation
- Restaurant Reservation
- Amazon Locker
- Cache
- LRU Cache
- Rate Limiter
- Logging Framework
- Task Scheduler
- Notification Framework
- Pub/Sub Library
- File System
- URL Shortener components
- API Client SDK
- Retry Framework
- Circuit Breaker
- Feature Flag Client
- Metrics Library
- Connection Pool
- Job Executor

## LLD Evaluation Criteria

- requirement clarity
- responsibilities
- abstractions
- state ownership
- invariants
- API design
- SOLID
- extensibility
- error handling
- concurrency
- persistence boundaries
- test strategy
- design-pattern appropriateness

---

# 9. Continuous High-Level Design Track

## Level 1

Focus:

- API
- storage
- caching
- simple scaling

Examples:

- URL Shortener
- Pastebin
- Rate Limiter
- Notification Service

## Level 2

Add:

- async workflows
- partitioning
- failure handling
- observability

Examples:

- News Feed
- Chat
- Job Scheduler
- Metrics Service
- Distributed Cache

## Level 3

Add:

- distributed consistency
- replication
- ordering
- large-scale operations

Examples:

- Kafka-like platform
- distributed KV store
- search engine
- feature flag platform
- workflow engine
- object storage

## Level 4 — Staff-Level Design

Add:

- migrations
- multi-region
- cost
- organizational ownership
- operational risk
- evolution over years

Examples:

- global payments platform
- multi-region metadata system
- enterprise search platform
- multi-tenant event platform
- global authorization platform

---

# 10. Java Mastery Track

## Language

- objects and references
- generics
- type erasure
- exceptions
- annotations
- reflection
- streams
- lambdas
- records
- sealed classes concepts

## JVM

- bytecode
- class loading
- metaspace
- heap
- stack
- object layout
- compressed references concepts
- JIT
- code cache
- inlining
- escape analysis
- GC
- safepoints

## Garbage Collection

Understand behavior and trade-offs of major modern collectors conceptually, including:

- generational collection
- G1
- low-pause collectors such as ZGC concepts

Focus on workload behavior rather than flag memorization.

## Concurrency

- JMM
- volatile
- synchronized
- locks
- atomics
- CAS
- executors
- CompletableFuture
- ForkJoin
- virtual threads
- concurrent collections

## IO

- blocking IO
- NIO concepts
- buffers
- channels
- selectors
- Netty architecture concepts

## Production Debugging Scenarios

- high GC
- allocation churn
- memory leak
- CPU hot loop
- deadlock
- contention
- blocked thread pool
- exhausted connection pool
- thread explosion
- latency after deployment

---

# 11. Production Engineering Track

Every project must progressively acquire production characteristics.

## Testing

- unit tests
- integration tests
- contract tests
- compatibility tests
- concurrency tests
- property/invariant tests where useful
- performance tests
- failure tests

## Observability

Every substantial service should expose:

- request rate
- errors
- latency
- saturation
- dependency metrics
- business outcomes where relevant

## Reliability

Every design should consider:

- timeouts
- retries
- idempotency
- circuit breaking
- rollout
- rollback
- feature flags
- dependency failure
- data corruption

## Performance

Every performance claim should eventually answer:

- what was measured?
- with what workload?
- p50/p95/p99?
- CPU?
- allocation?
- GC?
- IO?
- contention?

---

# 12. Portfolio Strategy

Do not build ten shallow repositories.

Prefer a smaller number of increasingly production-grade systems.

## Primary Portfolio Projects

### 1. Distributed Key-Value Store

Highest priority.

Topics:

- WAL
- TTL
- snapshotting
- replication
- partitioning
- consensus
- failure recovery

### 2. Event Streaming Platform

Topics:

- append-only log
- partitions
- consumers
- offsets
- retention
- replication

### 3. Workflow / Distributed Job Engine

Topics:

- DAGs
- retries
- scheduling
- leases
- workers
- idempotency

### 4. API Gateway / Rate Limiting Platform

Topics:

- routing
- authentication
- rate limiting
- circuit breaking
- telemetry

### 5. Search Engine / Indexing Service

Topics:

- inverted indexes
- tokenization
- ranking concepts
- caching
- partitioning

## Secondary Project Candidates

- Redis-like cache
- Authentication Platform
- Notification Service
- Feature Flag Platform
- Distributed Scheduler

Projects are selected based on learning value, not repository count.

---

# 13. Open Source Track

## Candidate Ecosystems

- OpenJDK
- Spring Framework
- Spring Boot
- Apache Kafka
- Apache Flink
- Apache Pulsar
- Apache Camel
- OpenSearch
- Netty
- gRPC Java
- OpenTelemetry Java
- Micrometer
- Testcontainers
- JUnit
- Mockito
- Hazelcast
- Quarkus
- Micronaut
- OpenRewrite
- Redis Java clients

## Progression

```text
Read architecture
    ↓
Build locally
    ↓
Read tests
    ↓
Reproduce issue
    ↓
Documentation/test contribution
    ↓
Small bug fix
    ↓
Subsystem understanding
    ↓
Regular contribution
```

---

# 14. Leadership Preparation Track

Maintain a living story bank.

For each story capture:

- situation
- scope
- stakeholders
- constraints
- decision
- alternatives
- conflict
- action
- measurable outcome
- what you learned
- what you would change

## Required Themes

- ownership
- mentoring
- conflict
- technical debt
- architecture decision
- production incident
- scaling
- cross-team collaboration
- stakeholder management
- failed project / mistake
- ambiguous problem
- migration
- quality improvement
- influencing without authority

---

# 15. Interview Landscape Track

The exact company process changes over time, so company-specific preparation should be verified close to application time.

The curriculum prepares for:

- recruiter screen
- hiring manager
- coding
- pair programming
- machine coding
- LLD
- HLD
- Java/JVM
- concurrency
- databases
- networking
- distributed systems
- cloud
- production debugging
- architecture
- behavioral
- leadership
- cross-team influence

As applications approach, create company-specific interview packets rather than distorting the entire curriculum around one company.

---

# 16. Progress and Evidence Model

Progress is not based on reading completion.

## Knowledge Levels

### Level 0 — Unknown

Cannot explain the concept.

### Level 1 — Familiar

Recognizes terminology.

### Level 2 — Explain

Can explain clearly without notes.

### Level 3 — Apply

Can use the concept to solve a problem.

### Level 4 — Implement

Can build a simplified version.

### Level 5 — Diagnose / Design

Can reason about trade-offs, failures, production behavior, and alternatives.

For staff-level topics, the target is usually Level 5.

## Registry Fields

| Field | Meaning |
|---|---|
| Topic | Knowledge area |
| Priority | Interview/production priority |
| Difficulty | Current difficulty |
| Confidence | Self + mentor confidence |
| Status | Not Started / Learning / Practicing / Mastered |
| Evidence | Implementation, explanation, benchmark, design, mock |
| Revision Count | Number of revisits |
| Mock Score | Latest relevant score |
| Last Revision | Last practice date |
| Next Revision | Scheduled revisit |

---

# 17. Weekly Operating Model

Each week should normally contain:

1. Fundamentals
2. Coding
3. Java/JVM
4. Engineering Lab
5. Design
6. Production Engineering
7. Revision
8. Interview Simulation

A typical 20–25 hour allocation evolves over time.

### Early Program

```text
DSA / Data Structures       35%
Java / JVM                  20%
Engineering Projects        20%
LLD / HLD                   15%
Production / Debugging      10%
```

### Middle Program

```text
DSA / Coding                25%
Java / JVM                  20%
Distributed / Databases     20%
Projects                    15%
LLD / HLD                   15%
Leadership / Production      5%
```

### Late Program

```text
Coding                      20%
System Design               25%
Distributed / JVM           20%
Production Debugging        15%
Leadership                  10%
Mocks / Company Prep        10%
```

Registry gaps override these guidelines.

---

# 18. Daily Curriculum Generation Rules

When generating a new day, do **not** blindly take the next topic.

## Step 1 — Read Registry

Determine:

- current phase
- incomplete work
- observed misconceptions
- pending deliverables
- next action

## Step 2 — Read Master Curriculum

Identify:

- dependency order
- required depth
- cross-track connections

## Step 3 — Preserve Continuity

Do not abandon unfinished core work simply because a new calendar day started.

## Step 4 — Compose the Day

A daily curriculum should usually contain some combination of:

- learning objective
- theory
- guided questioning
- coding problems
- implementation lab
- Java/JVM deep dive
- LLD/HLD
- production connection
- debugging/performance exercise
- reflection
- exit criteria

Not every day requires every section.

## Step 5 — Keep Scope Achievable

A day must have a clear finish line.

## Step 6 — Test Before Marking Complete

Use questions, dry runs, code review, debugging, or design challenges.

## Step 7 — Update Registry

Record:

- completed work
- evidence
- misconceptions
- strengths
- growth areas
- pending deliverables
- exact next action

---

# 19. Promotion Gates

## Gate A — Strong Senior Backend Engineer

Evidence:

- consistent medium coding performance
- strong Java fundamentals
- reliable API and database design
- competent LLD
- basic/medium HLD
- production debugging discipline

## Gate B — Lead / SMTS Interview Ready

Six-month target.

Evidence:

- reliable coding under time pressure
- strong JVM/concurrency
- strong database reasoning
- strong distributed-system fundamentals
- medium/hard system design
- production troubleshooting
- clear leadership stories
- architecture ownership evidence

## Gate C — Staff Interview Ready

Target around 9–12 months depending on actual progress and experience evidence.

Evidence:

- strong system design across ambiguous requirements
- architecture evolution and migration thinking
- multi-team influence
- production reliability judgment
- deep distributed-system reasoning
- strong technical leadership examples
- demonstrated project/open-source depth

## Gate D — Principal Trajectory

Requires sustained organizational impact:

- multi-year technical strategy
- organization-wide architecture
- systemic reliability improvements
- development of other technical leaders
- cross-organization influence

---

# 20. What We Will Not Do

- Grind hundreds of LeetCode problems without pattern understanding.
- Memorize system-design templates.
- Learn Kubernetes commands without understanding distributed infrastructure.
- Tune JVM flags without workload evidence.
- Add design patterns simply to name them.
- Build ten shallow portfolio projects.
- Mark a topic complete after merely reading it.
- Postpone HLD until the final month.
- Treat production debugging as optional.
- Optimize exclusively for one company's interview format.

---

# 21. Six-Month Outcome

At the end of the core program, the target is not:

> "I finished a curriculum."

The target is:

> "I can reason from first principles, implement core mechanisms, design distributed backend systems, diagnose production failures, explain trade-offs, and lead technical decisions with the depth expected from a strong Lead/Staff-track backend engineer."

Interview readiness will be assessed continuously, but engineering capability remains the primary metric.

---

# 22. Twelve-Month Extension

After the six-month core program, continue toward Staff/Principal depth through:

- deeper distributed systems
- database internals
- advanced JVM performance
- large-scale architecture
- multi-region systems
- advanced networking
- security architecture
- open-source contribution
- technical strategy
- architecture migrations
- organization-level reliability
- mentoring and technical leadership

The second six months should be driven heavily by observed gaps, target-company calibration, and real production/portfolio work rather than a fixed syllabus.

---

# 23. Source-of-Truth Rule

This document is the **master curriculum**.

It should change only when the long-term learning strategy changes.

`registry.md` is the **execution state** and should change frequently.

Daily files are **tactical plans** and should be generated from both.

When conflicts occur:

```text
Learning strategy        → MASTER_CURRICULUM.md
Current progress         → registry.md
Today's execution        → dayXX.md
```

This separation must be maintained throughout the bootcamp.
