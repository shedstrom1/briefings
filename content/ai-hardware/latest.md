# AI Hardware & Local Models Brief — Nov 23, 2026 (07:45 ET)

Window: 2026-11-22 17:45 ET → 2026-11-23 07:48 ET (~14h).

**TL;DR**
- z.ai launches GLM-5.4, its first Loop Transformer flagship — 750B-scale, 28 of 78 layers run twice with shared weights, 1M context, vision added; closed (Coding Plan rollout Nov 23), open weights planned after safety hardening.
- Community quant ladder for Inkling 2 Mini: ji-farthing's ik_llama.cpp-native NVFP4/IQ4/IQ3/IQ2/IQ1 GGUFs (8 files, 6.5–19.0 GB), measured on GB10, created 02:24 ET today.
- Inference-software wave: llama.cpp b12647–b12651 (inkling parser, WebGPU ROLL, OpenCL xielu, Vulkan-friendly cleanups); vLLM #68685 backs Engram NVLink pool shards with Grace EGM (4x GB200, 188.8 GiB moved off HBM); SGLang ROCm/NPU/Intel-Arc fixes.
- No new silicon, GPU, or pricing news in the window.

## AI hardware and silicon
Nothing new in this window: no new GPU/accelerator launches, price movements, or silicon announcements beyond the software notes below.

## Model releases
### GLM-5.4 — z.ai's first Loop Transformer flagship
- **Event date: November 23, 2026** (released to all GLM Coding Plan users; z.ai blog post Nov 23)
- GLM-5.4 reuses 28 layers of the GLM-5 base and runs them twice per forward pass with shared weights — ~750B scale, ~36% more effective depth than the 750B non-looped equivalent at zero additional parameters. Intelligence reportedly comparable to 2T–3T parameter models. Z.ai converted a pre-trained base through continued training rather than training a looped model from scratch (scratch-trained loops trail unshared layers at equal pre-training compute), and adds independent per-token gates for stable loop-to-loop updates plus a hybrid KV cache (second pass reuses sparse entries from the first; a local sliding window keeps newly generated entries) plus reuse of the first pass's sparse-attention indices. A 30B controlled run showed ~10% extra training compute for the looped version and ~10–14% shorter average outputs, with the largest gains in math, scientific reasoning, and long-context tasks. Throughput reported up to 200 tok/s off-peak (70–80 at peak), 1M context. Closed model: API access and open weights planned after a safety evaluation — not locally runnable today.
- **Why it matters:** it is a new inference-architecture trade — looped depth at roughly one-third of the parameter cost — that any future open-weight GLM family will have to budget for on dense/MoE GPUs. It also changes the mental model for hardware sizing: a ~750B model with reused layers is not the same as a ~1.5T dense one, and the KV-cache trick materially cuts long-context deployment cost (Z.ai reports nearly identical results to recomputing indices per pass).
- **Local inference:** not runnable locally — no open weights, no quants; 750B-class closed API. Any future open-weight GLM-5.4 would plausibly land at GB300-class / multi-GPU scale, not on single RTX 5090-class hardware; a loop-aware runtime is required (standard llama.cpp/vLLM assume a single pass per layer).
- Sources: z.ai blog (https://z.ai/blog/glm-5.4), unite.ai (https://www.unite.ai/z-ais-glm-5-4-uses-iterative-depth-without-adding-parameters/, 2026-11-23), aiweekly.co (https://aiweekly.co/alerts/zai-ships-glm-54-a-750b-loop-transformer-that-runs-28-of-78-layers-twice, Nov 23 2026), HN story_50609685 (259 pts, 113 comments).

## Community spins and quantizations
### ji-farthing/Inkling-2-Mini-ik-llama-GGUF — NVFP4/IQ ladder for Inkling 2 Mini
- **Event date: November 23, 2026** (repo created 07:24 UTC = 02:24 ET)
- 8 files, all served from ik_llama.cpp (not llama.cpp) @ main 1180df7 or later with Inkling 2 Mini support (#2967), text-only (no vision tower): NVFP4-FP8B128 19.0 GB (5.83 bpw, KLD 0.0024, 97.8% same-top, 8.201 PPL), IQ4_KS 14.3 GB (4.38 bpw, KLD 0.0111), IQ4_KT 13.4 GB (4.12, 0.0179), IQ3_KS 11.7 GB (3.58, 0.0307), IQ3_KT 10.7 GB (3.27, 0.0357), IQ2_KS 8.6 GB (2.65, 0.1047), IQ2_KT 7.8 GB (2.40, 0.1188), IQ1_KT 6.5 GB (1.99, 0.2008, 81.9% same-top). KLD/same-top vs BF16 transformers on wikitext-2, 16×512 tokens, measured on NVIDIA GB10 (`-ngl 99 -fa on`); IQ1_KT gets a custom attention/embedding mix to avoid the KLD 0.357 default.
- **Why it matters:** an NVFP4-family ladder that reaches a 1.99 bpw point (6.5 GB, 81.9% same-top) — a leaner alternative to the published UD/IQ tiers for Inkling 2 Mini, and fully documented with KLD so the user can pick a tier by quality budget rather than file size.
- **Local inference:** GB10 (DGX Spark class, 128 GB unified); IQ2_KT at 7.8 GB is squarely in 8–16 GB card territory via CPU offload (`-ot exps=CPU`), Q4_KS at 14.3 GB fits a 16 GB card with headroom.
- **Source:** Hugging Face (https://huggingface.co/ji-farthing/Inkling-2-Mini-ik-llama-GGUF, indexed 2026-11-23T07:24:52Z).

## Inference software and benchmarks
### llama.cpp b12647–b12651 (Nov 23)
- **Event date: November 23, 2026** (b12647 03:18Z → b12651 08:52Z)
- Eleven in-window commits, headline: chat `inkling` parser/grammar improvement (#32833), WebGPU `ROLL` support (#31887, b12651), OpenCL `xielu` + q4_k coop-k c8 GEMM for spec verify (#32716), LFM2.5-VL template-detection fix (#32843), generic `ggml_backend_tensor_memset` fallback (#32574), cmake KleidiAI vendoring (#32605) and MATH_LIBRARY check fix (#32864), UI cancel-button for model load (#32225), GDN snapshot narrowing, and a Qwen3.6-27B test-snapshot refresh (#32845).
- **Why it matters:** ROLL support closes a missing-op gap for WebGPU; the q4_k spec-verify GEMM is a quiet throughput win for spec-decode on Adreno/OpenCL-class devices. Incremental, no breakage — a v1.0.0-line maintenance wave.
- **Local inference:** any platform llama.cpp supports — CPU, CUDA, Metal, Vulkan, Adreno/Hexagon, SYCL.
- **Source:** GitHub ggml-org/llama.cpp releases/commits.

### vLLM — Engram pool backed by Grace EGM; Inkling 2 Mini recipe move
- **Event date: November 23, 2026** (#68685 merged 07:55Z, #69296 merged 00:29Z)
- #68685 backs DeepSeek Engram NVLink pool shards with Grace Extended GPU Memory (EGM): on 4x GB200, DeepSeek-V4.1-Flash's two Engram tables occupy 47.21 GiB of HBM per GPU when sharded — placement exhausted memory while starting two DEP2 prefill engines. Moving 188.83 GiB of table storage from HBM to Grace memory lets one shared copy serve both engines; 4 GiB KV per rank, p50/p95 553/719 ms (vs 563/735 with host copies) at 16K input, concurrency 4/engine; Qwen FP8 PLE can use the same backing over NVLink (#68684, also in window). #69296 moves the Inkling 2 Mini recipe from the dedicated `vllm/vllm-openai:inkling-2-mini` image to the nightly image (matches the Inkling 2 Small pattern). Also in window: #69193 HY4 Flash CUDA platform-checks restore, #69229 private worker-local LoRA tensor loading.
- **Why it matters:** Grace EGM backing is the first engram-pool tier that frees HBM without host-NUMA copies — a real capacity win for 2×DEP2-scale serving on NVL72-class boxen.
- **Local inference:** GB200-class NVLink clusters; Qwen FP8 PLE and DeepSeek Engram-style table-offload paths only.
- **Source:** GitHub vllm-project/vllm, PR #68685, #69296.

### SGLang — ROCm/NPU/Intel-Arc fixes and a determinism launch fix
- **Event date: November 23, 2026**
- #52234 ROCm qknorm: fall back from the fused QK-norm kernel when norm weights differ in dtype; #52438 draft megakernels with an unmet spec should decline instead of refusing the server; #50968 NPU DSA-TAIL PD-transfer + decode-retraction; #52069 Arc Pro B70 diffusion perf baseline (CI-picked); #47410 fixed number of Triton decode blocks for large split budgets (deterministic launch).
- **Why it matters:** each is a small robustness/perf patch for the consumer-side stack — ROCm (AMD), NPU (Ascend), Intel Arc — rather than headline-scale changes.
- **Local inference:** ROCm/NPU/Intel Arc targets; no CPU/desktop-consumer impact.
- **Source:** GitHub sgl-project/sglang commits.

## Also noted
- llama.cpp #32799 Inkling 2 official tokenizer ignore_merges and #32807 host-expert copy-corruption fix for --n-cpu-moe (Nov 22 18:52Z / 19:04Z, ledger Nov 22 17:45 fire; already listed 2026-11-22).
- vLLM #69193 / #69229 noted above.

## Just missed the window
- aci-cpp (Actual Computer's llama.cpp fork): published Nov 18 — aci-cpp answers follow-ups 17–28% faster on DGX Spark (TTFT 0.78s vs 0.97s @64K), 46% faster decode on M3 Ultra over previous client build, 6–25% more total throughput on DGX Spark serving, 8/10 workloads ahead of llama.cpp v1.0.0. Re-surfaced on HN Nov 23 (story 50609848). Footnoted once — not re-listed.

## Coverage notes
- Verified substantive items: 5 (GLM-5.4; Inkling-2-Mini ik_llama GGUF; llama.cpp b12647–b12651 wave; vLLM #68685/#69296 wave; SGLang cluster).
- Reddit r/LocalLLaMA lane is UNREACHABLE today (search.rss 404 on this host — not a negative finding, per Reddit lane rules; login-less fallback disabled). HN was used for community-signal instead.
- Dedupe: ledger suppressed ~3 candidates in-window (aci-cpp HN re-surface Nov 23 → footnote-once; Inkling 2 Mini techzine.eu re-coverage Nov 23 12:33 PST → pre-window Nov 20 event; Qwen4-Max Together AI serverless listing Nov 21 → pre-window Nov 16 event). The 2026-11-22 17:45 ledger already covered 2026-11-22's in-window llama.cpp/vLLM/SGLang/HF uploads — nothing from that ledger repeated here.
- No fresh GPU/silicon, pricing, or DGX/GB300 hardware news in this window — honestly empty.
