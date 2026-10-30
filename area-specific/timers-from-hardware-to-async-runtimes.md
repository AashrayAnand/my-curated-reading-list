# Timers: From Hardware to Async Runtimes

A roughly one-hour reading path through hardware counters, Linux timekeeping,
timer interrupts, and high-level deadlines. This is a standalone reference,
not an addition to the weekly schedule. No coding exercises are required.

The central distinction is **reading the time versus requesting a future wakeup**.
A hardware counter supplies time readings. Programmable timer hardware can
generate an interrupt. The operating system and runtime manage many software
deadlines above those devices.

## Reading order and time budget

The estimates apply to the selected sections, not every linked page.
Read in order and stop at the suggested boundaries.

| Order | Reading | Layer | Time |
| --- | --- | --- | --- |
| 1 | [Clock sources, Clock events, sched_clock() and delay timers](https://docs.kernel.org/timers/timekeeping.html) | Hardware counters and interrupts | 12 min |
| 2 | [clock_gettime(2)](https://man7.org/linux/man-pages/man2/clock_gettime.2.html) | Clock semantics | 10 min |
| 3 | [vdso(7)](https://man7.org/linux/man-pages/man7/vdso.7.html) | Reading time from userspace | 8 min |
| 4 | [hrtimers: subsystem for high-resolution kernel timers](https://www.kernel.org/doc/html/latest/timers/hrtimers.html) | Kernel deadline management | 10 min |
| 5 | [timerfd_create(2)](https://man7.org/linux/man-pages/man2/timerfd_create.2.html) | Timers in event loops | 8 min |
| 6 | [New Timer implementation](https://tokio.rs/blog/2018-03-timers) and [Tokio Sleep](https://docs.rs/tokio/latest/tokio/time/struct.Sleep.html) | Runtime data structures and APIs | 12 min |
| | **Total** | | **60 min** |

## 1. Hardware: A Counter Is Not a Wakeup

- [ ] Read the introduction, **Clock sources**, and **Clock events**.

**Tags:** Foundational, Mental Model

**Why read it:** The kernel authors connect hardware timer registers to two
different abstractions. A clocksource answers "what time is it?" A clockevent
device requests an interrupt at a future time.

Spend about eight minutes on counters and four on clock events. Follow the
counter-to-nanoseconds conversion and the wraparound example. Skip
`sched_clock()` and delay timers on this pass.

**Question:** Why can reading a counter avoid an interrupt, while waking a
sleeping processor requires another mechanism?

This starts at hardware registers and counter frequency, not the electronics
of crystal oscillators or clock-generation circuits.

## 2. Clock Semantics: Which Time Are You Measuring?

- [ ] Read **DESCRIPTION**, focusing on `CLOCK_REALTIME`, `CLOCK_MONOTONIC`,
  `CLOCK_MONOTONIC_RAW`, and `CLOCK_BOOTTIME`.

**Tags:** Foundational, Practical

**Why read it:** A deadline only has meaning relative to a clock.
Wall-clock time can jump. Linux monotonic time avoids discontinuous wall-clock
changes, but its rate can be adjusted. Raw monotonic time excludes those rate
adjustments.

Notice the suspend distinction: Linux `CLOCK_MONOTONIC` excludes suspended
time, while `CLOCK_BOOTTIME` includes it. Do not assume every platform's
high-level monotonic clock has identical suspend behavior.

**Question:** Should a ten-minute timeout expire while a laptop is suspended?

## 3. Reading Time: Why a Clock Call Need Not Be a System Call

- [ ] Read **DESCRIPTION**, **Example background**, and **Finding the vDSO**.

**Tags:** Mental Model, Practical

**Why read it:** The virtual dynamic shared object, or vDSO, exposes
kernel-provided functions in a process's address space. Supported time reads
can use that path instead of entering the kernel through a system call.
Availability depends on the platform and requested clock.

Skip symbol-versioning and architecture tables. Keep clock-reading cost
separate from the cost of registering, cancelling, or firing a timer.

**Question:** Why is checking a deadline different from scheduling a wakeup?

## 4. Kernel Timers: Why Different Workloads Need Different Structures

- [ ] Read the opening rationale and **hrtimer subsystem implementation details**
  through the explanation of the red-black tree.

**Tags:** Foundational, Historical Context, Pattern Recognition

**Why read it:** The document explains why Linux added high-resolution timers
beside its existing timer-wheel subsystem. It did not replace every kernel
timer with one universal mechanism.

Compare coarse timeouts, which are often cancelled before expiry, with
time-ordered high-resolution deadlines. Stop before the detailed implementation
discussion. Treat this as design history, not a complete map of today's kernel.

**Question:** Why might a timing wheel suit one timer workload and a tree suit another?

## 5. Event Loops: Exposing Expiration as Readiness

- [ ] Read **DESCRIPTION**, the clock choices, the opening `timerfd_settime()`
  explanation, and **Operating on a timer file descriptor**.

**Tags:** Practical, Mental Model

**Why read it:** A timer file descriptor becomes readable when expirations
occur. An event loop can wait for it alongside other descriptors using
`select`, `poll`, or `epoll`. Read how relative and absolute timers differ,
and how `read()` reports the expiration count.

**Important:** This is one Linux interface, not a claim that Tokio creates a
`timerfd` for each `Sleep`. Runtimes can manage deadlines in userspace and
use a timeout on their operating-system wait.

**Question:** Why does an application with many software timers not need a
dedicated hardware timer or operating-system thread for each one?

## 6. Runtime Timers: Wheels, Futures, and Resets

- [ ] Read the Tokio post, then the `Sleep` overview, its `select!` example,
  and the **reset** method documentation.

**Tags:** Historical Context, Pattern Recognition, Practical

**Why read it:** The post explains hierarchical timing wheels with a concrete
layout. The API documentation connects that structure to a reusable future
and an absolute deadline.

The post dates from 2018. Use it for the data-structure explanation, not
current API syntax. Use the `Sleep` documentation for current examples.
Resetting a sleep reuses its associated state, but it is not cost-free.
Costs depend on the timer's state and runtime implementation.

**Question:** What is the difference between constructing a sleep future,
polling it, waking its task, and the executor actually running that task?

## Put the Layers Together

Keep two paths separate:

- **Read time:** hardware counter -> kernel timekeeping -> userspace clock API.
- **Wait for time:** software deadline -> runtime/OS wait -> timer event ->
  runnable thread/task -> scheduled execution.

The exact wait path depends on the runtime and operating system. Expiration
does not guarantee immediate execution. Interrupt handling, scheduling, and
cooperative task execution can add delay.

After reading, explain why a timer can expire without its application code
running immediately, and why a cheap clock read does not prove a cheap timer reset.

## Related Reading and Notes

- [Disk I/O, Blocking vs. Async I/O, and Rust Async Runtimes](disk-io-blocking-vs-async-and-rust-runtimes.md)
- [Concurrency at Scale](concurrency-at-scale.md)

- A distinction that became clearer:
- A question about the hardware-to-runtime path:
- A claim I would measure rather than assume:

Public source links checked on October 30, 2026.
