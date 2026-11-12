# AI Hardware & Local Models Brief — November 12, 2026 (07:45 ET)

Window: 2026-11-11 17:45 ET → 2026-11-12 08:34 EST (previous fire 17:45 Nov 11; this is the 07:45 edition)

## TL;DR
- A purported **96GB RTX 5090** surfaced as a photo listing on Chinese marketplace Goofish — the first photographed hardware in the 5090-96GB saga; VIK-on says the board is likely an RTX PRO 6000 Blackwell Max-Q transplant and calls it plausible but unverified.
- AMD's next "Halo" APU, now rumored as **"Siren Halo" (Zen 6 + RDNA 5)**, is leaked with **dedicated GDDR7 VRAM alongside LPDDR6** — a break from the Strix Halo unified-memory formula the local-AI community built rigs around.
- **Meta published meta-encoder-v1** (28.4B Muse-Glimmer-30B-based multimodal bi-encoder) to the Hub overnight — one encoder for retrieval and decision scoring, weights in-window.
- Overnight inference-stack waves: **SGLang shipped a disk tier for paged experts** (#49195), **vLLM landed SM90 MegaMoE for MXFP4 and block-FP8 experts**, and llama.cpp b12302–b12318 brought CUDA MoM tile-skips, few-row MMQ, Intel Vulkan flash-attention and NVFP4 prefill fusions.
- **KQuant**, a new quantization method from Nace (the Drex decision-model team), claims 1.45–3.7× KL-divergence reductions on Qwen-family MLX packs — author-measured, GGUF promised "tomorrow."

## AI hardware and silicon

**Purported 96GB RTX 5090 goes on sale in China — first photographed board** — event date: Nov 11–12 (VideoCardz article Nov 12, 08:15 GMT)
A listing on secondhand marketplace Goofish, shared by modding outlet VIK-on, advertises an "RTX 5090 96GB" mod inside a complete PC, business customers only under NDA with a placeholder price. The photo shows a genuine GB202-300-A1 die surrounded by Samsung K4VCF325ZC-SC28 GDDR7 packages — 3GB each at 28 Gbps, 16 on the visible side (48GB), implying 16 more on the back. VIK-on calls the mod technically possible but says the board is likely an RTX PRO 6000 Blackwell Max-Q donor (which already supports 96GB) with a 5090 chip transplant, the VBIOS being the main hurdle — and notes the listing could still be a scam. No working card has been independently verified.
*Why it matters:* After months of unverified 64/96GB claims and the AI-conversion-factory coverage, this is the first physical photo evidence that 3GB-die GDDR7 doubling is being productized. A credible 96GB Blackwell at a fraction of a PRO 6000 (street $16.5k+) would change the sizing conversation for Qwen3.8-Flash-Next/DSv4.1-Flash-class MoE rigs.
*Update on:* ledger 2026-11-02 "rtx5090-64-96gb-mod-claim" (TweakTown, unverified).
*Local inference:* targets 16GB-card owners' capacity ceiling — 96GB runs UD-Q4_K_XL-class 200B+ MoE fully in VRAM; 3GB GDDR7 availability is the gating constraint.
Sources:
- Alleged GeForce RTX 5090 with 96GB listed for sale in China — VideoCardz, Nov 12 2026 08:15 GMT — https://videocardz.com/newz/alleged-geforce-rtx-5090-with-96gb-listed-for-sale-in-china
- r/LocalLLaMA discussion — Nov 12 2026 13:12 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xucffq/alleged_geforce_rtx_5090_with_96gb_listed_for/

**AMD "Siren Halo": Zen 6 / RDNA 5 Halo APU rumored with dedicated GDDR7** — event date: Nov 12 (Gotou_3rd leak; VideoCardz 11:20 GMT)
Leaker Gotou_3rd says the next Halo-class APU is named "Siren Halo," replaces the previously rumored Medusa Halo (the Medusa rename consistent with Kepler_L2's Oct 30 roadmap claims), and carries **dedicated GDDR7 VRAM in addition to LPDDR** support — after an update confirming both memory types. The GPU is described as "AT2"-based RDNA 5, and VideoCardz reports a likely connection to the next-gen Xbox "Magnus" chip (Zen 6 + Zen 6c, 68 RDNA 5 CUs, up to 48GB GDDR7 on a 192-bit bus). A separate VideoCardz line says the lack of a new Halo design in 2027 explains the rename.
*Why it matters:* The Halo line (Strix Halo / Ryzen AI Max 300, and Gorgon Halo 400) is the community's go-to 96–128GB unified-memory local-inference platform. Splitting the iGPU onto dedicated GDDR7 would decouple graphics from the RAM pool — bigger effective VRAM for models, but a different memory-sizing math than the coherent 256-bit LPDDR5X designs this audience standardized on.
*Local inference:* 128GB-class APU mini-PC/workstation segment; GDDR7 variant would target the 48GB+ dedicated-VRAM footprint at Strix-Halo price points.
Sources:
- AMD next-gen "Siren Halo" Zen6/RDNA5 APU rumored to support GDDR7 memory — VideoCardz, Nov 12 2026 11:20 GMT — https://videocardz.com/newz/amd-next-gen-siren-halo-zen6-rdna5-apu-rumored-to-support-gddr7-memory
- AMD Siren Halo APU rumored to feature dedicated GDDR7 VRAM — Notebookcheck, Nov 11 2026 (~19:45 EST) — https://www.notebookcheck.net/AMD-Siren-Halo-APU-rumored-to-feature-dedicated-GDDR7-VRAM-alongside-LPDDR-memory.1448811.0.html

## Model releases

**Meta publishes MetaEncoder (facebook/meta-encoder-v1) — 28.4B multimodal bi-encoder from Muse-Glimmer-30B** — event date: Nov 12 (HF repo created 2026-11-12T02:00Z)
Meta pushed the weights for MetaEncoder, an instruction-following embedding model built by contrastively fine-tuning meta-models/Muse-Glimmer-30B (28.4B params, BF16, MuseGlimmerModel architecture). You give it a free-form task description plus candidates (text with image/video as side information); task and candidates are encoded separately, and matching is a plain inner product — so it serves millions of candidates from an ANN index without re-running the model, and representations are cacheable. An eval harness (facebook/meta-encoder-eval) and paper (arXiv 2610.11316) accompany it; the earlier `facebook/meta-encoder` repo is Apache-2.0 while the new v1 card carries no license section yet — flag for reuse.
*Why it matters:* Decision/routing pipelines are this community's current obsession, and a strong open multimodal bi-encoder that replaces "generate-then-judge" with cached dot-products is directly deployable for agent tool routing, RAG reranking and guardrail scoring on a single big card.
*Local inference:* 28.4B BF16 ≈ 57GB → 48–64GB class (M3/M5 Ultra, 2×24GB) today; INT8 ≈ 28GB drops it to 32GB cards once quants land; embedding heads make it KV-cheap.
Sources:
- facebook/meta-encoder-v1 — Hugging Face, repo created Nov 12 2026 02:00 UTC — https://huggingface.co/facebook/meta-encoder-v1
- r/LocalLLaMA thread (includes v1-vs-base note) — Nov 12 2026 09:28 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xu8eie/metaencoder_a_facebook_collection_one_encoder_for/

## Community spins and quantizations

**KQuant: new quantization recipe from Nace claims 1.45–3.7× better KL on Qwen-family MLX packs** — event date: Nov 12 (r/LocalLLaMA post 10:47 UTC)
Alish, an ML engineer at Nace (the Drex decision-model team, #1 on the Artificial Analysis decision index 2.1), released KQuant — a quantization method with open Q4 MLX packs for Qwen3.5-4B, Qwen3.5-9B, Qwen3.8-27B, Qwen3.6-35B-A3B and Qwen3.8-Flash-Next, "free on Hugging Face." Reported KL divergence vs the full-precision next-token distribution: Qwen3.8-27B 0.035 vs 0.054 public (1.5× better), Qwen3.6-35B-A3B 0.059 vs 0.096 (1.6×), Qwen3.5-9B 2.6×, with the title claiming 3.7× for Flash-Next 180B. Sizes track stock 4-bit pack sizes (27B at 15.13GB). GGUF is promised "tomorrow." Treat with care: numbers are author-run original-text KL, and commenters immediately demanded comparisons against Unsloth Dynamic 3.1, turboderp EXL3 and ISTA GSQ-RCO, which the launch does not include.
*Why it matters:* The quant war (Unsloth UD v3.1 GA, MACaQ, EXL3) got another entrant the same week — more independent Q4 methods means more pressure on the incumbents' claim discipline.
*Local inference:* MLX-first — targets 16GB/32GB Apple Silicon at the 4-bit tier (27B Q4 at ~15GB fits 16GB Macs with effort).
Sources:
- KQuant — new quantization technique, 3.7× better quants (MLX) — r/LocalLLaMA (u/m4rkGS), Nov 12 2026 10:47 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xu9onk/kquant_new_quantization_technique_37_better/

**MACaQ-R: Huawei's open quant method lands its first big-model release (Qwen3.6-35B-A3B, 22.36GB Q4_K_XL)** — event date: Nov 11 (repo created 23:28 UTC) — *update on previously reported* (ledger Nov 11 "MACaQ-Qwen3-4B-IQ4_XS")
Huawei's huawei-csl team pushed MACaQ-R GGUFs for Qwen3.6-35B-A3B: a single Q4_K_XL file, 22.36GB, 262K context, Apache-2.0, backed by arXiv 2611.10649 and a GitHub toolkit (github.com/huawei-csl/MACaQ) whose pitch is quantizing models on your own hardware. Measured KL to FP16: MACaQ-R Q4_K_XL 0.013367 vs Unsloth UD-Q4_K_XL 0.015731 — though commenters correctly note Unsloth never shipped a v3.1 for this base, so the comparison is against Dynamic 2.0-era numbers, and the earlier IQ4-XS-macaq tier required a llama.cpp fork while this Q4_K_XL is reported stock-compatible.
*Why it matters:* A reproducible, self-hostable quantizer beating imatrix pipelines — validated on a 35B MoE — is a structural threat to single-quantizer lock-in.
*Local inference:* 22.36GB fits 24GB cards or 16GB+RAM offload; 35B-A3B at 262K ctx is the sweet spot class for RTX 4090/5070Ti-tier rigs.
Sources:
- huawei-csl/Qwen3.6-35B-A3B-MACaQ-R-GGUF — Hugging Face, created Nov 11 2026 23:28 UTC — https://huggingface.co/huawei-csl/Qwen3.6-35B-A3B-MACaQ-R-GGUF
- r/LocalLLaMA thread (KL table) — Nov 12 2026 08:21 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xu7cyr/huaweicslqwen3635ba3bmacaqrgguf_hugging_face/

**Day-0 GGUF ladders: bartowski's GLiDE-9B (decision model), mradermacher's H3-Image-PE, InnovatorVL-v2, Japanese-RP Gemma-4-31B, Qwen2.5-Coder-32B-abliterated-v3** — event date: Nov 12 (repos 01:16–06:16 UTC)
bartowski quantized fastino/GLiDE-9B (06:03Z) — the Nov 1 Qwen3.5-9B-based decision model with a readout head, decision_config and NVFP4 module path, on the JEV decision-index spaces. mradermacher's overnight batch (all verified in-window repo creation) covers MiniMaxAI/MiniMax-H3-Image-PE in FP4 GGUF (06:03Z — the prompt-expansion model that drives H3-Image), InnovatorLab/InnovatorVL-v2 science VLM (06:16Z), an abliterated Japanese-RP Gemma-4-31B pair (01:16Z/08:04Z), jsantillana's Qwen2.5-Coder-32B-abliterated-v3 refusal-direction research spin (05:03Z), and the i1 tier of schneewolflabs/B6-27B (ORPO agent-tuned Qwen3.8).
*Why it matters:* Same-day quant coverage remains the difference between a release and a usable release; the H3-Image-PE tier is the piece that makes the MiniMax image stack practical to run alongside the 68GB main model.
*Local inference:* GLiDE-9B Q4 ≈ 5GB (any 8GB card); H3-Image-PE FP4 ≈ sub-6GB (co-resides on the 16GB card that runs the community H3-Image reductions); 31–32B tiers land at 17–20GB — 24GB-card territory.
Sources:
- bartowski/fastino_GLiDE-9B-GGUF — Hugging Face, created Nov 12 2026 06:03 UTC — https://huggingface.co/bartowski/fastino_GLiDE-9B-GGUF
- mradermacher/MiniMax-H3-Image-PE-GGUF — Hugging Face, created Nov 12 2026 06:03 UTC — https://huggingface.co/mradermacher/MiniMax-H3-Image-PE-GGUF
- mradermacher/InnovatorVL-v2-GGUF — Hugging Face, created Nov 12 2026 06:16 UTC — https://huggingface.co/mradermacher/InnovatorVL-v2-GGUF
- mradermacher/gemma-4-31B-it-abliterated-Japanese-RP-GGUF — Hugging Face, created Nov 12 2026 01:16 UTC — https://huggingface.co/mradermacher/gemma-4-31B-it-abliterated-Japanese-RP-GGUF
- mradermacher/Qwen2.5-Coder-32B-Instruct-abliterated-v3-i1-GGUF — Hugging Face, created Nov 12 2026 05:03 UTC — https://huggingface.co/mradermacher/Qwen2.5-Coder-32B-Instruct-abliterated-v3-i1-GGUF

**Field data: DSv4.1-Flash-1017 EXL3 2.9bpw holds up on APEX-2025 across six power-limited 3090s** — event date: Nov 12 (r/LocalLLaMA post 00:50 UTC)
u/hp1337 benchmarked the LibertaIDAI DeepSeek-V4.1-Flash-1017 EXL3 2.9bpw quant (6x RTX 3090, power-limited to 175W, Project Maya engine): ~1,000 t/s prefill, ~75 t/s decode, and APEX-2025 math scores trading blows with frontier models from six months ago. Pushback in-thread: a repeat tester found the same 2.9bpw-class EXL3 quant scored 10/100 on a strict pass vs 17/100 for Unsloth IQ2_XXS and 13/100 for ISTA — quant quality claims at this bit-width still hinge on whose eval you run.
*Why it matters:* APEX-2025 (HF MathArena) is emerging as the community's harder-than-TB2.1 reasoning signal, and Project Maya keeps showing up as the multi-GPU engine of choice for huge MoE.
*Local inference:* 6×3090 at 175W (~1050W package) serving a 748B-class MoE at 75 t/s decode — the 2-bit expert-resident recipe keeps 128GB DDR4 rigs competitive.
Sources:
- DeepSeek V4.1 Flash 1017 exl3 2.9bpw APEX 2025 benchmark results — r/LocalLLaMA (u/hp1337), Nov 12 2026 00:50 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xtyxl9/deepseek_v41_flash_1017_exl3_29bpw_apex_2025/
- Quant: LibertaIDAI/DeepSeek-V4.1-Flash-1017-EXL3-2.9bpw — Hugging Face — https://huggingface.co/LibertAIDAI/DeepSeek-V4.1-Flash-1017-EXL3-2.9bpw

## Inference software and benchmarks

**SGLang overnight wave: paged-experts gets a disk tier (and Kimi-K3.1/MiniMax/H3 plumbing)** — merges Nov 11 22:45–Nov 12 13:34 UTC (71 PRs)
Headliner #49195 adds **a disk tier for paged experts** — experts that don't fit in host RAM now spill to NVMe as a third level under the Nov 2 v0.5.23 "Paged Experts" CPU-offload feature — extending huge-MoE serving to machines with modest DDR5. The rest of the wave: #50005 Kimi-K3.1 DSA layers on PD decode with DFLASH, #49851 ModelOpt MXFP8 KDA for K3.1-Pro, #49665 draft-KV ring over Mooncake PD disaggregation (+ #49664 sliding-window MLA draft KV ring), #48412 relaying routed experts through pipeline-parallel stages, #48295 NVFP4-quantizing block-FP8 MTP draft experts, #49707 running MiniMax-H3-Image on ROCm with MI300X→MI355X cookbook recipes (update on the H3-Image series), #48996 FP8 DSA KV default on gfx950, and #50018 defaulting GLM-5.3-Flash to an agentic Mamba pool split.
*Why it matters:* The disk tier is the consumer-rig story: host-RAM exhaustion was the wall for 700B+ MoE on 4-GPU boxes, and the engine is now formally GPU→DRAM→SSD three-level for weights, matching what paged attention did for KV.
*Local inference:* 748B-class models on 64GB DDR5 + NVMe become schedulable; gfx950 FP8-KV default and H3-Image ROCm recipes widen AMD usability.
Source:
- sgl-project/sglang merged PRs (in-window) — GitHub — https://github.com/sgl-project/sglang/pull/49195 (wave: #50005, #49851, #49665, #48412, #48295, #49707, #48996, #50018)

**vLLM wave: SM90 MegaMoE for MXFP4 and block-FP8 experts; ROCm FP8/MXFP4 MLA KV caches** — merges in-window (62 PRs)
#64061 runs DeepGEMM Mega MoE on SM90 with MXFP4 experts, and #62603 enables SM90 block-FP8 MegaMoE via FlashInfer MoE-EP — bringing the Blackwell-style fused-MoE fast path to Ada/Hopper, which is the majority of installed local-inference silicon. #67040 adds DeepGEMM BF16 MegaMoE for unquantized GLM-5.3. On AMD: #66036 enables tensor-scale FP8 KV cache for DeepSeek V4/4.1 sparse MLA on ROCm and #60321 adds an mxfp4_mla KV cache for rope-free sparse MLA. #65373 shares DeepSeek V4.1 CPU KV across TP ranks, and #66891 lands an Inkling renderer in the frontend (Thinking Machines model family gaining first-class tool-call rendering).
*Why it matters:* SM90 MegaMoE is a decade-scale install base just inheriting Blackwell's MoE decode/prefill tricks; two KV-cache formats on ROCm keep AMD's long-context economics closing the gap.
*Local inference:* RTX 4090/5090 (SM89/SM120 users adjacent) — the fused-MoE kernels cut decode latency for A3B-class models on 24GB/32GB cards; ROCm FP8-KV doubles usable context on 32GB MI300-class.
Source:
- vllm-project/vllm merged PRs (in-window) — GitHub — https://github.com/vllm-project/vllm/pull/64061 (wave: #62603, #67040, #66036, #60321, #65373, #66891)

**llama.cpp b12302–b12318: MoE tile-skips, few-row MMQ, NVFP4 prefill fusion, Intel/Adreno attention** — builds Nov 12 02:22–11:36 UTC (29 PRs merged)
Merged in-window: CUDA skips empty MoE tiles in the MMQ stream-k fixup (#32067) and a few-row MMQ for batch sizes 2–24 (#31533) — both aimed at the low-batch decode path that dominates local serving; a fused weight-scale epilogue MMQ for NVFP4 checkpoints speeds CUDA prefill (#26048). Vulkan gains an Intel flash-attention prefill kernel (#29357), OpenCL gains decomposed flash-attention for Adreno (#32046), CPU gains the 4×4 NEON simd_gemm tile (#32128), and SpacemiT RISC-V gets Q4_1 dispatch (#31929) plus a GATED_DELTA_NET RVV kernel (#31897). Also: SYCL AOT (arl-h) >4GB garbage-output fix (#31873), backend prefetch split-awareness (#32100), and a server security batch hardening CORS/loopback/tools-runtime surfaces (#32113/#32115/#32120).
*Why it matters:* The few-row MMQ + tile-skip pair directly targets agent-style low-batch MoE decode; Intel iGPU and Adreno users get real attention kernels instead of fallbacks; the CORS fixes matter for anyone exposing llama-server on a LAN.
*Local inference:* CUDA (12–16GB), Intel Arc iGPU (Strix Halo/Lunar Lake), Snapdragon X2 Adreno and RISC-V single-board class all see kernel-level gains this build range.
Source:
- ggml-org/llama.cpp releases b12302–b12318 — GitHub — https://github.com/ggml-org/llama.cpp/releases (PRs #32067, #31533, #26048, #29357, #32046, #32128, #31929, #31897, #31873, #32100, #32113, #32115, #32120)

## Also noted
- **Beelink's Panther Lake NAS with Arc B390 priced at $1,725**, sales starting Nov 16 — 16-core Core Ultra + B390 + 32GB LPDDR5X in an OCuLink-equipped 10GbE-capable box (VideoCardz, Nov 12 — https://videocardz.com/ ; AI angle: B390 + NPU make it an unofficial edge-inference box; price/availability as listed).
- **NVIDIA board reportedly preparing to oust Jensen Huang** — top of r/LocalLLaMA and MacRumors (https://www.macrumors.com/2026/11/12/johny-srouji-eyed-as-potential-nvidia-ceo/), reported ~18:30 Nov 11; corporate governance with no hardware/local-model consequence — logged for completeness, not counted as a brief item.

## Just missed the window
- **Voltage-glitching a CMP 170HX into a real A100-80G** — Kate's blog post (https://blog.kate.land/posts/using-voltage-glitching-to-unlock-the-a100/) published Nov 10: GA100 voltage-fault-injection blows "fuseless fuse" rows to unlock 64–80GB HBM, 120–126 SMs, PCIe Gen4 and NVLink on the mining card — ~85.9 TFLOP/s FP16 vs 72.5 retail A100; went viral on r/LocalLLaMA at 02:47 UTC today (in-window coverage, out-of-window disclosure — footnoted once, per policy).
- **Meta's MetaRoCE NIC at OCP Summit 2026** — ServeTheHome's write-up posted Nov 11 (https://www.servethehome.com/this-is-the-meta-metaroce-network-adapter/): open RDMA transport (packet spraying, per-path congestion control, runs on commodity ECN+ECMP Ethernet) shown as an OCP NIC 3.0 card, previously demoed on AMD Pensando; the booth disclosure predates the window — footnoted once.

## Coverage notes
- **Verified count: 13 report items** (5 leads read in full + 3 software-wave leads verified via merged-PR search with per-PR titles/timestamps, 4 spin/bench items repo-verified, 2 also-noted) plus 2 once-only "Just missed the window" footnotes.
- **Ledger dedupe: ~14 candidates suppressed as already reported or already footnoted** — incl. Unsloth UD v3.1 ladder refresh (Nov 11 12:45), MAXENITH-32B-i1 and eidolon-v3-i1 (Nov 11 17:45), Raon-1-nano re-coverage wave (missed-tagged), bartowski Nex-N2.6 GGUF (missed-tagged, pre-window creation), HP ZGX Fury / RTX 5080-24GB cancellation / Radeon SSD-write-blocker (Nov 11), GIGABYTE AI TOP AERO (missed-tagged), EPYC 9006 benchmark re-coverage (Nov 10 + missed-tagged), TPU 8tx (missed-tagged), BotPsy-0.8B (repo Nov 10) and Runway Praxis-1 (repos Nov 10) — both in-window coverage of pre-window disclosures, held to nothing per policy.
- Dead ends: VideoCardz /newest and HF /trending 404'd (used front page + API instead); DuckDuckGo backend flaked on two queries (Nous fallback served them); third r/LocalLLaMA "new" page 429'd. KQuant's HF pack URLs sit past the anonymous-feed body truncation — cited via the thread itself.
- Sources swept this run: ServeTheHome, VideoCardz, Notebookcheck, MacRumors, GitHub (llama.cpp/vLLM/SGLang/Ollama/TensorRT-LLM), Hugging Face API (mradermacher/bartowski/unsloth/qvac/fastino/huawei-csl/facebook/runwayml), r/LocalLLaMA (23 in-window posts), HN Algolia river. No Ollama (latest v0.40.17-rc0, Nov 10) or TensorRT-LLM (rc3, Nov 6) releases in-window.
