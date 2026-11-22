# AI News Briefing — Sunday, November 22, 2026

Window: 2026-11-22 12:00–17:00 ET (5 hours, previous 12:00 fire → this fire). Thin window: one genuinely new in-window community launch, routine engine merges, and no AI security/governance events first disclosed in this window. All items below are deduplicated against the reported ledger.

**TL;DR**
- New community launch: Gall v0.1.0, a from-scratch Metal inference engine for Thinking Machines Lab's Inkling 2 Mini, released today 16:36 ET (Apple Silicon, ~20 tok/s on an M2 Air, OpenAI-compatible local API, 24GB target).
- Local inference engines: llama.cpp, vLLM and SGLang all shipped in-window commits; Ollama quiet.
- Agent tools: no new agent-framework launches first disclosed today.
- AI security/governance: nothing first disclosed in-window (Nov 20–21 security/policy items already ledger-reported).
- ~25 candidates scanned; most in-window HN/community threads traced back to already-reported or pre-window events and were suppressed (see Coverage notes).

## Models and local inference

**Gall v0.1.0 — new community Metal engine for Inkling 2 Mini**
Event date: 2026-11-22 (release 21:36 UTC / 16:36 ET). Community project `ltrgoddard/gall`, a from-scratch Rust+Metal inference engine that runs Inkling 2 Mini (26.4B params, 3.4B active MoE, 1M context, Apache 2.0) on an Apple Silicon Mac with 24 GB of memory. It serves the model as an OpenAI-compatible API locally (`http://127.0.0.1:9940/v1`), supports text/images/tool calls/structured JSON, unloads the model ~1s after each answer and persists conversation state to disk to restart without re-processing the full prompt. Measured ~20 tok/s on an M2 MacBook Air, reading the Unsloth UD-IQ3_XXS GGUF in place (nothing is bundled with ggml/MLX). A separate community guide today walks through Unsloth Desktop setup for the same model on consumer GPUs.
*Why it matters:* it is the first in-window community engineering of the Inkling 2 Mini stack — an unofficial, from-scratch local-inference path rather than the official Unsloth/GGUF route, with a genuinely distinct architecture (Metal kernels, no ggml). Not vendor-backed; no release assets are notarized and the engine has no 0.1.0-era independent validation.
Source: https://github.com/ltrgoddard/gall (v0.1.0, 2026-11-22T21:36Z) — README: https://github.com/ltrgoddard/gall — Show HN: https://news.ycombinator.com/item?id=50604195

**Local-inference engine sweep (also-noted)**
Event date: 2026-11-22 (window). Routine merges, not lead stories:
- llama.cpp: 7 commits in-window (17:36–21:04 UTC; 13:36–17:04 ET): mtmd shared audio FFT speedup (#32815), qwen3a audio tail fix (#32697), host-weight copy for several nodes (#32807), `ignore_merges` for inkling2 (#32799), server `grep_search` offset pagination (#32792), tokenizer flags note (#32819), token-aware chat parsing (#29307). b12631/b12632/b12633 were released before this window (earlier today) — not counted as new.
- vLLM: 2 merges in-window: Kimi-K3.1-Pro recipe (MI355X image + released DSpark draft, aligned with InferenceX) and Nemotron Shortcut (text/MTP/vision-language, #68198).
- SGLang: 5 in-window diffusion merges: MiniMax-H3 fl2va/ref2va single-server serving (#52023), cuDNN vision-tower warm-up (#52225), platform fake for MiniMax-H3 config (#51734), VSA-H3 no-recompile + scaled-residual-add perf (#52224), CUDA partition-plan CI (#52216).
- Ollama: quiet — no in-window commits.
Source: https://github.com/ggml-org/llama.cpp (commits since 2026-11-22T17:00Z) — https://github.com/vllm-project/vllm — https://github.com/sgl-project/sglang

**Beginner's local-AI guide (community, also-noted)**
Event date: 2026-11-22. GPU Partner published a beginner guide to running a personal ChatGPT locally with Unsloth Desktop — Qwen3.8-27B UD-Q4_K_XL at 128K context on an RTX 5090 (32 GB), Qwen3.5-9B/Gemma 4 12B on 8–12 GB cards, Inkling 2 Mini (UD-Q4_K_XL ≈ 17.6 GB) fitting 24–32 GB, and Inkling 2 Small (594B; 171.7 GB smallest file) for unified-memory machines. LAN access via Unsloth's Remote & LAN settings. Self-published/vendor-unverified (Unsloth, Qwen and Inkling 2 figures as reported in the guide).
Source: https://www.gpupartner.com/blog/beginners-guide-local-ai-inkling-qwen-unsloth (Nov 22, 2026)

## Agent tools

No new agent-framework launch or release first disclosed in this window. Two in-window HN threads were checked and dismissed as pre-window events (see Coverage notes): TheGitAI CLI/Desktop Agent (thegit.ai) and OpenAgentCore (MiniMax-AI fork of the OpenAI Agents API, created September, pushed Nov 19). Taskless v0.13.8 (Nov 20) and Strict (v1.0.4) are either pre-window or unrelated tooling.

## AI security and governance

No AI security or governance events first disclosed in this window. All Nov 20–21 in-window-adjacent security/policy stories (AI Safety Caucus, Signal screenshot blocking, China-linked Cairn agents / MONAI, Capsule MCP supply-chain, Bell Canada intrusion, METR time-horizon report, OpenAI/Anthropic sandbox-escape briefing, Nvidia SEC probe, CrowdStrike-Zscaler talks, ANT debut) were already covered by the previous runs and remain in the reported ledger.

## Just missed the window
None newly footnoted this run (see Coverage notes for candidates that failed the event-date rule, e.g. the TechCrunch honey-trap analysis, MIT Processor Database launch-story, WSJ Anthropic-siblings story — all pre-window or unverifiable).

## Coverage notes
- Verified items: **3** (Gall/v0.1.0; engine sweep; GPU Partner guide), all community or routine — thin window by design; no vendor releases today.
- Ledger dedupe: 25+ candidates scanned — suppressed as already reported/missed: Praxis-1 robotics WAM (Nov 11; ledger-missed), Anthropic honey-trap analysis (Nov 20), China-linked Cairn sandbox escapes (Nov 20–21), Signal screenshot-block (Nov 20), METR time-horizon (Nov 20), OpenAI/Anthropic sandbox-escape caucus (Nov 20), Signal/OpenAI honey-trap (Nov 20), MIT Processor Database (Nov 17), Taskless v0.13.8 (Nov 20), Gall repo creation (Nov 21 — the v0.1.0 release is the in-window event), MIT Processor Database, OpenAgentCore (Nov 19 push), Beginner's-guide/Gall/MIT/Honey-trap/WSJ all traced to pre-window or self-published sources.
- Dead ends/limitation: WSJ "Brother-Sister Duo" article (HN in-window, links to WSJ) could not be fetched (paywall auth) and was not counted. Reddit anonymous lane 404'd — no community threads read via Reddit this run. HN in-window threads (honey-trap, Etsy AI policy, MIT Processor Database, OpenAgentCore) all traced to pre-window events or non-AI topics and were excluded.
- Indexes used: HN Algolia date-filtered search (47 hits in-window), GitHub commit feeds for llama.cpp/vLLM/SGLang/Ollama, web searches.
