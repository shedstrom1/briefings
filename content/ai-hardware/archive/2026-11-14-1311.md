# AI Hardware & Local Models Brief — Saturday, November 14, 2026, 12:45 ET edition

**Window:** 2026-11-14 07:45 → 12:45 EST (previous fire → this fire). New items only, deduped against the reported.md ledger.

## TL;DR
- homebrewserver.club published the first independent measurement of llama.cpp v1.0 on an RTX 3090: single-stream is flat, but four-way batch is 14–22% faster and MTP speculative decoding lifts the 27B model 52–55%.
- RustingStrata ran Recursive's Flint 40B (27.9 GiB UD-Q5_K_XL) on an RTX 3060 12 GB + 30 GB RAM rig at 64.6 tok/s decode — about 33% over llama.cpp on the same file.
- Unsloth pushed new sub-4-bit tiers (UD-IQ1_S → UD-IQ3_XXS) into the Inkling-2-Small-GGUF repo this morning.
- A quiet Saturday for weight drops: no new open-weight model releases verified in window; community spins carried the day (tiny-voca 4.5M TTS, basal-omni-2.0-mini decision model).

## Inference software and benchmarks

**llama.cpp v1.0 measured: batch +14–22%, MTP spec-decode +52–55% on an RTX 3090** — Event date: today, November 14 (published "14 November 2026").
homebrewserver.club compared llama.cpp v1.0.0 (build 12359, tagged Friday November 13) against pre-v1 build 12199 on a Fedora 44 Docker box with an RTX 3090 24 GB and Ryzen 9 5900X, using Unsloth Q4_K_M files. One-request decode is unchanged (9B 116.0 vs 114.7 tok/s; 27B 38.7 vs 39.2), but four concurrent requests gained 14% (9B, 316.8 tok/s) and 22% (27B, 116.4 tok/s) thanks to the new CUDA few-token kernel for RTX 30-series and newer, and MTP speculative decoding on 27B jumped from 45.1 to 68.5 tok/s (+52%; up to +55% on short prompts). CPU-only and VRAM footprint were identical. Operational notes: `--defrag-thold` is removed, and `llama-server` now refuses tools/MCP on non-loopback addresses without an `--api-key`. Ollama (v0.40.16 at b12177) still ships none of it.
*Why it matters:* the v1.0 gains are concentrated in exactly the multi-user and spec-decode configurations homelab agents use, and they land on Pascal-era-successor hardware owners already have — free throughput without new silicon.
*Local inference:* RTX 30-series and newer; measured on a 24 GB RTX 3090 with 16.7 GB 27B Q4_K_M; single-user chat sees no change.
Source: homebrewserver.club — "llama.cpp v1.0 on an RTX 3090: up to 55% faster with speculative decoding," November 14, 2026 — https://homebrewserver.club/llama-cpp-v1.html (surfaced on HN at 11:30 ET, story_50498314)

**Update on previously reported (RustingStrata): Flint 40B at 64.6 tok/s decode on a 12 GB card** — Event date: today, November 14 (post 10:51 ET).
Serious_Scarcity7818 benchmarked RustingStrata (the pure-Rust Qwen/GGUF engine first reported November 4) on Recursive's trending Flint 40B, UD-Q5_K_XL at 27.9 GiB — larger than VRAM — on an RTX 3060 12 GB with 30 GB system RAM. Warm decode: 64.6 vs 48.6 tok/s for llama.cpp (≈+33%); warm 4K-prompt prefill: 1.14–1.30k vs 0.85–1.02k tok/s. The wins come from adapting expert placement to the in-flight reply instead of the prompt's routing pattern, and releasing host-RAM pages for experts already resident in VRAM. Author-run, quality claims rest on the author's prior benchmarks.
*Why it matters:* a 40B-class model running faster than llama.cpp on a 12 GB card + modest RAM keeps old mid-range GPUs viable for MoE serving as pages, not weights, become the bottleneck.
*Local inference:* RTX 3060 12 GB + 30 GB DDR5; the class of rig most of the community actually owns.
Source: r/LocalLLaMA — https://www.reddit.com/r/LocalLLaMA/comments/1xw5nzn/rustingstrata_runs_flint_40b_on_a_12_gb_card_64/ (November 14, 2026)

**In-window merge waves: llama.cpp b12379–b12383, vLLM 13 PRs, SGLang 5 PRs** — Event date: today, November 14 (12:47Z–16:36Z).
llama.cpp: mtmd now allows a custom video decoder (#32188) plus video aspect-ratio/rotation fixes (#32267), Granite4Vision registered for text conversion (#32237), pplx-decider conversion aligned to qwen3.5 model_type (#32156), and a ggml-backend fix starting a new split at incompatible weights (#31775). vLLM: XPUFlashAttnMLABackend registered for FLASH_ATTN_MLA on Intel (#64255), EPLB allowed with modular TRT-LLM FP8 block-scale MoE (#67591), Qwen2 servable via the Transformers backend (#66821), sparse-NCCL update OOM fix (#66375). SGLang: Hy-Nomas diffusion fused groupwise INT8 weight dequantization (#49270), multi-rank IPC all-to-all leak fixed (#50321), expensive Python stop-regexes rejected (#49915). Saturday-size waves, nothing arch-breaking.
*Why it matters:* the mtmd custom-decoder path and XPU MLA registration keep widening which backends can serve which models without patches.
*Local inference:* benefits llama-server video pipelines, Intel Arc XPU serving, and diffusion stacks on consumer GPUs.
Source: GitHub — https://github.com/ggml-org/llama.cpp/releases (b12379–b12383), https://github.com/vllm-project/vllm/commits/main , https://github.com/sgl-project/sglang/commits/main (November 14, 2026)

## Model releases

No new open-weight model releases were verified inside this window (Saturday lull; major-lab Hugging Face authors posted no new repos 07:45–12:45 ET). The two genuinely new weights above 100M parameters were community models, listed next section.

## Community spins and quantizations

**tiny-voca: fully open 4.5M-parameter TTS runs 70× real-time on CPU** — Event date: today, November 14 (post 12:26 ET).
n4ze3m released tiny-voca, a 4,537,291-parameter (18 MB) English TTS with two 24 kHz voices that decodes ~70× faster than real time on four CPU threads, with PyTorch, ONNX and in-browser (WebGPU-era) runtimes. It trains from scratch in 7.7 hours on one RTX 4090 and replaces the neural vocoder with a small source-filter synthesizer — the main reason it is so cheap to run. Everything is open: code, weights, training recipe, dataset (train audio was itself synthesized with Qwen3-TTS). Limitations stated by the author: English only, two voices, no cloning, pronunciation misses on odd spellings.
*Why it matters:* it is a working existence proof that TTS without a vocoder at ~4.5M params can hit real-time-multiples on plain CPU — relevant to assistants that must run alongside an LLM on the same box.
*Local inference:* any x86/ARM CPU, 18 MB model; demo runs entirely in-browser, nothing sent to a server.
Source: r/LocalLLaMA — https://www.reddit.com/r/LocalLLaMA/comments/1xw7zrg/tinyvoca_a_fully_open_45mparameter_tts_code/ ; weights https://huggingface.co/n4ze3m/tiny-voca ; code https://github.com/n4ze3m/tiny-voca (November 14, 2026)

**basal-omni-2.0-mini: two-way-reading typed decision model on Gemma 4 E4B** — Event date: today, November 14 (post 10:16 ET).
Remek published an early release of the small model of basal-omni 2.0, fine-tuned on Gemma 4 E4B: it reads the input state in both directions before answering, scores 0.811 on the hidden bilingual Werdykt set (5,000 Polish/English decisions), and emits calibrated probability distributions over up to 255 coded options in a single forward pass — no text generation, answers never leave the allowed set. Runs on the public basal engine 2.8.0 (2.5+ required for two-way reading).
*Why it matters:* typed decision heads remain the community's favorite low-VRAM answer to flaky JSON tool-calling; this one competes on a hidden benchmark rather than self-built suites.
*Local inference:* E4B class — fits comfortably in ~8 GB VRAM quantized; engine is CPU/GPU-portable.
Source: r/LocalLLaMA — https://www.reddit.com/r/LocalLLaMA/comments/1xw4thp/basalomni20mini_typed_decision_model/ ; weights https://huggingface.co/Remek/basal-omni-2.0-mini-preview (November 14, 2026)

**Update on previously reported (Unsloth Inkling-2-Small-GGUF): sub-4-bit tiers landed** — Event date: today, November 14 (commits 09:02 and 09:13 ET).
danielhanchen pushed two "upload-large-folder" commits to unsloth/Inkling-2-Small-GGUF this morning, extending the ladder first reported by this brief yesterday (Q4_K_XL→Q8_K_XL + mmproj). The repo now lists UD-IQ1_M, UD-IQ1_S, UD-IQ2_XXS and UD-IQ3_XXS directories alongside the Q2→Q8_K_XL tiers, plus BF16 and three mmproj variants — giving 594B/17B Inkling-2-Small's serving story its low-bit end.
*Why it matters:* sub-3-bit i-quants of the Thinking Machines flagship are the tiers that make the model discussable on 16 GB cards with expert offload.
*Local inference:* IQ2/IQ3 tiers target 16–24 GB cards with host-RAM offload; Q4_K_XL-class remains the 32 GB+ recommendation.
Source: Hugging Face commits API — https://huggingface.co/unsloth/Inkling-2-Small-GGUF/commits/main (November 14, 2026)

## AI hardware and silicon

**Update on previously reported (Corsair PinProtect+ / HX1000i SHIFT CRYSTAL): the six-switch torture cable** — Event date: today, November 14 (VideoCardz 08:40 ET).
VideoCardz reported (source: Profesional Review) a non-commercial Corsair test jig: a 12V-2×6 cable with a control box of six switches that kills one 12V pin at a time while the GPU loads. With ~250 W draw, the PinProtect RM1200e Gen 2 shut down GPU power after five of six pins were dropped; the PinProtect+ HX1000i SHIFT CRYSTAL tripped at the per-pin 9.5 A threshold with two pins left, after a 30-second persistence window. Corsair has no plans to sell the cable.
*Why it matters:* per-pin OCP is becoming a real design feature for 600 W-class workstation rigs — the same boards this brief's readers stack with RTX PRO and 5090 cards — and Corsair is publishing the failure-mode demo to differentiate from "rated for 600W" sticker compliance.
*Local inference:* matters to multi-GPU AI workstations and high-TDP Blackwell boards; nothing changes for laptop/single-card builds.
Source: VideoCardz — "Corsair builds special 12V-2×6 cable with six switches to simulate GPU power pin failures," November 14, 2026 13:40 GMT — https://videocardz.com/newz/corsair-builds-special-12v-2x6-cable-with-six-switches-to-simulate-gpu-power-pin-failures

## Just missed the window

- **NVIDIA: "When One GPU Isn't Enough" — GLM-5.3 NVFP4 across two DGX Stations with vLLM** (NVIDIA Developer Blog, November 13). The 753B/40B-active GLM-5.3 NVFP4 checkpoint (~464 GB) served TP=2 over dual ConnectX-8 RDMA rails on two GB300 DGX Stations (252 GB HBM3e each) through one OpenAI-compatible endpoint: peak 607.8 tok/s aggregate at concurrency 16, 385.0 tok/s at C32, with MTP, FP8 KV-cache and prefix caching. Published yesterday afternoon — ~18h before this window opened. — https://developer.nvidia.com/blog/when-one-gpu-isnt-enough-serving-glm-5-3-across-two-nvidia-dgx-stations-with-vllm/
- **Corsair HX1000i SHIFT CRYSTAL hit retail at $339.99 / €289.90** (VideoCardz, today 06:19 ET — 86 minutes before window open): transparent 1000W Cybenetics Platinum unit with PinProtect+ per-pin OCP (7–10 A threshold, 10–180 s shutdown delay) and integrated iCUE LINK hub. — https://videocardz.com/newz/corsairs-transparent-hx1000i-shift-crystal-psu-is-now-available-for-340

## Coverage notes

- Verified in-window items: 7 (5 new + 2 "update on previously reported") in this edition's manifest, plus 2 one-time "just missed" footnotes. Counting unit: manifest entries.
- Ledger dedupe: 11 candidates suppressed as already reported or already-footnoted — Siren Halo GDDR7 (re-coverage of Nov 12), lawyer V100 NVFP4 (reported Nov 9/13), LumaBrowser 150-star post (reported Oct 22), STH "EPYC 9006 Knockout Part Deux" (Venice benchmark event footnoted Nov 11), WSJ Kress $125B AI-financing cap (circular-financing item already footnoted today 07:45), FEVM FNPH1 Panther Lake mini-PC (Nov 11 event), BIOSTAR BIPTL-IHT (Nov 12 event), Meta Muse Ember (Nov 9), "NVIDIA's Jacob Freeman says the end of GeForce is not happening" (pure gaming, out of scope), gpu-unlock sidebar re-coverage (reported Oct 21/Nov 1), Blackwell checkpoint-forensics sidebar (reported Nov 12).
- Dead ends / limitations: Tom's Hardware's Bessent/NVIDIA-shell-companies and Malaysia GPU-tracking pieces carry no extractable timestamp on fetch and trace to Nov 12–13 statements (out-of-window policy stories — excluded); ARC 1.0 (0.6B routing decision model, rL post 08:20 ET) has no locatable HF repo yet — held back rather than cited unverified; davidAU posted nothing in window; ServeTheHome published nothing on November 14; HF's global newest-models feed was unusable noise.
- No index was down; Reddit anonymous feed and HN Algolia both healthy.
