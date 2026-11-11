# AI Hardware & Local Models Brief — November 11, 2026 (07:45 ET)

**Window:** 2026-11-10 20:08 EST → 2026-11-11 07:45 EST (previous fire → now) · 13 verified items (6 leading, 7 also-noted), 4 "just missed the window" footnotes (new items only; 16 candidates suppressed as already reported).

## TL;DR

- **Qwen4-Max-Preview is live** on QwenCloud and Alibaba Cloud Model Studio as of November 11 — a closed 1M-context preview (alias qwen4.0-max-preview), timed to Alibaba's Double-11 promo; open weights still not committed.
- **HP's GB300 tower, the ZGX Fury, is now orderable** in the US at $132,000–$136,000 with 748GB of coherent memory.
- **NVIDIA reportedly canceled the RTX 5080 24GB** and will keep the 16GB model in production — 3GB GDDR7 modules now cost ~3x the 2GB parts.
- **llama.cpp merged CUDA OCP FP8 weights support (#28413)** overnight — FP8 GGUFs now run on CUDA with per-tensor scales; the same window shipped builds b12263–b12283.
- **vLLM published its EPD (encoder/prefill/decode disaggregation) deep-dive** and merged first **Rubin VR200 recipes**; SGLang landed a 58-PR wave headlined by Kimi-K3.1 DFlash2 spec-decode.

---

## AI hardware and silicon

**HP ZGX Fury GB300 AI Station goes on sale: $132,000–$136,000** — event date November 11 (US retail listings live; Notebookcheck reported the landing November 10, VideoCardz priced it today)
HP has started selling the ZGX Fury AI Station on its US webstore and at B&H. Every SKU carries the NVIDIA GB300 Grace Blackwell Ultra desktop superchip (72 ARM Neoverse V2 cores), 496GB LPDDR5X-6400 coherent with up to 276GB of GPU-visible memory (748GB total), dual 2TB NVMe with two more M.2 slots (up to 32TB), three PCIe Gen5 slots and QSFP112 400Gbps ports for linking two units. HP unveiled the machine in early September without a price; Notebookcheck inferred >$100k — actual MSRPs landed well above that.
*Why it matters:* the third GB300 "DGX Station-class" tower to reach pricing (after the MSI XpertStation and Supermicro's AI Station), and the first from HP with a hard MSRP — a real datapoint for what trillion-parameter-class on-prem inference costs when you don't rent a rack.
*Local inference:* 748GB coherent unified memory targets full-precision trillion-param MoE inference and 100B+ fine-tuning on a single tower; B&H's listed config pairs the GB300 with Ubuntu 24.04 and optional discrete RTX Pro 4000 Blackwell.
Source: Notebookcheck, "HP releases new ZGX desktop with Nvidia GB300, 496 GB LPDDR5X RAM and 276 GB VRAM," Nov 10, 2026 — https://www.notebookcheck.net/HP-releases-new-ZGX-desktop-with-Nvidia-GB300-496-GB-LPDDR5X-RAM-and-276-GB-VRAM.1446984.0.html
Source: HP ZGX Fury product page — https://www.hp.com/us-en/workstations/ai-stations/zgx-fury-ai-station.html ; B&H listing (E70LKUT#ABA) — https://www.bhphotovideo.com/c/product/1998077-REG/hp_e70lkut_aba_zgx_fury_g1n_ai.html ; VideoCardz carried the $132,000–$136,000 pricing on its homepage Nov 11 (article "HP launches ZGX Fury desktop with 748GB of memory, priced at $132,000," slug not yet in RSS).

**NVIDIA reportedly kills the RTX 5080 24GB; 16GB lives on** — event date November 11, 08:15 UTC (rumor)
Citing Chinese board-partner channel source Board Channels, VideoCardz reports NVIDIA told AIB partners the GeForce RTX 5080 24GB will not launch in January (2027) with no replacement date. The card's plan hinged on eight 3GB GDDR7 modules — now reportedly ~3x the price of the 2GB chips — making an acceptable price impossible; the same memory economics have delayed the RTX 5080 SUPER. NVIDIA will reportedly keep supplying 2GB-chip GPUs so partners continue the 16GB model, which had been headed for end-of-life. Unconfirmed by NVIDIA; the 24GB model was never officially listed.
*Why it matters:* the GDDR7 cost curve (an AI-memory-crowding consequence) just deleted the only credible sub-flagship 24GB consumer path, extending the 16GB era that most local-AI rigs are built on — and echoing the Oct 23 report that first priced the rumored 24GB variant at ~$2,990.
*Local inference:* the 16GB RTX 5080 stays the viable mid-tier local-inference card (Qwen3.8-27B-class Q4 fits with offload); anyone waiting on 24GB of GDDR7 for the next tier should plan around 5090 32GB or PRO-class cards instead.
Source: VideoCardz (WhyCry), "NVIDIA reportedly cancels GeForce RTX 5080 24GB launch, brings back 16GB model over GDDR7 costs," Nov 11, 2026 08:15 UTC — https://videocardz.com/newz/nvidia-reportedly-cancels-geforce-rtx-5080-24gb-launch-brings-back-16gb-model-over-gddr7-costs
Source: r/LocalLLaMA discussion — https://www.reddit.com/r/LocalLLaMA/comments/1xtdwps/nvidia_reportedly_cancels_geforce_rtx_5080_24gb/

**Modder ships tool to stop AMD Radeon drivers hammering SSDs; Adrenalin 26.11.1 fix pending** — event date November 11, 10:30 UTC
A ComputerBase forum member (Necandi) released an open-source utility that blocks the Radeon driver's repeated file copies (via AMD External Events Utility) that have been racking up thousands of gigabytes of SSD writes; AMD had confirmed the bug (November 9) with a fix promised in Adrenalin 26.11.1. The tool is a stopgap that leaves the external-events service otherwise intact.
*Why it matters:* the persistent-write bug quietly erodes NVMe endurance on 24/7 inference boxes — the exact duty cycle Radeon AI Pro R9700/32GB cards are getting put to in the local-AI community.
*Local inference:* relevant to any always-on rig running AMD graphics (R9700, 7900 XTX); apply before the driver fix lands.
Source: VideoCardz, "Modder releases tool to stop AMD Radeon drivers from constantly writing to SSDs," Nov 11, 2026 10:30 UTC — https://videocardz.com/newz/modder-releases-tool-to-stop-amd-radeon-drivers-from-constantly-writing-to-ssds

---

## Model releases

**Qwen4-Max-Preview goes live on QwenCloud — Alibaba's Qwen4 era opens closed, on Double-11** — event date November 11
Model id `qwen4-max-preview` (doc alias `qwen4.0-max-preview`) appeared in Token Plan docs on both the 千问 AI platform and QwenCloud on November 11, listed with reasoning, text generation and visual understanding, 1M-token context, up to 393,216 output tokens, and no published price — the preview is scheduled to run through November 25 on discounted token plans. Community threads note Alibaba "rushed" the preview for the 11.11 shopping festival, and several complain the branding leaves open-weight timing unanswered; a widely-shared single-prompt Flappy Bird test is already circulating. This is the first public Qwen4 artifact — the open-weights lineage (Qwen3.8-27B/Flash-Next's successor family) remains unscheduled, tracking the lab's previous max-first, weights-later pattern.
*Why it matters:* Qwen is the most-deployed open-weight family in local inference; a Max preview with no small tiers announced (see last week's unverified "no 4B/9B/35B" rumor) sets expectations for what Qwen4 will — and won't — run on your hardware.
*Local inference:* nothing to download today — closed API preview only; the sizing implication is that Qwen4's public tiers land later, and 27B/35B-class local quants remain Qwen3.8-era until then.
Source: r/LocalLLaMA, "qwen4-max-preview on qwencloud" (u/Evening_Ad6637, Nov 11) — https://www.reddit.com/r/LocalLLaMA/comments/1xtckm8/qwen4maxpreview_on_qwencloud/
Source: AinovaTools Qwen-4 tracker (Nov 11 doc confirmation) — https://ainovatools.com/news/qwen-4-release-date ; OrcaRouter "Qwen4 Max vs Qwen4-Max Preview" (Nov 11) — https://www.orcarouter.ai/blog/qwen-4-max-preview-vs-qwen-4-max ; Alibaba Cloud developer article — https://developer.aliyun.com/article/1782128 ; context: "Qwen4 open weights not soon?" — https://www.reddit.com/r/LocalLLaMA/comments/1xtfk3n/qwen4_open_weights_not_soon/

**Baidu: ERNIE 5.5 Flash full release in two weeks** — event date November 11 (announcement)
Baidu's official X account said ERNIE 5.5 Flash will be released in two weeks (≈Nov 25), per an r/LocalLLaMA post. The ERNIE-5.5-Flash-Base checkpoint (280B-A14B MLA+HCA hybrid, 128K-train/1M-infer) already landed quietly Nov 2 — but its license excludes EU/UK/KR and carries a $50M-ARR clause.
*Why it matters:* a dated runway for a frontier-class Chinese hybrid-attention model whose open checkpoint already exists — day-0 quant interest is guaranteed if the full release keeps the weights.
*Local inference:* the Nov 2 base checkpoint fits ~160GB class (Q4) — 32GB+offload territory; watch whether the retail release adds anything beyond the leaked base.
Source: r/LocalLLaMA (u/jacek2023, Nov 11) — https://www.reddit.com/r/LocalLLaMA/comments/1xted8w/ernie_55_flash_will_be_released_in_two_weeks/ (via https://x.com/Baidu_Inc/status/2120391732248637734)

---

## Community spins and quantizations

**mradermacher adds imatrix (i1) tier to Sage-A1.5-Concise** — event date November 11, 01:50 UTC
The overnight ladder was thin: the notable in-window upload is mradermacher/Sage-A1.5-Concise-i1-GGUF, an imatrix tier of TheFusionCube's RL efficient-reasoning MoE (Apache-2.0), supplementing the full-GGUF ladder from yesterday evening. bartowski's newest uploads (Rein-NPC-4B, LiquidCode-2.5-24B-A2B) both predate the window.
*Why it matters:* imatrix tiers measurably cut low-bit perplexity on MoE reasoners — the practical build for 8–12GB cards.
*Local inference:* Sage-A1.5-Concise targets small-active MoE; i1 tiers typically run 4–6GB at Q4_K_XL, fine on 8GB cards.
Source: mradermacher/Sage-A1.5-Concise-i1-GGUF, created Nov 11 01:50 UTC — https://huggingface.co/mradermacher/Sage-A1.5-Concise-i1-GGUF

**Community ports Memorilla memory layer to Gemma-4 on Strix Halo** — event date November 11 (post)
u/DataPhreak ported snap-stanford's Memorilla (arXiv 2610.21375) from CUDA to train Gemma-4 26B-A4B on Ryzen AI Max (Strix Halo), working around Gemma-4's unique encoder dimensions.
*Why it matters:* external-memory training methods reaching AMD unified-memory boxes broadens who can experiment beyond CUDA clusters.
*Local inference:* 128GB unified-memory Strix Halo as a fine-tuning target for 26B-class MoE.
Source: r/LocalLLaMA (u/DataPhreak, Nov 11) — https://www.reddit.com/r/LocalLLaMA/comments/1xtfle1/training_memorilla_on_strix_halo_for_gemma4/

**GLB-3D-Coder gets a 1.7B Godot variant** — event date November 11 (post)
ConfidentDinner6648 followed up the CCORE-GLB-3D-Coder 4B line with a Qwen3-1.7B fine-tune targeting Godot asset generation; Q8 GGUF is 1.83GB, aimed at CPU/limited-VRAM use.
*Why it matters:* same text-to-3D-asset workflow at half the footprint — viable on phones and iGPUs.
*Local inference:* 1.83GB Q8 → any 4GB card or modern CPU.
Source: r/LocalLLaMA (u/ConfidentDinner6648) — https://www.reddit.com/r/LocalLLaMA/comments/1xt6rc7/glb3dcoder_i_tried_making_a_17b_version_for_godot/

---

## Inference software and benchmarks

**llama.cpp merges CUDA OCP FP8 weights — b12263–b12283 overnight wave** — event date November 11, 08:44 UTC (merge)
PR #28413 ("CUDA: Add OCP FP8 support") landed at 08:44 UTC: the CUDA backend now executes OCP-FP8 (E4M3) ggml tensors with runtime static per-tensor scales. Author llama-bench snapshot ran Qwen3.8-27B's FP8 GGUF (19.56GiB) on an RTX PRO 4500 Blackwell 32GB; MoE FP8 kernels and FP8-native prefill are deferred follow-ups. Same window: MMVF for few-row BF16 at small batch (#31959), CUDA flash-attention MMA for head sizes 40/72 (#30285), sm70 FA config DKQ/DV=256 (#29810), a fixed rocBLAS workspace on HIP (#31838), Vulkan dequantized-KV with coopmat2 (#31526), CUDA-graph re-instantiation on cluster-dim change (#31284), and an imatrix filepath metadata-leak fix (#32000).
*Why it matters:* FP8 model weights on CUDA — alongside yesterday's FP8 KV-cache merge (#28392) — is llama.cpp's march toward matching vLLM/TensorRT precision support on consumer Blackwell, with 27B FP8 now fitting 24–32GB cards headroom-rich.
*Local inference:* direct benefit to RTX 40/50 and PRO Blackwell owners; 27B-class FP8 GGUFs at ~19.5GB leave KV room on 32GB cards.
Source: GitHub PR #28413 (merged Nov 11 08:44 UTC) — https://github.com/ggml-org/llama.cpp/pull/28413 ; builds b12263–b12283 — https://github.com/ggml-org/llama.cpp/releases

**vLLM ships EPD disaggregation deep-dive; 46-PR wave adds first Rubin VR200 recipes** — event date November 11
The vLLM blog (with Inferact, InferMatrix and IBM) details encoder/prefill/decode disaggregation: metadata-only multimodal requests (no re-preprocessing downstream), a dedicated encoder runner with encoder-only CUDA graphs, NIXL/Mooncake embedding transfer, cross-encoder cache reuse, and router-level orchestration — metadata-only EPD is live for DeepSeek-V4.1-Flash on main (#62802). Merges in the window: Kimi-K3 and MiniMax-M3.1-Flash recipes gain **Rubin VR200** support (#65400, 01:33 UTC — vLLM's first VR200 recipe row); DSv4.1 Engram 128-row lookup tiles with GPU tables split across engines (#66552) plus MXFP4-packed Engram embedding tables (#61530); lazy GPU-cache offloading (#61797); opt-in FlashInfer SSD prefill scan for Mamba (#61501); DSpark sliding-window MLA drafts under DCP (#63796); Muse Spark DSpark norm/Markov fusion (#66764); Kimi-K3.1-Pro B300 AgentX presets aligned to InferenceX (#66433).
*Why it matters:* EPD moves vision encoding off LLM GPUs — the architecture question for any self-hosted VLM fleet — and the VR200 recipes are the first upstream vLLM tuning data for Rubin's inference SKUs.
*Local inference:* EPD is datacenter-scoped, but Engram/MTP/DSpark work trickles down to single-box MoE serving; metadata-only handling reduces redundant image decode on modest rigs.
Source: vLLM blog, Nov 11, 2026 — https://vllm.ai/blog/2026-11-11-encoder-disaggregation-epd ; GitHub merges via search — https://github.com/vllm-project/vllm/pull/65400

**SGLang 58-PR wave: Kimi-K3.1 DFlash2 draft serving, per-M decode tactics, FA4 FP8 KV for Gemma-4** — event date November 11
Highlights merged 01:08–12:45 UTC: #49621 serves the Kimi-K3.1 DFlash2 sliding-window-MLA draft (12:30 UTC); #49478 adds per-M decode GEMM tactics + side-stream attention GEMMs for Kimi-K3.1; #49458 gives Kimi-K3 video support via frame sampling; #46965 enables FlashAttention-4 SM90 FP8 E4M3 KV cache for head_dim 256/512 (Gemma-4); #44588 adds an online acceptance estimator for DSpark confidence-scheduled verification; AMD track: fused KDA decode with BF16 state and Quark weights (#41794), in-place breakable-graph attention output (#49567), MI35x InferenceX AgentX Qwen3.5 MXFP4 MTP nightly (#49534); NPU track gains weight cache + overlapped startup load (#41495/#40141); MXFP4 MoE dequant moves to a JIT kernel as the triton_kernels backend is removed (#49661/#49660); MiniMax-H3-Image diffusion follow-ups (#49574) and FA4-graph replay for SM12x continue.
*Why it matters:* spec-decode depth (DFlash2 drafts + acceptance estimators) is where SGLang is pulling away on agentic serving, and FP8 KV for Gemma-4's big head-dims unlocks longer contexts on Hopper.
*Local inference:* SM12x (consumer Blackwell) keeps getting first-class attention; K3.1 drafts pair with the Unsloth/vLLM quant ladder from last week.
Source: GitHub merges via search — https://github.com/sgl-project/sglang (PRs #49621, #49478, #49458, #46965, #44588)

**Community bench: 763B DeepSeek-V4.1-Flash 2-bit at 50 tok/s on one 32GB card** — event date November 11 (post, early ET)
u/Ryyn_ measured DeepSeek-V4.1-Flash-1017 as ISTA-DASLab's GSQ-RCO IQ2_XXS (232GiB on disk) on a single RTX PRO 4500 Blackwell (32GB) + 128GB DDR5 + Ryzen 9 9950X3D: 50 tok/s decode, with top-1 agreement vs BF16 at 83.5% — quantifying "what the 2-bit costs." Note the ongoing reproducibility dispute: Unsloth has been unable to reproduce ISTA-DASLab's GSQ-RCO benchmarks (Nov 7), and several engine devs switched bases to Unsloth quants.
*Why it matters:* the clearest published cost/benefit datapoint yet for 2-bit expert-heavy MoE on a single prosumer card.
*Local inference:* 32GB card + 128GB RAM class (PRO 4500/6000, or 5090+DDR5 rigs) runs the 763B MoE at usable speed — with quality caveats attached.
Source: r/LocalLLaMA (u/Ryyn_-) — https://www.reddit.com/r/LocalLLaMA/comments/1xtbfnx/763b_deepseek_v41_flash_on_one_32_gb_card_50_toks/

**Researcher claims 27B pretraining on a single 24GB RTX 3090** — event date November 11 (post, ~07:00 ET)
u/oguzeldereli published code, a paper and a repo claiming to train your own LLMs (a 27B model) on a 24GB 3090 with a memory-efficiency method, caveat included: "you would have to go through hundreds of billions of tokens for it to be good"; existing-model conversion (40.4GB Qwen-Flash-Next GGUF) is in progress. Author-reported; not independently replicated.
*Why it matters:* if the method holds, from-scratch training escapes CUDA-cluster gatekeeping — the community has been primed by last week's $0 collaboratively-trained 1B (Crowd-v1).
*Local inference:* one Volta-era 24GB card as a pretraining device — verify the harness before believing the curve.
Source: r/LocalLLaMA (u/oguzeldereli) — https://www.reddit.com/r/LocalLLaMA/comments/1xtgnh9/you_can_now_train_your_own_llms_with_less_memory/

---

## Just missed the window (footnoted once, will not repeat)

- **unsloth/MiniMax-H3-Image-FP8** — day-0 FP8/INT8 quants of Monday's H3-Image open-weight image model, created Nov 10 17:17 UTC (~12:17 ET, before this window, unreported by any run) — https://huggingface.co/unsloth/MiniMax-H3-Image-FP8
- **IFM/PAN2** — 29B-parameter Apache-2.0 general-purpose world model (image+text+action → up to 20s predicted video; Wan 2.2 VAE; weights Nov 9 ~21:52 UTC); community flags "needs quantization/pruning to be usable on a 24GB GPU" — https://huggingface.co/IFM/PAN2
- **ComfyUI v0.45.0** — released Nov 10 21:42 UTC (16:42 ET, missed by the failed 17:45 run and its manual catch-up): MiniMax-H3-Image support (kijai), ByteDance Seedream 5.5, Breeze TTS 2 nodes — https://github.com/Comfy-Org/ComfyUI/releases/tag/v0.45.0
- **Zyphra/ZONOS3-Mini** — Apache-2.0 100M-active-param real-time TTS with voice cloning, ships GGUFs + WebGPU/browser web shards (repo Nov 9, card updated Nov 10) — https://huggingface.co/Zyphra/ZONOS3-Mini

## Coverage notes

- **Verified count:** 13 reportable items (6 leading + 7 also-noted), each fetched with in-window page timestamps or exact GitHub/HF UTC metadata; plus 4 "just missed the window" footnotes.
- **Ledger dedupe:** 16 candidates suppressed as already reported — incl. the Zen 6 architecture detail (reported Nov 10 20:08), Phoronix EPYC 9686F Linux benchmarks (chip reported Nov 10 12:45; bench article Nov 10), Ollama v0.40.17-rc0, Gork-27b + Kvark i1 ladders, 17btest-nov6, Qwen3-TTS-Sam, SenseNova-U1.5 terrarium demo, Bielik-11B re-post, Deletion-Tuning re-post, Apple/Claris Harbor re-post, llama.cpp FP8-KV rL post (all previously ledgered), plus two stale Nov 9–10 items (NVIDIA overseas cloud-GPU rental crackdown per Tom's Hardware Nov 9; Hanselman gputaskman, repo Nov 3, coverage Nov 10) kept out without footnotes.
- **Dead ends / limitations:** VideoCardz HP ZGX Fury article URL never surfaced via RSS or extraction (two attempts) — cited via the confirmed homepage placement plus fetched Notebookcheck/HP/B&H; r/LocalLLaMA anonymous feed throttled (no scores; five threads read); Microsoft Research "<10-dormant-neurons backdoor" rL thread could not be date-anchored inside the window and was dropped; NVIDIA DGX Spark forum's "SGLang+NVFP4 one-command setup" Nov 11 topic not fetched; HN river in-window yielded only the dubious "SPECTRA 3x compression" post (unverified claim, excluded); TensorRT-LLM and Ollama had no in-window releases.
- **Indexes checked:** ServeTheHome (nothing newer than Nov 10 EPYC wave), The Register (Nov 11 items all out-of-scope: OpenAI kill switch, Cisco 100G, Google XSE), Tom's Hardware, VideoCardz RSS, HF trending/API, GitHub release+merge rivers (llama.cpp/vLLM/SGLang/Ollama/TensorRT-LLM), HN Algolia, r/LocalLLaMA.
