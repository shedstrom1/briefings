# Microsoft's AI beats doctors on diagnosis; Anthropic hands Agent Skills to the Linux Foundation

**Window:** 2026-10-22 12:00 → 17:00 EDT (this run covers the previous fire at 12:00; fire time 17:00 EDT)

## TL;DR

- Microsoft AI published peer-reviewed work in *Science* (The Verge, 2:00 PM UTC / 10:00 AM ET embargo, coverage 2:00 PM ET): its multi-agent Diagnostic Orchestrator (MAI-DxO), powered by GPT-5, hit ~83% diagnostic accuracy on NEJM cases versus ~36% for physicians — and a hospital trial is planned within months.
- At AGNTCon + MCPCon North America (San Jose, Oct 22), Anthropic donated the Agent Skills standard to the Agentic AI Foundation, and the Linux Foundation announced three more contributions: Open Responses (OpenAI), OpenEnv (Hugging Face), OpenSandbox (Alibaba) — AAIF now hosts 10 projects.
- llama.cpp shipped five builds inside the window (b11821–b11827), headlined by a fix for DFlash speculative-draft crashes on qwen4exp targets and Intel XMX multi-column GEMM refactor.
- Canva released Canva Craft, its first in-house image-generation model, trained on human-designed templates rather than photographs.
- Quiet day for AI security inside the window — the day's big METR/Redwood red-team report on OpenAI's monitoring system landed ~11:00 AM ET, one hour early (footnoted below).

## Models and local inference

**Microsoft's Diagnostic Orchestrator outperforms physicians in a *Science* paper** — event date: Thursday, October 22, 2026
Microsoft AI published research in *Science* showing its MAI-DxO system — a panel of coordinated AI agents powered by OpenAI's GPT-5 that reasons through symptoms, orders tests, and converges on a diagnosis — achieved roughly 83% accuracy on cases drawn from hundreds of *New England Journal of Medicine* case reports, versus ~36% for generalist physicians with references and ~22% without. On emergency-department cases from Beth Israel Deaconess Medical Center's admissions database it scored 80% versus 47% for human physicians, at a lower estimated cost. Microsoft built a new benchmark, SDBench, for step-by-step diagnostic reasoning and says it will open-source it; CEO Mustafa Suleyman says clinical validation trials at Beth Israel Deaconess should "get underway in the next couple of months," and projects systems "indistinguishable from human performance" within a couple of years. The peer-reviewed paper predates GPT-6 Astra, Muse Wonder Full, Gemini 4 Argon and Opus 5.5, and was built when GPT-4.1 was state-of-the-art — the team says stronger base models don't erase the multi-agent orchestration benefit.
*Why it matters:* This is the strongest published evidence yet that agentic orchestration — not raw model size — drives real-world task accuracy, and it lands on a benchmark (SDBench) Microsoft plans to release. The orchestration-layer result is directly transferable to open-weight stacks: an agent harness over a capable local model can outrank the model alone.
*Local inference:* Not directly — MAI-DxO is a served GPT-5 system — but the finding that multi-agent orchestration preserved its advantage across model generations supports harness-first local agent designs.
Source: "Microsoft says its AI tool is better than doctors at diagnosing conditions," The Verge, Oct 22, 2026, 6:00 PM UTC — https://www.theverge.com/ai-artificial-intelligence/1016690/suleyman-microsoft-diagnostic-orchestrator-science-medical-superintelligence
Source: "Building Medical Superintelligence," Microsoft AI (primary, published Thursday) — https://microsoft.ai/news/building-medical-superintelligence/

**llama.cpp ships five builds: qwen4exp DFlash drafter fix, Intel XMX refactor, MiniCPM-OCR** — event date: October 22, 2026 (builds 13:02–15:36 ET)
Builds b11821–b11827 all landed inside the window. The headline fix for local users is b11822's correction of qwen4exp layer inputs for DFlash drafters (#30927): the hyper-connection residual was published in the wrong tensor shape, so evaluating a DFlash speculative draft over a qwen4exp target aborted on the first request — the fix collapses the streams with a plain mean, and the PR was co-developed against Qwen3.8-Flash-Next-GGUF on an M5 Max MacBook. b11821 refactored multi-column GEMM on Intel XMX matrix engines (#29864), b11823 optimized GLM5 input handling (#30932), b11826 added MiniCPM-OCR 1.0 support to mtmd (#30979), and b11827 added an Adreno depthwise conv2d kernel for OpenCL (#30900).
*Why it matters:* The qwen4exp/DFlash fix restores speculative decoding on that architecture — directly relevant to keeping Flash-family GGUFs fast on single-GPU rigs — while MiniCPM-OCR and Adreno kernels extend llama.cpp's vision-OCR and mobile/edge coverage.
Source: llama.cpp releases b11821–b11827, GitHub, published 2026-10-22 17:02–19:36 UTC — https://github.com/ggml-org/llama.cpp/releases

**Canva ships Canva Craft, its first in-house image model** — event date: Thursday, October 22, 2026
Canva released Canva Craft, its first self-built image-generation model, replacing reliance on third-party image APIs. It works in two stages — a planning pass that lays out spatial relationships, scale, hierarchy and text content, then a render pass — and was trained on Canva's library of human-designed templates (posters, social posts, infographics, typography-heavy layouts) rather than photographs, which the company says yields legible in-image text and cleaner partial edits. It is available to all users via Dream Lab and to developers through the Canva API, with planned support inside third-party chatbots (ChatGPT, Gemini) via Canva's MCP, and cheaper Craftfast/Craft-ultrafast variants coming. Researchers from Canva's Leonardo.ai acquisition contributed.
*Why it matters:* A vertical app company owning its generation stack is the unit-economics story of late 2026 — image generation shifts from an API bill to an internal capability, and design-native training data is a differentiator general models don't have.
Source: "Canva ships its first in-house image model to take on Google and OpenAI," ZGLG (summarizing TechCrunch and AFR reporting), 2026-10-22 — https://zglg.work/en/ai/news/2026-10-22-canva-ships-its-first-in-house-image-model-to-take-on-google-and-openai

## Agent tools

**Anthropic donates Agent Skills to the Agentic AI Foundation; four projects join in one day** — event date: Thursday, October 22, 2026 (AGNTCon + MCPCon North America, San Jose)
At AGNTCon + MCPCon North America, Anthropic donated Agent Skills — the SKILL.md-based open standard for giving agents procedural knowledge — to the Linux Foundation's Agentic AI Foundation, the same neutral home that received MCP. Anthropic says 70+ platforms (including ChatGPT, Cursor, GitHub Copilot and VS Code) have integrated it, with ~1 million skills published and nearly 280 million installs; the gift includes the name, logo, GitHub organization and agentskills.io domain, with engineers from Anthropic, Google and OpenAI maintaining it as individuals (lead maintainers David Soria Parra and Mahesh Murag). The Linux Foundation simultaneously announced three further contributions — Open Responses (OpenAI's multi-provider Responses-API interoperability spec), OpenEnv (Hugging Face's containerized RL-environment framework, backed by Microsoft, Meta-PyTorch, Modal and others) and OpenSandbox (Alibaba's general-purpose agent sandbox with multi-language SDKs and Docker/Kubernetes runtimes) — bringing AAIF to 10 hosted projects in its first year.
*Why it matters:* Skills, model-interop, RL environments and sandboxing all landing under one vendor-neutral roof in a single day is a consolidation moment for the agent stack — the governance layer that MCP normalized last year now covers the entire agent lifecycle, with security guidance for skill authors on the roadmap.
*Local inference:* OpenSandbox (Docker/K8s runtimes) and OpenEnv (containerized, MCP+Gym training interface) are self-hostable infrastructure — relevant to running and evaluating local agent fleets without cloud lock-in.
Source: "Donating Agent Skills to the Agentic AI Foundation," Anthropic, Oct 22, 2026 — https://www.anthropic.com/news/donating-agent-skills-to-the-agentic-ai-foundation
Source: "Agentic AI Foundation Announces Four New Projects: Agent Skills, Open Responses, OpenEnv, and OpenSandbox," Linux Foundation press release, 22 October 2026 — https://www.linuxfoundation.org/press/agentic-ai-foundation-announces-four-new-projects-agent-skills-open-responses-openenv-and-opensandbox

## AI security and governance

No verified items inside this window. The day's major governance story (METR/Redwood's red-team of OpenAI's monitoring stack) published ~11:00 AM ET — see "Just missed the window."

## Also noted

- **ModelCompare's cross-model survey: 12 frontier models converge on the same "controversial take"** (2026-10-22, 70 points on HN in-window) — asked for their most controversial honest view of humans, 29 of 35 answers across 12 models from ten labs (GPT-6.1 Sol, Grok 4.7, DeepSeek V4 Pro, Kimi K3, Claude Opus 5.5, Mistral Large 4, and others) landed on the same thesis: humans are loyal to belonging over truth. — https://www.modelcompare.ai/blog/ai-models-controversial-take-on-humans

## Just missed the window

- **METR + Redwood Research red-teamed OpenAI's internal misalignment monitoring** (published Thursday morning, ~11:00 AM EDT — one hour before this window): the three-week July–August pilot found an exploit that could have kept hundreds of recent agent actions unmonitored, estimates that 75–94% of eligible Responses-API traffic was monitorable at the time, and found two client-controllable headers that disabled monitoring; OpenAI says all production-affecting vulnerabilities are patched. — https://metr.org/blog/2026-10-22-red-teaming-openai-agent-monitoring/ (also: https://www.cnbc.com/2026/10/22/metr-redwood-research-open-ai-test.html)
- **Microsoft Research announced the proof of the 31-year-old KLS conjecture** via a human-directed agentic process (MSR blog October 21): agents explored 100+ approaches before the researchers closed the argument. — https://www.microsoft.com/en-us/research/blog/solving-a-31-year-old-grand-challenge-in-mathematics-the-kls-conjecture/
- **1Password passed $500M ARR (up from $400M in Nov 2025) and launched 1Password for Agents** for securing non-human identities — Thursday morning, ~9:50 AM ET. — https://betakit.com/1password-tops-500-million-usd-arr-on-the-back-of-accelerating-ai-demand/
- **OpenAI, Anthropic and Nvidia posted record Q3 federal-lobbying quarters** per new disclosures (The Verge, ~11:50 AM ET). — https://www.theverge.com/policy/1016585/openai-anthropic-nvidia-lobbying-records

## Coverage notes

- **Verified in-window: 5 items** (3 lead fully read + Canva Craft + ModelCompare), plus 4 once-only "just missed" footnotes above.
- **Ledger dedupe: 9 candidates suppressed as already reported** despite fresh re-coverage today: NVIDIA OpenShell (HN front page this window; reported 10-21), DeepSeek $15B round, HF Carbon-X, Palantir Maven kill-switch, Barclays 5,000 cuts, Walmart Agent Gateway, Microsoft ARTEX/Storm-0940, PHP2Shell (AWS MadPot + Cloudflare volume stats), OpenAI-on-Google-Cloud.
- **Rejected as stale, not new:** Apple×Meta "Muse on Mac" (CNBC re-promotion of the Oct 15 story), llmgateway's "Qwen3.6 27B, Oct 21" (Hugging Face repo shows creation 2026-04-21 — timeline mislabeled), OpenAI's ~$30B UAE/Coatue round (CNBC, yesterday evening).
- Indexes used: Techmeme river, AIBriefs, LLM-Gateway timeline, HN Algolia (11 in-window stories >40 pts), GitHub releases API (llama.cpp, vLLM, Ollama, SGLang). No new vLLM/SGLang/Ollama releases in-window. No index down.
