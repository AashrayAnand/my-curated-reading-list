# 02 — GPU and Systems Foundations

[Home](README.md) · Previous: [Physics](01-models-and-inference-physics.md) ·
Next: [Serving and KV memory](03-serving-and-kv-memory.md)

**Question:** what does the machine execute, and what must move before that execution can happen?
Prerequisite: module 01's memory and arithmetic-intensity models.

## Reading path

- [ ] **Book: 3.1–3.3 (pp. 74–86).**
  **Tags:** Foundational, Mental Model.
  **Why:** distinguish compute, memory, GPU generations, instances and multi-GPU topology.
  Compare Hopper, Ada, Blackwell and the book's Rubin roadmap by supported precision, memory
  capacity/bandwidth and connectivity—not just nominal FLOPS. Grace/Vera are host CPU topics,
  not GPU names. Treat launch dates and announced specifications as dated claims.
- [ ] **Book: 3.4–3.5 (pp. 87–91).**
  **Tags:** Practical, Pattern Recognition.
  **Why:** other datacenter accelerators and desktop/mobile inference expose portability, memory,
  thermals, power and software-support trade-offs. A CPU/mobile demonstration is useful but does
  not predict datacenter GPU throughput.
- [ ] **Book: 4.1–4.2 and 4.5.3 (pp. 96–105, 114–115).**
  **Tags:** Foundational, Practical.
  **Why:** locate CUDA, kernels, framework dispatch, file formats and profiling in the stack.
  Distinguish PyTorch/Transformers/Diffusers, ONNX Runtime/TensorRT, and a serving engine.
  Check model-file trust and conversion compatibility before executing downloaded artifacts.
  Compare tensor-only formats such as Safetensors with pickle-based loading, and portable model
  representations with compiled engine artifacts. Packaging, driver/runtime compatibility and
  model architecture support are separate from the mathematical weights.
- [ ] **NVIDIA — [tiled CUDA matrix multiplication](https://github.com/NVIDIA/cuda-samples/blob/master/cpp/0_Introduction/matrixMul/matrixMul.cu).**
  **Tags:** Foundational, Practical.
  **Read:** kernel indexing, shared-memory tiles, synchronization, event timing and validation.
  **Why:** a concrete bridge from matrix math to device execution.
  **Limit:** the sample explicitly prioritizes explanation over a performant general GEMM;
  its supported dimensions and simplified checks are not a complete correctness suite.
- [ ] **Reference: [CUDA Samples overview](https://github.com/NVIDIA/cuda-samples/blob/master/README.md).**
  **Tags:** Practical.
  **Why:** locate device-query, stream, graph and library examples as questions arise.
  This is a source index, not an assignment to build every sample.

## What to understand at the machine level

1. **Execution:** grid → thread blocks → warps on streaming multiprocessors; SIMT, divergence,
   warp scheduling, tensor-core operations, synchronization scope and latency hiding.
   A warp is not a CPU thread pool. Occupancy is a resource constraint/latency-hiding signal,
   not an objective to maximize regardless of performance.
2. **Storage:** registers, shared memory, caches and global device memory/HBM; coalescing,
   bank conflicts, tile reuse and register pressure/spills. Shared memory is explicitly managed;
   it is not simply another coherent CPU cache.
   Architecture-specific cache labels in the book are orientation, not a portable hardware
   specification; distinguish software-controlled shared storage from hardware caching.
3. **Movement:** host RAM → PCIe → device; GPU-to-GPU NVLink where available; distinct
   NVLink chip-to-chip host connections. Compare bandwidth direction, actual topology,
   transfer sizes and latency. Aggregate advertised bandwidth is not the rate of every pair.
4. **Submission:** host launch overhead, streams, events, asynchronous copies, warmup and
   synchronization. An unsynchronized CPU timer can measure enqueue time rather than execution.
5. **Languages:** enough C++ to read CUDA host/device interfaces and lifetimes. Rust is optional
   for host-side tools/proxies; existing Rust familiarity does not replace kernel knowledge.
   Learning two entire languages is not an entry gate.

## Profiling is two investigations

**Nsight Systems** reveals the CPU/GPU timeline: launches, copies, overlap, gaps and synchronization.
Use it to locate phases and the critical path. **Nsight Compute** investigates individual kernels:
memory throughput, achieved compute, occupancy and stalls. A timeline alone does not identify
the exact memory bottleneck. Profiling changes timings; collect unprofiled baselines too.

Ask whether an idle GPU is waiting for CPU dispatch, transfers, another GPU, or requests.
High memory allocation is not evidence of saturated memory bandwidth.

## Database bridge and capability gate

Cache-aware joins and SIMD teach data reuse and locality. GPU tiling applies the same cost lens
with different execution/synchronization rules. Async I/O teaches overlap, but a stream does
not grant arbitrary dependencies or make a blocking host operation disappear.

- [ ] Trace one tile's lifetime from global memory to shared memory to registers to output.
- [ ] Explain how divergence and register pressure can defeat a nominally parallel kernel.
- [ ] Distinguish allocation capacity, transfer bandwidth and latency.
- [ ] Match a performance question to timeline or kernel-level evidence.

**Optional tweet lab:** write a correct CUDA matmul from scratch, then compare with cuBLAS
at matched shapes, dtype, layout and numerical tolerance. Include non-tile-aligned shapes,
boundary checks, warmups, synchronized timing and device-only versus end-to-end measurements.
Do not expect to beat cuBLAS or equate its math modes with naive FP32 automatically.
Start by explaining the sample without a GPU; only run experiments with an explicit spending cap.
