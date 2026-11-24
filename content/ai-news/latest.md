# AI News Briefing — Tuesday, November 24, 2026, 12:00 ET edition

**Window:** 2026-11-24 07:00 → 12:00 EST (this job's 12:00 run; new items only, deduped against the reported ledger).

**TL;DR**
- The OpenClaw Foundation launched **ShellBench** this morning: agents on 100 messy real-world office tasks top out at only ~62% completion — and 9.9% of fully-correct runs still failed a safety check.
- **Anthropic broke below its $83 IPO price** (-12.6% to ~$76.6), while a Monday-night filing shows Dario and Daniela Amodei each sold 3.37M+ shares worth $280M+ to underwriters the day after the debut.
- **Tidal becomes the first major streamer to ban wholly AI-generated music outright** — removing ~29 million tracks starting December 9.
- Inference engines kept shipping: **llama.cpp b12676 plus ~20 in-window merges** (CUDA fused-norm kernels, MCP multimodal tool results), and **vLLM took 19 merges including two security fixes**.
- No new closed- or open-weight model releases were disclosed in the window; the morning belonged to benchmarks, markets and policy.

---

## Agent tools

**ShellBench: frontier agents complete only six in ten real-world office tasks** — event date Nov 24, 2026 (announced 07:30 EST)
The OpenClaw Foundation debuted ShellBench, a benchmark of 100 everyday office tasks run as 53,100 graded attempts, built with Parsewave, Hugging Face, Red Hat and UC Berkeley (Paradigm, Columbia and NVIDIA listed as collaborators) and drawn from 10M+ anonymized real-world agent interactions. Each task pairs a median-43-word request with a cluttered workspace (median 188 files: emails, calendars, spreadsheets, receipts, PDFs, chat logs) and often hides a trap — a stale record, two look-alike accounts, or a phishing-like embedded instruction. Grading is all-or-nothing against 721 rubric criteria, 381 of them safety checks. GPT-6 Astra led at 62.5% completion, with Claude Fable 5.2 at 61.9% and GPT-6.1 Sol at 61.4%; DeepSeek V4.1 Flash and Claude Sonnet 5.5 landed near four in ten. Twelve models ran across five harnesses: Claude Code, Codex, Hermes, OpenClaw and Pi — and the four open-source harnesses edged the proprietary Claude Code harness. Failure anatomy: 51% of misses were information omission even though 87% of those runs had collected the right data; GPT-6 Astra passed 75.1% of tasks at least once across three attempts but only 48.8% on all three; 15.7% of all runs failed a safety check, and among runs that met every correctness criterion, 9.9% still failed a safety requirement.
- **Why it matters:** This separates capability from reliability in a way coding leaderboards don't. The repeat-attempt gap (~75% → ~49%) means the same agent on the same job needs human checking more than one time in three, and the safety-in-correct-answers finding (9.9%) says a passing output is not a safe output. The price-performance spread — Astra at $1.01/run for 62.5% vs Sol at $0.17/run for 61.4% — is the number to quote in any agent TCO conversation. For this newsletter's own stack, Hermes placing in the open-source-harness tier that outscored Claude Code is a directly actionable signal.
- Source: "Exclusive: OpenClaw offers an agent reality check," The Deep View (Nat Rubio-Licht), Nov 24, 2026 07:30 EST — https://www.thedeepview.com/articles/exclusive-openclaw-offers-an-agent-reality-check
- Source: "Six In Ten: ShellBench Measures Agents On The Messy Work Nobody Benchmarks," llms.blog, Nov 24, 2026 — https://www.llms.blog/posts/six-in-ten-shellbench-measures-agents-on-the-messy-work-nobody-benchmarks
- Source: benchmark repo — https://github.com/openclaw/shellbench

## Models and local inference

**A model-release-quiet morning** — event date Nov 24, 2026
No new closed- or open-weight model was disclosed between 07:00 and 12:00 EST. The llm-releases tracker's newest dated entries remain yesterday's (GLM-5.4, Illuin IVE-4B, Aleph Alpha Kolibri 1.1, inclusionAI Ling-3.2-air), Hugging Face's newest-created feed shows only community fine-tunes, and the vendor newsrooms checked posted nothing new before the cutoff. Worth watching: GLM-5.4's promised public weights/API (pending safety review per yesterday's announcement) and the Qwen4 open-weight follow-up signal in llama.cpp's qwen4exp kernels from last week.

**llama.cpp ships b12676 and ~20 in-window merges; vLLM takes 19 including two security fixes** — event date Nov 24, 2026
llama.cpp tagged b12676 (08:24 EST) on the back of roughly 20 in-window merges: the server now includes MCP image and audio data in tool results (#32064), CUDA fuses RMS_NORM+MUL+MROPE/IMROPE with tuned block size (#32500), ARMv8.0 gets repack kernels for targets without dotprod (#32142), SVE gains q5_0/q5_1 dot-product kernels (#30867), Inkling 2 Mini model math was corrected (#32855, #32940), and prompt-cache restore now retries after clearing idle slots (#30997). vLLM merged 19 PRs including two explicitly security-tagged fixes — containing Sentence Transformers module files (#61430) and bounding media preprocessing per prompt (#69098) — plus MLA-from-latent-shape detection for Youtu-LLM (#69689), vectorized per-token-head KV tile loads in Triton attention (#69170), smaller RDNA prefill tiles (#69321), and Mistral v11+ tool-call parsing in the Rust frontend (#69601). Ollama and SGLang had no in-window releases (Ollama v0.40.18 already covered this morning).
- Source: llama.cpp releases — https://github.com/ggml-org/llama.cpp/releases (b12676 published Nov 24, 2026 13:24 UTC)
- Source: llama.cpp commits since 11:00 UTC Nov 24 — https://github.com/ggml-org/llama.cpp/commits/main
- Source: vLLM merged PRs (in-window timestamps 12:29–16:31 UTC) — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged

**Songe: an Apache-2.0 engine that trains your local model from your own agent sessions, on idle compute** — event date Nov 24, 2026 (HN front page, 10:03 EST)
Songe sits as a proxy in front of LM Studio/vLLM/Ollama/llama.cpp, records what happens after each model action (tool results, errors, your corrections), and while the agent is idle trains a small adapter on those lessons — "Quick Dream" minutes-long preference injection or "Deep Dream" full adapter training on MLX (Apple Silicon) or PyTorch (NVIDIA/AMD). New versions are adopted only if they score better on your own recorded conversations, with one-click rollback; nothing leaves the machine.
- **Why it matters (local inference):** it operationalizes the "personal model" loop this community has discussed for months — continuous post-training on your own traffic with guard-railed promotion — and its Apple Silicon + NVIDIA support covers the 16GB-class machines where LoRA-adapter training is practical. Self-published and unbenchedmarked; treat claims accordingly.
- Source: Show HN — https://news.ycombinator.com/item?id=50629231 (Nov 24, 2026 10:03 EST)
- Source: repo — https://github.com/sous-labs/songe

**Weight-space programming returns on HF Daily Papers** — event date Nov 24, 2026
Today's HF Daily Papers carry "WeightCode: Installing New Capabilities into Neural Networks via Weight-Space Programming" and CMU's "Self-WSP: Can Language Models Improve Themselves Through Weight-Space Programming?" — a second wave on the weight-editing thread that made the front page last week (ledger Nov 20). Early-signal only; no independent replication.

## AI security and governance

**Anthropic slips below its $83 IPO price as insider-sale filings surface** — event date Nov 24, 2026 (premarket break; Amodei filing disclosed Monday late night)
Shares fell below the $83 offering price in early trading, last down 12.6% at ~$76.6 — erasing close to $200B of market value from the $111.50 debut and leaving ~$1.5T, about 40× trailing sales. A filing late Monday showed CEO Dario Amodei and President Daniela Amodei each sold 3.37M+ shares (worth $280M+ each) to the IPO underwriters the day after the debut, under lock-up provisions that allow insiders to sell portions from the first trading day, with staggered releases tied to earnings. The underwriters also exercised their greenshoe, lifting gross proceeds to a record $93.5B while removing a potential support bid. Reuters framed the key risks as insider selling, open-weight competition (DeepSeek, Mistral, Moonshot — pressure Claude's pricing has already drawn down), and any data-center buildout slowdown; MSCI adds Anthropic to its global indexes at the Nov 30 close, and Intropic estimates passive funds could absorb up to 30% of offered shares by year-end.
- **Why it matters:** As the first public frontier lab, Anthropic is the sector's repricing proxy — and today it became the first to trade under its issue price, five days after debut (SpaceX took ~a month; Facebook, two days). The Amodei sales reframed as a governance story: a public-benefit company whose founders monetized immediately under a day-one lock-up carve-out, while its own S-1 risk language about open weights is being vindicated by Jensen Huang's reinstatement as an open-weight advocate.
- Source: "Anthropic falls below $83 IPO price in swift reversal after record listing," Reuters (Medha Singh), Nov 24, 2026 10:33 EST — syndicated copy: https://www.aol.com/articles/anthropic-breaks-below-83-ipo-111703000.html
- Source: "Anthropic Breaks Below $83 IPO Price in Premarket Trading," Bloomberg, Nov 24, 2026 — https://www.bloomberg.com/news/articles/2026-11-24/anthropic-breaks-below-83-ipo-price-in-premarket-trading

**Tidal to remove all wholly AI-generated music from December 9** — event date Nov 24, 2026 (announced 10:06 EST)
Block-owned Tidal said it will ban tracks it believes are completely AI-generated, becoming the first music streaming platform to ban AI music entirely. Tidal Lead Robert Anderson said 43% of tracks distributed to the service in the last 90 days were detected as AI-generated, 96% of those got zero plays, and removal will strip roughly 29 million tracks while rejecting 80,000–85,000 songs per day going forward. It escalates Tidal's May policy (demonetization + labeling) and lands while Spotify (75M spam/slop tracks removed, 50M more muted in recommendations this year) and Apple Music (AI labeling) still stop short of bans.
- **Why it matters:** First full-deliberate platform ban creates a detection-and-enforcement template other services can point to — and the 43%-of-inflow number is the first hard, platform-reported quantification of AI slop's share of new music supply.
- Source: "Tidal Bans AI-Generated Music," Variety, Nov 24, 2026 7:06am PT — https://variety.com/2026/music/news/tidal-bans-ai-generated-music-1236956924/

---

## Just missed the window
- Financial Times (Techmeme river, Nov 24 06:55 EST — five minutes before open): how frontier labs and Big Tech are fighting state-sponsored espionage — recruiting CIA talent and limiting staff movements. First in-window disclosure would have been this morning; footnoted once.

## Coverage notes
- Verified and reported: **6 qualifying items** (2 lead stories fully read, 4 verified items) — counting unit: distinct events after ledger dedupe; plus one first-and-only footnote above.
- Ledger dedupe: **8 candidate events suppressed** as already reported (Megaspeed Entity List re-coverage incl. HN/UPI; Huang-reinstatement analysis pieces — NYT, NPR; WSJ Alpöge profile on the reported Lean-claim event; Alfozan recordings re-coverage; Anthropic workplace study; Axios unreleased-models warning; Ollama v0.40.18; GLM-5.4 river re-list).
- New items with coverage elsewhere: ShellBench (3 sources), Anthropic IPO break (2 sources); all engine claims from GitHub's own API.
- Dead ends / limitations: the search backend intermittently returned no results (5 of ~11 queries failed) — recovered via Techmeme river, HN Algolia, HF and GitHub APIs; the Reddit lane was fully down this fire (both search.rss and new.rss 404 — logged as unavailable, not a negative finding); CNBC's chip-pullback piece (Nvidia -8%, AMD/Micron -11%+ at 10:25 EST) failed two retrieval strategies and is excluded per the two-strike rule; Reuters' own URL was paywall-fetched blank, so the syndicated AOL copy is cited alongside Bloomberg. GreyNoise's Check Point compromise campaign (22 orgs, HN front 11:32 EST) is deliberately excluded as non-AI security; left to the cybersecurity brief. No AI-security CVE cluster was disclosed in this window.
