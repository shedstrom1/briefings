# AI Hardware & Local Models Brief — Nov 19, 2026 (12:45 ET)

**Window:** 2026-11-19 08:27 → 13:08 EST (the 12:45 fire ran ~23 min late; the prior 07:45 slot itself fired at 08:27, so 07:45–08:27 was already covered by the previous edition). New-items-only; deduped against the reported ledger.

- **Day-0 GGUFs of the harness-adapted Qwen3.8-27B landed** — standalone OpenCode and Hermes RL-adapted checkpoints, MTP intact, 10.5–29 GB, Apache-2.0. The Hermes build reports SWE-bench Pro 29.4→43.1%.
- **Chimera puts Qwen3.6-35B-A3B on a CPU in 7 GB of RAM** via a patched `ik_llama.cpp` with SSD expert streaming — 12.8–13.3 tok/s on a laptop i5.
- **llama.cpp shipped ten builds (b12541–b12550)**, headlined by a Metal CONV_2D tensor API for M5, WebGPU POOL_2D, and Inkling-2 attention-mask fixes.
- **vLLM, SGLang and TensorRT-LLM merged in-window kernel waves** — Inkling-2 Humming expert GEMMs on Hopper, a ROCm W4A16 path worth up to +44% decode, and GLM-5.3-Flash FP8 on sm_103.
- **AMD's Adrenalin 26.11.2 WHQL driver adds ROCm 10.2 support**, extending the idle-SSD-write fix to RX 5000/6000.

## AI hardware and silicon

**AMD Adrenalin 26.11.2 WHQL adds ROCm 10.2 support (update on previously reported)**
*Event date: 2026-11-19 (driver covered 09:04 EST).*
TechPowerUp reports AMD released Adrenalin 26.11.2, whose headline new-feature entry is support for ROCm 10.2 — the AMD compute stack that local-inference users rely on for PyTorch/vLLM on Radeon. It also extends the idle system-drive write fix (previously only RX 7000 and newer on 26.11.1) to RX 5000 and RX 6000 on Windows 10, and fixes a memory leak and Smart Access Memory disabling itself after install. This is a material update to the ledger's 26.11.1 / radeon-ssd-write-blocker entry, not a new driver family.
**Why it matters:** ROCm 10.2 support landing in a WHQL branch is the difference between a Radeon card being a viable local-inference target or a gaming-only one; the SSD-write fix matters for any box doing heavy model/checkpoint I/O.
**Local inference:** Targets all Radeon RX 5000–9000 and Ryzen AI iGPUs; ROCm 10.2 is the stack used by R9700/B70-class 16–32 GB cards.
Source: TechPowerUp, "AMD Software Adrenalin 26.11.2 WHQL Drivers Released," 2026-11-19 14:04 UTC — https://www.techpowerup.com/355010/amd-software-adrenalin-26-11-2-whql-drivers-released
Source: Guru3D, "AMD Adrenalin 26.11.2 fixes more Radeon SSD writes and game crashes," 2026-11-19 — https://www.guru3d.com/story/amd-adrenalin-26112-fixes-more-radeon-ssd-writes-and-game-crashes/

## Model releases

No qualifying new open-weight base-model release was first disclosed inside this window. The two items below are conversions/merges of already-reported adapters and are placed under Community spins as updates, not new base releases.

## Community spins and quantizations

**Day-0 GGUFs of the harness-adapted Qwen3.8-27B (OpenCode + Hermes) — update on harness-adapters**
*Event date: 2026-11-19 (HF createdAt 18:00–18:01 UTC).*
Two independent conversions put harness-hub's RL-trained adapters into standalone, LoRA-free Qwen3.8-27B GGUFs with multi-token-prediction (MTP) heads and vision projectors intact. `netprojects/Qwen3.8-27B-hermes-adapted-GGUF` merges the Hermes adapter (rank 32, alpha 64, AsyncGRPO in TRL) and reports SWE-bench Pro (hard 51) 29.4%→43.1% and Terminal-Bench 2.1 44.7%→58.8% with thinking off; its Q6_K is 22.4 GB at 98.9% next-token agreement with BF16 (KL 0.0031), with Q4_K_M at 16.8 GB and IQ2_M at 10.5 GB. `prithivMLmods/Qwen3.8-27B-opencode-adapted-GGUF` merges the OpenCode adapter (AsyncGRPO in Harbor/OpenEnv) and cites SWE-bench Pro 35.3%→51.0% and Terminal-Bench 2.1 46.4%→52.4%, with 6–8× fewer tokens per task; Q4_K_M is 17 GB. Both are Apache-2.0 and the reported scores were measured on the BF16 adapter/base, not re-evaluated on the merged GGUFs.
**Why it matters:** These are the first ready-to-run local GGUFs of the per-harness RL adapters first reported on Nov 6 — the user's own Hermes Agent has a matching 27B build with documented SWE-bench/TB2.1 gains and a copy-paste `llama-server` config.
**Local inference:** 27B at Q6_K 22.4 GB / Q8_0 29 GB fits a 24–32 GB card or DGX Spark 128 GB; Q4_K_M 16.8 GB / IQ2_M 10.5 GB fits a 16 GB RTX 5060 Ti–class card with offload. MLX builds exist for Apple Silicon.
Source: Hugging Face, "Qwen3.8-27B-hermes-adapted-GGUF," 2026-11-19 18:00 UTC — https://huggingface.co/netprojects/Qwen3.8-27B-hermes-adapted-GGUF
Source: Hugging Face, "Qwen3.8-27B-opencode-adapted-GGUF," 2026-11-19 18:01 UTC — https://huggingface.co/prithivMLmods/Qwen3.8-27B-opencode-adapted-GGUF

**Chimera: Qwen3.6-35B-A3B in 7 GB of RAM on a CPU, no GPU**
*Event date: 2026-11-19 (HF createdAt 15:30 UTC).*
`samueltm/Chimera-Qwen3.6-35B-A3B-GGUF` runs the 35B-total/3B-active MoE on a stock x86 CPU with the server and page cache capped at 7 GB, using a patched `ik_llama.cpp` that streams packed experts from NVMe on demand (~24 MB read per token), skips low-mass experts, and applies a 153 MB RL-trained LoRA for concise reasoning. The author measured 12.8–13.3 tok/s on a 4-core i5-8265U and 7.7–8.1 tok/s on a network-disk cloud VM, with ~42–50 tok/s estimated on a 6-core Zen 3; quality held within +0.18% perplexity and 15/28 on AIME 2026. It requires AVX2 Linux/WSL2, 16 GB system RAM, ~40 GB disk, and the patched engine (a stock llama.cpp will not fit in 7 GB).
**Why it matters:** It is a concrete, reproducible data point that a 35B-class MoE is CPU-viable well below the usual 16 GB VRAM floor — expert-offload and MTP are doing the heavy lifting.
**Local inference:** CPU-only, no GPU; 7 GB server cap + 16 GB host RAM, NVMe required. Apache-2.0.
Source: Hugging Face, "Chimera-Qwen3.6-35B-A3B-GGUF," 2026-11-19 15:30 UTC — https://huggingface.co/samueltm/Chimera-Qwen3.6-35B-A3B-GGUF

**SenseNova-Loop-9B-heretic with a DGX Spark NVFP4 recipe**
*Event date: 2026-11-19 (HF createdAt 15:26 UTC).*
`Kaleto/SenseNova-Loop-9B-heretic` applies Heretic 2.1.0 Arbitrary-Rank Ablation to the looped 9B model (layers 4–27 execute twice), cutting refusals from 100/100 to 5/100 at KL 0.1001 while holding GSM8K at 90.7 (vs 91.7 original). The card ships an NVIDIA ModelOpt 0.43.0 W4A16 NVFP4 recipe served under vLLM 0.31.0, with the vision tower, MTP and loop gates left unquantized, and benchmarks tracked on DGX Spark in the DX10 space. Apache-2.0.
**Why it matters:** A low-VRAM, looped-architecture model with a validated 4-bit path is unusual; the abliteration is measured, not asserted.
**Local inference:** 9B class; NVFP4 path targets DGX Spark / 16 GB-class GPUs.
Source: Hugging Face, "SenseNova-Loop-9B-heretic," 2026-11-19 15:26 UTC — https://huggingface.co/Kaleto/SenseNova-Loop-9B-heretic

**Also noted (community):** `HexaSpaghetti/Qwen3.8-27B-Heretic-oQ8e` — an oMLX mixed-precision quant (affine-8 group-64 plus BF16, 8.627 effective bpw, 27.5 GiB) for Apple Silicon, created 15:19 UTC (https://huggingface.co/HexaSpaghetti/Qwen3.8-27B-Heretic-oQ8e). `Austriani/Orion-26B-A4B-v1.1-ik_llama.cpp` (15:52 UTC), `diffutfy/Qwen3.5-9B-GGUF` (15:23 UTC), and `thquangpc/gpt-oss-20B-vietnamese-GGUF` (17:34 UTC) were smaller in-window uploads; a batch of `loom-draft/*` auto-generated drafter repos appeared at 14:49 UTC.

## Inference software and benchmarks

**llama.cpp: ten in-window builds, b12541–b12550**
*Event dates: 2026-11-19, 08:57–12:51 EST (13:57–17:51 UTC).*
The release stream carried Metal CONV_2D tensor-API support for M5 with split-K for all chips (#32542), WebGPU POOL_2D (#32250), Inkling-2 attention-mask fixes that give it its own relative-position input (#32429), and a backend-scheduler test harness with an experimental Vulkan `GGML_OP_SLEEP` (#27258). Also in the set: IQ2 quant-creation speedup via caching (#31910), qwen3vl `t_layer_inp` for EAGLE-3 (#32589), and small Metal/graph-parameter cleanups. These are the first builds after the ledger's b12528–b12540 wave.
**Why it matters:** M5 tensor-core convolution and WebGPU pooling expand which Apple and browser backends get accelerated ops; the Inkling-2 fixes matter because the 594B model's day-0 GGUF wave is still fresh.
**Local inference:** Apple Silicon (Metal/M5), WebGPU browsers, and Vulkan/CPU backends; no single new VRAM floor.
Source: llama.cpp releases feed, b12541–b12550, 2026-11-19 13:57–17:51 UTC — https://github.com/ggml-org/llama.cpp/releases

**vLLM / SGLang / TensorRT-LLM: in-window kernel and model-enablement merges**
*Event dates: 2026-11-19, merged 13:27–18:08 UTC.*
vLLM merged "[Model][Perf] Inkling 2: Humming expert GEMMs on Hopper" (#68761), a ROCm W4A16 fix for the gfx1151 bf16 tile cliff worth up to +44% decode (#66133), Rust-frontend EPD metadata-only capability (#68565), and a fused `qk_norm_rope` int32-overflow/NeoX partial-rotary fix (#68386). SGLang added Inkling-2-Small support (#50186), pinned the AMD mono-kernel for a Qwen3.5 FP8 state-layer-cache fix (#51518), and added NPU multimodal JPEG decode (#45792) and MindIE-SD sparse attention (#40580). TensorRT-LLM merged GLM-5.3-Flash FP8 Modeling V2 on sm_103 (#22621) and a DeepSeek-V4.1 SM107 MXFP8 prefill-GEMM/CSA2/GVR-decode path (#22358). These sit on top of — not duplicate — the overnight waves already in the ledger.
**Why it matters:** The ROCm +44% decode and the Hopper/H100-class Inkling-2 kernels change tokens/sec and $/token on the exact hardware readers run; the sm_103 GLM-5.3 path extends TensorRT-LLM to newer Blackwell SKUs.
**Local inference:** vLLM/SGLang W4A16 and Inkling-2 paths target 8–80 GB cards and DGX Spark; TRT-LLM sm_103 targets Blackwell workstation/server GPUs (Inkling-2 itself needs ≥500 GB multi-GPU).
Source: vLLM merged PRs, 2026-11-19 — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged+merged%3A2026-11-19
Source: SGLang merged PRs, 2026-11-19 — https://github.com/sgl-project/sglang/pulls?q=is%3Apr+is%3Amerged+merged%3A2026-11-19
Source: TensorRT-LLM merged PRs, 2026-11-19 — https://github.com/NVIDIA/TensorRT-LLM/pulls?q=is%3Apr+is%3Amerged+merged%3A2026-11-19

## Just missed the window

- Intel put 18A yields at 70% and named Microsoft its first external 18A/EMIB customer under a multi-year Xeon supply deal — Lip-Bu Tan's remarks circulated ~20:25 EST Nov 18 and Tom's Hardware's write-up ran 08:00 EST, both before this window (and distinct from the already-footnoted EMIB-T-Maia-Cobalt item). https://www.tomshardware.com/pc-components/cpus/intel-18a-yields-hit-70-as-company-signs-microsoft-as-emib-customer
- Architect Labs' Frostbite — an AI-discovered CPU architecture claiming 4.59× perf/W — is dated Nov 18; its HN re-surfacing fell in-window but the underlying disclosure did not. https://architectlabs.com/blog/frostbite

## Coverage notes

**10 candidates suppressed by the ledger dedupe gate** (already reported or already footnoted): Top500/LineShine (Nov 16), 3x-V100 e-waste rig and the llama.cpp b12528–b12540 / vLLM-SGLang-TRT-LLM overnight waves (08:27 fire), DDR6 JEDEC preview (Nov 18), Adrenalin 26.11.1 / radeon-ssd-write-blocker, EMIB-T-Maia-Cobalt and Edge0-145B-NVFP4 (footnoted once, not repeated), the harness-adapters base announcement (Nov 6 — surfaced here only as the new GGUF conversion), SenseNova-Loop-9B base, and Hy4-flash. Verified in this window: 6 qualifying items (3 hardware/software leads + 3 community leads), plus 4 also-noted and 2 once-only footnotes.

**Limitations:** r/LocalLLaMA is unavailable this run — the reddit-reading skill returned HTTP 404 on the subreddit listing, search and `--json` fallback paths (anonymous feeds blocked). Hugging Face trending was read through the browser and the creation stream via HF's API; the broader web search backend returned "no results" on several hardware queries. No item was padded in from outside the window.
