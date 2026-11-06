# AI Hardware & Local Models Brief — November 6, 2026 (afternoon edition)

**Window:** 2026-11-06 12:45 → 17:45 ET (17:45–22:45 UTC) · New items only, deduped against the job ledger.

## TL;DR
- **Nous Research ships Arete-27B** — an Apache-2.0 SFT of Alibaba's Qwen3.8-27B aimed at stripping state-aligned framing: propaganda rate on Nous's 803-prompt eval fell from 22% to 0.9%, with no capability regression; day-0 GGUFs landed within two hours.
- **TensorRT-LLM v1.4.0rc3** published — and it **enables DeepSeek-V4.1-Flash on SM120** (consumer RTX 50-series), plus Kimi K3 NVFP4-KV consolidation, MiniMax-M3.1-Flash DSpark serving and Blackwell speedups for Qwen3.5/Qwen3.8-Flash-Next/GLM-5.3-Flash.
- **Kimi K3.1-Pro goes day-0 local**: merged into llama.cpp (#31779) at 17:42 ET and into vLLM (first two of five K3.1 split PRs) minutes before the window closed.
- **zml/llmd llmd-20261106.0** — the Zig/MLIR universal inference server announced its daily release on r/LocalLLaMA: NVIDIA/AMD/Intel/TPU/Metal, DFlash/DFlash2/DSpark spec-decode, 1.96 GB CUDA image.
- **LlamAmpere v0.6.3** claims 120+ tok/s on Qwen3.8-Flash-Next at 100K context from an RTX 3090 + 64 GB RAM with its AmpereInteger+ quants — author-reported, "50% lower error than Strata 3XS."

---

## Model releases

**Nous Research releases Arete-27B: post-training "Western values" onto Qwen3.8-27B** — *November 6, 2026 (HF repo 19:31 UTC; blog same day)*
Nous published Arete-27B, a 27.4B-parameter supervised fine-tune of Alibaba's Qwen3.8-27B (license Apache-2.0), trained on ~52.8M tokens of teacher-generated data over two epochs (1,202 steps, TP4, bf16) using Thinking Machines' Inkling as the open teacher — explicitly chosen to dodge "illicit distillation" terms-of-use issues. Nous built a new 803-prompt "Propaganda_Eval" across seven value categories (freedom of expression, state ideology, democracy, data privacy/surveillance, education, free market, environment) judged by Grok 4.7: the fact score rose from 2.32 to 2.98 of 5 and the share of answers containing state-frame propaganda fell from 22% to 0.9% (freedom-of-expression category: 0.89→2.44 fact score, 60%→1% propaganda). Hermes Bench was unchanged (65.60% vs 65.77%) and Terminal-Bench-2.1 came in at 70% vs 67%; vision encoder frozen, MTP head disabled during training. The eval set and the 20K-prompt training dataset (arete-data-pairs) are released.
**Why it matters:** This is the strongest demonstration yet that value alignment in frontier-class Chinese open weights is a *thin, late-applied layer* — fifty million tokens removed it without touching agentic capability. It reframes the "Chinese model risk" debate from a pre-release ban problem into a fine-tune away, and it puts an open, judge-published value-eval into circulation that anyone can run on any checkpoint.
**Local inference:** same qwen3_5 arch as Qwen3.8-27B — GGUFs (bartowski, created 21:39 UTC) land in the 16–24 GB card class at IQ3/Q4 tiers with offload; Q8_0 ~30 GB wants a 4090/5090; BF16 (54.7 GB) runs on 96 GB Mac Studio or DGX Spark 128 GB. Note: trained with the MTP head disabled, so self-speculative drafts from base Qwen3.8-27B quants may not transfer.
Sources:
- Nous Research blog — "Introducing Arete-27B" (Nov 6, 2026) — https://nousresearch.com/arete
- HF model card (created 2026-11-06T19:31Z) — https://huggingface.co/NousResearch/Arete-27B
- bartowski GGUF ladder — https://huggingface.co/bartowski/NousResearch_Arete-27B-GGUF
- r/LocalLLaMA thread — https://www.reddit.com/r/LocalLLaMA/comments/1xpmj33/nousresearcharete27b_hugging_face_qwen_finetune/
- Independent coverage: https://explainx.ai/blog/nous-research-arete-27b-qwen3-8-western-values-propaganda-22-to-0-9-percent-claimed-vs-verified-2026 ; https://hermes-ai.net/news/nous-research-s-arete-27b-cuts-chinese-state-aligned-responses-by-95/

---

## Inference software and benchmarks

**TensorRT-LLM v1.4.0rc3: DeepSeek-V4.1-Flash qualified on SM120, Kimi K3 NVFP4-KV, Helix KV manager** — *November 6, 2026 (release published 21:38 UTC)*
NVIDIA pushed the third 1.4 release candidate with a dense changelog: **DeepSeek-V4.1-Flash enabled on SM120** (consumer Blackwell RTX 50-series), Muse Spark Open model support, GLM-5.2 NVFP4 Modeling V2 targets for GB300/SM103, MiniMax-M3 NVFP4 low-latency decode plus MiniMax-M3.1-Flash DSpark serving, consolidated Kimi K3 native modeling with NVFP4 KV caching, and dedicated Blackwell serving accelerations for Qwen3.5, Qwen3.8-Flash-Next and GLM-5.3-Flash. Foundation work includes a unified KV-cache manager with host-backed pools, "Helix" aggregated KV sizing with request preemption, native sparse-MLA attention for DeepSeek-V4 generation, FP8 MoE LoRA weights, and TTFT-percentile admission control. Known issues: DSA models (e.g. GLM-5.2) may mis-output with NVFP4 KV; Kimi K3 DSpark+MegaMoE can hang during GB300 warmup.
**Why it matters:** TRT-LLM has historically been datacenter-only; an SM120 gate for DeepSeek-V4.1-Flash means NVIDIA is now qualifying its fastest engine path for consumer Blackwell — directly competing with llama.cpp/vLLM on the boxes local users own. The NVFP4-KV and Helix host-pool work previews how 100K+ contexts will fit in 16–32 GB cards.
**Local inference:** SM120 = RTX 5060 Ti/5070 Ti/5080/5090 class; SM103/107 and GB300 lines target workstation/rack. NVFP4 quantization keeps MoE decode within 16 GB cards when combined with host-backed KV.
Source: NVIDIA GitHub release (2026-11-06T21:38:32Z) — https://github.com/NVIDIA/TensorRT-LLM/releases/tag/v1.4.0rc3

**Kimi K3.1-Pro day-0 support lands in llama.cpp and vLLM** — *November 6, 2026 (llama.cpp #31779 merged 22:42 UTC; vLLM #66030/#66028 merged 22:31 UTC)*
llama.cpp merged "model: add Kimi-K3.1-Pro text model" (PR #31779) — Moonshot's ~3T-class open-weight flagship can now be loaded and quantized by the community pipeline — while vLLM merged the first two of five K3.1 split PRs: model files with an NVIDIA-specialized decode path (#66030) and a Rust-frontend chat renderer (#66028). This is a new milestone on the previously reported K3.1-Pro event (ledger Nov 5: weights + Unsloth/MLX/vllm-project quant wave), the first in-engine text-model support from the core repos.
**Why it matters:** Day-0 core-repo support compresses the time from weights to working quants from weeks to hours; it also signals the two ecosystems (llama.cpp server, vLLM) are treating 3T-class MoE flagships as first-class targets, including specialized NV decode kernels.
**Local inference:** full K3.1 remains multi-GPU/pruned-tier territory (738B-token pruned mxfp4, 2-bit UVMAX MLX tiers already reported); the llama.cpp merge is what makes the hobbyist IQ1/IQ2 single-3090-class experiments (à la the reported DS-V4.1-Flash 1-bit builds) feasible for K3.1 next.
Sources:
- https://github.com/ggml-org/llama.cpp/pull/31779 (merged 2026-11-06T22:42Z)
- https://github.com/vllm-project/vllm/pull/66030 (merged 2026-11-06T22:31Z)
- r/LocalLLaMA: https://www.reddit.com/r/LocalLLaMA/comments/1xpqm0b/model_add_kimik31pro_text_model_by_usrlocalben/

**zml/llmd llmd-20261106.0 — universal multi-backend inference server, "we believe the fastest"** — *November 6, 2026 (r/LocalLLaMA announcement, in-window)*
ZML (the Zig/MLIR/Bazel stack out of the Zed team's orbit) posted its daily build train "llmd-20261106.0" to r/LocalLLaMA: one engine spanning NVIDIA CUDA, AMD ROCm, Intel oneAPI, Google TPU and Apple Metal with continuous batching, prefix caching, tool calling, tensor/expert-parallel auto-sharding, DFlash/DFlash2/DSpark speculative decoding, and a native VFS that streams s3:// and hf:// straight to GPU. Ships unusually small images (1.96 GB CUDA, 4.77 GB ROCm, 272 MB TPU, 142 MB Metal) and supports a long current-model list: Qwen 3.5/3.6/3.8 series incl. Qwen3.8-Flash-Next, DeepSeek 4.1 Flash, Muse Glimmer, GLM 5.2/5.3, Mistral Large 4, Ripple 65B, Poolside Laguna S 2.1. The framework is Apache-licensed; the team says only the framework is open "for now."
**Why it matters:** a genuinely multi-vendor server with modern spec-decode drafts and tiny images attacks the two things that keep people on llama.cpp/Ollama — install weight and non-NVIDIA support — from a serious engineering org.
**Local inference:** Metal/Homebrew path for Macs, sub-2 GB CUDA images for any 16 GB+ card; DSpark/DFlash2 support matters most for DGX Spark and RTX 50-class boxes running Qwen3.8-Flash-Next.
Sources:
- r/LocalLLaMA post (in-window) — https://www.reddit.com/r/LocalLLaMA/comments/1xpppfl/zmlllmd_a_fast_and_universal_inference_server/
- Repo — https://github.com/zml/zml ; product post (July baseline) — https://zml.ai/posts/llmd/

**LlamAmpere v0.6.3: Qwen3.8-Flash-Next at 120+ tok/s on RTX 3090 + 64 GB (author-reported)** — *November 6, 2026*
Developer Brief-Tap-6616 released LlamAmpere v0.6.3 with new AmpereInteger+ quants (encoder promised publicly): Qwen3.8-Flash-Next runs ~120+ TPS decode at 100K depth with prefill >3,000 tok/s through the first 100K on a single RTX 3090 (24 GB) + 64 GB system RAM. Claims: 50% error reduction vs the Strata 3XS-tier file released days ago and 27% vs Unsloth Dynamic v3.1 3XXS (KLD-based), plus a 3XS-class build ~10% faster that the author doesn't recommend. Performance depends on CPU/RAM speed as with Strata; the author notes Unsloth's v3.1 was used as the quant base.
**Why it matters:** Ampere cards (3090/3080) can't run NVFP4 or the new Blackwell engines; a native Ampere engine closing the quality gap with Strata/UD quants extends the viable hardware base for Flash-Next-class MoEs to hundreds of thousands of used cards.
**Local inference:** targets exactly the 24 GB GPU + 64 GB DDR4/5 tier (3090/3080 rigs, 12+64 GB configs).
Source: r/LocalLLaMA post (in-window) — https://www.reddit.com/r/LocalLLaMA/comments/1xpmzg7/50_lower_error_than_strata_3xs_27_lower_error/

**Engine merges in-window (llama.cpp / vLLM / SGLang)** — *November 6, 2026, 17:45–22:45 UTC*
llama.cpp: Hexagon conv_2d optimizations + conv_3d support (#31814, 19:51 UTC), Docker NVIDIA library search path (#31806). vLLM: Sleep-Mode Level 2 discards only parameters (#66010, 20:34 UTC) — big for single-GPU model hot-swapping; ROCm AITER all-gather for small TP (#63890); Muse Glimmer AMD fusion kernels (#61448); ROCm MoE AITER-quant fix (#65517). SGLang: fused single-kernel decode MoE routing for Kimi-K3 at 1–256 rows (#48586); gemma4 out-of-tree MoE expert-executor selection (#41635); NVLink custom-allreduce scoped to its own node's fabric UUID (#48554); a five-PR "sglang-miles" multimodal per-item feature-cache series; rmsnorm_mxfp8 two-warp fast path (#48941).
**Why it matters:** sleep-mode L2 and per-node allreduce scoping are quality-of-life wins that change practical single-GPU and small-cluster operation; the fused-routing and mxfp8 norm work keeps K3/DSv4-class MoE decode pressure dropping on both CUDA and ROCm.
**Local inference:** Sleep-Mode L2 → multi-model serving on one 16–32 GB card; Hexagon conv work improves Snapdragon/Qualcomm NPU boxes; K3 routing fusion is datacenter-side (H200/GB300).
Sources: https://github.com/ggml-org/llama.cpp/pull/31814 ; https://github.com/vllm-project/vllm/pull/66010 ; https://github.com/vllm-project/vllm/pull/63890 ; https://github.com/vllm-project/vllm/pull/61448 ; https://github.com/sgl-project/sglang/pull/48586 ; https://github.com/sgl-project/sglang/pull/41635 ; https://github.com/sgl-project/sglang/pull/48554

---

## Community spins and quantizations

**bartowski day-0 GGUFs for XingChen-AGI Xing4.0-29B-A4B** — *November 6, 2026 (17:31 UTC upload)*
A quant ladder for China Telecom's Xing4.0-29B-A4B (31.2B total / ~4B active MoE, custom `xing4_0` arch that llama.cpp just learned on Nov 5) landed in-window: intent-tuned sibling also quantized. Base weights have been on the Hub since September but this is the first GGUF tier, and r/LocalLLaMA is pairing the two ("new 29B MoE today?").
**Why it matters:** gives the community a fourth mid-size MoE option (alongside Qwen3.6-35B-A3B / DSv4.1-Flash / M3.1-Flash) whose fresh arch support was the actual blocker.
**Local inference:** A4B MoE at Q4_K_M ≈ 19–20 GB — comfortable on 24 GB cards, 16 GB-class (5060 Ti) at IQ3/Q3 with CPU offload of routed experts.
Sources: https://huggingface.co/bartowski/XingChen-AGI_Xing4.0-29B-A4B-GGUF (created 2026-11-06T17:31Z) ; base https://huggingface.co/XingChen-AGI/Xing4.0-29B-A4B ; r/LocalLLaMA https://www.reddit.com/r/LocalLLaMA/comments/1xplkdu/bartowskixingchenagi_xing4029ba4bgguf_hugging_face/

**Scylla's Band v4: singing 131M open TTS, 30 ms warm first-audio** — *November 6, 2026*
Solo developer clockentyne released v4 of his Apache-2.0 TTS: 131M params + 30M phonemizer, 14 voices across EN (US/UK)/ES/FR/DE/IT/VI, per-line intensity/pace/pitch, multi-voice dialogue tags, streaming, and — unusually for a phone-class model — singing from a JSON score. Reported: ~30 ms warm first audio on M5 Max, 70 ms iPhone 17 Pro Max, 120 ms Galaxy Z Fold 8 (CPU only), 30x+ real-time on desktop CPUs, English WER 0.85%/UTMOS 4.35. Ships PyTorch plus LiteRT, Core ML, ONNX Runtime and WebGPU ports and a C++ runtime with text normalization.
**Why it matters:** sub-100 ms local TTFB with singing capability is a real alternative to cloud TTS for voice-agent loops; the multi-backend ports make it drop-in for assistant stacks.
**Local inference:** phone SoC and laptop CPU class — no discrete GPU required.
Source: r/LocalLLaMA (in-window) — https://www.reddit.com/r/LocalLLaMA/comments/1xpq3l0/scyllas_band_v4_open_source_tts_with_code_to_run/

**mradermacher in-window quant ladder** — *November 6, 2026 (17:21–22:55 UTC uploads)*
Fresh i1/imatrix GGUFs: **Qwen3.5-0.8B-iPhone-Agent-Weight-Space-Programming** (20:15 UTC — a quant of the weight-space-programming iPhone-agent spin, riding the MIT WSP wave), Industry-Coder-27B-v3.0 (19:40 UTC), Meerkat2-14B-i1 (21:13 UTC), LFM2.5-24B-A2B-uncensored-fiction-nblora-merged2 (21:36 UTC), plus kofu-2b-edge and Alpie_seed.
**Why it matters:** the WSP-quantized agent model is the quant-layer echo of this week's most-discussed research direction; Industry-Coder 27B v3.0 keeps a long-tail of vertical coders quantized within a day.
**Local inference:** 0.8B–14B tiers run anywhere (CPU/phone/8 GB); the 24–27B MoE/fiction merges target 16–24 GB cards at IQ1–Q3.
Sources: https://huggingface.co/mradermacher/Qwen3.5-0.8B-iPhone-Agent-Weight-Space-Programming-GGUF ; https://huggingface.co/mradermacher/Industry-Coder-27B-v3.0-GGUF ; https://huggingface.co/mradermacher/Meerkat2-14B-i1-GGUF ; https://huggingface.co/mradermacher/LFM2.5-24B-A2B-uncensored-fiction-nblora-merged2-i1-GGUF

---

## Also noted

- **ServeTheHome reviews the Cisco Catalyst C1300-24XTS** (Nov 6, 20:30 UTC) — a 24-port 10GbE switch (12×10GBase-T + 12×SFP+, Marvell silicon) torn down and loaded; relevant to 10GbE homelab inference clusters. https://www.servethehome.com/cisco-catalyst-c1300-24xts-review-a-neat-24-port-10gbe-switch-marvell/
- **RTX PRO 6000 Max-Q price watch** (r/LocalLLaMA, in-window): Micro Center cut the workstation variant to $16.5k (from $18k peak) while Max-Q sat at $14k; Dell discounts reached $11.5k for some buyers; a decommissioning-sector contact predicts used A100 40GB SXM4 units approaching ~$1k within 3–6 months as DC fleets cycle — anecdotal, single-sourced. https://www.reddit.com/r/LocalLLaMA/comments/1xppbrr/some_gpu_prices_are_dropping/
- **Ollama prompt cache breaks determinism** (r/LocalLLaMA): same model/prompt/temp-0/fixed seed gives different answers purely from prompt-cache reuse — a correctness gotcha for eval harnesses (v2.0 of the thread). https://www.reddit.com/r/LocalLLaMA/comments/1xpmwub/how_is_it_that_with_the_same_model_the_same/
- **Another weight-editing replication**: kevinpl07 applied the MIT weight-space-programming recipe to prompt-injection-prone models and a Whisper silence-hallucination case — "useful for patching one articulable flaw, nothing crazy." https://www.reddit.com/r/LocalLLaMA/comments/1xpls17/tried_the_weight_editing_thing_from_the_mit_post/
- **CCORE author kicks off a community-designed 4B coding agent** — ConfidentDinner6648 (3D-Coder/GLB-3D-Coder) opens design discussion for a "genuinely useful 4B coding agent" before training this time. https://www.reddit.com/r/LocalLLaMA/comments/1xppw37/im_going_to_try_to_turn_a_4b_model_into_a/
- **SketchAgentBench** — benchmark for 3D spatial reasoning over 25 real photos, 11 models run, results inspectable in 3D. https://www.reddit.com/r/LocalLLaMA/comments/1xppg8x/sketchagentbench_evaluate_llms_on_3d_spatial/
- **Sam Altman's "PC RAM is used 5% of the time" quote draws a scripted rebuttal** — IainKay shipped a bash script that prints local model weight residency to dispute the claim. https://www.reddit.com/r/LocalLLaMA/comments/1xpmui2/altman_says_pc_ram_is_being_used_maybe_5_of_the/

## Just missed the window
- **Cactus Compute Mirage** — 22 MB (51.3M-param) single-pass VLM (captioning+OD+OCR+segmentation+relations), 74 ms on Galaxy S26 NPU, Apache-2.0: the HF repo was created Nov 5, 19:01 UTC — ~23h before this window — with the author's r/LocalLLaMA announcement landing in-window. First footnote, will not repeat. https://huggingface.co/Cactus-Compute/mirage

## Coverage notes
- **Verified count: 16 in-window items** (5 leads; 4 section items; 2 substantive minor items; 5 one-liners — counting the engine-merge wave as one) **plus 1 just-missed footnote**, across outlets/sources including Nous, NVIDIA GitHub, ggml-org, vLLM, SGLang, Hugging Face Hub (12+ distinct orgs), ServeTheHome, TechPowerUp, The Register, r/LocalLLaMA. Lead stories (Arete card+blog, TRT-LLM rc3 notes, llmd/LlamAmpere/Mirage/Scylla post bodies) were read in full.
- **Ledger dedupe:** ~17 candidates suppressed as already reported or out-of-window re-coverage: TSMC 3D DRAM (3 fresh in-window articles), RTX 5070 Ti price-tracking (TPU re-run of today's 07:45 item), $11.4M NVIDIA heist (TPU re-run), RTX PRO 5500 $17,999 (repost), Inkling-2 recap (latent.space/HN), Kimi K3.1-Pro trending (weights/quant wave reported), "bare metal +20%" (footnoted Nov 5 — repeat suppressed), Pi 16GB $385 hike (Nov 3), DRAM +369% Counterpoint (Nov 5), PyTorch 2.15 (Oct 28 event, article re-cover), AMD "data-center-first" (Nov 5 interview), AtomicChat uncensored GGUF trending (Oct 28), Open WebUI v0.13.0 (Nov 3), Microsoft/Oracle + Jensen-congress (out of scope), Supermicro Super AI Station (repeat missed-suppressed), Gartner/Revolut re-coverage.
- **Dead ends / limitations:** VideoCardz RSS blocked (Cloudflare, request ID a51aca609f0738ce) — its data arrived via TechPowerUp citing VideoCardz; nvidia/simready-assets (HF trending) is gated/auth-gated — unresolved, dropped after one attempt; zml/zml GitHub has no tagged releases (rolling train verified via post + repo only); Reddit anonymous backend caps scores/comments, so community ranking signals were unavailable (timestamps verified by in-window "new" listing + thread fetches).
