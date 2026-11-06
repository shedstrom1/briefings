# AI Hardware & Local Models Brief — November 6, 2026 (07:45 ET Edition)

**Window: 2026-11-05 17:45 EST → 2026-11-06 07:46 ET** (previous fire → now; new items only)

## TL;DR

- **Thinking Machines released Inkling 2 Small** — a 594B-total / 17B-active MoE scoring 31 on the Artificial Analysis Intelligence Index (up from 13), with full **Apache 2.0 weights promised "next week"** after external red-teaming. It is the highest-scoring would-be open-weights model developed outside China.
- **Microsoft's Foundry Local now runs Qwen3.8-27B for agentic coding on-device**, claiming **35-135% higher decode throughput than llama.cpp** on an RTX 5090 and DGX Spark, with INT4 at ~18 GB (24 GB cards → 64K context; 32 GB+ → 262K).
- **llama.cpp shipped b12140 → b12167 overnight** — MoE LRFU/MQ hybrid cache, Vulkan ReBAR detection, OpenCL q6_k multi-column tiled GEMV for speculative verification, CUDA 13.5 release builds.
- **vLLM added day-0 Liquid AI LFM3-Text and LFM3-VL support**; **SGLang added ERNIE 5.5 Flash support** plus a Kimi-K3.1 DSA decode series.
- **Community quant ladder (day-0, in-window):** mradermacher pushed VIGOR-27B, Muse-Glimmer-30B-2026-Knowledge, SDRL-Qwen3-32B, OPO-8B and StoryGen-12B-It i1 GGUFs.

---

## Model releases

### **Thinking Machines ships Inkling 2 Small: 594B/17B-active MoE, Apache 2.0 weights promised next week**
**Event date: November 5, 2026** (post dated Nov 5; surfaced on Hacker News at 2026-11-06T00:00:24Z and r/LocalLLaMA at 03:50Z, both inside the window)

Inkling 2 Small is the lab's second-generation model: a sparse Mixture-of-Experts with **594B total and 17B active parameters**, 48 decoder-only layers, each token routing to **8 of 384 experts**, image input via a **292M-parameter Perception-Encoder-based vision tower**, and variable reasoning effort. It scores **31 on the Artificial Analysis Intelligence Index v5.0.1**, up 18 points from Inkling Small, and matches Gemini 3.8 Flash (high) while beating Xiaomi's MiMo-V2.6-Pro. Thinking Machines says it will publish full weights under **Apache 2.0 "next week"**, after external safety testing and red-teaming, per its "safe path to open weights" policy; until then it is Tinker-only (closed) at **$0.30 / $1.00 per 1M input/output tokens** ($0.03 cached). Best reported results: SWE-Bench Pro v2 (Hard) 86.3%, JobBench 70.5%, EnterpriseBench-CoreCraft 71.8%, PerceptionBench 64.7%; weakest: Terminal-Bench 4.0 at 13.1%. Notably, pre-training ran **natively in NVFP4 — 8.4T tokens in 68 hours on GB300 NVL72 racks**, followed by a single joint RL run of 1,075 steps / 17M+ rollouts over 9 days on 2,048 GPUs.

**Why it matters:** This is the most consequential Western open-weights commitment of the quarter. A 17B-active / 384-expert MoE is exactly the shape the local ecosystem is built for — high quality per decoded FLOP, and expert offloading is the whole game for consumer rigs. That the checkpoint is NVFP4/MXFP8-native (per MarkTechPost) means Blackwell cards get the cheapest path, and the community quant machine (Unsloth, mradermacher, bartowski) will have i1/UD tiers within hours of the drop.

**Local inference:** Nothing here lands on a 16 GB card. At 594B total, an FP4 checkpoint is ~300 GB of weights — realistic local targets are **2-4× 96-128 GB cards, a 128 GB DGX Spark / Ryzen AI Max+ 395 box with expert offload, or a multi-GPU Strata/Basalt-class rig**; the 17B active footprint is what keeps decode tolerable. The lab discloses no self-hosting hardware. Watch for the Apache-2.0 drop next week; the day-0 quants will be the story.

Sources:
- Introducing Inkling 2 Small — Thinking Machines Lab, Nov 5, 2026 — https://thinkingmachines.ai/news/inkling-2-small/
- Thinking Machines Inkling 2 Small: the highest scoring open weights model outside of China — Artificial Analysis, Nov 5, 2026 — https://artificialanalysis.ai/articles/thinking-machines-releases-inkling-2-small-highest-scoring-open-weights-model-outside-of-chinia-upon-weights-release-it
- Thinking Machines Releases Inkling 2 Small, a 594B MoE With 17B Active — MarkTechPost, Nov 5, 2026 — https://www.marktechpost.com/2026/11/05/thinking-machines-inkling-2-small-release/
- HN discussion (226 points) — https://news.ycombinator.com/item?id=50386338

---

## AI hardware and silicon

### **RTX 5070 Ti 16GB hits twice MSRP at US retail as the memory crunch works down the stack**
**Event date: continuing price state, re-reported this morning** (VideoCardz "Just Posted", Nov 6, 2026; verified tracker snapshot dated Nov 2, 2026)

The GeForce RTX 5070 Ti — the card NVIDIA and ASUS have spent this year insisting is *not* discontinued — is now clearing **$1,500 at US retailers**, roughly **twice its $749 MSRP**. The tracked PNY RTX 5070 Ti OC Triple-Fan (16 GB GDDR7) went **$1,189.99 (Oct 9) → $1,300 (Oct 24) → $1,574.95 (Nov 2)**, a +32.3% move in three weeks and **+71.2% from its $919.96 July low**; used eBay sits at $1,399. Same-morning context from the same tracker: RTX 5080 $2,174.68, RTX 5090 $7,999.95, RTX PRO 6000 Blackwell $17,690, RX 7900 XTX $1,820.99.

**Why it matters:** The 16 GB GDDR7 5070 Ti is the budget-king entry point for serious local inference, and it has now decoupled completely from MSRP — the same pattern that already consumed the 5090 and 5080. With GDDR7 and DDR5 both squeezed by HBM allocation, there is no sign of relief before 2027, so the value calculus for local builds keeps sliding toward 16 GB Ada/Blackwell refreshes, Arc Pro B60/B65, and unified-memory boxes.

**Local inference:** 16 GB GDDR7 at ~$1,575 makes a single-card Qwen3.8-27B-class build (~Q4_K_M) cost roughly what a 32 GB Arc Pro B65 did at launch — for a **16 GB card**, the entry tier for 27B Q4 with a small KV budget. This run's own reference class (RTX 5060 Ti 16GB) is tracked at $649.99, i.e. still the rational buy while the 5070 Ti drifts.

Sources:
- RTX 5070 Ti Price Tracker – November 2026 — videocardprices.com (price as of Nov 2, 2026) — https://videocardprices.com/card/nvidia-rtx-5070-ti/

---

## Inference software and benchmarks

### **Microsoft Foundry Local puts Qwen3.8-27B agentic coding on-device, claims 35-135% over llama.cpp**
**Event date: November 5, 2026** (blog dated "November 5th, 2026"; r/LocalLLaMA thread 2026-11-06T03:50Z)

Microsoft's Foundry Local blog announces agentic coding powered by **Qwen3.8-27B** (quoted at Terminal Bench 2.1 73.0, SWE-bench Pro 61.7, NL2Repo-Bench 42.3, DeepSWE 1.1 42.2), built on **ONNX Runtime** with **DFlash2 speculative decoding**, CUDA graph fusions and kernels tuned specifically for **NVIDIA's SM121 architecture (DGX Spark)**, INT4 weight handling, KV-cache quantization, embedding memory sharing, CPU offload, prefix caching and continuous batching. Headline result: **faster generation than llama.cpp at every tested prompt length on both an RTX 5090 (32 GB) and a DGX Spark (128 GB unified memory)**, with the gap widening beyond 131K tokens; the r/LocalLLaMA thread quantifies the claim as **35-135% decode (tok/s) improvements**. Requires Foundry Local CLI 0.13.0+ and hooks into GitHub Copilot via BYOK. Microsoft's own commenters pushed back, arguing llama.cpp was likely not tuned to a matching draft length and that the int4 kernel work may not transfer.

**Why it matters:** A vendor-optimized ONNX stack beating llama.cpp at long context is the kind of shift that changes which runtime you actually run for repo-scale agent work — and it is the first big official benchmark framed around SM121, i.e. the Spark. It also validates Qwen3.8-27B as *the* default local coding model. The caveat is real: llama.cpp's own DFlash2 work (already merged) closes much of that gap when configured correctly, so treat the numbers as tuned-runtime-vs-default-runtime until independent runs land.

**Local inference:** **~18 GB at INT4 → any 24 GB+ card (3090/4090/5080 class) at up to 64K context; 32 GB+ (5090, Spark) unlocks the full 262K.** Sub-24 GB cards including the 16 GB 5060 Ti are outside the stated support envelope, though INT4 with CPU offload is what the community already does there. Mac and AMD GPU support is promised.

Sources:
- Bring Agentic Coding On-Device with Foundry Local — Microsoft Foundry Blog, Baiju Meswani & Emma, Nov 5, 2026 — https://devblogs.microsoft.com/foundry/foundry-local-agentic-coding-qwen3-8-27b/
- Community thread with the 35-135% decode figure and llama.cpp methodology critique — r/LocalLLaMA, Nov 6, 2026 — https://www.reddit.com/r/LocalLLaMA/comments/1xp4lv5/bring_agentic_coding_ondevice_with_foundry_local/

### **llama.cpp b12140 → b12167: MoE hybrid cache, Vulkan ReBAR detection, OpenCL spec-verify GEMV**
**Event date: November 5-6, 2026** (commits 2026-11-05T23:16Z → 2026-11-06T10:02Z; b12167 published 09:45:44Z)

Twelve merges landed inside the window. The substantive ones: **`moe-cache`: LRFU/MQ-like hybrid cache, ClassLRU (#31233)** — a frequency/recency policy for which experts stay resident; **`vulkan`: detect ReBAR support, avoid BAR allocations if unavailable (#31658)**; **`opencl`: q6_k multi-column tiled GEMV for speculative verification (#31763)** plus repeat op for f16/i32/i16/bf16 (#31782); **`hexagon`: implement set_tensor_n and improve repacking/tiling robustness (#31744)**; **`webgpu`: batched tensor API (#31473)**; **CI: bump CUDA 13 release builds to 13.5 (#31788)**; **CUDA: fix Maxwell build (#31787)**; and a security hardening change restricting the HF token to huggingface.co hosts (#31730).

**Why it matters:** The MoE cache policy is the piece that determines how much of a 384-expert model you can actually keep on-GPU, which is precisely the bottleneck Inkling 2 Small will expose next week. Vulkan ReBAR detection fixes real allocation failures on older/laptop cards, and moving release CI to CUDA 13.5 means official Windows/Linux builds start exercising the Vera Rubin-era toolchain.

**Local inference:** Directly relevant to **16-24 GB cards running MoE quants** (Qwen3.8-Flash-Next, DeepSeek-V4.1-Flash, GLM-5.3-Flash) and to **Hexagon NPU** and **Vulkan/AMD** users; no VRAM class is left out.

Sources:
- llama.cpp releases b12167 — GitHub, published 2026-11-06T09:45:44Z — https://github.com/ggml-org/llama.cpp/releases/tag/b12167
- moe-cache LRFU/MQ hybrid cache — https://github.com/ggml-org/llama.cpp/pull/31233
- vulkan ReBAR detection — https://github.com/ggml-org/llama.cpp/pull/31658
- opencl q6_k multi-column tiled GEMV (spec verification) — https://github.com/ggml-org/llama.cpp/pull/31763

### **vLLM in-window: day-0 Liquid AI LFM3-Text / LFM3-VL, SM120 NVFP4 GEMM fix, MiniMax-M3.1 ROCm tuning wave**
**Event date: November 5-6, 2026** (60 merged commits, 2026-11-05T22:45Z → 2026-11-06T12:39Z)

The overnight wave added **LFM3-Text (#65826)** and **LFM3-VL (#65827)** as first-class models, an **SM120 NVFP4 CUTLASS GEMM with the bias folded into the epilogue (#61821)**, a **MiniMax-M3.1 ROCm series** (MXFP8 activation-quant fused post-attention #65937-adjacent, retuned dot_scaled GEMM tiles for M=17-256 #65530, per-XCD NVFP4 sparse decode splits #65481, opt-in parallel decode top-k #65267, DSpark drafter monokernel #65386), **AITER bumped to v0.1.28.post1 (#65937)**, **FlashInfer upgraded to 0.7.3 (#65930)**, a **Kimi-K3.1-Pro TPU serving recipe via vllm-torchtpu (#65725)**, data-parallel multimodal encoder for **Gemma-4** (#60314), and a KV-sharing fast-prefill hang fix.

**Why it matters:** Day-0 support for a vendor's edge model family is how a small model becomes runnable everywhere — LFM3's 1.7B/2B class is the phone/Jetson/mini-PC tier, and vLLM support means OpenAI-compatible serving for it. The MiniMax-M3.1 ROCm block is the clearest signal that AMD's MI300-series inference stack is being actively perf-tuned for open MoEs.

**Local inference:** LFM3-Text/VL targets **sub-8 GB (Jetson Orin Nano, laptops, NPUs)**; the SM120 NVFP4 work targets **RTX 50 / RTX PRO Blackwell desktop cards**.

Sources:
- Add LFM3-Text — vLLM, 2026-11-06T05:39:42Z — https://github.com/vllm-project/vllm/pull/65826
- Add LFM3-VL — vLLM, 2026-11-06T08:56:07Z — https://github.com/vllm-project/vllm/pull/65827
- SM120 NVFP4 CUTLASS GEMM epilogue bias — https://github.com/vllm-project/vllm/pull/61821

### **SGLang in-window: ERNIE 5.5 Flash support, Kimi-K3.1 DSA decode series, Gemma-4 per-layer attention**
**Event date: November 6, 2026** (merged 2026-11-06T08:22Z → 12:01Z)

SGLang merged **model support for ERNIE 5.5 Flash (#48547)**, a **Kimi-K3.1 block** — opt-in TensorRT-LLM DSA backend (#48714), shared-expert TP spanning interleaved prefill CP (#48713), and a DSA decode context-parallelism runtime (#48721) — plus **Gemma-4 per-layer attention backend (#44399)**, a **MiniMax sparse decode indexer running on Hopper at TP4/TP8 (#46597)**, re-landed `moe_finalize_fuse_shared` JIT, HiCache L3 prefetch limit fix (#48522), and housekeeping that **drops flash-attn-4 from default dependencies (#48829)**.

**Why it matters:** Day-0 serving support for Baidu's 280B-A14B hybrid (reported by this brief on Nov 5) is what turns a checkpoint into a deployable model, and the Kimi-K3.1 DSA series is the clearest evidence yet that sparse-attention decode — not dense attention — is where the MoE serving race has moved. Dropping flash-attn-4 from defaults lowers install friction considerably for consumer-GPU users.

**Local inference:** The DSA and sparse-indexer work targets **datacenter Hopper/Blackwell (TP4/TP8)**, while the flash-attn-4 dependency change and Gemma-4 backend help **single-card RTX 40/50 and MI300** installs.

Sources:
- Support ERNIE 5.5 Flash — SGLang, 2026-11-06T08:22:25Z — https://github.com/sgl-project/sglang/pull/48547
- kimi-k3.1: DSA decode context parallelism runtime — 2026-11-06T08:35:24Z — https://github.com/sgl-project/sglang/pull/48721
- gemma4: per-layer attention backend — 2026-11-06T12:01:28Z — https://github.com/sgl-project/sglang/pull/44399

---

## Community spins and quantizations

### **mradermacher day-0 i1 ladder: VIGOR-27B, Muse-Glimmer-30B-2026-Knowledge, SDRL-Qwen3-32B, OPO-8B, StoryGen-12B-It**
**Event date: November 6, 2026** (repo creations 2026-11-06T07:35Z → 12:23Z)

Five in-window imatrix (i1) GGUF drops, each with its own card link:
- **VIGOR-27B-i1-GGUF** (12:23Z) — imatrix quants of the 27B VIGOR visual-safety/guardrail family.
- **Muse-Glimmer-30B-2026-Knowledge-i1-GGUF** (10:12Z) — a **2026-news knowledge-update spin built with Muse Spark Open**, dataset OfflineBase/Muse-Glimmer-30B-2026-Knowledge-Data, "other" license.
- **SDRL-Qwen3-32B-i1-GGUF** (08:09Z) — Apache-2.0 **RL-trained optimization/operations-research solver** (gurobi meta-solver) on Qwen3-32B.
- **OPO-8B-i1-GGUF** (07:35Z) — qwen3_vl **visual-reasoning RL + tool-use** 8B.
- **StoryGen-12B-It-i1-GGUF** (11:15Z) — Gemma-licensed storytelling/creative merge, unsloth-built.
- Also: **agentmujo-qwen3.5-9b-bos-i1-GGUF** (09:10Z) — Bosnian function-calling/agentic LoRA merge (ROCm-tagged).

**Why it matters:** Two of these are genuinely useful rather than noise: the Muse-Glimmer 2026-Knowledge spin is the community's answer to stale cutoffs on a 30B-A3B class model, and SDRL-Qwen3-32B is a rare open RL model for combinatorial-optimization tool use. The 32B/30B tier is the sweet spot for 16-24 GB cards with expert/token offload.

**Local inference:** **8B → any 8-12 GB card; 12B → 12-16 GB; 27-32B → 16-24 GB at i1 Q2/Q3 tiers (or 32 GB comfortably), with CPU/DDR5 offload for the Q4+ rungs.** All are GGUF, so llama.cpp/Strata/KTransformers and llama-swap-class loaders apply.

Sources:
- VIGOR-27B-i1-GGUF — mradermacher, Hugging Face, created 2026-11-06T12:23:24Z — https://huggingface.co/mradermacher/VIGOR-27B-i1-GGUF
- Muse-Glimmer-30B-2026-Knowledge-i1-GGUF — created 2026-11-06T10:12:38Z — https://huggingface.co/mradermacher/Muse-Glimmer-30B-2026-Knowledge-i1-GGUF
- SDRL-Qwen3-32B-i1-GGUF — created 2026-11-06T08:09:21Z — https://huggingface.co/mradermacher/SDRL-Qwen3-32B-i1-GGUF
- OPO-8B-i1-GGUF — created 2026-11-06T07:35:33Z — https://huggingface.co/mradermacher/OPO-8B-i1-GGUF
- StoryGen-12B-It-i1-GGUF — created 2026-11-06T11:15:37Z — https://huggingface.co/mradermacher/StoryGen-12B-It-i1-GGUF

---

## Also noted

- **CCORE GLB-3D-Coder dataset released**: 3,918 rendered-and-tested Three.js/GLB examples across 40 domains, the training set behind the 4B Three.js scene generator (follow-on to the model reported Nov 5). — https://www.reddit.com/r/LocalLLaMA/comments/1xozbe6/ccore_glb3dcoder_dataset_is_here_3918_rendered/
- **Unsloth and bartowski had no in-window uploads** (last-modified sweeps top out at Nov 4 and Nov 3 respectively) — the day-0 quant pressure on Inkling 2 Small has not started yet.
- **Ollama / TensorRT-LLM produced no in-window releases**: latest Ollama is v0.40.15 (Nov 3), latest TensorRT-LLM is v1.4.0rc2 (Oct 28).

## Just missed the window

- **ServeTheHome's Supermicro Super AI Station review** (GB300 DGX Station in a convertible tower/5U chassis: Grace 72-core + **252 GB HBM3E @ 7.1 TB/s**, 496 GB LPDDR5X, 2×400 Gbps ConnectX-8, first 80 Plus **Titanium** 1.6 kW DGX Station) published **16:15 EST**, 90 minutes before the window opened — https://www.servethehome.com/supermicro-super-ai-station-review-the-ai-server-that-doubles-as-a-workstation/

## Coverage notes

- **Verified items in this edition: 8** (counting unit: distinct underlying events — 1 model release, 1 hardware-price state, 4 inference-software items, 1 community quant cluster, 1 also-noted dataset), plus 1 "just missed" footnote. Lead stories read in full: Inkling 2 Small (primary + Artificial Analysis + MarkTechPost), Foundry Local (primary blog + community thread).
- **Ledger dedupe: 14 candidates suppressed** as already reported — CUDA 13.5 GA, RTX PRO 5500 Blackwell $17,999, ASUS Ascent QN10 Snapdragon X2 mini PC, FurMark 2.11 RTX Spark support, SGLang v0.5.23, vLLM v0.33.0/v0.33.0rc3, llama.cpp b11840-b12094 and b12119-b12128 lines, Ollama v0.40.14/15, TensorRT-LLM v1.4.0rc2, LiquidAI LFM3 base weights, the Nov 5 mradermacher quant ladder, Kimi-K3.1-Pro quant wave, and AMD Q3 2026 earnings (re-covered this morning as "Lisa Su: we are a data center first company" — same Nov 3 event). One repeat "Just missed" footnote (BOSGAME M5 Max 192GB, Nov 4) was suppressed as already footnoted-eligible; ServeTheHome Super AI Station is footnoted for the first time.
- **Dead ends / limitations:** VideoCardz article deep-links for the RTX 5070 Ti 2×-MSRP and ASRock RX 7900 XT/XTX bricked-VBIOS stories could not be resolved (both slugs redirect to the homepage) — the 5070 Ti claim is therefore sourced to a dated price tracker instead, and the bricked-VBIOS story is dropped for lack of a fetchable page. TechPowerUp's news index is behind a bot check. The r/LocalLLaMA "Qwen 4 is stealth announced" cluster is rumor/speculation with no verified artifact (multiple sources confirm no Qwen 4 release date as of Nov 5), so it is excluded per the new-items-only rule. GitHub's API was the load-bearing timestamped river for the four runtime items.
