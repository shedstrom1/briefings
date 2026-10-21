# AI Hardware & Local Models Brief — Oct 21, 2026 (17:45 ET edition)

**Window:** 2026-10-21 12:45 → 17:45 EDT (previous fire 12:45, this fire 17:45 America/New_York)

**TL;DR**
- Community verification landed on a Linux kernel-module patch (`gpu-unlock`) that lifts NVIDIA's half-rate BF16 tensor limit on GeForce — measured RTX 3090 BF16 jumps of ~70–80% (e.g. 76→129 TFLOPS), turning the homelab favorite 30-series cards into substantially faster prompt-processing boxes.
- Meta-independent day for local voice: Izwi 0.1.0 shipped — first stable of a fully local ASR→LLM→TTS voice runtime with OpenAI-compatible routes (14:14 EDT).
- Flint (Recursive's 39.8B-A2.5B Apache-2.0 coding MoE) gained native llama.cpp support — merged 17:26 EDT, one release after b11800.
- Mica v0.5-4B, a calibration-first "decision model" (one prefill per judgment, no text generation), uploaded today — 19.8 ms per decision on a single RTX 6000 Ada.
- Ollama shipped day-0 Flint support (v0.40.8) at 12:20 EDT — 25 minutes before the window (see footnote).

---

## AI hardware and silicon

**GPU patch unlocks NVIDIA's half-rate BF16 tensor limit on GeForce — 3090 owners measure up to ~80% compute gains** — event: community verification thread posted mid-afternoon in-window (module repo public since Oct 18, actively patched today through 14:46 EDT)
A Linux kernel module, `gpu-unlock` (Zagitta/gpu-unlock), runs the CMP-unlocker V67/PLM technique with pre-driver UEFI timing, writes the GPU speed selectors (SS0 = 0x88888888 selects the full BF16 tensor rate; the 4-bit SS1 write is marked untested by the repo) and performs a function-level reset before the NVIDIA driver loads — removing the half-rate limit NVIDIA applies to BF16/TF32 tensor math with FP32 accumulate on GeForce. The r/LocalLLaMA thread posted this afternoon carried independent measurements: the OP saw prompt-processing on Qwen3.8-27B improve 22–30% on a 230 W-limited RTX 3090; one user replicated the repo's issue-page deltas (BF16 87.50→157.41 TFLOPS, TF32 42.58→82.61 TFLOPS) and measured 75.98→129.28 BF16 TFLOPS on his own 250 W 3090; another confirmed a 3080 Ti (315 W) going 71.59→113.98 TFLOPS in cuBLAS BF16. Skeptics in-thread pointed out that decode is memory-bandwidth-bound, so end-to-end llama.cpp generation gains were 1–5% (one vLLM W8A16 dual-3090 test saw ~10–15% prefill, ~5% decode), and called the current patch fragile (systemd-boot and Supermicro UEFI init breakage). The repo has accrued 132 stars/27 forks with commits landing today.
**Why it matters:** the RTX 3090 remains the default second-hand inference GPU for homelab multi-GPU rigs; a free, reversible Linux-side unlock of full-rate BF16/TF32 directly cheapens prefill and training-adjacent workloads for the largest installed base of local-inference cards — at the cost of warranty risk and driver-load ordering care (it must attach before the NVIDIA driver).
**Local inference:** targets GeForce RTX 30/40-series on Linux (kernel 6.16+ referenced by setup notes); biggest wins for compute-bound work (prompt processing, FP/BF16 fine-tuning) on 24 GB 3090 rigs; power-limited cards benefit most since the unlock raises the compute ceiling, not bandwidth.
Source: RTX 3090 speedup hack — verified, r/LocalLLaMA (posted Oct 21, in-window): https://www.reddit.com/r/LocalLLaMA/comments/1xbuzj5/amazing_free_3090_speedup_hack_verified_works_15/
Source: Zagitta/gpu-unlock (GitHub repo, created Oct 18 01:28 UTC, last push today 18:46 UTC): https://github.com/Zagitta/gpu-unlock

---

## Model releases

**Mica v0.5-4B: a 4B "decision model" that answers with calibrated probabilities in one prefill — 19.8 ms per decision** — event: Oct 21, in-window (HF repo updated 16:54 EDT; launch post hit r/LocalLLaMA mid-afternoon)
Mica v0.5 (sky7350/Mica-v0.5-4B, fine-tuned from Qwen3.5-4B, Apache 2.0) is a text-classification/decision model: you pass the evidence, the question, and the allowed answers; it returns a probability per option with no text generation, so a decision costs exactly one prefill. The author reports 19.8 ms per decision at 256 tokens and ~1.2 s at 32 k on a single RTX 6000 Ada — claimed up to 3.8× faster than other 4B judges and far faster than 27B deciders — aimed at log/ticket triage, agent-routing, RAG judging and guardrails. The repo ships safetensors plus GGUF (BF16/Q8_0/IQ4_XS) and int8 folders, with llama.cpp compatibility tagged.
**Why it matters:** cheap calibrated decisions are the glue layer agents keep paying cloud APIs for; a 4B one-prefill judge at ~20 ms moves guardrail/router/judge loops on-device without KV-cache text decoding.
**Local inference:** 4.66B params BF16 (~9.3 GB); IQ4_XS GGUF fits a 4 GB card and runs comfortably on RTX 5060 Ti 16 GB-class or any 8 GB GPU; int8 variant provided.
Source: sky7350/Mica-v0.5-4B, Hugging Face (updated Oct 21 20:54 UTC): https://huggingface.co/sky7350/Mica-v0.5-4B
Source: Mica v0.5 launch post, r/LocalLLaMA (Oct 21, in-window): https://www.reddit.com/r/LocalLLaMA/comments/1xbw3oy/mica_v05_the_smartest_4b_decision_model_is_also/
Source: akivet/Mica (GitHub project page): https://github.com/akivet/Mica

---

## Update on previously reported

**Flint (Recursive 39.8B-A2.5B coding MoE): native llama.cpp support merged 17:26 EDT** (ledger: 2026-10-21 | Recursive/HF | flint)
PR #30928 "models: Add Flint model (FlintForCausalLM)" merged to master at 17:26 EDT today — minutes after the b11800 tag, so it lands in the next build. Combined with the day-0 Unsloth GGUFs already covered at 12:45, Flint (49-layer MoE, ~2.5B active incl. output head, 262K context, Apache 2.0) is now runnable in llama.cpp, vLLM/SGLang kits, and (pre-window, see footnote) Ollama on macOS.
**Why it matters:** llama.cpp support puts the strongest-reported new coding MoE (Terminal-Bench 2.1 54.9, SWE-bench Verified 74.2 by the team's numbers) on CPU-offload and consumer-NVIDIA setups that the CUDA-first kits don't cover.
**Local inference:** ~2.5B active params means GGUF quants from ~5 GB (IQ4_XS-class) upward decode fast on 8–16 GB cards; full expert set offloads on 32 GB+ rigs.
Source: llama.cpp PR #30928 (merged Oct 21 21:26 UTC): https://github.com/ggml-org/llama.cpp/pull/30928

---

## Inference software and benchmarks

**llama.cpp b11799–b11800: fused QSA-mask op for sparse-attention speedups, SYCL q8 fast dequant** — event: merges/tags 13:02–17:15 EDT in-window
Two merges landed in-window: `llama: create new fused op for QSA mask` (#30640) — the community callout was "time to update llama.cpp for another speedup" on sparse-attention models — and `sycl: q8 fast dequant` (#30775) for the SYCL backend. b11800 tagged 17:15 EDT; the Flint support commit (above) followed at 17:26.
**Why it matters:** fused mask ops directly cut per-token overhead for the sparse-attention architectures (QSA-style) that are becoming common in this year's long-context models; the SYCL dequant path narrows the gap on Intel GPUs.
**Local inference:** llama.cpp users on CUDA/Intel-GPU SYCL builds should re-pull; sparse-attention model users see the most benefit.
Source: llama.cpp releases b11799 (17:52 UTC) / b11800 (21:15 UTC): https://github.com/ggml-org/llama.cpp/releases/tag/b11800
Source: PR #30640 (merged Oct 21 17:02 UTC): https://github.com/ggml-org/llama.cpp/pull/30640

**vLLM / SGLang in-window merges: Cohere2MoE dual-stream + fused norm batch; SGLang DeepSeek-V4 prefill/decode perf series** — event: commits merged 13:00–17:46 EDT
vLLM (16:45–21:46 UTC window): `[Perf][Model] Cohere2MoE: attention/MoE dual stream, single TP all-reduce per layer, fused add + RMSNorm` (#62150), `[Bugfix][Core] Release libgomp workers before forking engine processes` (#62884), `[KV Offload] Preserve group classification on placeholder events` (#59703), plus new DeepSeek-V4.1-Flash B200 AgentX-router documentation (#62954). SGLang: a DeepSeek-V4 perf series (#45573–#45585: sparse prefill restore, request-window history rows cached per batch, index-K slot gather, decoder-tail out of the prefill graph under DP attention with MegaMoE), tuned MXFP8 decode tactics (#45630), SM100 FP8 deep_gemm fix (#45585), per-worker SO_REUSEPORT uvicorn tokenizer workers (#45482), and FlashInfer KDA prefill cuda-graph capture (#45473).
**Why it matters:** the SGLang dsv4 series is an accumulation of decode/prefill latency cuts on the currently-popular DeepSeek V4.1-Flash line, and SO_REUSEPORT tokenizer workers attack a real single-GPU-server scaling bottleneck (tokenizer process contention).
**Local inference:** single-GPU and small-TP deployments gain from the tokenizer-worker and fused-norm changes; MXFP8 decode tactics are SM100 (B200/B300-class) rather than consumer, but SGLang's dsv4 kernel fixes benefit consumer runs too.
Source: vLLM commits: https://github.com/vllm-project/vllm/commits/main
Source: SGLang commits: https://github.com/sgl-project/sglang/commits/main

**Izwi 0.1.0: first stable of the local-first voice-AI runtime (ASR → chat → TTS) for Mac, Linux, Windows** — event: GitHub release published 14:14 EDT
Izwi (izwi-ai/izwi, Apache 2.0, from Zonos developer zinyando) shipped v0.1.0 after 18 public betas: a desktop app plus web UI, CLI, and local server for real-time local voice conversation — local ASR, chat model, and TTS with no cloud keys — exposing OpenAI-compatible API routes and standalone gateway/supervisor/worker binaries; CUDA support via a `production-cuda` Docker target, with VibeVoice ASR/TTS pipeline support and CUDA long-audio stability fixes in the release notes.
**Why it matters:** end-to-end local voice stacks are still the hardest part of air-gapped assistants to assemble; a packaged runtime with OpenAI-compatible endpoints (and a gateway that links no inference engine) makes voice front-ends drop-in for llama.cpp/vLLM backends.
**Local inference:** runs models on-device (Metal/CUDA lanes; CPU supervisor first); typical TTS/ASR models of this class fit 4–8 GB VRAM, so a 5060 Ti 16 GB box handles voice plus a small chat model.
Source: Izwi v0.1.0 GitHub release (published Oct 21 18:14 UTC): https://github.com/izwi-ai/izwi/releases/tag/v0.1.0
Source: Izwi 0.1.0 launch post, r/LocalLLaMA (Oct 21, in-window): https://www.reddit.com/r/LocalLLaMA/comments/1xbuxja/izwi_010_fully_local_voice_ai_tts_asr_voice_chat/

**748B DeepSeek-V4.1-Flash at 7.2 tok/s on a €2,000 used rig (single 3090 + 128 GB DDR4)** — event: r/LocalLLaMA post in-window
Running DeepSeek-V4.1-Flash-1017's smallest quant (Unsloth UD-IQ1_S 1-bit) on a used build — RTX 3090 24 GB, 128 GB DDR4, i9-9900X — the poster reports the 748B MoE working at 7.2 tok/s, positioned as the cheapest entry to a near-frontier open MoE.
**Why it matters:** with the gpu-unlock news above, 3090 demand and value climb further; 1-bit quants of giant MoEs on decade-old X-series platforms are turning "748B-class on a €2k PC" from demo to daily-driver-adjacent for patient users.
**Local inference:** 1-bit imatrix quant + CPU expert offload: needs 128 GB system RAM, one 24 GB (or 16 GB with more offload) GPU; usable for chat/code at prose-reading speed, not interactive-fast.
Source: Tried running 748B DeepSeek-V4.1-Flash on a €2k PC, r/LocalLLaMA (Oct 21, in-window): https://www.reddit.com/r/LocalLLaMA/comments/1xburkx/tried_running_748b_deepseekv41flash_on_a_2k_pc/

---

## Community spins and quantizations (also noted)

- **KOLIBRI-1-DERISKED i1-GGUF** — mradermacher quantized Blackfrost-AI's "derisked" (compliance-preamble) fine-tune of Aleph-Alpha's Kolibri-1 long-context MoE (384 experts, 6 active, de/en) in i1-imatrix tiers; upload 13:07 EDT. Base BF16 is ~78 GB, so i1_S/Q tiers are the practical local path on 16–32 GB cards with offload. https://huggingface.co/mradermacher/KOLIBRI-1-DERISKED-BF16-i1-GGUF
- **wesjos/mario-html-v2-9B-GGUF** — bartowski quant (created 16:57 EDT) of the mario-html-v2 9B image-text-to-text model tuned for HTML/UI generation from zero-system-prompt COT data; ~5–6 GB at Q4, 8 GB-class friendly. https://huggingface.co/bartowski/wesjos_mario-html-v2-9B-GGUF
- **Gemma-4-12B-HybridAgent-i1-GGUF** — mradermacher i1 imatrix of the milkshake721 agent-tuned Gemma-4-12B (15:29 EDT); 12B dense, ~7 GB at i1-Q4 class — 5060 Ti 16 GB fits with room for KV. https://huggingface.co/mradermacher/Gemma-4-12B-HybridAgent-i1-GGUF
- **Swift 1.5 Qwen3.8 Flash Next – Heretic – Unsloth Dynamic IQ4_XS** — community quant by jjusko20 of the full-precision Swift 1.5 heretic merge in Unsloth-dynamic IQ4_XS, pre-configured for Strata; fills a gap since existing uncensored editions targeted GSQ-RCO versions. https://www.reddit.com/r/LocalLLaMA/comments/1xbweuw/made_a_quant_swift_15_qwen_38_flash_next_heretic/

---

## Just missed the window

- **Ollama v0.40.8 — day-0 Flint support (`ollama run flint`)** released 12:20:30 EDT, 25 minutes before the window opened; macOS only at launch, GPU platforms "in a subsequent release". https://github.com/ollama/ollama/releases/tag/v0.40.8

---

## Coverage notes

- Verified items this run: 10 (1 lead hardware item, 1 model release, 1 previously-reported update, 5 inference-software/benchmark items — 2 grouped as "also noted" quants carry their own fetched links, 4 community quant notes). Every URL above was fetched this run or returned verbatim by search; lead stories (gpu-unlock thread + repo) were read in full via thread fetch and repo/README reads.
- Ledger dedupe: ~12 candidates suppressed as already reported — yehyakar's Strata RTX 5080 bench and jesdga95's Basalt single-5090 DeepSeek run (both reported 12:45 fire), RecursiveSI/flint HF + Unsloth flint-GGUF (12:45 fire), NVIDIA GTC Berlin morning batch incl. Register's afternoon Nemotron-Retriever re-coverage (already footnoted once at 12:45 — not re-footnoted), Ornith-1.5-122B coding-agent discussion (base + quants previously reported), and the previously-footnoted SGLang/GTC items.
- Non-ledger exclusions: IBM Granite Speech 5.0 2B (HF blog dated Oct 21 by IBM, but no retrievable time-of-day and its r/LocalLLaMA post pre-dates the 12:45 queue — treated as pre-window, not footnoted); CXMT DRAM capacity doubling (announced Oct 19–20); Meta Muse Spark Open (Oct 15); NVIDIA RTX Spark laptop availability (Oct 16); TechPowerUp 621.01 WHQL (game-ready only, no AI angle); VideoCardz R620/Advanced-Shader-Delivery item (gaming driver feature).
- Sweep coverage: ServeTheHome, TechPowerUp, VideoCardz, The Register, NVIDIA newsroom, r/LocalLLaMA (hot+new), HF API (unsloth, mradermacher, bartowski, DavidAU), GitHub releases/commits for llama.cpp/vLLM/SGLang/Ollama. Reddit's anonymous feed omits timestamps; in-window membership was established by post-id ordering against the 12:45 run's known queue — noted as a precision limit. The Register's AI index served a 404 on the legacy URL; the ai-and-ml section path was used instead. No dead-end candidate consumed more than two strikes.
