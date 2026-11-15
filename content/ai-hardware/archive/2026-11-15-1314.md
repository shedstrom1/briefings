# AI Hardware & Local Models Brief — Sunday, November 15, 2026 (12:45 ET edition)

**Window: 2026-11-15 07:45 → 12:45 EST** (previous fire → this fire; new items only, ledger-deduped)

## TL;DR
- **Basalt 0.4.0 lands its first dense model**: Qwen3.8-27B at up to 776 tok/s structured output on a single RTX 5090, 92.8% top-1 agreement vs BF16 — author-run numbers, with methodological pushback in-thread.
- **llama.cpp b12414–b12420** merges MiniMax-M3.1 model support, an AVX-VNNI Q2_0 dot-product path claiming ~3x CPU speedups, and DFLASH2 spec-decode for Ripple-65B.
- **A second builder independently reproduces the CMP 170HX voltage-glitch unlock** — 80GB HBM and 126 SMs revived on a ~$2.5k mining card.
- **A cudaMemcpy-tuning tool lifts CPU-offloaded MoE prefill 345→466 tok/s (+35%)** on a lone RTX 3090.
- **Stealth sighting**: forensic analysis of a leaderboard screenshot points to an unreleased GLM trio (Nano/Med/Small); no such models on z.ai or the Hub yet.

## Inference software and benchmarks

**Basalt 0.4.0: first dense model, Qwen3.8-27B at up to 776 tok/s on one 5090** — event date: announced today (r/LocalLLaMA 17:30 UTC; GitHub tag v0.4.0 Nov 14 23:51 UTC).
Developer u/jesdga95 shipped Basalt 0.4.0, adding its first dense-model engine alongside the existing MoE path: Qwen3.8-27B claims up to 776 tok/s on structured output, 262 tok/s conversation, 194 tok/s prose on a single RTX 5090, prefill 12,000 tok/s at 4K context (degrading as context grows), 92.8% top-1 agreement vs the BF16 reference (0.0262 KLD), up to 262K context with chat/tools/thinking/images/video. Also new since 0.3.x: multi-model router support starting with DeepSeek-V4.1-Flash (IQ1_M 90 tok/s conversation / 246 structured on a 5090 + 5060 Ti pair). Commenters pushed back that the headline "up to 770" may lean on the second GPU and that KLD was measured on only 512 wikitext tokens — treat as author-reported. The work is framed as a head start on dense kernels ahead of Qwen 4.
*Why it matters:* Basalt's engine-per-model specialization keeps resetting the practical speed ceiling on consumer Blackwell, and dense 27B at triple-digit decode directly pressures llama.cpp's defaults on the most-populated VRAM tier.
*Local inference:* single RTX 5090 32GB (with optional second card for the MoE router path); Q4-class 27B quants, CUDA.
Sources:
- Basalt 0.4.0 announcement, r/LocalLLaMA, Nov 15 17:30 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xx1cu9/basalt_040_more_models_still_as_fast_qwen_27b_up/
- GitHub release v0.4.0, Nov 14 23:51 UTC — https://github.com/jesdga95/basalt/releases
- Companion quant — https://huggingface.co/jesdga/Qwen3.8-27B-Basalt

**llama.cpp b12414–b12420: MiniMax-M3.1 day-0 support, AVX-VNNI Q2_0 ~3x CPU boost, Ripple-65B DFLASH2** — event date: Nov 15 (builds 14:57–17:25 UTC).
Five in-window builds merged a CPU-relevant headline: PR #26348 adds an x86 AVX-VNNI integer dot-product path (plus AVX-512 VBMI unpacking) for Q2_0 matmuls, claiming ~3x speed for Q2_0 on VNNI-capable CPUs (b12418). PR #32301 (b12420) lands native MiniMax-M3.1 architecture support — ~3 weeks after the M3.1 open weights — and PR #31904 (b12416) fixes DFLASH2 speculative decoding for RecursiveAI Ripple-65B-Preview (NoPE handling). Also in the wave: #31617 CUDA/HIP warp `_sync` refactor now using ROCm-native primitives and raising the ROCm floor to 6.3 (b12414), #32321 Anthropic-messages `effort` drop fix, #32093 empty-stop-string fix.
*Why it matters:* VNNI Q2_0 is a free double-to-triple for the large installed base of no-GPU boxes (Xeon Scalable/EPYC/Most consumer AM5) running small quants, and M3.1 support makes the 428B/23B MoE finally usable in the flagship consumer runtime.
*Local inference:* Q2_0 CPU path targets VNNI-capable desktop/server CPUs (24GB+ RAM class); M3.1 support benefits offload rigs and 128GB-class boxes.
Sources:
- b12418 (15:53 UTC) — https://github.com/ggml-org/llama.cpp/releases/tag/b12418
- b12420 (17:25 UTC) — https://github.com/ggml-org/llama.cpp/releases/tag/b12420
- b12416 (15:39 UTC) — https://github.com/ggml-org/llama.cpp/releases/tag/b12416
- b12414 (14:57 UTC) — https://github.com/ggml-org/llama.cpp/releases/tag/b12414

**cudamemcpy-tuner: +35% prefill on a 3090 by tuning the expert copies** — event date: Nov 15 (r/LocalLLaMA 17:43 UTC).
u/makneeee released a tool that autotunes `cudaMemcpy` configurations and applied it to MoE CPU-offload: Qwen3.6-35B-A3B Q8_0 with `-ot exps=CPU` on one RTX 3090 + Ryzen 7 8000X3D goes from 345 to 466 tok/s prompt processing (+35%) at 2048 tokens, llama-bench repeated runs. Code on GitHub, write-up on the author's blog.
*Why it matters:* offloaded-expert prefill is the bottleneck that makes or breaks 30–40B-A3B MoE on a single 12–16GB card; a 35% copy-path win is a system-wide gain any engine can adopt without touching kernels.
*Local inference:* RTX 3090 24GB + AM5 (X3D) host; directly relevant to 16GB-card offload rigs (5060 Ti 16GB class).
Sources:
- r/LocalLLaMA post, Nov 15 17:43 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xx1owx/moe_offload_on_a_3090_prefill_345_466_ts_by/
- Tool — https://github.com/Maknee/cudamemcpy-tuner

**vLLM / SGLang in-window merge waves** — event date: Nov 15 (12:45–17:45 UTC, GitHub merge search).
vLLM merged #65992 (DeepSeek-V4.1: fused mHC seams run as AITER's single-launch HIP Mega-MHC on ROCm), #65926 (DSv4.1 K-cache gather int64 row-offset bugfix) and #63976 (compressed-tensors CUTLASS block-FP8 cache restarts). SGLang merged #43048 (qwen4-exp gains GR + PLE sequence parallelism), #50374 (in-kernel event tracing, IKET/%clock backends for kernel debugging), #49328 (HiCache L3 prefetch token accounting) and #50358 (qwen-image-2.1 diffusion edit-size parity with Diffusers). Ollama: no in-window merges; TensorRT-LLM merged CI/infra-only changes.
*Why it matters:* the ROCm single-launch Mega-MHC and qwen4-exp SP work both widen what AMD and mid-tier cards can serve efficiently.
*Local inference:* ROCm (MI-series and Radeon AI Pro class) and SM10x-heavy stacks; SGLang qwen4-exp targets multi-GPU parity serving.
Sources:
- vLLM #65992 — https://github.com/vllm-project/vllm/pull/65992
- vLLM #63976 — https://github.com/vllm-project/vllm/pull/63976
- SGLang #43048 — https://github.com/sgl-project/sglang/pull/43048
- SGLang #50374 — https://github.com/sgl-project/sglang/pull/50374

## AI hardware and silicon

**CMP 170HX voltage-glitch unlock independently reproduced: 80GB + 126 SMs restored on a second card** — event date: Nov 15 (r/LocalLLaMA 15:16 UTC).
u/Yarukiless-cat reports successfully executing the CMP 170HX "voltage glitch" (disclosed by kate.land Nov 10, footnoted once in this brief): using a Raspberry Pi Pico to glitch fuses, they restored the full A100-class 80GB HBM3 and 126 SMs on a CMP 170HX, after ~15 hours of work with an AI-written glitch script, and plan a second card. Thread consensus notes used CMP 170HX prices have already climbed to ~$3k on the back of the mod, with several owners waiting for the procedure to stabilize (partial-VRAM and 64GB-error risks mentioned).
*Why it matters:* a second independent success moves the unlock from "blog claim" toward a replicable procedure — a ~$2.5–3k path to 80GB of HBM is the cheapest way yet into long-context local inference territory.
*Local inference:* CMP 170HX (GA100-class, 8GB→80GB unlocked, PCIe Gen4 + NVLink restored per the original write-up); per card, 80GB serves 70B-class BF16 or 200B+-class MoE offload.
Sources:
- Reproduction thread, r/LocalLLaMA, Nov 15 15:16 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xwxzgj/i_finally_succeed_the_cmp_170hx_voltage_glitch/

## Model releases (and sightings)

**Stealth sighting: forensics point to an unreleased GLM trio (Nano / Med / Small)** — event date: Nov 15 (r/LocalLLaMA 15:21 UTC).
u/Altruistic_Heat_9531 compiled evidence that Z.ai has three models sitting on an internal leaderboard (blurred names, scores 59.4 / 57.1 / 54.0), most likely a GLM-Nano / -Med / -Small ladder: a de-blurring experiment reproduced the screenshot text at RMSE 1.254/255, the tipster AICodeKing's past GLM calls landed 0–11 days before official releases, and a spike in Z.ai Hugging Face activity (commits in the last 3–4 days vs a 27-day baseline) points at imminent drops, with speculation of a launch this week. Negative check confirms nothing named GLM-Nano exists on z.ai's model docs or the Hub beyond the unrelated GLM-ASR-Nano (Dec 2025) — treat as unverified until weights appear.
*Why it matters:* the community's early-warning pipeline (leaderboard forensics + HF commit velocity) is now precise enough to size hardware ahead of releases; a GLM "Nano" tier would slot straight into the 16–32GB single-card class this brief tracks.
*Local inference:* speculative — if the trio is 3B/8B/30B-class, all fit consumer VRAM tiers.
Sources:
- r/LocalLLaMA thread, Nov 15 15:21 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xwy3ka/probable_glm_model/

## Community spins and quantizations

**mradermacher's Sunday ladder: 11 day-0 GGUF tiers, 14:39–17:43 UTC** — event date: Nov 15 (Hub createdAt timestamps).
In-window creations: Sijo-1.7B-GGUF (myjellylabs agentic/coding RSLora-DPO-RLHF family), MEMPHIS-GGUF (darkc0de heretic/uncensored), LFM3-1.7B-3D-Coder-GGUF, gemma-4-31B-it-heretic-NSFW GGUF + i1 pair, SmolLM3-3B-Instruct-heretic GGUF + i1 pair, Aether-15B-i1-GGUF (K-Ai JP/ID math-code; new tier), AxonMed-32B-Titan-GGUF (USMLE/clinical MoE), Qwen3.5-0.8B-Python-SoftMoE-GGUF (NikitaLazarev no-training weight-steering), granite-moe-code-specialist-0.7b-v0-GGUF (expert-pruned PoC), gemma-4-E4B-it-SRE-GGUF, gemma-3-heretic-4-public-GGUF.
*Why it matters:* the 0.7B–3.7B experimentals (Python-SoftMoE, expert-pruned Granite) are the tier where new quant/steering techniques reach phones and edge boxes first.
*Local inference:* all sub-35B — 0.8B/1.7B tiers run on 8GB cards, phones, or CPU; 31B heretics target 16–24GB with Q3/Q4.
Sources: https://huggingface.co/mradermacher (per-repo timestamps verified via Hub API)

**balllvin/LFM3-1.7B-NVFP4-MLX** — Nov 15 17:31 UTC: NVFP4 (4-bit) MLX repack of Liquid AI's LFM3-1.7B for Apple Silicon. Sub-1GB-class footprint; phone/M-series-friendly.
Source: https://huggingface.co/balllvin/LFM3-1.7B-NVFP4-MLX

## Also noted
- Homelab PCIe watch: r/LocalLLaMA analysis of Intel's LGA 1954 (Nova Lake) platform spec suggests Z990/W980 boards could wire three PCIe 5.0 x8 slots to the CPU — the first consumer 3-way Gen5 x8/x8/x8 topology since the MSI X670E era, plus full AVX-512 back and LP e-cores; discussion-level, unconfirmed (15:21 UTC post). https://www.reddit.com/r/LocalLLaMA/comments/1xwxbjk/spotted_a_very_interesting_detail_about_intels/
- NVIDIA's RTX 4090 Ti 18,176-CUDA-core Ada engineering sample resurfaced in China (VideoCardz 15:49 UTC) — collector leak, no AI angle.

## Just missed the window
- "No Second Run: Does RSI Stop at Frontier Pre-training?" + "801-30B-A3B-Base Coming Soon" — r/LocalLLaMA post at 06:21 EST today, ~84 minutes before the window opened. (footnoted once, never again)

## Coverage notes
- **Verified items this edition: 9** (4 leads, 2 model/spin entries, 1 hardware reproduction counted in leads, plus 2 also-noted) — counting unit: underlying events, one entry each.
- **Ledger dedupe: 5 candidates suppressed** as already reported (Agnes-2.5-Pro-Theta re-post, Strata regression re-post, Lazarus trojan re-surface, Stanford NCM re-post, PS4 9B/VideoCardz re-coverage) — all matched prior `reported.md` lines, including one repeat of previously footnoted items.
- A further 7 never-reported candidates were dropped as out-of-window on event-date grounds: LongCat-Video-Avatar-1.6 (Nov 13), Vimoo 1.0 (Hub repos Sept 30), X3Guard-150M (Nov 13), Haru-4B (Nov 12), ASRock VBIOS 1.0.6 recovery (Nov 10), AMD GFX13 Linux patches (Nov 9–10), kate.land CMP unlock original (Nov 10; only today's reproduction is listed).
- Dead ends: The Register /server/ and TechPowerUp /recent/ fetches failed (one strike each, not retried); VideoCardz RSS feed failed — the site's news index was used instead; DuckDuckGo backend errored twice (queries switched, not reworded); r/LocalLLaMA search.rss 403'd — fell back to the new-listing feed with timestamp gating per procedure. Reddit anonymous feed returns no scores. Sunday = vendor newsrooms quiet; no hardware launches surfaced in-window.
- All GitHub merge dates come from the merged-PR search (exact `merged:` range); all Hub timestamps from the models API `createdAt`.
