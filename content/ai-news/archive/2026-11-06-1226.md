# AI News Briefing — Friday, November 6, 2026 (12:00 ET Edition)

**Window:** 2026-11-06 07:00 → 12:01 EST (previous fire → now). New items only; events disclosed before 07:00 EST appear only in footnotes.

## TL;DR
- **Qwen 4 is officially teased.** Alibaba's official Qwen account posted "Qwhen? Qsoon!" at 07:00:18 EST with a Zhuangzi "great fish" poster — the first official Qwen 4 signal; no weights or date yet, and the community expects the Qwen4-27B/Max/Plus/Flash lineup next.
- **The EU's first-ever move against a recommendation algorithm landed today:** the European Commission preliminarily found TikTok's "For You" feed violates the DSA by trapping users in harmful "rabbit holes," with fines up to 6% of global revenue possible.
- **TSMC is weighing a push into 3D DRAM at Nvidia's request** (The Information), the latest twist in the AI memory shortage.
- **A Democratic Congress is lining up tougher scrutiny of Nvidia** on chip exports and circular financing, with Elizabeth Warren a top contender to chair Senate Banking (Bloomberg).
- **Inference-stack churn:** vLLM took 20 in-window merges (LFM3-Audio support, GLM-5.3 FP8 MoE TMA kernels up to 1.6x); llama.cpp shipped b12170–b12176 including an invalid-UTF-8 crash fix.

---

## Models and local inference

**Alibaba officially teases Qwen 4 — "Qwhen? Qsoon!"** — Event date: Friday, Nov 6, 07:00:18 EST.
The official @Alibaba_Qwen X account published a teaser post at 07:00:18 EST (12:00:18 UTC) reading "Qwhen? Qsoon!" alongside an artwork panel referencing the Zhuangzi parable of the giant fish Kun (whose Chinese pronunciation puns on "Qwen") transforming into the bird Peng — literally birds flying in the shape of a fish. The r/LocalLLaMA front page erupted (two separate threads in the day's top posts), with community screenshots of an accompanying announcement referencing Qwen 4 sizes and future "Qwen 4.5 and Qwen 5" scale. No weights, model card, or release date exist yet — this is a confirmed-not-yet-released status as of the window close. Context: trackers reported (FlexAI, Oct 29) a planned early-November lineup of Qwen4-Max, Plus, Flash and 27B, and Alibaba's CTO was reported to have targeted the week of Nov 2.
*Why it matters:* the Qwen open-weight family is the default engine of the consumer-GPU scene; an imminent Qwen 4 is the highest-impact pending release for local inference, and an official teaser — after the quiet Apsara (Sept 22) naming of a Qwen4-27B — means download-prep time, not speculation time.
Source: X — @Alibaba_Qwen "Qwhen? Qsoon!" teaser, https://x.com/Alibaba_Qwen/status/2118674834632978755 (posted 2026-11-06 12:00:18 UTC, timestamp verified via post-ID decode)
Source: Reddit — r/LocalLLaMA "Qwen 4 is stealth announced," https://www.reddit.com/r/LocalLLaMA/comments/1xpbjku/qwen_4_is_stealth_announced/ (Nov 6, day-top)
Background: FlexAI — "Qwen4 Release Date and Models," https://flex.ai/blog/qwen4-release-date-and-models (Oct 29)

**TSMC weighs 3D DRAM manufacturing at Nvidia's request** — Event date: Friday, Nov 6 (The Information, 10:05 EST on the Techmeme river).
The Information reports, citing two people with direct knowledge, that TSMC has begun formulating plans to manufacture advanced memory — specifically vertically stacked "3D DRAM" — at the request of its biggest customer, Nvidia. The push is led by TSMC vice president Hsu Kuo-chin and is weighing building new fabs versus acquiring and converting existing plants; production timelines remain unclear. TSMC and Nvidia did not immediately respond to Reuters' requests. TSMC shares rose on the report.
*Why it matters:* it extends the ledger's HBM/flash squeeze (Samsung's HBM sold out through next year; hyperscaler demand quintupled flash prices) into logic-manufacturing territory — the world's most advanced fab entering the memory business reshapes the supply picture that sets GPU and laptop prices, including the DRAM floor Altman already called "quite terrible" for home PCs.
Source: The Information (via Techmeme river, Nov 6 10:05 AM ET), https://techmeme.com/river
Source: Reuters summary via piqmarkets — "TSMC weighs 3D DRAM memory production at Nvidia's request," https://piqmarkets.com/story/tsmc-weighs-3d-dram-memory-production-at-nvidia-s-request-the-information-reports (Nov 6)
Source: Yahoo Finance — "TSMC rises on report of potential push into advanced memory," https://finance.yahoo.com/technology/ai/articles/tsmc-rises-report-potential-push-151300006.html (Nov 6)

**vLLM takes 20 in-window merges; llama.cpp ships b12170–b12176** — Event date: Friday, Nov 6, 07:00–12:00 EST (GitHub).
vLLM merged 20 PRs inside the window, headlined by LFM3-Audio model support (#65828), expanded FP8 MoE TMA kernels for GLM-5.3 on sm100 claiming 1.03–1.60x kernel gains (#65746), fused GDN MTP speculative decoding supporting DSpark K=8 and using idle SMs at small batch sizes (#65874), a standalone-draft-model prefill-as-decode fix (#65804), and a security PR raising patched runtime and test-dependency floors (#63574). llama.cpp tagged seven builds (b12170–b12176) whose master commits fix a crash on invalid UTF-8 in prompts or files (#31563) plus CI/test hardening. SGLang (v0.5.23, Nov 2), Ollama (v0.40.15, Nov 3) and OpenClaw (v2026.11.1, Nov 5) were quiet in-window — all already reported.
*Why it matters:* the spec-decode and FP8-MoE work targets exactly the small-batch, consumer-adjacent serving regime where this reader's fleet lives.
*Local inference:* DSpark K=8 idle-SM utilization and draft-prefill handling matter for single-GPU rigs; sm100-specific kernels are datacenter-targeted.
Source: GitHub — vLLM merged PRs (API-verified), https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged
Source: GitHub — llama.cpp releases, https://github.com/ggml-org/llama.cpp/releases (b12170–b12176 published 13:50–16:39 UTC Nov 6)

## Agent tools

**Nace AI publishes full Drex decision-model technical report in r/LocalLLaMA AMA** — Event date: Friday, Nov 6 (post is the newest listing on r/LocalLLaMA at ~12:00 EST).
Nace AI's technical staff ran an AMA announcing they "just published a full technical report" on Drex, their family of four open decision models that answer typed questions with per-option probabilities in a single forward pass (no autoregressive decoding): Drex DLM (8B diffusion-LM backbone), Drex v1.5 (9B Gated DeltaNet hybrid), Drex v2.0 (35B MoE, 3B active) and Drex Max (27B dense with vision). Claims: top 3 on OpenRouter and category-top on five Artificial Analysis leaderboards; weights already exist as HF repos with Q8_0 GGUFs and dedicated llama.cpp/Ollama forks. Treat as vendor-reported — the AMA post itself drew an AI-generated-content detector flag in replies, and a commenter challenged the dataset-release claim ("Nothing relevant looking in github or hf").
*Why it matters:* decision models are the cheap routing/verification layer agents increasingly offload to local hardware; a published training playbook (architecture, datasets, evals, inference) from a top-ranked provider is reusable material.
*Local inference:* the 8B–35B-A3B tiers with Q8_0 GGUFs are consumer-GPU sized and the stack already ships llama.cpp/Ollama forks.
Source: Reddit — r/LocalLLaMA AMA, https://www.reddit.com/r/LocalLLaMA/comments/1xphq4u/ama_how_we_trained_a_sota_decision_model_got_top/ (Nov 6)
Source: GitHub — nace-ai/drex-decision-models, https://github.com/nace-ai/drex-decision-models (family, GGUFs, forks)

## AI security and governance

**EU preliminarily finds TikTok's "For You" algorithm violates the DSA** — Event date: Friday, Nov 6 (Commission press release + The Record, 7:10 AM ET).
The European Commission issued preliminary findings that TikTok breaches the Digital Services Act through the "rabbit hole effect" of its recommender: the more a user engages, the more the feed serves content around sadness, isolation, self-harm and body image ("SkinnyTok," "what I eat in a day"). Officials called it the first time EU regulators have directly challenged a major platform's engagement-based recommendation system — and, to their knowledge, the first enforcement action of its kind worldwide. The Commission says TikTok's own countermeasures (feed diversification, refresh, "not interested") are ineffective, wants recommendations anchored in explicit user intent rather than implicit dwell-time signals, and analyzed ~20,000 videos with psychologist support. TikTok faces fines up to 6% of global annual revenue if confirmed; this is the Commission's third set of TikTok preliminary findings this year, after February (addictive design) and July (minors' account safety).
*Why it matters:* regulating the engagement-optimization objective itself — not illegal content — is a new enforcement class that every AI personalization and agent-recommendation system will be measured against, and officials said they are "looking more widely beyond TikTok."
Source: The Record — Alexander Martin, "EU accuses TikTok of trapping users in harmful 'rabbit holes'," https://therecord.media/eu-accuses-tiktok-of-rabbit-hole-DSA-violation (2026-11-06, full text read)
Source: European Commission — "Commission preliminarily finds TikTok's 'For You' feed in breach of the DSA for harmful 'rabbit holes' effect," https://digital-strategy.ec.europa.eu/en/news/commission-preliminarily-finds-tiktoks-you-feed-breach-digital-services-act-harmful-rabbit-holes (published 6 November 2026)

**Democrats promise tougher Nvidia scrutiny on chip exports and circular financing** — Event date: Friday, Nov 6 (Bloomberg, 10:51 AM ET).
Bloomberg reports that Nvidia faces tougher congressional scrutiny as the incoming Democratic majority prepares to demand answers from CEO Jensen Huang about export-control compliance, with chip-export enforcement and the circular-financing loop (vendor credit funding its own customers) expected to headline hearings; Elizabeth Warren is a top contender to chair Senate Banking. The piece compounds this week's pressure: House GOP chairs already demanded a Nov 18 closed-door Huang briefing over Megaspeed ties, and the post-midterm AI sell-off has politics priced in.
*Why it matters:* export enforcement scrutiny and circular-financing disclosure are the two levers most likely to reshape the GPU supply and AI-financing stack this reader's work sits on.
Source: Bloomberg — Maggie Eastland, "Nvidia Faces Congressional Scrutiny Over AI Chip Sales," https://www.bloomberg.com/news/articles/2026-11-06/nvidia-s-ai-chip-exports-emerge-as-target-in-democratic-congress (Nov 6; snippet-level — paywalled)
Source: Techmeme river (Nov 6 10:51 AM ET), https://techmeme.com/river

---

## Just missed the window (footnotes — first and only time)
- **Nvidia makes GPU-location tracking mandatory for Southeast Asia cloud partners** (Bloomberg 06:30 EST, 30 min pre-window; Tom's Hardware re-coverage in-window) — Fleet Intelligence enrollment now required for Nvidia Cloud Partner status, aimed at China-bound smuggling. https://www.tomshardware.com/tech-industry/big-tech/nvidia-tightens-tracking-of-ai-gpus-in-southeast-asia-as-chinese-chip-smuggling-draws-scrutiny-report-claims-cloud-providers-must-join-monitoring-program-to-qualify-for-official-partner-status
- **Tencent's Hy4-preview Apache-2.0 weights surfaced on Hugging Face** (secondary coverage Nov 5; in-window HF API shows 780B safetensors, SGLang `hy4-preview` docker tag — but repo lastModified dates conflict (Aug 28), so the go-public moment is unverifiable). https://huggingface.co/tencent/Hy4-preview
- **Baidu released ERNIE-5.5-Flash-Base**, 280B sparse MoE / ~14B active, 33.5T pre-training tokens, 128K context (1M for fine-tune) — disclosed Nov 5 (ernie.baidu.com).
- **Z.ai chief scientist Jie Tang detailed the GLM-5.5 training pipeline** (32B, 122B, 256B variants) at a lecture — X posts Nov 5 10:52 EST; update on the ledger's Oct 31 GLM-5.5 rumor line.
- **METR's Ryan Greenblatt pegged AI software-R&D acceleration at ~4.5x, then corrected to 3.8x** (X post Nov 6 01:07 EST, pre-window; follow-on to yesterday's reported 1.9x AECI finding). https://x.com/RyanGreenblatt/status/2118585948015603868
- **Perplexity rolled out Teammates**, an always-on agent system integrating with Computer for simultaneous multi-role task execution (The Verge, Nov 5 10:15 PM ET).
- **DOJ launched the "Super Intelligence Task Force"** (justice.gov press release dated Nov 5; surfaced today via AI Weekly) — prosecutorial unit for AI misuse, co-chaired by Associate AG Stanley Woodward; concretizes the administration's Oct 27 "Super Intelligence" renaming. https://www.justice.gov/opa/pr/department-justice-launches-super-intelligence-task-force
- **Check Point showed ChatGPT's Lockdown Mode was bypassable** on the Electron desktop app via nested sandboxed iframes with popups-to-escape-sandbox permissions, exfiltrating full chat histories; OpenAI quietly removed the permissions Sept 18 (research published Nov 5; gbhackers).

## Coverage notes
- **Verified and reported: 7 items** (3 leads, 2 governance/industry items, 2 repo/community also-noted), plus 8 first-time "just missed" footnotes. Every in-window item's URL was fetched; lead stories read in full except the paywalled Bloomberg (snippet + Techmeme corroboration, explicitly labeled).
- **Ledger dedupe: 7 candidates suppressed as already reported** — Gemini Omni 1.2 Flash HN front-page bump (`missed:gemini-omni-1-2-flash-ga`, already footnoted 07:00), Ask HN Ubuntu CVE-2026-98432 delay (repeat of once-footnoted `missed:cve-2026-98432`), NYT Microsoft-60k-buyouts follow (ledger `msft-60k-buyouts`), Microsoft Foundry Local Qwen3.8-27B aiweekly re-syndication (ledger `foundry-local-agentic`, 07:00), Nikkei Asia rout continuation (ledger `nasdaq-2.5t-blue-wave` Nov 6 continuation), "Banks block AI agents" AI News Nov 6 (re-coverage of once-footnoted `missed:axios-banks-ai-agents`), Block second layoff round (off-scope/already-covered adjacent).
- **Outlets used:** The Record, European Commission, The Information (via Techmeme/Reuters summaries), Bloomberg, Yahoo Finance, GitHub API (llama.cpp/vLLM/SGLang/OpenClaw), Hugging Face API, r/LocalLLaMA, HN Algolia, X (post-ID decode), justice.gov, Techmeme, AI Weekly — well past the ≥5-outlet requirement.
- **Dead ends:** guessed The Record slug 404'd (recovered via exact-headline search, no extra budget); Hy4-preview HF commit dates self-contradictory (Aug 28 uploads vs Nov 5 press coverage — left as footnote); Qwen4 "sizes" claim rests on community screenshots, stated as such; ByteDance SAE-interprets-MoE paper (AGI Hunt dates akhaliq's post Nov 5) excluded as out-of-window rather than footnoted; execute_code blocked in cron again — all parsing done via scratch script files per skill guidance.
