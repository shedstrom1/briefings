# AI Hardware & Local Models Brief — 25 Nov 2026, 17:45 ET

**Window:** 2026-11-25 12:45 EST → 17:45 EST (UTC 17:45 → 22:45). New items only; an already-reported ledger gates every entry.

- **Huawei/Phlexing detail homegrown "Tau EDA" toolchain** for 3D hybrid-bonded, multi-die packages — the chip-design layer under its AI accelerators (The Register, 13:02 ET).
- **JetBrains ships Mellum2.1 Instruct** — Apache-2.0 12B-A2.5B coding MoE, 40.4% SWE-bench Verified, with day-0 GGUFs landing mid-window (bartowski, 15:57 ET).
- **Inference stacks kept moving:** llama.cpp sped up Inkling-2-Mini prefill on Metal (+18–19%), vLLM added ROCm W4A16 MoE DP-attention and GLM-5.3-Flash SM10x prefill kernels, SGLang hardened PD-disaggregation and its Rust server.
- **Community spin wave on Inkling-2-Mini:** independent Heretic, abliterated and "Abliterated-Healed" i1 GGUFs uploaded in-window, plus a 27B semiconductor-engineering VLM quant (Micius-Flash).

## AI hardware and silicon

**Huawei and Phlexing detail "Tau EDA" — a domestic chip-design toolchain for 3D-stacked AI processors**
*Event date: 2026-11-25 (published 18:02 UTC / 13:02 ET, in-window).*
Phlexing chairman Qing He described an EDA toolchain co-developed with Huawei to support Huawei's "Tau Scaling Law" design philosophy: using 3D hybrid bonding to raise transistor density despite SMIC being stuck at a 7 nm-class process. The GloryBolt and GloryEX platforms handle multi-process, multi-physics analysis that single-process EDA tools cannot, with a PDK co-developed with Huawei since 2024 now live. Phlexing says the tooling underpins Huawei's multi-die AI accelerators, near-packaged optics and rack systems.
**Why it matters:** Packaging — not process node — is Huawei's route to competitive AI silicon; EDA is the gating dependency, and a domestic toolchain reduces reliance on export-controlled Cadence/Synopsys/Siemens tools.
**Local inference:** No direct VRAM/device fit — this is chip-design infrastructure; the downstream target is Huawei Ascend/HiSilicon multi-die accelerators.
Sources: The Register, "Huawei and crew tout homegrown EDA tools to overcome China's fab deficit," published Wed 25 Nov 2026 18:02 UTC — https://www.theregister.com/systems/2026/11/25/huawei-and-crew-tout-homegrown-eda-tools-to-overcome-chinas-fab-deficit/5312205

## Model releases

**JetBrains releases Mellum2.1 Instruct — Apache-2.0 12B/2.5B-active coding MoE, day-0 GGUFs**
*Event date: 2026-11-25 (HF card dated Nov 25; bartowski GGUF created 20:57 UTC / 15:57 ET, in-window).*
Mellum2.1 Instruct is the direct-answer (no chain-of-thought) sibling of Mellum2.1 Thinking: 12B total / 2.5B active, 64 experts with 8 active, 28 layers, 131,072-token context (1,024 sliding window on 3 of 4 layers), bfloat16, Apache-2.0. Trained with large-scale RL in real repositories using NVIDIA NeMo RL and Megatron-Bridge, it resolves 40.4% of SWE-bench Verified (up from 20.2% for Mellum2 Instruct), scores 61.1 on AIME (from 32.3) and 38.0 on WorkBench (from 22.2), and emits about half the tokens of the Thinking variant. The `mellum` architecture is supported in transformers 5.11+; vLLM serving with the Hermes tool-call parser is documented on the card.
**Why it matters:** A permissively licensed, small-active MoE that trades reasoning traces for short direct answers is exactly the profile for fast local sub-agents and tool loops — and bartowski shipped GGUFs the same day.
**Local inference:** ~24 GB at BF16; a Q4_K_M GGUF lands near 7–8 GB, comfortably inside RTX 5060 Ti 16GB class, or ~12–14 GB at Q8 for a 24 GB card.
Sources: Hugging Face model card, "JetBrains/Mellum2.1-12B-A2.5B-Instruct," dated 2026-11-25 — https://huggingface.co/JetBrains/Mellum2.1-12B-A2.5B-Instruct ; bartowski/Mellum2.1-12B-A2.5B-Instruct-GGUF, created 2026-11-25 20:57 UTC — https://huggingface.co/bartowski/Mellum2.1-12B-A2.5B-Instruct-GGUF

## Community spins and quantizations

**Inkling-2-Mini ablation/merge quant wave — Heretic, abliterated and "Abliterated-Healed" i1 tiers**
*Event dates: 2026-11-25; mradermacher uploads at 18:11 UTC (13:11 ET) and 21:49 UTC (16:49 ET), in-window.*
Independent labs pushed fresh imatrix tiers of the 26B Inkling-2-Mini looped MoE: JoaoZaokk's Heretic spin (GGUF + i1, tagged dspark/MTP/1M-context, Apache-2.0), mrfakename's abliterated i1, derrickNg1412's "Abliterated-Healed" (KL-healed DPO) line, and Blackfrost-AI's Derisked i1. These are distinct base spins, not re-quants of already-reported trees, and extend the day-0 ladder past the previously logged NVFP4/UD tiers.
**Why it matters:** 1M-context, speculative-decoding-friendly looped models are the current local-agent workhorse; refusal-removal and KL-healed variants let users tune behavior without touching the base weights.
**Local inference:** Full GGUF ladder spans roughly 7–28 GB; Q4_K_M-class tiers fit a 16 GB card, Q8/near-BF16 needs 24–32 GB with expert offload.
Sources: Hugging Face, mradermacher/Inkling-2-Mini-Heretic-i1-GGUF (created 21:49 UTC) — https://huggingface.co/mradermacher/Inkling-2-Mini-Heretic-i1-GGUF ; mradermacher/Inkling-2-Mini-abliterated-i1-GGUF (created 18:11 UTC) — https://huggingface.co/mradermacher/Inkling-2-Mini-abliterated-i1-GGUF

**Micius-Flash GGUF + i1 — 27B semiconductor-engineering VLM reaches llama.cpp**
*Event date: 2026-11-25; quant created 13:29 UTC (08:29 ET) and i1 at 20:50 UTC (15:50 ET, in-window).*
mradermacher quantized zhiman-ai/Micius-Flash, a 27B (27.36B-parameter) text/image/video model built for semiconductor engineering: circuit/PCB understanding, hardware code generation, schematics and waveforms, 262,144-token native context, BF16 checkpoint ~54.7 GB. It is a community quant of a model whose technical report (arXiv 2611.10015) dates to Nov 4; the in-window event is the GGUF/i1 release.
**Why it matters:** A hardware-design-specific VLM that runs under llama.cpp gives local EDA-adjacent workflows (datasheet/schematic QA, HDL help) an offline option.
**Local inference:** ~54.7 GB at BF16; a 4-bit GGUF is ~16–20 GB — fits a 24 GB card (4090/RTX 6000 class), not a 16 GB card at full context.
Sources: Hugging Face model card, zhiman-ai/Micius-Flash — https://huggingface.co/zhiman-ai/Micius-Flash ; mradermacher/Micius-Flash-i1-GGUF (created 20:50 UTC) — https://huggingface.co/mradermacher/Micius-Flash-i1-GGUF

## Inference software and benchmarks

**llama.cpp evening wave — Metal short mat-vec speedup for Inkling-2-Mini, LoRA support, UI releases**
*Event dates: 2026-11-25, commits landed 14:49–16:39 ET (19:49–21:39 UTC), in-window.*
PR #33034 spreads the short mat-vec (K<32) lanes over src1 rows, targeting the depth-state weighted sum in Inkling-2: on an M5 Max with UD-Q4_K_XL, pp512 went 888→1,052 t/s and pp2048 771→894 t/s, with bit-identical output. PR #33055 maps Inkling-2 LoRA tensor names so PEFT conversions no longer abort, #33050 reverts an iPhone command-buffer change, and #33036 ships llama-ui 0.7.0 / llama-chat 0.3.0 with phone-sheet settings and MCP management.
**Why it matters:** A ~18–19% prefill gain on the model everyone is running locally, from a backend-neutral kernel fix, propagates to every Metal user.
**Local inference:** Apple Silicon (M5 Max measured); benefits any Inkling-2-Mini GGUF user on Metal.
Sources: GitHub ggml-org/llama.cpp commits #33034 (21:39 UTC) — https://github.com/ggml-org/llama.cpp/commit/7135e2fd6979d99f76337c81f42ef4466f801f5a ; #33055 (19:49 UTC) — https://github.com/ggml-org/llama.cpp/commit/5917b39ff7b58a808eced9d5c3d63b06f57d6972

**vLLM afternoon wave — ROCm W4A16 MoE DP-attention, GLM-5.3-Flash SM10x prefill, K3.1 sampling defaults**
*Event dates: 2026-11-25, commits landed 14:33–17:41 ET, in-window.*
PR #66159 adds DP-attention support to the AITER Triton W4A16 MoE path on ROCm; #66884 adds a planar short-conv kernel for chunked prefill of GLM-5.3-Flash's KDA on SM10x; #69086 enables ShardQ for SM90 FlashInfer NoPE sparse-MLA prefill; #69774 makes vision FA3 batch-invariant; #69955 fixes `model_impl="transformers"` for architectures Transformers lacks; and a Kimi-K3.1-Pro recipe makes the model card's sampling defaults authoritative with opt-in strict tool calls.
**Why it matters:** W4A16 MoE on AMD and low-bit sparse-MLA prefill on NVIDIA both widen what runs per GPU; the K3.1 recipe removes a common local-serving footgun of mismatched sampling defaults.
**Local inference:** Datacenter-first (ROCm MI350x, SM90/SM10x); the MoE/W4A16 paths are the same ones being backported to consumer cards in downstream engines.
Sources: GitHub vllm-project/vllm commits #66159 (22:41 UTC) — https://github.com/vllm-project/vllm/commit/49ab32c35c6a33feb37a8a91195c41179f0dbd1b ; #66884 (22:01 UTC) — https://github.com/vllm-project/vllm/commit/a588d3974e08fa0a614152ee6b62762f62777ed4

**SGLang evening wave — PD-disaggregation room-lock fix and Rust server hardening**
*Event dates: 2026-11-25, commits landed 13:28–17:25 ET, in-window.*
PR #53037 holds the status lock while `clear()` pops a room in disaggregated serving, removing a race in prefill/decode room reuse; #52969 keeps the launch core partition on the server instance and #52958 reports pre-admission `/generate` refusals to the generate hook; #53150 restores hybrid target-verify rows/scratch taps; #53132 adjusts multicast-gather accounting in the mem-cache CI.
**Why it matters:** These are correctness and multi-tenant robustness fixes for PD-disaggregated and Rust-front-end serving — the configuration local operators hit first when scaling past one GPU.
**Local inference:** Server/homelab scale (multi-GPU PD disaggregation); no specific VRAM tier.
Sources: GitHub sgl-project/sglang commits #53037 (22:25 UTC) — https://github.com/sgl-project/sglang/commit/8b122e2e5b46e417d9d233a2cd9b61ebc465708f ; #52969 (21:38 UTC) — https://github.com/sgl-project/sglang/commit/c40b0058d6f07bcbbcf6d7ed44e45535c602341c

### Also noted

- A "Tell HN" post (17:13 ET, in-window) reports a four-Mac cluster running Baidu ERNIE 5.5 Flash Preview via agent-written custom kernels at ~96 tok/s low-context and ~50 tok/s high-context in Hermes; anecdotal, no repo — https://news.ycombinator.com/item?id=50648334 (story_50648334).
- The Register's "HP sees premium-priced AI PCs as its meal ticket" (17:00 UTC = 12:00 ET) argues high-margin edge-AI PCs offset memory-shortage-driven shipment declines — business angle, no new silicon spec.

### Just missed the window

- `missed:` The Register, "HP sees premium-priced AI PCs as its meal ticket in a shrinking market," published 17:00 UTC / 12:00 ET — 45 minutes before window open; footnoted once, never again.
- Unverified/out-of-window: The Register AI page surfaced "Amazon scoops up AI accelerator tech" and a Marvell/Penguin photonic-memory-fabric item (16 TB DRAM + 0.5 TB HBM shared across 16 systems), but neither carried a fetchable in-window timestamp; logged as coverage limitations, not reported.

### Coverage notes

- Verified in-window items: **7** across AI hardware (1), model releases (1), community spins (2) and inference software (3); plus 2 "also noted" and 1 once-ever "just missed" footnote. Every cited page was fetched; lead stories read in full.
- Sources: ≥7 distinct outlets/feeds — The Register (article + Atom/AI feeds), Hugging Face model cards and quantizer APIs (mradermacher, bartowski, unsloth), GitHub commit APIs for llama.cpp/vLLM/SGLang, HN Algolia, VideoCardz, TechPowerUp, Tom's Hardware, ServeTheHome. Breadth was on sources; items were filtered strictly to AI hardware/local models.
- Ledger dedupe: **~20 candidates suppressed** as already reported — notably the GeForce Q4 20–30% supply cut, Nova-Lake-S 4970K QS, CXMT/YMTC memory audit, YMTC Xtacking cross-license, the Inkling-2-Mini quant-ladder/new-quantizer entry, Hy-MT3-30B-A3B quants, the 14:26-fire llama.cpp/vLLM/SGLang/TRT-LLM/Ollama waves, Adreno E17 #33013, vLLM TeleFLM #69929, NariQ, rustling, marathon-local-engine, omega-helix and the SK hynix 3D-DRAM item.
- Coverage limitations: Reddit's anonymous lanes returned 404 on both `search.rss` and `sub LocalLLaMA` (logged, not chased — no third strike); the web-search backend failed on several queries (fallback unavailable), so discovery leaned on timestamped rivers; GitHub commit APIs were capped at the most recent 6–8 records per repo, so earlier-wave PRs may be undercounted; TensorRT-LLM and Ollama were not re-swept this window; VideoCardz/TechPowerUp front pages carried only already-reported or out-of-scope (gaming) items in-window.
