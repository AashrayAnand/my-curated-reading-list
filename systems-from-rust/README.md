# Systems From Rust

Learn systems concepts *through* the Rust language — then strip the abstraction away to see the
machine underneath. The premise: Rust's type system makes normally-invisible systems ideas
(ownership, stack vs. heap placement, zero-cost abstraction, what a "task" really is) explicit in
the source, so it is an unusually good lens for learning them. But the lens only helps if you keep
going *past* the Rust surface to what the compiler actually emits and what the OS actually does. So
each entry here pairs "how Rust models this" with "what this compiles to and why the systems concept
it illuminates matters beyond Rust."

This is a growing section. It starts with async/futures because that is where the gap between the
Rust abstraction and the underlying machine is widest — an `async fn` looks like a function but is
really a hand-written state machine, and misunderstanding that has real, measurable consequences
(oversized futures, blown stacks, mysterious `.await` costs).

Every link below is free and open on the public internet and was verified live.

---

## Entry 1 — What a future really is: Rust async internals at the systems level

**The question this answers:** what *is* a future, mechanically? How is it different from an OS
thread? What actually happens to your local variables across an `.await`? Why do people worry about
"large futures", and what is the difference between a future you `spawn` and one you just `.await`
inline? By the end you should be able to explain, from first principles, why a deeply-nested
`async fn` can produce a surprisingly large state machine, and why holding a big buffer across an
`.await` bloats it.

### Time budget and how to weight it

Total: ~6-8 hours. The center of gravity is Tier 2 (the state-machine transform). Tiers 1 and 3
exist to make that transform meaningful — Tier 1 gives you the thread-vs-task contrast, Tier 3 turns
the model into an actual size/perf intuition you can act on.

| Area | Tier | Suggested time | Why this weight |
| --- | --- | --- | --- |
| The model: future vs. thread, poll, the executor loop | 1 | ~2 h | The conceptual base. What a task *is* and why it has no stack of its own. |
| The transform: `async fn` → state machine, Pin, self-reference | 2 | ~3 h | The load-bearing core. This is where "a future is a struct whose size is your across-await locals" comes from. |
| The consequences: future size, spawned vs. inlined, the lints | 3 | ~2 h | Turns the model into a decision: what to box, what to spawn, when a buffer is too big to hold across `.await`. |

Rule of thumb: **the size of a future is the sum of the local state that must survive across its
`.await` points — internalize that one sentence and everything else here is elaboration.**

---

### Tier 1 — The model: what a future is, and how it differs from a thread

Build the mental contrast first: an OS thread has its own kernel-scheduled stack; a Rust task is a
stackless state machine the *runtime* drives by calling `poll`. Get this straight before any syntax.

1. **Async in depth** — Tokio tutorial — *the anchor*
   https://tokio.rs/tokio/tutorial/async
   Builds a working future and a mini-executor by hand, so `Future::poll`, `Poll::Pending/Ready`,
   and the `Waker` stop being magic. This is the single best free "show me the mechanism" starting
   point. Do the whole thing, typing it out — the hand-built executor is the payoff.

2. **Futures Explained / Executing Futures and Tasks** — the async book — *the reference spine*
   https://rust-lang.github.io/async-book/
   The official book. Read the "Under the Hood: Executing Futures and Tasks" chapter
   (https://rust-lang.github.io/async-book/02_execution/01_chapter.html) for the executor/reactor/
   waker split. Use it as the authoritative reference alongside the Tokio tutorial's hands-on version.

3. **The Async Await section of "Writing an OS in Rust"** — Philipp Oppermann — *futures with zero runtime hidden*
   https://os.phil-opp.com/async-await/
   Implements cooperative multitasking with futures on **bare metal, no Tokio** — so you see the
   state machine and executor with nothing hidden by a framework. The clearest demonstration that a
   task is "just" a polled state machine and a runtime is "just" a loop that polls ready tasks. Read
   the "Async/Await" and "The Future Trait" portions; the earlier kernel setup can be skimmed.

4. **Why async Rust?** — withoutboats — *the design rationale*
   https://without.boats/blog/why-async-rust/
   From one of the async designers: *why* Rust chose stackless, poll-based, allocation-free-by-default
   futures instead of green threads — and what that buys and costs. Read this to understand that the
   state-machine model is a deliberate systems tradeoff (no per-task stack, no forced heap
   allocation), not an accident. This is the "future vs. thread" contrast stated by the source.

---

### Tier 2 — The transform: `async fn` becomes a state machine

The core. An `async fn` is compiled into an anonymous struct — an enum of states — whose fields are
exactly the local variables that must live across `.await` points. This is *the* fact that explains
future size, `Pin`, and self-reference. Spend the most time here.

5. **Understanding Rust futures by going way too deep** — fasterthanlime (Amos) — *the definitive deep dive*
   https://fasterthanli.me/articles/understanding-rust-futures-by-going-way-too-deep
   Exactly what the title says: it desugars async, shows the generated state machine, and follows the
   bytes far enough that nothing stays hidden. Long. Read it in two sittings. This is the single most
   important entry in this section — if you read only one thing past the Tokio tutorial, read this.

6. **The Pin module documentation** — std — *the primary source on why futures can't move*
   https://doc.rust-lang.org/std/pin/index.html
   Once a future is polled it may hold pointers *into itself* (a borrow across an `.await`), so moving
   it would dangle those pointers — hence `Pin`. The std docs are the authoritative explanation of the
   guarantee. Dense; read after #5 when you already feel *why* pinning must exist, then this tells you
   exactly *what* it promises.

7. **Pin, and suffering / the pin blog posts** — withoutboats — *the intuition for #6*
   https://without.boats/blog/pin/
   The designer's-eye view of why `Pin` is shaped the way it is and why self-referential generators
   forced it. Read alongside the std docs to convert the formal guarantee into intuition. If `Pin`
   still feels arbitrary after the std page, this is the fix.

8. **The Future trait** — std — *the exact contract*
   https://doc.rust-lang.org/std/future/trait.Future.html
   Short. The precise signature: `poll(self: Pin<&mut Self>, cx: &mut Context) -> Poll<Output>`. Read
   it deliberately — note that `poll` takes `Pin<&mut Self>`, **not** `self` by value, which is why
   polling a future does *not* move or copy it. That detail kills the common misconception that each
   `.await` copies the future.

---

### Tier 3 — The consequences: future size, spawned vs. inlined, and the lints

Now turn the model into decisions. Nested `.await`s collapse into **one** flat state machine up to
the nearest `spawn`/`Box::pin` boundary, so the whole inline chain's across-await state adds up into
a single object. That is why a big stack buffer held across `.await`, or a giant enum future, is a
real cost — and why `Box::pin` and `spawn` are the tools to break the chain.

9. **How Rust optimizes async/await (Parts I and II)** — Tyler Mandry — *where future size comes from*
   https://tmandry.gitlab.io/blog/posts/optimizing-await-1/
   https://tmandry.gitlab.io/blog/posts/optimizing-await-2/
   From a compiler engineer: how the generator layout is computed, why the size is the max of the live
   across-await state, and the optimizations (and their limits) that shrink it. This is the direct
   answer to "why is my future so big and what controls it." Read both parts; this is the
   size-intuition payoff of the whole entry.

10. **Tokio tutorial — Spawning** — *the spawned-vs-inlined boundary, concretely*
    https://tokio.rs/tokio/tutorial/spawning
    What `tokio::spawn` actually does: it takes ownership of a future, boxes/stores it in a heap task
    cell, and hands it to the scheduler as an independently-pollable unit — the point where one giant
    inline state machine gets split into separately-scheduled tasks. Read it specifically for the
    "a task is the unit the scheduler owns" framing that complements the inline `.await` model from
    Tier 2. The `'static` + `Send` bounds fall out of this naturally.

11. **`clippy::large_futures` lint** — Rust Clippy — *the guardrail to adopt*
    https://rust-lang.github.io/rust-clippy/master/index.html#large_futures
    The lint that flags futures above a size threshold — the practical tool for catching an oversized
    state machine (e.g. a large array or buffer held across `.await`) before it bloats a spawned task
    or overflows a fixed worker stack. Read what it checks and consider enabling it; it operationalizes
    everything in Tier 3. The fix it points you toward is usually "`Box::pin` it" or "don't hold that
    buffer across the await."

12. **Rust Atomics and Locks — the async-relevant chapters** — Mara Bos (free book) — *tying async back to the machine*
    https://marabos.nl/atomics/
    Not an async book, but the chapters on the memory model and building safe concurrent abstractions
    are what make the *runtime* side of async (wakers, cross-thread task handoff, `Send`/`Sync` on
    futures) mechanically sensible. Read after the async material to connect "a task moved between
    worker threads" to the atomics that make that safe. Cross-listed from `systems-knowledge`.

---

### The one-paragraph synthesis (what you should be able to say afterward)

A Rust **future is not a thread** — it has no stack of its own. An `async fn` compiles to an
anonymous struct-shaped **state machine** whose fields are exactly the local variables that must
survive across its `.await` points; `size_of` that future is the sum of that across-await state. The
runtime drives it by calling `poll(Pin<&mut Self>, ...)` — `Pin` because a polled future may hold
references into itself, and `&mut` (not by-value) because polling must **not** move or copy it.
Nested `.await`ed futures **inline** into one flat state machine up to the nearest `spawn` or
`Box::pin`, where the future is moved **once** into a heap **task cell** the scheduler owns as an
independent unit. Therefore a large buffer held across an `.await`, or a deep inline `.await` chain,
produces a single large object that gets copied on the moves before it is pinned and can bloat or
overflow a fixed-size worker stack — which is exactly why `Box::pin`, `spawn`, and the
`large_futures` lint exist. Everything about async performance follows from that one model.

---

## How to read this section

- Do Tier 1 hands-on (type out the Tokio tutorial and skim the phil-opp executor) before reading a
  single word about `Pin`. The mechanism must feel real first.
- Tier 2's #5 (Amos) is the keystone. If your time is limited: Tokio "Async in depth" → Amos deep
  dive → the two Mandry posts, in that order, gets you 80% of the value.
- Always close the loop back to the machine: after each entry, ask "what does this compile to, and
  what would the equivalent look like with raw threads and stacks?" That question is the entire point
  of the `systems-from-rust` framing.
