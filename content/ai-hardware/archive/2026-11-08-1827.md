# AI Hardware & Local Models Brief — Sunday Nov 8, 17:45 ET

Window: 2026-11-08 12:45 ET → 17:45 ET (Sunday; 5h since the 12:45 run). New-items-only brief.

**TL;DR**
- Intel Arc Pro B70 32GB ($1,299) reaches ~half of an RTX 5090's generation speed on Qwen3.8-27B after SYCL + MTP tuning — but the card's price spiked to $1,700–$1,800 within days.
- vLLM merged 7 PRs (DeepSeek-V4 MegaMoE sequence-parallel + pipeline-parallel, Gemma/StableLM transformers-backend migration, ROCm extensible-KV sizing fix); SGLang merged 5 (Kimi-K3.1 PD-disaggregation + HiCache with DFLASH under decode context parallelism, AMD gate-mode fix).
- mradermacher shipped ~20 GGUF quants between noon and 5:30 PM ET, including an i1 imatrix ladder for NVIDIA Nemotron 3.5 Transcribe 30B-A4B.
- Two near-misses footnoted: Grawnch's 775 GB Kimi-K3.1-Pro-Coder quant (weights landed yesterday ~8:13 PM ET) and Tom's Hardware's CPU-only Optane Kimi K3 run (published ~8:00 AM ET).

## AI hardware and silicon

**Intel Arc Pro B70 32GB runs Qwen3.8-27B at roughly half RTX 5090 speed — for 1/5th the price, before the spike** — Event date: Nov 8, 2026 (posted 2:46 PM ET)

A homelabber bought an ASRock Arc Pro B70 (32 GB GDDR6) for $1,299 and posted a full tuning path: Ubuntu with kernel 6.17+ (Resizable BAR, CSM off), Intel compute runtime from `ppa:kobuk-team/intel-graphics`, and Mesa bumped to 26.x — alone that gave ~3x Vulkan generation over Mesa 25.2. On Qwen3.8-27B Q6 with MTP spec-decode (`--spec-type draft-mtp --spec-draft-n-max 4`) in a SYCL-built llama-server, the card hits ~960–1,032 tok/s prompt processing and ~52–56 tok/s generation at 16K–64K context, versus ~2,830 / ~98 tok/s on an RTX 5090 running $6,550+. It holds 223K context with q8_0 KV on SYCL (quantized KV on Vulkan is a 1.75x–3x+ gen penalty), and Qwen3.6-35B-A3B fits entirely with 262K context (~1,748 pp / ~74 tg at 16K). Thread comments document the price already jumping: Amazon shows $1,800 within days of the $1,299 purchase; Micro Center lists $1,700; EU street is ~€1,700–1,900.

**Why it matters:** Intel's SYCL stack (oneAPI + Xe3) is now a credible half-speed Blackwell path at ~$41/GB of VRAM, and the tuning notes (Mesa 26.x, ReBAR, SYCL q8_0 KV, MTP) are reusable for every Arc Pro buyer. But the card is becoming scarce and expensive just as local-AI GPU demand surges — the price/perf story is closing fast.

**Local inference:** 32 GB GDDR6, $1,299 street price (now $1,700–$1,800); runs 27B dense Q6 and 35B MoE fully on-card; needs Linux + SYCL (llama-server) or Mesa 26.x Vulkan with f16 KV.

Source: r/LocalLLaMA — "Intel Arc Pro B70 32GB ($1,299) vs NVIDIA RTX 5090 ($6,500+) on Qwen3.8-27B" by u/maikerukonare, posted Nov 8, 2026, 2:46 PM ET — https://www.reddit.com/r/LocalLLaMA/comments/1xr78s5/intel_arc_pro_b70_32gb_1299_vs_nvidia_rtx_5090/

## Model releases

No new open-weight releases, license changes, or major model updates inside the window.

## Community spins and quantizations

**mradermacher ships an afternoon quant ladder — ~20 GGUF repos, including Nemotron 3.5 Transcribe 30B i1** — Event date: Nov 8, 2026 (12:45–5:27 PM ET)

mradermacher published roughly 20 quant repositories between noon and 5:30 PM ET. Notable entries: an i1 imatrix ladder for NVIDIA Nemotron 3.5 Transcribe 30B-A4B (the multilingual promptable ASR model reported Oct 29 — update on that ledger entry); an LFM3-1.7B heretic/ARA-abliterated cluster (Fable5.1-heretic-ara, ARA-Abliterated, plain heretic); LFM3-Omni-2B uncensored and LFM3-VL-2B heretic/uncensored variants; Zenith-4B (Qwen2-VL-based agentic-coding ties-merge); FinchAI-Rune2-Luna (Chinese LFM3 tool-calling); Grasmere-E4B-FTPO (Welsh/Scottish-Gaelic/Irish tool-calling); and LucetV3-2B-Frontier.

**Why it matters:** The Nemotron Transcribe quant completes day-0 local availability for the reported Nov 29 ASR model; the dense LFM3-1.7B cluster keeps Liquid AI's edge-class lineup quantized for sub-2 GB and phone/NPU-class targets.

**Local inference:** GGUF targets; 1.7B–4B models run on 16 GB cards or CPU/edge devices; Nemotron 30B-A4B targets multi-GPU or large-RAM rigs at 4-bit.

Source: Hugging Face API (author=mradermacher, createdAt timestamps Nov 8, 2026, 12:45–5:27 PM ET) — https://huggingface.co/mradermacher (incl. /NVIDIA-Nemotron-3.5-Transcribe-30B-A4B-i1-GGUF, /LFM3-1.7B-ARA-Abliterated-GGUF, /Zenith-4B-GGUF, /FinchAI-Rune2-Luna-GGUF)

## Inference software and benchmarks

**vLLM afternoon merge wave — DeepSeek-V4 MegaMoE parallelism, Gemma/StableLM migration, ROCm KV fix** — Event date: Nov 8, 2026 (12:53–4:39 PM ET)

Seven PRs merged in-window: #65766 (DeepSeek-V4: sequence parallelism with pipeline parallelism for MegaMoE), #66393 (Transformers backend: migrate Gemma and StableLM), #65137 (KV Connector: keep same-host NIXL on NVLink with extensible KV cache), #63401 (ROCm: size extensible KV cache from exact VRAM use, not 2 MB-rounded free memory), #66263 (bugfix: do not initialize CUDA when importing vendored FLA), plus #65158 (CI docstring hook) and #66313 (tensorizer 2.12.1 for Python 3.14).

**Why it matters:** MegaMoE SP+PP expands DeepSeek-V4-class serving across heterogeneous GPU topologies; the ROCm sizing fix matters for AMD-card users running long-context serving where free-memory rounding previously wasted or starved the KV pool.

**Local inference:** vLLM server stack; ROCm path targets Radeon AI PRO and consumer AMD; DeepSeek-V4 recipes target GB300/H200-class clusters.

Source: GitHub vLLM merged-PR search (merged_at Nov 8, 2026, 17:53Z–20:39Z) — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged

**SGLang afternoon merge wave — Kimi-K3.1 PD-disaggregation + HiCache under decode context parallelism** — Event date: Nov 8, 2026 (12:50–5:18 PM ET)

Five PRs merged in-window: #48741 (kimi-k3.1: PD disaggregation under decode context parallelism), #49105 (kimi-k3.1: HiCache with DFLASH under decode context parallelism), #49152 (AMD Kimi-K3: pass gate_mode INTERLEAVE for GU-interleaved SiTU w13), #49151 (scheduler: finish rejected requests and offloaded releases through the request retiree), and #49201 (CI rerun-status fix).

**Why it matters:** K3.1 serving keeps absorbing disaggregated prefill/decode and cache-layer support under context parallelism — the serving pattern for 3T-class open-weight flagships — plus an AMD-specific gate-mode fix for interleaved weight layouts.

**Local inference:** SGLang targets multi-GPU clusters (B300/H200/MI355X class); AMD gate-mode fix targets Radeon/MI-series serving of Kimi-K3.

Source: GitHub SGLang merged-PR search (merged_at Nov 8, 2026, 19:50Z–22:18Z) — https://github.com/sgl-project/sglang/pulls?q=is%3Apr+is%3Amerged

## Just missed the window

- **Grawnch/Kimi-K3.1-Pro-Coder** — 2.8T-parameter Kimi-K3.1-Pro compressed to 775 GB (−56%) via 3-tier routed-expert quantization (2.81 / 2.56 / 1.44 bpw; shared experts, attention and embeddings kept BF16/F32); runs at 36.8 TPS batch / 10.8 TPS on one Blackwell GPU + pinned RAM + NVMe mmap via a Strata fork and dr-triton; 96.7% on AIME 2025 and LiveCodeBench. Weights landed on HF yesterday ~8:13 PM ET (Nov 8, 01:13Z); the community thread surfaced inside our window — missed: https://huggingface.co/Grawnch/Kimi-K3.1-Pro-Coder
- **Tom's Hardware: Kimi K3 runs CPU-only on Optane PMem 200** — 2.8T model on a 40-core 3rd-gen Xeon Scalable with 1-bit quantization at 0.85 tok/s, active DIMM cooling required; published ~8:00 AM ET this morning, ~5 hours before window open — missed: https://tech.yahoo.com/computing/articles/homelabber-uses-second-gen-optane-122000813.html

## Coverage notes

- **Verified items:** 4 (counting unit: discrete events — 1 hardware/benchmark disclosure, 2 inference-stack merge waves, 1 community quant-ladder batch).
- **Ledger dedupe:** ~11 candidates suppressed as already reported (RustingStrata Q5 benchmark, LexiSuite AIO suite, Kimi roadmap rumor, Drex-Max v1.1, QFN motion-graphics Strata demo, Qwen3.5-0.8B pure-assembly run, R9700 PCIe-switch build, ram-altman Linux script, Qwen3.8-27B-DSpine-b16, llama-modchip v2.4.0, kapitsa quant batch — mostly from this morning's 07:45 and 12:45 runs).
- **Dead ends:** VideoCardz RSS blocked by Cloudflare (two strikes, not chased); Reddit thread RSS returns 403 for non-script clients; llama.cpp had genuinely zero merged PRs in the window; Tom's Hardware Tesla Dojo D3 / SI6 roadmap article is dated ~Nov 6 (outside window, not footnoted).
- **No in-window releases:** llama.cpp (last build b12211, 11:48 AM ET), Ollama (v0.40.15, Nov 3), TensorRT-LLM (rc3, Nov 6), vLLM/SGLang version tags.
