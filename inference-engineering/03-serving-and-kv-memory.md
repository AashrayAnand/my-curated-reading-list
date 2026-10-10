# 03 — Serving, Batching, and KV Memory

[Home](README.md) · Previous: [GPU foundations](02-gpu-and-systems-foundations.md) ·
Next: [Execution and compression](04-execution-and-compression.md)

**Question:** how does a replica share finite GPU memory and service time among evolving requests?
Prerequisites: module 01's KV math and module 02's device model.

## Reading path

- [ ] **Book: 4.3 (pp. 105–110), 2.5 (pp. 67–69).**
  **Tags:** Foundational, Pattern Recognition.
  **Why:** place vLLM, SGLang and TensorRT-LLM in the stack, and distinguish paged allocation
  from IO-aware attention kernels. Both techniques can coexist; neither is a universal speedup.
- [ ] **Book: 5.3 (pp. 136–141).**
  **Tags:** Practical, Mental Model.
  **Why:** prefix reuse, KV placement, routing and long-context handling are a single memory
  budget problem. Reuse avoids repeated prefill, not every subsequent decode read.
- [ ] **vLLM — [Optimization and tuning](https://github.com/vllm-project/vllm/blob/main/docs/configuration/optimization.md).**
  **Tags:** Practical, Pattern Recognition.
  **Read:** preemption and chunked prefill; defer the parallelism sections to module 05.
  **Why:** relates token budgets, active sequences, recomputation and memory pressure.
  The fetched V1 description prioritizes decode before using remaining budget for prefill.
  Defaults and flags are version-specific, not architectural laws.
- [ ] **vLLM — [Prefix cache design](https://github.com/vllm-project/vllm/blob/main/docs/design/prefix_caching.md).**
  **Tags:** Practical, Mental Model.
  **Read:** key identity, block pool, reference counts, allocation/free/eviction and isolation.
  **Why:** concrete memory-manager invariants rather than a cache-hit slogan.
- [ ] **Reference: [Hugging Face cache strategies](https://github.com/huggingface/transformers/blob/main/docs/source/en/kv_cache.md).**
  **Tags:** Practical.
  **Why:** compare dynamic/static, offloaded and quantized caches and architecture-dependent support.
  Consult when evaluating a cache choice, not as a second compulsory implementation.
- [ ] **Reference: [SGLang overview](https://github.com/sgl-project/sglang/blob/main/README.md).**
  **Tags:** Practical.
  **Why:** locate its serving, prefix reuse and multimodal ecosystem. Feature lists are discovery
  aids, not head-to-head performance evidence.

The PagedAttention paper (*Efficient Memory Management for Large Language Model Serving with
PagedAttention*), Orca and Sarathi-Serve are named follow-up primary readings. Direct paper sites
could not be fetched here, so they are **not linked or claimed as verified assignments**.
The fetched implementation docs and book provide the initial path.
The vLLM historical paged-attention document is not a description of today's complete engine.

## Mechanisms to compare

**Static batching:** requests can wait for batch formation or the longest sequence.
**Continuous batching:** admit/remove work between iterations rather than retaining a fixed batch
until all members finish. This improves utilization but still requires explicit fairness and limits.
**Chunked prefill:** bound uninterrupted prefill work so long inputs need not monopolize decode.
Sweep chunk/token budgets; smaller chunks can add overhead and delay new requests.

**Paged KV allocation:** map logical token blocks to physical blocks, allocate incrementally and
track ownership. Account for tail-block waste, metadata and fragmentation. More free allocation
space permits larger concurrency, which can in turn increase contention and latency.

**Prefix and multi-turn reuse:** reuse requires compatible model, tokenization/chat template,
exact preceding tokens, adapter state and relevant modality inputs. Similar meaning or an identical
system-prompt string is not sufficient if its resulting prefix differs. A semantic response cache
is a different optimization with different correctness/quality risks.

**Cache placement:** compare GPU retention, KV quantization, CPU offload and recomputation.
Offload trades capacity for transfer and synchronization costs; KV compression needs its own
quality analysis. Weight quantization does not automatically quantize KV tensors.

**Cache-aware routing:** affinity can improve hit rates while creating hot replicas. Balance
expected saved prefill against waiting, cache residency, failure and eviction. Keep tenant/auth
boundaries in the cache identity; do not expose prompt contents through logs or routing keys.

## Database bridge

Buffer frames, pin counts, slab allocation and eviction are strong analogies. KV blocks are derived,
potentially shared state, not durable data. They must not be reclaimed while active readers own them.
SEDA and Flat Combining explain queues and amortization; continuous batching additionally has to
schedule repeated token steps and handle requests whose resource needs grow during execution.

## Capability gate

- [ ] Draw request → scheduler → block allocation → prefill/decode → completion/cancellation.
- [ ] Trace two requests sharing a prefix, diverging, and freeing their last references.
- [ ] Explain why cache-hit rate alone can mislead a routing decision.
- [ ] Predict TTFT/ITL effects of a long prefill mixed with active short decodes.
- [ ] Compare offload and recomputation with transfer cost and memory headroom included.

**Optional labs:** use one small compatible model before the tweet's 70B deployment. Sweep chunk
budgets at fixed offered load and length distributions; report TTFT/ITL percentiles, throughput,
preemptions and memory use. Compare cold and warm prefix workloads separately.

For the routing-proxy exercise, start with a design trace or two small replicas. Compare
least-loaded routing with tenant-safe prefix affinity, account for queue delay, and test replica
failure, cancellation and cache eviction. Identical prompts do not guarantee resident KV blocks.
