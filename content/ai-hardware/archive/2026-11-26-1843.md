# AI Hardware & Local Models Brief — 13:24→17:45 ET, Nov 26 2026 (Black Friday lull)

**Window:** 2026-11-26 13:24 EST → 2026-11-26 17:45 EST (18:24Z → 22:45Z). New items only; everything matched against the reported.md ledger is suppressed.

**TL;DR**
- **llama.cpp landed CUDA/HIP Flash Attention with indexed bias** (PR #33063, merged 15:13 ET) — the NVIDIA/AMD path for Inkling 2 Mini's bias-table attention, plus a same-window OpenCL MoE int8 kernel for Adreno A850.
- **vLLM's in-window wave was ROCm-heavy**: HY4 Flash FP8 `fp8_ds_mla` with sinks, ShardQ and iHC sequence parallelism (#70004), plus an UltraQuant-aware attention-backend fix (#70169).
- **SGLang shipped a diffusion/AMD batch**: MiniMax-H3 gate-kernel TP fix (#52480) and an AMD `NEXTN` draft-extend crash fix (#40872).
- **Community**: mradermacher published the first static GGUF ladder of **Monkey-1.0**, a 9B qwen35-architecture multimodal model (4.1 GB Q2_K → 9.9 GB Q8_0) — a 12–16 GB card fit.
- **No first-disclosed AI-silicon or new open-weight flagship landed in the window**; the Thanksgiving/Black Friday newsfeeds were deals and CEO-saga follow-ups.

## AI hardware and silicon

No verified first-disclosures in this window. The outlet rivers (Tom's Hardware, TechPowerUp, VideoCardz, ServeTheHome, The Register, NVIDIA blog) carried Black Friday deals and follow-ups on already-reported stories; the one new driver report (GeForce 621.40 black-screen complaints) concerns a driver released Nov 24, so the event date is outside the window.

## Model releases

No qualifying new open-weight flagship or closed-with-local-impact release first disclosed in this window. The GigaChat4 line that surfaced on HF trending was already reported in the 08:45 fire (37B-A4B) or carries a pre-window timestamp (708B-A41B FP8, lastModified 17:48Z = 12:48 ET).

## Community spins and quantizations

**mradermacher publishes first static GGUF ladder of Monkey-1.0 (9B multimodal)** — 2026-11-26
- Files landed at 21:29 UTC (16:29 ET). Monkey-1.0 is a 9B-parameter, qwen35-architecture multimodal model from Little-AI; mradermacher's static-quant ladder runs Q2_K 4.1 GB, Q3_K_M 4.9 GB, IQ4_XS 5.5 GB, Q4_K_M 5.9 GB, Q5_K_M 6.8 GB, Q6_K 7.7 GB and Q8_0 9.9 GB, with separate mmproj Q8_0 (0.7 GB) / f16 (1.0 GB) vision supplements. No imatrix tiers yet.
- **Why it matters:** a fresh sub-10B VLM with a complete small-GGUF ladder is immediately usable as a local vision/chat model without a datacenter-class card.
- **Local inference:** Q4_K_M is 5.9 GB, so it fits an 8 GB GPU with headroom or a 12–16 GB class card (RTX 5060 Ti 16 GB, RTX 3060 12 GB) alongside KV cache; f16 at 18.5 GB needs 24 GB-class hardware.
- Source: mradermacher/Monkey-1.0-GGUF model card, lastModified 2026-11-26T21:29:56Z — https://huggingface.co/mradermacher/Monkey-1.0-GGUF

**In-window mradermacher quant ladder — heretic/abliteration and merge tiers** — 2026-11-26
- Hub timestamps between 19:08 and 22:44 UTC (14:08–17:44 ET) show new tiers: Qwen2.5-32B-Instruct-ARA-heretic-i1 (22:44Z), fara-9b-heretic-uncensored + i1 (21:37Z), gemma-3-4b-persian-v0-abliterated (21:35Z), Qwen2.5-32B DiM-heretic and MPOA-heretic i1 (21:33Z), HarmonicHarlequin_v5-20B (19:11Z), plus NeonViolin, DarkInstincts-LLaMa-70B and SmolLM2-1.7B-magpie ladders (19:08–19:09Z). Most are new quant tiers on older bases rather than new base models; the 32B i1 files run roughly 7.8–22 GB (IQ1_S to Q6_K class).
- **Why it matters:** the steady refresh of i1/abliterated tiers is where uncensored and roleplay users get usable small-footprint variants first, ahead of any first-party release.
- **Local inference:** 32B i1 tiers fit 24 GB cards (Q4_K class ~16–18 GB); the 4B/9B fara and gemma tiers fit 8–12 GB.
- Source: Hugging Face Hub API (author=mradermacher, sort=lastModified), read 2026-11-26 — https://huggingface.co/api/models?author=mradermacher&sort=lastModified&direction=-1&limit=40

## Inference software and benchmarks

**llama.cpp: CUDA/HIP Flash Attention with indexed bias merged, targeting Inkling 2 Mini** — 2026-11-26, merged 20:13 UTC (15:13 ET)
- PR #33063 (7 commits) implements Flash Attention with an indexed bias table on both CUDA and HIP, fulfilling the contract first sketched in #32740 for Inkling 2 Mini's bias-table attention. F32 bias is used to match the CPU reference, rows route through KV indirection, and fully masked rows now return zeros instead of NaN. The same window brought #33081 (fuse `ssm_conv`+SiLU for bailingmoe3), #33086 (HIP BF16 MFMA operand-type fix for CDNA), #33069 (OpenCL MoE int8 bin kernel host changes for Adreno A850) and #33098 (OpenCL multi-token F32 GEMV for speculative verification). Build tags b12785/b12791/b12793 were cut in-window (21:31–22:30 UTC).
- **Why it matters:** it moves the Inkling-2 attention path from CPU/reference behavior to optimized GPU kernels on both major discrete-GPU backends, and the CDNA fix removes a correctness/performance blocker for older AMD cards. An in-thread follow-up (#33150) flags repeated-token degeneration with `-np 2` on HIP, so the ROCm path is still settling.
- **Local inference:** CUDA (sm80+) and HIP/CDNA discrete GPUs; Adreno A850 via OpenCL on Snapdragon hosts; indexed-bias FA is specifically the Inkling-2-Mini attention path.
- Sources: ggml-org/llama.cpp PR #33063, merged 2026-11-26 — https://github.com/ggml-org/llama.cpp/pull/33063 ; PR #33081/#33086/#33069/#33098, merged 2026-11-26 — https://github.com/ggml-org/llama.cpp/pull/33081

**vLLM: ROCm-centric wave adds HY4 Flash FP8 MLA, ShardQ and iHC parallelism** — 2026-11-26, 18:59–22:00 UTC (13:59–17:00 ET)
- #70004 (20:10Z) adds `fp8_ds_mla` for HY4 Flash on ROCm with sinks, ShardQ and iHC sequence parallelism; #70169 (20:49Z) passes `is_ultraquant_run` into automatic attention-backend selection. A ROCm CI/test cluster (#62524, #69911, #70155, #70134) fixed ViT backend and custom all-reduce with AITER and Python 3.14 stack dumps. On the general path, #70116 keeps the BOS token the HF processor prepends and #70076 stops checkpoint tensors of skipped modules from being split.
- **Why it matters:** HY4 Flash is one of the heaviest open-weight MoEs now in circulation, and giving ROCm a fused FP8 MLA path with sequence parallelism is what makes it practical on multi-GPU AMD boxes rather than NVIDIA-only.
- **Local inference:** targets ROCm (CDNA3/MI300- and MI350-class) for the HY4 path; the BOS/checkpoint fixes apply to any Transformers-backend load, including single-GPU setups.
- Sources: vllm-project/vllm PR #70004, merged 2026-11-26T20:10Z — https://github.com/vllm-project/vllm/pull/70004 ; PR #70169 — https://github.com/vllm-project/vllm/pull/70169

**SGLang: MiniMax-H3 TP gate-kernel fix and AMD NEXTN draft-extend fix** — 2026-11-26, 18:29–21:10 UTC (13:29–16:10 ET)
- #52480 (19:42Z) makes MiniMax-H3's gate kernels read both TP ranks' partial sums, fixing a multi-GPU image-generation path; #43242 (19:48Z) keeps TP/Ulysses head shards on the unsharded torch SDPA kernel. #40872 (21:10Z) fixes a NEXTN draft-extend crash on AMD's CK kernel, and #51786 (18:29Z) scopes diffusion media jobs to tenants with request-privacy protection. #52528 adds optional prompt rewriting for lingbot.
- **Why it matters:** these are the small correctness/privacy fixes that decide whether diffusion serving and AMD speculative decoding work in production — particularly relevant where a single 16 GB card is doing both draft and target duty.
- **Local inference:** NVIDIA multi-GPU TP for MiniMax-H3 image generation; AMD ROCm via AITER for NEXTN speculation; diffusion jobs otherwise single-GPU-servable.
- Source: sgl-project/sglang PR #52480, merged 2026-11-26T19:42Z — https://github.com/sgl-project/sglang/pull/52480 ; PR #40872 — https://github.com/sgl-project/sglang/pull/40872

**Also noted:** ComfyUI's only in-window commits were #18015 (fix Pruna P-Video Upscale turbo pricing) at 19:08Z and a README manual-install update (#18013) at 21:29Z — no new model nodes or architecture support this window (https://github.com/Comfy-Org/ComfyUI/pull/18015).

## Just missed the window

- Ollama **v0.40.19-rc0** was published at 17:52 UTC = 12:52 ET, 32 minutes before this window opened (and inside the prior 08:45→13:24 fire, which did not list it). It bundles faster short histories / draft proposals / conversation snapshots for Inkling 2 on Metal, compact Engram history buffers, and a llama-server bump — https://github.com/ollama/ollama/releases/tag/v0.40.19-rc0. (Footnoted once; never repeated.)

## Coverage notes

- **Verified count:** 6 in-window items (3 inference-stack lead stories, 2 community-quant entries, 1 minor ComfyUI note), plus 1 just-missed footnote. Counting unit: distinct underlying events, not PRs.
- **Ledger dedupe:** 9 candidates surfaced in-window or near-window but were suppressed as already reported or previously footnoted — the HN/AMD llama.cpp fork (amd-rdna-boosts-benchmark, already in the 13:24 fire), GigaChat4-37B-A4B and its GGUF (08:45 fire), Kioxia GP2 SSD (08:45 fire), llama.cpp v1.1.0 marker (11-25 fire), Ollama v0.40.18 (11-24 fire), NVIDIA Southeast-Asia shipment halt (previously footnoted), the Jensen/CEO follow-ups (reported repeatedly), NariQ Qwen3.8-27B quants (previously footnoted), and AMD AGESA 1.3.0.1f (11-24 12:45 fire). No repeat footnotes were emitted.
- **Coverage limitations:** Reddit was unavailable — both `search.rss` and `sub LocalLLaMA/new.rss` returned HTTP 404 from this host, so the community lane rests on HN Algolia and the HF Hub API. VideoCardz's RSS endpoint failed; the HTML front page was read instead. HF's `sort=created` endpoints failed to fetch, so the model-release lane was swept via trending, author-sorted `lastModified` and per-repo API checks rather than an exhaustive "newest repo" river. Hugging Face `lastModified` reflects a re-upload as well as a first release; the quant-ladder item is labeled accordingly rather than presented as wholly new models.
- **Thin day is a valid day:** this is a Thanksgiving/Black Friday afternoon. No hardware or flagship-model first-disclosures were found, and the report does not pad that absence.
