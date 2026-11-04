# AI News Briefing — Tencent Hy4 hits top open-weight tier; Oracle scraps $7B Tencent chip lease; RTX Pro 5500 orders hit 6M with 1% delivery

**Window: November 3, 2026 17:00 → November 4, 2026 07:00 ET** (overnight sweep from the previous 17:00 fire; new items only, ledger-deduped)

## TL;DR

- **Tencent released Hy4**, saying its 770B flagship has entered the top tier of open-weight models for the first time — vision and computer-use are new, weights promised "in the coming days," price unchanged at $0.90/$2.70 per million tokens.
- **Oracle canceled a ~$7B, five-year lease of ~100,000 advanced AI chips to Tencent** in Southeast Asia after pressure from the Trump administration (FT); Reuters could not independently verify.
- **Chinese AI firms have ordered ~6M Nvidia RTX Pro 5500 cards (~$70B) but expect only ~1% delivery within four months** — a hard supply wall behind the compliant-chip boom (Bloomberg).
- **Cat Wu, head of product for Claude Code and Cowork, left Anthropic** — the third Claude Code leader exit ahead of the IPO.
- **vLLM landed 72 merges overnight including day-0 ROCm support for Hy4**; llama.cpp shipped ~14 builds and Ollama cut v0.40.15-rc0.

---

## Models and local inference

### Tencent releases Hy4, claiming first entry into the top open-weight tier
**Event date: November 4, 2026 (overnight ET).** Tencent announced Hy4, its latest-generation LLM at 770B parameters — a 2.6× jump over Hy3's 295B — and said it now ranks "among the world's leading open-source AI models." The release adds visual understanding (charts, fine-grained image analysis, visual math reasoning) and computer-use for the first time, with specialized training on Blender, CAD, Unreal, Unity and Godot; Tencent cites +19.1% on GameDev and +9.8% on GameLogic versus Hy4 Preview, hallucination rate cut from 12.95% to 7.2%, and a blind 579-task financial evaluation where Hy4 scored 3.04 versus DeepSeek-V4.1-Flash-1017's 2.97, Kimi K3.1 Pro's 2.68 and GLM-5.3's 2.68. Model weights and system cards are promised "in the coming days" with cybersecurity/biosafety review already run by Xuanwu and Keen labs (offensive-exploit data excluded from training). API pricing is unchanged from Preview: ~US$0.90/M input, US$2.70/M output, US$0.045/M cached; a cheaper Hy4 Flash is coming.
**Why it matters:** If the GA weights land as Apache-style open weights like the August Hy4-preview (770B-A49B, 1M context), Tencent finally has a credible answer to DeepSeek V4.1-Flash and Kimi in the self-hostable tier — with enterprise co-design (WorkBuddy 83.18 avg / 96.15% pass) as the differentiator.
Tencent's own API at $0.90/$2.70 undercuts most Western hosted equivalents.
- Source: Tencent official announcement (November 4, 2026) — https://www.tencent.com/tencent-launches-hy4-joining-the-ranks-of-the-worlds-leading-open-source-ai-models/
- Source: SCMP, "Tencent unveils Hy4 as open-weight AI race with DeepSeek, Z.ai, Alibaba heats" (November 4, 2026) — https://www.scmp.com/tech/big-tech/article/3373227/tencent-unveils-hy4-open-weight-ai-race-deepseek-zai-alibaba-heats

### Update (ledger 2026-10-20 RTX-PRO-5500-3M): Chinese RTX Pro 5500 backlog now ~6M cards / ~$70B — with ~1% expected to arrive in four months
**Event date: November 4, 2026 (Bloomberg, 3:14 AM UTC).** Bloomberg reports Alibaba, Tencent, ByteDance and other Chinese AI/cloud firms have ordered about 6 million Nvidia RTX Pro 5500 cards in total — roughly a $70B orderbook — but expect to receive only tens of thousands of units (about 1%) within the next four months as Nvidia struggles to ramp production. This materially sharpens the October 20 report (3M+ orders, $35B+) that this job already covered: the demand number has doubled and the delivery reality is now quantified.
**Why it matters:** The "compliant chip" workaround (export-legal Blackwell workstation cards rented/bought into China) is demand-capped by supply, which reinforces the rental-market workarounds Oracle just exited and keeps pressure on domestic Chinese silicon (see the Ascend 950 line, already in the ledger).
**Local inference:** The same Blackwell silicon is the desktop-class card local-inference buyers are queuing for worldwide; a 1%-fill rate on 6M units signals continued tight consumer/pro Blackwell supply into 2027.
- Source: Bloomberg, "Nvidia China Clients to Get 1% of $70 Billion RTX Chips Ordered" (November 4, 2026, 3:14 AM UTC) — https://www.bloomberg.com/news/articles/2026-11-04/nvidia-china-clients-to-get-1-of-70-billion-rtx-chips-ordered

### Overnight engine merges: vLLM 72 merges (day-0 Hy4 ROCm), llama.cpp b12084–b12102, Ollama v0.40.15-rc0
**Event dates: November 3 22:00 → November 4 12:00 UTC (all timestamps verified via GitHub API).** vLLM merged 72 PRs in-window, highlights: `[ROCm][HY4] Use breakable graphs without torch.compile` (#64763) — day-0 ROCm plumbing for the Hy4 launch — Muse Open specialized GB300 decode at 1.5× (#64077), DFlash2 candidate-edge scoring fused into a Triton kernel (#64916), a KV-offload cluster (extensible-KV UMA sizing #65413, Mooncake committed-block registration #65331), a large Rust-frontend conformance wave (#65369–#65377, #65619), MiniMax-M3.1 NVFP4 indexer top-k on ROCm (#65087), and Kimi-K3 B200 AgentX aggregated Mooncake preset (#65529). llama.cpp shipped ~14 builds (b12084 23:53Z → b12102 09:12Z); Ollama tagged v0.40.15-rc0 at 22:52Z November 3. No -specific fixes in this window.
**Why it matters:** Same-day vLLM support for a major Chinese open-weight launch has become table stakes — and the GB300/Blackwell decode work tracks directly to next-gen local nodes.
- Source: vLLM merged-PR search (merged 2026-11-03T22:00Z..2026-11-04T12:00Z) — https://github.com/vllm-project/vllm
- Source: llama.cpp releases — https://github.com/ggml-org/llama.cpp/releases ; Ollama releases — https://github.com/ollama/ollama/releases

### Hugging Face Daily Papers (Nov 4): agent-focused batch
**Event date: November 4, 2026.** Trending on the Hub this morning: "Axion 1.0: Open Agent Foundation Models, Simplified" (open agent foundation models), "SpecPilot" (UC Berkeley, autonomous algorithm discovery for speculative decoding), "Switch-SWE" (SJTU, do coding agents hold focus across unfinished tasks?), and "You don't need long context" — worth a skim for agent and spec-decode practitioners.
- Source: HF Daily Papers, November 4, 2026 — https://huggingface.co/papers/date/2026-11-04

---

## Agent tools

### Cat Wu, head of product for Claude Code and Cowork, leaves Anthropic
**Event date: November 3, 2026 (X post at 21:33 UTC — 5:33 PM ET, in-window).** Cat Wu — one of the most public faces of Claude Code since joining two years ago and co-leading its product rise alongside Boris Cherny — announced "Friday was my last day at Anthropic," giving no reason and no next role. She is the third Claude Code product leader to exit recently (after Thariq Shihipar and Anthony Morris per prior coverage), landing just as Anthropic's IPO (S-1 on file, Nasdaq "ANT") approaches.
**Why it matters:** Three product-lead departures from the company's flagship agent franchise in a month, on the eve of an IPO, is a signal worth watching on execution continuity — Business Insider frames the exits against Claude Code's revenue surge.
- Source: Business Insider, "Claude Code Leader Cat Wu Exits Anthropic Before Big IPO" (November 3, 2026) — https://www.businessinsider.com/claude-code-leader-cat-wu-exits-anthropic-before-big-ipo-2026-11
- Source: Unite.AI, "Cat Wu Steps Down as Claude Code Product Chief After Two Years" (November 3, 2026) — https://www.unite.ai/cat-wu-steps-down-as-claude-code-product-chief-after-two-years/

### Traversal raises $120M Series B at $1B to build a "superintelligent AI SRE"
**Event date: November 3, 2026 (Business Wire, 5:00 PM ET).** Traversal — causal-ML researchers from MIT, Berkeley, Cornell and Columbia — closed $120M at a $1B valuation, co-led by Kleiner Perkins, Redpoint and Khosla, with Sequoia, Battery and Amex Ventures participating (total raised: $173M). Its agents autonomously diagnose, remediate and prevent production incidents — the "outer loop" between writing code and keeping it running.
**Why it matters:** Agent tooling money is rotating from coding to production operations; a unicorn AI-SRE validates the long-running-agent stack (message threads, KV-offload, persistent memory — all recent vLLM/Anthropic territory) as the substrate ops agents run on.
- Source: Business Wire via Yahoo Finance, "Traversal Raises $120M with a $1 Billion Valuation…" (November 3, 2026) — https://finance.yahoo.com/technology/ai/articles/traversal-raises-120m-1-billion-170000526.html
- Source: SiliconANGLE (November 3, 2026) — https://siliconangle.com/2026/11/03/autonomous-site-reliability-engineering-startup-traversal-bags-120m-in-funding-at-a-1b-valuation/

### GitHub degradation hits PRs, Git operations, Issues and Actions — resolved
**Event date: November 4, 2026 (investigating 04:49 ET; resolved 06:50 ET).** GitHub took a multi-hour degradation affecting Git Operations, API Requests, Issues, Pull Requests and Actions, resolved with an RCA promised. Hit the HN front page (95 points) during the morning commit rush.
- Source: GitHub Status, incident smtdswd1h2w4 (November 4, 2026, 09:49–11:50 UTC) — https://www.githubstatus.com/incidents/smtdswd1h2w4

---

## AI security and governance

### Oracle scraps $7B, five-year Tencent AI-chip lease under Trump-administration pressure
**Event date: November 4, 2026 (FT report; TNW and Business Times published in-window).** Oracle walked away from a ~$7B, five-year contract to lease Tencent access to ~100,000 advanced AI chips across Oracle's Southeast Asia data centers — Tencent's largest overseas compute deal — after US officials raised concerns; pressure also came from Beijing, with Guangdong officials urging Tencent to favor domestic chips. Reuters could not independently verify; Oracle, Tencent and the White House didn't respond. Context: it follows the FT's report that Nvidia told cloud partners to cap Chinese business below 51% of capacity, and Oracle itself recently priced a $20B funding round at up to 8.65% while its shares have roughly halved over a year.
**Why it matters:** Cloud rental was the last export-control-safe path for Chinese labs to reach frontier-class compute; with the 51% rule and now this cancellation, the sanctioned-compute gap widens — which in turn pushes Chinese labs toward Ascend (this job reported DeepSeek V5's Ascend-950 native pre-training last night) and keeps open-weight Chinese models flowing as the alternative export.
- Source: TNW, "Oracle cancels $7B Tencent AI chip lease under US pressure, FT reports" (November 4, 2026) — https://thenextweb.com/news/oracle-cancels-tencent-ai-chip-lease
- Source: The Business Times (Singapore), "Oracle scraps US$7 billion deal to lease chips to Tencent" (November 4, 2026, 14:47 SGT) — https://www.businesstimes.com.sg/companies-markets/oracle-scraps-us7-billion-deal-lease-chips-tencent-under-trump-pressure-ft-reports

### Meta raises $15B in five-part dollar bond sale — $58B of orders — to bankroll AI capex
**Event date: November 3, 2026 (pricing Tuesday evening ET).** Meta sold $15B of investment-grade US dollar bonds in five tranches (incl. $3.25B of 2031 notes at 5.85% and $3.25B of 2033s at 6.25%), drawing up to $58B in orders (~3.9×), the largest slice of its $20B+ cross-border offering, funding the $135–145B capex year.
**Why it matters:** Deep AI capex is now moving from equity-linked structures into plain IG debt at 6%-plus coupons — a durable signal on how the buildout is being financed (this job has tracked Meta's Class C stock and the neocloud bond shutout).
- Source: Reuters via AOL, "Meta raises $15 billion in five-part US bond sale to fund AI expansion" (November 3, 2026) — https://www.aol.com/articles/meta-raises-15-billion-five-225011000.html
- Source: Bloomberg, "Meta Raises $15 Billion as Elevated Bond Yields Stoke Demand" (November 3, 2026) — https://www.bloomberg.com/news/articles/2026-11-03/meta-kicks-off-debt-offering-in-push-to-fund-ai-investments

### Update (ledger 2026-11-03 ai-learning-era-updates-2026): Google walks back the "ban AI and lose Search" edge
**Event date: November 3, 2026 (evening response).** After backlash — including criticism from Sen. Bernie Sanders and NYC Mayor-elect Zohran Mamdani — Google clarified that Gemini, Gemini Notebook and Search will each be opt-in independently for K-12 student accounts from early 2027: schools can enable plain Search without any Gemini product. VP for education Chris Phillips: search and AI "deeply integrated" still stands for feature design, but the punitive framing was softened.
**Why it matters:** Corrects the job's earlier midday item: the off-by-default policy holds, but the "lose Search entirely" consequence Google initially floated was retracted within hours — a case study in AI-policy backlash speed.
- Source: CNBC, "Google responds to outrage over removing search in schools that ban AI" (November 3, 2026) — https://www.cnbc.com/2026/11/03/google-responds-to-outrage-over-removing-search-in-schools-that-ban-ai.html

### Google extends voluntary buyouts to Canada, France, Germany, Japan and Switzerland
**Event date: November 4, 2026 (Reuters exclusive).** Google confirmed it began offering voluntary exit packages in June to employees in five more countries — one month's salary per year of service, capped at 12 months — as Alphabet manages costs against heavy AI spend; Cloud and DeepMind staff were excluded. Follows Microsoft's ~60K-employee buyout (ledger, Oct 26).
- Source: Reuters, "EXCLUSIVE: Google extends voluntary buyout offers to employees in several countries" (November 4, 2026) — https://www.reuters.com/business/media-telecom/google-extends-voluntary-buyout-offers-employees-several-countries-2026-11-04/

---

## Just missed the window (footnoted once)

Surged to the HN front page this morning (story 50362905).
- **Anthropic's Message Threads public beta** (announced **November 2**, missed by prior runs): Messages API threads send only new messages after the first full request — ~35% median time-to-first-token cut on 500K–1M-token requests, encrypted in-memory threads expiring after 5 minutes idle; live on Claude Platform, AWS, Microsoft Foundry and Google Cloud; beta header `message-threads-2026-08-12`. — https://claude.com/resources/articles/message-threads

## Coverage notes

- **Verified: 12 qualifying items** (10 new + 2 "Update on previously reported"), counting each milestone once; 2 first-time "Just missed" footnotes; 4 lead stories read in full. All in-window timestamps confirmed by fetched pages or GitHub API.
- **Ledger dedupe: 9 candidates suppressed as already reported** — Anthropic FlashProve (river, reported Nov 3 17:00), CrowdStrike AI Kill Switch/Falcon Gateway GA (reported Nov 3 17:00), fal $1.65B (reported Nov 3), OpenAI bug-bounty/Cathedral sprint + Nvidia 51% rule + AMD-vs-Intel x86-takeover (all reported Nov 2–3; AMD/Intel item derived from already-reported earnings), Revolut-GLM and Driscoll hire (both previously footnoted as `missed:`), OpenAI "compute campuses" rebrand (reported Nov 1).
- Dead ends (two-strike rule): The Information's "Nvidia soft power" feature (river 19:20 ET, paywalled, no fetchable copy); SemiAnalysis' SpaceX ~13GW 2027 revision (river-only, URL unresolved); MarkTechPost Letterpress-1 slug 404s; Bloomberg's Meta bond piece paywalled (Reuters/AOL copy cited instead); Supermicro Q1 +298% (16:45 ET Nov 3, minutes pre-window).
- Sources used: Techmeme river, HN Algolia (in-window query), The Decoder, Tencent/SCMP/Bloomberg/Reuters/FT-via-TNW/CNBC/Business Times/Business Insider/Business Wire/SiliconANGLE/GitHub API/HF Papers — 12+ distinct outlets across indexes and multi-angle search.
