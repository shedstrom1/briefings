# AI Hardware & Local Models Brief — 2026-11-27 17:45 ET

**Window: 2026-11-27 12:45 ET → 2026-11-27 17:45 ET (new items only; deduped against the reported ledger).**

## TL;DR
- **SemiAnalysis's Trainium3 deep dive** puts real numbers on AWS's inference chip: 144 GB HBM per chip and 4.9 TB/s at roughly 30% of GB300's cost, yielding a 73.5% modeled profit margin at 135 tok/s/user versus 53% for GB300+SGLang.
- **A documented 101 tok/s from Inkling 2 Mini on a single GB10** (DGX Spark-class) in vLLM, with full 131K context and Qwen3.8-27B still resident — a reproducible five-step tuning recipe, not a vendor claim.
- **A 3-bit, 1.48 GiB WebGPU quant** (Pragma-1-4B-Mobile-Q3-WGSL) now runs a 3.78B hybrid model inside mobile Safari/Chrome; a 612B "derisked" Inkling-2-Small GGUF ladder landed the same afternoon.
- **In-window inference-stack waves**: vLLM added a Qwen4Exp SM100 fused kernel, ROCm 10.1 and a Mooncake EP backend; SGLang fused AMD gfx950 GEMM+all-reduce and added NGRAM PD-drafting; TensorRT-LLM enabled breakable prefill CUDA graphs for multimodal.

## AI hardware and silicon

**SemiAnalysis publishes a first-principles Trainium3 inference microarchitecture deep dive** — Event date: 2026-11-27 (surfaced on HN ~16:02 ET, in window).
The paid report measures Trainium3 against GB300 using its open UBenchX/InferenceX suite. Each Trainium3 chip has 8 NeuronCore-v4s and 144 GB HBM (half a GB300 GPU's capacity), 2,517 TFLOPS FP4 (17% of Blackwell Ultra's ~15,000) but 4.9 TB/s bandwidth (61% of GB300's 8.0 TB/s) at about 30% of the TCO. At a p90 of 135 tok/s/user, Trainium3+vLLM models a 73.5% profit margin versus 53% for GB300+SGLang at 3-year rental prices. A "Trainium3n" revision using 320-lane Astera Scorpio X gen2 switch trays delivers 2.8× the all-to-all bandwidth of gen1 — directly benefiting MoE expert routing. Descriptor Generation Engines let Trainium hit peak memory-bandwidth utilization at small message sizes, which SemiAnalysis says Nvidia GPUs cannot do.
**Why it matters:** It is the most detailed independent counter-claim yet that a non-CUDA, memory-bound-optimized accelerator can win on inference economics despite having a fraction of the raw FLOPs — relevant to anyone sizing inference hardware on tokens-per-dollar rather than peak TFLOPS.
**Local inference:** Datacenter-class (144 GB HBM/chip, 16 chips/node) — not a desktop part; the "Short King" thesis (less HBM, more throughput at target latency) is the transferable idea for 16–32 GB local cards.
Source: SemiAnalysis, "AWS Challenger: Trainium3 Inference and Microarchitecture Deep Dive," 2026-11-27 — https://newsletter.semianalysis.com/p/trainium3-inferencex (HN discussion surfaced 2026-11-27 21:02Z)

## Inference software and benchmarks

**Reproducible 101 tok/s from Inkling 2 Mini on one GB10 in vLLM** — Event date: 2026-11-27 (HN Show HN 18:59Z = 13:59 ET, in window).
Runtime Notes documents tuning a DGX Spark-class MSI EdgeXpert (single GB10, 128 GB unified) to 101 tok/s for one request, 195.7 tok/s across four, and 9.7 s time-to-first-token on a 32K prompt — while keeping Qwen3.8-27B loaded and Inkling's full 131,072-token context. Five changes are detailed: rounding the expert planner's `max_ctas` to a power of two (compile stalls cut 67–90%); INT8 draft copies of DFlash2's 21 linear layers and output head (+4.3–4.9%); probabilistic drafting (+14–20% on sampled requests); adaptive verify width that marks low-confidence rows as "no expert" padding (+5.2–5.4%); and a prefill GEMM config that cut 32K TTFT from 12.2 s to 9.7 s. Inkling used 36% of device memory; the author's single-request result beats tonyd2wild's published TensorFold port (105.5 on a different bench) and trails knapcio's SGLang recipe (112.0).
**Why it matters:** It is a fully reproducible, numbers-backed local-inference result on the same GB10/DGX Spark class many readers own, and it shows spec-decode tuning (draft precision, verify width, compile-stall removal) now matters more than raw model swaps on unified-memory boxes.
**Local inference:** Single GB10 / DGX Spark-class 128 GB unified (Inkling ≈44 GiB allocation, Qwen3.8-27B resident) — directly transferable to RTX 5060 Ti 16 GB-class reasoning about memory headroom.
Source: Runtime Notes, "Getting 101 tok/s from Inkling 2 Mini in vLLM on one GB10," 2026-11-27 — https://runtimenotes.com/blog/getting-101-tok-s-from-inkling-2-mini-in-vllm-on-one-gb10/
Source (model support): vLLM Recipes, "thinkingmachines/Inkling-2-Mini" (Blackwell/Hopper/DGX Spark verified) — https://recipes.vllm.ai/thinkingmachines/Inkling-2-Mini

**vLLM in-window merge wave (UI→early evening ET)** — Event date: 2026-11-27.
Merged this window: #70160, a Qwen4Exp SM100 tcgen05 fused "HC down+inject+SiLU" kernel (22:02Z); #62536, ROCm bumped to 10.1 with MORI v1.2.4 and amd-smi fixes (20:57Z); #58375, a Mooncake EP all-to-all backend (20:42Z); #69666, use of the public Gluon reinterpret API for Inkling 2 (19:17Z); and #70020, corrected `/v1/decisions` score probabilities (18:02Z).
**Why it matters:** ROCm 10.1 plus a fused Blackwell MoE kernel and a new EP backend change where Qwen4Exp-class MoEs run efficiently (AMD gfx950 and SM100) — hardware-fit shifts, not cosmetic changes.
**Local inference:** SM100 (B200/GB200/Radeon-side ROCm 10.1) datacenter tiers; the Gluon/Inkling-2 path is the consumer-relevant one (GB10/DGX Spark).
Source: vLLM GitHub commits, 2026-11-27 17:45–22:45Z — https://github.com/vllm-project/vllm/commits/main/

**SGLang in-window wave: AMD fused MoE + NGRAM PD drafting + Lingbot-MoE** — Event date: 2026-11-27.
Merged this window: #43480, fusing DSv4.1's `wo_b` GEMM with its all-reduce via `mori cco` on gfx950 (20:56Z); #52551, an NGRAM draft-input builder for PD decode (22:12Z); #47920, Qwen prefill context-parallelism with whole-expert EP and optimized NIXL KV transfers (21:34Z); #32848, decoder-free Lingbot-MoE video diffusion adapters (21:57Z); #52223, pi-H3 2/4-step adapters; and #51728, a `weight_cache` load-diff format.
**Why it matters:** The gfx950 GEMM+all-reduce fusion and whole-expert-EP prefill lower the communication tax on AMD MI350/MI355-class hardware; NGRAM drafting adds a vendor-free speculative-decode option for disaggregated serving.
**Local inference:** Primarily MI350X/MI355X (gfx950) and datacenter NIXL deployments; NGRAM drafting is engine-level and consumer-applicable.
Source: SGLang GitHub commits, 2026-11-27 17:45–22:45Z — https://github.com/sgl-project/sglang/commits/main/

**TensorRT-LLM and Ollama: multimodal prefill graphs and a server bump** — Event date: 2026-11-27.
TensorRT-LLM #21333 enabled breakable prefill CUDA graphs for multimodal models for both text and media requests (21:09Z), with #22958 isolating a QSA decode plan-count test (20:03Z). Ollama merged #19953, bumping its bundled llama-server (21:06Z).
**Why it matters:** Breakable prefill graphs reduce graph-replay waste on mixed text/image batches — a concrete latency win for VLM serving on NVIDIA tiers.
**Local inference:** TensorRT-LLM targets NVIDIA datacenter/Blackwell; the Ollama llama-server bump propagates llama.cpp speedups to local installs.
Sources: https://github.com/NVIDIA/TensorRT-LLM/commits/main/ and https://github.com/ollama/ollama/commits/main/ — 2026-11-27 17:45–22:45Z

## Community spins and quantizations

**Pragma-1-4B-Mobile-Q3-WGSL: a 3-bit, 1.48 GiB WebGPU quant for phone browsers** — Event date: 2026-11-27 (HF repo created 22:33Z = 17:33 ET, in window).
Yours Research packed all 32 hybrid Gated-DeltaNet + GQA layers of Pragma-1-4B (3.78B params, Apache-2.0, Qwen3.5-4B-derived) into a planar Q3_B128 layout at 3.25 bits/weight with 4-bit tied embeddings. Total download is 1.48 GiB; live tracked GPU memory in mobile Safari/Chrome is ~1,583 MiB (1,511 MiB weights + 50 MiB DeltaNet state + 17 MiB Int8 KV), under iOS Jetsam limits, using `shader-f16` with no subgroups requirement and resumable OPFS streaming.
**Why it matters:** It is a credible, spec'd example of a modern hybrid-attention model fitting entirely in a phone browser's GPU budget — a real local-inference hardware milestone rather than a size-only claim.
**Local inference:** Apple/Qualcomm mobile GPUs via WebGPU, ~1.6 GiB VRAM; desktop Q4 and BF16 variants also published.
Source: yours-research/Pragma-1-4B-Mobile-Q3-WGSL, Hugging Face, created 2026-11-27 22:33Z — https://huggingface.co/yours-research/Pragma-1-4B-Mobile-Q3-WGSL

**Inkling-2-Small-Derisked-GGUF: a 612B derisked-MoE GGUF ladder** — Event date: 2026-11-27 (HF repo created 22:07Z = 17:07 ET, in window).
freakyskittle uploaded static quants of Blackfrost-Research's derisked Inkling-2-Small (612B MoE, 48 layers, 384 experts, top-8): Q8_0 631 GB down through UD-Q4_K_XL ~380 GB (imatrix) to IQ1_M ~135 GB. Files require a llama.cpp build with Inkling-2 architecture support (`#33055`); only the text backbone is converted so far — no vision tower or MTP head — and the repo is explicitly work-in-progress with one-at-a-time smoke tests.
**Why it matters:** "Derisked" (refusal/alignment-ablation) work on a 600B-class open MoE arriving as community GGUFs the same week the base is fresh shows how fast the quantization pipeline now turns frontier-scale weights into locally loadable tiers.
**Local inference:** 135 GB+ (IQ1_M) up to 631 GB (Q8_0) — multi-GPU or very large unified-memory machines only; well beyond a 16 GB card.
Source: freakyskittle/Inkling-2-Small-Derisked-GGUF, Hugging Face, created 2026-11-27 22:07Z — https://huggingface.co/freakyskittle/Inkling-2-Small-Derisked-GGUF

### Also noted
- **mradermacher/LFM3-1.7B-Looped-GGUF** (HF, created 22:40Z = 17:40 ET): static quants of vaultpls's looped LFM3-1.7B, Q2_K 0.9 GB → f16 4.1 GB; tiny-footprint local tier. https://huggingface.co/mradermacher/LFM3-1.7B-Looped-GGUF

## Coverage notes
- **Verified: 10 items** (2 lead hardware/benchmark, 4 inference-software merge waves, 2 community-spin ladders, 1 also-noted quant, 1 model-support cross-reference) across ~6 independent outlets/rivers: SemiAnalysis, Runtime Notes/HN, vLLM, SGLang, TensorRT-LLM/Ollama GitHub, and Hugging Face (~177 models scanned; 57 created in-window, mostly zero-download noise).
- **Reddit lane unavailable:** both the anonymous Atom `search.rss` and `r/LocalLLaMA/new.rss` returned 404 this fire — r/LocalLLaMA community posts could not be swept; not a negative finding.
- **Suppressed as already reported (ledger dedupe):** the Tom's "sandboxed models talk via GPU rounding errors" story (footnoted once, never again), the Razor Lake-AX iGPU/RTX 5070 Ti rumor update, and the China AI-PCB bottleneck item — all re-surfaced in-window but previously listed.
- **Held / could not pin to window:** Kioxia 3rd-gen XL-Flash (3 µs read latency, 70% higher bit density, samples March 2027) appeared at the top of Tom's November 27 archive but the search index reported it ~23 hours old (Nov 26 evening); with the related GP2 SSD already in the ledger, exact in-window placement could not be verified. No third retrieval attempt.
- GitHub rivers were healthy (llama.cpp 1, vLLM 10, SGLang 17, TensorRT-LLM 2, Ollama 1, ComfyUI 0 commits in-window); llama.cpp's single commit was a UI-only change, not substantive.
