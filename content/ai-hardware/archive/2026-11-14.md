# AI Hardware & Local Models Brief — Saturday, November 14, 2026

**Window:** 2026-11-13 17:45 EST → 2026-11-14 07:45 EST (previous fire → now) | 8 events verified (unit: underlying events, lead + also-noted; appendix footnotes excluded)

## TL;DR
- **llama.cpp shipped Qualcomm Hexagon IQ2_XXS and NVFP4 kernels** (PR #31954, build b12376) — ultra-low-bit and NVFP4 weights now run natively on Snapdragon DSP hardware, with HVX matmul plus HMX matrix-engine paths.
- **Community "CQ" calibration-free 4-bit quant posted rigorous benchmarks**: statistically indistinguishable from BF16 on Qwen3.8-27B (McNemar, greedy), while bitsandbytes NF4 loses 12 GSM8K points; plus GCMP, a 2.9M-param LoRA replacement (author-reported).
- **vLLM (38 merges) and SGLang (54 merges) overnight waves** landed Kimi-K3.1-Pro MI355X AgentX presets, MiniMax-M3.1 ROCm GEMM/indexer tuning, DSpark monokernel Markov sampling, NVFP4-on-B12X scale compression, SGLang rust-server HTTP/2 and MXFP8 KV-cache linking.
- **Quantizer mradermacher dropped a fresh in-window ladder**: ViRA-9B/27B visual-agent RL models, CyberBuddy-9B cyber pair, Ripple-65B-Heretic-light GGUF+i1, Vision-AutonoSearch 9B/35B-A3B.
- Quiet Friday-night/Saturday-morning hardware tape: no verified new silicon or device launches in window; WSJ's NVIDIA circular-financing report broke ~74 minutes before the wall (footnoted).

## Inference software and benchmarks

**llama.cpp: Hexagon NPU gains IQ2_XXS and NVFP4; DFlash spec-decode refactored** — event date 2026-11-14 (merged 04:57Z/08:42Z; builds b12374–b12377)
The night's marquee merge (PR #31954, co-authored with Qualcomm's Max Krasnyansky) adds tiled repack layouts, HVX matmul kernels and HMX matrix-engine paths for IQ2_XXS and NVFP4 in the Hexagon backend, including an IQ2_XXS 2-gather tile decode with compact grid and a fix for HMX dequant-scale underflow in fp16. Build b12377 (09:46Z) also carries a large refactor of DFlash-family speculative decoding (#32016) that splits head/backbone loaders per file (DFlash, DFlash2, DSpark, LiliCorr variants detected once), while b12374 fixed a Metal mul_mv destination-offset overflow (#32221) and #32023 cuts jinja chat-template rendering overhead.
*Why it matters:* IQ2_XXS (~2.6 bpw) plus NVFP4 as first-class Hexagon kernels pulls sub-3-bit quantized 27B-class models into reach on Snapdragon X2/8-Elite-class NPUs — the NPU finally gets the same low-bit vocabulary that CUDA and Vulkan already have, and the spec-decode cleanup makes draft-head work (DFlash2/DSpark) far cheaper to extend.
*Local inference:* runs on Qualcomm Hexagon NPU devices (Snapdragon X series, 8-Elite Gen phones/mini-PCs); IQ2_XXS quants of 27B-class models fit single-digit-GB budgets on shared LPDDR5X.
Sources:
- llama.cpp release b12376 (2026-11-14 08:42:49Z): https://github.com/ggml-org/llama.cpp/releases/tag/b12376
- llama.cpp release b12377 (2026-11-14 09:46:55Z): https://github.com/ggml-org/llama.cpp/releases/tag/b12377
- PR #31954 Hexagon IQ2_XXS/NVFP4 (merged 2026-11-14 04:57:06Z): https://github.com/ggml-org/llama.cpp/pull/31954

**Community "CQ" 4-bit matches BF16 on Qwen3.8-27B benchmarks; GCMP claims LoRA parity at 55× smaller** — event date 2026-11-13 21:55 EST (r/LocalLLaMA post)
Researcher u/textclf published benchmark-counted results for CQ, a calibration-free 4-bit format (first posted weeks ago as a training-loss comparison): on Qwen3.8-27B under identical lm-evaluation-harness runs with greedy decoding, CQ 4-bit at 18 GB shows no statistically significant difference from BF16 on nine tasks, while bitsandbytes NF4 (22.4 GB) drops 12.0 GSM8K points (p=3e-6); CQ vs NF4 is +8.7 GSM8K (p=0.0005), with WikiText-2 KL 0.0282 vs 0.0330. The same post introduces GCMP, a LoRA replacement training 2.9M params (~11 MB) vs ~159M (~320 MB) at equal-adapter quality settings. All numbers are author-run; a commenter flagged the post as AI-assisted prose (author confirmed Claude formatting).
*Why it matters:* calibration-free 4-bit that survives paired significance testing would cut both quantization friction (no calibration corpus) and VRAM — a 27B at 18 GB lands squarely in the RTX 3090/4090 tier and is only a hair above 16GB cards.
*Local inference:* targets 24GB-class cards for the 18GB Q4 base (RTX 3090/4090/5090); NF4 comparison implies a 4.4GB VRAM saving at equivalent-or-better accuracy.
Source:
- r/LocalLLaMA post (2026-11-14 02:55Z): https://www.reddit.com/r/LocalLLaMA/comments/1xvrgf1/cq_4bit_matches_bf16_on_qwen3827b_benchmarks/

**vLLM overnight wave: Kimi-K3.1-Pro MI355X presets, MiniMax-M3.1 ROCm tuning, DSpark monokernel** — event date 2026-11-13/14 (38 PRs merged 22:45Z–12:38Z)
Highlights from GitHub's merged-PR log: Kimi-K3.1-Pro gained MI355X AgentX presets covering all six InferenceX#5043 concurrencies (#67600, #67487) plus TP8 mono-build CI (#67439); MiniMax-M3.1 on ROCm got GEMM improvements (#66933) and attention-indexer XCD balancing (#66934); Muse Spark DSpark Markov sampling now runs one launch per round via --monokernel (#66051); NVFP4 MoE expert scales stay CSF-compressed on GPU under B12X (#64753); NIXL can now pull KV from pipeline-parallel prefill stages (#65336), and MRV2 skips draft attention metadata on context-KV-only prefills (#67291).
*Why it matters:* the AMD MI355X recipe line keeps maturing toward day-one parity for K3.1-class MoEs, and the DSpark single-launch sampler plus NVFP4-scale residency are decode-loop speedups on Blackwell.
*Local inference:* MI355X is datacenter-class; the MRV2/NIXL and quant paths carry down to SM120/SM121 (RTX 5090 / DGX Spark) builds.
Source:
- GitHub vLLM merged search (2026-11-13T22:45Z→2026-11-14T12:45Z): https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged

**SGLang overnight wave: rust-server HTTP/2, MXFP8 KV links, Mooncake TENT, HiCache batched writes** — event date 2026-11-13/14 (54 PRs merged 22:45Z–12:43Z)
The Rust frontend now serves HTTP/2 (h2c) alongside HTTP/1.1 behind --enable-http2 (#50401) and passes video frame/token caps to multimodal processors (#50407). The unified cache gained MXFP8 KV support in the direct external linker (#50324); AMD HiCache write-backs move via hipMemcpyBatchAsync (#50363); disaggregation can use an opt-in Mooncake TENT transfer-engine backend (#47025); qwen4-exp gathers prefill PLE rows host-side for the file-backed table (#50318); and a joint Full/SWA eviction walk landed on the Rust tree core (#49992). Also merged: dense SiLU+mul fusion for masked per-token-group quantization (#50364) and sglang-kernel 0.4.11 / sgl-fa3 0.1.0 bumps (#50397).
*Why it matters:* HTTP/2 on the Rust server cuts proxy overhead for multi-user local fleets, and MXFP8-KV in the external linker extends Blackwell-friendly cache compression across the composable-KV stack.
*Local inference:* HTTP/2 and scheduler gains are hardware-agnostic (helps DGX Spark/Strix Halo single-box servers); MXFP8 KV and PLE items target SM100+/Blackwell and ROCm MI-series.
Source:
- GitHub SGLang merged search (2026-11-13T22:45Z→2026-11-14T12:45Z): https://github.com/sgl-project/sglang/pulls?q=is%3Apr+is%3Amerged

## Community spins and quantizations

**mradermacher in-window ladder: ViRA visual agents, CyberBuddy pair, Ripple-65B heretic-light** — event date 2026-11-14 (repos created 00:39Z–06:40Z)
New GGUF ladders today: ViRA-9B and ViRA-27B (huaweilin's high-resolution vision-language agent RL models, Apache-2.0) at https://huggingface.co/mradermacher/ViRA-27B-GGUF and https://huggingface.co/mradermacher/ViRA-9B-GGUF; CyberBuddy-CloudSec-9B and CyberBuddy-CyberOps-9B (cloud-security / SOC agentic pair) at https://huggingface.co/mradermacher/CyberBuddy-CyberOps-9B-GGUF; Ripple-65B-Heretic-light in plain and imatrix i1 tiers (schonsense's decensored Ripple-65B-Preview variant — closure on the Nov 5 "missed" footnote) at https://huggingface.co/mradermacher/Ripple-65B-Heretic-light-i1-GGUF; plus Vision-AutonoSearch-9B and Vision-AutonoSearch-35B-A3B GGUFs (https://huggingface.co/mradermacher/Vision-AutonoSearch-35B-A3B-GGUF).
*Why it matters:* the Ripple-65B family now has a "heretic-light" abliteration with day-0 quants, and ViRA extends RL-trained visual-agent workloads to local sizes.
*Local inference:* ViRA-9B ≈ 5–6 GB at Q4 (any 8–12GB card); ViRA-27B Q4 ≈ 17 GB (24GB class, tight-16GB with offload); CyberBuddy-9B ≈ 5–6 GB (8–12GB); Ripple-65B-Heretic-light Q4 ≈ 36–40 GB (48GB class: A6000, RTX 6000 Ada, or multi-GPU).
Source:
- Hugging Face author feed (createdAt verified in-window): https://huggingface.co/mradermacher

## Also noted
- **Weight-space reading:** u/suayptalha released a paper + open toolkit ("WSR", github.com/suayptalha/wsr) testing whether 9B–30B open models can predict behavioral effects of weight edits without running the target: Qwen-27B hits 73.0% balanced accuracy on subtle edits with weight access vs 57.8% from text descriptions — but Muse-30B's 34 "successful" edits showed no detectable behavior change (2026-11-14 09:07Z). Runs on 9B–30B models, any 16–24GB card. https://www.reddit.com/r/LocalLLaMA/comments/1xvxyhj/can_openweight_llms_can_predict_the_effects_of/
- **Ollama** merged #19686 (preserve unmodeled tool-schema keywords in the API, 2026-11-14 00:00Z) — small correctness fix for tool-calling apps on local Ollama servers. https://github.com/ollama/ollama/pull/19686
- **Commentary:** Nathan Lambert's Interconnects piece "Open-source AI without Jensen" (published 2026-11-14) argues NVIDIA's founder-driven funding of the open-weight ecosystem will dry up post-resignation and calls for a new coalition; relevant context for anyone betting on Nvidia-backed open models (Nemotron family). https://www.interconnects.ai/p/open-source-ai-without-jensen

## Model releases
No verified new open-weight base releases first disclosed inside this window (Saturday-morning lull; the mrady/bartowski activity above is quant-tier, and SenseNova-Loop-9B-Preview re-surfacing on r/LocalLLaMA today is an Oct 30 event — excluded).

## AI hardware and silicon
No verified in-window hardware or silicon announcements. The two freshest candidate headlines on TechPowerUp's board ("RTX 50 up to $500 hike", "AMD data-center-first") turned out to be Nov 3 and Nov 6 articles re-bumped by weekend comment traffic — correctly rejected on event date.

## Just missed the window
- **WSJ: NVIDIA allegedly funded AI startups to absorb excess TSMC silicon rather than lower GPU prices** — reported November 13; TechPowerUp's write-up published 16:31 EST, ~74 minutes before the 17:45 wall (Kress pushed to curb "circular financing"; the $250B OpenAI Ohio plan pared to $120B). Footnoted once, never again. https://www.techpowerup.com/354814/nvidia-allegedly-engineered-scarcity-by-funding-ai-startups-to-buy-its-gpus

## Coverage notes
- Verified: 8 events (5 full items + 3 also-noted), from ≥6 distinct outlets/sources (GitHub, Hugging Face, r/LocalLLaMA, Interconnects, TechPowerUp, WSJ/Wccftech).
- Ledger dedupe: ~9 candidates suppressed as already reported or out-of-window re-coverage — SOV-Transcribe-1 rL repost (missed-tagged), Nace-vs-Unsloth comparison discussion, ERNIE-5.5-Flash link, SenseNova-Loop-9B repost (Oct 30 event), KittenML kitten-tts-2 mini/nano/pico tiers (created Nov 13 03:34Z, ~19h pre-window — same family as the Oct 23 `missed:` footnote), TPU AMD "data center first" (Nov 6 event), RTX-50 $500 hike (Nov 3), Altman DRAM quote (Oct 29), chip-stock Friday selloff (off-scope).
- Dead ends (two-strike rule): VideoCardz feed blocked (Cloudflare); "IBM trains two small open models to ignore hidden document instructions" (r/LocalLLaMA 11:33Z) — no primary source found in two attempts (search + thread), excluded; ServeTheHome and Tom's Hardware published no in-window items; The Register AI feed had nothing new.
- Phase walls respected; window derived once from now_et.py at fire time.
