# AI Hardware & Local Models Brief — Nov 22, 2026 (07:45 ET)

Window: 2026-11-21 17:45 EST → 2026-11-22 07:45 EST (5h, fire 07:45 Sun ET). New-items-only; ledger dedupe applied.

## TL;DR
- New open-weight release: China Telecom's **Xing4.1-Preview** — 285B total / 17B active MoE with MLA+DSA, 1M ctx, Apache 2.0, no benchmarks published (repo created Nov 22, 02:47 EST).
- **Hy4-GGUF** (Xylolabs): IQ2_XXS tier (219 GB) and the Q6_K tier (~390 GB so far, 8 of 13 shards) landed/were added in-window — a quant update on the Nov 20 Hy4 event.
- llama.cpp **b12626–b12631** in-window builds: Adreno 850 (Qualcomm) coopmat GEMMs for six quantization types.
- vLLM in-window: Inkling-2-Mini small-M GEMM on RTX 5090 (sm120); KV-offload and multimodal-processing fixes.

## Model releases

**Xing4.1-Preview (China Telecom / XingChen-AGI)** — Nov 22, 2026 (repo created 02:47 EST)
China Telecom's Xing4.1-Preview is a new open-weight model in the Xing 4 family: a 45-layer sparse MoE (3 dense layers + 42 MoE layers) with Multi-head Latent Attention plus DeepSeek Sparse Attention, 256 routed experts (1 shared), 8 routed experts per token, 1M maximum context, 131,072 vocab, Apache 2.0. Total parameters ~285B with ~17B active per token; hidden 4096, qk head dim 192 (NoPE 128 + RoPE 64), v head dim 128, MLA q LoRA rank 1536 / kv LoRA rank 512, DSA index heads 64/head dim 128/top-k 2048, YaRN factor 32 (base 32,768), BF16, checkpoint format safetensors (261 shards, ~2.15 GB each ≈ 550+ GB on disk). The repo deliberately refuses to quote third-party benchmark numbers before the technical report — "no scores are quoted before they are confirmed and published through official channels." Lead story.
Why it matters: The only 285B-class open weights landing this week — a fourth Xing 4-family release with a genuinely different architectural configuration (cross-layer IndexShare) from Xing4.0. The lineage is China Telecom-trained on domestic Ascend silicon per the Nov 17 tech report (285B, 8,192× Ascend 910C, MindSpore). No GGUF tier exists yet.
Local inference: BF16 is far beyond a 16 GB card — this is GB300/H200-class or heavily quantized work; no consumer-GPU-class GGUF published day-0. Running 17B-active at ~285B total still needs ~550 GB BF16 / ~350–400 GB at Q4-class; not DGX Spark-128GB-class without quantization.
Source: Hugging Face — XingChen-AGI/Xing4.1-Preview, https://huggingface.co/XingChen-AGI/Xing4.1-Preview (created 2026-11-22T07:47:49Z; config + model card)

## Community spins and quantizations

**Hy4-GGUF — Q6_K tier + IQ2_XXS updates (Update on Nov 20 Hy4 event)** — Nov 22, 2026
Xylolabs' imatrix GGUF ladder for Tencent's ~774B/A49B multimodal MoE (tencent/Hy4, hy_v4_vl, Tencent Hy License, cat-weight work only) has grown in-window: the **IQ2_XXS** tier (219.36 GB, 13 parts; WikiText-2 KL 0.888 vs Q4_K_M 0.092, top-1 70.1% vs 90.8%; Chat KL 0.561, top-1 80.7%) was added 2026-11-22, and a **Q6_K** tier (8 of 13 shards in-window, ~390 GB so far, ~495 GB/shard at completion) landed 2026-11-22 11:29Z–13:04Z, plus accuracy-table/chart updates 08:48Z and a files-table fix 05:33Z. Prior tiers stand: Q8_0 818.49 GB (WikiText-2 KL 0.037, top-1 94.6%), Q4_K_M 464.10 GB (KL 0.092 / top-1 90.8%), IQ3_S 328.03 GB (KL 0.324 / top-1 82.8%), with `mmproj-Hy4-BF16.gguf` (2.68 GB) and `mmproj-Hy4-Q8_0.gguf` (1.61 GB); production for llama.cpp requires PR #32517 (not yet merged).
Why it matters: Hy4 is the largest MoE this job has tracked (~774B/A49B, 1M ctx multimodal); a Q6_K tier at ~390 GB in flight is the first consumer-end-adjacent quant stack for it — Q4_K_M already fits a multi-DGX-Spark-GB10 class box (the 1× 2×GB300-station FP8 recipe was ~775 GB).
Local inference: 219–818 GB total model — not a consumer-GPU single-card fit; Q4_K_M ~464 GB fits a 4× GB300-class system; IQ2_XXS at 219 GB is the largest single-GPU-class store, still a multi-GPU or DGX Spark-class-plus setup, not a 16 GB card.
Source: Hugging Face — Xylolabs/Hy4-GGUF, https://huggingface.co/Xylolabs/Hy4-GGUF (created 2026-11-20T10:45Z; Q6_K shards added 2026-11-22T11:29Z–13:04Z; README + accuracy table)

## Inference software and benchmarks

**llama.cpp b12626–b12631 (in-window builds)** — Nov 22, 2026
In-window: vulkan memset fix for unaligned ranges on non-UMA devices (#32725, b12626 02:20Z); server fits non-causal batches in one ubatch (#32713, b12627 03:51Z); vulkan supports non-contiguous src in SCALE (#32735, b12628 06:53Z); **OpenCL coopmat GEMM for Adreno 850 (Qualcomm) covering Q4_0, Q5_0, Q8_0, Q4_K, Q5_K, Q6_K** (#32686, merged 08:01Z, b12629 08:43Z); webgpu GDN supports_op check (#32782, b12631 09:03Z). Also in-window post-b12631 ggml fixes: SIGMOID backward pass (#32126), HIP fix for double halving of NVFP4 scales on CDNA3 (#32790), invalid-enum rejection for untrusted GGUF data (#32641), grammar lazy-trigger for bare JSON tool calls (#32497). The Adreno 850 coopmat adds the feature to the Qualcomm/edge inference path — significant for Android/NPU-side experimentation.
Why it matters: Adreno gains real GEMV/GEMM acceleration across the six most-used quant families — day 1 for Qualcomm-class NPU/SoC deployment experiments; the vulkan fixes unblock non-UMA systems (e.g. multi-GPU discrete + iGPU).
Local inference: llama.cpp is hardware-agnostic; Adreno coverage targets Qualcomm 8-series / Oryon-class devices running Q4_0–Q6_K; the vulkan/webgpu fixes apply to Windows/Linux discrete + AMD/Intel Vulkan paths.
Source: GitHub — ggml-org/llama.cpp releases b12626–b12631 + PR #32686, https://github.com/ggml-org/llama.cpp/releases/tag/b12629

**vLLM in-window wave** — Nov 22, 2026
Merged in-window: **Inkling-2-Mini small-M GEMM improvement on RTX 5090 / sm120** (#69168, merged 2026-11-22T00:09:52Z, author gau-nernst) — a perf fix to the small-M GEMM path for the 26B/3.5B Inkling-2-Mini serving stack; Rust-frontend `allowed_tools` tool-choice support (#68408); KV-offload batch: idle FS worker wake-up avoidance (#68451), ARC prefix retention during running-request eviction (#69110), offload `access_mode` read|write|readwrite (#67961), CPU-tier chunk fit for oversized requests (#66719); multimodal fixes: OpenCUA processor kwargs (#68317), torch.Tensor videos (#69052), NumPy/PIL in Muse Spark Open (#69099), offline LLM API error classification (#68667).
Why it matters: Inkling-2-Mini is the current frontier local-serving target; the 5090 path is the most relevant consumer-GPU target this window. Offload and multimodal fixes harden the stack behind it.
Local inference: RTX 5090 (sm120) — small-M GEMM perf; KV-offload fixes apply to CPU-tier vLLM serving on RTX 5060 Ti/PRO 6000 class boxes.
Source: GitHub — vllm-project/vllm commits/PRs #69168, #68408, #68451, #69110, #67961, #66719, https://github.com/vllm-project/vllm/pull/69168

## Also noted
- llama.cpp releases for b12630 do not exist (b12629 → b12631 jump); b12629 is the newest published build at fire time.
- r/LocalLLaMA lane was unavailable this run (anonymous 404 + 429 throttle with no app-credentials configured) — logged as a coverage limitation, not a negative finding.
- HN river had no material in-window AI-hardware/local-model item at meaningful points; top in-window stories were unrelated (e.g. a 181-point data-incident piece on inference providers; a 1-point "Inference Stack" Show HN).
- SGLang: no release in-window; in-window work is internal (sampling pipeline refactoring, speculative-verify rings, Mooncake transport extraction, AMD mxfp8-moe cleanup) — nothing user-visible beyond the Inkling-2-Mini path already covered by vLLM.
- ComfyUI v0.46.0 (Nov 18), Ollama v0.40.17 (Nov 10), TensorRT-LLM v1.3.0 (Nov 20), vLLM v0.34.0 (Nov 16), SGLang v0.5.23 (Nov 2): all pre-window; no releases in this window.

## Coverage notes
- Ledger dedupe: Xing4.1-Preview, Hy4-GGUF (Q6_K update), llama.cpp in-window builds and vLLM in-window wave were checked against reported.md — no prior line matches; Hy4 is listed as an update on the Nov 20 ledger entry, not a new item.
- Reddit/r/LocalLLaMA: lane unavailable (anonymous 404, no credentials) — not searched further.
- HN alive but low-yield; search backend returned no in-window outlets beyond these.
- HN Algolia via terminal worked; HF API worked; GitHub API worked; web_extract/search were weak this run.
- Verified items: 4 (1 new model release + 1 update + 2 inference-software waves). Deliberately thin — five hours, thin news cadence, no padded items.
