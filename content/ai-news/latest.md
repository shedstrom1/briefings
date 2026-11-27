# AI News Briefing — thin Black Friday window: llama.cpp lands FP8/NVFP4 quant-scale support

Window: 2026-11-27 07:00–12:00 EST (previous fire → now). New items only; the reported.md ledger gate suppressed any event already covered by earlier fires.

## TL;DR

- Only one substantive in-window release cleared verification: llama.cpp shipped four builds between 08:51 and 10:12 EST, headlined by generalized FP8/NVFP4 quant-scale support in ggml (PR #28898) plus MoE-cache and CUDA/Metal correctness fixes.
- No new closed-weight or open-weight frontier model was announced inside the window. The day's marquee stories — the US Air Force Cloud One breach, a GB300 rack surfacing at an Alice Huang-linked Shanghai data center, SAP's 10,000-job AI restructuring, and OpenAI's "Athena" semantic-layer report — all broke before 07:00 EST and are footnoted or excluded, not re-listed.
- The community lane produced two modest, self-reported items: an agent-initiated phone-call demo and an ESP32 desktop body for a local AI assistant. No vendor agent-framework or security disclosure qualified.
- Thin day, stated plainly: **one primary-source item and two community also-noted items**. No padding.

## Models and local inference

**llama.cpp adds general FP8/NVFP4 quant-scale support and three correctness fixes in four in-window builds**
Event date: 2026-11-27 (builds published 13:09–14:12 UTC = 09:09–10:12 EST, all inside the window).

The headline build, b12818 (08:51 EST), teaches ggml per-tensor, per-channel, per-expert and per-expert-per-channel scaling, adds optional activation scales to `mul_mat` and `mul_mat_id`, and handles bitnet scales — the plumbing required for fine-grained low-bit quantization of mixture-of-experts weights. Three follow-ups landed in the same window: b12814 (09:31 EST) lets the MoE cache read experts selected on a different backend, fixing the layer-split `inkling2_loop` path (issue #33101); b12812 (09:09 EST) fixes a Metal fused-kernel concurrency race in which fused top-k could run alongside the router matmul writing its input; and b12815 (10:12 EST) fixes a CUDA NVFP4 MMQ `y_scale` over-read in the stream-k fixup. No throughput numbers were published with the builds — this is a capability change, not a benchmark.

**Why it matters:** per-expert and per-channel scales are what make 4-bit-class NVFP4/FP8 MoE checkpoints accurate enough to run rather than merely load. That is the practical difference between a quant that fits in VRAM and one that is usable.

The third is an Apple Silicon Metal race fix; the fourth is a backend-agnostic MoE-cache correction.

Source: GitHub, ggml-org/llama.cpp releases feed — b12818 "ggml: support quant scales for fp8 and nvfp4 (#28898)" (2026-11-27 13:51:55 UTC / 08:51 EST); b12812 "metal: check all fused nodes for concurrency (#33175)" (13:09:53 UTC / 09:09 EST); b12814 "moe-cache: support experts selected on another backend (#33133)" (13:31:40 UTC / 09:31 EST); b12815 "cuda: fix NVFP4 MMQ y_scale over-read (#33136)" (14:12:56 UTC / 10:12 EST). https://github.com/ggml-org/llama.cpp/releases.atom

**Also noted — Show HN: open-source ESP32 desk body for a local AI assistant.** A hobby project ("BoBe") pairing a cheap ESP32-S3 talking toy with a Mac/Linux desktop app, keeping all API keys off the device; the builder reverse-engineered the screen, audio and power-latch pins and added a Raspberry Pi Pico 2 to press its buttons behind a simulated packet-loss link. Self-reported, no benchmarks. Posted to Hacker News at 11:29 EST, inside the window. Source: Hacker News item 50668024, "Show HN: Open-source ESP32 desk body for a local AI assistant" (2026-11-27 15:29 UTC / 11:29 EST), https://news.ycombinator.com/item?id=50668024 ; project page https://johnkozaris.com/blog/bobe-toy-robot-body

## Agent tools

No vendor or framework release qualified in this window. One community demo cleared the timestamp gate.

**Also noted — Show HN: "AI agents can now call you."** A demonstration of agent-initiated outbound phone calling, posted to Hacker News at 11:04 EST. Self-reported, with no independent verification and no model or vendor announcement behind it; recorded here as a signal of interest in outbound voice tool-use, not as a product launch. Source: Hacker News item 50667755, "Show HN: AI agents can now call you" (2026-11-27 15:04 UTC / 11:04 EST), https://news.ycombinator.com/item?id=50667755

## AI security and governance

No qualifying new disclosure originated inside this window. The items that dominated security feeds during these five hours were re-reports of events disclosed before 07:00 EST: the Cloud One intrusion (Reuters exclusive, 06:01 EST), the Blackwell serial-number finding (Bloomberg, 06:45 EST), the SAP GitHub incident (BleepingComputer, 04:56 EST), and Meta's UK Muse terms changes (CMA statement, 24 November). Per the event-date rule they are not re-listed as new items; two are footnoted once below.

### Just missed the window

- **US Air Force Cloud One breach.** Reuters' exclusive (Mike Stone and Raphael Satter) published 06:01 EST, 59 minutes before the window opened; in-window BleepingComputer and Guardian coverage is re-reporting, not a new disclosure. Attackers reached the control plane via a software vulnerability; updates were blocked, migrations paused, some administrator rights revoked, and Cyber Command was called in. Source: Reuters via U.S. News, "Exclusive — Hackers Breach US Air Force Flagship Cloud One Computing Platform" (2026-11-27 06:01 EST), https://www.usnews.com/news/top-news/articles/2026-11-27/exclusive-hackers-breach-us-air-force-flagship-cloud-one-computing-platform
- **Blackwell rack at an Alice Huang-linked Shanghai data center.** Bloomberg published at 06:45 EST, 15 minutes before the window. Serial numbers in November regulatory filings identify a Gigabyte-built GB300 NVL72 rack at the Yangtze River Delta Project, offered by Shanghai Shuoyao as collateral for a 96 million yuan ($14 million) loan; a second firm described a GB200 server. Source: Bloomberg, "Nvidia Blackwell Chips Turn Up at China Site Tied to Alice Huang" (2026-11-27 11:45 UTC / 06:45 EST), https://www.bloomberg.com/news/articles/2026-11-27/nvidia-blackwell-chips-turn-up-at-china-site-tied-to-alice-huang

## Coverage notes

- **Verified count:** 1 primary-source in-window item (llama.cpp, four builds, each with an in-window publish timestamp) and 2 community also-noted items with in-window Hacker News timestamps. Zero new model releases, zero agent-framework releases, and zero new security/governance disclosures originated in the window. No padding.
- **Ledger dedupe:** roughly 15 candidates were suppressed as already reported or already footnoted, including SAP-Project-Granite, DeepMind-agent-compute, Claude-sabotage-SOTA-repro, Dante-Security-10m, FL-AI-impersonation-sentence, engines-1127-housekeeping, update:glm-5.5-postraining-rumor, update:BIS-remote-access-IFR, starmind-fcc, hy-mt3 (already footnoted) and minimax-dragos-prompt-links (already footnoted).
- **Sources and angles checked:** Techmeme (top/newest rivers), The Decoder, BleepingComputer, HeadsUpAI, BenchLeader, LM Market Cap, The Open Weights, Hacker News Algolia by-date (four query lanes: AI, model, LLM, open source), GitHub release feeds for llama.cpp, vLLM, SGLang, Ollama, OpenClaw and Pydantic AI, plus official pages from Kimi and OpenAI and reporting from Reuters/U.S. News, The Guardian and Bloomberg/Yahoo Finance.
- **Dead ends and limitations:** the Reddit reading lane was down this run (anonymous Atom feed returned 404, then 429) — no Reddit signal. The search backend intermittently returned "no results." Hugging Face's newly-created model listing exposed only opaque relative timestamps, so no open-weight release could be anchored in-window. vLLM, SGLang, Ollama, OpenClaw and Pydantic AI had no in-window release or security advisory. Black Friday explains much of the thinness; no story was stretched across the boundary to compensate.
