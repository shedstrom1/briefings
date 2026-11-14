# AI News Brief — Saturday, November 14, 2026, 07:00 ET

**Window:** 2026-11-13 17:00 EST → 2026-11-14 07:00 EST (previous fire → this fire).

## TL;DR
- Amazon has a secret "World Product Graph" plan to crawl and own the world's product data so AI shopping agents keep routing shoppers to Amazon — an internal PR/FAQ targets October 1, 2027 (Reuters exclusive).
- Moonshot AI is deactivating its Cayman Islands holding company and moving investor stakes onto its mainland China entity, clearing the path for a Hong Kong IPO at a reported ~$50B valuation.
- The engine treadmill didn't stop after llama.cpp's v1.0: 4 llama.cpp merges and 4 builds (b12374–b12377), 38 vLLM merges (including a v0.34.0-prep Mistral Large 4 backport), and 51 SGLang merges (a big Rust-server wave) landed overnight.
- The FAA's $875M AI air-traffic tool SMART became the weekend's accidental AI-safety story: a four-and-a-half-hour nationwide ground stop prompted an FBI probe, and SMART's developer says the outage had nothing to do with it.
- Just outside the window, the biggest AI-business story of the weekend was a WSJ report alleging Nvidia engineered the GPU shortage by funding startups that then bought its chips.

## Models and local inference

**Moonshot AI unwinds its Cayman holding company to clear the way for a ~$50B Hong Kong IPO** — Event: Friday, November 13 (WSJ, 5:01 PM ET).
According to The Wall Street Journal, Moonshot AI — the Kimi developer — is deactivating its Cayman Islands holding company and moving investors' USD-denominated stakes to its primary operating entity in mainland China, a full unwind of the variable-interest-entity structure driven by tightened CSRC rules on offshore listing vehicles. A confidential application is already with the Hong Kong Stock Exchange, targeting a Q1 2027 raise of up to $5 billion; the company's last round valued it at roughly $50 billion (up from $31.5B this summer), with annual recurring revenue at about $1B and reportedly on track to double to $2B by December. A data-security probe into Moonshot and DeepSeek hangs over the timeline.
**Why it matters:** For a Chinese frontier lab, Hong Kong is now effectively the only door open — the same week banks shut mainland-China and Hong Kong investors out of Anthropic's IPO. Restructuring away from the Cayman VIE model is a regulator-driven precedent that ripples through every Chinese AI lab planning to list.
- Source: WSJ — "China's Moonshot AI Restructures to Pave Way for IPO" (Nov 13, 2026) — https://www.wsj.com/tech/chinas-moonshot-ai-restructures-to-pave-way-for-ipo-49f09abb
- Source: AI Weekly — "Moonshot AI Unwinds Cayman Vehicle for $50B Hong Kong IPO" (published Nov 13, 23:05 UTC) — https://aiweekly.co/alerts/moonshot-ai-unwinds-cayman-vehicle-for-50b-hong-kong-ipo

**Overnight engine batch: llama.cpp b12374–b12377, vLLM 38 merges (v0.34.0 prep), SGLang 51 merges** — Event: Nov 13 5:00 PM ET → Nov 14 7:00 AM ET (GitHub).
llama.cpp merged the DFlash speculative-decoding refactor (#32016), Hexagon IQ2_XXS + NVFP4 support (#31954), and a Metal mul_mv destination-offset overflow fix (#32221), shipping builds b12374–b12377. vLLM landed 38 merges headlined by a cherry-pick of Mistral Large 4 support onto the v0.34.0 branch (#67568), Kimi-K3.1-Pro MI355X AgentX presets covering all six InferenceX#5043 concurrencies (#67600), DeepSeek-V4/V4.1 DeepGEMM kernel warmup (#64828), and pipeline-parallel NIXL KV pull with the hybrid KV-cache manager (#65336). SGLang merged 51 PRs dominated by its Rust-server wave — HTTP/2 (h2c) serving beside HTTP/1.1 (#50401), MXFP8 KV caches in the direct external linker (#50324), and AMD HiCache batched-async write-backs (#50363).
**Why it matters:** The v0.34.0 cherry-pick signals an imminent vLLM release with first-class Mistral Large 4 support, and the SGLang Rust-server work targets the GIL/latency ceiling that matters most for high-concurrency local serving.
- Source: GitHub — ggml-org/llama.cpp releases b12374–b12377 (Nov 14 00:08–09:46 UTC) — https://github.com/ggml-org/llama.cpp/releases
- Source: GitHub — vllm-project/vllm merged PRs in window (search: merged:2026-11-13T22:00Z..2026-11-14T12:00Z) — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged
- Source: GitHub — sgl-project/sglang merged PRs in window (51 PRs incl. #50401, #50324) — https://github.com/sgl-project/sglang/pulls?q=is%3Apr+is%3Amerged

**FT does the math: SBF's $500M Anthropic stake would be worth ~$37B — but nobody collects it** — Event: Friday night, Nov 13 (FT, Techmeme 11:25 PM ET).
Alameda Research Ventures bought nearly 14% of Anthropic in 2021 for $500M when the lab was worth about $4B; at next week's expected IPO price (bankers weighing above the filed $70–78 range, ~$1.5T) that stake would be worth roughly $37 billion, the Financial Times reports. The FTX bankruptcy estate sold the holding in 2024 for about $1.3B after Bankman-Fried's fraud conviction.
**Why it matters:** A vivid marker of how fast frontier-lab valuations repriced — and free IPO-preview color as Anthropic's Nov 18 pricing approaches (already-covered thread; the FT math itself is the new material).
- Source: AI Weekly (via FT, George Hammond) — "Alameda's $500M Anthropic bet would be worth $37B at IPO" (published Nov 14, 04:56 UTC) — https://aiweekly.co/alerts/alamedas-500m-anthropic-bet-would-be-worth-37b-at-ipo

## Agent tools

**Amazon's secret "World Product Graph": own every product on Earth to defang AI shopping agents** — Event: Friday, November 13 (Reuters exclusive, 8:15 PM ET on the Techmeme river).
An internal PR/FAQ document reviewed by Reuters describes World Product Graph, an initiative in Amazon's Selection & Catalog Systems unit (under VP Robert Tekiela) to gather data on virtually every item for sale anywhere — including by crawling rival retailers' and brands' sites — with an aspirational release date of October 1, 2027. The document says Amazon's own shopping agent (Rufus, now "Alexa for Shopping") produces factually wrong answers on ~83M weekly searches and is expensive to run, so richer structured catalog data would let cheaper models serve it — and, crucially, would compel AI agents acting for shoppers to route through Amazon rather than sites with better data. Coca-Cola and Bayer have signed on to contribute product data, with a P&G trial discussed at CEO level; Amazon's response called it a "draft, outdated" brainstorming document.
**Why it matters:** This is the clearest signal yet that incumbents see agentic commerce as a channel-power threat: whoever holds the product graph becomes the toll booth for AI shopping agents. The unaddressed question — whether Amazon can crawl competitors' sites without permission — is exactly what robots.txt and agent-authentication fights are about.
- Source: Reuters (Greg Bensinger) — "EXCLUSIVE: Amazon's plan to own the world's product data and fight AI shopping agents" (Nov 13, 2026) — https://www.reuters.com/business/media-telecom/amazons-plan-own-worlds-product-data-fight-ai-shopping-agents-2026-11-13/
- Source: Euronext syndication of the same Reuters exclusive (Nov 13, 18:28 GMT) — https://live.euronext.com/en/financial-news/exclusive-amazons-plan-own-worlds-product-data-and-fight-ai-shopping-agents

## AI security and governance

**FAA's AI routing tool SMART under scrutiny after nationwide ground stop; FBI probes software-update cause — developer denies involvement** — Event: Friday evening, Nov 13 disclosures (5:20–6:55 PM ET).
The FAA lifted a four-and-a-half-hour nationwide ground stop at 4:35 PM ET after a private communications-network update failed, per CBS News sources; Bloomberg reported at 5:20 PM that the FBI is investigating whether a software update caused the outage, and the FAA had just expanded SMART — an $875M AI airspace-routing tool — to all 22 centers (announced Nov 10). SMART's developer ASI said the outage "is not based on or a result of the SMART system." Gizmodo's piece lays out the underlying AI-governance concern: NATCA was not involved in SMART's design or testing, House Democrats have asked the GAO to review its implementation and safety-risk management, and controllers retain final acceptance of its suggestions.
**Why it matters:** Weekend news cycle aside, this is a live case study in AI-in-critical-infrastructure governance — a high-value AI tool scaled nationally with limited frontline-worker input, then publicly suspected of a major outage it didn't cause.
- Source: Gizmodo (Tom McKay) — "The FAA Is Rolling Out Its AI Air Traffic Tool Nationwide" (Nov 13, 2026) — https://gizmodo.com/the-faa-is-rolling-out-its-ai-air-traffic-tool-nationwide-2000839347

**Update on previously reported (megaspeed-alice-huang):** US officials told CNBC on Friday evening (published 7:11 PM EST, updated 8:34 PM) that Trump will *not* raise the Alice Huang espionage allegations with Xi Jinping at the Nov 18–19 APEC summit in Shenzhen — where Musk will accompany the president and a Trump–Xi–Putin three-way is planned. Overnight, FT's Eleanor Olcott (2:30 AM) added analysis on Huang's China-relations handicap.
- Source: CNBC (Dan Mangan) — "Trump doesn't plan to raise Nvidia, spy issue during China trip" (Nov 13, 2026, 7:11 PM EST) — https://www.cnbc.com/2026/11/13/trump-nvidia-huang-china.html

## Also noted
- Weekend was quiet on new security disclosures and model launches (a normal Saturday); the notable business/industry stories above are the in-window verified set.
- No new model releases verified in-window: Ling 3.2 Flash (inclusionAI) was Nov 5 (not Nov 13 as one tracker claims — verified and excluded), and the HN-surfaced "DeepSeek V4 Pro 0813" AA listing is an August checkpoint.

## Just missed the window
- **ggml v1.0.0** (Nov 13, 03:34 ET — before this run's window; llama.cpp v1.0.0 proper was reported yesterday): the tensor library behind llama.cpp hit its first major version; the HN post did 293 points overnight — https://github.com/ggml-org/ggml/releases/tag/v1.0.0
- **WSJ: Nvidia allegedly engineered the GPU shortage by funding AI startups to buy its chips** (disclosed Friday ~7:50 AM ET — in the previous fire's window, not reported there): the August-meeting story (Huang berating CFO Colette Kress over pushing to cut circular financing, an "avalanche" of TSMC GPUs needing buyers) became the weekend's #1 HN AI story (658 pts) — https://www.techpowerup.com/354814/nvidia-allegedly-engineered-scarcity-by-funding-ai-startups-to-buy-its-gpus
- **IBM's LLMON markup + two small Granite 4.1 previews (3B/8B) trained to ignore hidden instructions in documents** (HF blog Nov 12): attack success rates drop toward zero on some indirect-prompt-injection benchmarks at unchanged general capability — very relevant to local agent stacks — https://huggingface.co/blog/ibm-granite/llmon-an-llm-native-markup-language-now-in-granite
- **Salesforce SOV-Transcribe-1** (HF Nov 12): 26.6B/4.6B-active Apache-2.0 ASR MoE (Whisper large-v3 encoder + Gemma 4 26B A4B), #1 open-weight on AA non-streaming ASR, DFlash draft model included — https://huggingface.co/salesforce-sovereign-intelligence/SOV-Transcribe-1

## Coverage notes
- **Verified:** 6 in-window items (3 leads + 3 noted/update lines), each from a fetched page whose own timestamp places it inside 11/13 17:00 → 11/14 07:00 EST; lead stories read in full; plus 4 first-time "just missed" footnotes.
- **Outlets:** Reuters, WSJ (via AI Weekly copy), CNBC, FT (via AI Weekly/Techmeme), GitHub, Hugging Face, Gizmodo, TechPowerUp/WSJ, Euronext — 9 distinct sources.
- **Ledger dedupe:** 6 candidates suppressed as already reported — Unsloth Dynamic v3.1 Qwen3.8-27B (HN 161 re-cover of 11/11 item), SenseNova-Loop-9B re-surface (footnoted 10/31), ERNIE 5.5 Flash Base/Preview re-surface (footnoted 11/12), "Nvidia chip-tracking" (footnoted 11/13), GR00T 2 HN post (footnoted 11/09), and one repeat "Stand with Jensen" staff-letter thread (megaspeed-adjacent, non-new). Two further megaspeed items (FT Olcott, CNBC Trump/APEC) were folded into the single update line rather than re-listed.
- **Dead ends (two-strike rule):** "Open-weight LLMs can predict effects of weight edits" paper (r/LocalLLaMA 09:07Z) not located; "Bolt PCHIE" harness repo not found via gh search; KittenML kitten-tts-2-mini repo created Nov 12 ET (out of window) — dropped rather than footnoted; inclusionAI/Ling-3.2-flash HF API returned no repo (closed flagship; thehype's Nov 13 date is wrong).
- Indexes used: Techmeme river (fully), HN Algolia date queries (AI/model/front-page), r/LocalLLaMA + r/MachineLearning new/top feeds, GitHub releases + merged-PR searches for llama.cpp/vLLM/SGLang/Ollama/OpenClaw (Ollama v0.40.17-rc0 and SGLang v0.5.23 remain the latest releases — no in-window versions).
- Weekend caveat: Saturday-evening US news flow is thin; governance/security produced no new in-window disclosures beyond the FAA story.
