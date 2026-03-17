# Backlog

Readings that were scheduled but not completed in their target week. These should be prioritized in future weeks or revisited when the topic comes up again.

## From Week 1

- [ ] **Dynamo: Amazon's Highly Available Key-value Store** (SOSP 2007)
  - 🔗 [Paper (PDF)](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf)
  - 🔗 [Companion blog](https://distributed-computing-musings.com/2022/05/paper-notes-dynamo-amazons-highly-available-key-value-store/)
  - *Original week*: Week 1
  - *Topic*: Distributed KV stores, eventual consistency, consistent hashing, vector clocks

- [ ] **mimalloc: Free List Sharding in Action** — Daan Leijen (Microsoft Research)
  - 🔗 [Paper (PDF)](https://www.microsoft.com/en-us/research/uploads/prod/2019/06/mimalloc-tr-v1.pdf)
  - *Original week*: Week 1
  - *Topic*: Allocator internals, thread-local heaps, free list sharding, segment/page layout

- [ ] **The Illustrated Transformer** — Jay Alammar
  - 🔗 [Blog post](https://jalammar.github.io/illustrated-transformer/)
  - 📄 *Supplementary*: [Attention Is All You Need (arxiv:1706.03762)](https://arxiv.org/abs/1706.03762)
  - *Original week*: Week 1
  - *Topic*: Transformer architecture, self-attention, multi-head attention, positional encoding
  - *Reason deferred*: Need to build math/ML fundamentals (linear algebra, gradients, loss functions, backprop) before this can be properly digested. Revisit after ~Weeks 8-9.

## Future Topics: Linkers, Loaders, and Dynamic Linking

Deep dive into how executables are built, linked, and loaded — covering ELF structure, symbol tables, position-independent code, the PLT/GOT mechanism, and how shared libraries work across processes.

- [ ] **"Linkers & Loaders"** — John Levine
  - 📖 [Book](https://www.amazon.com/Linkers-Loaders-John-R-Levine/dp/1558604960)
  - *Topic*: The definitive reference on linking and loading — static/dynamic linking, symbol resolution, relocation, object file formats

- [ ] **Eli Bendersky's Linkers & Loaders series**
  - 🔗 [Load-time relocation of shared libraries](https://eli.thegreenplace.net/2011/08/25/load-time-relocation-of-shared-libraries)
  - 🔗 [Position Independent Code on x64](https://eli.thegreenplace.net/2011/11/03/position-independent-code-pic-in-shared-libraries)
  - 🔗 [Position Independent Code on x64 — PLT and GOT](https://eli.thegreenplace.net/2011/11/11/position-independent-code-pic-in-shared-libraries-on-x64)
  - *Topic*: PIC, GOT/PLT mechanics, how shared libraries achieve position independence, lazy vs eager binding

- [ ] **"How To Write Shared Libraries"** — Ulrich Drepper (Red Hat)
  - 🔗 [Paper (PDF)](https://www.akkadia.org/drepper/dsohowto.pdf)
  - *Topic*: ELF shared library internals from the glibc maintainer — symbol visibility, versioning, optimization techniques for shared objects

- [ ] **Ian Lance Taylor's 20-part linker series**
  - 🔗 [Part 1: Introduction](https://www.airs.com/blog/archives/38)
  - *Topic*: Comprehensive ground-up explanation of linkers by the author of the gold linker — object files, symbol resolution, relocation, shared libraries, TLS, linker scripts

- [ ] **LWN.net: "How programs get run"**
  - 🔗 [Article](https://lwn.net/Articles/631631/)
  - *Topic*: The `execve` → kernel → dynamic linker → PLT resolution chain, how a program goes from disk to running process
