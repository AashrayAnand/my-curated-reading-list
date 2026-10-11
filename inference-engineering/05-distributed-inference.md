# 05 — Distributed inference and topology

[Track map](README.md) · [Previous: execution](04-execution-and-compression.md) · [Next: production](06-production-and-benchmarks.md)

## Scope and prerequisites

Choose between a bigger replica, more replicas, and separate prefill/decode workers.
Prerequisites: module 04's kernel/precision distinctions, KV-cache sizing, prefill/decode,
and basic distributed queues and communication. Read the MoE router explanation before
expert parallelism; no multi-node hardware is needed for the main path.

Work in **45-min sessions**. Checkboxes denote readings or evidence you produce, not
capabilities already demonstrated. Book links use PDF page anchors; ranges are printed pages.

## Curated reading path

- [ ] **Book spine — hardware and architecture:** [§2.2.4 “Mixture of Experts Models,” pp. 53–54](../resources/Inference%20Engineering.pdf#page=55);
  [§3.3 “Instances” and §3.3.1 “Multi-GPU Instances,” pp. 83–86](../resources/Inference%20Engineering.pdf#page=85).
  **[Foundational / Mental Model]** Why: total parameters determine weight residency, while
  activated experts determine per-token work; routing varies by layer and token. Ask: how
  does a large batch broaden the set of touched experts? Which link connects each pair of
  GPUs, and is a published number in GB/s or Gb/s?
- [ ] **Book spine — parallelism:** [§5.4 “Model Parallelism,” including §5.4.1 “Tensor Parallelism for Lower Latency,” §5.4.2 “Expert Parallelism for Higher Throughput,” and §5.4.3 “Multi-Node Inference,” pp. 142–148](../resources/Inference%20Engineering.pdf#page=144).
  **[Pattern Recognition]** Why: compare TP's within-layer collectives, PP's stage dependencies,
  and EP's expert dispatch/combine. Ask: where is synchronization on a single token's critical
  path? When would TP within nodes plus PP between nodes beat cross-node TP? When would
  replica-level data parallelism be cheaper than either?
- [ ] **Book spine — disaggregation:** [§4.4 “NVIDIA Dynamo,” pp. 111–112](../resources/Inference%20Engineering.pdf#page=113);
  [§5.5 “Disaggregation,” including §5.5.1 “How Disaggregation Works,” §5.5.2 “When to Use Disaggregation,” and §5.5.3 “Dynamic Disaggregation with NVIDIA Dynamo,” pp. 148–151](../resources/Inference%20Engineering.pdf#page=150).
  **[Mental Model / Cutting Edge]** Why: specialize workers and scale phases independently,
  but pay for KV transfer, layout changes, queues, and coordination. Ask: what happens on
  a prefix hit or short prompt? What evidence justifies this over colocated chunked prefill?
- [ ] **Primary research implementation — [DistServe README](https://github.com/LLMServe/DistServe/blob/main/README.md).**
  **[Pattern Recognition]** Read the research overview and evaluation design before installation.
  Why: study prefill/decode interference and SLO-constrained **goodput**, not only raw tokens/s.
  Ask: which sequence mix, placement, interconnect, and latency targets underpin the result?
  Paper-first alternative: analyze the described design and published evaluation without
  reproducing its multi-GPU setup.
- [ ] **Official reference — [NCCL README](https://github.com/NVIDIA/nccl/blob/master/README.md).**
  **[Foundational / Practical]** Why: map all-reduce, all-gather, reduce-scatter, and all-to-all
  to the computations that require them. Ask: which operation moves activations, which combines
  partial products, and which depends on expert skew? A collective library does not make
  a slow or misconfigured network disappear.
- [ ] **Official implementation anchor — [NVIDIA Dynamo README](https://github.com/ai-dynamo/dynamo/blob/main/README.md).**
  **[Practical / Cutting Edge]** Follow routing, planner, disaggregated-serving, NIXL transfer,
  and KV-block-manager documentation selectively. Why: locate engine execution versus
  orchestration, cache hierarchy, and resource-control responsibilities. Ask: which components
  maintain soft state, and what survives a worker loss? Confirm backend/release support rather
  than assuming every engine exposes identical features.
- [ ] **Research implementation reference — [Ring FlashAttention README](https://github.com/zhuzilin/ring-flash-attention/blob/main/README.md).**
  **[Mental Model]** Why: see how attention can partition **context**, rather than weights,
  and stream remote K/V blocks while computing local queries. Ask: what remains replicated,
  and how do communication overlap, causal masking, and context length affect the break-even?
  Continue with video-specific applications in [module 07](07-modalities.md).

The linked source files were fetched through GitHub MCP; see the [source record](sources.md).
Direct DistServe/Ring Attention paper verification is deferred. Read their verified
implementation overviews first; do not treat an upstream paper link as independently verified.

## Decision model

| Choice | What is partitioned | Main risk to explain |
|---|---|---|
| TP | Matrices/heads within layers | Frequent collectives can dominate small matmuls and decode |
| PP | Model layers into stages | Bubbles, stage imbalance, and serial dependencies; batching changes utilization |
| EP | MoE experts | Token dispatch/combine, skewed hot experts, all-to-all congestion |
| Context parallelism | Sequence/context attention work | Replicated weights, remote K/V traffic, masking and load balance |
| Separate replicas | Independent requests | Duplicate weights/caches; locality competes with queue balance |
| Prefill/decode disaggregation | Inference phases | KV transfer plus extra queues can exceed avoided interference |

**Topology is an input to the plan.** NVLink/NVSwitch, PCIe, NIC attachment, NUMA placement,
and rack fabric are different paths. RDMA is a transfer/access mechanism, not a synonym
for InfiniBand: supported Ethernet/RoCE fabrics can also provide it. NCCL chooses communication
paths; GPUDirect RDMA requires supported hardware/software and appropriate connectivity.
Inspect actual instance topology and measured bandwidth/latency. The book's network-speed
comparisons and eight-GPU-node examples are publication-specific, not universal ceilings.

**MoE sparsity does not mean small resident weights.** EP changes ownership of experts,
while the router still chooses experts per token/layer and must combine their outputs.
TP and EP can coexist, often with different strategies for attention and MoE layers.
“TP for latency, EP for throughput” is a starting hypothesis: expert skew, batch size,
collective implementation, and topology can reverse the result.

**Disaggregation has a measurable break-even.** Estimate KV payload and transfer time
(`bytes / effective bandwidth`, plus fixed overhead) before allocating separate workers.
Include precision, layout conversion, prefix-hit rate, queueing, and transfer/computation
overlap. Compare conditional local prefill against transfer; the book's volume/model-size
thresholds are heuristics, not admission rules. Dynamic `xPyD` ratios are worker counts,
not TP/PP degrees. A split deployment can increase failure modes and idle capacity.

## Database analogy — and its limits

TP/EP resemble partitioned operators with exchange; expert skew resembles skewed joins.
Disaggregation resembles separating compute stages with different resource needs.
But KV transfer is request/model/version-specific state, not an arbitrary SQL row batch,
and every generated token can revisit synchronization. Database sharding intuition alone
does not predict a decoder's critical path or sampling correctness.

## Capability gate

- [ ] Draw one token's TP, PP, and EP paths, labeling communicated data and blocking points.
- [ ] Produce a weight-plus-KV-plus-buffer memory budget and a topology-aware placement sketch.
- [ ] Defend replicas versus disaggregation using transfer cost, traffic mix, and TTFT/ITL
  constraints; identify one case where each proposed distributed optimization loses.

## Optional exercises

- **No GPU:** hand-calculate collective/transfer lower bounds with explicitly assumed effective
  bandwidth; sketch a skewed MoE dispatch and a failed-worker recovery path. Read the DistServe
  evaluation as an experiment-design critique, not a requirement to rent its hardware.
  Extend the placement sketch to the tweet's 405B/eight-node scenario: state GPUs per node,
  dtype, weight/KV/buffer budgets, TP/PP layout and link topology before claiming it fits.
- **Existing multi-GPU access:** compare one supported TP configuration with independent
  replicas on identical traffic. Inspect topology and collectives before explaining results.
  If two nodes are available, compare within-node and cross-node TP at matched model, precision,
  total GPU count and workload; separate communication from compute and queueing.
- **Existing cluster access only:** test a supported Dynamo colocated/split recipe with bounded
  traffic; vary prompt length and prefix hits, measure KV transfer and both queue times, then
  terminate workers to observe cleanup. Set a spend/resource cap; this is not a capability gate.
