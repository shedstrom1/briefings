# AI Hardware & Local Models Brief — SC26 Wave Lands (Nov 16, 15:40 ET catch-up)

**Window: 2026-11-16 07:45 → 15:40 ET** (operator-authorized catch-up for the failed 12:45 slot; all events first disclosed inside this window, deduped against the reported ledger)

**TL;DR**
- NVIDIA's SC26 drop (published 11:00 ET): **Rubin HPC** — a 50,000+ CUDA-core simulation GPU with NVLink 7, shipping 2028 — plus the **Vera CPU rack-scale platform** (192 custom Olympus-core CPUs per rack, early 2027).
- **MLPerf Training v6.1** results went live today: first-ever NVIDIA Rubin and Google TPUv7 (Ironwood) submissions, 153 systems from 24 orgs, and a brand-new Qwen3.5-397B GRPO post-training benchmark; NVIDIA claims a sweep of Training and Endpoints.
- The **Commerce Department issued an interim rule** requiring export licenses for AI chips/servers destined for any data center that can serve compute to China and other D:5 countries — the biggest AI-hardware export-control change since the Apr '25 H20 rules.
- **AMD's Lux supercomputer** (MI355X + EPYC + Pensando, built by HPE) opened to scientific users at Oak Ridge — No. 14 on the new TOP500, first dedicated AI Factory for the DOE Genesis Mission.
- On the local front: **AMD's Loom** stack posted average 42% speedups over llama.cpp across 48 models on a Radeon AI PRO R9700, and NVIDIA shipped **Nemotron for Science** with a 128GB-DGX-Spark-optimized Nemotron 3.5 Super VL.

---

## AI hardware and silicon

**NVIDIA announces Rubin HPC: 50,000+ CUDA cores, NVLink 7, 2028 availability** — Event date: November 16 (SC26 embargo lift, 11:00 ET)
Announced at SC26 in Chicago, Rubin HPC is a new member of the Vera Rubin platform aimed at simulation and data processing rather than AI training. NVIDIA confirms more than 50,000 CUDA cores on a single GPU — a company record — paired with a "sparse data acceleration" block that keeps the cores fed for stencil, sparse-matrix, and sort/reduce patterns, and seventh-generation NVLink for GPU-to-GPU bandwidth. No memory configuration or FLOPS figures were disclosed; availability is stated as 2028.
*Why it matters:* A CUDA-core count this deep implies either a dual-die package or a chiplet design beyond Rubin's 889-billion-transistor AI part, and it's the first NVIDIA GPU explicitly positioned for agentic HPC loops (hypothesis → simulate → refine). The missing memory specs are the number to watch in the coming months.
*Local inference:* Not a local product — but the NVLink 7 and CUDA-X agent-library stack it debuts with is the same software spine the RTX Spark/DGX-class local tools run on; 2028-era HPC pricing puts it firmly in datacenter territory.
Source: NVIDIA Blog, "NVIDIA Announces Rubin HPC," Nov 16, 2026 — https://blogs.nvidia.com/blog/rubin-hpc/
Source: Tom's Hardware — https://www.tomshardware.com/pc-components/gpus/nvidia-announces-rubin-hpc-gpu-with-record-number-of-cuda-cores-at-supercomputing-2026-new-chip-accelerates-agentic-hpc-workloads-arrives-in-2028
Source: VideoCardz — https://videocardz.com/newz/nvidia-announces-rubin-hpc-gpu-with-over-50000-cuda-cores-and-nvlink-7-launching-in-2028

**NVIDIA Vera CPU rack-scale platform: 192 custom Olympus-core CPUs per rack, shipping early 2027** — Event date: November 16 (11:00 ET)
Each Vera CPU carries 88 custom NVIDIA Olympus cores (176 threads) with SOCAMM2 LPDDR5X delivering up to 1.2 TB/s per socket; the liquid-cooled MGX rack packs 192 of them, ~300 TB of memory and 1 PB of storage behind Quantum-X800 InfiniBand with SHARP in-network computing, exceeding 200 TB/s aggregate bandwidth. NVIDIA claims 1.9x performance and 2.4x performance-per-watt vs x86 on OpenFOAM motorBike and projected 1.6x/2x on WRF climate modeling; Dell, HPE, Lenovo and Supermicro sell it from early 2027, and Germany's DKRZ has already picked Vera for its HLRE-5 climate machine (with GB200 systems, available early 2028). HPC SDK 26.11 and a public-beta next-gen Fortran compiler ship alongside.
*Why it matters:* This is Grace's successor lineage moving from CPU+GPU superchips to a standalone CPU rack that reuses NVL72 chassis/power/cooling — the strongest signal yet that NVIDIA intends to own the whole rack bill of materials, including the memory-centric x86 workloads that feed agent pipelines.
*Local inference:* No local angle directly, but SOCAMM2 LPDDR5X at 1.2 TB/s per socket is the same memory format class that DGX Spark and the coming Arm workstations normalize — expect it to leak down and grow the unified-memory ceiling for big local models.
Source: NVIDIA Newsroom, Nov 16, 2026 — https://nvidianews.nvidia.com/news/nvidia-expands-vera-cpu-platform-to-accelerate-scientific-discovery

**US tightens AI-chip export controls to block remote access from China (interim rule)** — Event date: November 16 (10:02 ET)
The Commerce Department's interim final rule requires licenses to export, re-export, or transfer AI chips and servers to any data center worldwide that may provide compute to companies in the D:5 group (China, Russia, Iran, North Korea +18). It triggers at 12 million total processing performance — roughly the peak of about 750 H100/H200s — covers indirect access through brokers, adds new licensing for Malaysia, Singapore, Indonesia, Thailand and Brazil (with a carve-out for US-destined chips), gives existing operators 90 days to comply, and formally rescinds the January 2025 AI Diffusion Rule. Bloomberg and the Times both report the move landed days after Jensen Huang's resignation and the Megaspeed scrutiny.
*Why it matters:* Location-of-chip has been the control surface; this extends jurisdiction to who the compute serves — effectively a remote-access kill switch for the "buy in SEA, serve in Shenzhen" playbook that followed earlier rounds, and it lands right on NVIDIA's leadership vacuum and Nov 18 earnings.
*Local inference:* Homelab-relevant in the aggregate: SEA/Malaysian-warehouse gray-market supply has been a channel for workstation cards; added licensing friction there tends to push enterprise-card pricing up, while consumer/local hardware (RTX 50-class, DGX Spark, Arc Pro) is unaffected on its face.
Source: TechCrunch, Ram Iyer, Nov 16, 2026 — https://techcrunch.com/2026-11-16/us-tightens-ai-chip-export-controls-to-deter-chinese-companies-from-accessing-compute-remotely/
Source: Bloomberg — https://www.bloomberg.com/news/articles/2026-11-16/us-moves-to-limit-remote-access-to-advanced-ai-chips
Source: The New York Times — https://www.nytimes.com/2026/11/16/us/politics/trump-china-ai-chip-loophole.html

**AMD's Lux supercomputer opens at Oak Ridge — No. 14 on TOP500, first Genesis Mission AI Factory** — Event date: November 16 (14:00 ET)
AMD, ORNL and HPE opened the Lux SI+HPC system to scientific users: AMD EPYC CPUs, Instinct MI355X GPUs, Pensando networking and ROCm software, ranked 14th on the TOP500 released today (220.79 PFlop/s HPL per StorageReview). It's the first dedicated U.S. AI Factory for the DOE's Genesis Mission, with first allocations spanning fusion power-plant design, materials development, and Earth-system prediction.
*Why it matters:* Every MI355X sold into Lux validates the CDNA4 ROCm stack at national-lab scale — the same stack that now runs the community's AMD inference engines (Basalt, saber, Strata on AMD rigs), and the Genesis Mission is a demand valve for the MI355/MI400 roadmap AMD guided to ~$17B Q4.
*Local inference:* No single-user angle, but Lux-class MI355X deployments are what fund the ROCm kernel work that flows into gfx1201/1250 (R9700/Strix) local performance.
Source: AMD Newsroom, published Nov 16, 2026 1:00 PM CST — https://newsroom.amd.com/news/amd-powered-lux-supercomputer-opens-scientific-users/
Source: TOP500 November 2026 list — https://top500.org/lists/top500/2026/11/

**Update on previously reported — Intel Data Center GPU D270 (ledger: GPU-D-Series-D270, Oct 12): Intel publishes first own-side benchmarks** — Event date: November 16 (~12:40 ET)
Intel released GPU D270 comparison results against NVIDIA's RTX PRO 6000 Blackwell (600W): claims of up to 5.9x in FP64 scientific tests, with "smaller gains in AI inference," from Intel's 350W Crescent Island card (480 GB LPDDR5X). VideoCardz carried the news in-window.
*Why it matters:* The Oct 12 OCP reveal gave specs only; this is Intel's first concrete competitive claim post-resignation-of-Jensen market turbulence — and the FP64-led framing concedes the AI-inference crown while attacking power and memory capacity instead.
*Local inference:* D270 is a datacenter card, but Intel's Xe3P kernels feed the same Arc Pro B-series (B60/B65/B70) ecosystem the community serves Qwen/DeepSeek quants on via SYCL.
Source: VideoCardz, Nov 16, 2026 (17:40 UTC listing timestamp) — https://videocardz.com/ (news river; article "Intel claims its 350W D270 GPU is up to 5.9x faster than NVIDIA's 600W RTX PRO 6000 Blackwell")

## Model releases

**NVIDIA launches Nemotron for Science — open chemistry dataset + Nemotron 3.5 Super VL tuned for DGX Spark 128GB** — Event date: November 16 (11:00 ET)
The bundle pairs Nemotron 3.5 Super VL with LitmusBench v0.2 (~50,000-question open chemistry dataset for training and eval) and post-training recipes; all available on Hugging Face today. NVIDIA cites a KTH Royal Institute agent that repaired 81% of broken GROMACS molecular-dynamics setups in one overnight run on a single DGX Spark (every fix independently verified by GROMACS), Lila Sciences hitting 61% quantum-dot recipe accuracy vs GPT-6 Astra's 54% "at 10-20x lower inference cost," and Argonne/RIKEN/Jülich integrations. Super VL was explicitly "optimized to run locally on a 128GB NVIDIA DGX Spark system."
*Why it matters:* It's the clearest vendor proof yet that 120B-class open MoE agents are desktop hardware now — and an open 50k-question chemistry eval is immediately reusable by the fine-tune community (the same pattern that made NanoChat-style benchmarks spread in days).
*Local inference:* DGX Spark 128GB is the named target; the Super VL MoE also runs on 4x RTX 5090-class or 1x PRO 6000 96GB boxes with expert offload, and day-0 quants typically land within 24h from mradermacher/Unsloth.
Source: NVIDIA Newsroom, Nov 16, 2026 — https://nvidianews.nvidia.com/news/nvidia-introduces-nemotron-for-science-to-accelerate-discoveries-with-agents

## Community spins and quantizations

**mradermacher's daytime ladder: Mirage 35B-A3B merge, USS-Emergent 9B, Smaug Gemma-4-12B, six new repos + three new tiers** — Event date: November 16 (11:21–15:40 ET)
In-window uploads (Hub createdAt): Qwen3.6-35B-A3B-Mirage GGUF + i1 (nightmedia's mergekit/LoRA reasoning merge incl. Fable traces; 16:39/16:53Z), Qwen3.5-9B-USS-Emergent-i1 (16:36Z), Gemma-4-12B-Uncensored-i1 from abacusai's Smaug DPO (17:26Z), Lythri-Wink-2B on-device companion (16:59Z), Lanternfall-4B roleplay SFT/DPO (18:49Z), Temper-2-Sapient-i1 (Rust/code, 17:44Z), HeTong-NMT-7B-i1 (14:14Z), plus new i1/GGUF tiers on previously reported repos (Qwen3.8-27B-Perplexing-Pegasus-Heretic 17:51Z, experimental_merge_241026_v2-i1, Nemotron-3.5-Bolt-E4B-BF16-heretic-3-i1).
*Why it matters:* The Mirage/USS lines keep extending the Qwen3.6-35B-A3B class that now dominates sub-40GB agent rigs, and the Perplexing-Pegasus tier closes the loop on the morning's rL unveiling.
*Local inference:* All Q4-and-below tiers fit 16GB cards (5060 Ti/4060 Ti 16GB); Mirage 35B-A3B Q4 lands ~20-21GB for 24GB cards; 2B/4B on-device entries target phones and 8GB mini-PCs.
Source: Hugging Face API (createdAt verified), e.g. https://huggingface.co/mradermacher/Qwen3.6-35B-A3B-Mirage-GGUF , https://huggingface.co/mradermacher/Gemma-4-12B-Uncensored-i1-GGUF , https://huggingface.co/mradermacher/Qwen3.8-27B-Perplexing-Pegasus-Heretic-GGUF

## Inference software and benchmarks

**MLCommons releases MLPerf Training v6.1: first Rubin and TPUv7 results; NVIDIA sweeps Training + Endpoints (lead)** — Event date: November 16 (~9:00–11:00 ET release)
v6.1 adds two benchmarks — DLRMv4-HSTU (recommender on the Yambda dataset) and Qwen3.5-397B, the suite's first large-scale GRPO post-training workload (tool-calling/reasoning SWE tasks) — and draws record participation: 24 organizations, 153 systems, 250 results, 37% cloud, twelve systems at 1,000+ accelerators (largest 10,240). It's the first round with NVIDIA Rubin-generation GPUs and Google TPUv7 (Ironwood) on the board; NVIDIA separately claims it swept every MLPerf Training v6.1 benchmark and led the Endpoints debut, per its same-day blog.
*Why it matters:* A GRPO post-training benchmark is the industry admitting agentic capability is bought in the RL stage, not pretraining — hardware value now hinges on rollout throughput; and first Rubin numbers make the new silicon comparable to MI355X and Ironwood on a level field.
*Local inference:* The Qwen3.5-397B workload's tool-calling GRPO recipe is exactly the training mode local labs replicate at smaller scale; Rubin-vs-Ironwood-vs-MI355X ratios inform what the next refresh of 128GB–256GB local boxes gets priced against.
Source: MLCommons, Nov 16, 2026 — https://mlcommons.org/2026/11/mlperf-training-v6-1-results/
Source: NVIDIA Blog (sweep claim + Slurm/CUDA-X items), Nov 16, 2026 — https://blogs.nvidia.com/sc26-news/

**Update on previously reported — AMD Loom (ledger: hrx-loom-strix-top51, Nov 13): Radeon AI PRO R9700 support, average 42% over llama.cpp across 48 models** — Event date: November 16 (12:10 ET)
AMD's jfowers_amd posted Loom results on the R9700 32GB: the top-51 HF trending models trimmed to the 48 that fit in 32GB, averaging +42% over llama.cpp on decode+prefill (Flint 6,476 vs 3,190 tok/s pp32k and 255 vs 152 decode; gemma-4-12B-it 2,811 vs 1,537 pp), with all kernels generated via agent "loom-skills" and full tables on loom-fun.github.io. A follow-up adds an MXFP4 repack (Quark-AWQ Qwen3.8-27B: 2,790 t/s prefill, vLLM baseline); Loom kernels are being integrated into Gufo and a llama.cpp backend; on the one model common with Strata (Qwen3.6-35B-A3B) he calls it "roughly a tie." RDNA2 is unsupported; a 9070 XT pass is planned.
*Why it matters:* This is the first AMD-employee-led stack showing across-the-board double-digit wins on RDNA4 workstation cards — measured on the community's actual trending models rather than a demo — and it pressures Basalt/Strata/saber on the same silicon.
*Local inference:* Directly on target for R9700 32GB (and its 4-card homelab rigs, cf. Nov 8/9 entries); Q4_K_M models that fit 32GB benefit today, with mxfp4 next.
Source: r/LocalLLaMA (u/jfowers_amd), Nov 16, 12:10 ET — https://www.reddit.com/r/LocalLLaMA/comments/1xxuzgg/amd_loom_fun_added_radeon_9700_support_over_the/ ; tables: https://loom-fun.github.io/loom-dev/index.html?gpu=radeon-r9700 ; https://github.com/loom-fun/loom-skills , https://github.com/ROCm/hrx-system

**llama.cpp b12438–b12445 (19 in-window merges): Inkling 2 chat support, YuE2-3B, Fish Audio S2, CDNA GDN MMA, multi-device tensor-split** — Event date: November 16 (07:49–15:44 ET)
Between builds 12:49Z and 20:44Z: Inkling chat parser #32381 + inkling-2 mmproj conversion fix #32430 (finishing the Nov 15 day-0 work), mtmd support for YuE2-3B music (#32369) and Fish Audio S2 (#32330), WebGPU per-head FA masks (#32394), HIP/CUDA GDN mma kernel enabled on CDNA (#31327), three OpenCL bin flash-attention defect fixes (#32110) plus a new Adreno ILA q4_k kernel (#30948), --tensor-split/--fit-target now allow per-device configs up to llama_max_devices() (#31435), MMVQ gate tuning for RDNA3_5 (#32308), and Vulkan split_k for conv3d (#29929).
*Why it matters:* CDNA GDN + RDNA3_5 MMVQ + OpenCL fixes mean AMD and Qualcomm backends keep closing the gap that Strata/Loom built their pitches on, and multi-device tensor-split config is the plumbing multi-GPU offload engines need.
*Local inference:* Benefits: CDNA (MI50/MI250 class) GDN decode paths, RDNA3_5 (7800M/9700S-adjacent) MMVQ models, and multi-GPU rigs (3090+X combos) from per-device split control.
Source: GitHub ggml-org/llama.cpp releases + merged-PR search (verified timestamps) — https://github.com/ggml-org/llama.cpp/releases

**vLLM ~40 in-window merges: DSpine spec-decode support, online weight-only NVFP4, DeepSeek-V4.1 on A100/H100, LFM3-Omni day-0** — Event date: November 16 (07:45–15:40 ET)
Highlights (merged_at verified): DSpine draft-model support #67094 (the Tencent 5-layer drafter first footnoted Nov 8 — now first-class in vLLM), opt-in online weight-only NVFP4 for dense linear layers #67006, DeepSeek-V4.1 enabled on SM80 A100 and SM90 H100 #66497, LFM3-Omni model support #65829, ripple-65b ROCm recipes for MI350X/MI355X #67055, Kimi-K3 vision-tower encoder graph capture #63086, tuned AITER Triton FP8 decode GEMMs on gfx950 #65711, fused block-Hadamard rotation into AITER MXFP4 quant #63272, and skinny-GEMM RDNA4 (GFX12) extensions via SWMMAC #45559.
*Why it matters:* A100/H100 DSv4.1 support opens production-class serving on $1.5–3k used accelerators, and online NVFP4 weight-only quantization removes the pre-quantization step that slowed adoption on Blackwell.
*Local inference:* #45559 and #65711 land directly on RDNA4/RDNA2-class workstation cards; DSpine drafts are the same trick llama.cpp's DFlash line uses for 25-50% decode gains on local rigs.
Source: GitHub vllm-project/vllm merged-PR search (verified timestamps) — https://github.com/vllm-project/vllm

**SGLang (12) and TensorRT-LLM (14) in-window commits: routed experts as MXFP8, NPU W4A8 sharding, Kimi K3 SM107 MXFP8 kernel** — Event date: November 16 (07:45–15:40 ET)
SGLang merged dsv4 routed-experts-as-MXFP8 serving on block-wise FP8 checkpoints #49591, NPU W4A8 MoE scale_bias sharding by TP rank #40880, an MI35x AgentX DSpark nightly #50489, and Marlin WNA16 tile-padding fixes #48794. TensorRT-LLM merged Kimi K3 shared-experts through the SM107 MXFP8 FC12 kernel #21959, an SM103 NVFP4 GEMM scale-factor race fix #22287, and multimodal encoder timing/backend-selection work (#20739/#20802/#20011).
*Why it matters:* MXFP8 expert storage on block-FP8 checkpoints cuts MoE serving memory without a requant pass, and the SM107 (next-gen Blackwell/Rubin) kernel work shows NVIDIA pre-building the inference path for the same Rubin silicon that topped MLPerf today.
*Local inference:* SM103 fixes touch RTX PRO/Spark-adjacent Blackwell parts; W4A8 sharding is the quant class used by 16–32GB consumer cards in MoE rigs.
Source: GitHub sgl-project/sglang and NVIDIA/TensorRT-LLM commit feeds (verified timestamps) — https://github.com/sgl-project/sglang , https://github.com/NVIDIA/TensorRT-LLM

## Also noted

- **TOP500 November 2026 (SC26, today): China's LineShine holds No. 1** at 2.198 EF on custom LingKun LX2 (304-core) processors with LingQi interconnect and 42.2 MW; the US "chases AI-HPC fusion," no other new Chinese entries, TOP10 unchanged otherwise (top500.org; The Register covered same day). *Local inference:* HPL-first lists stay tangential, but LineShine's power envelope frames the "efficiency vs brute force" argument local MoE offload makes daily.
- **Hawking Runtime (new repo today 14:33 ET): NVIDIA GPU inference on Apple Silicon Macs via Thunderbolt** — a native macOS DriverKit eGPU driver + host runtime with CUDA kernels, plus a llama.cpp-based OpenAI-compatible server (repo fetched; HN 19:57Z, story_50522268). Early-stage, but the first credible DriverKit-based NVIDIA-on-Apple-Silicon path. *Local inference:* Promises Mac-unified-memory + spare NVIDIA card combos; unproven perf. https://github.com/hawking-systems/runtime
- **MSI EdgeXpert OTA2610 (vendor post 13:13 ET):** headless setups reclaim more usable memory and provisioning got easier — relevant to the EdgeMesa/RTX-Spark-class mini-PC fleet. https://www.reddit.com/r/LocalLLaMA/comments/1xxwn6z/
- **Google quietly fixed small Gemma 4 tokenizers (E2B/E4B/12B)** — the update corrects an issue causing nonsense output on certain tokens; community notes it may need re-converted GGUFs to propagate (r/LocalLLaMA, 13:55 ET). https://www.reddit.com/r/LocalLLaMA/comments/1xxxs0a/
- **AMD completes acquisition of World Labs** (AMD Newsroom listing dated November 16) — Fei-Fei Li's spatial/world-model shop joins Instinct's roadmap; watch for world-model workloads on MI400. https://newsroom.amd.com/
- **GIGABYTE next-gen laptop filings (EEC)** — AORUS Master 16 Gen 3 + "RTX Spark" systems and GPU codes pointing at a new RTX 50 refresh, with Nova Lake/Zen6 CPU options rumored (VideoCardz river, 19:20Z). Rumor-grade. https://videocardz.com/
- **GPD WIN Mini Ultra officially detailed** — 14-core Panther Lake + Intel Arc G3 Extreme (B390-class), up to 64GB LPDDR5X-8533, dual 80Gbps USB4 (VideoCardz river, "Just Posted"). The 64GB unified config is the interesting spec for 30B-class INT4 handheld inference. https://videocardz.com/

## Just missed the window

- **Show HN: Goethe — Rust inference server with own NVFP4/ternary kernels** (meshapplied, YC W27): Qwen3.8-27B NVFP4 at 255 tok/s c1 / 686 c4 on a single RTX 5090, OpenAI/Anthropic-compatible API; the Show HN landed at 15:52 ET, twelve minutes past the wall (repo pushed today; HN story_50523086). — footnoted once, never again
- **MachinePulse Karpo-Decision-31B "release" wave (rL 15:04 ET)** — claim checks show the 31B repo has been on the Hub since Nov 5 (family 0.8B–12B Nov 12, IQ3_M GGUF Nov 14); today's post is re-coverage anchored on the Nov 12 Decision Index #1 (73.9). — footnoted once, never again
- **NVIDIA Nemotron-Labs-Sigma 8B/3B continuous-diffusion LMs (rL 13:06 ET)** — repos created Oct 5 with distill/spec variants Nov 9 (arXiv 2610.02665); today's thread is rediscovery, not a release. — footnoted once, never again

## Coverage notes

- **Verified count: 19 items** (11 hardware/silicon incl. the D270 update + TOP500/also-noted, 2 model items, 1 community-ladder item counting 10 repos/tiers, 5 inference-software items) — counting unit: distinct events from the canonical manifest; the mradermacher ladder counts as one item (10 repos), the merge-waves count one per project.
- **Ledger dedupe: 9 candidates suppressed** as already reported or re-covered — morning-report repeats (Perplexing-Pegasus rL unveiling, experimental_merge GGUF, Bolt-heretic-3 base, Strata benchmark drama x3, CMP170HX fuse-unlock story which was also pre-window ~07:00 ET, Garlic PS4 re-coverage, Qwen4-Max AA-index discussion).
- Three new "Just missed" footnotes (Goethe, Karpo, Nemotron-Labs-Sigma) — all tagged for the ledger, never to be footnoted again.
- Dead ends / limitations: DuckDuckGo search backend was fully down ~15:10–16:30 ET (recovered late in the run — several second-source lookups were done via index pages instead); VideoCardz RSS/feed and article pages 403 both directly and via browser (river-only sourcing is flagged on the Intel D270 and also-noted items); ServeTheHome published nothing new today (latest = Nov 15 TE Connectivity interview); Ollama shipped nothing in-window (latest v0.40.17-rc1, Nov 10); vLLM had no release (latest v0.33.0rc3, Nov 2); the Register's per-article URLs could not be harvested (homepage links stripped by extractor, browser route bot-blocked) so Register is cited only where its headline+topic is corroborated by a fetched primary (TOP500, export rule).
- NVIDIA SC26 embargo lifted 11:00 ET (blog page states "Monday, Nov 16, 8:00 am PST") — all SC26 items sit inside the window; AMD Lux PR timed 1:00 PM CST = 14:00 ET.
