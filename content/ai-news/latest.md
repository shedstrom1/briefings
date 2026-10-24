# AI News Briefing — Saturday, October 24, 2026 (12:00–17:00 ET)

**Window:** 2026-10-24 12:00 → 17:00 America/New_York (previous fire 12:00; this 17:00 run). New-items-only, ledger-deduped.

## TL;DR

- **Ollama v0.40.11 went GA** this afternoon — the release adds on-device speech generation on macOS (voice replies during `ollama run`, plus a streaming `/v1/audio/speech` endpoint) and ships the d3 decision-model family (2B/4B/9B/27B) from vLLM Semantic Router into Ollama.
- **The Information (3:45 PM ET):** Replit moved most of its AI spend to OpenAI in a deal worth **$100M+/year at 30%+ off**, after Anthropic discontinued its discount — an update on this month's provider-switch story.
- **Local-inference engines kept shipping:** llama.cpp put out seven builds (b11857–b11868, 14:20–16:23 ET) and vLLM merged four PRs in-window, including adaptive verification for batch-sharded speculative sampling.
- **Just missed the window:** Google's Agent Compute Engine launch (announced Oct 22 at AGNTCon+MCPCon, HN thread this afternoon) and Pennsylvania's AI ad-labeling law (signed Thursday).

## Models and local inference

**Ollama v0.40.11 hits GA — speech generation and d3 decision models land on desktop**
*Event date: Saturday, Oct 24 — GA binaries published 17:27 UTC (1:27 PM ET).*
The stable v0.40.11 build went live this afternoon (asset timestamps 2026-10-24T17:27:58Z), completing the v0.40.11-rc0 line reported this morning. Two features define it: **speech** — Ollama can now generate audio on macOS, so `ollama run phonon "Hello"` plays text or a chat model's reply aloud as it is generated, and apps can stream audio through an OpenAI-compatible `/v1/audio/speech` endpoint; and **d3 decision models** — the vLLM Semantic Router decision-model family (2B, 4B, 9B, 27B) arrives natively, letting a local model classify text, answer yes/no questions, or score options with calibrated probabilities via `--ask`.
*Why it matters:* This turns Ollama into a full local voice + routing stack — TTS and cheap structured-decision inference without any cloud call.
*Sources:*
- Release notes — Ollama GitHub Releases, v0.40.11 (assets 2026-10-24T17:27 UTC): https://github.com/ollama/ollama/releases
- Prior edition coverage of the RC this morning (Ollama v0.40.11-rc0: Phonon voice catalog, vllm-d3 fix).

**llama.cpp ships seven builds this afternoon (b11857–b11868)**
*Event date: Saturday, Oct 24, builds tagged 18:20–20:23 UTC (2:20–4:23 PM ET).*
Highlights: MUL_MAT_ID now accepts a `-1` expert index to skip computation (#26631, all backends incl. Vulkan/WebGPU); `-sm tensor` is denied on arches that build MoE amax scaling graphs, preventing silently wrong split-state results (#31081); an RDMA `rdma_recv` bounds check (#30399); Metal drops the legacy `mul_mm_id` amax rescale now handled in the compute graph (#30924); a server entrypoint refactor introducing `server_role` (router/single/download modes, #31113); and Hexagon flash attention gains a KV-head team split for multicore NPU inference (#31055) — directly relevant to Snapdragon/Hexagon laptops running MoE models with few KV heads.
*Why it matters:* The Hexagon head-split fix and the MoE split-state correctness guard both improve quality of small-MoE serving on non-NVIDIA hardware; server_role cleanup lands groundwork for router-mode deployments. (The MiniMax-M3 NVFP4 scale-forwarding fix, b11861/#31097, was reported this morning and is not re-listed.)
*Sources:*
- Releases — ggml-org/llama.cpp GitHub, b11857–b11868 (tags 2026-10-24 18:20–20:23 UTC): https://github.com/ggml-org/llama.cpp/releases

**vLLM merges four PRs in-window, led by speculative-sampling verification**
*Event date: Saturday, Oct 24, merged 16:22–20:35 UTC (12:22–4:35 PM ET).*
The most notable merge: **[Sampling] adaptive verification support for batch-sharded speculative sampling** (#58466, merged 4:35 PM ET), tagged for Model Runner V2. Also merged: a GLM-5.3 perf change skipping scale calculation in `concat_and_cache_mla` for a claimed 1.2–3.0% end-to-end TPOT improvement, a `profile_prefix` payload on the HTTP `/start_profile` endpoint, and a FlashInfer bump to 0.7.2.post1.
*Why it matters:* Adaptive verification tightens vLLM's spec-decode pipeline (verifying draft tokens more cheaply across sharded batches), and the FlashInfer bump propagates upstream kernel fixes into default installs.
*Sources:*
- Merged-PR search — vllm-project/vllm GitHub (merged:2026-10-24T16:00Z–21:00Z, 4 results): https://github.com/vllm-project/vllm/pull/58466

## Agent tools

**Update on previously reported (ledger Oct 18, "Replit makes OpenAI its default over Anthropic"): The Information puts numbers on the switch — $100M+/year at 30%+ off**
*Event date: Saturday, Oct 24, published 3:45 PM ET.*
The Information reported that Replit recently moved most of its AI spend from Anthropic to OpenAI, which gave **more than 30% off in a deal worth over $100M per year**, after Anthropic discontinued its discount. The piece ("Anthropic's Margin is OpenAI's Opportunity") frames it as part of a broader pattern of OpenAI undercutting Anthropic to win coding-platform customers, with companies like DataCamp and Rippling shifting spending. This materially extends the Oct 18 item, which established the switch (default model swap) but not the deal's economics.
*Why it matters:* The 30%+ discount rate and nine-figure annual size show how price — not just capability — is reshaping agent-platform provider choice, and that Anthropic's tighter discount policy is actively bleeding customers.
*Sources:*
- The Information, "Anthropic's Margin is OpenAI's Opportunity" (Techmeme river listing 3:45 PM ET): https://www.theinformation.com/articles/anthropics-margin-openais-opportunity (paywalled; summary via Techmeme card)
- Techmeme river, Oct 24 3:45 PM ET: https://www.techmeme.com/river
- Background: TNW, "Anthropic ends discounts once tokens run out" (Sept 28): https://thenextweb.com/news/anthropic-openai-enterprise-discounts-tokens-the-information

## AI security and governance

No new in-window disclosures this afternoon. The php2shell storyline was quiet since the overnight Barron's/SoftBank updates (all reported in earlier editions), and Techmeme's afternoon carried no new AI-governance events.

## Also noted

- Nothing else cleared the verification bar for this five-hour Saturday window; AI model-release trackers recorded **zero new tracked model releases on Oct 24**.

## Just missed the window

- **Google announces Agent Compute Engine + expanded agent governance** (announced Thursday, Oct 22 at AGNTCon+MCPCon; HN submission today 4:44 PM ET): purpose-built microVM compute (hardware-isolated Cloud Hypervisor on Titanium IPU, copy-on-write memory sharing, fast checkpoint/restore, per-microVM Hyperdisk) that already powers Gemini agent sessions, preview "in the coming months"; plus open-source **Agent Substrate** on GKE (claimed 10x density, 200k-agent benchmark at 500 activations/sec under 500 ms suspend/resume, works with Claude Code, OpenClaw, Hermes) and Agent Gateway governance (token/cost governance, model routing). First time surfaced in this brief. https://cloud.google.com/blog/topics/ai-infrastructure/introducing-agent-compute-engine-and-expanded-agent-governance
- **Pennsylvania's AI Deceptive Advertising Act signed by Gov. Shapiro on Thursday, Oct 22** (The Verge surfaced it today): ads significantly modified or generated by AI must carry a label in the same format as the ad, regardless of where the ad was created; parody/satire/fiction exempted. https://www.theverge.com/ai-artificial-intelligence/1018187/pennsylvania-ai-deceptive-advertising-act

## Coverage notes

- **Verified this run:** 4 items (1 lead GA release, 1 lead market update, 2 engine-tracker items) + 2 "Just missed the window" footnotes, backed by 13 fetched source pages (HN Algolia API, Techmeme home + river, aireleasetracker, Google Cloud blog, The Decoder ×2, AIBriefs, GitHub releases ×3, GitHub merged-PR API ×2, TNW, The Information [paywall — relied on Techmeme's verbatim card]).
- **Ledger dedupe:** ~8 candidates suppressed as already reported or re-covered: Block Dessa (footnoted at 12:00 today), Kimi K3.1 Pro + Artificial Analysis ranking (footnoted at 07:00 today), OpenAI offline-world/internet-restriction and CPU-cache covert-channel details (reported 07:00 today as securing-frontier-training), Sam Altman sandbox-escape interview (The Decoder, Oct 23), Google Personal Intelligence ads policy (Oct 23 coverage of Oct 16 policy), arXiv submission rate cap (Oct 1–2), Andy Jassy AWS "sold out through 2027" (reported 07:00 today). Repeat "Just missed" footnotes were not re-listed (Block Dessa, Cursor Computer Use, Hy-Nomas, TypeSafe leak, Kimi K3.1, AWS Infinity, Mythos-5 attribution).
- **Out-of-window skips:** Utopai $60M seed (Fri 7:10 PM ET river), Mave $28M Series A (Fri 5:15 PM ET), Ciena CEO WSJ interview (11:40 AM ET, 20 min early), Instacart sale talks (non-AI business), NYT Alpha School piece (education, out of scope).
- **Limitations:** The Information lead item is paywalled — deal figures come from Techmeme's verbatim card plus corroborating outlets; no new model releases were disclosed today per release trackers; Saturday afternoon is a structurally quiet news block.
