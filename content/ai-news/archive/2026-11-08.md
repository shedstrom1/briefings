# AI News Briefing: Sat Nov 7 17:00 → Sun Nov 8 07:00 ET

Window: 2026-11-07 17:00 ET → 2026-11-08 07:00 ET (previous fire → now; 14-hour weekend sweep). This is a NEW-ITEMS-ONLY edition against the reported ledger.

**TL;DR**
- Richard Sutton — author of "The Bitter Lesson" — published a new post saying raw compute alone is not enough for intelligence; ex-OpenAI researcher Jerry Tworek says blind transformer scaling "has reward hacked brains of a whole generation of scientists," while OpenAI's Altman and Anthropic's Amodei push back.
- SemiAnalysis went inside China's fastest-growing AI data center region: Envision's 800MW Galaxy campus in Inner Mongolia delivered its first 120MW in under a year at roughly a fifth of US costs, with an SST-native 800V DC pilot and chiller-free cooling.
- llama.cpp shipped eight builds (b12195–b12202) across the window — SYCL/Vulkan/OpenCL fixes, speculative-decoding MoE-cache guards, and Hexagon auto-load.
- Thin weekend window: only four verified new items made the cut. Five prominent disclosures (SpaceX Starbase supercomputer, Public First PAC midterm results, Alibaba's Co-Design Bench, Vals AI's Inkling study, llama-modchip) landed just before or well outside the 17:00 ET cutoff and are footnoted below.
- Reuters reports environmental groups are embracing AI for climate and wildlife tracking even as they warn about data-center power and water impacts; Turkey's COP31 is expected to feature an AI sustainability pledge.

## Models and local inference

**Richard Sutton's bitter-lesson post puts LLM scaling in the crosshairs**
Event date: November 8, 2026

Turing Award winner Richard Sutton — who in 2019 argued that scaling general methods beats human-engineered shortcuts — writes in a new post that raw compute alone is not sufficient for intelligence and that AI science needs "clever new methods" and is "far from finished." The Decoder reports he again called synthetic data a "big mistake" because the real world is infinitely complex, and argued for agents with world models that keep learning from their own experience rather than stopping after training. Ex-OpenAI researcher Jerry Tworek (Core Automation) amplifies the critique: the more compute goes into training, the more the algorithm matters, and the temporary success of transformer scaling "has reward-hacked the brains of a whole generation of scientists." Jayden Teoh adds that the bitter lesson is often misread to dismiss promising academic work. Industry CEOs dissent: Sam Altman says scaling is far from exhausted (citing OpenAI's recent disproof of a long-standing math conjecture), and Dario Amodei calls scaling progress "a rainbow with no end." The Decoder notes its Frontier Radar #5 is tracking 45 new AI labs betting on this post-scaling gap — including Sutton's Oak Lab and Tworek's Core Automation.

Why it matters: the sharpest public split yet between scaling maximalists and founders/ researchers betting that continuous-learning, world-model agents — not bigger pretraining — will deliver the next leap. That divergence is now visibly shaping startup funding directions.

Source: The Decoder, "The inventor of the bitter lesson has a bitter lesson for LLM scaling enthusiasts" (Matthias Bastian, Nov 8, 2026) — https://the-decoder.com/the-inventor-of-the-bitter-lesson-has-a-bitter-lesson-for-llm-scaling-enthusiasts/ (Source cited in article: Sutton via X)

**SemiAnalysis inside "China's Stargate": Inner Mongolia's Galaxy campus**
Event date: November 7, 2026 (newsletter); Techmeme river entry Nov 8, 6:00 AM ET

SemiAnalysis published a field report from Inner Mongolia, China's fastest-growing AI data center region — calling it the "West Texas" of China, but without the anti-datacenter sentiment. The main stop is Galaxy, an Envision Group campus in Ulanqab planned for up to 800MW of IT capacity; its first 120MW phase was delivered within a year at about a fifth of US costs, built-to-suit for two internet giants. In one 5MW hall, solid-state transformers take 10kV AC straight to 800V DC for the racks — removing the low-voltage transformers, UPS and rectifier stages that dominate US designs. Cooling uses a shared water loop for liquid- and air-cooled servers with no central chiller plant, a design common in northern China but still a roadmap item in the US. The surrounding Chahar High-tech Zone had nearly 30,000 construction workers across 22 data center projects. Regional economics: rents have roughly halved over five years to about $40/kW/month (ex-power), capex is under $3M per MW, and power costs about $0.055/kWh all-in. The region has 25GW of announced projects, and the report notes roughly a quarter of Chinese data centers built in the past decade run on DC power, versus about 97% of US facilities still on AC UPS.

Why it matters: China is building hyperscale AI capacity faster and far cheaper than the US, with power architectures (800V DC, chiller-free loops) that US roadmaps are still trying to reach — a direct signal on where inference capacity economics are heading.

Source: SemiAnalysis, "We Visited China's Stargate in Inner Mongolia" (Everlyn Tan, Jeremie Eliahou Ontiveros, Xiaopeng Laoban, Dylan Patel, Nov 7, 2026) — https://newsletter.semianalysis.com/p/we-visited-chinas-stargate-in-inner (Techmeme river, Nov 8, 2026, 6:00 AM ET)

**llama.cpp ships eight builds (b12195–b12202) with SYCL, OpenCL and speculative-decoding fixes**
Event date: Nov 7, 20:11 ET → Nov 8, 03:11 ET (GitHub releases)

Eight builds landed in the window (13 commits). Notable changes: SYCL gets exact rounding in CPY to Q4_1/Q5_1/Q8_0 (#31465) and refuses device buffers that would exceed VRAM (#31285); OpenCL improves q8_0 and MoE small-n verification for speculative decoding (#31831); the MoE cache is no longer applied to the draft model (#31497); Hexagon now auto-loads libhtp without legacy env vars (#31781); invalid UTF-8 is sanitized in PEG tool-call arguments (#31861); WebGPU gains COUNT_EQUAL support (#31578); CI moves to Ubuntu 26.04 (#31529); the CPU backend switches to F32 src1 (#31854); and mtmd follows ggml reference precision (#31851) with log-mel computation moved to cgraph (#31808).

Why it matters: continued rapid mainline iteration — the SYCL/Intel GPU and speculative-decoding stability work is directly relevant to local inference stacks running MoE models.

Local inference: these builds feed directly into GGUF-serving runtimes (llama.cpp server, LM Studio, Ollama under the hood).

Source: GitHub ggml-org/llama.cpp releases b12195–b12202 (Nov 7–8, 2026; commit log via GitHub API) — https://github.com/ggml-org/llama.cpp/releases

## Agent tools

No new agent-framework releases or launches verified inside this window. The recent stable lines remain outside it: OpenClaw v2026.11.1 (Nov 5), Ollama v0.40.15 (Nov 3), vLLM v0.33.0rc3 (Nov 2).

## AI security and governance

**Reuters: environmental groups embrace AI even as they warn of data-center impacts**
Event date: November 8, 2026 (2:02 AM EST)

Reuters reports that environmental groups worldwide are using AI to analyze climate data, track forest fires, and count endangered wildlife — while warning that the technology is driving a surge in electricity demand, water use, and greenhouse-gas emissions. The outlet interviewed 15 major environmental, conservation, and development organizations; all said they use AI in some form and all expressed concern about the data centers powering it. Conservation International, for example, is using AI to assess satellite-detected hotspots and send real-time wildfire alerts to community patrols in Cambodia. The tradeoffs are expected to draw attention at the UN's COP31 climate conference this month in Turkey, where the host is calling for a pledge outlining principles to ensure AI is developed and deployed sustainably.

Why it matters: the governance debate around AI's environmental footprint is moving from industry self-policing toward formal multilateral pledges — at exactly the moment data-center buildout is accelerating globally.

Source: Reuters, "Environmental groups embrace AI, even as they warn of data center impacts" (Valerie Volcovici, Nov 8, 2026, 2:02 AM EST) — https://www.reuters.com/sustainability/cop/environmental-groups-embrace-ai-even-they-warn-data-center-impacts-2026-11-07/

## Also noted

- No other verified in-window items beyond the four listed above. Community leads seen but not fully verified within budget: Show HN "Aiport" (self-improving local LLM gateway, Nov 8 ~06:44 ET) and "RepoMagpie" (coding-agent repo memory, Nov 8 ~05:13 ET).

## Just missed the window

- **SpaceX "Starbase supercomputer"** — The Information, Nov 7, ~2:40 PM ET (minutes to hours before the 17:00 cutoff): public records, SpaceX job listings, new steel structures, and gas turbines point to a multigigawatt AI "Starbase supercomputer" near SpaceX's Texas launch site; Hinojosa's election (vowing an immediate data-center moratorium) may delay parts of the build, though a 45-year, 166-acre Cameron County development agreement prohibits local moratoriums (RGV Business Journal). Corroborated by FourWeekMBA (Nov 7) and Techmeme. The Information body is paywalled; records not independently seen.
- **Public First PAC midterm results** — Bloomberg, Nov 7, 9:30 AM ET: Anthropic co-founder Dario Amodei and other AI-safety advocates funded more than $63M for the midterms; 10 of the 17 congressional candidates Public First backed won. Politico magazine ran a companion account Nov 7, ~9:45 PM ET.
- **Co-Design Bench** — Alibaba/Qwen-team arXiv paper (Oct 26/27) covered by The Decoder Nov 8 (in window): 273 real-world multimodal coding tasks; GPT-6 Astra fully solves only 26.4% of tasks despite passing ~70% of criteria on average; harness choice swings results (Qwen3.8-27B scores 75% vs 56% on large overhauls depending on environment).
- **Vals AI: Inkling 2 Small swears in internal chain-of-thought** — vals.ai blog Nov 6 + The Decoder Nov 7: across 2,655 runs on 13 benchmarks, "fuck" appears 316 times (50 runs; 241 instances from two runaway runs, including one 500,000-character loop); not a failure predictor (96% of failed runs never swore).
- **llama-modchip v2.4.0** — GitHub release Nov 3–4 (repo created Oct 29): claims 284B DeepSeek-V4-Flash on a pure 64GB CPU (<59GiB RAM, 44.4 tok/s prefill, 9.1–14.7 tok/s decode, 256K ctx) via custom Fractal-64 Q2_0 tensors, SwiGLU-H2 compression, and AVX-512 register tiling; HN front-page resurgence Nov 8, ~05:22 ET (story 50411961, 6 points).

## Coverage notes

- **Verified this run:** 4 reportable items (Sutton/Decoder; SemiAnalysis; llama.cpp builds; Reuters) + 5 "Just missed" footnotes (each first noted).
- **Ledger dedupe:** 11 candidates suppressed as already reported or already footnoted — Nous Arete-27B (Techmeme/Decoder re-runs); Anthropic IPO $80–100B update (Decoder short news); OpenAI ~$11B tranche (Decoder short news); Google smartwatch hypertension study (`missed:`); nine-agents DOE research failure (`missed:`); Vals cyber-offense benchmark (`missed:`); Meta Muse Wonder specialized models (`missed:`); Censys ARTEX exposure (`missed:`); NYT H200 Chinese-firm empty offices (reported Nov 7); NYT ETH/Impagliazzo follow-up (reported Oct 26); Guardian SEA data-center pause (reported Nov 7). TechNode's GenRobot $100M robot-data round was disclosed Nov 2 — stale for this window.
- **Dead ends / index gaps:** SGLang GitHub releases endpoint returned 404; The Information Starbase body paywalled (corroborated via FourWeekMBA + Techmeme); HN Algolia slice covered only the final ~2 hours of the window (100 hits), so earlier Sunday/Saturday-night stories were not fully enumerated; Reddit anonymous feed was rate-limited (one successful call per sub).
