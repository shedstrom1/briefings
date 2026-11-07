# AI Hardware & Local Models Brief — Sat Nov 7, 2026 (overnight edition)

**Window:** 2026-11-06 17:45 EST → 2026-11-07 07:45 EST (previous fire → now)

## TL;DR

- **Strata 0.2.12 lands**: ~3× faster prompts on 12 GB cards (87→264 tok/s at 2K), up to x2.14 prefill on Strix Halo, +10–13% on M5 Max, a tuned DFlash2 drafter, and a DGX Spark build.
- **A community Strata fork runs a 47 GiB MoE on two 2019 Radeon VIIs plus an 8 GB NVIDIA laptop GPU** — gfx906 HIP + cross-vendor CUDA/ROCm serving, +49% decode.
- **Quant war escalates**: Unsloth says ISTA-DASLab's GSQ-RCO benchmark numbers don't reproduce; the Basalt engine author swapped his Flash-Next bases to Unsloth quants (20–40% lower KLD).
- **Qwenx86-4B**: a full x86-64 computer compiled into Qwen3.5-4B weights — calculator, C compiler and MicroPython as firmware; the compact checkpoint runs in ~9 GB.
- **CPU-Z stats show the first-ever year-over-year decline in PC RAM capacity** (32 GB −2.5%, 64 GB −18%) as memory prices bite.

## AI hardware and silicon

**CPU-Z data: PC RAM capacity falls year-over-year for the first time** — Event date: Nov 7, 2026 (09:40 GMT).
Doc TB's latest CPU-Z Validator stats show 32 GB systems at 37.2% of validations (−2.5% YoY) and 64 GB at 7.3% (−18% YoY), while 16 GB climbs 12.9% to 33.0% and 8 GB grows 5.5%; DDR4 share is up 6.0% to 54.5% while DDR5 slips to 32.9%. The site cautions these are validation-database shares, not shipment data — but it is the first reversal of the multi-year "more RAM" trend, and it lands amid AI-driven DRAM pricing that has already hit GPU MSRPs this week.
*Why it matters:* RAM capacity is the single biggest determinant of what local users can run — CPU-offloaded MoE serving (Strata, llama.cpp --n-cpu-moe, capy-flash disk tier) depends on cheap big-memory kits. Shrinkers buying 16 GB boxes will feel this in 70B-class GGUF workloads.
*Local inference:* Directly targets the 32–64 GB DDR5 tier that 27B–35B-A3B offload rigs rely on; 16 GB-class (RTX 5060 Ti) users should expect less CPU-expert-offload headroom over time.
Source: VideoCardz, Nov 7 2026 09:40 GMT — https://videocardz.com/newz/cpu-z-statistics-show-32gb-and-64gb-ram-configurations-declining-year-over-year
Source: Doc TB (@d0cTB) via CPU-Z Validator, Nov 6 2026 — https://videocardz.com/newz/cpu-z-statistics-show-32gb-and-64gb-ram-configurations-declining-year-over-year (embedded)

## Model releases

No new base-weight disclosures verified inside this window (Saturday overnight; the notable Nov 6 afternoons — Zellige's Tessera-RL and bartowski's Xing4.0-Intent ladder — landed pre-window and are footnoted below).

## Community spins and quantizations

**Qwenx86-4B: an x86-64 computer compiled into Qwen3.5-4B weights, GGUF + self-managed memory arrive** — Event date: Nov 7, 2026 (08:13Z GGUF, 08:58Z v3 update).
Quazim0t0's Qwenx86 puts a 13-layer, compiler-generated CPU circuit *inside* the transformer weights — Qwen calls tools via <tool_call> and the in-weights machine executes x86-64 integer code from MSVC-compiled C, verified against Unicorn on 2,000 lockstep cases. The compact checkpoint is ~9 GB so it fits a 16 GB GPU with KV headroom; v2 adds a filesystem, 1,000-fact store, 50,000-token verbatim recall and JPEG "heads," and v3 (in-window) lets the model decide on its own when to save/read/search notes. The GGUF quant ladder is Apache-2.0 off Qwen3.5-4B.
*Why it matters:* "Computer-in-weights" removes the host tool-runtime dependency — the agent's calculator, C compiler and Python exist as matrix rows, which is the most aggressive anti-injection tool-use architecture yet for air-gapped local rigs.
*Local inference:* compact checkpoint ≈ 9 GB VRAM (RTX 4070/5060 Ti 16 GB class, any 12–16 GB card); GGUF ladder via llama.cpp.
Source: Hugging Face — https://huggingface.co/Quazim0t0/Qwenx86-4B-compact (model card, lastModified Nov 7 2026 08:58Z)
Source: Hugging Face GGUF — https://huggingface.co/Quazim0t0/Qwenx86-4B-GGUF (created Nov 7 2026 08:13Z)

**mradermacher's overnight ladder: CyberFusion, HiveCoder3, Fikra Swahili, ARA-heretic 27B** — Event date: Nov 6 23:58Z → Nov 7 11:50Z.
Twelve in-window quant repos from mradermacher: Qwen3.6-35B-A3B-CyberFusion (static + i1), HiveCoder3 (00:04Z + i1), Fikra-Model-1-2B and Fikra-Model-2-4B (zabibutechnika's Kenyan Swahili on-device family, 23:58Z), Qwen2.5-32B-Instruct-SOM-heretic (05:04Z), penclaw-GLM-5.3-abliterated (09:25Z), richardyoung's Qwen3.8-27B-ARA-heretic (04:32Z + i1 at 11:12Z, i1-IQ2_M 10.1 GB → Q4_K_M 16.6 GB), plus new i1 tiers for Agent-R2-4B (rubric-RL agent) and the iPhone-Agent WSP 0.8B, and Industry-Coder-27B-v3.0-i1.
*Why it matters:* The Fikra pair keeps the African-languages on-device wave going (offline-first Swahili at ~1–2 GB), and the ARA-heretic 27B ladder gives uncensored Qwen3.8-27B variants a full sub-16 GB path.
*Local inference:* 27B IQ2_M fits 12 GB cards, Q4_K_M 16.6 GB is tight for a 16 GB 5060 Ti (use IQ3_M 12.7 GB); the 4B/2B/1.7B-class entries run on phones and Jetsons.
Source: Hugging Face — https://huggingface.co/mradermacher/Qwen3.8-27B-ARA-heretic-i1-GGUF (created Nov 7 2026 11:12Z)
Source: Hugging Face — https://huggingface.co/mradermacher/Qwen3.6-35B-A3B-CyberFusion-i1-GGUF (created Nov 7 2026 10:27Z)
Source: Hugging Face — https://huggingface.co/mradermacher/Fikra-Model-2-4B-GGUF (created Nov 6 2026 23:58Z)

**bartowski quantizes p-e-w's Oc-e-Non-v1: the terse anti-sycophancy 4B** — Event date: Nov 7, 2026 (02:45Z).
bartowski pushed a full imatrix GGUF ladder (bf16 8.05 GB → IQ2_M 1.49 GB, Q4_K_M 2.59 GB, llama.cpp b12180) for p-e-w/Oc-e-Non-v1, a heretic-processed, deliberately terse Qwen3-4B-Instruct finetune. The base landed Nov 6 12:58Z; the GGUFs are the in-window event.
*Why it matters:* terse by construction, it's a cheap answer-shape corrector for agent pipelines that burn tokens on preamble.
*Local inference:* 2.59 GB Q4_K_M — any 4–6 GB GPU, laptops, Pi 5 class.
Source: Hugging Face — https://huggingface.co/bartowski/p-e-w_Oc-e-Non-v1-GGUF (created Nov 7 2026 02:45Z)

**Obsessive-1.7B: a 1.1 GB web-search subagent with 0% fabricated answers** — Event date: Nov 7, 2026 (12:13Z post, GGUF 10:49Z).
MuzafferMahi fine-tuned Liquid AI's LFM3-1.7B to do exactly one job: fire one web_search, return a short value + verbatim quote + URL, or say "Not found." On 131 held-out SimpleQA questions it hits 94% find-rate, 100% correct abstention and zero invented answers (base model: 50%/0%/10%), at 2.0 s/question median on a laptop RTX 4060; a community GGUF (banyohavlusu23) already exists.
*Why it matters:* Strict single-shot grounded extraction is the pattern commenters flagged for keeping parent-agent context clean — a tiny model that refuses beats a big one that embellishes.
*Local inference:* 1.1 GB — runs alongside any chat model on 8 GB+ GPUs or CPU.
Source: Reddit r/LocalLLaMA — https://www.reddit.com/r/LocalLLaMA/comments/1xq4yju/obsessive17b_a_11_gb_websearch_subagent/ (Nov 7 2026 12:13Z)
Source: Hugging Face — https://huggingface.co/banyohavlusu23/Obsessive-1.7B-GGUF (created Nov 7 2026 10:49Z)

## Inference software and benchmarks

**Update on previously reported (Strata) — Strata 0.2.12: 3× faster prompts on 12 GB cards, tuned DFlash2 drafter, DGX Spark build** — Event date: Nov 7, 2026 (08:54Z release).
Niko1221's engine ships RTX 3060-class UD-IQ4_XS-v31 prompts ~3× faster (87→264 tok/s at 2K; code +37%, agent +34%), Strix Halo prompts x1.24–2.14, M5 Max output +10.3–12.7% with host-pipelined prefill, and a new 0.34 GB tuned DFlash2 drafter giving +2.4–5.0% decode on RTX PRO 5000 and DGX Spark (linux-aarch64 gets 0.2.12). Radeon R9700 sees 32K prompts +8.6–11.6% via chunked GDN scan, but Auto falls back to MTP on gfx12 after DFlash2 hangs; a raft of tool-call fixes lands (Claude Desktop model name, forced tool_choice 24/24, Anthropic tool_use_id).
*Why it matters:* These are exactly the low-end and Spark-class numbers that decide whether an agent loop feels instant; the honest regressions table (JSON −18.9% on 3060) keeps the release credible.
*Local inference:* Measured on RTX 3060 12 GB, Strix Halo, M5 Max, R9700 32 GB, 2×A6000, DGX Spark; direct fit for RTX 5060 Ti 16 GB-class UD-IQ4_XS-v31 users.
Source: GitHub release — https://github.com/Niko1221/Strata/releases/tag/v0.2.12 (published Nov 7 2026 08:54Z)
Source: Reddit r/LocalLLaMA — https://www.reddit.com/r/LocalLLaMA/comments/1xq2pxr/stratadflash2gfx906mixedrocmcuda_use_cuda/ (ecosystem corroboration)

**Community Strata fork: 47 GiB MoE daily-driver on two Radeon VIIs + an idle NVIDIA laptop GPU** — Event date: Nov 7, 2026 (09:51Z post).
milpster's fork adds a full HIP backend for gfx906 (Vega, where stock Strata "doesn't build as tagged"), a custom rocBLAS that fixes the first-inference segfault, speculative DFlash2 decoding on ROCm (stock: CUDA-only), and — the headline — cross-vendor serving: the NVIDIA card ships expert/prefill rows over a socket to the AMD engine, with SIGKILL-survivable self-healing. Author-reported: +49% pooled decode (34.7 vs 23.3 tok/s) and 1.4–2.4× short-prompt wins vs same-day stock on the same box; cross-vendor overhead measured under 1% (8% at depth from staging).
*Why it matters:* It's the first practical mixed AMD+NVIDIA inference rig, and it resurrects 2019 Vega silicon as serving hardware — homelab upgrade paths just changed.
*Local inference:* Validated on 2×Radeon VII 20 GB (gfx906) + one 8 GB NVIDIA laptop GPU; built from Swift-1.5 / Qwen3.8-Flash-Next 47 GiB MoE.
Source: Reddit r/LocalLLaMA — https://www.reddit.com/r/LocalLLaMA/comments/1xq2pxr/stratadflash2gfx906mixedrocmcuda_use_cuda/ (Nov 7 2026 09:51Z; author-reported benchmarks)

**Quant war: Unsloth can't reproduce GSQ-RCO numbers; Basalt switches to Unsloth bases** — Event date: Nov 7, 2026 (04:28Z post).
TingTingin compiled the escalating dispute between ISTA-DASLab (creators of the widely-used GSQ-RCO "pruned-experts" quants) and Unsloth, who report the quoted benchmark results don't reproduce — the methodology is unpublished — and that comparable Unsloth quants beat them. The clinching data point comes from the Basalt engine author (who had built his packed Flash-Next quants on ISTA-DASLab bases): after independent testing he replaced them with similar-sized Unsloth equivalents, showing 20–40% lower KL-divergence vs BF16.
*Why it matters:* Half the 748B-class MoE rigs on reddit standardize on GSQ-RCO tiers; if their eval numbers can't be reproduced, quant choice — and KLD-vs-BF16 as the tiebreaker — is being repriced across the ecosystem.
*Local inference:* Affects DSv4.1-Flash/Flash-Next mix-tier users on 32 GB+ (dual-5090, Spark) setups choosing between GSQ-RCO and UD-v3.1 tiers.
Source: Reddit r/LocalLLaMA — https://www.reddit.com/r/LocalLLaMA/comments/1xpxfob/another_benchmark_has_hit_the_quant_war/ (Nov 7 2026 04:28Z)

**llama.cpp b12168–b12189 overnight wave** — Event date: Nov 6 23:58Z → Nov 7 12:15Z (GitHub builds b12187–b12189).
Nine merges: CUDA lightning-indexer shared-memory bank-conflict fix (#31803), Hexagon post-norm residual fused into RMS_NORM_MUL (#31822), WebGPU group-norm kernel (#31490), Metal GROUP_NORM fix for ne03>1 (#31852), OpenCL arange (#31829), server visits parent slots before child slots (#30779), and ggml compute-view-limit raise for tensor-sparse buffers (#31447).
*Why it matters:* The lightning-indexer and Hexagon fusion fixes land on the two hottest sparse-attention/offload paths (DSv4-style indexing, Snapdragon X2 Elite boxes).
*Local inference:* Touches CUDA (all), Hexagon (Snapdragon X2/8-Elite boxes), WebGPU (browser/Iris Xe), Metal (Apple).
Source: GitHub — https://github.com/ggml-org/llama.cpp/releases (b12189 published Nov 7 2026 12:15Z; commit SHAs 80d2c0c2…ca89968a)

**vLLM overnight: DSpark Gumbel fusion, MiniMax-M3 NVFP4 MegaMoE warmup, HugeTLB KV offload** — Event date: Nov 6 23:38Z → Nov 7 10:57Z (~25 merges).
Highlights: fuse the DSpark Markov bias and block-combine into Gumbel sampling (#64476), warm DeepGEMM NVFP4 MegaMoE kernels before serving MiniMax-M3 (#66227), process 4 heads per program in DSv4 fused_inv_rope_fp8_quant (#63540), SimpleCPUOffload gets one host buffer per rank + opt-in HugeTLB pages (#60130), a MiniMax-M3.1-Flash MI355X AgentX nightly recipe (#66188), faster CPU GDN speculative-decode recurrence (#64829), plus ROCm CI hardening (skip-condition, DPX attention shards) and Aria moving to the Transformers modeling backend (#59767).
*Why it matters:* HugeTLB-backed CPU KV offload and NVFP4 MegaMoE warmup both move throughput/TTFT on the cards the homelab tier actually owns; the MI355X recipe keeps ROCm first-class.
*Local inference:* SM100/MI355X datacenter lanes, but #60130 (KV→host RAM with HugeTLB) and #64829 (CPU spec-decode) are workstation/CPU-relevant.
Source: GitHub — https://github.com/vllm-project/vllm/pull/64476 , https://github.com/vllm-project/vllm/pull/60130 , https://github.com/vllm-project/vllm/pull/66188 (merged in-window)

**SGLang: Rust-server hardening batch + Kimi-K3.1 MXFP4 decode kernel + gemma4 NVLink multicast fusion** — Event date: Nov 6 22:53Z → Nov 7 12:27Z (30+ merges).
A ~15-PR Rust-frontend wave adds config-refusal/GenerateValidator launch checks, request decompression, DP-rank meta_info, key redaction in /server_info, crash dumps and warmup-fail exits; kernels side ships an opt-in persistent MXFP4 decode MoE kernel for Kimi-K3.1 (#48638), gemma4 TP all-reduce fused with layer epilogue + FP8 quant on NVLink multicast (#48563), reduce-scatter for DeepSeek-V4.1 CP MoE output (#47247), glm5_next fused KDA decode at 8/16 heads (#47893), an architecture-independent ATen→MLX lowering layer for Apple (#37354), Muse-Glimmer-NVFP4 MXFP8 lm_head fix (#48128), and file-backed PLE tables on HMM devices for qwen4-exp (#48689).
*Why it matters:* The Rust server maturing (API keys, redaction, SSE charset, crash dumps) is what turns SGLang from a lab stack into a self-hostable production endpoint; the K3.1 MXFP4 kernel extends Blackwell-class quantized decode to Moonshot's flagship.
*Local inference:* SM12x/SM100 + NVLink multicast multi-GPU rigs benefit; the ATen→MLX layer opens Apple Silicon serving paths.
Source: GitHub — https://github.com/sgl-project/sglang/pull/48638 , https://github.com/sgl-project/sglang/pull/48563 , https://github.com/sgl-project/sglang/pull/37354 (merged in-window)

## Also noted

- **Harness Leaderboard** benchmarked Kimi Code, Hermes Agent, Pi, Claude Code and Codex on Terminal-Bench 2.1 + Deep-SWE 1.1 (r/LocalLLaMA, Nov 7 02:41Z) — community reception is mixed on chart design, missing OpenCode 2 and no DeepSeek harness; https://www.reddit.com/r/LocalLLaMA/comments/1xpvfx8/harness_leaderboard_kimi_code_hermes_pi_claude/
- **UploadYourRam.org** — satirical "donate your idle RAM" site (mock 2/4/16 GB tiers, "donate to Sam Altman") hit HN front page (141 pts) and r/LocalLLaMA, a joke with teeth during the DRAM squeeze; https://uploadyourram.org / https://news.ycombinator.com/item?id=50403214
- **Unsloth UD v3.1 QFN IQ4_XS tool-call failures** — field report of frequent tool-call breakage on the new v3.1 dynamic quant tier (r/LocalLLaMA, Nov 6 23:22Z); https://www.reddit.com/r/LocalLLaMA/comments/1xprc6o/unsloth_v31_qfn_iq4_xs_fails_tool_calls_very/
- **Weight-Space-Programming reaches production 4-bit** — user reports applying WSP edits to a production Qwen3.8 4-bit model (r/LocalLLaMA, Nov 7 01:50Z); https://www.reddit.com/r/LocalLLaMA/comments/1xpuexr/weight_space_programming_on_my_production_qwen_38/
- **D3D: Decoupled Diffusion Drafting** (arXiv 2611.06425, surfaced r/LocalLLaMA Nov 7 09:00Z) — standalone diffusion drafter removes the inter-round dependency so drafting runs *during* verification; https://arxiv.org/abs/2611.06425

## Just missed the window

- **ZelligeAI Tessera-27B-B-CJK-RL** (r/LocalLLaMA Nov 6 23:23Z) — RL-tuned CJK-compressed-thinking Qwen3.8-27B coding agent, Apache-2.0, 0.67× base thinking-tokens, AIME+HMMT 108/126 vs 97; weights pushed Nov 6 18:39Z, ~4h pre-window (footnoted once). https://huggingface.co/ZelligeAI/Tessera-27B-B-CJK-RL
- **bartowski XingChen-AGI Xing4.0-29B-Intent-GGUF** (created Nov 6 18:45Z, ~4h pre-window) — intent/slot-filling IE variant of the Xing4.0-29B MoE the A4B ladder covered yesterday; imatrix full ladder (footnoted once). https://huggingface.co/bartowski/XingChen-AGI_Xing4.0-29B-Intent-GGUF

## Coverage notes

Verified and reported: **15 qualifying items** (6 leads, 3 engine-wave items, 6 short items) + 2 just-missed footnotes — counting unit: one entry per underlying event. Saturday overnight window: ServeTheHome/Tom's quiet since the Nov 5–6 STH wave (nothing post-C1300); no new Ollama/vLLM/SGLang/TensorRT-LLM releases in-window (checked GitHub releases). **Ledger suppression:** ~10 candidates dropped as already reported (Kimi K3.1-Pro day-0 coverage, llmd daily build, Strata "is unreal" re-coverage, Lisa Su data-center-first re-circulation, omlx HN repost, PLaMo 3 Translate pre-window base, PyTorch 2.15 article on an Oct 28 release, Asahi progress report [out of scope]). Dead ends (two-strike rule): Tom's Hardware feed 404'd; Register PyTorch article confirmed pre-window on timestamp; Reddit search lane returned only re-coverage.
