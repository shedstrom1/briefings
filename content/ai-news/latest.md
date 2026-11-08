# AI News Briefing — Sunday, Nov 8, 2026, 12:00 ET

**Window:** 2026-11-08 07:00 ET → 12:00 ET (5 hours since the 07:00 fire).

**TL;DR**
- Financial Times reports Anthropic locked up **$3.4B for 1.7GW of gas-fired generators** via OnePWR — the largest known power-equipment buy by an AI lab, signed in late September.
- Apple CEO John Ternus is pushing Craig Federighi to build a **true agentic Siri** (tracks goals, works between conversations) with a 2027 ship target, per Bloomberg's Gurman — and restructured the iOS 28 sprint to 6 weeks of coding vs 4.
- **llama.cpp** shipped builds b12207–b12211 (SpacemiT MXFP4 repack, llamafile bf16/q8_0 vxe, deterministic CUDA/Metal Top-K); **vLLM** merged 10 PRs including Mooncake circuit breakers and encoder CUDA-graph batching.
- Quiet 5-hour window otherwise: no in-window model releases or security disclosures; six overnight stories sit in "Just missed" footnotes.

## Models and local inference

**Anthropic buys commitments for 1.7GW of gas-fired generators for $3.4B**
Event date: disclosed 2026-11-08 (deal signed late September 2026). The Financial Times reports Anthropic struck a $3.4B deal with OnePWR Solutions (San Antonio, TX) to take over 1.7GW of heavy-duty generators that OnePWR had ordered from Caterpillar roughly two years ago but lacked capital to complete. Anthropic assumes the orders plus installation work — not OnePWR itself and no staff transfers. FT calls it the largest known single purchase of power-generation equipment by an AI company; Bloomberg followed up the same day, and Piper Sandler describes it as the sale of OnePWR's power-gen platform to "a frontier AI lab." The deal lands as Anthropic prepares its IPO — its amended S-1 filed last week showed ~$895B in purchase commitments and leases, almost all compute and infrastructure.

*Why it matters:* This is physical compute security, not cloud contracts. By buying generator commitments directly, Anthropic is locking in firm power capacity independent of grid timelines — a signal that data-center energy, not just chips, is now the binding constraint for frontier training.

*Source:* Financial Times, "Anthropic strikes $3.4bn deal to generate gas-fired power for its data centres," 2026-11-08 ~10:05 ET — https://www.ft.com/content/41bf5c6a-9735-4f1a-a8e3-2bcccad58ba0
*Further:* The Next Web, "Anthropic buys $3.4bn of gas generators, FT reports," 2026-11-08 ~10:30 ET — https://thenextweb.com/news/anthropic-3-4bn-gas-generators-onepwr-ft

**llama.cpp ships builds b12207–b12211 (five builds) with SpacemiT MXFP4 repack and llamafile updates**
Event date: 2026-11-08, 09:20–11:48 ET (GitHub). Five release builds landed in-window, carrying six notable commits: SpacemiT IME2 MXFP4 dense repack (#31776), llamafile bf16 and q8_0 vxe implementations (#31866), deterministic tied-selection fixes for CUDA/Metal radix Top-K (#31084), a d_state=64 fast path in the SSM_SCAN CUDA kernel (#31818), moe-cache no longer blocking the CPU path on cached experts (#31888), and mtmd consolidation removing clip.h (#31865).

*Why it matters:* The SpacemiT MXFP4 repack extends low-precision CPU inference to RISC-V accelerators, and the llamafile vxe additions broaden the quant formats available in the single-binary local runner — both expand what runs on non-NVIDIA local hardware.

*Source:* GitHub, ggml-org/llama.cpp releases b12207 (14:20Z) through b12211 (16:48Z), 2026-11-08 — https://github.com/ggml-org/llama.cpp/releases

**vLLM merges ten PRs: MOSS-Transcribe-Diarize config, Mooncake circuit breaker, encoder CUDA-graph batching**
Event date: 2026-11-08, 07:20–10:03 ET (GitHub). Merges include: upstream config adoption for MOSS-Transcribe-Diarize (#66388); a circuit breaker bounding Mooncake store prefix lookups (#65939); multimodal encoder CUDA graphs running one replay per batch with a finer grid up to one max budget (#65227); pluggable shared stores for daemon warmup caches (#65168); ROCm padding fix for Qwen4Exp PLE n-gram inputs in piecewise CUDA-graph replays (#62675); and sleep-mode offload of model-runner construction buffers (#59994).

*Why it matters:* The Mooncake guard and warmup-cache plumbing reduce long-tail failures in distributed KV-offload deployments, while the encoder CUDA-graph change lowers latency for multimodal serving stacks.

*Source:* GitHub, vllm-project/vllm merged PRs (search: merged 2026-11-08T12:00Z..17:00Z), 2026-11-08 — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged

## Agent tools

**Bloomberg: Ternus demands agentic Siri from Federighi, targets 2027, overhauls iOS 28 sprint**
Event date: 2026-11-08, 11:20 ET (Bloomberg "Power On," Mark Gurman). Apple's new CEO John Ternus held a closed-door meeting with software chief Craig Federighi explicitly demanding more urgency: move beyond incremental Siri AI tweaks to an agentic assistant that tracks goals and continues work between conversations, with a target of shipping as early as 2027. The push also restructured the iOS 28 M1 milestone from a 4/4 coding/integration split to 6 weeks of coding and 2 of integration — a 50% coding-time increase. Vision Pro creator Mike Rockwell is reportedly weighing a departure amid the shake-up.

*Why it matters:* This is Apple's clearest internal mandate yet that Siri must become a persistent agent, not a voice assistant with LLM polish. The sprint restructuring shows engineering resources are being reallocated to agent continuity — the same "works across conversations" capability that defines the current agent-tool race.

*Source:* Bloomberg (Mark Gurman), "Apple Explores Turning Siri AI Into ChatGPT Dots-Like Agent," 2026-11-08 ~11:20 ET — https://www.bloomberg.com/news/newsletters/2026-11-08/apple-explores-turning-siri-ai-into-chatgpt-dots-like-agent-ios-28-shake-up
*Further:* 9to5mac, "Bloomberg: Apple CEO Ternus Pressures Federighi to Ship Agentic Siri by 2027, Overhauls iOS 28 Sprint," 2026-11-08 16:59 UTC — https://9to5mac.com/ (via AI Weekly alert, https://aiweekly.co/alerts/bloomberg-apple-ceo-ternus-pressures-federighi-to-ship-agentic-siri-by-2027)

## AI security and governance

No verified items first disclosed inside this 07:00–12:00 ET window. (The Kahn v. Anthropic class-action ruling, TSMC's 3D-DRAM denial, and the xAI Grok watermark activation all broke before the window opened — see footnotes.)

## Also noted

- **Show HN: Webcrawl** — open-source Firecrawl alternative that runs locally with no API key (brainfish-ai/webcrawl-anything); agent-scraping utility, posted ~11:03 ET on Hacker News (story 50414396).

## Just missed the window

- **Thinking Machines Lab is closing a $5B round led by Accel at a $40B pre-money valuation** — more than triple its $2B seed valuation (Business Insider, Nov 5; resurfaced on HN's front page ~11:39 ET today).
- **Menlo Ventures "State of Enterprise AI 2026" report** (released Nov 3; SCMP coverage Nov 6): Chinese models took **89% of global enterprise spending on open-source models** — Kimi 61%, DeepSeek 15%, Z.ai's GLM 11% — while Meta's Llama held just 7%; Chinese models have led OpenRouter token usage for 27 consecutive weeks.
- **xAI activated an invisible statistical text watermark in Grok API responses** on Nov 7 across grok-4.7, 4.6, 4.5, 4.3 and three grok-4.20 variants; detection exposed via an `x-watermark` header, and admins can disable it only by asserting an EU AI Act legal exception (DataStudios, ~05:00 ET today).
- **Microsoft released APM (Agent Package Manager)** — MIT-licensed dependency manager declaring skills, prompts, plugins, and MCP servers in a single `apm.yml` manifest across nine harnesses including GitHub Copilot, Claude Code, and Cursor (microsoft.github.io, ~03:00 ET today).
- **Tencent published Qwen3.8-27B-DSpine-b16 on Hugging Face** (Nov 7): a five-layer parallel speculative-decoding drafter (block size 16, 8,192-token sliding window) that hits up to 3.84x autoregressive throughput on agent benchmarks in SGLang — a small (~2B-param) model that runs comfortably on local GPUs alongside Qwen3.8-27B.

## Coverage notes

- **Verified in-window:** 4 items (2 news disclosures, 2 GitHub layer updates) + 1 also-noted community post = 5 verified. Counting unit: distinct events/items.
- **Ledger dedupe:** 7 candidates suppressed as already reported or previously footnoted — SemiAnalysis Inner Mongolia Galaxy hub (07:00 run), environmental groups/COP31 embrace of AI (07:00 run), Nous Arete-27B (Nov 6 run), Andy Hall agent-auditor study re-coverage (Nov 7 run), Claude Code `.claude/settings.local.json` secret-leak scan (footnoted Nov 7), MSR AI Frontiers closure/Langford exit (footnoted Nov 7), TSMC 3D-DRAM denial (update on Nov 6 item, broke ~03:00 ET pre-window).
- **Indexes:** Techmeme river (Nov 8, 00:00–11:20 ET), HN Algolia river (12:00–17:00Z), Reddit r/LocalLLaMA + r/artificial (new feed), GitHub release/commit streams (llama.cpp, vLLM, Ollama, SGLang, OpenClaw, Codex). Ollama (v0.40.15, Nov 3), SGLang (v0.5.23, Nov 2), OpenClaw (v2026.11.1, Nov 5), and Codex (rust-v0.169.0-alpha.3, Nov 7) were quiet in-window.
- **Dead ends / exclusions:** Lovable "100k PRs" blog post (dated Sept 24 — stale HN resurfacing); "Show HN: Cinnamon AI" and "0rwx framework" (timestamps/claims not verifiable in budget); GenRobot $100M Series A+ (TechNode 01:05 ET — prior window, peripheral robotics funding, not footnoted).
