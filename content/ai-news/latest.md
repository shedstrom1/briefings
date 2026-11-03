# AI News Briefing — Nov 3, 2026, 07:00 ET (overnight edition)

**Window:** 2026-11-02 17:00 EST → 2026-11-03 07:00 EST (14-hour overnight sweep, America/New_York). New items only; event dates anchor placement.

## TL;DR

- **SGLang v0.5.23 lands the release local-inference users have been waiting for**: opt-in "Paged Experts" runs MoE models larger than GPU memory on a single consumer GPU (up to 30x faster than CPU offload), plus fused megakernel/monokernel decode for DeepSeek-V4.1-Flash at up to 44% (GB300) and 75% (MI350X) faster single-user decode — 1,007 PRs from 257 contributors.
- **Nvidia has verbally told cloud and neocloud customers that at least 51% of their services must go to non-Chinese companies**, with enforcement now including physical data-center inspections, contract verification and end-user interviews.
- **The UK NCSC says AI-powered malware is "highly likely" to feature in a ransomware attack on UK critical national infrastructure before 2028**, and that Russia and China will use AI-cyber "routinely" by then.
- **China's NDRC will fast-track whole-life-cycle AI safety standards** spanning data through compute, covering DeepSeek, Moonshot and Alibaba — plus OpenAI more than quadrupled bug-bounty rewards (to $30k critical / $172k with bonuses) and named Cathedral as its security-sprint partner.
- **OpenAI published Pando**, its Git-compatible monorepo backend that cut median push latency ~5x and now serves close to a petabyte of Git data per day, built in ~10 weeks with Codex; **DeepSeek is reported to be pre-training V5 natively on Huawei Ascend 950**, with Huawei's Ascend 960DT pulled forward three quarters to 1Q27.

---

## Models and local inference

### SGLang v0.5.23: consumer-GPU MoE overflow, fused persistent-kernel decode (event: Nov 2, 2026 — 22:14 UTC / 5:14 PM ET)

SGLang shipped v0.5.23, a large drop (1,007 PRs from 257 contributors, 575 commits to main since v0.5.22). The headline for anyone on a single desktop card is **Paged Experts (opt-in), which runs MoE models larger than GPU memory on one consumer GPU at up to 30x faster than CPU offload**. A new opt-in **LoRA engine serves adapters on quantized MoE weights (FP8 and NVFP4)**, and DeepSeek-style MLA models using decode context parallelism can now keep their prefix cache in HiCache file-based L3 storage.

On the latency side: **DeepSeek-V4.1 on GB300 gets up to 1.5x more prefill throughput under load and ~28% lower first-token latency on 32K–64K prompts**; the new **low-latency mode** runs DeepSeek-V4.1-Flash decode as fused persistent kernels via opt-in NVIDIA megakernel / AMD monokernel plugins, for **up to 44% faster single-user decode on GB300 and 75% on MI350X**; MoE models including DeepSeek-V4.1-Flash, GLM-5.3-Flash and gpt-oss reach the first token 30–58% sooner on 8K prompts on H200. Wheels/images: CUDA 13, ROCm 10 (MI35x/MI30x), Intel XPU and Xeon, TPU7x.

**Why it matters:** Paged Experts attacks the exact wall that makes big MoE models unusable locally — expert weights no longer have to fit in VRAM, which moves the ceiling from "quantize until it hurts" to "keep hot experts resident, page the rest on-device." Combined with the fused-kernel decode work and the LoRA-on-NVFP4 path, this is the most practical single release this cycle for a 16GB card or a 128GiB GB10 box running DeepSeek-V4.1-Flash or GLM-5.3-Flash agents.

`Source: Release v0.5.23 · sgl-project/sglang — GitHub, published Nov 2, 2026 22:14 UTC` — https://github.com/sgl-project/sglang/releases/tag/v0.5.23

### Ollama v0.40.14: Claude Code / Codex launch paths fixed, MLX sampling faster (event: Nov 2, 2026 — 20:40 ET)

Ollama released v0.40.14 (its third release in six days). `ollama launch claude` now applies the model's own output-token limit to Claude Code, so cloud models no longer hit a hard 128k default cap; `ollama launch codex` verifies ChatGPT routing before configuring and fails loudly when routing is unavailable. OpenAI-compatible endpoints no longer override a model's packaged sampler defaults — clients that omit `temperature`/`top_p` now get the model's own values instead of 1.0, matching `/api/chat`. Also: `/v1/responses` preserves readable input content including prior assistant refusals, the app retries ChatGPT disconnect failures, and top-k sampling is faster on MLX for large vocabularies.

**Why it matters:** the sampler-defaults fix is a correctness fix that quietly changes output quality for anyone pointing OpenAI-compatible tooling at local models, and the `launch` fixes remove two failure modes people hit when driving Claude Code or Codex against Ollama-hosted or Ollama-routed models.

`Source: Releases · ollama/ollama — GitHub, v0.40.14 published Nov 3, 2026 01:40:40 UTC (Nov 2, 20:40 ET)` — https://github.com/ollama/ollama/releases/tag/v0.40.14

### Update — Huawei's HBM squeeze: smuggled memory, customers told to self-source, Ascend 950DT at ~$37k (event: Nov 3, 2026)

*Update on ledger items `missed:deepseek-huawei-chips` (2026-10-19) and `cxmt-hbm3e-risk` (2026-10-28).*

TrendForce's Nov 3 roundup, citing The Information, says smugglers are smuggling banned HBM into China by bundling memory stacks with lower-performance server processors — export rules assess the package's computing power from the processor, not the memory — and technicians then heat the package to soften solder and harvest the HBM. The smuggled memory reportedly lands with Huawei customers: the Ascend 960DT (now expected 1Q27) is set to double the memory bandwidth and capacity of Huawei's current flagship, and **Huawei has reportedly asked some AI-chip customers to source the HBM themselves** because it cannot buy enough from Samsung and SK hynix. Meanwhile **Huawei raised the suggested price of the Ascend 950DT above RMB 250,000 (~US$37,000)** — roughly B200 money for a chip with reportedly about half the raw compute of a 2022 H100. CXMT's HBM3E yields are still stuck at 20–30% versus SK hynix's ~70%, and Cambricon has pushed indicative pricing for its next-gen 690 chip up 20–30%, with MetaX and Iluvatar CoreX following.

**Why it matters:** the memory wall is now the binding constraint on China's accelerator stack, and the price/performance gap versus Nvidia is widening rather than closing — which keeps domestic Chinese models (and any open-weight model trained on Ascend) dependent on scarce, smuggled or low-yield HBM.

`Source: [News] China Reportedly Obtains Smuggled HBM via Server Packages; Huawei 960DT Faces Domestic HBM Shortage — TrendForce, Nov 3, 2026` — https://www.trendforce.com/news/2026-11-03/news-china-reportedly-obtains-smuggled-hbm-via-server-packages-huawei-960dt-faces-domestic-hbm-shortage/

### Update — DeepSeek V5 to pre-train natively on Huawei Ascend 950; 960DT pulled forward three quarters (event: Nov 3, 2026)

Citing Caiwen News and Star Market Daily, TrendForce reports DeepSeek plans to train its next-generation **V5 model primarily on Huawei Ascend 950 chips**, expected to materialize in 2027 — which would make V5 DeepSeek's first LLM trained natively on Ascend rather than Nvidia. Ascend is described as DeepSeek's only training platform (no Moore Threads), driven by a compute crunch now that previously approved H-series GPUs cannot carry V4/V5 training loads. Separately, Crypto Briefing reports DeepSeek intends to deploy at least **160,000 Ascend 950DT chips at a new ~1 GW site in Ulanqab, Inner Mongolia** — a fleet originally earmarked for inference — with pre-training workloads starting as soon as late 2026. TrendForce adds that Huawei rotating chairman David Wang said the **Ascend 960DT is now ready in 1Q27, three quarters ahead of schedule**, with 960PR in 3Q27, 970 in 2028 and 980 in 2029; Caiwen's sources also say **Huawei holds no equity in DeepSeek** despite funding rumours.

**Why it matters:** moving a frontier-class open-weight model's *pre-training* onto Ascend is the milestone export-control watchers have been waiting for — inference migration was expected, native training is the real decoupling signal, and it is also the strongest evidence yet that the Ascend roadmap is being pulled forward under memory-supply pressure.

`Source: [News] DeepSeek V5 Could Complete Training on Huawei Ascend 950 in 2027, Moving Beyond Inference — TrendForce, Nov 3, 2026` — https://www.trendforce.com/news/2026-11-03/news-deepseek-v5-could-complete-training-on-huawei-ascend-950-in-2027-moving-beyond-inference/

`Source: DeepSeek plans to train its V5 model on Huawei Ascend 950 chips — Crypto Briefing, Nov 3, 2026` — https://cryptobriefing.com/deepseek-v5-huawei-ascend-950-training/

### Nvidia tells cloud clients 51%+ of services must go to non-Chinese companies (event: Nov 2–3, 2026; FT)

The Financial Times reports, citing five people, that **Nvidia has told cloud and data-centre customers that at least 51% of their services must be sold to non-Chinese companies** — a majority floor on Chinese exposure in future contracts, with existing agreements exempt. Crypto Briefing's Nov 3 write-up adds the enforcement detail: the instruction was delivered **verbally to cloud and neocloud customers in Singapore, Malaysia and Japan**, and Nvidia's enhanced compliance regime now includes **physical data-centre inspections, contract verification of end customers, and interviews with end users**, following May 2026 Commerce Department guidance; Nvidia cut its approved Asian customer list by more than 50% in July 2026 and has modified fiscal-2026 revenue reporting to reflect customer location.

**Why it matters:** this closes the "chip stays outside China, compute is rented inside" loophole without a new export rule — it makes Nvidia, not the customer, the enforcement authority over who rents AI capacity in Asia. For anyone buying capacity for local-inference or agent workloads in that region, availability and pricing should tighten.

`Source: Nvidia tells cloud clients to sell at least 51% of services to non-Chinese firms — Crypto Briefing, Nov 3, 2026` — https://cryptobriefing.com/nvidia-cloud-clients-51-percent-non-chinese/
`Source: Nvidia orders data centre clients to reduce China exposure — Financial Times` — https://www.ft.com/content/1902df70-1380-4588-bad5-171352b575f2
`Corroboration: [News] China Reportedly Obtains Smuggled HBM... (cites Financial Times) — TrendForce, Nov 3, 2026` — https://www.trendforce.com/news/2026-11-03/news-china-reportedly-obtains-smuggled-hbm-via-server-packages-huawei-960dt-faces-domestic-hbm-shortage/

### Also noted — engines

- **llama.cpp: nine in-window builds, b12059–b12069** (b12059 22:41 UTC Nov 2 → b12069 11:19 UTC Nov 3). — https://github.com/ggml-org/llama.cpp/releases
- **OpenClaw v2026.10.10** GA at 03:22 UTC Nov 2/3 (first stable since v2026.10.10-beta.5). — https://github.com/openclaw/openclaw/releases
- **Claude Code v2.1.317** (01:25 UTC Nov 3) and **Codex rust-v0.167.0-alpha.4** (02:24 UTC Nov 3); **PraisonAI v4.7.23** (08:02 UTC Nov 3). — https://github.com/anthropics/claude-code/releases · https://github.com/openai/codex/releases

---

## Agent tools

### OpenAI publishes Pando: a Git-compatible backend that cut median push latency ~5x at Codex scale (event: Nov 2, 2026)

OpenAI detailed **Pando**, a new backend for Git repositories that now powers all development in OpenAI's monorepo. Built to handle agent-driven volume — commits on master grew **1,378% between Q1 2024 and Q3 2026**, with the Q4 2026 estimate at 2,442% — Pando **cut median push latency by nearly 5x (~80%) and now delivers close to a petabyte of Git data per day**, while staying compatible with the Git protocol and client. Two design choices do the work: **each branch's ref updates are coordinated independently** so pushes to unrelated branches proceed in parallel (instead of the quorum round-trips of 3PC or the shared write-ahead-log ordering), and **packfiles are reused across fetches** rather than regenerated per client. Reliability had at times fallen below 90% before the rewrite. OpenAI contributed parallel pack download/indexing, fetch validations and "explicit haves" upstream (the latter enabling `--force-with-lease` against trunk-only packs), and says it is exploring **multi-agent collaboration on shared Git repositories** and ways to "share the technology that makes Pando possible with our users."

**Why it matters:** agent fleets are now the bottleneck for source control, not humans — parallel-push contention is a real, measured failure mode. And OpenAI describing Pando as internal while "exploring ways to share the technology" is the tell that agent-scale Git is about to become a product surface; teams running swarms of coding agents on GitHub/GitLab should expect the same ceiling.

`Source: Scaling Git at OpenAI — openai.com, Nov 2, 2026` — https://openai.com/index/scaling-git-at-openai/
`Corroboration: OpenAI Pando: Git Backend With 5x Faster Pushes — explainx.ai, Nov 3, 2026` — https://www.explainx.ai/blog/openai-pando-git-backend-codex-monorepo-5x-faster-pushes-petabyte-per-day-2026

### Also noted — agent tooling

- **Claude Code v2.1.317** and **Codex rust-v0.167.0-alpha.4** both shipped in-window builds (see engines above); **PraisonAI v4.7.23** is the in-window agent-framework bump.
- **Nvidia Auto Ontology** hit the Hacker News front page in-window (108 points, 02:04 ET) — see the "Just missed the window" footnote below for why it is not counted as a new item.

---

## AI security and governance

### UK NCSC: AI-powered malware "highly likely" in a CNI ransomware attack by 2028; state AI-cyber use "routine" (event: Nov 3, 2026)

The National Cyber Security Centre published **"Artificial intelligence and cyber intrusion"**, an NCSC Assessment (part of the Professional Heads of Intelligence Assessment framework) inside the State of the Cyber Nation review. Its key judgements, using the PHIA probability yardstick: by 2028 it is **highly likely Russia, China and other nation states will routinely use AI-cyber** to pursue geopolitical goals, with the UK facing increased scale and speed of attacks; **almost certain** that many systems stay vulnerable to AI-cyber tools that find and exploit weaknesses — attackers will show "less caution over losing an access, as the cost to replace is minimal"; **likely** that AI-cyber capability is applied against known vulnerabilities in critical-national-infrastructure legacy tech plus the new attack surface created by AI deployed *inside* CNI; and **highly likely that between now and 2028 AI-powered malware will have been used in a ransomware attack on UK CNI**. The paper models three 2028 scenarios (baseline / pessimistic / optimistic); the pessimistic case includes an **automated cyber actor "no longer under its operator's control"** that can self-replicate, and attributes much of the threat to jailbroken models becoming commercially available to non-professionals. It also notes defensive AI has compressed the zero-day-to-n-day window, so the constraint shifts to organisational patching and testing speed, and judges that Russia has favored retraining **open-source** models over in-house development.

**Why it matters:** this is a national-intelligence agency formally putting a probability on agentic-offence-versus-defence imbalance, and it lands on the same diagnosis the open-source security community has been making — the zero-day-to-n-day window is shrinking for defenders *and* attackers, so the differentiator is remediation throughput, not discovery. It is also the clearest official statement yet that **fine-tuned open-weight models are the assumed vector for state actors**, which cuts against both "ban open weights" and "defence will win" narratives.

`Source: Artificial intelligence and cyber intrusion — NCSC (ncsc.gov.uk), published 3 November 2026` — https://www.ncsc.gov.uk/report/artificial-intelligence-and-cyber-intrusion
`Second source: AI-powered malware 'highly likely' to be used to hack UK infrastructure by 2028 — The Guardian, Nov 3, 2026` — https://www.theguardian.com/technology/2026/nov/03/ai-powered-malware-artificial-intelligence-hacking-critical-uk-infrastructure

### China's NDRC will fast-track "whole life cycle" AI safety standards from data to compute (event: Nov 3, 2026 — 05:24 UTC)

China's state economic planner announced it is **quickening legislation to govern and oversee how AI is developed and applied, covering the entire life cycle of models**, with rules extending from data to computing power. **Wang Ruomeng**, a senior official at the **National Development and Reform Commission**, told reporters at a Tuesday press conference that the agency is also closely monitoring AI's impact on employment. The standards cover the labs driving China's frontier push — DeepSeek, Moonshot, Alibaba — and follow Beijing's May 2026 agent guidance and its ~200-standard/ethics-review buildout. Bloomberg frames them as some of the most comprehensive loss-of-control guardrails attempted by any jurisdiction, arriving after a run of global AI security incidents.

**Why it matters:** compute is now named as a regulated object, not just content or data — for Chinese labs, compliance reaches the training cluster. Practical effect for the open-weight ecosystem: every downstream user of DeepSeek/Moonshot/Alibaba weights inherits whatever provenance, logging and safety obligations China imposes upstream, and any "safety review of the whole lifecycle" is also a lever over who may export models.

`Source: China Plans Broad AI Guardrails After Global Security Lapses — Bloomberg, Nov 3, 2026 05:24 UTC` — https://www.bloomberg.com/news/articles/2026-11-03/china-plans-broad-ai-guardrails-after-global-security-lapses
`Second source: China Signals Plans for Broad AI Safety Guardrails — The Information (briefing), Nov 3, 2026` — https://www.theinformation.com/briefings/china-signals-plans-broad-ai-safety-guardrails

### OpenAI more than quadruples bug bounties (to $30k / $172k) and names Cathedral as security-sprint partner (event: Nov 2, 2026)

OpenAI published two paired posts. **Rewarding defenders with bigger bug bounties**: the midpoint of reward ranges for medium, high and critical findings is more than quadrupled on average; **a critical vulnerability in a core product (ChatGPT, Codex, the API Platform) now pays up to $30,000**, exceptional critical findings remain eligible for **up to $100,000, and up to $172,000 with bonuses** (worked example: $100,000 × 1.15 quality × 1.30 priority-campaign × 1.15 loyalty = $171,925). New mechanics: report-quality multipliers for reliable proofs of concept, a **five-tier loyalty programme**, and time-limited priority campaigns tied to feature launches; reports not yet awarded get the new ranges, and bounties paid in the prior 30 days are trued up. **Preparing our next security sprint**: OpenAI will bring **Cathedral** researchers inside, using OpenAI's own models alongside OpenAI engineers to attack its systems; the last sprint committed **25% of product engineers** to finding and fixing weaknesses using Codex to reproduce vulnerabilities in isolated environments. OpenAI repeats that "traditional cyber defenses alone are no longer sufficient," points to **$1 billion in subsidised access over six months for frontline defenders** (Daybreak), and says it is sharing its blueprint, architecture and lessons with industry and government defenders.

**Why it matters:** OpenAI is pricing critical findings into parity with Google's VRP and institutionalising *offensive use of its own models* on its own production systems — with an external partner and published architecture. For AI security teams, the meaningful precedent is the retroactive true-up plus campaign multipliers: bug bounty economics are being run like a growth programme, which will pull researcher supply away from smaller AI vendors.

`Source: Preparing our next security sprint to harden OpenAI — openai.com, Nov 2, 2026` — https://openai.com/index/preparing-our-next-security-sprint-to-harden-openai/
`Source: Rewarding defenders with bigger bug bounties — openai.com, Nov 2, 2026` — https://openai.com/index/rewarding-defenders-with-bigger-bug-bounties/
`Second source: Bug hunters can now earn up to $172,000 from OpenAI as company ramps up security — The Decoder, Nov 3, 2026` — https://the-decoder.com/bug-hunters-can-now-earn-up-to-172000-from-openai-as-company-ramps-up-security/

### Update — Anthropic IPO: institutional investors push pricing toward the low end of the range, ~$1.5–1.6T (event: Nov 3, 2026 — 06:04 ET)

*Update on ledger item `anthropic-s1-ant` (2026-10-26).*

Fortune's Nov 3 analysis, drawing on Jay Ritter (Florida), Tomasz Tunguz (Theory Ventures), Aswath Damodaran (NYU), Campbell Hutcheson (Epoch AI) and Tony Trzcinka (Impax), reports institutional investors are pushing Anthropic and its advisors to price at the **lower end of the $1.5–2 trillion range**, and that history is unhelpful: any IPO valuation above roughly **$1.41 trillion means paying >$40 per dollar of trailing revenue**, a level reached by only **14 of 4,110 IPOs from 1980–2024**, which on average returned **15.4% below the market**. Against ~**$35.3 billion** of trailing-four-quarter revenue, $1.5–2T implies **43–57x sales**; Ritter says Anthropic needs a credible path to **≥$100 billion in annual after-tax profit** to justify it. Q3 detail: revenue **+~47% QoQ**, compute and infrastructure expense **+~77% to $9.9 billion** — about **$5 billion of it short-term capacity** — with adjusted operating margin down to **15% from 19%** and **~$131.8 billion** in cash and short-term investments. Tunguz still pegs implied gross margin at ~85%; Damodaran's pre-results DCF landed at **~$1.475 trillion** and he saw no reason to revise.

**Why it matters:** the "AI is unprofitable" story is now being argued with IPO-historical base rates rather than vibes, and it lands the week Anthropic is touring. If the book prices at $1.5T it resets public-market comps for every private lab, and the short-term-compute line ($5B of a $9.9B bill) is the number to watch — it is what capacity scarcity costs a lab *today*.

`Source: Anthropic's third quarter results mean IPO math and history are not on its side, analysts say — Fortune, Nov 3, 2026 6:04 AM ET` — https://fortune.com/2026/11/03/anthropics-third-quarter-results-mean-ipo-math-and-history-are-not-on-its-side-analysts-say/

---

## Just missed the window

Footnoted once, never repeated. All three were disclosed before 2026-11-02 17:00 ET and are excluded from the verified count; their Nov 3 coverage is re-coverage, not a new event.

- **Deep Principle releases Hydrogen-1.0** — 753B-parameter open-weight MoE for scientific discovery, post-trained from Z.AI's GLM-5.3 on chemistry/materials/life-science data plus its own lab records and agent trajectories; MIT licence, 1M-token context, 128K max output, BF16 plus NVFP4 (B200/B300) and MXFP4 (MI350X/MI355X) builds, SGLang `--tp 8` recipe; vendor evals claim it beats GPT-6 Astra on FrontierScience-Research and +5.13/+4.76 pts on scientific computing and forward-reaction prediction versus Astra at max thinking. **Missed:** Hugging Face repo created Oct 31 15:54 UTC, weights last modified Nov 2 09:00 UTC, blog asset-tagged `v20261102a`. — https://huggingface.co/deep-principle/Hydrogen-1.0 · https://blog.deepprinciple.com/introducing-hydrogen-1.0/
- **NVIDIA Auto Ontology** — Apache-2.0 framework that builds a semantic, graph-based "context layer" over enterprise metadata and serves it to any agent harness via MCP, CLI and REST, with deterministic query generation for text-to-SQL; NVIDIA claims ~90% reduction in ontology build time; NGC container published; 1,614 GitHub stars after an in-window Hacker News front-page run (108 points, 02:04 ET Nov 3). **Missed:** MarkTechPost carried the release Nov 1–2; the in-window event is attention, not disclosure. — https://github.com/NVIDIA/auto-ontology
- **Zyphra Hierarchical Attention (ZHA)** — sparse attention that builds a tree of recursively compressed key summaries over the KV cache and traverses it top-down, keeping the *indexer itself* subquadratic; retrofits frozen pretrained LLMs with ~81,920 added parameters and 4B tokens of KL distillation, roughly matching dense accuracy on reasoning and long-context retrieval (Qwen3.6-35B, RULER). **Missed:** arXiv 2610.42112 and the Zyphra post predate the window (Nov 1–2); the subquadratic speedups are modelled, not measured. — https://www.zyphra.com/our-work/zyphra-hierarchical-attention · https://arxiv.org/abs/2610.42112

---

## Coverage notes

**Verified and counted in this edition: 13 items** — 5 lead developments, 4 additional items/updates in Models and local inference, 1 lead in Agent tools, 4 in AI security and governance (2 leads + 1 update + 1 item), counting one entry per underlying event. Plus 11 also-noted engine/tooling items consolidated into 5 lines, and 3 once-only "just missed" footnotes. Counting unit: underlying events, not articles.

**Ledger dedupe (suppressed as already reported): 11 candidate mentions across 10 distinct ledger events, including 3 repeat "Just missed the window" footnotes** — suppressed: weight-space programming (HN #1, 2,750 pts; reported 11-02 17:00), Anthropic amended S-1 cluster (RSI-threshold language, $1.7B quarterly payroll, Q3 $17B/$895B — all from the already-reported filing), Claude Code game-clone confirmation (update already reported 11-02 17:00), Marvell–Lumentum $115B confirmation (11-02 12:00), SUSE ~10% layoffs (11-02 17:00), vLLM v0.33.0 (11-02 07:00), Momentic "Mo" QA agent (11-02 17:00), plus repeats of once-footnoted items: Oracle $20B filing, Altman/DRAM "your RAM is efficient" re-coverage, Kimi K3.1 Pro weights-by-Nov-4. Four further in-window-surfacing items were excluded as out-of-window events rather than as ledger repeats: Revolut self-hosted GLM (AI Engineer NYC talk, ~Oct 29), Hydrogen-1.0, NVIDIA Auto Ontology, Zyphra ZHA — the last three footnoted once here.

**Dead ends / limitations**
- Techmeme river-only items carry no article URLs. Two were deliberately dropped rather than slug-guessed: **Apple's Q4 call** (John Ternus calling memory costs "unprecedented" and forecasting further increases; inventory $14.54B, +154% YoY) — the 4:05 PM ET release sits pre-window and the river item could not be resolved to a fetchable primary within budget — and **Palantir's Q3 beat** (revenue +96% to $2.31B, FY guide ~$8.53B), where only river attribution was available.
- The Information's briefing page for the China guardrails story returns a signup shell; the item is carried on the fetched Bloomberg page (timestamped Nov 3, 05:24 UTC) with The Information cited as the second source.
- techxplore.com (Nvidia/FT syndication) is Cloudflare-gated — one strike, abandoned; Crypto Briefing and TrendForce substituted as fetchable sources.
- The FT's own first-publication minute could not be pinned (Nov 2 late-UK vs Nov 3); the Nvidia 51% item is anchored on fetched Nov 3 corroboration and flagged here rather than presented as exact-minute verified.
- Hugging Face's "newest models" river was pure noise at 07:00 ET (LoRA adapters, RL run dumps) — no in-window flagship open-weight release was found there; SGLang/vLLM/llama.cpp/Ollama/OpenClaw/Claude-Code/Codex were enumerated from GitHub APIs directly.
- Indexes used: Techmeme, Hacker News front page (Algolia API), The Hacker News, The Decoder, MarkTechPost, The Neuron, The Open Weights, AI Weekly, TrendForce. All responsive. No index was down.
