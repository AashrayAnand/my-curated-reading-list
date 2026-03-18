# Week 2: S3 at Scale, Marc Brooker on AI, & Linux Observability Foundations

**Time Budget**: ~170 min (45 min × 4 days)

---

## 💻 Tech Blogs

- [x] **Building and Operating a Pretty Big Storage System Called S3** — Andy Warfield
  - 🔗 [Blog post](https://www.allthingsdistributed.com/2023/07/building-and-operating-a-pretty-big-storage-system.html)
  - 📁 *Category*: Pattern Recognition, Practical
  - 💡 *Why*: A VP/distinguished engineer at S3 reflects on what "scale" really means across three dimensions — hardware, operations, and customer experience. Connects directly to the GFS lineage you've already explored, but from the perspective of running the system in production for years. Real-world lessons you won't find in papers.
  - ⏱️ *Est. time*: 25 min
  - 🎥 *Supplementary*: [Andy Warfield — USENIX FAST '23 Keynote](https://www.youtube.com/watch?v=sc3J4McebHE) (embedded at end of the blog post)

- [x] **Put Your Agents in a Box** — Marc Brooker
  - 🔗 [Blog post](https://brooker.co.za/blog/2026/01/12/agent-box.html)
  - 📁 *Category*: Mental Model, Cutting Edge
  - 💡 *Why*: Argues that the right way to control AI agents is deterministic, external sandboxing — not relying on alignment or prompting alone. A systems-engineering perspective on agent safety that resonates with how we think about fault isolation in distributed systems.
  - ⏱️ *Est. time*: 15 min

- [x] **Natural Language is the Future of Programming** — Marc Brooker ✅
  - 🔗 [Blog post](https://brooker.co.za/blog/2025/12/16/natural-language.html)
  - 📁 *Category*: Mental Model, Cutting Edge
  - 💡 *Why*: Makes the case that programming has always been converging toward specification, and natural language is the logical next step. Grapples with ambiguity head-on and connects to Lamport, Dijkstra, and formal methods. A thoughtful framing for where AI-assisted development is heading.
  - ⏱️ *Est. time*: 15 min

- [x] **LLMs as Components, Not Whole Systems** — Marc Brooker
  - 🔗 [Blog post](https://brooker.co.za/blog/2025/08/12/llms-as-components.html)
  - 📁 *Category*: Mental Model, Practical
  - 💡 *Why*: Systems built *with* LLMs are far more capable than LLMs alone — the key insight that even trivial tool use (code interpreters, databases, browsers) creates systems orders of magnitude more powerful and cheaper than raw inference. Directly relevant to understanding how to build with AI effectively.
  - ⏱️ *Est. time*: 15 min

- [x] **False Sharing: How It Pops Up and How To Fix It** — Marc Brooker ✅
  - 🔗 [Blog post](https://brooker.co.za/blog/2023/03/07/false-sharing.html)
  - 📁 *Category*: Practical, Mental Model
  - 💡 *Why*: Clear walkthrough of false sharing in concurrent systems with simulations showing the impact on shard heat distribution. The range-vs-hash sharding tradeoff is evergreen — range sharding preserves locality but concentrates heat on recent keys, hash sharding disperses heat but destroys locality.
  - ⏱️ *Est. time*: 15 min

- [ ] **The Effect of Switching to TCMalloc on RocksDB Memory Use** — Cloudflare
  - 🔗 [Blog post](https://blog.cloudflare.com/the-effect-of-switching-to-tcmalloc-on-rocksdb-memory-use/)
  - 📁 *Category*: Practical, Debugging Story
  - 💡 *Why*: Real production debugging story: RSS was 3× the heap size due to glibc malloc fragmentation. Shows exactly the committed-vs-RSS gap in practice, how allocator choice impacts memory consumption, and why switching to tcmalloc cut memory use by almost 3×. A concrete companion to the mimalloc paper from Week 1.
  - ⏱️ *Est. time*: 15 min

---

## 🌿 Perspective

- [x] **Choose Boring Technology** — Dan McKinley ✅
  - 🔗 [Blog post](https://mcfunley.com/choose-boring-technology)
  - 📁 *Category*: Mental Model, Practical
  - 💡 *Why*: The classic "innovation tokens" essay. Argues that every company has a limited budget for new technology, and you should spend it wisely rather than chasing novelty. A healthy counterbalance to the tech industry's bias toward shiny new things — especially relevant after reading about cutting-edge systems all week.
  - ⏱️ *Est. time*: 15 min

---

## 📅 Suggested Daily Schedule

| Day | Reading | Time |
|-----|---------|------|
| Mon | S3 at Scale blog | 25 min |
| Tue | Marc Brooker: Agents in a Box + Natural Language | 30 min |
| Wed | Marc Brooker: LLMs as Components + TCMalloc/RocksDB | 30 min |
| Thu | Choose Boring Technology (done ✅) | — |

---

## 📝 Notes & Reflections

**Dan McKinley — Choose Boring Technology**: The quote "consider how you would solve your immediate problem without adding anything new" is evergreen, and arguably 100× more important now in the age of AI coding. The cost of development and prototyping has collapsed so dramatically that the default response to any problem is to generate something new — a script, a large refactor, a sweeping design decision off a short-lived prototype. And while it's true that decisions previously considered imprudent are now feasible with AI, somebody still has to be responsible for the technical and cognitive overhead of managing all of it. The orphaned artifact problem is real and accelerating: scripts and automations get spun up out of nowhere, but are abandoned immediately after without ownership or maintenance. In the past, when someone had to handwrite something, they at least had the tacit knowledge to grow it, and the implied ownership that came with authorship. Now things appear and disappear faster than anyone can track. Don't just try to be fast — be fast in a way that won't slow you down later.

There's also the follow-up: "posing this question should detect the situation where the 'problem' is that someone really wants to use the technology. If that is the case, you should immediately abort." This used to be about classic overengineering — evangelizing a new language or framework without a real use case. Now, in the AI coding age, there's a spin-off: the impulse to make large sweeping changes to systems with the sole purpose of putting more code into production, more "surface area" owned by someone. Lines of code and percentage of a system written by a person should not demarcate their value or purpose — especially now, when anyone can generate a quantity of work like nothing. We should almost be thinking the opposite: how can we accomplish the goal in a way that generates the *lowest* new surface area in the system? It's like making a bridge unnecessarily long to increase its architectural complexity, rather than just taking the shortest path from one side to the other. It makes no sense once you step away and remove the stroking of your ego from the equation.

**Marc Brooker — Natural Language is the Future of Programming**: The key quote: *"This is, I think, the fundamental thing that most of the takes on natural language and programming miss. They think we're solving a one-hit problem of going from an ambiguous messy, human, specification to a precise and perfect program. They see the trips around the loop as failures. I believe that the trips around the loop are fundamental to the success of the whole enterprise. They're what we've been doing all along."*

This is the part about AI-based development that resonates most. It is often criticized that there is a constant back-and-forth between a developer and agent — to course correct, provide more information, or flat out overrule the agent's decisions. These are seen as indicators that the process itself is flawed, like you are relying on a sporadic machine to do something deterministic and complex. However, the machine is a reflection of the detail of your specification, and its execution of your specification will always be, at the very least, faster than your own. If you can hone the specification to the point of perfection, you can offload the responsibility of actually executing it to the agent.

This leans the balance of the typical software engineering scale to 100% valuing the ability to specify a problem without any ambiguity, rather than the ability to execute on a strategy — your ability to write new code, research documentation for frameworks, or become a domain expert of tools. If the tools themselves can do what you specify, and you can specify it like a proof to a machine, it can be done. The iterative loop isn't a bug in the process. It's the process. It always has been — we just used to do both the specifying and the executing ourselves.

**Marc Brooker — False Sharing**: The contrast between range-based and hash-based sharding never gets old. Range sharding preserves locality but concentrates heat on the most recent keys — the exact keys that tend to be accessed most often. Hash sharding disperses that heat but destroys the locality that made range queries efficient. Neither scheme is universally correct, and heat management for sharded systems is always harder to reason about than it looks — there's always an obscure access pattern where your particular scheme fails to disperse heat well.
