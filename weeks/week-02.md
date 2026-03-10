# Week 2: Global Consistency & Understanding GPT from Scratch

**Time Budget**: ~170 min (45 min × 4 days)

---

## 📄 Anchor Paper: Spanner — Google's Globally Distributed Database

- [ ] **Spanner: Google's Globally-Distributed Database** (OSDI 2012)
  - 🔗 [Paper (PDF)](https://www.usenix.org/system/files/conference/osdi12/osdi12-final-16.pdf) — skim/refresh
  - 🔗 [Companion blog: Paper Notes on Spanner](https://distributed-computing-musings.com/2023/09/paper-notes-spanner-googles-globally-distributed-database/) — walkthrough of architecture and TrueTime
  - 🔗 [Deep dive: TrueTime Explained](https://sookocheff.com/post/time/truetime/) — focused explanation of TrueTime's clock uncertainty model
  - 📁 *Category*: Foundational, Historical Context
  - 💡 *Why*: Introduced TrueTime — using atomic clocks and GPS receivers to achieve globally-consistent distributed transactions without the traditional trade-offs. Spanner showed that you *can* have strong consistency at global scale, challenging the "pick two" CAP theorem intuition. One of the most referenced papers in Andy Pavlo's courses and a direct ancestor of CockroachDB, YugabyteDB, and TiDB. The companion blog and TrueTime deep dive are great refreshers if the paper is familiar.
  - ⏱️ *Est. time*: 45 min (skim paper + read companion blog + TrueTime deep dive)
  - 🎥 *Supplementary*: [Andy Pavlo — CMU 15-721: Google Spanner](https://www.youtube.com/watch?v=LKEPsV4kMpU)

---

## 🤖 AI Deep Dive: GPT in 60 Lines of NumPy

- [ ] **GPT in 60 Lines of NumPy** — Jay Mody
  - 🔗 [Blog post](https://jaykmody.com/blog/gpt-from-scratch/)
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: After the visual introduction from Week 1's Illustrated Transformer, this post takes it to the next level: a complete, working GPT-2 implementation in 60 lines of NumPy. It demystifies the actual computation — tokenization, embedding, attention, feed-forward layers, and text generation — making the transformer tangible as code rather than just diagrams. Perfect for a developer who thinks in code.
  - ⏱️ *Est. time*: 40 min
  - 💻 *Code*: [github.com/jaymody/picoGPT](https://github.com/jaymody/picoGPT)

---

## 💻 Tech Blogs

- [ ] **Building and Operating a Pretty Big Storage System Called S3** — Andy Warfield
  - 🔗 [Blog post](https://www.allthingsdistributed.com/2023/07/building-and-operating-a-pretty-big-storage-system.html)
  - 📁 *Category*: Pattern Recognition, Practical
  - 💡 *Why*: A VP/distinguished engineer at S3 reflects on what "scale" really means across three dimensions — hardware, operations, and customer experience. Connects directly to the GFS lineage you've already explored, but from the perspective of running the system in production for years. Real-world lessons you won't find in papers.
  - ⏱️ *Est. time*: 25 min
  - 🎥 *Supplementary*: [Andy Warfield — USENIX FAST '23 Keynote](https://www.youtube.com/watch?v=sc3J4McebHE) (embedded at end of the blog post)

- [ ] **Put Your Agents in a Box** — Marc Brooker
  - 🔗 [Blog post](https://brooker.co.za/blog/2026/01/12/agent-box.html)
  - 📁 *Category*: Mental Model, Cutting Edge
  - 💡 *Why*: Argues that the right way to control AI agents is deterministic, external sandboxing — not relying on alignment or prompting alone. A systems-engineering perspective on agent safety that resonates with how we think about fault isolation in distributed systems.
  - ⏱️ *Est. time*: 15 min

- [ ] **Natural Language is the Future of Programming** — Marc Brooker
  - 🔗 [Blog post](https://brooker.co.za/blog/2025/12/16/natural-language.html)
  - 📁 *Category*: Mental Model, Cutting Edge
  - 💡 *Why*: Makes the case that programming has always been converging toward specification, and natural language is the logical next step. Grapples with ambiguity head-on and connects to Lamport, Dijkstra, and formal methods. A thoughtful framing for where AI-assisted development is heading.
  - ⏱️ *Est. time*: 15 min

- [ ] **LLMs as Components, Not Whole Systems** — Marc Brooker
  - 🔗 [Blog post](https://brooker.co.za/blog/2025/08/12/llms-as-components.html)
  - 📁 *Category*: Mental Model, Practical
  - 💡 *Why*: Systems built *with* LLMs are far more capable than LLMs alone — the key insight that even trivial tool use (code interpreters, databases, browsers) creates systems orders of magnitude more powerful and cheaper than raw inference. Directly relevant to understanding how to build with AI effectively.
  - ⏱️ *Est. time*: 15 min

- [ ] **The Effect of Switching to TCMalloc on RocksDB Memory Use** — Cloudflare
  - 🔗 [Blog post](https://blog.cloudflare.com/the-effect-of-switching-to-tcmalloc-on-rocksdb-memory-use/)
  - 📁 *Category*: Practical, Debugging Story
  - 💡 *Why*: Real production debugging story: RSS was 3× the heap size due to glibc malloc fragmentation. Shows exactly the committed-vs-RSS gap in practice, how allocator choice impacts memory consumption, and why switching to tcmalloc cut memory use by almost 3×. A concrete companion to the mimalloc paper from Week 1.
  - ⏱️ *Est. time*: 15 min

- [ ] **An Opinionated Map of Incremental and Streaming Systems** — Jamie Brandon
  - 🔗 [Blog post](https://www.scattered-thoughts.net/writing/an-opinionated-map-of-incremental-and-streaming-systems)
  - 📁 *Category*: Mental Model, Cutting Edge
  - 💡 *Why*: A sharp taxonomy of the streaming/incremental computation landscape — Flink, Kafka Streams, Materialize, Differential Dataflow, and more. Clarifies what these systems actually do differently from each other and from batch systems. Great for building a mental map of a confusing space.
  - ⏱️ *Est. time*: 20 min

---

## 🌿 Perspective

- [ ] **Choose Boring Technology** — Dan McKinley
  - 🔗 [Blog post](https://mcfunley.com/choose-boring-technology)
  - 📁 *Category*: Mental Model, Practical
  - 💡 *Why*: The classic "innovation tokens" essay. Argues that every company has a limited budget for new technology, and you should spend it wisely rather than chasing novelty. A healthy counterbalance to the tech industry's bias toward shiny new things — especially relevant after reading about cutting-edge systems all week.
  - ⏱️ *Est. time*: 15 min

---

## 🔬 Bonus: Linux Observability with bpftrace

- [ ] **BPF Performance Tools** (Chapter 7: Memory, Chapter 8: File Systems) — Brendan Gregg
  - 🔗 [Book site](https://www.brendangregg.com/bpf-performance-tools-book.html)
  - 🔗 [bpftrace reference guide](https://github.com/bpftrace/bpftrace/blob/master/docs/reference_guide.md)
  - 🔗 [bpftrace one-liners tutorial](https://www.brendangregg.com/blog/2019-01-01/learn-ebpf-tracing.html)
  - 🔗 [Brendan Gregg's bpftrace cheat sheet](https://www.brendangregg.com/BPF/bpftrace-cheat-sheet.html)
  - 📁 *Category*: Practical, Debugging Toolkit
  - 💡 *Est. time*: 45 min (skim chapters + read one-liners tutorial + bookmark cheat sheet)

### Future bpftrace applications for Page Server profiling

**CPU profiling:**
- `uprobe:sf_psvr:*redo*` — profile time spent in redo functions per shard, identify hot redo code paths
- `tracepoint:sched:sched_switch` — measure involuntary context switches on page server threads (high counts indicate contention or CPU saturation)
- `profile:hz:99` with `ustack` — CPU sampling without `perf`, directly in bpftrace, filterable to specific threads by name (e.g. `comm == "ps-bg-dw"`)
- `uprobe` on XStore client functions — latency histograms for blob reads/writes, catch tail latencies

**Memory profiling:**
- `tracepoint:kmem:mm_page_alloc` — track kernel page faults to see when RSS is actually growing (not just what userspace allocates)
- `uprobe` on `mmap`/`munmap` — catch large anonymous mappings (e.g., pool resize, unexpected mmap from libraries)
- `tracepoint:kmem:rss_stat` — kernel-level RSS change events, pinpoints exactly which code path triggers RSS growth

**I/O and cache profiling:**
- `uprobe` on `disk_page_cache::get_page` / `disk_page_cache::put_page` — cache hit/miss ratios at function level, latency per operation
- `tracepoint:block:block_rq_issue` — disk I/O latency histograms for the NVMe cache, catch slow reads
- `uprobe` on XStore dirty writer flush path — bytes per flush, flush latency, backoff frequency

---

## 📅 Suggested Daily Schedule

| Day | Reading | Time |
|-----|---------|------|
| Mon | Spanner paper skim + companion blog + TrueTime deep dive | 45 min |
| Tue | S3 at Scale | 25 min + Choose Boring Technology | 15 min |
| Wed | GPT in 60 Lines of NumPy | 40 min |
| Thu | Streaming Systems Map | 20 min |

---

## 📝 Notes & Reflections

_Space for your thoughts after reading — what connected, what surprised you, what you want to dig deeper on._
