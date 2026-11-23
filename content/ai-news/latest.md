# AI News Briefing — Monday, Nov 23, 2026 (12:00–17:00 ET)

Window: 2026-11-23 12:00 ET → 17:00 ET (5h, previous 12:00 fire). Fire: 2026-11-23 17:00 ET.

## TL;DR
- Amazon agreed to buy Israeli startup Decart's AI optimization stack (DOS) plus founders/team for a reported $7.5B — part of its effort to turn Trainium into a third-party chip competitor to Nvidia.
- The Trump administration added Megaspeed — the Singapore-based Nvidia customer at the heart of the Huang scandal — to the US trade blacklist (Entity List).
- Virginia and Oregon sued SpaceX over Grok's generation of sexualized images, including child depictions, in complaints filed Monday morning.
- llama.cpp accumulated in-window commits including Metal NVFP4 support (#32825), a prompt-cache-restore fix (#32828) and an auto quant-selector (#15550); no vLLM/SGLang/Ollama releases.

## Models and local inference

### Amazon snaps up Decart ($7.5B) for its inference stack
Amazon signed an agreement to purchase the technology behind Decart (DOS) — software that helps AI models run efficiently across Nvidia, Amazon and Google hardware — along with the Decart founders and research team, per the company blog and Bloomberg via TechCrunch. Founded in 2023 (Israel), Decart is known for real-time world models/Oasis 3 and AI video tools; its per-chip efficiency gains matter as Amazon prepares to sell Trainium chips to third-party customers. Completion is subject to customary closing conditions including regulatory clearance.
- Event date: Nov 23, 2026. Source: [Amazon blog](https://www.aboutamazon.com/news/company-news/amazon-decart-ai-optimization) (Nov 23, 2026) · [TechCrunch](https://techcrunch.com/2026/11/23/amazon-snaps-up-decarts-ai-optimization-tech-for-7-5b-as-it-prepares-to-sell-trainium-chips/) (Nov 23, ~09:14 PST; Bloomberg $7.5B figure; via GeekWire).
- Why it matters: Anthropic/OpenAI-scale workloads are increasingly priced on tokens-per-GPU-hour.

### llama.cpp: Metal NVFP4, quant auto-selection, prompt-cache fix
In-window commits (via gh api, 2026-11-23 17:00–21:10 UTC): Metal gained NVFP4 support (#32825, 2026-11-23T19:02Z) and batched tensor-concat/copy (#32824); ggml added a compact-bias fast path for FLASH_ATTN_EXT (#32740); quantize now supports automatic selection of optimal quant types to hit a target file/bpw size (#15550, 2026-11-23T17:06Z); server now preserves a prompt-cache entry until restores succeed (#32828, 2026-11-23T20:50Z); OpenCL fetch of the Adreno binary kernel library at configure time for Windows on Snapdragon (#31759, 2026-11-23T20:39Z); the UI removes the Matte theme (#32906). No new build tag this window (latest tag v1.0.0, Nov 13).
- Why it matters: Metal NVFP4 finally extends NVIDIA-style micro-scaling to Apple Silicon, and the auto quant selector gives CPU-side users a target-size knob instead of hand-picking K/Q strings — both relevant to consumer-GPU local inference.
- Sources: [ggml-org/llama.cpp commits](https://github.com/ggml-org/llama.cpp/commits/master/?since=2026-11-23T22:00:00Z) (in-window); latest tag check 2026-11-23.

## Agent tools

### No agent-framework releases in window
OpenClaw v2026.11.7 (published Nov 23 ~01:33 UTC) was already reported by the 07:00 fire; no other agent-framework versioned release, PR or CLI build landed in 12:00–17:00 ET. vLLM's in-window commits (e.g. fused all-reduce+mHC+RMSNorm on Hopper, +8.7% e2e for GLM-5.3-Flash, #69001; HF3FS pipeline-parallel fix #67301) are operational, not newsworthy releases; SGLang has a single DFLASH kernel_plugins commit (#52495); Ollama quiet.
- Why it matters: The agent-tooling layer is quiet this window — the week's movement is happening in inference/serving code paths, not in agent-framework releases.

## AI security and governance

### Megaspeed blacklisted (update on Nvidia/Huang scandal)
The Trump administration added Megaspeed — the Singapore-based AI-chip data-center operator at the center of the Nvidia/Huang affair — to the US Commerce Department Entity List, barring it from buying advanced semiconductors without a license. Megaspeed, accused of transferring Nvidia chips to China (denies), was already on Nvidia's internal "no sell" list since June 2025; the Justice/Commerce departments confirmed that last week. The blacklist came within hours of the board's Monday reinstatement of Huang (+33% that session, ~$1T recovered).
- Event date: Nov 23, 2026 (~1 hour ago as of 17:00 ET). Sources: [CBS News](https://www.cbsnews.com/news/megaspeed-trade-blacklist-nvidia-jensen-huang/) (Nov 23) · [Semafor](https://www.semafor.com/article/11/23/2026/commerce-to-add-megaspeed-to-trade-blacklist) (Nov 23) · [Reuters](https://www.reuters.com/business/us-blacklist-megaspeed-chip-firm-linked-nvidia-scandal-source-says-2026-11-23/) (Nov 23).
- Why it matters: Publicly confirming the China-customer line after the board capitulated to Huang shows the administration separating "national security" from "business as usual" — the same line the board re-affirmed.

### Virginia and Oregon sue SpaceX/Grok over sexualized images
Oregon Attorney General Dan Rayfield and Virginia Attorney General Jay Jones sued SpaceX — alleging it failed to deploy safeguards allowing Grok to generate and share sexualized images of real people, including children, and that it misled users about content safety — in separate complaints filed Sunday evening/Monday morning (Multnomah County Circuit Court and Richmond Circuit Court). The suits seek civil penalties, injunctions and disgorgement. SpaceXSI's public response: standard "we take action on abusive accounts" language; at an Oct. NYC Council hearing a company policy leader had said "safeguards" were in place.
- Event date: Nov 23, 2026. Sources: [CNBC](https://www.cnbc.com/2026/11/23/oregon-virginia-spacex-grok-deepfake-lawsuits.html) (Nov 23, 07:40 EST) · [The Hill](https://thehill.com/regulation/court-battles/6237551-virginia-oregon-lawsuit-spacex-ai/) (Nov 23) · [NYT](https://www.nytimes.com/2026/11/23/technology/spacex-lawsuit-ai-sexualized-images.html) (Nov 23).
- Why it matters: First state-level suits targeting Grok's image generator specifically, and they frame the harm as a false-advertising/deception problem rather than a platform-liability problem — a legal path that extends beyond Section 230 waivers.

### Nov 23 AI-advisory batch (low-severity)
The Axis Intelligence tracker lists four advisories published today: CVE-2026-135287 (LiteLLM, persistent global callback injection, 5.4), CVE-2026-134492 (mcp-server-fetch, IPv6 site-local bypass of SSRF guard, 2.3), CVE-2026-134465 (mcp-server-git, nested gitdir bypass of --repository restriction, 5.8), and CVE-2026-133632 (astro-airflow-mcp, uncontrolled search path element, 7.8).
- Source: [Axis Intelligence AI vulnerability tracker](https://axis-intelligence.com/research/ai-model-vulnerability-tracker/) (updated 2026-11-23). LiteLLM and MCP-server ecosystems are where the active attack surface is — of particular note to anyone running MCP servers.

## Also noted
- llama.cpp: server now preserves prompt-cache entries across restores (#32828); automatic quant-type selection to fit a target bpw (#15550); Metal NVFP4 (#32825).
- Amazon's Decart deal is the largest in-window AI acquisition on policy-adjacent grounds: $7.5B for "getting more out of every chip" (DeSantis quote).
- Stale/pre-window (not reported): Perplexity "Self-improving skills" blog (Nov 18); MAI Base 2 pre-training report (Oct 2026, stale); Alpoge Lean RH/GRH claim (Nov 20, already reported); Huang reinstatement + Applied Digital 150MW lease + SenseNova-Loop-9B-DFlash2 + agentic evals guide (all 12:00 fire, not re-reported).

## Coverage notes
- Verified: 5 (Amazon Decart, Megaspeed blacklist, VA/OR Grok suits, llama.cpp in-window commits, Nov 23 AI CVEs). All other candidates suppressed as already-reported in prior fires or stale-date (MAI Base 2, Perplexity skills, Alpöge RH). No in-window closed-weight model releases, no agent-framework releases, no Ollama/SGLang/vLLM release events, no AI security breaches announced in 12:00–17:00 ET Monday.
- Candidates suppressed as already-reported (per ledger): nvidia-huang-reinstated (12:00), applied-digital-150mw-18yr (12:00), sensenova-loop-9b-dflash2 (12:00), agentic-evals-guide (12:00), engines-1123 (12:00), GLM-5.4 (07:00), perplexity-and-intelligence/Pachocki (07:00), openclaw-v2026.11.7 (07:00), llama-cpp-b12647-b12651 (07:00), and the Alpöge/Lean RH item (Nov 20). Polymarket "suspicious clusters" WSJ item not verified — not reported.
- Dead ends: web_search backend repeatedly failed (ddgs: No results found) for Anthropic/OpenAI-specific queries; WSJ livecoverage cards failed to fetch (wall.auth); Reuters blacklist article failed to fetch (wall.auth) — Reuters credited via CBS/Semafor instead.
- Window: only 5 hours (previous 12:00 fire); items listed reflect the full 12:00–17:00 ET window.
