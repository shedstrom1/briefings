# AI Hardware & Local Models Brief — Thanksgiving edition

Window: 2026-11-24 07:45 → 12:45 EST (previous fire: 07:45). New items only.

## TL;DR
- ServeTheHome published its full review of the ASUS Turbo Radeon AI PRO R9700 — a sub-$2,000 32GB RDNA4 inference card purpose-built for stacking up to four in one workstation.
- Poros, a new Apache-2.0 streaming LoRA fine-tuning engine, hit Hacker News: 72B Qwen fine-tuning in 13.5 GiB of VRAM on a single RTX 4090, or from a USB4 SSD on a 16GB M4 Mac.
- MAXSUN opened an official Newegg brand store in the US — and the only GPU listed so far is the 32GB Intel Arc Pro B70 Turbo at $2,419.
- AMD quietly capped max core voltage to 1.4V on Ryzen 8000 APUs (the Zen4+XDNA "AI PC" Phoenix silicon) via AGESA 1.3.0.1f.
- llama.cpp shipped b12676 plus a 16-PR wave (CUDA attention fusion, SVE/ARMv8 CPU kernels, MCP multimodal tool results); vLLM merged validated Arc Pro B60/B70 recipes.

## AI hardware and silicon

### ServeTheHome reviews the ASUS Turbo Radeon AI PRO R9700: 32GB of GDDR6 for under $2,000
Event date: 2026-11-24. Ryan Smith's review of AMD's flagship workstation inference card landed at 11:00 EST. The ASUS Turbo variant is a dual-slot blower built on the full Navi 48 die: 64 CUs (4,096 shaders, 128 AI accelerators) at 2.92 GHz, 382.7 dense FP8 TFLOPS, 47.8 FP32 TFLOPS, 32GB of GDDR6 (16×2Gb clamshell) on a 256-bit bus at 20Gbps for 640 GB/s, 64MB Infinity Cache, 300W total board power, PCIe Gen5 x16. Design details matter for multi-GPU local rigs: strict PCIe CEM dimensions (10.5" long), rear-mounted 12V-2×6 power, open-backed cooling, and 4-card support in one chassis. Smith flags two trade-offs: the R9700 actually backs off memory versus last gen's 48GB Radeon Pro W7900, and its ECC is soft, Linux-only, and off by default.
**Why it matters:** This is AMD's clearest play at the "affordable big-VRAM local inference" niche — sub-$2,000 per 32GB card, where the RTX PRO Blackwell equivalents cost multiples of that. The FP8 support that RDNA4 finally adds, combined with a community that's already squeezed the same GPU class hard (see below), makes a 4×R9700 box the obvious 2026 homelab counter-programming to Blackwell.
**Local inference:** 32GB per card targets the 27B–35B class at Q5–Q8, and community rigs have already demonstrated the GPU on both ends: an Arc Pro B70 (same-market Xe2 rival) runs Qwen3.8-27B Q6 at ~52–56 tok/s decode, and a 2×R9700 Rust server hit 316 tok/s peak decode on DeepSeek-V4.1-Flash NVFP4. Four of these is a ~128GB pool for 122B-class MoE in FP8 territory.
Source: ServeTheHome, "ASUS Turbo Radeon AI PRO R9700 Review," 2026-11-24 16:00 UTC (11:00 EST) — https://www.servethehome.com/asus-turbo-radeon-pro-ai-r9700-review/
Source (spec sheet): ASUS, "TURBO-AI-PRO-R9700-32G" product page — https://www.asus.com/us/motherboards-components/graphics-cards/turbo/turbo-ai-pro-r9700-32g/
Context: ServeTheHome, "ASRock Radeon AI Pro R9700 review," 2026-10-26; r/LocalLLaMA whodoneit1 saber-engine 2×R9700 bench, 2026-11-06 (earlier editions).

### MAXSUN's official US store opens on Newegg — and its only GPU is a 32GB Arc Pro B70 at $2,419
Event date: 2026-11-24. VideoCardz reports MAXSUN launched an official Newegg brand store: AMD B650/B850 and Intel B460/B760/H810/Z890 motherboards from $74.99 to $239.99, but the sole graphics card listed is the MAXSUN Intel Arc Pro B70 32G Turbo (Xe2-HPG, 32GB GDDR6, PCIe 5.0, blower/vapor chamber) at $2,419. No GeForce cards are in the official store despite MAXSUN's RTX 50 lineup, only third-party marketplace listings.
**Why it matters:** Another AIB entering US retail with an inference-class 32GB card normalizes the sub-$2,500 big-VRAM niche — and does so amid a documented price surge: the Arc Pro B70's street price has more than doubled in weeks (US listings up to $2,400 vs the $1,299 MSRP). MAXSUN skipping GeForce in the official store also hints at NVIDIA distribution friction for smaller AIBs.
**Local inference:** 32GB Xe2 card for the Intel stack (SYCL/XPU). Same-day context: vLLM merged validated 8192-token context recipes for Qwen, Nemotron-3-Nano and GPT-OSS on Arc Pro B60/B70, and community benches put Qwen3.8-27B Q6 at ~52–56 tok/s decode with 223K context on a tuned B70.
Source: VideoCardz, "MAXSUN expands US availability through Newegg, lists Intel GPUs but no GeForce cards yet," 2026-11-24 14:15 UTC (09:15 EST) — https://videocardz.com/newz/maxsun-expands-us-availability-through-newegg-lists-intel-gpus-but-no-geforce-cards-yet
Source (listing): Newegg, MAXSUN Intel Arc Pro B70 32G Turbo, $2,419 — https://www.newegg.com/p/pl?d=maxsun+gpu

### AMD's new AGESA caps Ryzen 8000 APU core voltage at 1.4V
Event date: 2026-11-24. AMD has quietly shipped AGESA ComboAM5 PI 1.3.0.1f Patch A, which limits maximum Vcore to 1.4V for select Ryzen 8000-series desktop APUs (the Phoenix/Phoenix 2 Zen 4 silicon with XDNA NPUs). MSI rolled the change into MEG X870E GODLIKE and MPG X870E CARBON BIOSes dated November 17; ASUS is posting beta BIOSes across X870E down to B650, with its changelogs spelling out the "Limited Vcore to 1.4V max" restriction. AMD has not explained the cap, nor which specific SKUs are affected.
**Why it matters:** Firmware silently clipping voltage headroom on a whole processor family is the kind of surprise that hits the used/AI-PC market — Ryzen 8000G boards are a budget local-AI path precisely because the XDNA NPU and cheap DDR5 make small inference boxes, and the same chips' iGPU/NPU overclock margins are now formally trimmed. The TechPowerUp thread adds that the release also carries MSI's version of the DDR5 memory-training POST counter ASUS debuted last week.
**Local inference:** Ryzen 7 8700G/8600G-class AM5 machines (RDNA3 iGPU + XDNA NPU, 64GB RAM builds run 35B-A3B MoE quants) — buyers/overclockers should read BIOS changelogs before updating.
Source: VideoCardz, "AMD AGESA 1.3.0.1f BIOS limits Ryzen 8000 CPU core voltage to 1.4V," 2026-11-24 16:58 UTC (11:58 EST) — https://videocardz.com/newz/amd-agesa-1-3-0-1f-bios-limits-ryzen-8000-cpu-core-voltage-to-1-4v
Source (corroboration): TechPowerUp forums, "AMD AGESA 1.3.0.1f Patch Arrives to Limit Max vCore on Ryzen 8000 series," 2026-11-24 — https://www.techpowerup.com/forums/threads/amd-agesa-1-3-0-1f-patch-arrives-to-limit-max-vcore-on-ryzen-8000-series.355153/

## Model releases
No new open-weight base models were disclosed in this window. (The Tencent WePocketModel/WeMM-Ecom decision-and-embedding family was reported in this morning's 07:45 edition; its quant tiers appear below. The Shuttle NW10U Wildcat Lake mini-PC and Strata v0.2.21 were also confirmed as pre-window disclosures — see Coverage notes.)

## Community spins and quantizations

### First GGUF ladder for Radiant-Dawn-26B-A4B, a Gemma-4 any-to-any merge
Event date: 2026-11-24. bartowski published day-first GGUFs (imatrix ladder) of Sorihon/Radiant-Dawn-26B-A4B at 10:29 EST. The base is a mergekit merge of two Gemma-4-26B-A4B-class models (Erebus-RP-Engine + Gloomy-Dusk), pipeline tag any-to-any/image-text-to-text, 26.5B parameters BF16, created November 21 — the quant ladder is the in-window event.
**Why it matters:** Gemma-4-26B-A4B merges remain the community's sweet spot for MoE-on-a-laptop; day-first quants are what make a three-day-old merge usable.
**Local inference:** A4B active weights mean the Q3/Q4 tiers land around the 12–16GB VRAM class (single 5060 Ti/4070 Ti Super; comfortable at 24GB).
Source: Hugging Face, bartowski/Sorihon_Radiant-Dawn-26B-A4B-GGUF, created 2026-11-24 15:29 UTC (10:29 EST) — https://huggingface.co/bartowski/Sorihon_Radiant-Dawn-26B-A4B-GGUF
Base: https://huggingface.co/Sorihon/Radiant-Dawn-26B-A4B

### In-window quant tiers: Tencent WePocket/WeMM imatrix ladders, Unsloth bnb-4bit for Inkling 2 Mini
Event date: 2026-11-24. New tiers on today's reported families: bartowski posted the GGUF ladder of Tencent's WeMM-Ecom-0.8B e-commerce VLM (09:03 EST); Unsloth added a single-file unsloth-bnb-4bit of Inkling-2-Mini for the TRL/training crowd (08:48 EST); and mradermacher rolled out i1 imatrix tiers of WePocketModel1.0-4B (08:01 EST), EliasAI-Micro-v1.0 German Qwen3.5-0.8B (10:19 EST), WeMM-Ecom-0.8B (10:48 EST) and slovo-4b — a Qwen3 fine-tune covering dozens of Indigenous languages of the Russian Federation (11:08 EST).
**Why it matters:** imatrix (i1) tiers are the quality-defining layer for sub-4-bit local runs; Tencent's WePocket/WeMM family is notable as an on-device decision/embedding line with published Apache quants within hours of release.
**Local inference:** 0.8–4B tiers run on CPU/NPU/phone class; Inkling bnb-4bit targets 24GB training rigs; slovo-4b Q4 lands ~2.4GB, fine on a Steam Deck.
Source: Hugging Face, bartowski/WeMM-Ecom-0.8B-GGUF (14:03 UTC) — https://huggingface.co/bartowski/WeMM-Ecom-0.8B-GGUF ; unsloth/Inkling-2-Mini-unsloth-bnb-4bit (13:48 UTC) — https://huggingface.co/unsloth/Inkling-2-Mini-unsloth-bnb-4bit ; mradermacher/WePocketModel1.0-4B-i1-GGUF (13:01 UTC) — https://huggingface.co/mradermacher/WePocketModel1.0-4B-i1-GGUF ; mradermacher/slovo-4b-i1-GGUF (16:08 UTC) — https://huggingface.co/mradermacher/slovo-4b-i1-GGUF

## Inference software and benchmarks

### Poros: streaming LoRA fine-tuning trains 72B on a 24GB RTX 4090 — or a 16GB Mac from a USB4 SSD
Event date: 2026-11-24. Caistro Labs' Poros arrived on Hacker News' Show HN at 11:57 EST and is a from-scratch answer to QLoRA memory walls: frozen 4-bit base weights stay in host RAM (or on SSD for MoE experts/Apple silicon), and one block of layers streams through the GPU per step, recomputing activations block-wise on the backward pass. Documented runs: Qwen2.5-72B (41.4 GiB at 4-bit) trains on an RTX 4090 at 13.5 GiB VRAM reserved / 45.1 GiB host RAM, 17.7 s/step; the same 72B streams from an external USB4 SSD on a 16GB M4 Mac at 11.4 GiB peak unified memory; Qwen3-30B-A3B fits an 8GB RTX 4060 Laptop. It supports SFT/DPO/IPO/KTO/ORPO/SimPO, is Apache-2.0 on pip (`poros-train`), verifies exact-match parity (`torch.equal`) with a resident run when the model fits in VRAM, and ships certified recipes plus a public benchmark-log repo. Limits: single GPU, LoRA only, no Windows.
**Why it matters:** Fine-tuning has been the last bastion of "you need a datacenter"; a verified, pip-installable streaming trainer with published logs drops the barrier hard — and the torch.equal equivalence check is exactly the rigor most community training tools skip.
**Local inference:** Ampere-or-newer NVIDIA on Linux, Apple silicon macOS 14+; 24GB consumer cards fine-tune 70B-class, 8GB laptops run 30B-A3B MoE adapters.
Source: Hacker News, "Show HN: Fine-tune a 72B model on a 16 GB Mac or 24 GB RTX 4090," 2026-11-24 16:57 UTC (11:57 EST) — https://news.ycombinator.com/item?id=50630996
Source: GitHub, Caistro-Labs/poros — https://github.com/Caistro-Labs/poros

### llama.cpp b12676 plus a 16-PR wave: CUDA attention fusion, SVE CPU kernels, MCP multimodality
Event date: 2026-11-24. After this morning's b12675 (already reported), b12676 cut at 08:24 EST atop 16 merges: #32500 fuses RMS_NORM+MUL into the MROPE/IMROPE path on CUDA with tuned block sizes; #30867 adds SVE-optimized q5_0/q5_1 dot-product kernels and #32142 adds repack kernels for ARMv8.0 64-bit targets without dotprod (Q4_0/Q8_0); #32721 brings chunked gated-delta-net replay to Vulkan; #32064 teaches the server to include image and audio data in MCP tool results; #30997 retries cached-prompt restoration after idle-slot clears; #32855 fixes Inkling 2 Mini's mix() weighted sum; and a six-PR UI cluster (#32945 et al.) decouples Llama Chat into a standalone PWA named via config.json or `npx --server`.
**Why it matters:** The CPU-side ARM work matters less for raw speed than for correctness on older/serverless ARM silicon, and the MCP multimodal tool results close a real feature gap between llama-server and hosted agent APIs. The UI decoupling quietly turns llama.cpp into a front-end vendor.
**Local inference:** CUDA users (5060 Ti and up) get the fused attention path free with the next build; ARM Mac/SBC users get new quant kernel paths; agent users get image/audio round-tripping through MCP.
Source: GitHub, ggml-org/llama.cpp release b12676, 2026-11-24 13:24 UTC — https://github.com/ggml-org/llama.cpp/releases ; merged PRs: #32500 https://github.com/ggml-org/llama.cpp/pull/32500 , #30867 https://github.com/ggml-org/llama.cpp/pull/30867 , #32064 https://github.com/ggml-org/llama.cpp/pull/32064

### vLLM 17-PR wave: validated Arc Pro B60/B70 recipes, RDNA attention tiles, quantized-target rewriting
Event date: 2026-11-24. The window's merges include #69321 (smaller tiles in the ROCM_ATTN prefix_prefill kernel on RDNA), #68901 (validated 8192-token context defaults for several Qwen, Nemotron-3-Nano and GPT-OSS models on Arc Pro B60/B70 — merged 09:07 EST), #69170 (vectorized per-token-head KV tile loads in Triton attention), #66342 (rewrites compressed-tensors regex targets at load time — fixes mismatched quant configs), #69041 (async renderer defaults to 4 workers), two security bounds (#69098 per-prompt media preprocessing limits, #61430 Sentence-Transformers module containment), plus gfx1250 torch-2.12 cleanup (#69572) and Transformers-backend MLA auto-detection (#69689).
**Why it matters:** The XPU recipes line pairs directly with the MAXSUN story above: Intel's Pro B-series now has first-party-validated serving defaults, not community guesswork. The compressed-tensors rewrite removes a chronic day-0 quant loading failure mode.
**Local inference:** Arc Pro B60/B70 owners get drop-in serving configs; RDNA3/4 attention prefill improves; multi-GPU/quant users inherit the loader fixes.
Source: GitHub, vllm-project/vllm merged PRs 2026-11-24 — https://github.com/vllm-project/vllm/pull/68901 ; https://github.com/vllm-project/vllm/pull/69321 ; https://github.com/vllm-project/vllm/pull/66342

### SGLang and TensorRT-LLM: Mooncake linker batching, configurable ReplaySSM, MiniMax-H3 static FP8 on Rubin
Event date: 2026-11-24. SGLang merged #49468 (08:42 EST — builds every Mooncake layer group's range reads before the forward fence), #46633 (11:34 EST — makes the NVIDIA GDN ReplaySSM verify pipeline stages configurable) and #48114 (kv-events replay retries under backpressure). TensorRT-LLM merged #22814 (12:02 EST — adds a MiniMax-H3 static FP8 U4 scenario, SM107 (Rubin) fusions and FP8 example configs).
**Why it matters:** These are disaggregation/decode-path polish PRs, but TRT-LLM's SM107 track is how Blackwell successor silicon gets usable early; MiniMax-H3 static FP8 landing on Rubin previews the first-week model list for next-gen NVIDIA boxes.
**Local inference:** Indirect — Rubin-targeted kernels eventually trickle down as consumer-sm_12x support; Mooncake linker work benefits large RAM-expert offload rigs.
Source: GitHub, sgl-project/sglang #49468 — https://github.com/sgl-project/sglang/pull/49468 ; NVIDIA/TensorRT-LLM #22814 — https://github.com/NVIDIA/TensorRT-LLM/pull/22814

## Also noted
- Ollama had no in-window release or merges; its v0.40.18 (04:18 UTC today) was reported in the 07:45 edition.
- NVIDIA's Game Ready 621.40 driver (DLSS 5 game launches) shipped this morning — gaming-graphics only, no AI-capability change.

## Coverage notes
- Verified items this run: 9 (3 hardware stories, 2 community/quant items, 4 software items incl. lead Poros), counting each bolded headline as one unit; quant-tier bundles listed as one item with per-repo timestamps.
- Ledger dedupe: 10 candidates suppressed as already reported or ledger-tagged — NVIDIA CEO-reinstatement re-coverage (STH "Jensen is BACK," Techmeme war-room/Trump/rally cluster), ASRock RBK-100 robotics kit, bartowski next3-pro + MoganOCR-TR-1.5 + LingSante ladders, mradermacher WePocketModel/WeMM base GGUFs + BLIP-Live i1, Unsloth Inkling-2-Mini native-8bit, llama.cpp b12670–b12675 / vLLM 58-PR / SGLang 52-PR / TRT-LLM #22552 waves from the 07:45 edition, NariQ (missed-tagged), and the Marvell/Penguin photonic-memory-fabric story (event date Nov 16, Register tag-page re-surface).
- Dead ends: r/LocalLLaMA's anonymous feeds returned HTTP 404 on both search.rss and the new-listing fallback (repeat of the Nov 20 outage) — Reddit lane unavailable, logged not chased. TechPowerUp's recent-news index failed to fetch; individual articles were reached via search instead.
- Out-of-window checks (confirmed pre-window, not footnoted — minor): Shuttle NW10U 0.4L Wildcat Lake mini-PC (announced Nov 23, TechPowerUp #355118), Strata v0.2.21 (05:10 EST). Hugging Face Daily Papers surfaced a weight-space-programming trio (WeightCode, Self-WSP, Feedback Shortcut spec-decode) but their submission timestamps cannot be pinned inside a 5-hour window.
- US Thanksgiving: mainstream press was near-silent; the community/GitHub/HF lanes carried the edition.
