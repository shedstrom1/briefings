# AI News Briefing — Friday, November 20, 2026, 5:00 PM ET

**Window: Friday, November 20, 2026, 12:00 PM → 5:00 PM ET (previous fire → this fire). New items only; already-reported events excluded per ledger.**

## TL;DR

- **Nvidia fell 14% as Reuters reported the SEC's probe is examining its accounting**; the Nasdaq erased all 2026 gains and the S&P 500 entered correction territory — Nvidia has now lost ~$2.7T since October 6.
- **CrowdStrike is in talks to acquire Zscaler** — Bloomberg says it would be among the largest-ever cybersecurity deals; Zscaler rose 18% after hours, CrowdStrike fell 8%.
- **Trump threatened to block or slather a "tremendous tax" on US companies building data centers overseas**, while Treasury Secretary Bessent said Trump and Xi agreed humans must stay in the loop on military AI decisions.
- **NYT/Strider: ~$3.6B in Chinese state money flowed through Alice Huang's family network**, with ties to PLA missile/radar/aircraft contractors — the freshest leg of the Nvidia crisis.

## Models and local inference

### Thinking Machines Lab ships Inkling 2 Mini: an open-weights 26B "looped" transformer built to run on laptops
**Event date: Friday, November 20.** TML released Inkling 2 Mini, a 24-layer Mixture-of-Experts model with 26.4B total / 3.4B active parameters (5.4B weight activations per token) that runs its two middle blocks of six layers three times per token — more reasoning per token without writing a long chain of thought. TML says it leads every open-weights model under 100B on DeepSWE v1.1 (48.2%) and Vals Index v2.2 (46.5%), beats Qwen3.8-27B on SWE-bench Pro v2 (77.9% vs 73.8%) and Vibe Code Bench (77.9% vs 64.9%), but trails it on multi-turn chat (MultiChallenge 50.0%) and complex constraints. Most weights are trained and deployed natively in NVFP4 (last four layers MXFP8) for a ~20GB footprint
Why it matters: looping is the first production-scale attempt to buy reasoning quality with compute rather than parameters, and its stated monitorability trade-off (shorter, less controllable chains of thought) is now a public research artifact. For local-first users it lands as the rare open release that is efficient *by architecture*, not just by quant.
Sources:
- Introducing Inkling 2 Mini — thinkingmachines.ai, Nov 20, 2026 — https://thinkingmachines.ai/news/inkling-2-mini/
- thinkingmachines org (Inkling-2-Mini + TML-d24-varloop-research model pages, published today) — huggingface.co, Nov 20, 2026 — https://huggingface.co/thinkingmachines
- Day-0 support commits: llama.cpp "model: Inkling 2 Mini" #32694; vLLM "[Recipe] Inkling 2 Mini: serve the full 1M context on one GPU" #69039 — github.com, Nov 20, 2026 — https://github.com/ggml-org/llama.cpp/pull/32694 · https://github.com/vllm-project/vllm/pull/69039
- Front-page HN discussion (339 points) — https://news.ycombinator.com/item?id=50581260

### Update — the Anthropic/Claude "proved GRH" claim rolls on: 17.4M lines of Lean, Aaronson weighs in, and a competing all-human GRH draft appears
**Event date: Friday, November 20 (in-window reactions; underlying claim first reported this morning).** Following this morning's Scientific American piece (already briefed), Anthropic mathematician Levent Alpöge says Claude-assisted work produced a Generalized Riemann Hypothesis proof with a 17.4-million-line Lean certificate. Scott Aaronson posted at 12:29 PM ET that he sees "zero indications" of Lean-kernel exploitation and "my bet is that the proof will stand," noting no AI result with a Lean certificate has needed retraction (several without one have). Minutes earlier (10:18 AM ET, pre-window), Ravi Vakil made a "hurried announcement" on Tao's math216 blog releasing Alessandro Masullo's proposed GRH proof — "all human, with no AI" — months-vetted by Brian Conrad, pushed out early because of the AI claim's timing.
Why it matters: whether or not the certificate holds, formal Lean proofs have just become the acceptance currency for AI-math results, and the field is now racing AI-assisted and human proofs against each other in public.
Sources:
- The stochastic parrots have apparently proved the Riemann Hypothesis — Scott Aaronson, Shtetl-Optimized, Nov 20, 2026 12:29 PM ET — https://scottaaronson.blog/?p=10344
- Hurried Announcement: Alessandro Masullo GRH — Ravi Vakil, math216 (Tao blog), Nov 20, 2026 10:18 AM ET — https://math216.wordpress.com/2026/11/20/hurried-announcement-alessandro-masullo-grh/

### Also noted — "Vibecoding an inference chip": coding agents wrote 3M lines to run an LLM on an FPGA
Blog published today and HN front-page-adjacent (78 points at window close): a solo engineer used Codex on AWS F2 FPGA instances (16GB HBM) to implement LLM inference in Verilog/C++ — 3M lines of agent-written code, a working 8,192-token-context ternary-weight model (DeepGrove's architecture), "they are incredible at programming them." Relevant as a signal that agent-driven hardware bring-up is becoming a hobbyist sport. Source: https://ankitg.me/blog/vibecoding-inference-chip/ (Nov 20, 2026) · HN: https://news.ycombinator.com/item?id=50579297

## Agent tools

### Engine wrap — Inkling day-0 across the open stack; SGLang adds Hunyuan-4-Flash
Between noon and 5 PM ET: llama.cpp landed 12 commits with builds b12589/b12590 (RDNA1 MMVQ table #32389, drama3 voice cloning #32586, qwen3-asr language-in-thinking-block #32571, Inkling 2 Mini mainline #32694); vLLM 18 commits (Inkling 2 Mini recipes #69027/#69039, /v1/decisions predicate read fix #68917, Rust-frontend additional_tools #69019, Transformers-backend Omni support #60648); SGLang 25 commits (Hunyuan-4-Flash text model #51668, Hy4-Flash IndexPool/Engram kernels #51646, gfx950 TLX GEMM + Muse MoE kernels #50354, Inkling-2-Mini cookbook #51927); Ollama quiet (1 test commit). SGLang's Hunyuan-4-Flash work is incremental support for Tencent's model reported Nov 16. Source: github.com commit logs, Nov 20, 2026 — https://github.com/ggml-org/llama.cpp/commits/master · https://github.com/vllm-project/vllm/commits/main · https://github.com/sgl-project/sglang/commits/main

## AI security and governance

### Update — Nvidia sinks 14% as Reuters reports the SEC probe is examining its accounting; Nasdaq erases all 2026 gains
**Event date: Friday, November 20.** Reuters reported ~3:25 PM ET that SEC investigators are examining potential accounting irregularities at Nvidia — including how it books revenue through its Singapore unit (~$104B, roughly half of 2025 sales, per the FT, with Singapore customer Megaspeed flagged); the WSJ had reported Tuesday the probe covers whether disclosures misled investors (footnoted then as outside-window). NVDA closed -14.01% at $125.53 — ~$485B lost in a day, ~$900B this week (worst since October 2008), ~$2.7T off since October 6, now the #4 US company by market cap. The market-wide toll: Nasdaq -5.3% and back to flat for 2026; S&P 500 -3.7% for a seven-day losing streak (worst since the pandemic) and correction territory (-10% from peak); Dow -1.9%; Anthropic -9% on day two (already briefed). Wired reports the board's main ouster reason for Jensen Huang was "lying" and "lack of candor" since October. Treasury Secretary Bessent has demanded answers "in the next 10 days" from Nvidia's board and outside counsel.
Why it matters: the Nvidia crisis has moved from export-control theater to accounting exposure — securities law plus a delayed 10-Q and an independent-committee investigation is a materially different risk class, and it is repricing the whole AI trade.
Sources:
- Nvidia stock extends $2.7 trillion rout as SEC probe reportedly investigates its accounting — Yahoo Finance, Nov 20, 2026 4:35 PM EST — https://finance.yahoo.com/markets/article/nvidia-stock-extends-25-trillion-rout-as-sec-probe-reportedly-investigates-its-accounting-194945274.html
- Markets plunge: Nasdaq erases 2026 gains, S&P and Dow enter corrections — NBC News, Nov 20, 2026 9:20 AM EST, updated 4:37 PM EST — https://www.nbcnews.com/business/markets/anthropic-shares-markets-selloff-nasdaq-rcna610394
- Nvidia shares closed down 14% on Friday — CNBC (Kif Leswing), Nov 20, 2026 3:10 PM ET (via Techmeme river) — https://www.techmeme.com/river
- Board ouster-reason reporting: Wired (sources), Nov 20, 2026 3:20 PM ET (via Techmeme river) — https://www.techmeme.com/river

### CrowdStrike is in talks to acquire Zscaler — would be among the largest cybersecurity deals ever
**Event date: Friday, November 20 (~4:20 PM ET).** Bloomberg reported CrowdStrike is in talks to buy Zscaler; Reuters and CNBC confirmed the story within the hour. Zscaler jumped 18% after hours while CrowdStrike fell 8%+. A completed deal would combine a $235B acquirer with a $32B target — beating Google–Wiz ($32B), Cisco–Splunk ($28B) and Palo Alto–CyberArk ($25B) — and lands weeks after CrowdStrike's own Fiddler AI and Dreadnode agentic-security deals. Bloomberg describes the talks as ongoing; no terms disclosed.
Why it matters: endpoint + zero-trust-edge consolidation under one AI-era security vendor would reshape the competitive map right as buyers are bolting AI agents onto SOC workflows — and CrowdStrike's stock falling while Zscaler's soars signals investor skepticism about the price.
Sources:
- CrowdStrike in Talks to Acquire Zscaler in Potential Mega-Deal — Bloomberg, Nov 20, 2026 4:20 PM ET — https://www.bloomberg.com/news/articles/2026-11-20/crowdstrike-is-in-talks-to-buy-cybersecurity-firm-zscaler
- Zscaler stock jumps 18% on report that CrowdStrike in talks to buy security vendor — CNBC, Nov 20, 2026 — https://www.cnbc.com/2026/11/20/zscaler-crowdstrike-deal.html
- CrowdStrike in talks to acquire Zscaler, source says — Reuters, Nov 20, 2026 — https://www.reuters.com/legal/litigation/crowdstrike-talks-acquire-zscaler-source-says-2026-11-20/

### Trump threatens to block US companies from building data centers overseas — or tax them "tremendously"
**Event date: Friday, November 20 (~1:35 PM ET, White House briefing).** Trump said "we're not going to allow our companies to build data centers outside of our country," threatening a "tremendous tax" otherwise and claiming "China will take all of them" if they go abroad. He said he hadn't even briefed his own staff; Axios notes the tax would require Congress and the mechanism is unspecified. Backdrop: voters backed limiting data-center development in 52 of 53 ballot measures through this week, and Trump paired the remarks with praise for Xi on US "super intelligence" progress, saying the two agreed on "joint guardrails on AI" (see next item).
Why it matters: an outbound-build tax would hit exactly the latency/sovereignty-reasoned overseas expansions hyperscalers rely on, and — coming days after the BIS remote-access rule — marks another shift of data-center policy from permits to trade-war tooling.
Sources:
- Trump threatens tax on US companies building data centers overseas — Axios (Julianna Bragg), Nov 20, 2026 1:35 PM ET — https://www.axios.com/2026/11/20/us-ai-trump-data-centers-overseas
- Trump seeks curbs on US-built data centers overseas — The Hill (Mallory Wilson), Nov 20, 2026 — https://thehill.com/homenews/administration/6233765-trump-threatens-data-center-tax-overseas/
- Trump says US to penalize firms building data centers abroad — Reuters, Nov 20, 2026 — https://www.reuters.com/technology/trump-says-us-penalize-firms-building-data-centers-abroad-2026-11-20/

### Update context — Bessent: Trump and Xi agreed humans must stay in the loop for military AI decisions
**Event date: Friday, November 20 (Reuters at 12:20 PM ET).** Treasury Secretary Bessent said at the Shenzhen APEC sidelines that Trump and Xi agreed "humans should always be involved in any military decisions that SI makes, and the SI should not be allowed to be autonomous for major military actions." No joint statement or treaty text exists, and neither "major military actions" nor "human involvement" is defined; Bessent also said H200 sales to China remain capped by a Trump-drawn "very firm line" and that Chinese orders have been sparse as China pushes its own stack.
Why it matters: a verbal US–China understanding on human-in-the-command for military AI is the highest-level guardrail statement of the year — but with no definitions, verification or written text, it is a political marker, not a control regime.
Sources:
- Trump, Xi agreed that military AI decisions should involve humans, Bessent says — Reuters, Nov 20, 2026 12:20 PM ET — https://www.reuters.com/world/china/trump-xi-agreed-that-military-ai-decisions-should-involve-humans-bessent-says-2026-11-20/
- Bessent: Trump, Xi Agreed Humans Must Stay in Military AI Decisions — AI Weekly summary of the Reuters/CNBC report, Nov 20, 2026 18:33 UTC — https://aiweekly.co/alerts/bessent-trump-xi-agreed-humans-must-stay-in-military-ai-decisions

### Update (ledger: megaspeed-alice-huang) — NYT/Strider: ~$3.6B in Chinese state money flowed through Alice Huang's family network
**Event date: Friday, November 20 (NYT at 1:30 PM ET).** A New York Times investigation with Strider Technologies found companies in Alice Huang's family and business network received about $3.6 billion in Chinese government funding and are tied to state-owned contractors supplying the PLA's missile, radar and military aircraft programs. Separately (2:35 PM ET), Bloomberg reported Trump said he "would love to see" Jensen Huang return as CEO, saying Nvidia "should be smart" to bring him back — extending the reinstatement pressure line already briefed, while the board's search for a successor runs against the new accounting-probe overhang.
Why it matters: the probe's center of gravity keeps moving from chip diversion toward national-security finance flows around the Huang network — an escalation that makes the compliance answers Bessent demands in 10 days considerably harder.
Sources:
- Spying Suspect's Network Has Ties to Chinese Military Suppliers — New York Times, Nov 20, 2026 1:30 PM ET — https://www.nytimes.com/2026/11/20/us/politics/nvidia-alice-huang-chips-china.html
- NYT: Alice Huang's family network passed $3.6B in Chinese state money to military — AI Weekly summary, Nov 20, 2026 — https://aiweekly.co/alerts/nyt-alice-huangs-family-network-passed-36b-in-chinese-state-money-to-military
- Trump says he "would love to see" Jensen Huang return as CEO — Bloomberg, Nov 20, 2026 2:35 PM ET (via Techmeme river) — https://www.techmeme.com/river

### Also noted — Cognition in funding talks at a $75B valuation, two months after its last round
Bloomberg reported Friday afternoon that the Devin/Windsurf maker is talking itself up to $75B — versus ~$48B reported in late August; Reuters relayed within the hour (a $70–80B consideration was footnoted once on Oct 29; this is a new, specific development). Sources: https://www.bloomberg.com/news/articles/2026-11-20/cognition-in-talks-for-75-billion-value-with-new-funding · https://www.reuters.com/technology/cognition-ai-talks-be-valued-75-billion-latest-funding-round-bloomberg-news-2026-11-20/

## Just missed the window
- OVHcloud's AI arm OVHai released Olala-v1.0-mini-7A1B on Nov 19 — Apache-2.0 open-weight 6.8B-total/1B-active Mamba-3/attention MoE (only 7 of 36 layers keep KV cache), 12.8T tokens, "runs on a laptop," EU AI Act training-content summary included. (One-time footnote; first reported here as outside the window.) Source: https://huggingface.co/OVHaiLLM/Olala-v1.0-mini-7A1B

## Coverage notes
- **Verified count: 10 items in this edition** (7 new + 3 material updates) plus 1 "just missed the window" footnote; every item's supporting page was fetched with an in-window timestamp, except paywalled primaries (Bloomberg/NYT/Reuters), where a fetched copy or attributed river/syndicated summary is cited alongside the primary URL.
- **Ledger dedupe: ~14 candidates suppressed** as already reported by previous fires — incl. Nvidia–Inception talks, Anthropic greenshoe/day-2 trading, Cursor PRC-model phaseout, SK Hynix–Kioxia stake, Signal screenshot restriction, Apple agent team, Hermes-in-Teams, this morning's engine wave, GPT-Live 1.1, and the NYT "Humiliation Routine" piece (already footnoted once).
- Community lane down: Reddit anonymous feeds returned 404 on both routes this fire (known intermittent); HN lane covered fully (11 stories ≥40 pts in-window).
- Dead ends: MIT Technology Review's METR time-horizon update (river at 2:05 PM ET) could not be resolved to a fetchable URL within two strikes — noted only; Reuters/Bloomberg/NYT direct fetches hit paywall walls, handled as above.
- No new model releases on OpenRouter/HF other than those briefed in-window; OpenClaw v2026.11.6 stable shipped yesterday (out of window).
