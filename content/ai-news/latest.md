# AI News Briefing — Sunday, November 15, 2026 (07:00–12:00 ET)

**Window:** 2026-11-15 07:00 EST → 2026-11-15 12:00 EST (5 hours; previous fire 07:00 today). New items only — the underlying disclosure date anchors every entry.

**TL;DR**
- Treasury Secretary Scott Bessent told Fox's *Sunday Morning Futures* that Nvidia's board must "go outside the company" for a new CEO and gave the board plus its outside counsel **10 days** to answer whether the company tolerated export-restricted chips ending up in China — the first senior-Cabinet demand since Jensen Huang's Thursday resignation.
- TSMC confirmed in an **SEC filing** that the 6.9GB / ~46,000-record employee dataset matches "data stored in the system services of a cloud service provider" — an external breach, no evidence TSMC's own systems were compromised — and the leaker's one-week free-release deadline is **today**.
- **llama.cpp mainlined MiniMax-M3.1 model support** (#32301) and added an **x86 AVX-VNNI Q2_0 dot product** (#26348) — a CPU-side decode/prefill win that matters for small quants on CPU-only and hybrid hosts.
- r/LocalLLaMA is chasing a **possible new Z.ai GLM family**: three blurred entries (59.4 / 57.1 / 54.0) on a public leaderboard, suspected GLM-Nano/Pro/Med/Small, plus an abnormal Hugging Face activity spike. Unverified.
- Thin Sunday window: **9 verified items** (7 new + 2 updates on previously reported events) plus 3 "just missed the window" footnotes.

---

## Models and local inference

**llama.cpp mainlines MiniMax-M3.1 and adds an AVX-VNNI Q2_0 dot product** — event date: today, Nov 15 (commits 10:19 and 11:39 ET)
Ggml-org's repo took four in-window commits: `model: support MiniMax-M3.1 (#32301)` at 11:39 ET, `ggml-cpu: add x86 VNNI Q2_0 dot product (#26348)` at 10:19 ET, `server: ignore empty stop strings (#32093)` at 11:23 ET, and a CI/HIP symbol fix at 09:53 ET. MiniMax-M3.1 (the 428B-A23B MoE whose open weights landed Oct 23) now has first-class loader support in the flagship local runtime, and the VNNI work gives the low-bit Q2_0 path an integer dot-product kernel on modern x86 — the class of op that shows up as CPU prefill and batch-1 decode gains rather than headline benchmark moves.
*Why it matters:* day-0 loader support is the gate for everything downstream in the GGUF ecosystem (community quants, llama-swap, Ollama/LM Studio ingestion), and a VNNI-accelerated Q2_0 kernel is directly useful on hosts where the model runs from RAM rather than VRAM.
Source: llama.cpp commit history (github.com/ggml-org/llama.cpp), Nov 15 2026, 15:19–16:40 UTC — https://github.com/ggml-org/llama.cpp/commits/main
Source: r/LocalLLaMA thread "CPU-only boost for Q2_0 on AVX-VNNI · Pull Request #26348", Nov 15 2026 11:55 ET — https://www.reddit.com/r/LocalLLaMA/comments/1xx0fqn/cpuonly_boost_for_q2_0_on_avxvnni_pul

**Community spots three blurred "GLM" entries on a public leaderboard** — event date: today, Nov 15 (post 10:21 ET)
An r/LocalLLaMA user says three model names, blurred in a leaderboard screenshot, sit at 59.4%, 57.1% and 54.0%, and argues they are most likely GLM-Nano (or GLM-Name) Pro/Med/Small; one community experiment re-applied a Gaussian blur (σx ≈ 6.9px) to reconstructed text and hit RMSE 1.254/255 against the original image. The post also tabulates leaker AICodeKing's historical lead times on GLM releases (GLM-4.6 same day, GLM-5 one day, GLM-5.1 eleven days, GLM-5.2 three days, GLM-5.3 four days) and flags an unusual Hugging Face activity burst for the org. Treat as speculation: the names are unread, no Z.ai statement exists, and commenters immediately attacked the sourcing.
*Why it matters:* small/cheap GLM variants are exactly the tier that lands in consumer-GPU rigs within days of sighting, and prior stealth-leaderboard sightings in this ledger have preceded real releases.
Source: r/LocalLLaMA "Probable GLM model", Nov 15 2026 10:21 ET — https://www.reddit.com/r/LocalLLaMA/comments/1xwy3ka/probable_glm_model/

**vLLM takes two DeepSeek-V4.1 merges, both AMD-path** — event date: today, Nov 15 (merged 07:56 and 10:27 ET)
Only two PRs merged in the window: `[ROCm][DSv4.1][Perf] Run the fused mHC seams as AITER's single-launch HIP Mega-MHC` (#65992) and `[Bugfix][DSv4.1] Use int64 row offsets in the K-cache gather kernels` (#65926). The first collapses the hyper-connection seams into one AITER HIP launch on ROCm; the second fixes an integer-overflow class bug in the K-cache gather path.
Source: vLLM merged-PR search, repo:vllm-project/vllm, Nov 15 2026 — https://github.com/vllm-project/vllm/pull/65992

**Apple's Siri-AI smart glasses targeted at WWDC 2027** — event date: today, Nov 15 (AppleInsider 10:15 AM EST; Bloomberg 11:30 AM ET)
Mark Gurman's *Power On* reports Apple intends to announce its smart glasses at WWDC 2027 with a launch later that year, with the pitch built on deeper third-party Siri AI integration rather than bespoke glasses apps; AppleInsider adds prototype detail — oval lenses with cameras flush to the frame, a proprietary magnetic connector on the left arm, frames reportedly thinner than Meta's current glasses — and frames the privacy story around the Secure Exclave, the separate processing unit Apple uses for Watch Audio Intelligence so nothing is ever written as a recording. AppleInsider itself scores the item "Possible" and notes the WWDC 2027 announcement date is the only genuinely new element.
*Why it matters:* on-device assistants that reach into third-party apps are the deployment pattern where local inference has to carry latency and privacy guarantees — Apple's Secure Exclave framing is an explicit argument for doing the sensing locally.
Source: AppleInsider, "Lightweight Apple Glasses rumored for WWDC 2027 reveal", Nov 15 2026 10:15 AM EST — https://appleinsider.com/articles/26/11/15/lightweight-apple-glasses-rumored-for-wwdc-2027-reveal
Source: Bloomberg (via Techmeme river), Nov 15 2026 11:30 AM ET — https://www.bloomberg.com/news/newsletters/2026-11-15

---

## Agent tools

**langchain-core 1.8.1 ships** — event date: today, Nov 15 (release published 09:57 ET)
The patch release is the only versioned agent-framework tag published in the window (langchain 1.5.0 and langchain-openai 1.8.0 both landed earlier in the week; Microsoft Agent Framework's python-1.26.0 dates to Nov 13). No release notes beyond the version bump were published in the window.
Source: GitHub releases, langchain-ai/langchain, Nov 15 2026 14:57 UTC — https://github.com/langchain-ai/langchain/releases

**Also noted (agent tooling)**
- r/LocalLLaMA (10:23 ET): a "record once, replay with zero calls, judge with a local model" harness for testing agents in CI without invoking a live model — self-published, no independent validation yet. https://www.reddit.com/r/LocalLLaMA/comments/1xwy5ia/testing_agents_without_running_a_mode
- Show HN (11:38 ET, 2 points): Claudette — run Mistral models inside Claude Code (github.com/raed667/claudette), HN item 50508283. Early signal only, no traction yet.

---

## AI security and governance

**Update on previously reported (ledger: megaspeed-alice-huang) — Bessent demands a "dramatic shakeup", an outside CEO, and answers in 10 days** — event date: today, Nov 15 (Fox "Sunday Morning Futures"; Bloomberg river 10:50 AM ET)
US Treasury Secretary Scott Bessent said on Sunday that Nvidia's board "need to go outside the company" for the next CEO — "this makes me think that as the board conducts a search for a new leader that they need to go outside the company, that just (with) what was going on inside the company, they need a dramatic shakeup" — and said the board and its outside counsel have **10 days** to answer questions about the company's culture of compliance with export restrictions. Reuters filed the interview from Washington (byline Douglas Gillison); Bloomberg led with the 10-day demand. This is the first Cabinet-level ultimatum since Huang resigned Thursday, and it lands on top of the board's already-confirmed CEO search, which the FT reported has approached Arm CEO Rene Haas.
*Why it matters:* a Treasury secretary setting a public countdown converts an internal governance crisis into an export-control enforcement deadline — it raises the probability that Nvidia's China/SEA channel (Megaspeed, the Senawang data-centre network) is resolved by legal compulsion rather than board housekeeping, which is the part of this story that actually affects chip supply.
Source: Reuters (syndicated, Global Banking & Finance Review), "Bessent says Nvidia must 'go outside the company' to find new CEO", Nov 15 2026 — https://www.globalbankingandfinance.com/bessent-nvidia-go-outside-company-find-new-ceo/
Source: Reuters via Money.usnews.com, "Bessent Says Nvidia Must 'Go Outside the Company' to Find New CEO", Nov 15 2026 — https://money.usnews.com/investing/news/articles/2026-11-15/bessent-says-nvidia-must-go-outside-the-company-to-find-new-ceo
Source: Bloomberg, "Bessent Calls for Nvidia Leadership Overhaul After Huang's Ouster", Nov 15 2026 (Techmeme river 10:50 AM ET) — https://www.bloomberg.com/news/articles/2026-11-15/bessent-says-nvidia-needs-dramatic-shakeup-after-huang-ouster

**Update on previously reported (ledger: tsmc-3btc-extortion) — TSMC's SEC filing points the breach at a cloud service provider; the leaker's deadline is today** — event date: today, Nov 15 (10:23 AM ET)
BleepingComputer reports TSMC confirmed in an SEC filing that "some of our current and former employee data was made available on an external website without authorization", that the leaked material matched "data stored in the system services of a cloud service provider", and that it has "found no evidence that TSMC's systems have been compromised". The company is notifying affected staff and has engaged a third-party cybersecurity firm. The actor "SmithBianl" posted a 499-row sample on November 8 claiming a 6.9GB / 46,000-record dataset (national ID numbers, addresses, contacts, banking details), auctioned it from 3 BTC (~$65,000), and said it would leak the set free after one week — a deadline of November 15. TSMC declined to say whether it engaged the actor or planned to pay.
*Why it matters:* this is the supply-chain-reconnaissance angle the Alice Huang saga just made salient — a national-ID plus banking dataset for the world's largest contract chipmaker, exfiltrated from a vendor's cloud tenancy rather than TSMC's own network, is a social-engineering and physical-access target list, not just a privacy incident.
Source: BleepingComputer, "TSMC employee data breach linked to cloud provider", Bill Toulas, Nov 15 2026 10:23 AM — https://www.bleepingcomputer.com/news/security/tsmc-employee-data-breach-linked-to-cloud-provider/

**"All AI-generated code should be Open Source" tops Hacker News** — event date: today, Nov 15 (posted 09:46 ET; 353 points, 222 comments)
Jakub T. Jankiewicz argues that code produced with AI help should be released open-source or under CC0, on two grounds: models were trained on scraped open-source code and CC-licensed Stack Overflow content "without permission or proper attribution", so closing the output is "license laundering"; and purely AI-generated code is not human-authored and therefore not copyrightable — citing the Monkey Selfie line of precedent that the U.S. Copyright Office later applied to AI, plus Wikimedia Commons' public-domain tagging of purely AI-generated images. He concedes jurisdictions differ (the UK grants limited computer-generated-work protection) and that most code is a human/AI hybrid.
*Why it matters:* this is the licensing argument that open-weight ecosystems will weaponise next — if AI-generated code is uncopyrightable, the moat in agent-written codebases disappears, and the "attribution debt" framing gives the OSS-maintainer backlash (see the OSS-contribution-ban tracker already in this ledger) a legal hook.
Source: Jakub T. Jankiewicz, "All AI-generated code should be Open Source" — https://jakub.jankiewicz.org/blog/ai-code-open-source/
Source: Hacker News item 50507303, Nov 15 2026 09:46 ET (353 points, 222 comments) — https://news.ycombinator.com/item?id=50507303

---

## Just missed the window
- **Agnes 2.5 Pro Theta open BF16 weights** — the Hugging Face repo `Agnes-AI/Agnes-2.5-Pro-Theta` (187.8B-parameter multimodal MoE, 512 routed experts / top-10, 262,144-token context, Apache-2.0, 105 shards / ~375.5 GB, publisher-supplied scores of 92.9 GPQA-Diamond / 90.5 LiveCodeBench v6 / 64.1 SWE-bench Pro against Nemotron 3.5 Super VL 120B, Qwen3.8-27B, GLM-5.3-Flash and DeepSeek-V4.1-Flash-1017) was created 2026-11-15 02:46 UTC — **21:46 ET Nov 14, nine hours before the window opened**. The card itself flags the numbers as a "publisher-provided chart, not a reproducible evaluation bundle", and API-side trackers date the hosted model to Nov 7.
- **X3Guard-150M** — Axiomic Labs' 149M prompt guard fine-tuned from GPT-X3-150M, dual-scoring prompts as Harmful and Adversarial, Apache-2.0 with an in-browser demo and ONNX quants; the repo was created Nov 13 (lastModified Nov 14 18:10 UTC) and the author posted it to r/LocalLLaMA today at 10:46 ET, so the in-window activity is promotion, not disclosure. Its own card puts Granite Guardian 3.1 2B and Qwen3Guard-Gen 0.6B ahead on harm F1, and Sheltron's prompt-guard-68m ahead on adversarial detection — i.e. smaller/better alternatives already exist.
- **SEBI AI guidelines for India's capital markets** — Chairman Tuhin Kanta Pandey's commitment to detailed responsible-AI guidelines (disclosures, accountability of regulated entities, human-in-the-loop for critical decisions) was made **Saturday** at SEBI's IITF pavilion; the PTI wire published Sunday 12:38 IST (02:08 ET), five hours before this window.

## Coverage notes
- **Verified: 9 in-window items** (7 new + 2 updates on previously reported events), plus 3 just-missed footnotes. Every cited page was fetched and carries an in-window timestamp, except where the primary is a GitHub/Reddit/HN record whose timestamp came from the API or post metadata.
- **Ledger dedupe: 6 candidates suppressed as already reported** — NYT's Lutnick/Cantor chip-curbs piece (river 11:10; `lutnick-cantor-chip-rules`), The Decoder's Megaspeed $12.4B follow-up (`update:megaspeed-alice-huang` 07:00 fire), The Hacker News's Cursor/Fireworks-key story (repeat footnote of `missed:cursor-fireworks-key`, suppressed), The Decoder's LongCat-2.5 weights piece (repeat footnote of `missed:longcat-2-5-weights`, suppressed), THN's Italy/Meta €805M re-coverage (`garante-meta-805m`), and the CMP 170HX voltage-glitch repost (`cmp170hx-a100-glitch`).
- **Stale re-coverage excluded outright (out-of-window events):** The Decoder's RBA AI-and-young-workers piece (analysis published Nov 9), Haru-4B full-duplex voice model (repo Nov 12), LongCat-Video-Avatar-1.6 (repo Nov 13), Marcos' EO 131 AI council (Nov 12), Stuart Russell's Hindustan Times Leadership Summit remarks (Nov 14), Cadence joining the DOE Genesis Mission (announced Nov 14), TestMu AI's State of AI in QE (Nov 13).
- **Engine traffic was genuinely quiet:** vLLM merged 2 PRs and SGLang/Ollama/OpenClaw published nothing in-window (OpenClaw v2026.11.5-beta.3 was 19:31 ET Nov 14). llama.cpp's four commits were the only steady activity.
- **Dead ends / limitations:** web_search's backend threw `No results found` on several queries (three searches lost to provider errors, not bad wording — retried with different framings rather than synonyms); the-decoder.com's "Claude Fable 5.2 doesn't generate images, it paints them" could not be resolved to a fetchable URL and is re-coverage of the Nov 9 Fable 5.2 launch anyway; CNBC's Bessent URL returned `content.notfound` to the fetcher, so the story is carried on the Reuters syndication plus US News/Bloomberg instead; The Hacker News and r/LocalLLaMA produced no AI-specific disclosures of their own in the window beyond those listed.
