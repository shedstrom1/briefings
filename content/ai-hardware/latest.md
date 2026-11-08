# AI Hardware & Local Models Brief — Saturday Evening Edition, November 7, 2026

**Window:** 2026-11-07 12:45 → 17:45 EST (17:45Z → 22:45Z UTC) · 5-hour new-items-only sweep · fire 17:45 EST

## TL;DR
- **Microsoft's Mage-VL-Cowork computer-use agent went llama.cpp-native tonight**: mtmd support merged (PR #31819, 18:36Z), builds b12191/b12192 shipped, and the official **ggml-org/Mage-VL-Cowork-GGUF** landed at 18:51Z — Q4_K_M weighs just 2.72 GB.
- **SGLang landed AMD's next-gen plumbing**: MI450 carv2 custom all-reduce with FABRIC handles and a gfx1250 table (#49001), plus a per-model in-tree AITER tuned-rows folder (#47862).
- **vLLM's afternoon wave** fixed Step5 spec-decode MTP re-validation (#66000) and MRV2 full CUDA graphs (#65275), and moved MiniMax-M3 VL config upstream to Transformers (#61813).
- **Fresh quant ladders**: bartowski's imatrix line for Jackrong Qwopus3.8-27B-Coder-Flash (27B vision coder), mradermacher's LiquidCode-2.5-24B-A2B (Liquid AI LFM2.5 MoE coder), plus four small-model ladders.
- Thin Saturday: no in-window hardware/silicon disclosures; the day's biggest hardware story (TSMC's denial of the 3D-DRAM-for-Nvidia report) landed ~4.5 hours pre-window — footnoted.

## Model releases
### Microsoft's Mage-VL-Cowork gets day-0 llama.cpp support + official GGUFs
**Event date:** November 7, 2026 (18:36Z merge, 18:51Z upload).
llama.cpp merged mtmd support for **MageVL-Cowork** (PR #31819, committed 18:36Z, co-authored by Pascal (serveurperso)), and releases **b12191** (21:11Z) and **b12192** (21:28Z) carry it. Minutes after the merge, the official **ggml-org/Mage-VL-Cowork-GGUF** repo appeared (created 18:51Z), auto-converted via ggml-org/convert: a **4B Qwen3-based** vision-language agent model with **Q4_K_M at 2.72 GB, Q8_0 at 4.69 GB, BF16 at 8.83 GB**, runnable via `llama serve -hf ggml-org/Mage-VL-Cowork-GGUF`. The source model, **microsoft/Mage-VL-Cowork**, is an Apache-2.0 hybrid agent (GUI/computer-use, coding, terminal; llava_onevision2 lineage, RL-trained, OSWorld/WebVoyager tags) that landed Oct 30 — tonight's event is local enablement, not the weights themselves.
**Why it matters:** a Microsoft agent-VLM you can run from a 2.72 GB Q4 file means computer-use/terminal-agent capability on a Raspberry-Pi-adjacent budget — and the day-0 upstream support (not weeks-late community patches) shows the llama.cpp team tracking Microsoft's agent stack.
**Local inference:** fits any 4 GB+ GPU or Apple Silicon Mac in Q4_K_M; Q8_0 suits 8 GB cards; the mtmd path needs b12192+. Community GGUFs (Hansda, MLX 4-bit) already predate this — the ggml-org repo is the canonical tier.
Sources:
- PR #31819 commit — llama.cpp/GitHub, 2026-11-07T18:36:44Z — https://github.com/ggml-org/llama.cpp/commit (see release b12192 changelog)
- Release b12192 (MageVL build) — GitHub, 2026-11-07T21:28:25Z — https://github.com/ggml-org/llama.cpp/releases
- ggml-org/Mage-VL-Cowork-GGUF — Hugging Face, created 2026-11-07T18:51:10Z — https://huggingface.co/ggml-org/Mage-VL-Cowork-GGUF
- microsoft/Mage-VL-Cowork (base, Oct 30) — https://huggingface.co/microsoft/Mage-VL-Cowork

## AI hardware and silicon
*No verified in-window disclosures this period* (ServeTheHome's last post was 16:00Z, pre-window; TechPowerUp returned 403 — one strike, logged). Next scheduled hardware watch continues after the weekend.

## Inference software and benchmarks
### SGLang afternoon: AMD MI450 carv2 all-reduce, AITER tuned-rows library
**Event date:** November 7, 2026, 18:17Z → 22:33Z merges.
Nine merges, AMD-heavy: **#49001** runs carv2 custom all-reduce on **MI450** using FABRIC handles with a gfx1250 tuning table (21:27Z); **#47862** (22:33Z) reorganizes AITER tuned rows into one in-tree folder per model — a maintenance signal that AITER-tuned configs are now standard equipment; **#49087** refreshed the Muse Spark Open MI355X AgentX cookbook to the 1106 image (19:14Z). The Rust frontend gained cache-report (`--enable-cache-report` cached-prompt-tokens reporting, #49030), JSON-lines request logging (#49023) and image detail-hint passthrough (#49006); ernie55 import and kv-canary warmup fixes closed the day.
**Why it matters:** FABRIC-handle all-reduce on MI450/gfx1250 is early multi-GPU software for AMD's next Instinct generation — homelab-relevant because carv2 lineage is the same code path that speeds small MI300/MI350 clusters.
**Local inference:** MI450 parts target datacenter, but the AITER tuned-rows and Rust-frontend logging land for all AMD users (gfx94x/gfx12x); no NVIDIA-specific changes this wave.
Source: SGLang commits — GitHub API, 2026-11-07T18:17Z–22:33Z — https://github.com/sgl-project/sglang/commits

### vLLM afternoon wave: Step5 spec-decode fix, MRV2 CUDA-graph fix, MiniMax-M3 upstreaming
**Event date:** November 7, 2026, 17:46Z → 22:38Z merges.
Ten merges: **#66000** (22:33Z) skips VllmConfig re-validation for the Step5 MTP draft config — a spec-decode crash-class fix for Step-5-Preview users; **#65275** (19:29Z) fixes Model Runner V2 encoder-only FULL CUDA graphs using the promised max_query_len; **#61813** (20:09Z) moves to upstream Transformers `MiniMaxM3VLConfig`; ROCm CI made Zen5 CPU tests optional (#66297) and pinned AITER test fixes (#63166 at 22:37Z). Note: the large **[Core] Remove Model Runner V1 (#63373)** merge landed 22:50Z — five minutes past the window boundary, so it belongs to the next edition.
**Why it matters:** MRV2's graph fixes and the MiniMax-M3 config upstreaming are the kind of housekeeping that decides whether the 2026-serving stacks stay usable on consumer CUDA and ROCm boxes.
**Local inference:** affects anyone running MiniMax-M3.1 or Step-5-Preview spec-decode on SM100/SM120 and MI300-class cards.
Source: vLLM commits — GitHub API, 2026-11-07T17:46Z–22:38Z — https://github.com/vllm-project/vllm/commits

### llama.cpp b12191/b12192
Builds at 21:11Z/21:28Z — b12191 carries the CPU-execution MoE-cache path (#30966, reported at the 12:45 edition), b12192 adds MageVL-Cowork (see Model releases). No other merges in-window.

## Community spins and quantizations
### bartowski: Qwopus3.8-27B-Coder-Flash imatrix ladder (21:01Z)
Full GGUF ladder of **Jackrong/Qwopus3.8-27B-Coder-Flash** — a Qwen3.5-class 27B multimodal coding/reasoning FT (tool-use, agent tags) with imatrix. In the 16–20 GB band at Q4_K_M: fits the RTX 5060 Ti 16GB / 5070 Ti class with modest context; Q6/Q8 tiers want 24 GB+.
https://huggingface.co/bartowski/Jackrong_Qwopus3.8-27B-Coder-Flash-GGUF
### bartowski: Spark-x2.6-4B-HTML (21:54Z), Acaua-Lite (21:45Z), Frost-Cyber-Mini v1.1 (21:48Z)
Three small ladders: **wesjos/Spark-x2.6-4B-HTML** (HTML/UI-generation SFT from the mario-html dataset, Apache-2.0 — sub-3 GB at Q4, phone-to-laptop class); **HarpiaIA/Acaua-Lite** (LFM3-based code/agent model trained on OrionLLM/OpenAgentInstruct + CodeX-7M — edge coder); **FrostWolfAI/Frost-Cyber-Mini-Uncensored-v1.1** (abliterated agentic cybersecurity mini on spark2_5, Apache-2.0). All under ~4 GB in mid quants — 8 GB-card and below territory.
### mradermacher: LiquidCode-2.5-24B-A2B-GGUF (20:07Z) and Spark-4B-Faboulous-Edition-i1 (18:32Z)
**LiquidCode-2.5-24B-A2B** is ItachiSan's Apache-2.0 code fine-tune of Liquid AI's LFM2.5-24B-A2B MoE (unsloth-quantized, TGI-tagged) — A2B-active MoE that streams comfortably on 16 GB cards at IQ4/Q4 tiers. **Spark-4B-Faboulous-Edition** (groxaxo, code/MLX lineage) joins as an i1 imatrix quant.
https://huggingface.co/mradermacher/LiquidCode-2.5-24B-A2B-GGUF

## Also noted
- **Crowd-v1** (u/Vxtzq1, r/LocalLLaMA, in-window): follow-up to CrowdGPT — claims the first collaboratively trained 1B-parameter transformer with no datacenters and zero budget. Early-stage, watch the training claims. https://www.reddit.com/r/LocalLLaMA/comments/1xqh8hk/crowdv1_the_collaborative_llm/
- **Observer** (u/Roy3838): open-source, 100%-local phone agent (iOS+Android) that runs local models to monitor physical-world events (car approach, bird nest → Telegram alerts). https://www.reddit.com/r/LocalLLaMA/comments/1xqd058/use_your_phone_to_monitor_anything_with_local/
- **Determinism experiment** (u/Full-Travel857): one extra leading token flipped 6 of 15 answers across Ollama/llama.cpp at temp 0 — a practical note for cache-seeded evals. https://www.reddit.com/r/LocalLLaMA/comments/1xqh2jw/one_token_at_the_beginning_of_a_prompt_changed_6/

## Just missed the window
- **TSMC formally denied** The Information's report that it was drafting 3D-DRAM production plans at Nvidia's request (Tom's Hardware, Anton Shilov, published ~13:00Z — ~4.5 h pre-window; update on the ledger's 2026-11-06 tsmc-3d-dram-nvidia entry): TSMC says the tech is "years from market viability"; analysis points it toward base-die/SoIC value capture instead. Footnoted once, never again.
- **Inferact's TPU megakernels post** (700+ tok/s Kimi K3 on Ironwood vs 452 GB200, 1.4–2x decode at batch 1–8) resurfaced on HN at 20:40Z but the page is dated **Nov 5** — event predates window; related to the Oct 20 TorchTPU footnote. Footnoted once.
- **PyTorch 2.15** (The Register, Nov 6) tackles the "one bad GPU" problem for large-scale training resilience — disclosed pre-window, never footnoted before. Footnoted once.

## Coverage notes
- **Verified count:** 3 lead stories (1 model-ecosystem, 2 software waves) + 6 quantization repos + 3 also-noted = **12 qualifying items**, all with fetched, in-window-timestamped sources (GitHub API commits/releases, HF API/ repo pages, reddit feeds).
- **Ledger dedupe:** ~17 candidates suppressed as already-reported or morning re-coverages — moe-cache thread, KV-quant KLD study, Qoduck/Qwen4 rumor threads, EOU indic detector, Steam Machine Strata bench, kofu/grok-oss-Cerberus/persona_lambda/Arete-i1/K2-Horizon ladder (5 repos), Lisa Su "data center first" re-surfacing, D3D paper, llmd/Scyllas/Jeff/Mirage re-lists. Three new `missed:` footnotes added (TSMC denial, Inferact megakernels, PyTorch 2.15) — each now tagged, never to be footnoted again.
- Dead ends: TechPowerUp news 403 (one strike, not chased); The Register AI index 404'd on the guessed path (homepage window scan used instead); Tom's slug-guess 404'd once before the exact-headline search returned the real URL (per skill, resolved on second attempt).
- Exclusion notes: mlx-community/Mage-VL-Cowork-MLX-4bit was already reported Nov 21; vLLM #63373 (Model Runner V1 removal) missed the boundary by 5 minutes and is reserved for the next edition; The Register PyTorch 2.15 and Inferact megakernels were excluded from items per the event-date rule.
- A quiet Friday-evening window: no new silicon, no new base weights, drivers unchanged (NVIDIA 621.14 WHQL / AMD 26.10.2 / Intel 9175 current).
