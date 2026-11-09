# AI Hardware & Local Models Brief — Mon Nov 9, 2026 (07:45 ET)

**Window:** 2026-11-08 17:45 ET → 2026-11-09 07:45 ET (previous fire → now; ~14h overnight sweep)

**TL;DR**
- **MoEShift**, an open-source Apple Silicon MoE inference backend, was benchmarked overnight on 24GB Macs: Qwen3.8-Flash-Next 125B (GSQ-RCO IQ3_XXS) runs with only **8.5GB peak process memory** (vs 14GB for Strata), and an M5 Pro hits **374 tok/s prefill** — roughly 2.6x the M4 Pro's 199.
- **Overnight GitHub waves are heavy:** 14 llama.cpp PRs (builds through b12221), 52 vLLM PRs (Kimi K3.1 TP8 serving split, RDMA-capable KV offload tier, Qwen4Exp DeepGEMM MegaMoE), and 59 SGLang PRs (ComfyUI FastH3/H3 FP8/Qwen-Image 2.1 Turbo loaders, AMD UALink all-reduce, DGX Spark env restore).
- **mradermacher** dropped a fresh overnight quant cluster: LFM3-Omni-2B heretic/SOMPOA-heresy family, LFM3-1.7B Uncensored/DERISKED, Mercury-Acad-1.7B, and a 14B Qwen2.5 SOMPOA spin.
- **Hardware news is quiet** — weekend outlets ran nothing new in the local-AI silicon space; Techmeme's river was entirely the Nvidia board probe into Jensen Huang (corporate, not silicon).

---

## Inference software and benchmarks

**MoEShift runs 125B Flash-Next on a 24GB Mac with 8.5GB peak memory; M5 Pro prefill benchmark lands** — Event date Nov 9, 2026 (thread posted 12:10Z / 07:10 ET).
 Davide Troiani (MoEShift author) published a head-to-head community benchmark running Qwen3.8-Flash-Next 125B (ISTA-DASLab GSQ-RCO IQ3_XXS, 76GB on disk) on a Mac mini M4 Pro 24GB and a 24GB M5 Pro, using MoEShift's shared SSD expert runtime. M5 Pro: 374.15 tok/s prefill, 14.69 tok/s short decode, 83.9s → 55.1s long-prompt inference time. M4 Pro: 199.09 / 9.84 / 83.9s. Both held peak physical process memory at 8.5GB — the README comparison shows MoEShift at 6.4–7.5GB vs Strata 0.2.7 at 14GB on the same M4 Pro, with 2.6x faster long-prompt processing. MoEShift (repo public Oct 21, alpha, C++) uses Lookahead Expert Scheduling and a protected cache over llama.cpp/ggml; installs via Homebrew and serves an OpenAI-compatible API on localhost:8080.
- **Why it matters:** This is the clearest evidence yet that 125B-class MoE models are practical on consumer 24GB Apple Silicon — not just "it boots" but agent-usable prefill speeds with the rest of the RAM left for the OS and other apps.
- **Local inference:** Mac mini/MacBook Pro M4 Pro or M5 Pro, 24GB unified memory; needs ~120GB of SSD for the prepared expert files; OpenAI-compatible endpoint at localhost:8080.
- Source: u/dave-tro, "M4 Pro vs M5 Pro (24GB): Qwen 3.8 Flash Next IQ3 with MoEShift" — posted 2026-11-09 12:10 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xrq2fv/m4_pro_vs_m5_pro_24gb_qwen_38_flash_next_iq3_with/
- Source: davide-troiani/moeshift GitHub README (benchmarks + install) — https://github.com/davide-troiani/moeshift (repo created 2026-10-21; pushed 2026-11-04)

**llama.cpp overnight build wave: b12212 → b12221 (14 merged PRs)** — Event date Nov 9, 2026 (merges 08:10Z–12:42Z; release b12221 at 12:28Z / 07:28 ET).
 The overnight merge set includes Granite NVFP4 weight-scale attachment (#31449, fixes #31448), YaRN truncation config support across OpenVINO/SYCL/Vulkan/WebGPU/RPC (#30802), RPC loading-time improvements (#29413), OpenCL f32 MUL_MAT_ID (#31783), Metal flash-attention out-of-bounds write guard (#31662), Pocket TTS generic temperature support via API in mtmd (#31655), and a gfx1151 ROCm CI move to Strix Halo devlab runners (#31567). Builds b12220 (12:00Z) and b12221 (12:28Z) shipped binaries for CUDA 12/13.5, ROCm 10, Vulkan, SYCL, OpenVINO, and Snapdragon/Hexagon.
- **Why it matters:** The Granite NVFP4 fix and YaRN truncation rollout widen quantized long-context support on non-CUDA backends, and the Metal FA guard addresses a real stability bug on Apple Silicon.
- **Local inference:** All llama.cpp targets — CPU, CUDA 12/13.5, ROCm 10, Vulkan, SYCL, Metal, OpenCL, OpenVINO, WebGPU, RPC.
- Source: GitHub ggml-org/llama.cpp merged PR search (window-filtered) — merged 2026-11-08 22:45Z → 2026-11-09 12:45Z — https://github.com/ggml-org/llama.cpp/pulls?q=is%3Apr+is%3Amerged
- Source: Release b12221 — published 2026-11-09 12:28 UTC — https://github.com/ggml-org/llama.cpp/releases/tag/b12221

**vLLM overnight merge wave: 52 PRs including Kimi K3.1 TP8 serving and RDMA KV tier** — Event date Nov 9, 2026 (merges 23:15Z Nov 8 → 12:36Z Nov 9).
 Highlights: Kimi K3.1 served at TP8 via the KV-cache/attention "split 3/5" (#66037); Qwen4Exp routed experts moved onto DeepGEMM MegaMoE (MXFP8/MXFP4) (#64315); a new RDMA-capable OBJ tier for KV offload (#56192, building on #55855); XPU all-to-all expert-parallelism backend for batched MoE (#46871); ROCm SimpleCPUOffload SDMA batched copies (#65559); fused multi-step draft decode enabled for ROCm unified attention (#63096); and Inkling NVIDIA sconv cache block-size fix (#51951). The Rust frontend got a large parity batch (logprob token spelling, tool_reference content parts, scale-out route logging, metrics export).
- **Why it matters:** The K3.1 TP8 split and MegaMoE paths are the serving-side plumbing for 2.8T-class open-weight MoE serving on multi-GPU boxes; the RDMA OBJ tier extends low-cost CPU/RDMA hybrid serving.
- **Local inference:** GPU serving stacks (CUDA, ROCm MI355/MI300X, XPU/Intel Arc, CPU offload); relevant to 4–8 GPU local/server builds running Kimi K3.1 or Qwen4Exp.
- Source: GitHub vllm-project/vllm merged PR search (window-filtered, 52 items) — merged 2026-11-08 22:45Z → 2026-11-09 12:45Z — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged

**SGLang overnight merge wave: 59 PRs including ComfyUI image-model loaders and AMD UALink all-reduce** — Event date Nov 9, 2026 (merges 22:49Z Nov 8 → 12:50Z Nov 9).
 Highlights: multimodal sglang-miles gets the Inkling token-space strategy and serving spec (#49126); minimax-sparse decode indexer runs on Hopper TP8 with attention DP2 (#49088); AMD custom-all-reduce carv2 runs across gfx1250 UALink nodes (#49003); HiCache adds a TMA copy kernel for unified layout (#48786); ComfyUI integration now loads FastH3, H3 FP8, Qwen-Image 2.1 Turbo and community INT8 single files (#48656); Intern-S2-Mobius CPU support (#48364); and the DGX Spark b12x environment was restored in rope/env cleanup (#49171). Also in: NPU DSpark argmax/verify scheduler kernels (#47581) and a unified memory-cache layout planner series (#48009–#48014).
- **Why it matters:** The ComfyUI loader cluster means local image-generation serving can pull Turbo/FP8/INT8 checkpoints directly; UALink all-reduce extends AMD multi-node local-cluster serving.
- **Local inference:** CUDA Hopper/Blackwell, AMD MI3xx/gfx1250 clusters, Intel NPU, CPU — SGLang targets all of these; DGX Spark users get the restored b12x env.
- Source: GitHub sgl-project/sglang merged PR search (window-filtered, 59 items) — merged 2026-11-08 22:45Z → 2026-11-09 12:45Z — https://github.com/sgl-project/sglang/pulls?q=is%3Apr+is%3Amerged

---

## Community spins and quantizations

**mradermacher overnight i1 quant cluster (LFM3 family + SOMPOA spins)** — Event date Nov 9, 2026 (HF repo creation timestamps 06:24Z–12:34Z).
 Fresh GGUF repos include: LFM3-Omni-2B-heretic-i1 and LFM3-Omni-2B-SOMPOA-heresy-i1 (edge omni models with voice-cloning/ASR tags), LFM3-VL-2B-Surgical-Abliterated-i1, LFM3-1.7B-Uncensored-i1 and LFM3-1.7B-DERISKED-i1 (Blackfrost baked-weights variant), Mercury-Acad-1.7B-i1 (Liquid AI academic/math/agent 1.7B), 14B-Qwen2.5-Kunou-v1-SOMPOA-heresy (+ i1), and 17btest-nov5-1x8k-i1. All carry imatrix where applicable.
- **Why it matters:** The LFM3-Omni and 1.7B academic quants keep Liquid AI's edge family running on phones/RPi5/NUD-class hardware; the SOMPOA-heresy cluster is the recurring "abliterated reasoning" pattern at 14B.
- **Local inference:** LFM3 1.7B/2B tier — under 4GB quantized, phone/RPi5/NPU class; 14B SOMPOA — 16GB VRAM / Mac unified-memory class.
- Source: Hugging Face API (author=mradermacher, sort=createdAt) — repos created 2026-11-09 06:24Z–12:34Z — https://huggingface.co/mradermacher?search_models=&sort=created

---

## Also noted

- **Ryzen AI Max+ 395 llama.cpp tuning thread** (Nov 9, 11:06Z): u/Prudence-0 reports a 48% performance gain running QFN3.8 on the 96GB-VRAM-configured Strix Halo with tuned llama-server flags (`--fit on`, `-ctk q8_0 -ctv q8_0`, `--kv-unified`, cont-batching); community cross-references gufo (1,200–1,700 tok/s prefill claims) and notes Strata is wrong for 128GB unified-memory boxes. Source: https://www.reddit.com/r/LocalLLaMA/comments/1xrowl7/best_llamacpp_settings_for_qwen38flashnext_on/ — Local inference: Ryzen AI Max+ 395 (Strix Halo) 128GB unified / 96GB VRAM-allocated.
- **4x Radeon AI PRO R9700 QFN benchmark** (Nov 8, 23:36Z): u/Anarchaotic joins the 4x R9700 club — ~190 tok/s writing per user, ~6,800 tok/s prefill total (user-count invariant), agent follow-ups 80–225 tok/s/user with 0.2–2.5s start latency. Source: https://www.reddit.com/r/LocalLLaMA/comments/1xrcia4/joined_the_4x_r9700_club_initial_benchmarks_for/ — Local inference: 4x R9700 32GB (ROCm), multi-user serving.
- **RTX 5090 Qwen3.8-27B one-shot bench** (Nov 9, 01:33Z): Q4_K_M (120K ctx) and Q5_K_M (90K ctx) both at 110–120 tok/s in OpenCode 2 + LM Studio with MTP on; 31GB VRAM used. Source: https://www.reddit.com/r/LocalLLaMA/comments/1xrexe0/quick_one_shot_pelican_rtx_5090_test_qwen_38_27b/ — Local inference: single RTX 5090 32GB.
- **TimeScroll Show HN** (Nov 9, 11:19Z): open-source local Mac screen recorder (Rewind replacement) — HEVC segments, ~2GB/month storage, 1.1% CPU on M4, uses Ollama local models for timeline summaries via macOS Accessibility API text capture. Source: https://news.ycombinator.com/item?id=50424070 — Local inference: Apple Silicon Mac, Ollama backend.
- **Torchlens** (Nov 9, 03:25Z): new GitHub repo for inspecting, visualizing and intervening on any operation in any neural network — relevant to the weight-editing/brain-surgery wave. Source: https://www.reddit.com/r/LocalLLaMA/comments/1xrh5iz/github_repo_torchlens_inspect_visualize_and/ — Local inference: research tooling, any stack.
- **L11 N8147 "neuron" study** (Nov 9, 06:42Z): community weight-editing curiosity on Qwen-3.5-4B — one neuron dial makes the model confidently wrong and snarky/poetic. Source: https://www.reddit.com/r/LocalLLaMA/comments/1xrkomn/l11_n8147_is_a_neuron_that_will_make_qwen354b_get/ — Local inference: 4B-class models, weight-level editing.

## Just missed the window

- **missed:** Next-Concept Modeling (NCM) — Stanford/Hazy Research parallel coarse-to-fine text generation paper (arXiv 2610.14162, v1 Oct 9, 2026) claims 10–31x faster generation than diffusion baselines with 4–5x fewer training tokens; r/LocalLLaMA discussion (19:19Z Nov 8) and the paper itself both predate the window. https://arxiv.org/abs/2610.14162
- **missed:** "Resurrecting 2013 E-Waste" — native CUDA Mamba-1 + PyTorch 2.7.1 port to legacy Kepler–Turing GPUs (sm_35–sm_75) on Windows (posted 21:09Z Nov 8, ~1.5h pre-window). https://www.reddit.com/r/LocalLLaMA/comments/1xr97iz/resurrecting_2013_ewaste_native_cuda_mamba1/

## Coverage notes

- **Verified items:** 10 (4 lead + 6 also-noted) plus 2 "Just missed" footnotes. Counting unit: one entry per underlying event; GitHub waves are reported as single merge-set entries, not per-PR.
- **Ledger dedupe:** 7 candidates suppressed — Arc Pro B70 vs RTX 5090 benchmark (reported 2026-11-08 17:45), Kimi K3.1-Pro-Coder 775GB (already footnoted `missed:`), Kimi K3 Optane PMem200 (already footnoted `missed:`), Qwen3.8-27B-DSpine-b16 (already footnoted `missed:`), llama-modchip v2.4.0 (already footnoted `missed:`), kapitsa quant batch (already footnoted `missed:`), and the Arete-27B GGUF quant thread (reported 2026-11-06/07; post itself at 22:17Z pre-window).
- **Hardware news:** Quiet overnight. Techmeme, VideoCardz, and Tom's Hardware had no new local-AI silicon items in the window; the dominant river item (Nvidia board probe into Jensen Huang / export-control fallout) is corporate/governance news with no hardware or local-model tie, and is out of scope.
- **Indexes used:** Techmeme river, HN Algolia (search_by_date "llama"), r/LocalLLaMA new feed (anonymous Atom), Hugging Face API (trendingScore + author feeds), GitHub merged-PR search (llama.cpp/vLLM/SGLang), llama.cpp/Ollama/TensorRT-LLM release APIs. No dead ends requiring abandonment; Reddit JSON endpoints were blocked for direct fetch but recovered via the skill's Atom backend.
