# Week 1: Distributed Storage & The Transformer Revolution

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

---

## 💻 Tech Blogs

- [ ] **Surprising Scalability of Multitenancy** — Marc Brooker
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
