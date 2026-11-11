# AI Hardware & Local Models Brief — Nov 10, 2026 (17:45 edition)

**Window:** 2026-11-10 12:45 → 20:08 EST (manual catch-up for the failed 17:45 fire)

## TL;DR

- **Apple's Claris subsidiary is testing "Harbor"** — a private AI inference server app that turns Apple silicon Macs into local open-weight model endpoints and pools unified memory across Thunderbolt 5 Mac clusters.
- **XeMaya arrived**: a new SYCL/Level-Zero engine bringing native DeepSeek V4.1-Flash and GLM-5.3-Flash support to Intel Arc Pro GPUs, with prefill up to ~3x patched llama.cpp SYCL builds on the author's three-card B70/B65/B60 rig.
- **AMD filled in Zen 6 details** post-launch: 12-core CCDs (up to 32 for Zen 6c), a claimed 27% average IPC gain (31% on AI workloads), and a pointed pushback on NVIDIA/Arm "agentic CPU" marketing.
- **SGLang shipped a 48-commit afternoon wave** including AMD Muse Spark Open ROCm day-0 support, GPTQ/AutoRound INT4 enablement on AMD, and MiniMax-H3-Image deployment performance docs; **Ollama cut v0.40.17-rc0** with Vulkan-iGPU mmap and concurrent MLX generation.
- **RustingStrata answered the "speed = quality loss" critique** with a measured perplexity/KLD study: +29% decode over llama.cpp at near-identical output quality on an RTX 3060.

---

## AI hardware and silicon

**Apple's Claris is building Harbor, a local AI inference server for Macs with Thunderbolt 5 memory pooling** — event date: Tuesday, Nov 10, 2026
Claris — the FileMaker company owned by Apple — is offering early access to "Harbor," an app that turns an Apple silicon Mac into a private AI inference server for open-weight models. The official Claris page (live today) describes zero cloud fees, no per-token billing, single-Mac endpoint setup "in minutes," and — the headline feature — connecting two or more Thunderbolt 5 Macs to pool their unified memory into one inference resource for larger models or higher concurrency. MacRumors (3:34 PM PST) adds that Harbor auto-detects TB5 devices to form a cluster, includes an in-app model browser/downloader, requires no command line, and leans business-focused (the sign-up form asks for a company name); an App Store listing briefly appeared and was pulled. Apple shipped RDMA-over-Thunderbolt 5 in macOS Tahoe 26.2, which is what makes the clustering fast.
*Why it matters:* This is an Apple subsidiary productizing exactly what Exo and LM Studio do separately — cluster Macs, serve open weights — with a no-config GUI and enterprise positioning. If Harbor ships broadly, the Mac becomes a first-class, vendor-sanctioned local-inference node, and Thunderbolt-5 memory pooling becomes a competitive axis against DGX-class boxes.
*Local inference:* Runs on Apple silicon Macs; clustering requires TB5-capable models (M5 Pro-class and newer); pooled memory targets models exceeding any single Mac's unified-memory budget (128GB+ territory per machine).
Source: MacRumors — "Apple's Claris Testing 'Harbor' App for Local AI on Macs," Nov 10 2026, 3:34 PM PST — https://www.macrumors.com/2026/11/10/apple-claris-harbor-app/
Source: Claris (official product page, live Nov 10) — https://content.claris.com/local-ai-server-get-started
Source: 9to5Mac — "Apple has new Mac app coming to make running local AI models easy," Nov 10 2026 — https://9to5mac.com/2026/11/10/apple-has-new-mac-app-coming-to-make-running-local-ai-models-easy/
Source: AppleInsider — "Apple's Harbor app will make running local AI across multiple Macs trivial," Nov 10 2026 — https://appleinsider.com/articles/26/11/10/apples-harbor-app-will-make-running-local-ai-across-multiple-macs-trivial

**AMD details Zen 6: 12-core CCDs, 27% average IPC claim, up to 256 EPYC cores** — event date: Nov 10 (Update on previously reported: "EPYC-9006-SP7-availability," reported Nov 10 12:45)
With Venice shipping, AMD published its architecture deep-dive: Zen 6 moves to 12-core CCDs (Zen 6c up to 32 cores per CCD), four-MRDIMM-12800 support on 16 channels, PCIe 6.0, and high-frequency parts at 5.3 GHz, topping out at 96 Zen 6 or 256 Zen 6c cores. The headline claim is 27% average IPC over Zen 5 in selected server workloads — but note the methodology VideoCardz flags: 16 Zen 6c cores on one CCD versus 16 Zen 5 cores spread across two CCDs at matched clocks (~20% enterprise/cloud, ~31% AI workloads), so it is not a single-core comparison and says nothing confirmed about Ryzen. TechPowerUp's same-day Zen 6 Technical Deep Dive corroborates the wider execution window and 4MB L3 per core.
*Why it matters:* The 12-core CCD is the number to watch beyond the server: it lets AMD lift Ryzen core counts without extra chiplets, and the AI-workload IPC delta matters for anyone hosting agents on CPU-heavy serving nodes.
*Local inference:* Server/host silicon — most relevant as the agentic-inference host CPU tier (SP7, 16-channel DDR5-8000/MRDIMM-12800); no consumer-card implications announced today.
Source: VideoCardz — "AMD Zen 6 architecture detailed with 12-core chiplets and up to 256 EPYC cores, 27% higher IPC," Nov 10 2026, 18:26 GMT — https://videocardz.com/newz/amd-zen-6-architecture-detailed-with-12-core-chiplets-and-up-to-256-epyc-cores-27-higher-ipc
Source: TechPowerUp — "AMD Zen 6 Technical Deep Dive," Nov 10 2026 — https://www.techpowerup.com/ (reviews front page)
Source: ServeTheHome — "AMD EPYC 9006 Results Are Here and AMD Delivers a Knockout Part Deux," Nov 10 2026 — https://www.servethehome.com/amd-epyc-9006-results-are-here-and-amd-delivers-a-knockout-part-deux/

**AMD pushes back on NVIDIA and Arm "agentic CPU" marketing** — event date: Nov 10, 2026, ~7:35 PM EST
Madhu Rangarajan, AMD corporate VP of Compute and Enterprise AI, said at a media session (covered by Tom's Hardware and TechPowerUp) that EPYC 9006 "Venice" was never purpose-built around agentic AI — a jab at NVIDIA's Vera and Arm's identical positioning — while still calling 9006 "the best CPU for the agentic era." Chief Zen architect Mike Clark separately admitted AMD "didn't see agentic computing coming," framing the CPU's AI role as handling diverse workflow tasks rather than any agent-specific hardware feature.
*Why it matters:* Cross-vendor CPU marketing is now explicitly fighting over the AI-agent host slot; expect host-CPU selection (core count, MRDIMM bandwidth, PCIe6) to become a real differentiator in inference-node pricing.
*Local inference:* Peripheral for homelabs, but it validates buying high-core-count standard server CPUs for agentic serving rather than "AI CPUs."
Source: TechPowerUp — "AMD Pushes Back on NVIDIA and Arm's 'Agentic CPU' Marketing," Nov 10 2026, 19:35 EST — https://www.techpowerup.com/

## Model releases

No new open-weight base releases landed in this window beyond the previously reported MiniMax-H3-Image (12:45 edition); community quant activity covered that plus new finetune families below.

## Community spins and quantizations

**XeMaya: a native Intel Arc engine for DeepSeek V4.1-Flash, GLM-5.3-Flash and Qwen3.8-Flash-Next** — event date: Nov 10, 2026 (posted ~7:45 PM EST)
Developer Jumbomuffin1312 unveiled XeMaya, a fork of XeStrata built to run flagship MoE models natively on Intel Arc via SYCL and Level Zero — the "Project Maya for Intel" the Arc community has been missing, replacing patched llama.cpp builds. Author-reported numbers on his rig (Arc Pro B70 32GB + B65 32GB + B60 24GB, Ryzen 9950X, 128GB RAM): GLM-5.3-Flash (Maya-S v2 IQ2_XXS) prefill/generation moved from 68.3/13.6 tok/s on stock llama.cpp SYCL to 152–161/17.4 (~20 with MTP); DeepSeek-V4.1-Flash-Coder (GSQ-RCO IQ2_XXS) from 48.5/16.3 to 224.8/21.7; Qwen3.8-Flash-Next (IQ3_S) trails Strata slightly (1,842–1,884 vs 2,211 prefill). He also found two GPUs beat three for the 384-expert model because the B60 sits on a slower chipset link, and Engram files (~110.6GB) are SSD-mmap'd rather than VRAM-resident.
*Why it matters:* Intel Arc has been a second-class citizen for the newest MoE architectures; a purpose-built SYCL/L0 engine with MTP and multi-GPU expert handling makes the B60/B65/B70 price-per-VRAM story genuinely competitive for long-context MoE serving.
*Local inference:* Targets Intel Arc Pro B-series (24–32GB per card), multi-GPU + large system RAM + SSD for Engram tables; iGPU support theoretically possible but untested by the author.
Source: r/LocalLLaMA — "XeMaya, an Intel Arc inference engine to run DeepSeek V4.1 and GLM 5.3," Nov 10 2026 — https://www.reddit.com/r/LocalLLaMA/comments/1xt2mjk/xemaya_an_intel_arc_inference_engine_to_run/

**RustingStrata posts a quality-vs-speed rebuttal: 29% faster than llama.cpp at essentially identical output** — event date: Nov 10, 2026 (Update on previously reported: "RustingStrata," first reported Nov 4)
Serious_Scarcity7818 answered the recurring "these engines cheat on quality" objection with a controlled test: the same Qwen3.8-Flash-Next IQ2_XS file, same prompts, 128-token replies on an RTX 3060 12GB. RustingStrata: 33.0 tok/s decode (29.3–35.8 range) vs llama.cpp 25.6; first-prompt read 109 vs 31 tok/s. Over 9,180 WikiText-2 tokens: perplexity 2.9212 vs 2.9156, 96.78% same most-likely next token, mean KLD 0.0098 — within noise, per the author's own framing ("one model, one quant, one card"). Commenters pushed back that vanilla Strata with DFlash2 hits ~60 t/s at IQ2_S, so the honest comparison set is still missing.
*Why it matters:* Engine-level quality fidelity (KLD/perplexity vs a reference engine) is becoming the accepted bar for new local runtimes — the same rigor Basalt and Strata threads have established — and it's now coming from engine authors themselves.
*Local inference:* Demonstrated on 12GB RTX 3060 + 32GB DDR5; the point of RustingStrata is low-VRAM Qwen/GLM/DeepSeek MoE serving.
Source: r/LocalLLaMA — "Does Strata like projects trade accuracy for speed?," Nov 10 2026 — https://www.reddit.com/r/LocalLLaMA/comments/1xsw9mr/does_strata_like_projects_trade_accuracy_for_speed/

**Day-0 quant ladder: Gork-27b-Cybersecurity, Qwen3.8-40B NEO Build2, Sage-A1.5-Concise, Ling-3.0-tiny-CuteHuman** — event date: Nov 10, 2026
mradermacher pushed four fresh GGUF ladders inside the window (Hugging Face API creation timestamps): Gork-27b-Cybersecurity-GGUF (18:37 EST) plus its imatrix i1 sibling (19:12 EST) — a Qwen3.8-27B-based security/t threat-response agentic finetune from ConsensusProtocol-Thanos using heimdallm consensus tooling; Qwen3.8-40B-Ultimate-Heretic-Uncensored-Build2-i1-GGUF (15:39 EST), the next build of DavidAU's expanding-architecture heretic line; Sage-A1.5-Concise-GGUF (19:23 EST), an efficient-reasoning RL model from TheFusionCube; and Ling-3.0-tiny-CuteHuman-i1 (13:11 EST), a persona spin of Ant's tiny BailingMoE3.
*Why it matters:* The cybersecurity-agentic tier keeps densifying on Qwen3.8-27B — Gork joins Arete, Frost-Cyber-Mini and Industry-Coder — and Build2 signals the 40B NEO expansion is iterating weekly.
*Local inference:* All standard GGUF for llama.cpp-class engines: 27B Q4_K_M ≈ 16GB class (fits a 5060 Ti 16GB with KV headroom at moderate ctx); tiny-class repos run anywhere.
Source: Hugging Face — https://huggingface.co/mradermacher/Gork-27b-Cybersecurity-GGUF (created Nov 10 23:37 UTC)
Source: Hugging Face — https://huggingface.co/mradermacher/Qwen3.8-40B-Ultimate-Heretic-Uncensored-Build2-i1-GGUF (created Nov 10 20:39 UTC)
Source: Hugging Face — https://huggingface.co/mradermacher/Sage-A1.5-Concise-GGUF (created Nov 11 00:23 UTC)

**Deletion Tuning: zeroing <0.05% of weights matches full fine-tuning** — event date: surfaced on r/LocalLLaMA Nov 10, 2026 (NeurIPS 2026 poster)
A Princeton-affiliated agentic-learning team showed that learning a binary mask over transformer gate projections — zeroing fewer than 0.05% of weights, nothing else changed — matches full fine-tuning across RLVR, SFT, and on-policy distillation, while forgetting far less (prior-task accuracy near base vs up to 5.4 points lost for FFT/LoRA/random-sparse under RL). The mask artifact is hundreds-to-thousands of times smaller than the resulting checkpoint, and geometry analysis (stable rank, spectrum drift, principal-weight overlap) puts it closest to FFT of all sparse methods. Code on GitHub (agentic-learning-ai-lab/deleting-weights), masks on Hugging Face.
*Why it matters:* For the local community this is the strongest "distribution-as-delta" result yet — capability updates that ship as a few megabytes of mask instead of 50GB checkpoints — and in-thread it's already being mapped onto the heretic/abliteration workflow (p-e-w noting it slots into Heretic's plugin architecture). It's also the third weight-editing result this week after MIT's weight-space programming and the neuron-map posts.
*Local inference:* Engine-agnostic post-training technique; immediate fit is tuning local quants without re-merging full models.
Source: r/LocalLLaMA — "The Surprising Effectiveness of Deleting Weights in LLM Post-Training," Nov 10 2026 — https://www.reddit.com/r/LocalLLaMA/comments/1xswmb7/the_surprising_effectiveness_of_deleting_weights/
Source: project page — https://agenticlearning.ai/deleting-weights/

## Inference software and benchmarks

**SGLang 48-commit afternoon wave: AMD Muse Spark Open day-0, GPTQ/AutoRound INT4 on ROCm, gfx1250 MLA decode** — event date: Nov 10, 2026, 12:45–8:30 PM EST
Highlights from GitHub's commit feed: ROCm day-0 implementation for Muse Spark Open (#47371); GPTQ and AutoRound INT4 checkpoint support on AMD (#42119); aiter's Triton MLA decode on gfx1250 consumer/workstation RDNA (#44458); DeepSeek-V4.1 Engram 16-row gather kernels for SM103/SM107 (#48731); opt-in MXFP8 draft lm_head for DSpark on SM10x (#49595); GLM-5.3-Flash B300 TP4 AgentX tuning results (#49071); StepFun models (Step-3.5/3.7-Flash, Step-5-Preview) rendered through sglang-processor in the router (#49392); Kimi-K3 wire-format chat shaping (#48777); measured MiniMax-H3-Image deployment performance docs (#49593); CUDA 13.4 nightly images published for x86_64 and aarch64 (#47999).
*Why it matters:* The AMD column keeps compounding — day-0 MoE support, INT4 formats, and tuned MLA decode on gfx1250 are precisely what Radeon AI Pro cards need to be viable Qwen/DeepSeek serving platforms.
*Local inference:* Direct gains for RDNA4 (gfx1250) and consumer Blackwell (SM12x) users; CUDA 13.4 nightlies cover GB300-class through Spark-class boards.
Source: GitHub — sgl-project/sglang commit feed, Nov 10 2026 17:45Z–01:08Z — https://github.com/sgl-project/sglang/commits/main

**vLLM afternoon wave: Kimi-K3 Mono decode on MI355X, GLM-5.3 MTP GEMM fix, NVFP4-KV layout for MiniMax-M3** — event date: Nov 10, 2026
From the GitHub commit feed: a Mono decode layer for Kimi-K3 on MI355X with vanilla decode (#61498); Kimi-K3.1-Pro GB300 AgentX presets aligned to InferenceX (#66642); a low-latency GEMM plan fix for GLM-5.3's MTP eh_proj (#65704); BLHNC KV-cache layout allowed with NVFP4 KV for MiniMax-M3 (#66892); DomynEdge now mixes sliding and full attention layers (#66819); external KV connectors can declare KV-cache memory registration (#66605); plus a security fix pruning quadratic attribute scans in Muse tool-call extraction (#62345).
*Why it matters:* ROCm-side Kimi-K3 decode and GB300 agent presets keep shrinking the gap between AMD DC cards and NVIDIA for frontier MoE serving; the KV-registration hook matters for anyone building custom offload tiers.
*Local inference:* Mostly DC-class (MI355X/GB300), but the MiniMax-M3 NVFP4-KV and GLM-5.3 MTP fixes benefit the same models run on local SM120/SM121 boxes.
Source: GitHub — vllm-project/vllm commit feed, Nov 10 2026 17:45Z–01:08Z — https://github.com/vllm-project/vllm/commits/main

**Ollama v0.40.17-rc0: mmap'd Vulkan on integrated GPUs, concurrent MLX generation** — event date: Nov 10, 2026, 5:25 PM EST
The rc published 22:25 UTC with four functional changes: `llm: use mmap for Vulkan integrated GPUs` (#19640), concurrent generation support for the MLX backend (#19197), a llama.cpp bump to b12257 plus MLX/MLX-C bumps, and improved ChatGPT CLI restore/graceful restart.
*Why it matters:* iGPU mmap is a real quality-of-life unlock for NPU/APU-class machines (Strix Halo, Panther Lake boxes) running Ollama on Vulkan, and MLX concurrency makes Apple silicon multi-user serving practical without third-party wrappers.
*Local inference:* Vulkan iGPU rigs (AMD 8060S-class, Intel Arc iGPUs) and Apple silicon Macs are the direct beneficiaries.
Source: GitHub — ollama/ollama release v0.40.17-rc0, published Nov 10 2026 22:25 UTC — https://github.com/ollama/ollama/releases/tag/v0.40.17-rc0

**llama.cpp minor afternoon wave + b12257–b12262** — event date: Nov 10, 2026
Quiet-but-useful merges: Muse Spark no longer ignores `response_format` json_schema (#32017), the UI Discover tab now surfaces audio for models with an audio-encoder mmproj (#31837), and mtmd got a format-string cleanup/rename (#32040). Builds b12257–b12262 shipped 21:54–23:04 UTC.
*Why it matters:* Structured-output compliance on Muse Spark closes an agent-harness papercut; audio discovery in the UI keeps the multimodal surface consistent.
*Local inference:* Applies to every llama-server deployment; Muse Spark users get spec-compliant JSON mode.
Source: GitHub — ggml-org/llama.cpp commits and releases, Nov 10 2026 — https://github.com/ggml-org/llama.cpp/releases

---

## Also noted

- **HF API timestamps confirm the quant economy's pace**: Gork-27b-Cybersecurity went from base-model exposure to full GGUF + i1 ladders in roughly an hour this evening (huggingface.co/mradermacher).

## Just missed the window

- (missed:) **MINISFORUM's $1,559 Ryzen AI Max+ 388 mini PC** — VideoCardz, Nov 9 ~9:58 AM EST: Radeon 8060S with 40 CUs, but memory speed cut ~20% versus the originally announced spec — the bandwidth hit matters for Strix Halo-class inference. https://videocardz.com/newz/minisforums-new-1559-gaming-mini-pc-features-ryzen-ai-max-388-but-memory-speed-has-been-reduced *(headline reference; VideoCardz home listing Nov 9)*
- (missed:) **llama.cpp on Tesla K80/Kepler: "3× faster"** — larfus303's nc-dequantize fork hit Hacker News at 20:12 EST, four minutes past window end. https://github.com/larfus303/llama-cpp-kepler-k80-nc-dequantize

## Coverage notes

- **Verified items: 12** (4 hardware/silicon incl. 1 update, 5 community/engine stories, 3 software waves) — every one fetched and page-timestamp-confirmed inside 12:45–20:08 EST, except two r/LocalLLaMA threads verified by creation-order bounding and one project page (Deletion Tuning) cited as "surfaced today."
- **Ledger dedupe: ~15 candidates suppressed** as already reported or already footnoted: MiniMax-H3-Image rL wave, TTS-bench, Startlux-4xRPi5, Elixir/OCaml/Haskell bench, ConeML 348M, DavidAU 40B NEO (Build2-i1 quant treated as new tier), Fz1zz Strata fork, Ren-WuJun CUDA+ROCm Maya, pwilkin external-PLE PR (11 re-coverage) + repeat-missed items Bielik-11B-v3.1, Comet-60B GGUF, Supermicro Super AI Station, Google TPU 8tx (4).
- **Dead ends:** HF `api/models?sort=createdAt` returned unusable noise (dropped in favor of per-author `sort=lastModified`); DuckDuckGo backend errored on two of three early searches (nous fallback served); anonymous Reddit feed lacks scores/timestamps (used base-36 ID ordering against known-in-window anchors).
- **Budget:** run started 20:08, research concluded ~20:35 (~27 min — slightly over the tightened 20-min target but within all phase walls; no rabbit holes chased).
