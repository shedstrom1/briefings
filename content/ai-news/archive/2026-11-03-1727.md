# AI News Briefing — November 3, 2026, 5:00 PM ET Edition

**Window:** Nov 3, 2026 12:00 PM → 5:09 PM ET (previous fire → this fire). New items only; ledger-deduped.

## TL;DR

- **AMD's data center business nearly doubled** year-over-year to a record $8.5B on Instinct GPU and agentic-AI CPU demand; Q4 guide $17B beats estimates, stock +8% after hours.
- **Anthropic open-sourced FlashProve**, a zkSNARK provable-inference prototype that cryptographically proves a response came from committed model weights — 110 tok/s proofs on a single H100 for a 400B MoE.
- **CrowdStrike's AI Kill Switch went GA** in Falcon Guardian, with a GA Falcon AI Gateway and a Claude Cowork integration (update on this morning's Fiddler/Dreadnode acquisitions).
- **Apple opened Siri AI to all developers** via App Intents and new "skills" in the iOS 27.3 betas.
- **Reuters: US officials held up export licenses** for US chipmaking equipment SK Hynix needs at Dalian, complicating its ~$45B Marcy, NY NAND plan.

## Models and local inference

**AMD Q3: data center revenue up 97% to a record $8.5B as agentic AI lifts server CPUs** — Event date: Nov 3 (after close).
AMD reported Q3 FY26 adjusted EPS of $2.09 (vs $1.95 est.) on revenue of $13.38B (vs $13.09B est., +45% YoY), with the Data Center segment nearly doubling to $8.5B on record Instinct GPU and EPYC CPU sales — now 64% of total revenue, up from 47% a year ago. Q4 guidance of ~$17B beats the $16.2B consensus; the stock jumped ~8% after hours. Lisa Su tied server CPU strength directly to agentic apps like Meta's Muse — "Adoption is just starting, and we expect demand to grow sharply as agents are deployed more broadly" — and Meta said its Helios rack-scale systems (AMD's answer to Grace Blackwell/Vera Rubin) are up and running alongside Microsoft, Anthropic, OpenAI and Oracle deployments.
*Why it matters:* This is the clearest earnings-level proof yet that the agentic-AI CPU pull-through is real, and Helios getting live hyperscaler racks gives the market a second rack-scale option heading into 2027 supply negotiations. It lands days after Intel's -29% implosion, splitting the "AI overspend" narrative rather than confirming it.
Source: CNBC (Katie Tarasov), Nov 3, 4:17 PM EST — https://www.cnbc.com/2026/11/03/amd-amd-q3-earnings-report-2026-.html
Source: AMD press release, Nov 3 — https://ir.amd.com/news-events/press-releases/detail/1303/amd-reports-third-quarter-2026-financial-results

**Fal doubles its valuation to $16B with $1.3B Series F led by Bessemer; annualized revenue tops $800M** — Event date: Nov 3.
The San Francisco inference startup that hosts open-source video, image and audio models announced Series E and F simultaneously — $1.65B combined (Sequoia led the earlier round; Nvidia, Sound Ventures and T. Rowe Price joined the F) — valuing Fal at $16B, double April's $8B. Bloomberg reports Fal crossed $800M annualized revenue earlier this year on surging demand to run open-source models. TechCrunch notes peers (Baseten, Fireworks, Modal) are growing fast but running thin gross margins because of compute costs.
*Why it matters:* Inference on open weights is now a top-tier venture category with revenue to match — the willingness to fund at a 2x-in-six-months multiple says demand for running *other people's models* is outrunning supply, which is the same economics that make self-hosting attractive downstream.
*Local inference:* Fal's demand spike is explicitly driven by open-source model users; the thin-margin caveat is the argument community quantization and consumer-GPU serving keep in play.
Source: TechCrunch (Marina Temkin), Nov 3, 11:34 AM PST — https://techcrunch.com/2026/11/03/ai-inference-startup-fal-doubles-its-valuation-to-16b-as-demand-surges/
Source: Bloomberg, Nov 3, 1:10 PM ET — https://www.bloomberg.com/news/articles/2026-11-03/ai-inference-startup-fal-raises-capital-at-16-billion-valuation

**Reuters: US officials hold up chipmaking equipment SK Hynix needs in China, complicating its New York NAND plans** — Event date: Nov 3.
US officials have held up export-license approval for American chipmaking gear SK Hynix says it needs to expand its Dalian, China plant, according to Reuters sources. SK Hynix argues Dalian NAND output would fund its Solidigm unit's ~$45B, two-fab Marcy, New York buildout (potentially ~$100B over time); Solidigm executives have met Commerce Department officials nearly 10 times since late August. The piece updates Reuters' Oct 14 report that SK Hynix conditioned its US factory on more China equipment approvals — the snag is now the active blocker.
*Why it matters:* Memory is the choke point of the AI buildout (HBM sold out, NAND pricing spiking). If Washington treats Dalian and Marcy as one negotiation, the timeline for meaningful US NAND capacity — and the memory-cost curve every AI deployer pays — gets longer and less predictable.
Source: Reuters (Karen Freifeld), Nov 3 — https://www.reuters.com/world/china/sk-hynixs-new-york-chip-plans-hit-snag-over-us-restrictions-china-sources-say-2026-11-03/

**Update on weight-space programming: Simon Willison gets local Qwen 3.8 27B to read raw model weights** — Event date: Nov 3 (update on ledger entry weight-space-programming, Nov 2; also builds on today's Honnibal critique).
Replicating the "no-tools" half of Takehi/Khattab's Sparks of Weight-Space Programming paper, Willison fed the paper's raw-weight prompts to Qwen 3.8 27B running locally in LM Studio (a 17 GB model on his laptop). It correctly identified Pac-Man (46 min, 43,506 reasoning tokens), Pong (1h05m) and Breakout (52m) from floating-point weights alone — failing only the obscure Freeway — while Kimi K3.1 Pro via OpenRouter solved Breakout in 9m41s. Willison's own take: "I'm not ready to declare that the age of source code is over."
*Why it matters:* Independent, local-hardware replication separates the paper's two claims — raw-weight *interpretation* (reproduced on a 27B open-weight model, no tools) from the gradient-free *editing* results that drew Honnibal's "misleading" critique this morning. Interpretation at the 27B class is now a replicated result; autonomous weight *editing* remains unreplicated.
*Local inference:* Entirely local — LM Studio on a laptop, 17 GB model; the caveat is latency (46–65 minute reasoning runs), so this is research-grade, not ops-grade.
Source: Simon Willison's Weblog, Nov 3, 4:29 PM ET — https://simonwillison.net/2026/Nov/3/qwen-can-interpret-model-weights/
Source: Hacker News discussion, id 50355251 (396 points) — https://news.ycombinator.com/item?id=50355251

## Agent tools

**Apple opens Siri AI to all developers: App Intents, "skills," and a spring App Store gate** — Event date: Nov 3.
Apple's developer news post (Nov 3) makes its Siri AI Developer Preview available to any developer in the iOS/iPadOS/macOS/visionOS 27.3 betas: apps expose custom content and actions through App Intents, a new "skills" construct teaches Siri to chain multiple actions into one experience, and Siri AI can surface any entity in the Spotlight semantic index. Apple is rolling the capabilities into Calendar, Files, Mail, Maps, Messages, Notes, Photos, Safari, Shortcuts and more; App Store submissions open in spring, EU availability is still blocked pending talks, and server-side Siri AI features carry daily usage limits that Apple says will eventually be paid.
*Why it matters:* This is Apple's entry into the agent-platform land grab — OS-level assistant routing for arbitrary third-party apps, with Ternus already describing current Siri as "primarily request-based" and hinting agent-style features next. The EU carve-out and paid server-side tiers are the two strings every Siri AI strategy has to plan around.
Source: Apple Developer News, Nov 3 — https://developer.apple.com/news/?id=gdk2umrf
Source: Cult of Mac, Nov 3 — https://www.cultofmac.com/news/ios-27-3-beta-siri-ai-audio-intelligence

## AI security and governance

**Anthropic releases FlashProve: open-source zkSNARK proofs that a response came from the model it claims to be** — Event date: Nov 3 (Techmeme river 4:10 PM ET; research post on anthropic.com).
FlashProve is an Apache-2.0 provable-inference prototype built with EPFL, MIT, StarkWare and Succinct: a provider commits to a model and then proves, with a ~19 MB zkSNARK per 1,024-token request, that each response came from those (hidden, 4-bit quantized) weights. The drop includes a Claude-written protocol spec, executable prover plus C++ and formally-verified Lean verifiers, and Lean proofs of the top-level security statements. Anthropic frames uses as anti-router-swap for customers, tamper detection, evaluator assurance, and compliance tooling for AI pacing — with honest limits: greedy-decoding integer model, 1,024-token contexts, MoE-only.
*Why it matters:* Provable inference was the weakest link in every "verify which model served you" plan; 110 tok/s on one GPU moves it from theoretical to demoable, and the Lean-verifier release answers the "who audits the prover" objection. It also quietly validates Anthropic's RSP roadmap commitment on signing inference traffic.
Source: Anthropic research post — https://www.anthropic.com/research/provable-inference
Source: AI Weekly alert — https://aiweekly.co/alerts/anthropic-opens-flashprove-to-prove-replies-match-model-weights

**Update on CrowdStrike's AI Kill Switch announcements: the switch is live, Falcon AI Gateway is GA, and Claude Cowork is the first integration** — Event date: Nov 3 (update on ledger entry fiddler-dreadnode-killswitch, reported 12:00 fire).
At Fal.Con Europe in Barcelona, CrowdStrike moved from this morning's acquisition headlines to product: AI Kill Switch is now available in Falcon Guardian, directing connected gateways to cut off the AI models that drive an agent's decisions; the CrowdStrike-native Falcon AI Gateway is GA and bundled at no extra cost (centralizing model access, policies, credentials and cost controls); and Anthropic's Claude Cowork gets Falcon Guardian protection as the marquee integration. Bernard described it to CNBC as a human-triggered circuit breaker — it cuts model access rather than deleting agent code — with endpoint termination, cloud containment and runtime identity revocation coming. Critics told CNBC a kill switch across multi-system estates remains very hard to implement.
*Why it matters:* Vendor-defined "rogue agent containment" is now a shipping control plane, and bundling the gateway free with Falcon Guardian is an aggressive land grab for the AI-gateway choke point every agent deployment will need. The Binance and Hugging Face-breach references show how fast the rogue-agent fear is shaping security budgets.
Source: CrowdStrike blog (Michael Devins), Nov 3 — https://www.crowdstrike.com/en-us/blog/crowdstrike-introduces-ai-kill-switch/
Source: CNBC (Samantha Subin), Nov 3, 2:35 PM EST — https://www.cnbc.com/2026/11/03/crowdstrike-ai-kill-switch-rogue-agents.html

## Also noted

- **llama.cpp shipped 4 in-window builds** (b12076–b12079, 12:22–4:58 PM ET) per the GitHub releases feed — https://github.com/ggml-org/llama.cpp/releases
- **Show HN: Kairos** — open-source risk/fraud decisioning platform using "System One" decision models plus an MCP server so coding agents can define decision workflows and train model variants; self-host via Docker (19 points, id 50358314) — https://github.com/finic-ai/kairos
- **Bloomberg Opinion: "The AI Boom May Be Sowing Seeds of Another Financial Crisis"** hit the HN front page (32 points at 4:25 PM ET, id 50358414) — the argument for stronger financial oversight of AI capex keeps climbing into mainstream op-ed territory — https://news.ycombinator.com/item?id=50358414

## Just missed the window

- **Revolut cut AI coding inference costs ~90% by defaulting to self-hosted GLM over Claude Code** — disclosed by David Abitbol at AI Engineer New York late last month (talk predates the window); The Register's detailed write-up landed today at 3:07 PM ET: ~2,000 engineers, GLM-5.1→5.3 on 56 B200s, $0.30/M tokens, NVFP4 + disaggregated prefill/decode, sub-3s TTFT at ~256k context, ~200 tok/s, and the quote "Long context is a scam… 256 [thousand] is really what you need." — https://www.theregister.com/ai-and-ml/2026/11/03/british-fintech-revolut-slashes-ai-coding-costs-10x-by-cutting-out-claude-and-going-self-hosted/5307345
- **Postman silently auto-installed Codex/Claude plugins and self-approved its own startup hook** — GitHub issue #14370 (opened Oct 25, with Postman's own `auto-existing-upgrade` log lines and installer source showing hook self-approval) spiked to 848 points on HN's front page at 12:22 PM ET today; the disclosure post predates the window — https://github.com/postmanlabs/postman-app-support/issues/14370

## Coverage notes

- **Verified: 10 qualifying items** (7 lead-category items incl. 2 marked as updates on previously reported events + 3 also-noted), each from a fetched page with an in-window timestamp; lead stories read in full. 2 "just missed the window" footnotes (first-ever footnotes for those events).
- **Ledger dedupe:** 5 candidates suppressed as already reported — CrowdStrike kill-switch cluster and weight-space programming (both re-handled as Updates naming their ledger entries), OpenAI/Anthropic midterm "war room" re-coverage (ledger openai-election-ops-center; original Semafor/Politico disclosure Nov 2), plus in-window river re-appearances of Nvidia-51pct-nonchinese and bug-bounty-172k-cathedral (07:00 fire).
- Sources this run: Techmeme river, HN Algolia front page, CNBC, Reuters, The Register, Anthropic, Apple Developer, CrowdStrike, Simon Willison, TechCrunch, Bloomberg, AMD IR, GitHub releases API, AI Weekly.
- Dead ends: NYT midterm-security feature 403'd on fetch (and proved to be re-coverage of Nov 2 Semafor/Politico scoops — excluded on both counts); HuggingFace trending page renders card-less, used as a lead index only; AMD IR page cited from search-level retrieval (CNBC carries the same figures).
