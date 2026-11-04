# AI Hardware & Local Models Brief — RustingStrata doubles Flash-Next speed on 12GB; llama.cpp v0.9.0 ships GA

**Window: 2026-11-04 07:45 → 12:45 EST** (previous fire → this fire; new items only, ledger-deduped)

## TL;DR

- **RustingStrata**, a pure-Rust (no ggml) inference engine for Qwen GGUFs, posted a benchmark showing Qwen3.8-Flash-Next IQ2_XS at ~36 tok/s decode on a 12GB RTX 3060 — roughly double the original Strata — and Qwen3.6-35B-A3B at 118 tok/s vs llama.cpp's 68.
- **llama.cpp v0.9.0 went GA** at 08:08 EST with OCP FP8 E4M3 quantization, the flash_attn_ext_rows unified-KV op, and day-0 architecture support for Clover, Limite, Metis-1.6 and Mistral Large 4.
- A clean **2x RTX 5090 vs 2x RTX PRO 6000 vs 2x PRO 6000 Max-Q** showdown started on r/LocalLLaMA — same GB202 silicon, 32GB vs 96GB, 575/600/300 W — on a common Threadripper PRO host.
- **mradermacher's morning quant wave**: day-0 i1 GGUFs of Qwen3.5-4B-Salieri (coding/agent DPO), Qwen3.5-4B-DeepThought-STEM (multi-teacher reasoning distill) and a Saudi-dialect Gemma-4-E4B-Arabic merge.
- Just outside the window: **SambaNova × Supermicro's 10,000-SN50 RDU inference-rack deal** (announced Nov 3) and **Kimi-K3.1-Pro weights already sitting on Hugging Face since Nov 2** per the Hub's own timestamps.

## Inference software and benchmarks

**Rust inference engine RustingStrata runs Qwen3.8-Flash-Next twice as fast as Strata on a 12GB card** — event date Nov 4 (12:29:24 EST post, Showcase flair).
A community developer released RustingStrata, an inference engine for Qwen GGUF models written in pure Rust with no ggml dependency; CUDA kernels are compiled at startup via NVRTC and the same kernels run on Vulkan. On a 12GB RTX 3060 + Ryzen 7600X with 32GB RAM, Qwen3.8-Flash-Next IQ2_XS decodes at ~36 tok/s (CUDA) and prefill averages ~370 tok/s (up to ~610), versus 19-22 tok/s decode and 140-146 tok/s prefill for the reference engine, niko1221's Strata — with greedy output matching Strata's argmax at every checked position. Against llama.cpp it reports 118 vs 68 tok/s decode on Qwen3.6-35B-A3B Q2_K_XL (11.7 GiB, expert-offloaded), ~92 vs 44 tok/s on Qwen3-Next-80B IQ1_S with most experts on CPU, and ~209 tok/s on an AMD RX 7900 XT via Vulkan. It ships an OpenAI-compatible server with tool calling and a self-tuning calibrate command; Vulkan requires shaderFloat64 + subgroup size 32, ruling out Intel Arc and older AMD.
**Why it matters:** Flash-Next's MoE-plus-DeltaNet architecture has been a moat for specialist engines; a from-scratch Rust rewrite beating both the specialist (Strata) and the generalist (llama.cpp) on the same 12GB hardware resets the baseline for sub-16GB MoE serving and shows NVRTC-compiled kernels as a viable alternative to ggml's prebuilt kernels.
**Local inference:** targets exactly the 12-16GB VRAM class (RTX 3060 12GB / 5060 Ti 16GB territory) with CPU-side expert residency; AMD works via Vulkan (shaderFloat64 + subgroup 32 required), Intel Arc does not.
Source: r/LocalLLaMA (Serious_Scarcity7818), Nov 4 2026, 12:29 EST — https://www.reddit.com/r/LocalLLaMA/comments/1xnu9iv/rust_inference_engine_for_qwen_models_on_a_12_gb/

**llama.cpp v0.9.0 tagged GA — first major release of the 0.9 line** — event date Nov 4 (GitHub release published 13:08:55 UTC = 08:08 EST). *Update on the previously reported b12096–b12109 commit wave (this morning's edition); the GA tag and release notes are the new development.*
The release formalizes OCP FP8 E4M3 tensor quantization (new ftype, saturating mode), the `flash_attn_ext_rows` op that restores non-unified-KV prefill performance for multi-sequence inference, a batched tensor-copy API, and day-0 architecture support for Clover-35B-A3B-Preview (AR mode), Limite 1B "Violetto", Metis-1.6 (hybrid Mamba-2/GQA with LatentMoE) and Mistral Large 4. ggml moves to v0.29.0, adding initial NCCL-based multi-node tensor parallelism, SVE CPU kernels, SYCL DPAS flash attention on quantized KV, fused MoE MMQ in CUDA, and Hexagon ops including the lightning indexer. Audio expands with Chatterbox TTS, Nemotron 3.5 Transcribe (40-language speaker-attributed ASR) and predefined Pocket TTS voices; Gemma 4 E2B/E4B get major prefill optimizations.
**Why it matters:** v0.9.0 lands the two things homelab operators have been waiting on — a standards-based FP8 format and multi-node TP in mainline — plus native Mistral Large 4 support, which makes the ~1T MoE loadable in the llama.cpp ecosystem before quants fully mature.
**Local inference:** FP8 E4M3 + flash_attn_ext_rows benefit 16-24GB cards running quantized KV; multi-node TP opens two-box clusters (e.g. 2x5060 Ti rigs over NCCL) without leaving llama.cpp.
Source: ggml-org GitHub release, Nov 4 2026, 08:08 EST — https://github.com/ggml-org/llama.cpp/releases/tag/v0.9.0

## AI hardware and silicon

**2x RTX 5090 vs 2x RTX PRO 6000 vs 2x PRO 6000 Max-Q: a controlled GB202 inference showdown begins** — event date Nov 4 (12:22:39 EST post).
A r/LocalLLaMA builder with all three dual-slot cards launched a same-machine comparison: identical Threadripper PRO 9975WX host, WRX90E-SAGE SE board, 128GB ECC, one 1700W PSU, cards swapped pairwise. Spec spread: 5090 (21,760 cores, 32GB, 575W) vs PRO 6000 (24,064 cores, 96GB ECC GDDR7, 600W, 2.62GHz boost) vs PRO 6000 Max-Q (same 96GB, 300W, 2.28GHz), all at the same 1.79 TB/s. First protocol: Qwen3.8-27B FP8 and NVFP4 at tensor-parallel 2, baseline plus MTP and DFlash2, with power draw logged; the 5090s run both stock and with the P2P driver unlock. One early datapoint from the author: the 600W PRO 6000 needs roughly 425W to match the Max-Q's performance, and the Max-Q (blower) stays cool.
**Why it matters:** the 3TB-class RTX PRO 6000 is becoming the aspirational single-node local card, but this is the first apples-to-apples test of whether the 600W variant earns a 2x power premium over the 300W Max-Q — a direct price/perf and thermal guide for anyone speccing a dual-Blackwell inference box.
**Local inference:** directly informs 64-192GB two-card workstation builds (Qwen3.8-27B FP8/NVFP4 TP2, DGX Spark alternative pricing); power and slot-cooling are the differentiators, not bandwidth.
Source: r/LocalLLaMA (Street-Buyer-2428), Nov 4 2026, 12:22 EST — https://www.reddit.com/r/LocalLLaMA/comments/1xnu36y/2x_rtx_5090_vs_2x_rtx_pro_6000_vs_2x_rtx_pro_6000/

## Community spins and quantizations

**mradermacher ships day-0 i1 imatrix GGUFs of Qwen3.5-4B-Salieri, the Open-SWE-traces coding/agent DPO model** — event date Nov 4 (11:03:19 EST upload).
Full imatrix quant ladder (i1 variants) of Jitheswar/Qwen3.5-4B-Salieri, a Qwen3.5-4B fine-tune aimed at coding agents: SWE/tool-calling DPO on nvidia/Open-SWE-Traces, multilingual (en/zh), Apache-2.0, with Unsloth-derived dynamic quant tags. A companion plain GGUF (no imatrix) also went up.
**Why it matters:** 4B agent-capable models are the sweet spot for cheap parallel tool-calling sidecars, and Salieri's NVIDIA-trace DPO recipe is one of the few open SWE-trace fine-tunes with quants the same morning it surfaced.
**Local inference:** Qwen3.5-4B class — Q8_0 sits near 4GB, IQ1/IQ2 tiers well under 2GB; runs on any 4-8GB GPU or CPU.
Source: Hugging Face, Nov 4 2026, 11:03 EST — https://huggingface.co/mradermacher/Qwen3.5-4B-Salieri-i1-GGUF

**Qwen3.5-4B-DeepThought-STEM GGUFs: multi-teacher reasoning distill with a drug-discovery twist** — event date Nov 4 (09:40:40 EST upload).
Quants of xtools-at/Qwen3.5-4B-DeepThought-STEM, a LoRA-derived reasoning fine-tune trained on a stacked dataset list spanning Claude-Haiku-5.5 high-reasoning trace inversion, Xiaomi-MiMo-2.6-Pro, ChatGPT-6.1-Terra, GPT-5.6-Luna and GLM-5.1 reasoning traces, plus synthetic pharmaceutical-formulation "red pill" reasoning sets for STEM rigor; heretic/uncensored variant, Apache-2.0, unsloth-quantized.
**Why it matters:** the recipe is a snapshot of which closed-model reasoning traces the community is distilling this month, and the math/science (drug-discovery formulation) focus is a niche most 4B reasoning spins skip.
**Local inference:** 4B dense — fits any 6GB+ card at Q4_K_M, near-instant on 8-16GB GPUs.
Source: Hugging Face, Nov 4 2026, 09:40 EST — https://huggingface.co/mradermacher/Qwen3.5-4B-DeepThought-STEM-GGUF

**Gemma-4-E4B-Arabic-It i1 quants: Saudi-dialect Arabic reasoning merge goes GGUF** — event date Nov 4 (12:09:25 EST upload).
i1 imatrix GGUFs of noman-asif/Gemma-4-E4B-Arabic-It, a dense Gemma-4 E4B tuned with RSLORA + trust-region DPO and ties-merging on Arabic-Reasoning-Instruct-120k and Arabic-Preference-DPO-20k (Saudi dialect focus, ar/en), Apache-2.0.
**Why it matters:** regional-dialect Arabic reasoning models remain badly underserved in GGUF form; day-0 quant coverage is what makes them usable on local hardware.
**Local inference:** Gemma-4 E4B effective-params class — ~3-5GB at imatrix Q4/Q3 tiers; comfortable on 8GB cards and Apple Silicon.
Source: Hugging Face, Nov 4 2026, 12:09 EST — https://huggingface.co/mradermacher/Gemma-4-E4B-Arabic-It-i1-GGUF

## Also noted

- **mradermacher merge-quants, morning batch**: Mero-Artemis-31B-v1-heretic-2 (mergekit heretic/abliterated roleplay merge, Apache-2.0, 08:32 EST — https://huggingface.co/mradermacher/Mero-Artemis-31B-v1-heretic-2-GGUF) and Lunar-Trace-31B / -i1 (Cyclone-Labs roleplay/storytelling merge, 10:16 & 11:28 EST — https://huggingface.co/mradermacher/Lunar-Trace-31B-i1-GGUF).
- **VIGOR-mini-i1** — i1 quants of prithivMLmods' 4B-class multimodal visual-safety classifier (moderation guardrail, JSON output, MTP), the cheap local alternative to API moderation, 11:47 EST — https://huggingface.co/mradermacher/VIGOR-mini-i1-GGUF
- **LFM3-Omni-2B runs fully offline on an iPhone 18 Pro Max at ~80 tok/s** using custom WebGPU kernels inside a React Native app — on-device omni inference without llama.cpp, 12:08 EST — https://www.reddit.com/r/LocalLLaMA/comments/1xntou1/lfm3omni2b_running_fully_offline_on_my_iphone_18/
- **"I removed 85% of Gemma's weights (and it is mostly fine)"** — pruning Gemma-4-26B-A4B MoE to 15% of original size retains 92% label agreement with the full model on a narrow classification task (about the same agreement as the 12B has with the 26B), 08:53 EST — https://www.reddit.com/r/LocalLLaMA/comments/1xnoqyy/i_removed_85_of_gemmas_weights_and_it_is_mostly/
- **MOSS-Transcribe-Diarize 0.9B passes a long-gap speaker-continuity test**: 23:39 of a public commission meeting diarized locally on an M5 Ultra Mac Studio in 229s, 309 segments, all six speakers re-identified under the same labels across a 17-19 minute gap (follow-up to the Oct 22 model report), 12:26 EST — https://www.reddit.com/r/LocalLLaMA/comments/1xnu74b/testing_whether_speaker_labels_survive_a_17minute/
- **Paper — "Think Less, Locally"**: local preference learning for reasoning compression, trimming chain-of-thought that continues past sufficient information to cut local inference cost, 10:38 EST — https://www.reddit.com/r/LocalLLaMA/comments/1xnrcfw/paper_think_less_locally_local_preference/

## Just missed the window

- **SambaNova × Supermicro strategic collaboration — 10,000 SN50 RDU chips** for disaggregated inference racks (GPUs do prefill, RDUs do decode; Supermicro builds/sells/supports; NeoClouds/sovereign clouds first). SambaNova's own press release is dated **Nov 3** (San Jose); only the syndicated HPCwire copy carries a Nov 4 stamp and an HN post landed at 12:15 EST — event date governs, so it footnotes here (first and only time). https://sambanova.ai/press/sambanova-and-supermicro-partnership — https://www.hpcwire.com/off-the-wire/sambanova-and-supermicro-to-build-ai-inference-systems-combining-rdus-and-gpus/
- **Kimi-K3.1-Pro weights are already downloadable** — the moonshotai/Kimi-K3.1-Pro Hub repo (2.8T-class, 1M ctx, image-text-to-text, 8-bit compressed-tensors) carries Hugging Face createdAt **and** lastModified of Nov 2, 04:13:51 UTC, i.e. the drop landed early relative to Moonshot's "by November 4" promise (announced Oct 23, already in the ledger); the community "Kimi-K3.1-Pro is now open-weights" wave peaked this morning (10:44 EST post). Out-of-window by the Hub's own timestamps — footnote only; no major quantizer has shipped GGUFs yet, so expect that to be the next story. https://huggingface.co/moonshotai/Kimi-K3.1-Pro
- **oMLX 0.7.4.dev1** (Apple Silicon inference server with SSD KV cache) published 05:13 EST — just pre-window — including a fast-Mac-as-prefill-accelerator mode, LFM3/d1 decision-model support, and Bonsai 1/2-bit prefill at +262% (362 tok/s). https://github.com/jundot/omlx/releases

## Coverage notes

- **Verified: 13 qualifying events** (6 lead items + 7 "also noted" one-liners; counting unit = distinct in-window events, one per underlying development; the llama.cpp v0.9.0 entry is an update on a reported item, not a new one). Three "just missed the window" footnotes added, each tagged once in the ledger.
- **Ledger dedupe:** 9+ candidates suppressed as already reported — TinyWebUI, Maya 4x3090 bench, 3090 weight-editing replication and Tencent Hy4 re-coverage (all reported by this morning's 07:45 run), Sipeed MaixStation/Axera AX8860P (a ~2-week-old Tom's Hardware story recirculating on Reddit — excluded, not "just missed"), MOSS AMA highlights (Oct 30 event), Kimi-K3.1-Pro API-placement question (Oct 23 ledger), bartowski's Nov 3 uploads (out of window), Strata opinion post.
- **Dead ends / limitations:** VideoCardz's feed blocked the fetch (Cloudflare wall) — one strike, abandoned per two-strike rule; reddit `.rss` returned 403 through the page extractor (worked around via the reddit-reading skill script + arctic-shift timestamp archive); ServeTheHome, The Register AI/ML and the vLLM/SGLang/Ollama/TensorRT-LLM release feeds carried nothing new in the 07:45→12:45 window; web_search indexes were stale for a 5-hour window, so discovery leaned on river/index sources (HN Algolia window query, GitHub release atoms, HF lastModified APIs, r/LocalLLaMA new-listing).
- Kimi-K3.1-Pro placement relies on the Hub's machine timestamps (Nov 2 04:13:51 UTC) over the community's "today" framing; if Moonshot gated→un gated the repo without bumping lastModified, the placement could shift — flagged honestly rather than stretched across the window boundary.
