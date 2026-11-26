# AI Hardware & Local Models Brief — midday edition

**Window: 2026-11-26 08:45 → 13:24 EST** (previous fire → now). New items only; every entry deduped against the reported ledger.

- **vLLM's daytime merge wave adds GPU-Direct-Storage KV offload and a sparse-disk RL weight-transfer backend** — both change what fits on a single box.
- **llama.cpp lands a broad CUDA fusion of the Inkling 2 looped-model path plus a Windows RDNA1 Vulkan fix**, in a five-commit daytime wave.
- **SGLang adds day-0 MAGI-2-preview diffusion support and Lark grammars via xgrammar 0.2.9.**
- **A detailed community benchmark of the AMD RDNA-specific llama.cpp fork (llama-cpp-rdna-boosts) hit HN's front page**, reporting ~35→55 tok/s decode and ~700→1300 tok/s prefill on an R9700 eGPU.
- **Two in-window quantization drops**: an NVFP4 document-parsing VLM at ~8.4GB, and a 27B biomedical imatrix ladder down to 7.8GB.

## AI hardware and silicon

No new hardware or silicon disclosure originated inside this window. The two stories that dominated the morning river — Kioxia's GP2 high-IOPS SSD and Intel's Razor Lake-AX memory-on-package leak — were both first disclosed before the 08:45 window start and were already carried in the previous edition; they are not repeated here. A separate report that NVIDIA ordered partners to halt AI server shipments to Malaysia, Singapore, Thailand and Indonesia also broke in the pre-window morning hours (see footnote).

## Model releases

No new open-weight base model first went public in this window. The only model-family activity was community quantization work on existing bases, covered below.

## Community spins and quantizations

**OCRia-1-NVFP4 and OCRia-1-NVFP4-GGUF land as 4B NVFP4 document-parsing quants** — 2026-11-26 (HF repos created 13:55:15Z and 13:55:28Z = 08:55 EST). prithivMLmods published an NVFP4 GPTQ build (imatrix-mse observer, block size 128, visual tower left untouched) of LlamaIndex's OCRia-1, a Qwen3.5-4B-based image-to-Markdown OCR VLM, with the MTP speculative-decoding drafter preserved (241 MB `model_mtp`). Two shards total ~8.43 GB, so it fits comfortably on an RTX 5060 Ti 16GB or any 16GB+ card; tags target SM120/GB202 (RTX PRO 6000, RTX 5090) and B200/B300. The model card explicitly says the parent's ParseBench 2 score of 85.24 has **not** been independently re-verified on this NVFP4 build.
**Why it matters:** a genuinely small-footprint NVFP4 VLM that turns page images into structured Markdown/tables/LaTeX, with a drafter intact — the kind of local document pipeline that now fits consumer VRAM.
**Local inference:** ~8.4 GB weights + MTP → 12–16GB card; NVFP4 tensor cores required for the speed claim (Blackwell/RTX 50-class), CPU/Mac fall back to non-NVFP4 quants.
Source: prithivMLmods/OCRia-1-NVFP4 — https://huggingface.co/prithivMLmods/OCRia-1-NVFP4 (created 2026-11-26 13:55:28Z); model card https://huggingface.co/prithivMLmods/OCRia-1-NVFP4/raw/main/README.md. Companion GGUF: https://huggingface.co/prithivMLmods/OCRia-1-NVFP4-GGUF (13:55:15Z).

**mradermacher ships a full i1 imatrix ladder for AxonAI-v1-27B** — 2026-11-26 (HF repo created 14:27:50Z = 09:27 EST). A 27B biomedical model (AxonDAO/AxonAI-v1-27B, a LoRA-derived checkpoint) gets 24 i1 tiers from IQ1_S at 7.8 GB through Q6_K at 22.2 GB, with the Q4_K_S tier at 15.7 GB marked "optimal size/speed/quality" and Q4_K_M at 16.8 GB "recommended". Static (non-imatrix) GGUFs live in a separate repo.
**Why it matters:** a domain-specific 27B that can be dialed from 8 GB to 22 GB, so the same weights span 12GB through 24GB cards.
**Local inference:** IQ1_S/IQ2_XXS (7.8–9.0 GB) fit a 12GB card; IQ4_XS/Q4_K_M (15.4–16.8 GB) fit an RTX 5060 Ti 16GB; Q6_K (22.2 GB) needs 24GB.
Source: mradermacher/AxonAI-v1-27B-i1-GGUF — https://huggingface.co/mradermacher/AxonAI-v1-27B-i1-GGUF (created 2026-11-26 14:27:50Z); tier table https://huggingface.co/mradermacher/AxonAI-v1-27B-i1-GGUF/raw/main/README.md.

## Inference software and benchmarks

**vLLM's daytime wave adds GPU-Direct-Storage KV offload and a sparse-disk RL weight-transfer backend** — 2026-11-26, commits between 14:05Z and 17:28Z (10:05–13:28 EST). Twelve merges: GDS KV offloading (#66805) lets the KV cache spill to NVMe via GPU-Direct Storage; a sparse-disk backend (#69112) moves sparse RL weight deltas through storage; the Rust front end gains a `/v1/decisions` next-token endpoint (#70031); DCP now sizes sparse-indexer decode logits by local context (#70178). The wave also deprecates `cudagraph_copy_inputs`/compiler-managed input copies (#69675), removes DBRX (#70133), and fixes Mamba decode-resume caching a later-state block (#70068, #69626).
**Why it matters:** two distinct storage-tier features in one window — offloading KV to NVMe and streaming RL weight updates from disk — both trade PCIe/NVMe bandwidth for VRAM, changing the viable config for big-context and RL training boxes.
**Local inference:** KV-offload and sparse-disk paths benefit machines with fast NVMe and limited VRAM (16–24GB cards); the Rust FE endpoint and Mamba fixes apply across NVIDIA and ROCm builds.
Source: vLLM commits — https://github.com/vllm-project/vllm/commits/main (wave 14:05–17:28Z, e.g. #66805, #69112, #70031, #70178, #69675, #70068).

**llama.cpp fuses the Inkling 2 looped-model path in CUDA and fixes RDNA1 on Windows Vulkan** — 2026-11-26, five commits between 13:57Z and 16:15Z (08:57–12:15 EST). Lead change #33060 fuses the Inkling 2 short conv, depth mix, loop controller, L2 clip and "situ" chains into the CUDA path; #33123 fixes a Windows driver misclassifying RDNA1 as the wrong architecture under Vulkan; #33061 adds a DPI setting for PDF-as-images on the Inkling-2-Mini-BF16 work; #33125 reorganizes the UI demo harness.
**Why it matters:** Inkling 2 Mini is the dominant small local model of the moment, and folding its per-step operations into fused CUDA kernels cuts launch overhead on NVIDIA cards; the RDNA1 fix restores correct kernels for older AMD GPUs.
**Local inference:** CUDA fusion helps RTX 30/40/50-series (incl. 5060 Ti 16GB); the Vulkan fix targets RDNA1 (RX 6000) on Windows.
Source: llama.cpp commits — https://github.com/ggml-org/llama.cpp/commits/master (13:57–16:15Z, PRs #33061, #33123, #33060, #33125).

**SGLang adds day-0 MAGI-2-preview diffusion support and Lark grammars through xgrammar 0.2.9** — 2026-11-26, eight commits between 13:54Z and 17:43Z (08:54–12:43 EST). #35014 brings up the MAGI-2-preview video-diffusion model; #52655 loads the UMT5 text encoder in BF16 by default for the Wan diffusion path; #39380 adds Lark grammar support and bumps xgrammar to 0.2.9; #53390 refreshes the SemiAnalysis AgentX launch cookbook; #53392 allows the fake PD transfer backend under Kimi-K3 DCP.
**Why it matters:** fast diffusion-model onboarding and a more expressive structured-output grammar engine both widen what a single SGLang server can serve.
**Local inference:** diffusion and grammar features are backend-agnostic; benefit NVIDIA and AMD (ROCm) servers alike.
Source: SGLang commits — https://github.com/sgl-project/sglang/commits/main (13:54–17:43Z, PRs #35014, #52655, #39380, #53390, #53392).

**A community benchmark of the AMD RDNA-specific llama.cpp fork surfaced on HN's front page** — 2026-11-26 (blog post 09:59 local, HN story 17:11Z = 13:11 EST, 81 points / 22 comments). The write-up recommends `llama-cpp-rdna-boosts`, a small patch set reapplied to pinned llama.cpp commits, reporting on an RX 9700 (R9700) connected over eGPU: prefill ~700→1300 tok/s and token generation ~35→55 tok/s on Qwen3.8-27B Q6_K_L with MTP enabled. The fork's README describes RDNA3/3.5/4 support (gfx1100/gfx1151/gfx1201) with RDNA2 best-effort, MTP decode, chunked gated-delta-net prefill, BF16 KV and WMMA flash-attention, fused MoE/k-quant paths, and release v18-r366 rebased on upstream 919794500. Note: the fork itself dates to August, and the blog's numbers are a single author's setup, not a controlled benchmark.
**Why it matters:** a credible, numerically-assurance-focused AMD-specific path in a field where most speed claims come from opaque vibe-coded forks — plus an explicit critique of forks that change how a model "thinks" to inflate tok/s.
**Local inference:** targets RDNA3/RDNA3.5/RDNA4 discrete cards (e.g. R9700 16/32GB, RX 9070, Strix Halo APU); RDNA2 (gfx1030) best-effort, no tensor-core paths.
Source: devnull.land — "AMD-specific fork of llama.cpp", https://devnull.land/amd-specific-llama-cpp (2026-11-26 09:59); HN thread https://news.ycombinator.com/item?id=50657265 (created 2026-11-26 17:11:29Z); repo https://github.com/stew675/llama-cpp-rdna-boosts (release v18-919794500-r366, published 2026-11-26 12:52:06Z).

### Also noted

- **TensorRT-LLM minor wave** — four in-window commits (15:32–16:06Z): Kimi-K3 serve now takes chat prompt ids from its renderer and aligns `count_tokens` with usage (#21111); NIXL agents recover and stalled requests fail cleanly (#22530); two Helix cache-isolation test fixes. Bugfixes, no new hardware target. Source: https://github.com/NVIDIA/TensorRT-LLM/commits/main.

## Just missed the window

- NVIDIA was reported to have ordered partners (including Dell and Supermicro) to halt AI server shipments to Malaysia, Singapore, Thailand and Indonesia under the November 16 BIS interim rule. The story broke in the pre-window morning hours (Reuters/FT, ~03:00–04:00 EST) and is a policy/availability development rather than a product disclosure. Source: Data Center Dynamics — https://www.datacenterdynamics.com/en/news/nvidia-halts-server-shipments-to-malaysia-singapore-thailand-and-indonesia-in-wake-of-new-dept-of-commerce-export-rule-report/.

## Coverage notes

Verified in-window items: **7** (2 community spins/quantizations, 4 inference-software/benchmark items, 1 also-noted TRT-LLM wave; 1 just-missed footnote, excluded from the count). Counting unit = distinct first-disclosed events, not commits. The four inference waves bundle 29 upstream commits across vLLM (12), SGLang (8), llama.cpp (5) and TRT-LLM (4).

Dedupe: candidates suppressed as already reported = **4** (Kioxia GP2 SSD, Intel Razor Lake-AX MoP, Persistent-Exploration-Qwen3VLi-i1-GGUF, and the earlier overnight llama.cpp/vLLM/SGLang/TensorRT-LLM waves already listed under the 08:45 fire). One repeat "just missed" candidate was not footnoted a second time (`us-entity-list-megaspeed-loophole`).

Coverage limitations: r/LocalLLaMA was unreachable this run — both `search.rss` and `sub` anonymous feeds returned HTTP 404 — so Reddit community posts could not be swept; HN's date-sorted API was used as the community lane instead. The general web search backend returned no in-window hardware hits for two queries (SemiAnalysis/MLCommons and a dated RDNA-4 query); index pages for Tom's Hardware, VideoCardz, ServeTheHome, The Register and TechPowerUp were read directly. HF "createdAt" was used as the disclosure anchor for all model items; commits were gated on author timestamps. This midday window fell on a US holiday, and no first-disclosed AI-silicon item cleared the in-window bar — the report does not pad to fill that gap.
