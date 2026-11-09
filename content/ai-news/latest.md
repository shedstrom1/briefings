# AI News Briefing — Monday, November 9, 2026, 7:00 AM ET

**Window:** Sunday, November 8, 2026, 5:00 PM ET → Monday, November 9, 2026, 7:00 AM ET (14-hour overnight window; previous fire 11/08 17:00 ET).

## TL;DR

- **Nvidia's board probe went overnight global:** shares fell ~4.4–5% in premarket (~$199B wiped out), Trump called Huang "honorable" on Sunday, and Beijing's Foreign Ministry denied the "Chinese spy narrative" Monday morning.
- **Trump's first post-midterm sit-down is out (CBS transcript, Sun 7:00 PM ET):** he says he "doesn't mind guardrails" for AI, but won't accept rules that put the US in "a deep second position" behind China, which he says "has no guardrails."
- **Inference engines kept shipping:** llama.cpp landed 10 in-window commits (Granite NVFP4 weight scales, Metal FA buffer fix, multi-backend YaRN) and vLLM merged ~40 PRs (Kimi K3.1 at TP8, Qwen4Exp routed experts on DeepGEMM MegaMoE, RDMA-capable KV-offload tier).
- **Three near-misses footnoted:** Tencent's Yao Shunyu bio-risk warning (Saturday symposium), Cognition CISO Alex Stamos's "dark age" Decoder episode (Friday), and the NYT report that Rubio oversaw a classified memo on Huang (Sun ~2:30 PM ET, hours pre-window).

## AI security and governance

**Nvidia board-probe fallout cascaded overnight: stock -5%, Trump backs Huang, Beijing rejects the spy framing** — Update on previously reported (ledger: `update:megaspeed-alice-huang`, 2026-11-08). Event date: disclosures Nov 8, 8:40 PM ET → Nov 9, 6:00 AM ET.
After Huang's Sunday staff email admitting the affair ("I was unfaithful to my wife... There is no excuse") and the board's independent probe, Nvidia shares dropped ~4.4% in premarket as of 5:15 AM ET Monday (~$199B in market value; ~$7.1B personal hit to Huang). President Trump praised Huang Sunday as "an honorable guy" who "has done an amazing job for our country." China's Foreign Ministry spokesperson Guo Jiakun said Monday the "so-called Chinese spy narrative" was unsubstantiated, and Chinese state media accused US outlets of a fabricated story that could hurt trade relations. Meanwhile a New York Times deep-dive (Nov 9, 6:00 AM ET) details Huang's tight grip — 60+ direct reports and a 3.56% stake, a challenge for the 10-person board — while WSJ filings show he has elevated children and other family members unusually high in the company. Analyst Dan Ives called it a "black eye moment" that will spark congressional inquiries.
**Why it matters:** Nvidia is the chokepoint of the AI supply chain; a leadership crisis at the world's second-largest company now intersects with an active foreign-intelligence concern, and the stock weakness is already being read as a potential signal for AI-capex cuts. Nvidia reports Q3 earnings Nov 17.
Source: CNBC, "Nvidia stock falls 5% as scrutiny turns to Jensen Huang's relationship with alleged Chinese spy," published Mon Nov 9, 4:05 AM ET — https://www.cnbc.com/2026/11/09/nvidia-jensen-huang-chinese-spy-investigation.html
Source: Axios, "Nvidia shares sink as board launches probe into Jensen Huang's affair," published Mon Nov 9, ~5:00 AM ET — https://www.axios.com/2026/11/09/nvidia-shares-stock-jensen-huang
Source: New York Times, "Is Jensen Huang Too Powerful to Be Punished by Nvidia?" — https://www.nytimes.com/2026/11/09/technology/jensen-huang-affair-nvidia.html
Source: Financial Times (via Techmeme river, Nov 9 5:40 AM ET): Nvidia drops ~5% pre-market after board probe; Bloomberg (river, Nov 9 4:20 AM ET): Chinese media denies Beijing's link.

**Trump signals openness to AI guardrails — but not at the cost of falling behind China** — Event date: Nov 8, 7:00 PM ET (CBS transcript publication).
In his first sit-down since Tuesday's midterms (interviewed Friday, transcript published Sunday 7:00 PM ET by Ross Douthat for 60 Minutes), Trump said AI risks "can be managed," telling CBS he "doesn't mind guardrails" — but "I don't wanna put us in a deep second position. We're leading number one. And China has no guardrails, by the way."
**Why it matters:** Coming days after Democrats seized both chambers and a day before the lame-duck session opens, the President is drawing a line: US AI regulation is acceptable only if it doesn't cede ground to Beijing — a direct constraint on the "mandatory speed limits" framework OpenAI is pitching this week.
Source: CBS News, "60 Minutes Transcript: Donald Trump, the 2026 post-midterm elections interview," Nov 8, 2026 / 7:00 PM EST — https://www.cbsnews.com/news/donald-trump-2026-post-midterm-elections-interview-60-minutes-transcript/

## Models and local inference

**llama.cpp ships 10 in-window commits: Granite NVFP4 scales, Metal FA bounds fix, YaRN on four backends** — Event date: commits Nov 8, 22:00Z → Nov 9, 11:32Z.
Notable merges: `models: Attach Granite NVFP4 weight scales` (#31449); `metal: prevent out-of-bounds writes to the Metal FA output buffer` (#31662); `ggml: support YaRN truncation config on openvino, sycl, vulkan, webgpu, rpc` (#30802); Pocket TTS generic temperature via API (#31655); and RPC loading-time improvements (#29413).
**Why it matters:** The Granite NVFP4 scale attachment extends 4-bit NVIDIA-precision loading to IBM's Granite family, and the Metal fix hardens Apple-Silicon flash-attention runs — both direct wins for consumer-GPU and laptop local inference.
Source: GitHub, llama.cpp commit feed since 2026-11-08T22:00Z — https://github.com/ggml-org/llama.cpp/commits

**vLLM merges ~40 PRs overnight: Kimi K3.1 at TP8, Qwen4Exp on DeepGEMM MegaMoE, RDMA KV-offload tier** — Event date: merges Nov 8, 22:00Z → Nov 9, 12:00Z.
Highlights: `[Core][Kimi K3.1] KV cache and attention changes to serve Kimi K3.1 at TP8` (#66037); `[Model][Qwen4Exp] Run routed experts on DeepGEMM MegaMoE (MXFP8 or MXFP4)` (#64315); `[Feature][KV Offload] Add RDMA-capable OBJ tier` (#56192); `[MoE] Make fused MoE routers pluggable` (#51006); plus an Inkling NVIDIA sconv cache-block-size fix (#51951) and a continuing Rust-frontend wave.
**Why it matters:** The Kimi K3.1 TP8 work makes the 2.8T-parameter open-weight model servable on 8-GPU nodes, and the Qwen4Exp MegaMoE path is the first DeepGEMM-routed-expert integration — both expand what open-weight MoE families can run outside proprietary stacks.
Source: GitHub, vLLM merged-PR search (merged:2026-11-08..2026-11-09) — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged

**Show HN: TimeScroll — open-source local Rewind replacement for Mac with Ollama summaries** — Event date: Nov 9, 06:19 ET (HN submission).
XInTheDark's Mac app records the screen locally and uses the macOS Accessibility API ("Direct mode") to capture text without OCR; a new feature summarizes usage ranges via a local model through Ollama, choosing representative frames with existing embeddings. Reports ~2GB/month storage and ~1.1% CPU / 150–250MB RAM on an M4.
**Why it matters:** Rewind-style screen-memory tools have been cloud-dependent; this runs entirely local with open source.
**Local inference:** Practical local-model usage on Apple Silicon — Ollama-backed summarization of screen-capture embeddings without any cloud round-trip.
Source: Hacker News (id 50424070), "Show HN: TimeScroll – an open-source, local replacement for Rewind on Mac," Nov 9, 11:19Z — https://news.ycombinator.com/item?id=50424070

## Agent tools

No qualifying agent-framework releases or tooling announcements opened inside this window (OpenClaw v2026.11.2 shipped Nov 8, 2:31 PM ET — just outside the window start; Ollama, SGLang, and the major agent CLIs were quiet).

## Also noted

- **FT workplace analysis (Nov 9, 4:00 AM ET, via Techmeme):** as AI agents replace workers, manager grade/pay tied to headcount is breaking — noted in river, URL not resolved within budget.
- **Semafor analysis (Nov 8, 11:25 PM ET):** the Huang affair is a rare US corporate-history crisis given Nvidia's national-security centrality to the AI race.

## Just missed the window

- **Tencent chief AI scientist Yao Shunyu warned AI bio-capabilities will soon "do harm"** (speech at HK Academy of Sciences / Future Science Prize symposium Saturday Nov 7; SCMP reported Nov 8 ~10:15 PM ET — https://www.scmp.com/tech/big-tech/article/3373697/ai-will-soon-master-biology-unleashing-new-safety-risks-tencent-ai-chief-warns). He said Anthropic has a "strong argument" for closed frontier models, and noted Hugging Face used a Chinese open model to defend itself after closed US models refused. Weights for Tencent's Hy4 still promised "in the coming days." `missed:`
- **Cognition CISO Alex Stamos, Decoder episode:** "How AI broke cybersecurity" — open-weight models now automate "most of the kill chain"; the industry is entering a "dark age" (Verge page dated Nov 7; Techmeme river Nov 8, 11:05 PM ET — https://www.theverge.com/podcast/1029038/ai-cybersecurity-hackers-data-breach-dark-age-alex-stamos). `missed:`
- **NYT: Rubio oversaw drafting of classified memo warning officials about Huang/Alice Huang relationship** (published Sun ~2:30 PM ET, ~2.5h pre-window — https://www.nytimes.com/2026/11/08/us/politics/rubio-nvidia-ceo-chinese-spy.html). `missed:`

## Coverage notes

- **Verified items:** 5 (2 lead governance items, 2 inference-engine clusters, 1 community launch).
- **Ledger dedupe (suppressed as already reported):** ~10 candidates — Amazon robot job listings (CNBC, ledger `amazon-personal-robotics-group`), Apple $200 AI-cap story (HN, ledger `apple-engineer-ai-caps`), DOJ $129M AI-voice-scam figure (NYT river, ledger `missed:doj-129m-ceo-clone` — footnoted once already), Nous Arete-27B (Techmeme, ledger Nov 6), DeepSeek Harness 1.54M DAU (Bloomberg river, ledger Nov 8 17:00), Anthropic OnePWR 1.7GW (FT, ledger Nov 8 12:00), Ternus agentic-Siri push (Gurman, ledger Nov 8 12:00), Inner Mongolia Galaxy campus (SemiAnalysis, ledger Nov 8 07:00), environmental-groups AI/COP31 (Reuters, ledger Nov 8 07:00), and the core board-probe/affair disclosure itself (Nov 8, 3:25 PM ET, ledger `update:megaspeed-alice-huang`).
- **Stale-event exclusions (disclosed pre-window):** Ada/Dialpad ~$430M merger (disclosed ~Nov 3–4, Techmeme rerun Nov 9), CAISSI White House reporting concerns (AP, Nov 8 ~3:06 AM ET), ByteDance Brazil Phase 2 (Bloomberg, Nov 6), Applied Digital Harwood ND vote (vote Nov 4; WaPo feature Nov 8), Googlebook on-device RAM update (rollout began ~Nov 5).
- **Dead ends / limitations:** FT "AI agents vs. manager compensation" river item (URL unresolved); Ginux kernel HN story (zenodo record not fetched within budget, dropped); Reddit r/LocalLLaMA sweep returned discussion threads only — no in-window primary material; OpenAI's promised Nov 9 "post-midterm blitz" produced no verifiable in-window announcement.
- **Window discipline:** 07:00 ET fire; window computed once from the ET anchor helper; no items pulled across the boundary.
