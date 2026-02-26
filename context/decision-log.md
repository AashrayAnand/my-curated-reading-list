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
| 2 | GPT in 60 Lines of NumPy — Jay Mody | Hands-on GPT-2 implementation, tokenization, inference |

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
- _Awaiting feedback_

**Adjustments for Next Week**:
- _N/A yet_

---

## Week 2

**Theme**: Global Consistency & Understanding GPT from Scratch

**Selections & Rationale**:
- **Spanner** chosen as a natural progression from Dynamo — both are foundational distributed databases, but Spanner takes the opposite philosophical stance (strong consistency vs. eventual). This contrast is intentional.
- **GPT from Scratch** builds directly on Week 1's Illustrated Transformer — moving from visual understanding to code implementation of the same architecture.
- **S3 at scale** connects to the user's GFS writeup and Aurora experience — same storage systems lineage, but from the operations perspective.
- **Streaming systems map** introduces a new domain (stream processing) that's increasingly important in modern data infrastructure.
- **Choose Boring Technology** as a counterbalance to all the cutting-edge systems being studied.

**User Feedback**:
- _Awaiting feedback_

**Adjustments for Next Week**:
- _N/A yet_
