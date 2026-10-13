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

## Entry 2 — The Reactor pattern: event-driven I/O as a general, language-agnostic idea

**The question this answers:** Entry 1 is Rust-specific (futures, state machines, `Pin`). This
entry steps back to a pattern that **predates async/await and isn't Rust- or Tokio-specific at
all**: the **Reactor pattern** — a general architecture for handling many concurrent I/O sources
on one thread without blocking on any single one. Rust's `mio`/Tokio reactor is just one instance
of a pattern that also shows up as libevent/libuv (C), Java NIO `Selector`, Node.js's event loop,
and nginx's worker architecture. The goal here is to recognize the *shape* of the pattern
independent of any specific language, so that "reactor" stops meaning "a Tokio thing" and starts
meaning "the standard answer to: how do you wait on thousands of sockets with one thread."

The core idea, stated generally: an **event demultiplexer** (a kernel facility — `select`/`poll`/
`epoll`/`kqueue`/IOCP) lets one thread block on *many* registered I/O handles simultaneously and
returns only the ones that are ready. A **reactor** sits on top of that: callers *register*
interest in a handle plus a handler/callback for it, the reactor's single-threaded event loop
blocks in the demultiplexer, and when it wakes up it *dispatches* to the registered handler for
each ready handle. That's the whole pattern — no async/await, futures, or executor required; those
are just how Rust happens to expose "the handler is a task's `Waker`."

### Time budget

Total: ~3-4 hours. This is foundational/cross-language background, not Rust-specific depth.

1. **"Reactor: An Object Behavioral Pattern..."** — Douglas C. Schmidt — *the original, canonical definition*
   https://www.dre.vanderbilt.edu/~schmidt/PDF/reactor-siemens.pdf
   The source of the name and the pattern, written years before async/await existed in any
   mainstream language. Defines the roles precisely: `Handle`, `Synchronous Event Demultiplexer`,
   `Dispatcher`, `Event Handler`. Read this first so the vocabulary is pattern-level, not
   Rust-level — every other resource in this entry is an instance of these same four roles.

2. **`select(2)`/`epoll(7)` man pages** — Linux manual — *the demultiplexer primitives themselves*
   https://man7.org/linux/man-pages/man7/epoll.7.html
   The actual OS-level "synchronous event demultiplexer" from Schmidt's paper: register file
   descriptors once, block in one call, get back only the ready ones. You don't need to write C
   against this directly — you need to recognize it as the mechanism every reactor implementation
   (mio, libuv, Java NIO, libevent) is wrapping, under different names on different OSes
   (`epoll` on Linux, `kqueue` on BSD/macOS, IOCP on Windows).

3. **"Scalable IO in Java"** — Doug Lea — *the pattern worked out concretely, outside Rust entirely*
   https://gee.cs.oswego.edu/dl/cpjslides/nio.pdf
   A classic, widely-cited slide deck building the Reactor pattern up from first principles using
   Java's NIO `Selector` — register channels, one thread demultiplexes, dispatch to handlers. Good
   because it's a second, independent concrete implementation (no Rust, no futures) that makes the
   pattern's shape obvious by repetition.

4. **libuv design overview** — *the pattern inside Node.js, in C*
   https://docs.libuv.org/en/v1.x/design.html
   libuv is the C event-loop library underneath Node.js. Read the "Design overview" page: it names
   the same pieces (event loop, I/O polling via epoll/kqueue/IOCP, handles, callbacks) in a
   completely different ecosystem. Useful for seeing that "the event loop" (Node.js' own popular
   term for this) and "the reactor" (Tokio's term) are the same architectural idea with different
   names.

5. **"Executing Futures and Tasks"** (async-book) — *how Rust's executor/waker map onto the pattern*
   https://rust-lang.github.io/async-book/02_execution/01_chapter.html
   Already in Entry 1 Tier 1. Reread it here specifically to map Schmidt's generic roles onto
   Rust's terms: the reactor is the *demultiplexer + dispatcher*, and a `Waker` is just Rust's name
   for "the registered event handler," wired so that dispatch means "mark this task runnable" rather
   than "invoke this callback directly."

### How the mapping works for `TcpStream::read().await` specifically

Using Schmidt's four roles: the socket's file descriptor is the **Handle**; `epoll`/`kqueue`/IOCP
is the **Synchronous Event Demultiplexer**; the reactor's event loop (one thread parked in a single
blocking syscall covering every registered descriptor) is the **Dispatcher**; and the stored
`Waker` for that task is the **Event Handler** that gets invoked on readiness. So yes — when a task
awaits a socket read that isn't ready yet, it registers its `Waker` with the reactor against that
descriptor (instead of directly with the executor), returns `Pending`, and goes idle. The
dispatcher's blocking call returns when the kernel reports that descriptor ready; the dispatcher
then invokes the registered handler — which, in an async runtime, means calling `.wake()` on that
`Waker`, and *that* is what re-queues the task with the **executor** so it gets polled again. The
reactor itself never runs the task's code; it only ever decides "this handle is ready, invoke its
handler" — scheduling and running the task's code is the executor's job, not the reactor's.

---

### The one-paragraph synthesis (what you should be able to say afterward)

The **Reactor pattern** is a general, language-agnostic architecture (Schmidt, mid-1990s) for
handling many concurrent I/O sources on one thread without blocking on any single one: register a
handle and a handler with a dispatcher, which parks in a single OS-level **event demultiplexer**
call (`select`/`poll`/`epoll`/`kqueue`/IOCP) that can watch thousands of handles at once, and
invokes the matching handler whenever the demultiplexer reports a handle ready. It predates and is
independent of async/await — libevent, libuv/Node.js, and Java NIO all implement the same four
roles (Handle, Demultiplexer, Dispatcher, Event Handler) without any notion of futures. Rust's
async runtimes are simply one more instance of it: the "handler" invoked on readiness is a stored
`Waker`, and invoking it doesn't run the task directly — it re-queues the task with the
**executor**, which is a separate component responsible for actually calling `poll()`. Keeping
reactor (watches readiness, dispatches on events) and executor (runs ready tasks) as two distinct,
cooperating roles — rather than one blurred "async runtime" — is what makes the pattern recognizable
outside of Rust entirely.

---

## Entry 3 — Hardening it in Rust: implementing the reactor pattern for I/O, hands-on

**The question this answers:** Entry 2 is deliberately general and cross-language. This entry comes
back down into one specific language to make it concrete: how does a real async I/O primitive (a
TCP stream) actually register itself with a reactor and get woken, in Rust, **without** pulling in
Tokio? The goal is to build/read a reactor+executor pair small enough to hold in your head
end-to-end, so the Entry 2 roles (Handle, Demultiplexer, Dispatcher, Event Handler) stop being
abstract and become specific lines of Rust you've either written or can point to.

### Time budget

Total: ~4-5 hours. This is the hands-on payoff of Entries 1 and 2 — budget for actually typing the
code out, not just reading it.

1. **"Writing epoll in Rust"** and **"Basic Reactor and Executor for non-blocking IO in Rust"** — zupzup — *build it yourself, no crates beyond `libc`*
   https://zupzup.org/epoll-with-rust/
   https://www.zupzup.org/rust-reactor-executor/index.html
   The best available from-scratch pair: the first post wraps raw `epoll_create1`/`epoll_ctl`/
   `epoll_wait` (via `libc`) into a minimal `Poll`/`Registry`; the second builds a reactor and
   executor on top of it with real callback registration, driving a tiny non-blocking HTTP server.
   Zero async/await, zero Tokio — pure Rust + raw syscalls, so every Entry 2 role is visible as an
   actual struct/function with nothing hidden. Type both out rather than just reading them.

2. **`async-io` crate source** — smol-rs — *a real, production, non-Tokio reactor for comparison*
   https://docs.rs/async-io/latest/async_io/
   https://github.com/smol-rs/async-io
   `smol`'s reactor crate: a single background thread wraps `epoll`/`kqueue`/IOCP (via the
   `polling` crate) and exposes `Async<T>` — a wrapper that takes any standard blocking I/O type
   (including `std::net::TcpStream`) and makes it pollable, storing wakers exactly like Tokio's
   `ScheduledIo` does. Read `Async<T>`'s `poll_readable`/`poll_writable` in the source — it's a
   fraction of the size of Tokio's equivalent and makes the same Handle→Demultiplexer→Dispatcher→
   Waker chain readable in one sitting. This is the direct, non-Tokio answer to "how would a
   `TcpStream` register with the executor/reactor."

3. **async-book — "Applied: Build an Executor"** — *the matching executor half, in Rust, no I/O*
   https://rust-lang.github.io/async-book/02_execution/04_executor.html
   Builds a minimal single-threaded executor (task queue + `Waker` that re-queues itself) from
   scratch. Pair this with #1/#2 above: this chapter gives you the executor side, #1/#2 give you the
   reactor side — together they're the full loop (register → park → dispatch → wake → re-poll) in
   plain, non-Tokio Rust.

### Suggested order

Do #3 first (executor alone is simpler, no I/O or syscalls involved), then #1 (reactor alone, no
futures involved), then wire the two together mentally — this is exactly what #2's `async-io` does
for real, so read it last as the "production-grade version of what you just built."

---

- Do Tier 1 hands-on (type out the Tokio tutorial and skim the phil-opp executor) before reading a
  single word about `Pin`. The mechanism must feel real first.
- Tier 2's #5 (Amos) is the keystone. If your time is limited: Tokio "Async in depth" → Amos deep
  dive → the two Mandry posts, in that order, gets you 80% of the value.
- Always close the loop back to the machine: after each entry, ask "what does this compile to, and
  what would the equivalent look like with raw threads and stacks?" That question is the entire point
  of the `systems-from-rust` framing.
