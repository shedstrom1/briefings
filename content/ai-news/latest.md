# AI News Briefing — Anthropic hits the tape; Superhuman makes Go its centerpiece

**Window:** 2026-11-19 07:00 → 12:00 EST (the 5 hours since this job's 07:00 fire; ET-anchored). New-items-only: events first disclosed in this window, deduped against the reported ledger.

**TL;DR**
- **Anthropic (ANT) began trading on Nasdaq Thursday morning, opening at $111.50 — about 34% above its $83 IPO price — and briefly carries a ~$2T market cap.** A material new step in the already-reported IPO story, not a new event to re-list wholesale. CEO Dario Amodei used the opening bell to recommit to pacing AI development.
- **Superhuman (formerly Grammarly) is making "Go" its central work agent** across Docs and Mail, adding 20 third-party agents via a new Go SDK and repositioning Go as a proactive, cross-app executor with 1M+ active users.
- **Inference-engine churn continued within the window:** llama.cpp shipped a large OpenVINO backend fix and Inkling 2 attention-mask corrections; vLLM landed ROCm MXFP4 and Rust-frontend fixes. No new versioned release from llama.cpp, vLLM, SGLang or Ollama in-window.
Several high-profile stories trending today were already filed by this job's 07:00 fire.

---

## Models and local inference

No qualifying new model release or hardware item in this window. HF's newest uploads (17:34–17:41 UTC) land after the 12:00 EST close, and would be routine community quant/RL repos in any case. The ledger's open-weight threads from the last two days (Inkling 2, Gemini 4 Neon, Kimi K3.1 Pro, Qwen4-Max) are all already reported.

**In-window engine builds (also noted).**
*Event date: 2026-11-19, 12:58–16:58 UTC.* llama.cpp's afternoon wave included PR #32590, a substantial OpenVINO backend pass (rounds GPU KV length to a power of two, optimizes prefill recompiles, fixes LFM2/DSpark and Qwen3.6-A3B static-shape paths, adds Mamba models on OV CPU-GPU) and PR #32429, Inkling 2 attention-mask corrections. Builds b12546/b12547 were tagged in-window. vLLM merged PR #65251 (ROCm AITER MXFP4 linear op registration), #68584 (Rust-frontend Mooncake lookup-lease stats) and #68510 (dead-code cleanup).
**Why it matters:** the OpenVINO work broadens practical CPU/NPU execution of small open models, and the Inkling 2 fixes matter for anyone running Nous's new release locally. Nothing here is a headline feature, but it is the real state of local inference between releases.
Sources: [llama.cpp commits, 12:00–17:00 UTC](https://api.github.com/repos/ggml-org/llama.cpp/commits?since=2026-11-19T12:00:00Z&until=2026-11-19T17:00:00Z&per_page=40) · [vLLM commits, 12:00–17:00 UTC](https://api.github.com/repos/vllm-project/vllm/commits?since=2026-11-19T12:00:00Z&until=2026-11-19T17:00:00Z&per_page=40) · [llama.cpp releases](https://github.com/ggml-org/llama.cpp/releases) (retrieved 2026-11-19 ~12:00 EST).

## Agent tools

**Superhuman makes Go the center of its agent stack.**
*Event date: 2026-11-19 (morning ET; TechCrunch posted ~3 hours before the 12:00 fire).* Superhuman, the company formerly known as Grammarly, is elevating its "Go" assistant from a feature to the central agent across its products. Go now works inline in Docs and Mail, learns context across apps, and runs proactively — watching calendars and email and drafting responses. A new Go SDK adds 20 connected third-party agents including DeepL, Legora, Amplitude, Databricks and Blackboard. CEO Shishir Mehrotra says Go has passed 1 million active users and is positioning it against OpenAI's Dots and Claude Duet rather than consumer companions.
**Why it matters:** this is a concrete example of a productivity suite consolidating on a single persistent, cross-app agent with an extension SDK — the pattern to watch for enterprise agent tooling. It also signals that "assistant as the OS layer" is now a mainstream product bet, not a startup demo.
**Local inference:** none claimed — Go is a hosted, proprietary agent.
Source: [Superhuman pushes its Go assistant everywhere — TechCrunch, 2026-11-19](https://techcrunch.com/2026/11/19/superhuman-pushes-its-go-assistant-everywhere/).

## AI security and governance

**Update on previously reported — Anthropic (ANT) makes its Nasdaq debut.**
*Event date: 2026-11-19, 09:30 ET opening bell.* Building directly on the IPO pricing already filed in this ledger ($83/share, raised $81.3B), Anthropic's stock began trading Thursday: it opened at $111.50, roughly 34% above the offer, and the company briefly cleared a ~$2 trillion market capitalization — surpassing SpaceX's debut and becoming by far the largest US-listed public benefit corporation, overseen by a five-member long-term benefit trust. The order book was reported to have left more than half of bidding institutional investors with no allocation, with fill rates favoring US investors. At the opening bell Amodei recommitted to "pacing" frontier AI development, echoing early investor Dustin Moskovitz's line that he backed Anthropic "to promote safety, not racing." Allocation detail (US retail $1.1B, ~$3B Japan tranche) was already in the previous fire's ledger and is not re-reported here.
**Why it matters:** the debut turns a heavily analyzed private fundraising saga into a live public-market instrument, and puts the "circular financing" question (hyperscaler revenue recycling back into compute) in front of public shareholders on day one. The PBC/long-term-benefit-trust structure also becomes a real governance test rather than a filing footnote.
**Local inference:** none.
Sources: [TechCrunch AI wire — "Anthropic hits NASDAQ exchanges at $111, 33 percent above IPO price" and "At Nasdaq opening bell, Anthropic CEO recommits to pacing AI development," 2026-11-19](https://techcrunch.com/category/artificial-intelligence/) · [Techmeme top-of-river, 2026-11-19](https://www.techmeme.com/) · [The Decoder — "Anthropic's $81.3 billion IPO shatters SpaceX's record," 2026-11-19](https://the-decoder.com/).

---

## Also noted

- No qualifying AI-security disclosure originated in this window. The Hacker News's Nov 19 items (phpAfterShell / PHP RCE set, VMware vCenter CVE-2026-123890, Drupal, N-able, CISA KEV with LiteLLM LLMjacking) are either general-security rather than AI-security, or were already footnoted/filed by the 07:00 fire (LiteLLM KEV, Ruby2Shell).
- The OpenAI "researchers value their AI tools more than nearly half their colleagues" piece (The Decoder, Nov 19) re-packages the same internal research-acceleration report already filed at 07:00 as `jagged-frontier` (46% of 2–4h tasks, $2,400/day). Not re-listed.

## Just missed the window

- **Frostbite — an AI-designed CPU architecture** (Architect Labs blog, dated Nov 18, before this window's 07:00 EST open): a general-purpose CPU built on interaction nets that the company says its agent swarms took end-to-end from exploration through GDS, claiming 4.59× the performance-per-watt of a modern superscalar core. Genuinely on-theme for AI-designed silicon, but the underlying disclosure predates the window — footnoted once here, not counted as an in-window item. It surfaced on Hacker News inside the window. [Source](https://architectlabs.com/blog/frostbite) (2026-11-18).

## Coverage notes

- **Verified in-window items: 2 substantive** (Anthropic Nasdaq debut as an update on a reported event; Superhuman Go), **1 also-noted** engine line (llama.cpp + vLLM in-window commits), and **1 just-missed footnote** (Frostbite). Counting unit: distinct underlying events.
- **Dedupe gate:** ~8 trending candidates suppressed as already reported in the ledger and not re-listed — Gemini 4 Neon, the OpenAI `jagged-frontier` report, the TSMC/Samsung China shipment hold + Moonshot/NYT export-control cluster, CISA KEV LiteLLM (already footnoted), Anthropic IPO allocation detail, Microsoft in-house MAI model replacements (Ignite), Meta's ads-ranking agent blog (dated Nov 18), and the PHP2Shell/Ruby2Shell follow-ons. Repeat "just missed" footnotes were not permitted.
- **Limitations:** Reddit's public RSS ended Nov 13, 2026, so the r/LocalLLaMA lane returned 404 this run; the HN river, Techmeme, GitHub commit APIs, Hugging Face model API and outlet homepages were walked instead. General web search returned mostly trackers. Several secondary links (individual TechCrunch/The Verge article pages, Techmeme history) failed to fetch; those items were corroborated through the outlet's own wire page rather than cited to an unverified slug. No padding with stale or adjacent-day material.
