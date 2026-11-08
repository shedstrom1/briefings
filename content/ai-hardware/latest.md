# AI Hardware & Local Models Brief — Sunday, Nov 8, 2026, 12:45 ET

**Window:** 2026-11-08 07:45 ET → 12:45 ET (previous fire 07:45 → this fire; Sunday, 5-hour window).

**TL;DR**
- GameGPU reports the RTX 5060 Ti 16GB is being bought en masse for local AI — multi-card rigs (28GB, 48GB) are running Qwen3.8-Flash-Next, and retailer stock is depleting with shortages predicted.
- Nace AI announced **Drex Max v1.1**: its 27B multimodal decision model now claims SOTA on the Artificial Analysis multimodal decision index; v1.1 weights are being optimized for local inference.
- **RustingStrata** (pure-Rust Qwen GGUF engine) posted Q5_K_M results: 70 tok/s decode on a 12GB RTX 3060 vs llama.cpp's 43, with ~⅔ of experts on CPU.
- Tencent's **DSpine** draft model for Qwen3.8-27B landed on Hugging Face yesterday evening — up to 3.84x agent throughput, just outside the window (footnoted).
- Overnight waves: **llama.cpp b12211** released (SpacemiT MXFP4, Zamba2 SSM fast path, llamafile VXE); **vLLM** merged 7 PRs (Muse Spark cleanup, sleep-mode offload, ROCm Qwen4Exp fix).

## AI hardware and silicon

**RTX 5060 Ti 16GB is being bought up en masse for local AI**
Event date: 2026-11-08.
GameGPU reports regular users have started buying budget GeForce RTX 5060 Ti 16GB cards in bulk for local AI workloads, driven by a shortage of available VRAM as open-source model requirements grow. The article documents common rigs: pairing a 5060 Ti with an existing RTX 5070 12GB for 28GB total to run the Qwen3.8-Flash-Next IQ3_S quant, and installing a third 5060 Ti via M.2/OCuLink when 28GB cannot cover larger contexts. With Strata 0.2.10 streaming KV cache into 64GB DDR5, users can open a 256K-token window and process ~246 input tokens/s on two cards — but RAM offload slows prefill, so enthusiasts prefer more silicon. Retailer inventories are depleted and market watchers predict imminent shortages.
**Why it matters:** The 5060 Ti 16GB ($429 MSRP, ~180W, single 8-pin) is the default budget 16GB local-inference card; mass multi-card adoption now competes with gaming demand in a market where GPU prices have already jumped 35% in three months.
**Local inference:** 16GB VRAM class per card; single card serves 27B-class Q4 quants; 2–3 card rigs (32–48GB) target Qwen3.8-Flash-Next IQ3_S at 128K–256K context via Strata with DDR5 KV streaming.
Source: GameGPU — "Users have started buying RTX 5060 Ti 16GB graphics cards en masse for the sake of AI" — published Nov 8, 2026 — https://en.gamegpu.com/news/zhelezo/polzovateli-nachali-massovo-skupat-videokarty-rtx-5060-ti-16gb-radi-ii
Source: r/LocalLLaMA — "Users have started buying RTX 5060 Ti 16GB graphics cards en masse for the sake of AI" (Nov 8 community thread) — https://www.reddit.com/r/LocalLLaMA/comments/1xr01bj/users_have_started_buying_rtx_5060_ti_16gb/

## Model releases

**Nace AI announces Drex Max v1.1 — 27B multimodal decision model tops Artificial Analysis**
Event date: 2026-11-08 (announcement; base v1 weights Nov 6, paper Nov 5).
Alti from Nace AI posted in r/LocalLLaMA that Drex Max v1.1 now achieves SOTA and the Pareto frontier for multimodal decision models on Artificial Analysis, with every previous Drex open-weight version on Hugging Face. Drex Max is a dense 27B-parameter backbone with a 255-way readout head: pass a context (`state`) plus named questions and it returns a probability for every option in one forward pass — no autoregressive decoding. The architecture mixes 48 Gated DeltaNet linear-attention layers with 16 full-attention layers, supports images, and runs with Python on NVIDIA GPUs or on Apple silicon via llama.cpp, Ollama, Kapitsa and MLX. Scores: 67.52 on the public Decision Index 0.4 (vs Jev 1.13.0's 57.96), 73.92 on the public vision index, 64 on the AA Decision Index; a typical request answers in ~183 ms vs ~2.0 s for GPT-6 Sol. v1.1 weights are "upcoming soon," being optimized for local inference.
**Why it matters:** Decision models replace long chain-of-thought generation with a single probability pass — no parsing, stable under option reordering — and Nace is iterating open weights rapidly in a space where local serving cost is a first-class metric.
**Local inference:** Q8_0 GGUF needs ~48 GB (Mac or dual-GPU); affine Q4 checkpoint runs on Apple silicon Macs with 24 GB+ (Kapitsa/MLX); 8-bit backbone loads in ~26 GB on GPU.
Source: r/LocalLLaMA — "Artificial Analysis: multimodal decision model Drex Max ranks #1" — Nov 8, 2026, 11:44 ET — https://www.reddit.com/r/LocalLLaMA/comments/1xr2rqk/artificial_analysis_multimodal_decision_model/
Source: Hugging Face — nace-ai/drex-max-v1 model card — Nov 6, 2026 — https://huggingface.co/nace-ai/drex-max-v1
Source: arXiv — "Towards Foundation Decision Models: Drex" (2611.07854) — submitted Nov 5, 2026 — https://arxiv.org/abs/2611.07854

## Community spins and quantizations

**RustingStrata Q5_K_M: 70 tok/s decode on RTX 3060 12GB vs llama.cpp's 43**
Event date: 2026-11-08.
Update on RustingStrata (pure-Rust Qwen GGUF engine, first reported Nov 4): the author benchmarked Unsloth's Qwen3.6-35B-A3B UD-Q5_K_M (25 GiB, MTP version) on an RTX 3060 12GB + Ryzen 5 7600X + 32GB DDR5, with about two-thirds of experts living on CPU. Decode runs 70 tok/s vs llama.cpp b12119's 43 (65 vs 43 after a 4K prompt); 4K prefill is ~1,870 vs 1,210; at 32K context, 1,470 prefill / 49 decode vs 1,030 / 29. With `--mtp 1`, RustingStrata reaches up to 96 tok/s (86 after 4K). In realistic chat (256-token outputs, thinking off): ~50 vs ~37 without MTP, and ~62 vs 45–56 with MTP enabled on both sides. The author also tested llama.cpp's `--moe-cache-mib 6000`, which lifts decode to ~48 tok/s — still behind.
**Why it matters:** As CPU-expert offload matures, a 12GB card plus cheap DDR5 now serves a 35B MoE at interactive speeds, and a pure-Rust challenger is pulling away from llama.cpp on hybrid quant MoE.
**Local inference:** RTX 3060 12GB + 32GB DDR5 (Q5_K_M 25 GiB, ~⅔ experts on CPU); Vulkan backend also tested (~60 tok/s decode, 85 with MTP).
Source: r/LocalLLaMA — "RustingStrata running Qwen3.6:35b-a3b with Q5 and MTP vs llama.cpp" — Nov 8, 2026, 11:48 ET — https://www.reddit.com/r/LocalLLaMA/comments/1xr2v59/rustingstrata_running_qwen3635ba3b_with_q5_and/

**LexiSuite AIO: bootable all-Rust OS/engine/kernel suite claims 5–25% over llama.cpp**
Event date: 2026-11-08.
A solo developer released LexiSuite AIO — a bootable USB suite (OS, Rust inference engine, Linux kernel 7.4-rc1, and tuning/training management) that runs Qwen3.8-27B with a claimed lossless 5–25% speed lead over llama.cpp on an RX 7900 XTX via Vulkan, attributed to using 91% of card bandwidth versus ~80%. The poster asks the community to flash it with balena etcher, run benchmarks, and report back.
**Why it matters:** Another pure-Rust challenger to llama.cpp on AMD, distributed as a ready-to-boot image rather than a build-from-source engine — lowering the barrier for Vulkan MoE serving.
**Local inference:** AMD RX 7900 XTX (Vulkan); bootable on x86 systems; targets Qwen3.8-27B-class models.
Source: r/LocalLLaMA — "LexiSuite AIO bootable all rust OS/Engine/Kernel..." — Nov 8, 2026, 11:12 ET — https://www.reddit.com/r/LocalLLaMA/comments/1xr20gs/lexisuite_aio_bootable_all_rust/

**Rumor: Moonshot Kimi roadmap — K3 Flash by EOY 2026, K4 at 5.4T by Q3 2027**
Event date: 2026-11-08 (rumor surfaced in-window).
An r/LocalLLaMA thread circulated a Moonshot roadmap rumor (via an X post): Kimi K3 Flash by end of 2026, replacing K2.8 and competing with DeepSeek V4.1 Flash; Kimi 3.5 with the same ~3T parameters but faster at reaching simple answers in early 2027; and Kimi K4 scaled to 5.4 trillion parameters in Q3 2027. Unverified.
**Why it matters:** A Flash-Next-class K3 Flash release would land directly in the single-GPU local-serving niche this brief tracks; the 5.4T K4 claim frames the next open-weight frontier jump.
**Local inference:** N/A for the rumor itself; K3-class models would target 16–32GB quantized serving (IQ3/UD tiers) once weights drop.
Source: r/LocalLLaMA — "Rumor: Moonshot Kimi roadmap" — Nov 8, 2026, 11:45 ET — https://www.reddit.com/r/LocalLLaMA/comments/1xr2t4c/rumor_moonshot_kimi_roadmap/

## Inference software and benchmarks

**llama.cpp b12202→b12211: SpacemiT MXFP4, Zamba2 SSM fast path, llamafile VXE — b12211 released**
Event date: 2026-11-08 (merges 08:29–09:49 ET; build b12211 published 11:48 ET).
Morning merge wave: SpacemiT IME2 MXFP4 dense repack enabled on CPU (#31776); CUDA `d_state=64` fast path added to the SSM_SCAN kernel for Zamba2-style recurrent-scan architectures (#31818); llamafile gains bf16 and q8_0 VXE implementations (#31866); CUDA/Metal radix Top-K made deterministic for tied selections with a thread-timing fix (#31084); mtmd consolidated by removing clip.h (#31865); and moe-cache now ensures the CPU part does not wait for cached experts (#31888). Build b12211 was released at 11:48 ET.
**Why it matters:** MXFP4 on SpacemiT's CPU line and the llamafile VXE kernels widen cheap CPU/local inference options; the moe-cache tuning continues last night's `llama_moe_cache` feature (reported Nov 7).
**Local inference:** All backends; SpacemiT CPU MXFP4 targets domestic Chinese CPU boards; SSM fast path benefits Mamba/Zamba2 hybrid models on CUDA; moe-cache fixes apply to any CPU+GPU split rig.
Source: GitHub — ggml-org/llama.cpp commits and release b12211 — Nov 8, 2026, 11:48 ET — https://github.com/ggml-org/llama.cpp/releases/tag/b12211

**vLLM morning wave: Muse Spark cleanup, sleep-mode buffer offload, ROCm Qwen4Exp fix, MOSS-Transcribe-Diarize**
Event date: 2026-11-08 (7 merges 09:11–12:40 ET).
In-window merges: Muse Spark Open cleanup (#66334); sleep mode now offloads model-runner construction buffers (#59994); model inspection RPC moved out of `LLM.__repr__` (#65866); Step3-VL encoder output aliasing fixed with shared CUDA graph capture buffer guards (#65351); ROCm padding for Qwen4Exp PLE n-gram inputs for piecewise CUDA graph replays (#62675); MOSS-Transcribe-Diarize switched to the upstream config (#66388); XPU RoPE buffer dtype preserved for privacy-filter model tests (#65323). SGLang merged request_logger media-input summarization (#48800) at 08:35 ET.
**Why it matters:** The sleep-mode and buffer offloads directly affect VRAM-constrained serving boxes; the Muse Spark and Qwen4Exp fixes matter for Blackwell/ROCm local clusters.
**Local inference:** GPU serving stacks (CUDA/ROCm/XPU); sleep-mode improvements target boxes with limited VRAM running multi-model serving.
Source: GitHub — vllm-project/vllm commits — Nov 8, 2026, 09:11–12:40 ET — https://github.com/vllm-project/vllm/commits/main

**Also noted** (verified, minor)
- Motion-graphics demo: Qwen3.8-Flash-Next GSQ RCO IQ3_XXS running on Strata with an RTX 4080 16GB + 64GB DDR5 (r/LocalLLaMA, 12:23 ET) — https://www.reddit.com/r/LocalLLaMA/comments/1xr3qes/motion_graphics_video_made_with_qwen38_flash_next/
- Qwen3.5-0.8B ran in pure assembly on an AMD EPYC 7B12 single thread: 6.51 tok/s vs the Transformers reference at 4.34, all 32 tokens matching (r/LocalLLaMA, 10:39 ET) — https://www.reddit.com/r/LocalLLaMA/comments/1xr18t3/i_ran_qwen3508b_in_pure_assembly/
- Build report: 4x Radeon AI PRO R9700 + 2x RTX 3090 on one x16 slot using a PCIe switch (r/LocalLLaMA, 11:23 ET) — https://www.reddit.com/r/LocalLLaMA/comments/1xr2a6y/4x_radeon_ai_pro_r9700_2x_rtx_3090_on_one_x16/

**Just missed the window**
- `missed:` Tencent **Qwen3.8-27B-DSpine-b16** — five-layer draft model (block size 16, 8,192-token sliding window) uploaded to HF Nov 7; runtimewire coverage (~Nov 7 evening) reports up to 3.84x agent throughput (WorkBuddy Office, xhigh reasoning) vs autoregressive decoding, with acceptance lengths beating official DFlash2 across general and agent benchmarks. In-window r/LocalLLaMA discussion at 11:23 ET — https://huggingface.co/tencent/Qwen3.8-27B-DSpine-b16 · https://www.reddit.com/r/LocalLLaMA/comments/1xr2aaa/tencentqwen3827bdspineb16_hugging_face/
- `missed:` **llama-modchip v2.4.0** hit the HN front page at 05:22 ET (142 points): 284.33B DeepSeek-V4-Flash (13.17B active) runs on 64GB DDR5 with zero GPUs via custom Fractal-64 (Q2_0, 3.75 bpw) tensors and a patched llama.cpp fork — 44.36 tok/s prefill, 9.06–10.74 tok/s decode on a Ryzen 9 9955HX. The v2.4.0 release itself landed Nov 4 — https://github.com/synchopate/llama-modchip · https://news.ycombinator.com/item?id=50411961
- `missed:` nace-ai **Kapitsa quant batch** — Nemotron 3.5 Lightning 30B / Super VL 120B / Bolt 74B / Dory 4B NVFP4 quants for Apple Silicon local inference, repos created 06:44 ET — https://huggingface.co/nace-ai

**Coverage notes**
- **Verified:** 7 report items (1 lead hardware, 1 model release, 3 community, 2 software waves) + 3 "Also noted" one-liners + 3 "Just missed" footnotes. Counting unit: distinct underlying events with in-window disclosure or verified in-window coverage.
- **Ledger dedupe:** 4 candidates suppressed — Nvidia "1% of $70B China GPU orders" (Tom's Hardware, ~Nov 4; matches Nov 20 ledger entry on the RTX PRO 5500 fill-rate story); npu-render Windows 11 NPU desktop driver (GitHub Oct 26 / press ~Nov 4, out of window); QFN aliyuncs URL follow-up thread (continuation of the Nov 7 reported incident); GPT-6 Luna/Sol pricing HN post (in-window but out of scope — no hardware/local-model tie).
- **Thin Sunday window:** mainstream hardware outlets were quiet; the STH /news index failed (redirects to a legacy news page) and VideoCardz /news 404'd — both logged as dead indexes. Discovery relied on the r/LocalLLaMA new-feed sweep, HN Algolia, GitHub commit/release APIs, and the Hugging Face API.
- **Dead ends:** several web_search queries returned empty DDG results (paid fallback served); the DSpine article timestamp was ambiguous ("18 hours ago" at search time) — resolved via the HF repo date (Nov 7) and runtimewire text, placing disclosure just pre-window.
