# AI Hardware & Local Models Brief — Nov 18, 2026 morning edition

**Window:** Nov 17, 2026 17:45 EST → Nov 18, 2026 08:08 EST (previous fire → now; new items only)

**TL;DR**
- Nvidia interim CEO Colette Kress confirmed the company has stopped shipping licensed H200 accelerators to China, ending the Trump-brokered 25%-tariff side-deal just one quarter after $2.4B of H200 China revenue.
- XingChen-AGI's Xing4 technical report — a 285B-parameter agentic LLM family trained on 8,192 Huawei Ascend 910C cards — hit #1 on Hugging Face Daily Papers, the strongest public datapoint yet on frontier training entirely on China's domestic silicon stack.
- New weight-space-programming research (POSTECH) shows LLMs hand-writing weights abandon the Transformer for sparse associative-memory tables that run up to 17x faster on GPUs at equal size.
- Day-0 GGUF ladders landed for Sage-S2.1-Flash (118–342 GB static quants) plus fresh community quants (Alok-v3-14B, Qwen3.8-27B-3D-Coder, fox-1.6, One-For-All-4B).
- Overnight inference-stack waves: llama.cpp b12497–b12503 (OpenVINO NPU expansion, Vulkan BF16, V100 MoE MMQ), vLLM ~35 PRs, and SGLang shipping Kimi-K3.1 serving on AMD gfx950 plus muse-spark support for the Radeon AI PRO R9700 (gfx1250).

## AI hardware and silicon

**Nvidia halts H200 shipments to China, ending the tariff side-deal** — event: Nov 17, 2026 (earnings call, ~7:00 PM ET)
On Tuesday's Q3 earnings call, interim CEO Colette Kress said "we are no longer shipping licensed Hopper data center products to China," Gizmodo reports from the call. That kills the December deal in which Trump allowed H200 sales to approved Chinese customers in exchange for a 25% US tariff — a program that generated $2.4B of H200 revenue in Q3 alone, according to Bloomberg figures cited in coverage. TechCrunch broke the call detail and Reuters ran it early Wednesday under "Nvidia stops shipping US-approved AI chips to China." The move follows the resignation of Jensen Huang, who had personally brokered the arrangement, and lands alongside the new remote-access export rule that extends licensing to overseas data centers serving Chinese customers.
*Why it matters:* The single most flexible US-approved China channel for AI compute is gone, at least for now. It also removes a revenue line Nvidia's guidance explicitly no longer counts on (Q4 guide assumes zero China data-center), and pushes Chinese buyers further onto domestic silicon — which is exactly where the Xing4 report below says the ecosystem is heading.
*Local inference:* No direct consumer impact — H200 (141 GB HBM3e) is enterprise-class — but the halt curbs one supply channel that fed grey-market accelerator liquidity, and signals sustained supply-side tightness for anyone shopping used data-center cards.
Source: Gizmodo, "Nvidia Stops Shipping H200s to Customers in China," Nov 18, 2026 — https://gizmodo.com/nvidia-stops-shipping-h200s-to-customers-in-china-2000841474
Source: Reuters, "Nvidia stops shipping US-approved AI chips to China," Nov 18, 2026 (headline/paywall; corroborated) — https://www.reuters.com/world/china/nvidia-stops-shipping-us-approved-ai-chips-china-2026-11-18/
Source: Techmeme river attributing TechCrunch (Tim Fernholz), Nov 17, 7:00 PM ET — https://www.techmeme.com/river

**Update on previously reported (Nov 12–17 ceo-transition): a board faction is reportedly pushing to reverse the no-return decision** — event: Nov 17, 2026, 9:50 PM ET
Hours after Chair Harvey Jones stated on the earnings call that the board "does not intend to reinstate" Huang, Semafor reported sources saying a faction of Nvidia board members is pressing for his return anyway — the first sign the no-return posture may not be settled inside the company, matching the ~200K-signature reinstatement campaign and stockholder letters. Original story is paywalled; cited via the Techmeme river entry.
*Why it matters:* Roadmap credibility (Rubin ramp, DGX Spark/RTX Spark consumer launches, China strategy) is being priced in by the market right now — NVDA moved on the call itself despite a beat.
*Local inference:* Indirect — Spark-class device and driver cadence ride on the leadership outcome.
Source: Techmeme river (Semafor), Nov 17, 2026, 9:50 PM ET — https://www.techmeme.com/river

**Also noted:** AMD SVP Jack Huynh teased a CES 2027 keynote (Jan 5, 2027, Mandalay Bay) with a cryptic "A" graphic, stoking Zen 6 desktop / RDNA 5 speculation — VideoCardz, Nov 17, ~8:24 PM ET — https://videocardz.com/newz/amd-teases-ces-2027-announcement-with-mysterious-a-shaped-graphic

## Model releases

**Xing4 technical report: 285B agentic LLMs trained entirely on Huawei Ascend 910C** — event: Nov 17, 2026 (arXiv 2611.22115; #1 HF Daily Paper of Nov 18)
XingChen-AGI (the TeleChat lineage, tied to China Telecom) published the design and systems report for the Xing 4 series: pretraining a 285B-parameter model on 8,192 Ascend 910C cards using MindSpore HyperParallel (topology-aware hybrid sharding, distributed-optimizer efficiency, long-sequence activation management, CPU affinity tuning) plus "TeleGym," a disaggregated agent-execution platform for post-training. The paper's #1-of-day HF slot, with 145 upvotes by morning.
*Why it matters:* This is the most detailed public account of a frontier-scale training run on Chinese accelerators and MindSpore — a working blueprint for the post-H200 China market and an existence proof that the Ascend stack carries 285B-class jobs. For the weights ecosystem, note the lineage: the community already quantized the smaller Xing4.0-29B-A4B MoE in early November.
*Local inference:* The 285B flagship is server-only, but the Xing4 family's small MoE tier (29B-A4B) already runs on 16–24 GB consumer GPUs via community GGUFs; day-0 quants of any released Xing4 checkpoints should land within hours based on lineage history.
Source: Hugging Face Daily Papers, arXiv 2611.22115, published Nov 17, surfaced Nov 18 — https://huggingface.co/papers/2611.22115

## Community spins and quantizations

**Day-0 static GGUF ladder for Sage-S2.1-Flash (321B scientific agent)** — event: Nov 18, 2026, ~03:45 EST
mradermacher published the first GGUF ladder for internlm/Sage-S2.1-Flash (the MIT-licensed scientific multimodal agent reported here Nov 16): static quants from Q2_K (118.0 GB) through Q8_0 (341.6 GB), with Q4_K_S (182.5 GB) and Q4_K_M (193.9 GB) flagged "fast, recommended," Q6_K (264.1 GB) "very good quality," plus Q8_0/f16 mmproj supplements (0.8/1.2 GB). No imatrix quants yet. Update on the Nov 16 Sage release entry.
*Why it matters:* Day-0 quants make a 321B MoE research-flagship servable outside NVIDIA-reference stacks, and MLE-bench Lite #1 models are exactly what the local coding-agent crowd stress-tests next.
*Local inference:* Server-class only — even Q2_K is 118 GB, so think 2×H200/MI300X or a 4-GPU EPYC box; not a Spark/5060 Ti fit. Smaller Sage derivatives may follow.
Source: https://huggingface.co/mradermacher/Sage-S2.1-Flash-GGUF (repo created Nov 18, 2026 08:45Z)

**Overnight quant ladders: Alok-v3-14B, Qwen3.8-27B-3D-Coder, fox-1.6, One-For-All-4B, chiara-qwen3-14b** — event: Nov 17–18, 2026 (repos created 20:17–08:12 EST)
Fresh GGUF from mradermacher covering five new-ish community bases: Alok-v3-14B-NSLRE (neuro-symbolic reasoning FT targeting software engineering, chemistry, biology, theorem-proving with MCTS traces; base shivrajgurjar, Apache-2.0; GGUF + i1 imatrix pair), Qwen3.8-27B-3D-Coder (syaikhipin's Three.js 3D-scene coder, Apache-2.0, GGUF + i1 — extends the CCORE-GLB 3D-coder trend), fox-1.6 "compact-LLM" (GGUF + i1), TendieLabs One-For-All-4B roleplay merge (GGUF + i1), and chiara-qwen3-14b persona SFT+DPO (i1).
*Why it matters:* The 3D-scene-generation niche is consolidating fast around Qwen3.8-27B (three related coders in a week), and neuro-symbolic reasoning FTs are becoming a recurring quant target.
*Local inference:* All consumer-friendly — the 14B Alok ladders sit ~9–16 GB in Q4_K_M-class tiers (12–16 GB cards); the 27B 3D-Coder needs ~17–24 GB (24 GB+ class, or offload); 4B/1.6B runs anywhere including 8 GB cards.
Sources: https://huggingface.co/mradermacher/Alok-v3-14B-NSLRE-GGUF · https://huggingface.co/mradermacher/Qwen3.8-27B-3D-Coder-GGUF · https://huggingface.co/mradermacher/fox-1.6-GGUF · https://huggingface.co/mradermacher/One-For-All-4B-GGUF · https://huggingface.co/mradermacher/chiara-qwen3-14b-i1-GGUF

## Inference software and benchmarks

**llama.cpp b12497–b12503 overnight: OpenVINO NPU expansion, Vulkan BF16 ops, MoE-on-V100** — event: Nov 17, 11:04 PM – Nov 18, 3:28 AM EST
Seven builds overnight with a notable hardware-fit cluster: #32172 expands ggml-openvino model support with optimized NPU/GPU/memory paths (Core Ultra-class PCs get a real inference backend upgrade), #32202 adds BF16 support to various Vulkan ops (RDNA3/4, Ada, Intel iGPUs), #32504 routes MoE mul_mat_id to CUDA MMQ on V100 (revives older Volta data-center cards for MoE serving), #32454 fuses NORM+MUL(+ADD) on CUDA, #31352 widens Pascal DP4A MMQ tiles, #32427 adds mtmd support for the tiny-voca TTS, and #29539 adds a benchmark-only random-expert routing env var for MoE profiling.
*Why it matters:* Each item shifts what runs where: OpenVINO NPU offload and Vulkan BF16 both change the practical quality/power envelope of iGPU-class local rigs, and V100 MoE MMQ gives the used-V100 homelab community a decode path.
*Local inference:* Directly on-target for 8–16 GB iGPU/NPU laptops, RDNA3/4 desktops, and the used-V100 rigs this brief has been tracking; builds published at https://github.com/ggml-org/llama.cpp/releases
Source: https://github.com/ggml-org/llama.cpp/releases (b12497 04:02Z → b12503 08:28Z, Nov 18) and merged-PR search, GitHub API, Nov 17–18, 2026

**vLLM overnight wave: ROCm DSv4.1 fusions, HY4 Flash day-0 recipe, gpt-oss TP fix** — event: Nov 17–18, 2026
~35 PRs merged across the window, headlined by AMD: #67906 fuses the MXFP8 input quant of fused_wqa_wkv and the shared expert into their producers for DeepSeek-V4.1 on ROCm, #63254 finalizes the Muse Spark token-sharded MoE reduction in one reduce-scatter, and #67302 serves ModelOpt INT8_PB_WO on ROCm with an 8-bit Triton WNA16 kernel. Also: #68295 adds a HY4 Flash recipe (day-0 tuning for the Nov 16 model), #68097 Triton-kernel optimization of DeepSeek V4/V4.1 vision ops, #68153 NemotronH MTP multimodal-embedding pass-through for spec-decode, #68373 a Kimi-K3 MegaMoE shared-expert fix without sequence parallelism, and #65353 splitting streamed gpt-oss MXFP4 experts in whole TP blocks.
*Why it matters:* The ROCm side of the wave is the story — fused MXFP8 and INT8 WNA16 paths keep narrowing the AMD/NVIDIA gap for the open models people actually run.
*Local inference:* ROCm kernels target MI300/MI355-class, but the INT8_PB_WO/W4A16 lineage and gpt-oss-20b TP correctness matter for 16–32 GB consumer AMD (RX 9070 XT / R9700 class) builds.
Source: https://github.com/vllm-project/vllm (merged-PR search 2026-11-17T22:45Z..2026-11-18T13:08Z, GitHub API)

**SGLang ships Kimi-K3.1 serving on AMD gfx950 and muse-spark on R9700 (gfx1250)** — event: Nov 17–18, 2026
The overnight wave's spine is AMD enablement: #51000 serves Kimi-K3.1 on MI350X/MI355X (gfx950), backed by #50997 (gfx950 sparse-MLA, DCP top-k and FP4 index-K kernels), #50998 (KDA verify, A8W4 MoE, latent-tail and LL collective kernels) and #50999 (MLA draft-verify/DFLASH spec-decode kernels); #50707 adds gfx1250 support for muse-spark — the Radeon AI PRO R9700 — while #47756 fixes FLUX.3 diffusion serving on gfx1151 and #51048 adds FlashInfer CuTe-DSL + DeepGEMM MegaMoE ops for Inkling-2. Also #49066 FP8 DeepGEMM prefill scoring for DeepSeek-V4.1-Flash on Hopper and a Hy4-flash cookbook (#51160).
*Why it matters:* Kimi-K3.1 (the 2.8T open-weight flagship class) gaining first-class AMD decode paths, and 32GB workstation cards getting muse-spark support, are both hardware-fit changes more than incremental tuning.
*Local inference:* MI350X/355X are server cards, but the same gfx950 kernel family benefits MI325X and future workstation SKUs; muse-spark-on-gfx1250 puts this line's flagship image model inside a 32GB, ~$1,700 R9700 — the most consumer-adjacent AMD card in this brief.
Source: https://github.com/sgl-project/sglang (merged-PR search window above, GitHub API)

**TensorRT-LLM overnight: MXFP4 Engram tables for DeepSeek-V4.1; FP4-MLA work lands** — event: Nov 17–18, 2026
Merges include DeepSeek-V4.1 MXFP4 Engram tables, loading of BF16 PLE tables with native NVFP4 MTP weights, an unwaived Qwen3.8-Flash-Next FP8 PLE-offload accuracy test, and the NVFP4 MLA tech blog formally dated 11/17 in the README (the dense block-reuse + 4x decode-kernel work reported Nov 16).
*Why it matters:* NVIDIA's own stack is now shipping the same Engram/MXFP4 and FP4-attention tricks the community engines pioneered — day-0 validation on Blackwell/Rubin silicon.
*Local inference:* SM100/SM120 (RTX PRO 6000 Blackwell, DGX Spark-adjacent SM121) coverage; Qwen3.8-Flash-Next FP8 PLE offload accuracy fixes touch exactly the 32–96 GB Blackwell tier.
Source: https://github.com/NVIDIA/TensorRT-LLM (merged-PR search window above, GitHub API)

**WSP research: LLM-written weights ditch Transformers for 17x-faster associative-memory tables** — event: Nov 17, 2026 (arXiv 2611.22055, POSTECH; GitHub effl-lab/Weight-Space-Programming)
Asking three frontier LLMs to hand-write model weights (the weight-space-programming paradigm this brief has followed since MIT's Nov 2 result), the authors found that when models design their own architecture they uniformly produce sparse lookup-table / associative-memory models instead of Transformers — beating the best same-LLM-written Transformer while using ~1,800x fewer parameters, reaching 54.4% test accuracy on 10-shot tasks, with a proposed table-based design up to 17x faster on GPUs than a same-sized Transformer. Pretrained LLMs remain more accurate; the claim is about which structure to build when writing weights by hand.
*Why it matters:* WSP has been a curiosity with big compute claims; this formalizes why tiny task-specific "models" on local machines can look like editable memory tables — interpretable, single-row-fixable, and GPU-cheap. Expect local-engine folks to prototype these (the repo is live with 27 stars at time of writing).
*Local inference:* Directly targets the micro-model niche — table models this small run comfortably on any laptop iGPU or CPU, and the edit-a-row debugging property is the anti-hallucination story the quant crowd keeps chasing.
Source: https://huggingface.co/papers/2611.22055 (published Nov 17, 2026)

**Also noted:** MIT Han Lab released Evolving Infra, an agent-adaptive LLM-serving-at-scale framework (GitHub repo, HN-posted Nov 18, 2:16 AM ET) — https://github.com/mit-han-lab/EvolvingInfra · SGLang's #41329 landed CPU FP8/INT8 quant ops for compressed-tensors — CPU-only inference gets native quant kernels.

**Just missed the window:** Multi-Branch Transformer (arXiv 2611.19800, posted Nov 15) surfaced to #1-adjacent on HF papers Nov 18 — parallel Transformer branches + Joint-KV cut KV-cache storage up to 34% and lifted decode throughput 51.4% on a single H200; if you're watching single-GPU long-context speed, read it.

## Coverage notes
- Verified items: 10 (2 hardware incl. 1 update; 1 model release; 2 community entries covering 6 repos; 4 software/benchmark entries; plus 3 "also noted" and 1 footnote — counting unit: one entry per underlying event, stack waves counted as one entry per repo).
- Ledger dedupe: ~12 in-window candidates suppressed as already reported — re-coverages of Rubin HPC, Lambda CPO cluster, EPYC Embedded 9006, the entire ceo-transition news cycle (Jones statements, Rene Haas approach, SEC probe), bartowski's KORMo-Web/Draft ladders, CXL DDR4-reuse card, DRAM-antitrust ruling, TeamGroup shortage warning, and Dell's 2027 memory warning (originally Oct 16–20).
- Community lane degraded: Reddit's anonymous Atom feeds returned HTTP 404 on all four planned sweeps (r/LocalLLaMA unavailable via the normal no-login path); HN Algolia and GitHub/HF lanes substituted. Notable dead ends: Reuters (paywall — used as corroborating headline only), The Register AI index (404), TechCrunch article URL (404 — attribution kept via Techmeme).
- Intermittent search-backend failures (DuckDuckGo "no results") required index-first discovery; recovered mid-run.
