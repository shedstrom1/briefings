# AI Hardware & Local Models Brief — Oct 21, 2026 (07:45 ET edition)

**Window:** 2026-10-20 17:45 EDT → 2026-10-21 07:45 EDT (previous fire → this fire; new items only)

## TL;DR
- KRAFTON released Raon-1 mini preview — a from-scratch 28.8B/4.3B-active hybrid-attention MoE under Apache 2.0, with an NVFP4 build that fits a single RTX 5090.
- Kapitsa 0.2 shipped KFlash tree speculative decoding for Apple Silicon, claiming ~65 tok/s on Qwen3.8-27B 4-bit on an M4 Pro — beating Inco Splash 1.4.0's ~50.
- turboderp's EXL3 build of Qwen3.8-Flash-Next hit 1.66 bits/weight — the whole model under 32 GB, running on one RTX 5090 with no expert offload.
- llama.cpp's server grew a POST /tts endpoint and a realtime transcription API; vLLM landed MI355X-specialized decode kernels for MiniMax-M3 MXFP4 and Kimi-K3.
- Berth v0.3.0 introduced fast-swap (engines park in RAM, no disk reload) — a material update to yesterday's T-Crypt/berth report; Strata v0.2.2 added per-GPU Main/Helper/Off roles.

## Model releases

**KRAFTON releases Raon-1 mini preview — 28.8B/4.3B hybrid-attention MoE, Apache 2.0, NVFP4 fits one RTX 5090** — *event date: October 21, 2026 (KST)*
KRAFTON AI (the PUBG studio's AI lab) published a preview of Raon-1 mini, trained from scratch: 28.8B total / 4.3B active parameters, 25 layers with a 18:7 hybrid layout of "Raon Delta Attention" (decoupled erase/write gates over Kimi Delta Attention) and gated MLA, NoPE position encoding, and native 262,144-token context. Only 7 MLA layers keep a KV cache (~8 KiB/token BF16); the RDA layers use fixed-size state. It ships in MXFP8 and NVFP4 — KRAFTON says NVFP4 enables inference on a single NVIDIA RTX 5090 (32 GB) — plus a DFlash2 draft model delivering up to 2.18x output throughput vs autoregressive decoding on a single B300. Card benchmarks show SWE-Verified 70.8 and strong Korean-conversation scores; it's a gated (request-access) repository under Apache 2.0.
**Why it matters:** a games company shipping a competitive sub-4B-active MoE with a genuinely small-footprint attention hybrid — and the first frontier-adjacent weight drop sized explicitly for one 32 GB consumer Blackwell card.
**Local inference:** NVFP4 → single RTX 5090 32GB (also SM120 workstations / DGX Spark class); MXFP8 → 48GB+ class or two-GPU tensor parallel; linear-attention design means tiny KV cache at 262K context.
Source: KRAFTON/Raon-1-mini-preview model card, Hugging Face, dated 2026-10-21 — https://huggingface.co/KRAFTON/Raon-1-mini-preview
Source: "Krafton Releases Raon-1 Mini, Its In-House AI Language Model," Seoul Economic Daily EN, October 21, 2026 — https://en.sedaily.com/technology/2026-10-21/krafton-releases-raon-1-mini-its-in-house-ai-language-model
Source: "Krafton Unveils Preview of 30B 'Raon-1 Mini'," BigGo Finance — https://finance.biggo.com/news/760bb58d-5dcc-490a-9fa4-2c03ecbbef67

## Community spins and quantizations

**turboderp Qwen3.8-Flash-Next EXL3: 1.66 bits/weight puts the whole model under 32 GB — one RTX 5090, no offloaded experts** — *event date: October 20, 2026 (~19:34 EDT upload)*
The turboderp/Qwen3.8-Flash-Next-exl3 repo received a new sub-2-bpw revision at 23:34 UTC, and a r/LocalLLaMA demo post showed it running fully on a single 32 GB RTX 5090 with zero experts offloaded — poster-reported 200-300 tok/s (with heavy KV-cache quantization eating the rest of the card; the author corrected the fit to "under 32 GB"). EXL3's trellis quantization continues to beat GGUF at these bit-widths, and the thread's Q&A (with the quant's author in-thread) doubles as a solid primer on KL-div vs end-to-end quant evaluation and dynamic expert placement for CPU offload.
**Why it matters:** sub-2bpw stable inference on the flagship consumer card means 16 GB and 24 GB owners get headroom for context, and 5090 owners get a usable all-GPU agent model.
**Local inference:** targets RTX 5090 32GB (EXL3 / ExLlamaV3, CUDA); 24 GB cards remain just short; KV cache quantization required for long context.
Source: turboderp/Qwen3.8-Flash-Next-exl3, Hugging Face, lastModified 2026-10-20T23:34:59Z — https://huggingface.co/turboderp/Qwen3.8-Flash-Next-exl3
Source: "Qwen3.8-Flash-Next @ 1.66bpw-exl3 STABLE under 36gb," r/LocalLLaMA (u/Unstable_Llama), Oct 21 2026 — https://www.reddit.com/r/LocalLLaMA/comments/1xbgi6i/qwen38flashnext_166bpwexl3_stable_under_36gb/

## Inference software and benchmarks

**Kapitsa 0.2 ships KFlash tree speculative decoding for Apple Silicon — ~65 tok/s on Qwen3.8-27B 4Q, vs Splash 1.4.0's ~50** — *event date: October 21, 2026*
Ye, developer of the Apple Silicon-optimized engine Kapitsa (Qwen/Nemotron focus), released v0.2 with KFlash, a tree speculative-decoding method that surfaces hidden states from specific Qwen layers inside a single target pass. Team benchmarks on a MacBook Pro M4 Pro 48 GB (Qwen3.8-27B 4-bit) report ~65 tok/s with KFlash against ~50 tok/s for Inco's Splash 1.4.0 (which shipped with DFlash2 the day before), with Ollama/llama.cpp/LM Studio also trailing; the repo's own doc measures greedy-decode gains of 3-16% depending on model and prose-vs-code content. All figures are author-reported on consumer Macs.
**Why it matters:** Apple Silicon's local-inference race is now a speculative-decoding arms race (KFlash vs DFlash2), and the deltas land squarely on the M4 Pro 48GB class — the most popular "serious local LLM" Mac.
**Local inference:** Apple Silicon (M-series, MLX-class stacks); 27B-4bit comfortable on 48 GB unified memory, 4B/35B-A3B also benchmarked.
Source: "KFlash: Benchmarks of newly released versions of Apple Silicon optimized engines," r/LocalLLaMA (u/DotaMate), Oct 21 2026 — https://www.reddit.com/r/LocalLLaMA/comments/1xbdxg3/kflash_benchmarks_of_newly_released_versions_of/
Source: kapitsa-xl/docs/kflash.md, GitHub, main — https://github.com/necrasov-ilya/kapitsa-xl/blob/main/docs/kflash.md
Source: "Kapitsa 0.2's KFlash tree speculative decoding," AGI Hunt, Oct 21 2026 — https://agihunt.info/en/p/1a15cd76171d3e1801de2627292

**Update on previously reported (ledger: T-Crypt/berth, Oct 20) — Berth v0.3.0 adds fast-swap: engines park in RAM, no disk reload** — *event date: October 20-21, 2026*
The VRAM-leasing daemon shipped v0.3.0 (Oct 20 22:57 UTC) and quick-follow-ups v0.3.1/v0.3.2 (through Oct 21 02:10 UTC). The headline: engines that can self-offload are parked in system RAM on a drain and resume under the same lease without reloading from disk — Berth admits a park only when measured RAM covers it, and evicts parked engines first under memory pressure. v0.3.2 adds Windows-tester fixes (Epic Games launcher no longer holds the card, per-process VRAM counts, `berth service install`). The author is publicly seeking testers with large RAM and multi-GPU rigs.
**Why it matters:** model-swap latency is the main ergonomic wall for one-GPU multi-engine rigs (llama.cpp + vLLM + ComfyUI sharing one card); RAM-parked swaps cut the switch from a full reload to a re-map.
**Local inference:** any single-GPU Linux/Windows box; benefit scales with RAM (author notes llama.cpp/Strata-class engines pin tens of GB when parked).
Source: T-Crypt/berth releases v0.3.0-v0.3.2, GitHub, Oct 20-21 2026 — https://github.com/T-Crypt/berth/releases

**Strata v0.2.2 — per-GPU Main/Helper/Off roles for the 125B-MoE-on-12GB-VRAM engine** — *event date: October 21, 2026*
Strata, the open-source engine pitched at running 125B+ MoE checkpoints on 12 GB VRAM consumer GPUs by splitting weights across VRAM/RAM/NVMe, released v0.2.2 (11:10 UTC). Each graphics card can now be assigned Main (holds last layers, draft layer, final window check), Helper (a share of layers), or Off — in the app, `strata launch`, server and Docker; Gyro acceleration extends to Intel Arc and Mac with faster NVIDIA paths; an engine package arrives for Radeon RX 6000, plus an optional API key and a single definition of "answer speed." Engine packages bump to 0.1.47.
**Why it matters:** explicit multi-GPU role control is the homelab answer to asymmetric rigs (e.g. a 16GB card + an old 8GB helper) and keeps Strata in the running for the hybrid-inference crowd.
**Local inference:** 12GB-VRAM-class Windows/Linux gaming PCs; Intel Arc, Radeon RX 6000+, NVIDIA; MoE checkpoints that don't fit VRAM.
Source: Niko1221/Strata release v0.2.2, GitHub, published 2026-10-21T11:10:21Z — https://github.com/Niko1221/Strata/releases

**llama.cpp b11787-b11792: server POST /tts endpoint, realtime transcription API, OpenCL dp4a GEMM additions** — *event date: October 20-21, 2026*
Six builds landed in the window. The two functional additions are server-side: a POST /tts endpoint for TTS models (#26603) and a realtime transcription API (#30636) — llama-server becomes a one-binary ASR+TTS serving stack. On the kernel side: OpenCL gains dp4a GEMM variants on vector load activation (#30753, on top of yesterday's cooperative-kernel dp4a work), Hexagon NPU repack/tiling is now NEON-vectorized and threaded (#30901), GGML/CPU computes f32 gelu_quick/geglu_quick without the fp16 table, MoE down-projection now scales per expert token (#30861), vocabulary loading got optimized, and the Metal GPU-capture path's memory leaks were fixed.
**Why it matters:** a TTS/ASR endpoint in llama-server removes the last reason to keep a separate Python speech stack on local rigs; the dp4a/Hexagon work keeps improving the iGPU/NPU tail.
**Local inference:** runs anywhere llama.cpp does — the /tts + transcription APIs matter most on 8-16 GB cards where separate speech models don't fit; OpenCL/Hexagon targets iGPU and Snapdragon-class devices.
Source: ggml-org/llama.cpp releases b11787-b11792, GitHub, 2026-10-21T09:48Z-11:38Z — https://github.com/ggml-org/llama.cpp/releases

**vLLM + SGLang in-window merges: MI355X specialized decode for MiniMax-M3 MXFP4 and Kimi-K3; SGLang adds online INT8 for Qwen-Image-2.1 in ComfyUI** — *event date: October 20-21, 2026*
vLLM landed MonoKernel decode for MiniMax-M3 MXFP4 on MI355X (#62712) and a specialized Kimi-K3 decode kernel for the same flagship AMD GPU (#60683), bumped AITER to v1.26.0, cut paged-attention kernel build time 7.6x on ROCm, added Nemotron Parse 3.5 serving with MTP support, and documented a TOKENSPEED_MLA draft backend for Kimi-K3 on GB200/GB300. SGLang added online INT8 quantization for BF16 Qwen-Image-2.1 single files in its ComfyUI integration (#44865), NIXL per-GPU UCX device selection (#43482), GLM-5.3/Flash NPU deployment tutorials, and a Qwen3.8-Max W4A8 Ascend checkpoint reference in its NPU docs.
**Why it matters:** AMD's MI355X keeps collecting day-0 specialized kernels for the newest MoE models, and ComfyUI users on a single 16-24 GB GPU can now run Qwen-Image-2.1 in INT8 without pre-quantizing.
**Local inference:** vLLM items target MI355X (321GB HBM3e) datacenter and GB200/GB300; the SGLang INT8 image path is workstation-class (RTX 4090/5090, RX 9070 class).
Source: vllm-project/vllm commits since 2026-10-20T21:45Z, GitHub — https://github.com/vllm-project/vllm/commits/main
Source: sgl-project/sglang commits since 2026-10-20T21:45Z, GitHub — https://github.com/sgl-project/sglang/commits/main

## Also noted
- NVIDIA GeForce driver page refreshed 617.42 WHQL (Oct 21, TechPowerUp driver index); AMD Adrenalin 26.10.1 WHQL also released Oct 21 but its changes are FSR 4.1 upscaling and game day-0 support — gaming-only, out of scope here.

## Just missed the window
- **NVIDIA quietly open-sourced "Flare"** — an Apache-2.0 Rust local-inference server built for SM120/SM121 (RTX PRO 6000 Blackwell, DGX Spark): PyTorch+FlashInfer at build time ("Ignition"), Python-free Rust runtime ("Helios") serving captured CUDA graphs, FP8 KV cache by default, recipes for Qwen Flash/GLM/DeepSeek. Repo went public Oct 20 ~13:09 EDT — ~4.5 hours before this window opened (it sat inside the 12:45→17:45 run and surfaced there unnoticed; NVIDIA developer forum attention post-dated it). If you have a Spark, this is the one to try. — https://github.com/NVIDIA/flare-local-inference
- **ane-fieldfare** — Show HN (in-window) for a repo that reverse-engineered Apple's ANE to run Gemma 4 26B at 100+ tok/s while streaming MoE experts from SSD (vs 20-30 tok/s for Turbo Fieldfare), with speculative decoding on top; the repo itself went public ~2 hours before this window. — https://github.com/zw5/ane-fieldfare

## Coverage notes
- Verified items this run: 7 (each page-fetched, in-window timestamp confirmed; leads read in full) + 2 one-time "Just missed the window" footnotes (Flare, ane-fieldfare — first and only footnote for each).
- Ledger dedupe: ~12 candidates suppressed as already reported or excluded by ledger policy — RTX PRO 5500 China order (re-surfaced on r/LocalLLaMA), llama.cpp overnight builds b11744-b11756 (prior runs), T-Crypt/berth v0.2 (listed above only as a material v0.3 update), Unsloth Studio/FLUX.2 commits (yesterday), Bria Fibo 2.0 + edit-turbo (yesterday, still trending), DeepSeek V4.1-Flash-1017 / DeepInfer (yesterday), lodestones/Kromoss + OpenMOSS MOSS-Image-SFT re-trending, APEX-GGUF (Oct 19), BlueField-4 STX CMX (already `missed:`-tagged), Intel GPU D-Series (EXCLUDE row), MiniMax M3.1-Flash (weights due Oct 23 — not yet), STH RTX Spark N1X launch overview (Oct 17, event predates window).
- Dead ends / limitations: The Register AI-and-ML index 404'd (fell back to homepage river — no in-window hardware items); TechPowerUp /software/ returned 403 (driver details taken from search results + driver index); Specter MoE-offloading paper (ASPLOS 2027) is arXiv 2511.14102 — November 2025 disclosure, re-covered in-window, excluded as stale; AWS push-based NIXL KV connector blog dated Oct 19 — out of window; Sage-A1.5 (InternScience) and OvisOCR3 Reddit posts are in-window but the underlying HF upload dates could not be verified — dropped per two-strike rule; Qwen3.8-Max W4A8 Ascend checkpoint referenced in SGLang docs has no verifiable release date — dropped; "Strata 4x tok/s vs llama.cpp" community claim lacks a primary benchmark — not repeated.
- All performance numbers above (tok/s, %, bits/weight, VRAM fit) are author/vendor-reported unless a spec sheet is cited.
