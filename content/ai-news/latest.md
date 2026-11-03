# AI News Briefing — Monday Night/Morning Edition, Nov 3 2026

**Window: 2026-11-03 07:00 → 12:02 EST (previous fire 07:00 → now; new items only)**

## TL;DR

- **CrowdStrike buys its way into AI-agent security**: definitive agreements for Fiddler AI (~$400M) and Dreadnode (~$95M), announced Tuesday at Fal.Con Europe, plus a runtime "AI Kill Switch" for stopping any agent — both products GA in Q4 FY27.
- **Google flips K-12 AI to opt-in — and says schools that block AI entirely will lose Google Search**; the same post rolls Guided Learning into Search and Classroom (Nov 3).
- **The Astra "weight-space programming" result is under dispute**: spaCy co-founder Matthew Honnibal's Nov 3 post — 640+ points on HN — argues the viral MIT demo's headline Qwen improvement never touches raw weights; it writes Python instead.
- Inference-stack churn: 23 vLLM merges in-window (XPress speculative decoding, MRV2 ReplaySSM, FP8/NVFP4 weight-cache fast-restart), three llama.cpp builds (b12072–b12074), and Yandex open-sourced FLAMIA microscaling attention kernels for Blackwell.

---

## Models and local inference

**Honnibal: the "Astra can read and write weights to improve Qwen" post is misleading** — *event date Nov 3, 2026* (Update on previously reported: ledger `weight-space-programming`, Nov 2)

SpaCy/Explosion co-founder Matthew Honnibal published a detailed critique of the viral MIT CSAIL "Weight-Space Programming" result (GPT-6 Astra editing Qwen3.5-0.8B's weights to lift MATH-500 from 7.9% to 48.1%). His core charge: the "just by looking at the raw weights" setting only appears in experiments 1, 2 and 4, on tiny 17K–172K-parameter networks. Every experiment involving a real language model — including the flagship Qwen math fix — uses Python tools, and the Qwen run also gets to execute the model on up to 50 batches of practice problems. The result still shows Astra can do real ML-engineer work, but it is not evidence of a single "gestalt of the weights" capability. The post hit the HN front page within 21 minutes of the window opening (641 points by noon).

**Why it matters:** the original result reframed frontier models as native weight-editors and raced through every AI feed yesterday; this is the first heavyweight methodological pushback, and it lands on the same day, which is rare for a correction cycle. For practitioners, the practical takeaway shifts from "models can read weights" to "models are now competent gradient-free ML engineers" — still notable, much less mystical.

**Local inference note:** the disputed artifact is Qwen3.5-0.8B (trivially local); the critique itself is about experiment methodology, not local performance claims.

- Source: The 'Astra can read and write weights to improve Qwen' post is misleading — honnibal.dev, 2026-11-03 — https://honnibal.dev/blog/astra-read-write-misleading
- Source (community corroboration): HN front page, 641 pts, posted 12:21 UTC — https://news.ycombinator.com/item?id=50351111

**Yandex Research open-sources FLAMIA microscaling attention kernels for Blackwell** — *event date Nov 3, 2026 (HF Daily Papers, Nov 3)*

FLAMIA (FLAsh MIcroscaling Attention) is a FlashAttention-4 fork adding low-precision *training* paths on SM100-class GPUs: MXFP8 (E4M3 with per-32-token power-of-two block scales) in forward and backward, and a mixed path using NVFP4 for QKᵀ plus E4M3 elsewhere, with quantization fused into the CuTe kernels and packed variable-length documents handled natively. Reported speedups: 1.10–1.42x end-to-end on B200/B300 and 1.22–2.01x on the NVFP4 backward pass, with only marginal loss drift on a 1B Llama-style run over 100B FineWeb-Edu tokens. Causal-safety options remove intra-block lookahead dependencies.

**Why it matters:** low-bit attention for training (not just serving) has been the missing piece for MXFP4/NVFP4 stacks; a production-grade reference implementation from Yandex — CUDA 13, cutlass-DSL based — will likely feed the open serving stacks.

**Local inference note:** requires SM100 (B200/B300) — it is data-center Blackwell only, not RTX 50-series; consumer Blackwell kernels remain the open frontier.

- Source: FLAMIA: FLAsh MIcroscaling Attention — Hugging Face Daily Papers, Nov 3 — https://huggingface.co/papers (FLAMIA entry)
- Source: yandex-research/flamia-blackwell-attention — GitHub — https://github.com/yandex-research/flamia-blackwell-attention

---

## Agent tools

**Community reports Codex silently re-enabled "Improve the model for everyone"** — *event date Nov 3, 2026 (Ask HN, 09:53 EST; unverified user report)*

An Ask HN post (85 points by noon) reports that several employees at a company that had deliberately disabled data sharing across Codex/Claude Code found the ChatGPT/Codex training opt-out silently re-enabled, with none of them having re-opted in. The post links the settings page (chatgpt.com/settings/data-controls) and notes a prior similar report. No vendor confirmation as of fire time; treat as an unverified signal.

**Why it matters:** if real, silent re-enabling of a training-data consent toggle on enterprise coding traffic is exactly the class of incident that hardens enterprise skepticism toward hosted agent tooling — and it lands a week after the Gheist/stolen-token campaigns targeting AI-provider keys.

- Source: "OpenAI seems to be silently re-enabling model training on Codex chats" — Hacker News Ask HN, Nov 3 (~85 pts) — https://news.ycombinator.com/item?id=50352757

**vLLM merges 23 PRs in-window; llama.cpp ships b12072–b12074** — *event date Nov 3, 2026 (also-noted)*

In-window vLLM merges (07:00–12:02 EST) include: XPlus/XAccept speculative decoding support with an acceptance estimator, MRV2 (Model Runner V2) Triton ReplaySSM decode planned on-GPU, DeepSeek V4/V4.1-Vision packed multi-image encode passes, "Fast Start" online-FP8 and CUTLASS-NVFP4 weight-cache restarts, batch invariance for ShortConv/LFM2 models, and a KV-offload correctness/async-lookup cluster (SWA null-page offsets, P2P role cleanup, per-request-group lookups). llama.cpp published three builds in-window (b12072 09:58, b12073 10:17, b12074 10:40 EST).

**Local inference note:** the FP8/NVFP4 weight-cache restart path matters for 16GB-class boxes — quantized weights re-warm after sleep without re-quantizing from disk.

- Source: vllm-project/vllm merged PRs (search window 2026-11-03T12:00Z..17:02Z) — GitHub — https://github.com/vllm-project/vllm
- Source: ggml-org/llama.cpp releases b12072–b12074 — GitHub — https://github.com/ggml-org/llama.cpp/releases

**Frontier models are poor teammates: Collaborative Intelligence Benchmark** — *event date Nov 3, 2026 (also-noted; HF Daily Papers)*

A Stanford/Oxford/MIT team released a 240-task Collaborative Intelligence Benchmark: Gemini 4 Argon leads at 82.2/100, yet even frontier models reach the best team deal in only 14% of negotiation conflicts, pass 45% of emotional-intelligence probes, and accept weaker offers 81% of the time despite asking about teammates' needs 99% of the time; 45% of failures occur *after* the model has recognized the problem.

- Source: "Measuring AI Agents as Teammates with Collaborative Intelligence" — Hugging Face Daily Papers, Nov 3 — https://huggingface.co/papers

---

## AI security and governance

**CrowdStrike signs definitive agreements for Fiddler AI and Dreadnode (~$495M combined) and unveils an AI Kill Switch** — *event date Nov 3, 2026*

Announced Tuesday at Fal.Con Europe in Barcelona: Fiddler AI (agent observability — pre-deployment eval, reasoning traces, drift and hallucination monitoring; works with LangGraph, Amazon Bedrock, Google ADK) becomes "Falcon Observe"; WSJ pegs Fiddler at ~$400M and Dreadnode (automated AI security testing: 100+ jailbreak/prompt-injection/agent-specific attack strategies, mapped to OWASP LLM Top 10, OWASP ASI01-ASI10, MITRE ATLAS, NIST AI RMF) at ~$95M, mostly cash with vesting stock. Both are slated GA in CrowdStrike's Q4 FY27, with Dreadnode findings feeding Falcon Guardian policy recommendations and SafeMind model-scoring, and the Dreadnode team joining the Cyber Superintelligence Lab. Alongside, CEO George Kurtz introduced an "AI Kill Switch" — "outside the model, at runtime, where an agent can't argue with it, rewrite it, or wait it out."

**Why it matters:** this is the clearest signal yet that agent observability and pre-deployment AI pentesting are being absorbed into EDR-platform distribution — the same consolidation that happened to SIEM — and "kill switch" is becoming a marketed control-plane feature rather than a conference-slide concept. For anyone running agents with credentials, audit-trail + runtime-stop capability is now a procurement checkbox.

- Source: CrowdStrike to Acquire Dreadnode to Advance AI Trust (press release) — crowdstrike.com, 2026-11-03 — https://www.crowdstrike.com/en-us/press-releases/crowdstrike-to-acquire-dreadnode-advance-ai-trust/
- Source: CrowdStrike moves to acquire Fiddler and Dreadnode — CyberScoop (Greg Otto), 2026-11-03 — https://cyberscoop.com/crowdstrike-acquires-fiddler-dreadnode-ai-security/
- Source: CrowdStrike Reportedly Paying $495 Million — SecurityWeek, 2026-11-03 — https://www.securityweek.com/crowdstrike-reportedly-paying-495-million-for-ai-security-firms-fiddler-dreadnode/

**Google makes K-12 AI opt-in — and will cut off Search for schools that block AI entirely** — *event date Nov 3, 2026*

Google's education post (VP Chris Phillips) says access to Gemini, Gemini Notebook, Gemini in Classroom and Google Search AI features will be "entirely off for K-12 student accounts until school administrators explicitly turn them on," with specifics to schools in coming months and rollout aimed at early 2027. Same post: Guided Learning (open-ended questioning, step-by-step scaffolding) is now globally in Classroom and will pilot in select US school districts in Search later this month. TechCrunch and NYT report the harder edge: because AI is fused into Search, schools that restrict AI altogether also lose student access to Google Search.

**Why it matters:** bundling search with AI turns a parental-choice debate into a de facto distribution mandate — opt-in by default while making opt-out expensive. Expect this to be the template regulators and districts litigate over in 2027.

- Source: Designing responsible AI for learning — blog.google, 2026-11-03 — https://blog.google/products-and-platforms/products/education/ai-learning-era-updates-2026/
- Source: Google will let schools disable its AI tools — but not without sacrificing Search — TechCrunch, 2026-11-03 — https://techcrunch.com/2026-11-03/google-will-let-schools-disable-its-ai-tools-but-not-without-sacrificing-search/
- Source: Google Will Turn Off Search for Schools That Reject A.I. — The New York Times, 2026-11-03 — https://www.nytimes.com/2026-11-03/us/google-search-schools-restrict-ai.html

---

## Just missed the window (footnoted once, not reportable)

- **OpenAI appoints ex-Army Secretary Dan Driscoll as Senior Counselor for National Security Modernization** (newly created role; Daybreak follow-on) — openai.com post dated Nov 2, 2026 — https://openai.com/index/dan-driscoll-joins-openai/
- **Salesforce signs definitive agreement to acquire voice-AI contact-center startup Cresta for a reported $2.7B** — announced Mon Nov 2; in-window re-coverage only (Axios Pro Nov 3) — https://www.axios.com/pro/enterprise-software-deals/2026-11-03/salesforce-cresta-software-deals
- **Anthropic's amended S-1A (filed Monday) says it "may imminently meet" its RSP threshold for dramatically AI-accelerated progress** and concedes it has "not identified… a significant acceleration until after it has occurred" — first reported ~17:35 ET Monday (outside this window; financial aspects already reported Nov 2 12:00) — https://aiweekly.co/alerts/anthropic-s-1a-claude-may-imminently-hit-rsi-threshold

---

## Coverage notes

- **Verified items this run: 10** (3 lead items, 4 also-noted, 3 first-time "just missed" footnotes), counting one entry per underlying event; every item's citable page was fetched, and lead stories were read in full.
- **Ledger dedupe: 15 candidates suppressed as already reported**, including the DeepSeek V5/Ascend-950 story, Nvidia's 51% non-Chinese rule, NDRC lifecycle-standards, NCSC 2028 AI-malware warning, SGLang v0.5.23, Ollama v0.40.14, OpenClaw v2026.10.10, vLLM v0.33.0, SemiAnalysis GDPx, Marvell/Lumentum, OpenAI Grid Protectors, Anthropic $1.5T IPO push, OpenAI/Cathedral bug-bounty revamp, the original weight-space post (carried as an Update, not a new item), and the NVIDIA auto-ontology HN re-spike (already footnoted once, so not repeated).
- Also excluded as pre-window/stale rather than ledger dupes: Drax/Brookfield/Radiant £20B Yorkshire DC (announced UK morning, ~05:20 UTC), Microsoft Mage-VL-Cowork (Oct 30), pydantic-ai v2.65.0 (minor, overnight).
- Dead ends: The Information's Anthropic-15-person-chip-team piece had no fetchable second outlet (Techmeme original, dropped); Adam Fry's ChatGPT "1.2B WAU" claim surfaced only via Techmeme/X with no resolvable article URL (dropped); guessed AI Weekly alert slug for the chip story 404'd (one strike, dropped).
- Ollama (v0.40.14 overnight) and SGLang/OpenClaw produced no new in-window releases; GitHub search API and HN Algolia healthy; Techmeme river fetched clean.
