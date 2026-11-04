# AI Hardware & Local Models Brief — November 4, 2026

**Window: 2026-11-03 17:45 → 2026-11-04 07:45 EST** (previous fire → this fire; new items only)

## TL;DR

- **Tencent launched Hy4** — a 770B-parameter (49B active) MoE flagship with first-time vision, computer-use, and 1M+ context; open weights and system cards promised "in the coming days."
- **llama.cpp hit version 0.9.0** in an overnight build wave (b12096–b12109) headlined by **Chatterbox TTS support** and a Hexagon NPU LIGHTNING_INDEXER kernel; the releases now carry llama.app as the project's official website.
- **Axion 1.0** — a ByteDance-affiliated team's "open agent foundation model" paper — shipped its 35B-A3B reproduction weights (Apache-2.0) plus the full training dataset.
- Big overnight merge waves: **vLLM 69 PRs** (incl. Hy4 ROCm graph prep and a 1.5x GB300 Muse Spark decode recipe) and **SGLang 46 PRs** (dsv4 mega-MoE prefill series, MXFP4 Engram packing, Nemotron-3-Embed).
- AI-hardware news cycle was quiet inside the window; AMD's record Q3 print and the Intel Arc B580 EOL leak both landed pre-window (footnoted).

## Model releases

**Tencent Hy4 launches — 770B-A49B MoE, vision + computer-use, weights "in the coming days"** — event date: November 4, 2026 (Wednesday morning China time; inside the window).
Tencent released Hy4, its next-generation flagship, jumping from Hy3's 295B to 770B total parameters (49B active) with a context window exceeding 1M tokens. It adds visual understanding for the first time (fine-grained image analysis, complex charts, visual math reasoning) and computer-use across CLI and GUI, with specialized training on Blender and CAD. Tencent's own benchmarks show Hy4 beating GLM-5.3 and DeepSeek-V4.1-Flash-1017 across all 12 tested agentic/coding/workplace tasks and trading wins with Kimi K3.1 Pro; it scores 83.18 average / 96.15% pass on WorkBuddy workplace tasks. Model weights and system cards are promised within days, with cybersecurity and biological-safety carve-outs (exploit-development and reverse-engineering data filtered from training). API pricing holds at the preview level: ~$0.90/$2.70/$0.045 per million input/output/cached-input tokens, with a Hy4 Flash cost-reduced variant coming soon.
*Why it matters:* This puts a third Chinese lab in the frontier open-weight tier alongside DeepSeek and Moonshot — and the weights drop (when it lands) will be the first 770B-class release with native vision + computer-use, likely to seed a wave of quants and agent-tooling like K3.1 Pro did.
*Local inference:* 770B-A49B is data-center class — no 16GB-class fit; watch for day-0 NVFP4/FP8 quants aimed at multi-GPU and 128GB unified-memory rigs (Strix Halo / M-series Ultra tier), as happened with K3.1 Pro.
Source: SCMP, "Tencent unveils Hy4 as open-weight AI race with DeepSeek, Z.ai, Alibaba heats up," Nov 4, 2026 — https://www.scmp.com/tech/big-tech/article/3373227/tencent-unveils-hy4-open-weight-ai-race-deepseek-zai-alibaba-heats
Source: Tencent official release, "Tencent Launches Hy4, Joining the Ranks of the World's Leading Open-Source AI Models," Nov 4, 2026 — https://www.tencent.com/tencent-launches-hy4-joining-the-ranks-of-the-worlds-leading-open-source-ai-models/

**Axion 1.0-35B: open agent foundation model ships reproduction weights + full training data** — event date: arXiv paper Nov 3 (2611.03233); reproduction checkpoint published Nov 4 ~04:47 UTC (in-window).
The ByteDance-affiliated Axion team (Yuqi Jia et al.) released Axion 1.0-35B, a 35B-total/3B-active MoE agent model built on the Qwen3.5-MoE architecture, trained in just three stages — two SFT passes on ~18K examples (reasoning, then tool-use) plus RL over agent trajectories, no dedicated middle-training. The reproduction checkpoint posts HLE 52.8, SEAL-0 60.4, HiPhO 45.9, FrontierScience Olympiad 85.0 — essentially matching the paper's numbers and beating GPT-5.5 on HLE and SEAL-0. Weights are BF16 across 12 safetensors shards under Apache-2.0, and the training dataset is published (Axion1-0/axion-training-data).
*Why it matters:* The claim is that frontier-ish agent capability needs a tiny, reproducible recipe — 18K examples, three stages — and the released data lets the community verify or remix it, which is exactly the pattern that made DeepSeek's releases ecosystem events.
*Local inference:* 35B-A3B MoE — same class as Qwen3.6-35B-A3B: ~20GB at Q4, comfortable on a 24GB card or 16GB-class rigs with MoE offload; day-0 quants are likely given the 3B-active footprint.
Source: HF model card, Axion1-0/Axion-1.0-35B (lastModified Nov 4, 2026 04:47 UTC) — https://huggingface.co/Axion1-0/Axion-1.0-35B
Source: GitHub, bytedance/axion1_0 — https://github.com/bytedance/axion1_0
Source: arXiv 2611.03233 — https://huggingface.co/papers/2611.03233

## AI hardware and silicon

No new hardware items verified inside the window. AMD's record Q3 2026 print (Data Center +97% YoY) and the Intel Arc B580 EOL leak both disclosed pre-window — see "Just missed the window."

## Community spins and quantizations

**mradermacher overnight batch: PhantomReasoning gsminf quants, custos-1.0 cyber model, HumanQwen3.5-4B** — event date: Nov 4, 2026, 12:38–12:50 UTC (in-window).
New GGUF uploads: PhantomReasoning-Qwen3-0.6B and -1.7B in mradermacher's gsminf (min-float group-scaled) custom tier — the first application of that tier to the PhantomReasoning Qwen3 reasoning spins; custos-1.0-GGUF, quantizing the qianqianzhisuan/custos-1.0 cybersecurity-tuned Qwen3.5 model (Apache-2.0, zh/en); and HumanQwen3.5-4B-GGUF, an Unsloth-pipeline human-like-writing fine-tune of Qwen3.5-4B.
*Local inference:* the 0.6B/1.7B gsminf quants are CPU/nano-class (sub-2GB under IQ-family tiers); the 4B models fit any 12–16GB card.
Source: https://huggingface.co/mradermacher/PhantomReasoning-Qwen3-0.6B-gsminf-GGUF , https://huggingface.co/mradermacher/custos-1.0-GGUF , https://huggingface.co/mradermacher/HumanQwen3.5-4B-GGUF

**Community runs MIT's weight-space programming on a 3090 with Strata** — event date: Nov 4, 2026 09:28 UTC (in-window).
A r/LocalLLaMA user applied the MIT CSAIL "weight-space programming" technique (reported Nov 3) using a Strata IQ4 Qwen3.8-Flash-Next engine on a single RTX 3090 + Xeon E5 + 96GB DDR4 as the "harness creator," editing Qwen3.5-0.6B weights via ~39.8M training tokens over 3h12m at 41 tok/s decode, reporting non-compression-graph results.
*Why it matters:* It's the first end-to-end home-rig replication of using one local LLM to directly edit another's weights — the gating factor was throughput (MTP drafter + Strata), not data.
*Local inference:* single RTX 3090 (24GB) + 96GB DDR4.
Source: Reddit (r/LocalLLaMA), Nov 4, 2026 — https://www.reddit.com/r/LocalLLaMA/comments/1xnjwte/using_strata_iq4_qwen_flash_to_edit_qwen_35_06b/

## Inference software and benchmarks

**llama.cpp reaches 0.9.0: Chatterbox TTS, Hexagon LIGHTNING_INDEXER, Adreno q2_0 GEMV gains** — event date: builds b12096–b12109, Nov 4, 2026 03:12–12:33 UTC (all in-window).
Thirteen PRs merged across the overnight wave: version bump to 0.9.0 (#31646); **Chatterbox TTS support in model/mtmd with built-in voices** (#30993 — the Resemble.AI TTS family now runs inside llama.cpp); Hexagon NPU gains — LIGHTNING_INDEXER op with HMX prefill/decode paths and q4_0/f32-K support (#31289), TOP_K_MASK (#31552); OpenCL q2_0 GEMV improvements for Adreno (#31577); GGUF writer now writes host-buffer tensors directly, skipping a staging copy (#31144); strided-view bounds fix affecting delta_net conv windows (#31584); -hf tag matching fixed at quant-name token boundaries (#31620); qwen3-vl video-prompt format in mtmd (#31619); RVV gating on target extension (#31607); Gemma4 assistant now follows ctx_other devices + flash attention (#31635). Release notes now carry https://llama.app as the project's website.
*Why it matters:* Chatterbox-in-llama.cpp makes production TTS a first-class GGUF citizen (no separate Python service), and the Qualcomm NPU series keeps Snapdragon laptops as a real inference target.
*Local inference:* Chatterbox quants run on consumer GPUs/CPU; the Hexagon work targets Snapdragon X-class NPUs; Adreno q2_0 lifts mid-range Android.
Source: GitHub releases, ggml-org/llama.cpp b12096–b12109 — https://github.com/ggml-org/llama.cpp/releases

**vLLM 69-PR and SGLang 46-PR overnight waves — Hy4 ROCm prep, 1.5x GB300 Muse Spark decode, MXFP4 Engram packing** — event date: merges between Nov 3 22:45 and Nov 4 12:46 UTC.
vLLM highlights: [ROCm][HY4] breakable graphs without torch.compile (#64763) — day-one Hy4 serving prep; Muse Spark Open specialized decode at 1.5x on GB300 (#64077); DFlash2 candidate-edge scoring fused into a Triton kernel (#64916); MiniMax-M3.1 ROCm NVFP4-indexer decode tactics (#65087/#65432); Kimi-K3 B200 AgentX Mooncake preset (#65529); a 12-PR Rust-frontend parity batch; AWQ/GPTQ Marlin and ModelOpt block-FP8 cache restarts (#64182/#64119). SGLang highlights: dsv4 mega-MoE pre-dispatch four-chunk series and paged-dequant widening (#48245/#48243), MXFP4 Engram tables kept packed with E2M1 decode in the gather (#47935), deep_select 3-CTA bf16 top-512 tier (#48246), Nemotron-3-Embed support (#45885), HALOps MXFP8 GEMM backend for Blackwell (#47853), Kimi-K3 fused KDA decode (#47526), Ascend 950 (arch35) reference refresh across NPU modules (#41504).
*Why it matters:* The Hy4 ROCm PR arriving within hours of the launch shows how fast the stacks track Chinese lab releases; the DFlash2/MXFP4 items keep moving spec-decode and MoE-offload numbers for Blackwell and MI-class cards.
*Local inference:* mostly data-center paths, but the SGLang fp8 small-M GEMV/Blackwell-backlog items and llama.cpp-side work filter down to RTX 50-class and Strix boxes within weeks.
Source: GitHub merged-PR search, vllm-project/vllm — https://github.com/vllm-project/vllm (merged 2026-11-03T22:45Z..2026-11-04T12:46Z)
Source: GitHub merged-PR search, sgl-project/sglang — https://github.com/sgl-project/sglang

**ComfyUI v0.44.0: MiniMax-H3 latent upscaler + Sol-H3-VAE, 4K Flux Video Upscale V2** — event date: Nov 4, 2026 01:12 UTC (in-window).
Comfy-Org shipped v0.44.0 with support for the MiniMax-H3 latent upscaler model and the MiniMax Sol-H3-VAE (both by kijai), 1080p/2K/4K targets added to the BFL Flux Video Upscale V2 node, skip-concat optimizations when there's a single conditioning, Patina texture nodes, and VEED dubbing partner nodes.
*Why it matters:* first-class upscaler/VAE plumbing for MiniMax-H3 — the community's default video model — improves local 4K video pipelines on single cards.
*Local inference:* targets consumer CUDA GPUs; upscaler runs as a lightweight pass alongside the H3 base model (16GB+ class for 1080p workflows).
Source: GitHub release, Comfy-Org/ComfyUI v0.44.0 — https://github.com/Comfy-Org/ComfyUI/releases/tag/v0.44.0

**Ollama v0.40.15-rc0: MLX loads bundled CUDA libs in-process on Linux; MiMo V2.6 support** — event date: Nov 3, 2026 22:52:31 UTC (in-window).
Two changes: the MLX engine path now loads its bundled CUDA libraries in-process on Linux (fewer subprocess/shim layers), and MiMo V2.6 gets native model support.
*Why it matters:* in-process CUDA libs on the MLX path is the plumbing step that could let Ollama's MLX runtime serve CUDA rigs without separate server processes.
*Local inference:* Linux CUDA hosts and Apple Silicon; MiMo V2.6 is Xiaomi's MoE family.
Source: GitHub release, ollama/ollama v0.40.15-rc0 — https://github.com/ollama/ollama/releases/tag/v0.40.15-rc0

**Maya engine: DeepSeek-V4.1-Flash on 4×RTX 3090 + 256GB DDR4 hits 400–600 tok/s prefill** — event date: Nov 4, 2026 10:22 UTC (in-window).
A community run of mw00/project-maya serving orcarouter/DeepSeek-V4.1-Flash-1017-abliterated-GGUF (mix-256G) at 524K context on four PCIe-x16 RTX 3090s: prefill 400–600 tok/s, decode 30–40 tok/s peaking at 60 with the DSpark drafter.
*Why it matters:* another data point that 3090 quads + big host RAM are still a viable long-context DeepSeek platform when the engine handles expert placement well.
*Local inference:* 4× RTX 3090 (96GB VRAM total) + 256GB DDR4.
Source: Reddit (r/LocalLLaMA), Nov 4, 2026 — https://www.reddit.com/r/LocalLLaMA/comments/1xnkr5q/deepseek_v41_flash_1017_on_4rtx3090_256ddr4_maya/

**Also noted:**
- Three RTX 3090s on a consumer B850 board running Qwen3.8-Flash-Next over PCIe Gen4 x1 slots — Qwen3.8-27B tensor-parallel on two 3090s at Gen4 8x/x1 mixes, documenting lane-starved multi-3090 serving (Reddit, Nov 4, 2026 — https://www.reddit.com/r/LocalLLaMA/comments/1xnct4r/3x3090s_with_qwen38_flash_next_with_pcie_gen4_1x/ ).
- TinyWebUI: zero-install Open WebUI alternative via `npx tinywebui`, with local tinysearch/tinycontext and per-user isolation (Reddit, Nov 4, 2026 — https://www.reddit.com/r/LocalLLaMA/comments/1xnmre2/tinywebui_lightweight_open_webui_alternative_just/ ).
- A community llama.cpp fork thread (QFN + bespoke quant + ultra-specific hardware configs) continues the irreproducible-benchmark wars — treat fork tg/pp claims with the usual salt (Reddit, Nov 3, 2026 22:48 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xn7rfh/llamacpp_fork_qfn_specific_quant_ultra_specific/ ).

## Just missed the window

- **AMD Q3 2026 earnings — record quarter on record Instinct GPU + EPYC sales** (press release Nov 3 ~16:05 EST, ~100 min pre-window; also unclaimed by the catch-up run): revenue $13.38B +45% YoY, Data Center $8.5B +97% YoY, Q4 guidance ~$17B. (https://ir.amd.com/news-events/press-releases/detail/1303/amd-reports-third-quarter-2026-financial-results ; analysis: https://www.servethehome.com/amd-q3-2026-earnings-data-center-growth/ )
- **Intel Arc B580 may go EOL to prioritize Arc Pro AI-inference cards** — Haze2K1 leak picked up by TechPowerUp Nov 3 ~13:12 UTC (pre-window); B580 already out of stock at most retailers, Arc Pro B50/B60/B70 carry higher margins on the same BMG-G21/G31 silicon. (https://www.techpowerup.com/354440/intel-might-discontinue-the-arc-b580-to-prioritize-higher-margin-arc-pro-cards )
- **NVIDIA RTX 5080 nears $2,000 at US retailers** — VideoCardz, Nov 3 09:45 GMT (pre-window): +$800 in two months, memory-driven price pressure on the 16GB class most local-model users target. (https://videocardz.com/newz — exact URL via site search; article was listed in site index)
- **Hydrogen-1.0** (deep-principle): 753B BF16 GLM-5.3 fine-tune for scientific reasoning, MIT license, gated-auto; repo last modified Nov 2 — r/LocalLLaMA post Nov 4 04:21 UTC was re-coverage. (https://huggingface.co/deep-principle/Hydrogen-1.0 )
- **steffi.ai "trust me bro" IGX Thor + RTX PRO 6000 SGLang benchmarks** — posted Nov 3 22:27 UTC, 18 minutes pre-window. (https://www.reddit.com/r/LocalLLaMA/comments/1xn78cm/some_trust_me_bro_benchmarks_while_we_prep_the/ )

## Coverage notes

- **Verified: 10 in-window items** (2 model releases, 2 community spins, 6 software/benchmark items) + 5 "just missed" footnotes; every in-window item's source page was fetched or returned verbatim by search, with timestamps inside 2026-11-03 17:45 → 2026-11-04 07:45 EST. Lead stories (Hy4, llama.cpp wave, Axion, vLLM/SGLang waves) read in full.
- **Ledger dedupe: 4 candidates suppressed as already reported** — gemma4 PR #31356 (in llamacpp-b11840-b12094), Revolut self-host story (in gartner-open-models entry), bartowski Clover GGUF (reported Nov 3), MIT weight-space base event (reported Nov 2; only the new home-rig replication listed). No repeat "Just missed" footnotes — all five above are first-time `missed:` entries.
- Dead ends: DuckDuckGo backend returned zero results on the first query wave (nous fallback served); tomshardware.com/ai 404s (site restructure). SpecPilot (r/LocalLLaMA paper post, in-window) excluded — arXiv 2611.02501 was published Nov 1/2, outside the window. HN llama.app spike excluded as re-coverage of the October app launch, not a new event.
- Indexes swept: ServeTheHome river, VideoCardz index, HN Algolia (date-filtered), HF trending + per-author (mradermacher/bartowski/unsloth), GitHub releases + merged-PR searches (llama.cpp/vLLM/SGLang/Ollama/TensorRT-LLM/ComfyUI/KTransformers), r/LocalLLaMA new with timestamps. TensorRT-LLM (rc2 Oct 28) and KTransformers (v0.7.2 Oct 15) had no in-window releases.
