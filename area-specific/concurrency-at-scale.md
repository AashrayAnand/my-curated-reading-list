# Concurrency at Scale: Synchronization, Batching, and Backpressure

A focused reading path on making concurrent servers efficient without losing fairness,
bounded resource use, or correctness.

The common question is not just "which lock should I use?" It is:
**How much coordination does each unit of useful work require, and where should that work run?**

These four resources approach that question through synchronization, staged execution,
database logging, and data ownership. This is a standalone reference path, not an addition
to the weekly schedule.

## Reading order and time budget

Allow about 3-4 hours, split across several sessions or two weeks.
Start with Flat Combining for synchronization mechanics, then SEDA for the larger pipeline.

| Order | Resource | Main lens | Suggested time |
| --- | --- | --- | --- |
| 1 | Flat Combining | Amortize coordination across operations | 45-60 min |
| 2 | SEDA | Control queues, stages, and overload | 45-60 min |
| 3 | Aether | Remove bottlenecks across a logging pipeline | 45-60 min |
| 4 | Seastar tutorial | Reduce sharing through explicit ownership | 30-45 min |

## 1. Flat Combining and the Synchronization-Parallelism Tradeoff

- [ ] Read the [open-access author-hosted paper](https://people.csail.mit.edu/shanir/publications/Flat%20Combining%20SPAA%2010.pdf).

**Authors:** Danny Hendler, Itai Incze, Nir Shavit, Moran Tzafrir  
**Venue:** SPAA 2010  
**Tags:** Foundational, Pattern Recognition, Mental Model

**Why read it:** Fine-grained locking and lock-free algorithms are not automatically the fastest
choices. Coordination itself can cost more than the operations it protects.

**Core mechanism:** Threads publish operation requests. A thread that becomes the combiner
processes several requests while holding one lock, then publishes their results.

**Key takeaways:**

- Amortize lock acquisition, memory synchronization, and shared-data access across multiple operations.
- A sequential batch can outperform finer-grained parallel execution when coordination costs dominate.
- Locality matters alongside contention. Reusing a working set can be as important as avoiding a blocked waiter.
- The combiner can become a bottleneck. Large batches or a delayed combiner can hurt latency and fairness.
- Ordinary batching by one worker is not the full flat-combining algorithm. Distinguish the general principle from the specific design.

**Read closely:** The publication mechanism, the combiner's work, and the experimental conditions
under which combining wins or loses.

**Question to carry forward:** Is an operation expensive because of its useful computation,
its synchronization, or the repeated movement of shared state?

## 2. SEDA: An Architecture for Well-Conditioned, Scalable Internet Services

- [ ] Read the [original conference paper](https://www.sosp.org/2001/papers/welsh.pdf).

**Authors:** Matt Welsh, David Culler, Eric Brewer  
**Venue:** SOSP 2001  
**Tags:** Foundational, Historical Context, Mental Model

**Why read it:** A concurrent server is a pipeline of resources and queues, not just a set of
individually fast functions.

**Core mechanism:** Divide a service into stages connected by explicit queues.
Control each stage's resources and admission policy using feedback.

**Key takeaways:**

- Separate queueing delay from service time. A large queue is evidence of an imbalance, not a complete diagnosis.
- Bounded queues make overload visible and constrain resource use.
- Moving a backlog to another stage does not necessarily increase end-to-end capacity.
- Batching trades fixed-cost amortization and locality against latency and fairness.
- Admission and overload policies must match the service's semantics. Lossless processing cannot freely discard accepted work.
- Stage boundaries are a reasoning tool; they do not require a dedicated operating-system thread for every stage.

**Read closely:** Stage organization, resource controllers, batching, and behavior under overload.

**Question to carry forward:** If one queue becomes shorter, did the system finish work faster,
or did the waiting move elsewhere?

## 3. Aether: A Scalable Approach to Logging

- [ ] Read the [official open-access paper](https://www.vldb.org/pvldb/vol3/R61.pdf).

**Authors:** Ryan Johnson, Ippokratis Pandis, Radu Stoica, Manos Athanassoulis, Anastasia Ailamaki  
**Venue:** PVLDB 2010  
**Tags:** Pattern Recognition, Historical Context, Practical

**Why read it:** Logging combines memory coordination, ordering, durability, I/O, and transaction
wakeups. Improving only one component can expose another bottleneck.

**Core mechanism:** Treat logging as a pipeline and combine techniques such as log-insert
consolidation, flush pipelining, and early lock release with the required correctness machinery.

**Key takeaways:**

- Shared in-memory logging structures can limit scalability before storage bandwidth is exhausted.
- Consolidating insertions can reduce repeated synchronization around a shared log buffer.
- Grouping flush work and reducing unnecessary context switches can matter as much as faster I/O.
- Early lock release requires correct dependency and durability ordering. It is not permission to weaken commit guarantees.
- Evaluate the complete transaction/logging path rather than optimizing one microbenchmark in isolation.

**Read closely:** The separate sources of logging contention, how the techniques interact, and
the ordering conditions that make them safe.

**Question to carry forward:** Which coordination costs are paid per record, per transaction,
or per flush, and which can be amortized?

## 4. Seastar: Ownership and Cooperative Execution

- [ ] Read selected sections of the [official Seastar tutorial](https://docs.seastar.io/master/tutorial.html).

**System:** Seastar, an asynchronous C++ framework used by ScyllaDB  
**Tags:** Practical, Mental Model, Pattern Recognition

**Why read it:** Instead of improving every shared lock, an architecture can reduce how much
mutable state is shared in the first place.

**Core mechanism:** Partition ownership across cores and use explicit communication for work
that crosses an ownership boundary.

**Key takeaways:**

- Shared mutable state carries cache-coherence, atomic-operation, and synchronization costs.
- Per-core ownership can reduce those costs, but cross-core messages and uneven load become explicit design concerns.
- Futures describe asynchronous progress; they do not make blocking operations or long CPU loops harmless.
- Cooperative execution requires bounded work and opportunities for other tasks to run.
- Lifetime, failure, and shutdown behavior remain part of the design when work crosses asynchronous boundaries.
- Shared-nothing is a tradeoff, not a universal rule. Compare communication costs and load balance against the sharing costs removed.

**Read closely:** The introduction, asynchronous programming model, futures, and multicore programming sections.
This revisits a resource in the disk-I/O path with ownership and coordination as the primary lens.

**Question to carry forward:** Can one owner process this state efficiently, and what does
communication with that owner cost?

## Synthesis

| Design choice | Potential benefit | What to watch |
| --- | --- | --- |
| Combine operations under one acquisition | Lower coordination cost per operation | Longer holds and delayed peers |
| Add a bounded stage queue | Decouple stages and constrain overload | Extra waiting and memory |
| Increase concurrency | Overlap independent work | More contention and coordination |
| Give state a single owner | Less shared-state synchronization | Owner bottlenecks and cross-owner traffic |
| Pipeline ordered work | Overlap stages without discarding dependencies | Incorrect publication or completion ordering |

After reading, be able to explain:

- The difference between an uncontended synchronization cost and time waiting for another owner.
- Why more concurrency can reduce throughput.
- Why batching can improve throughput without proving that lock contention was the original bottleneck.
- How to distinguish higher service capacity from additional buffering.
- Which ordering, lifetime, and completion guarantees an optimization must preserve.

## Related paths

- [Lock-Free Programming and Memory Allocators](lock-free-programming-and-allocators.md)
- [Disk I/O, Blocking vs. Async I/O, and Rust Async Runtimes](disk-io-blocking-vs-async-and-rust-runtimes.md)

## Notes

- Most useful concept:
- A tradeoff that changed my mental model:
- An assumption I want to question:
- Follow-up reading:

Public resource links verified on October 1, 2026.
