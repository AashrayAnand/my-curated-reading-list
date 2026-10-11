# 01 — Models and Inference Physics

[Home](README.md) · Next: [GPU foundations](02-gpu-and-systems-foundations.md)

**Question:** what work and persistent state does a request create before it reaches the scheduler?
Start with tensor shapes and the forward pass, not training every kind of neural network.

## Prerequisite check

Can you explain matrix multiplication, a dot product, softmax as a normalized distribution,
an embedding lookup, and training versus inference? If not, use the existing
[AI fundamentals profile](../context/reading-profile.md) for targeted repair. Gradients and
backpropagation help explain fine-tuning but are not prerequisites for initial KV memory accounting.
The Illustrated Transformer is a permitted revisit, not a new recommendation.

## Reading path

- [ ] **Book: preface, ch. 0, 1.1–1.4 (pp. 9–37).**
  **Tags:** Foundational, Mental Model, Practical.
  **Why:** establish the application's objective before optimizing hardware. Compare online/offline,
  consumer/B2B, and interactive versus tool-call workloads. Model selection, domain fine-tuning,
  and distillation change quality/capacity; they are not interchangeable serving switches.
  **Ask:** what quality threshold must hold, and what latency does the user actually observe?
- [ ] **Book: 2.1–2.2 (pp. 42–54).**
  **Tags:** Foundational, Mental Model.
  **Why:** trace linear layers, activations, transformer blocks, attention, and MoE.
  **Ask:** which tensors are weights, temporary activations, or reusable per-request state?
- [ ] **Book: 2.4–2.5 (pp. 61–69).**
  **Tags:** Foundational, Pattern Recognition.
  **Why:** arithmetic intensity is a resource model, not a label permanently attached to a phase.
  **Ask:** which changes increase reuse, which reduce bytes, and which change the algorithm?
- [ ] **Hugging Face — [LLM inference optimization](https://github.com/huggingface/transformers/blob/main/docs/source/en/llm_tutorial_optimization.md).**
  **Tags:** Practical, Mental Model.
  **Read selectively:** weight memory, autoregressive tensor flow, multi-query/grouped-query attention.
  **Why:** connects the book's diagrams to tensor-level inference.
  **Caution:** historical model/hardware examples are not current capacity limits; loading weights
  is not the entire serving footprint. Do not run the large-model examples as prerequisites.

## Derive before measuring

For a conventional dense decoder's full-context attention:

- Weight storage is approximately `parameter_count × bytes_per_weight`, plus scales/metadata.
- Unshared KV storage is approximately
  `2 × layers × KV_heads × head_dimension × bytes_per_KV_element × sum(retained_tokens_per_request)`.
  The two accounts for keys and values. Include prompt and generated tokens; padding, block
  rounding, sharing and implementation metadata alter allocation. GQA uses KV heads, not query heads.
  Sliding windows, latent/compressed attention and hybrid state models require different accounting.
- Matmul `M×K` by `K×N` is roughly `2MKN` floating-point operations. Arithmetic intensity is
  operations divided by bytes transferred at the memory level being analyzed.
- A roofline estimate compares compute time with memory-transfer time. It is an optimistic bound,
  not measured latency; synchronization, launches, communication and queueing can add time.

Prefill processes many input tokens; decode advances each active sequence incrementally.
Caching K/V avoids recomputing historical projections. It does **not** eliminate attending to
historical state: full attention is quadratic across an entire prefill and linear in retained
context for each ordinary decode step. Do not read the book's shorthand as globally linear attention.

Define TTFT, individual inter-token gaps (ITL), end-to-end latency, aggregate output tokens/sec,
and successful requests/sec separately. Per-user speed is not fleet throughput. Keep input and
output token counts distinct, and report percentiles at a stated load.

## Database bridge

A physical query plan also exposes operators, state, cardinality, and resource demand. Input/context
length and output length play a role analogous to workload shape—but generation is sequential,
variable-length and model-dependent. SQL result equivalence is not a sufficient quality evaluation.
MoE active parameters explain compute selection, not permission to omit all inactive weights from
capacity planning.

## Capability gate

- [ ] Draw tokenization → prefill → repeated decode → output, with host/device boundaries.
- [ ] Calculate a model's weight and KV footprint with explicit assumptions.
- [ ] Predict how longer context, more active requests, and GQA change the memory budget.
- [ ] Explain why a larger batch may move decode toward compute limits.
- [ ] Separate model quality evaluation from infrastructure latency measurement.

**Optional lab:** profile a small supported Hugging Face model under fixed input/output lengths.
Compare prefill and decode rather than asserting that an observed long operation is memory-bound.
Use module 02's distinction between timeline and kernel measurements. Without hardware, annotate
the tensor flow and calculate competing bottleneck hypotheses instead.
