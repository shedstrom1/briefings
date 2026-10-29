# AI News Briefing — Mistral's Le Chonk Weights Drop With Day-0 vLLM Recipe; Serval and Wabi Land $150M/$55M Agent Rounds

**Window:** 2026-10-29 07:00 → 12:00 EDT (previous fire 07:00 ET → this fire 12:00 ET). New items only; ledger-deduped.

## TL;DR

- **Mistral released the weights of Mistral Large 4 "Le Chonk"** (1T-param multimodal MoE) under a modified-MIT "Mistral Ecosystem Open-Weight" license — it claims the top open-weight coding model (FrontierCode 2.0 lead at 19.7%, Terminal-Bench 4.0 at 41%) and 51% on the Artificial Analysis Cyber Index.
- **vLLM merged a day-0 Mistral-Large-4-1T-A52B serving recipe** ~4 hours after the weights post landed, alongside a large KV-offload/P2P reliability cluster and Qwen3.8 SM100 skinny-GEMM perf work.
- **Serval raised $150M (Series C, Sequoia-led) at a $3B valuation** to push its agentic enterprise-IT/ServiceNow-replacement platform; **Wabi (Replika founder Eugenia Kuyda) raised $55M at $1.25B** for its group-chat personal agent.
- **Microsoft launched a $25B+ US bond sale**, its first since 2017, explicitly funding the AI capex race.
- AI security/governance was a thin lane this window — the day's strongest security-adjacent stories (Megaspeed/Nvidia espionage piece, Trakkr "zombie stats" audit, Epic's AI-written Unreal Engine, dual-5090 KV-cache sharing) all disclosed just before the window and are footnoted once.

## Models and local inference

**Mistral Large 4 "Le Chonk" is now open weight — the strongest open coding model yet, with a cybersecurity twist.** Event date: Oct 29 (weights post published 2026-10-29; preview was Oct 6). Mistral published weights for the improved Large 4: a ~1-trillion-parameter (1T-A52B per the vLLM recipe; earlier specs list ~49–52B active) natively multimodal MoE trained from scratch in Europe. Three weeks of extra RL took it to the open-weight lead on Cognition's private FrontierCode 2.0 (19.7%) and Terminal-Bench 4.0 (41%, +13 points vs preview, 30% fewer output tokens), while its 48% on the Artificial Analysis Coding Agent Index effectively ties GLM-5.3 at 47% fewer tokens per task. It scores 51% on the AA Cyber Index (vuln discovery→repro→patch), refuses 96.6% of malicious cyber requests, and resists 97.1% of B3 Agent Security Benchmark attacks. Release adds three reasoning modes (Low/Medium/High), repriced API ($0.70 in / $0.07 cached / $2.10 out per M tokens), weights on Hugging Face, vLLM/SGLang deployment paths, NVIDIA NIM containers, Microsoft Foundry as first cloud, and a vetted-access pen-testing variant for design partners like Orange Cyberdefense. License is "Mistral Ecosystem Open-Weight" — free MIT-style use, modify, fine-tune, commercialize, except Model-as-a-Service operators must buy a commercial license.
Why it matters: this is the first trillion-class Western open-weight release with credible frontier-adjacent agentic-coding claims and a defensive-security posture — a direct shot at GLM-5.3, Kimi K3 and DeepSeek V4.1 Flash in the open-weight tier, and a sovereign-AI flag for European enterprises. Token-efficiency claims (≈half the tokens for parity coding) matter more to serving economics than another benchmark point.
- Source: Mistral newsroom — "Mistral Large 4 (Le Chonk) is now open weight," 2026-10-29 — https://mistral.ai/news/mistral-large-4-oss/
- Source (corroboration): vLLM merged PR #64527 "[Docs] Add Mistral-Large-4-1T-A52B recipe," merged 2026-10-29 14:33 UTC — https://github.com/vllm-project/vllm/pull/64527
- Source (community reception): Hacker News front page, story 50289418, posted 2026-10-29 14:13 UTC (408 pts) — https://news.ycombinator.com/item?id=50289418

**vLLM's 5-hour merge slate leans KV-offload and Qwen3.8/Blackwell perf.** Event date: Oct 29 (07:00–12:00 ET). 39 PRs merged in-window. Highlights: Mistral-Large-4-1T-A52B recipe (#64527); SM100 skinny-GEMM plans for Qwen3.8 at TP=1 (#64482); GLM-5.3 low-latency BF16 GEMM on SM100 (#64481); adaptive verification for GLM-5.3-Flash speculative decode (#60229); `thinking_token_budget` in the Responses API (#63896); Ming3-ASR model support (#63585); and a ~10-PR KV-offload/P2P reliability cluster, including reverting #42129 to fix KV-cache corruption under whole-graph compile (#64279).
Why it matters: the KV-offload/P2P fixes target exactly the tiering failures that bite long agent sessions on single-GPU boxes; `thinking_token_budget` is a practical cost knob for reasoning models in production.
- Source: GitHub vLLM merged-PR search, repo:vllm-project/vllm, merged 2026-10-29T11:00Z–16:00Z — https://github.com/vllm-project/vllm

**llama.cpp shipped b11982** (2026-10-29 12:17 UTC): Hexagon UPSCALE op support (#31331) — nearest-neighbor/bilinear upscale kernels moving onto Hexagon NPU offload. (Also-noted; only new build in-window after the 07:00 fire's b11970–b11981.)
- Source: GitHub releases — https://github.com/ggml-org/llama.cpp/releases/tag/b11982

**Microsoft opened a $25B+ US bond sale, first since 2017, to fund AI capex** (Bloomberg, 2026-10-29; 10 tranches, investment-grade; follows Azure's +48% Q1). (Also-noted.)
- Source: Bloomberg — https://www.bloomberg.com/news/articles/2026-10-29/microsoft-kicks-off-first-us-high-grade-bond-sale-since-2017
- Source (syndicated): Yahoo Finance/Investing.com — https://finance.yahoo.com/markets/stocks/articles/microsoft-seeks-least-25-billion-123509895.html

## Agent tools

**Serval raised $150M at a $3B valuation, led by Sequoia — third round in 12 months — to put AI agents on enterprise IT work.** Event date: Oct 29. The enterprise service-management/automation platform (CEO Jake Stauch) tripled its valuation from $1B in December 2025; total funding is $277M, with Elad Gil, Evantic, First Round, General Catalyst, Redpoint plus new investors Conviction and Headline. The money funds 500+ hires and a head-on pitch against ServiceNow, with agents handling access management and workplace tasks.
Why it matters: this is the "agentic ServiceNow replacement" thesis getting a public, sequoia-priced vote days after ServiceNow's own guidance miss sank its stock 22%+ — the incumbent-disruption framing is now explicit in the round's coverage.
- Source: Reuters — "Serval triples valuation to $3 billion as businesses adopt AI workflow automation," 2026-10-29 — https://www.reuters.com/business/serval-triples-valuation-3-billion-businesses-adopt-ai-workflow-automation-2026-10-29/
- Source: Axios Pro (Jake Stauch interview) — https://www.axios.com/pro/enterprise-software-deals/2026-10-29/serval-sequoia-enterprise
- Source (company post): serval.com — https://www.serval.com/blog/we-raised-150m-to-make-everyone-a-builder

**Wabi raised $55M at a $1.25B valuation, led by IVP, for its shared group-chat personal agent.** Event date: Oct 29. Replika founder Eugenia Kuyda's Wabi — relaunched publicly just ten days ago as a "personal agent" that lives inside group conversations and builds task-specific mini-apps (meal planning, fitness, booking) on demand — also drew a16z, Forerunner and the Amazon Alexa Fund.
Why it matters: multi-user, ambient agents that generate their own tools inside chats are a distinct bet from the single-user coding/cowork agent wave, and a $1.25B entry price in ten days is the market saying the shape has legs.
- Source: TechCrunch — 2026-10-29 — https://techcrunch.com/2026/10/29/wabi-the-ai-agent-that-builds-mini-apps-now-valued-at-1-25b-after-latest-round/
- Source: Bloomberg — https://www.bloomberg.com/news/articles/2026-10-29/ai-assistant-startup-wabi-raises-55-million-at-1-25-billion-value
- Source: RuntimeWire — https://runtimewire.com/article/wabi-raises-55-million-at-1-25-billion-valuation

**Also-noted — Collective raised $100M** (2026-10-29): $40M Series B led by General Catalyst plus a $60M non-dilutive growth facility from GC's Customer Value Fund for its AI bookkeeping/finance platform for solo business owners (CEO Hooman Radfar).
- Source: Axios Pro — https://www.axios.com/pro/fintech-deals/2026/10/29/collective-100m-financial-services-ai
- Source: Accounting Today — https://www.accountingtoday.com/news/collective-raises-100-million-in-new-funding-for-ai-finops-system

## AI security and governance

Nothing material first-disclosed in this 5-hour window in the AI security/governance lane after ledger dedupe — the window's strongest adjacent stories (below) all broke pre-window. NVIDIA's public response to the Megaspeed espionage report landed 11:53 ET but covers a Wednesday (Oct 28) NYT disclosure, so it earns only the footnote.

## Just missed the window (each footnoted once, per policy)

- **Megaspeed/Alice Huang (US intelligence) + NVIDIA's response** — NYT (Wed Oct 28): US officials believe Megaspeed exec Alice Huang (aka Huang Le) was a Chinese asset who cultivated a close relationship with Jensen Huang to pass intelligence; Megaspeed, Nvidia's largest SE-Asia customer, is suspected of ~$2B in H100/H800 diversions. NVIDIA responded publicly this morning (~11:53 ET). https://www.theguardian.com/technology/2026/oct/29/nvidia-ceo-targeted-chinese-spy ; https://www.cnbc.com/2026/10/29/nvidia-jensen-huang-chinese-intelligence-asset.html (missed:)
- **"Zombie stats": only 40% of AI-cited statistics trace to a study** — Trakkr Research (Mack Grenfell), published Oct 28: 1,285 statistics cited by ChatGPT, Perplexity, Gemini, Claude and AI Overviews walked back to source; 40% reached a matching study, 8% reached one with the number/context changed, only 21% of cited pages were the research itself. HN front page this morning. https://trakkr.ai/trakkr-research/zombie-stats (missed:)
- **Epic confirms Claude Code + Codex are writing Unreal Engine code** — This Week in Videogames (Oct 27): EVP Marcus Wassmer says AI tools rewrote a large slice of the D3D12 rendering layer in ~1 week (vs 3–6 engineer-weeks), nearly eliminated a decade of non-deterministic cooking bugs, and are feeding UE6/UEFN development. https://thisweekinvideogames.com/news/epic-games-confirms-ai-is-being-used-to-develop-code-for-unreal-engine/ (missed:)
- **Local-inference read: two RTX 5090 hosts sharing one KV cache over Ethernet** — viiwork (Oct 27): `kv_share` replicates Strata/Qwen3.8-Flash-Next session caches (~240MB delta per 50k-token turn) so a busy coding-agent host can hand a session to its peer warm instead of a ~41s-per-50k-token cold prefill; 1 GbE proved workable (~320–450 Mb/s average), import cost ~2.8s, fixes shipped in v2.11.1. https://viiwork.pulu.app/blog/kv-share-two-5090-hosts/ (missed:)
- **StackOverflow under 1,000 questions/month** — live data.stackexchange query that hit HN front page last night (Oct 28 ~7:35 PM ET): the Q&A site's monthly question volume has collapsed below four digits — the standard reference for the AI-shifted developer Q&A story. https://news.ycombinator.com/item?id=50282454 (missed:)

## Coverage notes

- **Verified count:** 7 in-window items (2 lead-class fully read: Mistral newsroom post, Reuters/Serval; vLLM/llama.cpp via GitHub API merge data; Bloomberg/Reuters/TechCrunch/Axios items corroborated by ≥2 outlets) + 5 once-only "Just missed the window" footnotes.
- **Ledger dedupe:** 17 candidates suppressed as already reported (incl. repeat footnotes): Gemini-4-Carbon AA-#1 article (ledger 10/28), nullpt.rs fingerprinting (10/28), Qwenputer re-spike (missed:), Sierra Club emissions (today 07:00), Samsung HBM sold-out (07:00), Marvell–Lumentum (07:00), Cognition $70–80B (missed:), FuriosaAI $620M (07:00), CS enrollment decline (07:00), WSJ flash-quintupled (07:00), software-stocks slide (07:00), Fragment $56M (07:00), vLLM MiniMax-M3.1 kernel batch (07:00), llama.cpp b11970–b11981 (07:00 — only b11982 new), GPT-6-Astra distillation (07:00), Nscale IPO profile (10/21), Apple touch-MBP re-coverage (10/28).
- **Dead ends / limitations:** DuckDuckGo backend returned zero results twice (fell back to Nous results); HF trending-page extraction dropped model-name link text, so three freshly-updated trending entries (a 15B text model, a 1B VLM, a 36B TTS) could not be named within budget — logged, not chased; kapitsa (nace-ai) and Google's elephant-goldfish-model trending repos predate the window (created Oct 16 / Sep 13) and were dropped; Open WebUI v0.12.5 shipped 05:08 ET — pre-window, skipped. Techmeme river items were resolved to fetchable URLs before citation; no index was down.
