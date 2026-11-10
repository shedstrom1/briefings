# AI Hardware & Local Models Brief — 2026-11-10 12:45 ET

**Window:** 2026-11-10 07:45 EST → 2026-11-10 12:45 EST (previous fire → now; 12:45–17:45 UTC). New-items-only; the event date anchors, not article freshness.

**TL;DR**
- **MiniMax released MiniMax-H3-Image open weights** — a 33.1B-parameter, 6-step (no-CFG) diffusion transformer that renders ~16-megapixel images natively, announced 09:15 EST with the HF repo public within the hour. Weights are 68.65 GB BF16 plus a 66.7 GB H3 text encoder — server-class, not a 16 GB-card model, and the community license excludes the US, EU, UK and South Korea.
- **AMD's EPYC 9006 "Venice" / Zen 6 server platform went available** at 12:00 EST with OEM/ODM systems shipping; the flagship 256-core EPYC 9996 lists at $14,904 and the high-frequency 96-core 9686F shipped at a confirmed 5.3 GHz boost (up from the 5.0 GHz originally disclosed).
- **llama.cpp merged FP8 (OCP E4M3) KV-cache support** after two months of review — a real memory-footprint lever for long-context local serving, with optional runtime static per-tensor scales.
- **AMD pushed MEXT predictive memory** (flash-as-DRAM tiering for EPYC) with Linux support but no source — directly aimed at the memory crunch that is pricing local builders out of RAM.
- Quiet but consequential background: SGLang landed MiniMax-H3-Image serving support and AMD gfx1250 W4A8 MoE kernels in the same five-hour band; a single RTX 3080 Ti was shown holding ~100 tok/s at 260 W — 42% below its 450 W peak.

---

## AI hardware and silicon

### AMD EPYC 9006 "Venice" (Zen 6) SP7 goes available; 256-core flagship at $14,904, 9686F boosts to 5.3 GHz
**Event date: 2026-11-10 (12:00 EST embargo lift; systems shipping today from OEM/ODM partners).**
AMD's 6th-gen EPYC reached actual hardware availability today after the July soft-launch at Advancing AI. The SP7 stack tops out at the EPYC 9996 — 256 Zen 6C cores / 512 threads, 2.55 GHz base / 4.1 GHz boost, 1,024 MB L3, 600 W default (configurable down to 400 W), 96 lanes of PCIe 6.0, 16 channels of DDR5-8000 or MRDIMM-12800, up to 8 TB — at $14,904 per 1KU. The notable spec change at availability is the high-frequency EPYC 9686F: originally disclosed at 5.0 GHz boost, it ships at 5.3 GHz on 96 cores / 192 threads (500 W). AMD's own briefing numbers claim 2.2x the HPC performance of Intel's Xeon 6980P (up to 3.2x on NAMD), and vs. last-gen Turin: 2x integer, 2.4x floating-point, 2.7x memory bandwidth, 1.3x matrix compute. SP8 arrives H1 2027; 9006X and the LPDDR5X "Verano" parts land H2 2027.
*Why it matters:* this is the first shipping Zen 6 silicon and it is a server part aimed squarely at agentic-inference host nodes — the CPU tier that feeds GPUs and the CPU-only inference tier. The MRDIMM-12800 / 16-channel bandwidth jump is the number to watch for anyone doing large MoE expert offload to host memory; CPU inference on this class of box stops being a novelty. It also means the desktop/mobile Zen 6 parts inherit a validated microarchitecture.
*Local inference:* not a workstation product — $14,904 flagship, 2P/SP7, 600 W — but its memory-subsystem gains (8 TB, MRDIMM-12800) are exactly the tier where multi-hundred-GB MoE weights live on host RAM, and today's benchmark embargo lifted on both the 9996 and the 9686F.
Source: AMD Showcases 6th Gen AMD EPYC Processors to Lead Next Era of AI and Enterprise Computing — ir.amd.com, Nov 10 2026 12:00 pm EST — https://ir.amd.com/news-events/press-releases
Source: AMD EPYC 9996 Benchmarks Show Unparalleled Performance With 256 Zen 6C Cores — Phoronix, Nov 10 2026 12:00 PM EST — https://www.phoronix.com/review/amd-epyc-9996
Source: AMD EPYC 9006 Results Are Here and AMD Delivers a Knockout Part Deux — ServeTheHome (Patrick Kennedy), Nov 10 2026 — https://www.servethehome.com/amd-epyc-9006-results-are-here-and-amd-delivers-a-knockout-part-deux/

### AMD markets MEXT predictive memory for EPYC — Linux driver, closed source
**Event date: 2026-11-10 (Phoronix, 12:00 PM EST).**
AMD used the EPYC availability briefings to push MEXT, the AI-driven predictive-memory technology it acquired this summer, which makes NVMe flash behave as an extension of DRAM. On EPYC servers it performs flash/DRAM tiering driven by a predictive AI engine that decides what stays in RAM; supported distros include RHEL, Rocky, AlmaLinux, Oracle Linux, SUSE, Debian and Ubuntu. Phoronix's point of note: the Linux driver exists but MEXT is one of the few AMD offerings that is *not* open-source, breaking from AMD's usual open-ecosystem posture.
*Why it matters:* during a memory crisis where DDR5 capacity and price are the binding constraint on self-hosted inference, "let an MLP predict which pages live in RAM" is the vendor answer, and it is the same technique the local-MoE community is building by hand (expert page-cache streaming, SSD-resident expert tiers). A closed source stack here means the community will keep rolling its own.
*Local inference:* targets EPYC + NVMe tiers — the architecture behind every "48 GB GPU + 128 GB DDR5 + NVMe expert stream" rig. AMD's positioning validates host-memory/flash tiering as the mainstream way to run big MoE on cheap parts.
Source: AMD Talks Up Their MEXT Software Amid RAM Crisis — Linux Support But Not Open-Source — Phoronix, Nov 10 2026 12:00 PM EST — https://www.phoronix.com/recent-linux-news
Source: AMD MEXT Predictive Memory Software — AMD product page (OS support, mextMemory config) — https://www.amd.com/en/products/software/mext.html

### AMD confirms ACE on 7th-gen EPYC "Florence"; 8th-gen is "Ravenna"
**Event date: 2026-11-10 (12:00 PM EST embargo window).**
Alongside SP7 availability AMD teases its roadmap: 7th-gen EPYC "Florence" brings ACE (AI Compute Extensions) and 8th-gen is named "Ravenna".
*Why it matters:* x86-native matrix ISA extensions on the server roadmap are what eventually make meaningful CPU inference (and CPU/GPU hybrid serving) viable without bolt-on accelerators — the same x86 Ecosystem Advisory Group that posted the ACE v2 / AVX10.3 specs this morning.
*Local inference:* long-horizon signal for CPU-side inference and for MoE gate/indexer offload; nothing to buy today.
Source: AMD Confirms ACE With 7th Gen EPYC "Florence" While 8th Gen EPYC Is "Ravenna" — Phoronix, Nov 10 2026 12:00 PM EST — https://www.phoronix.com/recent-linux-news

### AMD EPYC Embedded 9006 (SP7) announced
**Event date: 2026-11-10 (12:00 PM EST).**
Announced in the same batch as desktop-socket SP7 availability: an EPYC Embedded 9006 Series on SP7.
*Why it matters:* embedded SP7 is the part class that shows up inside PCIe inference appliances, rugged edge inference boxes and long-lifecycle homelab/server purchases — the Zen 6 + MRDIMM + PCIe 6 platform outside the datacentre.
*Local inference:* the realistic vehicle for a future 16-channel, high-memory inference mini-PC or industrial AI box.
Source: AMD EPYC Embedded 9006 Series SP7 Announced — Phoronix, Nov 10 2026 12:00 PM EST — https://www.phoronix.com/recent-linux-news

---

## Model releases

### MiniMax releases MiniMax-H3-Image open weights: 33.1B DiT, six sampling steps, native ~16-megapixel output
**Event date: 2026-11-10 (announced on X at 14:15 UTC / 09:15 EST; the Hugging Face repo loaded publicly between 14:37 and 15:07 UTC).**
MiniMax released the weights for MiniMax-H3-Image, an image generation + editing model built on its H3 video family. The diffusion transformer is a dense single-stream 33.1B-parameter model — 50 layers, hidden size 5376, 56 attention heads, FFN 14336, 3D multimodal RoPE, BF16 weights with FP32 embedding/timestep/output layers — distilled to 6 sampling steps (6 NFEs) with no classifier-free guidance, and it produces images natively at about 16 MP (4096×4096 up to 6272×2688) without an upscaler, with an effective working range of ~4 MP to 20 MP. Editing accepts 1–16 reference images. The DiT reads layer-50 hidden states of the full Qwen3-VL text encoder from MiniMax-H3 (66.7 GB); the image VAE is 2.34 GB FP32 with 16× downsampling and 24 latent channels. An optional prompt expander, MiniMax-H3-Image-PE (17.9 GB NVFP4, fine-tuned from Qwen3.8-27B), runs locally. Serving recipes are published for SGLang, vLLM-Omni, diffusers and ComfyUI, including single-GPU `vllm serve MiniMaxAI/MiniMax-H3-Image --omni` and four-GPU Ulysses sequence parallelism; MiniMax names H200, RTX PRO 6000 Blackwell and AMD Instinct MI350X as supported accelerators. Claimed speed is 4K×4K in 10–20 seconds — with no hardware stated and no benchmark published. The MiniMax-H3-Image Community License Agreement is dated the same day and excludes the US, EU, UK and South Korea, requires written authorization above $1M annualized revenue or for hosted serving, and forbids using outputs to train other models.
*Why it matters:* this is the rare open-weight image model that skips the upscale pipeline entirely and commits to a six-step, guidance-free sampler — the cheapest possible path to high-resolution output, and a direct challenge to closed editors on instruction following (up to 16 references). The territory carve-out on the license, though, means US/EU/UK builders cannot legally self-host it without a separate agreement, which is the story most coverage will skip.
*Local inference:* 68.65 GB BF16 DiT + 66.7 GB text encoder + 2.34 GB VAE rules out any single card short of ~144 GB; realistic local targets are multi-GPU H200/MI350X/RTX PRO 6000-class boxes, or the community quant tiers landing within hours (see below) that pull the footprint toward consumer territory. Community tiers from PotatoForge (W4A4) and WaveCut (pruned) were uploaded inside this window.
Source: MiniMaxAI/MiniMax-H3-Image — Hugging Face model card (architecture, Model Zoo sizes, deployment, license) — https://huggingface.co/MiniMaxAI/MiniMax-H3-Image
Source: MiniMax H3-Image: 16M-Pixel Images, Prices, Open Weights — CellCog (Nitish Garg), Nov 10 2026, published 14:37 UTC / updated 15:23 UTC — https://cellcog.ai/blog/minimax-h3-image/
Source: MiniMax H3-Image Released, an Open-Weight Image Model built on H3 — r/LocalLLaMA, Nov 10 2026 14:18 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xso1ie/minimax_h3image_released_an_openweight_image/

---

## Community spins and quantizations

### mradermacher ships the full GGUF ladder for Arete-27B-Unlocked-Uncensored-Heretic
**Event date: 2026-11-10 (16:38 UTC plain GGUF, 17:36 UTC i1 imatrix).**
Day-0 quantization ladder of SolsticeAI's unlocked/uncensored heretic fine-tune of Nous Research's Arete-27B (itself the Western-values SFT of Qwen3.8-27B), including an imatrix `i1` tier set.
*Why it matters:* Arete-27B was one of the most-pulled Qwen-27B-class spins of the last cycle; a decensored variant with a proper quant ladder is the version most local users will actually run.
*Local inference:* 27B dense — Q4_K_M class lands around 16–18 GB (fits the RTX 5060 Ti 16 GB tier tight, comfortable on 24 GB), Q8 ~29 GB for 32–48 GB cards.
Source: mradermacher/Arete-27B-Unlocked-Uncensored-Heretic-GGUF — Hugging Face, created Nov 10 2026 16:38 UTC — https://huggingface.co/mradermacher/Arete-27B-Unlocked-Uncensored-Heretic-GGUF
Source: mradermacher/Arete-27B-Unlocked-Uncensored-Heretic-i1-GGUF — Hugging Face, created Nov 10 2026 17:36 UTC — https://huggingface.co/mradermacher/Arete-27B-Unlocked-Uncensored-Heretic-i1-GGUF

### Community quant tiers for MiniMax-H3-Image land within hours of the weights
**Event date: 2026-11-10 (PotatoForge W4A4 17:14 UTC; WaveCut pruned 17:37 UTC).**
Two independent reductions of the new image model: PotatoForge's W4A4 quantization of the Comfy-Org MiniMax-H3-Image checkpoint (4-bit weights *and* activations, tagged for ComfyUI), and WaveCut's pruned variant of MiniMaxAI/MiniMax-H3-Image.
*Why it matters:* W4A4 on a 33.1B DiT is the aggressive end of what people will attempt on a 68.65 GB BF16 model, and the speed of turnaround (under three hours from public weights) shows how fast the image-model quant ecosystem now moves. Both are unvalidated for quality — no published FID/CLIP numbers.
*Local inference:* aimed at making a 16 MP DiT fit single-GPU territory that the BF16 checkpoint cannot approach; still needs the 66.7 GB H3 text encoder unless a substitute encoder is swapped in.
Source: PotatoForge/Minimax-H3-Image-W4A4 — Hugging Face, created Nov 10 2026 17:14 UTC — https://huggingface.co/PotatoForge/Minimax-H3-Image-W4A4
Source: WaveCut/MiniMax-H3-Image-Pruned — Hugging Face, created Nov 10 2026 17:37 UTC — https://huggingface.co/WaveCut/MiniMax-H3-Image-Pruned

### MLX "OQ" quant wave for Comet 60B and Ornith-1.5-35B-A3B
**Event date: 2026-11-10 (queuedog Comet 60B oQ4e-mtp 17:41 UTC; funnygeeker Ornith-1.5-35B-A3B oQ5e/oQ6e-mtp 17:41–17:42 UTC).**
Apple-Silicon MLX quant tiers on two current community favourites: queuedog's 4-bit `oQ4e-mtp` of Multiverse Computing's Comet 60B-A12B (nemotron_h_omni architecture, MTP draft head retained) and funnygeeker's `oQ5e`/`oQ6e-mtp` set of Ornith-1.5-35B-A3B, including an abliterated Huihui variant and an FP16 MTP tier.
*Why it matters:* keeping the MTP head inside the quant means Apple Silicon users get multi-token-prediction drafting, not just a smaller file — the same feature gap that has dogged MLX quants.
*Local inference:* MLX/Apple Silicon only. A 60B-A12B at 4-bit (~34 GB) needs a 36–48 GB M-series machine and is comfortable on 64–128 GB; the 35B-A3B set is a 24–48 GB class model.
Source: queuedog/Comet_60B_A12B_BF16-oQ4e-mtp — Hugging Face, created Nov 10 2026 17:41 UTC — https://huggingface.co/queuedog/Comet_60B_A12B_BF16-oQ4e-mtp
Source: funnygeeker/Ornith-1.5-35B-A3B-oQ5e-mtp — Hugging Face, created Nov 10 2026 17:41 UTC — https://huggingface.co/funnygeeker/Ornith-1.5-35B-A3B-oQ5e-mtp

### Austriani publishes ik_llama.cpp trellis quants of WaifuGemma4-26B-A4B
**Event date: 2026-11-10 (17:40 UTC).**
An `ik_llama.cpp` ladder (IQK trellis quantization, maxoptima search, v3 imatrix calibration) for hiwaifu-research's WaifuGemma4-26B-A4B, a Gemma-4-26B-A4B MoE roleplay fine-tune.
*Why it matters:* ik_llama.cpp's trellis/IQK tiers routinely beat stock llama.cpp at the same bits-per-weight on MoE — worth watching whenever a 26B-A4B gets a dedicated trellis pass rather than a stock re-quant.
*Local inference:* 26B-A4B MoE — Q4-class trellis sits near 14–16 GB, i.e. the RTX 5060 Ti 16 GB / RTX 4070-class tier, with expert offload making 8–12 GB cards workable.
Source: Austriani/WaifuGemma4-26B-A4B-V1-ik_llama.cpp — Hugging Face, created Nov 10 2026 17:40 UTC — https://huggingface.co/Austriani/WaifuGemma4-26B-A4B-V1-ik_llama.cpp

### sabda-ai ships Gemma-4 E2B/E4B on-device speech-recognition GGUFs
**Event date: 2026-11-10 (17:16 and 17:18 UTC).**
GGUF releases of Gemma-4-E2B-it and Gemma-4-E4B-it tuned for speech recognition and tagged `on-device` / `llama.cpp`.
*Why it matters:* ASR inside the GGUF ecosystem (rather than whisper.cpp's separate path) means one runtime and one VRAM budget for transcribe-plus-reason pipelines on phones and single-board hardware.
*Local inference:* E2B/E4B are sub-4 GB at 4-bit — phone, Raspberry Pi 5 and integrated-GPU territory, not discrete-GPU only.
Source: sabda-ai/gemma-4-E4B-sabda — Hugging Face, created Nov 10 2026 17:18 UTC — https://huggingface.co/sabda-ai/gemma-4-E4B-sabda
Source: sabda-ai/gemma-4-E2B-sabda — Hugging Face, created Nov 10 2026 17:16 UTC — https://huggingface.co/sabda-ai/gemma-4-E2B-sabda

### ConeML: a from-scratch 348M-active MoE that beats Qwen2.5-1.5B on deep fact chains
**Event date: 2026-11-10 (r/LocalLLaMA post, 17:11 UTC).**
An independently trained sparse MoE with 1.17B stored parameters and ~348M active per token, trained from scratch on roughly 11B tokens including instruction tuning. On a shuffled relational-fact-chain task (512 questions per depth, greedy, 16-token answers) it reports 487/512 at depth 1 falling to 74/512 at depth 24, against Qwen2.5-1.5B's 481→47, SmolLM2-1.7B's 426→21, Llama-3.2-1B's 267→13 and Gemma-3-1B's 343→19. The author concedes it is not a general assistant and that overall scores are weak (13.8 on another suite), and commenters flagged the post's AI-generated styling.
*Why it matters:* a hobby-scale training run beating 1.5B-class models by ~5x at depth 12 is the recurring community result that sparse routing plus targeted data beats parameter count on structured recall — and a useful stress-test dataset, since the author published both the GGUF and the eval set.
*Local inference:* 1.17B stored parameters — runs in a couple of GB of RAM; CPU-only or any GPU, no VRAM constraint.
Source: I trained a 348M-active MoE on ~11B tokens… GGUF + eval set — r/LocalLLaMA (u/Significant-Serve-58), Nov 10 2026 17:11 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xssj9q/i_trained_a_348mactive_moe_on_11b_tokens_it/

---

## Inference software and benchmarks

### llama.cpp merges FP8 (OCP E4M3) KV-cache support
**Event date: 2026-11-10 (PR #28392 merged 16:08 UTC / 11:08 EST).**
ORippler's PR landed after roughly two months of review: OCP-FP8-based KV caches, with runtime static per-tensor scales supported but optional. Scaling is implemented at the graph level rather than inside `ggml_flash_attn_ext`, matching the existing NVFP4/FP8 ownership model so FP8 logic can be shared across flash-attention and standard attention layers. It ships end-to-end tests for conversion/loading, context shifts and FP8-cache correctness, enables Gemma 4 Assistant with FP8 by storing and fetching scale pointers, allows BF16 scales, and multi-GPU/`--kv-ffload=false` tests are excluded for FP8 for now. Non-unit-scaled FP8 KV already exists in checkpoints such as nvidia/Mistral-Medium-3.5-128B-NVFP4.
*Why it matters:* KV cache, not weights, is what caps context length on a fixed card. Native FP8 KV in the most-used local runtime halves KV memory versus BF16 for the models that ship scales, which converts directly into usable context on 16 GB and 32 GB cards — and it puts pressure on the manual KV-quant experiments the community has been running.
*Local inference:* immediate relevance at the 16 GB (RTX 5060 Ti class) to 32 GB tier, where a 200K+ context budget is otherwise impossible; CUDA is the primary path today, with OpenVINO excluded from the FP8 KV tests.
Source: Add FP8 KV-cache support by ORippler · PR #28392 (merged 16:08 UTC Nov 10) — GitHub, Nov 10 2026 — https://github.com/ggml-org/llama.cpp/pull/28392

### SGLang's five-hour merge wave: MiniMax-H3-Image serving, gfx1250 mixed W4A8 MoE, SM120/SM12x FP8 fast paths
**Event date: 2026-11-10 (19 PRs merged 12:57–17:33 UTC).**
Highlights from the window: #49552 adds diffusion support for MiniMaxAI/MiniMax-H3-Image (day-0, alongside the weight release); #47398 lands mixed W4A8 MoE for gpt-oss on gfx1250 (Gluon below a token-count threshold, FlyDSL above); #45262 and #45264 add FlashInfer b12x block-FP8-as-MXFP8 linears on SM12x and a BLOCK_K=32 `hc_mix` config on SM120; #48928 switches DeepSeek-V4.1-Flash prompt encoding to segmented fastokens; #42855 adds an opt-in fused int8 Markov walk kernel for batch ≤64 on Hopper; #49263 pauses automatic GC during CUDA graph capture; and a nine-PR cluster reworks unified-memory capacity accounting in `mem_cache`.
*Why it matters:* the gfx1250 W4A8 MoE work and the SM12x FP8 configs are the two changes that move tokens/sec on hardware people actually own, and the unified-memory rework is groundwork for expert/attention pages being placed across device boundaries.
*Local inference:* SM120/SM12x coverage targets Blackwell consumer and RTX PRO cards; gfx1250 is the Radeon AI Pro tier. Hopper-only items are datacentre-relevant.
Source: sgl-project/sglang merged pull requests, merged:2026-11-10T12:45Z..2026-11-10T17:45Z — GitHub search API, Nov 10 2026 — https://github.com/sgl-project/sglang/pulls?q=is%3Apr+is%3Amerged

### vLLM merge wave: extensible-KV re-prepare for Kimi-K3, Humming MoE intrinsics, INT8 MoE scale indexing fix
**Event date: 2026-11-10 (11 PRs merged 13:39–17:20 UTC).**
In-window: #66370 re-prepares model state after an extensible KV-cache extension (the Kimi-K3 AMD specialized-decode path), #65975 uses fast SiLU intrinsics in Humming MoE activations, #65177 fixes INT8 MoE per-channel weight-scale indexing relative to activation quantization (#58570), #59025 registers the cuDNN backend for FlashInfer BF16 matmul, #65217 adds `prefill_context_kv_only` for prefill instances in MRV2 speculative decoding, plus ROCm/CI and XPU CI housekeeping.
*Why it matters:* the INT8 MoE scale-indexing fix is a correctness bug with real quality implications for quantized MoE serving, and the extensible-KV fix is what lets KV pools grow dynamically without desynchronising the decode state.
*Local inference:* the extensible-KV sizing and INT8-MoE fixes apply directly to 32 GB+ workstation cards and ROCm rigs (Radeon AI Pro / Instinct) running MoE models.
Source: vllm-project/vllm merged pull requests, merged:2026-11-10T12:45Z..2026-11-10T17:45Z — GitHub search API, Nov 10 2026 — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged

---

## Also noted

- **llama.cpp b12246–b12250 released inside the window** (13:09, 15:01, 16:08, 16:57, 17:13 UTC) — the FP8 KV-cache merge plus backend-scheduler reuse when sched_params are compatible (#32009), sampling-chain output marking to prevent aliasing (#32015), MUL_MAT src1 precision gating (#31896) and CUDA cleanup. — https://github.com/ggml-org/llama.cpp/releases
- **42% GPU power reduction with zero measured performance loss (author-reported).** u/Asthenia5 stepped an RTX 3080 Ti's power limit from 450 W to 260 W while running Qwen3.5-9B Q4_K_M and saw token rate hold at ~100 tok/s, only degrading to ~80 tok/s at 220 W; commenters attribute the flat region to memory bandwidth (912 GB/s over a 5.5 GB GGUF ⇒ ~165 tok/s theoretical ceiling) and to NVIDIA's half-rate matrix cap on consumer parts. — https://www.reddit.com/r/LocalLLaMA/comments/1xsp309/42_gpu_power_reduction_with_zero_performance_loss/
- **TTS benchmark of 91 open TTS models and 28 paid APIs on identical prompts** posted to r/LocalLLaMA at 16:09 UTC — a broad, single-prompt-set comparison for anyone picking a local voice stack. — https://www.reddit.com/r/LocalLLaMA/comments/1xsqv86/tts_bench_91_open_tts_model/
- **Startlux 4B/9B "Instant" (System1/Jev) Q4_0 running on four Raspberry Pi 5 8GB boards** (15:22 UTC) — small-model serving spread across SBC-class hardware. — https://www.reddit.com/r/LocalLLaMA/comments/1xspmhe/startlux_4b_9b_instant_syst/
- **Cross-language agentic coding benchmark of Qwen3.6-27B, Qwen3.8-27B and GLM-5.3 Flash** on real tasks in Elixir, OCaml, Haskell and ten other ecosystems (14:48 UTC) — a useful long-tail-language check against the usual Python/JS leaderboards. — https://www.reddit.com/r/LocalLLaMA/comments/1xsorsx/we_tested_qwen36_27b_qwen38/
- **Minor in-window quant uploads:** mradermacher's i1 ladder for DavidAU's Qwen3.8-31B-Looping-Transformer (looping-transformer experiment), plus MonadGPT-GGUF (en/fr/la philosophical model), gemma-4-12B-it-SOMPOA-heresy-i1, Sirigannada-2B-base-GGUF and shennong-1.5b-sft-GGUF. — https://huggingface.co/mradermacher

## Just missed the window
- **speakleash/Bielik-11B-v3.1-Instruct** and its day-0 quant family (GGUF, AWQ, NVFP4, NVFP4A16, MLX-4bit, MLX-8bit — repos created 01:58–07:12 UTC, base card last updated 11:21 UTC, all before the 07:45 EST fire; the r/LocalLLaMA post at 16:30 UTC is re-coverage). Apache-2.0, Llama-architecture 11B tuned for Polish and ~40 other languages; ~22–25 GB at BF16, so a 24–32 GB card or ~16 GB at Q4. — https://huggingface.co/speakleash/Bielik-11B-v3.1-Instruct

## Coverage notes
**Verified: 16 in-window events** (4 lead items, 8 sectioned items, 6 also-noted — counting unit: distinct underlying events, one entry each; the AMD EPYC entry bundles the SP7 availability announcement and the 9686F clock-bump milestone of the same launch, and the two AMD roadmap/embedded one-liners are counted separately because AMD disclosed them as separate announcements). One "just missed the window" footnote, used for the first and only time.

**Ledger dedupe:** 11 candidates suppressed as already reported — Kvark Sparkle 1.1 (reported this morning 07:45), DavidAU Qwen3.8-40B-Ultimate-Heretic-Turbo-Brilliance-50x-AGI-NEO-GGUF (07:45), llama.cpp b12242–b12244 (07:45), the vLLM 55-PR and SGLang 46-PR overnight waves (07:45), the 07:45 mradermacher/bartowski quant ladder items (Qwen3.8-4B-Distill-ARA-Abliterated, LiquidCode-2.5-24B-A2B-GGUF, jmc0_Rein-NPC-4B-GGUF), the two previously-footnoted "missed:" entries resurfaced in-window (Multiverse Comet 60B stock-llama.cpp GGUFs, ReadyArt MiniMax-M3 jablit ladder), and the Day-0 vLLM "MiniMax-H3" tweet which belongs to the H3-Image event already counted. No repeat "Just missed" footnotes were issued.

**Out of scope, deliberately excluded:** Gemini 4 Carbon availability (cloud API), Anthropic IPO valuation reporting, NVIDIA board investigation, KORA OS, Blender 5.3, .NET 11, GTA VI coverage on VideoCardz.

**Dead ends / limitations:** (1) direct `web_extract` of reddit.com thread URLs returns 403 — used the reddit-reading Atom route; the AMD MEXT thread body came back empty (link-only post), so that item rests on the Phoronix article. (2) The AMD IR press-release deep link 404'd; cited the IR press-release index page, which carries the exact "Nov 10, 2026 12:00 pm EST" timestamp. (3) Phoronix does not show minutes on every entry — the EPYC 9686F benchmark article is listed on the Nov 10 index with its timestamp collapsed into the 12:00 PM EST batch, so I treated it as part of the EPYC launch item rather than a separate event. (4) Search backend threw repeated DuckDuckGo timeouts; several angles went through the fallback provider only. (5) No independent benchmark exists yet for MiniMax-H3-Image's 10–20 s claim, and MiniMax names no test hardware — both flagged inline. (6) Hugging Face's `createdAt` for MiniMaxAI/MiniMax-H3-Image is Nov 3; the repo was not publicly readable until ~15:07 UTC today, and the anchor used here is MiniMax's own 14:15 UTC announcement. (7) ServeTheHome published nothing else AI-relevant in this five-hour band; its EPYC piece carries a date but no minute.
