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
