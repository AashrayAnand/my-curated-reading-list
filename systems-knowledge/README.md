# Systems Knowledge — the fundamentals bank

A curated, long-lived syllabus of the load-bearing fundamentals a systems programmer keeps in
their head for a career, not for a week. This is deliberately **not** a weekly list. It is a
knowledge *bank*: a small set of canonical, open-access resources that each teach a durable mental
model — how memory actually works, what a memory model is and why it exists, how to reason about
concurrency without lying to yourself, how lock-free code is built, and how to measure a running
system instead of guessing. The goal is that after working through this over roughly a year, the
ideas below are *reflexes* — you reach for them unprompted when reading code, reviewing a design,
or explaining a latency spike.

Everything here is free and open on the public internet (papers, author-hosted books, blogs,
conference talks). Nothing behind a paywall is linked, even when the paywalled book is excellent —
where a great reference is not legally free (e.g. *The Art of Multiprocessor Programming*,
*Computer Systems: A Programmer's Perspective*, Brendan Gregg's *Systems Performance*), it is
called out in prose but not linked, and a free equivalent is given instead.

Read top to bottom the first time; return to individual entries as reference forever after.

---

## How to weight a year on this

This is a ~12-month background track, not a sprint. The single most important curation decision is
**how much to invest in memory/hardware models versus concurrency versus measurement.** Weight
toward the two that pay off on almost every system you will ever touch: the hardware/memory model,
and how to measure. Lock-free algorithms are high-status but low-frequency in day-to-day work —
learn to *read* and *reason about* them, resist the urge to *write* them.

| Area | Tier | Suggested share of the year | Why this weight |
| --- | --- | --- | --- |
| Orientation (concurrency vocabulary) | 0 | ~1 week | A shared vocabulary so the rest reads cleanly. |
| Memory & CPU architecture | 1 | ~30% | The most universally applicable model. Cache behavior explains more real perf than any algorithm choice. |
| Concurrency & memory models | 2 | ~30% | The hardest to self-teach correctly and the easiest to get subtly wrong. Highest leverage per hour. |
| Lock-free & synchronization | 3 | ~20% | Learn to read/verify it; write it rarely. Depth here has sharp diminishing returns for most roles. |
| OS, scheduling & measurement | 4 | ~20% | Turns opinions into numbers. The skill that makes the other three *provable*. |

Rule of thumb: **for every hour spent learning a clever concurrent algorithm, spend an hour
learning to measure whether it actually helped.** The second skill is rarer and ages better.

---

## Tier 0 — Orientation: a shared vocabulary for concurrency

Start here so every later "acquire/release", "data race", "happens-before" lands on defined terms.

1. **What Every Systems Programmer Should Know About Concurrency** — Matt Kline — *the on-ramp*
   https://assets.bitbashing.io/papers/concurrency-primer.pdf
   The best short, honest introduction to the whole topic: atomics, memory ordering, compiler and
   hardware reordering, and why "it worked on my machine" is not evidence of correctness. ~35 pages.
   Read it in full first; it defines the vocabulary the rest of this list assumes. Re-read after
   Tier 2 and it will read as review — that is the point.

---

## Tier 1 — Memory & CPU architecture (the highest-frequency model)

Almost every real performance story is a memory story. Build the hardware model first so later
claims about cache misses, false sharing, and NUMA have a physical referent. Do not rabbit-hole on
DRAM cell physics; you want the shape of the hierarchy and its cost curve, not a hardware degree.

2. **What Every Programmer Should Know About Memory** — Ulrich Drepper (2007) — *the anchor*
   https://people.freebsd.org/~lstewart/articles/cpumemory.pdf
   The definitive walk through the memory hierarchy: DRAM, the cache levels, cache lines, coherency
   (MESI), TLBs, prefetching, and NUMA — each tied to code you can write differently. ~114 pages;
   best consumed in sections across many weeks. Read Parts 1-3 and 5-6 carefully; the DRAM-internals
   sections (Part 2 deep end) can be skimmed. Everything else on this list about false sharing and
   data layout descends from here.

3. **OSTEP — Virtual Memory** (Arpaci-Dusseau, free book) — *pick the address-space chapters*
   https://pages.cs.wisc.edu/~remzi/OSTEP/
   From the free "Operating Systems: Three Easy Pieces" book, read the Virtualization → Memory
   chapters (address spaces, paging, TLBs, swapping). This is where "the stack and heap are just
   ranges of one demand-paged virtual address space" becomes concrete — the model behind every
   later question about allocation cost and page faults. Read the memory chapters now; you will
   return for the concurrency and persistence chapters in Tiers 2 and 4.

4. **Hardware Memory Models** — Russ Cox (research.swtch.com) — *why "the hardware reorders" is precise, not folklore*
   https://research.swtch.com/hwmm
   The clearest plain-language derivation of *why* x86 (TSO) and ARM/POWER (weak) differ, what a
   memory model formally guarantees, and how litmus tests expose reordering. Read this before any
   atomics material — it makes memory ordering feel inevitable rather than arbitrary. Pair with its
   sequel in Tier 2.

5. **Agner Fog — Optimization manuals** — *reference, not front-to-back*
   https://www.agner.org/optimize/
   The practitioner's microarchitecture bible: instruction latencies, branch prediction, pipelining,
   and how the compiler and CPU actually execute your code. Do **not** read cover to cover. Skim
   "Optimizing software in C++" once for the mental model (branch misprediction cost, data
   dependencies, alignment), then keep it as a lookup table. This is the empirical backing for
   "measure, don't guess."

---

## Tier 2 — Concurrency & memory models (highest leverage per hour)

The material most people get subtly wrong for years. Invest here. The through-line: a *data race*
is undefined behavior, atomics with the right ordering are how you avoid one, and "the right
ordering" is a precise, learnable thing — not a vibe.

6. **Programming Language Memory Models** — Russ Cox (research.swtch.com) — *the sequel to #4*
   https://research.swtch.com/plmm
   How a language-level memory model (C/C++/Java/Go/Rust) sits on top of the hardware one, what
   sequential consistency for data-race-free programs actually promises, and why `memory_order`
   exists. Read directly after #4. Together they are the single best free treatment of memory models.

7. **Preshing on Programming — the lock-free/atomics series** — Jeff Preshing — *read as a set*
   https://preshing.com/20120612/an-introduction-to-lock-free-programming/
   https://preshing.com/20120710/memory-barriers-are-like-source-control-operations/
   https://preshing.com/20120913/acquire-and-release-semantics/
   https://preshing.com/20130618/atomic-vs-non-atomic-operations/
   The best intuition-builders anywhere for acquire/release, memory barriers, and what "atomic"
   buys you. The "barriers are like source-control operations" analogy is the one that finally makes
   ordering click for most people. Read these four in order; they are short and compounding.

8. **Linux kernel `memory-barriers.txt`** — *the rigorous backstop*
   https://www.kernel.org/doc/Documentation/memory-barriers.txt
   The kernel's own, deliberately pedantic specification of what barriers do and do not guarantee.
   Dense. Do not read it first — read it *after* Preshing, as the formal ground truth that kills any
   remaining hand-waving. Skim the control-dependency and examples sections; keep as reference.

9. **atomic<> Weapons: The C++ Memory Model and Modern Hardware** — Herb Sutter (talk) — *the definitive lecture*
   https://www.youtube.com/watch?v=A8eCGOqgvH4
   A ~3-hour two-part talk that assembles everything above into one coherent picture: hardware
   reordering, the language model, and how to actually use atomics correctly. Watch after the
   Preshing set. The C++ specifics generalize directly to Rust and Go. The single best "put it all
   together" resource in the tier.

10. **Concurrency Is Not Parallelism** — Rob Pike (talk) — *the mental-model reset*
    https://www.youtube.com/watch?v=cN_DpYBzKso
    Short, foundational framing that concurrency is about *structure* (independently progressing
    tasks) while parallelism is about *execution* (things literally at once). Watch early; it clears
    up a confusion that muddies almost every later discussion of runtimes and schedulers.

---

## Tier 3 — Lock-free & synchronization (read it, rarely write it)

The high-status corner of the field. The honest goal for most engineers is to *read, review, and
reason about* lock-free code — recognize an ABA bug, know why a seqlock works, understand what a
memory-reclamation scheme is for — not to ship hand-rolled lock-free structures. Calibrate ambition
accordingly.

11. **Is Parallel Programming Hard, And, If So, What Can You Do About It?** ("perfbook") — Paul McKenney — *the free reference tome*
    https://mirrors.edge.kernel.org/pub/linux/kernel/people/paulmck/perfbook/perfbook.html
    A complete, free, continuously-updated book on real-world parallel programming from the author of
    Linux RCU. This is the free stand-in for *The Art of Multiprocessor Programming*. Do not read
    cover to cover. Read the "Partitioning and Synchronization", "Locking", "Deferred Processing"
    (RCU/hazard pointers), and "Counting" chapters; treat the rest as reference. The counting chapter
    alone reframes how you think about scalable statistics.

12. **Rust Atomics and Locks** — Mara Bos (full book, free online) — *modern, concrete, hands-on*
    https://marabos.nl/atomics/
    The author's free-to-read edition. Even if you are not a Rust programmer, chapters on the memory
    model, building your own locks, and building safe abstractions over atomics are the most
    *concrete* free treatment of the topic — real, compilable code rather than pseudocode. Read
    chapters 1-4 and 9-10. Cross-referenced from the `systems-from-rust` section for the same reason.

13. **The LMAX Disruptor** — technical paper + site — *the canonical mechanical-sympathy case study*
    https://lmax-exchange.github.io/disruptor/disruptor.html
    A production ring-buffer design that beats queues by respecting the hardware: cache-line padding
    to kill false sharing, single-writer principle, no locks on the hot path. The best worked example
    of "design the data structure around the cache." Read the linked technical paper; then read
    Martin Thompson's **Mechanical Sympathy** blog (https://mechanical-sympathy.blogspot.com/) for
    the philosophy behind it.

---

## Tier 4 — OS, scheduling & measurement (make it provable)

The skill that converts the previous three tiers from opinions into numbers. Undervalued and
career-defining: the engineer who can *show* where the time went wins the design argument.

14. **OSTEP — Concurrency and Persistence chapters** (free book) — *the OS backbone*
    https://pages.cs.wisc.edu/~remzi/OSTEP/
    Return to the free OSTEP book for the Concurrency (threads, locks, condition variables,
    semaphores) and Persistence (I/O devices, file systems, journaling) chapters. This is the OS-level
    grounding under everything above — what a thread *is* to the kernel, what a syscall costs, how the
    scheduler and the I/O path actually behave. Read the concurrency chapters after Tier 2 as
    reinforcement.

15. **Brendan Gregg — Linux Performance** — *the measurement portal*
    https://www.brendangregg.com/linuxperf.html
    The hub for the free stand-in to Gregg's (paywalled) *Systems Performance* book: the USE method,
    the tool landscape (perf, ftrace, bpftrace), and how to methodically localize a bottleneck instead
    of guessing. Read the USE-method material and the "Linux Performance Analysis in 60 Seconds"
    checklist; keep the page as a launchpad.

16. **Flame Graphs** — Brendan Gregg — *the one visualization to master*
    https://www.brendangregg.com/flamegraphs.html
    How to read and generate flame graphs — the single most useful way to see where CPU time actually
    goes. Learn to read one fluently; it is the fastest path from "the system is slow" to "this stack
    is hot." Short, practical, high return.

17. **What Every Computer Scientist Should Know About Floating-Point Arithmetic** — David Goldberg — *the perennial gap*
    https://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html
    The classic on rounding, representable values, catastrophic cancellation, and why `==` on floats is
    a trap. Not concurrency, but a fundamental every systems programmer is eventually bitten by. Read
    once, remember it exists, return when a numeric result looks wrong.

---

## How to read this list

- Tiers 0-2 are the load-bearing foundation. If you only ever do those, you have gotten most of the
  value. Do not jump to lock-free algorithms (Tier 3) before you can explain acquire/release from
  memory.
- After Tier 2 you should be able to answer, unprompted: *why can two threads disagree about the
  order of two stores, and what exactly does an acquire-load synchronize with?* If you cannot, re-read
  #4, #6, and the Preshing set before moving on.
- Tier 3 is where to **cap your ambition on purpose**: aim to read and verify, not to author.
- Tier 4 is what makes the whole thing professional rather than academic — always be able to produce
  the number.

---

## From knowledge bank to capability — a one-year plan and how to measure it

Banking facts is not the goal; changing what you can *do* is. The failure mode of a list like this is
passive consumption — you read Drepper, nod, and retain nothing usable. The fix is to attach an
**active output** to every major topic and to measure progress by capability, not by pages read.

### A realistic 1-year goal

By the end of a year of steady background reading (a few hours a week alongside real work), a
concrete, honest target:

> **I can look at an unfamiliar piece of concurrent or performance-sensitive systems code, form a
> specific hypothesis about how it behaves on real hardware (cache traffic, contention, ordering,
> where the time goes), design a measurement that confirms or refutes that hypothesis, and explain
> the result to a colleague from first principles — without hand-waving.**

That is deliberately about *transferable judgment*, not trivia. It is the difference between "I've
heard of false sharing" and "I suspect false sharing here, here's the `perf c2c` output that shows
it, and here's the padding that fixed it."

### How to make it stick — one concept, one artifact

For each major concept, produce a small durable artifact. This is the whole game; reading without an
artifact is entertainment.

- **Teach-back / write-up.** For each Tier, write a short explainer *from memory* (a personal note or
  a public post like a GFS-style writeup). If you cannot explain acquire/release without looking it
  up, you have not learned it yet. Writing is the cheapest lie-detector for understanding.
- **One concept → one experiment.** Turn each idea into a tiny reproducible measurement:
  - *Cache lines / false sharing:* write two threads incrementing adjacent vs. cache-line-padded
    counters; measure the slowdown. Confirm it matches Drepper's numbers.
  - *Memory ordering:* write a litmus test (store/store vs. load/load) and observe reordering on
    weak hardware, or reason about why x86 hides it.
  - *Branch prediction:* benchmark a branch on sorted vs. shuffled data; explain the gap.
  - *Lock-free:* implement a single-producer/single-consumer ring buffer, then benchmark it against a
    mutex-guarded queue and explain *why* the numbers differ.
  - *Measurement:* take one real slow thing you work on, capture a flame graph, and find the hot
    stack before reading any code.
- **Apply it to real work within two weeks.** Deliberately use each concept on something you are
  actually building — a data-structure layout choice, a review comment grounded in the memory model,
  a perf investigation you drive with numbers. Knowledge that never touches a real change decays.
- **Spaced re-derivation.** Every month, re-derive one earlier concept from scratch on a whiteboard
  (or in a note). Fundamentals are retained by re-deriving, not by re-reading.

### How to chart it (make progress visible)

Track capability, not consumption. A few lightweight instruments:

- **A "can-explain" checklist.** Maintain a list of ~25-30 fundamentals (MESI, false sharing, TLB,
  acquire/release, happens-before, ABA, RCU, USE method, flame-graph reading, NUMA, ...). Mark each:
  *heard of it → can explain it → have measured/used it → could teach it.* The distribution shifting
  right over the year **is** the progress chart. Do not mark a box up without an artifact behind it.
- **An experiment log.** One line per experiment: hypothesis, measurement, result, surprise. A year of
  these is both a portfolio and a spaced-repetition deck.
- **A "wins" log.** Every time a fundamental changed a real decision (caught a bug in review,
  root-caused a regression, won a design argument with a number), record it in one sentence. This is
  the direct translation of knowledge → value, and it is what you point to in a promotion case or a
  self-review.

### Why this translates to career value

The tangible payoff is not "knows more facts." It is:

- **You become the person who can settle performance and correctness arguments with evidence.** In a
  systems org that credibility compounds — your reviews carry weight and your designs get trusted.
- **You root-cause faster.** The measurement skill (Tier 4) means you find the real bottleneck while
  others are still speculating, which is disproportionately visible and valued.
- **You catch whole classes of bugs before they ship** — data races, false sharing, ordering bugs —
  because you can reason about them from the model instead of waiting for a flaky failure.
- **You can mentor.** The teach-back artifacts double as onboarding material, and being the person who
  can explain the hardware model clearly is a quiet form of technical leadership.

The synthesis loop, in one sentence: **read a fundamental → produce a small artifact that proves you
can use it → apply it to a real change within two weeks → record the win.** Repeat ~25 times over a
year and the checklist, the experiment log, and the wins log are simultaneously your progress chart,
your portfolio, and your evidence that the knowledge became capability.

---

## Backlogged sibling track (not built yet)

A second future investment area — **LLM / AI systems** (how modern inference engines and model
internals work at a systems level) — is intentionally deferred for now. It will get its own section
when the AI-fundamentals track in the weekly lists matures. This document stays focused on the
timeless systems fundamentals.
