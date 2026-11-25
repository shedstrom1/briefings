# AI Hardware & Local Models Brief — Nov 25, 2026 afternoon

Window: 2026-11-25 07:45 → 14:26 EST (delayed 12:45 fire; START = prior fire, END = now). New items only; everything in the already-reported ledger is suppressed.

- **llama.cpp 1.1.0 release marker lands.** The version-bump PR merged 10:11 ET, capping a week of model and engine work; the formal git tag was not yet present at the window close.
- **SK hynix puts 3D DRAM on a ~2031 timeline.** A 100-layer stacked-memory roadmap was detailed at a Seoul forum — a capacity trajectory item, not a product shipping today.
- **Marathon Local, a free C++ Apple-Silicon inference engine, claims 1.1–1.5× over llama.cpp** across nine models on one M5 Max, using each model's NVFP4 checkpoints.
- **Supermicro's fanless Panther Lake edge box** pairs a Core Ultra X7 368H and Arc B390 in a 1.5 kg chassis — an edge-inference form factor, not a discrete-VRAM workstation.
- **A zero-CUDA engine claims 85,285 tok/s training on a DGX Spark GB10** — but PyTorch was 1.5–3× faster in its own head-to-head and it scored 0/159 on a coding bench.

## AI hardware and silicon

**SK hynix targets ~2031 for 100-layer 3D DRAM**
Event date: November 25, 2026 (forum talk and same-day reports).
At the AI Semiconductor Forum at Seoul National University, SK hynix distinguished engineer Choi Kang-sik said the company is developing 3D DRAM with mass production in view within four to five years, commercialization around 2031, and a first product stacking about 100 memory-cell layers — roughly a third more than the ~75 layers needed to match 10-nm-class density. The company is still working through interconnect, etch and materials approaches. TheElec and TechPowerUp carried the disclosure the same day, with derivative coverage following.
Why it matters: 3D DRAM is the memory industry's answer to the density wall that also constrains HBM and, downstream, how much weight and KV cache a machine can hold. It is a roadmap signal, not a shipping SKU.
Local inference: no VRAM or price impact today — this is a ~2031 server/memory roadmap. Read it as the long-horizon capacity trajectory behind future HBM and high-capacity inference memory, not something that fits a 16 GB card or a DGX Spark now.
Source: "SK hynix to Start 3D DRAM Production in 2031," TechPowerUp, 2026-11-25 17:34 UTC (12:34 ET) — https://www.techpowerup.com/355210/sk-hynix-to-start-3d-dram-production-in-2031
Source: "SK hynix Targets 100-Layer 3D DRAM Production Within Five Years," TheElec, 2026-11-25 — https://www.thelec.net/news/articleView.html?idxno=15925

**Supermicro SYS-E103-14P-H: a fanless Panther Lake edge appliance**
Event date: November 25, 2026 (review published).
ServeTheHome's review covers a 1.47 kg finned chassis built around Intel's Core Ultra X7 368H — a 16-core Panther Lake part (4P + 8E + 4 LPE, up to 5.0 GHz) with an Intel Arc B390 iGPU (Xe3, 12 Xe cores). The as-reviewed unit has 32 GB DDR5-5600 in two user-replaceable SODIMMs, a PCIe Gen5 x4 M.2 boot SSD, three Ethernet ports (2x 2.5GbE I226-IT plus a vPro-capable 1GbE I219-LM), dual HDMI 2.1, dual RS-232 COM and a DB9 GPIO, on a 150 W external adapter with 12–36 V wide-range DC input. The chassis is the heatsink; there are no fans.
Why it matters: it is a concrete example of the fanless, wide-DC, industrial-I/O box class that Panther Lake is enabling for on-device inference at the edge.
Local inference: integrated Arc B390 iGPU plus Panther Lake NPU, with 32 GB of shared DDR5 in the reviewed config (SODIMM-upgradeable). No discrete VRAM and no NPU TOPS figure stated in the review — treat it as a low-power edge inference target, not a 5060 Ti / DGX Spark-class acceleration box.
Source: "Supermicro SYS-E103-14P-H Review: A Fanless Panther Lake Edge Appliance," ServeTheHome, 2026-11-25 18:07 UTC (13:07 ET) — https://www.servethehome.com/supermicro-sys-e103-14p-h-fanless-panther-lake-review/

## Model releases

No new open-weight model checkpoints or license changes were first disclosed inside this window. The in-window llama.cpp work adds *support* for models whose weights were announced earlier (see Inference software). No closed release arrived with a local/quantization hook worth reporting.

## Community spins and quantizations

**Omega Helix: a zero-CUDA training/serving engine on raw Blackwell silicon**
Event date: November 25, 2026 (repository made public 09:59 ET).
The `omega-helix` repo presents a single static binary — roughly 773 KiB — that implements a BPE tokenizer, BF16/FP32 SM121 GPU kernels, a training loop and an OpenAI-compatible server in a purpose-built language, opening `/dev/nvidia*` through raw ioctl calls with no CUDA, cuBLAS, PyTorch or libc. Its headline is 85,285 tok/s BF16 training on a DGX Spark GB10, but the project's own matched-model head-to-head shows PyTorch eager at 130,769 tok/s and `torch.compile` at 257,463 tok/s on the same 15.8M-parameter model, and all three coding-bench arms scored 0/159. Those are author-reported numbers on a tiny model, with a documented missed benchmark.
Why it matters: worth watching as a proof that a hand-written runtime can drive current silicon without NVIDIA userspace libraries, but the throughput claim should not be read as a win over PyTorch.
Local inference: targets DGX Spark GB10 (SM121) and runs CPU-only on Apple Silicon or x86/aarch64 Linux for verification; the binary is small but the demo model is 15.8M parameters, so none of this models real 27B/125B-class local workloads. Apache-2.0.
Source: GitHub repository `drakestapleton/omega-helix`, public 2026-11-25 14:59 UTC (09:59 ET) — https://github.com/drakestapleton/omega-helix
Source: Show HN "LLM Training NO CUDA 85k tok/SEC locally," 2026-11-25 19:34 UTC — https://news.ycombinator.com/item?id=50646377

## Inference software and benchmarks

**llama.cpp 1.1.0 release marker merges; b12723–b12728 ship**
Event date: November 25, 2026 (version-bump PR merged 10:11 ET; builds through 10:18 ET).
PR #33043, "bump version to 1.1.0," merged with the release notes for what the project calls llama.cpp 1.1.0. The consolidated feature set includes the Inkling 2 family (base, Mini, DSpark), Domyn Edge 4B and Zaya1 8B, DFlash speculative decoding split into distinct `draft-dflash`/`draft-dflash2`/`draft-dspark`/`draft-lilicorr` methods, deprecation of the old `llama_batch` API in favour of `llama_batch_ext`, token-aware chat parsing, named `tool_choice`, and `--target-bpw`/`--target-size` per-tensor quantization targeting. ggml moves to 1.1.0. Most constituent changes were reported individually in earlier editions; the in-window event is the version milestone. A formal v1.1.0 git tag was not present at the window close. Builds b12723, b12726 and b12728 published inside the window.
Why it matters: 1.0.0 was the stability milestone on Nov 13; 1.1.0 is the first feature minor, and it is the marker downstream packagers and UI projects will pin to.
Local inference: GGUF across CPU and CUDA/ROCm/Metal/Vulkan; the engine spans 16 GB single cards to multi-GPU. The headline add — Inkling 2 Mini — is a ~16.6 GB-class model with an NVFP4 checkpoint, so it fits a 24 GB card or a 36 GB Apple-Silicon machine.
Source: PR #33043, "llama.cpp: bump version to 1.1.0," merged 2026-11-25 15:11 UTC (10:11 ET) — https://github.com/ggml-org/llama.cpp/pull/33043
Source: llama.cpp releases b12723 / b12726 / b12728, 2026-11-25 14:00 / 14:42 / 15:18 UTC — https://github.com/ggml-org/llama.cpp/releases

**Marathon Local: a free C++ inference engine for Apple Silicon**
Event date: November 25, 2026 (Show HN post 12:05 ET; benchmarks measured Nov 9–20).
Subconscious Systems published Marathon Local, a free C++ engine for Mac that it claims beats mlx-lm, vllm-metal and llama.cpp on nine tested models using the same Mac (M5 Max, 36 GB) and each model's NVFP4 checkpoint. One-stream decode behind a 32k-token prompt: Inkling 2 Mini + MTP 47.7 tok/s (llama.cpp 31.9; the other two cannot load it), Qwen3.8-27B + MTP 41.7 (llama.cpp 31.5; vllm-metal 20.5; mlx-lm OOM), Qwen3.6-35B-A3B + MTP 127 (llama.cpp 77.2), Gemma 4 26B-A4B 94.7 (llama.cpp 60). MTP heads are used where available; llama.cpp runs the nearest 4-bit GGUF. These are author-published numbers from a single machine, not an independent reproduction.
Why it matters: a fourth credible engine in the Mac local-inference stack, and one that exploits MTP speculation that mlx-lm and vllm-metal cannot use on these hybrid models.
Local inference: Apple Silicon only (MacBook/Studio/mini); tested on a 36 GB M5 Max with NVFP4 checkpoints. Models run include Inkling 2 Mini (~16.6 GB) and Qwen3.8-27B; an Enterprise tier adds inference-time context compression for a 1M-token window on 36 GB.
Source: "Marathon Local: Free, Fast LLM Inference for Mac," Subconscious, Show HN 2026-11-25 17:05 UTC (12:05 ET) — https://www.subconscious.dev/local
Source: Show HN thread, 2026-11-25 — https://news.ycombinator.com/item?id=50644653

**vLLM cuts Kimi K3 specialized-decode startup compiles by about two-thirds**
Event date: November 25, 2026 (PR merged 11:28 ET).
PR #69669 changes the NVIDIA Kimi-K3 specialized decode path (`decode_backend=latency_specialized`) so each kernel is compiled once, only for captured sizes. On 8x B300 with Kimi-K3.1-Pro at `--max-num-seqs 64`, CuTe DSL compiles per rank fall from 1,435 to 479 at TP8 and server bring-up after weight load from 583 s to 260 s; decode performance is unchanged.
Why it matters: startup time, not steady-state throughput, is the pain point for large-MoE serving, and a 2× cut in time-to-serve is a real operational win.
Local inference: data-center only — 8x B300/GB300-class hardware with Kimi-K3.1-Pro. No consumer-GPU or single-card path.
Source: PR #69669, "[Perf][Kimi K3] Specialized decode: compile each kernel once, only for captured sizes," merged 2026-11-25 16:28 UTC (11:28 ET) — https://github.com/vllm-project/vllm/pull/69669

### Also noted

- **vLLM in-window merge wave (7:45–14:26 ET).** Alongside #69669: Mamba1 models and kernels removed (#68177), TeleFLM support removed (#69929), `time_to_first_content_token_ms` added to per-request metrics (#56914), HY4 Flash allowed on `fp8_ds_mla` sliding-window MLA (#68365), and ROCm MoRIIO READ-mode fixes for mixed-precision KV (#67074, #67216). Data-center ROCm/NVIDIA paths; no consumer single-card change. Sources: vLLM PRs #68177/#69929/#56914/#68365/#67074/#67216, merged 2026-11-25.
- **SGLang lands the AMD Inkling-2 MI35x kernel series and a Mooncake bandwidth fix.** PRs #52726 (attention), #52727 (MoE routing / expert GEMM for NVFP4 and MXFP8) and #52728 (norm, short-conv, MTP) add gfx950 support for Inkling 2 on ROCm, where the CuTe DSL versions do not build; #52906 caps Mooncake ranged-get sizes so large KV load batches keep bandwidth. Targets MI350x/MI355x; no NVIDIA or consumer-GPU benefit. Source: SGLang PRs #52726/#52727/#52728/#52906, merged 2026-11-25 (11:04 UTC onward).
- **TensorRT-LLM CI/fix wave.** Seven PRs merged in-window: VisualGen GR100 shard coverage for Cosmos3/MiniMax-H3/FLUX.2 (#21943), host-resident table release on failed executor construction (#22878), encoder-replay row accounting for context-logits (#22742), and an FP8 wide-tactic MoE check that avoids e4m3 rounding ties (#22890). Day-0 qualification and bugfixes, not a new consumer feature. Source: TensorRT-LLM PRs #21943/#22878/#22742/#22890, merged 2026-11-25.
- **llama.cpp adds Raon-1-mini-preview support and enables Adreno E17 kernels by default.** PR #32176 adds HF-to-GGUF conversion and inference for KRAFTON's 30B-A4B MoE (base weights announced Oct 21); PR #33013 turns off compiler quirks so flash-attention and MoE kernels run by default on newer Qualcomm Adreno E17 compilers (Snapdragon X2-class). The latter is an on-device/mobile enablement; neither changes 16 GB discrete-GPU fit. Sources: llama.cpp PRs #32176 (merged 13:38 ET) and #33013 (merged 14:20 ET), 2026-11-25.
- **Ollama's inkling2 Metal path gets faster short histories.** PR #19881 uses the split-attention kernels for histories under 1024 keys, adds draft proposals and conversation snapshots; Apple Silicon Metal only. Source: Ollama PR #19881, merged 2026-11-25 16:56 UTC (11:56 ET).

## Just missed the window

- `rustling`, a Rust inference engine for Inkling 2 Mini on Apple Silicon with an OpenAI-compatible server for coding agents, surfaced on Show HN at 14:42 ET — 16 minutes past this window's close, and its repository predates the window (Nov 23). Not footnoted before.

## Coverage notes

Verified this run: 11 distinct in-window events (6 leads, 5 also-noted), plus 1 just-missed footnote. Counting unit = underlying event, not article or PR. Dedupe gate suppressed 4 candidates already in the ledger: the GeForce RTX 50-series 20–30% supply cut / RTX 60 delay (TechPowerUp, re-report of the 07:45 entry), Tom's China memory-industry audit (same CXMT/YMTC audit reported 07:45), Tom's Nova Lake-S 7-SKU leak (same QS-document leak reported 07:45), and the Megaspeed entity-list loophole (already footnoted once). Out-of-scope drops: Register's China EDA-tools piece and HP's AI-PC pricing commentary (industry/business, no specific hardware or local-model disclosure). Dead ends logged, not chased: VideoCardz feed (fetch returned empty), two Tom's article URLs (404), and several web searches that returned no results; the Show HN "rustling" and "omega-helix" items were checked on repository timestamps to place them correctly. Reddit's anonymous lane was unavailable this run (search.rss and sub feed both 404), so the community sweep relied on HN's timestamped river and GitHub events/merged-PR searches; HF trending and daily papers were checked but surfaced no in-window weight or license release. Source diversity: ServeTheHome, TechPowerUp/TheElec, Tom's Hardware, The Register, Hacker News, Hugging Face, and GitHub (llama.cpp, vLLM, SGLang, TensorRT-LLM, Ollama).
