# AI News Briefing — MiniMax Ships M3.1 Weights as Beijing Clears AI Exports; Treasury Tightens Outbound AI Capital

**Window: October 22, 2026 5:00 PM → October 23, 2026 7:00 AM ET** (swept back from the 5:00 PM Thursday fire)

**TL;DR**

- MiniMax published the MiniMax-M3.1-Flash open weights on Hugging Face: a 428B-A23B multimodal MoE, 252.4 GB across 48 shards, NVFP4 quantization-aware training — real downloadable weights, but under a community license with a $20M-revenue authorization line.
- Chinese state media says DeepSeek and MiniMax are the first vendors registered and approved to open-source and license AI models overseas under Beijing's October 16 export-control proposal; MOFCOM has carved out APIs and already-published open-source releases.
- The US Treasury proposed expanding the Outbound Investment Security Program to cover AI ("superintelligence"), supercomputing, HBM, hypersonics and biotech investments in China, Russia, North Korea, Iran and Cuba.
- Anthropic says Claude produced machine-checked formal proofs that 239 fuzz-test entry points across 123 open-source libraries can never crash — proved at the RISC-V assembly level, the first large-scale AI-driven formal verification of production software.
- The WSJ detailed why OpenAI fired three safety researchers: it suspected them of leaking closely guarded internal model designs through disappearing-message Signal chats.

---

## Models and local inference

### MiniMax releases M3.1 Flash open weights: 428B MoE, 252.4 GB, NVFP4 training — with a $20M licence line
**Event date: Friday, October 23 (weights landed on Hugging Face overnight Beijing time).** Fulfilling the promise from the model's October 19 launch, MiniMax published `MiniMaxAI/MiniMax-M3.1-Flash` with the full model card: ~428B total / 23B active parameters, native multimodal inputs (text, image, video), a 1M-token input window with up to 524,288 output tokens, sparse attention throughout the first layers, and a 2.3 GB DSpark speculative-decoding drafter. The download is 252.4 GB in 48 safetensors shards. Two details matter more than the size: the routed experts and sparse-attention KV path are trained in NVFP4 (shared experts MXFP8, query path FP8), which vLLM's day-0 recipe turns into a 43.75% smaller KV cache and 76 concurrent 256K-token sequences on four B300s versus 28 for M3's FP8 KV — a 2.7x concurrency gain. And the license is MiniMax's own "minimax-community" terms, filed on HF as *license: other*: free for non-commercial use, commercial use requires prominent "Built with MiniMax M3" attribution, a one-time notice under $20M annual revenue, and MiniMax's prior written authorization above it.
*Why it matters:* This is the second Chinese lab this week to put frontier-adjacent agentic weights on the Hub (after DeepSeek V4.1-Flash's MIT release) and it pressures the "open weights = free" assumption — the license is the procurement surprise. Benchmarks are launch-grade: 73.7% DeepSWE v1.1, 82.2 CyberGym, 61.7 OSWorld 2.0.
The trickle-down to watch is the NVFP4 quantization-aware training + sparse-attention combo and the DSpark drafter — exactly the techniques that later land in KTransformers/unsloth/ATOM integrations MiniMax lists as "in progress" for smaller hardware. *(Update on previously reported: MiniMax-M3.1-Flash launch, ledger 2026-10-19.)*
Sources:
- Hugging Face model card — MiniMaxAI/MiniMax-M3.1-Flash (fetched October 23, 2026): https://huggingface.co/MiniMaxAI/MiniMax-M3.1-Flash
- OrcaRouter, "MiniMax M3.1 Flash Weights: 428B and a $20M Licence Line" — October 23, 2026: https://www.orcarouter.ai/blog/minimax-m3-1-flash-open-weights
- tokens.bd, "MiniMax M3.1 Flash Weights Are Out: Self-Host Guide" — October 23, 2026: https://tokens.bd/blog/minimax-m31-flash-open-weights-self-host-guide

### DeepSeek and MiniMax become the first companies cleared to export AI models under Beijing's new rules
**Event date: Friday, October 23 (Thursday evening Beijing time, via CCTV-affiliated Yuyuan Tantian).** China's Ministry of Commerce and Ministry of Science and Technology proposed export controls on 23 AI technologies on October 16 — covering LLMs, AI agents and computing clusters — with registration requirements effective immediately. DeepSeek and MiniMax have now completed that registration and are approved to proceed with open-source releases and overseas licensing. The state-media account said "the vast majority of artificial intelligence technologies will still be freely exportable," and MOFCOM has clarified that overseas access via APIs, official websites and apps is not a regulated export, with expedited "automatic licensing" for open-source technology. The timing is pointed: days earlier, DHS ordered DeepSeek stripped from US federal civilian IT, and the account explicitly contrasted Beijing's "encouraging openness" stance with Washington's restrictions.
*Why it matters:* For anyone building on Chinese open weights, this converts last week's "is China about to choke off its own open-source strategy?" panic into a licensing regime that the two flagship labs have now navigated — with DeepSeek's V4.1 Flash upgrade and DeepInfer engine and MiniMax's M3.1 weights all cited as post-registration proof points. The regime risk is now procedural (registration, timing controlled by MOFCOM) rather than binary.
Sources:
- Reuters, "China's DeepSeek, MiniMax win approval for AI global use, state media-affiliated entity says" — October 23, 2026: https://www.reuters.com/business/media-telecom/chinas-deepseek-minimax-win-approval-ai-global-use-state-media-affiliated-entity-2026-10-23/
- SCMP, "DeepSeek, MiniMax secure licences to deploy AI overseas under China export rules" — October 23, 2026: https://www.scmp.com/tech/policy/article/3371880/deepseek-minimax-secure-open-source-licenses-days-after-new-china-export-rules-state-media
- Bloomberg, "DeepSeek, MiniMax Win Approval for Global Use, State Media Says" — October 23, 2026: https://www.bloomberg.com/news/articles/2026-10-23/deepseek-minimax-win-approval-for-global-use-state-media-says

---

## Agent tools

### Anthropic and PE firm Hg will launch a European AI-consultancy to embed engineers inside businesses
**Event date: Friday, October 23 (Financial Times, ~6:40 AM ET).** Anthropic has agreed to back an independent consultancy with London-based private-equity firm Hg, targeted to launch by the end of 2026 with hubs in London and Berlin. The new company — funded by Anthropic through balance-sheet capital with Hg participating via HgCapital Trust — places AI consultants and engineers inside client organizations to run agent deployments, targeting software companies and white-collar sectors, and will also serve Hg's portfolio. It puts Anthropic directly against Accenture, Deloitte, EY, KPMG and PwC in the enterprise AI-services market.
*Why it matters:* Frontier labs are verticalizing agent adoption: instead of only selling API tokens, Anthropic is selling guaranteed deployment capacity, and doing it in Europe where sovereignty concerns most slow enterprise agent rollouts. Expect the same playbook to spread — the model layer monetizing the integration layer.
Sources:
- Financial Times, "Anthropic strikes deal to launch European consultancy" — October 23, 2026: https://www.ft.com/content/df70bf20-e809-48d0-8485-f8f514a7fbc4
- Techmeme item (6:40 AM ET, October 23, 2026): https://www.techmeme.com/261023/p7

---

## AI security and governance

### US Treasury proposes banning Americans' investments in Chinese and Russian AI, supercomputing and HBM
**Event date: Thursday, October 22 (NPRM issued; AP's report at ~9:10 PM ET).** Treasury issued a Notice of Proposed Rulemaking implementing the COINS Act (codified December 2025) that expands the Outbound Investment Security Program beyond semiconductors, quantum and AI-in-China to cover prohibitions or notification requirements for investments in "superintelligence"/AI systems, supercomputing, hypersonic systems, biotechnology, and advanced semiconductors including high-bandwidth memory — now aimed at China, Russia, North Korea, Cuba and Iran. It applies to investments above $50,000, excludes many financial investments such as passive stock purchases, and takes comments until December 7, 2026. Beijing's foreign ministry warned the measures "serve no one's interests."
*Why it matters:* Combined with China's export registrations the same week (above), both governments are now formally treating AI capability — not just chips — as a controlled strategic asset, in both directions. VC and corporate-development teams touching Chinese labs (including open-weight-adjacent startups) face the next layer of the decoupling; Treasury's use of "SI" shows the definition itself is becoming policy.
Sources:
- AP, "US Treasury proposes a rule to crack down on high-tech investments in China, other adversaries" — October 22, 2026: https://apnews.com/article/treasury-china-tech-investments-rule-b742c292ff8eb19e6a4c8cf03d4d66b9
- Treasury press release, "Treasury Issues Proposed Rule to Implement Comprehensive Outbound Investment Security Program": https://home.treasury.gov/news/press-releases/sb0667/

### Anthropic: Claude formally proved 123 open-source libraries crash-free at the RISC-V assembly level
**Event date: late Thursday, October 22 (Anthropic research post; Techmeme 5:15 AM ET Friday).** Working with formal-methods startup Theorem, Anthropic's Frontier Red Team had Claude convert OSS-Fuzz fuzz tests into formal specifications and prove — on compiled RISC-V binaries — that 239 entry points across 123 real open-source libraries never crash for any input under 64 MB under modeled conditions (single-threaded static binary, fixed memory budget, modeled Linux OS). Roughly 180 million lines of Rocq proof were machine-checked by the Rocq kernel in a few weeks. Claude also found and patched bugs that had survived years of fuzzing. Anthropic is publishing a subset of the proofs plus its RISC-V and OS semantics; Theorem will run the pipeline for other OSS projects and ships a Claude skill for self-serve verification.
*Why it matters:* The classic counterweight to AI-amplified offense (see ARTEX, PHP2Shell): proof-writing that took 20 person-years for seL4 is becoming an agent workflow. The caveats are real — proven properties are modeled, not intent — but "verification as a CI step at fuzzing-scale economics" just got its strongest public demo, and it lands on binaries, not source.
Sources:
- Anthropic Research, "LLMs are making formal software verification feasible at a much larger scale" — October 22, 2026: https://www.anthropic.com/research/formal-verification
- Unite.AI, "Anthropic Says Claude Proved Security Properties Across 123 Libraries" — October 22/23, 2026: https://www.unite.ai/anthropic-says-claude-proved-security-properties-across-123-libraries/

### WSJ: OpenAI suspected fired safety researchers of leaking internal model designs via disappearing Signal chats
**Event date: Thursday, October 22, 9:00 PM ET.** The Wall Street Journal reports that OpenAI's security team summoned three safety researchers last month, made them unlock their personal phones, and found chats — some with disappearing messages enabled — with an external AI-safety researcher; the company suspected them of leaking the internal designs of its AI models, among its most closely guarded secrets. The three were fired a week later in simultaneous 2 PM meetings. Fired researcher Tomek Korbak said he was told it was "a breach of trust because I interacted with a third party through unsanctioned channels."
*Why it matters:* The October 1 firings were reported at the time; this is the first detailed account of *what* was allegedly leaked — model internals — and how (Signal with vanishing messages). It sharpens the chilling-effect debate over internal transparency work and tells outsiders that OpenAI now treats architectural detail as crown-jewel IP.
Sources:
- WSJ, "The Signal Chats That Cost OpenAI Researchers Their Jobs" — October 22, 2026 9:00 PM ET: https://www.wsj.com/tech/ai/the-signal-chats-that-cost-openai-researchers-their-jobs-594c5853

### PHP2Shell update: Japan stands up AI red-team unit as SoftBank suspends disaster-alert system for patching
**Event date: Friday, October 23 (Tokyo).** *(Update on previously reported PHP2Shell items, ledger 2026-10-21/22.)* Japan said it will set up a team to simulate AI-driven cyberattacks against national systems — industry minister Ryosei Akazawa: "Defenders also need to make use of AI and respond proactively, staying one step ahead." SoftBank said it is suspending its mobile emergency-alert system (earthquake, tsunami, evacuation notifications) during maintenance to address a PHP vulnerability; JR East is phasing service restorations while the Ministry of Finance, National Tax Agency and Meteorological Agency keep some online services down. Trend Micro's Eva Chen argued Japanese firms are now attractive targets precisely because AI removes the language barrier.
*Why it matters:* The most concrete national-response signal yet from the PHP2Shell week — an AI-attack-simulation unit and a carrier pulling its disaster-alert pipeline offline to patch show how far "critical infrastructure runs on PHP" consequences now reach.
Sources:
- Reuters, "Japan to simulate cyberattacks as PHP flaws roil global internet" — October 23, 2026: https://www.reuters.com/business/media-telecom/japan-simulate-cyberattacks-php-flaws-roil-global-internet-2026-10-23/

---

## Also noted

- **OpenAI cut its valuation target to $1.25T pre-money** (from $1.4T pitched earlier this month) as it nears an up-to-$30B round led by UAE investors and Coatue — FT reported the cut at 6:35 PM ET Thursday; CNBC's underlying report (4:15 PM ET, Oct 22, updated 4:41 PM) says signatures are targeted by end of next week, positioned as an "opportunistic" bridge to a 2027 IPO, with a parallel UAE compute deal in negotiation. https://www.cnbc.com/2026/10/22/openai-funding-round-uae-investors.html
- **llama.cpp shipped 4 in-window builds (b11828–b11836)** (Oct 22 23:25Z → Oct 23 08:20Z): server rerank-placeholder fix (#30864), CANN const-qualifier consistency (#30933); builds b11824–b11827/b11830–b11834 skipped. https://github.com/ggml-org/llama.cpp/releases

## Just missed the window

- **GitLab's CVSS 10.0 unauthenticated RCE** — CVE-2026-110264, an integer overflow in the bundled Onigmo regex compiler affecting every self-managed CE/EE release since 7.0; out-of-band patches shipped **October 21** (fixed: 18.11.13, 19.0.10, 19.1.9, 19.2.9, 19.3.6, 19.4.3, 19.5.1). Coverage spiked in-window (The Register Oct 22), but the disclosure predates the window; as of Friday morning it is not in CISA KEV and no active exploitation has been reported. https://www.theregister.com/security/2026/10/22/max-severity-gitlab-bug-allows-remote-code-execution-over-12-years-of-releases/5304854

## Coverage notes

- **Verified in-window: 7 full items + 2 also-noted lines + 1 footnote**, every one from a fetched page carrying an in-window timestamp (or, for the FT/Anthropic late-night posts, a dated primary page plus Techmeme river timestamp); lead stories read in full.
- Dead ends / limitations: FT and Bloomberg lead items are paywalled (corroborated via fetched Reuters/AP/Techmeme pages and attributed accordingly); WSJ is snippet-paywalled (lede fully read); Techmeme river items were resolved to fetchable article URLs per policy. No index outages.
