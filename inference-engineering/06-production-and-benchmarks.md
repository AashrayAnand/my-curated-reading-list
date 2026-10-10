# 06 — Production, reliability, and reproducible benchmarks

[Track map](README.md) · [Previous: distributed inference](05-distributed-inference.md) · [Next: modalities](07-modalities.md)

## Scope and prerequisites

Move from “the model runs” to a service that meets quality, latency, availability, and cost
constraints. Prerequisites: prefill/decode, queueing/backpressure, container basics, and modules
04–05's execution and topology distinctions. The main path is reading/design work, not an
instruction to provision Kubernetes or rent GPUs.

Use **45-min sessions**, one failure mode or benchmark question per session. All boxes begin
unchecked: curated coverage is not demonstrated completion. Book ranges are printed pages;
PDF links include the two-page front-matter offset.

## Curated reading path

- [ ] **Book spine — stack and measurement:** [§4.3 “Inference Engines,” including §4.3.1 “vLLM,” §4.3.2 “SGLang,” and §4.3.3 “TensorRT-LLM,” pp. 105–111](../resources/Inference%20Engineering.pdf#page=107);
  [§4.5 “Performance Benchmarking and Load Testing,” including §4.5.1 “Performance Benchmarking Tooling,” §4.5.2 “Performance Benchmarking Tips,” and §4.5.3 “Profiling Performance,” pp. 112–115](../resources/Inference%20Engineering.pdf#page=114).
  **[Practical]** Why: distinguish serving benchmarks from profiles and model evaluations.
  Ask: what baseline, prompt contents, sequence lengths, arrival process, and sampling settings
  make a comparison meaningful? Treat the book's engine rankings as hypotheses to test,
  and distinguish TensorRT from TensorRT-LLM and its release-specific backends.
- [ ] **Book spine — packaging and capacity:** [§7.1 “Containerization,” §7.1.1 “Dependency Management,” §7.1.2 “NIMs,” and §7.2 “Autoscaling,” including all §7.2.1–§7.2.5 subsections, pp. 179–193](../resources/Inference%20Engineering.pdf#page=181).
  **[Foundational / Pattern Recognition]** Why: connect pinned images and architecture-specific
  engines to batch/concurrency limits, cold starts, routing/queues, scale-to-zero, and independently
  scaled pipeline stages. Ask: does changing replica count also acquire nodes? Which startup
  step dominates: capacity, image, weights, compilation, or warmup?
- [ ] **Book spine — multi-cloud and assurance:** [§7.3 “Multi-Cloud Capacity Management,” including §7.3.1 “GPU Procurement,” §7.3.2 “Geo-Aware Load Balancing,” §7.3.3 “Building for Reliability,” and §7.3.4 “Security and Compliance,” pp. 193–199](../resources/Inference%20Engineering.pdf#page=195);
  [§7.4 “Testing and Deployment,” including §7.4.1 “Zero-Downtime Deployment,” §7.4.2 “Cost Estimation,” and §7.4.3 “Observability,” pp. 199–204](../resources/Inference%20Engineering.pdf#page=201).
  **[Practical / Mental Model]** Why: include procurement, reservations/on-demand/spot,
  failure domains, residency, rollout headroom, and total ownership cost. Ask: can a workload
  plane keep serving if the global control plane fails? Is failover capacity truly warm and
  legally eligible, and who pays for it?
- [ ] **Book spine — clients and vendor perspective:** [§7.5 “Client Code,” including §7.5.1 “Client Latency Overhead,” §7.5.2 “Asynchronous inference,” and §7.5.3 “Streaming and Protocol Support,” pp. 204–207](../resources/Inference%20Engineering.pdf#page=206);
  [§7.6 “Production Inference with Baseten,” p. 208](../resources/Inference%20Engineering.pdf#page=210).
  **[Pattern Recognition]** Why: include connection reuse, asynchronous jobs, streaming, and
  provider-managed operation in the full service boundary. Ask: what does the provider own,
  and what must your client, quality evaluation, and observability still verify? Read §7.6
  as vendor positioning, not independent evidence of superiority.
- [ ] **Official reference — [NVIDIA GPU Operator README](https://github.com/NVIDIA/gpu-operator/blob/main/README.md).**
  **[Practical]** Why: identify driver/toolkit, device exposure, GPU feature discovery, MIG,
  and monitoring responsibilities in Kubernetes. Ask: what is host-level versus image-level,
  and what does healthy pod scheduling fail to tell you about model readiness?
- [ ] **Official reference — [KEDA README](https://github.com/kedacore/keda/blob/main/README.md).**
  **[Pattern Recognition]** Why: connect event/queue signals to replica scaling and activation
  from zero. Ask: what backs the queue metric, how are stale metrics handled, and what separate
  controller obtains nodes? KEDA is not a GPU procurement mechanism.
- [ ] **Official benchmarking anchor — [NVIDIA GenAI-Perf README](https://github.com/triton-inference-server/perf_analyzer/blob/main/genai-perf/README.md).**
  **[Practical]** Why: inspect client-side latency/throughput definitions, endpoint support,
  workload generation, and output artifacts. Ask: which values include client/network delay,
  and what must be fixed to compare vLLM, SGLang, and TensorRT-LLM fairly?
- [ ] **Official reference — [OpenTelemetry GenAI semantic conventions README](https://github.com/open-telemetry/semantic-conventions-genai/blob/main/README.md).**
  **[Practical]** Why: connect request traces, model/provider identifiers, usage, and errors
  to application observability. Ask: which conventions are stable in the version you use,
  and how do you avoid exporting prompts, outputs, or high-cardinality sensitive labels?

These exact source files were fetched through GitHub MCP; see the [source record](sources.md).
The old OpenTelemetry GenAI page is a move notice, so this path uses the maintained repository.
Documentation links within upstream files are not automatically verified readings.

## Production checklist to reason through

| Concern | Decision or failure question |
|---|---|
| Orchestration | Kubernetes schedules pods; GPU Operator enables device management. Ray provides distributed actors/serving coordination; Slurm allocates scheduled cluster jobs. Which layer owns replicas, admission, gang placement, and failures? They are not interchangeable engines. |
| GPU sharing | MIG partitions supported GPU resources; time-sharing is not equivalent isolation. Confirm profile memory, kernel support, and multi-GPU communication constraints; host CPU/RAM allocation is a separate infrastructure decision. |
| Autoscaling | Combine queue age/depth, active tokens/requests, TTFT/ITL, and GPU pressure. Use bounded min/max capacity, hysteresis, startup awareness, and scaling delays; request counts alone ignore sequence-length differences. |
| Cold starts | Separate node acquisition, image pull, weight load, compilation, graph capture, and warmup. Cache artifacts by model revision, architecture, engine, driver/CUDA compatibility, and configuration; readiness follows successful warmup. |
| Overload | Bounded queues, token/context budgets, fair admission, deadlines, load shedding, and explicit retry signals prevent unbounded waiting. Limit retries with backoff/jitter and a shared budget; autoscaling cannot repair infinite demand. |
| Draining/spot | Stop admitting new work before termination; allow bounded in-flight completion, propagate cancellation, and define interruption/retry behavior. Lost KV/stream state is not magically checkpointed. Keep spot interruption within the reliability budget. |
| Routing | Balance queue delay with prefix/KV and LoRA locality. **Semantic routing** selects models or paths by task/content; unlike locality routing it can change quality, so evaluate misroutes and fallback policies. |
| Health/observability | DCGM supplies GPU health/utilization signals; OpenTelemetry connects client, router, queue, worker, and downstream spans. Track errors, queue age, sequence sizes, cache hits, replica readiness, TTFT, ITL, total latency, and resource pressure together. GPU utilization alone is not an SLO. |
| Quality/security | Version model/tokenizer/templates and maintain task/regression slices. Protect weights, credentials, caches, and data with authorization, isolation, encryption, retention limits, and audit trails; enforce quotas against abuse. Prompt injection and unsafe tool use also need application controls. |
| Multi-cloud | Map topology, quotas, artifact availability, egress costs, residency, and provider failure domains. Test active-active or warm standby with spare capacity; failover can lose cache locality and cannot generally continue a partially delivered stream transparently. |
| Deployment | Canary with readiness, extra capacity, explicit quality/latency/error rollback criteria, and draining. Compare sampled shadow traffic only with appropriate data authorization; GPU headroom is not free. |
| Client/protocol | Reuse connections and bound async concurrency. Distinguish an asynchronous client call from a durable asynchronous job with idempotency, status/polling or authenticated webhooks. HTTP/SSE fits one-way text; WebSockets and gRPC support duplex streams. Cancellation and slow-consumer backpressure must propagate. |

Do not inherit publication-specific absolutes: continuous batching still has scheduling and
admission limits; protocol overhead depends on implementation; a pinned package list alone
does not guarantee immutable artifacts or compatible host drivers. Verify those boundaries.
For suspected silent throttling, correlate clock/power/temperature and available throttling/error
signals with reduced achieved throughput; high utilization alone cannot diagnose it.
Define TTFT/ITL SLO thresholds and the fraction of eligible requests required to meet them.

## A reproducible engine comparison

1. **Freeze the contract:** model/weight revision, tokenizer/chat template, context limit,
   output policy, sampling, quality dataset, and acceptable degradation. Pick an architecture
   and precision supported by all compared versions; report unsupported cells rather than
   silently replacing models.
2. **Record the environment:** GPU model/count/MIG profile, topology, CPU/RAM, drivers/CUDA,
   engine version/container digest, flags, parallelism, memory/KV limits, and client placement.
   Keep a common-configuration comparison separate from each engine's independently tuned run.
3. **Define traffic:** input/output-length distributions, prompt contents, prefix reuse,
   arrival rate/bursts and concurrency. Open-loop arrivals reveal queue growth; closed-loop
   clients can hide overload by slowing offered load. Record which you used.
4. **Measure consistently:** warm and cold paths separately; repetitions and raw request
   results. Define TTFT's start point and ITL over streamed tokens, remembering chunks may
   contain multiple tokens. Report percentile TTFT/ITL/end-to-end latency, request/token
   throughput, errors, and **goodput satisfying both latency and quality requirements**.
5. **Choose a Pareto frontier:** compare latency, goodput, task quality, and cost per successful
   useful result. Include idle/warm capacity, CPU/storage/network, retries, and engineering
   cost rather than pricing only busy GPU time. Change one factor, then test interactions.
   Also report total serving cost divided by successful output tokens with explicit units and
   input/output accounting. A cheaper token is not a cheaper solved task if quality or retries worsen.

The public teardown should include configs, revisions, raw measurements, quality checks,
latency/throughput Pareto plots, pricing date, failed/unsupported cases, and limits of generalization.
Publish only authorized synthetic/public inputs—never prompts, credentials or proprietary traces.

## Database analogy — and its limits

Connection pools, admission control, queue-aware autoscaling, canaries, and benchmark
discipline transfer directly from database operations. However, a long streamed generation
has partial externally visible output, model-dependent quality, and expensive transient KV
state; retrying it is not a transparent transaction replay. Semantic routing is not merely
choosing an equivalent physical plan.

## Capability gate

- [ ] Produce a reproducibility manifest and explain why a vendor “tokens/s” number is insufficient.
- [ ] Trace a p99 latency spike from client through queue, prefill, decode, and network;
  propose an alert that separates saturation from GPU failure.
- [ ] Specify overload, draining, failed-job retry, canary rollback, and residency-safe failover
  behavior without promising seamless continuation of every stream.

## Optional exercises

- **No GPU:** design a queue/cold-start timeline and cost spreadsheet from clearly labeled
  assumptions; walk through a spot interruption, a slow consumer, and a region failure.
  Specify a gateway that chooses a quantized local model or a frontier API: define complexity
  signals, quality-tested thresholds, misroute/fallback behavior, budget and privacy constraints.
  A heuristic prompt-length rule is a baseline, not a reliable reasoning-difficulty oracle.
- **Existing endpoint:** run a small bounded GenAI-Perf experiment with authorized synthetic
  inputs; archive settings and raw results. A remote endpoint cannot establish GPU topology
  or engine equivalence unless the operator supplies those details.
- **Existing lab cluster only:** exercise scale-from-zero, readiness, canary and draining with
  a supported model. Observe queue age and client cancellation; do not provision a cluster
  merely to complete this reading path.
  The queue-based spot autoscaler exercise must separately handle replica scaling and node
  acquisition, maximum spend, startup delay, interruption notices and bounded drain deadlines.
