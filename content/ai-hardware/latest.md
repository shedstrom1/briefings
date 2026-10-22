# AI Hardware & Local Models Brief — Nvidia bankrolls the substrate shelf; Unsloth ships day-0 MOSS-Image quants

**Window:** 2026-10-21 17:45 → 2026-10-22 07:45 EDT (previous fire → this fire)

## TL;DR

- **Nvidia inked a wave of supply-chain capacity deals at GTC Berlin** — Samsung Electro-Mechanics joins its substrate chain, Amkor's Korean interposer capacity jumps 300%+, and co-packaged-optics engine output targets 15x by mid-2027, all aimed at the "capacity is the constraint" problem gating every AI box, local ones included.
- **Unsloth released day-0 quants of MOSS-Image**, the OpenMOSS 5.1B Apache-2.0 text-to-image model: INT8-ConvRot and FP8 transformer at 5.42 GB (vs 10.16 GB bf16) plus an 11.49 GB INT8-ConvRot text encoder — a layout-aware T2I stack that fits a 16 GB card.
- **llama.cpp shipped b11802–b11815 overnight**, headlined by KleidiAI Q8_0 SVE dotprod GEMV and SVE i8mm GEMM kernels (ARM CPU paths: DGX Spark, Snapdragon X, Raspberry Pi) and WebGPU Q2_0 support.
- **A draft llama.cpp PR (30971) claims to beat Strata's MoE decode on a single AMD GPU + 128 GB RAM** with an "adaptive single-graph MoE offload" — the community's answer to vendor-tier expert streaming.
- vLLM and SGLang both landed DeepSeek-V4.1-Flash decode work overnight; vLLM also **reverted the 7.6x ROCm attention build speedup** reported yesterday.

## AI hardware and silicon

**Nvidia signs Samsung Electro-Mechanics, commits cash to substrates, packaging, test and optics — capacity is "the constraint"**
*Event: announced at GTC Berlin, October 21; Register dispatch 2026-10-22 01:54 UTC (Oct 21 21:54 EDT, just inside the window's opening minutes).*
Jensen Huang told the Berlin keynote audience that demand is extraordinary but "capacity is the constraint," and Nvidia backed that with signed capacity agreements across six suppliers. Substrate deals with IBIDEN, Samsung Electro-Mechanics and Unimicron will in aggregate drive a **2.5x substrate capacity increase over three years**: IBIDEN's Ono plant (already on a multibillion-dollar Nvidia reservation advance) targets 3x, SEMCO joins the chain with immediate Korean capacity plus new Sejong/Busan fabs (The Register reports roughly a quarter of SEMCO's substrate output headed to Nvidia by 2028), and Unimicron's KF expansion ramps Q2-2027 to full capacity mid-2028. Amkor is expanding Korean interposer (advanced packaging) capacity **300%+ by end of 2028**; a **$1.1 billion Nvidia capacity-reservation advance to KYEC** funds new US test facilities (high-volume production April 2027, with Singapore test ramping February 2027); and SPIL's optical-engine capacity for co-packaged optics targets **15x by mid-2027 with another doubling by end-2028**. Reuters reported the SEMCO package (shared with Tesla) at ₩3.7 trillion (~$2.8B) combined.
*Why it matters:* every local-inference roadmap — DGX Spark successors, RTX-class cards, Rubin-era workstation boxes — is gated by substrates, packaging and test, the unglamorous layers where Nvidia's cash now buys shelf space years ahead. The CPO bet (15x optical engines) is also the strongest signal yet that co-packaged optics is arriving on the timeline that home-lab interconnect pricing will feel.
*Local inference:* near-term effect is supply-side (availability/price of Blackwell-and-later consumer and workstation silicon); the co-packaged optics scale-up is what eventually bends the cost curve for clustered local serving rigs.
Sources:
- The Register — "To help world gorge on GPUs, Nvidia brings cash bucket to fatten wafer-thin supply chain," 2026-10-22 01:54 UTC — https://www.theregister.com/ai-and-ml/2026-10-22/to-help-world-gorge-on-gpus-nvidia-brings-cash-bucket-to-fatten-wafer-thin-supply-chain/5304623
- NVIDIA Newsroom — "NVIDIA and Supply Chain Partners Expand Capacity to Accelerate AI Infrastructure Buildout," October 21, 2026 (GTC Berlin) — https://nvidianews.nvidia.com/news/nvidia-and-supply-chain-partners-expand-capacity-to-accelerate-ai-infrastructure-buildout
- Reuters — "Samsung Electro-Mechanics gets $2.8 billion chip investment from Tesla and Nvidia, report says," October 22, 2026 — https://www.reuters.com/business/autos-transportation/samsung-electro-mechanics-gets-28-billion-chip-investment-tesla-nvidia-report-2026-10-22/

## Model releases

No new open-weight base releases verified inside the window. Trending-page candidates (Sage-A1.5, OrcaCyber-Nano-1.0, Reflection Beam-1.0, MOSS-Image base, DeepSeek-V4.1-Flash-1017) all carry creation dates of October 11–17 and were suppressed as already-reported or re-covered.

## Community spins and quantizations

**Unsloth ships day-0 GGUF + FP8/INT8-ConvRot quants of MOSS-Image (OpenMOSS 5.1B text-to-image)**
*Event: uploaded 2026-10-22 00:04 UTC (20:04 EDT Oct 21, in-window).*
Four repos — `unsloth/MOSS-Image-Base-GGUF`, `unsloth/MOSS-Image-SFT-GGUF`, `unsloth/MOSS-Image-Base-FP8`, `unsloth/MOSS-Image-SFT-FP8` — quantize the Apache-2.0 MOSS-Image family (arXiv 2610.23374): a 5.1B transformer with a Qwen3.5-9B text encoder and the FLUX.2 small-decoder VAE. The recommended INT8-ConvRot transformer lands at **5.42 GB vs 10.16 GB bf16** (LPIPS 0.058 vs bf16, against 0.111 plain INT8 and 0.163 FP8), and the Qwen3.5-9B text encoder quantizes to **11.49 GB INT8-ConvRot (from 16.8 GB bf16)**, byte-shared between the Base and SFT variants. GGUF builds target CPU, Mac and smaller GPUs; the model needs transformers ≥5.3 and ships with JSON-layout prompt control (rendered text placement), with a native ComfyUI plugin already published via Comfy-Org.
*Why it matters:* MOSS-Image has been the fastest-rising open T2I model of the week (trending #3 with 6.9k downloads on the SFT repo), and day-0 quants mean the local community can run it while the team is still responding to issues — the Unsloth Studio pre-quantized path also skips the bf16 download entirely.
*Local inference:* 5.42 GB transformer + 11.49 GB quantized encoder runs on a single 16 GB card (RTX 5060 Ti 16GB class); GGUF covers CPU/Mac fallback.
Sources:
- Hugging Face — unsloth/MOSS-Image-SFT-FP8 (card incl. LPIPS table; repo created 2026-10-22 00:04 UTC) — https://huggingface.co/unsloth/MOSS-Image-SFT-FP8
- Hugging Face — unsloth/MOSS-Image-SFT-GGUF — https://huggingface.co/unsloth/MOSS-Image-SFT-GGUF
- Hugging Face — OpenMOSS-Team/MOSS-Image-SFT (upstream, Apache-2.0) — https://huggingface.co/OpenMOSS-Team/MOSS-Image-SFT

**Update on previously reported (RecursiveSI/flint): MLX mixed 3/4-bit builds land for 24–32 GB Macs**
*Event: 2026-10-22 05:25 UTC (Nav8ha) and 08:29 UTC (mlx-community mirror).*
New `flint-mixed-3-4bit` MLX quant layers the reported 39.8B-A2.5B coding-agent MoE (RecursiveSI/flint, reported Oct 21) at mixed 3/4-bit, tagged explicitly for 24 GB and 32 GB Apple Silicon — the quant tier the day-0 mlx-int4 release left open.
*Why it matters:* flint was the day's hottest Apache-2.0 coding MoE; the mixed-precision MLX tier is what makes it a daily driver on base-class M4 Macs rather than 64 GB rigs.
*Local inference:* targets M-series Macs with 24–32 GB unified memory.
Sources:
- Hugging Face — mlx-community/flint-mixed-3-4bit (created 2026-10-22 08:29 UTC) — https://huggingface.co/mlx-community/flint-mixed-3-4bit
- Hugging Face — Nav8ha/flint-mixed-3-4bit (created 2026-10-22 05:25 UTC) — https://huggingface.co/Nav8ha/flint-mixed-3-4bit

## Inference software and benchmarks

**llama.cpp b11802–b11815: ARM gets serious — KleidiAI SVE kernels, plus WebGPU Q2_0**
*Event: b11815 published 2026-10-22 09:32 UTC; in-window merge series.*
The overnight batch's headline is ARM: **KleidiAI Q8_0 SVE dotprod GEMV and SVE i8mm GEMM kernels (#30808)** accelerate quantized linear algebra on Armv9 CPUs — the DGX Spark's Cortex cores, Snapdragon X laptops and Raspberry Pi-class boards. Also landed: **WebGPU Q2_0 support (#30914)** (browser/iGPU ultra-low-bit tier), SYCL CONV_2D correctness fix and merged fp16/fp32 dequant switches, non-standard GQA + BPE tokenizer support for Nemotron models plus domyn-small pre-typing (#23343), an optional FC input scale for dflash drafters (speculative decoding), OpenCL F32→I32 argmax, LoRA prism.hadamard folded-weight support, and a `clean_spaces` GGUF vocab key.
*Why it matters:* SVE i8mm/dotprod kernels are the highest-leverage CPU-inference change this month for ARM-based local boxes, and Q2_0-on-WebGPU opens 2-bit-class models to no-install iGPU demos.
*Local inference:* direct speedups for DGX Spark CPU paths, Snapdragon X NPU-adjacent CPU serving, and any Armv9 board running Q8_0 GGUFs.
Sources:
- GitHub — llama.cpp release b11815, published 2026-10-22 09:32 UTC — https://github.com/ggml-org/llama.cpp/releases/tag/b11815

**Draft PR 30971: "adaptive single-graph MoE offload" aimed at beating Strata decode on one AMD GPU + 128 GB RAM**
*Event: opened 2026-10-22 09:48 UTC (in-window), draft/discussion.*
A solo contributor opened **llama.cpp PR #30971 ("llama : add adaptive single-graph MoE offload")**, self-described as a deliberately scrappy build whose stated goal "was to actually beat Strata on decode performance" on the author's **R9700-class single GPU with 128 GB DDR5** — using aggressive CPU-core "shenanigans" to keep expert traffic resident. The author concedes it has "too many changes to ever be completely viable," positioning it as a competitive benchmark that pressures the maintainers' offload design (and Strata's per-GPU role model reported yesterday).
*Why it matters:* MoE expert streaming on budget RAM is the current frontier for single-card local serving; open draft PRs with hard numbers keep llama.cpp's offload path honest against closed alternatives.
*Local inference:* pitched at single AMD GPU + 128 GB DDR5 rigs — the exact profile of 16 GB-card homelab boxes.
Sources:
- GitHub — ggml-org/llama.cpp PR #30971, created 2026-10-22 09:48 UTC — https://github.com/ggml-org/llama.cpp/pull/30971

**vLLM overnight: DeepSeek-V4.1-Flash specialized decode on GB300, SM90 FlashMLA sparse prefill — and the 7.6x ROCm build speedup reverted**
*Event: merges 2026-10-21 21:45 → 2026-10-22 11:45 UTC.*
Highlights: **[#61868] DSv4.1-Flash specialized decode on GB300**, **[#61615] small-head FlashMLA sparse prefill enabled for DSv4.1 on SM90 (Hopper)**, **[#62994] ROCm MiniMax-M3 MonoKernel serving EAGLE3 verify steps**, **[#62622] Muse Spark Open on ROCm**, and **[#62980] online-quantization support for Kimi K3 NVIDIA projections**, plus a Rust-frontend batch (DeepSeek DSML marker matching, attributed tool-parser deltas). **Update on previously reported (Oct 21, "7.6x ROCm attn build"): #62983 reverts the parallel compile of paged-attention kernels** — ROCm attention builds are back to serial while it's reworked.
*Why it matters:* the GB300/SM90 decode+prefill pair pushes V4.1-Flash latency toward interactive territory on hardware owners actually have (H100/H200), while the revert reminds everyone that ROCm build wins and stability still trade places.
*Local inference:* SM90 gains help the secondhand-Hopper crowd; the Kimi K3 online-quant fix removes a VRAM-alloc snag for 128 GB-class multi-GPU rigs.
Sources:
- GitHub — vllm-project/vllm merged PRs (window-verified via search API) — https://github.com/vllm-project/vllm/pull/61868 · https://github.com/vllm-project/vllm/pull/61615 · https://github.com/vllm-project/vllm/pull/62994 · https://github.com/vllm-project/vllm/pull/62983

**SGLang overnight: DeepSeek-V4.1 MXFP8 fusion series, chunked MLA prefill capture, Kev decision models**
*Event: merges 2026-10-21 21:45 → 2026-10-22 11:45 UTC.*
The dsv41 perf series fused the **q_norm MXFP8 quantize at every row count (#45633)**, landed "mega-mHC + wo_a einsum" MXFP8 writes (#45632), gathered V4.1 index-K slots once per prefill graph replay (#45741), and reordered **DSpark prefill read-done publishing (#45682)**; cuda-graph MLA prefill capture now defaults to chunked size capped at 8192 (**#45565**). Feature side: **systemone now serves Kev pointer-head decision models (#45413)**, diffusion gains **FastH3 Omni 8-step PDD support (#45530)**, and AMD users get **fp8 quantization enabled on gfx1250 for diffusion (#45304)**, alongside MI355X/GB300 AgentX cookbook recipes for Muse Spark Open.
*Why it matters:* SGLang is where V4.1-Flash's deployment numbers are actually being squeezed (yesterday's 2.3x H200 lineage continues), and the Kev serving path extends the "small decision model drives the agent loop" pattern from yesterday's Mica release into a mainstream engine.
*Local inference:* chunked MLA capture lowers activation VRAM ceilings for long-context sessions; gfx1250 fp8 extends local image-diffusion to newer RDNA silicon.
Sources:
- GitHub — sgl-project/sglang merged PRs (window-verified via search API) — https://github.com/sgl-project/sglang/pull/45413 · https://github.com/sgl-project/sglang/pull/45565 · https://github.com/sgl-project/sglang/pull/45304

**Also noted — Ollama v0.40.9-rc1 and TensorRT-LLM v1.4.0rc1 both landed in-window**
Ollama tagged **v0.40.9-rc1 at 2026-10-22 01:33 UTC** (follow-up to yesterday's day-0 `ollama run flint`), and **NVIDIA TensorRT-LLM cut v1.4.0rc1 at 01:38 UTC**, the first 1.4 candidate since rc0 on Oct 14 (https://github.com/ollama/ollama/releases · https://github.com/NVIDIA/TensorRT-LLM/releases).

## Just missed the window (footnoted once)

- **Google's TorchTPU: TPUs running PyTorch inference in production at Meta, vLLM/SGLang TorchTPU engines open-sourced** — disclosed October 20–21 at PyTorch Conference (Google blog Oct 20; RadixArk's "Meta running TPU inference in production" post Oct 21); Tom's Hardware's October 22 10:00 UTC write-up added Inferact's claim of **700+ tok/s single-user Kimi K3 (2.8T) on 16 Ironwood chips with speculative decode vs 452 tok/s on 16 GB200s** — event predates this window, so it footnotes only. — https://www.tomshardware.com/tech-industry/artificial-intelligence/googles-tpus-for-ai-workloads-now-run-in-at-metas-data-centers-tpus-sold-to-external-customers-for-the-first-time-threaten-nvidias-market-dominance · https://cloud.google.com/blog/topics/ai-infrastructure/torchtpu-is-now-open-source-run-native-pytorch-on-tpus
- **AMD + Intel fold ZenDNN into oneDNN: a single PyTorch code path for x86 CPU AI inference** (Tom's Hardware, Oct 21 12:00 UTC; Intel newsroom same day; claimed 1.24x FP32 / 2.21x BF16 inference uplift) — disclosed pre-window, footnoted once. — https://www.tomshardware.com/pc-components/cpus/amd-and-intel-will-work-together-to-create-a-single-pytorch-code-path-for-ai-inference-on-cpus-amd-zendnn-library-gets-folded-into-intel-onednn-to-make-cpu-ai-simpler

## Coverage notes

- **Verified items this run:** 8 reported (1 hardware, 2 community/quant, 5 software/benchmark lines) + 2 one-time footnotes. Every item's URL fetched; the lead was read in full; numbers traced to primary sources (NVIDIA newsroom PR, HF model cards, GitHub APIs).
- **Ledger dedupe:** 12 candidates suppressed as already-reported or re-covered (vLLM Cohere2MoE dual-stream, SGLang v0.5.22, Ollama flint day-0, DeepSeek-V4.1-Flash-1017 trending, Sage-A1.5 (Oct 15), OrcaCyber-Nano (Oct 16), Reflection Beam (Oct 16), MOSS-Image base weights (Oct 11), GTC-Berlin open-models wave, DSX POD, Intel GPU D-series, flint GGUF lastModified refresh). Both footnotes above are newly tagged `missed:` and will never recur.
- **Dead ends (logged, not chased):** r/LocalLLaMA JSON and RSS both 403'd (captcha wall) — only two posts surfaced via HTML render; the PR-30971 lead was verified directly on GitHub instead. TechPowerUp news.xml 403; MLCommons /news 404; ServeTheHome published nothing in-window (latest post Oct 21 16:30 UTC). An r/LocalLLaMA claim that "Ling-3.1-flash is getting a 1019 update with open weights to follow" couldn't be corroborated by a second source — excluded. The RadixArk TPU tweet's exact time was unresolvable, which is why the TorchTPU item footnotes rather than leads.
