# AI Hardware & Local Models Brief — Oct 23, 2026 (12:45 ET Edition)

**Window:** 2026-10-23 07:45 → 12:45 EDT (previous fire → now) | 10 items verified · 2 footnote-only

## TL;DR

- **Hundreds of RTX 5090s exposed heading to Chinese "AI conversion" factories** — HKEPC footage shows production-line teardowns (PNY, Gigabyte, ASUS, even Founders Edition) reborn as dual-slot blower AI cards; 32GB GDDR7 retained, no verified 64/96GB mod exists.
- **Ollama v0.40.10-rc0 (11:03 EDT)** ships day-0 `nemotron-3.5-bolt:e4b` with 128K context and a DFlash draft model.
- **Maya-XS posts first runs of DeepSeek-V4.1-Flash with all 384 experts GPU-resident (120.6 GB) on a 128 GB Ryzen AI Max**; **TensorSharp** claims 4.4–5.5× llama.cpp prefill on 5× A40.
- **llama.cpp b11838** (ggml time-overflow fix) plus an in-window CUDA **chunked-graphs** merge (#30804); vLLM and SGLang landed 12 and 16 in-window PRs.
- **AgentionAI Gyro Coder** — new coding finetune of Qwen3.8-Flash-Next, half the experts at higher bits, fits 32GB.

## AI hardware and silicon

**RTX 5090's missing supply: massive Chinese AI-conversion factory exposed, Founders Edition included** — event date: October 23, 2026 (HKEPC footage; VideoCardz published 10:00 EDT).
HKEPC-shared video shows a large Chinese workshop running production-line disassembly of hundreds of retail GeForce RTX 5090s — PNY, Gigabyte, ASUS and NVIDIA Founders Edition cards — stripping GPU and memory chips onto custom PCBs with compact dual-slot blower coolers for server/multi-GPU rigs. Unlike the RTX 4090→48GB era, there is still no publicly verified capacity mod: the converted cards keep the original 32GB GDDR7, and claimed 64GB/96GB boards have never been shown working. VideoCardz notes NVIDIA has taken no visible action against conversions, unlike the LHR anti-mining push, since AI performance is now core GeForce marketing; wccftech (11:56 EDT) adds that premium ROG Astral 5090s are circulating at ~$10,000 and that NVIDIA recently refuted discontinuation rumors.
*Why it matters:* the 5090 is the flagship single-card local-AI platform (32GB GDDR7), and this is the clearest evidence yet of how much Blackwell flagship supply is being absorbed by AI server conversion — a structural drain on the enthusiast/homelab pool that also feeds the export-restriction gray market.
*Local inference:* the converted cards remain 32GB-class — same LLM footprint ceiling as retail 5090s; blower form factor is what changes (dense multi-GPU servers); ~$10k street for premium variants.
Source: VideoCardz — "Here's where the last RTX 5090 cards are going: Massive AI conversion factory exposed in China, Founders Edition included," Oct 23, 2026 14:00 GMT — https://videocardz.com/newz/heres-where-the-last-rtx-5090-cards-are-going-massive-ai-conversion-factory-exposed-in-china-founders-edition-included
Source (2nd): wccftech — "The RTX 5090 Isn't Gone, It's In Chinese Labs, Where Technicians Rip Off Its Die And 32GB VRAM For AI Servers," Oct 23, 2026 (~11:56 EDT) — https://wccftech.com/the-discontinued-rtx-5090s-are-entering-chinese-labs-where-they-are-turned-into-ai-gpus/

**GIGABYTE WRX90 AI TOP: Threadripper PRO board ships with an active dual-fan DDR5 cooler** — event date: October 23, 2026 (VideoCardz 09:03 EDT; GIGABYTE product page live).
GIGABYTE's workstation board for AMD sTR5 Threadripper PRO pushes its "AI TOP" branding: a 32+3+3+3-phase VRM with 110A power stages on a 14-layer PCB, PCIe 5.0, 10GbE, DDR5 ECC — and a dual-fan cooling module clamped beside the DIMM slots that GIGABYTE advertises as cutting memory temperatures by up to 43°C (VideoCardz's headline reads it as keeping memory under 43°C).
*Why it matters:* high-capacity DDR5 is the cheap path to big unified-model rigs (128GB Strix Halo and Ryzen AI Max set the template), and heat throttling of dense DIMM packs is a real stability wall for long local-inference sessions — vendor-addressed thermal headroom is a genuine enabler.
*Local inference:* targets multi-GPU Threadripper PRO inference workstations; ECC RDIMM-heavy configs where sustained decode must not thermally hiccup.
Source: VideoCardz — "GIGABYTE WRTX90 AI TOP Threadripper motherboard comes with massive cooler to keep memory under 43°C," Oct 23, 2026 — https://videocardz.com/newz/gigabyte-wrtx90-ai-top-threadripper-motherboard-comes-with-massive-cooler-to-keep-memory-under-43c
Source (2nd): GIGABYTE — WRX90 AI TOP product page — https://www.gigabyte.com/Motherboard/WRX90-AI-TOP

## Model releases

*(No fully in-window model-weight drops — the day's big release, NVIDIA Nemotron 3.5 Bolt E4B, landed minutes-to-hours before the window; see footnotes.)*

## Community spins and quantizations

**AgentionAI Gyro Coder: coding-specialized Qwen3.8-Flash-Next spin fits 32GB** — event date: October 23, 2026 (announced on r/LocalLLaMA 11:41 EDT).
AgentionAI's Gyro-Coder Large is a coding finetune of Qwen3.8-Flash-Next built on the same total size as Gyro-S but with half the experts and more bits per expert, landing at a 32GB VRAM footprint. The developer's own harness (8 auto-graded coding tasks, 2 seeds each, thinking on, 64K context) scored 13/16 for the quant, versus 15/16 for the same expert set at 8-bit and for Gyro-Coder-L.
*Why it matters:* the expert-count/bits tradeoff is the live design axis for MoE quants on 32GB cards — a half-expert, higher-bit recipe that holds coding scores is a template other merges will copy.
*Local inference:* explicitly sized for one 32GB GPU (RTX 5090 class); the Q4_K_M quant is what community testers are pulling.
Source: r/LocalLLaMA — "Qwen 3.8 Flash Next Gyro Coder is out!," Oct 23, 2026 11:41 EDT — https://www.reddit.com/r/LocalLLaMA/comments/1xdhj4z/qwen_38_flash_next_gyro_coder_is_out/
Source (2nd): HF — agentionai/Qwen3.8-Flash-Next-Gyro-Coder-GGUF — https://huggingface.co/agentionai/Qwen3.8-Flash-Next-Gyro-Coder-GGUF

## Inference software and benchmarks

**Ollama v0.40.10-rc0 lands with day-0 Nemotron 3.5 Bolt E4B** — event date: October 23, 2026, 11:03:59 EDT (GitHub release).
The rc0 release adds `ollama run nemotron-3.5-bolt:e4b` — NVIDIA's Gemma-4-based compact model (128K-token context per Ollama's library entry) — with a bundled DFlash draft for speculative decoding.
*Why it matters:* Ollama's day-0 turnaround keeps the lowest-friction local runtime aligned with vendor launch cadence, and DFlash drafting in one `ollama run` moves spec-decode to default UX for single-box users.
*Local inference:* E4B-4GB targets low-memory/low-TTFT boxes — runs on 8GB-class hardware and up; drafter accelerates conversational decode on entry-level GPUs and CPUs.
Source: GitHub — ollama/ollama v0.40.10-rc0 release notes — https://github.com/ollama/ollama/releases/tag/v0.40.10-rc0

**Maya-XS runs DeepSeek-V4.1-Flash with all 384 experts resident on a 128GB Ryzen AI Max** — event date: October 23, 2026 (post 07:49:10 EDT, four minutes after window open).
DotaMate's community post announces Maya-XS support for DeepSeek-V4.1-Flash: the whole model loads in 120.6 GB with all 384 routed experts resident in GPU memory on a 128 GB AMD Ryzen AI Max (Strix Halo), adds tensor-parallel operation across several GPUs, and much faster drafting on a single GPU. Commenters countered that plain llama.cpp already runs comparable models on 128GB Strix Halo, positioning Maya-XS as a specialist engine (IQ1_S/IQ1_M tier discussion) rather than a necessity.
*Why it matters:* full expert residency kills the offload jitter that makes big MoEs feel slow on unified-memory boxes, and a third serious engine for Strix Halo deepens the post-llama.cpp ecosystem.
*Local inference:* the headline target is the 128 GB Ryzen AI Max ~$2,000-class mini-PC; all-expert-resident at 120.6 GB fits with ~7GB headroom.
Source: r/LocalLLaMA — "Maya-XS runs DeepSeek-V4.1-Flash in 120.6 GB, with all 384 experts resident in GPU memory on a 128 GB Ryzen AI Max," Oct 23, 2026 07:49 EDT — https://www.reddit.com/r/LocalLLaMA/comments/1xdbtvq/mayaxs_runs_deepseekv41flash_in_1206_gb_with_all/

**TensorSharp: developer posts 5× A40 DeepSeek-V4.1-Flash numbers — 4.4–5.5× llama.cpp prefill** — event date: October 23, 2026 (post 11:47 EDT).
TensorSharp's author benchmarked DeepSeek-V4.1-Flash-1017 (Unsloth UD-IQ2_XXS, ~300.5 GB over six shards, 125.9 GiB Engram table host-mapped) on five A40s over PCIe, TP=5 expert sharding, versus Unsloth's dsv41-all llama.cpp fork. With default YOCO enabled: prefill 960.5 vs 174.6 tok/s at ~1.8K tokens (5.50×) and 1,000.7 vs 225.7 at ~7.3K (4.43×); decode 35.5 vs 29.6 (1.20×); with the DSpark drafter 55.0 vs 41.3 (1.33×). YOCO-off still held 2.4–3.2× prefill, and the author flags a measurable YOCO quality trade-off with both configs published.
*Why it matters:* on budget 48GB homelab cards, prompt-processing is the pain point for agent workloads — a 4–5× prefill win on identical GGUFs and hardware is the strongest reason yet to trial a second engine, and the YOCO quality disclosure is unusually honest methodology.
*Local inference:* targets 5× A40/48GB-class PCIe rigs (~$2–3k used market); decode parity means the win is prefill-heavy (RAG/agents), not chat.
Source: r/LocalLLaMA — "DeepSeek V4.1 Flash 1017 on 5× A40: TensorSharp vs llama.cpp, with YOCO on/off and quality measurements," Oct 23, 2026 11:47 EDT — https://www.reddit.com/r/LocalLLaMA/comments/1xdho58/deepseek_v41_flash_1017_on_5_a40_tensorsharp_vs/

**llama.cpp in-window: CUDA chunked graphs (#30804), Hexagon CEIL, b11838 time-overflow fix** — event date: October 23, 2026 (b11838 tag 09:37 EDT; #30804 merged 12:26:16 EDT).
Merges since the last fire: CUDA "chunk graphs to amortize graph launch" (#30804, 12:26 EDT) — batching graph launches to cut per-step launch overhead; Hexagon NPU gains a CEIL op (#31006, 10:46 EDT); and b11838 shipped the `ggml_time_us` time-overflow fix (#30969) plus a new CI behavior-check workflow (#31036).
*Why it matters:* graph-launch amortization attacks fixed overhead that caps small-batch decode on every CUDA card, and Hexagon op coverage keeps creeping toward fuller Snapdragon X/NPU model support.
*Local inference:* CUDA users across all VRAM classes get the launch-overhead win; Hexagon CEIL extends Snapdragon-elite NPU builds (the Android arm64 Snapdragon bundle ships Adreno GPU + Hexagon NPU support).
Source: GitHub — ggml-org/llama.cpp commits + b11838 release — https://github.com/ggml-org/llama.cpp/releases/tag/b11838

**vLLM 12-PR morning wave: MiniMax-M3 B200 NVFP4 recipe, Humming MXFP4 MoE override, ROCm NIXL fix** — event date: October 23, 2026 (11:45–16:45 UTC merges).
Merged: a MiniMax-M3 recipe adding a B200 NVFP4 AgentX aggregated profile (#63163); quantization honor of MoE activation override for Humming MXFP4 (#62751); `cooperative_topk` sized by row length (#62171); ROCm UCX bumped to v1.23.0-rc1 so NIXL uses rocm_ipc (#62827); CPU GDN spec-decode conv-state layout fix (#63339); plus a run of Rust-frontend CI/wire-layout work (#63320/#63295/#59854).
*Why it matters:* the NVFP4 recipe extends Blackwell's native FP4 path to MiniMax's new open MoE, and rocm_ipc finally gives NIXL P2P on ROCm — both change what clusters can run efficiently.
*Local inference:* workstation relevance is mainly SM120-adjacent NVFP4 groundwork and the topk kernel; the ROCm fix matters for MI300/MI350 homelab NIXL setups.
Source: GitHub — vllm-project/vllm merged PR search, Oct 23 2026 — https://github.com/vllm-project/vllm

**SGLang 16-PR morning wave: LTX-2 video-diffusion perf series, Rust /abort_request, ComfyUI video-to-video** — event date: October 23, 2026 (11:45–16:45 UTC merges).
Highlights: a diffusion-performance run — LTX-2.3 BCG video RoPE offset fix (#45995), denoising-step host-overhead cut (#45966), image responses served from in-memory encoded bytes (#45987), residency-calibration probe deferred (#45979) — plus Rust-frontend `/abort_request` (#45888/#45899/#45914), ComfyUI server-mode video-to-video input with prompt enhancement (#43673), and HiCache MLA DCP with L3 (file/mooncake) backing (#41187).
*Why it matters:* SGLang is racing to be the local video-diffusion serving stack (LTX-2.3 is the go-to open video model), and MLA Deep-cache L3 via mooncake extends KV into cheap file-backed storage for long sessions.
*Local inference:* ComfyUI-side creators on single GPUs (LTX-2.3 fits in 16–24GB quantized) get faster steps and v2v workflows; HiCache L3 helps RTX 4090/5090 boxes sustain long contexts.
Source: GitHub — sgl-project/sglang merged PR search, Oct 23 2026 — https://github.com/sgl-project/sglang

## Also noted

- **mradermacher in-window quants** (all HF, i1/imatrix GGUF): K2-Horizon-7B-UnrealEngine (Blueprint/C++ codegen finetune, 10:39 EDT — https://huggingface.co/mradermacher/K2-Horizon-7B-UnrealEngine-i1-GGUF), Veloxy-1B + Veloxy-SLM-3B Turkish SLMs (09:52 / 11:58 EDT — https://huggingface.co/mradermacher/Veloxy-1B-i1-GGUF), SpaceQA-gpt-oss-20b (aerospace-QA LoRA quant, 11:40 EDT — https://huggingface.co/mradermacher/SpaceQA-gpt-oss-20b-i1-GGUF). All run on 8–16GB cards.

## Just missed the window

- **NVIDIA Nemotron 3.5 Bolt E4B (4GB) weights went live pre-window (~05:47 EDT):** Q4_K_M GGUF (6.18GB file), 8B-total/4B-effective Gemma-4-E4B post-trained for conversational/tool agents, 64K in+out, OpenMDW-1.1, with a companion DFlash drafter and BF16 twin; Unsloth's GGUF mirror created 05:47 EDT — first in-window consequence was Ollama's rc0 above. https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Bolt-E4B-4GB
- **Project Zero v0.2.0 (posted 12:45:17 EDT — 17 seconds past the wall):** CPU-only C inference engine now runs ternary (BitNet/Falcon, Ternary-Bonsai-27B), dense GGUF, DeepSeek-V2/Qwen3 MoE and Mamba-1 in one `pz` binary; DeepSeek-V2-Lite Q4_K_S improved to 11.7–12.6 tok/s (from ~6.3–7.3) vs llama.cpp's recorded 9.4–10.5 on a 4-vCPU AVX-512 Xeon, RAM 12.0→10.6 GB, plus `--parallel N` shared-forward batching (SmolLM2-1.7B: 12.7→29.8 tok/s) and built-in web UI + OpenAI-compat API. https://www.reddit.com/r/LocalLLaMA/comments/1xdj98z/a_few_months_after_my_earlier_post_project_zero/

## Coverage notes

- **Verified:** 10 items reported (all URLs fetched; page/API timestamps inside 07:45–12:45 EDT), plus 2 "Just missed the window" footnotes (Nemotron E4B, Project Zero). Lead stories read in full: RTX 5090 factory (both outlets), Nemotron E4B model card, TensorSharp and Maya-XS threads, llama.cpp/vLLM/SGLang merge lists, Ollama release notes.
- **Ledger dedupe:** 10 candidates suppressed as already reported — MiniMax-M3.1-Flash GGUFs, GLM-5.3-Flash-GGUF, RTX 5080 China hike re-coverage, Ollama v0.40.9 notes, two GPU-unlock discussion posts, Flint-3090 and Swift-1.5 engine questions, Baseten elephant-1 re-coverage (base Oct 19), Qotom Q51551UT (already `missed:`-tagged, not re-footnoted). The Oct-21 GTC Nemotron-family footnote's E4B half appears once, per the one-footnote rule.
- **Dead ends / limitations:** TechPowerUp news returned 403 (datacenter block); two r/LocalLLaMA attention-paper posts (Virtual Head / Continuum Attention) couldn't be timestamp-verified via the anonymous feed and were dropped; The Register's Maia-300 multi-gigawatt rack story checked out as Oct 12 (out of window). Machine-local clock confirmed EDT this run; all times ET unless labeled GMT/UTC.
