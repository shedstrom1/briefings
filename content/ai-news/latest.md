# AI News Briefing — Sunday-night sweep, Monday November 2, 2026

**Window:** 2026-11-01 17:00 EDT (Nov 1 21:00 UTC) → 2026-11-02 07:00 EST (Nov 2 12:00 UTC). New items only; events anchored to their original disclosure date and deduped against the job ledger.

## TL;DR
- **vLLM v0.33.0 ships** (Nov 2, 10:10 UTC): a 927-commit release that drops CUDA 12.9 wheels entirely, moves AMD to ROCm 10.0, adds `/v1/decisions` by default, and posts big spec-decode gains — DeepSeek-V4.1-Flash + DSpark on GB300 jumps from 1,373 to 3,390 output tok/s.
- **SemiAnalysis launches GDPx** (Nov 2): an 11-domain agentic benchmark where the best model, Claude Opus 5.5, scores just 12.0% — and calls Gemini 4 Carbon "a real improvement."
- **WIRED exposes a 400+-app deepfake network** (Nov 2, 5:00 AM ET): mundane-looking apps from Chinese-language developers that stealth-load explicit deepfake generators, promoted via tens of thousands of Meta ads.
- **South Korea nearly doubles AI/chip spending** in its 2027 budget (announced Monday): ₩21.3T (~$16B), including ₩4.7T for a "national strategic asset" frontier model.
- **White House refuses to release its voluntary AI testing framework draft**, citing national security, despite a September FOIA-lawsuit agreement with Protect Democracy (The Information, Nov 1 evening).

---

## Models and local inference

**vLLM v0.33.0: CUDA 13-only wheels, ROCm 10.0 default, and a speculative-decoding wave**
*Event date: November 2, 2026 (release published 10:10 UTC).*
The inference engine shipped its biggest release in months — 927 commits from 389 contributors (122 new). CUDA 12.9 artifacts are retired (CUDA 13.0 only), PyTorch moves to 2.14.0 with Triton 3.8.0, and the CUDA wheel is now libtorch-ABI-stable. Speculative decoding is the headline: DFlash/DSpark no longer reserve draft slots in the token budget, which vLLM's own PR benchmark shows taking DeepSeek-V4.1-Flash + DSpark on GB300 from 1,373 to 3,390 output tok/s; opt-in latency-specialized decode gives Kimi-K3 1.5–2x on GB300 (TP8/TP16) and DeepSeek-V4.1-Flash an 18.3% faster decode step at batch 1; a new opt-in lossy verification policy ("FLy") claims 1.04–1.31x decode throughput on MI355X and B300. On AMD, the default image moves to ROCm 10.0, `ROCM_OCTAVE` 3–4-bit KV cache delivers 4.6x the KV tokens of fp16 on an RX 7900 XTX, and gfx1250 enablement lands for gpt-oss-120b, DeepSeek-R1, DeepSeek-V4-Flash and MiniMax-M3. New model support includes Reflection Beam-1.0 (501B/23B-active MoE, originally Oct 16) with its DFlash2 drafter, Yandex AliceAI-Foundation-80B-A3B, Recursive Flint, Step-5 Preview, NVIDIA Alpamayo 2 Super and MiniCPM-OCR 1.0. Breaking changes: Medusa and the MLP speculator are removed, the legacy Ray compiled-graph executor is gone, and the NIXL connector now requires NIXL 1.5+.
The `/v1/decisions` endpoint being on by default for supported generation models pushes vLLM further into decision-model routing territory.
Source: GitHub — vllm-project/vllm release v0.33.0, published Nov 2, 2026 10:10 UTC — https://github.com/vllm-project/vllm/releases
Source: vLLM recipes — Reflection Beam entry (NVFP4/FP8/BF16, 1M ctx, DFlash2 drafter) — https://recipes.vllm.ai/reflection/Beam-1.0-501B-A23B

**llama.cpp: two in-window builds**
*Event dates: November 2, 2026.*
b12034 (08:02 UTC) fixes WebGPU CI by keeping scratch allocation sizes monotonic in batch length (#31422) — the GGML_SCHED_NO_REALLOC aborts on 2–19 query-row batches are gone. b12038 (11:31 UTC) repairs the clang-cl AVX512-BF16 build (#31276). Ollama (v0.40.13, Oct 28), SGLang (v0.5.22, Oct 19) and OpenClaw (2026.10.10-beta.5, Oct 30) were all quiet in-window.
Source: GitHub — ggml-org/llama.cpp releases — https://github.com/ggml-org/llama.cpp/releases
Source: GitHub — ollama/ollama releases — https://github.com/ollama/ollama/releases

---

## Agent tools

**SemiAnalysis launches GDPx: best model (Opus 5.5) scores 12% on 11 "most valuable" agentic domains**
*Event date: November 2, 2026.*
The research firm released GDPx, a benchmark built with 14 specialized RL-environment data partners across chip design, cybersecurity, biology, finance, legal, quant trading, recursive self-improvement, hardware design, materials science, alignment and software engineering — deliberately skipping government/real-estate/insurance because "the first trillion dollars of AI revenue" concentrates in high-ROI sectors. Debut leaderboard: Claude Opus 5.5 leads with just 12.0% (13 models shown); SemiAnalysis notes Opus and GPT-6 Astra sandbag on cyber/bio/alignment due to safety classifiers — exclude those and the ranking reshuffles. Domain winners: Opus 5.5 in quant trading, hardware design and chip design; Gemini 4 Carbon is "a real improvement… the only model in the same ballpark as Opus and Astra" and takes biology; GPT-6 Astra wins RSI and materials science; finance and legal tasks are so hard "basically every model scores exactly 0." The cost/performance Pareto line: Luna and DeepSeek fight for the low end, Opus owns the high end, OpenAI holds the middle — and Muse Wonder Full is "actually the best available cyber model today by a meaningful margin."
*Why it matters:* an explicitly anti-GDPval benchmark (OpenAI's Sept 2025 benchmark is criticized as non-interactive and environment-free) from a semis-focused firm — with enterprise-seeded environments like a ~4,000-file replica of a real PE transaction — reframes agentic progress measurement toward long-horizon, tool-heavy professional work, and the 12% ceiling is a sober counterweight to saturated coding benchmarks.
Source: SemiAnalysis (Max Kan, Andrew Megalaa, Dylan Patel) — "GDPx: Measuring Frontier Model Capabilities Across the Most Valuable Domains," Nov 2, 2026 — https://newsletter.semianalysis.com/p/gdpx-measuring-frontier-model-capabilities
Source: Unite.AI — "SemiAnalysis Launches GDPx Benchmark Spanning 11 Agentic Domains" (V1.0 = 206 tasks), Nov 2, 2026 — https://www.unite.ai/semianalysis-launches-gdpx-benchmark-spanning-11-agentic-domains/

---

## AI security and governance

**WIRED: 400+ "mundane" AI apps hidden a coordinated nudify/CSAM network tied to a Hong Kong-registered entity**
*Event date: November 2, 2026 (published 5:00 AM ET).*
Matt Burgess's investigation found ~400 apps and websites linked to Chinese-language developers and KeLing Technology Limited (Hong Kong) that masquerade as everyday AI tools — pet-portrait app Viefur is the lead example — but server-side gating can stealth-load a web interface generating explicit deepfake images/video, with templates including "bedroom rape" and knife-threat scenes. AI Forensics reverse-engineered the gating ("the server replies whether or to show the explicit or the non-explicit version"); Infoblox's Maël Le Touz identified nearly 500 linked domains sharing one codebase. At peak, ~30 nudify domains registered per week; 27 were live on Google Play in September with 350k+ combined downloads (DiffAI alone 100k+); tens of thousands of explicit deepfake ads — including AI-CSAM depicting real children and a European royal — ran on Meta platforms. Google removed most apps before contact; Apple pulled 19 including Viefur; a new model called "Veyra" was pushed to the interface in late September.
*Why it matters:* this is organized, commercially-run platform-evasion at scale — bait-and-switch app updates defeating App Store review, fake Meta payer names, and payments through Apple/Google Pay. Expect renewed regulatory heat on Apple, Google and Meta's enforcement, and it's a concrete supply-chain threat pattern for anyone evaluating AI app stores or ad pipelines.
Source: WIRED (Matt Burgess) — "Hundreds of Mundane AI Apps Are Hiding Sexually Abusive Deepfake Features," Nov 2, 2026 5:00 AM ET — https://www.wired.com/story/hundreds-of-mundane-ai-apps-are-hiding-abusive-deepfake-features/

**White House says it won't hand over the full draft of its voluntary frontier-AI testing framework**
*Event date: November 1, 2026 (The Information report, evening ET).*
In a letter sent Friday (Oct 30) by the White House Office of the National Cyber Director to the nonprofit Protect Democracy — shared with The Information — the administration argued it does not have to produce key documents from its voluntary framework for pre-release review of covered frontier models, citing national-security and commercial-supplier exemptions. The refusal walks back part of the September agreement that settled Protect Democracy's FOIA lawsuit over the framework, which stems from the June 2 executive order on "Promoting Advanced Artificial Intelligence Innovation and Security." A left-right coalition of 25+ groups has been demanding release since September.
*Why it matters:* the transparency settlement was the main accountability hook for a testing regime that gates frontier-model releases; a partial reversal revives the "closed-door AI policy" critique just as OpenAI pushes its post-midterms "speed limits" agenda, and sets up a courtroom fight over FOIA exemptions for AI safety documents.
Source: The Information (Leo Schwartz) — "Trump Administration Refuses to Release Information on AI Testing Framework," Nov 1, 2026 (carried by Techmeme at 8:45 PM ET) — https://www.theinformation.com/briefings/trump-administration-refuses-release-information-ai-testing-framework
Source: Protect Democracy — "Uncovering the Trump administration's secret rules for AI model release" (FOIA background) — https://protectdemocracy.org/work/uncovering-the-trump-administrations-secret-rules-for-ai-model-release/

**South Korea's 2027 budget: ₩21.3T (~$16B) for AI and chips, nearly double 2026**
*Event date: November 2, 2026 (Monday, National Assembly budget speech).*
President Lee Jae Myung's 2027 budget proposal totals ₩821T (+12.8%, a record), with ₩21.3T for chipmaking, AI and data centers — "nearly double this year's amount." A ₩4.7T slice funds "frontier AI development projects, with the aim of achieving world-leading performance for our own AI model, a national strategic asset," and ₩2.1T backs semiconductor-park power/water infrastructure. Defence rises ~8% to ₩73.3T, aimed at a "smart, elite force" built on AI, robots and unmanned systems — pointed, given North Korea's recent AI-claimed torpedo and hypersonic tests.
*Why it matters:* sovereign-AI spending keeps accelerating in US-ally tech states (following the June mega-projects and September ₩162.3T Future Fund), with a national frontier-model program that local-inference and open-weight vendors should watch — and it lands amid US midterms debates over data-center buildout.
Source: Hürriyet Daily News / AP — "South Korea to double spending on AI and chips in 2027," Nov 2, 2026 — https://www.hurriyetdailynews.com/south-korea-to-double-spending-on-ai-and-chips-in-2027-228893
Source: Reuters — "South Korean President Lee vows to dedicate ~$16B for AI, chips, physical AI and data centers," posted 11:05 PM ET Nov 1 — https://www.reuters.com (via Techmeme)

---

## Updates on previously reported

**Anthropic IPO — update (ledger 2026-10-26, anthropic-s1-ant): up to $3.5B Japanese retail/institutional tranche**
*Event date: November 2, 2026 (Monday filing).*
Anthropic filed to sell up to ¥552.9B ($3.5B) of shares in Japan as part of its US listing, with proceeds earmarked for working capital, opex and capex. Mizuho Securities manages the Japanese portion; Goldman Sachs, Mitsubishi UFJ Morgan Stanley Securities and Morgan Stanley MUFG Securities handle investor orders. Japan — where IPO volume sits at a 14-year low — joins Australia, Canada and parts of Europe with direct access; at SpaceX's June IPO, Japanese investors took $2.2B of the $75B raised. Some investors have discussed a ~$1.5T valuation.
*Why it matters:* a material new development on the S-1 story: a $3.5B first-tier allocation into the world's most starved IPO market signals how aggressively the deal is being distributed globally ahead of the reported mid-November listing.
Source: Reuters (Anton Bridge) — "Anthropic plans to sell $3.5 billion of shares to Japan investors in IPO, filing shows," Nov 2, 2026 — https://www.reuters.com/world/asia-pacific/anthropic-plans-sell-35-billion-shares-japan-investors-ipo-filing-shows-2026-11-02/
Source: The Business Times (Singapore) — same story, published Mon Nov 2, 2026 3:13 PM SGT — https://www.businesstimes.com.sg/companies-markets/capital-markets-currencies/anthropic-plans-sell-us3-5-billion-shares-japan-investors-ipo

---

## Just missed the window
- *missed:* SenseNova-Loop-9B-Preview — Apache-2.0 looped-decoder 9B multimodal (32 distinct layers executed 56x, two-stream mHC, 262K context, MTP support; ~14.6B inference params) released Oct 30; day-one paths via vLLM PR #64832 and SGLang PR #47281 — https://huggingface.co/sensenova/SenseNova-Loop-9B-Preview
- *missed:* PIF and Aramco closed the HUMAIN transaction (announced Nov 1, a few hours before window start): Aramco takes 49%, PIF keeps 51%, ~$2.6B cash in 2026 plus transferred AI assets — https://www.aramco.com/en/news-media/news/2026/pif-and-aramco-close-humain-transaction-to-accelerate-humains-global-growth

## Coverage notes
- **Verified: 7 qualifying items** (4 lead-section events + 2 governance/agent items counted in sections + 1 ledger update), counting underlying events; plus 2 one-time "missed" footnotes.
- **Ledger dedupe: ~14 candidates suppressed** as already reported or stale-recovered: OpenAI post-midterms blitz (Axios), YC agent-games follow-up, Google space-DC quotes, data-center moratorium analysis, AA Intelligence Index v5 re-run, Kioxia Monday slide (derivative of the already-footnoted Oct 30 results), Hex $100M Series D (Oct 27), Hyperbolic $30M A-1 (Oct 27), Casap $56M (Oct 28), Bites $40M (Oct 29), Broccoli AI $52M (Oct 28), Reflection Beam-1.0 coverage (model released Oct 16), China MOFCOM Announcement No. 48 recirculation (Oct 16), numbat leak (reported Oct 23/Mon).
- Thin window (Sunday evening → Monday 07:00 ET): most Techmeme Monday morning items traced to Friday–Sunday disclosures; all four "funding" river items were re-carried older announcements and were dropped.
- Dead ends: Techmeme /261102 index 404'd (river page used instead); The Information briefing page serves generic content to unauthenticated fetches, so the item is corroborated via Techmeme's 8:45 PM ET carry and Protect Democracy's own case page. Binance/TraderTraitor and the EU supermarket-freezer attacks are major cyber stories but out of this brief's AI scope.
- Phase walls: discovery and verification completed within budget; report written before 07:50.
