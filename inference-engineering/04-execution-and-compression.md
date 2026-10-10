# 04 — Execution, compression, and speculation

[Track map](README.md) · [GPU foundations](02-gpu-and-systems-foundations.md) · [Previous: serving and KV memory](03-serving-and-kv-memory.md) · [Next: distributed inference](05-distributed-inference.md)

## Scope and prerequisites

Turn a bottleneck prediction into an optimization choice, without treating smaller weights or
more tokens per pass as automatic wins. First understand prefill/decode, KV-cache size,
arithmetic intensity, matrix multiplication, and softmax. Rebuild missing math before the
acceptance-rule derivation; the systems argument can come first.

Use **45-min sessions**, taking one reading question at a time. Checkboxes record reading,
not demonstrated skill. Exercises are optional; no GPU purchase or training run is required.
Book references below identify exact sections and **printed pages**; PDF links account for
the two unnumbered opening pages.

## Curated reading path

- [ ] **Book spine — execution:** [§4.1 “CUDA” through §4.1.3 “Reducing Memory Accesses with Kernel Fusion,” pp. 96–101](../resources/Inference%20Engineering.pdf#page=98);
  [§4.2 “Deep Learning Frameworks and Libraries,” pp. 101–105](../resources/Inference%20Engineering.pdf#page=103).
  **[Foundational / Pattern Recognition]** Why: distinguish a kernel, graph, driver, runtime,
  compiler, and serving engine. Compare cuBLAS/CUTLASS/FlashInfer kernels with handwritten
  specialization; understand safetensors versus executable serialization and ONNX graphs.
  Ask: what intermediate tensor disappears after fusion? What hardware or shape assumption
  makes a kernel inappropriate? When is PyTorch compilation preferable to an engine?
- [ ] **Book spine — precision and quality:** [§5.1 “Quantization,” including §5.1.1 “Number Formats,” §5.1.2 “Quantization Approaches,” and §5.1.3 “Measuring Quality Impact,” pp. 120–128](../resources/Inference%20Engineering.pdf#page=122);
  revisit [§3.1.1 “Compute,” pp. 74–76](../resources/Inference%20Engineering.pdf#page=76).
  **[Mental Model / Practical]** Why: separate weights, activations, KV cache, and attention;
  distinguish FP8 exponent/range, INT4 storage, block/channel scales, accumulation precision,
  calibration, and quantization-aware training. Ask: which component saves bytes, which
  accesses faster Tensor Cores, and which still needs dequantization? What evidence would
  reject a configuration despite better throughput?
- [ ] **Book spine — speculation:** [§5.2 “Speculative Decoding,” including §5.2.1 “Draft-Target Speculative Decoding,” §5.2.2 “Medusa,” §5.2.3 “EAGLE,” and §5.2.4 “N-gram Speculation and Lookahead Decoding,” pp. 129–136](../resources/Inference%20Engineering.pdf#page=131).
  **[Mental Model / Cutting Edge]** Why: compare draft cost, accepted-prefix length, verification
  cost, batch saturation, and extra model memory. Ask: why can high acceptance still lose?
  Which methods require trained heads or target hidden states rather than just text?
- [ ] **Primary implementation anchor — [FlashAttention README](https://github.com/Dao-AILab/flash-attention/blob/main/README.md).**
  **[Pattern Recognition]** Read the overview and algorithm/version distinctions, not the whole
  codebase. Why: IO-aware tiling avoids materializing the full attention-score matrix while
  retaining dense attention semantics. Ask: why does this not change dense attention's
  quadratic full-sequence work? Revisit book [§2.5 “Optimizing Attention,” pp. 67–69](../resources/Inference%20Engineering.pdf#page=69).
- [ ] **Official reference — [Triton's fused softmax tutorial](https://github.com/triton-lang/triton/blob/main/python/tutorials/02-fused-softmax.py).**
  **[Practical]** Why: a manageable example of row reductions, SRAM residency, masking,
  numerical stability, and launch overhead. Ask: where are bytes saved, and when will
  register/shared-memory pressure or oversized rows defeat the simple kernel?
- [ ] **Official reference — [vLLM quantization documentation source](https://github.com/vllm-project/vllm/blob/main/docs/features/quantization/README.md).**
  **[Practical]** Consult FP8, AWQ, GPTQ, KV-cache, and hardware-support entries selectively.
  Why: **AWQ** uses activation information to protect important weight channels; **GPTQ**
  uses approximate second-order information to reduce weight reconstruction error. Both
  require more than choosing “INT4.” Ask: what calibration distribution, group size, kernel,
  model architecture, and GPU support does this artifact actually assume?
  For TensorRT-LLM calibration, inspect the supported quantization/export path and calibration
  corpus, then test the exported artifact on held-out tasks; calibration success is not a quality pass.
- [ ] **Official implementation reference — [TensorRT-LLM README](https://github.com/NVIDIA/TensorRT-LLM/blob/main/README.md).**
  **[Practical]** Read the feature overview and release-specific speculation support alongside
  the book, not as a substitute for a correctness derivation. Why: distinguish available
  draft/verification implementations from the sampling contract below.
  Ask: which supported method and acceptance mode actually preserve the target distribution?

These source files were fetched through GitHub MCP; verification details are in the
[source record](sources.md). Direct-paper verification is deferred: FlashAttention,
AWQ, GPTQ, and the original speculative-sampling papers are named here without assigning
inaccessible paper URLs. The book remains the main explanatory spine.

## Distinctions worth keeping

**Compression is not one switch.** FP8 and INT4 have different ranges and kernel paths;
weight-only INT4 may reduce memory without accelerating prefill matmuls. Scale metadata,
packing, conversion, and sensitive layers matter. Use representative calibration inputs and
separate held-out quality checks: perplexity, task success, long-context behavior, and failure
slices, with repeated trials and a predefined tolerance. The book's preference for floating
formats is advice, not proof that calibrated integer quantization always fails.

**Sparsity is separate from quantization.** Unstructured pruning, hardware-supported 2:4
structured sparsity, sparse attention, and MoE routing remove different work. A sparse FLOPS
specification is unavailable to a dense model without the necessary structure and kernels.
Pruning can also change quality; zero-valued weights do not guarantee execution skips them.

**Fusion and CUDA graphs solve different costs.** Fusion avoids intermediate memory traffic
and launches; graph capture/replay reduces host launch overhead for a repeated operation DAG.
Neither grants free compute. Dynamic shapes, allocations, capture compatibility, graph memory,
and compilation warmup complicate replay. FlashAttention remains exact in the mathematical
operation, not necessarily bitwise identical in floating-point output.

**Distribution preservation is a contract, not a method name.** For target distribution
`p` and draft distribution `q`, exact speculative sampling accepts a drafted token with
`min(1, p(token)/q(token))`; rejection samples from the normalized positive part of `p − q`.
With correct conditional probabilities and verification this preserves the target distribution,
not an identical seeded sequence. Temperature/top-k/top-p transformations must be handled
consistently. Greedy matching is a different, simpler contract. Medusa configurations may use
approximate acceptance or alter target weights; EAGLE versions differ in features and training.
N-gram and lookahead describe proposal construction, not permission to skip verification.

| Proposal method | What to compare with ordinary decode |
|---|---|
| Draft-target | Separate small model, ideally a compatible tokenizer; count its weights, KV state, prefill, and orchestration costs |
| Medusa | Additional trained prediction heads propose token trees; distinguish frozen-target and jointly trained variants and their acceptance rules |
| EAGLE | A trained feature/hidden-state-based speculator; distinguish versions rather than treating the book's three-layer feature example as universal |
| N-gram | Reuse matching prompt/history continuations without a learned draft model; repetition helps, novel output may not |
| Lookahead | Construct candidate continuations through extra parallel computation; broader applicability still costs compute and requires verification |

**An ordinary remote API is not an engine interface.** Text completions and limited logprobs
usually do not expose full conditional distributions, hidden states, KV state, or cheap
parallel verification. Client-side drafting therefore does not establish exact sampling or
a latency win. Use server-supported speculation; count network round trips and billing.

## Database analogy — and its limits

Fusion resembles vectorized operators or eliminating an intermediate materialization;
quantization resembles lossy column encoding; speculative execution resembles predicting
work before validation. But inference compression can change task behavior, and speculative
sampling validates a probability distribution, not transaction serializability. PagedAttention
manages KV allocation; FlashAttention manages attention IO. They are not interchangeable
“buffer pools.”

## Capability gate

- [ ] Explain which bytes/launches/operations each proposed optimization removes.
- [ ] Given a model, GPU, and workload, choose a baseline, a quality tolerance, and one change;
  predict a case where that change loses.
- [ ] Explain exact versus approximate speculation and why a plain remote completion API
  cannot supply the required verification contract.

## Optional exercises

- **Paper/CPU only:** quantize a small matrix with tensor and channel scales; compare an
  outlier-sensitive product. Hand-work a two-token `p/q` acceptance and rejection example.
- **Existing compatible GPU only:** adapt the Triton softmax tutorial or an RMSNorm reduction;
  check correctness on awkward shapes and extreme values before measuring. Compare unfused,
  fused, and graph-replayed execution where supported, including warmup and memory use.
  Include the native PyTorch operation as a baseline at matched dtype, shapes and tolerances.
- **Existing model access only:** compare baseline and one supported precision/speculation
  setting on fixed prompts at low and high concurrency. Record quality alongside TTFT, ITL,
  throughput, acceptance, and peak memory; do not infer a general win from one run.
  For the tweet's FP8-versus-FP16 comparison, use the same model/tokenizer and held-out corpus;
  report perplexity change alongside task quality, latency and VRAM. If FP8 is unsupported,
  record that limitation rather than substituting a different model silently.
