# AI Hardware & Local Models Brief — Friday, November 6, 2026 · 12:45 ET

**Window:** 2026-11-06 07:45 → 12:45 ET (previous fire → this fire; new items only)

## TL;DR

- The Information reports TSMC is drafting plans to manufacture **3D DRAM** at its largest customer Nvidia's request — a foundry first that would put logic and AI memory under one roof amid the ongoing memory shortage.
- Bloomberg says Nvidia now **requires Southeast Asian cloud providers to enroll in Fleet Intelligence** — physical accelerator location tracking — as a condition of Nvidia Cloud Partner status.
- Apple's **M6 iMac and M6 14-inch MacBook Pro go on sale today**, with Apple pitching local LLM running on a 32GB-capped unified-memory entry Mac lineup.
- Community engines keep sprinting: **Saber**, a new Rust engine for the AMD Radeon AI Pro R9700, posted 6,645 tok/s prefill / 316 tok/s decode peak on Qwen3.8-27B, and **Strata** added a multi-user `serve` mode that runs a 35B-A3B MoE on one 12GB RTX 3060 for four concurrent users.
- Maritaca AI shipped **S5-nano**, a 4B Brazilian open VLM, with day-0 GGUF and FP8 tiers on the Hub this morning.

---

## AI hardware and silicon

**TSMC weighs building 3D DRAM at Nvidia's request** — Event date: November 6, 2026
The Information reports TSMC has begun internal planning to manufacture 3D DRAM — vertically stacked memory cells for higher density and bandwidth — at the request of Nvidia, its largest customer. Neither TSMC nor Nvidia responded to requests for comment, and no production timeline has been set. The move would push the pure-play foundry into memory territory long held by Samsung, SK Hynix and Micron, and lands while AI-driven memory shortages keep inflating prices across the whole hardware stack.
*Why it matters:* If realized, Nvidia could co-optimize accelerator and memory silicon from the same fab roadmap — the clearest sign yet that the DRAM shortage is now shaping chip architecture decisions, not just pricing. Competitors relying on merchant HBM/DRAM face a supplier that may prioritize its biggest customer's stacked-memory roadmaps.
*Local inference:* Nothing for 16GB-class cards this year — this is a supply-side bet aimed at the data-center memory crunch that has pushed the RTX 5070 Ti past twice MSRP; consumer relief depends on capacity that doesn't exist yet.
Source: The Information — "TSMC Weighs Making Advanced Memory Chips to Capture AI Spending" (Nov 6, 2026, ~08:45 ET) — https://www.theinformation.com/articles/tsmc-weighs-making-advanced-memory-chips-capture-ai-spending
Source: Emergent (secondary) — "TSMC Mulls 3D DRAM Production for Nvidia AI Chips" (Nov 6, 2026) — https://emergent.sh/news/tsmc-mulls-3d-dram-production-nvidia-ai-chips
Source: Star-Telegram (syndicated The Information) — https://www.star-telegram.com/news/business/article317959971.html

**Nvidia makes GPU location tracking a condition of Cloud Partner status in Southeast Asia** — Event date: November 6, 2026
Bloomberg reports (Mackenzie Hawkins) that Nvidia now requires Southeast Asian computing providers to register with its Fleet Intelligence system to qualify for Nvidia Cloud Partner (NCP) status. The program logs the physical location of AI accelerators across participating companies' fleets; NCP status buys public endorsement and preferred access to allocation. Participation remains voluntary outside the NCP requirement, and it's unclear whether existing partners must enroll to keep the designation or whether the rule spreads beyond the region. The move follows US and Singaporean scrutiny of Singapore-based Megaspeed over suspected chip diversion into China.
*Why it matters:* This converts a data-center management tool into an export-control instrument: Nvidia is using allocation leverage to police where its accelerators physically run, after Washington's frustration with Southeast Asia as a smuggling transit point. For the channel, expect provenance paperwork to become part of getting GPUs at all.
*Local inference:* No direct effect on consumer or homelab purchases, but tighter data-center channel control can push more allocation toward compliant hyperscalers — a headwind for already-scarce high-VRAM cards.
Source: Bloomberg — "Nvidia Boosts Chip Tracking in Southeast Asia to Fight Smuggling" (Nov 6, 2026) — https://www.bloomberg.com/news/articles/2026-11-06/nvidia-boosts-chip-tracking-in-southeast-asia-to-fight-smuggling
Source: Tom's Hardware — "Nvidia tightens tracking of AI GPUs in Southeast Asia…" (Nov 6, 2026) — https://www.tomshardware.com/tech-industry/big-tech/nvidia-tightens-tracking-of-ai-gpus-in-southeast-asia-as-chinese-chip-smuggling-draws-scrutiny-report-claims-cloud-providers-must-join-monitoring-program-to-qualify-for-official-partner-status

**Apple M6 iMac and 14-inch MacBook Pro hit shelves today** — Event date: November 6, 2026 (announced October 27)
The M6 Macs Apple unveiled on October 28 for pre-order go on sale this Friday: the 24-inch M6 iMac from $1,499 (10-core CPU/10-core GPU) and the 14-inch MacBook Pro from $1,999, both topping out at 32GB unified memory. M6 pairs a 12-core CPU with a 12-core GPU carrying a Neural Accelerator in each core plus a dual 16-core Neural Engine; Apple claims up to 5x faster on-device AI than the M4 iMac in LM Studio prompt processing and up to 25% faster AI workloads than M5 in the MacBook Pro (manufacturer figures on Apple-chosen workloads). Apple's positioning explicitly includes "running LLMs locally," and independent local-AI coverage this morning frames the 32GB ceiling as the defining constraint.
*Why it matters:* This is the affordable on-ramp for local inference on Apple Silicon — a Neural-Accelerator-equipped GPU at a $1,499/$1,999 entry price resets the value question against RTX 5070 Ti street pricing for CPU-adjacent AI work.
*Local inference:* 16GB standard / 32GB max unified memory means quantized single-digit-to-low-tens-of-billions models (roughly 14B–30B class at GGUF/MLX quants) are the sweet spot; anything larger belongs on Mac Studio-class machines.
Source: Apple Newsroom — "Apple introduces the new 14-inch MacBook Pro with M6" (Oct 27, 2026; availability Nov 6, 2026) — https://www.apple.com/newsroom/2026/10/apple-introduces-the-new-14-inch-macbook-pro-with-m6/
Source: explainx.ai — "M6 iMac & MacBook Pro for Local AI: 5x Faster Than M4?" (Nov 6, 2026) — https://explainx.ai/blog/apple-m6-imac-macbook-pro-14-local-ai-specs-prices-november-6-2026

---

## Model releases

**Maritaca ships S5-nano, a 4B Brazilian open-weight vision-language model** — Event date: November 6, 2026 (weights landed Nov 5; day-0 GGUF/FP8 published this morning)
Brazil's Maritaca AI released S5-nano, billed by community posters as "the best open-weights model in Brazil" — a ~4B image-text-to-text model. The org published the base checkpoint plus S5-nano-GGUF and S5-nano-FP8 variants overnight, with the GGUF and FP8 tiers updated within the last hour of publication — day-0 llama.cpp and FP8 deployment paths out of the gate, alongside the company's observatorio-maritaca benchmark suite.
*Why it matters:* Day-0 GGUF/FP8 coverage from a regional lab signals how fast even non-frontier open-weight releases now become locally deployable, and Portuguese-language multimodal coverage is a first for this brief in months.
*Local inference:* 4B-class — runs quantized in well under 8GB, i.e., any 6–8GB GPU, Apple Silicon laptop, or CPU; FP8 targets modern 16GB-class cards.
Source: Hugging Face — maritaca-ai org (S5-nano, S5-nano-GGUF, S5-nano-FP8; published Nov 6, 2026 ~10:00 ET) — https://huggingface.co/maritaca-ai
Source: r/LocalLLaMA — "maritaca-ai/S5-nano 4B · the best open-weights model in Brazil" (Nov 6, 2026) — https://www.reddit.com/r/LocalLLaMA/comments/1xpibxn/maritacaais5nano_4b_the_best_openweights_model_in/

---

## Community spins and quantizations

**Jeff v1.4: 0.8B local decision model adds 34 open-weight adapters, auto-routing, 2 ms decisions** — Event date: November 6, 2026
Firelex's Jeff — a 0.8B base model with LoRA adapters that return calibrated probabilities over caller-supplied options — got its largest update yet: 34 open-weight decision adapters with a router that auto-selects the right one, 90%+ accuracy on supported domains, ~2 ms per local decision (~40 ms via the hosted API), five SDKs (Python, Node.js, Rust, C#, Java) and CUDA + MLX support. Weights are on Hugging Face (mstrasser), code on GitHub (firelex/jeff), with a playground at jeffhub.ai. The author pitches decisions as "a software primitive" — like expressions, but over unstructured data.
*Why it matters:* Decision models (Jev-style one-prefill option scoring) are becoming a distinct local-AI category, and Jeff's adapter-routing + SDK breadth makes the pattern embeddable in ordinary apps rather than just agent stacks — the same lane as this month's elephant/Mica/Drex wave.
*Local inference:* 0.8B base + LoRA adapters = negligible VRAM; runs on any dGPU, or Apple Silicon via MLX at 2 ms per decision — fast enough for router/guardrail hot paths.
Source: r/LocalLLaMA — "Jeff v1.4: Decisions as a software primitive, 5 SDKs, 34 open-weight adapters (CUDA + MLX), 90%+ accuracy, 2 ms inference" (Nov 6, 2026) — https://www.reddit.com/r/LocalLLaMA/comments/1xpdtx2/jeff_v14_decisions_as_a_software_primitive_5_sdks/
Source: GitHub — firelex/jeff — https://github.com/firelex/jeff

**mradermacher's morning quant ladder: DroidSenior-7B-v3 and a CCORE MTP repack** — Event date: November 6, 2026
The day-0 quant machine published i1 (imatrix) GGUFs of celebibasar's DroidSenior-7B-v3 (qwen2-architecture 8B Android/dev-codegen spin; Q2_K through Q4_K_S ladder, 3.02–4.46GB files) at ~12:06 ET, and repacked the CCORE GLB-3D-Coder Qwen3.5-4B line with MTP modules ("Qwen3.5-4B-GLB-3D-Coder-MTP-Repacked-i1-GGUF") ~15 minutes earlier.
*Why it matters:* The MTP repack extends multi-token-prediction acceleration to the community's little 3D-asset coding model (its dataset released this morning), and DroidSenior shows the Android-codegen spin family maturing.
*Local inference:* Both are 4B–8B — full ladder fits 6–12GB cards; the MTP repack pays off specifically on speculative-decode-capable llama.cpp builds.
Source: Hugging Face — mradermacher/DroidSenior-7B-v3-i1-GGUF (published Nov 6, 2026 ~12:06 ET) — https://huggingface.co/mradermacher/DroidSenior-7B-v3-i1-GGUF
Source: Hugging Face — mradermacher (recent activity; Qwen3.5-4B-GLB-3D-Coder-MTP-Repacked-i1-GGUF) — https://huggingface.co/mradermacher/models?sort=modified

---

## Inference software and benchmarks

**Saber: new Rust engine for AMD Radeon AI Pro R9700 posts 6,645 tok/s prefill on Qwen3.8-27B** — Event date: November 6, 2026 (publicly unveiled on r/LocalLLaMA; repo created Nov 5)
A developer with the handle whodoneit1 released Saber, a from-scratch Rust inference engine targeting AMD Radeon AI Pro R9700 GPUs, claiming it beats existing engines on both Qwen3.8-27B and Qwen3.8-Flash-Next. The headline BetterBench numbers (Unsloth NVFP4 weights): 6,645 tok/s prefill and a 316 tok/s decode peak on Qwen3.8-27B across a 2×R9700 rig (Ryzen 9900X, 64GB DDR5), with tensor parallelism at 1, 2 or 4 cards. The author says he'd been running it privately for a month before publishing; commenters immediately flagged the R9700 pricing angle, while one skeptic noted single-user numbers depend on the full system config.
*Why it matters:* Third-party CUDA-free engines for RDNA4 workstation cards are the pressure point AMD's 32GB/$1,700 R9700 needed — local MoE serving on red silicon just got a credible non-ROCm-stack option.
*Local inference:* R9700 32GB at ~$1,700 (MSRP) per card — 27B-class NVFP4 on a single card, or high-context MoE across two/four cards, with no NVIDIA licensing in sight.
Source: r/LocalLLaMA — "Qwen3.8 27b hitting 6,645 tok/s on prefill and 316 tok/s decode peak on Saber" (Nov 6, 2026) — https://www.reddit.com/r/LocalLLaMA/comments/1xpicjz/qwen38_27b_hitting_6645_toks_on_prefill_and_316/
Source: GitHub — GGZ14/saber-engine (created Nov 5, 2026) — https://github.com/GGZ14/saber-engine

**Strata adds multi-user `serve`: four concurrent users on one 12GB RTX 3060** — Event date: November 6, 2026
UPDATE on "RustingStrata" (reported Nov 4 — the pure-Rust Qwen GGUF engine). Same author's follow-up ships `strata serve`: OpenAI- and Anthropic-compatible endpoints serving Qwen3.6-35B-A3B UD-Q2_K_XL to several users at once on a single RTX 3060 12GB — 106 tok/s for one user, 139 tok/s aggregate for two, 164 tok/s aggregate (41 tok/s each) for four. It ships API-key management with per-key usage logging, a fair queue with per-key caps, /health and Prometheus /metrics, CUDA or Vulkan (NVIDIA/AMD/Intel), two-card model splitting, and one Rust binary with no Python or Docker.
*Why it matters:* This is the "team of four on a $300 GPU" deployment story — llama.cpp-class quantized MoE serving with real multi-tenant plumbing (keys, queues, metrics) in a single binary, squeezing cheap 12GB cards into workloads normally reserved for APIs.
*Local inference:* 12GB RTX 3060 (or equivalent AMD/Intel via Vulkan) runs a 35B-A3B MoE at human-readable speeds for four users; dual-GPU splits extend to bigger A3B models.
Source: r/LocalLLaMA — "I turned a 12 GB RTX 3060 into a Qwen API server for 4 people at once" (Nov 6, 2026) — https://www.reddit.com/r/LocalLLaMA/comments/1xpfzqk/i_turned_a_12_gb_rtx_3060_into_a_qwen_api_server/
Source: GitHub — RustingStrata (repo linked from the post) — https://github.com/Serious-Scarcity/RustingStrata

**vLLM morning wave: GLM-5.3 FP8 MoE kernels up to 1.60x, LFM3-Audio day-0, W4A8 FP8 MoE path** — Event date: November 6, 2026 (merges 07:45–12:45 ET)
23 PRs merged inside the window. Highlights: #65746 expands FP8 MoE TMA kernels for GLM 5.3 on SM100 for a claimed 1.03–1.60x kernel-speedup; #65828 adds day-0 Liquid AI LFM3-Audio support; #64410 adds a W4A8 FP8 checkpoint path for routed-expert MoE; #65874 fuses GDN MTP speculative decoding supporting DSpark K=8 and harvesting idle SMs at small batch sizes; #63795 gives DSpark sliding-window MLA draft layers; #65463 re-fits `--max-model-len auto` after extensible KV-cache shrink; plus ROCm build-speed serializations (#63365, #62718) and a DeepGEMM fallback for pre-12.9 nvcc (#60438).
*Why it matters:* The spec-decode cluster (DSpark K=8 + sliding-window MLA drafts + GDN fusion) compounds into meaningfully lower interactive latency for MoE serving, while the W4A8 path widens the quantized-MoE menu on Blackwell-class cards.
*Local inference:* SM100 items are B200/GB200-class; W4A8 FP8 MoE and the DSpark work benefit 24–32GB RTX 4090/5090-class builds running MoE checkpoints.
Source: GitHub — vllm-project/vllm merged PRs (Nov 6, 2026 12:45–17:50 UTC) — https://github.com/vllm-project/vllm

---

## Also noted

- **SGLang + llama.cpp in-window merges (Nov 6):** SGLang shipped #46291 (multimodal image preprocessing through the Rust server via sglang-processor), #48668 (router prefix-chain visibility fix) and #45772 (encoder cache allocation rank-0 only); llama.cpp's builds advanced to b12174–b12177 with the headline fix being #31563 (CLI crash on invalid UTF-8 in prompt/files) plus CI/test hygiene — https://github.com/sgl-project/sglang / https://github.com/ggml-org/llama.cpp
- **Nvidia RTX 5090 heist coverage surfaces (Nov 6; theft Aug 29):** Tom's Hardware and VideoCardz report police investigating six pallets of Nvidia hardware worth $11.4M taken from an Oakland-to-Texas truck posing as a food hauler; a stolen Zotac RTX 5090 surfaced on a Brazilian marketplace at $6,000+ with a serial-number-visible photo — note the heist itself is from late August, this is the new reporting — https://www.tomshardware.com/pc-components/gpus/six-pallets-of-nvidia-gpus-worth-usd11-4-million-stolen-in-california-cargo-heist-authorities-investigate-after-truck-posing-as-a-food-transport-company-disappears
- **Nvidia gives away RTX 5090s to deny discontinuation (Nov 6, VideoCardz):** the official store returns to 5090 promotions after a run of RTX 5080 giveaways while production/availability questions linger — a marketing counter-signal in the AI-card scarcity narrative — https://www.videocardz.com/
- **Hugging Face launches "harness adapters" (surfaced on r/LocalLLaMA Nov 6):** one RL-trained LoRA per model-and-harness (pi, opencode, hermes) making fewer tool calls than base models while holding accuracy, with the training run public on a Harness Hub Space — directly relevant to local agentic coding setups — https://huggingface.co/blog/harness-hub/harness-adapters

## Just missed the window

- **Muir: Linear Attention with Sensitivity-Allocated Gating and Matched-Filter Readout** (Google researchers) — arXiv posted Nov 5, 17:53 UTC, ~5h before this window opened: cuts perplexity 1.8–2.0% vs Gated DeltaNet-2 at 166M–1.34B scales, trains 2.7x faster than Transformer at 32K, extrapolates 4K-trained to 10M-token streams with near-100% passkey to 1M — a linear-attention candidate for cheap long-context local runs. https://arxiv.org/abs/2611.08376

## Coverage notes

- **Verified: 12 report items** (9 full entries + 4 one-liners; "Also noted" harness-adapters and merges counted as items) from ≥8 independent outlets (The Information, Bloomberg, Tom's Hardware, VideoCardz, Apple Newsroom, Hugging Face, GitHub, r/LocalLLaMA, Emergent/Star-Telegram syndication).
- **Ledger dedupe: ~17 candidates suppressed** as already reported or already-footnoted — incl. RTX 5070 Ti 2x-MSRP (re-covered today by VideoCardz, reported 07:45), Lisa Su "data-center first" (AMD-earnings footnote), MoE LRFU/MQ cache PR #31233 repost, Drex AMA (re-coverage of Oct 13/14 weights + Nov 5 paper), Nvidia RTX 5080 store pull (Nov 4), RTX PRO 5500 listing + ASUS Ascent QN10 + CUDA 13.5 (Nov 5), Unsloth UD-v3.1 (Nov 5). Excluded as out-of-window re-surfaces: NVIDIA Cosmos 3 Nano checkpoints (blog Oct 30, HN'd today), Linum pjit-21k (blog Nov 4), Zen 6 Medusa Point 2/3 leak (posted pre-07:45). "Lawyer with too many GPUs Part 4" (8×V100 DeepSeek-V4.1-Flash) posted before this window opened and after the last run's close — fell between windows, not counted.
- Dead ends: The Register AI index returned 404 (topic river used instead); maritaca-ai/S5-nano-4B slug guessed 404 (org page gave real slugs); Reddit anonymous feed exposes no scores/precise post times — in-window placement judged by listing order relative to the 07:45 run's reported posts.
- No SGLang/Ollama/TensorRT-LLM/ComfyUI releases fell in the window (latest: v0.5.23, v0.40.15, v1.4.0rc2, v0.44.0 — all reported previously).
