# AI Hardware & Local Models Brief — Nov 27, 2026 (12:45 fire)

**Window:** 2026-11-27 08:53 EST → 2026-11-27 13:05 EST (prior fire → now). New items only; anything already in the reported ledger is excluded unless it has a material new development.

- **AI-server PCB supply chain is the new bottleneck:** HSBC/J.P. Morgan say drill bits on dense AI boards wear after <200 holes (vs ~2,000) and Rubin Ultra backplanes could hit 78 layers — China is racing to corner copper foil and micro-drill capacity.
- **Intel Razor Lake-AX update:** a leaker claims the flagship iGPU (32 Xe3P cores) delivers RTX 5070 Ti-class mobile performance; MoP/MoB lines separate at 96 GB / 192 GB LPDDR5X.
- **llama.cpp gained NVFP4 "pass-through" for Inkling 2 Mini** — the native NVFP4 expert tensors can now be converted straight to GGUF, keeping the 4-bit format on-device instead of dequantizing.
- **vLLM added pipeline parallelism + DFly speculative decoding with PD disaggregation for Hy4 Flash**, validated on GB200 with bitwise-equivalent PP output.
- **SGLang tuned the low-M MoE stack on DGX Spark (GB10):** NVFP4 wins 1.03–1.18× across all batch sizes; MXFP8 is now gated to T=1 on SM121 where it actually helps.

## AI hardware and silicon

**China races to control the AI-server PCB bottleneck as board complexity explodes**
*Event date: 2026-11-27 (published 12:00 EST, in-window)*
Tom's Hardware, citing three recent HSBC and J.P. Morgan reports, details how the printed circuit boards underneath AI accelerators are becoming a supply constraint of their own. HSBC says a tungsten-carbide drill bit can wear out after fewer than 200 holes in the denser laminates used for AI boards, versus around 2,000 holes in older materials; it forecasts a backplane in NVIDIA's Rubin Ultra systems could carry 78 layers, against 4–8 for a notebook PCB and 20+ for H100-generation boards. KGI Securities notes hole depth-to-diameter ratios have moved from ~20:1 to 30:1 or 40:1, and HSBC sees a 36% annual rise in holes per board from 2025 to 2028. More than 20 Chinese PCB makers announced expansions in H1 2026, backed by a five-year MIIT/NDRC plan for high-frequency, densely layered boards and low-profile copper foil — but J.P. Morgan's Jerry Tsai warns the learning curve is "measured in years, rather than quarters."
**Why it matters:** GPU supply gets the headlines, but board materials and drilling capacity are a second, less-visible constraint on AI-server output in 2027 — relevant to anyone tracking when high-VRAM inference cards and systems actually reach the channel.
**Local inference:** No direct local-model impact; this is a server/manufacturing-layer story, though board-layer complexity ultimately shapes the cost and availability of the workstation and mini-PC inference hardware in this brief.
Source: Tom's Hardware — "China's AI hardware push is moving beyond GPUs to the PCBs underneath them" (2026-11-27 12:00 EST) — https://www.tomshardware.com/tech-industry/manufacturing/chinas-ai-hardware-push-is-moving-beyond-gpus-to-the-pcbs-underneath-them-beijing-races-to-corner-the-market-on-copper-foil-and-micro-drill-bits-as-board-complexity-explodes

**Update on previously reported — Intel Razor Lake-AX: MoP/MoB split and RTX 5070 Ti-class iGPU claim**
*Event date: 2026-11-27 (published 12:54 EST, in-window)*
Following yesterday's leak that Intel's next-next "Razor Lake-AX" chip uses on-package memory, TechPowerUp reports leaker Jaykihn's split into two lines: MoP (memory-on-package, up to 96 GB LPDDR5X, 16- and 28-core configs for laptops/mini-PCs) and MoB (memory-on-board, up to 192 GB, 28-core, with a possible 52-core dual-tile MoB still undefined). Separately, leaker Haze tweeted that the flagship's 32 Xe3P GPU cores deliver "RTX 5070 Ti mobile-like performance" — a single-line claim the outlet flags as grain-of-salt, since LPDDR5X bandwidth will be the limiting factor. MoP is tipped for late 2027. This is a material new development on the razor-lake-ax-mop+mob entry already in the ledger (which had the 96/192 GB split and 16 PCIe5 + 8 PCIe4 + 4 DMI5 lanes but not the iGPU performance tier).
**Why it matters:** If a 96–192 GB unified-memory APU really lands with RTX 5070 Ti-class graphics, it is a direct competitor to the Ryzen AI Max 395 / DGX Spark class for large local models in laptops and 1-liter boxes. The performance claim is unverified.
**Local inference:** Targets laptop/mini-PC class with 96 GB (MoP) to 192 GB (MoB) LPDDR5X — enough unified memory for 100B-class weights at 4-bit; availability late 2027, rumor status.
Source: TechPowerUp — "Intel 'Razor Lake-AX' Split Into MoP and MoB Lines, iGPU Tipped at RTX 5070 Ti Laptop Level" (2026-11-27 12:54 EST) — https://www.techpowerup.com/355272/intel-razor-lake-ax-split-into-mop-and-mob-lines-igpu-tipped-at-rtx-5070-ti-laptop-level

## Model releases

No new first-party open-weight flagship was first disclosed inside this window. Inkling 2 Mini (Thinking Machines) remains the dominant trending model on Hugging Face, but its release and the day-0 quant wave were covered in earlier editions.

## Community spins and quantizations

**Fresh imatrix quant ladder from mradermacher (in-window)**
*Event dates: 2026-11-27 16:29–16:47 UTC (12:29–12:47 EST), in-window*
Several new GGUF uploads landed inside the window: `mradermacher/Llama-3.2-3B-Thinking-SFT-i1-GGUF` (a reasoning-distilled Llama 3.2 3B trained on the qwen-glm-kimi-distillation-clean dataset), `mradermacher/DaisyRose-27B-Heretic-v2-MTP-i1-GGUF` (a 27B heretic/abliterated spin that keeps the MTP speculative-decoding head), `mradermacher/gemma-4-31B-it-scotoma-2-heretic-i1-GGUF` and `mradermacher/CIG-27B-IPR-i1-GGUF` (a code-based image-generation model). All are imatrix-tuned i1 ladders with published multi-tier file sets.
**Why it matters:** The Thinking-SFT quants bring chain-of-thought behavior to a 3B Llama footprint, and the DaisyRose MTP-preserving quant keeps draft-model spec-decode intact — both are small-VRAM-friendly additions to the local menu.
**Local inference:** The 3B and 27B quants fit consumer cards — roughly 2–4 GB and 16–24 GB at Q4_K_M respectively; 31B tiers need 20+ GB or CPU/RAM offload.
Source: Hugging Face model API — https://huggingface.co/api/models?author=mradermacher&sort=lastModified&direction=-1&limit=25 ; individual cards e.g. https://huggingface.co/mradermacher/DaisyRose-27B-Heretic-v2-MTP-i1-GGUF

## Inference software and benchmarks

**llama.cpp adds native NVFP4 pass-through for Inkling 2 Mini (PR #33062)**
*Event date: merged 2026-11-27 14:44 UTC (09:44 EST), in-window*
A new llama.cpp path lets the Inkling 2 Mini checkpoint — shipped as NVFP4 + MXFP8 + BF16 with 3-D fused expert tensors — be converted directly to an NVFP4 + Q8_0 GGUF, keeping the native 4-bit expert weights rather than dequantizing them. The workflow uses `convert_hf_to_gguf.py --fp8-as-q8` followed by `llama-quantize --tensor-type ...ffn_*_exps.weight=nvfp4`; conversion is gated behind an environment variable, and the author notes Muse Spark Open needs similar adaptation. Same-day llama.cpp merges also fixed an ADD_ID launch above 65,535 tokens (#33162), hardened Vulkan empty-conv3D handling (#33106), and further optimized the Vulkan coopmat2 Gated Delta Net path (#32860).
**Why it matters:** Keeping Inkling 2 Mini's experts in NVFP4 cuts its GGUF footprint and lets Blackwell/DGX Spark hardware use the format natively — a real hardware-fit improvement for the week's most popular local model.
**Local inference:** NVFP4 is a Blackwell-family (SM120/SM121, e.g. RTX 50-series / DGX Spark GB10) format; the Q8_0 non-expert layers keep quality on any backend.
Source: NVIDIA/ggml-org llama.cpp PR #33062 (merged 2026-11-27 14:44 UTC) — https://github.com/ggml-org/llama.cpp/pull/33062

**vLLM ships Hy4 Flash pipeline parallelism and DFly + PD disaggregation (PR #70300)**
*Event date: merged 2026-11-27 14:20 UTC (09:20 EST), in-window*
vLLM now serves the closed Tencent Hy4 Flash model with `--pipeline-parallel-size > 1` and with DFly speculative decoding over NixlConnector or MooncakeConnector — both were previously rejected at startup. Validation on GB200 with Hy4-flash-FP8 shows TP2×PP2 output bitwise-equal to PP=1 and accuracy matching TP; AIME25 scores run 0.978–1.000 across TP4/TP2/TP2×PP2 configs, with DFly acceptance around 38%/43%. A companion kernel change (#65427) avoids splitting rows shorter than k in `top_k_per_row_decode`, and #68968 lets out-of-tree platforms register FP8 MoE experts.
**Why it matters:** PP support lets a long-context 220B-ish model like Hy4 Flash spread across more GPUs without tensor-parallel-only constraints, and DFly + PD disaggregation brings its speculative decoding to split prefill/decode serving.
**Local inference:** Datacenter-class (GB200/H200, FP8, multi-GPU TP×PP); not a single-consumer-GPU configuration, but the patterns flow down to multi-GPU homelabs over time.
Source: vLLM PR #70300 (merged 2026-11-27 14:20 UTC) — https://github.com/vllm-project/vllm/pull/70300 ; coverage of same-window wave — https://github.com/vllm-project/vllm/commits/main

**SGLang tunes the Inkling 2 low-M MoE stack on DGX Spark (GB10) — PR #53557**
*Event date: merged 2026-11-27 13:59 UTC (08:59 EST), in-window*
A follow-up to earlier SM120 low-M kernels (#53368–53370) benchmarks the stack on NVIDIA GB10 (DGX Spark, CC 12.1, 48 SMs): low-M routing wins 1.40–2.65× for all T=1..64, low-M NVFP4 wins 1.03–1.18× across the same range, but the MXFP8 grouped GEMM is 4–5% slower than CUTLASS at T=8..64. The fix gates the MXFP8 path to SM120 or T=1, falling back to the general path on SM121 for larger batches; 24 tests pass bitwise-equal. Same-window SGLang merges also applied Nemotron 3 Nano Omni defaults (#50021), waited for stream-fenced hicache write-backs (#52437), and streamed the GigaChat/magi2 diffusion text encoder layer-by-layer on GPU (#53452).
**Why it matters:** These are the kernels that decide whether Inkling 2 Mini's 26B/3.5B MoE actually runs fast on a 128 GB DGX Spark or RTX 50-series card — the measurements are device-specific and favor native low-bit paths.
**Local inference:** Explicitly tuned for NVIDIA GB10 / DGX Spark (SM121, 128 GB unified) and SM120 RTX 50-series; the T=1 decode case is the target.
Source: SGLang PR #53557 (merged 2026-11-27 13:59 UTC) — https://github.com/sgl-project/sglang/pull/53557

**Also noted (in-window, minor):**
- TensorRT-LLM merged a batch of fixes/docs: batch attention-DP prompt delivery (#22727), Kimi K3 metadata prep coverage (#22915), and a serve-CLI refactor (#17271).
- llama.cpp UI: markdown tables now drawn with row rules (#33195); ggml-zdnn exposes zTensor memory for fitting (#31849).
- SGLang cookbook refresh for diffusion and "Action Models" pages (#53602).

## Just missed the window

- **TSMC AI Design Kit (ADK)** — Tom's Hardware, published ~08:00–08:28 EST, just before the 08:53 window start. TSMC unveiled the ADK at its 2026 Europe OIP Ecosystem Workshop in Munich: process-specific data and methodologies that let AI agents automate physical chip implementation, complementing traditional PDKs, with A14 design-rule count at 3.6× N5. Not re-footnoted in future editions. — https://www.tomshardware.com/tech-industry/semiconductors/tsmc-gives-a-sneap-peak-at-ai-design-kit-agentic-ai-with-foundry-expertise

## Coverage notes

- **Verified items this run:** 10 substantive in-window items (2 hardware, 1 community-quant cluster, 6 inference-software items, 1 update) plus 1 just-missed footnote. Counting unit: distinct underlying events/PRs, not headings.
- **Dedupe gate:** the reported.md ledger was read in full and applied. Roughly 25+ candidates were suppressed as already reported — chiefly the entire 11-27 08:53 fire set (Razor Lake MoP/MoB base leak, RDNA5 Linux 7.5, Vesper-31B heretic, llama.cpp #28898/#33133, vLLM/SGLang/TensorRT-LLM overnight waves) plus the 11-26 fires (A14 PDK, Kioxia GP2, GigaChat4-37B, R9700 RDNA benchmark write-up, OCRia-1 NVFP4). The 08:53 fire listed no just-missed footnotes, so none were repeat-suppressed this run.
- **Unavailable lane:** Reddit (r/LocalLLaMA) — both the search `.rss` and the `new.rss` fallback returned HTTP 404 from this host; logged as unavailable, not as a negative finding. HN Algolia river was reachable and swept (llama.cpp / local LLM / GPU AI queries), but its highest-scoring local-AI item (the AMD-specific llama.cpp fork write-up) was already reported in the 11-26 13:24 fire.
- **Thin hardware press day:** most mainstream hardware coverage in the window was Black Friday deal posts (motherboards, monitors, gaming laptops) or pre-window items (Nvidia $290M crypto-settlement, Kioxia XL-Flash Gen3 3µs flash). No new MLPerf/llama.cpp/vLLM benchmark result was published by a vendor in-window; the benchmark numbers above come from PR descriptions in the projects' own repos.
