# AI Hardware & Local Models Brief — Nov 9, 2026, 5:45 PM ET edition

**Window:** 2026-11-09 12:45 → 17:45 EST (fire-time-anchored, new items only; ledger dedupe applied)

## TL;DR

- **Ollama v0.40.16-rc0 landed in-window (13:21 ET):** Qwen3.8-Flash-Next made "memory efficient" on the MLX runner, Ricursive's Ripple-65B-Preview got day-0 MLX support, and Windows-on-ARM gained CUDA 13.5 + SM 103 targets.
- **Community quant day:** first GGUFs of Caboose-SFT-Ripple-65B-T5 (a tool-use SFT of Ripple-65B-Preview), the first Radiance-engine GPTQ W5NL64 builds tuned for the Radeon AI PRO R9700, "Starlight" engine trellis-coded + MTP quants of Flash-Next, and a QAT'd anemll-forge build of Qwen3.8-27B targeting Apple's Neural Engine.
- **Engine merge waves:** llama.cpp b12233 + b12236 (external PLE file loading, Windows unbuffered model load, mtmd resampler helper); vLLM merged 22 PRs including MXFP6 support and HiSparse promote-to-GPU-pages; SGLang merged 21 including DeepSeek-V4.1-Flash MegaMoE fused-decode and an MI450 DPX hierarchical all-reduce.
- **No verified in-window hardware announcements.** Two significant disclosures landed just outside the window: Google's TPU 8tx / 8i Max launch and the NVIDIA–Rebellions–SK Telecom NVLink Fusion deal (footnotes below).

## Inference software and benchmarks

### Ollama v0.40.16-rc0 — Flash-Next made memory-efficient on MLX, Ripple-65B day-0 (Nov 9, 13:21:19 ET)
The rc0 tag went out mid-afternoon with eight merged PRs. The two that matter for local users: "MLX: make Qwen3.8 Flash Next memory efficient" (#18078), a memory-footprint rework of the Apple-Silicon runner for the 125B-class Flash-Next MoE, and "Add ripple preview support to the MLX runner" (#19592), day-0 serving of Ricursive's 65B from-scratch agentic-coding preview on Apple Silicon. The release also adds CUDA 13.5 and SM 103 build targets for Windows ARM64 (#19580), forwards the truncation option on OpenAI responses requests (#19501), and fixes Claude Code routing plus retired-model error paths in `ollama launch` (#19587).
**Why it matters:** Ollama is the default on-ramp for most non-expert local users; a memory-efficiency pass for Flash-Next plus day-0 third-party model support (Ripple preview was released to the community ~5 days ago) shows the MLX runner now tracking community releases, not just Qwen/Llama.
**Local inference:** MLX path targets Apple Silicon (M-series, 24–64 GB unified); CUDA 13.5/SM103 ARM64 line opens Windows-on-ARM hosts with Blackwell-class dGPUs.
Source: Ollama v0.40.16-rc0 release notes, GitHub, published 2026-11-09 18:21:19 UTC — https://github.com/ollama/ollama/releases/tag/v0.40.16-rc0

### llama.cpp b12233 + b12236 — external PLE files, Windows unbuffered model load (Nov 9, 14:15 & 17:39 ET)
Two releases inside the window. The feature change is "feat: external PLE file loading" (#31185, merged 13:41 ET) — per-layer-embedding tensors can now be carried in a side file rather than inline in the GGUF, which matters for the PLE-heavy Qwen4Exp/Flash-Next family where the embedding tables inflate every quant tier. "Windows unbuffered model load" (#26014) changes how models stream off disk on Windows — relevant to every expert-streaming engine setup. Also merged: a functionality test suite for the new spec-decode path (#31027), `mtmd_helper_resampler` (#31964) for vision input resampling, and a pass making the bundled chat UI feel native on phones (#31949).
**Why it matters:** external PLE files cut the storage/IO cost of an entire emerging architecture family, and the Windows load change lands exactly as the 5060 Ti 16GB and R9700 expert-streaming crowd grows.
**Local inference:** benefits 16–32 GB cards running PLE/MoE GGUFs and all Windows users loading quant ladders off NVMe.
Source: llama.cpp releases b12233 (19:15:19 UTC) and b12236 (22:39:22 UTC), GitHub — https://github.com/ggml-org/llama.cpp/releases

### vLLM — 22 in-window merges: MXFP6 formats, HiSparse P/D promotion, ROCm gfx950 fixes (Nov 9, 12:45–17:45 ET)
The afternoon merge wave carried "[Quantization] Support compressed-tensors MXFP6 (mxfp6-pack-quantized)" (#61666), a new weight-format slot; "[HiSparse] promote P/D imports into GPU pages without NIXL changes" (#65888), letting prefill/decode-disaggregated KV land directly in GPU pages; "[Perf][MoE] Skip DP padding rows in TRT-LLM monolithic MoE with vLLM's top-k" (#65972); two ROCm gfx950 fixes (persistent-AITER long BF16 prefills #66400, Mamba-cache stochastic rounding #66319); EAGLE3 aux hidden states under pipeline parallelism in the Transformers backend (#66546); and JIT warm-up coverage for kernels only long prefills, eager drafters and constrained decoding reach (#65349).
**Why it matters:** MXFP6 widens the block-float ladder between FP8 and 4-bit, and the HiSparse change removes a NIXL dependency from KV page promotion — both quietly reshape what fits on a given card.
**Local inference:** MXFP6 and the gfx950/ROCm work targets 32 GB-class workstation cards; P/D promotion benefits multi-GPU homelab serving.
Source: vLLM GitHub merged-PR search, merged 2026-11-09 17:45–22:45 UTC — https://github.com/vllm-project/vllm

### SGLang — 21 in-window merges: dsv41 MegaMoE fused decode, MI450 DPX all-reduce, KDA MTP −12% (Nov 9, 12:45–17:45 ET)
Highlights from this wave: "[Perf][NVIDIA] dsv41: Write the MegaMoE input from the fused decode mHC post" (#49262) and "[mega_moe] Accept MXFP4 activations at DeepSeek-V4.1-Flash's 2304 intermediate size" (#49388); "[AMD] custom-all-reduce: Add a DPX hierarchical 2shot_push to carv2 on MI450 (gfx1250)" (#49205); "[Perf] kda: shorten the MTP verify phase-1 critical path (−11% at N=32, −12% at N=64 on VR)" (#49135); "mhc: one-pass DeepGEMM prenorm GEMM" (#48984); plus speculative-decode graph and dspark bookkeeping fixes (#40962, #37472).
**Why it matters:** the DeepSeek-V4.1-Flash decode path keeps consolidating into fused MegaMoE kernels, and AMD's gfx1250 generation is getting first-class custom all-reduce paths, not afterthoughts.
**Local inference:** KDA/MTP verify gains help anyone running multi-token-prediction drafts on consumer Blackwell; gfx1250 items target the R9700/MI450-class cards.
Source: SGLang GitHub merged-PR search, merged 2026-11-09 17:45–22:45 UTC — https://github.com/sgl-project/sglang

## Model releases

No new open-weight base model families were disclosed in-window. (Closest: Ant-linked Ricursive's Ripple-65B-Preview lineage — its first community SFT + quants are below as an in-window development on a previously footnoted model.)

## Community spins and quantizations

### Caboose-SFT-Ripple-65B-T5 — first GGUF ladder of the Ripple-65B tool-use SFT (Nov 9, 13:35–17:47 ET)
mradermacher published both a plain GGUF (13:35 ET) and an imatrix i1-GGUF (15:47 ET) of schonsense/Caboose-SFT-Ripple-65B-T5-v1-BF16, an Apache-2.0 supervised fine-tune of ricursiveAI/Ripple-65B-Preview aimed at tool-calling/terminal behavior (the "T5" checkpoint series was iterated publicly across Nov 6–9). This is the first quantization tier for Ricursive's 65B dense from-scratch agentic-coding model, whose weights preview was footnoted here on Nov 5.
**Why it matters:** it converts a footnoted preview into something runnable on single high-end consumer cards, and it validates Ripple-65B as a finetune base within days of exposure.
**Local inference:** 65B dense — Q4-class lands around 34–40 GB, i.e. 48 GB cards or 2×24 GB; BF16 needs workstation memory.
Source: https://huggingface.co/mradermacher/Caboose-SFT-Ripple-65B-T5-v1-BF16-GGUF (created 2026-11-09 18:35:31 UTC) and https://huggingface.co/mradermacher/Caboose-SFT-Ripple-65B-T5-v1-BF16-i1-GGUF (created 2026-11-09 20:47:57 UTC)

### Radiance-engine GPTQ W5NL64 for Flash-Next, tuned to the Radeon AI PRO R9700 (Nov 9, 16:21 ET)
frank8465 published "Qwen3.8-Flash-Next-GPTQ-W5NL64-QSAHC-BF16-Radiance-AMD-R9700", a GPTQ W5NL64 build (weights W5, non-linear layers 64-group; QSA + per-layer-embeddings in BF16, vision tower retained) tagged for the Radiance engine and gfx1201/ROCm — the C++/HIP AMD inference server that has been consolidating the R9700 community (vllm-radiance fork, Level1Techs 2×R9700 guides at ~200 tok/s single-stream Qwen3.8-27B MXFP4).
**Why it matters:** first-class W5 GPTQ quants for Radiance mean the AMD RDNA4 workstation card now has a spec-decoding-ready (MTP + QSA) 5-bit tier independent of llama.cpp/vLLM forks.
**Local inference:** targets 32 GB Radeon AI PRO R9700 (gfx1201) under ROCm; W5+BF16-PLE of the Flash-Next MoE sits in the ~28–32 GB envelope.
Source: https://huggingface.co/frank8465/Qwen3.8-Flash-Next-GPTQ-W5NL64-QSAHC-BF16-Radiance-AMD-R9700 (created 2026-11-09 21:21:11 UTC); engine: https://codeberg.org/StillDeadcode/radiance

### Starlight engine GGUFs — trellis-coded, MTP-bearing abliterated Flash-Next turbo quants (Nov 9, 16:53 & 16:57 ET)
feverdoingwork shipped two "Starlight" builds — Qwen3.8-Flash-Next iq3_xxs_turbo-abliterated and iq3_xxs_turbo_mini-abliterated — GGUFs made "for one program, the inference engine Starlight", with trellis-coded quantization, MTP heads, imatrix, based on ISTA-DASLab's GSQ-RCO Flash-Next quant.
**Why it matters:** another third-party engine (following Basalt, Strata, RustingStrata) enters the Flash-Next wars with a trellis-quant tier; the mini variant targets tighter VRAM.
**Local inference:** iq3_xxs-class of the Flash-Next MoE is the ~16 GB single-card bracket (5060 Ti 16GB / 4060 Ti 16GB class, CPU expert offload expected).
Source: https://huggingface.co/feverdoingwork/Qwen3.8-Flash-Next-iq3_xxs_turbo-abliterated-Starlight (created 2026-11-09 21:53:17 UTC) and .../Qwen3.8-Flash-Next-iq3_xxs_turbo_mini-abliterated-Starlight (created 2026-11-09 21:57:55 UTC)

### anemll-forge "qualityup" — QAT'd Qwen3.8-27B for the Apple Neural Engine (Nov 9, 15:57 ET)
gregcmartin released anemll-forge-qwen3.8-27B-qualityup, a community ANEMLL Forge build following ANEMLL 0.2's recipe (GPTQ, then quantization-aware training against BF16 Qwen3.8-27B) with the MLP matrices of layers 24–63 modified for quality; the companion gregcmartin/anemll-forge-speedup fork adds prompt-lookup drafts, head-free prefill blocks and a faster host decode path on the same weights.
**Why it matters:** the ANE has been the big unexploited tensor processor on Apple Silicon; a QAT pipeline plus measured speedup fork (the project claims dflash2-style speculative decoding on ANE/CoreML) is how it becomes a real decode device for 27B models.
**Local inference:** Apple Silicon (M4/M5 class) ANE tensor cores via CoreML conversion; low-power on-device inference rather than GPU-busy decode.
Source: https://huggingface.co/gregcmartin/anemll-forge-qwen3.8-27B-qualityup (created 2026-11-09 20:57:04 UTC); engine: https://github.com/Anemll/Anemll

### mradermacher in-window quant ladder (Nov 9, 13:21–17:48 ET)
Six day-0 GGUF sets crossed inside the window: LFM3-Omni-2B-abliterated GGUF + i1 (13:21/17:17 ET, JonusNattapong's Thai-multilingual omni abliteration), LFM3-VL-2B-Uncensored-i1 (16:13 ET, GCSA-AiLab), Qwen2.5-1.5B-Instruct-heretic-ara (14:02 ET, aasis001 heretic+ARA), Arete-27B-Heretic-ARA GGUF + i1 (15:52/17:21 ET, Luni's heretic/ARA spin of Nous's Arete-27B), plus the Caboose-Ripple ladder above.
**Why it matters:** the LFM3 (Liquid AI omni/VL) family is the week's hottest small-model base for abliterated edge builds; day-0 coverage keeps it deployable on sub-4GB tiers.
**Local inference:** 1.5–2B quants run on phones/Jetson class; Arete-27B tiers fit 24 GB (Q4_XL) to 16 GB w/ offload.
Source: https://huggingface.co/mradermacher (repos created 2026-11-09 18:21Z–22:48Z)

## Also noted (verified in-window, minor)

- **Doom-playing 2B decision model, trained on a Mac in 43 minutes** — u/armbues demoed LoRA image-training in mlx-decision (repo, release 1.3.0 Nov 8) producing a 2B decision model that plays Doom on Apple Silicon; MLX, open source. (r/LocalLLaMA, Nov 9 — https://www.reddit.com/r/LocalLLaMA/comments/1xryvbg/i_finetuned_a_2b_decision_model_to_play_doom_on/)
- **Arete-27B "refusal-to-fabricate" findings confirmed** — JLeonsarmiento's 30-question hand-made non-existent-entity bench reproduces community reports that Nous's Arete-27B alignment SFT introduces confident hallucinations on Qwen3.8-27B, ranking Q3.8FN > Q3.6-35B > Q3.8-27B > Arete on fabrication-resistance. (r/LocalLLaMA, Nov 9 — https://www.reddit.com/r/LocalLLaMA/comments/1xs1nqc/confirming_apollomg_findings_organic_handmade/)
- **llm-psicotest** — msalsas released a seeded 38-turn, byte-identical-prompt regression harness (29 capability items + protocol turns) to detect behavior shifts after a quant swap; scores every turn against a computed key. (r/LocalLLaMA, Nov 9 — https://www.reddit.com/r/LocalLLaMA/comments/1xs1my0/llmpsicotest_a_seeded_38turn_conversation_to/)
- **"Neuron hunting" part 2** — Tasty-Ad-3753's follow-up to this morning's L11-N8147 post maps more snark/poetic/contrarian neurons in Qwen3.5-4B (update on 2026-11-09 07:45 entry L11-N8147-neuron). (r/LocalLLaMA, Nov 9 — https://www.reddit.com/r/LocalLLaMA/comments/1xrydgx/more_interesting_and_unusual_qwen354b_neurons/)
- **supergemma4-26b-uncensored-gguf-v2** — gernblanksten's re-quantized Korean "fast" uncensored Gemma-4-26B-A4B GGUF v2 (22:09 UTC). (https://huggingface.co/gernblanksten/supergemma4-26b-uncensored-gguf-v2)
- **gpt-oss-20b-MedReason-MXFP4** — Rumiii MXFP4 medical-reasoning spin of gpt-oss-20b (21:46 UTC). (https://huggingface.co/Rumiii/gpt-oss-20b-MedReason-MXFP4)
- **tinybrainbot-53m-a9m-hybrid-instruct** — nkthebass's from-scratch 53M-active-9M LFM2-MoE hybrid SLM trained on a long open-data list (fineweb-edu, DCLM, OpenHermes, hermes-function-calling), Apache-2.0 (22:14 UTC). (https://huggingface.co/nkthebass/tinybrainbot-53m-a9m-hybrid-instruct)

## AI hardware and silicon

No in-window hardware announcements verified. The two significant chip/silicon disclosures today (Google TPU 8tx/8i Max, NVIDIA–Rebellions NVLink Fusion) were both made before 12:45 ET — footnoted below, never re-footnoted.

## Just missed the window

- **Google launches TPU 8tx and TPU 8i Max** (Nov 9, disclosed ~12:00 ET, ~45 min pre-window; blog tied to a 9 a.m. PT broadcast) — fourth and fifth systems of the 8th-generation family on existing silicon: 8tx = TPU 8t silicon + 5th-gen AMD EPYC hosts with hardware-level confidential compute and the Boardfly network (claim: up to 2x lower latency vs 8t, near-zero penalty), 8i Max = dual 8i chips one package with 2x compute/SRAM/HBM bandwidth and double compute density per rack. Sources: https://blog.google/innovation-and-ai/infrastructure-and-cloud/global-network/tpu-8i-max-and-8tx/ (Nov 9) ; https://www.servethehome.com/google-tpu-8tx-pairs-amd-epyc-turin-hosts-with-confidential-computing-while-tpu-8i-max-doubles-memory-bandwidth/ (Nov 9); https://www.constellationr.com/insights/news/google-launches-tpu-8tx-tpu-8i-max (Nov 9). Cloud-only — no local-inference fit.
- **NVIDIA–Rebellions–SK Telecom NVLink Fusion deal (AI Day Seoul)** (Nov 9, disclosed ~00:00–03:00 ET) — Rebellions' next-gen Rebel R200 NPUs join NVIDIA MGX racks over NVLink Fusion for rack-scale inference; planned disaggregation uses NVIDIA GPUs for prefill and R200s for decode; SK Telecom to deploy (A.X K2 and "AI for All" services); R200 systems targeted 2028. Sources: https://nvidianews.nvidia.com/news/nvidia-rebellions-and-sk-telecom-announce-collaboration-to-advance-sovereign-ai-infrastructure-with-nvidia-nvlink-fusion (Nov 9); https://www.engineering.com/nvidia-rebellions-and-sk-telecom-advance-koreas-sovereign-ai/ (Nov 9). Data-center class — implications are for the NPU-interconnect wars, not consumer boxes.

## Coverage notes

- **Verified count:** 12 in-window qualifying items (4 inference-software, 1 model-line note, 5 community-quant items incl. the mradermacher ladder, 7 also-noted lines folded into item count where substantive) + 2 "Just missed the window" footnotes (TPU 8tx/8i Max, NVIDIA–Rebellions Seoul), each tagged `missed:` once.
- **Ledger dedupe:** 8 candidates suppressed as already reported this week — RustingStrata DSv4.1-249GB (12:45 fire), TheDrummer Orion-26B (12:45 fire), WSP-PacMan (12:45 fire), qwen-concept-27b rL re-post (footnoted 12:45 fire — not re-footnoted), mradermacher Arete-27B-Uncensored-V2 + Vesper-26B-Nocturne (in 12:45 35-repo ladder), Dell PowerEdge R770AP STH review (Nov 8, Patrick Kennedy), mlx-decision 1.3.0 (Nov 8, used as evidence only).
- **Dead ends / limitations:** r/LocalLLaMA anonymous feeds carry no timestamps, so community posts were bracketed by listing order against the last item the 12:45 run reported and corroborated with HF/GitHub timestamps where possible; OmniVoice (rL "best voice clone TTS" claim) checked and rejected — k2-fsa/OmniVoice repo predates the window by months; "emdasterisk_go_home_14B" Ministral-3 IPO merge (14:45Z) sits pre-window and was too minor to footnote; SGLang repo is sgl-project/sglang (the sglang-project slug 404s); HN river 17:45–22:45Z produced no in-scope stories; VideoCardz's Nov 9 items were all pre-window or gaming-side.
- **Phase walls:** discovery and verification completed inside budget; report written before the write wall.
