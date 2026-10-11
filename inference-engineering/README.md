# Inference Engineering: Memory, Scheduling, and Execution

A dedicated area-specific path through Philip Kiely's *Inference Engineering* (Baseten Books,
2026) and the twelve-stage AI infrastructure roadmap. The book is the practitioner spine;
technical explanations and source walkthroughs supply mechanism-level depth.

**Goal:** explain where an inference request spends time and memory, predict which change
should help, and defend a serving decision with quality, latency, reliability, and cost evidence.
No syllabus guarantees employment, and a larger deployment is not automatically better evidence.

## Start here

- [Book and tweet coverage map](book-map.md): every chapter and all twelve stages.
- [01 — Models and inference physics](01-models-and-inference-physics.md).
- [02 — GPU and systems foundations](02-gpu-and-systems-foundations.md).
- [03 — Serving, batching, and KV memory](03-serving-and-kv-memory.md).
- [04 — Execution and compression](04-execution-and-compression.md).
- [05 — Distributed inference](05-distributed-inference.md).
- [06 — Production and benchmarks](06-production-and-benchmarks.md).
- [07 — Modalities beyond text](07-modalities.md).

Read 01–03 in order. Then 04 explains local optimization, 05 explains communication, and 06
integrates production decisions. Read 07 after the model foundations; it does not require
multi-node hardware. [Source verification](sources.md) records what was actually fetched.

## How this builds on the repository

The [decision log](../context/decision-log.md) confirms completion of the Illustrated Transformer
and allocator notes. The [database ledger](../database-expertise/progress.md) does not yet establish
completion of the query-engine intensive. The connections below are **covered or planned
territory**, not a claim that every reading or project is mastered.

| Existing lens | Inference connection | Where the analogy stops |
| --- | --- | --- |
| [Buffer pools and application memory](../area-specific/buffer-pool-management-and-app-memory.md) | KV blocks, reference counts, eviction, offloading | KV state is model-dependent derived state, not durable pages |
| [Query execution](../database-expertise/week-02-query-execution.md) | Batches, fusion, state, pipeline boundaries | SIMT and tensor cores introduce a new execution model |
| [Concurrency at scale](../area-specific/concurrency-at-scale.md) | Continuous batching, staged queues, admission, fairness | A decode request needs repeated service until completion |
| [Disk I/O and async runtimes](../area-specific/disk-io-blocking-vs-async-and-rust-runtimes.md) | Submission overhead, overlap, bounded outstanding work | GPU streams and device scheduling are not Tokio executors |
| [Systems fundamentals](../systems-knowledge/README.md) | Locality, bandwidth, measurement, numerical accuracy | CPU cache intuition does not establish GPU performance |
| Distributed systems and tail latency | Replicas, routing, retries, communication, SLOs | A collective can block every generated token |

Your strongest entry point is **execution + memory ownership + scheduling**. The new foundations
are tensor shapes, transformer state, GPU execution, precision trade-offs, and collective costs.
You do not need to become a training researcher before understanding serving.

## Scope without overload

This is a reference path, not a pre-generated sequence of weekly assignments. Use the existing
45-minute × four-session budget **instead of**, not on top of, another active track. The dated
database sprint has ended, but its completion is not assumed or changed by this path.

For each cycle:

1. Read one bounded book selection.
2. Read one mechanism explanation or selected source section.
3. Compare it with a familiar database mechanism and identify the broken analogy.
4. Produce a closed-book diagram, calculation, or benchmark critique.

Split long selections across cycles. Reference entries are lookups, not extra compulsory books.
The core is reading-first. Optional labs replace reading blocks; they do not expand the budget.
No GPU purchase, 70B deployment, or 405B cluster is required. Use paper analysis without hardware,
a small compatible model on one GPU for local measurements, and multi-node experiments only when
access and spending limits are explicit. CPU results do not establish GPU speedups.

## Capability, not checkboxes alone

For each module record: **recognize → explain → predict → measure → judge → teach**.
Attach evidence before claiming the next level. A checkbox marks a reading, not mastery.
Use a short artifact with:

- problem and workload;
- correctness or quality invariant;
- predicted resource bottleneck;
- evidence, including configuration and measurement boundary;
- counterexample where the technique loses;
- next question.

## Treat the tweet as hypotheses

- Small-batch decode is often bandwidth-bound; prefill often has greater arithmetic intensity.
  Batch size, context, kernels, CPU overhead, communication, and queueing can change the bottleneck.
- PagedAttention reduces allocation waste; it does not promise a fixed percentage of saved VRAM.
- Prefix reuse primarily avoids repeated prefill, not the work of generating every new token.
  Routing can increase cache hits while worsening load balance.
- Quantization is one capacity/performance option alongside model choice, parallelism, and offload.
  Precision changes need quality evaluation, not a claim of universally lossless compression.
- Exact speculative sampling can preserve the target distribution under its assumptions;
  acceptance and verification costs determine speedup.
- Disaggregated serving is a workload-dependent trade-off, not a universal architectural mandate.
- Dollar-per-GPU-hour and speedup claims are dated observations, not constants.

The payoff is transferable systems judgment, not memorizing a vendor stack.
