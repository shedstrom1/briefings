# AI Hardware & Local Models Brief — Tuesday, November 10, 2026

**Window:** 2026-11-09 17:45 EST → 2026-11-10 07:45 EST (previous fire → this fire, new items only)

## TL;DR
- **KVARK Sparkle 1.1 (27B)** landed on Hugging Face early this morning: an Apache-2.0 document-Q&A specialist fine-tune of Qwen3.8-27B that answers 91.0% of agentic-RAG questions and correctly declines 91.5% of unanswerable ones — with day-0 GGUFs from 15.3 GB (IQ4_XS) that fit a 16 GB card.
- **Epoch AI published its Inference Performance Index**, arguing FLOP/s underestimates modern inference silicon by ~10x — a GB300 delivers ~25x the H100's agentic-inference capacity on only 2.5x the 8-bit FLOPS; interconnect domains matter more than raw compute (NVL72 = 1.9x per-GPU vs the same Blackwell Ultra die in an HGX-8 box).
- **Project Maya fork runs CUDA and ROCm cooperatively on one model** — RTX 5090 handles attention/dense/shared layers and the expert cache while a Strix Halo iGPU computes complementary experts, claiming up to 48% faster mixed-vendor serving of GLM-5.3-Flash.
- **DavidAU shipped the 40B "NEO" GGUF ladder** of the Qwen3.8-40B-Ultimate-Heretic merge (27B base expanded to 40B, Claude-Opus reasoning traces, Turbo Brilliance 50x V2 with 21 AGI constructs).
- **Inference-stack waves:** llama.cpp b12242–b12244 (Kimi K3.1 vision in mtmd, SYCL device-side MoE scheduler, oai-compat /v1/audio/speech), vLLM ~55 merges (Kimi K3.1 split 4/6 with ROCm TP8, DeepSeek-V4.1 AITER MegaMoE wiring, default CUDA build 13.0→13.2), SGLang ~46 merges (Quark MXFP4 W4A16 MoE opened to consumer RDNA, RTX PRO 5000 block-FP8 GEMM configs).

## Model releases

### KVARK Sparkle 1.1 (27B): document-Q&A specialist with a trained "stop rule" (Nov 10, 03:16 EST)
Egzakta AI Lab (Serbia) released KVARK Sparkle 1.1, a fine-tune of Qwen3.8-27B (architecture unchanged: 64 layers — 48 linear + 16 full attention, 262K native context, 1-layer MTP head) trained for agentic document work. Trained with an explicit cost-aware stop rule (three fruitless searches → answer "NOT IN DOCUMENTS"), on its GroundedWork agentic-RAG benchmark it scores 91.0 on 70 document Q&A questions (vs 89.5 for Sparkle 1.0 with the same rule) and correctly declines 91.5% of unanswerable questions (up from 74%). General-benchmark differences vs Qwen3.8-27B are statistically insignificant (GPQA Diamond 88.3 vs 88.8, LiveCodeBench 86.0 vs 82.0, AIME 92.5 vs 95.8) while median reasoning-token use drops 7–48% (GPQA: 4,471 vs 7,670 tokens); with the MTP head on one H100 it answered 15 of 20 questions faster than the base model (median 32.9 s vs 40.8 s). Apache-2.0, no revenue cap; no proprietary-model outputs used in training data.
**Why it matters:** "knows when the answer isn't there" is the failure mode blocking local RAG/agent deployments; a 27B that says NOT IN DOCUMENTS 91.5% of the time — with fewer thinking tokens — is a practical win for self-hosted knowledge workers.
**Local inference:** BF16 55.6 GB, FP8 30.9 GB (vLLM), official GGUF ladder 15.3–54.7 GB (IQ4_XS→BF16, MTP included) — the IQ4_XS tier runs on 16 GB cards (RTX 5060 Ti 16GB class); Q4_K_M-class lands in 24 GB territory; DGX Spark 128GB runs BF16 with room for the 64K-evaluated context.
Source: Egzakta/Kvark-Sparkle-1.1-27B (model card, repo created 2026-11-10 08:16 UTC) — https://huggingface.co/Egzakta/Kvark-Sparkle-1.1-27B
Second source: r/LocalLLaMA announcement by the author (Nov 10) — https://www.reddit.com/r/LocalLLaMA/comments/1xskb49/kvark_sparkle_11_27b_better_document_qa_and_it/

## Inference software and benchmarks

### Epoch AI's Inference Performance Index: FLOP/s underestimates inference silicon by ~10x (published Nov 9; HN Nov 10 07:09 EST)
Epoch AI introduced the Inference Performance Index (IPI), scoring accelerators on how many concurrent agent requests they can serve at required output speeds rather than on FLOP/s. Modeled on agentic workloads (long KV-cache reuse, speculative decode assumed at 3 accepted tokens/cycle) across four representative models (GLM-5.2, Kimi K3, Qwen3-Coder-480B, a MAI-Base-2 proxy), it estimates the NVIDIA GB300 delivers ~25x the H100's inference capacity per chip despite only 2.5x the 8-bit FLOP/s — i.e. pure FLOP/s comparisons understate it ~10x. Scores are expressed in "GB300 equivalents" (GB300e); system topology counts: a GB300 NVL72 GPU models to ~1.9x the capacity of the identical Blackwell Ultra GPU in an 8-way B300 HGX box, purely from the 72-GPU NVLink domain. Validation vs measured Kimi K3 throughput on GB300 averaged ~12% error at 50% of speed-of-light.
**Why it matters:** This is the clearest public argument yet that memory bandwidth, HBM capacity, and interconnect domain size — not TOPS — determine who runs inference, which maps directly onto homelab buying: the same die in a bigger coherent domain is a different product, and a 16 GB card's value is defined by what its bandwidth can keep resident.
**Local inference:** The concurrency/output-speed tradeoff (2→8 requests/chip: 210→80 tok/s/req) is the same curve local users hit when batching; Epoch's hardware inputs (HBM GB, GB/s, interconnect tiers) are the right dial set for comparing a 5060 Ti 16GB against a Radeon AI Pro R9700 32GB or DGX Spark.
Source: Epoch AI, "Changing how we measure AI chip inference performance" (2026-11-09) — https://epoch.ai/publications/inference-performance-index
Second source: Hacker News submission 2026-11-10 12:09 UTC (story 50439174) — https://news.ycombinator.com/item?id=50439174

### Project Maya fork: CUDA + ROCm cooperate on one MoE model across RTX 5090 + Strix Halo iGPU (posted Nov 9 evening)
Developer Ren-WuJun unveiled a fork of Project Maya that runs GLM-5.3-Flash "Maya-M" GGUF prefill and decode cooperatively on mixed-vendor GPUs in one process: the CUDA backend on an RTX 5090 executes attention, dense/shared layers and the expert cache while the ROCm backend on a Ryzen AI Max (Strix Halo) iGPU computes complementary experts during both phases — claiming up to 48% faster than single-device operation. The PR (mw00/project-maya#879, opened Nov 6) was welcomed by Maya's creator, who plans to test before merging. In-thread commenters note llama.cpp can already build with both CUDA+HIP, though the Maya fork's contribution is tensor-level expert partitioning, not layer splitting.
**Why it matters:** Cross-vendor tensor splitting was the missing piece for Strix Halo owners who also own NVIDIA cards — it turns "128 GB unified + 32 GB of Blackwell" into one pool instead of two awkward halves.
**Local inference:** Targets exactly the enthusiast class this brief tracks: RTX 5090 + Ryzen AI Max 395 (or 128 GB Strix Halo) boxes; author-reported numbers, single-model (GLM-5.3-Flash) so far.
Source: r/LocalLLaMA post (Nov 9/10) — https://www.reddit.com/r/LocalLLaMA/comments/1xsaurn/cudarocm_dualbackends_llm_inference_for/
Second source: GitHub PR "GLM: opt-in CUDA+HIP expert helper for mixed-vendor GPUs" — https://github.com/mw00/project-maya/pull/879

### llama.cpp b12242–b12244: Kimi K3.1 vision in mtmd, SYCL device-side MoE scheduler (Nov 10, 21:01–02:25 EST builds)
The overnight-to-morning merge wave (29 commits) delivered: mtmd Kimi-K3.1 vision support (#31980), a SYCL device-side MoE scheduler (#30156), a Vulkan tiled-transpose concat shader for Qwen models (#30591), Vulkan scalar flash-attention capped at 8 rows for large heads on Qualcomm Adreno (#30829) and an OOB fix on MoltenVK (#31890), CUDA VMM pool reservation halved with cuMemAddressReserve retry (#30542), an OpenAI-compatible /v1/audio/speech server endpoint (#31763), iGPU lazy-mode fallback now gated on mmap (#31957), Hexagon 64-bit FastRPC handle fix (#30952), and CI moved to oneAPI 2026.2/oneDNN 2026.1.
**Why it matters:** K3.1 vision support continues the day-0 llama.cpp split for Moonshot's 2.8T-flag family, and the SYCL MoE scheduler plus Vulkan Qwen shader are meaningful wins for Intel/AMD/Qualcomm silicon paths.
**Local inference:** Runs everywhere llama.cpp does; the Vulkan and Qualcomm fixes directly benefit 16 GB-class AMD/Intel/Adreno deployments.
Source: ggml-org/llama.cpp releases + commit log (b12242 02:01 UTC, b12243 06:48, b12244 07:25 Nov 10) — https://github.com/ggml-org/llama.cpp/releases

### vLLM in-window wave (~55 merges): Kimi K3.1 split 4/6, DeepSeek-V4.1 AITER MegaMoE on ROCm (Nov 9–10)
Highlights: Kimi K3.1 split 4/6 (#66532) lands NVIDIA specialized decode kernels, AMD model updates and ROCm TP8 support; a DeepSeek-V4.1-on-ROCm series wired AITER MegaMoE into the model (#66644) with Mono TP8/TP4 tuning (#64153/#63893), a CuTe-DSL mHC boundary for decode batches up to 336 tokens (#66652) and prefill sparse-attention JIT avoidance (#66654); GLM-5.3-Flash gained specialized GB300 TP4 decode (#63102); ModelOpt per-block INT8 weight-only linear layers (#65272); Muse Spark Open online-FP8 routed experts on Hopper (#66139); copy-engine PP transport is on by default over NVLink (#65896); the default CUDA build moves 13.0→13.2 (#55387); legacy grpc_server/api_server/run_batch are deprecated for removal in v0.37.0 (#66688).
**Why it matters:** ROCm's MoE fast-path stack for DeepSeek-V4.1 is catching up to CUDA, and K3.1 TP8 on MI-series opens AMD serving of the 2.8T-class flagship.
**Local inference:** Datacenter-leaning kernels, but the CUDA 13.2 default and INT8_PB_WO path change what builds and quant formats desktop Blackwell/RDNA boxes get first.
Source: vllm-project/vllm commit log 2026-11-09 22:45 → 2026-11-10 12:45 UTC — https://github.com/vllm-project/vllm/commits/main

### SGLang in-window wave (~46 merges): consumer-RDNA MXFP4 MoE, RTX PRO 5000 FP8 configs (Nov 9–10)
Highlights: the Quark MXFP4 W4A16 MoE gate was widened to consumer RDNA (gfx12xx) with a clear error when aiter is absent (#47585); Qwen3.8-Flash-Next avoids padded PLE prefill activations (#43032); Triton block-FP8 GEMM configs were tuned for the NVIDIA RTX PRO 5000 (#45263); Kimi-K3 gained reasoning-effort tier mapping (#48776) and incremental tool-call streaming (#48775) plus split-out K3.1 kernels (#49411); Ascend 950 NPU support grew LongCat-2.0 (#47917), mixed-chunk pure-MLA (#47857) and MXFP8 KV cache (#48126); dflash draft decode now tops up KV pages batch-wide (#47685); paged-experts skips empty page-ins block-wide (#48605); sm_107a builds on CC 10.7 (#49420).
**Why it matters:** MXFP4 W4A16 MoE on consumer RDNA puts cheap 32 GB Radeon AI Pro cards on the sanctioned quant path, and the Flash-Next PLE fix cuts wasted prefill compute for the community's favorite 125B-class MoE.
**Local inference:** gfx12xx RDNA (R9700/CREATOR class), RTX PRO 5000/Blackwell workstation cards, and Strix/Adreno-adjacent configs benefit directly.
Source: sgl-project/sglang commit log 2026-11-09 22:45 → 2026-11-10 12:45 UTC — https://github.com/sgl-project/sglang/commits/main

## Community spins and quantizations

### DavidAU Qwen3.8-40B-Ultimate-Heretic-Uncensored-Turbo-Brilliance-50x-AGI-NEO-GGUF (Nov 10, 19:43 EST Nov 9)
DavidAU's new flagship in the Ultimate-Heretic line: Qwen3.8-27B (heretic/uncensored) expanded to 40B parameters, re-trained on a Claude-Opus reasoning dataset ("Qwen-Claude reasoning hybrid"), then fitted for Turbo Brilliance 50x V2 AGI edition — 21 AGI constructs plus AI constructs across 27 reasoning, 27 instruct and 9 xhigh modes, with a built-in "help" interactive system. The precision fine-tune reportedly cuts xhigh reasoning-token usage by ½–¹⁄₁₀ depending on prompt (AGI constructs themselves load as 6–8K-token prefills in 3–5 s). The GGUF ladder (BF16→low quantizations) was still uploading at post time; base merge is Apache-2.0. An r/LocalLLaMA commenter called the chat_template "a record."
**Why it matters:** DavidAU's 27B heretic spins are a staple of the 16–24 GB local tier; this is the first 40B expansion of the Qwen3.8 Ultimate line with the AGI-construct system.
**Local inference:** 40B quants land ~23–46 GB — two 16 GB cards, one 24/32 GB workstation card, or offload on Strix Halo; 4-bit class targets 24 GB cards.
Source: Hugging Face (repo created 2026-11-10 00:43 UTC) — https://huggingface.co/DavidAU/Qwen3.8-40B-Ultimate-Heretic-Uncensored-Turbo-Brilliance-50x-AGI-NEO-GGUF
Second source: r/LocalLLaMA post (Nov 10) — https://www.reddit.com/r/LocalLLaMA/comments/1xsde08/these_names_are_wild_but_i_love_themnew_model/

### In-window quant ladder: mradermacher + bartowski (Nov 9–10)
mradermacher: day-0 imatrix i1 + standard GGUFs of Austriani's Qwen3.8-4B-Distill-ARA-Abliterated (arbitrary-rank-ablation distill of the 4B Qwen3.8 line) and aasis001's Qwen2.5-1.5B-Instruct-heretic-ara. bartowski: GGUF ladders of ItachiSan's LiquidCode-2.5-24B-A2B (LFM2.5-MoE code fine-tune, 03:19 EST) and jmc0's Rein-NPC-4B (typed-decision game-NPC model — new quantizer tier after yesterday's ladder).
**Why it matters:** Fresh abliterated distills hit the 4B/1.5B sub-8GB tier fast — the segment that runs on NPUs, phones and 8 GB cards.
**Local inference:** 4B ARA-abliterated ≈2.5 GB at 4-bit (any 8 GB card/NPU); LiquidCode-24B-A2B MoE fits 16–24 GB; Rein-NPC-4B runs under 10 GB.
Source: https://huggingface.co/mradermacher/Qwen3.8-4B-Distill-ARA-Abliterated-GGUF · https://huggingface.co/bartowski/ItachiSan_LiquidCode-2.5-24B-A2B-GGUF · https://huggingface.co/bartowski/jmc0_Rein-NPC-4B-GGUF

## Also noted
- **Strata tuned for RTX 5090 + 4070 Ti Super over PCIe 3 x1** (ExTV/strata-5090-4070, pushed Nov 10): prefill on the big card, conversation parking via layer split, pread expert streaming — Qwen3.8-Flash-Next at 262K context; a commenter measured ~3x stock; tuned to that exact card pair and quality metrics are absent (author-reported). https://github.com/ExTV/strata-5090-4070
- **RustingStrata vs PolyStrata vs llama.cpp on one RTX 3060 12GB** (u/Serious_Scarcity7818, Nov 10 — update on previously reported RustingStrata): Qwen3.6-35B-A3B UD-Q3_K_M (15.9 GiB, exceeds VRAM), 5,290-token prompt: prefill 2,330 tok/s vs PolyStrata 1,458–1,549 vs llama.cpp b12119 1,692. https://www.reddit.com/r/LocalLLaMA/comments/1xsipew/rustingstrata_vs_polystrata_vs_llamacpp_on_one/
- **Qwen3.8-Flash-Next engram-table swap experiment** (u/Novel_Friendship913): swapping to the latest q8_0 engrams on Unsloth UD-Q4_K_XL produced no perceptible benefit on a 128 GB Strix Halo + gufo v0.33 — small negative result on the shared-MTP-module hype. https://www.reddit.com/r/LocalLLaMA/comments/1xsbmbx/qwen38flashnext_have_you_tried_swapping_the/

## Just missed the window
- **Felladrin's Comet 60B (A12B) GGUFs** — first stock-llama.cpp GGUF conversions of Multiverse Computing's CompactifAI-compressed Nemotron 3.5 Super (Q4_K_M 42.6 GB, Q8_0 64.2 GB, MTP head kept → `--spec-type draft-mtp`), repos created Nov 9 ~09:25–10:28 EST, ~7–8h before this window. https://huggingface.co/Felladrin/gguf-Q4_K_M-Comet-60B-A12B
- **bartowski's ReadyArt/Minimax-M3-jablit-GGUF** — imatrix ladder of the Jacobian-lens-abliterated 428B multimodal MiniMax M3 (experimental "jablit" abliteration), created Nov 9 ~09:58 EST, pre-window. https://huggingface.co/bartowski/ReadyArt_Minimax-M3-jablit-GGUF

## Coverage notes
- **Verified: 12 events** in this edition's report body (7 main items + 3 also-noted + 2 just-missed footnotes), each from a fetched page with an in-window timestamp (or explicitly dated pre-window for the footnotes); leading items read in full.
- **Ledger dedupe:** ~13 candidates suppressed as already reported or out-of-window re-coverage — incl. Ollama v0.40.16 GA, Google TPU 8tx/8i Max (previously footnoted), the 8x-V100 lawyer rig, RTX PRO 6000 cable-melt re-report, Alibaba Zhenwu V900 (Sep 22), NVIDIA IFA/Muse Glimmer (Sep), Qwen4/Qoduck stealth chatter (Nov 7), Apple M6 (Nov 6), Comet 60B base (Oct 15), AMD EPYC×TPU-8tx host PR (Nov 9 08:00 EST), WSP-Scope paper 2611.09382 (Nov 5), DeepMind AlphaProof Code and Anthropic IPO (out of scope).
- No in-window AI-hardware/silicon announcements verified tonight: ServeTheHome's Nov 9–10 slate (TPU 8tx, Dell R770AP [Nov 8], ASRock Rack DMRD16 [already reported]) fell outside; **VideoCardz "newest" feed 404'd** (index down this run); Tom's Hardware's Nov 9 evening block carried only re-coverages.
- Dead ends logged (two-strike rule): dnotit/dna-factory and zhenghaoz/clap.cpp GitHub lookups 404'd (repo names likely differ from Reddit titles) — DNA Factory (fine-tuning framework) and clap.cpp (GGML audio-embedding server) left unverified.
- Window computed once from the ET helper at 07:45 EST; no boundary stretching. Research phases: discovery ~07:46–08:10, triage+verify to ~08:20, writing thereafter.
