# Week 5: Rust Async Deep Dive

📆 **April 21 – April 27, 2026**

**Time Budget**: ~180 min (45 min × 4 days)

**Theme**: Deep understanding of Rust's async machinery — from the Future trait and pinning through to structured concurrency patterns. Context: implementing streaming dirty page flush with lazy futures, `buffer_unordered`, `async_stream`, and semaphore-bounded concurrency in the pageserver.

---

## 📖 Rust for Rustaceans — Chapter 8: Asynchronous Programming

> *Currently reading. Finish this first.*

- [ ] **Rust for Rustaceans, Ch. 8** — Jon Gjengset
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: Covers the Future trait, poll model, wakers, Pin, executors, and async ecosystem patterns. This is the authoritative treatment of how async Rust actually works under the hood — not just how to use it, but what the compiler generates and why `Pin` exists.
  - ⏱️ *Est. time*: 45 min (remaining)

---

## 🔧 Futures Concurrency Primitives

- [ ] **Futures Concurrency** — Yoshua Wuyts (blog post)
  - 🔗 [Post](https://blog.yoshuawuyts.com/futures-concurrency/)
  - 📁 *Category*: Practical, Design Patterns
  - 💡 *Why*: Deep dive into `join`, `select`, `merge`, `zip`, and how `buffer_unordered` / `for_each_concurrent` work internally. Explains the difference between "concurrent" (multiplexed on one task) and "parallel" (multiple tasks). Directly relevant to understanding why `buffer_unordered(N)` on a lazy stream gives us bounded concurrency without spawning N tasks.
  - ⏱️ *Est. time*: 20 min

- [ ] **Streams Concurrency** — Yoshua Wuyts (blog post)
  - 🔗 [Post](https://blog.yoshuawuyts.com/streams-concurrency/)
  - 📁 *Category*: Practical, Design Patterns
  - 💡 *Why*: Companion to the futures concurrency post — covers stream-specific patterns like `StreamExt::buffer_unordered`, `StreamExt::for_each_concurrent`, and how backpressure propagates through streams. The streaming dirty writer is exactly this pattern: a stream of request ranges → concurrent upload futures with bounded in-flight count.
  - ⏱️ *Est. time*: 15 min

---

## 📌 Pin and Unpin

- [ ] **Pin, Unpin, and why Rust needs them** — Fasterthanlime (blog post)
  - 🔗 [Post](https://fasterthanli.me/articles/pin-and-suffering)
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: The most accessible deep explanation of Pin. Covers why self-referential structs are a problem, what Pin guarantees, why `Box::pin` vs stack pinning matters, and how `async_stream::stream!` uses Pin internally (the macro expands to a self-referential generator that must be pinned). Understanding this is essential for knowing when `Box::pin(stream)` is needed vs `pin_mut!`.
  - ⏱️ *Est. time*: 30 min

---

## 🔄 async_stream and Generator Patterns

- [ ] **async-stream crate documentation and source** — Tokio project
  - 🔗 [Crate docs](https://docs.rs/async-stream/latest/async_stream/)
  - 🔗 [Source (GitHub)](https://github.com/tokio-rs/async-stream)
  - 📁 *Category*: Practical, Implementation Reference
  - 💡 *Why*: Read the macro expansion examples and understand how `stream!` and `try_stream!` work. The macro generates a Future that yields items via a hidden channel. Key questions to answer: what happens when the stream is dropped mid-iteration? How does backpressure work? What are the capture semantics for moved variables? We use `stream!` in the streaming dirty writer to lazily produce payloads.
  - ⏱️ *Est. time*: 15 min

---

## 🏗️ Structured Concurrency

- [ ] **Notes on structured concurrency, or: Go statement considered harmful** — Nathaniel J. Smith
  - 🔗 [Post](https://vorpus.org/blog/notes-on-structured-concurrency-or-go-statement-considered-harmful/)
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: The seminal blog post on structured concurrency — argues that spawning tasks without scoping their lifetime is the concurrency equivalent of `goto`. Rust's `JoinSet`, `tokio::task::JoinHandle`, and the `select!` + cancellation token patterns are all attempts to solve this. Directly relevant to our JoinSet-based upload task management where task lifetime is scoped to the flush operation.
  - ⏱️ *Est. time*: 25 min

---

## 🤖 AI Fundamentals: Linear Algebra (continued)

> *Moved from Week 4.*

- [ ] **3Blue1Brown: Essence of Linear Algebra** (Chapters 5-6)
  - 🔗 [YouTube playlist](https://www.youtube.com/playlist?list=PLZHQObOWTQDPD3MizzM2xVFitgF8hE_ab)
  - 📁 *Category*: Foundational, Mental Model
  - 💡 *Why*: Chapter 5 covers the dot product with its geometric interpretation (projection), and Chapter 6 covers the cross product. The dot product is especially important — it's the core operation in attention mechanisms (query · key = relevance score). Understanding it geometrically rather than just as "multiply and sum" builds the intuition needed for everything that follows.
  - ⏱️ *Est. time*: 25 min

---

## 📅 Suggested Daily Schedule

| Day | Reading | Time |
|-----|---------|------|
| Mon | Rust for Rustaceans Ch. 8 (finish) | 45 min |
| Tue | Futures Concurrency + Streams Concurrency (Yoshua Wuyts) | 35 min |
| Wed | Pin and Suffering (fasterthanlime) + async-stream source | 45 min |
| Thu | Structured Concurrency (njs) + 3Blue1Brown Ch 5-6 | 50 min |
