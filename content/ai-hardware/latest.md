# AI Hardware & Local Models Brief — Friday, November 13, 2026

**Window:** 2026-11-12 17:45 EST → 2026-11-13 07:45 EST (overnight edition; new items only)

## TL;DR
- **llama.cpp hits v1.0.0** — the first stable-versioned release in the project's history; ggml bumped to 1.0.0 alongside it, nightly line continues at b12360. No formal announcement had appeared as of fire time, only the tag itself.
- **Boole-32B-A2.5B-Preview**: Precision Numerics releases a 1.8-bit (8.96 GB) spin of Recursive's flint with expert pruning and custom kernels — author-reported 565 tok/s on one RTX 5090.
- **R9700 quad-card serving playbook**: a full hardware+software tuning log for 4x Radeon AI PRO R9700 on EPYC Rome lands on GitHub — DeepSeek-V4.1-Flash EXL3 2.25 bpw prefill 6.1k tok/s, Qwen3.8-Flash-Next 4.05 bpw to 1,100 tok/s aggregated, byte-identical output.
- **Strata EXL3 AMD fork update**: +10–24% decode on RX 7900 XTX (Q4-class quality at 3.05 bpw), optional CPU-side expert repacking, experimental MiMo V2.6 Flash support.
- **Inkling-2-Small gets its Unsloth Dynamic GGUF ladder** (UD-Q4_K_XL → UD-Q8_K_XL, 1M ctx) — the update item on yesterday's Thinking Machines weights drop.

## Inference software and benchmarks

### llama.cpp tagged v1.0.0 — first stable release
**Event date: Nov 13, 2026 (05:20 EST).** Georgi Gerganov merged PR #32190 bumping llama.cpp to 1.0.0 (09:06 UTC) and the github-actions bot published the `v1.0.0` release at 10:20 UTC, with ggml itself bumped to 1.0.0 (ggml/1690). The changelog since v0.9.0 (Oct 26) is long — highlights include OCP FP8 weights and FP8 KV-cache (#28413/#28392), Hexagon Q5_0/Q5_1, Intel Vulkan prefill FA kernel (#29357), CUDA NVFP4 prefill epilogue fusion (#26048), the deprecated-API prune waves (#30565/#29801) and the server CORS/SSRF hardening batch. The release note says "More info at 3PM CET," but no formal announcement had been found at this brief's fire time — trackers describe it so far as "just a version change across 3 commits." Nightly builds (b12347–b12360) continue in parallel.
- *Why it matters:* the flagship local-inference engine formally exits 0.x, which signals API/ABI stability intent to the ecosystem (engines, harnesses, distros) that build on libllama/ggml — and it lands on the same week the engine absorbed FP8 weights+KV as first-class formats.
- *Local inference:* every llama.cpp class device — RTX 5060 Ti 16GB through DGX Spark and Strix Halo; no hardware change required, drop-in builds published for all backends.
- Sources:
  - llama.cpp v1.0.0 release — GitHub, published Nov 13, 2026 10:20 UTC — https://github.com/ggml-org/llama.cpp/releases/tag/v1.0.0
  - "llama.cpp bumps to version 1.0.0" — AGI Hunt, Nov 13, 2026 — https://agihunt.info/en/p/1a1d4400966ec72614bee9d363f
  - r/LocalLLaMA thread (u/jacek2023) — Nov 13, 2026 09:22 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xv3cyt/llamacpp_bump_version_to_100_by_ggerganov_pull/

### Strata EXL3 AMD fork: +10–24% decode, CPU-side expert repacking, experimental MiMo V2.6 Flash
**Event date: Nov 13, 2026 (03:10 EST post).** u/nasone32 published an update to his Strata EXL3 fork for AMD (HIP kernels + memory-management work): decode is 10–20% faster than the previous version on average, with one case (4.05 bpw, 2x RX 7900 XTX, code) at +23.9%. Concretely on a Ryzen 9 7950X + 96GB + 2x 7900 XTX (Linux, ROCm core-7.14): Qwen3.8-Flash-Next EXL3 3.05 bpw (≈ IQ4_K_XS-quality) reaches 87.3 tok/s decode with two cards; 4.05 bpw (≈ Q5_K_M) reaches 76.7 tok/s; single-GPU 4.05 bpw with CPU assistance runs 55–60 tok/s. New optional "local expert repacking" reformats quantized experts for efficient CPU access from system RAM. MiMo V2.6 Flash now runs at ~25–30 tok/s with the EXL3 2.20 bpw MOPD quant (experimental, CPU assistance recommended).
- *Why it matters:* EXL3's non-equivalence to same-bpw GGUFs (3.05 bpw ≈ 4-bit quality) means a tuned Vulkan-free AMD path keeps closing speed gaps on RDNA3 cards that many homelabs already own — and it keeps pushing the "run Q4 at Q3 speeds" tier forward.
- *Local inference:* targets 2x RX 7900 XTX 24GB (or 1x) + high-RAM AM5 desktop; author's config is 96GB DDR5.
- Sources:
  - r/LocalLLaMA (u/nasone32) — Nov 13, 2026 08:10 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xv282p/strata_exl3_amd_updated_1520_tks_run_q4_at_q3/
  - turboderp/Qwen3.8-Flash-Next-exl3 (referenced quants) — https://huggingface.co/turboderp/Qwen3.8-Flash-Next-exl3

### 4x R9700 "exact serving" tuning log: DSv4.1-Flash prefill 6.1k, Flash-Next aggregate 1,100 tok/s
**Event date: Nov 13, 2026 (repo created 00:49 EST, post 03:30 EST).** u/mikoskinen published r9700-exact-serving, an investigation history + change registry for optimizing Radiance (fork + radiance-exl3 plugin) tensor-parallel over 4x Radeon AI PRO R9700 on an EPYC 7302P (Rome), 512GB DDR4-2933, all cards stock 300W. Results: DeepSeek-V4.1-Flash EXL3 2.25 bpw — 6,100 tok/s prefill, 96–165 tok/s single-user decode, 259 tok/s at 16-way concurrency; Qwen3.8-Flash-Next EXL3 4.05 bpw — 9,800 tok/s prefill and 1,100 tok/s aggregated decode, all byte-identical to each model's reference engine output. The author explicitly open-sources the tuning knowledge instead of another fork.
- *Why it matters:* the most complete public R9700 multi-card tuning document to date, aimed at agentic (Claude Code) multi-user serving on AMD pro cards — a credible NVIDIA-alternative price/perf data point at ~$1,700/card street.
- *Local inference:* 4x R9700 32GB (128GB VRAM pool) on any EPYC/Threadripper with 4x PCIe4 x16; also portable to 2-card setups per the repo.
- Sources:
  - r/LocalLLaMA (u/mikoskinen) — Nov 13, 2026 08:30 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xv2jst/4xr9700_exl3_hwsw_tips_ds41f_225bpw_pp_61k_c1/
  - mikoskinen/r9700-exact-serving — GitHub, created Nov 13, 2026 05:49 UTC — https://github.com/mikoskinen/r9700-exact-serving

### Update on previously reported — "Lawyer with too many GPUs" reaches near-NVFP4 quality on 8x V100 (2018 silicon)
**Event date: Nov 12, 2026 (18:31 EST).** The ProprietaryLegal lawyer's series (first reported Nov 9) posts part 5: DeepSeek-V4.1-Flash now runs on eight 2018 V100s at "nearly NVFP4 quality" (author-requantized experts + custom Volta kernels), 524K context, 100+ tok/s decode and ~160 tok/s with two chats in parallel, driving his fully local legal harness; he also runs two DGX Sparks with 3–4 concurrent Flash-Next agent sessions at 1M context. Author-reported; a top comment flags the post as AI-detector-suspicious, and precision claims ("near-NVFP4") are not independently measured.
- *Why it matters:* continued evidence that 16GB-of-VRAM-per-card legacy fleets remain economically viable for agentic local serving when paired with aggressive per-model requanting.
- *Local inference:* 8x V100 16/32GB + dual EPYC Rome, 512GB RAM — the cheapest per-GB-of-VRAM entry point in the community right now.
- Sources:
  - r/LocalLLaMA — Nov 12, 2026 23:31 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xus81t/lawyer_with_too_many_gpus_part_5_deepseekv41flash/

### In-window merge waves (Nov 12 17:45 → Nov 13 07:45 EST)
- **vLLM** — 57 merged PRs: GLM-5.3-Flash fused in-place q/k RMSNorm+RoPE (#67235), MiniMax-M3.1-Flash MI355X AgentX recipe (#67272), gfx950 MXFP8 native-linear 256x256x128 tiles (#66598), DSv4.1 Mono TP8 dual-kernel merge (#65461), AITER MLA full-graph decode metadata in one kernel (#65132), int4 fused-MoE tensor-descriptor path XPU/sm100 (#52652), Kimi K3 tool-call streaming on the Rust frontend (#66530), KV-cache allocation-policy RFC part 2 (#61452). — https://github.com/vllm-project/vllm
- **SGLang** — 35 merged PRs: Kimi-K3.1 served on the unified memory pool (#49913), NPU HiCache Mooncake L3 for DeepSeek-V4/GLM-5.2 (#49054), FP8-KV DSA prefill context-parallel on NPU (#50096), swap-AB MoE on Blackwell CuTe-DSL (#48350), hybrid-Mamba state checkpointing only at fork points (#49886), Inkling tool_choice rendering fix (#50237), deep-gemm 0.2.8 / flash-mla 1.0.5 bumps (#50016). — https://github.com/sgl-project/sglang
- **llama.cpp** — 13 merged PRs around the 1.0.0 bump: grammar left-factoring optimizer (#29557), Vulkan device keepalive (#29267), moe-cache refactor (#32157), K2 Horizon think-closer acceptance (#30335). — https://github.com/ggml-org/llama.cpp
- *Why it matters:* the day's kernels work concentrates on Blackwell/NPU MoE decode and on AMD ROCm recipes — both directly shift tokens/sec-per-dollar for self-hosters.
- *Local inference:* vLLM/SGLang items target datacenter (MI355X/gfx950/B200/GB300/910B); llama.cpp items reach all consumer classes.

## AI hardware and silicon

### Used-GPU price watch: A100s down another 20–30%, RTX 6000 to $14.8k
**Event date: Nov 13, 2026 (22:31 EST post).** u/Bascilian (of the A100-collapse posts) reports used A100 prices down another 20–30% since his previous post — sellers dumping supply as Rubin/Blackwell pull efficiency (3–4x perf/watt) — and quotes "RTX 6000" (RTX PRO 6000) now at ~$14.8k from an $18k peak. He argues the remaining buyer pool is price-sensitive enthusiasts and small renters comparing A100s against R9700 and Intel Crescent Island (160–480GB). Same hour, a separate weekly roundup ranked used datacenter/workstation GPUs on eBay by price-per-GB-of-VRAM (Nov 12, 17:49 EST). Anecdotal, single-seller observations — no transaction data.
- *Why it matters:* Ampere-era 80GB cards keep eroding as the budget long-context entry point; the community's used-market floor moves roughly monthly now.
- *Local inference:* used A100 80GB (SXM2/PCIe) rigs at these levels remain the cheap-VRAM play for offline batch serving despite no Flash-Attention-4 class kernels.
- Sources:
  - r/LocalLLaMA (u/Bascilian) — Nov 13, 2026 03:31 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xuxbeb/a100_prices_down_2030_since_my_post_about_them/
  - r/LocalLLaMA weekly eBay $/GB ranking — Nov 12, 2026 22:49 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xur9ud/used_datacenterworkstation_/

## Model releases

### Update on previously reported — Unsloth posts Inkling-2-Small Dynamic GGUF ladder
**Event date: Nov 13, 2026 (06:31 EST repo update).** Unsloth's `Inkling-2-Small-GGUF` refreshed with the full Unsloth Dynamic ladder (UD-Q4_K_XL and UD-Q8_K_XL nine-shard sets visible, mmproj BF16/F16/F32, imatrix + KLD/top-1 plots), 593.7B total params at `inkling2` architecture, 1M context, Apache-2.0. This follows yesterday's edition's Apache-2.0 weights drop (ledger: Thinking Machines Inkling-2-Small, Nov 12).
- *Why it matters:* the day-1 quant tier is what makes a 594B/17B MoE a self-hosting conversation rather than a benchmark footnote; the published KLD-vs-size curves continue the community's quant-quality arms race.
- *Local inference:* 594B-A17B class — single-node fit needs large unified-memory machines (Ryzen AI Max 128GB will load at aggressive tiers) or multi-GPU; UD-Q4_K_XL in the ~300GB range targets Mac Studio 512GB / dual-B300-class hosts.
- Sources:
  - unsloth/Inkling-2-Small-GGUF — Hugging Face, lastModified Nov 13, 2026 11:31 UTC — https://huggingface.co/unsloth/Inkling-2-Small-GGUF

### Community benchmark note: Artificial Analysis scores Qwen3.8-Flash-Next at 29
**Event date: Nov 13, 2026 (23:06 EST).** r/LocalLLaMA notes Artificial Analysis updated Qwen3.8-Flash-Next's Intelligence Index to 29 — between GPT-6 Luna Max (28) and Claude Haiku 5.5 High (30), and closer to Qwen3.8-Max (34) than the 27B (20).
- *Why it matters:* the 16GB-class flagship keeps re-scoring upward relative to closed mid-tier models — the practical effect is sustained pressure on API pricing at the small-model tier.
- *Local inference:* runs at usable speed on RTX 5060 Ti 16GB / 5090 / DGX Spark tiers (multiple prior editions' numbers).
- Source: r/LocalLLaMA (u/lionellee77) — Nov 13, 2026 04:06 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xuxzfs/artificial_analysis_updated_qwen_38_flash_next/ ; https://artificialanalysis.ai/models/qwen3-8-flash-next

## Community spins and quantizations

### Boole-32B-A2.5B-Preview: 9GB 1.8-bit MoE of Recursive flint, 565 tok/s on one RTX 5090
**Event date: Nov 13, 2026 (04:29 EST announcement).** Precision Numerics (authors of the October Boole-1.7B 1-bit LLM) released an early 1.8-bit version of Recursive's flint: mixed-precision quantization, expert pruning (commenters note 52 "unrouted" experts pruned) and custom kernels via their own llama.cpp fork. The Q2_0 GGUF is 8.96 GB with 262K context; the company claims 565 tok/s llama-bench decode on a single RTX 5090 — ~9x smaller than BF16 and 2x faster than INT4 (author-reported; no external quality eval yet; HF repo created Nov 10 but went public with today's post).
- *Why it matters:* sub-10GB MoE serving at triple-digit speed tests how far sub-2-bit training-adjacent compression can go on 32GB-class cards — the community's standing question since ternary Bonsai. The "is it actually good?" skepticism in-thread (faster garbage risk) is the open issue; eval numbers are promised.
- *Local inference:* fits any 12GB+ card (RTX 3060 and up); 565 tok/s claim measured on RTX 5090 32GB; custom kernels require the PrecisionNumerics llama.cpp fork.
- Sources:
  - r/LocalLLaMA (u/d77chong) — Nov 13, 2026 09:29 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xv3h93/boole32ba25bpreview_9gb_18bit_moe_565_tokens_on_1/
  - precision-numerics/Boole-32B-A2.5B-Preview-GGUF — Hugging Face (8.96GB Q2_0) — https://huggingface.co/precision-numerics/Boole-32B-A2.5B-Preview-GGUF
  - PrecisionNumerics/boole-demo + llama.cpp fork — https://github.com/PrecisionNumerics/boole-demo

### mradermacher's overnight quant ladder (all in-window uploads)
**Event date: Nov 13, 2026.** New GGUF repos created 23:37–06:00 EST: **Beek-i1-GGUF** (day-0 imatrix quants of UTF-ai/Beek — a Dutch-government-values-aligned Qwen3.8-27B fine-tune, Apache-2.0, from the Dutch public-sector AI team); **Qwensploit-V2-27B-CyberSecurity-Qwen3.8-i1-GGUF** (cyber agentic spin); **Rutam-33B-i1-GGUF**; **Summerwise-27B-i1-GGUF** (new imatrix tier on yesterday's reported Summerwise ladder); **diffusion-lfm2.5-24b-a2b-blocktune-GGUF** (diffusion-LM blocktune variant of Liquid AI's LFM2.5-24B-A2B).
- *Why it matters:* the public-sector-values model line (Beek) is the latest government-aligned open-weight experiment after Singapore's SEA-LION series — same 27B Qwen3.8 chassis, different alignment corpus.
- *Local inference:* all 27–33B-class — single 16–24GB cards at Q4 tiers, 32GB comfortable.
- Sources (examples): https://huggingface.co/mradermacher/Beek-i1-GGUF ; https://huggingface.co/mradermacher/Qwensploit-V2-27B-CyberSecurity-Qwen3.8-i1-GGUF ; https://huggingface.co/mradermacher/diffusion-lfm2.5-24b-a2b-blocktune-GGUF (all lastModified Nov 13, 2026)

## Also noted
- **LLM.cpp Arm prefill study claims 6.5x prompt-processing** via a NEON 4x4 kernel path (silicon-forge-arm-neon-study, hit HN front page 05:28 EST) — a community follow-up on llama.cpp's just-merged #32128 NEON tile; author-reported. https://github.com/hardwiredagent/silicon-forge-arm-neon-study
- **Gemma 4 E2B running on an 8GB-RAM netbook** (r/LocalLLaMA, Nov 13 19:31 EST post) — another sub-8GB unified-memory demo. https://www.reddit.com/r/LocalLLaMA/comments/1xutkqv/gemma_4_e2b_on_a_8gb_ram_ne/
- **BULaMU: Luganda language models (20M/47M/110M) trained from scratch** for offline phone use, shared by the creator (12:29Z Nov 13). https://www.reddit.com/r/LocalLLaMA/comments/1xv6m1p/a_multilingual_code_switching_tiny_model/
- **Local Parakeet STT on Android** — offline "Wispr Flow"-style dictation using Parakeet, usable even on a Redmi Note 7 (Nov 13, 06:24 UTC). https://www.reddit.com/r/LocalLLaMA/comments/1xv0ibj/local_parakeet_speech_to_text_on_android/
- **Thermal Grizzly clarifies WireView Pro II warranty** — two-year GPU-connector warranty, USB/auto-shutdown cables optional (VideoCardz, Nov 13 ~06:50 EST). Relevant to multi-card R9700/5090 owners. https://videocardz.com/

## Just missed the window
- **Kioxia + Dell's 157 PB single-rack object store** (640x LC9 245.76 TB E3.L QLC NVMe, 16x PowerEdge R7725xd, BiCS8 32-die) — PR dated Nov 12 morning, ~6h pre-window; TechPowerUp relayed it at 15:15 EST, minutes before window open. First footnote only.
- **Applied Materials FY2026 Q4: record $10.41B revenue** on AI wafer-fab-equipment demand (non-GAAP EPS $4.16, record $3.30B op. cash) — released ~16:05 EST Nov 12, pre-window.
- **Edge0-145B "145B on one consumer GPU"** (NVFP4/Q3_K, 48L/512-expert latent MoE @10B active, 5090D-24GB agent decode 98.3 tok/s claimed, RTX Spark N1X rows) — HF repos created Nov 11 05:22 UTC, pre-window; in-window r/LocalLLaMA hype + a top comment challenging provenance ("feels off/too good to be true"). Watch for eval corroboration.

## Coverage notes
- **Verified items this edition: 12** (4 leads, 8 also-noted/footnotes/updates; counting unit = distinct in-window events after ledger dedupe). Candidates suppressed as already reported: **9** (Siren-Halo re-cover, Adrenalin 26.11.1 shipping, Jensen-resignation follow-ups x2, Inkling rL release wave, Intel Arc Vulkan-FA thread, ERNIE-5.5-Flash-Preview coding wave, CMP170HX unlock re-cover, "lawyer part 1–4" re-references). Repeat-coverage "missed:" items this run: 3 (Kioxia/Dell, AMAT, Edge0 — each tagged, never to be footnoted again).
- Sources swept: r/LocalLLaMA (new + top/day), HN Algolia river + keyword queries, TechPowerUp front, ServeTheHome front, VideoCardz front, TrendForce, Hugging Face trending + author APIs (mradermacher/unsloth/DavidAU), GitHub releases + merged-PR searches for llama.cpp/vLLM/SGLang, vendor newsrooms (NVIDIA/AMD/AMAT/Kioxia), HF Daily Papers Nov 13.
- Dead ends / limitations: TrendForce memory-price-hike article (Nov 12) lacked a time-stamp placing it in-window and duplicates October–November ledger material — excluded; NVIDIA 621.24 Game Ready driver (Nov 12) carries no AI-relevant changes — out of scope; llama.cpp v1.0.0 "3PM CET" announcement had not appeared at fire time — re-check next edition; HF trending page rendered without model names (JS-dependent) — compensated via author-API sweeps; no new AI hardware vendor announcements landed overnight (quiet weekend-eve window — the two hardware stories were both pre-window).
