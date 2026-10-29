# AI News Briefing — Oct 29, 2026: China's labs distilled GPT-6 Astra; Claude Code's hidden status-line tax

**Window:** 2026-10-28 17:00 EDT → 2026-10-29 07:00 EDT (previous fire → this fire; new items only)

## TL;DR

- **The Information: DeepSeek and Moonshot AI leaned heavily on distilling OpenAI's GPT-6 Astra** to build Kimi K3.1 Pro and DeepSeek-V4.1-Flash 1017 — landing right after Anthropic's anti-distillation crackdown cut harvested data by 90–99%.
- **Claude Code has been quietly burning 15–20% of token spend on a 3–5 word status line** for background agents (per a widely-shared Flamerix AI gist); Anthropic's Claude Code team contests the number and says a fix shipped in 2.1.311+.
- **FuriosaAI closed a $620M Series D at a $2.9B valuation** for its RNGD inference accelerator — 20,000 cards this year, 180W air-cooled, claims RTX PRO 6000-matching throughput at twice the users per kW.
- **Samsung's chip profit jumped 15-fold** on the AI boom, with HBM sold out for next year and a call for a tight memory market through 2028; **Marvell is in advanced talks to buy Lumentum (~$83B market cap)** for AI optics.
- **Data centers went political at the ballot box:** voters in 10 US states weigh 21 curbs on data-center construction alongside Tuesday's midterms.

---

## Models and local inference

### FuriosaAI closes a $620M Series D at $2.9B to scale its RNGD inference chip
**Event date:** October 28, 2026 (company announcement; Bloomberg/Techmeme 7:45 PM ET)

FuriosaAI said it completed a $620M (KRW 820B) Series D at a $2.9B post-money valuation — roughly four times the $735M mark from its prior round. Korea's National Growth Fund committed KRW 370B (~$280M), with Cisco Investments, Infinitum, Jericho Capital, NeoFort Holding, QUDA Capital and Worldline Capital joining; Ex-Im Bank of Korea, UAMCO and Hanwha Asset Management came in new, while NAVER Ventures, DSC Investment and KDB Capital re-upped. The company says RNGD has been in mass production since January 2026, will produce 20,000 cards this year, and has locked memory and fab capacity to more than double that in 2027.

**Why it matters:** This is a second-source AI *inference* chip — not a training part — pitched directly against Nvidia's professional cards on the metrics that matter for serving: the 180W air-cooled PCIe RNGD card is claimed to match RTX PRO 6000 throughput at the same card count while serving twice as many users per kW, and the 4U/3kW NXT RNGD Server packs 4 petaFLOPS FP8 and 384 GB of HBM3 into existing enterprise racks with no cooling retrofit. Furiosa names Gemma 4 and Qwen 3.8-27B as its target open-weight workloads at ~100 tok/s/user, and cites Samsung SDS's NPU-as-a-Service and LG Group on-prem deployments as production proof.

Sources:
- FuriosaAI, "FuriosaAI closes $620 million Series D to scale efficient, production-ready AI inference," October 28, 2026 — https://furiosa.ai/blog/furiosaai-620-million-series-d
- Business Wire via Morningstar, "FuriosaAI Secures $620 Million Series D…," October 28, 2026 — https://www.morningstar.com/news/business-wire/20261028655033/furiosaai-secures-620-million-series-d-to-meet-surging-enterprise-ai-inference-demand-and-accelerate-next-gen-silicon
- Techmeme river, October 28, 2026 7:45 PM ET (Bloomberg) — https://www.techmeme.com/river

### Samsung's chip profit jumps 15-fold; HBM sold out for 2027, tight through 2028
**Event date:** October 28–29, 2026 (Seoul results, reported from 11:20 PM ET Oct 28)

Samsung posted Q3 operating profit of 107.4 trillion won (~$80B), up from 12.2 trillion won a year earlier, with semiconductor operating profit surging 15-fold to 106.5 trillion won — nearly all of the group's profit but a hair under the 107.3 trillion won LSEG estimate. It said HBM supply is sold out for next year with key customers asking for more, projected a tight chip market through 2028, and guided to a 62% capex increase to 76.8 trillion won this year for the chip division plus another sharp rise next year. Bloomberg put total 2026 capex at ~$63B (+~60%), with HBM capacity sold out through 2027 and 10 long-term supply deals signed. Shares reversed an opening gain to fall as much as 6.2% on the absence of a new shareholder-return plan, while SK Hynix — which said Wednesday its DRAM and HBM are sold out through 2029 — rose.

**Why it matters:** Two Korean suppliers independently confirming multi-year memory scarcity (HBM sold out, shortage through 2028–2030) is the cost curve under every local-inference build.

Sources:
- Reuters, "Samsung sees AI chip shortage worsening, keeps investors in suspense on returns," October 29, 2026 — https://www.reuters.com/world/china/samsung-posts-record-profit-q3-ai-chip-boom-2026-10-29/
- Techmeme river, October 28, 2026 11:20 PM ET (CNBC) and October 29, 2026 1:20 AM ET (Bloomberg) — https://www.techmeme.com/river

### Marvell in advanced talks to acquire Lumentum in a potential AI-optics mega-deal
**Event date:** October 28, 2026 (Bloomberg report, ~6:26 PM ET)

Bloomberg reported — and Reuters confirmed with a source — that Marvell Technology is in advanced discussions to buy data-center optics maker Lumentum Holdings, with an announcement possible as soon as next week. Lumentum, worth roughly $83B, makes the lasers and optical components that move data between AI racks; its shares rose ~9% after hours while Marvell fell more than 8%. Bernstein noted Marvell holds only about $3.92B in cash, so a deal of that size likely needs new financing and carries integration risk. It would extend Marvell's optics push (Celestial AI, $3.25B) on top of the fiscal-2028 revenue target it raised to $20B from $15B on October 6.

**Why it matters:** Optical interconnect is becoming the bottleneck layer of AI infrastructure — Lumentum's capacity was reported sold out into early 2029 and Nvidia put $2B into it this year. Consolidation here raises the price of scale-out networking that every inference cluster ultimately rents.

Source:
- Reuters, "Marvell Technology in advanced talks to buy optics maker Lumentum, source says," October 28, 2026 — https://www.reuters.com/business/marvell-advanced-talks-buy-lumentum-bloomberg-news-reports-2026-10-28/
- Yahoo Finance/Investing.com, "Marvell nears deal for optical supplier Lumentum…," October 28, 2026 6:26 PM EDT — https://finance.yahoo.com/markets/stocks/articles/marvell-nears-deal-optical-supplier-222616085.html

### Inference engines overnight: vLLM lands MiniMax-M3.1-Flash kernels; llama.cpp ships quantized-KV flash attention on SYCL
**Event date:** October 28–29, 2026 (merged in-window)

vLLM merged a large overnight batch, headlined by **#64111 "[New Model] Add MiniMax-M3.1-Flash Model and Kernels"**, alongside ROCm MXFP8 fusion work (#63179 tiny-M MXFP8 quant fused into the block32 GEMM; #63915 MXFP8 activation quant fused into the 1x32 dot_scaled linear for M≤16), FP8 KV plus indexer caches on the AMD QSA path for Qwen4Exp (#61529), a DeepSeek-V4.1 prefill-memory budget fix (#63047), a batch-sharded draft-logits spec-decode refactor (#64356), and a KV-offload reliability cluster (#62444, #64256, #52838). It also deprecated `torch.compile` for Qwen3.5 models (#51012).

llama.cpp shipped builds b11970 (Oct 28 21:28 UTC) through b11981 (Oct 29 08:11 UTC). The notable ones: a SYCL DPAS flash-attention kernel that runs attention directly on q4_0/q8_0 KV blocks instead of dequantizing per call — measured on Arc B580 at Llama 3.1 8B Q4_K_M, tg128, 16K context, **31.3 → 54.2 t/s** — plus vectorized reordered dequantization stores (#30976) that lifted MXFP4 MoE prefill by **+57%/+61%** (pp2048/pp512) on Arc Pro B70 and Q6_K 27B prefill by +28.7%, and Hexagon conv2d/IM2COL work (#30995, #31050) for Snapdragon NPU builds.

**Why it matters (local inference):** Quantized-KV flash attention is the pattern that stretches a 16GB or 20GB card to longer agent contexts without the per-call dequant tax; the same code path that got +73% decode on Intel Arc is the one the CUDA and Vulkan backends are converging on. Native MiniMax-M3.1-Flash kernels mean the 428B-A23B open-weight release now has a first-class serving path rather than a community port.

Sources:
- GitHub, vllm-project/vllm merged pull requests (merged Oct 28–29, 2026) — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged+sort%3Aupdated-desc
- GitHub, ggml-org/llama.cpp releases b11970–b11981 — https://github.com/ggml-org/llama.cpp/releases

---

## Agent tools

### Claude Code's background-agent status line was eating 15–20% of token spend — and there was no switch
**Event date:** October 28–29, 2026 (gist posted and front-paged on Hacker News ~11:30 PM ET Oct 28; 403 points)

Flamerix AI published an analysis of `agent_summary`, a Claude Code behaviour that has been on by default since version 2.1.232 (August 2026): every ~30 seconds, for every background agent, Claude Code sends that agent's **entire transcript** to the agent's own model — usually Opus — asking it to "describe your most recent action in 3–5 words," and uses the answer only to paint a status line in the agents panel. Reading Claude Code's own OpenTelemetry export, the author measured `query_source = agent_summary` at **16.4% of one week's spend versus 15.7% for the main conversation**, 19.7% of all API requests, 96% of that on Opus, at roughly 9,000 input tokens per output token — and notes issue #86879 collecting four independent measurements of 18–26%. The workaround is an ~80-line Bun preload (`no-agent-summary.js`, MIT) that wraps `setTimeout`/`setInterval` and refuses to arm any timer whose callback source contains the `[AgentSummary] Timer fired` anchor; verified across 2.1.257–2.1.312 on Linux.

Anthropic's Claude Code engineer edwinarbus replied in the thread with a different number: across telemetry-sending users, `agent_summary` is roughly **2% of API-list-price cost (6% in the terminal)**, arguing the 16.4% reflects "a team running mostly background agents." He said a cache-miss fix already shipped in **2.1.311+** and that the team is building "a much cheaper mode that sends short notes to a smaller model."

**Why it matters:** Whatever the true percentage, this is the recurring failure mode of agent tooling — silent Opus-tier calls inside the harness, invisible to the model and the user, that consume rate-limit headroom rather than a billed invoice on a subscription plan. If you run background agents at scale, the actionable takeaways today are: check your OTel `query_source` breakdown, upgrade past 2.1.311, and treat any always-on loop as a cost surface. Flamerix itself warns the shim matches on implementation-specific strings and a Claude Code update can silently undo it.

Sources:
- Flamerix AI, "Claude Code burns ~15-20% of your tokens on a 3-word status line…," gist, last active October 29, 2026 10:44 UTC — https://gist.github.com/flamerix-ai/c3905705cc4205e5de3011fa2e74efce
- Hacker News front page, October 29, 2026 03:33 UTC (403 points) — https://news.ycombinator.com/item?id=50284255
- AI Weekly, "Flamerix gist: Claude Code status line ate 16.4% of weekly spend," published October 29, 2026 06:31 UTC — https://www.aiweekly.co/alerts/flamerix-gist-claude-code-status-line-ate-164-of-weekly-spend

### Fragment exits stealth with $56M to run procurement exceptions as AI agents
**Event date:** October 28, 2026 (Axios Pro exclusive; Techmeme 12:05 AM ET Oct 29)

Fragment, which uses AI to resolve purchasing and payment exceptions across messy manufacturing datasets, came out of stealth with $56M — a Series A led by 8VC plus a seed co-led by General Catalyst and A*. CEO Pranav Mulgund says the company's "Autonomous Context Engine" reconciles invoice mismatches, order changes and payment holds inside customers' existing ERP systems, with Graybar and CNX as named deployments; roughly 20% of transactional procurement is still done by hand.

**Why it matters:** A useful template for vertical agent products — no new model, just cross-system context reconciliation plus write-back into systems of record, sold against a quantified manual-work backlog. It is also another datapoint in the week's pattern of agent funding landing outside chat/coding.

Sources:
- Axios Pro, "Procurement startup Fragment launches with $56M," October 28, 2026 — https://www.axios.com/pro/enterprise-software-deals/2026/10/28/fragment-procurement-software-botha
- TechFundingNews, "Fragment launches with $56M for autonomous procurement AI agents," October 28, 2026 — https://techfundingnews.com/fragment-56m-series-a-procurement-exceptions-ai-agents/

---

## AI security and governance

### DeepSeek and Moonshot relied heavily on distilling OpenAI's GPT-6 Astra
**Event date:** October 28, 2026 (The Information; Reuters pickup)

The Information reports, citing people with direct knowledge, that DeepSeek and Moonshot AI leaned heavily on outputs from OpenAI's GPT-6 Astra — the strongest model OpenAI has released, shipped in early September — when training their latest models: Moonshot's Kimi K3.1 Pro (released last week) and DeepSeek's upgraded V4.1-Flash 1017 (launched October 17). Reuters picked the story up and noted the timing: OpenAI said in a September 30 post that it had tied a distillation campaign begun in July to individuals associated with Moonshot AI, and Anthropic said on October 13 that new safeguards cut data harvested in distillation attacks by roughly 90–99%. OpenAI, DeepSeek and Moonshot did not respond to Reuters' requests for comment.

**Why it matters:** This is the clearest public account yet of *which* frontier model Chinese open-weight labs are now mining, and it reframes the open-weight price war. If the cheapest-performing open models carry distilled Astra behaviour, then capability, safety posture and any Astra-specific quirks are propagating into weights nobody audits — and the vendors' "clean-room, from-scratch" framing gets harder to defend. It also gives OpenAI and Anthropic a fresh lever for access controls, watermarking and ToS enforcement, and hands policymakers another national-security hook on top of the DHS DeepSeek-removal order.

Sources:
- The Information, "OpenAI's Astra Model Becomes Top Distillation Target for China's AI Labs," October 28, 2026 — https://www.theinformation.com/articles/openais-astra-model-becomes-top-distillation-target-chinas-ai-labs
- Reuters, "DeepSeek, Moonshot used OpenAI's GPT-6 Astra to train latest models, The Information reports," October 28, 2026 — https://www.reuters.com/technology/deepseek-moonshot-used-openais-gpt-6-astra-train-latest-models-information-2026-10-29/

### Data-center buildout goes to the ballot box in 10 states
**Event date:** October 29, 2026 (Reuters, 6:02 AM EDT)

Voters in 10 US states will decide on Election Day whether to curb data-center construction in their communities, with more than a dozen jurisdictions considering **21 ballot proposals** alongside Tuesday's midterms — the most data-center measures ever on a single ballot, according to Ballotpedia. Support for banning certain data-center construction is coming from both Democrats and Republicans; local organizer Jessica Baker said residents remain opposed even when developers propose on-site gas turbines or nuclear reactors, citing worries about children's health such as asthma. Amazon declined to say whether the initiatives affect its expansion plans.

**Why it matters:** Permitting, power and water are now a voter-level risk, not just a procurement one. Twenty-one simultaneous measures means the industry's siting playbook — off-grid power, "no new grid load" promises — is being tested as a political argument in real time, and the results will price future capacity well before supply catches up with the Samsung/SK Hynix shortage outlook.

Source:
- Reuters, "AI data center boom faces backlash at US ballot box," October 29, 2026 6:02 AM EDT — https://www.reuters.com/legal/litigation/ai-data-center-boom-faces-backlash-us-ballot-box-2026-10-29/

---

## Also noted

- **Microsoft booked $10B of revenue from OpenAI in Q1** — nearly 11% of its quarterly revenue — with $7.2B in outstanding receivables from OpenAI, per its filing. *Event date:* October 28, 2026 (6:10 PM ET). Rebecca Bellan, TechCrunch — https://www.techcrunch.com/2026/10/28/microsoft-openai-revenue/ *(Techmeme river entry 6:10 PM ET — https://www.techmeme.com/river)*
- **US computer-science enrollment fell more than 12% for undergraduates and 10%+ for graduates this fall**, per the National Student Clearinghouse, which the outlet attributes to AI reducing coding jobs. *Event date:* October 29, 2026 (2:02 AM ET). Bloomberg via Techmeme — https://www.techmeme.com/river
- **Sierra Club expects US power emissions to reach 2.1B metric tons by 2030, up 42% on 2025**, erasing 15 years of cuts, with off-grid data centers now matching Mexico's total. *Event date:* October 29, 2026 (6:25 AM ET). Bloomberg via Techmeme — https://www.techmeme.com/river
- **WSJ: AI hyperscaler demand has quintupled flash-memory prices**, shifting negotiating power from Apple to suppliers such as Kioxia. *Event date:* October 28, 2026 (11:55 PM ET). Rolfe Winkler, Wall Street Journal via Techmeme — https://www.techmeme.com/river
- **Software stocks slid premarket after Fortinet and ServiceNow missed expectations**, deepening "AI disruption" worries across the sector. *Event date:* October 29, 2026 (6:19 AM EDT). Reuters — https://www.reuters.com/technology/software-stocks-slide-fortinet-servicenow-fail-meet-lofty-investor-expectations-2026-10-29/
- **Hugging Face Daily Papers (Oct 29)** are led by *DTCA: Denoising Token Credit Assignment for Reinforcement Learning in Diffusion Language Models* (ZJU-OmniAI, 256 upvotes), with *TAO: Tool-Aware Joint Scheduling for LLM-Based Agentic Serving* also listed. — https://huggingface.co/papers

---

## Just missed the window

- **Qwenputer — a working, deterministic RISC-V CPU compiled into Qwen3.5-4B's weights.** First disclosed October 28, 2026 (~11:14 AM ET per Adafruit's write-up of divagr1925's post): an RV32IM RISC-V core whose "RAM" is the model's own Gated DeltaNet state, running C and MicroPython inside forward passes with no external runtime, passing official riscv-tests, 100% on NUPA long-integer arithmetic versus 48.6% for the base model. It front-paged Hacker News inside this window with 495 points, but the disclosure itself was ~6 hours pre-window. https://blog.adafruit.com/2026/10/28/qwenputer-a-risc-v-cpu-inside-a-models-weights/ · https://github.com/divagr18/qwenputer · https://news.ycombinator.com/item?id=50282838
- **Cognition (Devin) weighing a new round at $70–80B**, weeks after its September $48B round, per Business Insider. Published **2026-10-28T15:56Z (11:56 AM ET)** — four minutes before the noon fire that should have carried it; Techmeme only surfaced it at 8:20 PM ET. https://www.businessinsider.com/cognition-round-of-funding-at-a-75-billion-valuation-2026-10

---

## Coverage notes

- **Verified items in this edition:** 11 full items (6 lead, 5 secondary) + 6 "also noted" one-liners + 2 first-time "just missed" footnotes. Every included URL was fetched and carries an in-window date; the five lead stories (Astra distillation, Claude Code agent_summary, FuriosaAI, Samsung, Marvell–Lumentum) were read in full.
- **Ledger dedupe count:** **9 in-window candidates were suppressed as already reported** by prior runs — Gemini 4 Carbon's Fairwind rollout/pricing re-coverage (ledger `gemini-4-carbon`), Microsoft Q1 Azure/capex/FCF re-coverage (`msft-fy27q1-azure-48pct`), Alphabet search-revenue slowdown and AI Overviews ad expansion (`googl-q3-cloud-103pct`), the DOJ/Nvidia/Hugging Face antitrust repeat (`doj-nvidia-huggingface`), Artificial Analysis Index v5 re-coverage (`aa-intelligence-index-v5`), GitHub Octoverse / Copilot Fusion re-coverage (`octoverse-2026`, `copilot-fusion-ga`), the Apple touchscreen MacBook Pro analysis pieces (`mbp-proled-touch-m5`, `mac-studio-m5-ultra-512gb`), and the Nscale CEO IPO profile (`nscale-100b-memo`). Two additional pre-window stories were footnoted for the first time (Qwenputer, Cognition) and are tagged `missed:` in the ledger.
- **Dead ends / limitations:** divagr.com blocked web_extract ("targets a private or internal network address"), so Qwenputer is cited from Adafruit, GitHub and Hacker News instead; MLex's Korea AI Basic Act amendment and BC privacy-probe stories are paywalled with no fetchable copy found, so they were dropped rather than cited from snippets; the HN "StackOverflow now gets fewer than 1000 questions a month" item (486 points, in-window) was excluded because the linked target is a live data.stackexchange query with no verifiable publication timestamp.
- **Indexes:** Techmeme river, Hugging Face Daily Papers, GitHub (llama.cpp releases, vLLM merged PRs), Hacker News Algolia front-page feed — all healthy. BleepingComputer and The Hacker News produced no in-window AI items matching this brief's scope.
