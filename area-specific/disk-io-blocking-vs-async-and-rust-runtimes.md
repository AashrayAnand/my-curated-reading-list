# Disk I/O, Blocking vs. Async I/O, and Rust Async Runtimes

A ground-up reading path: from how a physical device moves bytes, up through the OS storage
stack and the blocking `pread`/`pwrite` syscalls, into why synchronous blocking calls stall a
thread, why async runtimes like Tokio cannot do "true" async disk I/O and instead push blocking
reads onto a thread pool (hence `spawn_blocking` as a bottleneck), and finally into the kernel
facilities (`io_uring`) and architectures (thread-per-core) that make real async disk I/O possible.
Ends with papers that fold all of this back into database and storage engines.

Read top to bottom; each tier assumes the one above it.

---

## Time budget and how to weight it

Total: ~8-10 hours. The single most important curation decision here is **how to split time
between the devices themselves and the kernel facilities used to talk to them.** For someone whose
goal is understanding I/O submission bottlenecks (blocking pools, async runtimes, io_uring), the
device internals are supporting knowledge, not the destination. Weight accordingly:

| Area | Tiers | Suggested time | Why this weight |
| --- | --- | --- | --- |
| Devices + OS I/O path (fundamentals) | 1 | ~1.5-2 h | Enough to know *why* latency has a long tail and *why* concurrency/queue-depth matters. Do not rabbit-hole on FTL/NAND internals. |
| Blocking syscalls, threads, async model | 2 | ~1.5 h | The conceptual bridge; load-bearing. |
| Kernel facilities + submission model (runtimes, io_uring, TPC) | 3-4 | ~4-5 h | This is where the actual bottleneck lives. Spend the bulk here. |
| Domain integration (DB/storage engines) | 5 | ~1.5 h | Ties it back to real engines; read with your own system in mind. |

Rule of thumb: **roughly 1 part "how the device works" to 3 parts "how you submit I/O to it."**
The device chapters (Tier 1) exist to make the submission-model discussion (Tiers 2-4) physically
concrete, not to make you a flash-endurance expert. If you find yourself deep in NAND
program/erase cycles, you have overspent Tier 1.

On references: **OSTEP's I/O chapters are the best free introduction that exists** and are the
right anchor; there is no blog that beats them for the fundamentals. The additions below are
complements (a practitioner meta-guide, an access-methods blog, a measurement guide), not
replacements. If you want a textbook alternative, *Computer Systems: A Programmer's Perspective*
(Bryant & O'Hallaron) Ch. 6 (memory hierarchy) and Ch. 10 (system-level I/O) cover the same ground
more formally, but they are not freely/legally online so they are not linked here.

---

## Tier 1 — How storage devices and the OS I/O path actually work

Start at the metal so every later "scheduling overhead" claim has a physical referent. Target ~1.5-2h.

1. **OSTEP — I/O Devices** (Arpaci-Dusseau, free book chapter) — *the anchor*
   https://pages.cs.wisc.edu/~remzi/OSTEP/file-devices.pdf
   The canonical low-level intro: device registers, polling vs. interrupts, PIO vs. DMA, the
   device-driver abstraction. Read this in full.

2. **OSTEP — Hard Disk Drives**
   https://pages.cs.wisc.edu/~remzi/OSTEP/file-disks.pdf
   Geometry, seek/rotation/transfer, and I/O scheduling. Even in an SSD/NVMe world this is where
   the vocabulary of "queueing" and "service time" comes from. Skim if short on time.

3. **OSTEP — Flash-Based SSDs**
   https://pages.cs.wisc.edu/~remzi/OSTEP/file-ssd.pdf
   Why real media behaves differently: pages/blocks, erase-before-write, the FTL, write
   amplification, and why SSD latency has a long tail even when the median is tiny. Read the first
   half carefully; the FTL details can be skimmed.

4. **Coding for SSDs (Parts 1-6)** — Emmanuel Goossaert
   https://codecapsule.com/2014/02/12/coding-for-ssds-part-1-introduction-and-table-of-contents/
   The practitioner version of the SSD chapter: internal parallelism, queue depth, and why
   concurrency (many outstanding requests) is what actually extracts NVMe bandwidth. This is the
   device-level justification for batching reads rather than issuing them one at a time.

5. **The Linux Storage Stack Diagram** — Thomas-Krenn wiki
   https://www.thomas-krenn.com/en/wiki/Linux_Storage_Stack_Diagram
   One picture of every layer a `read()` traverses: VFS → page cache → filesystem → block layer →
   `blk-mq` → driver → device. Keep this open as a map for everything below.

6. **Latency Numbers Every Programmer Should Know** (interactive) — Colin Scott
   https://colin-scott.github.io/personal_website/research/interactive_latency.html
   Calibration: memory ref vs. SSD read vs. disk seek across years. Grounds the microsecond figures
   in the later tiers in orders of magnitude.

---

## Tier 2 — Blocking syscalls, threads, and why a blocked thread is expensive

Connect the device path to the programming model: what a synchronous `pread` does to a thread. ~1.5h.

7. **Async: What is blocking?** — Alice Ryhl (Tokio maintainer) — *keystone*
   https://ryhl.io/blog/async-what-is-blocking/
   The single most important blog for this problem. Defines "blocking" precisely, explains why a
   blocking call on an async worker thread starves every other task on that worker, and introduces
   `spawn_blocking` and dedicated pools as the mitigation. Read it twice.

8. **Userland Disk I/O** — Alex Miller (FoundationDB) — *meta-guide*
   https://transactional.blog/how-to-learn/disk-io
   A curated, opinionated tour of the I/O submission surface: `O_DIRECT` vs. buffered, the page
   cache, `pread`/`pwritev2`, `fdatasync`, `libaio`/`io_submit`, and io_uring, with the DB-vs-OS
   worldview tension. The best single bridge between "the device" and "the API you call." Doubles as
   a how-to-learn roadmap for this whole area.

9. **The Rust Async Book — Ch. 1-2 (Getting Started; Under the Hood: Futures & Tasks)**
   https://rust-lang.github.io/async-book/
   How `async`/`await` compiles to a state machine, what a `Future` and `poll` are, and what a
   reactor/executor split is. Needed before "why can't the runtime just do async disk I/O" even
   makes sense.

10. **Crust of Rust: The What and How of Futures and async/await** — Jon Gjengset (YouTube, ~1h45)
    https://www.youtube.com/watch?v=9_3krAQtD2k
    Hand-builds the `Future` machinery so `poll`, `Waker`, and "the runtime only advances a task
    when woken" stop being magic. Pair with #9.

11. **Crust of Rust: async/await** — Jon Gjengset (YouTube)
    https://www.youtube.com/watch?v=ThjvMReOXYM
    The companion session: how the compiler turns `async fn` into a resumable state machine, where
    the yield points are, and why holding a blocking call across an `.await` boundary is
    fundamentally different from yielding.

---

## Tier 3 — Tokio internals: the scheduler, `spawn_blocking`, and why disk I/O is the odd one out

The crux: why Tokio is genuinely async for sockets but not for files. Spend real time here.

12. **Making the Tokio Scheduler 10x Faster** — Carl Lerche (Tokio blog)
    https://tokio.rs/blog/2019-10-scheduler
    The work-stealing multi-threaded scheduler: per-worker run queues, the global injector queue,
    stealing, and the LIFO slot. This is exactly what a task is *queued behind* — the origin of the
    "queue" component in any disk-read latency decomposition.

13. **`tokio::task::spawn_blocking` — API docs & guidance**
    https://docs.rs/tokio/latest/tokio/task/fn.spawn_blocking.html
    Read the doc prose, not just the signature: the blocking pool is separate from the worker pool,
    grows up to `max_blocking_threads` (default 512), and each call is a task hand-off with its own
    queue. This is the mechanism a Tokio-based disk read rides on.

14. **`tokio::fs` module docs**
    https://docs.rs/tokio/latest/tokio/fs/index.html
    Tokio states plainly that its filesystem ops are backed by `spawn_blocking` over `std::fs`,
    because the OS offers no portable readiness-based async file API. This is the "no true async disk
    I/O" fact, from the source. Note the throughput caveat they call out.

15. **I/O access methods and Seastar** — ScyllaDB engineering blog
    https://www.scylladb.com/2017/10/05/io-access-methods-scylla/
    A production storage engine compares synchronous, thread-pool, `libaio` + `O_DIRECT`, and
    memory-mapped I/O, and explains why each fails to be both async and CPU-efficient. The concrete,
    engine-level version of the tradeoff #14 states abstractly.

16. **Bridging with sync code** — Tokio topic guide
    https://tokio.rs/tokio/topics/bridging
    The other direction (calling async from sync, `block_in_place`, `Handle`), the context you need
    to reason about `block_in_place` vs. `spawn_blocking` as a short-term option.

17. **Why async Rust?** — withoutboats
    https://without.boats/blog/why-async-rust/
    Design-level: why readiness-based (epoll) async works for network sockets and why files don't
    fit that model at all, which is the root reason a blocking thread pool is the fallback. Sets up
    Tier 4.

---

## Tier 4 — Real async disk I/O: `io_uring`, completion-based I/O, and thread-per-core

Having seen why the thread-pool fallback exists, study what replaces it. Bulk of the reading time.

18. **Efficient IO with io_uring** — Jens Axboe (the design document/PDF)
    https://www.kernel.dk/io_uring.pdf
    The author's own writeup: submission/completion queues, shared-memory rings, why the older AIO
    (`libaio`) was inadequate for buffered I/O, and the syscall-amortization model. Primary source;
    read it slowly.

19. **Kernel Recipes 2019 — Faster IO through io_uring** — Jens Axboe (YouTube)
    https://www.youtube.com/watch?v=-5T4Cjw46ys
    The same material as a talk; good for intuition before/after the PDF. There is a 2022 follow-up
    ("What's new with io_uring", https://www.youtube.com/watch?v=ToSRCSijRuE) for the evolution.

20. **The rapid growth of io_uring** — LWN (Jonathan Corbet)
    https://lwn.net/Articles/810414/
    Concise, authoritative survey of the interface and its trajectory; LWN is the reference-quality
    kernel source. Its predecessor, "Ringing in a new asynchronous I/O API"
    (https://lwn.net/Articles/671649/), covers the original submission and origin story.

21. **Lord of the io_uring** — guide
    https://unixism.net/loti/
    A hands-on, example-driven tutorial. Use it to make the ring model concrete (SQE/CQE lifecycle,
    fixed buffers, registered files) rather than staying at the hand-wave level.

22. **Missing Manuals — io_uring worker pool** — Cloudflare blog
    https://blog.cloudflare.com/missing-manuals-io_uring-worker-pool/
    Crucial nuance: io_uring still uses in-kernel worker threads for operations that cannot proceed
    without blocking (including buffered file reads that miss cache). Prevents the naive belief that
    io_uring makes all disk I/O magically non-blocking; it moves the pool into the kernel.

23. **io_uring(7) man page**
    https://man7.org/linux/man-pages/man7/io_uring.7.html
    Reference for the actual syscalls (`io_uring_setup`, `io_uring_enter`, `io_uring_register`).
    Skim now, return when reading real code (glommio, tokio-uring).

24. **Ringbahn: a safe io_uring interface for Rust** — withoutboats
    https://without.boats/blog/ringbahn/
    (Plus the follow-up series at https://without.boats/blog/io-uring/.) The hard part of wiring a
    completion-based (not readiness-based) API into Rust's borrow-checked async model: ownership of
    in-flight buffers, cancellation safety. This is the design space of any dedicated io_uring
    reader thread in Rust.

25. **The Impact of Thread-Per-Core Architecture on Application Tail Latency** — Enberg, Rao,
    Tarkoma (ANCS 2019)
    https://penberg.org/papers/tpc-ancs19.pdf
    Why sharing a blocking pool across cores hurts tail latency, and why thread-per-core (pin a
    runtime to each core, no work stealing, no shared blocking pool) is the alternative structural
    model. The lens for interpreting a p99 queueing tail.

26. **Introducing Glommio** — DataDog engineering blog
    https://www.datadoghq.com/blog/engineering/introducing-glommio/
    A production thread-per-core, io_uring-based Rust runtime. Concrete counterpoint to Tokio's
    work-stealing + `spawn_blocking` model; shows what an end-to-end "true async disk I/O" Rust
    runtime looks like.

---

## Tier 5 — Folding it back into storage engines and databases

The systems papers and posts that connect all of the above to real engines. ~1.5h.

27. **Modern storage is plenty fast. It is the APIs that are bad.** — Glauber Costa
    https://itnext.io/modern-storage-is-plenty-fast-it-is-the-apis-that-are-bad-6a68319fbc1a
    The thesis for this entire list, from a storage-engine author: NVMe can deliver, but the read
    API (blocking, one-at-a-time, thread-per-IO) squanders it. Read after Tier 4 so the fixes land.

28. **What Modern NVMe Storage Can Do, And How To Exploit It** — Haas & Leis (VLDB 2023)
    https://www.vldb.org/pvldb/vol16/p2090-haas.pdf
    Measurement-driven: how many concurrent I/Os and which submission model you need to saturate
    modern NVMe, and how a storage engine should issue reads. The empirical backbone for raising
    queue depth and batching reads.

29. **High-Performance DBMSs with io_uring: When and How to use it** — (arXiv 2025)
    https://arxiv.org/pdf/2512.04859
    The direct domain integration: when io_uring actually beats a `pread` + thread-pool design in a
    DBMS, the pitfalls, and the decision criteria. The paper closest to the read-path change this
    list is meant to inform; read it last.

### Optional deeper cuts

30. **Exploring Better Async Rust Disk I/O** — Tonbo blog
    https://tonbo.io/blog/exploring-better-async-rust-disk-io
    A contemporary walk through the exact tradeoff space (blocking pool vs. io_uring vs.
    thread-per-core) from another Rust storage project. By this point it should read as review,
    which is the point.

31. **A Case for Coroutines / interleaving to hide I/O latency** — TUM DB group
    https://db.in.tum.de/~fent/papers/coroutines.pdf
    Software-side latency hiding: interleave many in-flight lookups so a cache miss on one overlaps
    useful work on another. The algorithmic analogue to overlapping I/O-bound batch preparation with
    other work, and a lens on amortizing per-page fetch cost.

32. **How to Write to SSDs** — Lee, Ziegler, Leis (VLDB 2026) — *adjacent, write-path focus*
    https://www.vldb.org/pvldb/vol19/p1469-lee.pdf
    Excellent and closely related, but note the scope: this is about the **write path** and SSD
    **endurance** — out-of-place writes, write amplification, and exploiting ZNS/FDP placement
    primitives in a B-tree engine (LeanStore). It complements the SSD device chapters (#3, #4) and
    is essential for a storage-engine/buffer-pool syllabus, but it does not cover the
    blocking-vs-async submission model that is the spine of this list. Read it if you want the
    write-side counterpart; otherwise it belongs on a storage-engine reading list rather than this
    one.

---

## How to read this

- Tiers 1-3 are the load-bearing foundation; do not skip to io_uring before finishing Tier 3.
- After Tier 3 you should be able to explain, unprompted: *why is `spawn_blocking` a bottleneck?*
  The blocking pool is a separate, queued, growable thread pool (#13); each disk read is a task
  hand-off with its own scheduling latency and no completion-based batching; the worker scheduler
  (#12) determines what you queue behind; and files cannot use the readiness/epoll model (#17), so
  the pool is the only portable option (#14).
- Tiers 4-5 are where "what to do about it" lives; read a measured p50/p99 disk decomposition of
  any real system next to #25, #28, and #29.
