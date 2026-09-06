## Day 11 — Retrieval, Transfer, Memory, HLD, Production Debugging

**Status:** Completed conceptually
**Phase advancement:** Do not advance phase solely because Day 11 is complete. Continue to evaluate based on demonstrated retrieval and transfer across upcoming sessions.

### DSA — Subarray Sum Equals K

* Reconstructed the brute-force approach independently:

  * Fix each start index.
  * Expand the end index.
  * Maintain a running sum.
  * Do not stop early when the sum exceeds or reaches the target because negative values can later change the sum.

* Derived the prefix-sum relationship:

  `currentPrefix - earlierPrefix = target`

  therefore:

  `earlierPrefix = currentPrefix - target`

* Understood why a frequency map is required instead of a set:

  * The same prefix sum may occur at multiple earlier boundaries.
  * Each occurrence represents a distinct possible starting boundary for a valid subarray.

* Understood the purpose of the initial prefix entry:

  `0 -> 1`

  It represents the boundary before index `0` and enables counting subarrays that begin at index `0`.

* Correctly implemented an O(n) expected-time solution after debugging two mistakes:

  * Lookup must be `currentPrefix - target`.
  * The map must store frequencies of observed prefix sums, not frequencies of lookup values.

* Verified the implementation against positive, negative, zero-heavy, empty-array, and single-element cases.

* Retrieval check:

  * Reconstructed the reasoning behind prefix sums and frequency counting without being given the pattern.
  * Minor wording imprecision remained around which quantity is subtracted from which, but the underlying invariant was retained.

**Current DSA evidence:** Prefix-sum + frequency-map pattern is understood at a conceptual level. Needs spaced retrieval on a different problem before marking the pattern as independently transferable.

---

### Java / OS Memory — Virtual vs Physical Memory

* Established the distinction between:

  * Java heap
  * process virtual address space
  * virtual pages
  * physical frames
  * physical RAM

* Understood that ordinary application memory accesses operate through virtual addresses rather than directly using physical RAM addresses.

* Understood the conceptual translation path:

  `virtual address -> MMU/page table -> physical address -> RAM`

* Clarified:

  * Virtual memory is primarily an addressing/isolation abstraction, not merely "extra RAM on disk."
  * Virtual address spaces provide isolation, stable addressing, flexible allocation, protection, and controlled sharing.

* Understood paging:

  * Virtual memory is divided into pages.
  * Physical RAM is divided into frames.
  * A page table maps virtual pages to physical frames.
  * Virtual address = virtual page number + offset.
  * The offset remains unchanged during address translation.

* Understood page faults:

  * If a required virtual page is not resident in RAM, the CPU raises a page fault.
  * The OS obtains the required page from its backing source, places it into a physical frame, updates the page table, and retries the instruction.

* Distinguished:

  * CPU cache miss from page fault.
  * Cache miss means data is not currently in CPU cache.
  * Page fault means the required virtual page is not currently resident in physical memory.

* Clarified that Java objects contain instance state; methods are not copied into every object.

* Retrieval check:

  * Correctly explained the overall page-table/MMU/page-fault flow.
  * Needed arithmetic correction for a 4 KB page example:

    * address `8200`
    * page size `4096`
    * virtual page `2`
    * offset `8`
  * Retained the key invariant that translation changes the page/frame number while preserving the offset.

**Current memory evidence:** Core virtual-memory and paging model is understood. Arithmetic with page boundaries needs a little more fluency. TLB internals, page replacement algorithms, JMM, and GC remain intentionally deferred.

---

### HLD — URL Shortener V1

#### Functional requirements identified

* Create a short URL from a long URL.
* Resolve a short URL and redirect to the original URL.
* Consider URL lifetime/expiration.
* Consider domain and allowed short-code characters.

#### Non-functional requirements discussed

* High redirect volume.
* Read-heavy workload.
* Low redirect latency.
* High availability.
* Durable mappings.
* Globally unique short codes.

#### API model

Two primary operations:

1. Create short URL.
2. Redirect short URL to original URL.

#### Data model

Minimum mapping identified:

* unique ID
* short code
* long URL
* creation metadata
* optional active/deactivated state

#### Short-code generation

* Initially proposed encryption/random generation plus existence checking.

* Refined to separate:

  * **ID generation** — provides uniqueness.
  * **Base62 encoding** — provides compact representation.

* Understood Base62 as 62 symbols:

  `0-9 + a-z + A-Z`

* Understood that Base62 does not create uniqueness by itself.

* Discussed database-generated IDs for simple V1.

* Identified collision risk when independent shards generate the same numeric IDs.

* Understood a Snowflake-style structure conceptually:

  `timestamp + machine/shard ID + sequence`

* Correctly identified that machine/shard ID differentiates generators.

* Clarified that sequence synchronization is required only locally within a generator/time bucket, not globally.

#### Redirect/read path

Developed layered read architecture:

`Client -> CDN -> Application -> Redis -> Database`

* CDN can absorb traffic for very hot URLs before requests reach the application.
* Redis provides low-latency mapping lookup and protects the database.
* Database remains the source of truth.
* Cache misses fall through to the next layer.
* Discussed 301 versus temporary redirect behavior and the caching implications.
* Recognized that immutable short-code mappings dramatically reduce cache-invalidation complexity.

#### Failure handling

* Correctly identified that Redis failure can push unexpected traffic onto the database.
* Discussed graceful degradation:

  * Redis fallback to DB.
  * timeouts.
  * bounded concurrency.
  * circuit breakers.
  * load shedding.
* Understood that restart is an operational response, not the primary resilience architecture.
* For simultaneous cache and DB failure, system should fail in a controlled manner rather than allowing cascading resource exhaustion.
* Recognized CDN as an additional protection layer for hot URLs.

#### Hot-key handling

* Identified CDN + Redis as protection for viral short URLs.
* Discussed cache-stampede risk when a hot entry expires.
* Introduced request coalescing/single-flight and sensible TTL/refresh strategies.
* Rate limiting understood as a protection/load-shedding mechanism rather than the normal solution to legitimate popularity.

#### Partitioning

Considered:

* region-based placement
* time-based partitioning
* range partitioning
* hash-based partitioning

Refinements:

* Region is more appropriate for replication/data placement than as the primary ownership key.
* Time-based sharding can concentrate writes on the newest shard.
* Range sharding is simple but may become uneven.
* Hash-based partitioning generally distributes records more evenly.

Identified the resizing problem with:

`hash(key) % N`

When `N` changes, many existing mappings move to different shards.

Introduced consistent hashing conceptually as a way to reduce data movement when shard membership changes.

#### HLD retrieval

Without looking back, reconstructed:

* creation API
* redirect API
* Base62 purpose
* Redis role
* CDN role
* database fallback
* core redirect path

Minor corrections required:

* persistence should precede treating cache as authoritative.
* CDN sits before the application.
* Base62 has 62 symbols, not 61.
* Short codes do not necessarily need to remain exactly seven characters.

**Current HLD evidence:** URL Shortener V1 fundamentals are understood. User is beginning to reason from NFRs toward caching, failure handling, ID generation, and partitioning rather than only drawing CRUD architecture.

---

### Production Debugging

Incident characteristics:

* Traffic approximately unchanged.
* CPU approximately normal.
* p50 approximately normal.
* p99 increased dramatically.
* Error rate increased slightly.

Demonstrated reasoning:

* Recognized that normal p50 with severely degraded p99 indicates a minority/tail path is slow rather than the entire service uniformly degrading.

* First useful question identified:

  **Which endpoint/API is contributing to the p99 increase?**

* Considered Redis/cache degradation as a hypothesis.

* Understood that a Redis restart/cold cache could cause DB load amplification.

* Improved reasoning from "guess likely dependency" toward:

  * isolate endpoint
  * isolate downstream dependency/span
  * verify Redis hit rate/latency
  * verify DB QPS/latency/connection-pool behavior
  * isolate query
  * inspect execution plan
  * identify recent changes

* Correctly recognized that an overall DB p99 increase may originate from only one API/query.

* For a slow search query, initially proposed replacing SQL with Solr/search infrastructure.

* Refined debugging discipline:

  * inspect the existing SQL/query plan first
  * determine why the regression occurred
  * redesign only if the workload fundamentally requires a search engine

* Discussed causes for an index no longer being used:

  * index removed/changed
  * query shape changed
  * functions/casts applied to indexed columns
  * composite-index mismatch
  * stale/changing statistics
  * changed selectivity/data distribution

* Understood that:

  * an index existing does not guarantee that the optimizer will use it
  * optimizer/query hints are database-specific
  * forcing an index should not be the first response

* Retrieval check:

  * Correctly started by segmenting p99 by endpoint.
  * Still has a tendency to jump from a plausible hypothesis directly to a presumed root cause.
  * Needs continued practice explicitly validating hypotheses with metrics/traces before declaring causality.

**Current debugging evidence:** Strong instinct for likely bottlenecks and useful first segmentation. Primary improvement area is disciplined evidence-driven narrowing before proposing fixes.

---

### Rate Limiter — Day 11 Closure

Conceptual test cases demonstrated:

* First request accepted.
* Requests up to threshold accepted.
* Threshold + 1 rejected.
* Rejected requests must not increment the accepted-request count.
* Exact fixed-window boundary resets the effective count.
* Per-user isolation understood.
* Recognized that the rate-limit key may instead be tenant/org scoped depending on business requirements.
* Invalid configuration such as non-positive limit/window size should fail fast.

**Important evidence limitation:** Automated rate-limiter test code/results were not demonstrated during this session. Do not record the implementation test suite as passing until test output or equivalent evidence is shown.

---

## Day 11 Overall Assessment

### Demonstrated strengths

* Stronger ability to derive solutions through invariants rather than only memorize patterns.
* Good system-design instincts around caching, high-read workloads, hot keys, failure chains, and sharding concerns.
* Core virtual-memory model is now coherent across JVM heap, virtual memory, pages, frames, RAM, and CPU cache.
* Production-debugging instincts are strong at identifying plausible bottlenecks and segmenting problems.

### Current improvement areas

1. **DSA precision**

   * Preserve the exact invariant/equation when explaining a derived solution.
   * Re-test prefix-sum transfer after several days on a different problem.

2. **HLD discipline**

   * Continue separating functional requirements, NFRs, data constraints, and architecture decisions.
   * Avoid prematurely choosing technology before isolating the requirement that necessitates it.

3. **Production debugging**

   * Do not convert a plausible hypothesis into a root cause without evidence.
   * Explicitly state the metric/trace/log/query-plan evidence that would confirm or reject each hypothesis.

4. **Memory arithmetic**

   * Improve fluency with page number and offset calculations.

### Deferred intentionally

* Trees / heaps
* Graphs
* Binary search on answer
* Rotated-array binary-search variants
* Rolling-window rate limiter
* Token bucket
* Redis/distributed rate limiter
* Java Memory Model
* Garbage-collection deep dive
* TLB internals
* Page-replacement algorithms
* Multi-region URL Shortener
* Distributed ID-generation deep dive
* URL analytics pipeline

### Phase readiness

Day 11 is complete, but **Phase 2 readiness should not be declared solely from day completion**.

Recommended evidence before phase advancement:

* Successful spaced retrieval of prefix-sum/frequency-map reasoning on a different problem.
* Automated Fixed-Window Rate Limiter tests demonstrated.
* At least one additional HLD problem derived with materially less prompting.
* Continued evidence-driven production-debugging exercise where hypotheses are validated before fixes are proposed.
