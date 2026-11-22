# AI Hardware & Local Models Brief — 2026-11-22 12:45 → 17:45 ET

Window: 2026-11-22 12:45 ET (17:45 UTC) → 2026-11-22 17:45 ET (22:45 UTC). Fire: 2026-11-22 17:45 EST.

## TL;DR
- vLLM merges day-0 support for NVIDIA's Nemotron Shortcut — text, image and video, with SDSA sparse attention, a gated delta-product mixer, and a shortcut MoE (#68198, merged 21:04 UTC).
- SGLang adds a single-server mode for MiniMax-H3 (`--model-variant fl2va+ref2va`), holding the shared 62.1 GiB text encoder once instead of two servers (#52023, merged 18:39 UTC).
- llama.cpp fixes an Inkling 2 Mini host-expert-copy corruption (--n-cpu-moe) and enables the official Inkling 2 tokenizer's ignore_merges (#32807, #32799).
- TensorRT-LLM: Gemma4-E4B multi-token prediction on SM120 via XQA (#22515) and a breaking Qwen-VL mRoPE fix (#22328).
- Community spins: a from-scratch Metal engine for Inkling 2 Mini on 24GB M2 (HN's day-0 "Gall") plus an Inkling-2-Mini quant stampede (NVFP4/heretic/MLX).

## Inference software and benchmarks

### vLLM adds Nemotron Shortcut day-0 support — NVIDIA
Author/source: vLLM-project PR #68198 (GitHub, merged 21:04 UTC 2026-11-22). Nemotron Shortcut lands as one self-contained package under `vllm/models/nemotron_shortcut/` (~7,610 lines, ~1,913 test lines), following the per-model layout of `kimi_k3/` and `glm5next/`. The model combines SDSA sparse attention (an indexer selects each query's Top-K keys while an MQA core attends to them), a Gated DeltaProduct (GDP) mixer on FlashInfer's GDP kernels, soft-clamped squared-ReLU MLPs, and a shortcut-MoE (routed experts read the block input, so dispatch can start before the mixer runs). Text, image and video inputs; TP/PP/DP+EP; BF16, MXFP8 and NVFP4 weights; BF16/FP8/NVFP4 (pending FlashInfer bump) KV; MTP draft head. Checkpoint config and image processor are loaded with `--trust-remote-code` — the checkpoint's own, and vLLM adds no config of its own. Validation: 1496 unit tests (1 known failure on main) + 165/1 on the per-model suite with FlashInfer 0.7.4 on one 4x GB300 node.
Why it matters: NVIDIA's next Nemotron generation is a genuinely new architecture class — sparse attention plus a gated mixer plus an MoE — and day-0 runner support means it will run out of the box on GB300/GB200-class stacks and, with appropriate quantizations, on consumer/edge hardware.
Local inference: Intended target is datacenter H200/GB300-class serving (BF16/MXFP8/NVFP4); the MoE and sparse-attention design implies it should also fit consumer cards at aggressive quant levels (NVFP4), though no single-GPU benchmarks yet.
Source: https://github.com/vllm-project/vllm/pull/68198

### SGLang: one MiniMax-H3 server for fl2va + ref2va — MiniMax
Source: SGLang-project PR #52023 (GitHub, merged 18:39 UTC 2026-11-22). MiniMax-H3 and Pika-H3-Flex split tasks across two checkpoint partitions — `FL2VA/` (t2va and fl2va) and `Ref2VA/` (ref2va). The partitions share everything except the transformer (all 65 non-transformer files and config.json hash identically; text encoder 62.1 GiB, video VAE 9.7 GiB, audio VAE 0.56 GiB, tokenizer/processor; each transformer is 61.73 GiB). New `--model-variant fl2va+ref2va` loads both DiTs in one process, downloads only the partitions actually needed (Ref2VA fetches its transformer on demand), routes reference requests to the Ref2VA DiT and the rest to FL2VA, applies adapters per-partition (`set_lora` with `target="transformer"` / `"transformer_2"`), and admits t2va/fl2va/ref2va from one server. Automatic placement: ≥120 GiB free + the DiT weight (120 + 61.73 = 181.7 GiB) keeps every DiT resident; ≥120 GiB falls back to component offload; otherwise startup fails with explicit instructions. Validated on 2x H200.
Why it matters: it halves the GPU memory needed to serve all three MiniMax-H3 tasks — one process holds 62.1 GiB of shared text encoder, VAEs and tokenizer once instead of two servers duplicating it, and the two 61.73 GiB DiTs share one VRAM pool instead of loading twice.
Local inference: Server-class (2x H200-class) diffusion serving; requires ~181.7 GiB of free VRAM per GPU to keep both DiTs resident, otherwise uses component offload.
Source: https://github.com/sgl-project/sglang/pull/52023

### TensorRT-LLM: Gemma4-E4B MTP on SM120 + Qwen-VL mRoPE fix
Source: NVIDIA TensorRT-LLM PR #22515 (merged 19:14 UTC) and #22328 (merged 19:27 UTC). #22515 adds Gemma4-E4B multi-token prediction (MTP) using XQA on SM120 — previously, Gemma4 MTP with the shared-KV assistant failed at startup on FlashInfer without trtllm-gen decode ("FlashInfer draft metadata views require the trtllm-gen decode backend"), which also prevented the overlap scheduler; XQA now reads KV lengths on the device so the overlap scheduler works. #22328 is a breaking fix: Qwen2.5-VL and Qwen3-VL image embeddings must now be passed with `{"mm_embeddings": tensor, "image_grid_thw": [1, h, w]}`; a bare image position raises an error.
Why it matters: Gemma 4 on SM120 (RTX 50-series / GB10-class consumer and workstation silicon) goes from no-MTP to working MTP with overlap scheduling, and Qwen-VL image users will hit the breaking API change on upgrade.
Local inference: SM120-class consumer/workstation GPUs (~RTX 5080/5090 class) and the GB10 sibling; the fix targets Qwen2.5-VL/Qwen3-VL serving on those parts.
Source: https://github.com/NVIDIA/TensorRT-LLM/pull/22515 · https://github.com/NVIDIA/TensorRT-LLM/pull/22328

### llama.cpp: Inkling 2 Mini host-expert copy fix + ignore_merges
Source: ggml-org/llama.cpp PRs #32807 (merged 19:04 UTC), #32799 (merged 18:52 UTC), #32815 (merged 21:04 UTC), #32697 (merged 20:22 UTC). #32807 fixes corruption when host tensors are copied for reuseable expert tensors — minimal reproducer: `inkling2_loop` with `--n-cpu-moe` emits "!" (token 0) forever after a batch of 32+ tokens; `--no-op-offload` masks it. #32799 enables `ignore_merges: true` for the Inkling 2 official tokenizer (337 vocab entries cannot be built by merge rules). #32815 speeds up the shared mtmd audio FFT (iterative radix-2 stages + batched odd-size DFT, FP order preserved). #32697 fixes a qwen3a audio tail bug.
Why it matters: anyone running Inkling 2 Mini on host/CPU-moe path will have seen silent token corruption; this fixes it on merge.
Local inference: laptop/consumer-class hardware — Inkling 2 Mini (~18GB-class weights) on 32GB-class Macs and Windows hosts; the fixes directly affect the `--n-cpu-moe` offload path.
Source: https://github.com/ggml-org/llama.cpp/pull/32807 · https://github.com/ggml-org/llama.cpp/pull/32799

## Community spins and quantizations

### Gall — from-scratch Metal engine for Inkling 2 Mini on a 24GB M2
Source: Show HN (story_50604195, ~16:00–17:00 UTC 2026-11-22), repo github.com/ltrgoddard/gall (pushed 21:38 UTC). The author needed an Inkling 2 Mini-class local agent setup that fits a 24GB M2 MacBook Air (Thinking Machines gives 32GB as the minimum for Mac; Q3 quant was not workable in llama.cpp even with patches). Gall is a from-scratch Metal inference engine for Inkling 2 Mini with strictly one-model-at-a-time loading, a KV-cache save/unload so RAM stays at ~50MB between calls (fast reload), and a menu-bar app with prefill/generating status. Built with ~a week of Fable allowance plus a sprinkling of Astra. 5 points, 1 comment.
Why it matters: proof-of-concept that the Inkling-2-Mini class can be run comfortably below the official minimum RAM via a purpose-built on-device engine.
Local inference: 24GB M2 (12GB-class streaming-class GPU); the author's normal setup is ~2,800-token system prompt + tool list and near-instant responses.
Source: https://news.ycombinator.com/item?id=50604195

### Inkling-2-Mini quant stampede (day 4+)
Source: Hugging Face API in-window (2026-11-22 18:50–22:15 UTC). The day-0 wave has moved well past Unsloth/bartowski into community and boutique repos: nathaninline/Inkling-2-Mini-NVFP4-Basalt (3 likes, 19:40 UTC), codegood/Inkling-2-Mini-GGUF (19:52 UTC), Losew/Inkling-2-Mini-Heretic-GGUF (20:56 UTC), rmaaorg/Inkling-2-Mini-NXS (19:42 UTC), robfon19/Inkling-2-Mini (21:59 UTC), netprojects/Inkling-2-Mini-MLX-2bit-mixed (19:27 UTC), Tdamre/Inkling-2-Mini-ML-Knowledge-SWE-GGUF (22:15 UTC), TechnoBaptist/Inkling-2-Mini-heretic (18:53 UTC), moonbox-dev/SeedVR2-3B-GGUF (22:45 UTC).
Why it matters: shows the quant/recipe ecosystem around the 18GB Inkling 2 Mini weights is still moving daily; the NVFP4/heretic/MLX tiers are the ones that move a model onto 24GB-class Apple Silicon and RTX 50-class cards.
Local inference: 24GB-class Apple Silicon Macs and 16-32GB consumer GPUs; a Basalt/NVFP4 repack targets the ~24GB unified-memory class.
Source: https://huggingface.co/nathaninline/Inkling-2-Mini-NVFP4-Basalt

## AI hardware and silicon
Nothing in-window. No new GPUs, NPUs, accelerators, memory, or pricing stories were verified between 12:45 and 17:45 ET. The Nov 22 hardware landscape is quiet — the hero banner stays anchored on the day's lead AI-software story.

## Also noted
- vLLM #69163 — Kimi-K3.1-Pro MI355X AgentX recipe aligned with InferenceX (#5119), switching the lane to the `kimi-k31-712d9eb` image and the released DSpark draft (merged 21:11 UTC). Update on the Nov 22 Kimi-K3.1-Pro recipe entries.
- vLLM #69168 was already reported at the 07:45 fire; the smaller-M GEMM on RTX 5090 (SM120) work is in the same stack.
- SGLang #52225/#52224/#52216/#51734 — MiniMax-H3 diffusion performance work merged ~18:37 UTC (cuDNN attention kernels built in warm-up, VSA-H3 no request-path recompiles, request-path timing, CUDA partition plan check, config fake platform).
- llama.cpp #32819 (skills tokenizer flags, merged 17:59 UTC), #32792 (offset pagination for grep_search, merged 18:00 UTC).
- HN: Localcode (story_50604540) — "Agentic coding. Local models. On your Mac." via one command; repo mjwsolo/localcode pushed 21:12 UTC; 1 point.
- HN: TSMC acquires 0.03% stake in Anthropic for $500M — financial story, no hardware/local-AI angle; not reported here.

## Just missed the window
None verified this run. The most tempting near-miss candidate (HN story_50603845, "A Beginner's Guide to Local AI with Inkling, Qwen, and Unsloth", 73 pts, ~17:00 UTC) is a blog post, not an event — not reported.

## Coverage notes
- Window: 2026-11-22 12:45 → 17:45 ET (5h). Reddit (r/LocalLLaMA) anonymous feed returned HTTP 404 for sub feed and search; the Reddit lane was skipped rather than re-tried, per the two-strike rule. Hugging Face trending page and web_search both returned empty/irrelevant results today; coverage relies on GitHub merged-PR feeds, HN Algolia, and the HF API.
- Ledger dedupe: zero candidates were suppressed as already-reported this run — the window is tight and every verified item above is new to the ledger.
- Verified items: 6 substantive (1 vLLM model-support, 1 SGLang diffusion, 1 TRT-LLM batch, 1 llama.cpp fix cluster, 1 community engine, 1 quant wave) + 2 minor (HN Localcode, also-noted cluster).
- No-hardware-no-item is correct this run: no verified AI-hardware or pricing story landed inside the window.
- Reddit lane down; everything else the lanes walked was healthy.
