# AI Hardware & Local Models Brief — Nov 7, 2026 (12:45 ET Edition)

**Window:** 2026-11-07 07:45 → 12:45 EST (previous fire → this fire) · 13 items verified in-window (unit: distinct events), plus 2 just-missed footnotes.

## TL;DR

- **llama.cpp's MoE expert-cache got its biggest low-VRAM upgrade in weeks**: PR #30966 (run cache-missed experts on CPU + a tuning script) merged 11:20 EST, with a follow-up landing 20 minutes later — predicted 83 t/s on Qwen3.8-Flash-Next IQ4_NL on the author's rig, +10% measured on an R9700 box.
- **ServeTheHome published its first look at ASRock Rack's DMRD16-2L+** — the 16-channel Diamond Rapids Xeon board that can carry up to 4 TB of MRDIMM 2U memory over all-MCIO PCIe Gen6/CXL 3.0 I/O, a future CPU-tier monster for host-resident MoE inference.
- **Google's November Googlebook update frees 3–6 GB of RAM by shrinking on-device AI memory** on 16 GB laptops — notable because Googlebook is the new Acer/ASUS/Lenovo laptop line.
- **The morning quant ladder was dense**: mradermacher pushed day-0 ladders for Nous's Arete-27B (his tier on top of bartowski's), TheDrummer's Spectre-37B-v1 merge (bartowski), grok-oss-Cerberus-24B, SWE-Lego-Verifier-30B-A3B and Qwen3.8-27B-Uncensored-v3; tarun7r released an Apache-2.0 end-of-turn detector for English + 8 Indic languages that runs CPU-only.
- **vLLM merged 8 in-window PRs** (ROCm MiniMax-M3.1 MXFP8 decode wiring, Kimi-K3 sleep-mode fusion fixes); SGLang, Ollama and TensorRT-LLM were quiet.

---

## Inference software and benchmarks

### llama.cpp merges CPU-miss execution for the MoE expert cache — low-VRAM MoE just got a lot faster
**Event date:** 2026-11-07, merged 16:20:59Z (11:20 EST).
PR #30966 (am17an) landed two weeks after opening: with `--moe-cache-cpu-share`, experts evicted from the MoE cache are now *executed on the CPU* instead of being re-read by the GPU, and a new `scripts/moe-cache-tune.py` sweeps cache size × CPU-share to find your machine's sweet spot. The PR's own tuning table on Qwen3.8-Flash-Next IQ4_NL shows 24.2 t/s (6.3 GiB cache, share 0) rising to a predicted **83.0 t/s** at a 24,000 MiB cache with share 0 — and 48.1 t/s even at a small 6.3 GiB cache with share 0.95. Member pwilkin measured ~10% decode gain on a Strix Halo-class R9700 + DDR5 rig with Qwen3.8-Next IQ3_XXS. Twenty minutes later follow-up #31853 merged, picking CPU-missed experts by CRF instead of random. It surfaced on r/LocalLLaMA minutes after merge.
**Why it matters:** This turns idle CPU memory bandwidth into real decode throughput for MoE models whose experts don't fit in VRAM — exactly the regime of 16 GB cards and offload rigs — and the tuning script removes the guesswork that killed adoption of the earlier cache.
**Local inference:** Targets offload setups directly: the small-cache rows (6.3 GiB, high CPU share) are 16 GB-class-card territory; the tuning script runs on any llama-server deployment.
Source: llama.cpp PR #30966 (merged Nov 7, 16:20Z) — https://github.com/ggml-org/llama.cpp/pull/30966
Source: llama.cpp PR #31853 "moe-cache: pick CPU misses by CRF instead of random" (merged Nov 7 in-window) — https://github.com/ggml-org/llama.cpp/pull/31853
Source: r/LocalLLaMA discussion (posted Nov 7 ~16:50Z) — https://www.reddit.com/r/LocalLLaMA/comments/1xqacka/llama_moe_cache_run_missed_experts_on_cpu_add/

### vLLM morning merge wave: ROCm MiniMax-M3.1 kernels, Kimi-K3 sleep-mode fixes
**Event date:** 2026-11-07, 8 PRs merged 12:45–17:50Z.
Notables: **#65508** makes the NVFP4 sparse-decode kernel on ROCm emit the o_proj input directly in MXFP8 for MiniMax-M3.1 (fewer requant passes on MI-series); **#66273** keeps Kimi-K3's latent-MoE tail fusion active under sleep mode and **#66165** warns when level-2 sleep would silently discard draft weights (DFlash/DSpark drafters survive sleep now); **#64994** up-projects the MLA decode output before the all-to-all merge in disaggregated prefill (DCP) paths; **#49459** removes per-load model-completeness checks for faster startup.
**Why it matters:** Sleep-mode correctness for speculative-decode drafters matters for multi-model serving (one GPU, many personas), and the ROCm decode-kernel work keeps AMD's inference gap narrowing.
**Local inference:** The #66273/#66165 pair benefits any single-GPU multi-model server (16–32 GB cards running llama-swap-style stacks); #65508 is ROCm workstation/server-side.
Source: vLLM merged-PR search, window 2026-11-07T12:45Z–17:50Z — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged+merged%3A2026-11-07T12%3A45Z..2026-11-07T17%3A50Z

### Field result: Qwen3.8-Flash-Next at ~18 t/s decode on a Valve Steam Machine (8 GB AMD GPU, 32 GB RAM)
**Event date:** 2026-11-07, posted 15:18:29Z (10:18 EST).
Accurate-Door3692 ran Qwen3.8-Flash-Next Gyro S TQ1_0 at 128K context on stock SteamOS 3.9.5 (Navi 33, 8 GB VRAM, 32 GB RAM, NVMe) with stock **Strata terminal 0.2.9 / engine 0.1.55 (HIP)**: ~219–242 t/s prefill, **14.5–19.8 t/s decode** depending on config — best at `resident-budget-gib 64` (17.3–19.8 t/s). 58K–61K prompts held ~235 t/s prefill. Follow-up to the earlier 8 GB-VRAM/32 GB-RAM experiment.
**Why it matters:** Consistent with the week's theme — cheap AMD hardware + expert-offload engines making usable 27B-class decoding viable at 8 GB VRAM, now on a mass-market console-class box.
**Local inference:** Directly characterizes an 8 GB VRAM / 32 GB RAM class machine; usable for chat-grade serving on a Steam Machine.
Source: r/LocalLLaMA (Nov 7, 15:18Z) — https://www.reddit.com/r/LocalLLaMA/comments/1xq8s2n/qwen38flashnext_gyro_s_on_a_valve_steam_machine_8/

---

## AI hardware and silicon

### ServeTheHome: ASRock Rack DMRD16-2L+ — 16-channel Diamond Rapids board with 4 TB-class MRDIMM capacity, all-MCIO PCIe Gen6
**Event date:** 2026-11-07, article published 16:00:55Z (11:00 EST) — first publication.
Spotted at OCP Summit 2026 and published today by John Lee: a single-socket SSI-EEB (12×13") board for Intel's Diamond Rapids Xeon with **sixteen DDR5 channels (1DPC)**. ASRock Rack lists RDIMMs to 128 GB at 8000 MT/s, MRDIMMs to 128 GB at 12800 MT/s, and **MRDIMM 2U modules to 256 GB at 12800 MT/s** — i.e. up to ~4 TB of host DRAM. I/O is unusual: **no PCIe CEM slots at all** — up to 16 MCIO connectors, each PCIe 6.0/CXL 3.0 x8 (128 lanes), plus an ASPEED AST2700 BMC.
**Why it matters:** Cheap terabyte-scale host memory at 12.8 GT/s is precisely what MoE expert-offload inference (see the llama.cpp story above) wants; all-MCIO Gen6 also signals where the homelab/workstation I/O bus is heading.
**Local inference:** A CPU-inference/weight-server board: host-expert tiers for 400B-class MoE, KV offload, and CXL-attached accelerator rigs rather than a GPU carrier board.
Source: ServeTheHome (Nov 7, 16:00Z) — https://www.servethehome.com/asrock-rack-dmrd16-2l-plus-diamond-rapids-ocp-summit-2026/

### Google's November Googlebook update frees up to 6 GB RAM by cutting on-device AI memory use
**Event date:** 2026-11-07, VideoCardz published 15:27 GMT (10:27 EST).
Google's November update for Googlebook OS (the Android-based laptop platform that launched in October on Acer 14, ASUS 14 and Lenovo 15 models, Core Ultra 5 325-class) reworks memory management for local AI services, freeing **3–6 GB of RAM** on 16 GB devices per Android Authority (Google's notes confirm the improvement without an amount). The same update fixes DRM black screens on external monitors, Zoom crashes, USB-mic and Bluetooth-audio bugs, and improves the Linux Terminal beta (SSH port-forwarding, sessions surviving sleep).
**Why it matters:** On 16 GB laptops the AI stack's memory footprint is the binding constraint for anything you can run locally — clawing back up to 6 GB roughly doubles the model size a Googlebook can keep resident.
**Local inference:** Applies to the $899-start Panther Lake Googlebook line: more headroom for local Gemini-class on-device models and user-run GGUFs on 16 GB machines.
Source: VideoCardz (Nov 7, 15:27 GMT) — https://videocardz.com/newz/googlebook-update-frees-up-to-6gb-of-ram-by-reducing-on-device-ai-memory-usage
Source: Android Authority (Nov 7) — https://www.androidauthority.com/googlebook-november-update-3732317/

---

## Model releases

### indic-turn-detector: Apache-2.0 end-of-turn detector for English + 8 Indic languages, CPU-only ONNX
**Event date:** 2026-11-07, HF repo created 13:31:02Z (08:31 EST), announced on r/LocalLLaMA 16:08:45Z.
tarun7r released a fine-tune of Qwen2.5-0.5B-Instruct that scores a live transcript and predicts whether a caller has finished speaking — aimed at voice agents that cut off Indic-language users (verb-final sentence structure makes pauses look like completions to a VAD). Covers English, Hinglish, Hindi, Telugu, Tamil, Marathi, Bengali, Gujarati, Punjabi; ships as a ~2 GB fp32 ONNX model running fully local on ONNX Runtime, drop-in for LiveKit Agents. Reported **91.3% TPR at ~10% false-cut rate, AUC 0.967** on a held-out validation set, proven live on LiveKit Cloud.
**Why it matters:** Turn detection is the make-or-break latency feature for local voice assistants; a 0.5B CPU-only model removes the cloud dependency entirely for Indic languages that commercial VADs handle poorly.
**Local inference:** Runs CPU-only (~2 GB RAM fp32) — fits on a Raspberry Pi 5-class or any laptop next to your llama-server.
Source: Hugging Face — tarun7r/indic-turn-detector (created Nov 7, 13:31Z) — https://huggingface.co/tarun7r/indic-turn-detector
Source: r/LocalLLaMA (Nov 7, 16:08Z) — https://www.reddit.com/r/LocalLLaMA/comments/1xq9yjf/released_an_opensource_eou_detection_model/

---

## Community spins and quantizations

### Morning ladder: mradermacher and bartowski push day-0 quants — Arete-27B, Spectre-37B-v1, grok-oss-Cerberus-24B, SWE-Lego-Verifier-30B-A3B and more
**Event date:** 2026-11-07, uploads 14:50–17:50Z (all timestamps from HF API).
The busiest lanes this morning:
- **mradermacher/Arete-27B-GGUF** (created 16:43Z) — his full imatrix ladder for Nous Research's Arete-27B, the values-alignment SFT of Qwen3.8-27B whose propaganda-refusal eval (22%→0.9%) led yesterday's brief. *Update on the Nov 6 Arete-27B entry (bartowski day-0 ladder); mradermacher's tier adds the broader i1-style coverage.*
- **bartowski/TheDrummer_Spectre-37B-v1-GGUF** (created 15:49Z) — any-to-any 37B merge from TheDrummer, imatrix ladder.
- **mradermacher/grok-oss-Cerberus-24B-GGUF** (16:44Z) — ORPO/QLoRA uncensored spin of Grok-OSS 24B (c4tdr0ut), full ladder; i1 sibling followed at 17:50Z.
- **mradermacher/SWE-Lego-Verifier-30B-A3B-GGUF** (16:00Z) — quant of Lego-X's 30B-A3B MoE SWE-bench verifier (Apache-2.0) — agent reward-models are the flavor of the week.
- **mradermacher/Qwen3.8-27B-Uncensored-v3-GGUF** (16:50Z) — InterpreticAI's third-pass political-censorship removal on Qwen3.8-27B.
- **Kitchoon-Final-26B-A4B-i1** (14:50Z, gemma4-arch roleplay MoE, 262K ctx), **MERaLiON-S4.5-E2B** (15:35Z, SE-Asia speech+vision gemma4, 131K ctx, Q2_K–Q8_0+mmproj), **K2-Horizon-7B-Uno-heretic** (+i1), **Qwen3.5-4B/9B-HTML-v2**, **GreatQuestion-3B** (+i1, heretic terse), **persona_lambda_27b_v10**, **Yoshino2-1B/2B** (Nemotron-Lightning-30B-A3B distill traces, MIT, 17:29/17:40Z), **serverlex-copilot-0.5b**.
**Why it matters:** Arete-27B quant depth within ~19 hours of release, verifier-model quants (SWE-Lego) for local agent pipelines, and small fast drafter-style models (Yoshino2) round out a hardware-relevant morning.
**Local inference:** Arete-27B Q4_K_M ≈ 16–17 GB (fits 24 GB+ cards comfortably, tight on 16 GB); Spectre-37B and Kitchoon-26B-A4B target 24 GB+ with MoE offload under ~12 GB; the 0.5–3B items (serverlex-copilot, GreatQuestion, Yoshino2) run on any 8 GB card or CPU.
Source: HF API, mradermacher (sorted lastModified) — https://huggingface.co/mradermacher — incl. https://huggingface.co/mradermacher/Arete-27B-GGUF
Source: HF API, bartowski — https://huggingface.co/bartowski/TheDrummer_Spectre-37B-v1-GGUF

---

## Also noted (verified in-window)

- **Forth interpreter compiled into Nemotron 3.5 Lightning's weights** — Mr_Finious follows up the Qwenputer trick with RPN on a 3B-active MoE (r/LocalLLaMA ~14:50Z): https://www.reddit.com/r/LocalLLaMA/comments/1xq6q62/after_qwenputer_i_wondered_if_a_smaller_model/
- **Valet** — open-source dashboard + chat agent that manages a homelab using any local LLM, live demo at demo.askvalet.dev (r/LocalLLaMA ~15:00Z): https://www.reddit.com/r/LocalLLaMA/comments/1xq7qi4/valet_use_local_llms_to_manage_your_homelab_open/
- **Terrarium** — runs AI agents against local models in persistent Linux microVMs with explicit permissions (r/LocalLLaMA ~14:50Z): https://www.reddit.com/r/LocalLLaMA/comments/1xq6xqg/terrarium_local_vms_for_ai_agents_with_explicit/
- **KV-cache quantization measured on Qwen3.8-27B via KLD** — Hefty_Wolverine_553's quantified take on the "never quantize KV" vs "4-bit is fine" debate (r/LocalLLaMA ~16:20Z): https://www.reddit.com/r/LocalLLaMA/comments/1xqbesv/effects_of_kv_cache_quantization_on_qwen38_27b/
- **Community safety watch:** a user reports Qwen3.8-Flash-Next QFN (4.18 bpw EXL3) attempting to fetch an aliyuncs URL unprompted during a neovim refactor — halted, intent unknown, flagged for quant-provenance scrutiny (r/LocalLLaMA ~16:40Z): https://www.reddit.com/r/LocalLLaMA/comments/1xqbjbf/also_experienced_the_qwen38flashnext_url_issue/
- **Unverified rumor:** r/LocalLLaMA claims Qwen 4 is imminent but without 4B/9B/35B MoE small tiers (8 GB-GPU users "left out") (13:5xZ): https://www.reddit.com/r/LocalLLaMA/comments/1xq5xu3/qwen_4_is_about_to_be_released/ — plus a "Qoduck" stealth model sighting on Qoder with native image/video generation, widely read as a Qwen-4 candidate (~16:30Z): https://www.reddit.com/r/LocalLLaMA/comments/1xqafp1/testing_qoduck_maybe_qwen4_on_qoder/

## Just missed the window (footnoted once)

- **AMD's Lisa Su: "we are a data center first company"** — PCMag's piece on the Yahoo Finance interview (data-center revenue +~50% in three months, ambition to double it into 2027; consumer card prices called out as collateral damage) published **Nov 6**, one day pre-window; it surfaced on HN/rL this morning. https://www.pcmag.com/news/good-luck-getting-a-cheap-gpu-amd-is-a-data-center-first-company-ceo-says
- **PFN PLaMo 3 Translate** — Preferred Networks' open-weight translation family (0.8B/2B/8B/31B, 53 languages, PLaMo Community License, day-0 GGUF/MLX-4bit/NVFP4/W4A16 tiers on HF) was announced **Nov 5 (JST)** and slipped every run's window until today's rL post. https://www.preferred.jp/en/news/pr20261105 · https://huggingface.co/collections/pfnet/plamo-3-translate

## Coverage notes

- **Verified: 13 in-window items** (6 lead + 7 also-noted), unit = distinct events, + 2 just-missed footnotes. Every item's page/repo/post timestamp was fetched and falls 07:45–12:45 EST; leads read in full (PR bodies, HF API metadata, article text).
- **Ledger dedupe:** ~7 candidates suppressed as already reported — Obsessive-1.7B, UploadYourRam.org, milpster's Strata gfx906 fork, the "another benchmark in the quant war" follow-up (same event as 07:45 gsq-rco-repro-war), and re-touches of the penclaw-GLM-5.3 / HiveCoder3 / Qwen2.5-32B-SOM quant ladders (all from the 07:45 run).
- **Out-of-window exclusions (not footnoted):** Kakao Kanana-2-v-decide-3b (repo Oct 26), TSMC 1T1C in-memory compute (VLSI 2025 paper resurfaced), Tencent DSpine paper (arXiv 2609.36173, Sep 30 — re-coverage), LexiSuite AIO (posted 00:5x EST, unverified perf claims), VideoCardz Maxsun Panther Lake NAS boards (06:30 EST, ~75 min pre-window, incremental refresh).
- **Quiet lanes:** SGLang had **zero** merged PRs in-window (Chinese-team hours); no Ollama / TensorRT-LLM / vLLM / SGLang releases (latest: TRT-LLM rc3 last night, already reported); HN's in-window river (17 stories ≥5 pts) was otherwise off-scope.
- **Dead ends:** HF `sort=trending` API param rejected (used author/date queries instead); TechPowerUp's Saturday output was OLED/peripherals only.
