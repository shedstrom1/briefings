# AI News Briefing — Sunday, November 15, 2026, 17:00 ET edition

**Window: 2026-11-15 12:00 → 17:00 ET (5 hours, previous fire → this fire). New items only — events first disclosed inside the window, deduped against the reported ledger.**

## TL;DR

- **Pax Silica partners signed the "San Francisco Declaration"** — the US plus South Korea, Japan, Australia, the UK, Israel, India, Qatar and the UAE will build a joint list of sensitive AI technologies and critical supply chains to protect from China, days before the Trump–Xi APEC meeting in Shenzhen.
- **Jensen Huang is weighing a comeback as Nvidia CEO**: an ally told the FT a "war room" of Silicon Valley executives is forming to lobby the board and seek White House support — with the ally saying the effort has Huang's backing.
- **Basalt 0.4.0 shipped its first dense model** — Qwen 3.8 27B on a single RTX 5090 at up to 776 tok/s structured / 194 tok/s prose (NVFP4 checkpoint), with the author saying the kernels were built "in preparation for Qwen 4."
- **BigMoeOnEdge** (community, self-published) claims any MoE GGUF on any hardware — 35 tok/s Qwen3.6-35B on an 8GB card, 10 tok/s on the 125B Flash-Next from a 16GB MacBook.
- Late engines were active: llama.cpp b12423–b12426 with new TTS/ASR multimodal support, vLLM ROCm prefill context-parallel for DeepSeek-V4.1-Flash, SGLang gfx1250 ring all-reduce, and a fresh Codex alpha.

## AI security and governance

**US and eight allies sign "San Francisco Declaration" to jointly protect AI supply chains from China** — Event date: Sunday, Nov 15, 2026 (in-window).
Undersecretary of State for Economic Affairs Jacob Helberg told the Financial Times that Pax Silica partners — the US, South Korea, Japan, Australia, the UK, Israel, India, Qatar and the UAE — agreed Sunday to jointly select sensitive AI-related technologies and critical infrastructure to protect and to share information on where supply chains should diversify. Germany, Sweden, the Netherlands and the European Commission are expected to sign next week. The declaration calls for working groups on AI-supply-chain vulnerabilities and "economic hubs" to attract investment, asserts that economic security is national security, and targets theft or unauthorized transfer of technologies that could give hostile actors military or intelligence advantage. It lands three days before Trump meets Xi at APEC in Shenzhen.
*Why it matters:* This converts Pax Silica from a declaration-and-timeline exercise into an operational coordination mechanism — a shared protected-technology catalog among allies is the kind of instrument that later hardens into investment screening, re-export conditions and procurement rules. It is not a Commerce/BIS rule change (entity-list and license obligations are unchanged), but allied lists historically pre-announce them — and it adds multilateral texture to the export-control fight now running through Washington after Huang's resignation.
*Local inference:* none claimed; relevant to component supply, not consumer sizing.
Source: "US and allies sign accord to protect AI supply chains from China" — Financial Times, Nov 15, 2026 (~12:47 PM ET): https://www.ft.com/content/3c2197fc-9618-4838-91e6-522ce1a96a22
Source: "Report: US, Allies Sign Accord to Protect AI Supply Lines From China" — Newsmax via Yahoo News, Nov 15, 2026 (~1:12 PM ET): https://www.yahoo.com/news/politics/articles/report-us-allies-sign-accord-181215730.html

**Update on previously reported (megaspeed-alice-huang): Jensen Huang discusses comeback as Nvidia CEO with allies** — Event date: Sunday, Nov 15, 2026 (in-window).
The Financial Times reports Huang is considering returning to the CEO seat and has discussed it with confidants since resigning Thursday. One ally said a "war room" of Silicon Valley executives is being assembled to lobby for his reinstatement and will try to garner White House support, adding "everyone that matters in tech outside the frontier labs loves Jensen" — and that the effort has Huang's support. The piece comes days after the FT reported the board approached Arm CEO Rene Haas, and hours after Treasury Secretary Bessent urged Nvidia to hire an outside CEO (this morning). Reuters ran the story at 2:26 PM ET; Nvidia did not respond outside business hours.
*Why it matters:* A reinstatement campaign changes the succession calculus Nvidia began publicly just three days ago — and it puts the White House directly into a board-level decision at the most valuable company in AI, the same week a "Pax Silica" bloc is forming partly as a response to the same China-entanglement scandal.
Source: "US and allies sign accord…" / "Jensen Huang Discusses Comeback as Nvidia CEO With Allies, FT Reports" — Reuters via U.S. News & World Report, Nov 15, 2026, 2:26 PM ET: https://www.usnews.com/news/top-news/articles/2026-11-15/jensen-huang-discusses-comeback-as-nvidia-ceo-with-allies-ft-reports
Source (primary report): Financial Times, Nov 15, 2026 — via Techmeme top slot (~2:45 PM ET pickup).

## Models and local inference

**Basalt 0.4.0: first dense model — Qwen 3.8 27B at up to 776 tok/s on a single RTX 5090** — Event date: Nov 15, 2026, 12:30 PM ET launch post (v0.4.0 tag published Nov 14, 6:51 PM ET).
jesdga95's Blackwell-focused engine Basalt added its first dense model, Qwen 3.8 27B: single-stream up to 776 tok/s on structured output, 262 tok/s conversation, 194 tok/s prose on one RTX 5090, with prefill at ~12,000 tok/s at 4k context, up to 262K context with tools/thinking/images/video, and 92.8% top-1 agreement vs the BF16 reference (KLD 0.0262; after pushback in-thread the author reported 16–32K-token agentic-row measurements at KLD 0.016 / 97.1%). The author frames it as "in preparation for Qwen 4, so we had a headstart on the dense kernels."
*Why it matters:* Basalt is the fastest reported single-GPU path for Qwen 3.8-class dense models on consumer Blackwell, and day-1 dense-kernel readiness is a signal the local community is pre-positioning for the Qwen 4 release everyone is waiting on. Caveats: vendor-self-measured numbers, the "up to 770" headline rides on the NVFP4 checkpoint's 4-bit activations (as NInfer/Infernix also do), and long-context agreement is still the open debate in-thread.
Source: "Basalt 0.4.0: More models, still as fast — Qwen 27B up to 770 tok/s on a 5090" — r/LocalLLaMA, Nov 15, 2026, 12:30 PM ET: https://www.reddit.com/r/LocalLLaMA/comments/1xx1cu9/basalt_040_more_models_still_as_fast_qwen_27b_up/
Source: GitHub release v0.4.0, jesdga95/basalt (tag published Nov 14, 2026, 6:51 PM ET): https://github.com/jesdga95/basalt

**BigMoeOnEdge: any MoE GGUF on 8–16GB cards, Macs and phones via adaptive expert streaming (community, self-published)** — Event date: Nov 15, 2026, 3:11 PM ET post.
Helldez's BigMoeOnEdge claims to run any MoE GGUF that llama.cpp can load, at whatever quant, on NVIDIA/AMD/Intel/Mac/Android/iOS/CPU with an `--auto` tuning mode and no repacking — positioning against the model-specific engines (Strata, ninfer, Basalt). The repo (873 stars) says output matches llama.cpp exactly and links raw bench logs; a llama.cpp discussion (31876) is open on upstreaming.
*Why it matters:* If the logs hold up, this is the "runs anything anywhere" alternative to single-model tuned engines — the practical middle ground for people with 8–32GB hardware who don't want to babysit per-model recipes. Treat all numbers as vendor/community claims until third-party repro.
*Local inference:* Explicitly targets 8–32GB consumer cards and edge; builds on stock llama.cpp code paths.
Source: r/LocalLLaMA, Nov 15, 2026, 3:11 PM ET: https://www.reddit.com/r/LocalLLaMA/comments/1xx5fng/bigmoeonedge_outperforms_strata_and_colibri_on/
Source: GitHub, Helldez/BigMoeOnEdge (bench data under docs/bench-data): https://github.com/Helldez/BigMoeOnEdge

## Agent tools

**Late engines sweep (also-noted)** — Nov 15, 12:00–5:00 PM ET: llama.cpp shipped four builds b12423–b12426 (19:55–21:05 ET) with mtmd support for Higgs v3 STT + VibeVoice-ASR (#32329), Zonos3-Mini TTS (#32020), qwen3vl output-head fix (#32349) and a jinja-features add/revert cycle; vLLM merged four including DeepSeek-V4.1-Flash eager prefill context-parallel on ROCm (#66842) and compressed-tensors CUTLASS block-FP8 fast-start cache restarts (#63976); SGLang merged a gfx1250 ring + unrolled 2-shot-push custom all-reduce (#49955) and tokenizer_manager pool-index validation (#49759); Ollama took one commit keeping weights out of pinned host memory on Windows iGPUs (#19636); OpenAI tagged Codex rust-v0.171.0-alpha.6 (1:58 PM ET); LangChain pushed a partner-package wave (langchain-xai 1.4.0, langchain-groq 1.2.0, langchain-deepseek 1.2.0, 4:34–4:49 PM ET) following this morning's langchain-core 1.8.1.
Source: GitHub commits/releases, ggml-org/llama.cpp, vllm-project/vllm, sgl-project/sglang, ollama/ollama, openai/codex, langchain-ai/langchain — Nov 15, 2026, 12:00–5:00 PM ET: https://github.com/ggml-org/llama.cpp/releases

**Essay: "Your Agent Will Pick the Hackable One"** — Nov 15, 2026 (HN front, 5:10 PM ET).
Geoff Stearns (product manager at Google; views his own) argues AI coding tools collapsed the cost of writing firmware (M-VAVE FM-1 synth custom firmwares, Doom port, Behringer's ensuing GPL openings), so when buying decisions flow through agents, agents will favor devices whose firmware they can change — pressuring hardware makers to open up; owner-held signing keys and recoverable installs as the test of good-faith openness.
*Why it matters:* A crisp thesis for the agent-hardware frontier, and it's already visible: vendor firmware opens (M-VAVE, Behringer, Akai, Erica) happened inside weeks.
Source: geoffstearns.com, Nov 15, 2026: https://geoffstearns.com/blog/your-agent-will-pick-the-hackable-one/ — HN discussion (story 50510799): https://news.ycombinator.com/item?id=50510799

**Live P2P open-model sharing tracker goes activity-feed (update on previously reported: libreagi)** — Nov 15, 2026, 2:05 PM ET HN post.
Pirateface (the "Every AI Model Should Ship as a Torrent" author, ledger Nov 12) posted a live activity page for peer-to-peer sharing of open-weight model files, showing transfers on the private-tracker archive; the HN thread (11 pts) is the in-window artifact — the site itself failed direct fetch this run.
*Why it matters:* Open-weight availability is drifting from hub hosting toward BitTorrent-style distribution — a resilience signal the community now tracks as a product surface.
Source: Hacker News, story 50509654, Nov 15, 2026, 2:05 PM ET: https://news.ycombinator.com/item?id=50509654

**Show HN: Widefleet Cloud — deploy agent-built internal apps with SSO, per-app storage and IT controls** — Nov 15, 2026, 1:58 PM ET.
Hosted platform (code open at github.com/widefleet/widefleet) for the "vibe-coded internal app" pile: company login, per-app workers with their own databases/object storage, centrally managed connectors so users never touch API keys, cron/queues for background workflows.
Source: Hacker News, story 50509606, Nov 15, 2026, 1:58 PM ET: https://news.ycombinator.com/item?id=50509606

**Also noted (community, local):**
- **inside-llm** — open-source browser tool to watch GGUF generation live via llama.cpp: two-model lockstep/free comparison for quant divergence, sampler-gate tracing (penalties/top-k/top-p/min-p/grammar), entropy+JSD timelines, exact KV-snapshot branch trees, optional logit lens. (r/LocalLLaMA, 4:46 PM ET: https://www.reddit.com/r/LocalLLaMA/comments/1xx7qbf/i_have_created_a_tool_to_observe_what_happens/)
- **audio.cpp demo** — SoulX-FlashHead + LFM3-Audio + OWSM v4 assembled into a real-time local avatar chat pipeline. (r/LocalLLaMA, 1:29 PM ET: https://www.reddit.com/r/LocalLLaMA/comments/1xx2vi0/audiocpp_soulxflashhead_lfm3audio_owsm_v4/)
- **MoE offload tuning** — Qwen3.6-35B-A3B on a 3090: prefill 345→466 tok/s by tuning cudaMemcpy behavior for expert offload. (r/LocalLLaMA, 12:43 PM ET: https://www.reddit.com/r/LocalLLaMA/comments/1xx1owx/moe_offload_on_a_3090_prefill_345_466_ts_by/)

## Just missed the window
- **TSMC's new $500M venture fund to back chip startups and suppliers ("the next Nvidia")** — disclosed in a Nikkei interview with Kevin Zhang Nov 13–14 (pre-window); Techmeme picked it up Sunday afternoon. Separate from the board's up-to-$500M-each Anthropic/OpenAI stakes already reported. Source: https://asia.nikkei.com/editor-s-picks/interview/tsmc-seeks-to-back-next-chip-titan-with-new-investment-arm

## Coverage notes
- **Verified: 4 qualifying items** (2 leads in governance incl. one "Update on previously reported", 2 in models/local) **+ 6 also-noted entries**, each from a fetched page with an in-window timestamp or dated repo/API record; leading items read in full; second sources where coverage exists (FT+Newsmax/AP; Reuters+FT; Reddit+GitHub).
- **Ledger dedupe: 11 candidates suppressed as already reported or already-footnoted** — AI-code-should-be-open-source HN (12:00 today), GLM stealth leaderboard entries (12:00 today), X3Guard re-promotion (footnoted 12:00 today), Italy €805M Meta fine re-coverage (Nov 14), Cursor–Fireworks key re-coverage (footnoted 07:00 today), Megaspeed "left in 2025 / internal probe" statement (early-Sunday disclosure pre-window + ongoing ledger thread), Trump-APEC-Huang avoidance (Nov 13), ChatGPT Wallet (Nov 13), LangChain $30M (Nov 12), TSMC $1B OpenAI/Anthropic stakes (Nov 10), Anthropic IPO pre-pricing commentary (ledger anthropic-s1-ant). One repeat "Just missed" (SEBI AI guidelines) suppressed per once-ever rule.
- **Excluded on event date:** Epoch AI's Horinger (Huawei) compute analysis was Techmeme-"New" today but the report is dated Nov 6; Zyphra Zonos3-Mini (Nov 10–14) and Nebuly $21M (Nov 12) are pre-window — today's llama.cpp Zonos3-Mini support is captured in the engines sweep.
- **Dead ends / limitations:** DuckDuckGo backend returned "no results" on 4 of ~13 queries (fell back to Google News RSS + direct fetches); pirateface.co/activity refused direct fetch (HN item cited instead); HuggingFace new-models API failed both fetch attempts — new-open-weight lane covered via theopenweights.com and community feeds instead; Bloomberg's Nvidia/Taiwan-smuggling story excluded (Bloomberg timestamp 9:48 AM ET, pre-window, paywalled); Nidus $30M seed (Techmeme More News) could not be resolved to a fetchable article in two attempts — logged, not chased.
- Community lanes walked: r/LocalLLaMA new-feed (25 posts, 14 in-window, gated on exact created_utc) and HN (window query points≥3 + front page). Engine repos checked via GitHub API for exact merge/tag timestamps.
