# AI News Briefing — Thursday, November 12, 2026 (12:00 ET edition)

**Window: 2026-11-12 07:00 → 12:00 EST** (since this job's previous fire; 11 verified in-window items — 4 full entries + 7 also-noted — plus 3 "just missed the window" footnotes)

## TL;DR

- **Jensen Huang resigns as Nvidia CEO, president and board member**, effective immediately; CFO Colette Kress is interim CEO, ex-Synopsys CEO Harvey C. Jones becomes the first-ever board chair, and shares fell as much as 12% intraday — roughly $500B of market value — the cap on a week-long scandal cascade this brief has tracked since October 28.
- **Nebius Q3: revenue +571% to $980.8M beat consensus, but the stock sank 16%** after the neocloud lifted FY2026 capex to $27B and guided FY2027 capex to $40–45B (FactSet: $28.77B) with revenue only at the low end of its $3–3.4B range.
- **LangChain raised $300M at a $3B valuation led by Alphabet's CapitalG** (WSJ), 2.4x its October 2025 mark, as it pushes its agent-development platform into large enterprises.
- **Inception Labs is winding down its Mercury diffusion-LLM API** — customers received wind-down emails today with a December 28 cutoff, less than a month after Mercury 3's flagship launch.

---

## AI security and governance

**Nvidia CEO Jensen Huang resigns; CFO Colette Kress named interim as the search begins** — *Event date: November 12 (announcement this morning)* — *Update on previously reported (ledger: megaspeed-alice-huang cascade)*

The NVIDIA Board announced that Jensen Huang, co-founder and CEO for 33 years, has resigned as President, CEO and board member, effective immediately; CFO Colette Kress takes the interim role while the Board searches internal and external candidates, and Harvey C. Jones (ex-Synopsys CEO) was appointed the company's first-ever Chair of the Board. Reuters reports shares tumbled more than 12% in early trading — on course to shed about $500 billion — while rivals AMD and Broadcom rose 2% and 5%; Kress told employees in a memo the company went through "an extraordinarily difficult period" and set an all-hands for November 18 at 9 a.m. PT, and Q3 FY2027 earnings remain scheduled for November 17. President Trump again weighed in, calling for Tim Cook; House Speaker Mike Johnson said there is a "growing consensus" in Congress on limiting chip exports to China.

*Why it matters:* This is the AI industry's biggest key-man event — Huang personally orchestrated the neocloud/labs financing web, the China market strategy, and the Washington relationship. For anyone whose local-inference roadmap rides on Blackwell/Rubin pricing and export policy, the interim CFO leadership (reportedly more conservative on riskier deals) plus the House's hardening stance on chip exports is a variable to watch through Q3 earnings next Tuesday.

- Source: NVIDIA Announces CEO Transition — November 12, 2026 — https://nvidianews.nvidia.com/news/nvidia-announces-ceo-transition
- Source: Nvidia CEO Jensen Huang resigns in big blow to AI boom (Reuters) — November 12, 2026 — https://www.reuters.com/business/retail-consumer/nvidia-ceo-jensen-huang-step-down-big-blow-ai-boom-2026-11-12/
- Source: Nvidia board reportedly weighs ousting CEO (BBC live timeline) — November 12, 2026 — https://www.bbc.com/news/live/c6zjry11v8w1t

**Also noted (governance):**
- IMF: "too early to say" whether a broad AI reassessment is underway, as the fund monitors markets after the post-midterm selloff deepened on the Huang resignation (Reuters, 11:36 AM EST). — https://www.reuters.com/business/retail-consumer/imf-monitoring-ai-developments-too-early-say-if-ai-reassessment-underway-2026-11-12/

## Models and local inference

**Nebius Q3 blowout-on-the-books, weak-on-the-guide — NBIS -16%** — *Event date: November 12, 7:30 AM EST release* — *New item*

Nebius reported Q3 revenue of $980.8M, up 571% year-over-year and above the $907.5M LSEG consensus, with a net loss of $37.5M (13c/share) vs $119.6M a year earlier — but the stock sank 16% as CEO Arkady Volozh guided FY2026 revenue to the low end of the $3–3.4B range and hiked FY2026 capex to $27B (from $20–25B; FactSet $23.85B), with an initial FY2027 capex outlook of $40–45B versus $28.77B expected. The company said it raised $5.75B in debt financing during the quarter; Nvidia itself held ~$329M of Nebius stock at end-June.

*Why it matters:* Neoclouds are the balance-sheet edge of the GPU supply chain; doubling the forward capex guide while a CoreWeave bond yield just spiked past 16% is exactly the financing-sustainability question now pricing AI hardware. Compute demand "running ahead of supply," per Nebius' CRO — but the capital cost of that promise keeps rising.

- Source: Nebius falls 16% as company expects increased 2026 capex spend, issues weak outlook (CNBC, Jordan Novet) — November 12, 2026, 7:30 AM EST — https://www.cnbc.com/2026-11-12/nebius-q3-earnings-report-2026.html

**Inception Labs abandons its Mercury diffusion-LLM API — wind-down email today, cutoff December 28** — *Event date: November 12 (customer emails)* — *Update on previously reported (ledger: Mercury-3, Oct 19)*

Customers received an email from hello@inceptionlabs.ai announcing the service "will remain fully available through December 28, 2026," after which access to the platform and submitted data ends; the company gave no reason and has posted no public announcement. Signs preceded the email: Mercury 2 and 2.5 are no longer offered to new users (existing access through November 15), Mercury 3 has been pulled from OpenRouter (developer bug report filed November 12; some users say November 8), and Mercury 3 was still being advertised at half price. The Ask HN thread surfacing the email hit the community mid-morning.

*Why it matters:* Mercury 3 launched October 19 as the flagship commercial diffusion LLM (vendor claim: 1,068 tok/s) and now disappears as a hosted service in six weeks. If you built on Mercury for fast drafting, migrate or capture outputs before December 28; older open-weight Mercury generations are the only self-hostable fallback.

- Source: Inception tells customers it will shut down its Mercury diffusion-model API on 28 December (AI Brief) — November 12, 2026 — https://dailyaibrief.com/news/inception-labs-shut-down-mercury-api-december-28-FJf5Gxgy
- Source: Ask HN — "Inception's Diffusion LLM API Is Shutting Down" (email quoted in full) — November 12, 2026, ~11:43 AM EST — https://news.ycombinator.com/item?id=50470335

**Also noted (models and local inference):**
- **llama.cpp engine watch:** build b12319 (11:34 AM EST) and four merges since the last fire — CUDA few-row MMQ for batch sizes 2–24 (#31533, helps small-batch decode/agent loops), server cancels MCP tool calls on client disconnect/timeout (#32025), qwen3.5 merge-tensors-on-convert (#32102), Hexagon Q5_0/Q5_1 (#32094). Otherwise engines were quiet: zero vLLM merges in-window and no new Ollama/SGLang releases (latest: v0.40.17-rc0 Nov 10, v0.5.23 Nov 2). — https://github.com/ggml-org/llama.cpp/releases
- **smolmaxxing (Show HN, 10:23 AM EST):** MIT-licensed measurement repo + llama.cpp patches running LLMs on absurdly small hardware — up to a 16B MoE (Ling-mini-2.0) at 5.1 tok/s on a 1.9GB Xiaomi TV box via expert paging from flash, and a 13-year-old iPad Air (1GB RAM) hitting 24–26 tok/s on a 1-bit Bonsai-1.7B.
- **Libreagi — "Save OSS Models" (HN, 9:11 AM EST):** a private-Tracker (apply-to-join) BitTorrent archive dedicated to preserving open-weight models in safetensors and GGUF; HN debate centered on why open-licensed content sits behind auth (1:1 ratio enforcement, seed load). — https://news.ycombinator.com/item?id=50467439
- **Update (Raon-1-nano, reported Nov 11):** community PR runs KRAFTON's official Raon-1 Nano 3.1B GGUF via ik_llama.cpp with full native runtime plus MTP speculative decoding. — https://www.reddit.com/r/LocalLLaMA/comments/1xue3d4/pr_run_kraftons_official_raon1_nano_31b_gguf_via/
- **Hardware rumor watch [rumor]:** r/LocalLLaMA flags an alleged 96GB RTX 5090 (modded VRAM) listed for sale in China — unverified, but the modded-card pipeline keeps moving. — https://www.reddit.com/r/LocalLLaMA/comments/1xucffq/alleged_geforce_rtx_5090_with_96gb_listed_for/
- **Google/Samsung Android XR "Intelligent Eyewear" preorders opened today** (WIRED, 9:00 AM EST): Warby Parker frames from $395, Gentle Monster from $449; 41g, 12MP camera, nine-hour battery, "Hey Gemini" hotword, local face-redaction on recordings; on sale December 10. Local-inference angle: on-device redaction as the privacy default on first-party Gemini hardware. — https://www.wired.com/story/google-samsung-intelligent-eyewear-launch-warby-parker-gentle-monster-smart-glasses/

## Agent tools

**LangChain raises $300M at a $3B valuation led by CapitalG** — *Event date: November 12 (WSJ report)* — *New item*

Alphabet's independent growth fund CapitalG led the round, more than doubling LangChain's October 2025 valuation of $1.25B, the Wall Street Journal reported Thursday; the company — best known for its open-source LLM-app framework — now sells an agent-development platform (LangGraph/LangSmith) targeting large enterprises building their own agents. LangChain had not published its own announcement at report time, and other investors and revenue figures were not in the report.

*Why it matters:* A 2.4x markup on an agent-orchestration framework, signed during an AI share selloff and an Nvidia leadership vacuum, is a strong third-party vote that enterprise agent tooling — not just models — is where budget keeps landing.

- Source: LangChain raises $300 million at a $3 billion valuation in round led by CapitalG (AI Brief, citing the Wall Street Journal) — November 12, 2026 — https://dailyaibrief.com/news/langchain-raises-300-million-at-3-billion-valuation-QZW0pMDO
- Source: Techmeme Top News (WSJ pickup) — November 12, 2026 — https://www.techmeme.com/

## Just missed the window

Licensing: Edge0 Community Model License 1.0 + Qwen CL 1.0 (commercial serving needs separate license). All performance figures are vendor claims — unverified. — https://huggingface.co/Edge0/Edge0-145B-q3k · https://www.reddit.com/r/LocalLLaMA/comments/1xuczw4/edge0edge0145bq3k_hugging_face/
- **Kazakhstan-linked Silent Lynx ran espionage with an open-source AI agent (Hermes) on DeepSeek models** — CloudSEK's report (published Wednesday Nov 11; The Record's piece today) describes an exposed server with GBs of stolen Tajik/Uzbek government data, a Telegram-controlled agent doing recon, data collection and trace-wiping, and some destructive commands pre-authorized without approval. — https://therecord.media/kazakhstan-hackers-central-asia
- **Commerce finalizing a rule to block Chinese AI firms from remotely accessing Nvidia-class chips via third-country data centers** (The Information, Wednesday evening; officials plan to announce this month) — closes the gap where export controls cover the physical chip but not remote access; Chosun Biz reports a parallel expansion of end-user license requirements to seven nations including Singapore and Malaysia. — https://www.theinformation.com/articles/commerce-department-finalizing-long-awaited-ai-remote-access-rule · https://biz.chosun.com/en/en-international/2026-11-12/M2ICNVHJPVCFLJDLAGG5IP4VDE/

## Coverage notes

- **Verified: 11 in-window items** (4 full entries + 7 also-noted), each from a fetched page with an in-window timestamp; lead stories (Nvidia, Nebius, Inception, LangChain) read in full with second sources where coverage exists. Plus 3 first-time "just missed the window" footnotes.
- **Ledger dedupe: 12 candidates suppressed** as already reported or out-of-window: Google Tabletop (re-covered by Techmeme; already footnoted Nov 11), AWS Emulator (Techmeme/HN re-spike; already footnoted Nov 11), Perplexity Teammates (thenewstack re-coverage; footnoted Nov 6), MiniMax H3-Image (aggregator re-surfacing; footnoted Nov 10), Micron $1B IPO commitment (part of reported Nov 10 Anthropic cornerstone list), Notion/Fireworks breach (Nov 10 item re-covered), Anthropic IPO microsite HN spike (covered 07:44), OpenAI Cursor/SpaceX contract shutoff (disclosed Aug 28; effective today), Anthropic Usage Policy (disclosed Oct 8; effective today), Georgia Project Camellia RICO suit (filed Nov 9), SAFA safety body (September), Apache HTTP RCE (off-scope, Nov 11 evening).
- **Dead ends / limitations:** squawknews (WSJ syndication) and BusinessWire (Nebius PR) both failed retrieval (one-strike each — covered by CNBC/Techmeme instead); fourweekmba behind a bot wall; libreagi.com resolves to a blocked/private address from this host (cited via the fetched HN thread instead); Qwen 4 full release still not out — nothing new this morning beyond the reported Qwen4-Max-preview (Nov 11). vLLM/Ollama/SGLang had no in-window releases or merges (quiet, stated as fact, not a limitation).
