# AI Hardware & Local Models Brief: Panther Lake Robotics Box, Ollama Inkling Speedups, Tencent Quant Wave

**Window:** Monday, Nov 23 17:45 → Tuesday, Nov 24 08:12 EST (previous fire → now; new items only, ledger-deduped)

**TL;DR**
- ASRock Industrial launched the RBK-100 "Robotics-in-a-Box" — a Panther Lake Core Ultra X7 358H NUC with two 6-DOF arms and pre-installed Physical-AI training stack (ACT, Pi0.5, SmolVLA), built with Intel; pricing TBD.
- Ollama v0.40.18 shipped overnight with the first Apple-Silicon-native Inkling 2 Mini speedups (fused Metal residual mixing, faster decode/prefill/draft rounds) and a llama.cpp b12651 bump.
- Unsloth published a native ModelOpt 8-bit safetensors build of Inkling 2 Mini (~20 GB, MTP drafter included) — the first non-GGUF day-two tier for the trending MoE.
- Baidu's ERNIE 5.5 Flash Preview instruct weights landed Monday at 04:00 EST — just before this window (footnoted) — but the day-0 vLLM recipe and SGLang cookbook updates merged inside the window.
- Quiet hardware market signal: a Korean report says Samsung's 2027 DRAM capacity is effectively sold out, with only 60–70% of demand coverable (footnoted).

---

## AI hardware and silicon

**ASRock Industrial RBK-100 "Robotics-in-a-Box" puts Panther Lake at the edge of Physical AI** — announced Tuesday Nov 24 (vendor PR dated Nov 24, Taipei)

ASRock Industrial, with Intel, launched the RBK-100, an integrated Physical-AI development kit built around the company's NUC BOX-358H mini-PC powered by the Intel Core Ultra X7 Processor 358H (Panther Lake). It bundles an RGB-D depth camera, a 6-DOF leader-and-follower robotic arm pair (record demonstrations on one arm, train models to reproduce them on the other), and pre-installed AiRoboStarter software covering demonstration recording, dataset review, model training, and skill execution — with ACT, Pi0.5 and SmolVLA workflows named at launch. The standard configuration ships 32 GB DDR5-5600; the Advanced configuration doubles to 64 GB and adds an agentic stack (STT/TTS, LLM reasoning, object detection, web search, custom APIs). Pricing was not announced.

*Why it matters:* This is the clearest sign yet that small VLAs and imitation-learning policy models (SmolVLA/Pi0.5 class) are becoming the default "local AI workload" for the edge-box market, and Intel is using Panther Lake to chase NVIDIA's Jetson/Thor turf with a x86, CUDA-free story aimed at labs and classrooms rather than miners of tokens.

*Local inference:* The NUC BOX-358H is the same Core Ultra X7 358H platform this brief measured earlier via the LattePanda Sigma-2 (ledger, Oct 22): ~180 TOPS INT8 combined Arc B390 iGPU + NPU, 64 GB LPDDR5 tier comfortably serving Qwen3.6-35B-A3B INT4 at ~30 tok/s. The RBK-100's 32–64 GB DDR5-5600 envelope fits 4B-class VLA policies and small quantized VLMs locally; nothing frontier-scale.

Sources:
- "ASRock Industrial Unveils Robotics-in-a-Box to Democratize Physical AI Development" — TechPowerUp press release, Tue Nov 24 2026, 03:32 EST — https://www.techpowerup.com/355151/asrock-industrial-unveils-robotics-in-a-box-to-democratize-physical-ai-development
- "ASRock launches Panther Lake Mini-PC for robotics with two robotic arms and up to 64GB RAM" — VideoCardz, Tue Nov 24 2026, 07:40 GMT — https://videocardz.com/newz/asrock-launches-panther-lake-mini-pc-for-robotics-with-two-robotic-arms-and-up-to-64gb-ram
- Vendor PR — ASRock Industrial, Nov 24, 2026 — https://www.asrockind.com/en-gb/article/326

**CyberPowerPC starts selling the ASRock AI BOX-A395 (Ryzen AI Max+ 395, 128 GB) as a compact AI workstation** — Nov 23, 17:46 EST (press release)

CyberPowerPC announced it will carry ASRock Industrial's AI BOX-A395 — 16-core Ryzen AI Max+ 395, 128 GB LPDDR5X, Radeon 8060S iGPU — in a compact desktop footprint, and previewed an enterprise-grade Ryzen AI Max+ PRO 495 "AI Workstation Series" planned for Q4 2026. Update on the AI BOX-A395 platform ServeTheHome reviewed in late October.

*Why it matters:* More retail channels for Strix Halo-class 128 GB unified-memory boxes keeps supply pressure off the DGX Spark/RTX Spark alternatives at the 32GB+ tier, and the PRO 495 preview signals AMD's move upmarket for fleet local inference.

*Local inference:* 395-class 128 GB unified memory — this brief's ledger covers the platform's measured range (all 384 experts of DSv4.1-Flash resident at 120.6 GB; Qwen3.8-Flash-Next tiers at ~100 tok/s class). No pricing in the PR.

Source: "CyberPowerPC Expands AI Workstation Line with Compact AMD Ryzen AI Max+ 395 Powered Desktop" — TechPowerUp press release, Mon Nov 23 2026, 17:46 EST — https://www.techpowerup.com/355143/cyberpowerpc-expands-ai-workstation-line-with-compact-amd-ryzen-ai-max-395-powered-desktop

---

## Model releases

**Unsloth ships a native 8-bit ModelOpt build of Inkling 2 Mini — first non-GGUF day-two tier** — Nov 24 (repo created 11:55 UTC, i.e. 06:55 EST)

Unsloth uploaded `unsloth/Inkling-2-Mini`, a single-file ModelOpt-quantized safetensors build of Thinking Machines' trending 26B/3.5B loop-MoE multimodal: the weight inventory is roughly 4.1B FP8 (E4M3) + 20.4B packed U8 elements plus 1.9B BF16, ~20 GB on disk, with the MTP drafter (`mtp.safetensors`) kept. Update on the Inkling 2 Mini quant stampede this brief has tracked since Nov 21.

*Why it matters:* ModelOpt NVFP4/FP8 is the checkpoint format vLLM/SGLang/TRT-LLM consume natively; a maintained Unsloth tier gives non-llama.cpp users a second supported path and puts day-2 quality comparisons (vs the native NVFP4 and MXFP8 tiers) on the table.

*Local inference:* ~20 GB FP8/8-bit fits single 24 GB cards (RTX 4500/5080 class) with headroom for long context; on 16 GB cards only the GGUF/EXL3 sub-16GB tiers or expert-offload engines apply. The native build targets SM100/SM120 data-center and Blackwell consumer serving.

Source: Hugging Face API record for `unsloth/Inkling-2-Mini` — created 2026-11-24T11:55:31Z — https://huggingface.co/unsloth/Inkling-2-Mini

---

## Community spins and quantizations

**bartowski's overnight ladder: next3-pro mystery model, MoganOCR 1.5, and a LingSante health-VL quant** — Nov 23–24 (00:10–11:25 UTC)

bartowski pushed three in-window ladders: `thelamapi_next3-pro-GGUF` (imatrix quant of the thelamapi "next3-pro" mystery vision-language coder this brief flagged Nov 20, 262K-style long-context tags, tool-use), `moganai_MoganOCR-TR-1.5-GGUF` (day-0 quant of the updated Turkish OCR VLM, GRPO-trained, Apache-2.0), and `LingSante-3.1-air-VL-GGUF` (imatrix quant of InclusionAI's ~125B-param LingSante healthcare multimodal, MIT license; base repo Nov 19).

*Why it matters:* The next3-pro ladder is the first full quant of a trending unattributed base — treat provenance as unverified, the same posture applied to last week's QFN-EXL3 URL-fetch incident. The MoganOCR and LingSante tiers keep small vertical VLMs (OCR, medical) locally usable within a few GB to a few-tens-of-GB.

*Local inference:* next3-pro and MoganOCR tiers are 4B–9B-class and run on 8–16 GB cards; the LingSante-3.1-air-VL quant ladder is the only way the ~125B MIT base approaches consumer memory — expect tens of GB at imatrix mid-tiers, i.e. 32–48 GB cards or offload engines.

Sources:
- https://huggingface.co/bartowski/thelamapi_next3-pro-GGUF (created 2026-11-24T11:25:04Z)
- https://huggingface.co/bartowski/moganai_MoganOCR-TR-1.5-GGUF (created 2026-11-24T10:45:33Z)
- https://huggingface.co/bartowski/LingSante-3.1-air-VL-GGUF (created 2026-11-24T00:10:10Z)

**mradermacher's morning wave: Tencent WePocket/WeMM quants, BLIP-Live, a new Ling-3.2-air tier — and a "249B" base that is actually 2.2B** — Nov 24 (02:04–13:01 UTC)

The in-window mradermacher ladders: `WePocketModel1.0-2B/4B-GGUF` (+i1 tiers) of Tencent's new on-device multimodal-embedding "structured decision" models (Qwen3.5-2B/4B based, Apache-2.0, arXiv 2611.27365; base repos live since Nov 16), `WeMM-Ecom-4B-GGUF` + `WeMM-Ecom-35B-A3B-i1-GGUF` of Tencent's e-commerce multimodal MoE (arXiv 2611.30662), `BLIP-Live-i1-GGUF` (Salesforce's video-streaming VLM on Qwen3.5-35B-A3B, CC-BY-NC-4.0, arXiv 2611.21553), a new `Ling-3.2-air-GGUF` tier (update on the Nov 23 ling-3.2-air entry), `MiMo-V2.6-Flash-RL-heretic-i1`, `Llama-3.3-70B-Instruct-apostate-i1`, `Wisp-2-0.8B-i1` — and `lexi-2-249b-base-GGUF`/i1, a quant of Reallexi's "lexi-2-249b" which the Hub safetensors metadata shows is a 2.24B-param Qwen2 (the name claims 249B; treat the marketing as noise, the 4.5 GB file size is the truth).

*Why it matters:* Tencent quietly shipped on-device decision/embedding models sized for exactly the phones-and-jetson tier this brief watches, and quants landed within days. The lexi-2-249b mismatch is a reminder to check safetensors parameter metadata before believing any "model size" name.

*Local inference:* WePocket 2B (2.2B params) / 4B GGUFs are phone/8GB-card territory with quantized tiers; WeMM-Ecom-35B-A3B needs 24 GB class at Q4-ish tiers (same footprint family as Qwen3.6-35B-A3B); BLIP-Live i1 tiers land the 35B-A3B video VLM in the 20–24 GB bracket; lexi-2 "249b" fits any 4 GB GPU.

Sources:
- https://huggingface.co/mradermacher/WePocketModel1.0-2B-GGUF (2026-11-24T02:04:27Z), …/WePocketModel1.0-4B-GGUF (03:23:56Z), i1 tiers (12:08/13:01Z)
- https://huggingface.co/mradermacher/WeMM-Ecom-35B-A3B-i1-GGUF (09:21:47Z), https://huggingface.co/mradermacher/WeMM-Ecom-4B-GGUF (07:59:31Z)
- https://huggingface.co/mradermacher/BLIP-Live-i1-GGUF (08:33:27Z) — base: https://huggingface.co/Salesforce/BLIP-Live (created Nov 19, lastModified Nov 23 20:46Z)
- https://huggingface.co/mradermacher/lexi-2-249b-base-GGUF (Nov 24 00:11Z) — base: https://huggingface.co/Reallexi-llc/lexi-2-249b-base (created 2026-11-23T20:19:40Z; F16 params 2.246B per Hub metadata)

---

## Inference software and benchmarks

**Ollama v0.40.18: first Inkling 2 Metal fast-path on Apple Silicon, llama.cpp b12651** — released Tue Nov 24 04:18 UTC (23:18 EST Monday)

The release carries four changes, two of them Inkling-2 Mini-specific for MLX: fused Inkling residual mixing on Metal (#19808) and "faster Metal decode, prefill and draft rounds" for the inkling2 architecture (#19843), plus a llama.cpp bump to b12651 and removal of JetPack 5 support.

*Why it matters:* Ollama is moving faster than llama.cpp-family engines on the new Inkling loop-MoE arch on Macs; draft-round speedups mean the bundled DSpark-style drafter is now part of the Metal path — directly changing the recommendation for Mac users of the #1 trending open-weight model.

*Local inference:* Apple-Silicon MLX hosts (M-series, 16 GB+) running Inkling 2 Mini; no numeric uplift claimed in the release notes.

Source: GitHub release v0.40.18 — ollama/ollama, published 2026-11-24T04:18:13Z — https://github.com/ollama/ollama/releases/tag/v0.40.18

**llama.cpp b12670–b12675: CUDA NVFP4 dequant speedup, faster BPE tokenizer, OpenCL spec-verify GEMM, VEGA MMQ revival** — four in-window builds (06:13–09:55 UTC)

Twenty-three PRs merged in the window. Highlights: #32875 speeds up CUDA NVFP4 dequantization (consumer cards running NVFP4 GGUFs), #32897 reduces cooperative-softmax partials when nsm > 256, #32859 accelerates BPE tokenization (long-context prompt ingest), #31669 switches gated-delta-net from rollback to replay, #32909 adds an OpenCL q6_k coopk c8 GEMM for spec-verify up to n=8, #32940 adds inkling2 CLIP normalization via ggml_l2_norm, and AMD old-guard attention: #32805 prefers MMQ over MMVQ for Q4_K/NVFP4 on VEGA20 while #32837 gives CDNA1 a non-stream-k MMQ config.

*Why it matters:* NVFP4 dequant and VEGA/CDNA1 MMQ attention round out the "any quant on any GPU" story that keeps 5–7-year-old cards useful for local inference, and the tokenizer speedup quietly cuts time-to-first-token on long prompts across every backend.

*Local inference:* NVFP4 tiers benefit RTX 40/50-class; VEGA20/CDNA1 fixes keep Vega 56/64 and VII boxes viable; OpenCL spec-verify path helps iGPUs/APUs (Strix class) running quantized MoE.

Sources: builds b12670–b12675 — https://github.com/ggml-org/llama.cpp/releases ; merged-PR window query https://github.com/ggml-org/llama.cpp/pulls?q=is%3Apr+is%3Amerged

**vLLM 58-PR wave: Qwen4Exp SM100 skinny GEMMs, HY4 small-head FlashMLA, ERNIE 5.5 Preview recipe** — merged Nov 23 22:45 → Nov 24 13:12 UTC

Standouts from the in-window merge wave: #69529 adds SM100 TP=2/TP=8 skinny GEMM plans for Qwen4Exp; #68321 enables native small-head FlashMLA kernels for HY4/V4-Flash on SM90; #66749 lands a FlashInfer CuTe-DSL unquantized MoE backend for Hopper; #69426 extends EPD (encode-prefill-decode) disaggregation to metadata-only images/videos for HY4 and HY4 Flash; #69425 wires the new Baidu ERNIE 5.5 Flash Preview checkpoint into the recipe system; #68659 fixes InstantTensor loading for HY4 Flash Engram weights; #67021 streams Kimi K3 tool arguments incrementally; #66581 adds IBM Granite Docling; the Rust frontend batch (#69601 Mistral v11+ tool calls, #68969 DeepSeek V4 completion suffixes) keeps parity pressure on.

*Why it matters:* ERNIE 5.5 Flash Preview getting day-0 recipes means the first big Apache-licensed 280B-class agent model is servable on existing vLLM infrastructure the same day it lands — that is the open-weight stack operating at frontier cadence now.

*Local inference:* SM90/SM100 datacenter focus for the kernel work; the EPD and Engram fixes matter for the HY4 ecosystem whose 224B-Engram flash variant is the open-weight model most likely to be fine-tuned onto 128 GB-class boxes next.

Sources: merged-PR window — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged ; e.g. https://github.com/vllm-project/vllm/pull/69529 , https://github.com/vllm-project/vllm/pull/68321

**SGLang 52-PR wave: Rubin SM107 KDA prefill, muse_spark NVFP4 expert-TP, ERNIE cookbook to Preview** — merged in window

Standouts: #52643 runs FlashInfer KDA prefill on SM107 (Rubin) — Kimi-style delta-attention kernels crossing to the next NVIDIA arch; #50065 adds expert tensor-parallel (`--ep-size 1`) for NVFP4 checkpoints on the muse_spark line; #52644 makes gfx1250 fall back to Triton MoE runners for FP8/BF16 experts; the ERNIE 5.5 Flash cookbook retargets to the Preview checkpoint (#52626/#52754); sampler params reuse unchanged batches (#52570); and an AMD perf cluster tiles fused RoPE+KV writes on gfx1250 (#49566), reads MLA slices in place (#49347), and reuses aiter RoPE/RMSNorm paths (#48154).

*Why it matters:* Two weeks after its Rubin SM107 preview opened in TensorRT-LLM, SGLang is already qualifying Kimi's kernel family on SM107 — the post-Blackwell serving fight is starting before consumer cards even see the silicon.

*Local inference:* NVFP4 expert-TP plus the gfx1250 Triton fallbacks widen RDNA-consumer and RTX-50-class MoE serving; ERNIE cookbook = recipe path for 280B preview checkpoints.

Sources: merged-PR window — https://github.com/sgl-project/sglang/pulls?q=is%3Apr+is%3Amerged ; e.g. https://github.com/sgl-project/sglang/pull/52643 , https://github.com/sgl-project/sglang/pull/50065

**TensorRT-LLM puts Qwen3.8-Flash-Next on Rubin: SM107 serving with NVFP4 and cross-node MTP** — PR #22552 merged Nov 24 (in-window)

TensorRT-LLM merged serving support for the Flash-Next line on SM107 (Rubin): NVFP4 tp2/tep2, MTP at depth-2, and depth-8 across nodes; alongside it #22274 precompiles the PrimTS sparse-MLA kernels in decoder warmup and reuses decode views, and #21978 makes MegaMoE uGPU weight localization opt-in.

*Why it matters:* This is the strongest local-adjacent signal that the Rubin platform timeline is real software-wise — Flash-Next-class MoEs are being qualified on Rubin SM107 today, months before any consumer device.

*Local inference:* Forward-compatibility signal: NVFP4 GGUF/GPTQ tiers of Flash-Next (the exact tiers DGX Spark and RTX 5060 Ti 16GB users run) are the format path Rubin inherits — quant investments hold.

Sources: https://github.com/NVIDIA/TensorRT-LLM/pull/22552 , https://github.com/NVIDIA/TensorRT-LLM/pull/22274 (merged in window; repo: https://github.com/NVIDIA/TensorRT-LLM)

---

## Also noted
- **HWiNFO 8.61 beta adds a REST sensor API** — JSON sensors over LAN, AI-agent dashboard samples on GitHub (license-gated 12h server) — useful for multi-GPU rig dashboards; posted Monday 15:48 EST, just outside the window — https://www.techpowerup.com/355144/hwinfo-can-now-serve-sensor-readings-to-phones-and-tablets-over-your-network (referenced for completeness; not counted in totals).

## Just missed the window
- **Baidu ERNIE-5.5-Flash-Preview instruct weights landed Monday 04:00 EST** (repos created 09:00–09:01 UTC Nov 23): 280B-A14B agent-tuned checkpoints with Apache-2.0 licensing and native BF16/FP8/8-bit tiers — a license upgrade from the Nov 2 "base" release's EU/UK/KR-excluding terms. FP8 ~280 GB puts it at 2×GB300-Station class. Day-0 vLLM #69425 + SGLang cookbook support merged inside this window. — https://huggingface.co/baidu/ERNIE-5.5-Flash-Preview
- **Samsung's 2027 DRAM supply is effectively sold out** (Economy Daily report; TPU English coverage Mon 18:58 EST, underlying Korean report Monday KST): 2027–2029 capacity largely committed with LTAs reaching 2031, prepayments and price bands, commodity DRAM wafer capacity for 2027 sold out, makers meeting only 60–70% of demand, bit growth capped ~20–25%/yr — pressure that keeps GDDR7/LPDDR5X-loaded inference hardware expensive. — https://www.techpowerup.com/355146/report-samsungs-2027-dram-supply-is-effectively-sold-out

## Coverage notes
- **Verified count: 10 reportable items** (2 hardware, 1 model tier, 2 community quant ladders, 5 inference-software items) **+ 2 "Just missed" footnotes**, all with fetched, timestamped pages or Hub/GitHub API records.
- **Ledger dedupe: ~14 candidates suppressed** as already reported or pre-window: Jensen-reinstatement re-coverage (VideoCardz/ServeTheHome/Tom's/Phoronix/Reg), ceo-transition aftermath pieces (WSJ, FT, Polymarket, Bessent), The Register Vera-deep-dive + PCB-crunch + DRAM-revenue + Toyota-SoC (all Nov 3–20 originals), RTX Lens per-pin watchdog (original Nov 18–22; today's Tom's pickup only), Shuttle NW10U Wildcat Lake mini-PC (pre-window, weak AI angle), XNU Linux on DGX Spark (pre-window Phoronix), Centec ZX530S switch review (pre-window), Phoronix Zen6-vs-Zen1 (Nov 23 morning), ASUS 8GB-RAM (reported Nov 23), and Inkling-2-Mini abliterated tiers created pre-window.
- **Dead ends:** r/LocalLLaMA anonymous lanes both failed this fire (`search.rss` and `new.rss` HTTP 404 — same outage pattern as Nov 20); the social layer was covered via HN Algolia and Hugging Face API timestamps instead. DuckDuckGo search backend dropped ~3 queries (worked around via source-index sweeps). ServeTheHome had no in-window post yet at run time.
