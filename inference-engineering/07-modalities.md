# 07 — Modalities: choose the execution model first

[Track map](README.md) · [Previous: production](06-production-and-benchmarks.md) · [Distributed attention](05-distributed-inference.md)

## Scope and prerequisites

Extend inference reasoning to vision, embeddings, speech, images, and video without assuming
every workload is an autoregressive, memory-bound decoder. Prerequisites: matrix/attention
intuition, prefill/decode, pipeline stages, batching, and modules 04–06's quality/measurement
discipline. Learn latent space as a compressed learned representation before diffusion details;
advanced probability math is not required to draw the pipeline.

Use **45-min sessions**, one modality at a time. Reading coverage is not demonstrated completion;
all exercises are optional and no accelerator cluster is required. Book ranges are printed
pages, with PDF links adjusted for its unnumbered front matter.

## Curated reading path

- [ ] **Book spine — generation mechanics:** [§2.3 “Image Generation Inference Mechanics,” §2.3.1 “Image Generation Model Architecture,” §2.3.2 “Few-Step Image Generation Models,” and §2.3.3 “Video Generation,” pp. 55–61](../resources/Inference%20Engineering.pdf#page=57);
  [§2.4.3 “Image Generation Inference Bottlenecks,” p. 67](../resources/Inference%20Engineering.pdf#page=69).
  **[Foundational / Mental Model]** Why: distinguish text encoder, denoiser, latent state, and
  VAE; understand conditioning/guidance, resolution, steps, distillation, and temporal attention.
  Ask: which state changes on each step? Is this specific model a U-Net, diffusion transformer,
  autoregressive generator, or hybrid? The book's broad architecture sketches are not universal.
- [ ] **Book spine — understanding and retrieval:** [§6.1 “Vision Language Models,” §6.1.1 “Video Processing for Vision Language Models,” §6.1.2 “Omni-Modal Models,” §6.2 “Embedding Models,” §6.2.1 “Embedding Model Architecture,” and §6.2.2 “Embedding Model Inference,” pp. 156–162](../resources/Inference%20Engineering.pdf#page=158).
  **[Pattern Recognition]** Why: compare visual encoding plus language decode with a pooled
  vector response; separate online retrieval from indexing backfills. Ask: what changes when
  resolution or frame sampling increases? What makes embedding versions incompatible even
  when vector dimensions match?
- [ ] **Book spine — voice pipelines:** [§6.3 “ASR Models,” §6.3.1 “Single-Chunk Latency Optimization,” §6.3.2 “Long File Latency Optimization,” §6.3.3 “Diarization,” §6.4 “TTS Models,” §6.4.1 “Streaming Real-Time Text to Speech,” and §6.4.2 “Speech-to-Speech Models,” pp. 162–169](../resources/Inference%20Engineering.pdf#page=164).
  **[Practical / Mental Model]** Why: include VAD, chunking, encoder/decoder work, timestamp
  stitching, speaker clustering, waveform decoding, and conversational turn-taking. Ask:
  when does parallel chunk transcription sacrifice cross-chunk context? What determines
  first audible output rather than the first internal audio token?
- [ ] **Book spine — media acceleration:** [§6.5 “Image Generation Models,” §6.5.1 “Image Generation Kernel Optimization,” §6.5.2 “One Weird Trick for Faster Image Generation,” §6.6 “Video Generation Models,” §6.6.1 “Attention Optimization and Quantization,” and §6.6.2 “Context Parallelism,” pp. 169–176](../resources/Inference%20Engineering.pdf#page=171).
  **[Cutting Edge / Practical]** Why: connect fusion/attention kernels to compute-intensive
  denoising; distinguish fewer steps, guidance changes, approximate timestep/layer caching,
  and precision changes from exact implementation improvements. Ask: which shortcut could
  harm prompt adherence or temporal consistency despite a good-looking single frame?
- [ ] **Official reference — [Transformers multimodal chat templates](https://github.com/huggingface/transformers/blob/main/docs/source/en/chat_templating_multimodal.md).**
  **[Practical]** Why: processors coordinate text, image/video inputs, templates, and tensors.
  Ask: where do decoding, resizing, frame sampling, and token expansion happen, and what
  must be measured before the language model's prefill starts?
- [ ] **Primary model implementation — [Whisper README](https://github.com/openai/whisper/blob/main/README.md).**
  **[Foundational]** Read the architecture/use overview and audio-processing/transcription
  path selectively. Why: ground ASR in an actual encoder-decoder model, not a generic LLM
  endpoint. Ask: what are the chunk, language, timestamp, and context assumptions? Streaming
  an API around a chunk model does not make the underlying model intrinsically streaming.
- [ ] **Official reference — [Diffusers inference optimization](https://github.com/huggingface/diffusers/blob/main/docs/source/en/optimization/fp16.md).**
  **[Practical]** Why: inspect precision, attention, compilation, and memory tradeoffs before
  using a reference pipeline as a speed baseline. Ask: which settings apply to your exact
  model/component/GPU, and which change output or startup cost?
- [ ] **Research implementation anchor — [xDiT README](https://github.com/xdit-project/xDiT/blob/main/README.md).**
  **[Pattern Recognition / Cutting Edge]** Read the parallel-methods and cache-acceleration
  overview, not the cluster installation first. Why: distinguish context/sequence parallelism,
  ring/Ulysses-style attention, pipeline methods, CFG parallelism, parallel VAE, and replicas.
  Ask: which methods communicate exact intermediate state, and which reuse approximate state
  across denoising steps? Paper/design-first reading remains useful without running the system.

These source files were fetched through GitHub MCP; see the [source record](sources.md).
Direct Whisper and diffusion-parallelism paper verification is deferred. The verified
implementation explanations and book supply the current reading path, not unverified
outbound paper links.

## Modality-specific performance and quality contracts

| Workload | Execution and likely pressure | Measure useful output and quality |
|---|---|---|
| VLM / video understanding | Decode follows visual preprocessing and encoding; resolution/frame count can inflate prefill and KV state. Model-specific cross-attention or token reduction changes the budget. | End-to-end TTFT/ITL plus preprocessing/encoder time; grounding, OCR/detail accuracy, temporal-event recall. Downsampling can hide decisive evidence. |
| Embeddings | Encoder or pooled LLM features, generally one vector per input, not a streamed decode loop; tokenization, padding, batching, and queues matter. | Query latency versus indexing documents/tokens per second; retrieval recall/ranking, task slices, and index migration consistency. Cosine agreement alone does not prove unchanged retrieval quality. |
| ASR | Audio preprocessing + encoder + text decoder; VAD/chunk boundaries and orchestration can dominate streaming experience. Batch files and live chunks have different objectives. | Partial/final transcript latency, word error rate, timestamps, silence hallucinations, and real-time processing ratio. Define `RTF = processing duration / audio duration` (lower is faster); the book uses reciprocal speedup terminology. |
| Diarization | Segmentation, speaker embeddings, and clustering; separate from transcript generation and not assumed to share its runtime. | Diarization error rate with stated overlap/collar conventions, speaker changes, timestamp alignment, and full-pipeline latency. |
| TTS | Some models generate audio tokens autoregressively then decode waveform chunks; others use diffusion or other architectures. Codec/decoder batching and buffering can be bottlenecks. | First playable/audible chunk, sustained audio generation faster than playback, concurrent stable streams, underruns, intelligibility, pronunciation, and voice fidelity—not merely internal TPS. |
| Speech-to-speech / omni | Compare ASR→LLM→TTS cascade with joint audio input/output models; VAD/endpointing, codec, turn-taking, and tools remain part of the system. | End-of-turn to first audible response, interruption/barge-in, semantic correctness, prosody, robustness, and cost per useful conversation. Book claims about available open models are dated snapshots. |
| Image diffusion | Text encoder→iterative denoiser→VAE; latent size, steps, guidance passes, attention, and GEMMs shape compute/memory demand. | Image latency, images/s, peak memory, prompt adherence, detail, artifacts, and blinded human comparisons over fixed prompt/seed sets. |
| Video diffusion | Whole spatiotemporal latent denoising can have large attention/activation costs; context parallelism and parallel VAE may help. | Clip latency, generated video duration per wall time, resolution/frame count, temporal coherence, motion/physics, and prompt adherence. A good frame is not a good video. |

**Transfer techniques selectively.** Prefix caching and prefill/decode disaggregation can help
the language-decoder portion of a VLM; they are not defaults for pooled embeddings or a
diffusion denoiser. Embedding-vector truncation is supported only by models trained for it,
such as Matryoshka representations; dimension changes primarily affect storage/search rather
than guaranteeing faster model execution.

For voice, compare sequential chunk context with parallel file throughput, and keep diarization
and waveform decoding visible as separate stages. Streaming needs flow control, cancellation,
and bounded buffers; faster internal generation alone does not guarantee a natural conversation.
MIG is an option only after checking actual memory, compute, and deployment compatibility.

For diffusion, fewer-step/distilled or consistency models differ from arbitrarily lowering a
full-step model's step count. Guidance schedules and timestep/layer caches are model-specific
quality tradeoffs, not lossless guarantees. Context parallelism partitions attention context
and can replicate weights; it is not the same as tensor-sharding weights. Long clips can
be limited by activation memory and communication even when compute is the primary bottleneck.
Do not assume every video model needs eight GPUs or that batching can never help.

## Database analogy — and its limits

Embedding backfill resembles index construction; online queries resemble latency-sensitive
lookups. Multimodal pipelines resemble heterogeneous operators with distinct batch and queue
policies. But re-embedding changes semantic space, not just physical index layout. Diffusion
steps iteratively transform a learned latent object, not rows flowing once through an operator;
lossy caching may change the final content.

## Capability gate

- [ ] Draw the complete stages for a VLM, embedding service, voice cascade, and diffusion
  generator; mark which are autoregressive and which can batch or parallelize independently.
- [ ] Choose useful-output latency and quality metrics for each rather than copying LLM TPS.
- [ ] Explain an optimization that transfers safely, one that requires quality evaluation,
  and one that does not apply; distinguish video understanding from video generation.

## Optional exercises

- **No hardware:** draw pipeline/queue timelines for live speech and long-file ASR; estimate
  visual-token or latent-shape growth using stated model assumptions. Critique an image/video
  benchmark that changes resolution, step count, and quality simultaneously.
- **CPU or existing small-model access:** compare sequential versus chunked ASR on authorized
  nonsensitive audio, or evaluate embedding retrieval before/after a supported dimension change.
  Record task quality, not just vector similarity.
- **Existing compatible GPU only:** change one supported diffusion precision, compilation,
  guidance, or step setting on a fixed prompt/seed suite; keep cold startup separate and inspect
  quality. Multi-GPU context-parallel execution is an optional extension, not a prerequisite.
