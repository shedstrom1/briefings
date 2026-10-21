# AI Hardware & Local Models Brief — Oct 21, 2026 · 12:45 ET

**Window: 2026-10-21 07:45 → 12:45 EDT** (previous fire → now; new items only, ledger-deduped).

## TL;DR

- **Recursive's Flint lands**: a 39.8B-A2.5B Apache-2.0 coding-agent MoE (89.7% SWE-bench Pro v2, 74.2% SWE-bench Verified) published to Hugging Face this morning with BF16 + INT4/NVFP4 GGUF + MLX INT4 variants — a full day-0 local quant set from a commercial lab.
- **Basalt**, a community inference engine in development, runs DeepSeek V4.1 Flash 1017 on a single RTX 5090 (plus an optional 5060 Ti): 53.6→63.5 tok/s decode vs llama.cpp's ~20 tok/s baseline on the same hardware, with 107 GiB of experts in system RAM.
- **Strata v0.2.1 hits 150 tok/s decode** with Qwen3.8-Flash-Next IQ3_XXS on a single RTX 5080 16 GB — first independent 5080 numbers for the new engine (reported at v0.2.2 yesterday).
- **Cosmos3-Edge shrinks to <8 GB** via community INT4: 152.5 tok/s VLM decode on a Jetson Orin Nano Super 8 GB at ~3.3 GB peak memory.
- **NVIDIA GTC Berlin keynote (DSX POD, DGX Spark 8-unit clusters) landed pre-window** — footnoted below, not counted as items.

---

## Model releases

### Recursive releases Flint — 39.8B-A2.5B Apache-2.0 coding-agent MoE with day-0 quants
**Event date: 2026-10-21 (morning ET).** Recursive (recursive.com) published Flint to Hugging Face at 10:21 AM ET: a decoder-only MoE with 39.8B total parameters, ~2.5B active per token (49 layers: 2 dense + 47 MoE; 256 routed experts, 6 per token + 1 shared; 35 sliding-window + 14 full-attention layers; 262K token context). It targets coding agents — codebase investigation, bug fixes, features, tests — and the release card reports 54.9% on Terminal-Bench 2.1 (web-retrieval solutions counted as failures), 89.7% on SWE-bench Pro v2 and 74.2% on SWE-bench Verified, measured by Recursive at temp 1.0. The BF16 checkpoint (~80 GB) serves on one B300 or H200 via a bundled SGLang kit, and three quantized repos went up within the half hour before it (9:51 AM ET): flint-int4-gguf, flint-nvfp4-gguf (NVIDIA RTX class) and flint-int4 for Apple Silicon MLX. Knowledge cutoff June 2026; Apache-2.0 weights, hosted API terms separate.

**Why it matters:** A commercial lab shipping a coding-agent MoE under Apache 2.0 with quants ready at launch is the strongest local-coding signal this week — the active-parameter count (~2.5B) means decode is compute-cheap and the routed-expert design is exactly what current GGUF/EXL3 offload stacks are getting good at.

**Local inference:** BF16 needs a B300/H200-class 80GB card per replica; the NVFP4/INT4 GGUF tiers put this 39.8B-A2.5B MoE in the 24 GB single-card class (RTX 4090/5090 territory) with expert weights small enough for RAM offload; MLX INT4 covers high-RAM Apple Silicon.

Sources:
- RecursiveSI/flint — Hugging Face model card, repo created 2026-10-21 14:20 UTC — https://huggingface.co/RecursiveSI/flint
- Quant companions: https://huggingface.co/RecursiveSI/flint-int4-gguf · https://huggingface.co/RecursiveSI/flint-nvfp4-gguf · https://huggingface.co/RecursiveSI/flint-int4 (all created 2026-10-21 13:51 UTC)
- Community pickup (11:38 AM ET): https://www.reddit.com/r/LocalLLaMA/comments/1xbqik0/recursivesiflint_40b_a25b_hugging_face/

## Community spins and quantizations

### Community INT4 build squeezes NVIDIA Cosmos3-Edge under 8 GB — 152.5 tok/s on a Jetson Orin Nano Super
**Event date: 2026-10-21 (10:59 AM ET).** A community optimizer (u/No-Dragonfly6246, who previously shipped a Cosmos Reason 2 build) published an INT4-quantized Cosmos3-Edge VLM that fits NVIDIA's $500-class Jetson Orin Nano Super 8 GB — officially unsupported because the BF16 stack exceeds 8 GB. The optimized build decodes at 152.5 tok/s on the Orin Nano Super (vs 44.1 tok/s BF16 vLLM on AGX Orin 64 GB; 207.8 tok/s on AGX Thor 128 GB), holds accuracy across five VLM benchmarks (65.56 → 65.20 average) and peaks at just ~3.3 GB memory. Only the VLM component is out; the author says video-generation and robot-policy parts are being added soon, with pre-compiled builds served via embedl.com because "Hugging Face doesn't really support" that format.

**Why it matters:** Sub-4 GB peak memory puts a capable edge VLM on every Orin Nano/Jetson-class board, and the same quant path previewed for the diffusion/policy heads means robot-policy inference is coming to 8 GB SBCs — a bigger deal for homelab robotics than any datacenter benchmark.

**Local inference:** Targets Jetson Orin Nano Super 8 GB (152.5 tok/s measured); also measured 207.8 tok/s on AGX Thor 128 GB. Any Orin Nano-class SBC qualifies.

Sources:
- Reddit post, 2026-10-21 14:59 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xbph8w/cosmos3edge_in_under_8_gb_vlm_out_now_video_gen/
- Build distribution: https://embedl.com/nvidia/cosmos3-edge/orin-nano-super

## Inference software and benchmarks

### Basalt runs DeepSeek V4.1 Flash 1017 on a single RTX 5090 — 53.6→63.5 tok/s with experts in RAM
**Event date: 2026-10-21 (11:38 AM ET).** Developer u/jesdga95 shared results from Basalt, an inference engine under development: DeepSeek V4.1 Flash 1017 (Unsloth UD-IQ1_S quant) served from a single 243 GB file (weights + vision + drafter) with the 107 GiB expert pool in system RAM and the RTX 5090 32 GB (400 W) as an expert cache. Baseline llama.cpp with --fit decoded ~20 tok/s on the same rig (128 GB DDR5); Basalt hit 53.6 tok/s on the 5090 alone and 63.5 tok/s once a second RTX 5060 Ti 16 GB (180 W) was added to compute its hottest experts (expert lookups: 66% on the 5090, 16% on the 5060 Ti, 5% over PCIe, 12% CPU). Prefill was ~610 tok/s warm; tool calls, images and up to 1M context work (a 1M-token prompt takes ~26 minutes). The author's own limits: the 12% CPU-resident lookups bottleneck on dual-channel DDR5, and speculation only gave 1.1-1.2x. Commenters with EPYC/RTX Pro 6000 rigs reported 100-130 tok/s using the CPU for cold experts.

**Why it matters:** This is the community's most convincing demonstration yet that 400B-class MoE flagships are usable on sub-$3k desktops if the engine treats the GPU as an expert cache rather than a model host — and it validates adding a second cheap GPU (5060 Ti) as a dedicated expert-compute device.

**Local inference:** Measured on RTX 5090 32 GB + RTX 5060 Ti 16 GB + 128 GB DDR5; the 16 GB 5060 Ti alone can't host the model but measurably raises decode when given expert compute. IQ1_S-class quants of this model need ~128 GB RAM, not VRAM.

Sources:
- Reddit post, 2026-10-21 15:38 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xbqir0/ran_deepseek_v41_flash_1017_on_a_single_rtx_5090/
- llama.cpp baseline referenced: llama.cpp PR #30817 (--fit)

### Strata v0.2.1 on a single RTX 5080: 150 tok/s decode, 4.4K tok/s prefill (Qwen3.8-Flash-Next IQ3_XXS)
**Event date: 2026-10-21 (12:37 PM ET).** First independent RTX 5080 numbers for Strata (the RAM-offload engine that hit v0.2.2 yesterday): on a 16 GB RTX 5080 + Core Ultra 9 285K + 64 GB DDR5-6400 Fedora 44 box, Qwen3.8-Flash-Next IQ3_XXS at 64K context decoded at a median 150.0 tok/s (range 126.7-170.2, n=9) with fresh-prompt prefill of 3,755 tok/s at 4K / 4,396 tok/s at 16K (TTFT 3.67 s) / 4,412 tok/s at 32K. Agent-session runs showed 69.5% cache hit rate and 0.97 s turn-TTFT. Caveats are disclosed: the 5080 was undervolted/OC'd (memory +6000 offset, 2850 MHz @ 900 mV, 360 W cap) and the engine was built from source at v0.2.1.

**Why it matters:** 150 tok/s at IQ3_XXS on a 16 GB card — the same VRAM class as the user's 5060 Ti — is the strongest data point yet that Strata's hybrid RAM/GPU scheduling reshapes price/perf for daily-driver local coding models.

**Local inference:** RTX 5080 16 GB class (and by extension 5070 Ti/5060 Ti 16 GB owners with more DDR5); 64 GB system RAM is the offload pool; PCIe 5.0 + NVMe-resident n-gram table.

Source:
- Reddit post, 2026-10-21 16:37 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xbs4xj/benchmark_strata_rtx_5080_16_gb_core_ultra_9_285k/

### llama.cpp ships builds b11793–b11798: OpenCL IM2COL_3D, faster vocabulary load, Anthropic-route sampling params
**Event date: 2026-10-21, 07:55–12:36 AM/PM ET (six builds).** The six builds released since the last brief land several merges that matter: ggml-opencl gained IM2COL_3D support (#30895, merged 10:59 AM ET) — the convolution-im2col path Qualcomm/Adreno and Intel GPU offload backends lean on; ggml got an optimized vocabulary-loading path (#30679), cutting startup time on big-vocab models; the server now forwards llama.cpp sampling parameters on the /v1/messages (Anthropic-compatible) route (#30856); mtmd gained a state object for multimodal generation (#30902); and jinja templates now support hexadecimal string escapes (#30929). Boring-by-name, but the OpenCL IM2COL_3D + vocab-loading pair is exactly the class of change that moves tokens/sec on Hexagon-powered laptops.

**Why it matters:** llama.cpp's non-CUDA backends (OpenCL on Snapdragon/Adreno, Hexagon) are where the local-AI frontier is broadening; IM2COL_3D removes a kernel-fallback penalty for vision-MTP and conv-heavy layers on those devices.

**Local inference:** Directly benefits Snapdragon X (Hexagon NPU) laptops and GPU-adjacent OpenCL devices; vocab-loading speedup applies to every quant start-up; /v1/messages param forwarding helps agent harnesses on 24 GB-class single cards.

Sources:
- Releases (b11793 11:55 UTC … b11798 16:36 UTC): https://github.com/ggml-org/llama.cpp/releases

## Also noted

- **llampart v2.0.1** (11:46 AM ET) — a local web UI unifying llama-server, vLLM and OpenAI-compatible APIs: https://www.reddit.com/r/LocalLLaMA/comments/1xbnm5c/llampart_a_local_web_ui_for_llamaserver_vllm_and/
- **DeepInfer interactive explainer** (10:16 AM ET) — step-by-step annotated walk-through of DeepSeek's open-source inference engine internals (follow-on tool to the framework reported Oct 20): https://www.reddit.com/r/LocalLLaMA/comments/1xbod5o/i_built_an_interactive_stepbystep_code_explainer/
- **On-device voice front-end** (8:59 AM ET) — open-sourced wake-word/voice-lock frontend for local assistants (any typed wake word, keeps only your voice): https://www.reddit.com/r/LocalLLaMA/comments/1xbmhct/i_built_a_small_ondevice_voice_frontend_for_local/

## Just missed the window

- **NVIDIA GTC Berlin keynote (announced ~05:00–07:00 EDT, 45-105 min pre-window):** DSX POD reference design — seven Vera Rubin NVL72 racks in one intermodal container, ~25 EFLOPs inference per POD, partner-built (Gigabyte/HPE/Dell/Delta/QCT) from early 2027, plus supply-chain targets (15x→30x co-packaged-optics capacity by 2028) — and DGX Spark gains 8-unit clustering via the updated NVIDIA Sync Cluster Assistant, with DGX Station Windows arriving in December. Sources: ServeTheHome (Oct 21) https://www.servethehome.com/nvidia-gtc-berlin-2026-nvidia-intros-dsx-pods-and-more/ ; NVIDIA live blog (keynote 11:00 CEST) https://blogs.nvidia.com/gtc-berlin-2026-news/ ; The Register https://www.theregister.com/software/ai_ml/
- **NVIDIA open-model wave at GTC Berlin (6:00 AM EDT, pre-window):** Nemotron 3.5 Super VL + Bolt, Nemotron Retriever (embedding/rerank/doc-parse), the Kumo open-weight family for structured business data, and Cosmos 3.5 world models — disclosed in the 3:00 AM PDT blog slot. Source: https://blogs.nvidia.com/gtc-berlin-2026-news/

## Coverage notes

**Verified in-window items: 8** (5 full items above + 3 "Also noted"), plus 2 single-use "Just missed" footnotes (both newly tagged in the ledger).

**Ledger dedupe:** 1 candidate suppressed as already reported — STH's in-window "NVIDIA BlueField-4 STX Server for CMX at OCP 2026" post (event = OCP disclosure, already footnoted `missed:` on 2026-10-20; per rules never re-listed).

**Rejected as out-of-window after verification** (event-date anchoring, not in ledger): OpenBMB's "35B MoE at 20.5 tok/s on iPhone" LES write-up (GitHub doc last updated 2026-10-19); IBM granite-speech-5.0-2b repo (created Oct 7); inclusionAI Ming-3.0-TTS-0.5B (created Sep 22) and Ling-3.0-flash-UBTree (created Oct 14) — both "found it today" Reddit posts; Taalas ChatJimmy 17k tok/s chip repost (AMD acquisition already months old).

**Dead ends (two-strike rule):** guessed `IBM/Granite-Speech-5.0-2B` HF path returned 401 (real repo is `ibm-granite/granite-speech-5.0-2b`, Oct 7 — rejected on date); `jllllll/LM-Studio` releases API 404 (wrong org); VideoCardz Game Ready 621.01 posted but not cited — no verified AI capability change; the "China doubling RAM production" Reddit link-share was not fetched to a dated primary source and is held out rather than padded in.

**Indexes healthy:** ServeTheHome, The Register AI, VideoCardz, HF trending API, r/LocalLLaMA new feed (all 25 newest posts in-window), GitHub releases for llama.cpp/Ollama/vLLM/SGLang. No new MLPerf round this window (v6.1 was Sep 16).
