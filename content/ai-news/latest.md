# AI News Briefing — October 31, 2026 (07:00 ET edition)

**Window: Friday, October 30, 17:00 ET → Saturday, October 31, 07:00 ET.** New items only; events already carried by previous runs are deduped against the reported-ledger.

## TL;DR

- **OpenAI's mega-round is stalling.** The company secured roughly $11B of its $30B target at a $1.25T valuation, then cut the ask to about $20B as the tech rout deepened — the first close is expected below $20B.
- **Microsoft Security Research published the full forensic analysis of the tampered Claude Code extension** that rode Cursor's marketplace for ~11 hours (page dated Oct 31): an Ed25519 `svc-telemetry` SSH key, a reverse tunnel, and confirmation that Anthropic's systems were never compromised.
- **S&P Global cut Meta's outlook to negative**, forecasting free-operating-cash-flow deficits of $39B in 2026 and $71B in 2027 on AI capex.
- **WSJ: CoreWeave, Nebius and Nscale are effectively shut out of the bond market**, a new financing bottleneck for the GPU-leasing model.
- **Friday's AI selloff**: Nasdaq -2.86% (worst day since June 5), Intel -28.58% — its worst day since 1974.

## AI security and governance

**Microsoft dissects the Cursor-marketplace Claude Code backdoor — "all evidence suggests the incident is limited to Cursor marketplace"** — event date: analysis published October 31 (page date); underlying intrusion October 29. *Update on previously reported (ledger 2026-10-30 | doublepulsar.com | claude-code-2.1.313-rat).*

Microsoft Security Research published a deep-dive on the trojanized `anthropic.claude-code` builds (2.1.312/2.1.313) served in Cursor's VS Code extension marketplace from about 02:27–13:35 PT on Thursday October 29, affecting up to ~5,000 users. The injected loader runs before normal extension init: it appends an attacker Ed25519 public key (comment `svc-telemetry`) to `authorized_keys`, decodes a second private key to a hidden `.stk_<hex>` temp file, and launches `ssh -N -R '*:0:localhost:22'` for reverse tunneling — via autossh/ssh on Unix and a hidden PowerShell retry loop on Windows — while collecting hostname, username, OS, architecture, home/working directory, global Git email, and up to 40 environment-variable *names* matching KEY/TOKEN/SECRET/PASS/AWS/GCP (names only, not values), then writing platform persistence and exfiltrating the recon bundle on a delayed callback. Crucially, the tunnel step needs admin rights on macOS/Windows and failed on most hosts — but the key-drop and persistence still land, so reinstalling the extension is not remediation; Microsoft says VS Marketplace and npm were never affected and "Anthropic confirmed to Microsoft that their systems were not compromised and they did not develop the malicious package." Microsoft also shipped KQL hunting queries and IOCs; Cursor says it blocked the package on detection, fully shut down the VSCode marketplace out of caution, rotated its keys, and worked with a cloud host to sinkhole the attacker's server.

*Why it matters:* this is now the best-documented supply-chain incident of the agent-tooling boom — a trusted dev-tool channel (Cursor's marketplace) shipped a signed-looking build whose payload targets SSH access to developer laptops. For anyone running Claude Code on Windows boxes, the remediation checklist (authorized_keys audit for `svc-telemetry`, `.stk_*` file hunt, persistence review) is the actionable takeaway, not just "update the extension."

- Source: Microsoft Security Blog (Microsoft Security Research), "Inside a malicious Claude Code-branded extension and its persistent SSH backdoor," page dated 2026-10-31 — https://www.microsoft.com/en-us/security/blog/2026-10-30/inside-malicious-claude-code-branded-cursor-extension-persistent-ssh-backdoor/
- Second source: The Register (Connor Jones), "Anthropic: That Claude Code extension compromise… couldn't be us," Fri 30 Oct 2026 16:27 UTC — https://www.theregister.com/cyber-crime/2026-10-30/anthropic-that-claude-code-extension-compromise-couldnt-be-us/5306682
- Cursor advisory: "Security notice — malicious version of Anthropic.claude-code (2.1.312/2.1.313)" — https://cursor.com/blog/claude-code-extension-security-notice

## Market and business

**OpenAI's $30B round collapses to ~$11B in commitments; ask cut to ~$20B at $1.25T** — event date: Friday evening, October 30 (WSJ) / early Saturday (FT via Reuters). *Update on previously reported (ledger 2026-10-22 | cnbc.com | missed:openai-30b-125t).*

The Wall Street Journal reported late Friday that OpenAI has secured only about $11B — less than half — of its targeted $30B, at a $1.25T valuation versus the $1.4T it had sought, and expects to add more in coming weeks; The Decoder framed the shortfall as fading appetite from big investors amid the Google/Amazon/Intel earnings-driven rout. FT (picked up by Reuters overnight) added the follow-through: OpenAI slashed the target by $10B to ~$20B, set a Friday commit deadline whose "first close" is expected under $20B after some investors trimmed allocations, with the MGX-led UAE consortium as largest participant (including an option to lease newly built regional compute), Founders Fund, Coatue, Sequoia and a16z expected in, and a later retail offering at the same $1.25T mark. OpenAI is chasing a 2027 listing while rival Anthropic eyes a November IPO.

*Why it matters:* this is the clearest market verdict yet on the AI financing supercycle — the most-watched round in private markets coming in under-bid against a backdrop of a -7.8% Nasdaq drawdown from the October 6 high. It also reshapes the local-inference story's competitive frame: a capital-constrained OpenAI leans harder on price cuts and efficiency, which accelerates open-weight pressure at the edge.

- Source: WSJ, "OpenAI Raises Less Than Half of $30 Billion Target in Latest Round," Oct 30–31, 2026 — https://www.wsj.com/tech/ai/openai-raises-less-than-half-of-30-billion-target-in-latest-round-91aecdc5 (paywalled)
- Second source: The Decoder (Matthias Bastian), "OpenAI raises just $11 billion of its $30 billion target as big investors lose their AI appetite," Oct 31, 2026 — https://the-decoder.com/openai-raises-just-11-billion-of-its-30-billion-target-as-big-investors-lose-their-ai-appetite/
- Third source: Reuters, "OpenAI slashes funding target by $10 billion amid tech rout, FT reports," Oct 31, 2026 — https://www.reuters.com/business/openai-cuts-funding-target-by-10-billion-ft-reports-2026-10-31/

**S&P Global revises Meta's outlook to negative: "$39B in 2026 and $71B in 2027" FOCF deficits** — event date: Friday, October 30, 17:14 ET (Reuters wire).

S&P Global Ratings moved Meta's outlook to negative from stable, citing "significant uncertainty" around capex and free operating cash flow: it now forecasts FOCF deficits of $39B (2026) and $71B (2027), expects deficits to persist beyond 2028 with the 2028 shortfall funded entirely by debt, and sees leverage rising above 1.0x as early as 2027 — S&P's threshold for the AA- rating. Context: Meta guided capex to $135–145B, posted a $2.12B Q3 free-cash-flow deficit, and is planning a ~$97.5B stock sale for the AI buildout. S&P's Naveen Sarma warned credit profiles "will continue to weaken" if capex keeps outrunning expectations; Meta said it "respectfully disagree[s]" with S&P's monetization doubts.

*Why it matters:* rating-agency language is the leading indicator for whether AI capex stays financeable at investment grade — a Meta downgrade path would ripple through GPU-neocloud debt and, eventually, consumer-hardware pricing.

- Source: Reuters via Investing.com, "S&P Global revises Meta's outlook to 'negative' on AI spending, cash flow worries," published Oct 30, 2026 05:14 PM — https://www.investing.com/news/stock-market-news/sp-global-revises-outlook-on-meta-to-negative-on-cashflow-concerns-4988756
- Second source: The Star (Reuters wire), same story, Oct 31, 2026 — https://www.thestar.com.my/tech/tech-news/2026/10/31/sp-global-revises-outlook-on-meta-to-negative-on-cashflow-concerns

**WSJ: neoclouds CoreWeave, Nebius and Nscale effectively shut out of the bond market** — event date: Friday evening, October 30 (Techmeme item surfaced ~23:00 ET).

The Wall Street Journal reports the three GPU-cloud leaders have been effectively locked out of the bond market for weeks as lenders and investors demand tougher covenants and stricter terms — turning credit availability into "a new bottleneck for the US AI buildout." It lands alongside CoreWeave's blown-out CDS spreads and Musk's "200+ neoclouds face guppy-level death" warning from last weekend.

*Why it matters:* neocloud credit is the financing layer under most frontier training and inference capacity; a closed bond market for the category's names is the most concrete stress signal yet for the GPU-leasing model.

- Source: Techmeme top item (WSJ-sourced; Wired's Steven Levy also cited), Oct 31 — https://www.techmeme.com/261031/p4
- Second source: AI Weekly alert, "WSJ: CoreWeave, Nebius and Nscale Effectively Shut Out of Bond Market as Lenders…" — https://aiweekly.co/alerts/wsj-coreweave-nebius-and-nscale-effectively-shut-out-of-bond-market-as-lenders

**Also noted (market):**

- **Intel closes -28.58% at $65.76 — worst day since 1974; ~$140B market cap and ~$12B of the US government's stake erased**, despite data-center revenue +78% y/y; guidance weakness from the Kiryat Gat blast (reported Oct 29) plus PC softness drove the rout (Yahoo Finance, "Intel stock plunges nearly 29%, its worst day since 1974," Oct 30 evening — https://finance.yahoo.com/markets/article/intel-stock-plunges-nearly-29-its-worst-day-since-1974-205955309.html ; CNBC, "Amazon, Intel shares fall after earnings amid AI spending concerns" — https://www.cnbc.com/2026/10/30/amazon-intel-earnings-big-tech-ai-spending-concerns.html).
- **Friday AI selloff wrap:** Nasdaq -2.86% to 25,437 (largest one-day drop since June 5, now -7.8% from the Oct 6 high), S&P 500 -1.82%, Dow -1.09%; SOX index -7.2% (over -15% for the month), AMD -10.9%, Nvidia -4.2%, Dell -10.8%, HP -9.6% (Reuters, "Wall Street closes sharply lower as AI cash burn fears spark tech selloff," Oct 30 — https://www.reuters.com/business/nasdaq-futures-tumble-amazon-intel-results-deepen-ai-spending-woes-2026-10-30/).
- **Baseten raising ~$2B in two tranches at $26B then $30B** — 2.3x its June valuation in four months, Sands Capital expected to lead; inference-layer pricing keeps re-rating (Bloomberg, Oct 30 evening — https://www.bloomberg.com/news/articles/2026-10-30/baseten-set-for-30-billion-valuation-with-new-funding ; AI Weekly, Oct 31 00:26 UTC — https://aiweekly.co/alerts/baseten-nears-2b-round-at-up-to-30b-23x-june-mark).

## Models and local inference

No new model releases were verified inside this 14-hour overnight window (Saturday-morning quiet period). Engine churn continued:

- **vLLM — 37 in-window merges**: day-0 NVIDIA **Canary (nvidia/canary-1b-v2) speech-to-text** support, **Step-5-Preview on ROCm**, MORI EP V2, NIXL bump to 1.5.2, RL "direct weight reload" (write checkpoint tensors into the live model), Qwen3.8 **SM103 skinny-GEMM plans for TP=1**, DSv4.1 PCP with PP/DP, MoE-LoRA shrink/expand kernels for 64-token batches (GitHub — https://github.com/vllm-project/vllm ; merged-PR query 2026-10-30T21:00Z..2026-10-31T11:00Z).
- **llama.cpp — three builds in-window**: b12018 (SYCL f32→q2_0/q4_0/q5_0 CPY rounding fix #31120), b12019 (strip think-block in qwen3-coder responses under `reasoning_format: none`, #31380), b12020 (OpenCL FWHT #31434) (GitHub — https://github.com/ggml-org/llama.cpp/releases).

## Agent tools

Nothing new and material verified in-window beyond the vLLM/llama.cpp items above (a quiet overnight for agent frameworks; the Oct 30 runs already covered the OpenClaw, Copilot Fusion and OpenAI Model Spec waves).

## Just missed the window

- **Publishers' AI-scraping opt-out "policy token"** — OpenAI, Google, Microsoft and Anthropic committed to honor an IETF draft robots.txt usage signal (user-agent `__usage_ai-training`) authored by Anthropic standards lead Kevin Kelley; Anthropic already honors it, OpenAI in "coming months," Google/early-2027 (and Microsoft Feb 1, 2027), Meta's Andy Stone voiced support. The Verge published Fri 20:26 UTC — 34 minutes before the window opened; Engadget's Oct 31 6:34 AM ET piece was re-coverage (The Verge — https://www.theverge.com/tech/1023763/openai-google-microsoft-ai-scraping-standard-opt-out ; Engadget — https://www.engadget.com/2303225/google-openai-anthropic-ai-crawling-controls/ ; OpenAI blog "Making it easier for websites to express AI training preferences," Oct 30).
- **Linode/Akamai discloses CVE-2026-98432 RCE** — Customer Security Advisory posted Fri 18:10 UTC (14:10 ET); in-window updates at 21:18 and 01:55 UTC said subject-matter experts were engaged, mitigations for the "underlying known attack vector" were deploying, and there is no evidence of exploitation on the Akamai Compute platform. Relevant to agent hosts parked on Linode (Linode Status, "Customer Security Advisory: CVE-2026-98432" — https://status.linode.com/incidents/nt58zhx3pmgm).
- **Sam Altman on Cheeky Pint: DRAM in home PCs "quite terrible"** — the OpenAI CEO told John Collison that scarce DRAM "sitting in their own computer and being used 5% of the time" belongs in data centers; the episode's publish time could not be pinned (Tom's Hardware ran it "30 October 2026," The Verge "Oct 30"), so the event date stays outside the verifiable window. The weekend backlash ("RAMpage," per NY Post) did land in-window (Tom's Hardware — https://www.tomshardware.com/tech-industry/data-centers/openai-ceo-says-its-quite-terrible-for-your-pc-to-have-ram-sam-altman-says-data-centers-offer-better-utilization ; NY Post, Oct 30 — https://nypost.com/2026/10/30/tech/sam-altman-roasted-over-comments-suggesting-ordinary-people-are-wasting-limited-computer-memory/ ; episode: Cheeky Pint "Why Sam Altman is against a single superintelligence" — https://www.youtube.com/watch?v=iMsUcCYNKqY).
- **OpenAI's "Fair use benefits everyone"** — OpenAI published excerpts of pro-fair-use amicus briefs in the NYT litigation (Authors Alliance, Engine/Developers Alliance, Matthew Sag, Kevin Kelly, ACB/ALA/ARL — and a US Government brief arguing NYT's theory "would severely hamper the Progress of Science"). Page is dated Oct 30 with no verified time; it hit the HN front page in-window at 23:33 UTC (story 50311537) (OpenAI — https://openai.com/index/fair-use-benefits-everyone/).

## Coverage notes

- **Verified and counted: 9 qualifying events** (4 leads + 5 also-noted items), each backed by a fully-read, in-window-dated page; lead stories read in full. Four additional events carried as "Just missed the window" footnotes.
- **Ledger dedupe: 7 candidates suppressed as already reported** — Anthropic high-junk credit rating (re-covered in-window by TechCrunch; `missed:blackstone-anthropic-junk-rating` from the Oct 30 17:00 run), Salesforce–Rune $375M (`missed:salesforce-rune-375m`, full Techmeme coverage appeared this window), FEC AI-PAC ~$80M spend (`missed:ai-pacs-80m-retreat`), OpenAI teen weapons-info limits (`community-safety-report-teens`), Anthropic Max/Ultra rebrand (`claude-max-ultra-rebrand`), Google Suncatcher online (`suncatcher-online`), and Reddit logged-in-only market reaction (`reddit-logged-in-only`). Intel's Friday crash was carried as an *update* to the reported Oct 29 Intel Q3 entry rather than suppressed.
- **Angles and indexes:** HN Algolia (in-window story list), Techmeme (Sat 06:00 archive + item page), GitHub releases/merged-PR queries for llama.cpp, vLLM, Ollama, plus ~10 multi-angle web searches across releases, hardware, agents, and governance.
- **Dead ends / limitations:** OpenSSF's "25% of PRs are AI slop" LF survey (posted Oct 30, no verifiable time) excluded as unverifiable-timing; Ofcom High Court dismissal of Meta/TikTok/X challenge sat in Techmeme "Earlier Picks" (Thursday) and was not chased per two-strike; WSJ and Bloomberg primaries are paywalled — cited via fetched secondary coverage; Cheeky Pint episode timestamp unresolvable (YouTube page exposes no publish date). Ollama latest GA remains v0.40.13 (Oct 28) — outside window, no item.
