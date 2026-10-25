# AI News Brief — Oct 25 noon: NVIDIA's looped Dory 4B lands in vLLM; llama.cpp ships quant fixes

**Window: 2026-10-25 07:00 → 12:00 EDT** (this job's 12:00 run; new items only, ledger-deduped)

## TL;DR
- **vLLM merged official support for NVIDIA's Nemotron-Labs Dory1.0-4B** — the 4B "looped" (latent-recurrent) open-weight model whose shared layer block can be run 1–4 times to buy accuracy with inference compute. Merged 07:13 EDT this morning.
- **llama.cpp shipped six builds in-window (b11873–b11878)** with a llama-quant 4D-tensor quantization fix, a dry-sequence-breaker CPU/memory exhaustion fix, and a grammar stack-overflow guard.
- **Quiet Sunday:** release trackers showed **zero new model releases from major labs** in the window ("no new models released today yet" as of ~12:00 EDT).
- **Just-missed:** Redwood's "Simulated Control Evaluations" (Saturday), OpenClaw 2026.10.7 with Gemini 4 Argon support (Saturday), the Elvis estate's suit against Anthropic (filed Friday), and AP's US-vs-China AI-blocs story (early this morning) — footnoted below.

## Models and local inference

**vLLM adds NVIDIA Dory1.0-4B support — looping goes local.** *Event: merged today 07:13 EDT.*
vLLM merged PR #60149 "[Model] Add Dory support," implementing the architecture behind NVIDIA-Nemotron-Labs-Dory1.0-4B — a 4-billion-parameter looped (latent-recurrent) LLM from NVIDIA's Nemotron Labs. It uses a normalized-GPT design with interleaved hybrid sliding-window and full attention, and repeatedly applies a shared block of layers during inference: loop count 1, 2, or 4 trades extra compute per token for accuracy from a single checkpoint, no retraining. The model itself is not new (released Oct 16–17, with GGUF checkpoints on Hugging Face since Oct 17; pre-trained in NVFP4, 256K context per its cards) — the in-window event is first-class serving support. Also merged in-window: daemon-to-daemon warmup cache seeding for fast start (#63088), four KV-offload fixes (#61390, #63010, #63126, and NIXL telemetry on the OBJ tier #55576), and release of cached cuBLAS workspaces after daemon warmup (#63451).
**Local inference note:** Dory1.0-4B GGUFs (Q4-class ≈ 2–3 GB) already exist on the Hub; the loop knob effectively adds a quality tier for 8–16 GB cards at the cost of throughput.
Sources:
- vLLM PR #60149 (merged 2026-10-25 07:13 EDT) — https://github.com/vllm-project/vllm/pull/60149
- NVIDIA model card — https://huggingface.co/nvidia/NVIDIA-Nemotron-Labs-Dory1.0-4B-BF16 (GGUF: https://huggingface.co/nvidia/NVIDIA-Nemotron-Labs-Dory1.0-4B-GGUF)

**llama.cpp b11873–b11878: quant and crash fixes for the MoE-quant era.** *Event: builds published 08:24–11:16 EDT.*
Six builds landed in the window (first reported since b11871 at the 07:00 run). The substantive merges: **llama-quant now handles 4D tensors correctly** via `ggml_nrows()` and name-based expert detection (#30820) — directly relevant to quantizing modern MoE checkpoints whose expert weights are 4D; a **dry-sequence-breaker leak fixed** that could exhaust CPU and memory (#31035); a **grammar recursion-depth limit** closing a stack-overflow crash class (#29755, fixing long-open #29495); and Qwen checkpoint conversion now merges tensors (#31028), plus a WebGPU CI test-gating fix.
**Why it matters:** #30820 fixes silently-wrong output for the class of GGUFs the community has been racing to ship this month (DeepSeek-V4.1-Flash, MiniMax-M3.1-Flash era quants), and the grammar guard removes a server-killing crash reachable through constrained decoding.
Sources:
- llama.cpp releases (b11873 08:24 EDT … b11878 11:16 EDT) — https://github.com/ggml-org/llama.cpp/releases

## Agent tools
No new verified releases inside the 07:00–12:00 EDT window. (OpenClaw v2026.10.7 landed just before it — see footnotes.)

## AI security and governance
No new verified disclosures inside the window. Sunday morning brought no CISA KEV additions and no lab safety postings; Saturday's items are footnoted below.

## Just missed the window
- **Redwood Research published "Simulated Control Evaluations"** (Oct 24): continue real agent transcripts with an attacker model pursuing a harmful objective while its tool calls are simulated, to test whether deployment monitors would actually catch a deliberately misaligned agent — a practical eval recipe as labs roll out agent monitors. (redwoodresearch.org/blog/simulated-control-evaluations)
- **OpenClaw v2026.10.7 shipped Saturday ~05:53 EDT** — replay-safe session recovery, safer update activation/recovery, and **Gemini 4 Argon support** added. (github.com/openclaw/openclaw/releases/tag/v2026.10.7)
- **Elvis Presley's catalog owner Authentic Brands Group sued Anthropic on Friday** over 274 copyrighted Presley songs, seeking $150K per work (~$41.5M); The A.V. Club's fresh coverage (10:14 EDT today) quotes Claude admitting its "renders" keep "landing very close to the originals." Filed Friday, so footnoted. (avclub.com/anthropic-sued-copyright-infringement-elvis, via Rolling Stone)
- **AP (published early this morning, before 07:00 EDT):** the State Department warned nations that substantively engaging with China-backed WAICO (~28 founding members) "cannot be trusted partners" in US-led Pax Silica — formalizing AI-bloc pressure on allies. (AP; fetchable AP URL not resolved within budget)

## Coverage notes
- **Verified:** 6 events — 2 in-window items (confirmed via GitHub API merge/publish timestamps) and 4 footnotes (fetched pages or primary feeds). Lead stories read in full (PR #60149 body, OpenClaw release notes, Redwood blog listing, A.V. Club article).
- **Ledger dedupe:** 2 candidates suppressed as already reported — Axios "AI leaders a political liability" (reported today 07:00 run) and the Replit→OpenAI $100M+/yr deal (reported Oct 24).
- **Dead ends:** r/LocalLLaMA blocked with a 403 captcha; theinjection.dev unreachable (private-network block); AP AI-blocs article URL could not be resolved to a fetchable copy; the Grok/GPT-6.1 Sol forced-choice political study (TechCrunch, ~midnight EDT) was excluded — publication time unverifiable inside budget.
- **Indexes:** Techmeme river (sparse Sunday), HN Algolia (no qualifying AI stories ≥20 pts), Hugging Face Daily Papers (no Sunday edition; latest Oct 23), CISA KEV (no weekend updates). Model-release tracker lmmarketcap confirmed no new models today as of ~12:00 EDT.
