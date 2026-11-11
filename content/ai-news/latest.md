# AI News Briefing — Nov 11: AlphaGenome 2, Helsing Clarity, ByteDance Muse-style agent, Anthropic IPO oversubscribed

**Window:** Wednesday, November 11, 2026 — 07:00 to 12:00 ET (previous fire 07:00 ET; new items only)

## TL;DR

- **Google DeepMind unveiled AlphaGenome 2**, a cell-type-aware successor to AlphaGenome that roughly doubles the number of gene-expression-altering variants it can identify — available today for non-commercial research via API.
- **doubleAI says its Maxwell code-execution model rewrote DeepSeek-V4.1-Flash serving on B200s to ~3x vLLM's peak AgentX throughput**, beating every comparable published GB300 setup and announcing it will serve Maxwell-optimized models on OpenRouter.
- **Helsing launched Clarity**, a European sovereign "agentic intelligence platform" rivaling Palantir Maven, while **ByteDance plans a Muse-style AI assistant within weeks** (with Zhang Yiming putting frontier parity at 2029–2030).

---

## Models and local inference

**Google DeepMind releases AlphaGenome 2 for cell-type-specific variant prediction** — event date Nov 11, 2026
DeepMind's new genome model predicts the effects of genetic variants on individual cell types and states, trained on more than 100 million single-cell profiles across 68 tissues. Google says it identifies nearly twice as many gene-expression-altering variants as its previous state-of-the-art model, can predict for cell types never seen in training without retraining, and can flag gaps in experimental data to guide wet-lab work. It ships via the AlphaGenome API for non-commercial research starting today, with a limited "private preview" for commercial research; its higher compute cost means it does not replace AlphaGenome for genome-wide scans like September's 1B-variant Atlas.
*Why it matters:* DeepMind is extending the AlphaFold playbook — publish a scientific workhorse behind a gated API while rivals (OpenAI with math, Anthropic with drug design) chase the same "AI for science" territory. Cell-type granularity is the long-standing weak point of variant-effect models, and the zero-shot-to-new-cell-types result is the piece that changes research workflows.
Source: "Google's AlphaGenome 2 zooms in on the cellular roots of disease," The Verge, Nov 11 2026 10:00 ET — https://www.theverge.com/ai-artificial-intelligence/1031524/google-deepmind-alphagenome-2-genetic-ai-model
Source (secondary): "Google DeepMind's AlphaGenome 2 Maps DNA Changes Across Cell Types," TechBuzz, Nov 11 2026 — https://www.techbuzz.ai/articles/google-deepmind-s-alphagenome-2-maps-dna-changes-across-cell-types

**doubleAI: Maxwell-optimized DeepSeek-V4.1-Flash serving on B200s claimed faster than published GB300 setups** — event date Nov 11, 2026 (update on previously reported "doubleAI launches Maxwell", ledger 2026-10-19)
In a new research post, doubleAI says it pointed its code-execution model Maxwell at the entire LLM serving stack for DeepSeek-V4.1-Flash on NVIDIA B200s and got the fastest known end-to-end B200 serving on SemiAnalysis's AgentX benchmark (which replays real Claude Code sessions): ~3x vLLM's peak throughput, ~2x per-user speedup over vLLM above 100 concurrent sessions, p90 time-to-first-token ~0.2s at 128 sessions, and ~5,000 tokens/s/GPU at 1,024 sessions where published vLLM configs crash at 512+. doubleAI claims the single engine stays within ~10% of its roofline estimate across all loads by adapting topology, kernels, and scheduling at runtime, and says Maxwell-optimized models are "coming to OpenRouter." All figures are vendor-published and unaudited; the comparison to GB300 covers only InferenceX runs on up to 4 GPUs.
*Why it matters:* Agentic serving — end-to-end turn latency under concurrency, not isolated decode tok/s — is becoming the metric that matters for agent workloads, and a code-execution model rewriting a full serving stack is a live demo of "AI optimizes AI infrastructure." The OpenRouter plan would make the engine testable by outsiders.
Source: "GB300 speed on B200 hardware," doubleAI Research, Nov 11 2026 — https://www.doubleai.com/research/gb300-speed-on-b200-hardware

**KRAFTON releases Raon-1 nano: Apache-2.0 hybrid linear-attention model (3.1B total / 1.5B effective), 262K context** — event date Nov 11, 2026
The game-studio-turned-AI-lab posted Raon-1 nano to Hugging Face: a dense model derived from the Raon-1 mini preview (30B-A4B MoE) via MoE-to-dense pruning plus distillation, with 18 gated delta-rule "Raon Delta Attention" layers + 6 gated-MLA layers (only the MLA layers keep KV cache, ~7 KiB/token BF16), per-layer embeddings, an MTP head, NoPE positions, and native 262,144-token context. Trained on 3T continued-pretraining tokens for Korean and English instruction following, conversation, and tool calling, it ships GGUF, MLX, and LiteRT-LM formats for on-device use under Apache 2.0.
*Why it matters:* It's a clean showcase of the linear-attention/KV-shrinking recipe for agent-capable small models, and one of the few Korean-English tool-calling models with a permissive license — plus the MTP head makes it a natural spec-decode drafter candidate.
Source: "KRAFTON/Raon-1-nano," Hugging Face model card, Nov 11 2026 — https://huggingface.co/KRAFTON/Raon-1-nano
Source (community): r/LocalLLaMA announcement thread, Nov 11 2026 — https://www.reddit.com/r/LocalLLaMA/comments/1xtmbkn/kraftonraon1nano_hugging_face/

**Unsloth ships Dynamic V3.1 GGUFs for Qwen3.8-27B (and updates DeepSeek-V4.1-Flash-1017, Qwen3.8-Flash-Next)** — event date Nov 11, 2026
Unsloth updated its flagship quant repos with its Dynamic V3.1 algorithm (first previewed Nov 4 on Qwen3.8-Flash-Next), claiming 5–10% higher average accuracy than other frontier quants of comparable size on DeepSWE, Terminal-Bench 2.1, and LiveCodeBench versus Unsloth Dynamic 3.0, GSQ-RCO, and Exlama-3, with 2–6-bit quants all refreshed and "better algorithms planned for Qwen4." The 27B is the community's workhorse dense model on 16–24GB cards; Unsloth also pushed its desktop app with thinking toggles for Mac/Windows/Linux. Claims are vendor benchmarks.
*Why it matters:* For the sub-24GB local scene, quant quality — not new checkpoints — is where most capability gains land between model launches; a 5–10% agentic-benchmark lift at 4-bit on the most-run open model is a material practical upgrade.
Source: "unsloth/Qwen3.8-27B-GGUF" release note ("Nov 11 update"), Hugging Face — https://huggingface.co/unsloth/Qwen3.8-27B-GGUF
Source (docs): "Unsloth Dynamic 3.0 GGUFs" (V3.1 description), unsloth.ai, Nov 2026 — https://unsloth.ai/docs/basics/dynamic-3.0-ggufs

**Engine watch (07:00–12:00 ET):** llama.cpp merged 6 commits — CUDA-graphs re-instantiation on kernel cluster-dims change (#31284), GDN non-fused graph-node removal (#32078), TTS custom text-normalizer via jinja (#32053), cpp-httplib 0.66.0, Windows SYCL MKL runtime DLL packaging (#32005). vLLM merged 16 PRs in-window, headlined by a new ROCm attention backend for gfx1151 (RDNA3.5 desktop/iGPU), DSpark sliding-window MLA drafts under DCP (#63796), GLM-5.3-Flash block-FP8 MoE tuning for H20, and an AutoGPTQ fix keeping unquantized routed experts unquantized. SGLang, Ollama, and OpenClaw had no in-window releases (latest: SGLang v0.5.23, Ollama v0.40.17-rc0, OpenClaw v2026.11.3 — all previously reported).
*Local inference:* The gfx1151 attention backend continues vLLM's push into consumer-RDNA viability; the llama.cpp CUDA-graphs fix targets Blackwell-generation kernel clusters.
Source: GitHub API merges/commits, Nov 11 2026 12:00–17:00 UTC — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged&newline=; https://github.com/ggml-org/llama.cpp/commits/master

**Anthropic gives CNCF maintainers 3,000 Claude Ultra codes at KubeCon** — event date Nov 11, 2026
A dedicated claude.ai/cncf page went live today at KubeCon + CloudNativeCon North America (Salt Lake City, Nov 9–12): CNCF project maintainers can request one of 3,000 Claude Ultra license codes via the CNCF ServiceDesk, alongside Anthropic's five Wednesday/Thursday KubeCon sessions on Kubernetes-at-scale.
*Why it matters:* OSS-maintainer licensing is the new standard open-source goodwill play for frontier labs, timed exactly where the infrastructure audience is.
Source: "Claude for CNCF maintainers," Anthropic/Claude, Nov 11 2026 — https://claude.ai/cncf

## Agent tools

**Helsing launches Clarity, a European sovereign "agentic intelligence platform" to rival Palantir Maven** — event date Nov 11, 2026
The €18B-valued German defense tech company unveiled Clarity today: an AI-native intelligence platform in which hundreds of military-customized agents run in parallel over satellite, drone, sensor, electromagnetic-spectrum and open-source feeds to detect adversary assets and produce courses of action — including long-range strike direction — with a human validating every threat before hand-off to a weapon. Helsing pitches modular open-systems integration, its own infrastructure, and compliant handling of classified data as the sovereign alternative to Maven (which Helsing notes has pulled ~$1.5B in European contracts).
*Why it matters:* This is Europe's most direct attempt yet to replicate Palantir's forward-deployed-software model with agentic AI at the core — and its explicit human-in-the-loop framing arrives while US labs are still digesting agent-containment incidents. Watch contract flow, not the launch.
Source: "Helsing launches Clarity, a competitor to Palantir's Maven," Resilience Media (Ingrid Lunden), Nov 11 2026 — https://resiliencemedia.co/helsing-launches-clarity-a-competitor-to-palantirs-maven/
Source (vendor): "Clarity — Helsing," Helsing product page, Nov 11 2026 — https://helsing.ai/clarity

**ByteDance plans a Muse-style AI agent "within weeks"; Zhang Yiming puts frontier parity at 2029–2030** — event date Nov 11, 2026 (Bloomberg, from an all-hands)
Per Bloomberg, co-founder Zhang Yiming told ByteDance's AI team the company will launch an assistant similar to Meta's Muse (the voice-agent-plus-avatar product on smart glasses and the Muse Charm) in the coming weeks, while cautioning that challenging OpenAI and Anthropic at the frontier realistically takes until 2029 or 2030 — Seed's pretraining data is orders of magnitude smaller than frontier rivals'. Zhang reiterated the lab will not lean on distillation. Seed (~1,000 people under Wu Yonghui) has shipped no top-of-benchmarks model nor open weights; its last major release was Seed 2.1 in June.
*Why it matters:* An admitted 4-year frontier gap from China's largest consumer-AI company is a rare candid datapoint, and a Muse clone would extend the agent-avatar race (Meta, OpenAI's Business Agents) to ByteDance's distribution.
Source: "ByteDance reportedly plans a Muse-like AI agent within weeks," The Next Web, Nov 11 2026 — https://thenextweb.com/news/bytedance-muse-like-ai-agent-zhang-yiming
Source (original): "ByteDance co-founder Zhang Yiming says the company plans to launch a Muse-like AI assistant...," Bloomberg, Nov 11 2026 — https://www.bloomberg.com/news/articles/2026-11-11/ (as posted on the Techmeme river 07:21 ET); also: RuntimeWire — https://runtimewire.com/article/zhang-yiming-bytedance-2030-anthropic-openai-muse-agent

**Rox raises $100M Series B at $1.3B for AI revenue agents** — event date Nov 11, 2026
Fortune reports the San Francisco AI-sales startup raised a $100M Series B co-led by General Catalyst and March Capital (with Sequoia and GV participating) at a $1.3B valuation, scaling autonomous "revenue agents" aimed at Global-2000 sales teams — positioning squarely against Salesforce-era CRM workflows.
Source: "Meet Rox, the $1.3 billion AI sales startup...," Fortune (Morgan Chittum), Nov 11 2026 09:40 ET — https://fortune.com/2026-11-11/rox-series-b-100-million-funding-take-on-salesforce/

## AI security and governance

**Anthropic's IPO reportedly multiple-times oversubscribed; Amodei–Dimon livestream set for Friday** — event date Nov 11, 2026 (update on previously reported "Anthropic public S-1 / roadshow", ledger 2026-10-26 and 2026-11-10)
Bloomberg sources say the roadshow launched Tuesday (825M Class A shares at $70–$78, up to $90.4B including the $26B chipmaker-cornerstone placement) has drawn multiple times the orders for available shares, and Anthropic is planning a livestreamed Friday lunchtime event in New York with Dario Amodei and JPMorgan CEO Jamie Dimon — echoing but scaled down from June's SpaceX/Musk spectacle. JPMorgan is running US retail distribution (roughly 15–20% of shares reserved for retail); pricing and the Nasdaq "ANT" debut are expected before Thanksgiving. Earlier overnight reporting (FT, Nov 10) said bankers weighed lifting the valuation target above $1.5T on demand.
*Why it matters:* Oversubscription at this size validates frontier-lab public-market appetite despite Q3 compute-cost losses, and a Friday Amodei/Dimon moment right before pricing puts maximum spotlight — and scrutiny — on the company's AI-safety and compute-commitment disclosures.
Source: "Anthropic's IPO Is Said to Be Multiple Times Oversubscribed," Bloomberg, Nov 11 2026 (~08:40 ET) — https://www.bloomberg.com/news/articles/2026-11-11/anthropic-s-mega-ipo-is-said-to-be-multiple-times-oversubscribed
Source (secondary): "Anthropic's IPO Order Book Reportedly Booms...," ZeroHedge, Nov 11 2026 — https://www.zerohedge.com/markets/anthropic-bankers-push-higher-ipo-valuation-thiel-capitals-jack-selby-warns-weak-debut
Source (livestream, prior-day detail): "Anthropic Plans IPO Live Stream With Amodei, JPMorgan CEO Dimon," Bloomberg via Yahoo Finance, Nov 10–11 2026 — https://finance.yahoo.com/technology/ai/articles/anthropic-plans-ipo-live-stream-031454541.html

**Torus raises $500M Series C led by Magnetar for containerized AI data centers** — event date Nov 11, 2026
The Salt Lake City energy-storage company raised $500M (ICONIQ, Origin Ventures, Trinity Capital participating) to scale "Foundry": a shipping-container unit packing 576 HGX B300 GPUs into 577 sq ft on 1.2MW, deployable from contract to operational in 12–16 weeks, sized 1–20MW per installation.
*Why it matters:* Grid-queue bypass is now a funded product category — battery-integrated, transportable B300 capacity attacks the single biggest constraint (interconnection time) rather than chip supply.
Source: "Utah-Based Startup Raises $500 Million for Modular AI Data Center," Bloomberg, Nov 11 2026 08:25 ET — https://www.bloomberg.com/news/articles/2026-11-11/utah-based-startup-raises-500-million-for-modular-ai-data-center
Source (secondary): "Torus raises $500M for Foundry container-sized AI data centers," AI Weekly, Nov 11 2026 — https://aiweekly.co/alerts/torus-raises-500m-for-foundry-container-sized-ai-data-centers

**Meta infrastructure chief Santosh Janardhan to step down in early 2027; Dave Brown to take over** — event date Nov 11, 2026
A leaked memo reported by Bloomberg says Meta's head of infrastructure and co-head of engineering plans to leave in early 2027, to be succeeded by Dave Brown, the senior AWS executive who joined Meta this year to lead its data-center build-out (and whom Meta earlier hired as it weighs selling compute, including a possible deal with Anthropic).
*Why it matters:* Janardhan has run Meta's entire AI-infrastructure expansion; his exit, mid-supercycle, lands as Meta reorganizes around superintelligence labs and possible cloud entry.
Source: "Meta's Head of Infrastructure, Engineering to Step Down in 2027," Bloomberg, Nov 11 2026 11:35 ET — https://www.bloomberg.com/news/articles/2026-11-11/meta-s-head-of-infrastructure-engineering-to-step-down-in-2027

---

## Also noted

- **Wesleyan Media Project:** political campaigns spent ~$187M on ~300 AI-generated/enhanced ads in the midterms; ~90% of tracked AI ads were for GOP candidates (Poynter, Nov 11 ~08:00 ET, via Techmeme river).

## Just missed the window

- **Grok Build app leak (disclosed overnight):** researchers found 35,000+ apps built with xAI's Grok Build exposed via "anyone with the link" shareable URLs found through passive DNS — SpaceXSI sales decks, Tesla scorecards, hospital rosters, defense staffing lists; built-in protections effectively none (Claroc report Nov 10; Ars Technica, Dan Goodin, ~01:05 ET Nov 11) — https://arstechnica.com/security/2026/11/grok-build-coding-agent-leaks-oodles-of-confidential-info-some-belonging-to-spacexsi/
- **European Commission formally introduced the Digital Fairness Act** (~06:25 ET, minutes before the window; curb addictive design/"dark patterns," fines up to 6% of turnover — follow-on to the leaked draft already reported) — Bloomberg, via Techmeme river 06:25 ET.
- **NVIDIA published Alpamayo 2 Edge open weights** (2B reasoning VLA for DRIVE AGX Thor; OpenMDW-1.1 weights, Apache-2.0 code) — Hugging Face model card lists HF release 11/10/2026, one day pre-window — https://huggingface.co/nvidia/Alpamayo2-Edge

## Coverage notes

- **Verified: 12 items** (7 developed entries incl. 2 ledger updates — doubleAI Maxwell and anthropic-s1-ant — plus 5 also-noted/one-liners), each backed by a fetched or search-returned page whose own timestamp places the first disclosure inside 07:00–12:00 ET Nov 11; three "just missed" footnotes (tagged once, never repeated).
- **Ledger dedupe:** 9 in-window-looking candidates suppressed as already reported — RTX 5080 24GB cancellation rumor, Baidu ERNIE 5.5 Flash "two weeks," Qwen4-Max preview, MSR dormant-neuron backdoor (all today's 07:00 fire), Alice-Huang 240,000-chips detail (megaspeed-alice-huang saga; Tom's Hardware piece also surfaced pre-window), OpenClaw v2026.11.3 (already `missed:`-tagged — not re-footnoted), Ollama v0.40.17-rc0, Apple "Harbor," AWS Strands Decider (all previously reported).
- **Window/date exclusions:** ZONOS3-Mini (Nov 9), Nex-N2.6 (Nov 10 launch, HF repo still gated), Apple "Amortized MIPS" RAG post (Nov 5), CISA KEV six-CVE batch (Nov 10), SoftBank 13GW turbines (06:50 ET).
- **Dead ends:** Poynter deep-link 404'd on a guessed URL (river-only attribution used instead); a Tom's Hardware slug 404'd before the real one was located. HN Algolia and the Techmeme river were healthy; Reddit's anonymous feed responded without throttling.
- Wednesday morning on a federal holiday — a comparatively light news window; nothing in-window qualified as a new security disclosure beyond the footnoted items.
