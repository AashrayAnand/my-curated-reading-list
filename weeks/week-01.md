# Week 1: Distributed Storage & The Transformer Revolution

📆 **March 10 – 17, 2026** · 🟡 *In Progress*

**Time Budget**: ~175 min (45 min × 4 days)

---

## 📄 Anchor Paper: Dynamo — Amazon's Highly Available Key-value Store

- [ ] **Dynamo: Amazon's Highly Available Key-value Store** (SOSP 2007)
  - 🔗 [Paper (PDF)](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf) — skim/refresh
  - 🔗 [Companion blog: Paper Notes on Dynamo](https://distributed-computing-musings.com/2022/05/paper-notes-dynamo-amazons-highly-available-key-value-store/) — detailed walkthrough with linked deep dives on each concept
  - 📁 *Category*: Foundational, Historical Context
  - 💡 *Why*: The paper that defined eventually consistent key-value stores. Introduced consistent hashing, vector clocks, sloppy quorums, and hinted handoff — patterns that directly influenced DynamoDB, Cassandra, and Riak. Understanding Dynamo gives you the vocabulary for nearly every distributed storage system built since. The companion blog is a great refresher that links to separate posts on each core concept.
  - ⏱️ *Est. time*: 45 min (skim paper + read companion blog)
  - 🎥 *Supplementary*: [Andy Pavlo — CMU 15-721 Lecture on Amazon DynamoDB](https://www.youtube.com/watch?v=jmc_M6dGuzo)

---

## 🤖 AI Deep Dive: The Illustrated Transformer

- [ ] **The Illustrated Transformer** — Jay Alammar
  - 🔗 [Blog post](https://jalammar.github.io/illustrated-transformer/)
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: The best visual introduction to the transformer architecture — the foundation of every modern LLM. Walks through self-attention, multi-head attention, positional encoding, and encoder-decoder structure with clear diagrams. This is the prerequisite for understanding how any LLM works internally.
  - ⏱️ *Est. time*: 45 min
  - 📄 *Supplementary paper*: [Attention Is All You Need (arxiv:1706.03762)](https://arxiv.org/abs/1706.03762) — the original 2017 paper, if you want the formal details after the visual walkthrough.
  - ⏩ *Deferred to backlog* — need to build math/ML fundamentals first

---

## 💻 Tech Blogs

- [x] **Surprising Scalability of Multitenancy** — Marc Brooker ✅
  - 🔗 [Blog post](https://brooker.co.za/blog/2023/03/23/economics.html)
  - 📁 *Category*: Mental Model, Practical
  - 💡 *Why*: A concise, insightful explanation of why cloud multitenancy works better than intuition suggests. Introduces the peak-to-average ratio concept and explains why shared infrastructure gets cheaper per customer, not just per unit. Directly relevant if you work with or on cloud databases.
  - ⏱️ *Est. time*: 15 min

- [ ] **The Simple Joys of Scaling Up** — Jordan Tigani (MotherDuck / DuckDB)
  - 🔗 [Blog post](https://motherduck.com/blog/the-simple-joys-of-scaling-up/)
  - 📁 *Category*: Mental Model, Cutting Edge
  - 💡 *Why*: Challenges the distributed-everything dogma. Makes the case that Moore's Law has made single-machine performance so powerful that many workloads no longer need distributed systems. Connects directly to the DuckDB movement and modern analytical database thinking — a counterpoint to the Dynamo paper's distributed approach.
  - ⏱️ *Est. time*: 20 min

---

## 🔧 Allocators & Memory Internals

- [x] **Memory Allocation** — Sam Who
  - 🔗 [Interactive blog post](https://samwho.dev/memory-allocation/)
  - 📁 *Category*: Foundational, Hands-On
  - 💡 *Why*: Beautifully illustrated, interactive walkthrough of how allocators work — first-fit, next-fit, buddy allocation, free lists, and fragmentation. Builds intuition for what malloc/free actually do under the hood, with zero prerequisites. The visual approach makes concepts like internal vs external fragmentation immediately tangible.
  - ⏱️ *Est. time*: 25 min

- [ ] **mimalloc: Free List Sharding in Action** — Daan Leijen (Microsoft Research)
  - 🔗 [Paper (PDF)](https://www.microsoft.com/en-us/research/uploads/prod/2019/06/mimalloc-tr-v1.pdf)
  - 🔗 [Landing page](https://www.microsoft.com/en-us/research/publication/mimalloc-free-list-sharding-in-action/)
  - 📁 *Category*: Technical Deep Dive, Directly Relevant
  - 💡 *Why*: The actual mimalloc paper. Explains the 3 page-local sharded free lists, temporal cadence for maintenance tasks, segment/page layout, and benchmarks vs tcmalloc/jemalloc. Directly relevant to debugging in the mimalloc allocator — this is the architecture you're stepping through.
  - ⏱️ *Est. time*: 40 min

---

## 🌿 Perspective

- [x] **How to Work Hard** — Paul Graham ✅
  - 🔗 [Essay](https://paulgraham.com/hwh.html)
  - 📁 *Category*: Mental Model
  - 💡 *Why*: A thoughtful essay on the nature of productive effort — distinguishing between fake work and real work, finding what genuinely interests you, and sustaining effort over a career. Relevant for anyone building a deliberate learning practice.
  - ⏱️ *Est. time*: 15 min

- [x] **The Bus Ticket Theory of Genius** — Paul Graham ✅ _(bonus read)_
  - 🔗 [Essay](https://paulgraham.com/genius.html)
  - 📁 *Category*: Mental Model
  - 💡 *Why*: Argues that genius requires not just ability and determination, but an obsessive interest in a specific topic — the kind of deep, seemingly useless curiosity that looks like collecting bus tickets until it pays off. A nice companion to "How to Work Hard."
  - ⏱️ *Est. time*: 15 min

- [x] **You Are Here** — Marc Brooker ✅ _(bonus read)_
  - 🔗 [Blog post](https://brooker.co.za/blog/2026/02/07/you-are-here.html)
  - 📁 *Category*: Mental Model, Practical
  - 💡 *Why*: A clear-eyed take on where AI tools leave software engineers — acknowledging the real loss of programming-as-craft while arguing for embracing the new tools to stay relevant. The Amdahl's law framing is particularly sharp.
  - ⏱️ *Est. time*: 15 min

---

## 📅 Suggested Daily Schedule

| Day | Reading | Time |
|-----|---------|------|
| Mon | Dynamo paper skim + companion blog (first half) | 45 min |
| Tue | Dynamo companion blog (second half) + Brooker: Surprising Scalability of Multitenancy | 30 + 15 min |
| Wed | The Illustrated Transformer | 45 min |
| Thu | MotherDuck: The Simple Joys of Scaling Up + Paul Graham: How to Work Hard | 20 + 20 min |

---

## 📝 Notes & Reflections

Wrote a blog post reflecting on the Paul Graham essays and the experience of curating an AI-driven reading list: [On Reading Lists, Paul Graham, and the Joy of Going Deep](https://www.shray.fyi/reading-lists-paul-graham-going-deep/)

Also finished the audiobook for [All the Shah's Men](https://en.wikipedia.org/wiki/All_the_Shah%27s_Men) this week — a non-technical but deeply worthwhile read on US/UK/Iran history.

**Marc Brooker — You Are Here**: Wholeheartedly agree with Marc's take. There's a real, legitimate sadness in losing programming for what it used to be — the craft of it, the satisfaction of building things from scratch. But the right move is to be honest about the change and optimize for getting things done over doing things the way I like it. The Amdahl's law reference hit especially hard — this is exactly how work feels now. Everything *outside* of development (talking to people, reviews, permissions, access requests) are the long poles, while writing software has become the fast wheel. When code generation is near-free, all the human coordination overhead becomes the bottleneck.

**Slab vs. Arena Allocation**: Two fundamentally different approaches to memory management. **Slab allocators** (mimalloc, jemalloc, tcmalloc) are general-purpose — they partition memory into size classes (8, 16, 32, 64, ... bytes), maintain free lists per class, and support arbitrary per-object `malloc`/`free`. **Arena allocators** are an application-level pattern — you bump a pointer forward on each allocation and free *everything at once* by resetting the pointer. No size classes, no free lists, no individual free. The trade-off is clear: arenas sacrifice flexibility (no per-object free) for speed (pointer bump is the fastest possible allocation) and simplicity (zero fragmentation within the arena).

The key insight is that arena allocation only makes sense when the application *knows* that a group of allocations share a lifetime — and this is exactly what happens in systems like PostgreSQL, where `MemoryContext` uses arena-style allocation for query execution. All memory allocated during a query lives in the query's context, gets bump-allocated sequentially, and is freed in one shot when the query completes. This is a perfect fit: the logical grouping of "query lifetime" maps directly to the arena's "allocate many, free all at once" model. You see the same pattern in Apache request pools, game engine per-frame allocators, and compiler pass arenas.

Importantly, the two aren't mutually exclusive — arenas still need to get their backing memory from *somewhere*, and that's usually a slab allocator or direct OS pages underneath. So you get layered designs: arena on top for fast domain-scoped allocation, slab allocator underneath managing the chunks the arena draws from.

**The Illustrated Transformer**: Read through this and got the high-level picture — self-attention, multi-head attention, positional encoding, encoder-decoder structure. The visualizations are excellent. However, I realized I need to go back and refresh a lot of my fundamental math and ML knowledge to actually *digest* this material deeply rather than just pattern-match on the diagrams. Concepts like softmax, cross-entropy loss, gradients, backpropagation, and even basic matrix operations need a real refresh before I can meaningfully internalize transformer internals. Going forward, the AI reading plan should slow down and spend 2-3 months on fundamentals (e.g., 3Blue1Brown neural networks series, Andrew Ng's deep learning courses, introductory ML resources) before circling back to LLM-specific content like "Attention Is All You Need" or GPT deep dives. The exception would be accessible sections from Jay Alammar's "Hands-On Large Language Models" book if they don't require the math foundation I'm rebuilding.
