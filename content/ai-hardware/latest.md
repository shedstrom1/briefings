# AI Hardware & Local Models Brief — 2026-11-23 12:45 EST

**Window:** 2026-11-23 07:45 EST → 12:45 EST (previous fire → now; 5 hours)
**Verified items:** 7 core stories + 2 one-line footnotes (Ling-3.2-air, NariQ) — no hardware/silicon announcements this window.

## TL;DR
- NVIDIA's board unanimously reinstated Jensen Huang as President and CEO, effective immediately, eleven days after his resignation — the same day Colette Kress returns to CFO-only and the board chair (Harvey Jones) is confirmed to stay on.
- llama.cpp merged an auto-quantization-target feature (`--target-size` / `--target-bpw`) that picks the per-tensor quant mix to hit a file-size or bits-per-weight goal — matching or beating standard quants in ~82% of 238 tests.
- vLLM got a TRITON_MLA decode-kernel overhaul (1.06–1.71x kernel-level, L2 hit rate 55%→74%), plus DFly speculative decoding unlocked for compressed-tensors FP8/NVFP4 HY4 checkpoints (RedHatAI/Hy4-NVFP4 accepted at last).
- SGLang fused Qwen3.8 QSA decode on AMD MI355X (gfx950) with AITER's FlyDSL kernel: 3.4–5.7x at the kernel level, +12.7% interactivity p50, +3.3% total throughput.
- ggml-openvino got a 12–24x graph-conversion speedup (Qwen3.5-0.8B, 4.1s → 0.17s) plus Ling-3.0-tiny (bailingmoe3) per-key-gate fused GatedDeltaNet support — a direct path for the new Ling family.

## AI hardware and silicon
None in-window. No new GPUs, NPUs, accelerators, HBM/memory, or inference-chip announcements landed during this window. (The CEO transition is a material NVIDIA governance development but is not an AI-hardware announcement; see update below.)

## Model releases
No new open-weight releases first disclosed inside this window (Ling-3.2-air was created ~22:55 EST Nov 22 — see "Just missed" footnotes). The engine-level release is llama.cpp b12651 (03:52 EST Nov 23, pre-window), so no new GGUF release from llama.cpp either.

## Community spins and quantizations
No new GGUF/spin uploads reached the public record inside this window. The in-window community-scale rating body was routine mradermacher i1-ladder repacks of existing bases (Lunar-Huntress-0.2, Nemotron-3.5-Embed, humanlike Gemma-4-31B, Gemma-4-E2B ARA) — routine repacks, not events, and excluded as noise.

## Inference software and benchmarks

**Update on Nov 12 NVIDIA CEO transition (ceo-transition).** NVIDIA's board voted unanimously to reinstate Jensen Huang as President, Chief Executive Officer and Board member, effective immediately, following a series of discussions; Colette Kress continues as Chief Financial Officer (interim CEO until now), Harvey Jones remains Chair. The filing received input from senior U.S. government levels and was satisfied that reinstatement poses no national-security risk; the independent-director investigation into a foreign intelligence effort against Mr. Huang proceeds to conclusion. Reuters separately reports the board was pressured by U.S. President Donald Trump in the reversal. — Nvidia SEC filing / press release, Nov 23, 2026; Reuters, Nov 23, 2026. Source: https://investor.nvidia.com/news/press-release-details/2026/NVIDIA-Board-Reinstates-Jensen-Huang-as-President-CEO-and-Board-Director/default.aspx · https://www.reuters.com/technology/nvidia-reinstates-jensen-huang-ceo-2026-11-23/
*Local inference:* None directly — NVIDIA's roadmap and flagship-chip supply is the context for DGX Spark / RTX Spark / Rubin-class local-AI hardware, and the reinstatement itself is the single most-watched corporate event of the day for that stack.

**llama.cpp #15550 — automatic per-tensor quant selection to hit a target file size or bpw** (merged 2026-11-23 17:06Z). `llama-quantize` now supports `--target-size 1.5g` or `--target-bpw 4.5678`: it Monte-Carlo-samples each tensor's error, builds a Pareto frontier per tensor, and solves a constrained Lagrangian problem to place the bit budget where error-reduction is highest. 34 models × 238 tests: 195 WIN / 26 TIE / 16 LOSS / 1 NA at target bpw; ~82% win-rate. The catch is cost — 5x–10x slower for the first run (state file makes it resumable and re-runs fast), and it requires an imatrix ≥ 0504396 with activation statistics or it refuses. — GitHub PR #15550 (EAddario, merged by pwilkin). Source: https://github.com/ggml-org/llama.cpp/pull/15550
*Local inference:* This is the quant-tooling update of the day — directly usable for DGX Spark / RTX-class 16–32GB 24–32GB-fit models; first-run latency is the trade-off.

**vLLM #69029 — TRITON_MLA decode kernel: 1.06–1.71x at the kernel level** (merged 2026-11-23 17:21Z). Co-schedule head blocks on `grid[0]` so all NUM_HEAD_BLOCKS sharing one KV slice run concurrently on SMs, and load K_Buffer row-major ([BLOCK_N, BLOCK_DMODEL]) for 128-bit vectorized coalescing. H100 1.06–1.71x (1.49–1.71x on long-context/batched decode), A100 1.05–1.47x, L40S 1.06–1.38x; L2 hit rate 55%→74%, shared-memory bank conflicts eliminated, 63.49KB→56.32KB per CTA. End-to-end E2E on a 16-head lite model is flat (as the author notes — 128-head models like DeepSeek-V2/V3/R1 are the real win). — GitHub PR #69029. Source: https://github.com/vllm-project/vllm/pull/69029
*Local inference:* H100/A100/L40S-class serving; kernel-level gains matter for DeepSeek-class MLA models at 64K+ context.

**vLLM #69274 — HY4 Flash DFly on any compressed-tensors target scheme** (merged 2026-11-23 16:24Z). DFly speculative decoding now works with HY4-Flash checkpoints whose layers are any compressed-tensors scheme (NVFP4, FP8) — draft layers must be BF16 or blockwise W8A8 FP8 (checked exactly), and any kv_cache_scheme/transforms still reject. RedHatAI/Hy4-NVFP4 is now accepted (38 BF16 draft layers), alongside tencent/Hy4-FP8 (37 FP8 + 1 BF16). On the full 770B model (4× B300 TP4), mean acceptance length 2.955 (NVFP4) and 2.939 (FP8). No E2E speedup claimed. — GitHub PR #69274. Source: https://github.com/vllm-project/vllm/pull/69274
*Local inference:* 770B-class, datacenter-only — the NVFP4 path points at GB200/GB300-class serving.

**SGLang #45196 — AMD Qwen3.8 QSA decode through AITER's fused FlyDSL kernel on gfx950** (merged 2026-11-23 13:44Z). Replaces the Triton two-pass gather+decode with AITER's `qsa_sparse_decode` on MI355X (Qwen3.8-Flash-Next Quark MXFP4, TP1, MTP NEXTN 3/1/4): 3.4–5.7x at the kernel level (16.6µs vs 89.6µs at batch 1), QSA attention GPU time 9.4%→2.8% of decode step (120µs→33.3µs per call), GSM8K 95.2→96.0% short and 95.8→97.9% long, MTP accept length 3.503→3.506 (short) / 3.394→3.441 (long), +3.3% total throughput, +12.7% interactivity p50, −10.8% ITL mean. GSM8K is accuracy-noise-level. — GitHub PR #45196. Source: https://github.com/sgl-project/sglang/pull/45196
*Local inference:* AMD MI355X-class (gfx950) only — the flagship local AMD GPU is RX 9000-class, not MI355X; the larger-context value is for datacenter serving.

**llama.cpp ggml-openvino — 12–24x graph-conversion speedup + Ling-3.0-tiny (bailingmoe3) fused GatedDeltaNet** (commit 51fc2f90, 2026-11-23 14:29Z). Decoder cloned once per graph conversion instead of per node — graph conversion time drops 12–24x (Qwen3.5-0.8B: 4.1s → 0.17s). Per-key-gate fused GatedDeltaNet now translates Ling-3.0-tiny (bailingmoe3) kda-gate layers instead of rejecting them; also fixed GDN padded-v views, zero-point-out-of-range u4/u8 wrap-around, a Q4_1 parallel_for race on zero-point bytes, a static-Split reference cycle, and a static-decode 0-out_ids bug. — Commit 51fc2f90e2, ggml-org/llama.cpp. Source: https://github.com/ggml-org/llama.cpp/commit/51fc2f90e23d4faf0f726f476a5452652b535145
*Local inference:* OpenVINO/NPU-class paths for edge/NPU deployments — the Ling family (BailingMoeV3VL) is the direct beneficiary of the fused GatedDeltaNet translation.

**llama.cpp #32677 — Metal BF16/F32 src1 for MUL_MAT and MUL_MAT_ID** (commit 87f816e8, 2026-11-23 16:46Z). Metal now supports BF16 and F32 source-1 tensors in MUL_MAT/MUL_MAT_ID — the standard Apple-Silicon compute path for MoE-expert and dense GEMMs. Source: https://github.com/ggml-org/llama.cpp/commit/87f816e87f
*Local inference:* directly relevant to DGX Spark / Mac Studio / M-class Apple Silicon — the main way more of the compute path can stay on the GPU without per-tensor F32 promotion.

**llama.cpp #32740 — compact bias on the existing FLASH_ATTN_EXT** (commit 2392423, 2026-11-23 17:17Z). Compact bias on the existing FLASH_ATTN_EXT path for CUDA and ROCm (Vulkan removed), C-reference implementations on CPU, plus F32 CPU reference implementation — matters for models with indexed-bias attention (e.g. applet/custom heads) where the prior path would silently miss bias support in the default flash kernel. Source: https://github.com/ggml-org/llama.cpp/commit/239242322dc24e8c15a458b1beae5064e3358cf8

## Also noted
- **llama.cpp #32651** — typed RPC request/response helpers + one validation step on the server; deserialize_tensor/rpc_check_region hardening, bounds-checked rpc_reader, topological-order graph-compute validation. RPC wire format unchanged. (commit 03813c56, 14:28Z.)
- **llama.cpp #32566** — zero-block few-row MMQ quantization edge case in CUDA handled. (commit 46f4da21, 14:17Z.)
- **llama.cpp #31815** — Metal GPU timeout on iPhone for Bonsai — the iPhone-class Metal path is being unbroken rather than just extended. (commit 1f3ea67f, 15:15Z.)
- **vLLM #69433** — MLA in-place LSE merge race fix for chunked context (commit 4f0ecfff, 17:24Z). #68770 — HY4 Flash improvement (17:13Z). #57011 — sleep/wake/reload lifecycle hooks on model loader (13:51Z). #69066 — TorchAO backend deprecation (17:01Z). #69412 — CuMemBackend allocator hook for SleepMode refactor (15:33Z). #69410 #69433 #69285-#69140 minor bug-fix group (12:45Z–17:32Z).
- **TensorRT-LLM #22732/#22646/#21207/#22643** — perf-sanity warmup timing upload (17:24Z); Qwen2-VL/Qwen2.5-VL tied-embedding lm_head fix (17:11Z); opt-in fused GDN prefill conv for rows the #21997 kernel skips (17:10Z); unused full-window DSA schedules skipped (17:05Z).
- **SGLang #48649** — faster decode MQA logits on ROCm (padded Q, group-strided launch), merged 13:16Z. **#51138** — softcapped router weights kept finite for large logits (13:15Z). **#49463/#49467** — linker slots resolved before the forward fence; published prefetch KV covered by the request's session (14:35Z/14:33Z).
- **ComfyUI #17918** — Meshy N1 texture and Image-to-Relief modes added (14:54Z).

## Just missed the window (footnote once)
- Ling-3.2-air (InclusionAI) — new open-weight multimodal model, 124B total / 5.5B active (35 KDA + 7 Gated MLA at 5:1), 512 routed experts + 1 shared, 8 active experts, 256K context, native FP8 storage ~129GB, license MIT; AutomationBench-AA 61% (vs 15.7%), GDP.pdf all-pass 16% (vs 8.4%), AA-Briefcase v1.1 Elo 1340 (+353 over Ling-3.1-flash), Terminal-Bench 4.0 27% (vs 0%), Harvey LAB-AA 87.7% criterion pass (0.8% post-hallucination-check), AA Intelligence Index v5.0.1 23; day-0 SGLang cookbook with MTP NEXTN (#52598): 2× B200 235.89 tok/s (c1, 2048/512) / 1451.78 (c16), GSM8K 97.04%; 4× GB300 209.14 (c1) / 1656.12 (c16), GSM8K 96.97%; 4× 141GB-class (H20-3e/H200) or B300/GB300 per the README. HF created 22:55 EST Nov 22 — outside this window, footnoted once.
- NariQ (Nari Labs) — trellis-coded Qwen3.8-27B at 2.50/2.00/3.00 bits per weight, BF16-level on long-horizon agentic tasks (DeepSWE 41.0/41.9/28.6, Terminal-Bench 2.1 63.8/60.2/52.4, 10.12GB/8.44GB/6.75GB), required NariLabs' fork; HN post Nov 23 (story_50611402) is re-coverage of the Nov 19–20 release. Footnoted once.

## Coverage notes
24 candidates were evaluated; the ledger suppressed zero in-window items (no prior-ledger event matched this window's candidates — the 07:45 fire's GLM-5.4/Metal items were the last in-scope items and are unrelated to this window). Noded dead ends: Reddit anonymous-atom lane failed (HTTP 404 — same lane-down status as the 11-22 fire); web_search returned no in-window hardware/silicon news; HF trending API returned only unrelated cheat-tool rows (low-quality sort). Hardware section deliberately empty — no AI-hardware/silicon, memory, or GPU announcement first surfaced in this window. items: 7 core + 2 footnotes. Ledger footnote tags added: Ling-3.2-air, NariQ.
