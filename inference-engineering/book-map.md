# Coverage Map: Book and Twelve-Stage Roadmap

[Track home](README.md) · [Book PDF](../resources/Inference%20Engineering.pdf)

Book: Philip Kiely, *Inference Engineering*, Baseten Books, 2026. Pages below are **printed
book pages**, not PDF viewer indices. Navigate by section title if the viewer's numbering differs.
This path contains original commentary and reading directions, not a reproduction of the book.
The existing user-supplied PDF is left unchanged; no additional copies are published.

## Book coverage

| Book selection | Printed pages | Destination and purpose |
| --- | --- | --- |
| Preface; ch. 0 | 9–22 | 01: inference scope and trade-offs; distinguish argument from evidence |
| 1.1–1.2 | 26–30 | 01: scale, specialization, AI-native apps, online/offline and consumer/B2B workloads |
| 1.3–1.4 | 31–37 | 01: selection, evaluation, fine-tuning, distillation, percentiles and end-to-end metrics |
| 2.1–2.2 | 42–54 | 01: linear layers, activations, LLM architecture, transformer blocks, attention, MoE |
| 2.3 | 55–60 | 07: image/video architecture, denoising, few-step models |
| 2.4 | 61–67 | 01: ops:byte ratio, arithmetic intensity, distinct LLM/media bottlenecks |
| 2.5 | 67–69 | 03 and 04: paging versus IO-aware attention; architecture versus implementation |
| 3.1–3.2 | 74–82 | 02: compute/memory; Hopper, Ada, Blackwell, Rubin, Grace/Vera; date-sensitive specifications |
| 3.3–3.5 | 83–91 | 02 and 05: instances, topology, MIG, other accelerators, desktop/mobile inference |
| 4.1–4.2 | 96–105 | 02 and 04: CUDA, kernel selection/fusion, PyTorch, file formats, ONNX/TensorRT, Transformers/Diffusers |
| 4.3 | 105–110 | 03: vLLM, SGLang, TensorRT-LLM |
| 4.4 | 111 | 05: NVIDIA Dynamo and distributed serving |
| 4.5 | 112–115 | 02 and 06: profiling, benchmarking and load testing |
| 5.1 | 120–128 | 04: number formats, quantization approaches, quality impact |
| 5.2 | 129–135 | 04: draft/target, Medusa, EAGLE, n-grams and lookahead |
| 5.3 | 136–141 | 03: prefix reuse, cache placement, cache-aware routing, long context |
| 5.4 | 142–147 | 05: model/tensor/expert parallelism and multi-node inference; add pipeline parallelism |
| 5.5 | 148–150 | 05: disaggregation, when it helps, dynamic Dynamo placement |
| 6.1 | 156–159 | 07: VLMs, video processing, omni-modal models |
| 6.2 | 159–161 | 07: embedding architecture and inference |
| 6.3 | 162–166 | 07: ASR, single-chunk versus long-file optimization, diarization |
| 6.4 | 166–168 | 07: TTS, streaming speech, speech-to-speech |
| 6.5–6.6 | 169–176 | 07: image kernels and optimization; video attention, quantization, context parallelism |
| 7.1 | 179–183 | 06: containers, dependency management, NIMs |
| 7.2 | 183–192 | 06: concurrency/batching, cold starts, routing/queues, scale-to-zero, component scaling |
| 7.3 | 193–198 | 06: capacity, procurement, geo routing, reliability, security/compliance |
| 7.4 | 199–203 | 06: testing, deployment, zero downtime, cost and observability |
| 7.5–7.6 | 204–208 | 06: client overhead, asynchronous inference, streaming/protocols, Baseten case study |
| Appendix A | 209–230 | All modules: glossary lookup after trying to define a term yourself |
| Appendix B | 231–256 | All modules: discovery index, not an additional compulsory syllabus |
| Acknowledgements | 257 onward | Optional provenance/background; no additional technical assignment |

Appendix B spans architecture, developer tools, frontier open models, GPU infrastructure,
optimization research, and intelligence evaluation. Select from it only to answer a specific
question; verify the chosen source before adding a future assignment. Model catalogues and
hardware roadmaps date quickly. A recommendation in the book is not itself live-link verification.

## Tweet coverage

| Stage | Main destination | Optional practice, scaled to available resources |
| --- | --- | --- |
| 1. Systems/GPU foundations | 02 | Custom CUDA GEMM versus cuBLAS; source reasoning without a GPU |
| 2. Transformer inference physics | 01 and 02 | Small Hugging Face model timeline in Nsight Systems; kernel diagnosis in Nsight Compute |
| 3. Engines/batching | 03 | Chunk-size sweep under mixed prefill/decode load; small model before 70B |
| 4. KV memory optimization | 03 | Tenant-safe prefix-aware proxy; compare affinity with queue delay |
| 5. Quantization/compression | 04 | FP8/FP16 quality, latency and VRAM comparison; AWQ/GPTQ/calibration and sparsity |
| 6. Kernel engineering | 04 | Fused Triton RMSNorm/softmax, correctness before PyTorch comparison |
| 7. Distributed parallelism | 05 | Sharding and communication model; optional 405B/eight-node design exercise |
| 8. Speculative decoding | 04 | Draft/target acceptance and verification; why a remote API may prevent the proposed lab |
| 9. Interconnects/disaggregation | 05 | Within-node versus cross-node communication and KV-transfer accounting |
| 10. Orchestration/scheduling | 06 | Queue autoscaling, spot interruption, draining; GPU operator/Ray/Slurm/MIG/KEDA |
| 11. Gateways/observability | 06 | Quality-tested local/frontier routing, TTFT/ITL SLOs, DCGM/OpenTelemetry |
| 12. Public benchmarks | 06 | Reproducible vLLM/SGLang/TensorRT-LLM teardown with Pareto curves and cost |

## Avoid repeated assignments

The Illustrated Transformer is an explicit **optional refresher**, already completed according
to the decision log. Existing buffer-pool, scheduling, locality and tail-latency paths are bridges,
not reassigned full readings. The new primary spine is the Baseten book; this track does not
mark previously scheduled database or AI fundamentals as completed.
