# AI News Briefing: Agentic PHP2Shell Attack Hits Government; Nanbeige 33B Lands Just Outside Window

**Window:** October 25, 2026, 12:00 → 17:00 ET (previous fire → this fire). New items only, deduped against the reported ledger.

**TL;DR**
- Unit 42 documented the first government-entity compromise via PHP2Shell — attackers held password hashes and MFA secrets within two hours, and the post-exploitation command batches carry machine-parseable delimiters ("==SHELLUSER==") that mark them as AI-agent-driven.
- CYFIRMA says a suspected North Korea-linked team is running deliberate, low-and-slow PHP2Shell scanning of 216 unpatched hosts across 14 countries from 21 nodes — and says it is "moving from reconnaissance into active targeting"; South Korean experts dispute the Lazarus attribution.
- Engine front was a quiet Sunday: llama.cpp shipped 8 builds including a fused CUDA FFN gate/up+GLU fast path for prefill; vLLM merged 6 bugfix/CI PRs; no new model releases landed in-window.
- Just outside the window: Nanbeige4.3-33B (Apache-2.0, 80.9 Terminal-Bench 2.1) and the Convovo study in which Grok 4.7 picked the Republican candidate in 92% of forced-choice midterm races.

## AI Security and Governance

**Update on previously reported: PHP2Shell (ledger php2shell cluster, first reported Oct 21) — first government intrusion with agentic-AI post-exploitation; suspected DPRK scan campaign now active**
*Event date: Sunday, October 25, 2026 (published 13:19 ET).*
Palo Alto Networks Unit 42 observed attackers exploiting PHP2Shell (CVE-2026-109618 / CVE-2026-109619) against a European government entity and, within two hours of initial exploitation, accessing password hashes and multi-factor-authentication secrets — then establishing an NTLM-relay tunnel into Active Directory and attempting Domain Admin escalation. Unit 42 says command batches were wrapped in section-header delimiters such as "==SHELLUSER==" and "==PASSWD==" that "produce machine-parseable output for an AI model to read," alongside AD and host reconnaissance patterns it reads as the work of an agentic attacker using AI platforms. Separately, CYFIRMA published a Version 3 early-warning advisory on a Korean-speaking team (handles active in a closed I2P channel, Munhwaŏ spelling) probing 216 unpatched or partially-mitigated hosts in the US, Japan, South Korea, Thailand, Indonesia, Australia, Taiwan, India, Singapore, UAE, Saudi Arabia and the EU from 21 compromised nodes — prioritizing government, finance, logistics, aviation, manufacturing and online transaction platforms — which it attributes with moderate confidence to the Lazarus Group ecosystem. Bitsight's scans count nearly 37 million internet-facing PHP systems, and Defused Cyber reports PHP2Shell probe requests (marked "p2s-rider") hitting its GlobalProtect honeypots from a known mass-exploit IP.

*Why it matters:* This is the first documented case in the PHP2Shell saga of an agentic AI platform acting as the post-exploitation operator in a real intrusion — machine-speed follow-through (2 hours to credential and MFA-theft) against a runtime on roughly 70% of the web. The DPRK scanning angle matters operationally: CYFIRMA says operators said they would shortlist promising targets the same day and pick public exploits to adapt, which is exploitation-on-a-timer for anyone still unpatched. Caveat: South Korean security experts told local outlets the evidence does not firmly establish the North Korea link.

*Source:* The Hacker News — "PHP2Shell Exploitation Hits Government Entity Amid Global Scanning and Signs of AI-Assisted Hacking" — Oct 25, 2026, 13:19 ET — https://thehackernews.com/2026/10/php2shell-exploitation-hits-government.html
*Second source:* CYFIRMA — "Suspected DPRK Campaign staging PHP2Shell Targeting against Global PHP Infrastructure" (June 2026 Minjun campaign advisory, v3) — https://lazarus.day/reports/suspected-dprk-campaign-staging-php2shell-targeting-against-global-php-infrastructure-VbQyF/
*Caveat source:* AI Stock Wire — "PHP2Shell attack plan names Korea, 112,000 servers at risk" — https://aistockwire.com/blog/php2shell-korea-112000-vulnerable-servers-lazarus-cyfirma-october-2026

## Models and Local Inference

**A 10-year-old CNN beats GPT-6 Astra and Claude Fable 5.1 at skin-cancer diagnosis**
*Event date: Sunday, October 25, 2026 (same-day pickup corroborates).*
Dr Magnus Lynch, a consultant dermatologist at Guy's Hospital, published a head-to-head on 200 histopathology-confirmed lesions drawn from the public ISIC MILK10k set: a fine-tuned Inception v3 (a 2015 architecture, fine-tuned ~20 minutes on one H100 over ~68,000 dermoscopic and clinical images) named the correct diagnosis on 125 of 200 (62%), versus 84 and 85 (42%) for GPT-6 Astra and Claude Fable 5.1. The CNN found 13 of 30 melanomas against 8 and 11 for the frontier models, and each LLM had a "favourite" wrong answer — Astra called 75 lesions basal cell carcinoma (25 truly were), misclassifying 14 of the melanomas. Asked as a patient, both LLMs advised seeing a doctor in ~99 of 100 melanomas — safe but non-discriminating.

*Why it matters:* Domain-specific small models still dominate frontier generalists on narrow clinical pattern recognition — a caution for anyone deploying frontier VLMs as triage tools, and a reminder that the benchmark leaderboard is not the deployment leaderboard. (Lynch discloses that analysis and first drafts were produced with Claude Code, one vendor under test.)

*Local inference:* the winning model is small enough to run entirely in a phone browser via WebGPU with nothing uploaded — a concrete example of specialized inference that doesn't need a datacenter or an API.

*Source:* lynchlab.net — "A 10-year-old CNN outperforms frontier LLMs for skin cancer diagnosis" — Oct 25, 2026 — https://lynchlab.net/llm-skin-cancer
*Second source:* AI Weekly alert pickup, ~16:00 ET Oct 25 — https://aiweekly.co/ai-news-today

**Also noted — engine builds (all in-window):**
- **llama.cpp b11879–b11887** (8 builds, 12:12–14:34 ET; b11885 CI-skipped): fused FFN gate/up matmuls with GLU in the CUDA MMQ path for faster prefill (#29948), qwen tensor-merge-on-convert (#31028), no_alloc respected in all memory caches (#31087), merge now rejects truncated GGUF tensor data (#31044). — https://github.com/ggml-org/llama.cpp/releases
- **vLLM** — 6 in-window merges (12:14–15:42 ET): preserve FP8 indexer WK pairs across load_weights calls, keep scheduling running requests after priority self-preemption, keep speculator limits in sync after auto-fit, plus CI hardening against unpinned/locked transitive installs. — https://github.com/vllm-project/vllm

## Agent Tools

Nothing new disclosed in-window. Release feeds checked (OpenClaw, Pydantic AI, LangGraph, sglang, PraisonAI) show no in-window releases. The BleepingComputer piece (11:49 ET today) on agents hijacked by an AGENTS.md file dropped into ~/Downloads is coverage of Forever Security's October 22 disclosure and is excluded under the event-date rule.

## Also Noted

- **Data-center late-delivery penalties now exceed $5M/day** — up from ~$1M this spring — as hyperscaler tenants flex their leverage; Marsh's "delay-in-delivery" cover (launched 2025) has been raised to $2.7B limits, and Goldman Sachs expects only about half of scheduled US capacity to arrive on time. (The Information, Oct 25, 3:10 PM ET, paywalled — https://www.theinformation.com/articles/data-center-late-fees-hit-5-million-per-day-spawning-new-insurance ; verified via AI Weekly copy at 3:57 PM ET https://aiweekly.co/alerts/data-center-delivery-delays-now-trigger-5mday-late-fees-up-from-1m-spawning-new and Techmeme river.)

## Just Missed the Window

- **Nanbeige4.3-33B** (Nanbeige LLM Lab, backed by BOSS Zhipin) posted its Apache-2.0, 33B dense "teacher" agent model with a 524,288-token context — self-reported Terminal-Bench 2.1 of 80.9 and DeepSWE of 65.5 versus Qwen3.8-27B's 73.0/42.5, with reasoning on by default.
- **Convovo study on model partisanship:** forced to recommend one candidate across ~500 midterm races, Grok 4.7 picked the Republican in 92% of responses and GPT-6.1 Sol the Democrat in 97%; with search disabled Grok's lean collapsed (refused 27 of 30) while GPT held — pointing at retrieval/source-routing, not weights, and ~1,240 opaque tokens per Grok API call. Originally reported by TechCrunch last night (~11:30 PM ET). — https://aiweekly.co/alerts/convovo-grok-picks-gop-92-of-races-gpt-61-picks-dems-97

## Coverage Notes

- **Verified: 7 items** (1 lead, 1 research story, 2 engine build lines, 1 also-noted, 2 footnotes), each from a fetched page with an in-window or explicitly-anchored date; THN publish times confirmed via the site's RSS feed (13:19 ET lead); GitHub timestamps from the releases/merged-PR APIs.
- **Ledger dedupe: 4 candidates suppressed as already reported** — OpenAI "VM escapes" coverage (ledger securing-frontier-training); Axios AI political-liability piece (ledger ai-political-liability, this morning's fire); Replit–OpenAI $100M deal re-surfaced by AI Weekly (ledger replit-openai-100m-deal); Cloudflare CEO Prince "worse bugs than PHP2Shell" re-coverage folded into the php2shell cluster (no new CVE/exploitation change, timing unverifiable).
- **Dead ends / limits:** Sunday thinness — HN window had 49 submissions with no front-page AI item; HF Daily Papers Sunday page still listing the Oct 23 set; BleepingComputer AGENTS.md story (11:49 ET) excluded as re-coverage of the Oct 22 Forever Security disclosure; THN's in-window "Tailworm" Tailscale SSH report (13:44 ET) is security but not AI, left to the cyber brief; The Information lead is paywalled and was verified through a full-text secondary.
