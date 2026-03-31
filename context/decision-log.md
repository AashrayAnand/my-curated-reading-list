# Decision Log

This file tracks what was recommended each week, the rationale behind selections, feedback received, and adjustments made. It serves as memory for future Copilot sessions to avoid repetition and incorporate lessons learned.

## Topics Covered

<!-- Updated after each week to track breadth and avoid repeats -->

### Database Papers
| Week | Paper | Era | Subtopic |
|------|-------|-----|----------|
| 1 | Dynamo: Amazon's Highly Available Key-value Store (SOSP 2007) | 2007 | Distributed KV stores, eventual consistency, consistent hashing |
| 2 | Spanner: Google's Globally-Distributed Database (OSDI 2012) | 2012 | Global consistency, TrueTime, distributed transactions |
| 3 | SemBench: Benchmarking Semantic Query Processing Engines (VLDB 2026) | 2025 | Semantic SQL operators, LLM-powered query processing, multimodal benchmarks |

### AI Resources
| Week | Resource | Subtopic |
|------|----------|----------|
| 1 | The Illustrated Transformer — Jay Alammar | Visual intro to transformer architecture, attention |
| 2 | 3Blue1Brown: Essence of Linear Algebra (Ch 1-4) | Vectors, linear transformations, matrix multiplication |
| 4 | 3Blue1Brown: Essence of Linear Algebra (Ch 5-8) | Dot product, cross product, change of basis, eigenvectors |
| 4 | 3Blue1Brown: Neural Networks (Ch 1-2) | Neurons, layers, activation functions, gradient descent |

### Tech Blogs
| Week | Resource | Subtopic |
|------|----------|----------|
| 1 | Surprising Scalability of Multitenancy — Marc Brooker | Cloud economics, peak-to-average ratio |
| 1 | The Simple Joys of Scaling Up — Jordan Tigani | Scale-up vs scale-out, DuckDB, analytical DBs |
| 2 | Building and Operating S3 — Andy Warfield | Large-scale storage operations |
| 2 | Opinionated Map of Streaming Systems — Jamie Brandon | Streaming/incremental computation landscape |
| 3 | Making the Tokio Scheduler 10x Faster — Carl Lerche | Work-stealing scheduler internals, queue design |
| 4 | Reducing Tail Latencies — Tokio Blog | Cooperative task yielding, budget-based preemption |
| 4 | How Tokio Schedules Tasks — Jiacai Liu | Task starvation, scheduler code paths, war story |
| 4 | Profiling Rust/Tokio Applications — HackMD | Async profiling challenges, tool comparison |
| 4 | Async Rust: What is a Runtime — Sylvain Kerkour | Futures, wakers, executor internals |

### Perspective Reads
| Week | Resource | Theme |
|------|----------|-------|
| 1 | How to Work Hard — Paul Graham | Productive effort, finding meaningful work |
| 2 | Choose Boring Technology — Dan McKinley | Innovation tokens, technology selection discipline |

---

## Week 1

**Theme**: Foundations — Distributed Storage & The Transformer Revolution

**Selections & Rationale**:
- **Dynamo** chosen as a foundational paper that introduces essential distributed systems vocabulary (consistent hashing, vector clocks, quorums). It's accessible and connects to many modern systems.
- **Illustrated Transformer** as the AI resource because it's the best visual on-ramp to understanding LLM internals, and a prerequisite for deeper AI content in future weeks.
- **Brooker + MotherDuck** blogs chosen as a pair that presents two complementary perspectives on scaling: multitenancy economics vs. the case for single-machine power.
- **Paul Graham** for a short, thoughtful read on sustaining effort — directly relevant to building a reading habit.

**User Feedback**:
- Completed The Illustrated Transformer ✅
- Read Paul Graham essays, wrote a blog post reflecting on them
- Read Marc Brooker's "You Are Here" as a bonus
- Read Sam Who's memory allocation interactive blog
- Completed the allocator section — wrote detailed notes on slab vs arena allocation and PostgreSQL MemoryContext
- _Awaiting feedback on Dynamo, Simple Joys of Scaling Up, mimalloc paper_

**Adjustments for Next Week**:
- **Major AI plan pivot**: User needs to refresh fundamental math and ML knowledge before continuing with LLM-specific content. Shifting AI readings to a 2-3 month foundational track:
  - Weeks 2-3: 3Blue1Brown linear algebra + neural networks series
  - Weeks 4-5: Andrew Ng deep learning courses and other introductory resources
  - Weeks 6-8: Build up through feedforward networks, backprop, CNNs, RNNs, embeddings
  - Weeks 9+: Return to attention, transformers, LLMs (re-introduce Illustrated Transformer, Attention Is All You Need, GPT from Scratch)
- Topics to cover before returning to LLMs: matrix math, softmax, cross-entropy loss, gradients, training vs. inference, loss functions, overfitting, feature engineering, evaluation metrics, feedforward networks, backpropagation, embeddings, CNNs vs RNNs, attention, self-attention, multi-head attention, positional encoding, tokenization, transformer blocks
- Exception: accessible sections from Jay Alammar's "Hands-On Large Language Models" book can be included if they don't require the math foundation being rebuilt

---

## Week 2

**Theme**: Global Consistency & AI Fundamentals Pivot

**Selections & Rationale**:
- **Spanner** chosen as a natural progression from Dynamo — both are foundational distributed databases, but Spanner takes the opposite philosophical stance (strong consistency vs. eventual). This contrast is intentional.
- **3Blue1Brown Linear Algebra** replaces GPT from Scratch — pivoting to fundamentals based on Week 1 feedback. Matrix math is the prerequisite for everything that follows.
- **S3 at scale** connects to the user's GFS writeup — same storage systems lineage, but from the operations perspective.
- **Streaming systems map** introduces a new domain (stream processing) that's increasingly important in modern data infrastructure.
- **Choose Boring Technology** as a counterbalance to all the cutting-edge systems being studied.
- **eBPF/bpftrace** bonus section added based on practical debugging experience with memory profiling tools.

**User Feedback**:
- _Awaiting feedback_

**Adjustments for Next Week**:
- Continue 3Blue1Brown linear algebra (chapters 5-8: dot product, cross product, change of basis, eigenvectors)
- Add 3Blue1Brown Neural Networks series (chapters 1-2) for visual intro to neural nets, gradient descent
- Keep database and systems content progressing normally

---

## Week 3

**Theme**: Semantic Query Processing, AI Math Foundations, & Linux Observability Essentials

**Selections & Rationale**:
- **SemBench** chosen to replace Spanner as the anchor paper. Spanner is foundational but well-known; SemBench (VLDB 2026) sits at the DB+AI intersection — benchmarking a new class of systems that extend SQL with LLM-powered semantic operators (filters, joins, ranking, classification). Directly relevant to both core interests.
- **Spanner** moved to backlog — still important, will revisit. Not dropped, just deprioritized in favor of cutting-edge content.
- **eBPF material** (BPF Performance Tools Ch 7-8, eBPF-Powered Databases video, bpftrace use cases cheat sheet) moved to backlog. User plans to explore these in an applied/hands-on context rather than as reading material.
- **Linux Perf in 60,000 ms** kept — practical, quick reference, not eBPF-specific.
- **3Blue1Brown Linear Algebra** continues from prior weeks — building the math foundation.
- **Making the Tokio Scheduler 10x Faster** added as a single reading for context on dial9-tokio-telemetry evaluation. Remaining 4 tokio readings moved to Week 4 to keep week 3 lighter.

**User Feedback**:
- _Awaiting feedback_

**Adjustments for Week 4**:
- Carry 4 tokio readings (cooperative yielding, task scheduling war story, async profiling, runtime internals) into Week 4 as the primary systems track
- Continue AI fundamentals: 3Blue1Brown linear algebra Ch 5-8, start neural networks Ch 1-2
- Pull hazard pointers from backlog — natural fit with concurrency/lock-free themes from tokio deep dive
- Pull Lamport "Blueprints" from backlog — completes the formal methods arc from Week 2

---

## Week 4

**Theme**: Tokio Deep Dive, Lock-Free Foundations, & Neural Network Basics

**Selections & Rationale**:
- **Tokio readings (4)** carried from Week 3: cooperative yielding, task scheduling, async profiling, runtime first-principles. These complete the context needed for the dial9-tokio-telemetry integration evaluation.
- **3Blue1Brown Linear Algebra Ch 5-8**: Continuing the math foundation — dot product, cross product, change of basis, eigenvectors. Eigenvectors particularly important for PCA and understanding how attention works.
- **3Blue1Brown Neural Networks Ch 1-2**: Beginning the neural networks track — visual intro to neurons, layers, activation functions, gradient descent. Natural progression from the linear algebra foundation.
- **Hazard Pointers (Alexandrescu)**: From Week 2 backlog. Lock-free memory reclamation is directly relevant after studying tokio's scheduler internals — dial9 uses crossbeam ArrayQueue (epoch-based reclamation).
- **Lamport "Blueprints" (CACM)**: From Week 2 backlog. Completes the formal methods arc (Brooker's TLA+ posts → Lamport's philosophy of specification).

**User Feedback**:
- _Awaiting feedback_
