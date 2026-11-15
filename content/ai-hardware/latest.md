# AI Hardware & Local Models Brief — Sunday, November 15 (17:45 ET edition)

**Window: 2026-11-15 12:45 → 17:45 ET (previous fire → this fire).** New items only; all events first disclosed inside the window unless marked as an update or footnote.

## TL;DR

- A leaker's schematic-grade dump puts Intel's next desktop flagship on Intel 18A-P: an unlocked **Razor Lake 12 P-core (12+0+4) die with bLLC** big last-level cache — while sibling parts go to TSMC N2P.
- **BigMoeOnEdge**, an adaptive MoE streaming optimizer running on stock llama.cpp, posted author benchmarks beating Strata and colibri on 8–12 GB cards and Macs — Qwen3.6-35B at ~35 t/s on an 8 GB card — though Strata still wins on its two tuned 5060 Ti boxes.
- **llama.cpp shipped six builds** (b12421–b12426) with day-0 mtmd support for Higgs v3 STT, Microsoft VibeVoice-ASR and Zyphra ZONOS3-mini TTS.
- The FT reports **Jensen Huang has discussed a comeback as NVIDIA CEO**, with allies standing up a Silicon Valley "war room" — four days after his resignation.
- On legacy iron, **1Cat-vLLM** (the V100-focused vLLM fork) out-decoded Strata 124–142 vs 74–79 t/s at tensor-parallel 4 on 4x V100-16GB over PCIe 3.

## AI hardware and silicon

**Intel Razor Lake leaks as an unlocked 12 P-core bLLC die on Intel 18A-P** — event date: November 15, 2026 (published 14:30 ET)
Leaker Jaykihn reports a previously unseen Razor Lake desktop configuration: a 12+0+4 layout (twelve Coyote Cove P-cores, zero full E-cores, four LP-E cores) on a single compute die built on Intel 18A-P, with bLLC (Big Last Level Cache) enabled. Jaykihn clarified it is a dedicated design distinct from the other planned Razor Lake desktop parts, which reportedly use TSMC N2P. VideoCardz tags the piece as a rumor; single source.
*Why it matters:* A big-cache, all-P-core 18A-P part is the clearest signal yet that Intel is building an x86 response to AMD's X3D-style cache-monster positioning — directly relevant to CPU-heavy local inference (prefill offload, expert streaming, small-model CPU serving) where L3 capacity is the bottleneck, not core count.
*Local inference:* No power/price yet. If bLLC lands as rumored, it competes for the "CPU that feeds the GPUs" slot in DDR5-heavy MoE rigs (Strata/moe-cache class workloads) rather than displacing GPUs.
- Source: VideoCardz, "Intel Razor Lake reportedly gets 12 P-core-only die with bLLC on Intel 18A-P," Nov 15, 2026 14:30 ET — https://videocardz.com/newz/intel-razor-lake-reportedly-gets-12-p-core-only-die-with-bllc-on-intel-18a-p

**Update on previously reported (Nov 12 "ceo-transition"): allies form a "war room" to bring Jensen Huang back as NVIDIA CEO** — FT report early Sunday; syndicated coverage 14:26 ET
The Financial Times reports Huang is weighing a comeback and has discussed returning with confidants; one ally told the FT they are assembling a "war room" of Silicon Valley executives to lobby the board and seek White House support, saying "everyone that matters in tech outside the frontier labs loves Jensen," with the effort having Huang's support. Reuters' syndication (14:26 ET) notes it follows last week's FT scoop that the board approached Arm CEO Rene Haas, and that Treasury Secretary Scott Bessent is publicly pressing for an outside CEO. NVIDIA did not respond to requests outside business hours.
*Why it matters:* Roadmap and ecosystem commitments (open-weight sponsorship, DGX/RTX Spark cadence, NVLink Fusion partners) hang on who sits in the chair; a reversal before the Nov 17 earnings would reshuffle the AI-hardware supply chain's leadership risk premium.
*Local inference:* Indirect — consumer/workstation GPU pricing, DGX Spark availability and open-model goodwill all sit under this decision.
- Source: Reuters via U.S. News, "Jensen Huang Discusses Comeback as Nvidia CEO With Allies, FT Reports," Nov 15, 2026 2:26 PM ET — https://www.usnews.com/news/top-news/articles/2026-11-15/jensen-huang-discusses-comeback-as-nvidia-ceo-with-allies-ft-reports
- Source: Financial Times (original report) — https://www.ft.com/content/45383d86-33c4-4d99-86e5-5e058d3567c1
- Source: VideoCardz, "Jensen Huang is reportedly considering a comeback as NVIDIA CEO," Nov 15, 2026 — https://videocardz.com/newz/jensen-huang-is-reportedly-considering-a-comeback-as-nvidia-ceo

## Model releases

No new open-weight model family was first disclosed inside this window. (The "Villanova2" EU MoE family posted on r/LocalLLaMA at 12:46 ET has Hub repos created Oct 26–Nov 9 — pre-window, excluded. The community ladder below is the day's model-side news.)

## Community spins and quantizations

**mradermacher's afternoon ladder: DeepSeek-V4-Pro-4B heretic, a SoftMoE trio, and ~15 more GGUF repos** — events: November 15, 12:49–17:41 ET (Hub lastModified, verified via API)
The window brought roughly 20 new/updated GGUF repos. Highlights: DeepSeek-V4-Pro-Qwen3.5-4B-UNCENSORED-heretic-GGUF (26,240 downloads already — the day's runaway community spin), three Qwen3.5 "SoftMoE" experimental quant variants (2B-Tools, 0.8B-HTML, 4B-SMS, 15:00–16:00 ET), Gemma-SEA-LION-v4-27B (AI Singapore) and Gemma-3-Fornax-V3-12B-QAT-CoT GGUFs, plus roleplay/steady-state ladders (Dungeonmaster-V2-LLaMa-70B, KangalKhan-HardRuby-7B, Fireball-R1.1-8B). New non-i1 GGUF tiers also landed for Sijo-1.7B, MEMPHIS and SmolLM3-3B-Instruct-heretic — first tiers for those three were reported by the 12:45 fire (updates).
*Why it matters:* The SoftMoE trio is the interesting signal — quantizers are now experimenting with soft-mixture-of-experts routing variants on consumer quants, and the 4B DeepSeek-V4-Pro heretic's download velocity shows where hobbyist attention is going.
*Local inference:* All GGUF — Q4_K_M class runs the 4B on any 6–8 GB card, the 27B Ladder on 16 GB (5060 Ti) at Q3/IQ4 tiers, 70B repos target 24–48 GB or offload rigs.
- Source: Hugging Face — https://huggingface.co/mradermacher/DeepSeek-V4-Pro-Qwen3.5-4B-UNCENSORED-heretic-GGUF (and sibling repos under https://huggingface.co/mradermacher)

## Inference software and benchmarks

**BigMoeOnEdge: any-MoE adaptive expert-streaming optimizer on stock llama.cpp — 35 t/s Qwen3.6-35B on an 8 GB card (author-run)** — event date: November 15, 2026 (rL post 15:11 ET; repo active same day)
Author dai_app unveiled benchmarks for Helldez/BigMoeOnEdge (878 stars, pushed 10:45 ET today): it takes any MoE GGUF llama.cpp can load — no repacking — and adaptively streams/caches experts (single hot experts or whole layers, chosen per hardware) behind a local OpenAI-compatible server with an --auto mode. Claimed: Qwen3.6-35B-A3B at ~35 t/s on an 8 GB card vs colibri ~23 and Strata failing to boot at 16K ctx; ~45 t/s on 12 GB vs Strata ~30; 5 s to first token on a long prompt (vs Strata 14 s, colibri ~2 min); ~20 t/s on a 16 GB MacBook; Qwen3.8-Flash-Next 125B at 10 t/s on Mac vs Strata ~7. On the two RTX 5060 Ti boxes Strata was still 1.3x faster where it's tuned. Numbers are author-reported, single-user, and the author is promoting his own project — treat as promising, not verified.
*Why it matters:* Runs on *stock* llama.cpp — a discussion is already open upstream (ggml-org discussion #31876) about folding the scheduler in, which would lift the whole ecosystem's MoE-on-small-VRAM story.
*Local inference:* Targets exactly this brief's hardware class: 8–16 GB cards, Macs, even Android/iOS (one commenter serves Qwen3.6-35B-A3B at 20+ t/s on a RedMagic phone).
- Source: r/LocalLLaMA (u/dai_app), Nov 15, 2026 15:11 ET — https://www.reddit.com/r/LocalLLaMA/comments/1xx5fng/bigmoeonedge_outperforms_strata_and_colibri_on/
- Source: GitHub — https://github.com/Helldez/BigMoeOnEdge

**1Cat-vLLM (V100/SM70 fork) vs Strata on 4x V100-16GB: up to 142 t/s decode, 2.7K t/s prefill at TP4 over PCIe 3** — event date: November 15, 2026 (rL post 12:49 ET)
u/Acceptable_Bell_1791 benchmarked Qwen3.8-Flash-Next UD-IQ3_XXS on a 4x Tesla V100 16GB rig (PCIe 3 x16, EPYC 7543, 256GB DDR4): 1Cat-vLLM hit 124.9–142.6 t/s decode across 2.6K–96K-token prompts vs Strata's 74.8–79.3, with prefill up to 2,751 vs 3,384 t/s (Strata ahead only at 96K). Repeated same-prompt runs showed 1Cat climbing +24% over three runs while Strata fell +10%→−; the author attributes consistency to 1Cat's expert-caching (added ~a month ago) and notes GPUs held 96–99% utilization. 1Cat-vLLM v1.5.3 shipped Nov 14.
*Why it matters:* Volta was written off for modern LLM serving; a maintained fork squeezing 2x decode from $150 cards changes used-GPU economics for multi-GPU homelabs, and the expert-caching work may upstream to vLLM.
*Local inference:* 4x V100-16GB (64GB pool) + 256GB DDR4 runs Flash-Next IQ3_XXS at agent-usable speeds — a budget alternative to the 2x5060 Ti/Strata path.
- Source: r/LocalLLaMA (u/Acceptable_Bell_1791), Nov 15, 2026 12:49 ET — https://www.reddit.com/r/LocalLLaMA/comments/1xx1u9y/1catvllm_vs_strata/
- Source: GitHub (1531 stars, v1.5.3) — https://github.com/1CatAI/1Cat-vLLM

**llama.cpp b12421–b12426: day-0 mtmd support for Higgs v3 STT, VibeVoice-ASR and ZONOS3-mini** — event date: November 15, 2026 (releases 13:14–16:05 ET, GitHub-verified)
Six builds in three hours: #32329 (15:25 ET) adds mtmd multimodal support for Higgs v3 STT and Microsoft VibeVoice-ASR; #32020 (13:44 ET) adds ZONOS3-mini TTS (day-0 engine support for Zyphra's 100M-active real-time TTS, previously footnoted Nov 11 — update); #32349 (13:45 ET) reads the qwen3vl output-head size from tensors.
*Why it matters:* Three new speech modality entry points (STT x2 + TTS) landing in the reference local stack the same day their upstream weights/tools matured keeps llama.cpp the default substrate for offline voice pipelines.
*Local inference:* All three run on the ggml backends the projects already support — small enough for 8–16 GB cards and CPUs; ZONOS3-mini is real-time on modest hardware per its paper.
- Source: GitHub releases (b12421 13:14 ET → b12426 16:05 ET) — https://github.com/ggml-org/llama.cpp/releases

**vLLM + Ollama in-window merges: DSv4.1-Flash prefill context-parallel on ROCm; CUDA-graph warmup cap; Windows iGPU pinned-memory fix** — event date: November 15, 2026
vLLM merged #66842 (16:49 ET): eager prefill context parallel enabled for DeepSeek-V4.1-Flash on ROCm — multi-GPU AMD long-prompt prefill; and #67469 (17:01 ET): caps warmup and CUDA graph allocations to max_num_seqs x max_model_len, stopping OOM-on-start for long-context configs. Ollama merged #19636 (13:46 ET): keeps model weights out of pinned host memory on Windows iGPUs — fixes double-residency stalls on AI PC laptops. TensorRT-LLM had no in-window merges.
*Why it matters:* The CUDA-graph cap removes a chronic "runs at 8K, dies at 128K" failure mode; the Ollama fix directly improves the Intel/AMD iGPU laptop class this brief watches.
*Local inference:* ROCm multi-GPU DSv4.1-Flash serving, long-ctx vLLM on consumer cards, and 16GB iGPU laptops with shared RAM all gain.
- Source: GitHub — https://github.com/vllm-project/vllm/pull/66842 · https://github.com/vllm-project/vllm/pull/67469 · https://github.com/ollama/ollama/pull/19636

**SGLang afternoon wave: AMD ring all-reduce + unrolled 2shot_push on gfx1250** — event date: November 15, 2026
#49955 (13:27 ET) adds a custom ring all-reduce and an unrolled 2shot_push variant to SGLang's AMD custom-all-reduce path for gfx1250 (Instinct MI350/MI355-class), complementing this week's UALink/MI450 work. Supporting merges: #49759 tokenizer_manager input validation at request entry (16:50 ET), plus CI hardening — sgl-router built into the ROCm image and launched in the MI355X PD nightly (#50604), thin-LTO Rust extensions (#50606), fork-token isolation (#50603).
*Why it matters:* All-reduce is the dominant multi-GPU comm cost for tensor-parallel decode on AMD; vendor-specific kernel variants in the fast path move ROCm toward feature parity with NCCL-tuned NVIDIA serving.
*Local inference:* AMD multi-GPU workstation/server owners (gfx1250) get lower TP decode latency; CI coverage means the MI355X recipes stay tested.
- Source: GitHub — https://github.com/sgl-project/sglang/pull/49955 (wave: /pull/49759, /pull/50604, /pull/50606, /pull/50603)

## Also noted

- **audio.cpp 0.9.5** now ships a real-time local avatar-chat app in its WebUI: SoulX-FlashHead talking-head + OWSM v4 ASR + LFM3-Audio dialogue/TTS, fully on-device (r/LocalLLaMA 13:29 ET) — https://www.reddit.com/r/LocalLLaMA/comments/1xx2vi0/audiocpp_soulxflashhead_lfm3audio_owsm_v4/
- **inside-llm**: open-source browser tool that loads two GGUF quants side-by-side via llama.cpp and visualizes per-layer divergence during generation (r/LocalLLaMA 16:46 ET) — https://www.reddit.com/r/LocalLLaMA/comments/1xx7qbf/i_have_created_a_tool_to_observe_what_happens/
- **webmodels.dev**: proposed browser API for enumerating and running local AI models from web apps (HN) — https://news.ycombinator.com/item?id=50510431
- A **DGX Spark** was shown as a self-hosted GitHub-Actions-style daily pipeline host: Qwen3.6-35B-A3B on vLLM + Qwen-Image on ComfyUI, five generated "quests" a day on 121 GB unified memory (r/LocalLLaMA 13:50 ET) — https://www.reddit.com/r/LocalLLaMA/comments/1xx3ek2/i_run_a_daily_isekai_guessinggame_pipeline_on_a/

## Just missed the window

- **MSI confirms RTX Spark EdgeMesa N AI+ pricing — $4,699 (64GB) / $7,999 (128GB)** (VideoCardz, Nov 15 05:04 ET, before this run's window): the 64GB MSI matches DGX Spark Founders Edition's $4,699 MSRP which carries 128GB + 4TB; first real price check on the RTX Spark N1X mini-PC class — https://videocardz.com/newz/msi-confirms-7999-price-for-rtx-spark-mini-pc-with-128gb-ram
- **SK hynix orders HBM4 equipment for +20,000 wafers/month** (TechPowerUp via ETNews, Nov 15 11:37 ET): P&T6 Cheongju back-end tools (bonding/dicing/inspection), H1 2027 install, on a ~150K wpm base; driven by AMD/Broadcom demand beyond NVIDIA, and eased by NVIDIA's Rubin HBM4 pin-speed cut to 9.6 Gbps — https://www.techpowerup.com/354861/sk-hynix-bets-on-hbm4-demand-beyond-nvidia-with-new-equipment-orders
- **Judge denies Samsung/SK hynix/Micron motion to dismiss DRAM price-fixing class action** (order Nov 14; TechPowerUp Nov 15 13:13 ET): court finds alleged coordination of production cuts and HBM capacity shift plausibly pleaded; cites 697% DRAM price rise over 18 months — https://www.techpowerup.com/354863/samsung-sk-hynix-and-micron-fail-to-get-dram-antitrust-suit-dismissed
- **Newegg restocks RTX 5090s at $6,599–$6,999 (3.3x MSRP)** (TechPowerUp, Nov 14 19:04 ET, fell between fires): AORUS Xtreme Waterforce $6,599.99; Astral $6,249.99 sold out same day — https://www.techpowerup.com/354841/neweggs-rtx-5090-restock-starts-at-usd-6-599-more-than-triple-the-msrp

## Coverage notes

Verified count: **9 qualifying items + 4 "also noted" one-liners + 4 "just missed the window" footnotes**, each from a fetched page with an in-window (or honestly labelled pre-window) timestamp; GitHub/Hub timestamps are API-primary. Ledger dedupe: **9 candidates suppressed as already reported or pre-window** — Garlic LLM PS4 (re-covered by VideoCardz; reported Nov 13), Blackwell checkpoint-forensics (Register re-coverage; Nov 12), HPE Cray E2000 (Register re-coverage; Nov 13), on-prem HPC +20% (re-coverage of Nov 3 Gartner item), NVIDIA circular-financing (re-coverage of Nov 14 `missed:` entry), Adrenalin 26.11.1 / Radeon SSD-write fix (re-surfaced; Nov 11–12), "Siren Halo" GDDR7 APU (Nov 12), "Stand with Jensen" petition (100K reported yesterday; 115K today is growth, not new), Basalt v0.4.0 (reported by today's 12:45 fire). Villanova2 excluded (weights Oct 26–Nov 9). Dead ends: web_search backend was down all run (DuckDuckGo connect errors) — pivoted to Bing News RSS + index pages; The Register AI index failed twice (two-strike rule, homepage river used instead); VideoCardz RSS feed failed once; r/LocalLLaMA search 403'd (sub/new fallback used). No MLPerf activity on a Sunday. bartowski/Unsloth/DavidAU: no in-window uploads.
