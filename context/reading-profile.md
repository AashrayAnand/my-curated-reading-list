# Reading Profile

## About Me

I'm a busy software developer building my technical depth across databases, AI, and systems design. I want a structured weekly reading practice that keeps my knowledge sharp and broadens my perspective — not just pure technical content, but also philosophical and introspective reads that deepen how I think about technology and engineering.

## Goals

1. **Grow database expertise**: Both historical papers of significance and cutting-edge systems. I'm particularly interested in the kinds of systems referenced in Andy Pavlo's CMU lecture series (15-445 Intro to Database Systems, 15-721 Advanced Database Systems), his seminars (Vaccination DB talks, "PostgreSQL vs. The World"), and similar academic/industry resources.
2. **Build AI/LLM fluency**: Transition from being a developer who *uses* AI tools to one who *understands* them. Starting with high-level introductions to LLMs, how they work internally (transformers, attention, inference), and the cutting-edge AI landscape around and adjacent to inference engines.
3. **Broaden perspective**: Include philosophical, introspective, or engineering-culture reads that go beyond pure technical content — think software craft, systems thinking, the human side of engineering.
4. **Read consistently**: Establish a sustainable daily reading habit rather than binge-reading.

## Time Budget

- **45 minutes per day**, 4 out of 5 working days per week
- **~180 minutes per week** total
- Papers can be split across multiple days

## Weekly Structure

| Slot | Type | Est. Time | Frequency |
|------|------|-----------|-----------|
| 📄 Anchor Paper | Significant DB paper (historical or cutting edge) | ~60 min | 1/week |
| 🤖 AI Deep Dive | AI/LLM blog, paper, or tutorial | 30–40 min | 1/week |
| ⚡ HPC / Systems | High-performance computing topic (blog, video, book chapter) | 20–30 min | 1/week |
| 💻 Tech Blog | Technical blog on databases, systems, dev practices | 15–20 min | 1–2/week |
| 🌿 Perspective | Philosophical, introspective, or engineering-culture read | 20–30 min | 1/week |

Videos are acceptable as primary resources for HPC topics (e.g., conference talks, YouTube explainers) and as supplementary resources for other slots.

## Topic Interests

### Databases (Primary Focus)
- Transaction processing and concurrency control
- Storage engines and data structures (LSM trees, B-trees, etc.)
- Query optimization and execution
- Distributed databases and consensus
- Cloud-native database architecture
- HTAP, OLAP, OLTP system design
- Database history and evolution
- Any system referenced in Andy Pavlo's courses or seminars

### AI / Machine Learning

> **Current phase (Weeks 2-8+): Fundamentals rebuild.** Before diving into LLM internals, building a solid foundation in linear algebra, calculus, basic ML concepts, and neural network architecture. Target: 2-3 months of fundamentals before returning to transformer/LLM-specific content.

**Fundamentals to cover first (in roughly this order):**
- Linear algebra: vectors, matrices, transformations, eigenvalues
- Calculus: gradients, chain rule, partial derivatives
- Core ML: loss functions (cross-entropy, MSE), softmax, sigmoid, overfitting, regularization, feature engineering, evaluation metrics, training vs. inference
- Neural networks: feedforward networks, backpropagation, activation functions, gradient descent, learning rate
- Embeddings: word vectors, learned representations
- Architecture survey: CNNs vs RNNs vs Transformers (high level)
- Attention mechanisms: attention, self-attention, multi-head attention, positional encoding, tokenization
- Transformer blocks: encoder-decoder, layer norm, residual connections

**Then return to:**
- Transformer architecture deep dive (re-read Illustrated Transformer, Attention Is All You Need)
- GPT internals (GPT in 60 Lines of NumPy, picoGPT)
- How LLMs work internally (training, inference, scaling)
- RLHF, fine-tuning, prompt engineering internals
- Inference engines and optimization

**Preferred resources:**
- 3Blue1Brown (linear algebra, neural networks, calculus series)
- Andrew Ng's deep learning courses
- Jay Alammar's visual explanations and "Hands-On Large Language Models" book (accessible sections)
- Other introductory/visual resources that build intuition before formalism

### High-Performance Computing & Systems Optimization (High Priority)

Understanding system optimization at a low level is universally valuable and directly relevant to DBMS work. These topics should be woven into the weekly lists regularly — via blog posts, book chapters, YouTube videos, or papers. Topics to cover:

**CPU & Memory Architecture**
- CPU cache hierarchy (L1/L2/L3)
- Cache lines and their implications for data structure layout
- Cache coherency protocols (MESI)
- False sharing
- Branch prediction
- Instruction pipelining
- SIMD basics
- Prefetching (hardware and software)
- NUMA awareness

**Concurrency & Synchronization**
- Lock-free queues
- Ring buffers
- Disruptor pattern

**I/O & Execution Models**
- Event-driven architecture
- Polling vs. interrupt
- Busy-wait loops
- Thread affinity and core pinning
- Zero-copy design
- Avoiding syscalls

### Philosophy & Perspective
- Software engineering craft and culture
- Systems thinking and complexity
- Engineering ethics and responsibility
- Career growth and technical leadership
- The human side of building systems

## Categorization

Each resource should be tagged with what concepts or patterns it teaches, and what benefit it provides. Categories include:

- **Foundational**: Core concept every developer should know
- **Historical Context**: Understanding where we came from and why
- **Cutting Edge**: What's happening right now at the frontier
- **Pattern Recognition**: Recurring design patterns across systems
- **Mental Model**: Changes how you think about a problem space
- **Practical**: Directly applicable to day-to-day work

## Example Resources I Value

- My GFS writeup at https://www.shray.fyi/google-file-system/ — reading a foundational paper and connecting it to modern systems (Aurora)
- The "25 Papers for 25 Years of Warehouse-Scale Computing" list (already in this repo)
- Andy Pavlo's database lecture series and seminars
- Papers from VLDB, SIGMOD, OSDI, SOSP conferences

## Link Policy

- Papers should use arxiv, university-hosted PDFs, or other open-access sources
- All links must be verified as working before inclusion
- Prefer stable, long-lived URLs over ephemeral blog posts where possible

## Feedback Loop

- I will provide feedback after each week on what worked, what didn't, and what to adjust
- The decision log (`context/decision-log.md`) tracks all recommendations and feedback
- Future weeks should evolve based on accumulated feedback
- Start with 2 weeks, then generate further weeks on demand
