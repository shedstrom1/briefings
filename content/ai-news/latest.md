# AI News Briefing — Saturday, October 31, 2026, 17:00 ET

**Window:** 2026-10-31 12:00 → 17:00 EDT (previous fire → this fire). New items only; ledger dedupe applied.

## TL;DR
- Meta built a ~106-pound humanoid robot prototype codenamed "Max" with Chinese startup Fourier in roughly three months and is already building a second version, The Information reported Saturday at 12:20 PM ET; Reuters relayed it same-day without independent verification.
- vLLM landed 7 in-window merges, headlined by computing prompt block hashes in the Rust frontend — removing GIL-held hashing from the engine input thread and yielding +9.6% to +39% decode throughput on a long-cached-prompt workload.
- llama.cpp's release train resumed in-window (b12021–b12023, b12023 published 3:18 PM ET) with a decision-model shared-prefix fix, /v1/responses extra_params support, and a Gemma 4 image-resize change.
- HN front page carried user reports that Anthropic blocks accounts for prompts "touching training larger models" — unconfirmed, but resonating with distillation-crackdown coverage from earlier this week.

## Models and local inference

**Meta built humanoid prototype "Max" with China's Fourier in three months** — event date: October 31, 2026 (The Information, 12:20 PM ET)
Meta Platforms built a prototype humanoid robot with Chinese robotics company Fourier and is already working on a second version, per people familiar with the initiative. The roughly 106-pound (≈48 kg) humanoid, internally codenamed Max, was assembled in just three months; Meta says it has no plans to sell these units. Reuters relayed the report at 15:0x ET the same day and said it could not immediately verify it.
*Why it matters:* This is the clearest signal yet that Meta's robotics push (alongside its Muse agent work) is moving from research to hardware with a Chinese supply-chain partner — Fourier is the maker of the GR-series humanoids. A three-month build cycle suggests Meta is iterating on platform capability, not product; watch for a home-assistant tie-in to Muse and for the China-partnership angle to draw Washington attention. Sources do not tie the program to local inference hardware.
- Source: The Information — "Meta Built Humanoid Prototype With Chinese Startup To Ramp Up Robotics Push" — Oct 31, 2026, 12:20 PM ET — https://www.theinformation.com/articles/meta-built-humanoid-prototype-chinese-startup-ramp-robotics-push
- Source: Reuters — "Meta builds humanoid robot with Chinese startup Fourier, The Information says" — Oct 31, 2026 (afternoon EDT) — https://www.reuters.com/world/china/meta-builds-humanoid-robot-with-chinese-startup-fourier-information-says-2026-10-31/
- Source: Techmeme river entry — Oct 31, 2026, 12:20 PM ET — https://www.techmeme.com/river

**llama.cpp b12021–b12023 ship in-window; decision-prefix cap fixes shared-option batching** — event date: October 31, 2026 (b12023 published 3:18 PM ET)
After a quiet morning (b12020 landed 4:07 AM ET, before this window), the release train resumed: b12023 published at 19:18 UTC with in-window merges including server: cap shared decision prefixes at the first output (#30710, fixes #30482 — decision output embeddings were being lost when questions share option tokens), server: /v1/responses now accepts extra_params (#30959), and mtmd: resize Gemma 4 images without letterbox padding (#30922). A follow-up reshaping multimodal input to 2D for LFM single-channel (#30831) merged 7:34 PM ET, queued for the next build.
*Why it matters:* The #30710 fix matters for anyone running decision/classification models (d-series style) through llama-server with shared option tokens — wrong-batch embedding reuse could silently degrade multi-question decisions. The /v1/responses extra_params change closes an OpenAI Responses-API compatibility gap.
- Source: GitHub — ggml-org/llama.cpp releases, tag b12023 (published_at 2026-10-31T19:18:38Z) — https://github.com/ggml-org/llama.cpp/releases/tag/b12023

**vLLM merges 7 in-window PRs; Rust-frontend prompt hashing cuts engine-loop stalls** — event date: October 31, 2026 (merges 12:00–4:30 PM ET)
Seven PRs merged inside the window (16:00–21:05 UTC). Standout: "[Perf][Frontend] Compute prompt block hashes in the Rust frontend" (#63842, merged 3:38 PM ET) moves prompt block-hash computation out of the engine's input thread, eliminating GIL-held hashing (~50 ms per 227K-token prompt with sha256, ~150 ms with sha256_cbor) — the PR reports +9.6% (sha256) and +39% (sha256_cbor) decode throughput on a GB300 workload admitting long cached prompts during decode, with byte-identical prefix-cache hashes. Also in-window: honoring moe/attention backend overrides for MRV2 standalone draft models (#64751), a single-launch fused scoring+top-k kernel for DeepSeek-V4 sparse-indexer prefill without materializing logits (#60618, ROCm/DSv4-labeled), reduced peak memory for chunked prompt logprobs in Model Runner V2 (#64841), whitespace preservation in HYV4 tool-argument names (#61542), redundant weight-tensor clone removal (#63174), and prompt_tokens_details in batched chat completions (#64757).
*Why it matters:* the Rust-frontend hashing move is part of vLLM's long-context prefill/decode CPU-overhead program and directly attacks a tail-latency stall that grows with prompt length. The MRV2 draft-override fix closes a silent misconfiguration trap for speculative decoding.
- Source: GitHub — vllm-project/vllm merged PRs (search: merged 2026-10-31T16:00Z..21:05Z) — https://github.com/vllm-project/vllm/pull/63842

## Agent tools

**"Evolving Agent Teams" posted to HN** — event date: October 31, 2026 (15:46 ET)
An arXiv paper on evolving agent teams (2610.36848) was submitted to Hacker News inside the window. Thin engagement (1 point) so far; noted as a research lead, not yet read in depth.
- Source: Hacker News (item id 50319413) — Oct 31, 2026, 19:46 UTC — https://news.ycombinator.com/item?id=50319413

## AI security and governance

**Users report Anthropic blocking accounts over "training larger models" prompts** — event date: October 31, 2026 (HN front page, 15:39 ET)
A front-page HN thread relaying an X post from user jiahaoshao1 alleges Anthropic's automated systems block or flag accounts for "anything touching training larger models." Comments in the thread characterize it as a months-long pattern, and one commenter alleges deliberate answer-botching on advanced training questions instead of blocking. All user-reported; no Anthropic confirmation and no dated official artifact. Included only as a community-signal item; treat as unverified.
- Source: Hacker News (item id 50319358, 24 points) — Oct 31, 2026, 19:39 UTC — https://news.ycombinator.com/item?id=50319358

## Just missed the window
- **ChatGPT shared-link P1 RCE disclosed by Parameter (The Hacker News coverage 3:01 PM ET today; underlying disclosure Oct 28).** A malicious ChatGPT share link could run attacker code in the victim's logged-in session via an unauthenticated MathLive postMessage listener plus a sandboxed-Preview→main-page HTML handoff, with a YouTube-hosted script slipping through the CSP; Parameter read private chats from another user's session, OpenAI escalated P2→P1 and fixed both web frontends by Oct 26, and paid $5,000. The disclosure writeup (Oct 28) anchors outside the window, so this appears once as a footnote only. (Source: The Hacker News, Oct 31, 2026 — https://thehackernews.com/2026/10/a-shared-chatgpt-link-could-run.html)
- **OpenAI will train on personal-account Daybreak Blue sessions from Nov 29, even with the training toggle off.** User emails and the updated data-controls help page surfaced Oct 30 (before this window), stating OpenAI "may use content from Daybreak Blue sessions on personal accounts to improve its models," citing cyber-safeguard goals; business/enterprise accounts are excluded. Footnote only. (Source: OpenAI community thread + help-page excerpts, Oct 30–31 — https://community.openai.com/t/daybreak-blue-data-use-to-improve-models/1411182)

## Coverage notes
- **Verified count:** 5 qualifying in-window events (Meta/Fourier humanoid; llama.cpp b12021–b12023; vLLM 7-PR cluster; HN Anthropic account-block reports; "Evolving Agent Teams" HN post), each with a fetched in-window source; Meta/Fourier carries a second independent outlet (Reuters). Plus 2 "Just missed the window" footnotes (first and only time for each).
- **Ledger dedupe:** ~9 candidates suppressed as already reported — Altman DRAM quote, CoreWeave/Nebius/Nscale bond shutout, S&P Meta negative outlook, Intel Friday close wrap, GPT-6.1 Terra HN re-spike, Anthropic IPO-filing video re-spike, VOLTA infostealer (reported 12:00 run), DeepSeek dsh-superagent (already footnoted), four-lab robots.txt opt-out (already footnoted).
- **Out-of-window drops:** Anthropic "Scaling Misuse Detection with Self-Assessment" (original post Oct 16; today's HN spike only), Bloomberg Google–DoD Golden Dome data-security talks (Oct 30, 6:50 PM ET).
- **Unverifiable, dropped:** Perplexity's WANDR state-of-the-art claim for Gemini 4 Carbon (no published scores), Musk's Colossus 2 "1.2M Blackwells by year-end" projection (self-reported, date unverifiable), Katelyn Lesse's "Rust port cut Messages API CPU 40–50%" interview claim (no published report).
- **Indices:** Techmeme river and HN Algolia both healthy (first HN query used a wrong epoch and returned zero; corrected on retry). No new closed- or open-weight model releases surfaced in the 5-hour Saturday window; Ollama unchanged since v0.40.13 per the 12:00 sweep (not re-checked this run).
