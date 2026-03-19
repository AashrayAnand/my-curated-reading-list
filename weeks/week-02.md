# Week 2: Formal Reasoning, Natural Language, & the Future of Specification

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

- [x] **Getting Into TLA+** — Marc Brooker ✅
  - 🔗 [Blog post](https://brooker.co.za/blog/2022/07/29/getting-into-tla.html)
  - 📁 *Category*: Practical, Mental Model
  - 💡 *Why*: A personal account of how formal specification became a practical tool — starting from ad-hoc state convergence bugs, through Alloy and Spin, to TLA+. Shows the journey from "formal methods are academic" to "this saves us real rework and catches real bugs." The most accessible on-ramp to TLA+ from someone who uses it in production at scale.
  - ⏱️ *Est. time*: 15 min

- [x] **Formal Methods: Just Good Engineering Practice?** — Marc Brooker ✅
  - 🔗 [Blog post](https://brooker.co.za/blog/2024/04/17/formal.html)
  - 🎥 [TLA+ Conf 2024 keynote video](https://www.youtube.com/watch?v=HxP4wi4DhA0)
  - 📁 *Category*: Mental Model, Cutting Edge
  - 💡 *Why*: Makes the economic case for formal methods — not as an academic luxury but as cost optimization. The Arthur Wellington quote frames it perfectly: engineering is the art of doing with one dollar what any bungler can do with two. Formal specifications reduce rework, which is where most engineering cost hides. Pairs directly with the Lamport piece below.
  - ⏱️ *Est. time*: 15 min

- [ ] **Who Builds a House Without Drawing Blueprints?** — Leslie Lamport (CACM)
  - 🔗 [Article](https://cacm.acm.org/opinion/who-builds-a-house-without-drawing-blueprints/)
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: Lamport's argument that writing specifications is as fundamental to programming as blueprints are to construction. The analogy is sharp: we would never start pouring concrete without a blueprint, yet we routinely start writing code without a clear spec. Connects directly to Brooker's posts and to the broader theme of how natural language + formal reasoning + agent execution may be the emerging stack.
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

**Marc Brooker — Getting Into TLA+**: The quote that captures it: *"I think of this as a kind of mix of hubris (software can be correct), humility (I can't write correct software) and laziness (I don't want to fix this again). Some people just didn't believe that it was a battle that could be won, and some hadn't yet burned their fingers enough to believe they couldn't win it without help."*

I've been vaguely aware of formal reasoning models for years but the barrier to entry always felt too high, or the cost of creating a formal spec felt like overkill for most projects. But with agentic development completely collapsing the cost of software development, it feels short-sighted now to ever develop a system without formal methods. The cost argument that kept formal methods in the "nice to have" category is evaporating — agents can help write and iterate on TLA+ specs the same way they help write code. The emerging stack is becoming: natural language → agent-assisted refinement loop → formal specification → deterministic execution by agents. The spec *is* the product. The code is just the compilation target.

**Marc Brooker — Formal Methods: Just Good Engineering Practice?**: The key observation: *"Software engineering is somewhat unique in the engineering fields in that design and construction tend to happen at the same time, and a lot of construction can be started without advancing much into design."* Software, unlike a bridge or a car, is a mode of engineering where things can be iterated on with a much higher threshold for damage or mistake. And because the iteration cost — compared to building a prototype car or pouring concrete — is in an entirely different realm of cheapness (and even more so with AI-assisted development), we've normalized skipping the design phase entirely. But that cheaper iteration cost now also applies to formal methods themselves. With AI on your side and endless computing power to run formal verification, there isn't an excuse not to use it for specifying complicated systems. The cost of *not* specifying is rework. The cost of specifying just collapsed. The math is obvious.

Another standout quote: *"It seems like the closer the requirements process is to the laws of physics the more valuable design, and formal design, are in the engineering process. The closer the requirements are to users' opinions, the less valuable they are."* This is exactly the world of database storage systems and cloud infrastructure. The user requirements are obfuscated in the sense that we're delivering a PostgreSQL-compliant database service, which has a pretty hard interface largely outside our own jurisdiction (the OSS community owns the spec). The requirements for our storage subsystem are much more about achieving guarantees within the realm of the laws of physics: the speed of the system, accounting for failure modes between networked components, and ensuring the correctness of a distributed system aligns with the expectations of a serializable environment. This is where formal methods shine. Compare that to domains where the success criteria is more ambiguously human, like how a certain UI responds to user input, which can't be as easily formulated in a model, and where the downside of getting it wrong is much lower. The further you are from user opinions and the closer you are to physics, the more a formal spec pays for itself.
