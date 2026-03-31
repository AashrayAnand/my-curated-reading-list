# Week 3: SemBench, Linear Algebra Foundations, & Linux Observability

📆 **March 24 – 31, 2026**

**Time Budget**: ~175 min

---

## 📄 Anchor Paper: SemBench — Benchmarking Semantic Query Processing Engines

- [ ] **SemBench: A Benchmark for Semantic Query Processing Engines** (VLDB 2026)
  - 🔗 [Paper (PDF)](https://arxiv.org/pdf/2511.01716)
  - 🔗 [arXiv abstract](https://arxiv.org/abs/2511.01716)
  - 📁 *Category*: Cutting Edge, Mental Model
  - 💡 *Why*: Defines and benchmarks a novel class of database systems: semantic query processing engines that extend SQL with LLM-powered operators (semantic filters, joins, mappings, ranking, classification). Evaluates LOTUS, Palimpzest, ThalamusDB, and Google BigQuery across multimodal scenarios (text, images, audio). This sits squarely at the intersection of databases and AI — two core interests — and represents where query processing may be heading. A concrete benchmark for "what does it mean to put LLMs inside SQL?"
  - ⏱️ *Est. time*: 50 min
  - 🎥 *Supplementary*: [LOTUS: A Semantic Lakehouse Framework](https://arxiv.org/abs/2407.11418) — one of the evaluated systems, extends DataFrames with semantic operators

---

## 🤖 AI Fundamentals: Linear Algebra & Neural Network Basics

> *Moved from Week 2*

> **Note:** Based on feedback from Week 1, the AI reading plan is shifting to fundamentals. The goal is to spend 2-3 months building a solid foundation (linear algebra, calculus, basic ML, neural networks) before returning to transformer/LLM-specific content. GPT in 60 Lines of NumPy and Attention Is All You Need will return in a later week once the math is solid.

- [ ] **3Blue1Brown: Essence of Linear Algebra** (Chapters 1-4)
  - 🔗 [YouTube playlist](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab)
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: The best visual introduction to linear algebra — vectors, linear transformations, matrix multiplication, and determinants. Grant Sanderson's geometric intuition approach makes these concepts click in a way that textbooks rarely achieve. Matrix math is the substrate of every neural network: weights are matrices, inputs are vectors, and forward passes are matrix multiplications. Chapters 1-4 cover vectors, span, linear transformations, and matrix multiplication.
  - ⏱️ *Est. time*: 40 min

---

## ⚡ Linux Observability: First 60 Seconds

- [ ] **Linux Performance Analysis in 60,000 Milliseconds** — Brendan Gregg (Netflix Tech Blog)
  - 🔗 [Blog post](https://netflixtechblog.com/linux-performance-analysis-in-60-000-milliseconds-accc10403c55)
  - 📁 *Category*: Practical, Debugging Toolkit
  - 💡 *Why*: The 10 commands you run in the first 60 seconds on a sick Linux box, from the creator of flame graphs. Covers `uptime`, `dmesg`, `vmstat`, `mpstat`, `pidstat`, `iostat`, `free`, `sar`, `top`, and `perf`. Each command gets a one-paragraph explanation of what to look for. An essential checklist for anyone debugging production performance issues on Linux.
  - ⏱️ *Est. time*: 15 min

---

## 🔧 Tokio Internals

> *Added for context on dial9-tokio-telemetry evaluation and tokio scheduler instrumentation work.*

- [ ] **Making the Tokio Scheduler 10x Faster** — Carl Lerche (Tokio Blog, 2019)
  - 🔗 [Blog post](https://tokio.rs/blog/2019-10-scheduler)
  - 📁 *Category*: Deep Dive, Mental Model
  - 💡 *Why*: The definitive explanation of tokio's work-stealing scheduler: local run queues, the global injection queue, task stealing, and the M:N threading model. Covers the queue algorithm redesign, throttled stealing, and how reducing allocations and atomics yielded 10× throughput. Essential context for understanding what dial9's PollStart/PollEnd/WorkerPark/WorkerUnpark events actually mean — they correspond directly to the scheduler states described here.
  - ⏱️ *Est. time*: 30 min

---

## 📅 Suggested Daily Schedule

| Day | Reading | Time |
|-----|---------|------|
| Mon | SemBench paper (first half — intro, benchmark design, operators) | 30 min |
| Tue | SemBench paper (second half — evaluation, results, takeaways) | 20 min |
| Wed | 3Blue1Brown Linear Algebra Ch 1-4 | 40 min |
| Thu | Linux Perf in 60,000 ms | 15 min |
| Fri | Making the Tokio Scheduler 10x Faster | 30 min |
