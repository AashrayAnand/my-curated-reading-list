# Backlog

Readings that were scheduled but not completed in their target week. These should be prioritized in future weeks or revisited when the topic comes up again.

## From Week 1

- [ ] **Dynamo: Amazon's Highly Available Key-value Store** (SOSP 2007)
  - 🔗 [Paper (PDF)](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf)
  - 🔗 [Companion blog](https://distributed-computing-musings.com/2022/05/paper-notes-dynamo-amazons-highly-available-key-value-store/)
  - *Original week*: Week 1
  - *Topic*: Distributed KV stores, eventual consistency, consistent hashing, vector clocks

- [ ] **mimalloc: Free List Sharding in Action** — Daan Leijen (Microsoft Research)
  - 🔗 [Paper (PDF)](https://www.microsoft.com/en-us/research/uploads/prod/2019/06/mimalloc-tr-v1.pdf)
  - *Original week*: Week 1
  - *Topic*: Allocator internals, thread-local heaps, free list sharding, segment/page layout

- [ ] **The Illustrated Transformer** — Jay Alammar
  - 🔗 [Blog post](https://jalammar.github.io/illustrated-transformer/)
  - 📄 *Supplementary*: [Attention Is All You Need (arxiv:1706.03762)](https://arxiv.org/abs/1706.03762)
  - *Original week*: Week 1
  - *Topic*: Transformer architecture, self-attention, multi-head attention, positional encoding
  - *Reason deferred*: Need to build math/ML fundamentals (linear algebra, gradients, loss functions, backprop) before this can be properly digested. Revisit after ~Weeks 8-9.

## From Week 2

- [ ] **The Effect of Switching to TCMalloc on RocksDB Memory Use** — Cloudflare
  - 🔗 [Blog post](https://blog.cloudflare.com/the-effect-of-switching-to-tcmalloc-on-rocksdb-memory-use/)
  - *Original week*: Week 2
  - *Topic*: RSS vs heap, glibc malloc fragmentation, allocator choice impact on memory — companion to mimalloc paper
  - *Reason deferred*: Week 2 theme shifted to formal reasoning and specification; revisit with allocator deep dive

- [ ] **Lock-Free Data Structures with Hazard Pointers** — Andrei Alexandrescu & Maged Michael (Dr. Dobb's, 2004)
  - 🔗 [Article](https://erdani.org/publications/cuj-2004-12.pdf)
  - *Original week*: Week 2
  - *Topic*: ABA problem, hazard pointers, why naïve CAS on lock-free stacks breaks
  - *Reason deferred*: Revisit in ~4 weeks when lock-free data structures topic comes up naturally

- [ ] **Lock-Freedom Without Garbage Collection** — Keir Fraser (2004, Chapter 3 of PhD thesis)
  - 🔗 [Thesis PDF](https://www.cl.cam.ac.uk/techreports/UCAM-CL-TR-579.pdf) — Chapter 3
  - *Original week*: Week 2
  - *Topic*: Epoch-based reclamation (EBR), ABA solution taxonomy, crossbeam-epoch foundation
  - *Reason deferred*: Revisit in ~4 weeks with hazard pointers reading

- [ ] **Crossbeam: Epoch-Based Memory Reclamation** — Aaron Turon
  - 🔗 [Blog post](https://aturon.github.io/blog/2015/08/27/epoch/)
  - *Original week*: Week 2
  - *Topic*: Rust's crossbeam epoch-based reclamation, why ownership alone doesn't solve lock-free memory management
  - *Reason deferred*: Revisit in ~4 weeks with the lock-free readings bundle

- [ ] **An Opinionated Map of Incremental and Streaming Systems** — Jamie Brandon
  - 🔗 [Blog post](https://www.scattered-thoughts.net/writing/an-opinionated-map-of-incremental-and-streaming-systems)
  - *Original week*: Week 2
  - *Topic*: Taxonomy of streaming/incremental computation — Flink, Kafka Streams, Materialize, Differential Dataflow
  - *Reason deferred*: Good reference but not urgent; revisit when streaming systems become more relevant

## Future Topics: Formal Methods in Practice

- [ ] **Using Lightweight Formal Methods to Validate a Key-Value Storage Node in Amazon S3** (SOSP 2021)
  - 🔗 [Paper (PDF)](https://assets.amazon.science/77/5e/4a7c238f4ce890efdc325df83263/using-lightweight-formal-methods-to-validate-a-key-value-storage-node-in-amazon-s3-2.pdf)
  - *Topic*: Applying lightweight formal methods (property-based testing, reference models, conformance checking) to validate ShardStore, the KV storage node in S3
  - *Why*: Direct example of formal methods applied to a production storage system at scale, from the team that runs S3

## Future Topics: Linkers, Loaders, and Dynamic Linking

Deep dive into how executables are built, linked, and loaded — covering ELF structure, symbol tables, position-independent code, the PLT/GOT mechanism, and how shared libraries work across processes.

- [ ] **"Linkers & Loaders"** — John Levine
  - 📖 [Book](https://www.amazon.com/Linkers-Loaders-John-R-Levine/dp/1558604960)
  - *Topic*: The definitive reference on linking and loading — static/dynamic linking, symbol resolution, relocation, object file formats

- [ ] **Eli Bendersky's Linkers & Loaders series**
  - 🔗 [Load-time relocation of shared libraries](https://eli.thegreenplace.net/2011/08/25/load-time-relocation-of-shared-libraries)
  - 🔗 [Position Independent Code on x64](https://eli.thegreenplace.net/2011/11/03/position-independent-code-pic-in-shared-libraries)
  - 🔗 [Position Independent Code on x64 — PLT and GOT](https://eli.thegreenplace.net/2011/11/11/position-independent-code-pic-in-shared-libraries-on-x64)
  - *Topic*: PIC, GOT/PLT mechanics, how shared libraries achieve position independence, lazy vs eager binding

- [ ] **"How To Write Shared Libraries"** — Ulrich Drepper (Red Hat)
  - 🔗 [Paper (PDF)](https://www.akkadia.org/drepper/dsohowto.pdf)
  - *Topic*: ELF shared library internals from the glibc maintainer — symbol visibility, versioning, optimization techniques for shared objects

- [ ] **Ian Lance Taylor's 20-part linker series**
  - 🔗 [Part 1: Introduction](https://www.airs.com/blog/archives/38)
  - *Topic*: Comprehensive ground-up explanation of linkers by the author of the gold linker — object files, symbol resolution, relocation, shared libraries, TLS, linker scripts

- [ ] **LWN.net: "How programs get run"**
  - 🔗 [Article](https://lwn.net/Articles/631631/)
  - *Topic*: The `execve` → kernel → dynamic linker → PLT resolution chain, how a program goes from disk to running process

## Future Topics: 25 Years of Warehouse-Scale Computing

Selected papers from the "25 papers for 25 years of warehouse-scale computing" series by Luiz Barroso and Urs Hölzle. Organized by theme. Full podcast playlist [here](https://lnkd.in/gjfisdYf).

### Search & Data Processing Foundations

- [ ] **The Anatomy of a Large-Scale Hypertextual Web Search Engine** (Computer Networks, 1998)
  - 🔗 [Post](https://lnkd.in/gaDxf3hb)
  - *Topic*: Original Google architecture — crawling, indexing, PageRank, serving

- [ ] **Web Search for a Planet: The Google Cluster Architecture** (IEEE Micro, 2003)
  - 🔗 [Post](https://lnkd.in/guqFuiEN)
  - *Topic*: Commodity clusters, replication, reliability through software, cost-efficient serving

- [ ] **MapReduce: Simplified Data Processing on Large Clusters** (OSDI, 2004)
  - 🔗 [Post](https://lnkd.in/gyY6AsDD)
  - *Topic*: Distributed batch processing, map/reduce programming model, fault tolerance

- [ ] **Dremel/BigQuery: Interactive Analysis of Web-Scale Datasets** (VLDB, 2010)
  - 🔗 [Post](https://lnkd.in/gtqDCNpC)
  - *Topic*: Columnar storage, nested data, multi-level execution trees, interactive SQL at scale

### Storage & Database Systems

- [ ] **The Google File System** (SOSP, 2003)
  - 🔗 [Post](https://lnkd.in/gFUZCa_2)
  - *Topic*: Append-optimized distributed file system, chunk servers, master failover, relaxed consistency

- [ ] **Bigtable: A Distributed Storage System for Structured Data** (OSDI, 2006)
  - 🔗 [Post](https://lnkd.in/gezsGFez)
  - *Topic*: Sorted key-value store, tablet serving, LSM-tree compaction, Chubby integration

- [ ] **The Chubby Lock Service for Loosely-Coupled Distributed Systems** (USENIX, 2006)
  - 🔗 [Post](https://lnkd.in/gtrUHazT)
  - *Topic*: Distributed consensus, coarse-grained locks, leader election, Paxos-based replication

- [ ] **Spanner: Google's Globally-Distributed Database** (OSDI, 2012)
  - 🔗 [Post](https://lnkd.in/gBjJF7W2)
  - *Topic*: External consistency, TrueTime, globally-distributed transactions, synchronous replication

### Networking

- [ ] **B4: Experience with a Globally-Deployed Software Defined WAN** (SIGCOMM, 2013)
  - 🔗 [Post](https://lnkd.in/ggC8GKNy)
  - *Topic*: Software-defined WAN, centralized traffic engineering, bandwidth allocation across datacenters

- [ ] **Andromeda: Performance, Isolation, and Velocity at Scale in Cloud Network Virtualization** (NSDI, 2018)
  - 🔗 [Post](https://lnkd.in/guTU4ccq)
  - *Topic*: Network virtualization, offloading, host-based packet processing, isolation at cloud scale

- [ ] **Snap: a Microkernel Approach to Host Networking** (SOSP, 2019)
  - 🔗 [Post](https://lnkd.in/gR8-__PW)
  - *Topic*: User-space networking, microkernel isolation, CPU scheduling for network processing

- [ ] **Swift: Delay is Simple and Effective for Congestion Control in the Datacenter** (SIGCOMM, 2020)
  - 🔗 [Post](https://lnkd.in/gQDHVVtD)
  - *Topic*: Delay-based congestion control, fabric vs endpoint delay, datacenter transport

- [ ] **Jupiter Rising: A Decade of Clos Topologies and Centralized Control in Google's Datacenter Network** (SIGCOMM, 2022)
  - 🔗 [Post](https://lnkd.in/gqM7pEqW)
  - *Topic*: Clos network evolution, centralized SDN control, optical circuit switching, capacity planning

### Observability & Reliability

- [ ] **Dapper: a Large-Scale Distributed Systems Tracing Infrastructure** (Google Tech Report, 2010)
  - 🔗 [Post](https://lnkd.in/gWbBFJUi)
  - *Topic*: Distributed tracing, trace context propagation, sampling strategies, low-overhead instrumentation

- [ ] **Google-Wide Profiling: A Continuous Profiling Infrastructure for Data Centers** (IEEE Micro, 2010)
  - 🔗 [Post](https://lnkd.in/gjk7Medk)
  - *Topic*: Always-on fleet-wide profiling, hardware performance counters, optimization at scale

- [ ] **Profiling a Warehouse-Scale Computer** (ISCA, 2015)
  - 🔗 [Post](https://lnkd.in/gnjH5xQ8)
  - *Topic*: Datacenter tax (serialization, compression, memory allocation), workload characterization, microarchitectural optimization

- [ ] **The Tail at Scale** (CACM, 2013)
  - 🔗 [Post](https://lnkd.in/g_PqCuRs)
  - *Topic*: Tail latency, hedged requests, tied requests, latency variability in large fan-out systems

- [ ] **Site Reliability Engineering** (O'Reilly, 2016)
  - 🔗 [Book](https://lnkd.in/gDJN-YGw)
  - *Topic*: SRE practices, error budgets, SLOs, toil reduction, incident management

### Scheduling, Power, & Infrastructure

- [ ] **Energy Proportional Computing** (IEEE Computer, 2007)
  - 🔗 [Post](https://lnkd.in/gXhpCsCV)
  - *Topic*: Energy efficiency at low utilization, power proportionality, implications for server design

- [ ] **Large-scale Cluster Management at Google with Borg** (EuroSys, 2015)
  - 🔗 [Post](https://lnkd.in/gXxFJmJD)
  - *Topic*: Cluster scheduling, resource packing, priority/preemption, allocs, job lifecycle

- [ ] **Thunderbolt: Throughput-Optimized, Quality-of-Service-Aware Power Capping at Scale** (OSDI, 2019)
  - 🔗 [Post](https://lnkd.in/gnQWvnDt)
  - *Topic*: Power oversubscription, dynamic power capping, QoS-aware throttling, datacenter power management

- [ ] **Killer Microseconds** (CACM, 2017)
  - 🔗 [Post](https://lnkd.in/g6NnsG4J)
  - *Topic*: Microsecond-scale latency gap, hardware/software co-design, implications for storage and networking

### Hardware Acceleration & ML Systems

- [ ] **In-Datacenter Performance Analysis of a Tensor Processing Unit** (ISCA, 2017)
  - 🔗 [Post](https://lnkd.in/gVH_72cY)
  - *Topic*: TPU architecture, systolic arrays, inference acceleration, comparison with GPU/CPU

- [ ] **Warehouse-Scale Video Acceleration** (ASPLOS, 2021)
  - 🔗 [Post](https://lnkd.in/gRRpzNe4)
  - *Topic*: Video transcoding ASICs, fleet-wide deployment, co-design of hardware and software

- [ ] **Pathways: Asynchronous Distributed Dataflow for ML** (MLSys, 2022)
  - 🔗 [Post](https://lnkd.in/gzZm77uG)
  - *Topic*: Multi-controller ML training, gang-scheduled accelerators, sharded dataflow, resource multiplexing

📖 *Companion book*: [The Datacenter as a Computer (3rd ed.)](https://lnkd.in/gZBX89UE)
