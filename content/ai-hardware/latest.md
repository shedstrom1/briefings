# AI Hardware & Local Models Brief — Oct 22, 2026 · 17:45 ET edition

**Window: 2026-10-22 12:45 → 17:46 EDT** (previous fire → now). New-items-only; event dates anchor to the window.

## TL;DR

- **vLLM's afternoon merge wave** landed micro-scaling MXFP8 (W8A8) support for routed MoE experts via Quark, SM100 support for Step-5-Preview, a low-latency BF16 GEMM path for GLM-5.3 on SM107, and a new XPress speculative-decoding method — 14 PRs merged in-window.
- **SGLang merged 18 PRs in-window**, headlined by plan-driven dense and MoE LoRA engines, a bf16 index-logits fast path for DeepSeek-V4.1 on SM100, and a QSA context-parallel optimization.
- **llama.cpp shipped b11821–b11827**, including day-0 mtmd support for OpenBMB's MiniCPM-OCR 1.0 — merged while the weights were still not yet public on Hugging Face — plus an OpenCL depthwise conv2d kernel.
- **mradermacher pushed a fresh quant wave**: i1 GGUFs of Terminal-RSI-27B (an RL-trained terminal-agent model), Baseten's elephant-1 decision model, Netra-2B Thai OCR, and AI Singapore's SEA-LION-Guard v2 moderation pair.
- **A quiet afternoon for hardware**: zero verified silicon announcements in the window; the nearest miss was ServeTheHome's Qotom 10GbE mini-PC review, published just under two hours before the window opened.

---

## Inference software and benchmarks

### vLLM merges MXFP8 routed-expert quantization and Blackwell SM100/SM107 enablement — Oct 22
Fourteen PRs merged between 12:45 and 17:46 EDT. The standout is **[Quantization][Quark] support for MXFP8 (W8A8) routed experts (#63125)** — micro-scaling 8-bit weights-and-activations applied specifically to MoE expert layers through AMD's Quark quantizer. Alongside it: **Step-5-Preview gains SM100 support (#63147)**, **GLM-5.3 gets a low-latency BF16 GEMM path on SM107 (#62843)**, and core gained a **new XPress speculative-decoding method (#54448)**. Housekeeping worth knowing: Kimi K3's XTML marker leakage in the Responses API was fixed (#62507) and Anthropic-style error vocabulary mapping landed (#62294).

**Why it matters:** MXFP8 for routed experts is the block-scaled-quant format war arriving in the mainstream serving stack — experts are where MoE VRAM lives, and micro-scaling recovers accuracy that per-tensor FP8 loses on expert outliers. The SM100/SM107 work is datacenter-platform enablement, but the MoE quant path is what eventually cascades to consumer NVFP4/MXFP8 parity.

**Local inference:** no immediate consumer-GPU change today; the payoff path is MoE quants (DeepSeek-V4.1-Flash, MiniMax-M3 class) getting accuracy-preserving block-scale formats that mirror what NVFP4 already does on RTX 50 cards like the 5060 Ti 16GB.

Source (GitHub, merge-verified in-window): https://github.com/vllm-project/vllm/pull/63125 · also read: https://github.com/vllm-project/vllm/pull/62843 · https://github.com/vllm-project/vllm/pull/54448 · https://github.com/vllm-project/vllm/pull/63147

### SGLang ships plan-driven LoRA engines and DeepSeek-V4.1 SM100 fast paths — Oct 22
Eighteen PRs merged in-window. The flagship is **#45892, plan-driven dense and MoE LoRA engines** — adapter serving driven by pre-planned execution rather than per-request dispatch. Also: **dsv4 scores the V4.1 index logits in bf16 on SM100 (#45880)**, a **QSA context-parallel optimization (#43792)**, deterministic-inference groundwork via **seeded rejection-sampling draft proposals (#33395)**, and lm_head precision preserved with shared buffers (#45855).

**Why it matters:** plan-driven LoRA is the multi-adapter serving story — community fine-tune stacks (Unsloth/axolotl outputs) become practical as swappable server adapters, and SGLang is racing vLLM for that workflow. The dsv4 items continue the week-long DeepSeek-V4.1 serving-accuracy series.

**Local inference:** LoRA-at-scale matters most on single-consumer-GPU servers running multiple tuned personas/models; the SM100 tuning is datacenter-only.

Source (GitHub, merge-verified in-window): https://github.com/sgl-project/sglang/pull/45892 · also: https://github.com/sgl-project/sglang/pull/45880 · https://github.com/sgl-project/sglang/pull/43792

### llama.cpp b11821–b11827: MiniCPM-OCR 1.0 day-0 support lands before the weights are public — Oct 22
Five releases published between 13:02 and 15:36 EDT. The headline merge is **#30979, "mtmd: add MiniCPM-OCR 1.0 support"** — llama.cpp's multimodal (mtmd) loader now carries support for OpenBMB's upcoming MiniCPM-OCR 1.0 document model; as of press time `openbmb/MiniCPM-OCR-1.0` was still not discoverable on Hugging Face, so engine support beat the weights. A tracker account reported matching day-0 support PRs opened in vLLM and SGLang as well. Also merged in-window: **OpenCL depthwise conv2d kernels (#30900)**.

**Why it matters:** day-0 llama.cpp support is the community's strongest signal that a model is imminent — and OpenCL depthwise convs feed vision-encoder paths on older AMD GPUs and APUs where CUDA isn't an option.

**Local inference:** once weights land, OCR VLMs in llama.cpp run comfortably on 6–12 GB cards via mtmd; the OpenCL kernel extends usable hardware to older Radeon/APU boxes.

Source (GitHub): https://github.com/ggml-org/llama.cpp/pull/30979 (merged in-window) · https://github.com/ggml-org/llama.cpp/pull/30900 · releases: https://github.com/ggml-org/llama.cpp/releases (b11821 17:02 UTC → b11827 19:36 UTC)

---

## Community spins and quantizations

### mradermacher quantizes Terminal-RSI-27B — a recursive-self-improvement terminal agent (Oct 22, 16:08 EDT)
New i1 imatrix GGUF ladder for **Teriminal-RSI/Terminal-RSI-27B**, an Apache-2.0 RL-trained terminal-agent model on the qwen3_5 architecture (arXiv 2610.22192), tagged for recursive self-improvement, code, and long context. The 27.8B BF16 base appeared yesterday (Oct 21) on Hugging Face.

**Why it matters:** terminal-agent RL models are the current community obsession for local coding assistants, and this one arrives with an accompanying paper rather than being a mere style fine-tune.

**Local inference:** i1 tiers bring 27B-class into ~12–16 GB cards (5060 Ti class); Q8_0 lands around 28 GB, i.e. 3090/4090 territory.
Source (Hugging Face): https://huggingface.co/mradermacher/Terminal-RSI-27B-i1-GGUF · base: https://huggingface.co/Teriminal-RSI/Terminal-RSI-27B

### Baseten's elephant-1 decision model gets GGUFs (Oct 22, 15:52 EDT)
mradermacher shipped GGUFs of **baselabs/elephant-1**, Baseten's MIT-licensed decision model — a Qwen3.5-9B fine-tune for classification, routing, guardrails and calibration with vision input, shipping its own readout/engine tooling. Same "single-prefill probability readout" idea as the community's Mica series, from an inference-company lab. (Base repo was created Oct 19; the GGUF upload is the in-window event.)

**Why it matters:** cheap local judge/routing models are the glue for agent pipelines — they gate which model or tool sees a request without paying API fees.

**Local inference:** a 9.4B BF16 decision model quantizes to roughly 5–6 GB (i1/Q4) — trivially co-resident with a working model on a 16 GB card.
Source (Hugging Face): https://huggingface.co/mradermacher/elephant-1-GGUF · base: https://huggingface.co/baselabs/elephant-1

### Also noted (all mradermacher, all verified in-window by creation timestamp)
- **Netra-2B-GGUF** (16:22 EDT) — Thai-language OCR/document VLM for receipts and key-information extraction; fits under ~2 GB. https://huggingface.co/mradermacher/Netra-2B-GGUF
- **Gemma-SEA-LION-Guard-v2 E2B/E4B GGUFs** (15:41, 16:27 EDT) — AI Singapore's SEA-language content-moderation/decision models (MIT). https://huggingface.co/mradermacher/Gemma-SEA-LION-Guard-v2-E4B-GGUF
- **Ministral-3-3B-Claude-Opus-Reasoning-Distilled-i1-GGUF** (16:46 EDT) — the 3B sibling of the already-reported 8B Opus-trace reasoning distill; ~2–3 GB at i1. https://huggingface.co/mradermacher/Ministral-3-3B-Claude-Opus-Reasoning-Distilled-i1-GGUF
- **OPSD-Qwen3-4B-i1-GGUF** (14:02 EDT) — hybrid-reasoning math spin with no-think control. https://huggingface.co/mradermacher/OPSD-Qwen3-4B-i1-GGUF

---

## AI hardware and silicon

No verified hardware announcements fell inside 12:45–17:46 EDT. VideoCardz's afternoon slate was gaming-driven (RTX 5090 melt saga, GALAX dual-connector 5080), and the circulating "NVIDIA discontinuing RTX 5090 / GB202 to RTX PRO" story traces back to Oct 10–11 reporting — excluded as out-of-window per the ledger rule.

## Model releases

No open-weight release crossed the window boundary. The three near-misses are footnoted below.

---

## Just missed the window

- **KittenML Kitten-TTS 2** (announced last night, ~21:00 EDT Oct 21; weights updated 00:39 EDT today) — the world's first 1.58-bit (ternary) TTS: real-time on CPU, zero-shot voice cloning, multilingual, emotion/emphasis control, with GGUF weights for a llama.cpp fork. Genuinely notable for on-device voice. https://huggingface.co/KittenML/kitten-tts-2 · https://github.com/KittenML/KittenTTS
- **InclusionAI Ling-3.0-flash-Sante** (announced this morning; HF last-modified 10:53 EDT) — Ant Group's healthcare-specialized open-weight MoE, 124B total / 5.1B active, MIT, 512 experts — the second Ling model dropped today. https://huggingface.co/inclusionAI/Ling-3.0-flash-Sante
- **HuggingFaceBio Carbon-X** (repo Oct 21, announced on Reddit this morning) — 14B DNA-and-language model fine-tuned from Qwen3-14B; claims to beat every open model tested on five DNA benchmarks at ~1,000x lower cost than the frontier comparator. Runs locally via llama.cpp. https://huggingface.co/HuggingFaceBio/Carbon-X
- **ServeTheHome: Qotom Q51551UT review** (published 10:52 EDT — 1h53m before the window) — industrial mini-PC with Intel Core Ultra 5 125H (NPU included), 4x 10GbE + 6x 2.5GbE, and a headless PCIe Gen5 x8 slot: a homelab box that can cluster inference nodes and host an add-in GPU. https://www.servethehome.com/qotom-q51551ut-4x-10gbe-and-6x-2-5gbe-intel-mini-pc-review/

---

## Coverage notes

- **Verified: 11 items** (3 lead, 5 quant/also-noted, 4 footnotes) — every included URL fetched; GitHub merges timestamp-verified via `merged:2026-10-22T16:45..21:46Z` queries; HF uploads verified by API `createdAt`.
- **Ledger dedupe: 8 candidates suppressed** as already reported — capy-flash v0.7.1, the £300 Snapdragon mini-PC/Janas follow-up, the Strata-beating R9700 PR thread, Flint re-coverage (two threads + bartowski's flint GGUF, also pre-window), Fujitsu GODZILLA re-surfacing, TensorRT-LLM v1.4.0rc1, Ollama v0.40.9-rc2 (pre-window + reported), Muse Spark Open score-vs-size chart (re-cover of a reported model).
- **Excluded on age:** NVIDIA OpenShell's Reddit resurgence traces to the Sept 28 developer-blog disclosure (repo Feb 24) — re-coverage, not an event.
- **Dead ends:** Reddit's JSON endpoints 403 (used the reddit-reading Atom route); `openbmb/MiniCPM-OCR-1.0` not yet public on HF (API-confirmed — item framed accordingly); DeepSeek/MiniMax China export-registration story looked borderline policy and its report timestamps sit before the window — skipped; AuroraTTS-100K and the community Huawei Atlas comparison table left out as unverifiable in-budget.
- **Indexes checked:** ServeTheHome, VideoCardz, Hugging Face trending + author feeds, GitHub releases/merged-PR searches (llama.cpp, vLLM, SGLang, Ollama, TensorRT-LLM), r/LocalLLaMA top-day via Atom feeds. Tom's Hardware /latest returned 404.
