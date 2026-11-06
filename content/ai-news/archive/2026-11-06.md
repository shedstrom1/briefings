# AI News Briefing — November 6, 2026 (07:00 ET edition)

**Window:** 2026-11-05 17:00 ET → 2026-11-06 07:00 ET (fires daily 07:00/12:00/17:00 America/New_York). New items only; the ledger gate was applied before and after research.

## TL;DR

- **Thinking Machines launched Inkling 2 Small** — a 594B-total/17B-active MoE scoring 31 on the Artificial Analysis Intelligence Index, available now on Tinker with closed weights and a promised Apache-2.0 open-weight release "next week"; AA says it would become the highest-scoring open-weights model developed outside China.
- **Claude Code was sending your email address to every website it browsed** in the User-Agent header; the GitHub bug report hit 1,200+ HN points overnight and Anthropic's mitigation is a new `cc-plugin-privacy` plugin shipped in v2.1.317.
- **NVIDIA's new open-source NVKE (NVIDIA Kubernetes Engine) surfaced** — a preview management plane for running managed-style Kubernetes on your own NVIDIA GPU bare metal, up on HN's front page overnight.
- **Post-midterm AI market rout:** a $2.5 trillion Nasdaq wipeout on Democratic data-center-scrutiny fears rolled into Friday's Asian/European sessions, with chipmakers down double digits.
- **Update on Anthropic's IPO (ledger anthropic-s1-ant):** WSJ reports major investors are now skeptical the week-before-Thanksgiving listing can proceed at the expected ~$1.5T.

---

## Models and local inference

### Thinking Machines releases Inkling 2 Small — closed now, Apache-2.0 weights "next week"
**Event date:** November 5, 2026 (evening ET — HN/Reddit community launches landed 19:00–23:00 UTC Nov 5/06; the prior 12:30 and 17:00 fires did not cover it).

The second-generation Inkling is a sparse MoE with 594B total / 17B active parameters, accepts text and images (292M-param Perception-Encoder vision stack, no audio), and supports variable reasoning effort. It scores 31 on the AA Intelligence Index v5 — up 18 points from Inkling Small — with big agentic gains: AutomationBench-AA 5%→46%, FrontierCode 2.0 5%→25%, HLE-Diamond 12%→29%; AA-Briefcase jumps 615 Elo to 1527, beating Claude Sonnet 5.5 (medium). It launched first-party on Tinker at $0.30/$1.00 per 1M in/out tokens (~$1.18 per Index task), and Thinking Machines says full weights go Apache-2.0 next week once external safety testing and red-teaming complete. Notably, the demo included the model autonomously training Qwen3.5-4B on Tinker for time-series forecasting — 4.5 hours, $426 in spend, >2x the forecasting score.

**Why it matters:** This is the strongest US/UK-lab open-weights candidate since MiniMax M3.1 Flash if the license date holds — AA frames the pending weights as the highest-scoring open-weights model from outside China (ahead of Mistral Large 4's open release trajectory). Its 17B-active MoE shape is exactly the profile the llama.cpp/vLLM/SGLang expert-offload work targets.

Source: Introducing Inkling 2 Small — Thinking Machines Lab, Nov 5, 2026 — https://thinkingmachines.ai/news/inkling-2-small/
Source: Thinking Machines Inkling 2 Small: the highest scoring open weights model outside of China — Artificial Analysis, Nov 5, 2026 — https://artificialanalysis.ai/articles/thinking-machines-releases-inkling-2-small-highest-scoring-open-weights-model-outside-of-chinia-upon-weights-release-it
Source: HN front page (215 pts) — https://news.ycombinator.com/item?id=50386338

### NVIDIA's NVKE: an open-source management plane for self-hosted GPU Kubernetes (preview)
**Event date:** November 6, 2026 (repo push 09:44 UTC Nov 6; HN front-page spike 03:16 UTC Nov 6).

NVKE (dsx-ai-factory/nvke, Apache-2.0, "Copyright NVIDIA") is a self-hosting factory for Kubernetes on your own NVIDIA GPU metal: a seed cluster stands up a management plane, which then provisions isolated workload clusters — each with its own control plane, etcd and node pools — through the NVIDIA Infra Controller (NICo), with SPIFFE/SPIRE TPM node attestation and a companion build-artifact project (NVKR). It is explicitly preview: API auth is off by default, plain HTTP, no management-plane upgrade/backup path.

The preview caveats matter: it is an evaluation platform today, not production.

Source: dsx-ai-factory/nvke (README, Apache-2.0) — https://github.com/dsx-ai-factory/nvke
Source: NVIDIA Kubernetes Engine Enters Preview — news.lavx.hu, Nov 6 2026 04:05 UTC — https://news.lavx.hu/article/nvidia-kubernetes-engine-enters-preview-a-management-plane-for-gpu-kubernetes-clusters
Source: HN (86 pts) — https://news.ycombinator.com/item?id=50387738

**Also noted (Models and local inference):**
- vLLM merged 63 PRs in-window (Nov 5 22:00 → Nov 6 12:00 UTC), headline adds day-0 **LFM3-Text and LFM3-VL** model support (#65826/#65827), **Kimi-K3.1-Pro TPU serving** via vllm-torchtpu (#65725), a MiniMax-M3.1 ROCm retune cluster and FlashInfer 0.7.3 — https://github.com/vllm-project/vllm
- Community 6-engine Qwen3.8-27B benchmark on a single RTX 5090 (TTFT/prefill/decode + quality at 64K/128K/256K, GGUF vs NInfer formats): NInfer led speculative and concurrent throughput, llama.cpp kept the highest plain single-request decode; rankings reshuffle sharply at long context — Reddit r/LocalLLaMA, Nov 5 23:39 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xoxx4a/qwen3827b_benchmark_6_inference_engines_gguf_vs/

## Agent tools

### Claude Code leaked the signed-in user's email to every site it visited via User-Agent
**Event date:** November 5, 2026 (GitHub issue opened Nov 5; HN #1 post 17:17 ET, 1,221 points).

Issue #107757 documents that Claude Code put the account email address into the `User-Agent` header of outbound web-search requests, broadcasting it to arbitrary third-party sites — no attacker needed, just a normal research prompt. Contributor poteat explained the mechanism: the harness preloads the user's email into model context, and when a site's policy (e.g., Wikipedia's scripted-client etiquette) asks bots to embed a contact email, the agent composed it into the curl. The reporter (on v2.1.318, darwin) said it produced dozens of scam emails a day. Anthropic's response: a built-in `cc-plugin-privacy` plugin, source-available, enabled by default in v2.1.317, that deterministically blocks header substitution — though a commenter on v2.1.318 couldn't see it in `/plugins`, and critics note the email still lives in context by default, so privacy remains a plugin rather than an invariant.

**Why it matters:** This is the archetypal agent-harness data-flow leak — identity material in context becomes identity material on the wire. It's the same class as the OpenCode key-exposure event (ledger 2026-10-18) and should change how everyone configures agent browsing (pin/strip UAs, firewall egress, audit what the harness preloads).

Source: Bug Report: Privacy violation in User-Agent header #107757 — GitHub/anthropics/claude-code, Nov 5, 2026 — https://github.com/anthropics/claude-code/issues/107757
Source: Claude Code sent your email in User-Agent — StartupHub.ai, Nov 6, 2026 — https://www.startuphub.ai/ai-news/artificial-intelligence/2026/claude-code-sent-your-email-in-user-agent
Source: HN front page (1,221 pts, 385 comments) — https://news.ycombinator.com/item?id=50385272

## AI security and governance

### Post-midterm AI rout: $2.5T off the Nasdaq on data-center scrutiny fears
**Event date:** November 5, 2026 close, extending into the Nov 6 overnight sessions (Guardian live blog Nov 6).

Reuters: the Nasdaq Composite fell 5.5% — its deepest one-day drop since April 2025's "Liberation Day" — wiping ~$2.5 trillion from the large-cap Nasdaq 100 as the Democratic midterm sweep raised fears of scrutiny on AI infrastructure and data-center construction; the chipmaker index tumbled 12% to a six-month-plus low and CoreWeave slumped 10% with five-year credit spreads at record highs. The second straight session of AI selling rolled into Friday's Asian and European markets (Guardian live coverage), with The Atlantic framing the election as a referendum on the AI buildout itself.

**Why it matters:** Regulation risk has converted from a long-tail story into a priced one — moratorium candidates won governor's races (see ledger dc-moratorium-15of36) and the sell-off is now the discount rate on neocloud credit, Anthropic's IPO, and NVIDIA's ecosystem financing terms.

Source: Investors wipe $2.5 trillion off Nasdaq as blue tsunami prompts AI fears — Reuters, Nov 5, 2026 — https://www.reuters.com/legal/transactional/ai-data-center-stocks-slip-democratic-sweep-clouds-expansion-plans-2026-11-05/
Source: Global stocks tumble as AI panic grips markets after Democrats' midterm success — The Guardian (live), Nov 6, 2026 — https://www.theguardian.com/business/live/2026/nov/06/global-stocks-tumble-ai-panic-markets-democrats-midterms-success-jobs-oil-dollar-latest-news-updates
Source: Silicon Valley's Bad Night — The Atlantic, Nov 6, 2026 — https://www.theatlantic.com/technology/2026/11/americans-really-dont-like-data-centers-elections/689284/

### Update on previously reported (ledger anthropic-s1-ant): investors doubt the $1.5T Anthropic IPO
**Event date:** November 5, 2026, WSJ updated 23:36 ET.

WSJ: major investors are "increased skeptical" Anthropic's IPO — targeted the week before Thanksgiving, expected to raise up to $100B at ~$1.5T — can proceed at that valuation after the AI selloff; one estimate floated closer to $1 trillion, while others argue October's Q3 disclosure (ledger: Q3 rev $16.96B) softens the blow. This extends the 2026-11-03 ledger entry (anthropic-ipo-1p5t-push) where investors were pushing the low end at $1.5–1.6T; the market event above is what moved the goalposts.

**Why it matters:** A repriced Anthropic float resets comps for every frontier-lab raise and feeds the neocloud credit tightening already visible in the CoreWeave spreads above.

Source: Major Investors Are Skeptical Anthropic IPO Can Achieve $1.5 Trillion Valuation — WSJ, Nov 5 2026 11:36 pm ET — https://www.wsj.com/finance/major-investors-are-skeptical-anthropic-ipo-can-achieve-1-5-trillion-valuation-ca965d8b
Source: Anthropic's $1.5T valuation under pressure — Seeking Alpha (WSJ pickup), Nov 6, 2026 — https://seekingalpha.com/news/4667757-anthropics-15t-valuation-under-pressure-as-major-investors-signal-skepticism-wsj-reports

**Also noted (AI security and governance):**
- ING is reportedly the seller of a **$500M data-center loan portfolio** backing Stargate/Cyrusone projects (Bloomberg, Nov 5–6) — https://www.bloomberg.com/news/articles/2026-11-05/mystery-seller-puts-up-500-million-data-center-loans-backing-stargate-cyrusone

---

## Just missed the window

- **Google's Gemini Omni 1.2 Flash GA** (native 15-second video generation/editing, $0.07/sec 720p — 30% cheaper than 1.1): Google blog + The Verge post at 12:56 pm ET Nov 5, ~4h before window start and inside the 17:00 fire's window but unreported there; footnote-eligible once. (missed: gemini-omni-1-2-flash-ga)
- **Huawei Qinyun HM650 "100% domestic" desktop** (Kirin X90, HarmonyOS, enterprise/government): announced during China-hours Thursday (≈Wednesday night ET, per SCMP), resurfaced on HN overnight; outside both my window and the prior fires' — footnote-eligible once. (missed: huawei-hm650-desktop)

## Coverage notes

- **Verified: 10 events** (5 lead sections + 5 also-noted one-liners), counted as underlying events; 2 footnotes. Lead stories (Inkling 2 Small, Claude Code leak, NVKE, market rout, IPO update) were read in full from primary pages; every cited URL was fetched this run (WSJ via extractable paywall-lite summary + Seeking Alpha pickup).
- **Ledger dedupe:** ~6 candidates suppressed as already reported — Delangue "P(doom) close to zero" (ledger 2026-11-05), GPT-6 Cyber re-appearance on tracker sites (ledger 2026-11-05), Kimi K3.1-Pro trending re-coverage (ledger 2026-11-04), MiniMax M3.1-Flash re-coverage (ledger 2026-11-01), weight-space-programming follow-up threads (ledger 2026-11-02/03), Ollama v0.40.14/15 (ledger 2026-11-02/04). No repeat 'Just missed' footnotes (the two above are first-time).
- Dead ends: Techmeme archive returned 404 for both day pages (logged, not chased); Anthropic "2026 Usage Policy update" turned out to be Oct 8 (stale, dropped); The Hill CAISI article was May (stale, dropped); Claude Haiku 5.5 on Anthropic's newsroom was an Oct 7 page (stale, dropped).
- Indexes used: HN Algolia (in-window river, corrected epoch), r/LocalLLaMA new-queue (Reddit Atom), GitHub releases/merge searches, llmbenchmark trending, model-release calendars.
