# AI Hardware & Local Models Brief — Nov 12, 2026 · 12:45→17:45 ET

**Window:** 2026-11-12 12:45 EST → 17:45 EST (UTC 17:45–22:45). New-items-only brief; all events first disclosed inside the window or material updates to ledger items.

## TL;DR
- A forensic report published this afternoon claims six flagship open-weight models from DeepSeek, Moonshot, MiniMax and Ant Group carry numerical fingerprints proving they were initialized on export-controlled NVIDIA Blackwell GPUs (B200/B300/GB200/GB300), confirmed on physical chips for two models.
- Thinking Machines shipped the Apache-2.0 open weights for Inkling 2 Small (594B/17B MoE, 1M ctx) — NVFP4 needs ~500 GB aggregate VRAM (2x B300 / 4x H200 class); day-0 recipes landed in vLLM, SGLang, TokenSpeed and Unsloth.
- Cerebras posted record Q3 (core revenue $230.9M, +70% YoY), two hyperscaler wins incl. Oracle OCI, 700 MW contracted, and WSE5 back from fab in system test; CS-5 targets 10,000 tok/s in mid-2027.
- The NVIDIA CEO shakeup deepened with a Senate Finance investigation into alleged kickbacks on illegal Blackwell sales to China, plus Bloomberg reporting on pre-exit pressure to expand NVIDIA's AI backstop guarantees.
- RustingStrata's author published Q8_0/BF16 datacenter benchmarks: 508 tok/s decode (MTP) for Qwen3.6-35B-A3B on a single H100.

---

## AI hardware and silicon

**Forensic report: six Chinese open-weight models fingerprint to export-controlled Blackwell GPUs** — Event date: November 12, 2026 (this window; HN ~21:49Z)
Will DePue (Unemployment Labs) published checkpoint-forensics research finding six models released since April 2026 — DeepSeek-V4-Flash (B200/B300, 148 SMs), DeepSeek-V4-Pro, Kimi K3, Kimi K3.1-Pro and Ant's Ling-3.1-flash (GB200/GB300, 152 SMs), MiniMax-M3 (B200/B300) — contain weight-initialization patterns matching Blackwell hardware, recovered from the public weights themselves; DeepSeek-V4-Flash and Kimi K3 matches were confirmed on physical Blackwell chips. Earlier models from the same labs (DeepSeek-V3, Kimi K2/K2.5, MiniMax-M2.7, Ling-3.0-flash) read as Hopper. Match strengths run 49–1,385 sigma against a ~8-sigma chance ceiling over ~6 trillion seed/layout combinations; the method reproduces all 24 publicly-disclosed hardware cases, including NVIDIA's own Nemotron-3 Ultra. The report sizes each run at roughly 500–3,000 Blackwell GPUs over 2–3 months ($150–200M per cluster at ~$70k/GPU) and notes Alibaba/Qwen, Zhipu/GLM, Tencent, Xiaomi and StepFun still read Hopper, while Meituan LongCat shows no NVIDIA fingerprint. The author is explicit this supports but does not prove Blackwell training, and does not establish where the chips are or that export controls were violated.
**Why it matters:** This is the first public technical evidence backing government allegations about smuggled Blackwells, and it lands the same day as the Senate probe below — with direct implications for China access policy, NCP/geo-attestation pressure, and the credibility of "trained on Ascend/Huawei" narratives. For homelab readers it is also a live demo of checkpoint-forensics: weights leak hardware provenance.
**Local inference:** Not a local-run item; relevant to everyone serving these same open weights (DeepSeek-V4.1-Flash, Kimi K3, MiniMax-M3 lineups) — provenance now carries a hardware-forensics layer.
Source: "Top Chinese Frontier AI Models Carry the Fingerprint of Export-Controlled NVIDIA Blackwell GPUs," Will DePue, Unemployment Labs, November 12, 2026 — https://unemploymentlabs.com/checkpoint-forensics
Source: Hacker News story 50474813, 2026-11-12T21:49Z — https://news.ycombinator.com/item?id=50474813 (tweet: https://twitter.com/willdepue/status/2120992457630326841)

**Cerebras Q3 2026: record revenue, two hyperscaler wins, WSE5 already in system test** — Event date: November 12, 2026 (released after market close, ~21:05Z)
Cerebras reported record Q3 core revenue of $230.9M (+70% YoY; GAAP $210.8M, +55%), core cloud revenue $144.9M (+272%), 41% core gross margin, $26.1B RPO and $7.8B liquidity; Q4 guide $310–330M core. Commercial wins: Oracle Cloud Infrastructure will integrate Cerebras fast inference in 2027, plus an unnamed "top-three hyperscaler" cloud deal (Techmeme/Bloomberg sourcing identifies Microsoft). Capacity: 700 gross MW contracted through end-2027 with new Norway/Finland sites; manufacturing with Flex/Sanmina to exit 2026 at >10x capacity. Silicon roadmap: CS-4 (up to 30x faster inference) entering full production, CS-5 targeted early summer 2027 at up to 10,000 tokens/s/user, and Wafer Scale Engine 5 is already back from the fab, brought up and in system test — tape-out to system test in under five months. Day-zero Kimi K3.1 serving claimed at up to 2,000 tok/s. Stock +15% after hours.
**Why it matters:** Wafer-scale is turning from a speed stunt into a contracted capacity business (700 MW) right as NVIDIA's leadership crisis puts "NVIDIA-only" procurement assumptions under review — heterogeneous inference (Cerebras prefill/decode split) is being pitched at exactly the moment clouds are diversifying.
**Local inference:** Datacenter-class, not homelab; the practical read-through is more offload options — Cerebras-hosted Kimi K3.1/Inkling-class serving at thousands of tok/s gives local-stack users fast "burst to cloud" targets.
Source: "Cerebras Systems Delivers Record Revenue in Third Quarter 2026, Raises Full-Year Outlook," Cerebras IR (GlobeNewswire), November 12, 2026 — https://investors.cerebras.ai/news-releases/news-release-details/cerebras-systems-delivers-record-revenue-third-quarter-2026
Source: Newsquawk wire, November 12, 2026 — https://www.newsquawk.com/headlines/cerebras-systems-inc-cbrs-q3-2026-usd-core-revenue-2309mln-exp-2147mln

**Update on previously reported (NVIDIA CEO transition, reported today 12:45): chip-sales probe opens; backstop pressure detailed** — Event date: November 12, 2026, afternoon developments
Within the window: Sen. Wyden and Rep. Neal launched a Senate Finance investigation into an alleged "administration scheme to take a cut" of illegal NVIDIA AI chip sales to Chinese companies (press release posted ~19:30Z), citing ignored warnings about the former CEO; Bloomberg reported (21:53Z HN) NVIDIA faced pressure to enlarge its AI backstop guarantees (e.g., the OpenAI deal) before Huang's exit; Reuters reported (19:51Z) a second employee relationship, and Forbes (20:36Z) a third affair with another NVIDIA employee; House Foreign Affairs Chair Mast said Huang agreed to testify under oath before Thanksgiving; interim CEO Colette Kress's staff email promised a November 18 all-hands; Bloomberg sources say Chinese AI firms are rushing to rent overseas NVIDIA cloud capacity amid tighter-enforcement fears; SemiAnalysis published "Nvidia and the World Without Jensen" (~18:50Z). November 17 earnings is now the marquee date.
**Why it matters:** The hardware-relevant core: export-control enforcement on Blackwell is becoming an active US legislative investigation on the same day the forensic paper dropped — expect tighter NCP attestation, supply uncertainty for China-adjacent clusters, and questions about continuity of Huang-era deals (backstops, MGX, Spark) under an interim CEO.
**Local inference:** No direct device impact; watch RTX Spark/DGX roadmap commitments and CUDA/driver cadence across the Nov 17 earnings and the Nov 18 staff meeting.
Source: Senate Finance Committee press release (Wyden/Neal), November 12, 2026 — https://www.finance.senate.gov/newsroom/press-releases/wyden-and-neal-launch-investigation-of-trump-administration-scheme-to-take-cut-of-nvidia-ai-chip-sales-to-chinese-companies-trump-and-commerce-department-reportedly-ignored-warnings-about-former-ceo/
Source: Bloomberg, 2026-11-12 — https://www.bloomberg.com/news/articles/2026-11-12/nvidia-faced-pressure-to-boost-its-ai-backstop-before-huang-exit
Source: SemiAnalysis, 2026-11-12 ~18:50Z — https://newsletter.semianalysis.com/p/nvidia-and-the-world-without-jensen

**Update on previously reported (Nov 11: radeon-ssd-write-blocker): Adrenalin 26.11.1 ships the fix** — Event date: November 12, 2026 (~20:50Z)
AMD released Adrenalin 26.11.1 (driver 26.20.15.16) as an optional update, delivering the promised fix for the atieclxx.exe bug that wrote 0.3–5 MB/s (26–432 GB/day) to the system drive at idle on RX 7000+; it also fixes writes during window moves/resizes. Known issues include crashes in several titles and SAM possibly toggling off after install on Win10 (re-enable ReBAR if you disabled Resizable BAR as the workaround). Community Necandi blocker tool no longer needed; early commenters split on whether the fix fully sticks.
**Why it matters:** Radeon workstation cards (the R9700/AI Pro tier many local-AI builds use) spend idle time in AI-serving rigs hammering SSD endurance; this closes a two-week-old endurance risk and restores confidence in 26.10.x-era deployments.
**Local inference:** Directly relevant to any RX 7000/9000 local-inference box left idling between jobs — update and re-check disk-write counters; keep ReBAR enabled.
Source: "AMD Patches Radeon SSD Write Bug in Adrenalin 26.11.1 Optional Driver," TechPowerUp, November 12, 2026 — https://www.techpowerup.com/354756/amd-patches-radeon-ssd-write-bug-in-adrenalin-26-11-1-optional-driver
Source: AMD Software: Adrenalin Edition 26.11.1 Release Notes — https://www.amd.com/en/resources/support-articles/release-notes/RN-RAD-WIN-26-11-1.html

---

## Model releases

**Update on previously reported (Nov 5: "weights promised next week"): Inkling 2 Small open weights are live** — Event date: November 12, 2026 (~21:35Z, HF lastModified; rL 22:25Z)
Thinking Machines published Apache-2.0 weights for thinkingmachines/Inkling-2-Small and an MXFP8 variant: 594B total / 17B active, 48-layer MoE (8-of-384 experts), hybrid local/global attention, native multimodal (ViT image encoder), 1M-token context, pretrained end-to-end in NVFP4. Model card hardware guidance: NVFP4 checkpoint needs ≥500 GB aggregate VRAM (2x B300 or 4x H200); MXFP8 needs ~800 GB (4x B300 / 8x H200), and TML says the MXFP8's 1–2 eval-point gain "is not meaningful" — NVFP4 is the recommended path. Eval table positions it atop the open-weight flash class (AA Index v5.0 30.6 vs GLM-5.3-Flash 27.8, MiniMax-M3.1-Flash 29.1; SWEBench Pro Hard 86.3). Day-0 recipes: SGLang, vLLM (merged #67319/#67322 this window), TokenSpeed, Unsloth; post-training via Miles/SkyRL/Snowflake; API via NVIDIA, Fireworks, Together, Baseten, Nebius, Modal, Cloudflare.
**Why it matters:** A frontier-adjacent flash model with genuinely open weights (not a teaser) and natively-NVFP4 training is the strongest argument yet that FP4-native checkpoints — not post-hoc quants — are the 2027 serving default; it also validates the "next week" cadence TML promised on Nov 5.
**Local inference:** Above consumer hardware (500–800 GB VRAM class per the card); the realistic local path is community quants (Unsloth has a recipe; expect GGUF/MLX tiers in coming runs) or offload to TML's partner clouds.
Source: thinkingmachines/Inkling-2-Small model card, Hugging Face, 2026-11-12 — https://huggingface.co/thinkingmachines/Inkling-2-Small
Source: r/LocalLLaMA release thread, 2026-11-12T22:25Z — https://www.reddit.com/r/LocalLLaMA/comments/1xuqplg/thinkingmachinesinkling2small_is_released/

**Community index: ERNIE 5.5 Flash Preview debuts strong in agentic coding** — Event date: November 12, 2026 (rL thread 20:13Z)
A community member's 12-benchmark "agentic coding" aggregate index (Terminal-Bench 4.0, DeepSWE, FrontierCode 2.0, SWE-bench Pro et al., distinct from AA) puts Baidu's "Ernie 5.5 Flash Preview" — 280B total / 14B active — near DeepSeek-V4.1-Flash-tier coding performance at roughly half the active parameters. Commenters immediately flagged the serving reality: ERNIE is BF16-native (~560 GB weights) vs DSv4.1-Flash's MXFP4-native (~280 GB), and "apples-to-apples would be Ernie at Q4"; context spec still unlisted. Recall: the ERNIE-5.5-Flash-Base checkpoint (FP8+BF16, 1M infer ctx) quietly landed Nov 2–5, and Baidu said full release is ~two weeks out.
**Why it matters:** Another 280B-class hybrid entering the flash-MoE price/perf war before its weights are formally GA'd; for self-hosters the native precision choice (BF16 vs MXFP4) is the whole ballgame, and Baidu has now shown it can compete on the coding board.
**Local inference:** Not runnable on consumer hardware at native precision (560 GB class); the released base checkpoint's FP8/quant path is the one to watch once GA'd.
Source: r/LocalLLaMA thread, 2026-11-12T20:13Z — https://www.reddit.com/r/LocalLLaMA/comments/1xune1i/new_model_ernie_55_flash_preview_hits_strongly_at/

---

## Community spins and quantizations

**mradermacher afternoon quant ladder (in-window uploads)** — Event dates: November 12, 2026, 18:45–22:20Z
New GGUF repos this window: Summerwise-27B-GGUF (22:03Z; Pleochroic literary/creative-writing SFT+DPO of Qwen3.8-27B, Apache-2.0, zh+en long-form), Qwen3-REAP-15B-A3B-i1-GGUF (21:52Z; imatrix tier of atbender's expert-pruned 15B-A3B MoE — the plain GGUF was reported at 12:45, this is the new i1 tier), VFIG-8B-GGUF (22:20Z; XunmeiLiu GRPO-trained SVG/vector-figure-understanding VLM, CC-BY-4.0), OptiScene-Qwen3-8B-GGUF (21:52Z; indoor 3D scene layout SFT+DPO on the 3D-SynthPlace dataset, MIT), Qwen3.8-27B-CodeForge-3xMerge-UNCENSORED-heretic-i1-GGUF (20:xxZ; coding/reasoning efficient-thinking merge), Velary-2.0-1.7B-GGUF (18:45Z; AuroraSystem Russian-English LFM3-architecture reasoning SLM), Mistral-7B-Liberated-i1-GGUF (20:37Z; abliteration classic).
**Why it matters:** Steady day-0 coverage keeps fresh research spins immediately loadable on llama.cpp/Strata-class rigs; Summerwise and CodeForge extend the Qwen3.8-27B spin ecosystem, VFIG is a rare vector-graphics RL spin.
**Local inference:** All llama.cpp-native; Summerwise/CodeForge Q4_K_M tiers ~16–18 GB (RTX 5060 Ti 16GB class at Q4 with offload), REAP-15B-A3B Q4 ~8–9 GB fits 12GB cards, VFIG-8B/OptiScene/Qwen3.5-4B-class files run comfortably on 8–12 GB, Velary-1.7B and Mistral-7B run on anything.
Source: mradermacher Hugging Face (created 2026-11-12T18:45–22:20Z) — https://huggingface.co/mradermacher

**Swanada: 12-language Indic + Classical Sanskrit offline STT in the browser** — Event date: November 12, 2026 (rL 19:50Z)
Builder init0 (of Ninada in-browser Indic TTS) released Swanada, a speech-to-text engine covering 12 Indian languages plus Classical Sanskrit that runs fully offline in the browser and on phones with zero cloud calls; in-thread discussion covers WebAssembly/GPU model loading and memory pressure on mobile Safari and low-RAM Android.
**Why it matters:** On-device ASR for low-resource/Indic languages has been a persistent gap — a zero-cloud, phone-capable implementation matters for privacy-sensitive deployments and for local-voice stacks that currently bail to cloud APIs for these languages.
**Local inference:** Runs in-browser/on-device (no GPU floor beyond modern WebGPU/WebAssembly hardware); pairs with Ninada TTS for a fully local Indic voice loop.
Source: r/LocalLLaMA thread, 2026-11-12T19:50Z — https://www.reddit.com/r/LocalLLaMA/comments/1xumryr/got_great_feedback_on_ninada_inbrowser_indic_tts/

---

## Inference software and benchmarks

**RustingStrata datacenter benchmark: Q8_0 and BF16 Qwen3.6-35B-A3B on A100/H100/L40S** — Event date: November 12, 2026 (rL 21:03Z)
Serious_Scarcity7818 published the high-quant side of the pure-Rust RustingStrata engine (NVRTC-compiled CUDA kernels, same kernels on Vulkan, no ggml): with the whole model in VRAM, H100 hits 11,486 tok/s prefill (4K prompt) / 290 tok/s decode / 508 tok/s with MTP at Q8_0 (37.8 GB); BF16 (71.1 GB) on H100 NVL 8,691/230/324; A100-80G Q8_0 6,507/193/288; L40S Q8_0 9,542/165/270. Counter-intuitive results: Q8_0 decodes faster than Q2_K_XL on A100/H100 (193 vs 174; 290 vs 265) though slower on L40S (165 vs 214); MTP gains shrink as quant quality rises (1.4–1.9x at Q2_K_XL/Q8_0, 1.2–1.4x BF16); Q8_0 on a 24 GB A30 (63% experts resident) still does 130 tok/s decode, 200 with MTP, while BF16 on the same card collapses to ~12.5 tok/s.
**Why it matters:** Hard numbers that quant choice is bandwidth- vs compute-bound per card — high quants are not automatically slower, which argues for per-GPU quant selection in serving stacks; and a no-ggml Rust engine matching datacenter expectations keeps pressure on the llama.cpp status quo.
**Local inference:** The Q8_0>Q2_K_XL decode reversals are consumer-relevant on bandwidth-poor cards; expert-residency math (63% experts → 130 t/s) directly informs 16–24 GB + DDR5 offload builds. Author-run.
Source: r/LocalLLaMA thread, 2026-11-12T21:03Z — https://www.reddit.com/r/LocalLLaMA/comments/1xuon4m/what_does_qwen3635ba3b_look_like_at_q8_0_and_bf16/

**vLLM afternoon merge wave: Inkling 2 Small day-0, FlashInfer 0.7.4, RDNA/MXFP4 kernels** — Event date: November 12, 2026, 18:00–22:20Z (GitHub merged)
18 PRs in window, highlights: Inkling 2 Small recipe + image (#67319/#67322), FlashInfer bump to 0.7.4 (#67042), ROCm/RDNA trio — gfx11 decode-kernel VGPR-spill workaround (#67226), triton_kernels on ROCm 3.8.x RDNA MXFP4 configs (#64331), w2-scatter + topk-sum MoE combine on RDNA (#64055), HiSparse GLM-5.3-Flash support (#61243), spec-decode draft-as-single-PP-stage (#66063), extended NVFP4 manual activation fusion (#65149), Kimi K3 into AMD DI nightly CI (#67056), CUDA-graph pool reset fix (#67201).
**Why it matters:** The RDNA MXFP4/MoE kernel cluster is the most consumer-relevant line — AMD's RDNA3/4 MoE decode path keeps closing on CUDA; day-0 Inkling support keeps the new open flagship servable on day 1.
**Local inference:** RDNA MXFP4 config work benefits RX 9070-class MoE serving (Strix Halo/R9700 adjacent); Inkling itself is datacenter-class.
Source: vLLM GitHub merged-PR search (window 2026-11-12T17:45Z–22:45Z) — https://github.com/vllm-project/vllm

**SGLang afternoon wave: Inkling cookbook, sgl-fa3 wheel packaging, ROCm diffusion ring attention** — Event date: November 12, 2026, 18:22–22:24Z (GitHub merged)
11 PRs: Inkling-2-Small deployment cookbook (#50185), sgl-fa3 standalone FA3 wheel build workflow + cp310-abi3 packaging (#49972/#50193), sgl-router configurable body cap via SGLANG_ROUTER_MAX_BODY_BYTES (#50161) and PD gap-closure toward replacing sgl-model-gateway (#50052), HiCache prefetch trailing-window key fix (#50056), diffusion dead-code cleanup series (#49841–43), and ROCm diffusion ring attention on aiter with reused K/V ring buffers (#49078).
**Why it matters:** sgl-fa3 as a packaged wheel lowers the FA3 adoption barrier outside CUDA-master builds; the router work moves SGLang toward a single gateway story; ROCm ring attention extends multi-GPU diffusion (image/video) to AMD.
**Local inference:** Router/HiCache fixes touch self-hosted multi-model deployments; ring attention matters for multi-Radeon diffusion boxes.
Source: SGLang GitHub merged-PR search (window 2026-11-12T17:45Z–22:45Z) — https://github.com/sgl-project/sglang

**llama.cpp b12329: prune wave + Hexagon Windows-ARM64 host build** — Event date: November 12, 2026 (build published 22:22:59Z)
Five merges since the 12:45 report (b12319–b12320): "llama: prune" (#30565) and "ggml: prune" (#29801) dead-code removal at 18:58–18:59Z, fix(hexagon) host build with clang-cl on Windows ARM64 (#32011, 21:52Z — Snapdragon-hosted builds unblocked), and two server CI hardening fixes (#32166/#32167). No new features; hygiene release with one real-platform fix.
**Why it matters:** The Hexagon clang-cl fix widens the Windows-on-ARM (Snapdragon X/RTX Spark-adjacent) build story; prune releases reduce attack/maintenance surface in the most self-compiled inference codebase on earth.
**Local inference:** Grab b12329 if you build for ARM Windows/Hexagon NPU; otherwise no behavior change.
Source: llama.cpp GitHub release b12329, 2026-11-12T22:22Z — https://github.com/ggml-org/llama.cpp/releases/tag/b12329

**Evolving benchmark: LLMs reverse-engineer a mystery audio codec (local models on the board)** — Event date: November 12, 2026 (rL 21:34Z)
Character-Result-2 released a four-stage "moving target" benchmark: given a mystery compressed audio file, the model must deduce the format, write a decoder, then build a better encoder (Python+numpy only, run locally via OpenCode); stages progressively reveal reference audio, codec info, then an oracle tool. Current "Evolve 2" leaderboard: Claude Fable 127 (stage 1), DeepSeek V4.1 Flash 1017 at 81 (stage 3), Qwen3.8-Flash-Next NVFP4 at 67 (stage 3), Q3_S 56 (stage 4). In-thread critique: SNR is a weak codec metric (PQeval suggested); promotion testing questioned.
**Why it matters:** A genuinely anti-benchmaxxing eval (evolving targets) that ranks the open-weight flash models people actually run locally on algorithmic-understanding tasks rather than instruction-following.
**Local inference:** Qwen3.8-Flash-Next NVFP4 (single-consumer-GPU class) placing 3rd behind only flagship closed/API models on a novel reverse-engineering task is a good local-agent signal. Author-run, unreviewed.
Source: r/LocalLLaMA thread, 2026-11-12T21:34Z — https://www.reddit.com/r/LocalLLaMA/comments/1xupf99/i_built_a_benchmark_where_llms_reverseengineer_an/

---

## Also noted
- r/LocalLLaMA (20:00Z): "MoE and expert offloading on llama, exllamav3, strata on RX 9070 XT" — fresh same-GPU engine-offload comparison from willeyh (discussion-level, no published table in the feed). https://www.reddit.com/r/LocalLLaMA/comments/1xun15v/moe_and_expert_offloading_on_llama_exllamav3/
- r/LocalLLaMA (20:29Z): users report a guardrail topic-avoidance pattern inside qwen4-max-preview thinking traces (update-adjacent to the Nov 11 preview item; unconfirmed, discussion-level). https://www.reddit.com/r/LocalLLaMA/comments/1xunsz9/guardrail_in_qwen4maxpreview_thinking_trace/
- VideoCardz (~17:50Z): "Jensen Huang officially resigns as NVIDIA CEO after 33 years" — confirmation coverage of the already-reported transition, no new facts.

## Just missed the window
- NVIDIA Snapshot (Nov 10, developer blog): open-source GPU checkpoint/restore for Kubernetes (DSX AI Factory platform) — restores initialized vLLM/SGLang/TRT-LLM replicas in 3.6–42.7 s (8–36x faster than cold start), TP/DEP/DCP multi-GPU MoE up to 1 TB checkpoints; Crusoe cut GLM-5.3 pod readiness 8 min→31 s on HGX B300. Disclosed two days pre-window; footnoted once. — https://developer.nvidia.com/blog/start-and-restore-gpu-workloads-in-seconds-with-nvidia-snapshot/

## Coverage notes
- Verified items: 11 qualifying (4 lead hardware/silicon incl. 1 update, 2 model releases, 2 community, 4 software/benchmarks) + 3 also-noted (discussion-level) + 1 missed-window footnote. All items fetched; lead pages read in full with in-window timestamps.
- Sources swept this window: Unemployment Labs, Cerebras IR, Senate Finance, Bloomberg, Reuters, Forbes, SemiAnalysis, TechPowerUp, AMD, VideoCardz, The Register, ServeTheHome, Techmeme river, Hacker News Algolia, r/LocalLLaMA (new river + 4 threads), GitHub merged-PR search (llama.cpp/vLLM/SGLang/ollama/TensorRT-LLM releases), Hugging Face API (mradermacher/bartowski/unsloth/thinkingmachines).
- Ledger dedupe: ~12 candidates suppressed as already reported — NVIDIA CEO re-coverage (VideoCardz/Reuters/USA Today profile pieces), llama.cpp Intel Arc Vulkan FA #29357 (reported 07:45), mradermacher Qwen3.5-4B-Arete / Qwen3-REAP plain GGUF / bartowski Barcenas+MoganOCR (reported 12:45), LexiSuite AIO re-post (Nov 8), plus 3 repeat-footnote blocks from `missed:` tags (CMP170HX voltage-glitch re-post 17:14Z, MetaRoCE STH article, TPU 8tx/8i Max items).
- Dead ends: TechPowerUp news index 403 (used per-article URL from search instead — fetched fine); Reddit thread fetch for the 22:39Z visual-challenge post returned no usable body (excluded); bartowski/unsloth showed no in-window uploads after 17:45Z (confirmed, not a failure).
- Out-of-scope by rule (not counted): Google Gemini 4 Hydrogen API launch, Microsoft 365 outage, AMD gaming-GPU China supply cuts (gaming-only angle), ZEPHYR/VASTARMOR AIB and monitor posts, SpaceX/Tesla merger, Anthropic IPO oversubscription.
