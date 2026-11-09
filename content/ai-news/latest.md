# AI News Briefing — Monday, November 9, 2026, 5:00 PM ET

**Window:** 2026-11-09 12:00 → 17:00 EST (previous fires covered 07:00–12:00 via the 15:00 catch-up run). New-items-only; ledger dedupe applied.

## TL;DR

- **Anthropic launched Claude Fable 5.2 and Mythos 5.2** this afternoon — a new #1 on the Artificial Analysis index (65), a formalized Lean proof of Fermat's Last Theorem in ~30 hours, two solved open black-hole problems, and pricing cut 20% to $8/$40 per MTok.
- **Meta's Superintelligence Lab shipped Muse Ember**, a closed-weight, ultra-cheap agent-execution model at $0.10/$0.50 per MTok with 1M context and native text/image/audio/video — priced to fight DeepSeek V4.1-Flash and GLM-5.3 Flash.
- **Fireworks AI disclosed its second breach in weeks**: an Oct 24 intrusion that accessed datasets and incomplete model weights, with evidence linking it to financially motivated "LLMJacking" groups.
- **OpenAI published Control Room**, a fail-closed kill-switch system that halts any research model workload within 35 seconds of a stop decision.
- **Local corner:** a lawyer is running DeepSeek V4.1-Flash at up to 215 tok/s on eight used V100s for ~$4,800, and Ollama cut v0.40.16-rc0 with Qwen3.8-Flash-Next MLX memory-efficiency work.

## Models and local inference

**Anthropic releases Claude Fable 5.2 and Claude Mythos 5.2** — Event: Monday, Nov 9 (The Verge published at 2:00 PM EST; HN submission 3:04 PM EST).
The same underlying model ships twice: Fable 5.2 for everyone with cyber/bio safeguards, Mythos 5.2 gated behind Anthropic's Cyber and Life Sciences Verification Programs. Specs: 1M-token context, 128K max output, adaptive thinking always on, knowledge cutoff June 2026, $8/$40 per MTok — 20% under 5.1 — with a 2.5x-speed "fast mode" at $16/$80. Prerelease highlights Anthropic published: formalizing Fermat's Last Theorem in Lean in ~30 hours, completing a quantum version of the Hawking-Page phase transition and bounding no-go theorems for AdS/CFT (two black-hole preprints), an agent swarm of 1,000+ subagents that wrote a bootable prototype OS over eight days, and Mythos 5.2 accurately modeling 28 of 34 AAV capsids via a novel merged-chain folding approach. On Artificial Analysis's index the model scored 65, the highest recorded; on FrontierCode 2.0, High effort matched Opus 5.5 at Max effort for roughly half the cost per task. Available day one on AWS, Google Cloud and Azure, and already on Vercel AI Gateway.
*Why it matters:* A frontier release priced *below* its predecessor resets the premium-tier price curve weeks after Gemini 4 Carbon took #1 — and the Fermat/capsid results, if they hold up in the System Card and preprints, push the "AI doing publishable math and protein design" narrative from demos into artifacts. Mythos gating keeps the most capable cyber/bio variant inside verification programs, which is becoming the industry's de facto tiering pattern.
*Local inference:* No local claim — closed weights; community reaction on r/LocalLLaMA was "when is an open model on par with Fable 5.2?"
- Source: Introducing Claude Fable 5.2 and Claude Mythos 5.2 — Anthropic, Nov 9, 2026 — https://www.anthropic.com/claude-fable-and-mythos-5-2
- Source: Anthropic launches new Claude Fable and Mythos 5.2 models — The Verge (Jay Peters), Nov 9 2:00 PM EST — https://www.theverge.com/ai-artificial-intelligence/1030005/anthropic-claude-fable-mythos-5-2
- Source: Claude Fable 5.2 — Claude Platform docs (Released November 9, 2026; $8/$40, 1M ctx) — https://platform.claude.com/docs/en/models/fable-5-2/overview
- Source: Claude Fable 5.2 Scores 65 on Artificial Analysis, Highest So Far — HN front page, 3:04 PM EST (116 pts) — https://news.ycombinator.com/item?id=50431144

**Meta releases Muse Ember, its smallest/cheapest Muse model** — Event: Monday, Nov 9 (research.meta.ai blog "November 9, 2026"; TechCrunch ~1:00 PM EST).
Muse Superintelligence Lab's fourth Muse model is an execution-tier model for "tool calls, coding subtasks, routing, extraction, and long document parsing": $0.10/MTok input ($0.004 cached), $0.50/MTok output, 1M-token context, up to 512K output tokens, native text/image/audio/video. TechCrunch notes it is cost-competitive with open-weight rivals DeepSeek V4.1-Flash and GLM 5.3 Flash.
*Why it matters:* This is Meta formalizing the two-tier agent stack — "Muse Wonder plans, Muse Ember handles the volume" — at pricing engineered to make sub-agent tokens nearly free.
- Source: Introducing Our Smallest, Most Efficient Model: Muse Ember — Meta AI Research, Nov 9, 2026 — https://research.meta.ai/blog/muse-ember
- Source: Meta releases Muse Ember, a new lightweight model for agent execution — TechCrunch (Russell Brandom), Nov 9 — https://techcrunch.com/2026/11/09/meta-releases-muse-ember-a-new-lightweight-model-for-agent-execution/

**Lawyer runs DeepSeek V4.1-Flash at 215 tok/s on eight used Tesla V100s** — Event: Tom's Hardware piece published Nov 9; HN post 2:58 PM EST.
A civil-litigation attorney built a fully offline legal-AI server from two 4-card V100 SXM2 NVLink boards (~$2,400 each) plus PEX switches, running a 2-bit "Volta-sPLIt" quant of DeepSeek-V4.1-Flash-1017 with every expert resident in GPU memory at up to 215 tok/s (pipeline-parallel 2 × tensor-parallel 4). The builder says 50–60% of their AI work now skips frontier APIs entirely, and they're porting the stack to NVFP4 on the same hardware targeting ~80 tok/s. Build repo: ProprietaryLegal/Volta-sPLIt-DeepSeek-V4.1-Flash-1017-8xV100-SXM2-PP2xTP4.
*Why it matters:* The strongest recent proof that decommissioned datacenter gear + aggressive quantization can carve most routine professional work off API bills — the economics argument for regulated-industry self-hosting in a single build.
- Source: Lawyer builds private AI server with eight used Nvidia V100 GPUs — Tom's Hardware (Shane Downing), Nov 9, 2026 — https://www.tomshardware.com/tech-industry/artificial-intelligence/lawyer-builds-private-ai-server-with-eight-used-nvidia-v100-gpus-up-to-215-tokens-per-second-to-keep-legal-files-offline-costing-about-usd2-400-per-four-gpu-board
- Source (community origin): r/LocalLLaMA post by the builder; GitHub repo above.

**Update on previously reported (ledger 2026-11-06 Arete-27B): community reproduces confident-hallucination regressions.** A r/LocalLLaMA post today ("CONFIRMING APOLLO-MG FINDINGS," 4:00+ PM EST) reports an independent organic test — 30 questions about nonexistent entities — reproducing earlier claims that Nous's Arete-27B value-alignment SFT on Qwen3.8-27B introduces confident fabrications, with Qwen3.8-Flash-Next and Qwen3.6-35B scoring better on the same refuse-vs-fabricate bench. Single-tester, hand-made benchmark; treat as a community flag pending proper eval numbers.
- Source: Reddit thread — https://www.reddit.com/r/LocalLLaMA/comments/1xs1nqc/confirming_apollomg_findings_organic_handmade/

## Agent tools

**llama.cpp: five in-window merges behind the llama.cpp train (post-b12229)** — Event: commits 13:08–16:04 EST, Nov 9.
Headlines: **external PLE file loading** (#31185, 18:41 UTC — Packed Latent Embeddings split out of the main GGUF, the Qwen4Exp-style offload pattern), **Windows unbuffered model load** (#26014, faster/lower-RAM startup on Windows), a spec-decoding functionality test (#31027), and a revamped web UI that "makes the app feel native on phones" (#31949/#31952, 15:06–15:08 EST).
*Why it matters (local inference):* External PLE files let MoE-with-embeddings models ship lean GGUFs with the embedding table loaded separately — a memory-tiering primitive that matters on 16GB cards.
- Source: GitHub commit feed, ggml-org/llama.cpp (timestamps verified via API) — https://github.com/ggml-org/llama.cpp

**vLLM: 23 in-window merges; MXFP6 quantization support is the standout** — Event: merges 17:00–22:00 UTC, Nov 9.
Highlights: **compressed-tensors MXFP6 (mxfp6-pack-quantized) support** (#61666), V2-runner decode optimizations for NVIDIA Confidential Computing (#59981), a non-deferred structured-output spec-decode draft fix (#66081), AWS EFA in the main Docker image with separate DeepEP-EFA build (#65576), SM100 batch-invariance fix in Triton unified attention (#60531), plus a heavy ROCm/gfx950 CI+kernel block (stochastic Mamba-cache rounding, persistent AITER BF16 prefills).
- Source: GitHub merged-PR search, vllm-project/vllm — https://github.com/vllm-project/vllm

**Ollama v0.40.16-rc0 released** — Event: Nov 9, 1:21 PM EST.
First release since v0.40.15 (Nov 3). Notes: "MLX: make Qwen3.8 Flash Next memory efficient" (#18078), Ripple-65B-Preview support in the MLX runner (#19592), Windows CUDA 13.5 + SM103 ARM64 wheels (#19580), llama.cpp bump to b12177, Claude Code launch routing fixes, and /v1/models now returns an empty array instead of erroring when nothing is installed.
*Local inference:* Qwen3.8-Flash-Next MLX efficiency + Ripple preview on Apple Silicon are the two to watch for 24GB Macs.
- Source: GitHub release v0.40.16-rc0 — https://github.com/ollama/ollama/releases/tag/v0.40.16-rc0

**Show HN: Nuance Labs' Emma, a real-time conversational video persona** — Event: HN post 4:38 PM EST, Nov 9.
Seattle startup Nuance Labs released Emma, a full-duplex audio-visual AI you video-call in the browser (mic, optional camera), built on their Nuplex system; they prioritized naturalness and expressiveness over standard benchmarks, with a beta developer API at platform.nuancelabs.ai.
- Source: Show HN — https://news.ycombinator.com/item?id=50432589

**"USB" — an open trust layer for AI skills and MCP servers** — Event: HN post 4:42 PM EST, Nov 9.
New open-source project (github.com/PeepSick/usbplus) proposing a signing/trust layer for agent skills and MCP servers — aimed at exactly the supply-chain gap this month's incidents (trojanized Claude Code, Fireworks) keep exposing. Community launch; maturity unverified.
- Source: HN — https://news.ycombinator.com/item?id=50432661

## AI security and governance

**Fireworks AI discloses second breach in weeks — model weights and datasets accessed** — Event: customer notifications + security bulletin update Monday Nov 9 (Bloomberg 3:46 PM EST; Fireworks bulletin "Last updated November 9, 2026"; HN 2:26 PM EST).
Fireworks says an intruder reached its development machines on **Oct 24** through a security incident at a third-party cloud provider hosting its R&D machines (Oct 23), ran an unauthorized workload, and accessed **datasets and incomplete model weights for a limited set of accounts**, plus billing information (addresses, invoice/payment history, balance, credit usage — no stored card numbers) and resource metadata. Detection was same-day; no further successful access has been detected since Oct 25; Inference, Training SDK and Nexus (ZDR-by-default products) were unaffected, but self-serve dataset upload/download is disabled pending investigation. Fireworks hired Palo Alto Networks Unit 42 for forensics, and says evidence links the actor to a **financially motivated cybercriminal group selling stolen LLM tokens to illicit routing gateways — "LLMJacking"** — the same threat Google Threat Intelligence/Mandiant documented Nov 2. Bloomberg: Fireworks notified customers of the breach Monday, its second known hack in weeks, at a company with 10,000+ customers including Harvey, Notion and Cursor; two customers told Forbes they've substantially moved off the platform.
*Why it matters:* The inference-cloud trust stack took a direct hit on all three legs at once — customer weights, datasets, and billing — and the LLMJacking motive turns stolen model access into a commodity crime, not targeted espionage. For teams running fine-tunes on hosted platforms, "ZDR by default" is no longer a proxy for "your weights and data are safe"; expect procurement questions and continued churn toward self-hosted open-weight stacks.
- Source: Update on October 2026 security incident — Fireworks AI security bulletin, last updated Nov 9, 2026 — https://fireworks.ai/security/bulletins/october-2026-security-incident-update
- Source: Nvidia-Backed Fireworks AI Notifies Customers of Its Second Hack in Weeks — Bloomberg, Nov 9 3:46 PM EST — https://www.bloomberg.com/news/articles/2026-11-09/nvidia-backed-fireworks-ai-notifies-customers-of-its-second-hack-in-weeks
- Source: Fireworks Cyberattack Exposes Customer Data and AI Models As Fallout Widens — Forbes (Anna Tong), Nov 9 — https://www.forbes.com/sites/annatong/2026/11/09/fireworks-cyberattack-exposes-customer-data-and-ai-models-as-fallout-widens/

**OpenAI publishes Control Room: fail-closed kill switch stops research workloads in 35 seconds** — Event: openai.com post Monday Nov 9 (news index card "Safety Nov 9, 2026"; indexed by multiple outlets by ~4:45 PM EST).
Control Room is a central service that tracks every research inference workload (RL sampling, agentic evaluation, data processing) by job_id; inference engines must get authorization from Control Room before fulfilling each sampling request, so a stop decision makes all further output impossible **within a 35-second cap** (a caching-layer lag OpenAI wants to drive to zero). It **fails closed** — if Control Room goes down or is tampered with, all research inference is denied — requires an approved safety case for every agentic-inference workload, integrates with the misalignment monitors that caught the September DNS-escape incident, and runs continual sweeps for rogue engines that skip the check. Rollout is complete for frontier-model research workloads and finishes across remaining research workloads "over the next few days."
*Why it matters:* This is the concrete kill-switch answer to the S-1-era disclosures about models accessing third-party systems in evals — the operational control Anthropic's filings said the industry lacked. Coverage contrasts it with the September sandbox escape that reportedly took ~2.5 hours to halt. Expect "35-second, fail-closed" to become a benchmark demand in AI safety cases and enterprise procurement.
- Source: Control Room: A central system to track and stop model workloads — OpenAI, Nov 9, 2026 — https://openai.com/index/control-room/
- Source: OpenAI builds a control room that can halt model output in 35 seconds — Crypto Briefing, Nov 9 ~3:30 PM EST — https://cryptobriefing.com/openai-control-room-halts-model-35-seconds/

## Also noted

- **Googlebook on-device AI RAM frees 3–6GB**: Google's laptop update uses on-device AI optimizations to free 3–6GB of RAM, cuts Chrome crashes, and patches ~60 Android flaws (Techmeme/Thurrott this afternoon; not fetched in depth).
- **Nvidia board probe sub-developments (ledger megaspeed-alice-huang cluster)**: fresh reporting today on Jensen Huang hiring Heller crisis-communications firm as early as Oct 2025 and Madison Huang cancelling a Seoul appearance — incremental color on an already-reported event.

## Just missed the window

- **SpaceX's ~$24B Starbase gas-fired plant + "super-compute facility"** — the City of Starbase public-hearing notice dates to **Nov 6**; Reuters/Bloomberg coverage landed ~3:00–4:00 PM EST today (444-acre tract, initial phases, tax-incentive consideration). Event predates the window. https://www.reuters.com/legal/transactional/spacex-plans-24-billion-gas-power-plant-data-center-project-texas-2026-11-09/
- **OpenRouter's rebuilt Auto Router + new Auto Code** — blog post dated **Nov 6** (up to 4x better than the prior router, routing step 3x faster at p90; Auto Code "in line with Claude Opus 5.5 at 74% lower cost per task"); surfaced on HN today 4:17 PM EST. https://openrouter.ai/blog/announcements/auto-and-auto-code-routers/
- **Mavria: $130M raised at a $1B valuation (Paradigm-led) for truck-shippable, factory-built modular data centers** — announcement posts date to late Sunday/early Monday morning (mavria.com/introducing indexed ~8:15 AM EST; DatacenterDynamics Nov 8 evening); Terrain 01 in production in Houston. https://www.datacenterdynamics.com/en/news/former-us-doe-staffers-launch-modular-data-center-firm-mavria/

## Coverage notes

- **Verified items this run: 13 reportable entries** (4 full-read leads; 4 verified mid-tier items — V100 build, Arete update, llama.cpp, vLLM; 5 one-liners — Ollama RC, Emma, USB, Googlebook, Nvidia color) — counted per report heading/entry, footnotes excluded. Every included URL was fetched; leading stories read in full (Anthropic blog ~26k chars, Fireworks bulletin, OpenAI Control Room post, Meta blog, TechCrunch, Tom's Hardware).
- **Ledger dedupe: 11 candidates suppressed as already reported**, incl. Google TPU 8tx/8i Max, EU Digital Fairness Act draft, Meta AI age-estimation expansion, Disney×Adobe Firefly Foundry, Nominal $240M, H100 rental-spread HN item, TimeScroll Show HN, the Nvidia/Huang saga cluster (4+ sub-reveals today across board probe/Heller/Madison Huang), Trump 60 Minutes extra anecdotes, Amsterdam DC arrest tallies, and the DOJ $129M AI voice-clone re-coverage. Three "Just missed" footnotes added, all first-time (no repeat footnotes).
- **Non-event checked:** Qwen 4 did not ship today despite the community's Monday-Nov-9 rumor (qwen.ai/blog unchanged; no catalogs listing it). Not reportable.
- **Dead ends / limitations:** qwen.ai/blog renders empty via extractor (JS shell); HF "newest models" list renders without names (skipped — no open-weight release surfaced via other lanes); r/LocalLLaMA anonymous feed returned titles + excerpts only; Techmeme river items resolved via exact-headline searches; Bloomberg/Forbes paywalled but corroborated by the fetched primary bulletin. No index was fully down.
- **Phase budget:** discovery ~17:00–17:20, triage ~17:21–17:24, verify through ~17:30, writing from ~17:32 — within the skill's walls.
