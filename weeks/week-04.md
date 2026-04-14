# Week 4: Tokio Scheduling, Tail Latency, & Linear Algebra

📆 **March 31 – April 7, 2026**

**Time Budget**: ~165 min (45 min × 4 days)

---

## 🔧 Tokio Internals (continued)

> *Continued from Week 3. Context: evaluating dial9-tokio-telemetry for production integration into Orion pageserver's tokio runtimes.*

- [x] **How Tokio Schedules Tasks: A Hard Lesson Learnt** — Jiacai Liu (Rust Magazine)
  - 🔗 [Article](https://rustmagazine.org/issue-4/how-tokio-schedule-tasks/)
  - 📁 *Category*: Practical, Debugging Toolkit
  - 💡 *Why*: A production war story about task starvation caused by scheduler internals. Traces through the actual code paths: how tasks move between local queues, the global queue, and work-stealing. Includes diagrams of task state transitions. Directly relevant to understanding what wake-to-poll latency means in practice and why dial9's QueueSample and WakeEvent visibility matters for diagnosing the exact class of problems this author hit.
  - ⏱️ *Est. time*: 25 min

---

## 📄 Warehouse-Scale Computing: The Tail at Scale

- [ ] **The Tail at Scale** — Jeff Dean & Luiz André Barroso (CACM, 2013)
  - 🔗 [Paper (PDF)](https://www2.cs.duke.edu/courses/cps296.4/fall13/838-CloudPapers/dean_longtail.pdf)
  - 🔗 [25 Years series post](https://lnkd.in/g_PqCuRs)
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: The paper that named and formalized the tail latency problem in large fan-out systems. Explains why even rare slow responses become dominant at scale (if 1 in 100 requests is slow and you fan out to 100 servers, ~63% of user requests hit at least one slow backend). Introduces hedged requests, tied requests, and the micro-partition + selective replication pattern. Directly connects to the tokio thread from weeks 3-4: cooperative task yielding, poll duration visibility, and scheduling latency are all mechanisms for taming tail latency at the runtime level. This is the "why" behind what dial9 measures.
  - ⏱️ *Est. time*: 40 min

---

## 🤖 AI Fundamentals: Linear Algebra

> *Continuing the fundamentals rebuild. Chapters 1-4 covered in Week 3.*

- [ ] **3Blue1Brown: Essence of Linear Algebra** (Chapters 5-6)
  - 🔗 [YouTube playlist](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab)
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: Chapter 5 covers the dot product with its geometric interpretation (projection), and Chapter 6 covers the cross product. The dot product is especially important — it's the core operation in attention mechanisms (query · key = relevance score). Understanding it geometrically rather than just as "multiply and sum" builds the intuition needed for everything that follows.
  - ⏱️ *Est. time*: 25 min

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
| Mon | How Tokio Schedules Tasks | 25 min |
| Tue | The Tail at Scale (first half) | 25 min |
| Wed | The Tail at Scale (second half) + Lamport: Blueprints | 30 min |
| Thu | 3Blue1Brown Linear Algebra Ch 5-6 | 25 min |
