# AI News Briefing — Friday, November 20, 2026 (overnight edition)

**Window:** Wed-cutoff rule applied — 2026-11-19 17:00 EST → 2026-11-20 07:00 EST (overnight sweep, 14 hours). New-items-only; ledger-deduped.

**TL;DR**
- Signal is restricting screenshots by default on phones, calling OS-level AI agents an "existential threat" — the first major platform countermeasure against screen-reading assistants.
- Bloomberg found Apple hiring for a dedicated autonomous-agent team: an agent that browses, codes, controls apps and coordinates sub-agents.
- OpenAI opened GPT-Live-1.1 to the API with a new "standby" mode that bills idle voice sessions at $0.02/min.
- California's attorney general served Nvidia with an investigative subpoena over China chip diversion and investor disclosure — a new legal front in the Huang/Megaspeed saga.
- Anthropic's record IPO flipped into a catalyst: Asian AI stocks bled Friday morning, with SoftBank down ~16%.

## Models and local inference

**OpenAI's GPT-Live-1.1 voice model reaches the API with per-minute standby pricing** — event date: late Thursday (Nov 19, evening ET).
GPT-Live-1.1 (model ID `gpt-live-1.1`), the full-duplex voice model that has powered ChatGPT Plus/Pro voice since Oct 15, is now generally available in the API. The notable piece is "standby," available for both GPT-Live-1 and 1.1: after a configurable 5–60 second silence the model pauses inference while the WebRTC call stays open, billing at $0.02/min versus $0.05/min active; backend model and tool usage bill separately. OpenAI claims better cross-language accents, and more reliable letter/number dictation and instruction-following.
*Why it matters:* voice-agent economics — most real calls contain long silences, and OpenAI just cut idle cost 60%. For agent builders this makes always-open voice pipelines materially cheaper, and the standby/active split is a template other realtime APIs will likely copy. Closed weights; no local-inference path.
*Local inference:* none — API only.
Source: OpenAI developer forum, "GPT-Live-1.1 has arrived in the API" (Nov 19, evening): https://community.openai.com/t/gpt-live-1-1-has-arrived-in-the-api/1418537
Source: OpenAI model docs (gpt-live-1.1, standby pricing): https://developers.openai.com/api/docs/models/gpt-live-1.1
Source: AI Weekly alert (Nov 20, ~03:20 ET): https://aiweekly.co/alerts/openai-opens-gpt-live-11-to-api-with-002min-standby

**Inference engines: llama.cpp ships 8 overnight builds; vLLM 79 in-window merges; SGLang preps v1.0** — event dates: in-window (Nov 19 22:00 UTC → Nov 20 07:00 ET).
llama.cpp merged 21 commits and tagged b12556–b12564, including CUDA Orin single-column MMVQ optimization (#31268), MoE duplicate-expert-ID support (#32505), OpenCL GQA-folded f16 flash attention (#32631), a chunked gated-delta-net Vulkan kernel (#30207), a Qualcomm subgroup-ops workaround (#30826), HarmonyOS/OHOS KleidiAI support (#32265), and mainline support for the October Domyn Edge 4B model (#32559). vLLM took 79 merges: an HY4 recipe (#68844), Qwen4Exp QSA sm_100 split-K launch configs (#68842), an Inkling 2 Small H200 recipe (#68864), a Rust-frontend wave (alias errors, LoRA unload, Muse tool-call argument streaming), an ICNS-input security reject (#68245) and ModelOpt INT8 fast-start support (#68176). SGLang merged 84 PRs with a steady stream of cherry-picks onto a `release/v1.0.0` branch — first-party v1.0 stabilization — plus AMD AITER fixes, Kimi-K3 sequence-parallel LayerNorm (#51520) and MiniMax-M3 shared-expert MegaMoE overlap (#51382). Ollama: one merge (MLX runner history/memory-pressure adaptation #19678).
*Why it matters:* the Qwen4Exp sm_100 work confirms open-weight Qwen4 quant-path preparation is ongoing, and SGLang's release/v1.0.0 branch suggests a 1.0 milestone is imminent for the second-biggest serving engine.
Note: no new open-weight model releases crossed this window; the only OpenRouter "release" Friday was a re-listing of Mistral's Feb-2025 Pixtral Large 2502.
Source: GitHub, ggml-org/llama.cpp commits & releases (in-window): https://github.com/ggml-org/llama.cpp/releases
Source: GitHub, vllm-project/vllm merged PRs (search, Nov 19–20): https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged
Source: GitHub, sgl-project/sglang release/v1.0.0 cherry-pick merges: https://github.com/sgl-project/sglang/pulls?q=is%3Apr+is%3Amerged

## Agent tools

**Bloomberg: Apple job listings reveal a dedicated autonomous-AI-agent team** — event date: Thursday, Nov 19 (Bloomberg report; evening ET coverage).
New Apple postings — headlined by a "Senior Engineering Manager, Agentic AI – Proactive" role — describe an agent that "browses the web, works across apps, runs commands, writes and debugs code, and calls services, and chooses the right tool for each step," plans multi-step tasks "across apps, browsers, command lines, code, and APIs," and "coordinates specialized sub-agents." The role sits across the Siri, UI Frameworks and Apple Foundation Models teams, days after Bloomberg reported Apple wants Siri to act proactively without step-by-step prompts, possibly next year.
*Why it matters:* Apple has been the last major platform without an answer to Meta's Muse (10M installs in 40 days, per Sensor Tower via 9to5Mac), OpenAI's Dots, Anthropic's Duet and Google's just-announced Gemini macOS computer use. The listing is the first concrete evidence of a first-party, cross-app agent program with sub-agent orchestration — and it lands while Apple simultaneously tightens third-party agent access to Macs (macOS 27.3 Full Disk Access controls).
Source: Bloomberg (Nov 19): https://www.bloomberg.com/news/articles/2026-11-19/apple-is-working-on-ai-agent-for-device-control-apps-browsing-and-coding
Source: The Verge, Emma Roth (Nov 19, 5:31 PM ET): https://www.theverge.com/tech/1038227/apple-ai-agent-engineer-job-listing
Source: 9to5Mac (Nov 19, 2:21 PM PT): https://9to5mac.com/2026/11/19/report-apple-job-listings-detail-plans-for-autonomous-ai-agents/

**Nous Research brings Hermes Agent to Microsoft Teams, the Microsoft Store and Azure** — event date: Friday, Nov 20 (Decoder; Nous blog).
Nous announced a partnership putting Hermes Agent inside Microsoft Teams for enterprise use — employees hand off tasks like financial analysis, document drafting or inbox sorting and review results in-chat — with the desktop app now on the Microsoft Store and agent sessions running in Azure Container Apps Sandboxes (isolated microVMs). Hermes converts successful workflows into reusable skills, and a new "Collective Wisdom" feature shares reviewed skills across a team; Nous stresses the knowledge stays inside the company and never feeds provider training data. Nous cites Hermes as the most-used open agent on OpenRouter, near two trillion tokens/day.
*Why it matters:* this is the strongest distribution deal yet for an open-weight agent harness into the enterprise, and it normalizes two things this brief tracks closely: microVM isolation as the default agent runtime, and on-prem skill libraries as the counter-model to provider-side memory. (Disclosure: this briefing is produced with Hermes Agent.)
Source: The Decoder (Nov 20): https://the-decoder.com/
Source: Nous Research blog (top post): https://nousresearch.com/blog/

## AI security and governance

**Signal restricts screenshots by default, calling phone AI agents an "existential threat"** — event date: Friday, Nov 20 (in-window).
In a post from President Meredith Whittaker, Signal announced it will block screenshots inside the app on iOS 27 (plus a new "Block Siri AI" setting) and will auto-arm its Android "Screen Security" whenever it detects an OS AI assistant reading the screen. Signal says OS agents "scan" on-screen content to answer questions and act for users, which captures Signal data at the endpoint where encryption ends; the company explicitly calls the protections "incomplete" (content already on the device remains reachable, and macOS screenshot blocking isn't reliably possible, so users are directed to Siri's excluded-apps list). Signal is pressing Apple, Google and Microsoft for documented developer opt-out controls.
*Why it matters:* this is the first significant platform-vs-platform defense against OS-level agents, and it directly follows the Reuters investigation (reported by this brief) that Meta's Muse surfaced private information read from Signal push notifications. Expect app categories holding genuinely sensitive data (banks, health, messaging) to follow with screen-capture defenses, and expect Apple/Google to respond with agent-scoping APIs — the negotiation over who controls agent access to the screen has just started.
Source: Digital Trends (Nov 20, 2:07 AM ET): https://www.digitaltrends.com/phones/signal-adds-screenshot-protections-to-stop-ai-agents-reading-chats/
Source: Reuters, "Signal restricts screenshots to block AI agents from reading messages" (Nov 20): https://www.reuters.com/legal/litigation/signal-restricts-screenshots-block-ai-agents-reading-messages-2026-11-20/

**Update on the Nvidia/Megaspeed saga (ledger: 2026-11-12 Huang resignation cluster): California AG subpoenas Nvidia** — event date: Thursday, Nov 19 (announced Thursday; coverage ran into Friday early hours).
Attorney General Rob Bonta served an investigative subpoena asking whether Nvidia's advanced chips or confidential information reached China in violation of export restrictions, and whether the company misled investors and the public about that risk. "The conduct of Nvidia and its former CEO raises bright red flags," Bonta said. The subpoena lands a week after Jensen Huang's resignation, with NVDA down 22% in the nine trading days since his affair admission and on pace for a seventh straight weekly loss (-38%).
*Why it matters:* this converts the Megaspeed affair from a federal export-control and board question into a state securities-enforcement matter — a second front that typically brings document demands, depositions and disclosure scrutiny. For the ecosystem, it compounds the compliance chill around China-linked compute deals already visible in BIS's remote-access rule and the TSMC/Samsung shipment suspensions.
Source: California DOJ press release (Nov 19): https://oag.ca.gov/news/press-releases/attorney-general-bonta-serves-investigative-subpoena-nvidia
Source: CNBC (Nov 19): https://www.cnbc.com/2026/11/19/nvidia-california-bonta-subpoena-china-chips.html

**Anthropic's record IPO turns into a sell-off catalyst across Asian AI stocks** — event date: Friday, Nov 20 (Asian session).
Friday morning Asia: SoftBank fell 16.2% (down ~31% in November), TSMC lost 5%+, and Samsung and SK Hynix slid 6%+, as Anthropic's debut — now closed +17% at $97.17 on ~$1.3–2T valuations — failed to calm doubts about AI capex sustainability; Bloomberg framed the IPO as "a catalyst for a savage selloff," with Nasdaq futures pointing lower after Thursday's 2%-plus decline.
*Why it matters:* the public scoreboard effect cuts both ways: a $81B raise at triple-digit revenue multiples sets an anchor private AI firms must clear, and equity markets are now repricing the financing that buys GPUs. Watch credit spreads and neocloud financings next — the same market that shut out CoreWeave/Nebius/Nscale in late October.
Source: CNBC (Nov 20): https://www.cnbc.com/2026/11/20/softbank-ai-tech-stocks-slide-anthropic-debut.html
Source: WSJ (Nov 20): https://www.wsj.com/finance/investing/asian-equities-fall-as-anthropics-ipo-fails-to-staunch-ai-stock-selloff-c47592b0

## Also noted
- SGLang's `release/v1.0.0` branch cherry-pick wave (dozens of AMD/AITER and Kimi-K3 fixes) indicates an imminent first major release of the serving engine (no tag yet as of 07:00 ET).

## Just missed the window (footnotes)
- **OpenAI "Performance Factory"** (openai.com blog + VentureBeat exclusive, Thursday daytime): Codex agents ran 1,700+ automated investigations of ChatGPT performance regressions in three months, root-caused 600+, and shipped 15 fully agent-authored PRs as written with ~50 more agent-influenced fixes. https://openai.com/index/how-openai-performance-factory-keeps-chatgpt-fast/
- **UK regulators' AI-cyber package** (BoE/FCA/PRA, Thursday London morning): sector-wide cyber-resilience guidance consultation (closes Feb 26, 2027) plus non-binding "effective practices" naming prompt injection, model/data poisoning, jailbreaking, autonomous-agent attacks and deepfakes; survey shows 88% of firms expect frontier-AI cyber capabilities to have moderate/high impact within 2 years. https://www.bankofengland.co.uk/news/2026/november/cyber-resilience-guidance-consultation-and-ai-and-quantum-effective-practices
- **Moonshot AI walked away from a ~$150M Nvidia B300 rental in Indonesia** after BIS's remote-access rule closed the Southeast-Asia loophole (The Information, Thursday morning). https://aiweekly.co/alerts/moonshot-ai-cancels-150m-nvidia-b300-rental-in-indonesia
- **DeepMind Institute essay "What Comes After AGI?"** (Tim Genewein, with Shane Legg and co-authors; Thursday): argues AGI→ASI is moving from philosophy to measurable research, citing ≥10x/yr effective-compute growth. https://institute.deepmind.com/essays/what-comes-after-agi/

## Coverage notes
- **Verified count:** 6 new items (2 models/inference incl. engine cluster, 2 agent tools, 2 security/governance) + 1 "update on previously reported" (CA AG subpoena) + 4 single-use "just missed" footnotes. Every main item's source page was fetched; Signal, Apple, GPT-Live-1.1, Hermes-Teams and the Asia selloff carry two or more independent sources.
- **Ledger dedupe count:** 10 candidate events suppressed as already reported (Anthropic debut/first-close/allocation details; Muse privacy investigation; Gemini 4 Neon and macOS computer use; Frostbite AI-designed CPU re-spike; Kokoro HF-account hijack; Citi/PNC/Citizens agent-terms shift; SenseNova Loop-9B repeat; arXiv browser-embedded-LLM abuse paper — submitted Nov 18, pre-window and ledger-adjacent).
- **Dead ends / limitations:** Reddit community lane failed from this host (anonymous `.rss` feeds now 404 on `sub` and `search` routes; pullpush.io 429) — logged per procedure, no third attempt; Reuters Signal article sat behind a fetch wall (substituted DigitalTrends page with in-window timestamp plus Reuters slug confirmation); Bloomberg/WSJ items cited via their own slugs and syndicated copies; Pragmatik Labs' reported $5B round (Bloomberg alert) could not be corroborated outside the paywall (two strikes); the HN community lane was fully walked in-window (Gemini Neon "Models Engineering Index" #1 claim, 352 pts, personal-site benchmark — unverified, excluded).
