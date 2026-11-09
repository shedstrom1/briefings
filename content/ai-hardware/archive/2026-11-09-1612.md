# AI Hardware & Local Models Brief — Monday, Nov 9, 2026 (catch-up edition)

**Window: 2026-11-09 07:45 → 12:45 ET (12:45–17:45 UTC)** — catch-up run for the failed 12:45 fire; the 12:45→17:45 slice belongs to the 17:45 edition. New items only; deduped against the reported.md ledger.

## TL;DR
- A civil-litigation lawyer is running DeepSeek-V4.1-Flash fully GPU-resident on **eight used Tesla V100s** — up to **215 tok/s** on hardware that cost about $2,400 per four-GPU SXM2 board; Tom's Hardware reported the build inside the window.
- **RustingStrata** (pure-Rust engine) now boots the **249 GB DeepSeek-V4.1-Flash IQ2_XXS** on a single RTX 3060 12 GB with just 30 GB RAM, beating llama.cpp --cpu-moe 2–3x.
- **TheDrummer returned** with first-announcements of Orion 26B-A4B v1 and v1.1 (Gemma-4 creative-writing FTs) and **BeaverNet**, a free anonymous OpenAI-compatible hub running 12 donated models.
- **vLLM's morning wave (21 PRs)** added an H200 FP8 config for GLM-5.3-Flash TP8 and a MiniMax-M3.1-Flash MI355X AgentX PD-disaggregation recipe; llama.cpp shipped **b12223–b12230**.
- mradermacher published **35 GGUF repos** inside the five-hour window, and two big open-weight drops (Meituan LongCat-2.5, qwen-concept-27b) landed as narrow near-misses.

## AI hardware and silicon

**Lawyer's eight-used-V100 private AI server pushes DeepSeek-V4.1-Flash to 215 tok/s offline** — event date Nov 9 (Tom's Hardware, published 13:20 UTC / 08:20 ET). A full-time civil-litigation attorney built a private rig from two 4-card Nvidia Tesla V100 SXM2 NVLink boards (about **$2,400 each**, 2018-era Volta, 32 GB per chip), four PEX switches and a tangle of SlimSAS cables — including one burnt power connector along the way. It runs the open **DeepSeek-V4.1-Flash-1017 at 2-bit quant with every expert resident in GPU memory**, reaching up to 215 tokens/s; the builder says 50–60% of their AI workload now skips frontier APIs entirely, on two ordinary office circuits. The build is documented as a PP2×TP4 parallel layout with top-k accuracy measurements in a public GitHub repo, and the builder says an "NVFP4" follow-up tier (described as essentially full precision) targeting ~80 tok/s on the same hardware is being finalized.
*Why it matters:* the used-datacenter-GPU economics keep improving for budget local inference — roughly $4,800 of Volta HBM2 bought this rig far more resident-expert capacity than a new 16 GB desktop card, and a regulated-profession (legal/health) offline use case is exactly where 2-bit quants plus a serious harness become defensible.
*Local inference:* 8× V100 32 GB SXM2 (Volta SM70, no FP8/FP4 hardware — quants run via the builder's custom kernels); fits the entire 2-bit DeepSeek-V4.1-Flash expert set; ~$4,800 GPU-side cost.
Sources:
- Lawyer builds private AI server with eight used Nvidia V100 GPUs — up to 215 tokens per second — Tom's Hardware, 2026-11-09 13:20 UTC — https://www.tomshardware.com/tech-industry/artificial-intelligence/lawyer-builds-private-ai-server-with-eight-used-nvidia-v100-gpus-up-to-215-tokens-per-second-to-keep-legal-files-offline-costing-about-usd2-400-per-four-gpu-board
- Builder's repo: ProprietaryLegal/Volta-sPLIt-DeepSeek-V4.1-Flash-1017-8xV100-SXM2-PP2xTP4, GitHub — https://github.com/ProprietaryLegal/Volta-sPLIt-DeepSeek-V4.1-Flash-1017-8xV100-SXM2-PP2xTP4

## Model releases
No brand-new open-weight drop was first disclosed inside the 07:45–12:45 ET window (the two closest, LongCat-2.5 and qwen-concept-27b, are footnoted below).

## Community spins and quantizations

**RustingStrata runs the 249 GB DeepSeek-V4.1-Flash on a 12 GB RTX 3060 + 30 GB RAM** (update on previously reported "RustingStrata", Nov 4; r/LocalLLaMA, Serious_Scarcity7818, 16:27 UTC). The pure-Rust, no-ggml engine (CUDA + Vulkan backends) now loads **DeepSeek-V4.1-Flash at IQ2_XXS (249 GB)** on a single 12 GB RTX 3060 with 30 GB of RAM: experts stream from SSD through the page cache while a **9 GB LRU expert cache** lives in VRAM and attention/shared matvecs stay on-GPU. Author-reported numbers on the same box: decode **2–3.6 tok/s vs llama.cpp b12116 --cpu-moe at 0.3–1.5 tok/s**, prefill up to ~2 tok/s, top-1 agreement with llama.cpp 189/192 on short prompts. Caveats from the author: greedy decoding only, 4096-token context cap, run-to-run variance with the model larger than RAM; `strata serve` exposes OpenAI-compatible /v1/chat/completions with SSE.
*Why it matters:* this is the strongest "huge MoE on junk-drawer hardware" result of the morning — a Turing-class 12 GB card plus 30 GB RAM serving frontier-adjacent MoE weights at usable-if-slow speeds, and the engine keeps expanding past Qwen to DeepSeek MoE architectures.
*Local inference:* 1× RTX 3060 12 GB + 30 GB DDR4 + NVMe; realistic ceiling is slow single-user decode, but the residency trick (SSD→page-cache→VRAM LRU) is the same pattern that made the 4×V100 and Ryzen-AI-Max rigs work.
Source:
- RustingStrata vs llama.cpp, DeepSeek-V4.1-Flash Q2 (249 GB) on a single RTX 3060 — r/LocalLLaMA, 2026-11-09 16:27 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xrw6uv/rustingstrata_vs_llamacpp_deepseekv41flash_q2_249/

**TheDrummer announces Orion 26B-A4B v1 + v1.1 and the BeaverNet free-model hub** (r/LocalLLaMA, TheLocalDrummer, 15:43 UTC). The prolific merge author returned from a self-described "stage fright" hiatus to push the first formal announcements of **Orion-26B-A4B** — fine-tunes of Google's Gemma-4-26B-A4B-it; v1 (previously unannounced, community RP workhorse) and v1.1, which "pushes creative writing further and makes full use of Gemma's reasoning" (v1.1 weights last modified Nov 5). Alongside, he announced **BeaverNet** (net.beaverai.org): a hub where generous providers collectively serve **12 models free, anonymously, behind an OpenAI-compatible API**.
*Why it matters:* 26B-total/4B-active MoE FTs are the sweet spot for the 16 GB–24 GB local tier, and BeaverNet is community inference-as-a-utility infrastructure — an on-ramp for people whose hardware can't host the donated models.
*Local inference:* Gemma-4-26B-A4B class — A4B active weights land it in 16 GB-card quant territory (community quants expected; none yet at announcement time).
Sources:
- Drummer's Orion 26B A4B v1 and v1.1! ... Plus a new project from yours truly — r/LocalLLaMA, 2026-11-09 15:43 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xruzry/drummers_orion_26b_a4b_v1_and_v11_plus_a_new/
- TheDrummer/Orion-26B-A4B-v1.1 — Hugging Face — https://huggingface.co/TheDrummer/Orion-26B-A4B-v1.1

**mradermacher publishes a 35-repo GGUF ladder in five hours** (Hugging Face, repos created 13:38–17:39 UTC). Highlights from the in-window cluster: **Arete-27B-Uncensored-V2** (+i1 imatrix) of SargeDev's abliterated Nous-Arete lineage; **Terminal-RSI-27B-Unlocked-Uncensored-Heretic** (+i1) of SolsticeAI's RL terminal-agent (arXiv 2610.22192); **Vesper-31B-Nocturne** and **Vesper-26B-A4B-Nocturne** (+i1) ReadyArt merges; **Glistening-Gem-31B-v2.2** (+i1) mergekit merge; **Qwen3-8B-3MPER0RR-BF16-DeepSeek-V4-Flash** (+i1) merge; **K2-Horizon-4x0.9B-MoE-heretic**; **EuroLLM-22B-grc-ro** (Greek/Romanian); **S5-nano-uncensored** (+i1, spin of Maritaca's Brazilian VLM); the LFM3-1.7B heretic/ARA/Max cluster, **LFM3-Omni-2B-Uncensored-i1**, **LFM2.5-2.6B-junie-harbor-rl-v0.3** (coding-agent RL); plus niche single-purpose quants: **Second-1.0-2B** (chess GRPO), **Rein-NPC-4B** (typed game-NPC decisions), **Fritter-TTS** (Irish-English voice-clone TTS), **qwen3-asr-0.6b-kazakh**, **dale-0.8b** (NL→Bash), **TabXLLM-8B**, **Wit-Coder-4B**, **AXIOMRIFT-3.8B** (neuro-symbolic code), **MAI-UI-2B5**, **character-animate-4b**, **ThyMind-9B**, **K-Oss-9-Mini**, **nib-4b-preview**, **abstain-doctrine-r1**.
*Why it matters:* the sub-4B and edge-tier specialist models (chess, NPC decisioning, ASR, TTS, NL→shell) are what make on-device agent stacks cheap; the 26–31B merge ladders feed the 16–24 GB card crowd the same day the merges drop.
*Local inference:* 0.6B–4B quants run on <8 GB cards/CPU/edge (Jetson/phone class); the 26–31B MoE merges target 16–24 GB VRAM at Q4-class.
Source:
- mradermacher — models created 2026-11-09 13:38–17:39 UTC — https://huggingface.co/mradermacher

## Inference software and benchmarks

**vLLM lands 21 merged PRs in the morning window** (GitHub, merges 13:11–17:28 UTC). Decision-relevant items: **#66537** adds an **H200 FP8 config for GLM-5.3-Flash TP8** (per-config 1.03–1.6x class tuning in the recent series); **#66268** publishes the **MiniMax-M3.1-Flash MI355X AgentX PD-disaggregation recipe over MoRI-IO**; **#65406** fixes expert down-projection bias being counted once under TP (MoE correctness); **#60390** shares the CPU-offloaded Qwen4Exp PLE table across co-located DP replicas; **#66397** builds DSv4.1 replay builders at warmup on ROCm; **#66559** migrates Phi3 to the Transformers backend; plus persistent-topk on ROCm, LoRA-on-embedding support, sleep-mode allocator refactor and CI fixes.
*Why it matters:* the ROCm/MI355X AgentX recipe line is turning AMD's inference rack into a documented, reproducible serving path for the hot open MoE models, and the H200 FP8 config keeps GLM-5.3-Flash TP8 deployments tuned.
*Local inference:* datacenter-class (H200/MI355X) work; the MoE bias fix and DP-replica table sharing benefit multi-GPU homelab TP users.
Source:
- vllm-project/vllm — pull requests merged 2026-11-09 12:45–17:45 UTC — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged

**llama.cpp b12223–b12230: five merges, server + ggml maintenance** (GitHub, releases 12:52–17:02 UTC). Merged in-window: **#31927** reads MTP (spec-decode) target hidden states from a device buffer; **#31940** keeps dummy FFN activation scales away from zero in tests; **#30712** implements the **backward pass for TRI**; **#31824** makes ggml-zdnn accept non-zDNN tensors and invalidate compute tensors after each op; **#28697** fixes server `response_format` when the JSON schema isn't wrapped. No new architecture support this wave; next builds (b12233, 14:15 ET) land after the window and are deferred.
*Why it matters:* the MTP device-buffer change shaves a host round-trip on every speculative step — small, but it hits every drafter-equipped server; the zDNN (Qualcomm) and TRI backward items keep niche backends honest.
*Local inference:* any llama.cpp backend; MTP drafting path on all quant tiers.
Source:
- ggml-org/llama.cpp — releases b12223–b12230, 2026-11-09 — https://github.com/ggml-org/llama.cpp/releases

**SGLang: four in-window merges (HiCache + dspark + NPU)** (GitHub, 12:52–16:23 UTC): **#46168** cuts host-side time of Mooncake direct-linker layer-wise loads; **#47627** stages DSv4.1 long-context target-verify metadata beside the draft for dspark spec-decode; **#49318** adds an NPU GLM-5.3 2P2D 32-prefill PD-separation perf case; **#46934** restores sub-window SWA L3 cache hits under a mid-tree HiCache anchor. (The window's fifth merge, #48786 HiCache TMA copy kernel, was already reported by the 07:45 edition.)
*Why it matters:* the dspark metadata-staging and HiCache L3-hit fixes both target the long-context MoE serving path on NVIDIA; the NPU perf case extends Huawei-side GLM-5.3 validation.
*Local inference:* primarily datacenter serving (Mooncake/NPU), with indirect long-context decode gains.
Source:
- sgl-project/sglang — pull requests merged 2026-11-09 — https://github.com/sgl-project/sglang/pulls?q=is%3Apr+is%3Amerged

## Also noted
- **Top 7 Decision Models Benchmark** — m4rkGS evaluated seven decision models across 2,826 real tech decisions (r/LocalLLaMA, 15:24 UTC) — https://www.reddit.com/r/LocalLLaMA/comments/1xruhp3/top_7_decision_models_benchmark_evaluation_across/
- **Weight-space programming applied to a Pac-Man decision agent** — facethef edited Qwen3.5 into a Pac-Man decision model; the thread was skeptical ("a solved game") — r/LocalLLaMA, 15:40 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xruwlf/weightspace_programming_editing_qwen35_into_a_top/
- **Meituan LongCat-2.5 open weights** — ~1.78T-parameter MIT multimodal MoE (208 BF16 shards + multimodal parts); final files hit the Hub at 11:38 UTC, 67 minutes **before** the window opened; community wave at 09:14 ET. (Just missed the window — footnoted once.) — https://huggingface.co/meituan-longcat/LongCat-2.5
- **ConceptCountry/qwen-concept-27b** — weight-space-programming "concept" edit of Qwen3.8-27B shipping GGUF Q4_K_M/Q8_0, FP8 and NVFP4a16 tiers with a published WSP recipe; repo last modified Nov 8 18:00 UTC, rL thread at 12:29 ET. (Just missed the window — footnoted once.) — https://huggingface.co/ConceptCountry/qwen-concept-27b

## Coverage notes
- **9 verified in-window events** (6 lead-section items, 3 also-noted lines), each from a fetched page carrying an in-window timestamp; lead stories read in full. All merge/quant timestamps are primary (GitHub merged_at / HF createdAt).
- **Ledger dedupe: 5 candidates suppressed as already reported or re-covered** — SGLang #48786 (reported by the 07:45 edition today), Confucius4-R2T2 (Sept repo re-surfaced), Kimi-K3-on-Optane CPU story (already footnoted once on Nov 8), Raspberry Pi 5 16GB $385 (Nov 3 event re-covered by HN front page), Gemini 4 Carbon (Oct 28 re-covered). The two "Just missed the window" footnotes above are first-time (never repeated).
- **Deferred to the 17:45 edition (published after window end, not claimed here):** NextPlatform's Google TPU 8tx/8i-Max architecture briefing (18:06 UTC), Nvidia's overseas cloud GPU-rental crackdown incl. $2.8M black-market B300 racks (17:48 UTC), Ollama v0.40.16-rc0 (18:21 UTC), llama.cpp b12233 (19:15 UTC).
- **Dead ends:** VideoCardz RSS blocked by Cloudflare (not retried, per two-strike rule); Tom's Hardware /feed 404s (worked around via category pages + browser meta timestamps).
- **A thin hardware day is a valid day:** no vendor hardware announcement fell inside the 07:45–12:45 ET slice; the used-V100 build is the section's honest lead.
