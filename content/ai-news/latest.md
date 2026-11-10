# AI News Briefing — Tuesday, November 10, 2026 (12:00 ET edition)

**Window:** 2026-11-10 07:00 → 12:00 EST (previous fire → now). New items only; the event date anchors every entry.

## TL;DR

- **Google's Gemini 4 Carbon went fully GA** this morning — paid API, enterprise, and all AI Ultra users — two weeks after its gated cyber-defender debut, and Google previewed `/swarm`, a multi-agent feature in Antigravity.
- **Amazon's Ring launched a "Ring Robotics" division**: the $249.99 Drone Cam (ships Jan 27) and $999.99 Rover Cam (ships Dec 16), both steered by an autonomous navigation agent called AI Pilot.
- **Anthropic's IPO sharpened**: Reuters exclusive pegs it at up to ~$1.4T fully diluted with run-rate revenue of $81B as of Oct 31; TSMC's board approved up to $1B of Anthropic/OpenAI equity the same morning.
- **Scale Labs + CAIS's Remote Labor Index**: Claude Fable 5.2 completes 41.3% of real paid Upwork projects — double Opus 5.5 — the first big independent benchmark on yesterday's release.

## Models and local inference

**Google Gemini 4 Carbon is now generally available; `/swarm` multi-agent previewed in Antigravity** — Event date: Tuesday, Nov 10 (in-window).
Koray Kavukcuoglu's blog post opens paid access to Carbon for developers (Gemini API in AI Studio and Android Studio), enterprises (Gemini for business, CodeMender) and all Google AI Ultra subscribers (Antigravity, Gemini app, AI Mode in Search), at the same introductory price as Argon: $2/M input, $10/M output. Google claims #1 on CursorBench 4.0, Perplexity's WANDR and Zapier's AutomationBench at "more than 2X lower cost per task" than the next-best external model, positions Carbon for deep-reasoning work while Argon stays the default, and previews `/swarm` in Antigravity — one prompt spawning a coordinated agent team (architect/auditor roles on Carbon, faster models in parallel workspaces, adversarial verifiers). Researchers can apply for 6 months free at Antigravity's top tier. This is the GA milestone for the Oct 28 launch (ledger: gemini-4-carbon, previously Fairwind cyber-defender-gated).
*Why it matters:* GA timing — two weeks after Argon, mid-IPO-season, with Google's own frontier pricing war intact — pressures both OpenAI and Anthropic's premium tiers, and `/swarm` is Google shipping the multi-agent orchestration pattern natively inside an IDE/agent product rather than leaving it to frameworks.
*Local inference note:* none — cloud-only model.
Source: "Gemini 4 Carbon is now available" — blog.google, Nov 10, 2026 — https://blog.google/innovation-and-ai/models-and-research/gemini-models/gemini-4-carbon-now-available/

**Google and Artificial Analysis launch the MKA benchmark for multimodal knowledge agents** — Event date: Tuesday, Nov 10 (in-window).
The Multimodal Knowledge Agent benchmark is a 200-task evaluation set built with domain experts across 10 verticals and 83 occupations (architects, underwriters, biologists…); each task mirrors 3–8 hours of professional work over multimodal input bundles and is graded against a ~70-item expert rubric weighted 75% factual accuracy / 25% visual design. All models run in the same open-source harness (Stirrup); an agentic autorater built on a non-Google model agrees with human experts 96% of the time; the full set stays private at Artificial Analysis with 10 public tasks on Hugging Face. Tested models burn 30–130 turns per task. Gemini 4 Carbon ranks #2 in quality at ~6x lower average cost per task than the top scorer.
*Why it matters:* it's this week's answer to "does the model actually do knowledge work" — visually grounded, expert-verified, leak-resistant — and it lands the same morning as Carbon's GA, so treat the #2-at-6x-cheaper claim as vendor-adjacent framing of a joint benchmark.
Source: "Introducing the Multimodal Knowledge Agent benchmark" — blog.google (Rohan Doshi, Ben Caine), Nov 10, 2026 — https://blog.google/innovation-and-ai/models-and-research/gemini-models/multimodal-knowledge-agent-benchmark/

**llama.cpp lands FP8 KV-cache support; SGLang optimizes consumer-Blackwell (SM12x) kernels** — Event date: Tuesday, Nov 10 (merges 07:09–12:08 ET).
llama.cpp merged "llama: Add FP8 KV-cache support" (#28392, 11:08 ET) plus backend-scheduler reuse (#32009), MUL_MAT src1-precision gating (#31896) and a sampling-graph output-aliasing fix (#31852/#32015), shipping builds b12246→b12249 in-window. In SGLang, the morning batch brought FlashInfer `b12x` block-fp8-as-MXFP8 linears for SM12x GPUs (#45262), an SM120 `hc_mix` BLOCK_K=32 tuning (#45264), a fused int8 Markov-walk DSpark draft kernel for Hopper (#42855), gfx1250 mixed W4A8 gpt-oss MoE (#47398) and MiniMax-H3-Image model support (#49552). vLLM merged ~9 PRs including cuDNN FlashInfer BF16 matmul registration (#59025), an INT8-MoE per-channel weight-scale fix (#65177) and a ROCm DSA short-prefill skip (#61942).
No in-window SGLang/Ollama versioned releases; Ollama stays at v0.40.16 (GA'd pre-window, already footnoted).
*Local inference note:* direct — FP8 KV-cache and SM12x kernels both target exactly this class of hardware.
Source: llama.cpp commits/releases — github.com, Nov 10, 2026 — https://github.com/ggml-org/llama.cpp/releases ; SGLang — https://github.com/sgl-project/sglang ; vLLM — https://github.com/vllm-project/vllm

## Agent tools

**Amazon launches Ring Robotics with the Drone Cam ($249.99) and Rover Cam ($999.99), driven by "AI Pilot"** — Event date: Tuesday, Nov 10 (in-window).
Ring's new division ships two mobile cameras steered by AI Pilot, a custom navigation system combining onboard cameras, specialized vision models, on-device sensors and cloud processing: it builds a floor plan from a walkthrough, suggests patrol points, plans routes and autonomously routes around furniture and obstacles; the Drone Cam flies preset or trigger-driven routes (Rover is the commercial patroller, 4K streaming, Astro lineage per WIRED). Rover Cam ships Dec 16 for $999.99; Drone Cam ships Jan 27, 2027 for $249.99 — and still needs FCC authorization before it can be sold.
*Why it matters:* this is consumer embodied-agents infrastructure normalized at a $249 price point — always-on indoor autonomy, mapping your home, with the privacy/security surface (and FCC/patent-debt questions) that implies. Jamie Siminoff frames it as five years of work finally "ready."
*Local inference note:* hybrid by design — vision models and sensors on-device, route planning in the cloud.
Source: "Ring announces Ring Robotics, ushering in a new era of security" — aboutamazon.com, Nov 10, 2026 — https://www.aboutamazon.com/news/devices/ring-drone-cam-rover-cam-security-robot ; CNBC — https://www.cnbc.com/2026-11-10/amazons-ring-unveils-flying-drone-robot-to-patrol-homes-businesses.html ; Bloomberg — https://www.bloomberg.com/news/articles/2026-11-10/amazon-s-ring-unveils-250-flying-security-camera-after-delays

**Scale Labs + CAIS Remote Labor Index: Claude Fable 5.2 automates 41.3% of real freelance projects** — Event date: Tuesday, Nov 10 (results posted this morning; update on ledger claude-fable-mythos-5-2).
The first independent benchmark on yesterday's Fable 5.2/Mythos 5.2 launch: on 240 verified Upwork projects (avg ~29 hours, ~$632.60 each, evaluated by trained humans in native apps like Blender/GIMP/Audacity), Fable 5.2 delivered client-acceptable professional work on 41.3% — versus Opus 5.5 at 21.25%, GPT-6 Astra at 20.83% and Gemini 4 Argon at 17.08%. A year ago the leader (Manus) managed 2.5%. Largest gains in audio editing, architectural design and 3D modeling (~50 tasks no prior agent ever solved); still weak on animation and industrial product design.
*Why it matters:* a doubling of accepted-work rate on paid human labor in one model generation is the most concrete economic datapoint yet from the Fable 5.2 wave — and the ~2.5%→41% year-over-year curve is the number labor-market arguers will quote for months. (Anthropic's own "Jagged AGI" capability profile for Fable 5.2 also circulated on Techmeme this morning.)
Source: "Anthropic's Fable 5.2 leads Remote Labor Index with 41.3%" — siliconreport.com, Nov 10, 2026 (~08:20 ET) — https://www.siliconreport.com/anthropic-s-fable-5-2-leads-remote-labor-index-with-41-3 ; CAIS on X — https://x.com/CAIS/status/2119949247948972441

**Mercor raises $750M Series D at $20B — double last year's mark** — Event date: Tuesday, Nov 10 (in-window).
CEO Brendan Foody's post and Reuters (late morning) confirm: $750M at $20B post-money, up from $10B in October 2025, in a round including an employee tender with participation from Benchmark, Felicis, General Catalyst, funds managed by Apollo affiliates, NVIDIA, Salesforce, Zoom, StepStone and others. Mercor sells evaluation + post-training infrastructure on a 5M-expert network, has acquired Deeptune (recreated enterprise apps where agents practice workflows), and says it now spends 3x on inference as on salaries.
*Why it matters:* this is the "evals as the new data-labeling" thesis getting a $20B price — the same expert-network flywheel Anthropic and OpenAI rely on, now productized for enterprises that want to post-train their own agents on their own workflows.
Source: "Organizing human intelligence for the entire economy" — mercor.com, Nov 10, 2026 — https://www.mercor.com/blog/series-d/ ; Reuters — https://www.reuters.com/legal/transactional/ai-training-startup-mercor-valued-20-billion-latest-funding-round-2026-11-10/

## AI security and governance

**Anthropic IPO update: up to ~$1.4T fully diluted, $81B run-rate revenue, $90.4B total raise** — Event date: Tuesday, Nov 10 (Reuters exclusive this morning; update on ledger update:anthropic-s1-ant / this morning's roadshow launch at the 07:00 fire).
The updated prospectus filed Tuesday (after Monday-night draft terms showing $69.3B were retracted) sets 825M Class A shares at $70–$78 (up to $64.4B) plus a $26B private placement with cornerstones Google, Nvidia, AMD, Microsoft et al. Reuters: up to ~$1.4T on a fully diluted basis (~$1T non-diluted); run-rate revenue hit **$81B as of October 31**. Analysts read the ~20% discount to the $1.5T investor-talk high end as deliberate room to bid up. FT/Sources this morning: Goldman Sachs models ~$52B Anthropic revenue in 2026, $600B+ by 2030, operating profit 2029, positive FCF 2030.
*Why it matters:* $81B run-rate against cooling AI tape (post-midterm rout, CoreWeave/Nebius bond stress) is the sector's most-watched solvency datapoint — this IPO prices the whole private AI debt complex behind it.
Source: "EXCLUSIVE: Anthropic eyes $1.4 trillion IPO valuation in test of Wall Street's AI fervor" — reuters.com, Nov 10, 2026 — https://www.reuters.com/business/anthropic-ipo-raise-much-644-billion-latest-draft-prospectus-shows-2026-11-10/

**TSMC board approves up to $1B into Anthropic and OpenAI** — Event date: Tuesday, Nov 10 (in-window).
TSMC's board approved Tuesday up to $500M of Anthropic shares at the IPO price and up to $500M of OpenAI preferred stock — while also signing off ~$40.93B in new capital appropriations (per company resolutions; Reuters). Wen-Yee Lee's report frames it as the foundry "positioning itself to benefit not only from supplying AI infrastructure, but also owning stakes in the industry's most influential developers," after a sixth straight record-profit quarter and "extremely robust" AI demand.
*Why it matters:* the world's most critical fab taking equity in both frontier labs deepens the circular-financing pattern regulators (and the incoming House Oversight demands on Nvidia) have been probing — supplier, financier and customer increasingly the same trade.
Source: "TSMC to invest up to $1 billion in Anthropic, OpenAI" — reuters.com, Nov 10, 2026 — https://www.reuters.com/world/china/tsmc-invest-up-1-billion-anthropic-openai-2026-11-10/

Security lane: no *new* AI-security disclosures verified in-window — BleepingComputer's morning Fireworks piece re-covers yesterday's already-ledgered disclosure (dev-machine access via third-party cloud); the Australia regulator and LoSapio-to-CISA stories were reported at the 07:00 fire and are not repeated.

## Also noted

- **Kora OS**: Cyanogen co-founder Kirt McMaster's Kora Systems (ex-Playtron) unveiled Tuesday a free Linux+Nix desktop OS with AI models/agents integrated system-wide — Cortex engine runs tasks locally when possible, Snapdragon X2 Elite + Intel/AMD support, 2027 launch, enterprise "Humain OS" with Saudi's Humain; built by ~50 engineers with Codex/Claude, which McMaster says sped development "at least fivefold." — TechCrunch, Nov 10, 2026 — https://techcrunch.com/2026-11-10/after-challenging-googles-android-with-cyanogen-its-co-founder-takes-aim-at-windows-for-ai-era/
- **TSMC-adjacent industry move**: Alibaba confirmed Tuesday its chip unit T-Head confidentially filed (Form A1, Sept 11) for a Hong Kong IPO — in-house AI silicon (Zhenwu V900, 3x prior gen) spun off as Nvidia-restriction hedge. — Reuters (syndicated), Nov 10, 2026 — https://www.centraloregondaily.com/news/consumer/alibaba-chip-unit-t-head-files-confidential-hong-kong-ipo-application/article_09805f97-86e7-5899-b9d6-bb5a874d5f91.html
- **Path Robotics** raised $300M Series E led by Georgian at $2.45B post (Tuesday PR): "physical AI" welding model Obsidian + Rove quadruped welder, ARR up 17x in 18 months, customers incl. Lockheed Martin, HII, Saronic; >$610M raised to date. — path-robotics.com, Nov 10, 2026 — https://www.path-robotics.com/news/path-robotics-announces-300-million-series-e-led-by-georgian
- **Reddit Reviews** launched: AI-generated product hub summarizing subreddit threads into pros/cons/sentiment for ~10,000 items, US on iOS/Android/web, no paid placements. — siliconreport.com, Nov 10, 2026 — https://www.siliconreport.com/reddit-launches-reddit-reviews-hub-covering-10-000-items

## Just missed the window

- **MiniMax H3-Image** — open-weight image generation/editing model from the H3 family (33.1B DiT, ~16MP output, 6-step distilled, up to 16 reference images, Community License) — was announced Monday evening ET (IT Home / official MiniMax X post, "16 million pixels in 10–20 seconds"); the weights repo was still taking commits this morning (lastModified 11:21 ET) and SGLang merged model support in-window (#49552). One-time footnote — see ledger.

## Coverage notes

- **Verified count: 12 qualifying items** (5 lead write-ups + 2 governance items + 4 also-noted + 1 once-ever missed footnote), all from pages fetched this run with in-window timestamps (engines verified via GitHub API commit/release timestamps).
- **Ledger dedupe: 13 candidates suppressed** as already reported — Anthropic roadshow launch, Megaspeed/Huang succession cascade, SoftBank Q2/$3.7B writedown, Australia AI regulator, LoSapio/CISA, Fireworks breach (BC re-coverage), Apple usage packs (HN re-spike), LongCat-2.5 weights (repeat-missed), Ollama v0.40.16 (repeat-missed), KVARK Sparkle 1.1 + Comet 60B GGUF (repeat), Muse Ember gripe thread, "Jagged AGI" re-coverage, Qwen4 speculation posts.
- **Dead ends / exclusions:** Bay's $50M agent-security launch (Tel Aviv dateline dates to ~19:00 ET Monday — pre-window, excluded per event-date law); Sakana AI 2027-IPO report and Terminal-RSI-27B (publication/creation timestamps unverifiable); no fresh THN/CISA-KEV/AI-security items found in-window. Techmeme river items were resolved to fetchable articles; no index was down.
