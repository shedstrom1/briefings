# AI News Briefing — overnight sweep to Wednesday 07:00 ET
**Window: Tuesday, November 17, 2026 17:00 EST → Wednesday, November 18, 2026 07:00 EST (14 hours)**

**TL;DR**
- Nvidia's first earnings call without Jensen Huang ends H200 sales into China outright, while interim CEO Colette Kress explicitly reaffirmed the open-weight strategy and the $12.9B Hugging Face acquisition — the open-model ecosystem's biggest state backer is unchanged.
- Microsoft's `agent-framework` PyPI package was compromised: versions 1.26.1 and 1.26.2 shipped 33 minutes apart on Tuesday and installed a credential stealer that ran on every Python start, harvesting cloud identity tokens and AI API keys.
- "Java4Shell" (CVE-2026-114938), a memory-corruption bug in OpenJDK's AVX-512 Base64 decoder that gives pre-auth RCE on Jira, Jenkins, Confluence, Keycloak and more, landed with a CISA alert and an AI-assisted discovery story: one hour to find, two hours to first exploit, under $200 in tokens.
- On the inference side, Dell says enterprise token bills of $10,000–$20,000 per person per month are pushing pilots to open-weight models on owned hardware, and SambaNova is selling air-cooled decode racks into brownfield telco and colo space because new AI data centers can't be built fast enough.

---

## Models and local inference

### Nvidia ends China H200 sales — and doubles down on open weights and Hugging Face
**Event: Tuesday, November 17, ~5:00–6:30 PM ET (Q3 FY27 earnings call)**

On Nvidia's first earnings call since Jensen Huang's resignation, interim CEO Colette Kress said the company will no longer sell H200 chips in China, ending its participation in the Huang-Trump arrangement that routed shipments through the US so Washington could collect a 25% tariff. The disclosure came the same day Nvidia stood up a board-level National Security and Export-Control committee, and days after BIS rules that bar advanced US chips from foreign data centers serving Chinese customers — rules Jefferies pegged at $44–62 billion of possible data-center revenue impact next year. Kress countered that Vera Rubin backlog depth makes the export rules revenue-neutral for now. Critically for open-weight users, she reaffirmed the pending $12.9B Hugging Face acquisition as on track ("we will scale its platform and make intelligence more open"), called Nemotron the most widely used US open model on OpenRouter, and cited a 35x year-over-year jump in weekly token volume on OpenRouter — including 3.5x in the past three months — as evidence open models are accelerating adoption.

**Why it matters:** Huang was the single loudest corporate advocate for open weights; his exit had the local-inference ecosystem bracing for a pullback (Hugging Face's CEO called it "a sad day"). The call says the opposite: the acquisition, Nemotron and the open-model pitch survived the leadership change. The China exit is the more consequential number — it removes the last legal H200 pathway and, combined with the new remote-access rules, pushes Chinese labs further toward domestic silicon and overseas rental capacity.

- Source: "Nvidia is abandoning China after Jensen Huang's ouster," TechCrunch (Tim Fernholz), Nov 17, 2026 — https://techcrunch.com/2026-11-17/nvidia-is-abandoning-china-after-jensen-huangs-ouster/
- Source: "Nvidia says its open source AI strategy remains intact after Jensen Huang's exit," TechCrunch (Sarah Perez), Nov 17, 2026 — https://techcrunch.com/2026-11-17/nvidia-says-its-open-source-ai-strategy-remains-intact-after-jensen-huangs-exit/
- Source: "Business as usual for Nvidia as it beats expectations in first earnings call after Huang's resignation," SiliconANGLE (Mike Wheatley), Nov 17, 2026 23:49 UTC — https://siliconangle.com/2026-11-17/business-as-usual-for-nvidia-as-it-beats-expectations-in-its-first-earnings-call-after-jensen-huangs-resignation/

### Token sticker shock is pushing enterprises from frontier APIs to open-weight "AI factories"
**Event: Tuesday, November 17, 2026 (SC26)**

Dell's senior director of AI solutions Brad Maltz told theCUBE at the SC26 supercomputing conference that enterprise AI pilots are stalling on cost and sovereignty rather than on GPU supply, with frontier-lab token spend hitting "10,000, 20,000 dollars per person per month" once coding assistants scale. That is steering deployments toward open-weight models enterprises can fine-tune and run on-premises — including US options such as Nvidia's Nemotron — and toward disconnected sovereign builds that keep models next to the data. Dell also launched an AI Ecosystem Program to validate software partners on its AI Factory, saying about 60 more partners have applied or are under evaluation.

**Why it matters:** This is the demand-side reason the open-weight release cadence keeps accelerating, and it comes from a vendor with direct line of sight to enterprise deals. "Do we have enough diversity with the open weights to meet enterprise use cases?" is now the buying question, not "can we get GPUs?"
- Source: "Token sticker shock pushes enterprises from frontier labs toward open-weight AI factories," SiliconANGLE (Kelly Knight), Nov 17, 2026 23:27 UTC — https://siliconangle.com/2026-11-17/gpus-alone-won-t-carry-enterprise-ai-pilots-production-dellatsc26/

### SambaNova sells air-cooled inference racks into brownfield telco and colo space
**Event: Wednesday, November 18, 2026 (SC26)**

With new AI data centers gated by power, capital and permitting, SambaNova is positioning its fifth-generation SN50 — a chip aimed specifically at the decode phase, where data movement is the bottleneck — in racks that stay air-cooled and light enough for existing facilities. VP of core product Anton McGonnell said the industry is "encumbered by the velocity of data center buildouts," with 18–24 month lead times on power and cooling gear, and that dense GPU racks can demand reinforced floors and 120–250 kW per rack. At SC26 the company is demonstrating Intel's D-Series GPUs for prefill paired with a SambaRack SN50 for decode in an air-cooled heterogeneous setup; SambaNova was valued at $11B in July.

**Why it matters:** Inference economics are splitting prefill from decode, and the winners will be the parts that fit buildings that already exist.
- Source: "Stalled data center buildouts send inference chips hunting for space in telco and colo sites," SiliconANGLE (Jonathan Anthony), Nov 18, 2026 — https://siliconangle.com/2026-11-18/ai-data-centers-delays-push-inference-chips-existing-sites-sc26/

### Suno's new Visuals feature runs on a MiniMax-based video model
**Event: Tuesday, November 17, 2026**

Suno launched Visuals, which generates images and 15-second video clips that auto-sync and lip-sync to Suno songs, with a reference image usable as a start or end frame, a clip-editing timeline, and separate "Visuals credits" that cost extra on top of song credits. The video model is the H3 Move model, based on H3 from MiniMax, which Suno says it optimized with additional text and visual filters blocking the likeness of specific artists, public figures and copyrighted works. Visuals is live on web, iOS and Android.

**Why it matters:** The commercial video model layer is consolidating around a small number of bases — a Western music platform shipping a MiniMax-derived model is another data point on how much of the creative AI stack is now built on Chinese open-ecosystem bases, with the differentiation moved into filters, credits and workflow.
- Source: "Suno now lets you make AI videos for your AI music," The Verge (Jess Weatherbed), Nov 18, 2026 11:16 UTC — https://www.theverge.com/ai-artificial-intelligence/1036415/suno-visuals-ai-generated-video-images-feature
- Corroborating first report: Bloomberg via Techmeme river, Nov 17, 2026 5:05 PM ET.

---

## Agent tools

### Mozilla AI launches Otari, an open-source gateway for open-weight and frontier models
**Event: surfaced Wednesday, November 18, 2026 (~6:00 AM ET on Hacker News)**

Mozilla AI's Otari puts open-weight and frontier models behind one OpenAI- and Anthropic-compatible API, aimed squarely at coding agents: per-person/team/agent API keys with spend caps, a per-request activity view (tokens, cost, errors), automatic provider fallback, and server-side tools — web search, web fetch, code execution — made available to any model including open-weight ones. It runs hosted on otari.ai with a 4.5% fee on top-ups or self-hosted as open source, and lists Mistral, Fireworks, Together, Nebius, OVHcloud, Bedrock and DeepInfra as providers with setup guides for Claude Code, Codex, opencode, Junie and Pi.

**Why it matters:** Routing, budgets and observability are becoming the layer where open-weight models actually get adopted; a Mozilla-backed OSS gateway is a credible non-hyperscaler option, and server-side tools for open-weight models is the feature that makes them usable in agent harnesses. Note: the launch itself is not independently date-stamped outside today's Hacker News posting, so treat the timing as provisional.
- Source: Otari product site (Mozilla AI) — https://otari.ai/
- Source: Hacker News submission, Nov 18, 2026 11:00 UTC — https://news.ycombinator.com/item?id=50544738

### Also noted (agent data/tooling, minor)
- Wherobots introduced "Waldo," a natural-language agent that discovers spatial data sources, generates queries, runs analysis and visualizes results — aimed at cutting specialist programming work in geospatial pipelines (SiliconANGLE, Nov 18, 2026 11:00 UTC; item carried in the site's feed: https://www.siliconangle.com/feed/).
- Inference-engine waves in-window (GitHub): llama.cpp took 19 commits including expanded OpenVINO model/NPU support (#32172), CUDA MMQ for MoE on V100 (#32504), Vulkan bfloat16 op coverage (#32202), a Pascal DP4A MMQ tile bump (#31352) and llama-ui 0.4.0 plus `npx @ggml-org/llama-chat`; vLLM merged 53 commits including a HY4 Flash recipe (#68295) and DeepSeek V4/V4.1 vision Triton kernels (#68097); SGLang merged 62 including serving Kimi-K3.1 on MI350X/MI355X (#51000) and an inkling2 MegaMoE/CuTe-DSL kernel set (#51048). Primary: https://github.com/ggml-org/llama.cpp , https://github.com/vllm-project/vllm , https://github.com/sgl-project/sglang

---

## AI security and governance

### Microsoft's `agent-framework` PyPI package compromised — credential stealer ran on every Python start
**Event: malicious releases published Tuesday, November 17, 2026; disclosure consolidated overnight into Wednesday, November 18**

Attackers pushed versions 1.26.1 and 1.26.2 of `agent-framework` — the meta-package for Microsoft Agent Framework, the open-source Python framework for building and orchestrating AI agents — 33 minutes apart on Tuesday. Both versions install Python startup hooks that silently collect credentials, environment data and cloud identity tokens, then exfiltrate to a `webhook[.]site` endpoint; the targeted secrets reportedly include Azure, Azure DevOps and GitHub Actions credentials alongside AI API keys. Both releases were quarantined on PyPI and no longer appear in the package's release history; how the attacker obtained publishing access is still unclear. Reports came from Aikido, Chainguard, Endor Labs, OX Security, SafeDep, Socket, StepSecurity and Google-owned Wiz.

**Why it matters:** This is the exact trust path agent tooling depends on — a pip install in an agent-orchestration stack that harvests the cloud and model credentials that agents operate with. It follows the LiteLLM and Fireworks incidents, and Microsoft's own agent-framework 1.26.0 shipped only five days earlier, so the delta between a legitimate release and a hostile one was days and two patch numbers. Verify provenance, pin versions, and assume anything installed unpinned this week needs credential rotation.
- Source: "Attackers Compromise Microsoft's Agent Framework PyPI Package to Steal Cloud Credentials," The Hacker News, Nov 18, 2026 02:45 ET — https://thehackernews.com/ (article dated Nov 18, 2026)
- Source: "Java4Shell…" / "Two malicious agent-framework releases hit PyPI in 33 minutes," Aikido blog, Nov 17, 2026 — https://www.aikido.dev/blog
- Source: "Microsoft agent-framework compromised on PyPI: 1.26.1 and 1.26.2 ship a credential stealer that runs on every Python start," Chainguard Unchained, Nov 17, 2026 — https://www.chainguard.dev/unchained/rss.xml
- Source: PyPI release state for `agent-framework` (1.26.0 of Nov 12 is current; 1.26.1/1.26.2 absent) — https://pypi.org/project/agent-framework/

### Java4Shell: pre-auth RCE in OpenJDK's Base64 decoder, CISA demands immediate patching
**Event: disclosed Tuesday, November 17, 2026 (late afternoon ET); CISA alert dated Nov 17; mainstream and vendor coverage continued overnight into Nov 18**

Calif disclosed Java4Shell (CVE-2026-114938), a memory-corruption bug in the hand-written AVX-512 VBMI assembly OpenJDK uses to speed up Base64 decoding. A malformed string — 64 `A` characters followed by `=` — makes the decoder compute a negative output length and write 62 bytes past the end of a 50-byte buffer; the attacker gets a constant-value heap overwrite that Calif turns, via remote heap feng shui, into a Heartbleed-style leak and remote code execution. The researchers demonstrated pre-auth RCE in Keycloak, Atlassian Jira, Atlassian Confluence, JFrog Artifactory, Jenkins and PingFederate, plus post-auth RCE in Kafka, Solr, Elasticsearch, Gerrit and Spinnaker, and a client-side chain where a Java program connecting to a malicious TLS server is compromised. It affects OpenJDK 17.0.6+ and every downstream build (Corretto, Temurin, Microsoft's OpenJDK, Zulu, Liberica, SapMachine, Red Hat, Oracle Java SE and GraalVM) but only x86-64 CPUs with AVX-512 VBMI — Intel Xeon Ice Lake or newer, AMD Zen 4 or newer; ARM64 is unaffected. Oracle shipped its CPU at 1:00 PM PT (4:00 PM ET) Tuesday with OpenJDK fixes; CISA posted an alert titled "Critical Java Vulnerability Requires Immediate Patching" dated Nov 17; interim mitigation is `-XX:-UseBASE64Intrinsics` or `-XX:UseAVX=2`. CVSS differs: Calif scores 9.2 under CVSS 4.0, CISA lists 8.1. No in-the-wild exploitation was reported, and Calif is withholding per-product exploits until upgrades land.

**Why it matters:** This is the second "the whole internet's plumbing, found by a centaur hacker" story in three weeks, and the economics are the point: Calif's Quang Luong says it took him an hour to find and two more to build the first working exploit, at under $200 in tokens, after directing Codex at the JIT, intrinsics and crypto code paths — and most later exploit chains were built almost autonomously. Java underpins 90% of the Fortune 500 and national-security systems, so the exposure surface dwarfs PHP's. The hardware gate (AVX-512 VBMI) is a genuine narrowing, but it excludes almost no modern data center.
- Source: "Java4Shell: Write once, RCE everywhere," Calif (primary write-up) — https://calif.io/java4shell/
- Source: "Critical Java4Shell flaw allows RCE attacks on major Java applications," BleepingComputer (Lawrence Abrams), Nov 17, 2026 6:04 PM ET — https://www.bleepingcomputer.com/news/security/critical-java4shell-flaw-allows-rce-attacks-on-major-java-applications/
- Source: "CISA Urges Immediate Patching of Java4Shell Flaw That Could Allow Remote Code Execution," The Hacker News, Nov 18, 2026 03:24 ET (CISA alert listing dated Nov 17, 2026) — https://www.cisa.gov/news-events/cybersecurity-advisories
- Source: "Java4Shell: RCE flaw in OpenJDK's Base64 decoder, with affected versions and fix," Aikido, Nov 17, 2026 — https://www.aikido.dev/blog

### Gallup: a third of young people use AI as a companion, and those who do are lonelier
**Event: Wednesday, November 18, 2026 (study published; 404 Media coverage 12:01 AM ET, Wired 2:30 AM ET)**

Gallup, with the Boston Children's Hospital Digital Wellness Lab, surveyed more than 16,000 people aged 15–22 across 16 countries. About 93% reported at least one strong in-person friendship, while 33% said they had an AI companion; usage split by purpose — 82% looked up information, 68% asked for advice, 33% used it as a companion. Young people in the US, Canada and the UK reported the least AI use overall (only 20% daily or more; nearly half had used AI no more than a few times in the past month), versus ~40% daily in Brazil and ~35% in Japan and Italy. Ratings of AI friendships clustered at "medium" in most countries — near half of Australians with AI friends called theirs high quality, only 12% of Poles did — and people using AI as a friend reported higher loneliness, though Gallup stresses causality is unresolved. The strongest finding about having no friendships at all was not about technology: not having a trusted adult outside the family.

**Why it matters:** This is the largest cross-national dataset yet on AI companionship and it undercuts both panic narratives — in-person friendship is still near-universal, and the AI-companion cohort is a minority whose loneliness link is correlational. It also gives regulators a factual baseline for the minor-safety rules multiplying across the US, EU and UK, where age-gating and companion-agent limits are being written right now.
- Source: "Massive New Study Shows How Young People Feel About AI Companions," 404 Media (Matthew Gault), Nov 18, 2026 12:01 AM ET — https://www.404media.co/gallup-study-friendship-ai-companions/
- Source: Wired coverage (Jason Parham) via Techmeme river, Nov 18, 2026 2:30 AM ET.

---

## Just missed the window
- Microsoft's Ignite keynote (Nov 17, ~noon ET, i.e. inside the previous run's window, which covered the MAI model releases but not this): Word, Excel and PowerPoint went generally available *inside* Copilot — Nadella calling Copilot "the new OS for work" — with Copilot Autopilot (persistent, proactive, credit-billed cloud agent), Microsoft IQ/Work IQ as the agent context layer, Copilot Workspaces, and per-agent IDs admins can restrict or block. Nadella's governance line was unusually blunt: models "by definition" can't be trusted, so the imperative is to separate the supply of intelligence from the authority granted to it (Ars Technica, Nov 17, 7:27 pm ET — https://arstechnica.com/gadgets/2026/11/microsoft-calls-copilot-the-new-os-for-work-at-ignite-keynote/).
- WSJ reported (~4:05 PM ET, Nov 17) that the SEC is investigating Nvidia, including whether its disclosures misled investors — https://techcrunch.com/2026-11-17/sec-reportedly-investigating-nvidia-amid-huang-exit/
- BleepingComputer (~4:27 PM ET, Nov 17): credentials and cloud secrets leaked in public Docker Hub images are exploited in as little as eight seconds of publication — https://www.bleepingcomputer.com/news/security/cloud-keys-leaked-in-docker-hub-images-are-stolen-within-seconds/

---

## Coverage notes
- **Verified: 9 items** (5 primary + 3 in Also noted/secondary + 1 provisional) across **10 distinct outlets/sources**: TechCrunch, SiliconANGLE, The Hacker News, BleepingComputer, Calif.io, Aikido, Chainguard, The Verge, 404 Media, PyPI/GitHub primary, plus Hacker News and Techmeme as discovery indexes. Two boundary cases (Java4Shell's late-afternoon disclosure and the agent-framework releases published Tuesday and reported overnight) are placed as items because their actionable milestones — the CISA alert plus overnight vendor/patcher rollout, and the PyPI quarantine plus the first consolidated public report at 02:45 ET Wednesday — fall in-window; disclosure times are stated inline rather than hidden.
- **Ledger dedupe: 14 candidates suppressed as already reported**, including the Nvidia/Huang board-and-scandal cascade (Semafor board-faction item, Jones "no intent to reinstate," The Information's Jay Puri piece, Ars "Trump was in bed with Jensen"), Amazon+Google's ~30% post-IPO Anthropic stake, Isomorphic Labs' $5B, GPT-6.2 Astra/Sol re-coverage, Microsoft MAI model re-coverage, vLLM v0.34.0, Tencent Hy4-flash, Kokoro Hugging Face account hijack, the Uber/Fireworks breach notice, and macOS 27.3 Full-Disk-Access AI warnings (repeat "just missed" footnote, suppressed as already footnoted).
- **Repeat-footnote suppression:** the Trajectory Labs / Epoch AI agent-attack story (BleepingComputer's version published Nov 16, re-surfaced on Hacker News in-window) was already footnoted once and was not relisted.
- **Dead ends / limitations:** general web search was unavailable for the entire run (search backend returned no results), so discovery ran on indexes — Techmeme river, Hacker News Algolia API, Hugging Face papers/model APIs, and outlet RSS/site indexes. Reddit's anonymous feed endpoint returned HTTP 404 for every request this run (LocalLLaMA new/rising/top all failed), so the Reddit community lane is missing — a real gap for open-weight GGUF and quant chatter, which normally surfaces there first. Socket's own report was captcha-blocked; VentureBeat's AI feed was serving stale items; the FT KPMG "100+ AI agents audit a FTSE 250 company" item (5:55 AM ET) and Tencent's Q3 results (5:45 AM ET, AI capex up 5x to ~$12B) were seen only in paywalled Bloomberg/FT river lines and could not be fetched and verified, so both are excluded.
- Research budget: ~35 minutes of the 60-minute hard stop; no item below is supported by an unfetched or undated source.
