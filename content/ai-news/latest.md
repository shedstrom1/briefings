# AI News Briefing — Thursday, November 12, 2026 · 12:00–17:00 ET

**Window:** 2026-11-12 12:00 → 17:00 EST (previous fire 12:00 → this fire 17:00). New-items-only; deduped against the reported ledger.

## TL;DR

- **Google launches Gemini 4 Hydrogen** (closed weights): the "Flash" successor sits at AA Intelligence Index 51 (54 at xhigh) — above Gemini 4 Argon at its default tier — priced $1.50/$7.50 per M tokens flat across a 1M-token context, rolled out across AI Studio, Gemini API, Search, the Gemini app, Sheets, Antigravity and Gemini for business.
- **A "checkpoint forensics" report claims six Chinese frontier models were initialized on export-controlled NVIDIA Blackwell** — DeepSeek V4-Flash/V4-Pro, Kimi K3/K3.1-Pro, MiniMax-M3 and Ant Ling-3.1-flash — using numerical weight fingerprints, corroborating prior US official allegations with published technical evidence.
- **The Nvidia cascade keeps rolling in-window**: Reuters exclusive on a relationship with a second employee; AG Todd Blanche says DOJ has "not reached a conclusion" on Alice Huang; White House reportedly discussing pushing out Commerce Secretary Lutnick.
- **SpaceX announces >$74B in two AI supercomputing campuses** (≈$50B Cochise County, AZ; >$24B Starbase, TX); **Cerebras beats Q3** (core revenue $230.9M, +70% YoY, new Microsoft deal, FY guide raised, +18% AH).

---

## Models and local inference

### Google launches Gemini 4 Hydrogen, retiring "Flash" branding for the mid-tier (event: Nov 12, ~1:40 PM ET)

Google's DeepMind unveiled Gemini 4 Hydrogen, positioned as the fast execution model between Carbon and Argon: 1M-token context (input and up to 1M output), $1.50/M input and $7.50/M output priced flat across the whole window, and a global rollout the same afternoon across AI Studio, the Gemini API, Android Studio, Gemini for business, the Gemini app (free users via Auto), AI Mode in Search, Sheets, and paid Pro/Ultra in Antigravity. Google claims it beats Gemini 4 Argon on the Artificial Analysis Intelligence Index at its default high thinking level (51 vs 50; 54 at xhigh), triples Gemini 3.8 Flash's Terminal-Bench 4.1 score (57% vs 18.2%), runs tasks 2.5x faster at high, and cuts fabricated claims by more than 5x. Safety-side claims: up to 81% less agentic overreach, 12x fewer misaligned actions/shortcutting, 96.7% on an internal agent-honesty eval, 2.3x more resilient to Gray Swan indirect prompt injection, 99.9% chain-of-thought legibility over 200k+ audited turns.

**Why it matters:** Hydrogen collapses the "cheap workhorse" slot onto the Gemini 4 family and undercuts the Flash-era price ladder at exactly the moment multi-model routing (see Orchestrator below) is becoming the cost-control pattern; 51 default-index at $1.50/$7.50 is aggressive against 3.8 Flash and MiniMax-M3.1-Flash-class pricing.
Source: Introducing Gemini 4 Hydrogen — blog.google (Koray Kavukcuoglu), Nov 12, 2026 — https://blog.google/innovation-and-ai/models-and-research/gemini-models/introducing-gemini-4-hydrogen/
Source: Google announces Gemini 4 Hydrogen with 'significant leaps' in speed, intelligence — 9to5Google (Abner Li), Nov 12, 2026, 10:01 AM PT — https://9to5google.com/2026/11/12/gemini-4-hydrogen-launch/
Source: Gemini 4 Hydrogen Hits Google's Price List at $1.50/$7.50 — Four Week MBA (model id gemini-4-hydrogen, 1,048,576-token ctx) — https://fourweekmba.com/ai-gemini-4-hydrogen-hits-googles-price-list-at-1-50-7-50/

### Update on previously reported (ledger Nov 5: Inkling-2-Small) — Thinking Machines releases Inkling 2 Small open weights (event: Nov 12, afternoon ET)

The Apache-2.0 weights promised for "next week" landed today on Hugging Face (thinkingmachines/Inkling-2-Small): 594B total / 17B active parameters, 48-layer decoder with 8-of-384-expert sparse MoE, hybrid local/global attention, native image input, 1M-token context. Two checkpoints ship: native-precision NVFP4 (requires ≥500 GB aggregated VRAM — e.g., 2x B300 or 4x H200) and MXFP8 (≥800 GB, marginal 1–2 pp eval gain). Day-0 support landed in-window: vLLM merged Inkling 2 Small recipes #67319/#67322 at 4:26/4:41 PM ET, SGLang merged cookbook recipes #50185 at 4:02 PM ET, plus TokenSpeed and Unsloth recipes; API availability at NVIDIA, Fireworks, Together, Baseten, Nebius, Modal and Cloudflare. AA Index (v5.0) 30.6 — competitive with DeepSeek-V4.1-Flash-1017's 32.6 in the open-weights flash tier.

**Why it matters:** First from-scratch Apache-2.0 release from Thinking Machines, delivered on the promised timeline — a credibility marker for the company's "safe path to open weights." The 500 GB VRAM floor means local users wait on community quants (Unsloth day-0 suggests they're coming).
Source: Introducing Inkling 2 Small (Nov 12, 2026 weights update banner) — thinkingmachines.ai, Nov 5 updated Nov 12, 2026 — https://thinkingmachines.ai/news/inkling-2-small/
Source: thinkingmachines/Inkling-2-Small model card — Hugging Face, Nov 12, 2026 — https://huggingface.co/thinkingmachines/Inkling-2-Small

### Cerebras posts Q3 beat with new Microsoft deal (event: Nov 12, ~4:00 PM ET, after close)

Record GAAP revenue $210.8M (+55% YoY; core revenue $230.9M, +70%, vs $214.7M expected), GAAP cloud revenue +265% YoY, net loss widened to $161M, FY26 core-revenue guidance raised to $940–960M with a stated expectation to triple revenue in 2027; the company touted a new deal with Microsoft. Shares +18% in extended trading.

**Why it matters:** Wafer-scale inference is converting AI-inference demand into hypergrowth faster than GPU clouds — Microsoft's name on the customer list is the credibility unlock for the second listed wafer-scale vendor.
Source: Cerebras Systems Delivers Record Revenue in Third Quarter 2026, Raises Full-Year Outlook — cerebras.ai press release, Nov 12, 2026 — https://www.cerebras.ai/press-release/cerebras-systems-delivers-record-revenue-in-third-quarter-2026-raises-full-yea-3402195
Source: Cerebras Q3 2026 earnings results — MarketBeat, Nov 12, 2026 — https://www.marketbeat.com/earnings/reports/2026-11-12-cerebras-systems-inc-stock/

### Also noted (Models and local inference)

- **Community sighting [unofficial]:** r/LocalLLaMA posted (3:13 PM ET) an "Ernie 5.5 Flash Preview" leaderboard entry — 280B total/14B active — scoring near DeepSeek-V4.1-Flash coding level on a 12-benchmark coding aggregate; follows Baidu's official Nov 11 "two weeks" statement (ledger ernie-5.5-flash-2wk); unverified, single community post — https://www.reddit.com/r/LocalLLaMA/comments/1xune1i/new_model_ernie_55_flash_preview_hits_strongly_at/
- **SpaceX commits >$74B to two AI supercomputing campuses** (Reuters, Nov 12, 4:15 PM ET): ~$50B in Cochise County, AZ (needs county approval; 5,500 construction/1,000 permanent jobs; dry cooling; a 2,400-signature anti-permit petition and December hearing) and >$24B at Starbase, TX (self-powered; gas plant + 444 acres per city notice; Dec 9 tax-abatement vote). AI already generated >half of SpaceX's Q3 revenue post-June IPO — https://www.reuters.com/business/media-telecom/spacex-invest-over-74-billion-supercompute-facilities-texas-arizona-2026-11-12/ (also: NYT — https://www.nytimes.com/2026/11/12/business/spacex-data-center.html)
- **Cisco raises annual revenue forecast on strong AI networking demand** (Reuters, Nov 12, 4:15 PM ET) — https://www.reuters.com/business/media-telecom/cisco-raises-annual-revenue-forecast-strong-ai-networking-demand-2026-11-12/
- **Engines, 12:00–17:00 ET:** llama.cpp 3 in-window commits (Hexagon clang-cl Windows-ARM64 host build #32011; llama/ggml prune #30565/#29801; a dedicated Intel Arc Vulkan prefill FA kernel landed on master per r/LocalLLaMA). vLLM ~20 merges: Kimi K3.1 Pro split 5/6 (ROCm DCP, P/D, indexer top-k) #65685, RDNA MXFP4 triton_kernels on ROCm 3.8.x #64331/#64055, spec-decode PP single-stage drafts #66063, GLM-5.3-Flash HiSparse #61243, FlashInfer 0.7.4, NVFP4 activation-fusion expansion #65149. SGLang 9 commits incl. sgl-router configurable body cap #50161 and an API-key application fix for tokenizer workers/WebSocket routes #49894. Ollama quiet (v0.40.17-rc0 since Nov 10).

## Agent tools

### Google ships Orchestrator in Antigravity: automatic mid-session model switching (event: Nov 12, same 1:40 PM ET announcement)

Alongside Hydrogen, Antigravity adds an "Orchestrator" dropdown (Google AI Ultra subscribers): within one session it switches between Carbon and Hydrogen as the trajectory develops — Carbon plans and recovers, Hydrogen implements and runs tests, and either can hand control back when the other hits friction. Google reports near-Carbon task success on Terminal-Bench 4.0 at 24% less quota consumed and 19% faster completion. An Antigravity SDK path offers explicit per-role model assignment instead (Google's open-source issue-triage demo has Hydrogen agents triaging/coding while a Carbon agent takes refactors, features and security checks). Unite.AI frames it against the Nov 10 /swarm preview: /swarm partitions work across agents; Orchestrator partitions it across models in one evolving conversation.

**Why it matters:** Model choice moves from a prompt-time decision to workflow architecture — the first mainstream, vendor-orchestrated multi-model agent runtime, and a direct answer to the quota-burn complaint that dogged frontier-model coding agents.
Source: Gemini 4 Hydrogen and Orchestrator — Google Antigravity blog, Nov 12, 2026 — https://antigravity.google/blog/gemini-4-hydrogen-in-google-antigravity
Source: Google Antigravity Rolls Out Orchestrator for Dynamic Model Handoffs — Unite.AI, Nov 12, 2026 — https://www.unite.ai/google-antigravity-rolls-out-orchestrator-for-dynamic-model-handoffs/
Source: Google's Gemini 4 coding agent now switches models mid-task — The New Stack, Nov 12, 2026 — https://thenewstack.io/gemini-4-hydrogen-orchestrator/

### Also noted (Agent tools)

- **Show HN: Kiln** — WebGPU inference engine positioned as a transformers.js alternative, 2.2x faster median (HN id 50474384, 4:15 PM ET) — https://news.ycombinator.com/item?id=50474384
- **Show HN: Telltale Bot** — detects agentic web traffic at the HTTP layer (HN id 50474320, 4:10 PM ET) — https://news.ycombinator.com/item?id=50474320
- **SCache / MOS** — claimed 32.9k-token O(1) KV cache (22.7 MiB) with a 72B INT4/ASVD engine (HN id 50474618, 5:34 PM ET; claims unverified) — https://news.ycombinator.com/item?id=50474618
- **RealtimeVAD** — end-of-speech detector trading false positives for ~110 ms earlier turn-taking, relevant to voice-agent pipelines (HN id 50474586, 5:30 PM ET) — https://news.ycombinator.com/item?id=50474586

## AI security and governance

### Report: six Chinese frontier models carry numerical fingerprints of export-controlled Blackwell GPUs (event: Nov 12, published afternoon; HN 4:49–4:56 PM ET)

Will DePue's "Checkpoint Forensics" report (Unemployment Labs) says forensic analysis of published weights finds six models from DeepSeek, Moonshot AI, MiniMax and Ant Group — DeepSeek-V4-Flash, DeepSeek-V4-Pro, Kimi K3, Kimi K3.1-Pro, MiniMax-M3, Ling-3.1-flash — contain numerical patterns matching NVIDIA Blackwell initialization (B200/B300, 148 SMs; GB200/GB300, 152 SMs), while predecessors from the same labs match Hopper. The method recovers weight-initialization traces and searches for hardware/init configurations that reproduce them; matches for DeepSeek-V4-Flash and Kimi K3 were confirmed on physical Blackwell chips. DePue carefully scopes the claim: it supports (does not prove) Blackwell training, identifies no chip location or access path, and doesn't itself establish an export-control violation; AMD alternatives were excluded for V4-Flash and judged very unlikely for the other five.

**Why it matters:** This converts what was previously official allegation (Reuters Feb 2026; OSTP July 2026 on Moonshot) into reproducible public evidence at a moment when Washington is actively debating exactly this (the pending remote-access rule, RASA, and today's Lutnick coverage). Expect vendors and senators to cite it; the technique — auditing released checkpoints for hardware provenance — is a new accountability tool.
Source: Top Chinese Frontier AI Models Carry the Fingerprint of Export-Controlled NVIDIA Blackwell GPUs — Unemployment Labs (Will DePue), Nov 12, 2026 — https://unemploymentlabs.com/checkpoint-forensics
Source: HN discussion of the report and the DePue tweet (ids 50474883 / 50474813, 4:49–4:56 PM ET) — https://news.ycombinator.com/item?id=50474883

### Update on previously reported (ledger mega-speed cascade) — Nvidia fallout: second-employee revelation, DOJ hedge, Lutnick under discussion (events: Nov 12, 1:18–4:25 PM ET)

New in-window developments on the reported Huang saga: (1) Reuters exclusive (2:46 PM ET) that Jensen Huang also had an intimate relationship with a *third* woman — a second, unnamed Nvidia employee, alongside Alice Huang and healthcare VP Kimberly Powell — as the board (now led by first-ever chair Harvey Jones with CFO Colette Kress interim CEO) searches internally and externally; (2) Reuters (1:18 PM ET): senior White House officials are privately discussing whether Commerce Secretary Howard Lutnick should be pushed out amid the scandal — he had defended/stalled Megaspeed curbs per earlier reporting; (3) AG Todd Blanche (4:25 PM ET) said DOJ has "not yet reached a conclusion" on whether Alice Huang was a Chinese spy, though "of course" the FBI is investigating, and declined to comment on Commerce's chip-smuggling probe; (4) Bloomberg reported Nvidia had faced pressure to expand its "AI backstop" guarantees before Huang's exit.

**Why it matters:** Governance risk for the AI supply chain is now concentrated in three simultaneously-moving processes — the Nvidia CEO search, DOJ/Commerce investigations of Megaspeed-era chip flows, and a possible Commerce leadership change — all of which feed export-enforcement posture that the Blackwell-fingerprint report (above) just gave new ammunition to.
Source: EXCLUSIVE: Former Nvidia CEO Huang had a relationship with a second employee, sources say — Reuters, Nov 12, 2026, 2:46 PM ET — https://www.reuters.com/technology/former-nvidia-ceo-huang-had-relationship-with-second-employee-sources-say-2026-11-12/
Source: White House discussing Howard Lutnick's future as Commerce secretary — Reuters, Nov 12, 2026, 1:18 PM ET — https://www.reuters.com/world/china/white-house-discussing-howard-lutnicks-future-commerce-secretary-people-familiar-2026-11-12/
Source: US Justice Department has not concluded Alice Huang was Chinese spy, Blanche says — Reuters, Nov 12, 2026 — https://www.reuters.com/world/china/us-justice-department-has-not-concluded-alice-huang-was-chinese-spy-blanche-says-2026-11-12/
Source: Jensen Huang Third Affair Allegations Revealed — Forbes, Nov 12, 2026 — https://www.forbes.com/sites/alisondurkee/2026/11/12/jensen-huang-had-a-third-affair-with-another-nvidia-employee-report-says/

### Also noted (AI security and governance)

- **Update (ledger anthropic-s1-ant):** Anthropic's IPO is about six times oversubscribed, with bankers reportedly telling prospective investors demand is the strongest of the cycle; pricing Nov 18, ANT trading Nov 19 (Bloomberg, Nov 12, 4:20 PM ET) — https://www.bloomberg.com/news/articles/2026-11-12/anthropic-s-ipo-is-said-to-be-about-six-times-oversubscribed (via Reuters syndication: https://money.usnews.com/investing/news/articles/2026-11-12/anthropics-ipo-is-about-six-times-oversubscribed-bloomberg-news-reports)

---

## Coverage notes

- **Verified and listed:** 12 events (4 lead write-ups + update sections), 22+ source pages fetched or search-verified in-window; every listed URL carries a Nov 12 timestamp or was fetched and read; lead items read in full (blog.google, thinkingmachines.ai, HF model card, unemploymentlabs.com, Reuters ×2, unite.ai, 9to5Google).
- **Ledger dedupe:** 13 candidates suppressed as already reported by prior runs (Huang resignation/Kress/Jones core event 12:00 fire; Nebius Q3; LangChain $300M; Inception Mercury API wind-down; llama.cpp b12319 builds; Samsung Android XR hands-on re-covering today's preorder event; CMP170hx A100 glitch; Qwen4-Max-preview guardrail trace; Raon-1 GGUF PR; Musk-hire-Huang; IMF reassessment; smolmaxxing; libreagi). The Anthropic IPO line is the only repeat-event listing (material new 6x figure). No repeat "Just missed" footnotes (missed: tags already used).
- **Out-of-window, excluded:** Sen. Cotton's CXMT Entity-List letter (Reuters ~early morning, pre-12:00); Mast's sworn-testimony request (11:35 AM ET); Microsoft 365 outage (not AI); Apple Foundation Models framework landing page (undated WWDC-era docs, no fresh announcement); PTC Windchill CVE-2026-126325 (Nov 11 disclosure, non-AI); CISA KEV IBM Aspera additions (Nov 9).
- **Dead ends / limitations:** CNBC's Cerebras URL 404'd on fetch (numbers corroborated via the company PR, MarketBeat, StockTitan 8-K and Panabee instead); Reddit anonymous feed returns no scores (timestamps still present); r/MachineLearning new-page sweep surfaced no in-window news events; ERNIE 5.5 Flash Preview remains a single unofficial community post.
