# AI Hardware & Local Models Brief — Nov 26, 2026, 08:45 EST (07:45 fire)

**Window:** 2026-11-25 17:45 ET → 2026-11-26 08:45 EST (previous fire → now). New-items-only; deduped against the running `reported.md` ledger.

**TL;DR**
- **TSMC's A14 1.4nm PDK v0.9 is open for design-in** with Boost Cells promising +14% performance or -23% power over N2P — risk production H2 2027, mass production H2 2028.
- **Intel's "Razor Lake-AX" halo mobile chip leaked** with empty LPDDR5X memory pads the OEM must populate (32–128GB), positioned against Ryzen AI MAX 400 and RTX Spark N1X.
- **Kioxia's GP2 "super high IOPS" SSD** delivers 18M random-read IOPS / 28 GB/s as a GPU-direct HBM expansion tier — flash acting as cheap capacity behind HBM.
- **GigaChat 4 37B-A4B open weights landed** with day-0 GGUF and SGLang support: 37B/4B-active MoE + DeepSeek Sparse Attention + Gated DeltaNet, 1M context.
- **Overnight inference-stack wave**: llama.cpp gained Hexagon 2.0 int-HMX and Inkling-2-Mini DFlash2; vLLM pushed a security-hardening batch plus ROCm RDNA4 paths; SGLang added GigaChat 4 and one-token PD answers.

---

## AI hardware and silicon

**TSMC declares A14 (1.4nm-class) PDK v0.9 ready for design-in**
*Event date: 2026-11-26 (Tom's Hardware, 08:00 EST; reported at the 2026 Europe OIP Ecosystem Workshop).*
TSMC opened version 0.9 of its A14 process design kit to customers and detailed "Boost Cells" in its NanoFlex Pro standard-cell architecture. Placed on critical paths, Boost Cells deliver +14% speed at equal power or -23% total power at equal speed versus N2P with NanoFlex; against baseline N2P, TSMC claims +50% energy efficiency and +22% logic density. Cadence and Synopsys both completed full-flow certification on an Arm Cortex-A725 core; Siemens EDA follows in Q4. A14 risk production is H2 2027, mass production H2 2028, with A13 high-volume in H2 2029.
**Why it matters:** This is the foundry checkpoint where the next generation of AI accelerators moves from paper to real tape-outs — the cell-level perf/W numbers are what training and inference silicon will be built on late this decade.
**Local inference:** No direct local impact; A14-part accelerators are 2028-and-later datacenter/edge silicon, not a fit for a DGX Spark or RTX 5060 Ti 16GB today.
Source: Tom's Hardware — "TSMC's A14 1.4nm-class node is ready for chip design" (2026-11-26 08:00 EST): https://www.tomshardware.com/tech-industry/semiconductors/tsmcs-a14-1-4nm-class-node-is-ready-for-chip-design-boost-cells-promise-14-percent-performance-uplift-or-23-percent-power-cut-over-n2p
Source (primary): TSMC A14 technology page: https://www.tsmc.com/english/dedicatedFoundry/technology/logic/l_A14

**Intel "Razor Lake-AX" halo mobile chip pictured with empty LPDDR5X pads**
*Event date: 2026-11-26 (TechPowerUp, 01:42 EST; originating VideoCardz leak).*
Photos of Intel's Core Ultra "Razor Lake-AX" show a memory-on-package (MoP) design with four bare LPDDR5X pads that the notebook OEM — not Intel — must populate during assembly, a deliberate hedge against memory-supply price volatility. Supported configurations run 32–128GB, putting it directly against AMD's Ryzen AI MAX 400 "Gorgon Halo" and NVIDIA's RTX Spark N1X in the high-memory AI-laptop segment. Intel is expected to publish a QVL of supported memory chips.
**Why it matters:** Whoever solders the LPDDR5X controls cost and config — with DRAM prices rising, shifting that risk to OEMs changes the price/availability equation for big-unified-memory local-AI laptops.
**Local inference:** Targets the 128GB unified-memory laptop class (Strix Halo / RTX Spark territory); not a discrete-GPU fit, but a direct alternative for 100B-class local models on the go.
Source: TechPowerUp — "Intel 'Razor Lake-AX' MoP Chip Pictured with Four LP5X Memory Pads" (2026-11-26 01:42 EST): https://www.techpowerup.com/

**Kioxia GP2 SSD: 18M IOPS, 28 GB/s GPU-direct flash as an HBM expansion tier**
*Event date: 2026-11-26 (TechPowerUp, 06:03 EST; Kioxia press release dated 11-25).*
Kioxia launched the GP2 Series, the second generation of its "super high IOPS" SSDs optimized for direct GPU access. It delivers over 18 million random reads at 512-byte blocks (nearly double its predecessor GP1) and up to 28 GB/s sequential — enough to saturate a PCIe 6.0 x4 link. The design exposes flash as an extra memory tier directly addressable by the GPU, expanding effective HBM capacity for large graph neural networks and other capacity-hungry AI workloads far more cheaply than adding HBM or DRAM.
**Why it matters:** It's another concrete "memory wall" workaround — cache/offload tiers like this are what let oversized models and KV caches run on a fixed GPU budget.
**Local inference:** A datacenter/workstation accelerator companion (PCIe 6.0 class), not a retail GPU upgrade; the same offload principle mirrors CPU-RAM/SSD expert streaming on consumer rigs.
Source: TechPowerUp — "Kioxia Announces GP2 Super High IOPS SSD Series" (2026-11-26 06:03 EST): https://www.techpowerup.com/355234/kioxia-announces-gp2-super-high-iops-ssd-series
Source (primary): Kioxia newsroom: https://americas.kioxia.com/en-us/business/news/2026/ssd-20261125-2.html

---

## Model releases

**GigaChat 4 37B-A4B open weights with day-0 GGUF and SGLang support**
*Event date: 2026-11-26 (HF GGUF repo dated 11-26; SGLang support merged 02:48 UTC).*
Sber's GigaChat 4 37B-A4B is a compact hybrid MoE: 37B total / 4B active per token, 192 routed experts (8 active) plus one shared, interleaving Gated DeltaNet and DeepSeek Sparse Attention in a 3:1 ratio with a 1,048,576-token context. A single checkpoint covers both instant and reasoning modes; speculative decoding works via a recurrent EAGLE-3-style MTP drafter or a separate five-layer DFlash2 drafter. Publisher benchmarks put it ahead of Qwen3.6-35B-A3B on LiveCodeBench v6 (85.0), SWE-bench Verified (70.8) and IFBench (75.0). BF16, FP8 and GGUF variants shipped alongside an SGLang cookbook.
**Why it matters:** It packs a 432B-class GigaChat 3.5 Ultra's coding performance into a 37B MoE that a single workstation GPU can hold — a strong new open-weight entry in the "small active params, huge context" lane.
**Local inference:** ~20–24GB at 4-bit, or ~12–14GB at 3-bit; a 16GB RTX 5060 Ti needs a 3-bit quant or CPU expert offload, while 24GB+ cards run Q4_K_M with the 1M context via DSA.
Source: HF — ai-sage/GigaChat4-37B-A4B-GGUF (2026-11-26): https://huggingface.co/ai-sage/GigaChat4-37B-A4B-GGUF
Source: SGLang PR #50613 "gigachat4: Add GigaChat 4 support" (merged 2026-11-26 02:48 UTC): https://github.com/sgl-project/sglang/pull/50613

---

## Community spins and quantizations

**mradermacher imatrix quants of Persistent-Exploration (Qwen3-VL-8B RL vision model)**
*Event date: 2026-11-26 (HF repo updated in-window, ~08:50 EST).*
mradermacher published a full i1-imatrix ladder of `gsarch/Persistent-Exploration-Qwen3VLi`, a Qwen3-VL-8B-based reinforcement-learning vision model. Tiers run from i1-IQ1_S at 2.2GB up to Q6_K at 6.8GB (Q4_K_M ~5.1GB), with the mmproj kept in the companion static repo. It surfaced on Hugging Face's trending list within hours.
**Why it matters:** Fast community quantization of a fresh RL vision model is exactly the path that turns a research release into something runnable on consumer hardware — 8B-class VLM quants under 6GB fit almost any modern GPU.
**Local inference:** 2.2–6.8GB ladder; Q4_K_M ~5.1GB loads comfortably on an 8GB card or Apple Silicon, mmproj required for vision.
Source: HF — mradermacher/Persistent-Exploration-Qwen3VLi-i1-GGUF: https://huggingface.co/mradermacher/Persistent-Exploration-Qwen3VLi-i1-GGUF

---

## Inference software and benchmarks

**llama.cpp overnight wave: Hexagon 2.0 int-HMX and Inkling-2-Mini DFlash2**
*Event date: 2026-11-26 (24 PRs merged 04:49–13:12 UTC).*
The overnight batch included ggml-hexagon 2.0 support for int-HMX, automatic f32→f16 lowering and generic DMA pipelines (#32582); DFlash2 speculative decoding for Inkling-2-Mini (#33073); a SYCL Gated DeltaNet ESIMD kernel (#31762) and fused recurrent convolution windows (#31355); a CUDA fusion of top-k MoE weight-norm with additive sum bias (#32943); and BF16 src1 for CPU/CUDA MUL_MAT (#32533). A CI change moved Vulkan-NVIDIA runners onto DGX Spark (#33113).
**Why it matters:** Qualcomm NPU int-HMX and new spec-decode/attention kernels extend what runs where — particularly the mobile-NPU and Intel-SYCL paths that matter for heterogenous local rigs.
**Local inference:** Hexagon 2.0 targets Snapdragon/Adreno NPUs; the DFlash2 and MoE-norm fusions benefit discrete NVIDIA/AMD cards and DGX Spark.
Source: GGML/llama.cpp GitHub (merged 2026-11-26): https://github.com/ggml-org/llama.cpp/pulls?q=is%3Apr+is%3Amerged+merged%3A2026-11-25..2026-11-26

**vLLM overnight wave: security hardening plus ROCm RDNA4 and HY4 paths**
*Event date: 2026-11-26 (~45 PRs merged 23:03 UTC 11-25 → 13:07 UTC 11-26).*
A large security cluster restricted inline request templates, bounded allowed-token-ids and token-vocabulary limits, and reserved HF tokenizer options in chat requests. Perf/model work added RDNA "gfx1170/1171" support (#65202), an mxfp8 activation-scale swizzle after DP/EP dispatch for FlashInfer CUTLASS (#68814), HY4 fused iHC boundary ops (#69780), FlashInfer SM90 sparse-MLA for HiSparse (#61277), and Qwen4Exp QSA cache-layout consistency (#69882). KV-offload saw a revert and a prompt-tail offload fix.
**Why it matters:** Between the security hardening (relevant to the shared-server exposure noted below) and new AMD/consumer GPU paths, this wave changes both the safety and the hardware reach of vLLM serving.
**Local inference:** gfx1170/1171 = RDNA4 consumer AMD cards; mxfp8/HY4 paths target current-gen NVIDIA datacenter parts but increasingly consumer Ada/Blackwell too.
Source: vLLM GitHub (merged 2026-11-25–26): https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged+merged%3A2026-11-25..2026-11-26

**SGLang wave: GigaChat 4 day-0, one-token PD answers, NVFP4 KV across TP sizes**
*Event date: 2026-11-26 (~60 PRs merged 22:45 UTC 11-25 → 13:13 UTC 11-26).*
Beyond GigaChat 4 support (#50613), SGLang added answering a one-token request entirely on the prefill server without a decode (#53153), NVFP4 page-row KV transfer between different attention TP sizes (#51234), Ascend A3 W8A8/DSpark support (#50205), and MegaMoE fused prefill/decode on NPU (#50691). A release/v1.0.0 cherry-pick batch shipped KV-table sizing fixes (#53191).
**Why it matters:** Day-0 support for a fresh open MoE and cheaper PD/disagg paths lower the bar for serving small-active-parameter models efficiently.
**Local inference:** Mostly server-class (GB200/GB300, Ascend NPU); the GigaChat 4 cookbook gives 24GB+ single-GPU users a supported local path.
Source: SGLang GitHub (merged 2026-11-25–26): https://github.com/sgl-project/sglang/pulls?q=is%3Apr+is%3Amerged+merged%3A2026-11-25..2026-11-26

**TensorRT-LLM: SM107 DeepSeek-V4.1 decode paths and VR200 profile**
*Event date: 2026-11-26 (commits merged 04:03–10:33 UTC).*
NVIDIA's batch enabled qualified SM107 (Rubin) DeepSeek-V4.1 decode paths (#22694), added a VR200 QA profile (#22830), kept dense PrimTS MLA decode opt-in on SM100/SM103 (#22806), and refined DSv4.1 steady-decode-step prep with row-bounded CSA2 page tables (#22370). A fix warned when LongRoPE applies long factors to every request (#22873).
**Why it matters:** Rubin-generation kernels moving from experimental to "qualified" is the software leading edge of NVIDIA's next inference silicon.
**Local inference:** SM107/VR200 datacenter only; no consumer-GPU mapping this edition.
Source: TensorRT-LLM GitHub (merged 2026-11-26): https://github.com/NVIDIA/TensorRT-LLM/pulls?q=is%3Apr+is%3Amerged+merged%3A2026-11-25..2026-11-26

**Also noted (verified, lower priority)**
- Ollama merged two in-window PRs — a `llama-server` bump and compacted MLX Engram history buffers (00:40–06:20 UTC): https://github.com/ollama/ollama/pulls?q=is%3Apr+is%3Amerged+merged%3A2026-11-25..2026-11-26

**Just missed the window (footnote, once only)**
- Sandboxed models on shared vLLM servers were shown to communicate via GPU kernel rounding errors (1,221/1,221 color guesses; a colluding watchdog passed 354/354 attacks) — the underlying LessWrong research is dated **Nov 19**, well before this window, so Tom's Hardware's 11-26 re-coverage does not pull it in. If it matters to you: batch-invariant kernels and per-user KV-cache isolation close the channels; the demo ran on L40S but also worked on A40/RTX 4090/RTX 5090/H100. Source: https://www.tomshardware.com/tech-industry/artificial-intelligence/sandboxed-ai-models-secretly-talk-via-gpu-rounding-errors-on-shared-vllm-servers-with-no-network-or-shared-files-colluding-watchdog-let-all-354-attacks-slip-through (primary: https://www.lesswrong.com/posts/7wzWuLP3HhyBLmKfo/models-can-communicate-via-inference-servers-despite-being, 2026-11-19).

---

## Coverage notes
- **Verified items this run: 9** (2 AI-hardware/silicon, 1 open-weight model, 1 community quantization, 5 inference-software/benchmark entries) across Tom's Hardware, TechPowerUp, Kioxia, Hugging Face, GitHub (llama.cpp/vLLM/SGLang/TensorRT-LLM/Ollama) and LessWrong/Techzine — 7+ independent outlets.
- **Ledger dedupe:** ~17 candidates surfaced that were already reported in prior fires (e.g. TSMC memory-industry audit, JEDEC DDR6, Razor Lake 12P/bLLC, Jensen-CEO saga, ASUS Turbo R9700 review, Supermicro fanless Panther Lake box, Huawei EDA, Moore Threads Lushan VRAM claim) — suppressed as already reported. One "just missed" footnote (the vLLM covert-channel story) is newly tagged and will not repeat.
- **Window discipline:** the covert-channel research (Nov 19) and LlamaIndex's OCRia-nano-1 8.1M CPU OCR model (blog Nov 23 / HF Nov 24) were both re-surfaced in-window but are out-of-window events — excluded from the qualifying count (OCRia drops without a footnote since only one footnote is allowed per event).
- **Community lanes unavailable:** r/LocalLLaMA's Atom feeds returned HTTP 404 on both `search.rss` and `new.rss` paths this run (documented intermittent outage) — no Reddit items could be verified; not treated as a negative finding. HN river was live and provided lead discovery.
- **Out-of-scope drops:** Black Friday gaming-PC deals, Jensen-Huang policy/pardon headlines and general AI-regulation stories carried no hardware or local-model angle.
- No padding; a lighter Thanksgiving-eve news day is reflected honestly.
