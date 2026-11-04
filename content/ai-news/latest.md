# AI News Brief — November 4, 2026 · 12:00 PM ET Edition

**Window:** 2026-11-04 07:00 → 12:00 ET (since the 07:00 fire). New items only; ledger dedupe applied.

## TL;DR

- **Moonshot released Kimi K3.1 Pro open weights** — the 2.8T-total / 104B-active MoE with 1M-token context landed on Hugging Face as promised, with day-0 vLLM and SGLang recipes.
- **llama.cpp v0.9.0 shipped at 8:08 AM ET** — OCP FP8 E4M3 quantization, a more efficient unified-KV attention op, NCCL multi-node tensor parallelism, and four new architectures including Mistral Large 4.
- **OpenAI is acquiring Pierre Computer** and folding its Git-based Code Storage into Codex; Pierre's open-source Trees/Diffs/Highlights stay open.
- **TeraWulf secured a Google credit backstop** for financing behind its ~$19B Anthropic lease in Kentucky, and signed a definitive Fluor EPC contract.
- **Post-midterm AI politics bit into markets**: CoreWeave's 2031 bonds hit 79.5 cents as Texas governor-elect Hinojosa pushes a data-center moratorium; WIRED showed the UK's £400B AI claim traced to a Google-commissioned report.

---

## Models and local inference

**Moonshot AI releases Kimi K3.1 Pro open weights — 2.8T MoE, 1M context, MXFP4 QAT** — *Event date: November 4, 2026 (weights drop; model itself announced Oct 23)*
The Kimi K3.1 Pro checkpoint is now on Hugging Face under the Kimi K3.1 Pro License, fulfilling the Nov 4 date Moonshot promised at the Oct 23 API launch. Per the model card: 2.8T total / 104B activated parameters, 896 experts (16 selected + 2 shared per token), 93 layers with a 69 KDA + 24 Gated DSA attention composition (Gated DeepSeek Sparse Attention replacing K3's Gated MLA), 1,048,576-token context, MoonViT-V2 vision encoder, and MXFP4 weights / MXFP8 activations via quantization-aware training. Moonshot's tables put it at 65.7 on Terminal-Bench 4.0 (vs 58.2 GPT-6 Astra, 66.4 Opus 5.5), 92.1 BrowseComp, and 1846 GDPval-AA Elo — top-tier for an open-weight model. Moonshot recommends vLLM, SGLang and TokenSpeed; vLLM merged Kimi K3.1 Pro recipes this morning (#65684, plus #65688 adding a pinned-Docker, NVIDIA Rubin VR200 reference).
*Why it matters:* This is the strongest open-weight agentic coder available for self-hosting, and the QAT-native MXFP4 path plus DSA attention means serving economics land far below dense 3T-class peers.
Community partitioning already started within the hour (e.g. expert-slice repos appearing on HF); single-GPU quants are weeks away, not hours.
Sources:
- Model card — Hugging Face, Nov 4, 2026 — https://huggingface.co/moonshotai/Kimi-K3.1-Pro
- "Kimi K3.1 Pro model weights are out" — Hacker News front page, posted 10:30 AM ET — https://news.ycombinator.com/item?id=50366999
- vLLM merged PRs #65684 / #65688 — GitHub, merged 12:00–17:00 UTC Nov 4 — https://github.com/vllm-project/vllm/pull/65688
- (Update on ledger entry `missed:kimi-k3.1-pro`, Oct 23)

**llama.cpp v0.9.0: OCP FP8 E4M3 quantization, unified-KV attention rows, NCCL multi-node TP** — *Event date: November 4, 2026, 08:08 AM ET release*
The first 0.9-series release lands OCP-standard FP8 E4M3 as a first-class tensor type (`LLAMA_FTYPE_MOSTLY_F8_E4M3`, #27512), a `flash_attn_ext_rows` op that makes the unified KV cache efficient for multi-sequence inference and restores non-unified-KV prefill performance (#29510), and a generic batched tensor-copy API for Metal/CUDA (#30881). Four new architectures (Clover, Limite, Metis-1.6, Mistral Large 4), Chatterbox TTS + Nemotron 3.5 Transcribe + predefined Pocket TTS voices, major Gemma 4 E2B/E4B prefill optimizations, and ggml v0.29.0 with NCCL-based multi-node tensor parallelism round out the release; builds b12109–b12110 shipped the version bump in-window.
*Why it matters:* FP8 E4M3 is now an ecosystem standard checkpoint rather than a vendor extension — quant tooling, GGUF libraries and server behavior around it will converge fast, and multi-node TP in ggml changes what heterogeneous home clusters can run.
Sources:
- GitHub release v0.9.0 — ggml-org/llama.cpp, published 2026-11-04T13:08:06Z (8:08 AM ET) — https://github.com/ggml-org/llama.cpp/releases/tag/v0.9.0
- Build b12110 (version bump) — GitHub, 2026-11-04T12:52:04Z — https://github.com/ggml-org/llama.cpp/releases/tag/b12110

---

## Agent tools

**OpenAI acquires Pierre Computer; Code Storage folds into Codex** — *Event date: November 4, 2026 (Axios scoop 7:10 AM ET; OpenAI post same morning)*
OpenAI announced it will acquire Pierre Computer Company, the team behind Git-based Code Storage and the Trees and Diffs libraries already powering file browsing and review in the Codex app. OpenAI's post says it will "set the standard for modern software development" alongside Pando, its in-house Git backend disclosed Nov 2, with Code Storage customers supported through closing; Pierre's open-source projects (Diffs, Trees, DiffsHub, Highlights) remain open-source. Code Storage is already in production at Lovable, Bolt, Amp and others; Axios frames the move as a challenge to GitHub at the source-control layer beneath coding agents. Deal is subject to regulatory approval, terms undisclosed.
*Why it matters:* Source control is becoming an agent substrate — whoever owns the diff/tree/review layer between agents and repositories owns a chokepoint. This is OpenAI stacking infrastructure beneath Codex rather than just model features.
Sources:
- "OpenAI to acquire Pierre Computer Company, Inc." — OpenAI, November 4, 2026 — https://openai.com/index/openai-to-acquire-pierre/
- Techmeme river, 7:10 AM ET (Axios: Madison Mills) — https://www.techmeme.com/river
- "Pierre Computer is being acquired by OpenAI" — Hacker News, posted 10:21 AM ET — https://news.ycombinator.com/item?id=50366863

---

## AI security and governance

**Google backstops TeraWulf's ~$19B Anthropic lease; Fluor EPC locked in** — *Event date: November 4, 2026, 8:15 AM ET filing/PR*
TeraWulf announced credit support agreements with Google LLC for the debt financing behind ~$19B of contracted 20-year Anthropic leases at its Justified Data Campus in Hawesville, Kentucky — three buildings, ~401 MW critical IT load per the lease terms, with Alphabet's AA+/Aa2 balance sheet now underwriting the project's debt. It also executed a definitive GMP-based EPC agreement with Fluor for three data-center buildings, with phased ready-for-service from late 2027 through Q2 2028.
*Why it matters:* Hyperscaler-grade credit support for a neocloud landlord is the new shape of AI capacity financing — and it lands the morning after bond markets shut out riskier neoclouds. Counterparty-quality engineering is becoming the bottleneck currency, alongside power.
Sources:
- Press release — TeraWulf IR, November 4, 2026 8:15 AM EST — https://investors.terawulf.com/news-events/press-releases/detail/148/terawulf-secures-google-credit-backstop-for-project-financing-in-support-of-19-billion-anthropic-lease-executes-definitive-fluor-epc-agreement
- "TeraWulf gets credit backstop from Google for Anthropic data center project" — Reuters, Nov 4, 2026 — https://www.reuters.com/technology/terawulf-gets-credit-backstop-google-anthropic-data-center-project-2026-11-04/

**Sentra raises $150M led by Capital One Ventures to govern what AI agents can reach** — *Event date: November 4, 2026*
The agentic-data-security startup announced a $150M strategic round (Series C per SiliconANGLE) led by Capital One Ventures, which takes a board seat, joining Bessemer, Zeev, Standard, Munich Re Ventures and Key1. Sentra's pitch: secure data at rest before agents touch it — classify, enforce least privilege for human and non-human identities, detect anomalous agent access, and reconstruct what an agent "could have reached." The company cites >300% YoY growth and names Lyft as a reference customer.
*Why it matters:* Every deployed agent is a new non-human identity with inherited, often ungoverned entitlements — the security budget is following the attack surface from endpoint to data layer, and a bank's venture arm taking a board seat says regulated enterprises are buying this now.
Sources:
- "Sentra Raises $150 Million for Agentic Data Security" — Sentra (PR Newswire), Wednesday, Nov 4, 2026 — https://sentra.io/news/sentra-raises-150-million-agentic-data-security
- Techmeme river, 8:15 AM ET (SiliconANGLE: Duncan Riley) — https://www.techmeme.com/river

**CoreWeave bonds hit new lows as data-center politics hit the junk market** — *Event date: November 4, 2026 (Bloomberg, 11:10 AM ET)*
CoreWeave's unsecured 2031 notes fell to 79.5 cents on the dollar — near the junk market's worst of the day — and 2030/2032 bonds yielded north of 15%, while shares slid 7.2% to $49.85 by 10:24 AM ET. The trigger: Democrat Gina Hinojosa unseating Greg Abbott in Texas on an immediate data-center moratorium pledge, alongside governors-elect Amy Acton (Ohio) and Keisha Lance Bottoms (Georgia) on similar platforms. The Washington Post reports voters in 29+ jurisdictions passed data-center bans or restrictions (out of ~41 measures), and S&P Global counted 145+ US projects delayed or canceled through early August.
*Why it matters:* This is the first clear repricing of AI-infrastructure credit on democratic backlash — regulation risk used to be a permitting story; now it shows up in debt spreads that fund the buildout. (Republicans still hold the Texas legislature, which may blunt Hinojosa's reach.)
Sources:
- "CoreWeave Bonds Hit New Lows as Texas Data Centers in Crosshairs" — Bloomberg, Nov 4, 2026 — https://www.bloomberg.com/news/articles/2026-11-04/coreweave-bonds-hit-new-lows-as-texas-data-centers-in-crosshairs
- Quartz summary of the Bloomberg report, updated Nov 4, 2026 — https://qz.com/coreweave-bonds-new-lows-texas-governor-data-center-110426
- Context — Washington Post, Nov 4, 2026 (7:50 AM ET via Techmeme) — https://www.washingtonpost.com/technology/2026/11/04/voters-pass-over-20-ballot-measures-ban-or-restrict-data-centers/

**WIRED: the UK's £400B AI windfall claim mainly came from Google** — *Event date: November 4, 2026, 8:49 AM ET publication*
AI minister Kanishka Narayan told Parliament that AI adoption and data investment could add £55B–£140B to the UK economy by 2030 (central estimate ~£120B) — under a third of the £400B figure officials cited for two years — and disclosed the £400B came solely from a 2023 report commissioned by Google and written by consultancy Public First (now PF Global). WIRED found no earlier recorded instance of the government placing the two numbers side by side or naming Google as the funder; ministers held the lower estimate since at least November 2025 while Skills England kept publishing the old figure.
*Why it matters:* A G7 government's flagship AI economic rationale was vendor-funded research — expect independent-evaluation demands (the Ada Lovelace Institute made exactly that call) to spread through procurement and regulatory impact assessments.
Source:
- "UK Officials Said AI Would Add £400 Billion to the Economy. That Figure Mainly Came From Google" — WIRED (Ben Gartside), Nov 4, 2026 8:49 AM — https://www.wired.com/story/uk-officials-said-ai-would-add-400-billion-to-the-economy-that-figure-mainly-came-from-google/

---

## Also noted

- **MCP OAuth hardening pattern**: Stream Vessel published a per-user trusted-redirect-URI design for MCP servers, citing a May 2026 study finding 95.8% of 119 tested MCP servers accept arbitrary redirect URIs from anonymous registrants (one-click token theft); ships a checklist and a shipped mitigation — https://streamvessel.com/blog/mcp-trusted-redirect-uris (Nov 4, HN 15:30 UTC)

## Just missed the window

- **Tell HN: Gemini trains on personal-subscriber chat history** — the reminder post (419 pts; "Keep Activity" on = chats used for model training, human-reviewed subsets retained up to 3 years, opt-out = 72-hour session loss) hit the HN front page at 05:10 AM ET, ~2h pre-window; underlying policy is longstanding, user-reported and unverified — https://news.ycombinator.com/item?id=50363461 (footnoted once)
- **SemiAnalysis: SpaceX revises its 2027 data-center capacity target up to ~13GW** — roughly all of Google's 1999–2026 capacity in 1.5 years; published 6:25 AM ET, 35 minutes pre-window — https://techmeme.com/river (footnoted once)

## Coverage notes

- **Verified items: 10** (5 leading + 2 in-section governance/market + 3 "also noted"), each fetched and page-timestamped inside 07:00–12:00 ET; lead stories read in full.
- **Dead ends / limitations**: Bloomberg and WaPo are paywalled — market item corroborated via Quartz's summary and Reuters via TeraWulf's own IR release; VentureBeat's "Comment and Control" prompt-injection audit turned out to be April 21 — dropped (two-strike rule applied); no new closed-weight model launches surfaced 07:00–12:00 ET (thin morning for launches); HF new-model API stream was noise (test repos, mirrors).
