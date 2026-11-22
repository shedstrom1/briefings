# AI Hardware & Local Models Brief — 2026-11-22 12:45 ET (12:45 fire)

Window: 2026-11-22 07:45 ET → 12:45 ET (5 hours). This run's headline is inference software, not silicon — no new hardware or silicon events were disclosed in-window.

## TL;DR

- **llama.cpp: token-aware chat parsing merged (#29307)** — chat parsers can now match `<think>`/`</think>` as tokens, not literal text, so reasoning boundaries for Qwen-family templates parse correctly.
- **vLLM: anchor-first DFlash/DFlash2 support merged (#68827)** — checkpoint-configured anchor-first layouts (e.g. `DaoCloud/Qwen3.8-27B-DFlash2-Exp`) are no longer rejected at startup; GSM8K acceptance 0.920 / avg length 6.03 (B200).
- **vLLM: Kimi-K3.1-Pro B300 presets moved to the released DSpark draft** (#69182, #69264) — `gb300_agentx_pd` / `pd_hybrid` / `pd_dep32` / `c1` presets updated to the released draft at revision `e4e6e626`; `gb300_agentx_pd` now points 1/20/48/96/660 on the `kimi-k31-mc-f4c9618` image, with 1P5D/5-draft tokens at 44 and 104.
- **llama.cpp b12632 / b12633** (published 14:37Z and 14:53Z) — HIP NVFP4 scale halving fix on CDNA3 (#32790, merged in-window) and WebGPU nonzero-clear fix for non-UMA devices (#32727 via b12633).
- No new hardware, new model releases, or new community spins verified in-window; the report is inference-software-heavy by honest design.

## AI hardware and silicon

**No events in-window.** No new GPU/APU/accelerator launch, recall, firmware or driver branch change, price move, or interconnect/clustering development was disclosed between 07:45 and 12:45 ET. The 07:45 fire already covered the day's hardware-adjacent items (Xing4.1-Preview weights, Hy4-GGUF quant refresh, llama.cpp b12626–b12631, vLLM `#69168` small-M GEMM on SM120); none re-appear here.

## Model releases

**No new model releases in-window.** The only model-adjacent items surfaced were (a) the in-window llama.cpp merges, (b) in-window vLLM merges, and (c) the already-covered Nov 22 early-morning releases, which the ledger dedupe suppresses (Xing4.1-Preview, 02:47 EST; Hy4-GGUF refresh). Xing4.1-Preview and Hy4 GGUF tier drops were reported at the 07:45 fire and are not re-listed.

## Community spins and quantizations

**No new community spins in-window.** Searches across Hugging Face community repos, r/LocalLLaMA and HN for in-window quant uploads, merges, or fine-tunes returned nothing new beyond what the 07:45 fire reported (Xing4.1-Preview, Hy4-GGUF). No verified HF repo creation time in-window. This is logged as a reporting gap, not a skim of records.

## Inference software and benchmarks

**llama.cpp token-aware chat parsing (#29307) — lead item.** The server's chat parsers could only match text, so tokens like `<think>` / `</think>` could be emitted by a model either as real special tokens or as plain text fragments (e.g. `</` `think` `>`), causing incorrect parsing of reasoning boundaries. The PR adds `p.token()` / `p.until(delimiter)` primitives that match real tokens and falls back to literal matching for unregistered templates. Merged 17:36Z. Local-inference relevance: fixes reasoning-block detection in Qwen-family reasoning traces for Hermes/llama.cpp-based local agents.

**vLLM anchor-first DFlash/DFlash2 (#68827) — lead item.** DFlash/DFlash2 can now accept checkpoints whose `dflash_config` sets `sample_from_anchor: true` (anchor-first layout: `K` queries for `K` draft tokens), which previously died at startup with a ValueError — the public `DaoCloud/Qwen3.8-27B-DFlash2-Exp` was one such checkpoint. Fill-in (`1 + K`) layout is unchanged; DSpark inherits the same logic (it already used the anchor-first layout). GSM8K: acceptance 0.920 / avg 6.03 length, B200, 164 GPU unit tests passed. Local-inference relevance: the Qwen3.8-27B DFlash2 drafter is now deployable on B200-class hardware.

**vLLM Kimi-K3.1-Pro B300 presets (#69182, #69264).** The `gb300_agentx_pd` family now serves the released `Inferact/Kimi-K3.1-Pro-DSpark` draft at revision `e4e6e626` — `gb300_agentx_pd` points 1 (1P1D, 7 draft tokens), 20 and 48 (1P3D, 5), 96 (1P3D, 4), 660 (4P5D, 5) with Mooncake store tail cleanup, prefix-match-unit 512 on prefill, BF16 indexer logits, `deep_select` indexer top-k on prefill; `gb300_agentx_pd_hybrid` (256, 448), `gb300_agentx_pd_dep32` (960, 1P1D) and `gb300_agentx_c1` (1, concurrency-1) all serve the released draft. The `gb300_agentx_pd` recipe aligns points to InferenceX#5175 (1P5D marginal-point set) and the DEP/hybrid points to InferenceX#5109. Local-inference relevance: B300-class capacity-first deployments of K3.1-Pro; no direct DGX Spark / RTX-class fit today.

**llama.cpp b12632 / b12633 (14:37Z / 14:53Z).** b12632: HIP fix for double-halving of NVFP4 scales on CDNA3 (#32790). b12633: nonzero-clear for non-UMA devices (WebGPU, #32727). Both are correctness fixes; neither is a feature release.

**Also noted (in-window, low-signal):**
- llama.cpp #32398 — `/models/sse` on single-model mode (merged 16:52Z).
- SGLang in-window merges — #52226 (LoRA snapshot fix on offloaded base), #52220 (six more hot Triton wrappers replayed for diffusion), #52074 (zero-token guard in `grouped_gemma_rmsnorm`). Diffusion/kernel polish, no headline demo.
- llama.cpp #29307 companion — token-aware parser extended by new grammar; covered in the lead item.

## Just missed the window

- None footnoted this run. The open in-window PRs — e.g. vLLM #69147 (SM120 nosync MegaMoE MXFP4 backend, still a draft) and SGLang #52400 (hunyuan_v4_flash length-aware IndexPool decode top-k) — are not merged and were not reported as shipped.

## Coverage notes

- Window: 2026-11-22 07:45 ET → 12:45 ET (fire 12:45 EST).
- Verified in-window items: 4 substantive (llama.cpp token-aware parsing; vLLM anchor-first DFlash/DFlash2; vLLM Kimi-K3.1-Pro B300 recipe updates: #69264/#69182; llama.cpp b12632/b12633). 1 also-noted cluster (SGLang diffusion/kernel batch + llama.cpp #32398). No hardware/silicon/model-release/community-spin items verified in-window — this is a software-heavy window, not a skipped search.
- Ledger dedupe: 07:45-fire items (Xing4.1-Preview weights, Hy4-GGUF tier updates, llama.cpp b12626–b12631, vLLM #69168) suppressed as already reported; zero in-window events were suppressed as repeats this run.
- Search-backend instability (DuckDuckGo/DDGS intermittent \"No results\") cost multi-angle search; coverage relied on GitHub release/PR metadata for the in-window PRs and is noted as the limiting factor. No candidates were found beyond the in-window merges listed above.
- 07:45-reported Xing4.1-Preview and Hy4-GGUF refresh remain on the ledger; no double-listing.
