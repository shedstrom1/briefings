# AI News Briefing — Saturday, October 31, 2026 (12:00 ET edition)

**Window: 2026-10-31 07:00 → 12:00 EDT** (previous fire → this fire; new items only)

## TL;DR

- **Cisco Talos disclosed "VOLTA-Autodoxer"** — a self-deleting Windows infostealer that POSTs a structured evidence bundle of everything it stole to an LLM (default `anthropic/claude-opus-4.6`, fallback Kimi then Gemini) to produce a ranked "WHAT THEY'RE WORTH" victim dossier; published simultaneously by Talos (08:00 ET) and WIRED's Kernel Panic (8:00 AM ET).
- **Prospero launched**: Christopher Wellons (nullprogram) released a deliberately minimal C++/Dear-ImGui coding harness — no web stack, ~two orders of magnitude less RAM, provider-agnostic (llama.cpp/OpenRouter/on-prem), desktop-control vision, free Community Edition plus paid Professional Edition.
- **Google DeepMind's PlanAssist exits pilot**: a 12-month Beta with 12 more UK councils (15 total) after Barnet/Camden/Dorset testing cut officer householder-assessment time by up to 58%, announced Saturday alongside a UK government delegation-of-authority reform.
- vLLM landed five in-window merges (MiniMax-M3.1 pipeline-parallel NIXL pull, GLM-5.3-Flash SM121 TP=2 skinny-GEMM plans, batch-invariance kernel pinning); llama.cpp shipped no builds in the 5-hour window; Ollama unchanged since v0.40.13.
- Quiet Saturday for model releases — no new closed- or open-weight model dropped in-window; the only movement is an unverified rumor that Zhipu's GLM-5.5 is in advanced post-training.

## AI security and governance

### VOLTA infostealer adds an LLM "intelligence analyst" to sort stolen data for the operator (event date: October 31, 2026)

Cisco Talos published research Saturday 08:00 ET on "VOLTA-Autodoxer," a self-deleting Windows infostealer surfaced through Talos's CAIRN framework (built to identify hacking tools that incorporate AI). Beyond a standard stealer haul — browser credentials, cookies, payment cards, crypto seed phrases, Discord/Telegram/Steam/Roblox sessions, Microsoft OAuth tokens, SSH and cloud keys, Wi-Fi profiles, screenshots — VOLTA runs a live enrichment pass using the victim's own stolen sessions (Gmail receipt searches to confirm a home address, UberEats/DoorDash order history, Discord profile enrichment, a live Roblox inventory valuation), serializes everything into an evidence bundle, and POSTs it to OpenRouter under a system prompt casting the model as "a forensic intelligence analyst drafting a target assessment." Output must include a "WHAT THEY'RE WORTH" ranking and a "THEIR OPSEC" section; the whole inference round-trip runs on the victim's own machine and network before exfil in `volta_dox.zip`. Notably, VOLTA hard-codes a refusal-aware fallback chain — default `anthropic/claude-opus-4.6`, then `moonshotai/kimi-k2`, then `google/gemini-2.5-flash`, temperature 0.2 — checking responses for phrases like "I can't" before pivoting, which Talos reads as deliberate engineering against model guardrails. Talos has not tracked the malware used in the wild; sample SHA256 `57ef4242666d78c50df145b34023df564f0fad38812d6a580e66433d427e2279`, first seen 2026-10-11.

**Why it matters:** This is the clearest documented case yet of an LLM embedded in commodity crimeware not as a novelty but as a labor-replacement step — the "effort displacement" pattern (same logic Talos calls CLOSEDQUORUM) moves from malware authoring into victim triage, lowering the human floor for a wide-net stealer operator. The refusal-resilient multi-model chain is the second signal: if anticipation of guardrail refusals becomes a standard design requirement in crimeware, per-provider refusal behavior matters operationally, not just reputationally.

**Local inference:** None to claim — the LLM stage is cloud inference via OpenRouter using the victim's own connection; no local-model sizing applies.

- Source: "Appraisal by AI: Model-driven victim prioritization in the VOLTA infostealer" — Cisco Talos Blog, Ryan Fetterman, Saturday, October 31, 2026 08:00 — https://blog.talosintelligence.com/appraisal-by-ai-model-driven-victim-prioritization-in-the-volta-infostealer/
- Source (independent, same-day exclusive): "Spooky New Infostealer Malware Uses AI to 'Autodox' Victims" — WIRED Kernel Panic, Oct 31, 2026 8:00 AM — https://www.wired.com/story/kernel-panic-volta-autodoxer-ai-infostealer-malware/

### Google DeepMind takes PlanAssist from pilot to a 12-month Beta across 15 English councils (event date: October 31, 2026)

DeepMind announced Saturday that PlanAssist — the Gemini-based tool that validates householder planning applications, checks them against heritage constraints and local/national policy, and drafts fully cited recommendation reports for officers — is moving out of its Barnet/Camden/Dorset pilot into a 12-month Beta, with 12 more local planning authorities onboarding in three cohorts (15 total, mixed metropolitan and rural). Early testing cut average officer time on householder assessment tasks by up to 58%, and more than 70% of tool-generated reports were approved by officers with no edits. Subject to successful testing, the UK government intends to offer it to every council in England in 2027; the tool is explicitly not mandated, and every assessment stays reviewed by a qualified officer. The announcement landed alongside a Ministry of Housing press release (Rayner) putting straightforward applications in the hands of expert officers from Saturday via a new National Scheme of Delegation, plus 420 planners recruited — the accompanying DeepMind post and X announcement both went out mid-morning ET Saturday.

**Why it matters:** A frontier lab's government-deployment story is finally quoting operational numbers (58% task-time reduction, 70% no-edit approval) rather than intentions — a template for "AI at work for the public" with human sign-off structurally retained. The mandated open-data digitisation of planning documents by 2027 is the quieter strategic bit: it lowers the data-access barrier for every competing tool, not just DeepMind's.

- Source: "Speeding up planning decisions with AI" — Google DeepMind blog (Owen Larter & David Thacker), October 31, 2026 — https://deepmind.google/blog/speeding-up-planning-decisions-with-ai/
- Source (independent, UK government): "Faster planning decisions as new rules and AI tackle delays" — GOV.UK / MHCLG press release (Rayner; Narayan), effective Saturday 31 October 2026 — https://www.gov.uk/government/news/faster-planning-decisions-as-new-rules-and-ai-tackle-delays

## Agent tools

### Prospero: a coding harness built in C++/Dear ImGui with no web stack (event date: October 31, 2026 announcement)

Christopher Wellons (skeeto, of w64devkit fame) published his long-form launch post for **Prospero**, a native coding harness he built after being "disappointed by all the harness offerings" — most of which are web-stack apps consuming gigabytes of RAM for what "is essentially curl glued to popen()." Key specifics from the post: roughly two orders of magnitude fewer resources than comparable harnesses; no built-in model providers (you bring llama.cpp, an on-prem model, OpenRouter, Anthropic or OpenAI APIs); subagent spawning with inter-agent messaging; headless remote deployment over SSH/WSL with embedded x86/x64/ARM64 engines on macOS/Windows/Linux (the Windows x86 engine runs on Windows XP); desktop control via vision — the agent sees the screen and clicks/types/drags to test GUI apps without per-app MCP; INI-based config (no JSON) with URL includes for managed/shared configuration; a "never wait forever" design principle where every operation has a deadline, permissions denied by timeout, and bounded agent mailboxes; passive rendering so it idles at zero GPU while the agent waits. Distribution is split: free open-source Community Edition on GitHub (no headless engines, gentle nag) and a paid DRM-free Professional Edition with all remote-deployment engines; both code-signed. The v0.9.0 GitHub release assets were tagged Friday evening ET; the post and HN front-page launch (105+ points) are Saturday morning — HN discussion at 07:54 ET.

**Why it matters:** It's the most credible argument yet against web-stack bloat in agent harnesses, aimed squarely at the local-inference audience, and the Community/Pro split (headless remote deployment as the paid feature) is a distribution experiment the agentic-tooling world will be watching.

**Local inference:** Directly relevant. Wellons builds Prospero around open-weight models running on "regular, higher-end consumer hardware," ships llama.cpp as the canonical endpoint (example config: `Qwen3.8-27B` with reasoning-effort variants), and his "two orders of magnitude less resources" claim means the harness itself stops competing with your model for RAM — the same failure mode that makes OOM-killer casualties of bloated harnesses during long agent runs.

- Source: "Prospero: a new coding harness for a more civilized age" — nullprogram.com (Christopher Wellons), 2026-10-31 — https://nullprogram.com/blog/2026/10/31/
- Source (repo + v0.9.0 release, published 2026-10-30 18:39Z): https://github.com/skeeto/prospero

## Models and local inference

### vLLM weekend merges: MiniMax-M3.1 pipeline-parallel optimizations and GLM-5.3-Flash Blackwell skinny-GEMM (event date: October 31, 2026)

Five pull requests merged inside the five-hour window on vLLM's mainline: MiniMax-M3.1 pipeline-parallel optimization (NIXL pull from PP prefill stages plus a Q8KV4 prefill workspace, #64733 at 08:40 ET), SM121 TP=2 skinny-GEMM plans for GLM-5.3-Flash (#62956 at 09:55 ET), a FlashInfer attention warmup deadlock fix under PP>1 (#64338 at 11:24 ET), batch-invariance pinning of FlashInfer FP8 bmm and W4A16 NVFP4 kernels (#62192 at 11:25 ET), and a CohereASR FP16 attention-bias dtype fix (#54399 at 11:42 ET).

Weekend merges like these are how day-0 recipes stay warm between releases.

- Source: vLLM merged PRs (GitHub API, merged_at timestamps 12:40–15:42 UTC Oct 31) — https://github.com/vllm-project/vllm/pull/64733 , https://github.com/vllm-project/vllm/pull/62956 , https://github.com/vllm-project/vllm/pull/64338 , https://github.com/vllm-project/vllm/pull/62192 , https://github.com/vllm-project/vllm/pull/54399

### Unverified: Zhipu's GLM-5.5 reportedly in advanced post-training (rumor — treat with caution)

AIBriefs' Saturday edition picks up chatter (amplified on X by AGTPinsights) that Zhipu's GLM-5.5 has entered advanced post-training with release expected soon, with community expectation of a ~32B model (~16GB at 4-bit — one-consumer-GPU territory) plus possible Flash and Pro variants. No Zhipu/Z.ai post, model card, or API entry substantiates this; a prior FelloAI rumor had pointed at an August window.

But this is a rumor line, not a verified release.

- Source: AIBriefs Saturday edition (page dated October 31, 2026): "GLM-5.5 said to be in advanced post-training, release expected soon" — https://aibriefs.news/ai-news-today

## Also noted

- **llama.cpp shipped no builds inside the window** — the last release, b12020, published 08:07 UTC (04:07 ET) Saturday, before the window opened, and was covered by the 07:00 edition. (https://github.com/ggml-org/llama.cpp/releases)
- **Ollama unchanged** — latest release remains v0.40.13 (October 28). (https://github.com/ollama/ollama/releases)

## Just missed the window

- *(footnoted once)* **DeepSeek open-sourced dsh-superagent-prototype** — a MIT-licensed research agent bundle for DeepSeek Harness that watches a GitHub repo (optionally Feishu) and turns allowed requests into work items and pull requests, explicitly labeled not production-ready. The repo (deepseek-ai/dsh-superagent-prototype, "Towards a More Autonomous Agent System") went public at 09:32 UTC = 05:32 ET Saturday — 88 minutes before this window opened — and had already passed ~1.2k stars by mid-morning. (https://github.com/deepseek-ai/dsh-superagent-prototype)

## Coverage notes

- **Verified count: 6 events** (3 lead stories read in full; 2 also-noted items verified against fetched sources; 1 missed-window footnote with fetched repo metadata). Independent second sources: VOLTA (Talos + WIRED), Prospero (author blog + GitHub repo/release API), PlanAssist (DeepMind blog + GOV.UK).
- **Ledger dedupe: 9 in-window candidates suppressed as already reported** (re-coverage of ledger entries): Google Suncatcher orbit response, Jassy "6-8 good enough models," OpenAI $20B round re-cut, Nvidia→Nscale credit guarantee talks, Reddit logged-in-only / Google AI Mode traffic, Intel Friday close -28.6%, Alphabet $1T compute-commitment detail, Claude Code 2.1.313 backdoor HN re-spike, and one repeat "Just missed" footnote (Northflank breach, previously footnoted Oct 30).
- **Event-date exclusions:** four items circulating in today's aggregators were dropped under the event-anchor rule — Parameter.ai's ChatGPT account-takeover writeup (blog dated Oct 28, OpenAI fixed Oct 26; HN front-page spike today is re-coverage only), Meta Muse share-link privacy flaw (Ars disclosure was Monday ~Oct 26), the Neuron Persona Library / "Evil Qwen" Qwen personality-neuron project (disclosed ~Oct 27; AIBriefs re-featured today), and Deepgram's acquisition of Pipecat-maker Daily (announced Oct 29).
- **Dead ends / thinness:** Saturday morning was genuinely thin for model releases — nothing new closed- or open-weight verified in-window, and the GLM-5.5 item remains an uncorroborated rumor. Two-strike abandonments: couldn't resolve an independently fetchable page for the "Anthropic Messages API Rust port, 40-50% CPU cut" claim (single-source interview via aggregator only), and the ARTEX/South-Korea-campaign angle turned out to be a re-surfacing of CrowdStrike's October 7 report (out of window, and adjacent to the ledger's Oct 29 VAPOR PANDA entry). HN front page, AIBriefs, DX Today, HuggingFace trending, and GitHub release streams were all healthy; no index was down.
