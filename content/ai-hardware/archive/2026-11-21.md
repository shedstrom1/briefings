# AI Hardware & Local Models Brief — Sat AM Nov 21: Inkling 2 Mini goes local

**Window:** 2026-11-20 17:45 → 2026-11-21 07:49 EST (previous fire → this fire; new items only).

## TL;DR

- **Inkling 2 Mini turned into a local-inference story overnight.** The quant ladder landed in waves — nace-ai's native NVFP4 GGUF (01:57 UTC), bartowski's 27-file imatrix set (05:45–06:59 UTC), Unsloth's Dynamic ladder down to 7.76 GB, plus EXL3 at 3.33 bpw — and SGLang published a verified single-GPU support matrix covering DGX Spark, RTX 5090 and RTX PRO 6000.
- **llama.cpp burned eight builds** (b12597 → b12606) with RDNA4 Vulkan MMVQ improvements, new SYCL gated-delta-net and grouped-XMX GEMM kernels, a looped-model (inkling2_loop) fix, and the fused lightning indexer now always on.
- **vLLM shipped an unusually large security batch** (int64 pointer arithmetic, prompt-count bounds, auth-path fixes) alongside HiSparse support for Hy4 Flash, a faster fused CUDA MTP decode kernel for 16-bit recurrent state, and a gfx942 AITER Triton MXFP8 MoE backend.
- **TensorRT-LLM took the Inkling 2 architecture into the tree** (operators, kernels, Inkling-2-Small target, merged 18:51 EST) and added a native static-FP8 MiniMax-H3 scenario on Rubin.
- **Hardware was thin in an overnight Saturday window.** The one in-window product launch was SilverStone's HELA 1350Rz (1350 W, three EPS cables, first PSU advertising Intel Nova Lake-S); the week's tempting silicon story — Moore Threads' "Lushan" record-VRAM tease — traces to November 18–20 and is footnoted, not counted.

---

## Model releases

### **Inkling 2 Mini's day-0 local stack: verified Spark/5090/PRO-6000 support, DSpark drafter bundled, quants down to 7.8 GB** — event date 2026-11-21 (overnight roll-out)

SGLang merged its Inkling-2-Mini cookbook at 03:24 EST (`#52042`), listing *verified* launch images for B300/GB300, B200/GB200, H200, RTX PRO 6000, RTX 5090 and DGX Spark, and describing the model as "26B total and 5.4B active parameters per token… layers 6–17 run three passes per token," with "a 3-layer DSpark draft head bundled for speculative decoding" and weights of "about 18 GB," served on a single GPU including desktop and workstation cards. Ollama merged MLX-runner support (`#19799`, 21:38 EST Nov 20) plus an Inkling follow-up and a Metal fusion of Inkling's residual mixing (`#19806`, `#19808`). llama.cpp fixed loop-slot sharing after the scale pass in `inkling2_loop` (`#32704`, merged 06:10 EST), which is the arch-specific bug that the day-0 GGUF tier depends on.

The official model card sets the sizing envelope: a single ~20 GB checkpoint in mixed NVFP4 (most weights, trained and deployed natively) and MXFP8 (last four layers), 1M-token context, Apache 2.0 with a Model Acceptable Use Policy, and stated support for 1x NVIDIA DGX Spark or Apple silicon with 32 GB+. LM Studio's listing pegs the smallest downloadable GGUF at a minimum of 18 GB of system memory, with 32 GB+ unified (Apple) or 32 GB+ VRAM recommended, and warns that "support for this looped architecture in local runtimes is new, so performance varies by hardware and runtime."

*Why it matters:* this is the first genuinely small looped transformer the community can run, and it arrives pre-quantized at the precision it was trained in. The trade-off is explicit in the lab's own results: looping buys reasoning efficiency (DeepSWE v1.1 48.2%, SWE-bench Verified 79.2%, BrowseComp 79.0% with compaction, Artificial Analysis Index v5.0 of 20.1 — tying Qwen3.8-27B) but not knowledge capacity, and Thinking Machines flags weaker factual recall, repetitive reasoning while debugging, and shorter chains of thought as loop depth rises.

*Local inference:* the native 20 GB checkpoint targets 1x DGX Spark or any 32 GB+ VRAM card; the quant ladder changes who gets in — Unsloth UD-IQ3_XXS at 11.66 GB and UD-Q4_K_M at 16.61 GB put the model inside the RTX 5060 Ti 16 GB / 4060 Ti 16 GB class, and UD-IQ1_S at 7.76 GB makes it viable on 8–12 GB cards, at KLD 0.0911 vs Q8_K_XL (89.4% same-top-token). Expect throughput to be worse than an equivalent 3.4B-active model, because three passes through 12 shared layers means roughly 5.4B effective active parameters per token.

**Source:** SGLang cookbook PR #52042 (merged 2026-11-21 08:24 UTC) — https://github.com/sgl-project/sglang/pull/52042
**Source:** Unsloth quant ladder + KLD table — https://huggingface.co/unsloth/Inkling-2-Mini-GGUF (tier files refreshed 2026-11-21 08:35 UTC)
**Source:** llama.cpp #32704 (merged 2026-11-21 11:10 UTC) — https://github.com/ggml-org/llama.cpp/pull/32704
**Source:** Ollama #19799 / #19808 (merged 2026-11-21 02:38 / 04:05 UTC) — https://github.com/ollama/ollama/pull/19799
**Corroboration:** LM Studio model page (updated ~2026-11-21 02:00 UTC) — https://lmstudio.ai/models/inkling-2-mini

---

## Community spins and quantizations

### **Inkling 2 Mini quant stampede: native-NVFP4 GGUF, MLX, EXL3 3.33 bpw, and a 5.4 GB tier** — event date 2026-11-21

nace-ai published `Inkling-2-Mini-GGUF` containing a single `Inkling-2-Mini-NVFP4.gguf` at 20:57 EST, plus an MLX 5.8-bit pack at 21:46 EST — the native-precision GGUF route rather than a requant. bartowski followed at 00:45 EST with a 27-file imatrix ladder (IQ2_XXS/IQ2_S/IQ2_M/IQ3_XS/IQ3_XXS/IQ3_M … plus f16 and bf16 `mmproj` vision files). Unsloth's Dynamic 3.x ladder spans UD-IQ1_S 7.76 GB → UD-Q8_K_XL 22.87 GB with a published KLD-vs-Q8_K_XL table and same-top-token agreement (IQ3_XXS 11.66 GB / 0.0269 / 94.7%; Q4_K_XL 17.63 GB / 0.0107 / 98.0%), and ships separate DSpark drafters (Q8_0 0.40 GB, BF16 0.65 GB). By the end of the window the long tail had started: `0xzknw/Inkling-2-Mini-EXL3-3.33bpw` (07:36 EST) and `shinmegamiboson/Inkling-2-Mini-LookAtMyQuant` at 5.4 GB and 8.9 GB with a matching drafter (07:48 EST).

*Why it matters:* the fastest thing about a small open-weight release is now the ladder, not the benchmarks — within ~12 hours of weights landing you have GGUF, i1 imatrix, MLX, EXL3 and 5–9 GB "fits-anywhere" tiers, plus draft heads. That matters for looped models specifically, because llama.cpp/SGLang support is days old and every tier is effectively a test artifact for the runtime.

*Local inference:* the practical tiers are 7.8–8.5 GB (any 8–12 GB card or a 16 GB Mac), 11.7–13.6 GB (16 GB cards, this machine's 5060 Ti included), and 16.6–17.6 GB for near-lossless 4-bit, which needs a 20–24 GB card or generous CPU offload.

**Source:** nace-ai/Inkling-2-Mini-GGUF (created 2026-11-21 01:57 UTC) — https://huggingface.co/nace-ai/Inkling-2-Mini-GGUF
**Source:** bartowski/thinkingmachines_Inkling-2-Mini-GGUF (created 2026-11-21 05:45 UTC) — https://huggingface.co/bartowski/thinkingmachines_Inkling-2-Mini-GGUF
**Source:** 0xzknw/Inkling-2-Mini-EXL3-3.33bpw (created 2026-11-21 12:36 UTC) — https://huggingface.co/0xzknw/Inkling-2-Mini-EXL3-3.33bpw

### **Also noted — mradermacher's overnight ladder** — 2026-11-20 22:21 → 2026-11-21 12:42 UTC

Roughly 45 GGUF repos in-window, the notable ones being `higgs-audio-v3-stt-v2` and `higgs-audio-v3-8b-stt-v2` (ASR), `granite-vision-4.1-4b` (+ heretic), `MiniMax-Music3-GGUF`, `LLaVA-OneVision-2-8B-Instruct`, `DaisyRose-27B` (RadixArk RL agent FT), `ThinkingBomb-gemma-4-26B-A4B`, `Mistral-Heretica-12B-RP-Evolved`, the `Wukong-4B/9B` Opus/DeepSeek SLERP 2x-merges, `Refusal-Judge-Qwen3-0.6B` (text-classification), and an i1-imatrix pass over the evening's releases from 10:28 UTC onward. All are llama.cpp-loadable; the audio/vision files carry `mmproj` companions.

**Source:** https://huggingface.co/mradermacher (repos created 2026-11-20 22:21 UTC → 2026-11-21 12:42 UTC)

---

## Inference software and benchmarks

### **llama.cpp b12597 → b12606: RDNA4 Vulkan MMVQ speedups, new SYCL kernels, always-fused lightning indexer** — event date 2026-11-21

Eight builds in the window (22:57 UTC Nov 20 → 10:28 UTC Nov 21). The performance-bearing merges: `#31804` improves Vulkan RDNA4 MMVQ for Q3_K, Q5_K and IQ4_XS (06:41 EST); `#32456` adds a column-blocked `gated_delta_net` SYCL kernel and `#31753` fuses ADD/RMS_NORM/MUL with a vectorized RMS norm on SYCL; `#31428` reworks grouped XMX GEMM to avoid SLM staging; `#32466` unrolls the FA coopmat1 array to dodge an AMD-Windows driver bug; `#32519` scores only the k-pools of the ubatch sequences in QSA; `#32650` drops the flags, auto-probe and non-fused fallbacks so the fused lightning indexer is always used; plus a grammar integer-overflow fix (`#32551`), a ppc64le tinyBLAS alignment fix, a zDNN load-race fix, and CUDA fastdiv UB abort.

*Why it matters:* RDNA4 and Intel Arc are the two backends getting the most attention in this batch, and the "always fused" lightning-indexer change removes a silent performance cliff for DeepSeek-Flash-class sparse attention on the CPU/Metal paths.

*Local inference:* direct benefit for RX 7900 XTX / R9700-class and RX 9000-series Vulkan users (improved small-batch quantized GEMV), and for Arc A770/B580/B60 users on SYCL. b12606 ships Windows CUDA 12.4 and 13.5 binaries, Linux ROCm 10.0, OpenVINO 2026.4.1, SYCL FP16/FP32, and Snapdragon (CPU+Adreno+Hexagon) builds.

**Source:** llama.cpp releases b12606 (2026-11-21 10:28 UTC) — https://github.com/ggml-org/llama.cpp/releases
**Source:** PR list merged in window — https://github.com/ggml-org/llama.cpp/pull/31804

### **vLLM: security-hardening batch plus Hy4 Flash HiSparse, faster MTP decode and a gfx942 MXFP8 MoE backend** — event date 2026-11-20/21

Six security/bounds PRs landed in-window: `#53131` widens int32 pointer arithmetic to int64 in CUDA kernels (02:52 EST), `#54721` completes input-bound hardening left incomplete by `#51447` (07:18 EST), `#53473` enforces a prompt-count bound on pooling and scoring routes, `#52794` fixes root-path matching in auth middleware, `#68249` shares media byte budgets across batched requests, and `#69101` adds a vLLM security-review skill to the docs. On the feature side: `#69026` adds HiSparse support for HY4 Flash, `#69065` ships a faster fused CUDA MTP decode kernel for 16-bit recurrent state, `#68831` gives the Qwen3-Omni / Qwen3-ASR audio tower encoder CUDA graphs, `#68921` exports ZAYA1 CCA prefill checkpoints, `#62516` enables mixed causal/bidirectional attention for DiffusionGemma on TRTLLM-Gen and makes it the default for text-only SM100/SM103, `#61214` adds an AITER Triton MXFP8 MoE backend for gfx942, and `#68351` prefetches the whole checkpoint into every node's page cache (multi-node load times).

*Why it matters:* the int64/KV bounds work is the unglamorous fix for a real failure mode — long-context servers on big KV pools overflowing 32-bit addressing — and `#68351` attacks the "cold start costs 10 minutes" problem on clusters with shared storage.

*Local inference:* `#61214` matters for MI250/MI300-era gfx942 owners running MoE models; the encoder-graph work lowers first-token latency for local Qwen3-Omni/ASR pipelines on any CUDA card; `#62516` is SM100/SM103-only, i.e. Blackwell workstation/Spark-class.

**Source:** https://github.com/vllm-project/vllm/pull/69026 (merged 2026-11-21 00:23 UTC), #69065 (08:55 UTC), #53131 (07:52 UTC), #61214 (2026-11-20 23:47 UTC)

### **SGLang: Hunyuan-4-Flash vision-language support, prefill CUDA graphs for CuTe-DSL MoE/MegaMoE, shortest-prefill-first scheduler** — event date 2026-11-21

Beyond the Inkling cookbook, the in-window merges that change behavior: `#51937` adds Hunyuan-4-Flash vision-language support (20:24 EST Nov 20); `#50684` adds prefill CUDA graphs for FlashInfer CuTe-DSL MoE and MegaMoE on NVIDIA; `#51688` switches beam search to a tiny FP32 router GEMM for NVFP4 checkpoints; `#51929` lets a JIT module build for its architecture *family* (sm_100f) rather than one exact arch; `#51957` (merged 07:45 EST) adds a load-scaled class boundary and token budget for shortest-prefill-first scheduling; `#51983` lets the K3.1 verify megakernel serve its own request buckets; and on AMD diffusion, `#51933` runs MiniMax-H3 packed attention on aiter's batch/MHA-v4 kernels while `#51934` runs the BigVGAN vocoder's convolutions in MIOpen's summation orders from Triton. Storage-side, `#46540` fixes NIXL HiCache moves for pages that are not whole O_DIRECT blocks and `#49537` adds L2→L3 KV write-latency metrics.

*Why it matters:* the sm_100f family-JIT change is the quiet one — it means a single wheel can serve Blackwell variants instead of shipping arch-specific builds — and shortest-prefill-first directly changes p95 TTFT under mixed agent loads.

*Local inference:* the scheduler and CUDA-graph work lands in the RTX 50-class/DGX Spark sweet spot; NIXL/O_DIRECT fixes matter for anyone tiering KV to NVMe on a workstation.

**Source:** https://github.com/sgl-project/sglang/pull/51957 (merged 2026-11-21 12:45 UTC), #50684 (23:25 UTC Nov 20), #51937 (00:24 UTC)

### **TensorRT-LLM takes Inkling 2 into the tree; adds native static-FP8 MiniMax-H3 on Rubin** — event date 2026-11-20/21

`#22627` "Add the Inkling 2 Modeling V2 family: operators, kernels and the Inkling-2-Small target" merged 18:51 EST. Also in-window: `#22658` adds a native static-FP8 MiniMax-H3 scenario on Rubin, `#22363`/`#22354` rework DeepSeek-V4.1 MoE decode at attention TP with FC1-only TRTLLM-Gen runs, a CuTe-DSL FC2, one-cluster routing and dynamic EP, `#19395` promotes PrimTS MLA prefill with MTP/Helix integration and Rubin support, and `#22695` fixes FlashMLA builds for SM107-only targets.

*Why it matters:* NVIDIA's own stack now carries first-party kernels for the looped-architecture family the community is only a day into supporting, which usually means the consumer-side runtimes get a reference implementation to chase.

*Local inference:* SM107 is the Rubin preview target (server), but the DSpark/MTP and MLA kernels propagate to Blackwell workstation cards; Inkling-2-Mini itself fits a 32 GB card as shipped.

**Source:** https://github.com/NVIDIA/TensorRT-LLM/pull/22627 (merged 2026-11-20 23:51 UTC)

### **Ollama opens a preview install channel** — event date 2026-11-20 22:47 UTC

`#19801` "install: support preview channel" merged two minutes into this window (17:47 EST Nov 20), alongside the Inkling MLX work. Small quality-of-life change with real consequence: users can now opt into day-0 MLX engine paths (like the Inkling residual-mix fusion) without living on dev builds.

*Local inference:* Apple-silicon-only for now (the merged MLX work is `mlxrunner`), no effect on CUDA/ROCm installs.

**Source:** https://github.com/ollama/ollama/pull/19801

---

## AI hardware and silicon

### **SilverStone HELA 1350Rz: first PSU advertising Intel Nova Lake-S, with three EPS cables** — event date 2026-11-21

SilverStone's new 1350 W ATX 3.1 / PCIe 5 unit is 80 PLUS Platinum, 150 × 86 × 160 mm, ships **three** 4+4-pin EPS CPU cables "to support the high power demands of Intel NVL-S CPUs," two native 12V-2×6 GPU connectors, Japanese capacitors, a 135 mm FDB fan with semi-fanless mode, and 50 °C continuous rating (VideoCardz, 11:19 UTC).

*Why it matters:* three EPS connectors is a signal that the next Intel desktop platform is being designed with server-class CPU power draw, which is exactly the direction homelab AI boxes have gone (EPYC/Threadripper-class boards already do this).

*Local inference:* 1350 W with only two 12V-2×6 is adequate for a 2-GPU rig; three-GPU+ inference builds will still want splitter-free headroom or a 1600 W-class unit.

**Source:** VideoCardz, 2026-11-21 11:19 UTC — https://videocardz.com/newz/silverstone-hela-1350w-psu-officially-supports-intel-nova-lake-s-cpus-features-three-eps-power-cables

---

## Also noted

- **mradermacher's overnight GGUF ladder** (~45 repos, 22:21 UTC → 12:42 UTC) — higgs-audio-v3 STT v2 (2B and 8B), granite-vision-4.1-4b, MiniMax-Music3, LLaVA-OneVision-2-8B, DaisyRose-27B, ThinkingBomb-gemma-4-26B-A4B, Wukong 4B/9B SLERP merges. https://huggingface.co/mradermacher
- **GitHub day-0 Inkling 2 Mini ports** (all created in-window, most are skeleton/wip): DGX Spark packages (tonyd2wild NVFP4-DGX-Spark 20:58 UTC, thefullctx sglang-dgx-spark, aarsh21, dedene), `inkling2-mini-strixhalo-gufo` (02:43 UTC, still being pushed at 07:42 UTC), `inkling-2-mini-2x3060`, `Inkling-2-Mini-RTX-5090`, `inkling-2-mini-sm80`, `intel-b70-inkling2-mini-vllm`, `Inkling-2-Mini-2xRTXPro6000`, `inkling2mini-sm120-speculator-fix`. No credible benchmark numbers published yet — treat as ports-in-progress, not results.

## Just missed the window

- **Inkling 2 Mini itself** was released on November 20, before this window: Thinking Machines' post and model card are dated Nov 20, and Unsloth's GGUF repo was created 13:09 UTC (08:09 EST) — roughly 9½ hours before the 17:45 EST window opened. The job's 17:45 Nov 20 run recorded `last_status: error`, so the release was never reported; the ecosystem response above (quants, runtimes, verified hardware) is all in-window.

- **Moore Threads "Lushan" record-VRAM tease.** The claim itself is not an in-window event: the product manager's Bilibili post dates to November 18 and Wccftech reported it on November 20, before this window opened. VideoCardz published a fresh write-up of the teaser artwork at 10:03 UTC today (https://videocardz.com/newz/moore-threads-teases-new-gpu-with-highest-vram-capacity-on-a-consumer-graphics-card), and Forge PC (21 November 2026, https://forgepc.co.uk/news/moore-threads-lushan-gpu-teaser) confirms the November 18 origin and the December 29 MDC 2026 reveal, with no capacity, price or availability confirmed. Treated as out-of-window coverage of an out-of-window disclosure; footnoted once.

## Coverage notes

- **Verified items in this edition:** 10 counted units = 6 lead items (Inkling 2 Mini roll-out, Inkling quant stampede, llama.cpp builds, vLLM wave, SGLang wave, TensorRT-LLM), 2 secondary (Ollama preview channel, SilverStone HELA 1350Rz) and 2 also-noted clusters (mradermacher ladder, day-0 GitHub Inkling ports). The Inkling roll-out counts as one event with three milestone units (quant tiers / runtime merges / verified hardware matrix), not three events.
- **Ledger dedupe:** 5 candidates suppressed as already reported — Microsoft RDMA-over-USB4 (`rdma-over-usb4-preview`, footnoted once 11-20), Unsloth Desktop XDNA2 NPU 35B (`unsloth-desktop-xdna2-npu`, footnoted once 11-20), ServeTheHome DDR6 follow-up (event reported 11-18 as `DDR6-preview`), the 12x V100 NVFP4 lawyer rig (reported 11-20), and the AMD Adrenalin 26.11.2 driver line (reported 11-19). Both "Just missed" footnotes above are new items with no prior `missed:` tag; nothing already footnoted was footnoted again.
- **Excluded on window:** NVIDIA GeForce hotfix 621.35 (SSD-write fix) published 2026-11-20 22:12 UTC — 33 minutes pre-window; GPD WIN Mini Ultra pricing ($1,699) at 19:51 UTC Nov 20, pre-window (and the underlying product is already on the ledger from 11-16); the Tom's Hardware Optane PMem 200 + 2x RTX 3090 Kimi K3 update (~22:10 UTC, 35 min pre-window, and its parent thread was footnoted once on 11-08 so it cannot be footnoted again).
- **Dead ends / down lanes:** r/LocalLLaMA was unavailable for the whole run — `search.rss` and `sub … new.rss` both returned HTTP 404 behind a 429 window, so no Reddit community sweep happened (known intermittent lane; not a negative finding). TechPowerUp `review-news.asp` and The Register `/ai_and_ml` returned 404 through the fetcher; wccftech failed TLS verification. HN Algolia returned nothing with traction in-window (Saturday morning). Two search queries returned zero results and were not reworded-retried.
- **Ledger note:** the 17:45 EST Nov 20 fire errored, which is why yesterday's headline release is arriving as a footnote plus an in-window ecosystem story rather than as a day-0 item.
