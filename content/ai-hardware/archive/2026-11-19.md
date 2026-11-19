# AI Hardware & Local Models Brief — November 19, 2026 (07:45 fire)

**Window:** 2026-11-18 17:45 EST → 2026-11-19 08:27 EST (overnight sweep; new items only). Fire time 08:27 EST.

**TL;DR**
- Three decommissioned NVIDIA Tesla V100-16GB cards in a roughly $1,042 homelab rig hit ~101 tok/s on Qwen 3.6 35B at 4K context — and outpace a $5,399 M5 Max Mac Studio past 64K context.
- Patriot launches "AI Matrix": DDR5 memory and NVMe SSDs tier-matched to 7B–70B local models, with monthly open-source LLM validation.
- llama.cpp's overnight builds add a Vulkan stream-k GEMM, WASM relaxed SIMD with Q3_K/Q6_K, and VoxCPM2 multimodal support.
- vLLM, SGLang and TensorRT-LLM ship overnight merge waves: HY4 Flash model support, a Kimi-K3.1 FP4 indexer under DCP, MXFP8 KV scales in unified memory, and Qwen3.8-Flash-Next FP8 hybrid-cache refactors.
- A 100M-parameter 1.58-bit ternary code model ships as a 47 MB GGUF that runs locally on a 4 GB GPU in pure Rust, with no CUDA toolchain.

## AI hardware and silicon

**Three "e-waste" V100s hit 100 tok/s on Qwen 3.6 35B in a $1,042 rig**
Event date: 2026-11-19 (Tom's Hardware / Yahoo Tech, 11:00 UTC)
A homelab builder benchmarked nine builds — one to three decommissioned K80, P100 and V100-16GB cards on a single Supermicro X10DRG-Q with dual Xeon E5-2687W CPUs — across 130+ llama.cpp tests at Q4/Q8/F16 and 4K–128K context. Three V100-16GB cards at Q8 delivered 100.8 tok/s at 4K, 89.4 at 32K, 83.5 at 64K and 72.7 at 128K; the dense Qwen 3.6 27B managed only 24.3→14.2 tok/s on the same rig. At long context the V100s beat the oMLX-tracked M5 Max Mac Studio (40-core GPU, 128GB, $5,399) by ~19%; a paired H100/B200 comparison (sponsored by Parasail) put one B200 at ~455 tok/s. Power was ~0.02 kWh for a 128K 35B run, about half a comparable P100 build.
Why it matters: it is a concrete price/perf data point for MoE local inference on the used enterprise-GPU market — an MoE's ~3B active params make a 48GB pile of aging VRAM competitive with far pricier modern silicon at long context.
Local inference: 3× V100-16GB = 48GB VRAM, Qwen 3.6 35B (35B total / ~3B active MoE) at Q8 under llama.cpp; the custom blower cooler is still unreleased.
Source: "Three 'e-waste' Nvidia V100s hit 100 tokens per second on Qwen 3.6 35B in a $1,042 homelab rig" — Tom's Hardware / Yahoo Tech, published 2026-11-19 11:00 UTC — https://tech.yahoo.com/computing/articles/three-e-waste-nvidia-v100s-110000340.html

**Patriot AI Matrix — DDR5 + NVMe tier-matched for local AI**
Event date: 2026-11-19 (TechPowerUp 10:10 UTC; PRNewswire 15:00 CST/Taipei)
Patriot launched a dedicated "AI Matrix" line of DDR5 modules and M.2 2280 NVMe SSDs organized into Entry (7B–14B), Mid (14B–34B) and Pro (34B–70B) tiers. The Pro-tier MS560 is a PCIe Gen5 x4 SSD up to 14,000/11,000 MB/s (4TB) paired with a 64GB (2×32GB) DDR5-6000 kit; Mid pairs a 5,000 MB/s Gen4 MS450 with a 32GB kit; Entry pairs a Gen3 MS350 with 32GB DDR5-5600. Patriot says every tier gets monthly pp512/tg128 benchmark validation on Llama, Gemma and CodeLlama, and its spec page lists CPU-offload generation of 12.59 tok/s (MD57N 48GB), 9.19 (MD55N 32GB) and 5.44 (MD35N 16GB).
Why it matters: it is an attempt to sell memory and storage on model-size fit rather than raw capacity — a response to the fact that local LLM bottlenecks are memory bandwidth and checkpoint load, not just GPU compute. Treat the throughput numbers as vendor-reported marketing until independently reproduced.
Local inference: targets CPU-offload / no-discrete-GPU local builds from 7B to 70B; no prices or endurance ratings were published.
Source: "Patriot Launches AI Matrix SSD and DRAM Series Engineered for Local AI Workloads" — TechPowerUp, 2026-11-19 10:10 UTC — https://www.techpowerup.com/355024/patriot-launches-ai-matrix-ssd-and-dram-series-engineered-for-local-ai-workloads ; vendor page https://www.patriotmemory.com/ai-matrix

## Model releases

No qualifying new open-weight model release was first disclosed inside this window. Two high-profile items surfaced in-window but were excluded because their underlying release predates it: the HF blog "180B Parameters, 32GB of RAM" (published Nov 18) and open-athena/Marin-Mochi-535B-A23B (535B-A23B BF16, 57 checkpoints, OpenMDW-1.1 — final upload Nov 10, README Nov 17). Neither is re-listed; see Coverage notes.

## Community spins and quantizations

**psudo — 100M 1.58-bit ternary code model, 47 MB GGUF, pure-Rust engine**
Event date: 2026-11-19 (Show HN 08:17 EST; repo commits Nov 19)
A community release bundles a toy Python-indented language, a Polish-prefix IR (pIR), a borrow-checked Rust transpiler and a pure-Rust TQ2_0 ternary inference engine. The primary model is 100.7M params in a 47.0 MB GGUF (a 33.6M / 23.2 MB fallback is embedded); it was distilled from Qwen2.5-Coder-7B-AWQ over ~7,800 sandbox-verified trajectories. A pushdown-automaton mask over an 8,192-token vocab gives 0% syntax errors at decode and 45–60% fewer tokens via pIR. The author reports running it on a GTX 1660 Ti with 4GB VRAM, with no CUDA/C++/Python toolchain required — but warns it only handles small atomic function bodies and can produce logically wrong code with valid syntax.
Why it matters: it is a clean example of the sub-100M, extreme-quantization local-coding lane — useful as a local helper function generator beside a larger orchestrator, and a proof that 2-bit ternary codegen fits in a 4GB card.
Local inference: 47 MB primary GGUF, 4GB VRAM budget (tested on GTX 1660 Ti), pure Rust + memmap2, CPU-friendly; no benchmark tokens/s published.
Source: Show HN "Psudo – 47MB 2-bit coding model, Polish-prefix IR, and Rust transpiler" — 2026-11-19 13:17 UTC — https://github.com/steveSujo/psudo (commits https://github.com/steveSujo/psudo/commits/main/)

## Inference software and benchmarks

**llama.cpp overnight wave — b12528–b12540**
Event date: 2026-11-18 23:38 UTC → 2026-11-19 13:01 UTC
The pre-release stream landed a dozen tags overnight. Highlights: Vulkan stream-k MUL_MAT (#28528) splitting 256-element K chunks across SMs with a resolve shader for coopmat2; WASM relaxed SIMD with Q3_K/Q6_K kernels (#19590); mtmd support for VoxCPM2 (#32443); a fix for sticky NaN in recurrent state (#31913); OpenCL adjustable KV padding in decomposed attention (#32522); and a v1/realtime sleep-state fix (#32567). Tag b12527's limiter OOB guard (#32562) was already covered in the prior edition and is not re-listed here.
Why it matters: stream-k on Vulkan and relaxed SIMD on WASM widen the set of GPUs and browsers that get efficient GEMM/quant kernels, which changes what hardware can serve a given quant tier.
Local inference: Vulkan path benefits AMD/Intel GPUs; WASM path targets in-browser inference; no headline tok/s figures in the builds themselves.
Source: "Releases · ggml-org/llama.cpp" — tags dated 18–19 Nov 2026 — https://github.com/ggml-org/llama.cpp/releases

**vLLM overnight merge wave**
Event date: 2026-11-19 (merges inside window)
Merged PRs inside the window include HY4 Flash + multimodal support (#68296), a DeepSeek-V4.1-Flash GB300 Dynamo AgentX preset (#68556), an Unlimited-OCR-2 DASH routing batch-invariance fix (#68563), a KV-cache sizing fix that stops draft-only sliding-window buckets inflating groups (#68533), per-request Mooncake offload limits (#68529), a ROCm Qwen4Exp PLE short-conv prefill export (#68574), compressed-tensors WNA16 "fast start" (#68328), and Python 3.14 as the default for images/CI (#68457).
Why it matters: day-0 HY4 Flash support and the speculative-decoding KV fix change which models and quant formats run cleanly on GB300 and ROCm hardware.
Local inference: GB300 / ROCm MI-series oriented; the WNA16 and KV fixes matter most for low-bit, memory-constrained serving.
Source: vLLM merged PRs, 2026-11-19 — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged+merged%3A2026-11-18T22%3A45..2026-11-19T13%3A27

**SGLang overnight merge wave**
Event date: 2026-11-19 (merges inside window)
Merges include inkling2 SM90 Marlin/expert/MTP kernels (#51302), turning the DFLASH draft KV ring off by default (#51386), an FP4 indexer for Kimi-K3.1 under DCP (#51450), storing MXFP8 KV scales in unified-memory pages (#51296), placing hybrid-SWA sidecar draft pools on target pages (#51300), per-layer FA overrides with block-anchored windows (#51297), an RTX PRO 6000 recipe for DeepSeek-V4.1 (#51410), and an NPU DSV4-Flash 128K prefill fraction fix (#51417).
Why it matters: the inkling2 and K3.1 kernels plus the unified-memory KV change extend high-throughput spec-decode to Blackwell and AMD/NPU targets, shifting the hardware fit for long-context serving.
Local inference: SM90 (Ada/Hopper) and RTX PRO 6000 recipes are the most desktop-relevant; NPU path targets Ascend-class accelerators.
Source: SGLang merged PRs, 2026-11-19 — https://github.com/sgl-project/sglang/pulls?q=is%3Apr+is%3Amerged+merged%3A2026-11-18T22%3A45..2026-11-19T13%3A27

**TensorRT-LLM overnight merge wave**
Event date: 2026-11-19 (merges inside window)
Merges include raising the Kimi K3 NVFP4 DEP8 memory gate (#22615), running the DSv4.1 "1017" NVFP4 export through a GB300 pre-merge GSM8K gate (#22592), a Qwen3.8-Flash-Next FP8 hybrid-cache refactor in tests (#22580), storing the qwen4_exp PLE host table once per host by default (#22517), honoring dummy all-reduce in MegaMoE scheduling (#22462), and Muse Spark Open AgentX GB300 serving recipes (#22498).
Why it matters: these keep NVIDIA's serving stack current with the same NVFP4/FP8 model revisions the community is quantizing, affecting GB300/B300 deployment readiness.
Local inference: datacenter/GB300-focused; little direct consumer-GPU impact this window.
Source: TensorRT-LLM merged PRs, 2026-11-19 — https://github.com/NVIDIA/TensorRT-LLM/pulls?q=is%3Apr+is%3Amerged+merged%3A2026-11-18T22%3A45..2026-11-19T13%3A27

## Also noted

- vLLM #68541 enables sampler-flag/logits-processor tests on Intel XPU; #68566 caps nvidia-cudnn-frontend below 1.33 (build compatibility).
- SGLang #51386 disables the DFLASH draft KV ring by default, a stability revert worth noting for anyone relying on it.
- llama.cpp b12540 removes PATH_MAX usage; b12529 fixes a sticky-NaN recurrent-state bug relevant to recurrent/hybrid models.

## Coverage notes

Verified count: 7 qualifying items (1 local-inference benchmark, 1 local-AI hardware launch, 1 community low-bit model/engine, 4 inference-stack merge waves) from 5 distinct source families: Tom's Hardware/Yahoo Tech, TechPowerUp/PRNewswire, and the GitHub release/PR feeds for llama.cpp, vLLM, SGLang and TensorRT-LLM, plus Hacker News.

Ledger dedupe: 11 in-window candidates suppressed as already reported or already footnoted "never again" — DDR6 JEDEC preview (re-coverage), MSI EdgeMesa N AI+ (footnoted Nov 15; RTX Spark desktops reported Nov 18 12:45), Intel 18A 70% yields / Microsoft EMIB (EMIB-T footnoted Nov 17), Intel Diamond Rapids SC26 live demo (Bull SC26 package reported Nov 18 12:45), Edge0-145B repo updates (footnoted Nov 13), llama.cpp #32562 (reported Nov 18 23:43), vLLM HY4-Flash recipe #68295 (reported Nov 18 23:43), and re-surfaced PR sub-items already covered in prior afternoon waves.

Out-of-window / undated (excluded, no footnote used): NVIDIA GeForce SSD-write driver bug (16:00 EST Nov 18, pre-window and no AI angle); Tideway MoE tiered-memory paper arXiv 2611.14848 (Nov 10); HF "180B Parameters, 32GB of RAM" MoE-streaming blog (published Nov 18); Marin-Mochi 535B-A23B (final checkpoint Nov 10, README Nov 17); The Register "Uncensored AI models thrive on code hosting platforms" (fetched only as a headline — full article unretrievable, so not cited).

Limitations: r/LocalLLaMA was unreachable this run (reddit-reading anonymous Atom feed returned HTTP 404 and 429; no OAuth credentials configured), so community chatter — normally a rich source of GGUF ladders and local benchmarks — was covered only via Hacker News and GitHub. The DuckDuckGo-backed web_search returned "No results found" on several queries; discovery leaned on timestamped index/feed pages (Tom's Hardware, TechPowerUp, The Register, ServeTheHome, HN Algolia, GitHub) instead. No "Just missed the window" footnote was added, since the only once-available slot is being held rather than spent on a weak candidate.
