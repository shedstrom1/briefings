# AI News Briefing — Friday, October 23, 2026 · 12:00 EDT Edition

**Window:** 2026-10-23 07:00 → 12:00 EDT (previous fire → this fire). New items only; event dates anchor every entry.

## TL;DR

- Meta delayed enforcement of its controversial new commercial terms (the "AI scraping" clause) from October 30 to November 20, after publisher letters and EU/UK antitrust complaints; the EU antitrust watchdog welcomed the move.
- On the tooling front, Microsoft Agent Framework .NET 1.26.0 and Ollama v0.40.10-rc0 both shipped inside the window.
- Just missed the window: OpenAI's Decisions API GA announcement (yesterday afternoon) and CNBC's report that OpenAI is nearing an up to $30B round at a $1.25T pre-money valuation.

## Models and local inference

**NVIDIA Nemotron 3.5 Bolt E4B 4GB ships open weights — the "fits your whole GPU budget" agent model** — *Event date: Friday, October 23 (~11:41 EDT)*

NVIDIA's AI account posted at 15:41 UTC (11:41 EDT) that "Nemotron 3.5 Bolt E4B 4GB is open and customizable… weights, data and recipes. Available now on Hugging Face," and the repo `nvidia/NVIDIA-Nemotron-3.5-Bolt-E4B-4GB` is live (last modified today). It is an 8B-total / 4B-effective model post-trained by NVIDIA from Gemma-4-E4B-it, shipped as Q4_K_M GGUF (~6.2 GB download) with a companion DFlash drafter for speculative decoding via llama.cpp (`--spec-type draft-dflash`), a 64K-token context (131,072 declared in GGUF metadata), chat-template switches for thinking mode and conversational mode, and the OpenMDW-1.1 license. NVIDIA's own Q4_K_M benchmark row beats Gemma4-E4B-it across the board and posts standout local-agent numbers: ToolSandbox 74.13, RoleBench 71.21, IHEval 82.40, MultiChallenge 55.60. The E4B tier was pre-announced at the Nemotron 3.5 launch (Oct 15–19) with a firm Oct 23 date in NVIDIA's blog.

**Why it matters:** This is the tier the local-agent crowd was waiting for — a tool-calling, voice-ready assistant model in a ~6 GB quantized file that pairs speculative decoding with llama.cpp out of the box.

NVIDIA's launch blog explicitly targets "fast local voice and device assistants."

Sources:
- NVIDIA Nemotron 3.5 Bolt E4B 4GB model card — Hugging Face, updated 2026-10-23 (repo public this morning) — https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Bolt-E4B-4GB
- @NVIDIAAI on X, "Available now on @huggingface," 2026-10-23 15:41 UTC — https://x.com/NVIDIAAI/status/2113657115890118764
- "NVIDIA Open Models Advance Europe's Sovereign Intelligence" — NVIDIA blog, Oct 21, 2026 (scheduled availability "Friday, Oct. 23") — https://blogs.nvidia.com/blog/open-models-europe-industry/

**llama.cpp b11838** — *Event date: Oct 23, 09:37 EDT.* One in-window build (b11837 was skipped): fixes a time overflow in `ggml_time_us` (#30969), which could distort timing/telemetry on long-running servers. Source: https://github.com/ggml-org/llama.cpp/releases/tag/b11838

**Ollama v0.40.10-rc0** — *Event date: Oct 23, 11:03 EDT.* First release candidate since v0.40.9; watch for the stable cut over the weekend. Source: https://github.com/ollama/ollama/releases/tag/v0.40.10-rc0

## Agent tools

**Microsoft Agent Framework .NET 1.26.0** — *Event date: Oct 23, 10:32 EDT.* The .NET line picked up an AG-UI endpoint fix, correct composition of Copilot session hooks, honoring `allow_multiple_tool_calls` in the Anthropic client when `tool_choice` is unset, and ACGS joined the Python community projects. A routine-but-real hardening release for teams running agentic workflows on .NET.

Source: https://github.com/microsoft/agent-framework/releases/tag/dotnet-1.26.0

## AI security and governance

**Meta delays its scraping-enabled business terms to November 20 after publisher and antitrust pressure** — *Event date: Friday, October 23* — *Update on: "Meta rewrote its scraping clause — legal experts say the problem is still there" (Nieman Lab, Oct 18, in ledger)*

Meta spokesperson Daniel Roberts confirmed the effective date of its updated Commercial Terms and Self-Serve Ad Terms moves from October 30 to November 20, "to allow more time for us to explain to our partners and stakeholders what's changing and what's not," and to lay out "how Meta uses web crawlers and the fact that content owners will still be able to use robots.txt to signal preferences." The trigger chain: the News/Media Alliance sent a letter to Mark Zuckerberg on Thursday demanding revisions over "severe harm" to publishers, and publishers plus digital advertisers filed complaints with EU and UK antitrust authorities alleging the terms granted Meta an unlimited right to use their content for AI services (Meta rejects the claim). Reuters adds that the EU antitrust watchdog "welcomed the delay" amid ongoing discussions and had sent requests for information to companies that have not published model-training data summaries.

**Why it matters:** A coordinated publisher + advertiser campaign moved a deadline from Big Tech in under a week, and the EU's parallel RFI on training-data transparency signals regulators are treating training-data provenance as an enforcement surface, not a voluntary disclosure. For anyone building on Meta's business platforms or scraping-adjacent data pipelines, the November 20 date is the one to watch — and robots.txt remains the operative opt-out lever.

Sources:
- "Meta delays new business terms after publisher pushback on AI scraping" — The Verge (Emma Roth), Oct 23, 2026 10:47 EDT — https://www.theverge.com/tech/1017450/meta-business-terms-delayed-ai-scraping
- "Meta to delay new commercial terms criticised by publishers, advertisers" — Reuters (Foo Yun Chee), Oct 23, 2026 — https://www.reuters.com/business/media-telecom/meta-delay-new-commercial-terms-criticised-by-publishers-advertisers-2026-10-23/

## Also noted

- **Pathos AI closed a $237M Series E at ~$3B post-money** (announced Friday; Bristol Myers Squibb-backed per multiple outlets), funding a registrational study for AI-identified oncology asset JSKN-016 and a five-year, $55M license of a Tempus AI neurological dataset. Sources: Pathos press release, Oct 23 — https://www.pathos.com/pathos-ai-secures-237-million-in-series-e-financing-to-scale-agentic-ai-drug-development-platform-and-advance-oncology-pipeline ; Axios Pro, Oct 23 08:40 EDT — https://www.axios.com/pro/biotech-deals/2026-10-23/pathos-ai-raises-237m-3b-valuation
- **BofA (via Reuters, 11:06 EDT):** global data-center spending to more than double to $2.1 trillion by 2028 — the macro backdrop keeping GPU supply tight. https://www.reuters.com/business/data-center-spending-more-than-double-21-trillion-by-2028-2026-10-23/

## Just missed the window

- **OpenAI's Decisions API went generally available — announced yesterday (Oct 22) afternoon, re-covered on Techmeme at 9:25 AM today.** Built with GPT-6 Luna for bounded-answer decisions (predicate/choice/score), up to 10x faster than the Responses API, $0.10 per million input tokens with **no output-token charges**, cached input $0.01/M, up to 400 questions per request, native GPT-Live voice integration, US/EU inference residency and ZDR for eligible customers. The direct-competitor shot at Jev/TypeSafe-style decision models is worth a benchmark slot if you route agent control flow. Sources: OpenAI blog, Oct 22 — https://openai.com/index/decisions-api/ ; TechCrunch (Tim Fernholz), Oct 22 — https://techcrunch.com/2026-10-22/openai-makes-its-jev-competitor-widely-available-as-decision-model-competition-heats-up/
- **OpenAI is nearing an up to $30B round at a $1.25T pre-money valuation**, led by UAE investors and Coatue, per CNBC (Oct 22); the company reportedly told investors it is "aiming to secure signatures by the end of next week." https://www.cnbc.com/2026-10-22/openai-funding-round-uae-investors.html
- **Ruby 4.0.9 / 3.4.12 / 3.3.13 shipped (Oct 22, 23:11 UTC)** with upstream Onigmo fixes for the four GitLab regex CVEs — including the CVSS 10.0 CVE-2026-110264 footnoted in the 07:00 run. Ruby notes it may decide whether to request its own CVEs for these fixes. https://www.ruby-lang.org/en/news/2026-10-23/regexp-fixes-for-gitlab-cves/

## Coverage notes

- **Verified: 10 items** (2 lead, 3 minor, 2 tooling releases, 3 footnotes), all with fetched pages carrying in-window (or explicitly-dated) timestamps; lead stories read in full.
- **Ledger dedupe: 11 candidates suppressed as already reported** (MiniMax M3.1 Flash community quants, DeepSeek/MiniMax export approvals, Anthropic×Hg, Anthropic×Theorem, PHP2Shell-Japan update, WSJ Signal-chats re-coverage by The Verge, Canva Craft, METR monitoring story, ConnectWise/Storm-0940, MS Diagnostic Orchestrator, Claude Duet), plus 1 repeat-footnote suppression (GitLab CVE itself — only the new Ruby-fix release was footnoted).
- Indexes used: Techmeme river, HN Algolia (created_at-filtered), HuggingFace model API, GitHub releases. HN was quiet in-window (one 5+ point story, non-AI). The Hacker News/The Verge morning cycle added no other in-window AI items. No index was down; the DDGS search backend flaked twice (nous fallback served).
- Dead ends logged, not chased: X post page itself (fetch not attempted — post time corroborated via search index + HF repo state); no evidence found of a PHP2Shell KEV addition today.
