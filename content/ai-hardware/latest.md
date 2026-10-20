# AI Hardware & Local Models Brief — Tue Oct 20, 12:45→17:46 ET

**Window:** 2026-10-20 12:45 EDT → 2026-10-20 17:46 EDT (5h; previous fire → now). New-items-only; event date anchors, not article freshness.

**TL;DR**
- **Bria's Fibo 2.0 open-weight image model** (Dev + Edit Turbo) lands today and is already ranked **#1 image-editing** (and #2 text-to-image) among open-weight models on the Artificial Analysis Image Arena as of Oct 20 — an 8B flow-matching model on the FLUX.2 VAE, runnable locally on CUDA/ComfyUI.
- **Overnight inference-stack merges**: llama.cpp added H2O-Lightning-4B and Nemotron-3.5-Embed support plus a Qwen output-reshape and Metal/Hexagon fixes; vLLM and SGLang shipped DeepSeek-V4 EPLB, ROCm Qwen3.8-27B, and Kimi-K3 perf work; Unsloth Studio patched FLUX.2 First-Block-Cache and FP8/int4 dequant.
- **Community local-inference surge on r/LocalLLaMA**: a new M5-Mac engine for Qwen3.8-Flash-Next (~100–120 tok/s), a llama-swap-style GPU engine-swapper, and a Mac local-LLM configurator.
- **AI silicon was quiet in-window** — the day's big hardware story (Intel GPU D-Series / Crescent Island, 480GB LPDDR5X) was announced Oct 12 and falls outside this window.

---

## Model releases

**Bria Fibo 2.0 (Dev + Edit Turbo) — open-weight image model tops the open editing board**
*Event date: 2026-10-20 (uploaded today).*
Bria AI published **Fibo 2.0 Dev** and the distilled **Fibo 2.0 Edit Turbo** on Hugging Face today. Fibo 2.0 is an **8B-parameter flow-matching model** operating in the latent space of the **FLUX.2 VAE**, with prompts encoded by **Qwen3-VL-4B-Instruct** and a two-layer Perceiver resampler compressing them into 256–512 "gist" tokens fed to a single-stream DiT (44 blocks). The headline: Bria reports it is **#1 in image editing and #2 in text-to-image among open-weight models on the Artificial Analysis Image Arena as of Oct 20, 2026**. Edit Turbo is distilled for 4-step, guidance-free editing (low latency); Dev is the full-quality, fine-tunable base. License is CC BY-NC 4.0 (commercial use needs a Bria agreement). It ships as open weights, API endpoints, and ComfyUI nodes, with a `BriaFibo2Pipeline` landing in diffusers (PR #14991).
- **Why it matters:** This is a genuine open-weight *editing* model (not just generation) hitting a public arena #1 on its release day — meaningful for anyone running local image pipelines on ComfyUI/diffusers, and it reuses the FLUX.2 VAE + a Qwen-VL encoder, so the local stack around it is familiar.
- **Local inference:** Single NVIDIA/AMD GPU on CUDA; runs in ComfyUI and diffusers. At 8B in bf16 plus the FLUX.2 VAE and a 4B VLM encoder, expect a **~16–24 GB VRAM class** fit — tight on an RTX 5060 Ti 16GB, comfortable on a DGX Spark 24GB or a 3090/4090 24GB.
- **Source:** briaai/Fibo-2.0-edit-turbo, Hugging Face, 2026-10-20 — https://huggingface.co/briaai/Fibo-2.0-edit-turbo ; briaai/Fibo-2.0-dev, 2026-10-20 — https://huggingface.co/briaai/Fibo-2.0-dev

---

## Community spins and quantizations

**Ijah — a community M5-Max engine for Qwen3.8-Flash-Next, plus Inco's DFlash2 drafter**
*Event date: 2026-10-20 (posted ~13:00 ET on r/LocalLLaMA).*
A new experimental inference fork, **Ijah** (`publicExcess/ijah`), targets **Apple M5 Max** and adds **Qwen3.8-Flash-Next** support — the author reports ~100 tok/s (90–110 through its server) with EXL3 "trellis" 5-bit weights on 5B–8B models, ~6% behind the optimal MLX build but with smaller RAM footprint, rising to **~120 tok/s paired with Inco's DFlash2 drafter** (also released today). Framed as a follow-on to the author's earlier M5 engines Splish/Sushi.
- **Why it matters:** Flash-Next is a heavy MoE; a Mac-native engine that fits 5B–8B classes at ~100+ tok/s with a companion drafter is exactly the "what runs where" progress this brief tracks for the Apple-Silicon local camp.
- **Local inference:** Apple M5 Max unified memory (Mac-class), 5-bit expert weights; the ~120 tok/s figure is with the DFlash2 drafter.
- **Source:** r/LocalLLaMA, "Ijah: M5 Mac engine for Qwen 3.8 Flash-Next" (via redlib mirror), 2026-10-20 — https://www.reddit.com/r/LocalLLaMA/comments/1xa30fc/ijah_m5_mac_engine_for_qwen_38_flash_next_swift/ ; repo — https://github.com/publicExcess/ijah

**berth — a llama-swap-style GPU engine manager that also serves engines as an API**
*Event date: 2026-10-20 (posted ~13:45 ET; 189 upvotes).*
A community resource, **berth** (`T-Crypt/berth`), automatically watches the GPU and swaps/loads whichever engine you want (llama.cpp/`llama-swap`, Stri­ta, ComfyUI, OpenWebUI) so only one holds VRAM at a time — the poster runs it across multiple devices on a single **RTX 4090** and can expose the active engine as an API endpoint.
- **Why it matters:** VRAM contention between an LLM engine and an image engine (e.g. ComfyUI holding memory) is a daily local-inference pain; an automatic swapper with an API front-end is a practical tool for the 24GB-class single-GPU user.
- **Local inference:** Single consumer GPU (demonstrated on RTX 4090 24GB); targets the 16–24GB VRAM class where one card is shared across engines.
- **Source:** r/LocalLLaMA, "24GB is plenty if your engines take turns" (berth), 2026-10-20 — https://www.reddit.com/r/LocalLLaMA/comments/1xahi82/24gb_is_plenty_if_your_engines_take_turns/ ; repo — https://github.com/T-Crypt/berth

**modelfit.io — a Mac local-LLM configurator with llama-bench-derived speed estimates**
*Event date: 2026-10-20 (posted ~12:45 ET).*
A community tool, **modelfit.io**, lets you pick a Mac and memory config and see which LLMs fit in RAM and an estimated tok/s for each — the estimates come from a model fitted on real `llama-bench` runs (with typical-error bands), and it pulls Apple list prices daily.
- **Why it matters:** It turns "will this model fit and how fast" into a purchase-time answer for unified-memory Macs, which is the core local-inference sizing question for that hardware.
- **Local inference:** Apple Macs (unified memory); the poster weighs M5-Pro 48GB vs M6 32GB.
- **Source:** r/LocalLLaMA, "I got tired of Apple's configurator, so I built one for local AI" (modelfit.io), 2026-10-20 — https://www.reddit.com/r/LocalLLaMA/comments/1xagmpd/i_got_tired_of_apples_configurator_so_i_built_one/ ; https://modelfit.io/

---

## Inference software and benchmarks

**llama.cpp — overnight builds add two models and several backend fixes**
*Event date: 2026-10-20, 12:54→17:17 ET (7 merges since the prior fire).*
Notable in-window merges: **H2O-Lightning-4B** decision-model support (#30361) and **Nemotron-3.5-Embed** support (#30877); a **Qwen output-reshape** to 2D instead of 3D (#30823); **LFM2** mtmd tiling/layout matched to transformers (#30261) and a shortconv/FFN base refactor (#30874); a **Hexagon** VTCM-size query fix for image layouts (#30655); and server **logprobs with speculative decoding** (#27196).
- **Why it matters:** Continuous nightly progress on model coverage (a 4B decision model, an embedding model) and backend correctness — the exact churn that keeps local engines running the newest weights.
- **Local inference:** CPU + Metal/CUDA/HIP/Vulkan/SYCL/Hexagon; the new models are small (4B decision, embedding) — fit any RTX 5060 Ti 16GB / DGX Spark 24GB class or CPU.
- **Source:** ggml-org/llama.cpp commits since 2026-10-20T16:45Z, GitHub, 2026-10-20 — https://github.com/ggml-org/llama.cpp/commits/main

**vLLM / SGLang — DeepSeek-V4, ROCm Qwen3.8-27B, Kimi-K3, and DSpark merges**
*Event date: 2026-10-20, ~12:48→17:48 ET.*
vLLM in-window merges include a **DeepSeek-V4 EPLB** bugfix (load redundant expert slots, #59611), **ROCm `rms_norm_gated` enabled by default for Qwen3.8-27B** (#60250), a **Kimi-K3** GLA-gate GEMM + MLA FP8 prefill fuse (#59634), and a FlashInfer DCP prefill pass via trtllm-gen (#62029). SGLang shipped **DSpark simulated-acceptance fast paths on CUDA** (#45421, then toggled off-by-default in #45433), a **muse_spark** unified-KV/TRT-LLM fix (#45356), and ROCm activation/diffusion fixes (#44198, #44596).
- **Why it matters:** ROCm parity for Qwen3.8-27B and Kimi-K3 perf fuses directly affect AMD-GPU local/data-center inference; the DSpark fast-path work is the ongoing tuning of the DGX-Spark-class inference path.
- **Local inference:** vLLM/SGLang on NVIDIA (B200/H200/MI3xx target profiles) and AMD ROCm (MI350-class); the DSpark work targets DGX Spark (Spark-class) hardware.
- **Source:** vllm-project/vllm and sgl-project/sglang commits since 2026-10-20T16:45Z, GitHub, 2026-10-20 — https://github.com/vllm-project/vllm/commits/main ; https://github.com/sgl-project/sglang/commits/main

**Unsloth Studio — FLUX.2 First-Block-Cache fix and FP8/int4 dequant patches**
*Event date: 2026-10-20, ~17:05→17:43 ET.*
In-window Unsloth commits: fix **First Block Cache on FLUX.2** (dual-stream head / single-stream tail, #14401); **FP8 block dequant below sm89** in one Triton pass (#14408); trace int4-packed dequant inside torch.compile (#14385); and a Studio retry when a custom provider (LM Studio) refuses `json_object` (#11813).
- **Why it matters:** The FLUX.2 cache fix keeps local image-model fine-tuning/inference correct on the newer VAE, and the dequant patches widen FP8/int4 support to older GPUs (below sm89).
- **Local inference:** Unsloth Studio on consumer NVIDIA GPUs; the sm89-path dequant helps pre-RTX-40-series cards.
- **Source:** unslothai/unsloth commits since 2026-10-20T21:00Z, GitHub, 2026-10-20 — https://github.com/unslothai/unsloth/commits/main

**Also noted (verified in-window):**
- A community note that **Kimi-K3** scores well on the new **Frontier Code v1.1** (agentic SWE bench) yet poorly on **Terminal Bench v4** — a reminder that the two reasoning/coding boards disagree; worth tracking which a model is tuned for. (r/LocalLLaMA, 2026-10-20 — https://www.reddit.com/r/LocalLLaMA/comments/1xahu9x/terminal_bench_v4_vs_frontier_code_v11/)
- A dual-**AMD Radeon 9700** layer-split showcase: splitting a model across two cards drops per-GPU load under 50% so fans stay quiet on a Debian workstation. (r/LocalLLaMA, 2026-10-20 — https://www.reddit.com/r/LocalLLaMA/comments/1xaj4y5/2x_r9700_layer_split_side_effect/)

---

## AI hardware and silicon

**Quiet in-window.** No new GPU/NPU/accelerator/APU or driver-branch announcements were first disclosed in this 5-hour window. The day's standout silicon story — **Intel GPU D-Series (Crescent Island)**, a 350W PCIe inference card with up to **480GB LPDDR5X** (160GB Intel / 480GB ODM), **1.3 PFLOPS FP8**, 1.5 TB/s, Xe3P, 32 Xe cores / 256 XMX — was announced **Oct 12** at the OCP Global Summit and re-reported by Seoul Economic Daily on Oct 20; per the event-date rule it is **out of window** and is not repeated here. VideoCardz's Oct 20 GPU items (GALAX HOF Extreme dual-16-pin RTX 5080, RTX 6060-2029 rumor) are gaming/AIB news with no AI-inference angle and are out of scope.

---

## Coverage notes
- **Verified count:** 8 items (1 model release, 3 community, 3 inference-software/benchmark clusters, plus 2 "also noted"); 1 hardware-section note (no in-window silicon).
- **Ledger dedupe:** Suppressed Intel GPU D-Series/Crescent Island (Oct 12 OCP reveal; re-reported Oct 20 — out-of-window, not in ledger, excluded by event-date rule); H2O-Lightning-4B as a *model* (released Oct 7–12; only its in-window llama.cpp adapter is listed, under llama.cpp); prior-today quants (Unsloth SAM3.1, mradermacher LFM2/CARDS, DiffSynth FLUX-3-Klein EntroPack, Ornith-1.5-122B GGUF, Kromoss, Gradium Phonon) and prior inference-stack merges (vLLM Rust-frontend/AITER FP8 MoE; SGLang GLM-5.2 NPU/T-HEAD SAIL; DeepSeek DeepInfer; Geezuz kernel; DS-V4.1-Flash 4×3090) — all already in `reported.md`, not repeated.
- **Dead ends / limitations:** Reddit's native endpoint returned 403 (used a redlib mirror for the r/LocalLLaMA new queue); ServeTheHome's Intel D-Series coverage confirmed the Oct 12 event date; no fresh in-window hardware announcements found across VideoCardz, Tom's Hardware, and The Register.
