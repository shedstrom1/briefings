# AI News Briefing: Nvidia's Open 30B-A4B Transcribe Model Tops Open ASR; llama.cpp Ships v0.8.0

**Window:** Tuesday October 28, 2026, 07:00–12:00 AM/PM EDT (previous fire 07:00 → this fire 12:00, America/New_York)

## TL;DR

- **llama.cpp hit its v0.8.0 milestone**: an OpenAI-protocol `/v1/realtime` endpoint and `POST /tts`, the full LiquidAI LFM3 audio/omni family, a fused QSA/DSA masking op, and a 50/50 SLRU cache for host-resident MoE experts.
- **Rime launched Prism**, a closed-weight but fine-tunable, self-hostable speech-to-speech model for enterprise voice agents — τ-Voice 56.6 with 0.57 s to first audio.
- **OpenAI stood up an Election Operations Center** a week before the US midterms, deploying hundreds of employees until all votes are counted.
- **Funding:** Upwind Security raised a $400M Series C at $3.8B (Bessemer) for AI/cloud security, and Foundry Robotics closed a $100M Series A (Marlinspike) for robot-run autonomous assembly.

---

## Models and local inference

**Nvidia ships Nemotron 3.5 Transcribe (open weights) + Granary v2** — *Event date: October 28, 2026*

Nvidia released Nemotron-3.5-Transcribe-30B-A4B, an open-weight offline speech model under the OpenMDW-1.1 license: a Mamba-2–Transformer hybrid mixture-of-experts with 34B total parameters and ~4.3B active including the audio encoder, shipping in BF16, FP8 and NVFP4. It transcribes 40 languages (65,536-token context, recordings up to one hour), translates 31 to/from English, attributes up to eight speakers, and is "promptable" — you can hand it domain context, term lists, formatting rules, or audio questions before it listens. Nvidia's model card claims best-open-weight results (mean WER 4.71 on the Open ASR Leaderboard's nine sets, 10.98 long-form, 4.62 multilingual) and best-published multi-speaker cpWER of 15.80; The New Stack pegs it at 2.81% WER on Artificial Analysis's ASR benchmark, essentially matching Voxtral Small with about a sixth of the active parameters. Fine-tuning a medical case study cut WER from 22.44% to 12.17% in roughly 14 GPU-hours on a single GB300. Alongside it comes Granary v2 — a reusable NeMo Curator data-curation pipeline plus public manifests covering roughly 463k hours across 48 languages.

**Why it matters:** This is the strongest open-weight ASR checkpoint released to date, and it lands squarely in the voice-agent wave (ElevenLabs Realtime, Mistral Voxtral 3, MAI-Transcribe-2 all shipped this month).

**Local inference:** Model card specifies 1×H100 80 GB for full-precision serving; NVFP4/FP8 variants and the 4.3B-active MoE footprint make quantized 16GB-card runs plausible once NeMo Speech.cpp / llama.cpp support lands.

Sources:
- Nvidia model card, "nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B" (October 28, 2026) — https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B
- TechCrunch, "Voice AI in vogue: Nvidia releases new open transcription model" (October 28, 2026, 8:00 AM PDT) — https://techcrunch.com/2026/10/28/voice-ai-in-vogue-nvidia-releases-new-open-transcription-model/
- The New Stack, "Nvidia launches a promptable transcription model" (October 28, 2026, 11:00 AM ET) — https://thenewstack.io/nvidia-nemotron-promptable-transcription/

**llama.cpp reaches v0.8.0: realtime voice endpoints, LFM3 family, reworked sparse attention** — *Event date: October 28, 2026 (08:47 ET)*

Core work: sparse attention (QSA/DSA) reworked onto a fused masking op, a 50/50 SLRU cache that keeps MoE experts host-resident, and cleaner no_alloc memory handling; ggml bumps to v0.28.0 with CUDA chunked graph launches, SYCL graph replay, OpenCL Q2_K/Q3_K kernels, a Hexagon HTP rework and KleidiAI SVE kernels. Breaking change: `--model` or `-hf` is now required.

The host-resident-expert SLRU cache and fused sparse-attention masking are exactly the kind of memory-hierarchy work that decides whether big MoE models stay usable on 4–16 GB cards.

Source:
- ggml-org GitHub release, "v0.8.0" (October 28, 2026, 12:47 UTC) — https://github.com/ggml-org/llama.cpp/releases/tag/v0.8.0

**Rime launches Prism, the first fine-tunable enterprise speech-to-speech model** — *Event date: October 28, 2026*

Rime released Prism, a closed-weight, self-hostable speech-to-speech model for voice agents that keeps an inspectable text checkpoint between deciding and speaking: guardrails and content/action tags run on the text before it becomes audio, while tool calls execute in parallel with speech. Rime reports 0.57 seconds time-to-first-audio — about half GPT-Live-1 / Gemini 3.8 Live — and a τ-Voice score of 56.6, ahead of every non-reasoning realtime model from OpenAI and Google by 17–30 points and above GPT-Realtime-2.1-with-reasoning; in telecom it scores 82.5 vs 45.6. It is also (per Rime) the first S2S model customers can fine-tune: a RL-trained Prism on 10 hours of customer Revmo's production calls asked before placing orders 94% of the time vs 57% for the incumbent OpenAI model.

**Why it matters:** The voice-agent stack is the current local-inference battleground (Nemotron above, ElevenLabs, Voxtral) and Prism opens the "own your weights" lane for the S2S tier — enterprises can run it on their own infrastructure and adapt it with RL/SFT instead of renting a fused black box.

Sources:
- Rime, "Introducing Prism: the first composable speech-to-speech model" (October 28, 2026) — https://www.rime.ai/resources/prism-first-speech-to-speech-for-enterprise
- The AI Insider, "Rime Launches Customizable Speech-to-Speech AI Model for Enterprise Voice Applications" (October 28, 2026) — https://theaiinsider.tech/2026/10/28/rime-launches-customizable-speech-to-speech-ai-model-for-enterprise-voice-applications/

**Also noted:** vLLM merged 9 PRs in-window, including day-one `[Model] Add Nemotron-3.5-Transcribe` support (#64282), the UMBP MORI embedded-DRAM runtime (5/10, #61793), KV-offload tiering fixes (#63779, #64250), and DSv4.1 FlashInfer decode-path fixes (#63914). llama.cpp shipped builds b11960–b11963 on the way to v0.8.0 (realtime tool calls #31287, fused CUDA MoE gate/up+GLU in MMQ #31188, LFM3 video input #31283).

---

## Agent tools

**Foundry Robotics raises an oversubscribed $100M Series A led by Marlinspike** — *Event date: October 28, 2026*

The Information reported exclusively that Foundry — founded less than a year ago, led by CEO Adarsh Kulkarni — closed a $100M Series A led by Marlinspike Partners, with Khosla Ventures (its seed lead), Red Glass, SE Ventures, Wing VC, Axiom, Hanabi and Zero Shot participating. Foundry builds and operates autonomous assembly factories in Emeryville, California, combining AI-powered robotic cells with its FoundryOS and "physical AI" models as manufacturing-as-a-service for defense and commercial hardware: drones, autonomous-vehicle subassemblies, wire harnesses, underwater vehicles, rugged computers and satellites.

**Why it matters:** Physical-AI is the agent ecosystem's newest capital magnet, and Foundry is pitching the full stack — models, orchestration OS and factory operations — as the near-term commercial use case for VLA and world-model research.

Sources:
- The Information, "Foundry Raises $100 Million for Autonomous Factories" (October 28, 2026) — https://www.theinformation.com/newsletters/ai-agenda/foundry-raises-100-million-autonomous-factories
- FinSMEs, "Foundry Robotics Raises $100M in Series A Funding" (October 28, 2026) — https://www.finsmes.com/2026/10/foundry-robotics-raises-100m-in-series-a-funding.html

---

## AI security and governance

**OpenAI launches an Election Operations Center a week before the midterms** — *Event date: October 28, 2026*

OpenAI told Politico's Decoded it is standing up an "election war room" that runs from this week until all votes are counted, deploying hundreds of employees to spot misuse of ChatGPT and its other tools — monitoring real-time events, fielding reports of unexpected model behavior, and enforcing policy as roughly 200 million Americans head to the polls. Head of civic and strategic risk Josh Lawson, who leads the center, framed it as years of election work "bringing our teams together to respond quickly to emerging issues."

**Why it matters:** Next week's midterms will be the first US federal election run entirely in the generative-AI era, with deepfake media and AI-driven misinformation as the named threat — and OpenAI positioning itself as a defender of the process while anti-AI messaging animates both parties' campaigns.

Source:
- Politico (Decoded by POLITICO), "OpenAI launching election war room to track interference efforts" (October 28, 2026) — https://www.politico.com/news/2026/10/28/openai-election-war-room-interference-01137690

**Upwind Security raises $400M Series C at $3.8B, six months after rejecting a ~$4B Datadog offer** — *Event date: October 28, 2026*

Bessemer led the round (Canapi joining; Cyberstarts, Greylock, Craft, Salesforce Ventures and Picture Capital participating), taking the cloud-and-AI security platform from $1.5B in January to $3.8B. Upwind monitors cloud workloads, APIs and AI agents in real time and traces agent actions back to the code that produced them; it reports over $50M ARR (up ~2.5x year-on-year, a 76x multiple), customers including Siemens, Roku, Carvana and Nubank, and that Datadog tabled a nearly $4B acquisition offer earlier this month which Upwind declined.

**Why it matters:** Agent-runtime visibility is now its own funded category — investors paid a strategic-buyer price to back the thesis that "AI agents change environments at a speed and scale human teams cannot match," and the capital will push Upwind straight at Wiz (now Google-owned), Palo Alto and CrowdStrike.

Sources:
- Tech Funding News, "Spot.io founder's Upwind raises $400M at $3.8B" (October 28, 2026) — https://techfundingnews.com/upwind-400m-series-c-3-8b-valuation-cloud-ai-security/
- Bloomberg, "AI Cybersecurity Startup Upwind Raises at $3.8 Billion Value" (October 28, 2026) — https://www.bloomberg.com/news/articles/2026-10-28/ai-cybersecurity-startup-upwind-raises-at-3-8-billion-value

---

## Just missed the window

- **Anthropic's Frontier Red Team published five working exploit chains** built by an unreleased internal Claude model — one-click Chrome RCE (62 CVEs patched as of Chrome 156, incl. a macOS Calculator PoC with zero clicks), a Safari same-origin-policy break through Gigacage, an Android lock-screen USB bypass on a Pixel 9, a zero-click Linux chain (CVE-2026-98432, CVSS 9.8) and a Windows chain. The post is dated October 27, so it anchors outside this window even though outlets covered it this morning. — https://www.anthropic.com/research/discovering-vulnerabilities-with-claude
- **Artificial Analysis launched Intelligence Index v5** (posted October 27): the biggest overhaul since v4 — 2 new benchmarks (Terminal-Bench-Science, CritPt v2 hard subset), 60% private test sets, Claude Opus 5.5 (max) leads at 58, released open weights top out 12+ points back (DeepSeek V4.1-Flash-1017 and GLM-5.3 at 32), and median tokens per task jumped from 35k to 59k. Plus a new Progress Index v1. — https://artificialanalysis.ai/articles/artificial-analysis-intelligence-index-v5
- **A federal grand jury indicted OpenAI engineer Abdulrahman "Abdul" Alfozan**, the Saudi national accused of planting an encrypted listening device in an executive conference room that OpenAI says its own models helped crack; Tuesday's indictment came Friday's arrest (October 14). — https://www.sfchronicle.com/bayarea/article/open-ai-engineer-indicted-for-bugging-headquarters-22497813.php

---

## Coverage notes

- **Verified this run:** 6 reportable items (all fetched, in-window timestamps confirmed; lead stories read in full) + 2 also-noted engineering clusters + 3 new "just missed" footnotes.
- **Ledger dedupe:** 14 candidates suppressed as already-reported ledger events across 11 entries — SK Hynix Q3/shortage/buyback (3), Anthropic S-1 TAM + cash analyses (2), Wan v. OpenAI suit, Anthropic agent-judgment RCT, Anthropic drug-dev/Mithril, Warren-Amodei letter, OpenAI TPU purchase, GSA OneGov, Infisical, CXMT HBM3E, llama.cpp b11938–b11954. Plus one stale re-cover rejected: Bloomberg/AIBriefs recirculated the Amazon Nova "keep the lights on" story, which is a July 28 Business Insider report (event date far outside window).
- **Out-of-window, not footnoted (non-lead):** Discord public-server searchability (announced Tue), Amazon tokenmaxxing memo (posted Tue Oct 27), Mistral Responses API preview (Tue ~8 pm ET), SpAItial Echo-3 (Oct 27), Giotto Alto/Solo (Tue), Cognition FrontierCode 2.0 (Tue), Fish Audio transcribe-stream-1 (undated launch, Cekura leaderboard discrepancy), eMudhra CA and FEC items (non-AI security).
- **Dead ends:** BusinessWire Foundry PR returned "Page Unavailable" (one strike; substituted FinSMEs + The Information). HuggingFace trending renders metadata without model names, so no named model could be confirmed from it — open-weight discovery instead came via trackers, Techmeme and outlets. HN front-page sweep surfaced no additional in-window AI items beyond those above.
- Sources consulted this run: Techmeme, AIBriefs, HeadsUpAI, Hacker News (Algolia), Nvidia/HF model card, TechCrunch, The New Stack, Politico, Bloomberg, Tech Funding News, The Information, FinSMEs, Rime, GitHub releases (llama.cpp/vLLM/Ollama/OpenClaw), nocode.mba release tracker.
