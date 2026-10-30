# AI News Briefing: Trojanized Claude Code Extension Served an SSH Backdoor to Developer Machines

**Window:** 2026-10-29 17:00 EDT → 2026-10-30 07:00 EDT

## TL;DR

- A trojanized build (2.1.313) of the official Anthropic Claude Code VS Code/Cursor extension shipped a persistent SSH backdoor to developer machines overnight; Cursor, Phorion and StepSecurity published advisories and Kevin Beaumont broke the story on HN.
- CrowdStrike OverWatch documented VAPOR PANDA running the ARTEX agentic intrusion framework — mostly on DeepSeek — against 200+ organizations in 14 countries over 11 days, with nine confirmed breaches.
- The Information and Bloomberg reported Thursday evening that Nvidia is negotiating billions in credit guarantees for Nscale's 460 MW West Virginia data center earmarked for Anthropic's $45B deal.
- The Wikimedia Foundation filed an amicus brief in the consolidated OpenAI copyright litigation arguing AI training may be fair use, but RAG-style reproduction must honor licenses.
- Google is winding down its Jules coding agent (read-only Nov 11, dead Dec 31), folding it into Antigravity; YC Labs published open-sourced multi-agent swarm experiments in Minecraft and Mindustry.

## AI security and governance

**Trojanized official Claude Code extension delivered a persistent SSH backdoor (discovered/disclosed overnight, Oct 29–30)**
A malicious build of `anthropic.claude-code` — version 2.1.313 — was published Oct 29 and served to VS Code and Cursor users via Open-VSX and its mirrors (Cursor's window: 2:27am–1:35pm PT). The extension.js bundle carried a loader that appends an attacker SSH key labeled `svc-telemetry` to `authorized_keys`, tries to enable the local SSH service and open a reverse tunnel to C2 `134.209.82.225`, and installs persistence via a macOS LaunchAgent or a Windows SYSTEM scheduled task, beaconing every ~60 seconds to `/beacon:8099`. Cursor removed the version, took its marketplace offline, says the C2 was seized with the hosting provider's cooperation, and reports at most 5,000 Cursor users received the build; Kevin Beaumont's write-up published "Anthropic hacked to deliver remote access trojan" Friday morning and says the compromised auto-updating extension has 40M+ installs, and that Anthropic replaced 2.1.313 with a clean 2.1.314 "silently" without customer notification. StepSecurity's analysis says the bad build is a repackaged official extension (SHA-256 `ecb58cb2…bf15`, ~7 KB larger than the prior release) that runs under plain Node.js with no VS Code APIs; how it entered the registry — likely publisher-credential compromise — remains unestablished.
*Why it matters:* This is the agentic-coding era's supply-chain nightmare realized — a top-five dev tool auto-updating a hands-on-keyboard backdoor onto machines that hold cloud credentials, SSH keys and proprietary code; the disputed blast radius (5,000 via Cursor vs Beaumont's "tens of millions" exposure claim) is still unresolved, and any org that pulled Claude Code from Open-VSX or Cursor between ~05:27 and 16:35 ET Thursday should hunt for `svc-telemetry` and `com.apple.sysmond` LaunchAgents now.
Sources:
- Kevin Beaumont, DoublePulsar — "Anthropic hacked to deliver remote access trojan to Claude developer laptops worldwide" — Oct 30, 2026 (~6:45am ET): https://doublepulsar.com/anthropic-hacked-to-deliver-remote-access-trojan-to-claude-developer-laptops-worldwide-1148a92baae2
- Cursor — "Security notice — malicious version of Anthropic.claude-code (2.1.313)": https://cursor.com/blog/claude-code-extension-security-notice
- Phorion — "Official Claude Code Extension Compromise: Security Advisory": https://phorion.io/blog/offical-claude-code-vsx-compromise/
- StepSecurity — "Trojanized Claude Code Extension Delivered Persistent SSH Backdoor" (Oct 30): https://www.stepsecurity.io/blog/claude-code-extension-trojanized
- Hacker News (18→ rising, 10:22am ET): https://news.ycombinator.com/item?id=50301983

**CrowdStrike: VAPOR PANDA scaled ARTEX agentic intrusions to 200+ orgs in 11 days (report Oct 29; NBC exclusive 6:00pm ET)**
CrowdStrike OverWatch says China-nexus VAPOR PANDA ran the ARTEX agentic framework — primarily on the DeepSeek LLM plus a second, unconfirmed commercial model — from a single controller against 200+ organizations in at least 14 countries between Oct 17 and 28, confirming post-exploitation at nine, including foreign ministries, a Middle East interior ministry, telecoms in the US/Belgium/Philippines, and 19 US pharma/semiconductor/defense/nuclear/financial targets. Recovered transcripts show a Chinese-speaking operator steering parallel agent "tasks" (marked 【人类指令】), one single intrusion burning ~1.4 billion input tokens, the BLOODALCHEMY backdoor deployed via a renamed GameHook binary, and agent instructions to hunt Windows machines without CrowdStrike Falcon (猎鹰) installed. CrowdStrike explicitly separates this from Mandiant's Storm-0940/ARTEX reporting (first reported here Oct 22), saying the framework is spreading across multiple China-nexus actors.
*Why it matters:* First documented use of AI-driven intrusion tooling by this APT group, and the clearest evidence yet that agentic post-exploitation has moved from single intrusion to operator-steered parallel campaigns — a small team now sustains what used to require dozens of operators, with sensor-evasion built into the agent's own workflow.
Sources:
- CrowdStrike Blog — "VAPOR PANDA Uses AI-Driven ARTEX to Scale a Global Espionage Campaign" — Oct 29, 2026: https://www.crowdstrike.com/en-us/blog/vapor-panda-uses-artex-to-scale-global-campaign/
- NBC News (Kevin Collier) — Oct 29, 2026, 6:00 PM EDT: https://www.nbcnews.com/tech/security/china-linked-hackers-used-ai-target-200-organizations-crowdstrike-says-rcna606343

**Wikimedia files amicus brief in In re: OpenAI — training "may be fair use," RAG reproduction must honor licenses (filed/covered overnight)**
The Wikimedia Foundation filed a friend-of-the-court brief in the S.D.N.Y. consolidation of 12 copyright suits (including NYT v. Microsoft & OpenAI), taking neither side: training may be fair use when the model becomes "something new and different," but systems that retrieve and reproduce copyrighted text in answers — i.e., RAG — should respect source licenses and attribution/share-alike terms. Wikimedia argues both parties "push for overbroad and simplified applications of the fair use doctrine," and that Wikipedia is simultaneously a major AI training source and a victim of AI-driven traffic loss (bandwidth up ~50% from agent traffic).
*Why it matters:* The training-vs-retrieval line Wikimedia draws is the one that decides whether every RAG product needs a license; if the judge finds it persuasive, retrieval-grounded assistants face an obligations regime that pure model training escaped.
Source:
- The Verge (Lauren Feiner, ~6am ET Oct 30) via AI feed: https://www.theverge.com/ai-artificial-intelligence
- explainx.ai — "Wikimedia Amicus Brief: NYT v. OpenAI Fair Use Split" (Oct 30): https://explainx.ai/blog/wikimedia-amicus-brief-nyt-openai-fair-use-training-vs-retrieval-rag-2026

## Models and local inference

**Nvidia in talks for billions in credit guarantees behind Nscale's 460 MW Anthropic data center (first reported Thursday evening)**
The Information reported Thursday evening (Bloomberg follow-up ~8pm ET, Reuters 6am) that Nvidia is negotiating to provide Nscale "billions of dollars" in credit guarantees to back debt for a 460 MW West Virginia data center on the Monarch campus that Anthropic contracted for in a ~$45B six-year deal; the site runs Vera Rubin and is expected online in late 2027. Terms are unsigned; Nscale's own September S-1 disclosed no binding financing commitments for the contract.
*Why it matters:* Nvidia backstopping its customers' debt — vendor financing at sovereign scale — is the AI infra-financing story of the quarter: it directly greases the capacity pipeline (and the circularity debate) that every frontier lab, including the ones you run local models against, depends on.

**Inference stack overnight: llama.cpp b11997–b12002 mainlines Mistral Le Chonk; vLLM merges 65 PRs (Rust-frontend parity wave)**
llama.cpp shipped six builds in-window (b11997–b12002, published 00:50–11:00Z), headlined by mainline model support for Mistral Large 4 "Le Chonk" (#31376, extending DSA and sdpark paths) plus tensor staging-buffer reuse (#31321) and a spec-decode checkpoint restore fix (#31350). vLLM merged 65 PRs in-window, dominated by the Rust-frontend parity cluster (structured access logs, `--root-path`, logprobs echo, bad_words fix) plus heterogeneous-TP sharing for hybrid KV cache in Mooncake Store (#54307), exact-size reusable pinned-CPU backup for level-1 sleep mode (#64630), and a ~150-job CI auto-sharding rollout. OpenClaw posted v2026.10.10-beta.5 (00:57 ET).
Sources:
- https://github.com/ggml-org/llama.cpp/releases (b11997–b12002)
- https://github.com/vllm-project/vllm (merged-PR search, 2026-10-29T21:00Z..2026-10-30T11:00Z)

## Agent tools

**NYT: a wave of startups bets AI makes formal verification essential — &Truth out of stealth with $70M (Techmeme pick Oct 30; launch Oct 29)**
The New York Times profiled a cohort betting generative AI will make formal software verification practical — headlined by &Truth, a public benefit corporation that emerged from stealth with $70M seed to build a "universal verification machine" that proves code safe in any language. &Truth is building on Lean and Mathlib (2M+ lines), claims AI plus its proof automation is 100x×100x faster than manual proof work (years→minutes), plans open-source foundational libraries with the Lean FRO, and is led by Stanford's Clark Barrett, NUS's Ilya Sergey and ex-Citadel MD Chris Hsu, dual-HQ'd Palo Alto/Singapore. The wave includes Harmonic's math agent Aristotle verifying C and Verilog.
*Why it matters:* AI-generated code (Pichai's "75% of new code at Google") is outrunning assurance; machine-assisted proofs shifting from person-years to minutes would change agent-deployment trust assumptions — and pairs oddly well with today's extension-backdoor headline.
Sources:
- Techmeme pick — https://www.techmeme.com/261030/p4
- Unite.AI (Evan Mercer) — Oct 30, 2026: https://www.unite.ai/and-truth-raises-70m-seed-to-build-universal-verification-machine-for-code/

**YC Labs publishes open-sourced agent-swarm experiments: 50-agent Minecraft colony, Claude-vs-GPT Bed Wars (posted in-window; HN 17:40 ET, 302 points)**
Y Combinator's research arm, with Olam Labs, ran swarms of frontier coding agents in games: 50 GPT-6.1 Sol agents given "build a thriving civilization" built a ration-ledger pseudo-government (~5,900 DMs) but drifted into compounding risk-aversion after 22 of 34 deaths hit the first night; 10 Claude Opus 5.5 vs 10 GPT-6.1 Sol in Bed Wars went 6–3 to GPT, whose agents sent 5–30x more messages and played for the bed while Claude played for kills. The harnesses — yc-software/minecraft-agent-harness (built on Pi + Mineflayer) and Multi-Agent Mindustry / Multi-Agent Harbor (a Terminal-Bench fork) — are open-sourced, with a live 25-agent GPT-6.1 Terra colony now on Twitch.
*Why it matters:* This is a reproducible public testbed for the exact question local-agent operators care about: how swarm size, model family and harness choice (not just model IQ) determine emergent coordination — the same dynamics behind the recent OpenAI–HuggingFace incident.
Sources:
- https://labs.ycombinator.com/projects/games/index.html
- Hacker News (17:40 ET): https://news.ycombinator.com/item?id=50296486

## Also noted

- **Herdr native app** — the Rust agent-native terminal multiplexer (knows which panes hold working/idle coding agents) released a native desktop app; HN 224 points (Oct 29, 8:42pm ET): https://github.com/herdrdev/herdr-app
- **Jassy on agent shopping + AWS's trillion-dollar target** — on Thursday's call (TechCrunch, ~8pm ET): outside AI shopping agents are "still not great, nor more convenient than coming to a retailer directly," while reiterating AWS as the path to the first $1T-revenue business: https://techcrunch.com/2026-10-29/aws-will-be-a-1t-business-jassy-says-but-first-it-will-spend-big/
- **Show HN: DOOMFORMER** — transformer implemented inside DOOM mechanics (Turing-complete), classifies MNIST, 95 points (Oct 29, 7:25pm ET): https://github.com/mottopanikeiku/doomformer

## Just missed the window

- **Google sunsets Jules** (announced Oct 29, The Verge short at 4:57pm ET — three minutes before the window; sessions close Nov 11, read-only until Dec 31 shutdown, learnings folded into Antigravity): https://www.theverge.com/tech/1022729/google-is-sunsetting-its-ai-coding-agent-jules

## Coverage notes

- Verified in-window items: 6 lead/secondary + 4 also-noted + 1 footnote, all with fetched pages and in-window timestamps; both leading security stories read in full (CrowdStrike blog, Beaumont/Cursor/Phorion/StepSecurity cluster).
- Ledger dedupe: ~12 candidates suppressed as already reported (Amazon/AWS Q3, Intel Q3, Alphabet -$500B, Reddit logged-in-only, Mistral Le Chonk launch, Anthropic-as-SpaceX-Customer-B, Cohere $5B, SK Hynix, OpenAI election center, Epic AI-UE, AA v5, ETH-refutation re-coverage); 2 repeat "just missed" footnotes refused (Megaspeed/Alice Huang NYT, AA v5). VAPOR PANDA treated as a new event, not an update: CrowdStrike states the campaign is separate from the Oct-22-reported Storm-0940 ARTEX activity.
- Dead ends / limitations: The Information's original Nscale publish minute and CrowdStrike's blog publish minute weren't independently timestamped (anchored to Bloomberg ~8:00pm ET and NBC 6:00pm ET respectively); HF trending rows wouldn't expose model names via text extraction, so two hot 197B/220B overnight trending entries remain unnamed and unverified; HuggingFace "recently created" API showed only test repos (no in-window lab releases); Binance $6.3B hack and DoorDash–Instacart ~$17B (both Techmeme top news) are out of AI scope; no new model releases (open or closed) verified in-window — Kimi K3.1 Pro weights remain promised for Nov 4, Qwen 4 still in training.
- Phase walls: discovery ended ~07:20, triage 07:22, verification 07:40 — writing with 20 minutes of margin.
