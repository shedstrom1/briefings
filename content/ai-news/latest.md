# AI News Briefing — Tuesday, October 27, 2026 (07:00 ET)

**Window:** 2026-10-26 17:00 ET → 2026-10-27 07:00 ET (previous fire to now). New items only; all items verified against fully-read source pages with in-window dates.

## TL;DR

- **OpenAI claims three research breakthroughs** — including that the Exponential Time Hypothesis is false (Lean-verified) — announced Monday evening, with academic shepherds for each result.
- **Broadcom and four financial partners formed a $63B SPV** to buy Broadcom XPU accelerators and lease them to Anthropic, one of the largest AI-linked debt structures to date.
- **Cloudflare disclosed a cross-request KV cache leak** in GLM-5.3 on Workers AI — 155 responses exposed other customers' data — with coordinated SGLang fixes now upstream.
- **Baidu released Unlimited-OCR-2**, a ~3B-parameter MIT-licensed open-weight OCR model that parses hundred-page documents in one request via Sparse Reference Sliding Window Attention.
- **Cognition relaunched Devin Security** with Claude Mythos-powered deep scans and hundreds of millions in credits; **Nvidia said Tuesday it will invest $3B in Meta's equity offering** as the Muse partnership expands.

---

## Models and local inference

### OpenAI announces three breakthroughs: ETH is false, local smoothing proved in all dimensions, QCD existence
**Event date: Monday, October 26, 2026 (announced ~6:00 PM ET).**
In a post titled "Working with academic communities to build scientific understanding," OpenAI announced three results produced by its models: (1) a proof that the Exponential Time Hypothesis — the widely believed conjecture that 3-SAT requires exponential time — is **false**, with the natural-language disproof found October 16 and formalized in Lean October 17, both published to OpenAI's public GitHub; (2) a proof of Sogge's local smoothing conjecture in every dimension (via a novel auxiliary PDE), which also resolves the Kakeya and Stein restriction conjectures in all dimensions, with the AI output released publicly; and (3) a rigorous existence proof for interacting 4D quantum field theories including QCD and a unified Standard-Model completion (subsuming pure Yang–Mills existence, a Clay Millennium problem) — here OpenAI is **withholding the AI artifacts** and deferring to an external group led by David Gross that will write it up. The ETH work is being shepherded by a group led by Russell Impagliazzo, Ramamohan Paturi, and Ryan Williams, and OpenAI will fund a conference on the implications. The Hacker News thread reached 645 points in a few hours; Ryan Williams says he asked OpenAI to verify that no existing cryptosystems are compromised by the ETH result and was told they are safe.

**Why it matters:** ETH underpins hundreds of conditional hardness results across graph algorithms, databases, and computational biology; its refutation is one of the most significant complexity-theory results in decades, regardless of which model produced it. The release protocol itself — raw AI output treated as "raw research data," expert shepherds, no academic credit claimed — is a template for how frontier labs will disclose machine-generated science. No immediate engineering impact, but the three-way experiment (open release / guided release / deferred release) is the story security and research teams will study.

**Local inference:** none — this is pure research; the practical relevance to consumer-GPU inference is community speculation (a related SETH refutation implication would touch sub-quadratic attention research), not a claim OpenAI made.

Sources:
- OpenAI, "Working with academic communities to build scientific understanding" — October 26, 2026 — https://openai.com/index/building-scientific-understanding-with-academic-communities/
- Hacker News, "The Exponential Time Hypothesis Is False [pdf]" (645 points, posted ~6:15 PM ET Monday) — https://news.ycombinator.com/item?id=50251661
- The Next Web, "OpenAI says its AI model has disproved the Exponential Time Hypothesis" — Monday evening — https://thenextweb.com/news/openai-exponential-time-hypothesis-false-kakeya-yang-mills

### Update on previously reported (ledger 2026-10-26 anthropic-s1-ant): Broadcom lines up $63B to buy Anthropic its chip fleet
**Event date: Monday, October 26, 2026 (announced late afternoon ET).**
Bank of America, Blackstone, Citi and Morgan Stanley announced a $63 billion financing to purchase Broadcom XPU AI accelerators and networking gear through a newly formed special purpose vehicle and lease the capacity to Anthropic, part of Broadcom's previously announced strategic compute agreement with deliveries starting in calendar 2027. The first-lien tranche is arranged by BofA, Citi and Morgan Stanley, with Blackstone as sole provider of the committed second-lien portion, and Broadcom is providing residual-value support equal to the first-lien amount. The Wall Street Journal reports Broadcom CEO Hock Tan held 11th-hour talks, agreeing to higher rates and loan guarantees to secure bank participation in what is being described as the largest debt deal ever raised to fund AI infrastructure.

**Why it matters:** This is the financial-architecture story behind the S-1. Anthropic reported only $482M of capex in H1 against $782B of lease obligations; deals like this SPV show how that gap is being financed — vendor-supported, collateralized by the chips themselves. It also cements Broadcom's XPU as a genuine second source to Nvidia at frontier-lab scale.

**Local inference:** none directly; this is datacenter-scale infrastructure financing.

Sources:
- Broadcom Inc. press release (PRNewswire) — October 26, 2026 — https://www.broadcom.com/company/news/financial-releases/64786
- Reuters, "Wall Street banks, Blackstone line up $63 billion to finance Broadcom chips for Anthropic" — October 26, 2026 — https://www.reuters.com/legal/transactional/wall-street-banks-blackstone-line-up-63-billion-finance-broadcom-chips-anthropic-2026-10-26/
- WSJ, "Broadcom AI Infrastructure Draws $63 Billion Financing Deal" — October 26, 2026 — https://www.wsj.com/tech/ai/banks-blackstone-form-63-billion-financing-vehicle-for-broadcom-ai-tech-0438f6d2

### Baidu releases Unlimited-OCR-2: open-weight 3B OCR model parses "unlimited-horizon" documents in one request
**Event date: Tuesday, October 27, 2026 (pre-dawn HF publication).**
Baidu published `baidu/Unlimited-OCR-2` on Hugging Face — a roughly 3B-parameter image-text-to-text OCR and document-parsing model under an MIT license, the successor to June's Unlimited-OCR (which itself built on DeepSeek-OCR). The headline technique is Sparse Reference Sliding Window Attention (SR-SWA), which compresses text–visual attention interactions from ultra-long sequences into a fixed-size set of working references (e.g. 512 tokens), keeping per-step decode latency and memory nearly constant as documents grow — enabling true unbounded parsing of hundred-page documents in a single request, SOTA on both MPDocBench (dozens of pages) and MarathonDocBench (hundred-page). The model card ships runnable Transformers and SGLang serving recipes (page-window scheduling env vars, CUDA-graph prefill buckets, /dev/shm tensor passing) and notes a tested configuration on an A800-80GB.

**Why it matters:** Long-document OCR is one of the most practical local workloads — invoices, scanned ledgers, regulatory filings — and an MIT-licensed 3B model with constant-memory attention is exactly the class of release that lands in llama.cpp/Ollama-class stacks within days. The SR-SWA trick (bounded working-reference set instead of full attention over every page token) is the kind of architecture detail local-inference engine authors will copy.

**Local inference:** at ~3B parameters this is comfortably a consumer-GPU model; the vendor's serving recipe targets SGLang, and the repo notes memory-tuning knobs (mem-fraction-static 0.7, max-prefill-tokens 2048) that matter most on 16–24 GB cards. No consumer-GPU throughput numbers published yet.

Sources:
- Hugging Face, `baidu/Unlimited-OCR-2` model card (release log: "[2026-10-27] We release Unlimited OCR 2") — https://huggingface.co/baidu/Unlimited-OCR-2
- explainx.ai, "Unlimited-OCR-2: Baidu's 3B MIT OCR Model, How to Run It" — October 27, 2026 — https://www.explainx.ai/news

### Update on previously reported (ledger 2026-10-26 meta-95b-raise): Nvidia to invest $3B in Meta's offering as Muse partnership expands
**Event date: Tuesday, October 27, 2026 (pre-dawn release).**
Nvidia announced it will invest $3 billion in Meta's equity offering (the up-to-$95B raise reported yesterday) as the two companies expand collaboration on Meta's Muse agent family: joint co-optimization of Muse training and inference on Nvidia hardware, expanded Meta deployment of Vera Rubin systems and Vera CPUs for next-generation agents, and Spectrum-X Ethernet plus BlueField-4 networking. OpenShell and Sentry are described as only under consideration. Muse has surpassed 10 million users in six weeks.

**Why it matters:** The circular-financing pattern Nvidia–OpenAI popularized is now Nvidia–Meta too: supplier, anchor investor, and infrastructure partner in one transaction, tied to Meta's fastest-growing agent product. For builders, the Vera Rubin/Vera CPU commitment signals where Meta's agent serving capacity is headed through 2027.

**Local inference:** none directly — but the Vera CPU line matters for agent-serving efficiency conversations beyond the datacenter (see the Modal/Vera item reported Oct 19).

Sources:
- NVIDIA Newsroom, "NVIDIA and Meta to Expand Collaboration on Muse and Muse Family of Models" — October 27, 2026 — https://nvidianews.nvidia.com/news/nvidia-and-meta-to-expand-collaboration-on-muse-and-muse-family-of-models-amid-robust-growth
- CNBC, "Nvidia to invest $3 billion in Meta" — October 27, 2026 — https://www.cnbc.com/2026-10-27/nvidia-invest-meta.html
- WSJ, "Nvidia to Invest $3 Billion in Meta's Equity Offering" — October 27, 2026 — https://www.wsj.com/tech/ai/nvidia-to-invest-3-billion-in-metas-equity-offering-as-they-expand-ai-partnership-e8d3850a

### Mirendil unveils Fern: from-scratch model with 10M-token context, early access only
**Event date: Monday, October 26, 2026 (evening disclosure).**
Mirendil — a lab founded by former Anthropic researchers — announced Fern 0.1, pretrained from scratch on its own Mirendil-Base (0.1–0.5T parameter variants) with a context window up to 10M tokens (~0.5M lines of code or days of agent traces). Vendor-reported retrieval: 100% single-needle and 90% multi-key at the full 10M tokens. Positioning targets AI R&D itself — auditing training data, debugging runs from logs, reading agent traces end-to-end. No independent RULER/benchmark numbers published, no pricing; access is early-request ("coming soon").

**Why it matters:** A new entrant leading with 10M context (10× Claude Opus 5 / Gemini 4 Argon's 1M class) pressures the long-context frontier from below, and the agent-trace-auditing pitch is a credible first wedge for a lab whose team came from interpretability-adjacent work. Treat all retrieval claims as vendor-reported until RULER-style numbers land.

Sources:
- Mirendil, "Introducing Fern" — October 26, 2026 — https://mirendil.com/news/introducing-fern/
- Startup Fortune, "Mirendil unveils Fern, an AI model claiming a 10 million token context window" — October 26, 2026 (evening) — https://startupfortune.com/mirendil-unveils-fern-an-ai-model-claiming-a-10-million-token-context-window/
- AICoder news brief (dated Oct 26) — https://aicoder.com/news/news-20261026-mirendil-fern-10m-context-model-pretrained-from-scratch

### Also noted — local-inference stack
- **llama.cpp** shipped 10 builds in-window (b11910–b11926, Oct 26 9:20 PM ET → Oct 27 6:29 AM ET): https://github.com/ggml-org/llama.cpp/releases
- **ComfyUI v0.43.0** released (Oct 26 10:40 PM ET): synchronized graph cleanup in model_prefetch, rope-scaling support among the headline changes: https://github.com/Comfy-Org/ComfyUI/releases/tag/v0.43.0
- **Pydantic AI v2.62.0** released (Oct 26 10:38 PM ET): mid-conversation thinking-effort changes via OpenAI `configuration_update` and Anthropic per-message `output_config`: https://github.com/pydantic/pydantic-ai/releases/tag/v2.62.0

## Agent tools

### Cognition relaunches Devin Security: Claude Mythos deep scans plus "hundreds of millions" in credits
**Event date: Monday, October 26, 2026 (announced ~5:30 PM ET).**
Cognition introduced the new Devin Security: deep vulnerability scans powered by Claude Mythos and other frontier cyber models (model-neutral, chosen per engagement), findings prioritized against a per-customer threat model built with the security team (reachability and business impact, not just CVSS — KEV-listed flaws cannot be relabeled), and thousands of parallel remediation sessions in isolated VMs that test changes and open evidence-backed PRs. Alongside it, Cognition committed hundreds of millions of dollars in Devin credits: free frontier-model deep scans for current and future enterprise customers, and remediation credits for new customers. Co-founder Jared Palmer called it an "autonomous defense factory"; Jeff Wang teased a same-day Alex Stamos webinar on what security teams should expect.

**Why it matters:** This is the clearest signal yet that frontier *cyber* models (Mythos-class) are becoming a commercially distributed defensive utility — Cognition is subsidizing access at scale to build the finding→fixing credit flywheel. For defenders already watching the PHP2Shell agentic-attack wave, a free deep-scan tier from a funded vendor is a practical, this-week action item.

Sources:
- Cognition, "Devin Security: Find and fix your most dangerous vulnerabilities" — October 26, 2026 — https://cognition.com/blog/devin-security
- Cognition on X (launch post, ~5:30 PM ET Oct 26) — https://x.com/cognition/status/2114741368627335379
- Digg AI, "Devin Security adds Claude Mythos-powered deep scans" (13–14h aggregation) — https://digg.com/ai/s2qa0dx2

## AI security and governance

### Cloudflare discloses cross-request KV cache leak in GLM-5.3 on Workers AI; SGLang fixes land upstream
**Event date: Monday, October 26, 2026 (coordinated disclosure, Monday evening ET).**
Cloudflare published a post-mortem (coordinated with the SGLang team and researcher Gadi Evron of Knostic, who reported via HackerOne on September 8): between August 27 and September 9, **155 responses** from GLM-5.3 on Workers AI to prompts longer than 681,280 tokens contained data from other customers' recent requests, all returned with HTTP 200. Two overlapping causes: the prefill KV-cache pool was sized at 681,280 tokens while the advertised context window was 1,048,576 (a mismatch Cloudflare knew about pre-launch), and SGLang's rejection path in disaggregated prefill/decode mode marked over-length requests as aborted but still shipped a placeholder KV cache over RDMA (Mooncake) — the decode node then generated against stale cache pages holding prior users' data. Remediation: pool now sized at ~2.04M tokens, rejected requests retired at the admission queue (upstream SGLang #38704/#38935, fix on main in #36988, released in v0.5.21), a per-transfer checksum (#39500), a page-tagging prototype costing <1% throughput, and a new formal-methods effort to prove the safety of the RDMA KV-transfer protocol. Cloudflare found no evidence of deliberate exploitation and confirmed no other Workers AI model was affected.

The takeaways for anyone operating inference engines: verify KV-pool size against advertised context from the running engine (not response codes), treat pool/rejection paths as a security boundary, and watch the SGLang fixes if you're below v0.5.21.

Sources:
- Cloudflare Blog, "Cross-request KV cache exposure in the GLM-5.3 Workers AI model, and our remediation with SGLang" — October 26, 2026 (timeline: "Oct 26 — Coordinated publication with the researcher and RadixArk") — https://blog.cloudflare.com/kv-cache-exposure-remediation/
- LMSYS/SGLang, "SGLang security update: improving request isolation" — October 26, 2026 (coordinated disclosure) — https://www.lmsys.org/blog/2026-10-26-cf-security/

---

## Coverage notes

- **Verified: 8 items** (5 lead, 1 update-pair folded from 2, 4 also-noted lines) — every cited page fetched and read; lead stories read in full. All items carry in-window (Oct 26 17:00 → Oct 27 07:00 ET) publication or disclosure timestamps; Mirendil's exact first-disclosure minute could not be pinned (multiple outlets first published it Monday evening ~8 PM ET; disclosed Oct 26).
- **Out-of-window, excluded (no footnote; 'Just missed' slot already used):** Google Gemini 4 Argon digest re-coverage (announced Sep 30), KRAFTON Raon-1 mini preview (Oct 21), Cerebras "59 wafers for Kimi K3" walkthrough (Oct 22), Huang's ~$2,000/GPU/yr agent-software quote (Oct 25), Tailworm Tailscale SSH report (Oct 25), Unconventional AI–NeuReality acquisition (Globes timestamps it Mon 17:02 Israel = 10:02 ET — pre-window), Bedrock Robotics $60M Series C (date unresolved after two attempts).
- **Dead ends / limitations:** no independent (non-vendor) benchmarks for Fern or Unlimited-OCR-2 yet; Devin Security's exact blog timestamp is secondhand (Digg/X cluster ~5:30 PM ET). Techmeme fetched cleanly; no indexes were down.
