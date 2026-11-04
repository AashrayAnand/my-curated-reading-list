# High-Performance Database Storage: LSM Engines and Disaggregated Systems

A reading-only path through **RocksDB, PolarFS, and the performance implications of
storage-disaggregated databases**, with WiscKey and Aurora as optional paper comparisons.
The focus is the database storage path: data layout, compaction, caching, network I/O,
replication, and log replay.

The central question: **When an architecture removes one kind of work, where does the remaining
work move, and what becomes the next bottleneck?**

This is a standalone reference, not extra work on top of the active database track.
There are no implementation exercises.

## Reading order and time budget

| Order | Resource | Main lens | Suggested time |
| --- | --- | --- | --- |
| 1 | RocksDB overview and selected tuning-guide sections | Local storage-engine costs and amplification | 45-60 min |
| 2 | PolarFS (PVLDB 2018) | Low-latency shared storage and replicated I/O | 60-75 min |
| 3 | Understanding the Performance Implications... (SIGMOD 2024) | Separate disaggregation's design choices through experiments | 60-75 min |
| Optional | WiscKey (FAST 2016) | Separate keys and values to reduce compaction traffic | 45-60 min |
| Optional | Aurora (SIGMOD 2017) | Move redo processing into a distributed storage service | 45-60 min |

The core takes **165-210 minutes**, roughly four or five 45-minute sessions. With both optional
papers, allow **255-330 minutes** across several weeks. Times are for focused first passes,
not exhaustive study of every experiment.

**Source access:** All external links below were fetched successfully on November 4, 2026.
Direct university and conference PDF fetches failed DNS resolution in this environment.
For PolarFS, the link therefore opens the official project's publication listing, not a PDF.
For the SIGMOD study, it opens the authors' project citation, which contains the university-hosted
paper link; that downstream PDF was not verified. WiscKey and Aurora use accessible PDF mirrors.
The summaries of the two unavailable PDFs draw on indexed abstracts and excerpts, not a full read.

## 1. RocksDB: Build a Storage-Engine Cost Model

- [ ] Read the [RocksDB Overview](https://github.com/facebook/rocksdb/wiki/RocksDB-Overview),
  then selected sections of the [RocksDB Tuning Guide](https://github.com/facebook/rocksdb/wiki/RocksDB-Tuning-Guide).

**Type:** Official documentation

**Tags:** Foundational, Practical, Mental Model

**Time:** 45-60 min

**Why read this:** RocksDB makes the local costs concrete before adding a network and remote
storage nodes. It is an embedded LSM-based key-value engine, not by itself a distributed database.

**Read closely:** The write/read paths in the overview; then the tuning guide's statistics,
performance bottlenecks, amplification factors, and flush/compaction discussion. Skip the
option-by-option catalog on the first pass.

**Concepts to extract:**

- Follow a write through the WAL, memtable, flush, and later compactions. Acknowledging a write
  and finishing all of its background storage work are different events.
- Distinguish write amplification, read amplification, and space amplification. State which
  layer and measurement each ratio covers; engine writes are not the same as SSD-internal writes.
- Separate logical lookups from physical reads. Block caching and Bloom filters can reduce I/O,
  but still consume memory and CPU.
- Background compaction competes with foreground work. Compaction debt can produce write stalls
  and slow reads even when an initial ingestion benchmark looks excellent.
- Diagnose the saturated resource before choosing a setting. More background threads are not
  automatically useful when storage bandwidth or CPU is already exhausted.

**Question to carry forward:** If a logical write is small, how many bytes and CPU cycles does it
cause over its lifetime, rather than only before acknowledgment?

**Prior coverage:** The overview already appears in the database intro and industry-SOTA pages.
Treat it as a short refresher if already read; the new emphasis is the tuning guide's cost model.

## 2. PolarFS: Make Remote Shared Storage Fast

- [ ] Read **PolarFS: An Ultra-low Latency and Failure Resilient Distributed File System for
  Shared Storage Cloud Database**. The [official PolarDB FileSystem publication listing](https://github.com/ApsaraDB/PolarDB-FileSystem#publications)
  identifies the paper; it does not host the PDF.

**Authors:** Wei Cao et al.

**Venue:** PVLDB 11(12), 2018

**Tags:** Pattern Recognition, Historical Context, Mental Model

**Time:** 60-75 min

**Why read this:** PolarFS connects database-aware shared storage with low-level systems
optimization. It asks how much software and coordination overhead can be removed from a
replicated remote I/O path.

**Read closely:** Architecture and I/O path, use of RDMA/NVMe/SPDK, the ParallelRaft design,
and experiments separating latency from throughput as I/O concurrency changes.

**Concepts to extract:**

- RDMA and user-space networking/storage stacks target different sources of overhead.
  Separate data movement, kernel crossings, CPU work, and network round trips.
- Replication introduces ordering and acknowledgment dependencies even when the device is fast.
- ParallelRaft exploits database tolerance for some out-of-order I/O completion. Identify the
  dependencies that still constrain execution; this is not permission to reorder arbitrary writes
  or a substitute for reasoning about failure recovery.
- Shared durable storage does not by itself make compute-node caches coherent or provide
  transaction isolation. Keep filesystem guarantees separate from database guarantees.
- Compare the reported hardware, I/O sizes, queue depths, and durability settings before treating
  near-local latency as a general property of disaggregation.

**Question to carry forward:** Which part of the end-to-end write latency comes from moving bytes,
which from software execution, and which from waiting for a safe replicated result?

## 3. SIGMOD 2024: Test the Design Principles Separately

- [ ] Read **Understanding the Performance Implications of the Design Principles in
  Storage-Disaggregated Databases** via the [authors' OpenAurora citation and paper reference](https://github.com/purduedb/OpenAurora#citation).

**Authors:** Xi Pang and Jianguo Wang

**Venue:** Proceedings of the ACM on Management of Data 2(3), SIGMOD 2024, Article 180

**Tags:** Mental Model, Pattern Recognition, Practical

**Time:** 60-75 min

**Why read this:** A whole-system benchmark cannot tell you which architectural choice caused
an improvement. This study uses a PostgreSQL 13-based prototype to investigate individual
design principles rather than treating disaggregation as a single optimization.

**Read closely:** Experimental methodology, the progression from disaggregation to
log-as-the-database and shared storage, and the comparisons of log-replay methods.

**Concepts to extract:**

- Distinguish moving existing storage I/O across a network from redesigning what crosses that
  network. Shipping redo rather than page images changes both traffic and storage-node work.
- Track where pages are reconstructed and which work a cache miss triggers. Lower write-path
  traffic can coexist with extra read-path reconstruction cost.
- Compare replay strategies by foreground latency, background work, replay backlog, and
  parallelism, not just average transaction throughput.
- Ask what shared storage saves across compute replicas and what it leaves unresolved:
  page visibility, cache freshness, and the version a reader requires.
- Treat the prototype's ablations as evidence about mechanisms under the tested conditions,
  not as a ranking of the closed-source production systems it draws ideas from.

**Question to carry forward:** If a design improves throughput, can you attribute the gain to
less work, better overlap, shared work, or merely deferred work?

## Optional Comparisons: Two More Papers

### WiscKey: Separating Keys from Values in SSD-conscious Storage

- [ ] Read the [open-access paper mirror](https://github.com/huachaohuang/awesome-dbdev/blob/master/papers/wisckey.pdf).

**Authors / venue:** Lanyue Lu et al., FAST 2016

**Tags:** Foundational, Pattern Recognition

**Time:** 45-60 min

**Why read this:** A clean contrast with the RocksDB cost model: keep keys and value locations in
the LSM tree while storing values in a separate log, reducing repeated value movement during
compaction. Read the design, range-query handling, garbage collection, and crash-consistency
discussion. Savings depend on key/value sizes and workload; value-log reclamation and scattered
range reads are costs, not implementation details to ignore.

**Comparison question:** Did the design eliminate byte movement, or trade compaction traffic for
garbage collection and less-local value reads?

### Amazon Aurora: Design Considerations for High Throughput Cloud-Native Relational Databases

- [ ] Read the [open-access paper mirror](https://github.com/huachaohuang/awesome-dbdev/blob/master/papers/aurora-2017.pdf).

**Authors / venue:** Alexandre Verbitski et al., SIGMOD 2017

**Tags:** Historical Context, Pattern Recognition, Mental Model

**Time:** 45-60 min

**Why read this:** Contrast PolarFS's shared-filesystem approach with a storage service that
processes database redo. Focus on the compute/storage boundary, network traffic, durability
quorums, page reconstruction, and recovery. Storage replication and a durable commit do not by
themselves establish transaction isolation or unlimited write scaling.

**Prior coverage:** Aurora is already assigned in the database intro and industry-SOTA paths.
This is an optional comparison pass, not a new core assignment.

**Comparison question:** What crosses the network in Aurora versus PolarFS, and which database
responsibilities must move to storage to make that interface work?

## Synthesis: Compare Mechanisms, Not Product Names

These resources operate at different layers; RocksDB and WiscKey are not alternatives to an
entire cloud database.

| Layer / choice | Potential saving | Cost or constraint to check |
| --- | --- | --- |
| RocksDB: LSM buffering and compaction | Batch updates and organize persistent data | Read/write/space amplification, CPU, compaction debt |
| WiscKey: separate keys and values | Avoid repeatedly compacting large values | Value-log garbage collection, range-read locality |
| PolarFS: optimized replicated shared I/O | Reduce software overhead and expose I/O parallelism | Network, replication dependencies, failure handling |
| Aurora: redo-oriented storage interface | Reduce page-shipping work on the compute side | Storage-side replay and page reconstruction |
| SIGMOD study: vary design principles | Attribute gains and regressions to mechanisms | Prototype scope, workload, cache state, deferred work |

For each evaluation, identify **cache state, dataset size relative to memory, read/write mix,
value size, concurrency, durability guarantees, and whether background work reached steady state**.
Keep throughput separate from p95/p99 latency, and steady-state behavior separate from recovery.

After reading, be able to explain why faster devices can expose CPU bottlenecks, why fewer
network bytes do not guarantee lower read latency, and why more buffered work is not the same
as higher sustainable capacity.

## Related Paths

- [Disk I/O, Blocking vs. Async I/O, and Rust Async Runtimes](disk-io-blocking-vs-async-and-rust-runtimes.md)
- [Buffer Pool Management and Application Memory](buffer-pool-management-and-app-memory.md)
- [Concurrency at Scale](concurrency-at-scale.md)
- [Database Expertise: Industry SOTA](../database-expertise/industry-sota.md)
