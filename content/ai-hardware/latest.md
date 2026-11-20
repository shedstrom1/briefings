# AI Hardware & Local Models Brief — November 20, 2026

**Window:** 2026-11-20 08:06 → 12:45 EST (previous fire → this fire)

## TL;DR
- First independent review of an NVIDIA RTX Spark laptop is out: TechPowerUp rates the Microsoft Surface Laptop Ultra ($2,600 base, $5,900 top) a class-of-its-own mobile AI workstation on the N1X with up to 128 GB unified memory.
- Microsoft's RDMA-over-USB4 developer preview (disclosed Nov 19, just missed the window) showed 235B-class MoE models at ~22 t/s across three Ryzen AI Max mini-PCs — footnoted below.
- Unsloth Desktop adds AMD XDNA 2 NPU acceleration (also Nov 19, footnoted): Qwen3.6-35B-A3B running on the NPU with ~28 GB shared memory.
- Tencent Hy4 day-0 quant wave keeps landing in-window: Grawnch's 2.50 bpw SM120 trellis variant plus fresh FP8 tiers.
- Heavy in-window merge waves across llama.cpp (b12572–b12589), vLLM (Hy4 fixes), SGLang (Foundry CUDA-graph save/load) and TensorRT-LLM (Rubin SM107 merges).

## AI hardware and silicon

**TechPowerUp publishes first detailed RTX Spark laptop review — Microsoft Surface Laptop Ultra** — event date: November 20, 2026 (review published this morning)
The review covers the full NVIDIA RTX Spark/N1X platform: 20-core Arm CPU (10× Cortex-X925 + 10× Cortex-A725, up to 4.1 GHz), a Blackwell GPU with 6,144 CUDA and 192 Tensor cores, and up to 128 GB of shared LPDDR5X-9400 memory (18-core models get a 5,120-core GPU with 24–32 GB). The NVIDIA App lets you set the CPU/GPU memory split — 64/64 by default — and TPU's verdict is that no other Windows laptop offers this much GPU-accessible memory with the full CUDA stack in such a refined package, while flagging the price: $2,600 entry, $5,900 for the 128 GB/1 TB unit. Gaming is a wash, but for local model work it "sits in a class of its own for efficiency and capability."
- **Why it matters:** RTX Spark units (DGX Spark, ASUS/Dell/MSI desktops) have been sold out or allocation-locked since launch; this is the first independent, full platform review with pricing context, and it validates the 128 GB unified-memory thesis for local inference — the same silicon class as the DGX Spark dev boards homelab builders are waiting on.
- **Local inference:** N1X = 6,144-core Blackwell with 64 GB GPU-default VRAM split (up to the full 128 GB), enough for Q4-class 70B models fully resident and 200B-class MoEs with light offload at the $2,600–$5,900 price point; 3× USB4 40 Gbps for expansion/clustering.
- Source: TechPowerUp, "Microsoft Surface Laptop Ultra Review – The next-gen Arm laptop," Nov 20, 2026 — https://www.techpowerup.com/review/microsoft-surface-laptop-ultra/

## Model releases

No new open-weight model disclosures met the window this run (Tencent Hy4 weights landed before the 07:45 fire and are reported there).

## Community spins and quantizations

**Update on previously reported (Hy4, reported today 08:06): day-0 quant wave keeps landing** — event dates: Nov 20, 14:46–17:19 UTC
Community quantizers kept working Tencent's ~774B Hy4 flagship through the window: Grawnch published Hy4-Flash-WV-v6.6 at 2.50 bpw targeting SM120 (RTX 50-series) hardware (16:25 UTC), and FamilyOrang shipped Hy4 and Hy4-flash mirrors plus FP8 tiers (14:46 and 17:18 UTC). Earlier pre-window day-0 tiers (MLX 3.1–4.6-bit from nace-ai/Xylolabs, W4A4/W8A8 variants, EXL3 builds) frame how fast the ladder is filling in — with vLLM already merging Hy4 mxfp4/DFly fixes this morning.
- **Why it matters:** Hy4 is the biggest new open-weight flagship in weeks, and a 2.5 bpw SM120 tier signals people are seriously attempting it on consumer Blackwell rigs rather than waiting for datacenter FP8 clusters.
- **Local inference:** at ~2.50 bpw the flash variant still implies ~240+ GB of weights — multi-GPU/multi-Spark territory, not a single card; the FP8 tiers (~775 GB) sit at DGX Station/2×GB300 class. SM120 tags make RTX 5060 Ti 16 GB–class machines part of the conversation as expert-offload nodes.
- Sources: https://huggingface.co/Grawnch/Hy4-Flash-WV-v6.6-2.50bpw-SM120 (created Nov 20 16:25 UTC); https://huggingface.co/FamilyOrang/Hy4-FP8 (created Nov 20 17:18 UTC)

**mradermacher in-window GGUF ladder, including the trending harness-adapted Qwen3.8-27B** — event dates: Nov 20, 13:39–17:05 UTC
Five imatrix/quant ladders landed inside the window: CalibForge-35B-A3B-v2-i1 (13:39 UTC), Gemma3NPC-4B-pre-thinking-i1 (14:07), Next-3-Pro-i1 (16:47 — a quant of thelamapi's 27B "next3-pro" qwen35-arch mystery model), Climb-1.2-Photon-i1 (16:53), and Qwen3.8-27B-pi-adapted-bf16-i1 (17:05), a new tier for the harness-adapted model that is currently #2 on HF trending (832 likes).
- **Why it matters:** the pi/opencode harness-adapted Qwen3.8-27B line is the community's current answer to "which quant runs my coding agent best," and its rise to #2 trending means broad 16 GB-class experimentation; Next-3-Pro continues the mystery-model-quant pattern.
- **Local inference:** the 27B ladder fits 10.5–30 GB (RTX 5060 Ti 16 GB at Q4_K_XL with light offload; 24 GB+ cards fully resident); Gemma3NPC-4B is sub-4 GB CPU/edge class.
- Sources: https://huggingface.co/mradermacher/Qwen3.8-27B-pi-adapted-bf16-i1-GGUF (Nov 20 17:05 UTC); https://huggingface.co/mradermacher/Next-3-Pro-i1-GGUF (Nov 20 16:47 UTC); https://huggingface.co/mradermacher/CalibForge-35B-A3B-v2-i1-GGUF (Nov 20 13:39 UTC)

## Inference software and benchmarks

**llama.cpp ships nine builds (b12572–b12589) with Vulkan, multimodal and UI gains** — event date: Nov 20, 2026 (14:07–17:42 UTC builds; 12 PRs merged in-window)
Highlights from the in-window merges: Vulkan COL2IM_1D support with misaligned bytes and split workgroups (#32673, 17:11 UTC), mtmd drama3 voice cloning (#32586, 17:34 UTC), Gemma 4 optimized prompt processing for multi-sequence queries (#32342, 15:41 UTC — meaningful for concurrent agent sessions), server --ui-mcp-proxy-allow-private-urls (#32633), voice messages/dictation with live waveforms in the web UI (#32531), and ggml deprecating ggml_ftype with major-version-bump reminders (#32647).
- **Why it matters:** the Gemma4 multi-sequence prompt-processing work directly improves throughput for anyone serving several agent conversations on one desktop GPU, and the UI gains voice I/O without a third-party frontend.
- **Local inference:** all consumer-side — Vulkan COL2IM helps RDNA/Intel iGPU image paths; Gemma4 optimization benefits 12–32 GB cards running E4B/26B-A4B variants.
- Source: https://github.com/ggml-org/llama.cpp/releases (b12572 14:07 UTC → b12589 17:42 UTC, Nov 20)

**vLLM merges Hy4 day-1 fixes plus cross-machine snapshot groundwork** — event date: Nov 20, 2026 (18 PRs merged 13:23–17:15 UTC)
The Hy4 support PR merged at 07:45 fire got immediate post-landing fixes: hy-v4 mxfp4/DFly, data-parallel and cudagraph_mm_encoder, and reasoning-parser errors (#68689, 14:09 UTC). Also in-window: DeepSeek-V3.2 NVFP4 loading fix via upstream DeepseekV32Config (#68948, 14:52 UTC), the first step of cross-machine snapshot support with portable attachments (#61544, 16:00 UTC), an SM89 int8 CUTLASS scaled_mm fix under batch-invariance (#67687, 15:49 UTC), Kimi-K2.5 video-chunk ViT fixes, and a security bound on batch chat conversations (#68904).
- **Why it matters:** cross-machine snapshot support is the serving-side answer to fast cold-starts on clustered consumer rigs — same problem space as Microsoft's USB4 RDMA push (footnoted below); the SM89 fix keeps RTX 40-series int8 paths healthy.
- **Local inference:** vLLM on RTX 4090/5090 (SM89/120) benefits directly from the CUTLASS and NVFP4 fixes; Hy4 fixes make multi-GPU Hy4 attempts more stable.
- Sources: https://github.com/vllm-project/vllm/pull/68689; https://github.com/vllm-project/vllm/pull/61544 (merged Nov 20, 2026)

**SGLang adds Foundry fast CUDA-graph save/load and megakernel bindings** — event date: Nov 20, 2026 (12 PRs merged 13:29–17:41 UTC)
The notable merges: an adapter for Foundry enabling fast CUDA graph save/load (#42254, 17:26 UTC) — graph persistence across process restarts cuts warm-up time on long-running servers; megakernels gained DSA/MLA binding views, EAGLE draft capture checks and early draft-embedding sharing (#46210, 17:41 UTC); Gemma 4 streaming tool-call ordering and text-preservation fixes (#51592/#51616); Kimi-K3.1 gfx950 module isolation for AMD (#51366); BLAKE3-based multimodal item identity in rust-server (#50846).
- **Why it matters:** graph save/load moves serving frameworks toward instant resume, which matters for GPU-time-sliced home servers that load different engines per request; tool-call fixes keep Gemma 4 agents reliable in local stacks.
- **Local inference:** rust-server improvements land on single-box deployments; AMD gfx950 isolation matters for MI350X-class prosumer rigs.
- Sources: https://github.com/sgl-project/sglang/pull/42254; https://github.com/sgl-project/sglang/pull/46210 (merged Nov 20, 2026)

**TensorRT-LLM merges Rubin SM107 path for MiniMax-M3.1-Flash plus GLM-5.3-Flash NVFP4 and WinoVae** — event date: Nov 20, 2026 (12 commits, 13:17–16:12 UTC)
A mass merge brought MiniMax-M3.1-Flash optimizations to Rubin (SM107) Modeling V2 (#22261, 15:07 UTC); GLM-5.3-Flash NVFP4 landed on sm_10x (B200/GB300) with TP4 + MTP (#21019, 15:12 UTC); Gemma4 decode got norm/RoPE, FP8 input-quant, PLE-tail and assistant-head fusions (#22497, 14:58 UTC); and WinoVae — an optimized Wan 2.2 VAE with fused norms, Winograd convolutions and spatial sharding — merged (#21576, 16:12 UTC).
- **Why it matters:** Rubin is now actively taking model optimizations months before hardware ships, and WinoVae matters to anyone running video-diffusion locally — VAE decode is a memory-bandwidth wall on 16–32 GB cards.
- **Local inference:** NVFP4/fusion work on B200/GB300 pushes the ceiling down-market as Marlin W4A16 NVFP4 (v1.3.0 GA, reported 08:06) covers Hopper+Ada; Wan 2.2 VAE sharding helps multi-GPU desktop video pipelines.
- Source: https://github.com/NVIDIA/TensorRT-LLM (commits #22261, #21019, #22497, #21576, Nov 20, 2026)

## Also noted
- Nothing else reached the verified-item bar this window.

## Just missed the window (footnoted once, never again)
- **Microsoft previews RDMA over USB4 on Windows for multi-PC local AI** (disclosed Nov 19): two or more USB4-linked Windows PCs split one model across combined memory + GPU compute using NetworkDirect; measured 28–50 µs round-trip, ~4× lower than UDP Ethernet on the same link. Three Ryzen AI Max+ 395 mini-PCs (128 GB each, 40 Gbps full mesh) ran Qwen3-235B-A22B Q4_K_M at ~18.5 t/s (AMD's own run: 21.98 t/s decode / 117.98 t/s prefill); three/four Intel Core Ultra Series 3 (Panther Lake) 64 GB TB4 PCs hit 49.4/54.2 t/s on GPT-OSS-120B at 1K context. Open-source NetworkDirect provider + llama.cpp fork available; Insider Experimental build "coming soon." — https://techcommunity.microsoft.com/blog/microsoftusbblog/introducing-rdma-over-usb4%C2%AE-on-windows-run-larger-local-ai-models-across-pcs/4574602 (Nov 19, 2026); https://videocardz.com/newz/microsoft-brings-rdma-over-usb4-to-windows-allowing-multiple-pcs-to-run-one-local-ai-model (recovered Nov 20)
- **AMD × Unsloth Desktop bring XDNA 2 NPU inference to Unsloth** (disclosed Nov 19): first NPU architecture supported by Unsloth Desktop, running via Lemonade + FastFlowLM; demoed Qwen3.6-35B-A3B needing ~28 GB NPU-available shared memory (64 GB RAM recommended), exposed to 100+ agent apps through Unsloth's OpenAI-compatible API. Efficiency play: frees CPU/GPU on Strix/Ryzen AI machines. — https://www.amd.com/en/blogs/2026/unlocking-the-amd-ryzen-ai-npu-for-unsloth-desktop.html (Nov 19, 2026); https://videocardz.com/newz/amd-xdna-2-npus-can-now-run-35b-parameter-ai-models-through-unsloth-desktop (Nov 19, 16:42 GMT)

## Coverage notes
- **Verified items in window: 7** (1 hardware review, 0 new model releases, 2 community/quant items [1 of them an update on today's Hy4 entry], 4 inference-software waves) + 2 once-ever footnotes.
- Ledger dedupe: 6 candidates suppressed as already reported (Hy4 weights, TRT-LLM v1.3.0 GA, ComfyUI v0.46.0, TrendForce NAND, Qualcomm Dragonfly AI200 Cirrascale, STH Nov 2026 TOP500 recap); 5 suppressed on disclosure-date grounds (arXiv "Language Models Can Rewrite Their Own Weights" v1 Nov 16 despite today's HN front-page surge at 123 points; GPU-Z 2.72; Zen 6 desktop delay rumor; NAND spot-price slide; HP Wildcat Lake mini PC — all Nov 19 disclosures) plus repeat-footnote policy applied to the two missed items above.
- r/LocalLLaMA community lane unavailable: both anonymous Reddit feeds (new.rss/hot.rss) returned 404 from this host this run — logged, not chased.
- The Register AI section index failed local fetch twice; homepage sweep done instead, nothing in-window met the bar (Anthropic IPO = market news, out of scope).
- HN Algolia, GitHub release/PR APIs, HF model API, VideoCardz and TechPowerUp front pages all healthy.
