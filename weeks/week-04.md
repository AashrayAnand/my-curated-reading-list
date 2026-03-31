# Week 4: Tokio Deep Dive, Lock-Free Foundations, & Neural Network Basics

📆 **March 31 – April 7, 2026**

**Time Budget**: ~210 min (45 min × 4 days + extended Tokio focus)

---

## 🔧 Tokio Internals & Async Runtime Profiling (continued)

> *Continued from Week 3. Context: evaluating dial9-tokio-telemetry for production integration into Orion pageserver's tokio runtimes.*

- [ ] **Reducing Tail Latencies with Automatic Cooperative Task Yielding** — Tokio Blog (2020)
  - 🔗 [Blog post](https://tokio.rs/blog/2020-04-preemption)
  - 📁 *Category*: Deep Dive, Debugging Toolkit
  - 💡 *Why*: Explains why a single task that never yields (because I/O is always ready) can starve the entire runtime, causing tail latency spikes. Introduces Tokio's per-task operation budget — the cooperative preemption mechanism. Critical for understanding why dial9's per-task poll duration tracking matters: long polls are exactly the problem this feature tries to mitigate, and having visibility into them (via PollStart→PollEnd) lets you find tasks that exhaust their budget or bypass it entirely.
  - ⏱️ *Est. time*: 15 min

- [ ] **How Tokio Schedules Tasks: A Hard Lesson Learnt** — Jiacai Liu (Rust Magazine)
  - 🔗 [Article](https://rustmagazine.org/issue-4/how-tokio-schedule-tasks/)
  - 📁 *Category*: Practical, Debugging Toolkit
  - 💡 *Why*: A production war story about task starvation caused by scheduler internals. Traces through the actual code paths: how tasks move between local queues, the global queue, and work-stealing. Includes diagrams of task state transitions. Directly relevant to understanding what wake-to-poll latency means in practice and why dial9's QueueSample and WakeEvent visibility matters for diagnosing the exact class of problems this author hit.
  - ⏱️ *Est. time*: 25 min

- [ ] **Profiling Rust/Tokio Applications** — HackMD
  - 🔗 [Notes](https://hackmd.io/@3-Bx3jf3RpqOnnXXpeALNw/BJsWxfNxJg)
  - 📁 *Category*: Practical, Debugging Toolkit
  - 💡 *Why*: Pragmatic notes on diagnosing latency and CPU bottlenecks in tokio servers. Discusses the fundamental challenge of async profiling: stack traces don't capture "what was this task waiting for?" because the stack unwinds at every `.await`. Compares approaches: CPU profilers (perf, flamegraph), async-aware profilers, tokio-console, and custom instrumentation. This is exactly the problem space dial9 operates in — and these notes explain why traditional profiling tools fall short for async code.
  - ⏱️ *Est. time*: 20 min

- [ ] **Async Rust: What is a Runtime? Here is How Tokio Works Under the Hood** — Sylvain Kerkour
  - 🔗 [Blog post](https://kerkour.com/rust-async-await-what-is-a-runtime)
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: Builds up from first principles: what a Future is, what a Waker does, what an executor loop looks like, then how tokio's multi-threaded runtime actually implements all of it. Good primer if you want to understand why dial9 needs a `Traced<F>` wrapper to intercept wakers — the standard Future trait doesn't expose wake events, so you have to wrap the waker itself.
  - ⏱️ *Est. time*: 20 min

---

## 🤖 AI Fundamentals: Linear Algebra & Neural Networks

> *Continuing the fundamentals rebuild. Chapters 1-4 covered in Week 3; now extending to the full linear algebra series and beginning neural networks.*

- [ ] **3Blue1Brown: Essence of Linear Algebra** (Chapters 5-8)
  - 🔗 [YouTube playlist](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab)
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: Chapters 5-8 cover the dot product (geometric interpretation), cross product, change of basis, and eigenvectors/eigenvalues. Eigenvectors are especially important — they show up everywhere from PCA to the attention mechanism. Change of basis is the conceptual foundation for understanding how neural networks transform data through successive layers.
  - ⏱️ *Est. time*: 40 min

- [ ] **3Blue1Brown: Neural Networks** (Chapters 1-2)
  - 🔗 [YouTube playlist](https://www.youtube.com/playlist?list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi)
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: Visual introduction to neural networks — what neurons represent, how layers compose, what activation functions do, and the intuition behind gradient descent. The same geometric approach that made the linear algebra series click. These two chapters cover "what is a neural network?" and "gradient descent" — prerequisites for understanding backpropagation, which is next.
  - ⏱️ *Est. time*: 30 min

---

## 💻 Concurrency Foundations (from backlog)

> *Lock-free data structures were deferred from Week 2. With the tokio scheduler context fresh, this is a natural time to understand the concurrency primitives underneath.*

- [ ] **Lock-Free Data Structures with Hazard Pointers** — Andrei Alexandrescu & Maged Michael (Dr. Dobb's, 2004)
  - 🔗 [Article](https://erdani.org/publications/cuj-2004-12.pdf)
  - 📁 *Category*: Foundational, Deep Dive
  - 💡 *Why*: The ABA problem is one of those things that's easy to gloss over until it bites you. This article walks through why naïve CAS on a lock-free stack breaks, introduces hazard pointers as a solution, and gives the clearest explanation of the fundamental memory reclamation problem in lock-free code. Directly relevant to understanding why dial9 uses crossbeam's ArrayQueue (which solves this with epoch-based reclamation) and why lock-free structures are so tricky to get right.
  - ⏱️ *Est. time*: 30 min

---

## 🌿 Perspective

- [ ] **Who Builds a House Without Drawing Blueprints?** — Leslie Lamport (CACM)
  - 🔗 [Article](https://cacm.acm.org/opinion/who-builds-a-house-without-drawing-blueprints/)
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: Lamport's argument that writing specifications is as fundamental to programming as blueprints are to construction. The analogy is sharp: we would never start pouring concrete without a blueprint, yet we routinely start writing code without a clear spec. Connects directly to the formal methods thread from Week 2 (Brooker's TLA+ posts) and to the broader question of how natural language + formal reasoning + agent execution may be the emerging engineering stack.
  - ⏱️ *Est. time*: 15 min

---

## 📅 Suggested Daily Schedule

| Day | Reading | Time |
|-----|---------|------|
| Mon | Reducing Tail Latencies + How Tokio Schedules Tasks | 40 min |
| Tue | Profiling Rust/Tokio + Async Runtime Under the Hood | 40 min |
| Wed | 3Blue1Brown Linear Algebra Ch 5-8 | 40 min |
| Thu | 3Blue1Brown Neural Networks Ch 1-2 | 30 min |
| Fri | Hazard Pointers + Lamport: Blueprints | 45 min |
