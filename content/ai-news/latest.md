# AI News Briefing — November 26, 2026 (12:00 ET)

**Window:** 2026-11-26 07:00 → 12:29 EST (the 12:00 fire; 5h29m). New-items-only. Event date, not article freshness, governs inclusion.

## TL;DR

- **No frontier closed-weight launch landed in the window.** The two substantive model disclosures are community/open-weight: a full-duplex speech-to-speech system built *from* Qwen3.5-4B open weights, and an AMD-specific llama.cpp fork that roughly doubled token generation on a consumer eGPU.
- **Kestrom shipped a byte-verifiable model-editing toolkit** — every weight edit comes with a machine-rebuildable report proving what did and did not change, with tiered guarantees demonstrated on Qwen3.5-9B/0.8B.
- **OpenAI corrected a GPT-6.2 launch billing error**, so paid users now hit usage limits up to 40% faster on Astra and 25% faster on Sol — a material change to an already-reported model.
- **AI security/governance was quiet in-window.** The notable Microsoft ARTEX architecture deep-dive is dated November 25 and lands as the single "just missed" footnote.

## Models and local inference

### Duo: a full-duplex speech-to-speech model built from an open-weight LLM
**Event date:** November 26, 2026 (technical report; HN 11:35 EST).

Duplex IO published a technical report on Duo, a full-duplex speech-to-speech model that starts from the open-weight **Qwen3.5-4B** hybrid (Gated DeltaNet + attention) rather than training a speech model from scratch. It reads and writes six interleaved streams — four text and two audio — on a shared 12.5 Hz / 80 ms clock, using a frozen text model as teacher, a streaming FastConformer encoder for user audio, Mimi-family continuous latents plus a Pocket-TTS-initialized flow-map head for assistant audio, delayed-streams modeling, and an audio sliding window that pins the voice prompt while keeping the KV cache bounded. Tool calls run on text-only rows between audio frames, and text-teacher distillation preserves instruction-following. An **open-weight preview and benchmarks are forthcoming**; no weights are published yet.

**Why it matters:** It is a concrete template for turning a small open-weight LLM into a real-time voice agent without a from-scratch speech stack — squarely relevant to local inference. **Local inference:** the 4B backbone, 32-dimensional continuous audio latents (no codebook depth transformer), and bounded audio KV window are all designed for modest hardware; specifics of the eventual release are not yet given.

Source: "How we built Duo," Anders Thuesen (Duplex IO), November 26, 2026 — https://duplexio.ai/blog/how-we-built-duo/ ; HN thread (11:35 EST) — https://news.ycombinator.com/item?id=50656359

### AMD-specific llama.cpp fork roughly doubles token generation on a consumer eGPU
**Event date:** November 26, 2026 (post 09:59 EST; HN 12:11 EST, front page, 42 points).

A hands-on write-up recommends **llama-cpp-rdna-boosts**, a minimal set of AMD-RDNA-specific patches re-applied to pinned llama.cpp commits, distributed as prebuilt `server-rocm-10.1` container images. On an AMD R9700 eGPU running `ukisai/Swift-1.5-Qwen3.8-27B-GGUF` at Q6_K_L with MTP enabled, the author measured token generation rising from ~35 to ~55 tok/s and prefill from ~700 to ~1300 tok/s. The post also usefully contrasts the trend of "vibe-coded" inference forks that optimize by changing *how the model thinks* (e.g., preferring VRAM-resident experts) rather than the hardware path. These are self-reported numbers on one setup.

**Why it matters:** it is the clearest in-window, reproducible-ish local-inference result — a small patch set, not a whole new engine, delivering a ~1.6x decode and ~1.9x prefill gain on AMD silicon.

Source: "AMD-specific fork of llama.cpp," devnull.land, 11/26/2026 9:59:36 AM — https://devnull.land/amd-specific-llama-cpp ; HN thread (12:11 EST) — https://news.ycombinator.com/item?id=50657265

### Update on previously reported — GPT-6.2 Astra/Sol billing correction
**Event date:** November 26, 2026 (The Decoder, 11/26; Techmeme Newest river, in-window).

OpenAI confirmed a billing error at the GPT-6.2 launch that had over-discounted usage, per Codex engineer Thibault Sottiaux. The fix means paid users now reach limits **40% faster on Astra and 25% faster on Sol** than the prior week (still longer-lasting than GPT-6 Astra / GPT-6.1 Sol); other models were unaffected, no retroactive charges, and all Go/Plus/Pro/Business subscribers get one banked reset, with Business also getting free Fast Mode through year-end. This is a material change to the already-reported `gpt-6.2-astra-sol` launch. Separately, the same report notes OpenAI is testing a merge of Chat and Work modes for select Pro users — already covered (`chatgpt-codex-limits-merge`, `openai-chat-work-merge-confirm`), so not re-listed.

**Why it matters:** effective cost-per-task on the current flagship moved materially for existing subscribers, which changes model-selection economics for heavy agent users.

Source: "GPT-6.2 Astra users now hit limits 40% faster after OpenAI patched a pricing error," The Decoder, Nov 26, 2026 — https://the-decoder.com/gpt-6-2-astra-users-now-hit-limits-40-faster-after-openai-patched-a-pricing-error/ ; TechRadar — https://www.techradar.com/ai-platforms-assistants/chatgpt/weve-fixed-this-now-so-the-same-work-will-use-more-of-your-plans-usage-allowance-openai-admits-it-made-a-mistake-when-launching-gpt-6-2-and-now-paying-chatgpt-users-could-hit-their-usage-limits-up-to-40-percent-faster

### Also noted (models/local inference)
- **Bare-metal OS claim:** an HN post (12:30 EST) points to a self-published X post claiming Qwen3-0.6B runs 54% faster than llama.cpp on a bare-metal OS. Unverified, single-source — https://news.ycombinator.com/item?id=50656879 .
Ollama was quiet.
- **Model trackers:** Ant Group's LingSante 3.1 Air VL appeared in provider catalogs on 11/26 at $0.090/M, but with no primary-source page or independent results — tracked, not reported as a launch.

## Agent tools

### Kestrom: byte-verifiable LLM weight edits with tiered guarantees
**Event date:** November 26, 2026 (HN 09:02 EST).

CAPHTECH released **Kestrom**, a toolkit for editing a language model "inside a range" and letting anyone verify, byte for byte, what changed. `kestrom-verify` (Apache-2.0) rebuilds a `report.json` that a receiver can reproduce with `cmp`; the editor tools (PolyForm license for larger/commercial use) declare the exact rows written and check determinism (U0), bit-identical logits outside the range (G1), and identical greedy output up to the range (G2). Measured results on Qwen3.5-9B include banning 25,451 Chinese-only output rows (mixing 0.32 → 0.0067 with JMMLU accuracy flat), conditional style switching that left 100 quiet prompts bit-identical, and a decode-time loop guard on Qwen3.5-0.8B that raised correct answers from 4 to 26 of 100 MATH-500 problems. The README is unusually candid that several tools missed their targets.

**Why it matters:** it brings reproducibility and non-interference guarantees to the weight-space-editing workflow that has recurred in this brief — useful for auditing model provenance and for safety-relevant edits where "nothing else changed" must be provable.

Source: CAPHTECH/kestrom GitHub repository — https://github.com/CAPHTECH/kestrom ; HN thread (09:02 EST) — https://news.ycombinator.com/item?id=50654870

### Also noted (agent tools)
- In-window framework/CLI commits, no versioned releases: OpenClaw saw heavy housekeeping (native-draft restore after paging older messages, Activity-chart DST fixes, Codex late-worker-notice fix); OpenAI Codex added Guardian V3 shadow-review configuration from the root thread (#64187, 12:05 EST) and MCP message-board notification docs/tests (#64179); pydantic-ai added authority-aware `RepoContext`/`Skills` and Coder project-skill loading. No LangChain or MCP-servers releases in-window.
- A small "behavioral drift detector for AI agents" (HN, 12:15 EST) and a local-first SQLite knowledge graph for coding agents (Dev-Mem, HN, 11:59 EST) were posted as self-published experiments, unverified.

## AI security and governance

No new, in-window AI-specific security or governance event met the verification bar. BleepingComputer's in-window items (the "GoodGuys" fake-interview phishing campaign, 12:15 EST; Remus Gmail-stealing Chrome extension, 10:23 EST) are credential/malware stories without an AI-specific hook and are not listed. The two AI-adjacent stories circulating in-window are out-of-window events being re-covered (see Just missed and Coverage notes).

### Just missed the window
- **Microsoft published a full ARTEX architecture and defender's guide** — detailing the goals/planner/worker/reporter agent loop, model-pool failover (frontier, open-weight, or abliterated models), and behavioral detections — on the Microsoft Security Blog dated **November 25, 2026**, outside this window; The Decoder re-covered it on 11/26. This is the single permitted "just missed" footnote for this event. Sources: https://www.microsoft.com/en-us/security/blog/2026/11/25/what-defenders-need-to-know-artex-agentic-ai-framework-threat-actors-abuse/ ; https://the-decoder.com/microsoft-details-how-state-backed-hackers-use-the-artex-ai-framework-to-automate-cyberattacks/

## Coverage notes

- **Verified count:** 4 substantive in-window items (Duo, AMD llama.cpp fork, GPT-6.2 billing update, Kestrom) plus 4 "also noted" lines, from a community/HN lane, vendor technical blogs, Techmeme/The Decoder/TechRadar, and GitHub commit APIs — well over five distinct outlets. Lead stories were read in full; the GPT-6.2 and AMD items carry a second independent outlet where one exists.
- **Ledger dedupe count:** 14 candidates were suppressed as already reported, including 3 repeat-"Just missed" candidates (Mellum2.1 Instruct, PAN-OS/Fragnesia, SpaceX Starmind FCC) barred by the once-only footnote rule. Top suppressed: GLM-5.5/Kimi K4 delay, Nvidia's SEA server-shipment halt, Aaronson crypto bombshells, CBS Chinese-owned Nvidia partners, Character.AI 42-AG auction objection, Moat gateway, 1Password breach, Italy/Switzerland AI-agent attacks, Anthropic free-will rebuttal, B300 resale glut.
- **Out-of-window re-reports not listed:** Apple pulling MiniMax Talkie (removal dated Nov 25), Dragos/MiniMax confidential-data access (~Nov 25 evening), Avicena's $100M+ Series C (Nov 24), TÜV/GitHub-agent safety-test story (Nov 3).
- **Dead ends / lane status:** Reddit anonymous lane was unavailable this run (`.rss` HTTP 404 plus 429 backoff; OAuth credentials not configured) — logged, not treated as a negative finding. Several DuckDuckGo queries returned no results; Reuters direct fetch hit an auth wall, so the Reuters-sourced items were verified via accessible secondary outlets only. No index outage affected the final set.
