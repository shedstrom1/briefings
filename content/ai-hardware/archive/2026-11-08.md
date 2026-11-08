# AI Hardware & Local Models Brief — Nov 8, 2026 07:45 ET

**Window:** Saturday, November 7, 2026 17:45 ET → Sunday, November 8, 2026 07:45 ET (14h; overnight merge window + early morning)

**TL;DR**
- A dual-RTX PRO 6000 Blackwell workstation owner found both native 12V-2×6 cables on a Seasonic PRIME TX-1600 Noctua Edition PSU with cracked, peeling sleeving — damage at both GPU and PSU ends, intermittent PCIe drops, no melting.
- vLLM merged the removal of Model Runner V1 (#63373) overnight — the legacy execution path is gone — plus a Kimi-K3.1-Pro MI355X concurrency-1 serving preset.
- llama.cpp shipped an overnight wave (#31831–#31861): OpenCL q8_0/MoE spec-decode verification, Hexagon thread cleanup, ggml CPU F32 switch, and UTF-8 sanitization for PEG tool-call args.
- SGLang merged a Kimi-K3.1 TensorRT-LLM DSA backend running under decode context parallelism (#49106) and a scheduler PauseState refactor.
- A Mac Studio MLX researcher published 35 domain-expert LoRAs for Qwen3.6-35B-A3B — with the honest negative result that the base model beat most of them.

## AI hardware and silicon

**Damaged Seasonic 12V-2×6 cables nearly destroyed a $32,000 dual RTX PRO 6000 workstation** — Event date: Nov 8, 2026 (reported 06:47 ET).
A Redditor running two RTX PRO 6000 Blackwell GPUs (600W Workstation Edition, 96GB GDDR7 each, limited to 500W) discovered deteriorating braided sleeving on both native 12V-2×6 cables of a Seasonic PRIME TX-1600 Noctua Edition PSU — damage near the connectors at both the GPU and PSU ends, with no visible melting or discoloration. The system had developed intermittent PCIe issues (one GPU or NVMe drive occasionally dropping from the bus), which prompted repeated cable reconnections; the PSU-side connectors, rarely touched, showed even worse deterioration on the same two wires. The owner is replacing them with Corsair thermal-protection cables and considering a 450W limit, noting that limiting the cards to 300W cut workload performance by roughly 40–50%.
**Why it matters:** High-wattage Blackwell workstation cards stress 16-pin power delivery paths continuously; this is a physical-layer warning for anyone running multi-GPU inference boxes above ~500W per card, regardless of PSU tier.
**Local inference:** Dual RTX PRO 6000 Blackwell 96GB — data-center-class local inference box; applies to any 12V-2×6-fed 600W-class GPU build.
Source: VideoCardz — "User spots damaged Seasonic Noctua power cables before losing two $16,000 RTX PRO 6000 GPUs," published Nov 8, 2026 11:47 GMT (06:47 ET), https://videocardz.com/newz/user-spots-damaged-seasonic-noctua-power-cables-before-losing-two-16000-rtx-pro-6000-gpus ; original post r/PcBuild by u/Doomslayer606.

## Model releases

No open-weight releases, license changes, or major model updates verified within this 14-hour overnight window. (The in-window community discussion of Apex Intelligence's Apex-35B-V1.0 concerns weights that landed Oct 28 — see "Just missed the window.")

## Community spins and quantizations

**35 domain-expert LoRAs on Qwen3.6-35B-A3B, trained with MLX on one Mac Studio — negative results published** — Event date: Nov 8, 2026 (posted to r/LocalLLaMA).
u/Holiday_Poetry_5133 trained 35 domain-expert LoRA adapters on Qwen3.6-35B-A3B (native MoE, 256 experts, 3B active) with MLX on a Mac Studio M3 Ultra 512GB, BF16, no quantization during training. Everything is Apache-2.0 and public: adapters, datasets, per-domain configs, a small MiniLM router, and training scripts (github.com/hypneum-lab/micro-kiki; HF org hypneum-lab; training stack ailiance/ailiance-mac-tuner, archived). The audit is damning for the adapter strategy: on an independent bench the base 35B model beat most specialist adapters (only HTML/CSS clearly gained); the MiniLM router tops out around top-1 46% / top-3 70%. Fusing a MoE LoRA into 8-bit weights produced garbage — only runtime BF16 adapter hot-swap (~2s per swap, one base loaded once) was usable. Side benefit: an upstream MLX docs fix for `mx.random.normal` bit-exactness across Apple GPU generations (M1 vs M3+).
**Why it matters:** At 30B-class MoE scale, domain LoRAs do not reliably beat the base model; for large unified-memory Macs the practical pattern is one BF16 base + runtime adapter hot-swap, not fused specialist weights.
**Local inference:** Mac Studio M3 Ultra, 512GB unified memory — BF16 base model + BF16 adapter pool served via MLX.
Source: Reddit r/LocalLLaMA — "35 domain-expert LoRAs on a MoE (Qwen3.6-35B-A3B) trained with MLX on one Mac Studio — what worked, what didn't, all Apache-2.0" (posted Nov 8, 2026), https://www.reddit.com/r/LocalLLaMA/comments/1xqw6fr/35_domainexpert_loras_on_a_moe_qwen3635ba3b/

**"Ram Altman" is now a Linux script that emails Sam Altman daily about your unused RAM** — Event date: Nov 8, 2026 (update on previously reported satire).
The "donate idle RAM" satire reported yesterday (uploadyourram) has spawned a derivative: u/Subulicious reports a Linux script (linked site: ram-altman.lol) that emails Sam Altman every day with a report of how much RAM is sitting unused on the poster's PC. Community reaction is uniformly amused ("OpenAI now training on automated emails sent to the CEO by a script").
**Why it matters:** Cultural footnote, not infrastructure — but it shows the local-compute-vs-OpenAI satire is now a recurring meme in local-LLM circles.
**Local inference:** Runs on any Linux box; targets idle RAM on local inference hosts.
Source: Reddit r/LocalLLaMA — ""Ram Altman" Is Now a Linux Script That Emails Sam Altman Daily to Report How Much Unused RAM Is in Your PC" (posted Nov 8, 2026), https://www.reddit.com/r/LocalLLaMA/comments/1xqwn5n/ram_altman_is_now_a_linux_script_that_emails_sam/

## Inference software and benchmarks

**vLLM: Model Runner V1 removed; Kimi-K3.1-Pro MI355X preset lands** — Event date: Nov 7–8, 2026 (overnight merges 22:50Z → 07:45Z).
The long-rumored #63373 ("Remove Model Runner V1") merged at 22:50Z — the legacy model-runner execution path is deleted, consolidating vLLM onto the V2 architecture. The wave also included #66343 (Kimi-K3.1-Pro concurrency-1 serving preset for MI355X, aligned with InferenceX#4887), #66329/#66328 (ROCm CI and UMBP runtime fixes), #66190 (Qwen2.5-Omni video encoder budget sized from processor video limits), and #66358 (CI ruff repair after the dots_ocr test change).
**Why it matters:** Removing Runner V1 eliminates a dual-path maintenance burden and confirms V2 is the only execution model for new features going forward.
**Local inference:** All vLLM deployments (CPU/CUDA/ROCm); the MI355X preset targets AMD data-center GPUs rather than consumer cards.
Source: GitHub vLLM merged PRs Nov 7–8: #63373 (22:50Z), #66343 (03:57Z), #66329/#66328 (00:10Z), #66190 (05:36Z), #66358 (07:45Z) — https://github.com/vllm-project/vllm/pull/63373 , https://github.com/vllm-project/vllm/pull/66343

**llama.cpp: overnight merge wave (#31831–#31861)** — Event date: Nov 7–8, 2026 (23:03Z → 09:44Z).
Six merges landed in-window across builds b12193→b12201: #31857 (hexagon n_hvx/n_threads cleanup — Qualcomm NPU backend hygiene), #31859 (server proxy per-request logs downgraded to trace — fixes log spam in --models mode), #31861 (sanitize invalid UTF-8 in PEG tool-call arguments — prevents parser crashes on malformed tool calls), #31854 (ggml CPU backend switched to F32 src1 — precision behavior change for CPU inference), #31851 (llama/mtmd: follow reference precision of ggml_prec_set_acc — multimodal tensor precision consistency), and #31831 (OpenCL: improved q8_0 and MoE small-n verification for spec decoding — AMD/OpenCL iGPU and APU paths).
**Why it matters:** The OpenCL q8_0/MoE spec-verification work extends speculative-decoding reliability to OpenCL devices; the UTF-8 fix prevents hard crashes in agentic tool-call pipelines.
**Local inference:** CPU (F32 precision path), OpenCL (AMD iGPU/APU), Hexagon (Qualcomm NPU) — all local backends.
Source: GitHub llama.cpp merged PRs Nov 7–8: #31831 (06:04Z), #31851 (07:37Z), #31854 (09:44Z), #31857 (23:03Z), #31859 (00:29Z), #31861 (04:06Z) — https://github.com/ggml-org/llama.cpp/pull/31861

**SGLang: Kimi-K3.1 TensorRT-LLM DSA backend under decode context parallelism; scheduler pause refactor** — Event date: Nov 7–8, 2026 (22:41Z → 09:41Z).
#49106 adds a TensorRT-LLM DSA (sparse attention) backend for Kimi-K3.1 running under decode context parallelism — an important serving path for long-context sparse-attention MoE models. #49144 moves scheduler pause state into a PauseState object (refactor touching the hicache/Apple Silicon scheduler path), #49118 reverts the unquant low-M BF16 GEMM autotune (#43703), and the remainder are CI/test hardening (#49128 precision-baseline fix, #49154 xeon disk-prune CI, #49097 kv-canary bf16 fixture).
**Why it matters:** The DSA decode-CP backend extends TensorRT-LLM-class sparse attention into SGLang's parallel decode path for K3.1-class models.
**Local inference:** Targets GPU serving clusters (TensorRT-LLM backend); the PauseState refactor also touches the Apple Silicon scheduler path.
Source: GitHub SGLang merged PRs Nov 7–8: #49106 (08:15Z), #49144 (08:42Z), #49118 (06:07Z), #49128 (06:39Z), #49097 (06:05Z), #49154 (09:41Z) — https://github.com/sgl-project/sglang/pull/49106

## Also noted

None.

## Just missed the window

- **Huawei Qingyun HM650** — first HarmonyOS desktop PC, Kirin X90 chip, "100% domestically made" components; unveiled at the HarmonyOS Office Industry Summit in Wuhan on Nov 5 (SCMP coverage was in-window Nov 7 ~01:25 ET; SiliconReport also published in-window Nov 8). Enterprise/government desktop; no local inference stack disclosed. `missed:` (footnoted once) — https://www.siliconreport.com/huawei-launches-its-first-harmonyos-desktop
- **Maxsun Panther Lake / Wildcat Lake NAS MoDT boards** — updated NAS motherboards: SK-PTLNAS (244×244mm) with Intel Core Ultra 9 386H, 128GB DDR5-7200, 10GbE, five M.2 slots; redesigned Wildcat Lake model (90×165mm, four M.2); reported by VideoCardz Nov 7 ~11:30 GMT (~06:30 ET, ~11h pre-window). `missed:` (footnoted once) — https://videocardz.com/newz/maxsun-updates-panther-lake-and-wildcat-lake-nas-motherboards-with-10gbe-and-up-to-128gb-ddr5
- **Apex-35B-V1.0** — Apex Intelligence's first model: Qwen3.6-35B-A3B fine-tune (36B total / ~3B active, 262K ctx, Apache-2.0), 28 GGUF quants from Q8_0 (37.8GB) to IQ1_S (9.0GB); repo created Oct 28, but in-window r/LocalLLaMA testing discussion ("Anybody tried Apex 35B and Axion 35B?") made it visible today. `missed:` (footnoted once) — https://huggingface.co/Apexintelligence-AI/Apex-35B-V1.0

## Coverage notes

- **Verified count:** 6 items (1 hardware report; 3 inference-stack merge waves; 2 community posts). Counting unit: distinct underlying events.
- **Ledger dedupe:** 5 candidates suppressed as already reported — CPU-Z RAM-capacity decline (Nov 7 07:45 run), Googlebook Nov RAM-freeing update (Nov 7 12:45), NVIDIA RTX 5090 giveaway (Nov 6 17:45), llama.cpp llama_moe_cache #30966 (Nov 7 12:45), Nous Research Arete-27B river item (Nov 6 17:45).
- **Notable dead ends:** ram-altman.lol direct fetch blocked (resolved to private network address); generic "llama.cpp release" web searches returned only comparison guides; HN river (points>30) was dominated by non-AI stories (Commodore PET history, GitHub UI previews); NVIDIA forum "70 tok/s Qwen3.8-Flash-Next-Int4-AutoRound" is a September thread with only recent replies — not an in-window event.
- **Indexes:** Techmeme river up (harvested Nov 7 9:45 PM–Nov 8 7:45 AM); HN Algolia up; ServeTheHome up (no new in-window articles beyond the already-reported ASRock Rack DMRD16); GitHub search API up (JSON truncated in cache but meaningful merges captured); HF papers page showed Nov 6 submissions only — no in-window papers verified.
- **Thin-day note:** No silicon announcements, model releases, or benchmark studies verified in this 14-hour overnight window — the report stands at six items, not padded.
