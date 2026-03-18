# Week 3: Spanner, Linear Algebra Foundations, & Linux Observability

**Time Budget**: ~150 min (lighter week — catching up from Week 2)

---

## 📄 Anchor Paper: Spanner — Google's Globally Distributed Database

> *Moved from Week 2*

- [ ] **Spanner: Google's Globally-Distributed Database** (OSDI 2012)
  - 🔗 [Paper (PDF)](https://www.usenix.org/system/files/conference/osdi12/osdi12-final-16.pdf) — skim/refresh
  - 🔗 [Companion blog: Paper Notes on Spanner](https://distributed-computing-musings.com/2023/09/paper-notes-spanner-googles-globally-distributed-database/) — walkthrough of architecture and TrueTime
  - 🔗 [Deep dive: TrueTime Explained](https://sookocheff.com/post/time/truetime/) — focused explanation of TrueTime's clock uncertainty model
  - 📁 *Category*: Foundational, Historical Context
  - 💡 *Why*: Introduced TrueTime — using atomic clocks and GPS receivers to achieve globally-consistent distributed transactions without the traditional trade-offs. Spanner showed that you *can* have strong consistency at global scale, challenging the "pick two" CAP theorem intuition. One of the most referenced papers in Andy Pavlo's courses and a direct ancestor of CockroachDB, YugabyteDB, and TiDB. The companion blog and TrueTime deep dive are great refreshers if the paper is familiar.
  - ⏱️ *Est. time*: 45 min (skim paper + read companion blog + TrueTime deep dive)
  - 🎥 *Supplementary*: [Andy Pavlo — CMU 15-721: Google Spanner](https://www.youtube.com/watch?v=LKEPsV4kMpU)

---

## 🤖 AI Fundamentals: Linear Algebra & Neural Network Basics

> *Moved from Week 2*

> **Note:** Based on feedback from Week 1, the AI reading plan is shifting to fundamentals. The goal is to spend 2-3 months building a solid foundation (linear algebra, calculus, basic ML, neural networks) before returning to transformer/LLM-specific content. GPT in 60 Lines of NumPy and Attention Is All You Need will return in a later week once the math is solid.

- [ ] **3Blue1Brown: Essence of Linear Algebra** (Chapters 1-4)
  - 🔗 [YouTube playlist](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab)
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: The best visual introduction to linear algebra — vectors, linear transformations, matrix multiplication, and determinants. Grant Sanderson's geometric intuition approach makes these concepts click in a way that textbooks rarely achieve. Matrix math is the substrate of every neural network: weights are matrices, inputs are vectors, and forward passes are matrix multiplications. Chapters 1-4 cover vectors, span, linear transformations, and matrix multiplication.
  - ⏱️ *Est. time*: 40 min (4 videos × ~10 min each)

---

## 🔬 Linux Observability with eBPF / bpftrace

> *Moved from Week 2*

- [ ] **BPF Performance Tools** (Chapter 7: Memory, Chapter 8: File Systems) — Brendan Gregg
  - 🔗 [Book site](https://www.brendangregg.com/bpf-performance-tools-book.html)
  - 🔗 [bpftrace reference guide](https://github.com/bpftrace/bpftrace/blob/master/docs/reference_guide.md)
  - 🔗 [bpftrace one-liners tutorial](https://www.brendangregg.com/blog/2019-01-01/learn-ebpf-tracing.html)
  - 🔗 [Brendan Gregg's bpftrace cheat sheet](https://www.brendangregg.com/BPF/bpftrace-cheat-sheet.html)
  - 📁 *Category*: Practical, Debugging Toolkit
  - 💡 *Why*: bpftrace uprobes let you instrument any user-space function at runtime — no recompilation, no restart, no LD_PRELOAD. Attach to allocator entry points to attribute allocation volume by call stack, trace kernel page faults to correlate RSS growth with specific code paths, or build latency histograms for I/O operations. Chapter 7 covers memory allocation tracing, page faults, RSS growth, and leak detection. Chapter 8 covers file system I/O patterns. The one-liners tutorial is the fastest way to internalize the tool.
  - ⏱️ *Est. time*: 45 min (skim chapters + read one-liners tutorial + bookmark cheat sheet)

- [ ] **eBPF-Powered Databases** — Andy Pavlo (CMU Database Group)
  - 🔗 [Video](https://www.youtube.com/watch?v=vD-0dw4gUhw)
  - 📁 *Category*: Cutting Edge, Mental Model
  - 💡 *Why*: Explores using eBPF not just for observability but as a compute layer for database operations — pushing query logic into the kernel. A provocative take on where the boundary between kernel and userspace should live for data-intensive systems.
  - ⏱️ *Est. time*: 30 min

- [ ] **Linux Performance Analysis in 60,000 Milliseconds** — Brendan Gregg (Netflix Tech Blog)
  - 🔗 [Blog post](https://netflixtechblog.com/linux-performance-analysis-in-60-000-milliseconds-accc10403c55)
  - 📁 *Category*: Practical, Debugging Toolkit
  - 💡 *Why*: The 10 commands you run in the first 60 seconds on a sick Linux box, from the creator of flame graphs. Covers `uptime`, `dmesg`, `vmstat`, `mpstat`, `pidstat`, `iostat`, `free`, `sar`, `top`, and `perf`. Each command gets a one-paragraph explanation of what to look for. An essential checklist for anyone debugging production performance issues on Linux.
  - ⏱️ *Est. time*: 15 min

### bpftrace use cases for database server profiling

**CPU profiling:**
- `uprobe` on hot-path functions — profile time spent in specific code paths per thread
- `tracepoint:sched:sched_switch` — measure involuntary context switches (high counts = contention or CPU saturation)
- `profile:hz:99` with `ustack` — CPU sampling without `perf`, filterable by thread name
- `uprobe` on storage client functions — latency histograms for blob reads/writes, catch tail latencies

**Memory profiling:**
- `tracepoint:kmem:mm_page_alloc` — track kernel page faults to see when RSS is actually growing
- `uprobe` on `mmap`/`munmap` — catch large anonymous mappings from libraries or pools
- `tracepoint:kmem:rss_stat` — kernel-level RSS change events, pinpoints exactly which code path triggers RSS growth

**I/O and cache profiling:**
- `uprobe` on cache get/put functions — hit/miss ratios at function level, latency per operation
- `tracepoint:block:block_rq_issue` — disk I/O latency histograms for NVMe or SSD caches
- `uprobe` on flush/writeback paths — bytes per flush, latency, backoff frequency

---

## 📅 Suggested Daily Schedule

| Day | Reading | Time |
|-----|---------|------|
| Mon | Spanner paper skim + companion blog + TrueTime deep dive | 45 min |
| Tue | 3Blue1Brown Linear Algebra Ch 1-4 | 40 min |
| Wed | BPF Performance Tools Ch 7-8 skim + one-liners tutorial | 45 min |
| Thu | eBPF-Powered Databases video + Linux Perf in 60s | 45 min |
