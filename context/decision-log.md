# Decision Log

This file tracks what was recommended each week, the rationale behind selections, feedback received, and adjustments made. It serves as memory for future Copilot sessions to avoid repetition and incorporate lessons learned.

## Topics Covered

<!-- Updated after each week to track breadth and avoid repeats -->

### Database Papers
| Week | Paper | Era | Subtopic |
|------|-------|-----|----------|
| 1 | Dynamo: Amazon's Highly Available Key-value Store (SOSP 2007) | 2007 | Distributed KV stores, eventual consistency, consistent hashing |
| 2 | Spanner: Google's Globally-Distributed Database (OSDI 2012) | 2012 | Global consistency, TrueTime, distributed transactions |

### AI Resources
| Week | Resource | Subtopic |
|------|----------|----------|
| 1 | The Illustrated Transformer — Jay Alammar | Visual intro to transformer architecture, attention |
| 2 | 3Blue1Brown: Essence of Linear Algebra (Ch 1-4) | Vectors, linear transformations, matrix multiplication |

### Tech Blogs
| Week | Resource | Subtopic |
|------|----------|----------|
| 1 | Surprising Scalability of Multitenancy — Marc Brooker | Cloud economics, peak-to-average ratio |
| 1 | The Simple Joys of Scaling Up — Jordan Tigani | Scale-up vs scale-out, DuckDB, analytical DBs |
| 2 | Building and Operating S3 — Andy Warfield | Large-scale storage operations |
| 2 | Opinionated Map of Streaming Systems — Jamie Brandon | Streaming/incremental computation landscape |

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
