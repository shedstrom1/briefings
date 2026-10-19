# AI Hardware & Local Models Brief — Oct 19, 2026: MiniMax M3.1 Flash ships, AMD HX 455 APU leak

**Window:** 2026-10-19 07:45 → 13:57 EDT (previous fire → now; America/New_York)

**TL;DR**
- MiniMax released **MiniMax M3.1 Flash** today: a 428B-total / ~23B-active MoE with a 1M-token context, API priced at $0.15/$0.60 per million tokens, and open weights promised **October 23** — the next big model for the local community to pre-size.
- **AMD Ryzen AI 7 HX 455** leaked today: an 8-core mobile APU with the full 16-CU Radeon 890M-class iGPU and 24 MB L3 — cheaper AI-laptop silicon.
- **SGLang v0.5.22** shipped ~5 hours before the window (Sunday night): DeepSeek-V4.1 gains up to 2.3x more throughput per GPU on H200 and lands on AMD MI350X/MI355X — footnoted below.
- Verified items in window: 2. Ledger suppressions this run: 0 (ledger was empty at fire time).

---

## AI hardware and silicon

**AMD readies Ryzen AI 7 HX 455 mobile APU with a full 16-CU iGPU** — *leaked Monday, October 19, 2026 (TechPowerUp 13:33 EDT; VideoCardz ~09:45 EDT)*

Per a Weibo leak from leaker 金猪升级包 ("Golden Pig Upgrade"), picked up by TechPowerUp, VideoCardz, Guru3D and Wccftech today, the Ryzen AI 7 HX 455 pairs eight cores / 16 threads (reportedly a Ryzen Z2 Extreme-style 3x Zen 5 + 5x Zen 5c layout across two CCDs) with a 16 compute-unit Radeon 890M-class RDNA 3.5 iGPU and 24 MB of L3 cache — a significantly larger cache than the Z2 Extreme's 16 MB, and the full-size iGPU of the 10-core Ryzen AI HX 470 at a lower core count. CPU clocks, NPU performance and launch timing are all still unknown, and this is a single-leaker story.

**Why it matters:** moving the full 16 CU graphics engine down to an 8-core SKU means AMD is repricing the "capable iGPU" tier of its Ryzen AI mobile line — the same iGPU-plus-XDNA2-NPU combo that drives on-device inference workloads. A cheaper 16-CU APU strengthens the laptop-class option for small-model local inference and upscaling tasks against NVIDIA's newly launched RTX Spark N1X laptops and Apple's M-series.

**Local inference:** targets mainstream AI laptops (consumer thin-and-light class, no discrete VRAM); the 16 CU Radeon 890M-class iGPU + 50 TOPS-class NPU is the sub-$1,500 segment's local workhorse for 7B–14B quantized models and image generation — well below the 16GB discrete-VRAM class, where shared system memory and bandwidth are the limit.

Sources:
- TechPowerUp — "AMD Ryzen AI 7 HX 455 Surfaces with 16 CU Radeon Graphics," Monday Oct 19, 2026, 13:33 ET — https://www.techpowerup.com/ (front-page news index; article link)
- VideoCardz — "AMD Ryzen AI 7 HX 455 leaked with 8-core CPU and 16-CU GPU," Oct 19, 2026 15:45 CET — https://videocardz.com/newz/amd-ryzen-ai-7-hx-455-leaked-with-8-core-cpu-and-16-cu-gpu
- Guru3D — "AMD Ryzen AI 7 HX 455 Leak Points to Eight Cores and 16-CU GPU," Oct 19, 2026 (citing IT Home / Weibo @金猪升级包) — https://www.guru3d.com/story/amd-ryzen-ai-7-hx-455-leak-points-to-eight-cores-and-16cu-gpu/
- Wccftech — "AMD Is Reportedly Preparing Ryzen AI 7 HX 455 With 16 CU" — https://wccftech.com/amd-is-reportedly-preparing-ryzen-ai-7-hx-455/

## Model releases

**MiniMax launches M3.1 Flash: 428B MoE, 1M context — open weights due October 23** — *released Monday, October 19, 2026*

MiniMax shipped M3.1 Flash on its API today at $0.15 per million input / $0.60 per million output tokens (cache reads $0.015), with weights confirmed for **October 23** (license not yet disclosed). The model is a 428B-total, ~23B-active sparse MoE with a 1,000,000-token context, native visual encoder (text/image/video in, text out), always-on reasoning with five effort levels, and roughly 150 tokens/second on MiniMax's serving stack. Vendor-reported benchmarks put it atop Automation-Bench v1.0.6 at 62.2 (vs Opus 5's 50.3) and 73.7 on DeepSWE v1.1, while trailing badly on Terminal Bench 4.0 (38.1 vs 51.8). MiniMax also confirmed its "Space Bunny" stealth model that churned through 200T+ tokens was an early M3.1 Flash build.

**Why it matters:** this is the agentic-MoE-at-flash-price play against DeepSeek-V4.1-Flash, and the October 23 weights drop is the real event for this readership — a 23B-active MoE is exactly the profile the community turns into fast local builds (the gpt-oss playbook). Caveat: every benchmark is vendor-reported with no independent replication yet, and self-hosting hardware guidance doesn't exist until the weights land.

**Local inference:** 428B total / ~23B active. For sizing: the M3 sibling's MXFP8 build is ~440 GB, so expect multi-GPU or cluster class at honest precisions; only aggressive low-bit quants (Q2-ish territory) would squeeze it into 128 GB unified-memory boxes like DGX Spark or Ryzen AI Halo (two clustered 64 GB Sparks pool to 128 GB), and it will not fit the RTX 5060 Ti 16GB class. The 23B active footprint is what makes those quants still quick if they work. Watch for GGUF/quant tiers within days of Oct 23.

Sources:
- MarkTechPost — "MiniMax Just Launched M3.1 Flash: 428B MoE Agentic Model at $0.15/M Input," Oct 19, 2026 (read in full) — https://www.marktechpost.com/2026/10/19/minimax-m3-1-flash-release/
- Artificial Analysis — "MiniMax-M3.1-Flash — Intelligence, Performance & Price Analysis" (release date Oct 19, 2026) — https://artificialanalysis.ai/models/minimax-m3-1-flash
- r/LocalLLaMA — "MiniMax M3.1 Flash released, Model weights Oct 23," Oct 19, 2026 — https://www.reddit.com/r/LocalLLaMA/comments/1x9uzv9/minimax_m31_flash_released_model_weights_oct_23/

## Inference software and benchmarks

No verified in-window developments this cycle. llama.cpp shipped builds b11734 / b11738 / b11739 inside the window (Oct 19, 10:08 / 12:59 / 13:57 EDT) but the release notes are internal fixes only (jinja `unique` filter, a sliding-window-attention checkpoint invalidation fix, Ascend NPU code cleanup) — nothing that changes what runs where. vLLM v0.32.0 (Oct 18) and Ollama v0.40.6/v0.40.7-rc0 (Oct 16–18) predate the window.

## Just missed the window

- **SGLang v0.5.22** — released 2026-10-19 at 02:55 UTC (Sunday 22:55 EDT), roughly 5 hours before this window opened. Big release (950 PRs, 245 contributors): DeepSeek-V4.1 gets up to 2.3x more throughput per GPU on H200, AMD MI350X/MI355X support arrives with up to 1.9x throughput via DSpark, and new model cookbooks land for Cloudflare Clef, Perplexity PPLX-Decider v1/v1.1, EmbeddingGemma 2, LLaDA2.2, HunyuanImage-3.0-Instruct and Kandinsky 6. It will not be footnoted again. — https://github.com/sgl-project/sglang/releases/tag/v0.5.22

## Coverage notes

- **Verified items in window: 2** (1 lead, read in full; 1 multi-outlet leak), plus 1 first-time "just missed" footnote (SGLang v0.5.22).
- **Ledger dedupe count: 0** — reported.md was empty (header only) at fire time; no candidate was suppressed as already-reported. This run's items (including the missed footnote) are appended to the ledger.
- Dead ends (two-strike rule, logged not chased): r/LocalLLaMA JSON and RSS both returned 403 (worked around via search-result mirrors); TechPowerUp's front-page review "AMD Ryzen AI Halo Review — Local-AI Powerhouse with 128 GB of Memory" could not be pinned to a confirmed publish timestamp, so it is excluded; The Neuron's "I Tested NVIDIA Nemotron Bolt and Super VL" local-benchmark piece (65.5 tok/s on a Surface Laptop Ultra) failed two URL-resolution attempts and is excluded; TechPowerUp's review index returned 403.
- Indexes swept: Tom's Hardware news, ServeTheHome, The Register AI, TechPowerUp front page, VideoCardz, Hugging Face trending, The Open Weights, LLM Releases, HN Algolia (in-window), GitHub releases for llama.cpp / vLLM / SGLang / Ollama / llamafile (llamafile repo: no releases, 404).
- Items seen and correctly excluded as out-of-window by event date: Microsoft Maia 300 (Oct 12), MiniCPM-V 4.7 (Oct 12–15), NVIDIA DGX Spark 64GB blog + $4,999 Oct 23 availability (Oct 2), STH RTX Spark N1X launch overview (Oct 17), Tom's Surface Laptop Ultra RTX Spark test (Oct 15), OpenMOSS MOSS-Image refresh (~05:00 EDT), datalab-to/indra-ocr update (created Oct 10), vLLM v0.32.0, Ollama v0.40.x, ComfyUI v0.42.x (Sunday).
- MiniMax benchmark numbers are vendor-reported and flagged as such; HX 455 is a single-leaker report, corroborated across outlets but unconfirmed by AMD.
