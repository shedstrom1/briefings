# AI News Briefing — Tuesday, Nov 10, 2026 (17:00 edition)

**Window:** 2026-11-10 12:00 → 19:28 EST (previous completed fire at 12:00 → now; this run serves as the failed 17:00 edition). Fire time 19:28 EST per now_et.py.

## TL;DR

- **Apple preps "Harbor"** — a Claris-branded Mac app (private preview) that turns any Apple-silicon Mac into a private inference server for open-weight models, with Thunderbolt 5 multi-Mac clustering that pools unified memory; no command line required.
- **Anthropic ships Claude Sheets, Meetings and Whiteboards** at a San Francisco event — spreadsheets, a Google Meet participant-agent, and collaborative whiteboards, plus Claude Data GA and full-app plugins for Adobe, Atlassian and HubSpot.
- **OpenAI launches "Businesses in ChatGPT"** beta — verified business profiles, Business Hub mention analytics, and sponsorable Business Agents for US ecommerce, atop a $1B+ ads run rate.
- **AMD, Supermicro and Spectro Cloud launched the Instinct Coder 355** at KubeCon NA — an on-prem AI coding appliance (8× MI355X, 288 GB HBM3e each) running GLM-5.3 locally for ~50 concurrent developers, claiming 75% lower TCO vs cloud frontier models.
- **Point72's Cubist sued a fired quant analyst** alleging he used Claude and Codex to exfiltrate proprietary source code — a first-of-its-kind agent-tools insider-theft suit.

## Models and local inference

**Apple in private preview of "Harbor" — a zero-setup local inference server for Macs** (event date: Nov 10, 2026)
Leaker Aaron Perris found evidence of a new Mac app called Harbor, in private preview under Apple's Claris brand, that "turns your Mac into a private AI inference server" for open-weight models on Apple silicon — data never leaves the hardware. Harbor ships an in-app model browser/downloader and, notably, clusters two or more Thunderbolt-5-capable Macs into a single inference resource, pooling unified memory so models larger than any one machine's capacity can run, or concurrency scales across the cluster. Setup is advertised as "plug in, start, and you're running — no manual configuration, no command line."
*Local inference:* This is the story — Mac unified memory + TB5 pooling is a legitimate multi-node local serving architecture; watch for public launch and license terms for model hosting.
Source: "Apple has new Mac app coming to make running local AI models easy," 9to5Mac, Ryan Christoffel, Nov 10 2026, 11:15 am PT (14:15 EST) — https://9to5mac.com/2026-11-10/apple-has-new-mac-app-coming-to-make-running-local-ai-models-easy/
Source (community reception, front page): Hacker News item 50446520, Nov 10 2026 (~21:2x UTC) — https://news.ycombinator.com/item?id=50446520

**AMD Instinct Coder 355: turnkey on-prem AI coding appliance runs GLM-5.3 for 50 devs** (event date: Nov 10, 2026, KubeCon NA)
Spectro Cloud and AMD launched the AMD Instinct Coder 355 at KubeCon North America: a Dell PowerEdge XE9785 with eight Instinct MI355X GPUs (288 GB HBM3e, 8 TB/s each), dual EPYC 9575F and 3 TB DDR5, running open-weight GLM-5.3 locally through Spectro Cloud's PaletteAI Inference Launchpad, with overflow routing to Claude/GPT/Gemini. One box is claimed to serve ~50 concurrent developers at up to 75% lower token cost than cloud frontier models and ~98% of frontier benchmark performance — vendor figures, no third-party workload published. Orders run through Spectro Cloud and resellers.
*Why it matters:* This is the strongest enterprise "local-first coding" appliance yet, and its economics pitch (open-weight model doing most work on-prem, frontier only as fallback) mirrors what self-hosted GLM shops like Revolut already claim.
Source: "Spectro Cloud and AMD launch Instinct Coder 355 on Dell PowerEdge," AI Weekly alert (orig. amd.com), published Nov 10 2026 22:59 UTC (17:59 EST) — https://aiweekly.co/alerts/amd-supermicro-and-spectro-cloud-launch-instinct-coder-355-an-on-prem-ai-coding

**Inference-engine ticker (Nov 10 12:00–19:28 EST window):**
- **vLLM** merged 16 in-window commits: Kimi-K3 mono decode layer on MI355X (#61498), Kimi-K3.1-Pro GB300 AgentX presets aligned to InferenceX (#66642), MiniMax-M3 BLHNC KV layout with NVFP4 KV (#66892), a security bugfix eliminating quadratic attribute scans in Muse tool-call extraction (#62345), GLM-5.3 MTP eh_proj low-latency GEMM plan (#65704), MRV2 prefill_context_kv_only for P/D (#65217), plus Rust-frontend SSE/cancellation fixes.
- **Ollama v0.40.17-rc0** (17:25 EST): mmap for Vulkan integrated GPUs (#19640), MLX concurrent generation (#19197), llama.cpp bump to b12257 — the Vulkan mmap change matters for iGPU-heavy local rigs.
Source: GitHub API — https://github.com/vllm-project/vllm/commits/main ; https://github.com/ggml-org/llama.cpp/commits/master ; https://github.com/ollama/ollama/releases/tag/v0.40.17-rc0

**Community corner: XeMaya — Intel Arc-native inference for DeepSeek V4.1 / GLM 5.3** (posted Nov 10, r/LocalLLaMA)
A XeStrata fork built (by the author, with Fable 5.2's help — post flagged as AI-assisted in comments) to run newer MoE models natively on Intel Arc via SYCL/Level Zero. Author's numbers on B70 32GB + B65 32GB + B60 24GB: GLM-5.3 Flash IQ2_XXS prefill/generate 152–161/17.4 tok/s vs stock llama.cpp SYCL 68.3/13.6; DeepSeek-V4.1-Flash-Coder 224.8/21.7 after optimization; two GPUs beat three due to chipset-link speed. Aggressive IQ2 quants; independent verification pending.
Source: Reddit permalink, Nov 10 2026 — https://www.reddit.com/r/LocalLLaMA/comments/1xt2mjk/xemaya_an_intel_arc_inference_engine_to_run/

**Also noted (Models and local inference):**
- **CoreWeave Q3:** revenue $3.66B (+168% y/y, beat $3.55B consensus), net loss $709M, backlog up to $132.5B, FY26 guide raised to $13.0–13.3B; CFO says no further parent-level unsecured debt "at current yields" through 2027; stock up after hours amid a -40% month for the neocloud trade. (CNBC, Jordan Novet, Nov 10 4:19 PM EST — https://www.cnbc.com/2026-11-10/coreweave-crwv-q3-earnings-report-2026.html)
- **Rackspace × Together AI:** multiyear ~$380M initial agreement for Rackspace to operate dedicated NVIDIA GB300 NVL72 + Spectrum-X for Together's managed inference, 2027 deployment, subject to GPU financing. (Rackspace IR, Nov 10 2026 — https://ir.rackspace.com/news-releases/news-release-details/rackspace-and-together-ai-announce-strategic-agreement-support)
- **OpenAI told investors Tuesday** Q3 revenue hit ~$10B, up ~50% QoQ (vs ~17% in Q2), with business sales overtaking consumer for the first time, per The Information (via AI Weekly alert, Nov 10 21:42 UTC — https://aiweekly.co/alerts/the-information-openai-tells-investors-q3-revenue-hit-roughly-10b-up-50-from-q2)

## Agent tools

**Anthropic launches Claude Sheets, Meetings and Whiteboards; Claude Data GA; full-app plugins** (event date: Nov 10, 2026)
At a San Francisco event, Anthropic unveiled Claude Sheets (beta, all paid plans; tabs, formulas, collaboration with Claude's edits tracked, Excel export, Google Workspace plugin support), Claude Meetings (Team/Enterprise; joins Google Meet calls as a marked participant, takes notes, starts work mid-call, leaves if outside guests join without admin consent), and Claude Whiteboards (beta, all paid users; groups ideas by theme, drafts per-person action items, integrates with Meetings). Claude Dashboards was renamed Claude Data and rolled to all paid users with CRM connectors; plugin capability now opens full apps inside Claude, starting with Adobe, Atlassian and HubSpot partners.
*Why it matters:* Anthropic is assembling an Office/Workspace-shaped suite on top of Claude — competing directly with Microsoft and Google productivity surfaces while its IPO roadshow runs; the "agent joins your call as a named participant" pattern also raises the governance bar other vendors will be asked to match.
Source: "Anthropic takes on Google and Microsoft with Claude Sheets," Engadget, Igor Bonifacic, Nov 10, 2026 12:30 pm EST — https://www.engadget.com/2312177/anthropic-takes-on-google-and-microsoft-with-claude-sheets/
Source (live alert): AI Weekly, Nov 10 2026 — https://aiweekly.co/ai-news-today

**OpenAI launches "Businesses in ChatGPT": verified profiles + sponsorable Business Agents** (event date: Nov 10, 2026, "Tuesday")
Businesses can claim verified profiles (blue check) in ChatGPT, update details/prices/stock, and see mention/click-through analytics via Business Hub; eligible US ecommerce and online-service firms can launch Business Agents that chat with users from product data, labelled and separated from ChatGPT's own answers — and the same agent can be sold as a ChatGPT Ads sponsorship, building on Sponsored Agents tested since September. OpenAI says paying doesn't change organic recommendation frequency (per Digiday); agents must disclose they're AI, keep prices accurate, and purchases/personal-data sharing need explicit approval; checkout goes to brand sites while agent payments are built with Visa.
*Why it matters:* This converts ChatGPT's 1.2B weekly users into a merchant surface with a measurement layer — squeezing third-party "AI visibility" startups (Peec AI) and deepening the ads business past $1B ARR/100,000 advertisers. Agent-mediated commerce now has its first at-scale profile+agent+ads bundle.
Source: "OpenAI lets businesses claim ChatGPT profiles and launch their own agents," TNW, Nov 10 2026 — https://thenextweb.com/news/openai-businesses-in-chatgpt-business-agents

**OpenAI acquires Odessia, the Sequoia-backed AI travel agent; hires Sonder founder Francis Davidson** (event date: Nov 10, 2026)
Davidson announced Tuesday that OpenAI acquired the year-old, IATA-accredited AI travel agency (which handled $60M+ in bookings during its summer preview after a $6M pre-seed) on undisclosed terms in an apparent acqui-hire; the standalone product is already shut down, and an OpenAI spokesperson confirmed the hire while declining to share travel plans. OpenAI already counts Booking.com, Expedia and KAYAK as customers/partners.
*Why it matters:* Travel is a top assistant use case and the natural proving ground for agentic booking + payments; OpenAI is buying the credential (IATA accreditation) rather than partnering for it — a signal it wants the transaction, not just the referral.
Source: "OpenAI acquires Odessia, travel startup created by Sonder founder," TechCrunch, Julie Bort, Nov 10 2026 (afternoon ET) — https://techcrunch.com/2026-11-10/openai-acquires-odessia-travel-startup-created-by-sonder-founder/

**Also noted (Agent tools):**
- **ChatGPT voice sunset:** OpenAI began notifying users Nov 10 that Standard and Advanced Voice modes retire Dec 11 in favor of GPT-Live (usage "nearly doubled" since July; live video/screen-share stay on Advanced until GPT-Live catches up). (AI Weekly live river, updated Nov 10 19:02 EST — https://aiweekly.co/ai-news-today)
- **Graphon Labs raised a $35M Series A** led by RRE (HP, Energy Impact Partners, Hitachi Ventures, Gaia VC; total $43.3M) for graph-based long-term agent memory; claims 93.9% LoCoMo / 81.7% ATM-Bench; customers include HP, Hitachi, the 49ers. (AI Weekly alert, pub. Nov 10 21:42 UTC, orig. TNW — https://aiweekly.co/alerts/graphon-labs-raises-35m-series-a-from-rre-ventures-to-give-ai-agents-long-term)

## AI security and governance

**Point72's Cubist sues fired lead analyst over allegedly stealing source code with Claude and Codex** (event date: Nov 10, 2026)
Cubist Systematic Strategies filed in Connecticut state court (Stamford) accusing ex-lead research analyst Xin Jin, terminated in September, of using Anthropic's Claude and OpenAI's Codex to exfiltrate proprietary source code before leaving for a competitor — "attempted to steal (and likely did steal)" per the complaint. The rival firm and damages aren't named. The suit lands weeks after Goldman Sachs pulled Claude Code and GitHub Copilot from traders/bankers, confining them to ~12,000 engineers.
*Why it matters:* First notable litigation in which agentic coding assistants are the alleged exfiltration instrument — expect compliance questions about prompt-retained IP, model-provider data terms, and enterprise DLP for agent output. For security teams this is the insider-threat case updated for the agent era.
Source: "Cubist Sues Fired Analyst for Using Claude, Codex to Steal Code," AI Weekly alert (orig. Bloomberg), pub. Nov 10 2026 21:42 UTC (16:42 EST) — https://aiweekly.co/alerts/cubist-sues-fired-analyst-for-using-claude-codex-to-steal-code

## Just missed the window (footnotes)

- **Google Chrome ships Modern Web Guidance 1.0** (blog published Nov 10, pre-12:00 EST; HN discussion from ~morning): stable release of the cross-browser skill pack (250+ features, 203 use cases, now stewarded with Edge and Firefox teams) that steers coding agents off ~2019-era JavaScript; installs via `npx modern-web-guidance@latest install` into Claude Code, Codex, Cursor, Antigravity, Copilot, WebStorm, Kimi Code and Grok. — https://developer.chrome.com/blog/modern-web-guidance-v1
- **404 Media (Nov 10, 9:41 am EST): "Come and Take It"** — Anthropic and OpenAI have tightened guardrails against AI-assisted reverse engineering/decompilation; Claude Code refuses many decompile prompts, an OpenAI account was banned for "cyber abuse" after Codex-assisted Samsung TV RE (user log4j), and Decomp.wtf shut down; OpenAI's Tarun Gogineni says bans "should not happen" and calls most restrictions accidental. Update lineage on ledger's claude-game-clone-block. — https://www.404media.co/come-and-take-it/
- **Anthropic's Nov 10 S-1/A (reported 07:22 EST)** — the $34.9B August-2026 Lambda tranche tied to Hut 8's Beacon Point, TX (350 MW, Nueces County) is now terminable by either party after Lambda missed a lease deadline (Anthropic says no plans to terminate); same filing breaks out ~$10B non-voting Class N stock each for Nvidia and Google and ~$10B new Microsoft/Google cloud rentals through 2033. — https://cryptobriefing.com/anthropic-lambda-compute-deal-terminable-lease/
- **NVIDIA unveiled GR00T 2** (blogs.nvidia.com, Nov 9 ~9 pm ET — slipped past the 07:00 fire): 2B-parameter VLA foundation model on Cosmos-Predict2.5, 71% one-shot across 50 robotics tasks at CoRL; Isaac Lab 3.0 (2.5x faster training) + DexBench/VABAR released; GR00T 2 weights and dataset arrive December. — https://blogs.nvidia.com/blog/robotics-simulation-advancements-corl/

## Coverage notes

- **Verified count: 14 in-window items** (6 leads + 8 also-noted) across ≥8 distinct outlets (9to5Mac, AI Weekly, Engadget, TNW, TechCrunch, CNBC, Rackspace IR, GitHub API, Reddit, plus cryptobriefing/Chrome-blog footnotes), plus 4 "just missed" footnotes (not counted).
- **Ledger dedupe:** ~19 candidates suppressed as already reported this week, including repeat footnoted events (LongCat-2.5 MIT weights, MiniMax H3-Image, Megaspeed/Alice Huang cascade, Fireworks LLMJacking bulletin update, MKA benchmark, Gemini 4 Carbon GA, Anthropic roadshow update, AlphaProof Code, Australia AI regulator + criminal-charges follow-on, Mercor, Ring Robotics, Kora OS, TSMC $1B, Reddit Reviews hub, RLI Fable 5.2, Moonshot B300, Amazon Strands Decider).
- **Dead ends / limitations:** FT "US regulator postpones AI computing futures" (posted 19:19 EST, paywalled, not retrievable — excluded); UK Computer Weekly model-agnostic piece was Nov 9 (out of window, not footnoted as stale); Bielik-11B-v3.1 release date unverified in remaining budget — excluded; Anthropic newsroom did not surface a fetchable Claude Sheets article URL — Engadget is the sole primary read; Reddit anonymous feed returns no scores/timestamps (XeMaya timed by "new" listing position). DuckDuckGo backend failed on one query (paid fallback served). Provider flapped earlier today; research stopped per adaptation rule at the verify wall.
