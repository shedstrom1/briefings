# AI News Briefing — Sunday, November 1, 2026 (07:00 ET edition)

**Window: 2026-10-31 17:00 ET → 2026-11-01 07:00 ET** (previous fire 17:00 Oct 31; new items only, ledger-deduped)

## TL;DR

- OpenAI told Plus, Pro, and Business subscribers that ChatGPT and Codex will share **one token-based usage allowance starting in December**, as ChatGPT Work's agent features fold into the main ChatGPT app — with Instant chat exempt and no per-plan numbers published yet.
- Memdoor launched: an Apache-2.0 single-binary coding/automation agent whose team-solve workflow — five Qwen3.8-27B agents — now posts a mean **9.8 of 10 on LiveCodeBench Hard**, beating both the original GVS5H harness (9.6) and Fable 5 in a single call (8.0), with all 50 runs published.
- The Washington Post's midterm testing found Copilot cited the error-prone, AI-built Ballot Brief site in **75% of voter-guide answers** (ChatGPT 22%), propagating zip-code-to-district mapping errors to real voters.
- A quiet zero: **no new model releases** landed in the window — verified across Hugging Face new-arrivals, the open-weights trackers, and release timelines.

## Agent tools

**OpenAI merges ChatGPT and Codex usage limits into one token pool in December** — event date: Friday, Oct 31 (Help Center article updated overnight; HN front page 21:49 UTC, 162 points).
The "Understanding ChatGPT and Codex usage limits" Help Center article now says Plus, Pro, and Business plans' usage allowance "will be shared across ChatGPT and Codex as we bring the agentic capabilities of Work into the main ChatGPT experience." Usage is measured in tokens, scales with model and task, and Instant-mode chat will not draw from the pool. OpenAI published no exact rollout day and no per-plan token counts, saying only that it "expects most users won't need to wait for their usage to reset more often than they do now"; when you hit the cap you can wait for reset, upgrade, or buy credits, with a combined remaining-usage view landing under Settings → Usage.
**Why it matters:** This is the metering change that reveals OpenAI's product plan — once the same agent runs inside ChatGPT itself, separate Codex meters stop making sense. For teams that chose Codex partly because the 5-hour rolling cap had been disabled on Pro, a single shared pool means heavy ChatGPT use can now starve agent runs, and vice versa — and unlike Anthropic's approach, the pool sizes are unpublished.
- Source: OpenAI Help Center, "Understanding ChatGPT and Codex usage limits" (updated Nov 1): https://help.openai.com/en/articles/20001625-understanding-chatgpt-and-codex-usage-limits
- Source: explainx.ai, "Codex Limits Merge in December: What Changes" (Nov 1, notes the article carried an "Updated: 8 hours ago" stamp): https://explainx.ai/blog/chatgpt-codex-usage-limits-merge-shared-token-allowance-december-2026
- Source (corroboration): Xenospectrum, "OpenAI to Merge ChatGPT and Codex Usage Limits in December for Plus…": https://xenospectrum.com/en/chatgpt-codex-shared-usage-limits-december/

**Memdoor ships an Apache-2.0 agent CLI; its five-Qwen3.8-27B team workflow now beats Fable 5 on LiveCodeBench Hard** — event date: Friday, Oct 31 (HN front page 22:46 UTC, 269 points). *Update on previously reported (ledger 2026-10-25, GVS5H).*
Gurego (guregodevo) released Memdoor, a headless-first coding/automation agent in one static binary (macOS/Linux/Windows, no Docker or Python), with a local gateway that keeps runs resumable from terminal, phone, CI, or editor over the Agent Client Protocol. Its distinguishing mechanic is a Jev decision model sitting in front of the chat model — across 44 paired runs a codebase question went from 54,661 to 14,367 input tokens (−74%) with identical pass rates. The headline update: rebuilt with the GVS5H authors' exact prompts and loop, its team-solve workflow (five Qwen3.8-27B agents, first ten LiveCodeBench-Hard problems, five passes each) averages **9.8/10 vs 9.6 for the authors' own harness and 8.0 for Fable 5 single-call**, with all 50 runs' requests, replies, programs and grader output published.
**Why it matters:** The Oct 25 GVS5H result was a research demo; this productizes it — the ensemble is now just a model-agnostic YAML workflow you can point at any endpoint.
**Local inference:** `memdoor connect local` targets Ollama, llama.cpp, vLLM, and LM Studio with no key, and the bundled janis server runs open models (e.g.
- Source: guregodevo/memdoor (GitHub, fetched Nov 1): https://github.com/guregodevo/memdoor
- Source (corroboration): Hacker News, "Memdoor: A team of 5 Qwen3.8-27B models beats Fable 5 on LiveCodeBench Hard" (Oct 31 22:46 UTC): https://news.ycombinator.com/item?id=50320995

## Models and local inference

**No new model releases verified in-window** — checked against Hugging Face newest-created API, LLM Gateway's release timeline (newest: DeepSeek V4.1 Flash Cyber, Oct 29), and The Open Weights aggregator; the community GGUFs visible in the window (Xiaomi-OCR-0 Q4_K_M, DeepSeek-V4.1-Flash-1017-DSpark, HTB's Qwen3.8-2B-Distill-heretic-v2 GGUF) all quantize bases disclosed before the window (Sep 29, Oct 17, and Oct 31 09:09 ET respectively).

**vLLM overnight merges: pipeline-parallel accuracy fix and Muse routing fusion** — event date: Saturday night/Sunday morning (merged 06:47–09:03 UTC Nov 1).
Four in-window merges verified: #59961 carries the shared indexer top-k across pipeline-parallel stages, fixing stale-top-k reads that cost GLM-5.3-NVFP4 GSM8K accuracy — 0.879→0.908 on PP2×TP2 (and 0.820→0.908 with gather-DCP), validated on 4×B300 and 8×MI350X; #64955 (WoosukKwon) fuses CUDA-graph padding skipping into grouped and Muse-prepared NVFP4 routing, collapsing five kernel launches to one with 43–70% lower isolated median operation span; plus KV-offload cleanup #63900 (touch-hook removal) and #63695 (request context on raw store events).
**Why it matters:** The PP top-k fix is a silent-correctness win for GLM-5.x/DeepSeek-V3.2-style indexer-reuse models on multi-node deployments; the padding fusion is another step in vLLM's serving-latency grind on Blackwell.
- Source: GitHub vLLM merged-PR search, merged 2026-10-31 21:00→2026-11-01 12:00 UTC: https://github.com/vllm-project/vllm/pull/59961 , https://github.com/vllm-project/vllm/pull/64955

**llama.cpp: eight builds overnight — OpenCL iq3_s, Hexagon DMA fix, shared-KV rotation fix** — event date: Oct 31 17:38–Nov 1 04:04 ET (b12024–b12032; b12026 skipped in the release feed).
Headliners: b12031 adds initial iq3_s OpenCL support (AOS multi-vector, tiled MM, and Adreno dp4a/GEMV kernels with Qualcomm collaboration); b12030 fixes Hexagon 2D-DMA transfers with strides over 24 bits that silently produced wrong addresses on v75+; b12025 fixes rotation of attention tensors in layers with shared KV; b12027 enables tensor split for LFM3; b12032 moves SYCL LevelZero discovery to PkgConfig.
**Why it matters:** The shared-KV rotation bug is a quality issue affecting any KV-sharing architecture (the popular Qwen/GLM-style dense-attention groups), and the iq3_s OpenCL work keeps extending what low-bit quants can run well on non-NVIDIA iGPU/Adreno paths.
- Source: llama.cpp releases feed (GitHub, fetched Nov 1): https://github.com/ggml-org/llama.cpp/releases

## AI security and governance

**Washington Post: chatbots lean on AI-built, error-prone voter sites for midterm info** — event date: Friday, Oct 31, 8:00 PM ET (WaPo tech desk; Techmeme river at that hour).
Post testing asked six popular chatbots for voter guides for a random address in every US county and checked more than 3,000 guides. Microsoft Copilot cited the Ballot Brief — a site the Post describes as largely produced by AI tools and run by a former Rumble executive — in 75% of voter-guide responses, ChatGPT in 22%, with Perplexity also citing it substantially; the Post found multiple wrong zip-code-to-district mappings on the site that chatbots then passed to users, and several bots missed the correct district entirely. Copilot, Perplexity and ChatGPT substantially relied on three obscure AI-built voter sites in total.
**Why it matters:** Election information is the archetype of a high-stakes, checkable domain — this is retrieval-grounding failure at the top of the stack (bots optimizing citation popularity) meeting a low-quality AI-generated feedback loop, two weeks before midterms. For local-inference operators it's an argument for pinned, curated grounding rather than default web search on election queries.
- Source: The Washington Post, "How an obscure website became a force in America's election information machine" (Oct 30–31, syndicated via Techmeme Oct 31 20:00 ET): https://www.washingtonpost.com/technology/2026/10/30/how-an-obscure-website-became-force-americas-election-information-machine/
- Source (corroboration): AI Weekly, "Copilot Cited AI-Built Ballot Brief in 75% of Election Tests": https://aiweekly.co/alerts/copilot-cited-ai-built-ballot-brief-in-75-of-election-tests ; explainx.ai test breakdown: https://explainx.ai/blog/washington-post-chatbot-voter-guides-ai-built-sites-ballot-brief-copilot-chatgpt-midterms-2026

## Just missed the window (footnoted once, ever)

- **Anthropic quietly steered Claude Code/Opus 5.5 away from recreating or reverse-engineering existing games** — Tom's Hardware published Oct 31 ~12:00 UTC (≈9h pre-window). The rule (internal name "tengu_heron_brook"): "Do build original games… Do not build a recreation of a specific existing game, even if it is renamed or called original"; users report non-malicious decomp/troubleshooting also blocked; Anthropic's Ramzi Metwally-Osmani confirmed a two-sentence prompt addition. In-window HN pickup (151 pts, 21:07 UTC) was re-coverage. Sources: https://www.tomshardware.com/tech-industry/artificial-intelligence/anthropics-ai-model-is-now-blocking-attempts-at-video-game-recreation-and-cloning-users-also-claim-non-malicious-generic-reverse-engineering-tasks-are-blocked ; https://www.gamedeveloper.com/programming/anthropic-appears-to-have-ordered-ai-tool-claude-to-stop-remaking-video-games
- **Deepgram acquired Daily (makers of the open-source voice-agent framework Pipecat), establishing an independent Pipecat Foundation; Daily co-founder Kwindla Hultman Kramer becomes Deepgram's head of open source** — announced Oct 29 per both companies' blogs (event date governs); Techmeme re-ran it Nov 1 1:15 AM. Sources: https://deepgram.com/blog/daily-pipecat-deepgram ; https://www.daily.co/blog/deepgram-is-acquiring-daily-and-establishing-an-independent-pipecat-foundation/

## Coverage notes

- **Verified: 5 in-window items** (3 lead stories + 2 ecosystem/tooling rows, each fetched and read with in-window timestamps) **+ 2 just-missed footnotes**. Zero qualifying model releases — verified, not a retrieval failure.
- **Ledger dedupe: 5 candidates suppressed as already reported** — Bloomberg's DeepSeek Super-Agent piece (repeat of `missed:dsh-superagent-prototype`), The Hacker News ChatGPT share-link P1 (repeat of `missed:chatgpt-sharelink-p1`), WSJ/FT OpenAI-round coverage (repeat of `openai-11b-of-30b`), hyperscaler CDS-at-2021-highs (same story family as `neocloud-bond-shutout`/`sp-meta-negative`), and the Oct 30 17:00-ET four-lab robots.txt opt-out (previously footnoted once).
- Excluded as stale-event resurfacing: HN front-page "Brazilian Court Sanctions Lawyers for Prompt Injection" (underlying Jetstream advisory dated July 1, 2026) and the 2024 air-gap RAM radio arXiv paper.
- Dead ends (two-strike rule): Lumaril's $66M Series A (WSJ 1:35 AM river item — no fetchable non-paywalled copy resolved); Bria Fibo-2.0-dev HF page returned an auth error, so its date is unverifiable — left out.
- Indexes healthy: HN Algolia, GitHub releases/merged-PR search, HF newest-models API, Techmeme river all returned; no Ollama or vLLM version releases in-window (latest: v0.40.13 / v0.32.0).
