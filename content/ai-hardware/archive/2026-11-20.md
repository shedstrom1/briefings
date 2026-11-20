# AI Hardware & Local Models Brief — Hy4 flagship weights land, OpenAI's 14% PPA AMD tape, TensorRT-LLM 1.3.0 GA

**Window:** 2026-11-19 17:45 → 2026-11-20 08:06 EST (overnight slot; previous fire Nov 19 17:45)

**TL;DR**
- Tencent's Hy4 flagship — a ~774B-parameter multimodal MoE — finally has open weights on the Hub, with day-0 vLLM and SGLang recipes landing within twenty minutes of the checkpoint becoming public.
- OpenAI says its models rewrote AMD flagship-GPU RTL for up to 14% PPA, condensing "a year of chip-design work into under 5 months," with some changes already sent to manufacturing; gains also flowed to OpenAI's own Jalapeño inference chip.
- TensorRT-LLM v1.3.0 hit GA overnight: Rubin SM107 preview, NVFP4-as-W4A16 on Hopper/Ada via Marlin, KV-cache compression, and DSpark/DFlash drafters.
- The 12× used V100 "lawyer rig" now fits every one of DeepSeek V4.1 Flash's 15,360 experts in 4-bit NVFP4 on $9,450 of 2018 silicon: 157–184 tok/s on code, first token on a 256K document in 58 seconds.
- Overnight stack waves: vLLM shipped W4A8 MoE decode kernels for RDNA3/3.5 and W4A16 skinny GEMMs for gfx1151 (consumer Radeon), SGLang brought Kimi-K3.1 and DeepSeek-V4.1 to Huawei Ascend, and llama.cpp b12565–b12567 landed Domyn Edge 4B support, Vulkan GDN chunking and CUDA Orin fast paths.

## AI hardware and silicon

**OpenAI's models give AMD's upcoming flagship GPU up to 14% PPA — some changes already at manufacturing** (event date Nov 20)
OpenAI published new details of its ongoing collaboration with AMD: its models rewrote SystemVerilog RTL on AMD's upcoming flagship GPU circuitry and found up to 14% power/performance/area improvement on IP that AMD's engineers had already spent years optimizing, describing it as roughly a year of conventional design work compressed into under five months. AMD's new CPUs got unspecified single-digit PPA gains from the same flow. OpenAI also reported gains on Jalapeño, its own Broadcom-designed inference ASIC (earlier disclosures: ~13% vector-unit and ~10% matrix-unit standard-cell area reductions, verified on silicon), and benchmarked the capability by having models optimize PicoRV32 and OpenTitan Ibex cores — GPT-6.2 Astra scored best on both cost and optimization. Tom's Hardware notes OpenAI is separately training a "GPT-Synopsys" with Synopsys for EDA tool use, and it's unconfirmed whether stock or fine-tuned models did the AMD work.

- *Why it matters:* This is the clearest vendor-confirmed case yet that agentic RTL optimization is moving from demo to tape-out. If PPA is being squeezed by models, the moat shifts from "who has the best designers" to "who has the best design-agent loop" — and the same tooling that touches AMD's flagship is now within reach of smaller custom-silicon efforts.
- *Local inference:* No immediate 16GB-class impact; the payoff lands in future Instinct-generation parts (better perf/W for rented frontier inference) and in validating the RTL-agent toolchains that community/open-silicon projects are starting to adopt.
- Source: Tom's Hardware, "OpenAI helped AMD improve its upcoming flagship GPU design by 14% PPA," Nov 20 2026 10:30 UTC — https://www.tomshardware.com/tech-industry/artificial-intelligence/openai-helped-amd-improve-its-upcoming-flagship-gpu-design-by-14-percent-ppa-gpt-models-also-helping-lisa-sus-outfit-boost-its-new-cpus
- Second source: Wccftech, "OpenAI Models Deliver Up To 14% PPA Gains On AMD Flagship GPU Designs, With Some Changes Already Sent To Manufacturing," Nov 20 2026 — https://wccftech.com/openai-models-deliver-up-to-14-ppa-gains-on-amd-flagship-gpu-designs-with-some-changes-already-sent-to-manufacturing/

## Model releases

**Tencent Hy4 flagship: ~774B open weights on the Hub, day-0 vLLM/SGLang support** (event date Nov 20)
The tencent/Hy4 repository went public with weights early Nov 20 (lastModified 03:05 UTC): 774.4B parameters in BF16 across 245 shards, architecture hy_v4_vl — the multimodal (image-text-to-text) flagship with 8 experts per token, under Tencent's "other" license. vLLM merged an HY4 recipe within twenty minutes (#68844, 03:24 UTC) and later registered tencent/Hy4 as the public checkpoint while documenting tencent/Hy4-FP8 (#68875, 12:36 UTC); SGLang landed its Hy4 cookbook at 03:17 UTC. This closes the promise from the Nov 4 API launch, where weights were announced "in coming days," and follows the Hy4-flash 220B repo that appeared Nov 17.

- *Why it matters:* Hy4 is the largest frontier-lab open-weights drop since Mistral Large 4, and the FP8 tier plus day-0 engine support signals Tencent is running a MiniMax/Kimi-style open-flagship cadence rather than API-only. Benchmark numbers in the open checkpoint still need independent verification.
- *Local inference:* Not a DGX Spark or 5060 Ti-class model — BF16 is ~1.55 TB and FP8 ~775 GB, just past a single 748 GB coherent 2×GB300 DGX Station; watch for community 2–4-bit expert quantizations (~195 GB at 2-bit), which would land it in Ryzen AI Max 256GB / multi-Spark territory.
- Source: Hugging Face API record for tencent/Hy4 (lastModified 2026-11-20T03:05:49Z) — https://huggingface.co/tencent/Hy4
- Second sources: vLLM PR #68875 (merged Nov 20 12:36 UTC) — https://github.com/vllm-project/vllm/pull/68875 ; SGLang PR #51691 (Nov 20 03:17 UTC) — https://github.com/sgl-project/sglang/pull/51691

## Community spins and quantizations

**mradermacher in-window ladder: Phai-A1-35B-1M, Tenet-32B-Unslop, Cerberus-24B** (event date Nov 20)
Fresh GGUF ladders, all uploaded overnight: Phai-A1-35B-1M (a Qwen3.6-35B-A3B fine-tune pitched at coding agents, RL-trained with a 1M-token context, Apache-2.0 — mradermacher's plain GGUF at 04:14 UTC plus imatrix i1 tier at 12:33 UTC, on top of first-party GGUFs from Nov 18); Tenet-32B-Qwen2.5-Unslop-v1-i1 (08:00 UTC, agentic coding + FP8/GRPO/abliterated lineage on a Qwen2.5-32B base); and Cerberus-24B in GGUF + i1 (07:05/09:46 UTC, an ORPO/QLoRA uncensored Mistral-line chat model).

- *Why it matters:* Phai-A1-35B-1M is the interesting one — a 35B-A3B MoE coding agent with trained 1M-context support is exactly the profile that becomes usable locally once quants stabilize, and imatrix coverage from day one shortens that runway.
- *Local inference:* These 24–35B-A3B quants target 16–24GB GPUs with host-RAM expert offload (llama.cpp --n-cpu-moe, Strata, ik_llama.cpp); the 1M-context claim is RAM-bound in practice — KV has to live in system DRAM on consumer boxes.
- Sources: https://huggingface.co/mradermacher/Phai-A1-35B-1M-GGUF , https://huggingface.co/mradermacher/Tenet-32B-Qwen2.5-Unslop-v1-i1-GGUF , https://huggingface.co/mradermacher/Cerberus-24B-i1-GGUF (base: https://huggingface.co/PhaiModelLab/Phai-A1-35B-1M)

## Inference software and benchmarks

**Update on the 12× V100 lawyer rig: all 15,360 DeepSeek V4.1 Flash experts in 4-bit NVFP4, 157–184 tok/s on code** (event date Nov 20)
Nick Mermiges added a third 4-slot V100-32GB board (12 cards, $9,450 total, 384 GiB HBM2) so every expert of DeepSeek-V4.1-Flash now sits on cards at full NVFP4 precision (278.9 GiB used, "none compressed further"), versus ~38.7% previously. Measured: first code token on a 256K context dropped from 108.4 s to 58 s; prompt reading past 32K is ~4,500 tok/s (up from ~2,200); code decode is 23% faster at 1K context but flat at 32K+; three concurrent 128K requests aggregate 360 tok/s (code) / 213 tok/s (legal). The V100 has no FP4/FP8 tensor cores, so NVFP4 is unpacked in software to half precision — the gain is bytes-read, not compute. Layers are pipelined 0–13 / 14–27 / 28–39 per board with NVLink inside boards and PEX88096/PLX8749 PCIe switches between them; cards run 175 W, and 200 W tripped the host. He claims it beats published 4× DGX Spark stacks on single-request decode at half the price of three Sparks, and says batched decode — "the one thing the Sparks have" — is next. (Update on the Nov 9 / Nov 13 ledger line V100-8x-lawyer-rig.)

- *Why it matters:* The strongest field evidence yet that used datacenter HBM2 pools plus aggressive 4-bit expert quantization beat new edge devices on long-context decode per dollar — and a live demonstration that Blackwell-era formats are viable on pre-FP8 silicon when you're bandwidth-bound.
- *Local inference:* V100-32GB cards run ~$250–400 used, but the rig needs three SXM4 boards, dual PCIe switches and ~1 TB of RAM — homelab at extreme. The portable idea is the format: NVFP4 experts on bandwidth-rich old cards.
- Source: Tom's Hardware, Nov 20 2026 12:30 UTC — https://www.tomshardware.com/tech-industry/artificial-intelligence/lawyers-usd9-450-private-ai-server-of-12-used-nvidia-v100s-now-runs-every-deepseek-v4-1-flash-expert-at-4-bit-nvfp4-up-to-184-tokens-per-second-on-code-and-long-documents-start-answering-in-about-half-the-time
- Second source: Gate.com News (syndicated read of the primary write-up), Nov 20 2026 — https://www.gate.com/news/detail/lawyer-upgrades-private-ai-server-to-12-nvidia-v100-gpus-for-deepseek-v41-25752904

**TensorRT-LLM v1.3.0 goes GA** (event date Nov 20, published 02:45 UTC)
NVIDIA published the v1.3.0 tag overnight with the largest feature list in the line's history: preview Rubin (SM107) support validated on Vera Rubin (DeepSeek-V4 and Kimi K3 included); Blackwell support for Qwen3.5 (incl. MTP + NVFP4), Qwen3.6/3.8-Flash-Next, MiniMax-M3, GLM-5/5.2, Gemma 4 multimodal, Step-3.7-Flash; W4A16 NVFP4 weight-only quantization through Marlin so NVFP4 checkpoints run on Hopper and Ada; MXFP8 W8A8 for linears and MoE; KV-cache compression (TriAttention, NVFP4 cold-page compression, NVFP4 KV and disk cache); DSpark, DFlash, Suffix-Automaton and PARD drafters; DWDP distributed weight data-parallelism; up to 2,048 experts via NCCL-EP; an Anthropic Messages API (/v1/messages); and prefix-aware scheduling enabled by default.

- *Why it matters:* Marlin-based W4A16 NVFP4 officially extends Blackwell-native quants back two architectures — Ada and Hopper owners get a supported path for the NVFP4 checkpoints the ecosystem is standardizing on, and the release consolidates weeks of the 1.4-rc material we've been tracking.
- *Local inference:* Still datacenter-class, but Ada (RTX 4090/4500) and Hopper users are now first-class NVFP4 targets; nothing here changes the 16GB-class picture yet.
- Source: GitHub release NVIDIA/TensorRT-LLM v1.3.0, Nov 20 2026 02:45 UTC — https://github.com/NVIDIA/TensorRT-LLM/releases/tag/v1.3.0

**llama.cpp overnight: builds b12565–b12567, Domyn Edge 4B, Vulkan GDN chunking, Orin fast paths** (event date Nov 20)
Merges in window: Domyn Edge 4B model support (#32559); CUDA optimizations for Orin single-column MMVQ (#31268) and per-expert MUL_MAT_ID precision in the fallback path (#31345); a chunked GATED_DELTA_NET implementation for Vulkan (#30207) plus RMS_NORM_MUL_ROPE loop tuning (#31149); OpenCL folds GQA heads into f16 flash-attention (#32631); llamafile gains QK data types on s390x (#32600); KleidiAI allows OHOS/HarmonyOS AArch64 targets (#32265); Vulkan disables subgroup arithmetic on Qualcomm's driver (#30826); webgpu COL2IM_1D (#31708); graph-node budget/reservation plumbing for the MoE cache (#32648/#32636) and ffn_gate_up_exps scale loading (#32657). Releases b12565–b12567 published 12:18–12:59 UTC.

- *Why it matters:* The Vulkan GDN chunking is the sleeper — it brings hybrid-attention models (Qwen3-Next-lineage) closer to efficient prefill on AMD/Intel iGPUs, and the Orin work keeps Jetson in the loop.
- *Local inference:* Directly improves 16GB-class and below: Orin Nano/AGX (MMVQ), Strix/Alchemist iGPUs via Vulkan (GDN), Snapdragon Windows-on-ARM (Qualcomm Vulkan guard), and the CPU-MoE cache line gets its scheduler plumbing.
- Source: ggml-org/llama.cpp commit log + releases, Nov 20 2026 (22:45–13:06 UTC window) — https://github.com/ggml-org/llama.cpp/releases

**vLLM wave: RDNA3/3.5 W4A8 MoE decode, gfx1151 W4A16 skinny GEMM, online NVFP4→MXFP4, Engram pooling over NVLink/RDMA** (event date Nov 20)
Key in-window merges: #62480 adds a W4A8 MoE decode kernel for RDNA3/RDNA3.5 (gfx11); #65911 serves 6–8 decode rows via a HIP skinny GEMM for W4A16 on gfx1151; #61620 converts online linear NVFP4 to MXFP4; #67539 splits DeepSeek-V4.1 Engram tables across engines over NVLink or RDMA; #64289 enables SM90 MXFP4 MegaMoE through FlashInfer MoE-EP; #60153 fuses the gfx942 mHC seam; #68479 lets Transformers-backend vision towers offload to CPU via --cpu-offload-gb; #68864 adds an Inkling-2-Small H200 recipe.

- *Why it matters:* vLLM keeps threading the needle for consumer AMD: official W4A8 MoE decode kernels for RX 7900 XTX-class parts and W4A16 row-scaling on Strix Halo's gfx1151 iGPU are the kind of features that previously only showed up in forks (Saber, Loom, XeMaya).
- *Local inference:* Two-Radeon MoE serving gets a first-party path; Strix Halo owners get more decode rows in the iGPU; online NVFP4→MXFP4 conversion eases checkpoint portability between Blackwell and Hopper/AMD.
- Source: vllm-project/vllm commit log, Nov 20 2026 — https://github.com/vllm-project/vllm/commits/main

**SGLang wave: Kimi-K3.1 + DeepSeek-V4.1 on Ascend, SynthID watermarking, AVX10.2 CPU FP8, Hy4 cookbook** (event date Nov 20)
In window: #48420 adds Kimi-K3.1 model support with the DSA attention path routed on Huawei Ascend; #47660 supports DeepSeek-V4.1 on Ascend A5; #45894 ships per-request SynthID Text watermarking; #51459 adds per-token FP8 quantization for CPU inference with AVX10.2 runtime dispatch; #51691/#51700 land the Hy4 cookbook and install docs; #43279 ports Wan2.2-S2V-14B speech-to-video with XPU support; #48314 adds H20-3e fused-MoE configs for GLM-5.3-Flash TP8/TP4; #49554 speeds small-M segmented BF16 matmuls on gfx950.

- *Why it matters:* The Ascend enablement of frontier open models (K3.1, DSv4.1, Hy4 the same week) is the strongest sign yet that the Huawei stack is a real second ecosystem for open-weight serving outside CUDA; SynthID watermarking is the first mainstream serving-level text watermark opt-in.
- *Local inference:* AVX10.2-gated CPU FP8 matters for next-gen Xeon-class boxes serving quants at the margins; consumer AMD sees gfx950 (Radeon AI Pro) decode-side work.
- Source: sgl-project/sglang commit log, Nov 20 2026 — https://github.com/sgl-project/sglang/commits/main

**Also noted:**
- Hugging Face Daily Papers for Nov 20 surfaced three local-serving-relevant items: NarrowSpec (speculative-decode drafters don't need the target's hidden width), "Training Vision Models for LLM-Driven Weight-Space Programming" (a WSP follow-up), and "On-Policy Morphing into Hybrid Attention Models" (post-training conversion to hybrid attention) — https://huggingface.co/papers

## Just missed the window
- TrendForce says NAND contract prices start falling in 1Q27 (0–5% QoQ, all categories downward from 2Q27) as Chinese cloud operators cut enterprise SSD orders more than US providers add — disclosed Nov 19; The Register pickup Nov 19 17:02 UTC was ~5.7 hours pre-window — https://www.theregister.com/systems/2026/11/19/nand-prices-tipped-to-fall-as-chinese-cloud-giants-cut-ssd-orders/5311116 (missed)
- Qualcomm opened Cirrascale AI Innovation Cloud trials of its rack-scale Dragonfly AI200 inference system (768 GB LPDDR per card, 43 TB per 140 kW liquid-cooled rack, targets up to 10T-parameter models); Qualcomm PR Nov 17, Register coverage Nov 19 14:03 UTC, pre-window — https://www.theregister.com/systems/2026/11/19/qualcomm-offers-test-drive-of-its-rack-scale-nvidia-ai-challenger/5310966 (missed)
- ComfyUI v0.46.0 released Nov 18 22:55 UTC (17:55 EST, between reported runs): MiniMax-H3-Image partner nodes, LTX AlphaGen/SDR-to-HDR/reframe/upscale nodes — and it removes DirectML, notable for AMD users on older Windows stacks — https://github.com/Comfy-Org/ComfyUI/releases/tag/v0.46.0 (missed)

## Coverage notes
- **Verified: 9 in-window items** (4 lead stories read in full), unit = underlying event; 3 "just missed" footnotes, each tagged for the ledger so they can never recur.
- **Ledger dedupe:** 8 candidates suppressed as already reported or already-footnoted: STH November-Top500 recap (missed-tagged Nov 19), MSI EdgeMesa review wave (product itself reported Nov 18; reviews Nov 18–20 are re-coverage), Hy4-flash repo trending (repo Nov 17; model reported Nov 16), NVIDIA CEO-transition items (out of scope), Intel 18A-yield/EMIB (footnoted once Nov 19), HF×OpenRouter partnership (Nov 16), wccftech V100 part-5 re-coverage (today's is the material update instead), ComfyUI Nov 19 commit coverage (reported Nov 19).
- **Dead ends:** r/LocalLLaMA anonymous feeds returned 404 on /new/.rss, /hot.rss and /top.rss (community Reddit lane unavailable this run); VideoCardz and TechPowerUp RSS failed to fetch; Tom's "power/cooling limits force 100k+ accelerator clusters into multiple data centers" (Nov 20 11:30 UTC, in-window) failed two URL resolutions and was logged, not chased; HN river in-window was low-signal (max 7 points on fetched stories).
