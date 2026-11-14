# AI News Briefing — Nov 14, 2026 (12:00 ET edition)

**Window:** 2026-11-14 07:00 → 12:00 ET (5h, since previous fire). New items only; deduped against the reported ledger.

## TL;DR

- **llama.cpp v1.0 gets measured:** an independent RTX 3090 test suite published this morning puts speculative decoding (MTP) on 27B models at **+52–55%** and four-way concurrent serving at **+14–22%** versus the last pre-v1 build — single-stream speed is unchanged, and the new CUDA kernel path targets RTX 30-series and newer.
- **Italy fines Meta €805M** for scanning Android users' photo galleries without valid consent — the largest fine ever imposed by the Italian regulator, announced Saturday morning.
- **Inference engines shipped ~38 in-window changes:** llama.cpp builds b12378–b12383 (Granite 4 Vision registration, video-mtmd fixes), vLLM 14 merges (XPU MLA attention backend, Qwen2-via-Transformers serving, EPLB with modular TRT-LLM FP8 block-scale MoE), SGLang 17 (Hy-Nomas INT8 dequant fusion, IPC leak fix); Ollama quiet.
- Just missed: WaPo's Rubio/CIA-memo report, Lambert's "Open-source AI without Jensen," and the basal-omni-2.0-mini preview — footnoted below.

## Models and local inference

**llama.cpp v1.0, measured on a home RTX 3090: +52–55% with speculative decoding** — Event date: 2026-11-14 (published today, Nov 14).
Homebrewserver.club published a measured comparison of llama.cpp v1.0.0 (build 12359) against the last pre-v1 build (12199) on Fedora 44/Docker, RTX 3090 24GB, Ryzen 9 5900X, Qwen3.5-9B and -27B Q4_K_M. Single-request decode is statistically flat (114.7→116.0 and 39.2→38.7 tok/s); four concurrent requests gain **+14% (9B)** and **+22% (27B)** total throughput; MTP speculative decoding lifts the 27B short-prompt case **45.1→68.5 tok/s (+52%)**, and the article's summary table reports up to +55%. The gains come from a new CUDA kernel handling 2–24 tokens per step on RTX 30-series and newer — the same path covers Blackwell cards (not separately tested in the article). Breaking-change notes for self-hosters: `--defrag-thold` is removed, and `llama-server` now refuses to enable tools/MCP on non-loopback addresses without `--api-key`. The test also confirms **Ollama still lags v1.0** — v0.40.16 bundles llama.cpp b12177, the 0.40.17-rc0 bundles b12257, both pre-v1.
*Why it matters:* this is the first independent, reproducible picture of what v1.0's versioning milestone actually delivers for consumer rigs — the benefit concentrates in multi-user serving and spec-decode, exactly the agent-workload shapes that matter on 16GB-class cards.
*Local inference:* directly relevant — FP8 KV cache (`-ctk f8_e4m3`), the MoE host-expert cache, and `/v1/audio/speech` are the v1.0 features this test's setup predates or ignores; check your Docker tags against build ≥12359.
Source: [homebrewserver.club — llama.cpp v1.0 on an RTX 3090 (Nov 14, 2026)](https://homebrewserver.club/llama-cpp-v1.html)
Source (cross-check): [GitHub — ggml-org/llama.cpp releases (v1.0.0 = 2026-11-13T10:20Z)](https://github.com/ggml-org/llama.cpp/releases)

**Inference-engine round-up — 38 in-window merges/builds (also-noted)** — Event dates: 2026-11-14 12:00–17:00 UTC (verified via GitHub API).
**llama.cpp:** builds b12378–b12383 published in-window; headline commits register **Granite 4 Vision** for conditional generation (#32237), custom video decoder for mtmd (#32188), video aspect/rotation fix (#32267), and a jinja template-rendering overhead reduction (#32023). **vLLM:** 14 merges — XPU FlashAttention-MLA backend (#64255), serving Qwen2 through the Transformers modeling backend (#66821), EPLB allowed with modular TRT-LLM FP8 block-scale MoE (#67591), NVFP4 expert scales kept CSF-compressed on B12X (#64753), RL sparse-NCCL weight-update OOM fix (#66375), Transformers bumped to 5.23.0 (#67502). **SGLang:** 17 commits — Hy-Nomas groupwise INT8 weight-dequant fusion (#49270), multi-rank IPC all-to-all leak fix (#50321), qwen4-exp PLE rows gathered on host for the file-backed table (#50318), spec-decode MRoPE non-blocking H2D (#46781), ComfyUI H3 SHA unpinned (#50223). **Ollama:** no releases since v0.40.17-rc0 (Nov 10).
Source: [github.com/ggml-org/llama.cpp/releases](https://github.com/ggml-org/llama.cpp/releases) · [github.com/vllm-project/vllm](https://github.com/vllm-project/vllm) · [github.com/sgl-project/sglang](https://github.com/sgl-project/sglang)

**Community engine: RustingStrata posts 64 tok/s decode with Flint 40B on a 12GB RTX 3060 (also-noted, self-published)** — Event date: 2026-11-14 (~11:51 ET).
A Rust engine benchmark claims Recursive Flint (40B MoE, 2.5B active) at 64 tok/s decode on a 12GB card, reported as 1.3x llama.cpp, posted to r/LocalLLaMA and r/LocalLLM. Vendor-author benchmark by its own developer; treat as provisional until third-party replication (the pattern of the earlier NInfer six-engine shootout suggests replication will come).
Source: [reddit.com/r/LocalLLaMA — RustingStrata runs Flint 40B on a 12 GB card](https://www.reddit.com/r/LocalLLaMA/)

## Agent tools

*Quiet morning:* no framework releases in the window (verified against OpenClaw/Ollama release feeds and multi-angle search). One notable HN debut in-window: **Cowork**, an open-source "mission control"/MCP task queue for multi-agent orchestration across Claude, Codex and other CLIs (repo pushed this morning 07:39 UTC; small project, two stars — early signal, not a verified release event).
Source: [github.com/slashman413/cowork](https://github.com/slashman413/cowork)

## AI security and governance

**Italy's Garante fines Meta €805M over Android photo-gallery scanning** — Event date: 2026-11-14 (Saturday announcement).
Italy's data-protection authority fined Meta €805M (~$887M) after finding the Facebook Android app accessed users' photographs, videos and metadata "without obtaining specific consent and without properly informing them" — the gallery-suggestions feature (live since 2023) piggybacked on a single app-wide permission, never clearly disclosing that it scanned device galleries to generate post suggestions. The regulator cited ~20 million affected Italian Android users, an infringement that is "still ongoing," and Meta's "lack of cooperation"; the fine is ~0.4% of Meta's $201B 2025 revenue and the largest the Italian authority has ever imposed. Meta must move to a separate, specific consent prompt with clear signposting and disable instructions, and may appeal.
*Why it matters:* this is the EU's consent doctrine being applied to algorithmic-media ingestion at the device-permission layer — the same pattern AI photo/video features inherit (one permission, silent scanning for "suggestions"). Coming six weeks after New Mexico's $25B Meta verdict, it keeps consent-wash enforcement accelerating while model-specific rules wait on the Digital Fairness Act.
Source: [Reuters — Italy's data protection agency fines Meta €805M (Nov 14, 2026, via globalbankingandfinance mirror)](https://www.globalbankingandfinance.com/italy-fines-meta-887-million-over-android-users-data-access/)
Source: [Rapporteur — Italy fines Meta €805mn over photo privacy (Nov 14, 2026)](https://www.rapporteur.com/news/italy-fines-meta-880mn-over-photo-privacy-authority/)
Source: [Al Jazeera — Italy fines Meta $880m over photo privacy (Nov 14, 2026)](https://www.aljazeera.com/news/2026-11-14/italy-fines-meta-880m-over-photo-privacy)

**Eric Trump-backed humanoid-robot startup Foundation misses payroll; CEO's $4.6M company loan surfaces (also-noted)** — Event date: 2026-11-14 (CNBC, 7:00 AM EST).
CNBC reports Foundation Future Industries (San Francisco; maker of the Phantom-01 military humanoid, advised/backed by Eric Trump) has missed its October 30 payroll and cannot pay staff, while CEO Sankaet Pathak — the company's sole board member — borrowed $4.6M from the firm last year to buy a Sausalito home; the widely claimed $24M in Pentagon contracts actually stems from the 2024 Boardwalk Robotics acquisition, and Pathak's previous fintech (Synapse) collapsed in 2024 with up to $96M in customer funds missing. He is currently raising at a claimed $3B valuation.
*Why it matters:* defense-adjacent AI/humanoid hype meets governance red-flags (sole-member board, related-party loans, contract-inflation optics) amid Senator Warren's ongoing nepotism questions — a credibility test for the "win robotics in America" political pitch.
Source: [CNBC — Eric Trump-backed robot soldier startup can't pay staff after founder borrowed $4.6 million (Nov 14, 2026, 7:00 AM EST)](https://www.cnbc.com/2026-11-14/foundation-payroll-ceo-loan.html)

## Just missed the window

- **WaPo: Rubio built the anti-Huang memo by recasting CIA reporting "in his own more assertive terms"** (Washington Post, published Friday evening Nov 13; surfaced on Techmeme's river 9:30 AM today) — an update on the megaspeed-alice-huang ledger thread; event date is outside this window. [clashreport summary](https://clashreport.com/world/articles/rubio-reportedly-recasts-cia-report-in-memo-on-nvidia-s-huang-l48noxg0yt9) · [Wikipedia: Jensen Huang spying scandal](https://en.wikipedia.org/wiki/Jensen_Huang_spying_scandal)
- **Nathan Lambert, "Open-source AI without Jensen" (Interconnects)** — argues Huang's exit removes the open-weight ecosystem's political-financial protector; Substack dateline Nov 14 but published ~7 PM ET Friday, before window open. [interconnects.ai](https://www.interconnects.ai/p/open-source-ai-without-jensen)
- **basal-omni-2.0-mini (preview)** — Polish/English typed-decision multimodal family on Gemma 4 E4B; HF repo created today 10:55 UTC (5:55 AM EST, ~65 min pre-window), 0.811 on the Werdykt 5,000-decision hidden bench; Apache-2.0. [Hugging Face: Remek/basal-omni-2.0-mini-preview](https://huggingface.co/Remek/basal-omni-2.0-mini-preview)

## Coverage notes

- **Verified in-window items: 4** (1 lead + 1 governance + 1 engine cluster + 1 community/engine also-noted) plus 1 agent-tools early signal (Cowork) and 3 once-only "just missed" footnotes (excluded from the qualifying count).
- **Ledger dedupe: 6 candidates suppressed as already reported** — TechCrunch "Huang departure blasts hole in VC funding" (Nov 13, megaspeed cluster), Tom's Hardware pickup of the V100 lawyer (reported Nov 9), ggml v1.0.0 HN re-spike (footnoted Nov 14 07:00 run), llama.cpp v1.0.0 release itself (reported Nov 13), Anthropic IPO weekend-demand coverage (anthropic-s1-ant thread), Qwen4-Max-preview chatter (reported Nov 11). Additionally time-excluded (outside window, not ledger): AMD "Siren Halo" GDDR7 leak (posted 12:02:40 PM EST — ~3 min after window close), Stand-with-Jensen fake-signature cleanup (03:38 EST), WIRED AI-ransomware boom (Nov 13), Epoch/Trajectory Gemini 4 Argon impersonation reports (Nov 13), DeepEP-Ascend HN post (repo last pushed Nov 3).
- **Dead ends / late finds (not chased):** rayline-ai **ARC-1.0** 0.6B Apache-2.0 agent-router trended on r/LocalLLaMA this morning but its HF repo dates to Nov 10 (BF16 + GGUF, Qwen3.5-0.8B base) — out of window; flagged as a candidate for the next run's ledger if it gains a real announcement. Halcyon/Cynthia-Kaiser ransomware coverage traced to a Nov 13 WIRED piece. Politico's "EU institutions survive Microsoft outage" is dated Nov 13.
- Index health: Techmeme river, HN Algolia, GitHub API, HF API, Reddit anonymous feeds all OK this run. Engines "commits" queries via gh api returned search-shaped payloads on first try; re-fetched clean.
- Window arithmetic done once from system clock (12:01 EST); ledger read fully (1,056 lines).
