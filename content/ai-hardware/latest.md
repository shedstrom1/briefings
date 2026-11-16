# AI Hardware & Local Models Brief — Monday, November 16, 2026 (07:45 ET edition)

**Window:** 2026-11-15 17:45 ET → 2026-11-16 07:45 ET (overnight slot; previous fire Sunday 17:45)

## TL;DR

- **Tencent launches Hy4 flash** — a 224B-total (64B Engram) / 12B-active natively multimodal model with 1M context, cutting Hy4 input prices ~92%, and Tencent says an **open-weight release is planned** — squarely in the community's expert-offload sweet spot.
- **Alibaba makes Qwen4-Max generally available** — 2.4T-param MoE, 71B active + 328B N-gram-embedding params, GDN+QSA attention, native vision, 1M ctx; Max itself stays API-only, but the Qwen team promises **smaller Qwen4 models will come to open source**.
- **Sage S2.1 Flash lands on the Hub** — Shanghai AI Lab's 321B scientific multimodal agent under MIT, with a first-party FP8 variant and #1 MLE-bench Lite (90.3).
- **TensorRT-LLM merges an FP4 MLA path** — dense block reuse plus a 4x decode kernel and dispatcher, alongside a new NVFP4 MLA tech blog.
- **tandem.cpp hits v0.3.0** — first tagged release; borrows VRAM only during long prompts and cuts prefill 2–6x on a 16GB+64GB rig.

---

## Model releases

### Tencent launches Hy4 flash — 224B/12B-active multimodal, ~92% cheaper API, open weights planned (event date: Nov 16)

Tencent Hunyuan introduced Hy4 flash on November 16, the Flash-tier sibling of the Nov 4 Hy4 flagship: 224B total parameters (including 64B of Engram parameters) with only 12B active per token, natively multimodal, 1M-token context (960K input / 64K output caps). Tencent's hybrid-attention + LatentMoE + Engram design shrinks the KV cache to one-sixth of full Hy4's, and megakernels, four-bit quantization and the DFly runtime push decode past 150 tokens/sec on their serving stack. Tencent Cloud list pricing lands at $0.070/M input and $0.281/M output (vs $0.834/$2.501 for Hy4 — about 92% and 89% lower), with cached input at $0.003/M; the company claims average cost per task at 1/13 of Hy4 preview, 1/20 of Hy4 and 1/8 of DeepSeek-V4.1-Flash-1017 at Max-tier peak pricing, and a blind test of 85 WorkBuddy users over 259 tasks put user satisfaction close to Claude Haiku 5.5. It is live now on Tencent Cloud TokenHub (50% off until 12:00 Beijing time today), WorkBuddy (free through Nov 30), Yuanbao, WeCom, QQ, ima and Marvis, with OpenRouter "soon" — and Tencent says an **open-weight Hy4 flash release is planned**.

**Why it matters:** A 224B-class MoE with 12B active and an Engram table layer is exactly the shape the local ecosystem has been optimizing for — it fits the Strata/Basalt/tandem-style RAM+NVMe expert-offload workflow, and if the weights land under a permissive license like full Hy4's, day-0 GGUF ladders on 96–128GB class rigs are a lock. Until weights ship, treat the 150 t/s and KV-shrink numbers as vendor-reported.

**Local inference:** Projected Q4_K_M class ≈ 110–130GB → dual RTX 5090 32GB + DDR5/SSD expert offload, Ryzen AI Max+ 128GB, or Mac Studio 192GB; API usable today via TokenHub/OpenAI-compatible routes (API id `hy4-flash`).

Sources:
- Unite.AI, "Tencent Launches Hy4 Flash as a Lower-Cost Multimodal Model," Nov 16, 2026 — https://www.unite.ai/tencent-launches-hy4-flash-as-a-lower-cost-multimodal-model/ (read in full)
- Tencent Hunyuan official research page, Nov 16, 2026 (via search; JS-gated to plain fetch) — https://hy.tencent.ai/research/hy4-flash?langVersion=en
- explainX, "Hy4 Flash: Tencent 224B Model, API Prices, vs Hy4," Nov 16, 2026 — https://explainx.ai/blog/tencent-hy4-flash-224b-workbuddy-pricing-open-weights-planned-november-2026
- r/LocalLLaMA unveiling, Nov 16 07:23 ET — https://www.reddit.com/r/LocalLLaMA/comments/1xxo137/introducing_hy4_flash_ultimate_costefficiency_for/

### Qwen4-Max goes GA — 2.4T/71B-active, 1M ctx, API costs cut; smaller Qwen4s promised open source (event date: Nov 16; update on the Nov 11 "qwen4-max-preview" ledger entry)

Alibaba made Qwen4-Max generally available Monday on QwenCloud (Token Plan/API), plus Qwen Studio, QwenWork and Qoder, ending the limited preview that began Nov 11. The blog details the new Qwen4 architecture: 2.4T backbone, 71B activated per token plus 328B N-gram-Embedding parameters, three Gated DeltaNet layers per Qwen Sparse Attention layer, Gated Residual branches, and native vision with 1M-token context. Alibaba reports zero loss spikes through the 2.4T run, Work360 52.69 vs Qwen3.8-Max's 47.74, token usage down 24–48% at matched effort, and average cost per task on Work360 falling $4.43 → $1.67 (-62%); TechNode independently reports the GA and an RMB 26.58 → 11.16 (-58%) task-cost drop. Max itself stays proprietary — top comment in the r/LocalLLaMA thread (Nov 16 07:45 ET) flagged that no weights were announced — but the blog closes with: "Smaller models in the family are coming to open source, built on the same lineage and trained with the same generation of techniques."

**Why it matters:** The open-weight commitment on the same lineage (GDN + QSA) is the local-relevant part: the community just built out QSA/Engram-class kernels for Qwen3.8-Flash-Next in llama.cpp/vLLM/SGLang, and a small Qwen4 with that architecture should see unusually fast day-0 support. Watch for the first Qwen4-Flash naming.

**Local inference:** Nothing runs locally today (2.4T closed); the open-sourced smaller tiers are the items to size for — expect 27B–35B-A3B class based on the family's history.

Sources:
- Qwen Team, "Qwen4-Max: Your New Teammate Has Arrived," Nov 2026 (fetched in full) — https://qwen.ai/blog?id=qwen4-max
- TechNode, "Alibaba launches Qwen4-Max with lower API prices and a million-token context window," Nov 16, 2026 (fetched) — https://technode.com/2026-11-16/alibaba-launches-qwen4-max-with-lower-api-prices-and-a-million-token-context-window/
- MarkTechPost, "Alibaba Releases Qwen4-Max: 2.4T MoE, 71B Active, 1M Context," Nov 16, 2026 — https://www.marktechpost.com/2026-11-16/alibaba-qwen4-max-release/

### Sage S2.1 Flash: Shanghai AI Lab's 321B scientific multimodal agent, MIT-licensed, FP8 variant day one (event date: Nov 16; repo created Nov 15, public Nov 16)

internlm published Sage S2.1 Flash, a 321B-parameter scientific multimodal model for multi-round research analysis: it understands 10+ scientific input types (molecular graphs, crystal structures, protein sequences/3D) via the Sage SciPerception encoder-projector stack and drives specialized software while checking results against visual feedback. Weights and a first-party FP8 checkpoint are on the Hub (safetensors, custom `glm5_next` code), deployment docs include a vLLM `--enable-prompt-embeds` path, and the release claims first place on four of fifteen benchmarks — 90.3 on MLE-bench Lite (ahead of DeepSeek-V4.1-Flash-1017's 89.2), 72.2 OfficeQA Pro, 25.0 Harvey LAB, 37.1 SpreadsheetBench 2 — while staying competitive on agentic coding (Terminal-Bench 2.1 87.9). Code and weights are MIT. The r/LocalLLaMA debut came 06:36 ET.

**Why it matters:** MIT + FP8 from day one + a scientific-tool-use niche makes this the open alternative to Claude/GPT-5.6-class "research agent" workloads; the `glm5_next` tag hints it shares GLM-5.x-lineage infrastructure the engines already handle.

**Local inference:** 321B BF16 is datacenter-class (~640GB); the FP8 (~320GB) or ~Q4 (≈180GB) tiers target 2x96GB / 4x5090+DDR5 offload rigs — a Strata/Basalt-style expert-streaming candidate from day two onward.

Sources:
- Hugging Face model card (fetched in full, dated Nov 16) — https://huggingface.co/internlm/Sage-S2.1-Flash (FP8: https://huggingface.co/internlm/Sage-S2.1-Flash-FP8)
- r/LocalLLaMA post, Nov 16 06:36 ET — https://www.reddit.com/r/LocalLLaMA/comments/1xxn631/internlmsages21flash_hugging_face/

---

## AI hardware and silicon

### Update on previously reported NVIDIA CEO transition: Musk headlines the "Bring Jensen Back" campaign; the spy allegation wobbles (event date: Nov 16)

The Register (Mon 06:31 ET) reports Elon Musk has joined — and effectively leads — the campaign to reinstate Jensen Huang as NVIDIA CEO, with the open letter now past 173,000 signatures (Business Insider's parallel piece argues the Chinese-spy allegation that precipitated the Nov 12 resignation "is starting to look shaky," echoing Singapore's Nov 13 complaint that US authorities and Supermicro barely cooperated with its probe). Roadmap continuity was pledged by interim CEO Colette Kress; earnings land Nov 18.

**Why it matters:** Successor uncertainty at NVIDIA now directly touches every shipping schedule this brief tracks (Rubin, DGX Station class, Spark) and Nov 18 earnings guidance; the credibility turn on the initial allegations is a materially new fact, not re-coverage.

**Local inference:** none directly — tracking for RTX Spark/DGX pricing and roadmap cadence.

Sources:
- The Register, "Elon Musk leads Silicon Valley's campaign to put Jensen Huang back in charge at Nvidia," Nov 16 11:31 UTC (fetched via headlines feed) — https://www.theregister.com/headlines.atom
- Business Insider, "The Chinese spy allegation that toppled Nvidia's CEO is starting to look shaky," Nov 2026 (HN-submitted 07:40 ET) — https://www.businessinsider.com/intelligence-jensen-huang-downfall-shaky-nvidia-china-spy-gpus-2026-11

*Note:* the only other hardware-cycle candidates this window — the Register's "Alibaba chip unit to float" roundup line and VideoCardz's "AMD RDNA 5 GFX13 Linux patches" list item — could not be resolved to dated primary articles (search backend errors; two-strike rule), so they are logged under Coverage notes rather than asserted.

---

## Community spins and quantizations

### mradermacher overnight ladder: IBM Granite 4.2 heretic pair, Swift-1.5-Qwen3.8-27B unsycophantic, Nemotron Bolt heretic-3, AxonMed-32B-Titan (event dates: Nov 15–16)

Day-0 GGUF ladders posted inside the window (all verified via the Hub API): `granite-4.2-30b-heretic-GGUF` (04:30Z) + `-i1` imatrix tier (09:43Z) of stuarch's Apache-2.0 IBM Granite decensor; `Swift-1.5-Qwen3.8-27b-heretic-unsycophantic-i1-GGUF` (05:53Z) — RacingAce66's token-efficient anti-sycophancy heretic on the Qwen3.8-27B base; `NVIDIA-Nemotron-3.5-Bolt-E4B-BF16-heretic-3-GGUF` (12:21Z, darkc0de); `AxonMed-32B-Titan-i1-GGUF` (06:00Z, USMLE-style clinical reasoning); `RusNanoMath-800M-GGUF` (23:37Z, sub-1B Russian math with linear attention); plus `StructuraAI-GGUF`, `Llama-Krikri-8B-Instruct-v1.5-Heretic-MPOA-GGUF` and `experimental_merge_241026_v2-GGUF`.

**Why it matters:** the Granite 4.2 heretic pair and the unsycophantic 27B are the practically useful ones — both fit single 16GB cards at Q4 and target agent tool-call reliability where IBM's and Qwen's bases over-refuse.

**Local inference:** 800M–32B range → Q4_K_M 0.5–19GB, all run on 8–16GB cards; the Nemotron E4B is 4GB-class.

Sources (all Nov 16 Hub timestamps):
- https://huggingface.co/mradermacher/granite-4.2-30b-heretic-i1-GGUF
- https://huggingface.co/mradermacher/Swift-1.5-Qwen3.8-27b-heretic-unsycophantic-i1-GGUF
- https://huggingface.co/mradermacher/NVIDIA-Nemotron-3.5-Bolt-E4B-BF16-heretic-3-GGUF
- https://huggingface.co/mradermacher/AxonMed-32B-Titan-i1-GGUF · https://huggingface.co/mradermacher/RusNanoMath-800M-GGUF

### Qwen3.8-27B-Perplexing-Pegasus-Heretic: unified vision + typed decider + abliterated in one GGUF line (event date: Nov 16)

Community release (r/LocalLLaMA, 23:22 ET Nov 15) packaging ButtercupLyn100's Perplexing-Pegasus — a Qwen3.8-27B heretic adding unified vision plus a typed-decider head for local agent stacks — as GGUFs.

**Why it matters:** the "one file does vision + routing/decision + unrefusal" pattern is the current community meta on the 27B base (cf. Drex-Max, GLiDE), and a single GGUF keeps sub-agent routing on one loaded model.

**Local inference:** Qwen3.8-27B-class: ~16GB at Q4_K_M, comfortable on a 5060 Ti 16GB or 3090.

Source:
- r/LocalLLaMA release post, Nov 16 04:22 ET — https://www.reddit.com/r/LocalLLaMA/comments/1xxg12n/release_qwen3827bperplexingpegasusheretic_unified/

---

## Inference software and benchmarks

### TensorRT-LLM merges FP4 MLA: dense block reuse, 4x decode kernel + dispatcher (event date: Nov 16)

NVIDIA merged #21313 "[feat] FP4 MLA: dense block reuse, 4x decode kernel and dispatcher, and follow-up fixes" (05:57 ET) and shipped the companion "NVFP4 MLA tech blog" (#21181, 00:58 ET). The same overnight wave landed Qwen3.5 GB300 round-2 optimizations (decode/prefill kernels, KV cache manager V2 remote tier, disaggregated GEN admission, #21974), skip-softmax with KVCM2 (#21553), and CuTe DSL GEMM autotuner pruning via nvMatmulHeuristics (#21185).

**Why it matters:** MLA decode in FP4 is the KV-bandwidth unlock for DeepSeek-V4-class models on Blackwell/GB300 — a 4x decode-kernel claim from NVIDIA's own stack is the kind of number that historically (EpochIPI: ~10x FLOPS understatement) understates agentic-serving wins; the tech blog should give reproducible recipes.

**Local inference:** Blackwell datacenter first (GB300/H-class); consumer SM120 cards inherit kernels but not the 4x headline — watch for community verification on RTX PRO 6000/5090.

Sources:
- NVIDIA/TensorRT-LLM GitHub commit log, Nov 16 (fetched via API) — https://github.com/NVIDIA/TensorRT-LLM (PRs #21313, #21181, #21974, #21553, #21185)

### tandem.cpp v0.3.0 — first tagged release: prefill 2–6x faster by borrowing VRAM per-prompt (event date: Nov 16; update on the tandem-vllm (Nov 13) / tandem-cpp ROCm (Nov 14) ledger entries)

ewhacc's host-RAM expert-offload fork released v0.3.0 with prompt processing in 4096-token batches: VRAM is borrowed from the decode pool only while a long prompt runs and returned afterward, no flags needed. Author's rig — RTX 5070 Ti 16GB + 64GB DDR5 + i5-12400F: Qwen3.8-Flash-Next IQ3_S 15.8k-token prefill 81.9s on llama.cpp → 15.1s (450 → ~1,047 tok/s; Strata 11.7s); DeepSeek-V4.1-Flash IQ2_XS 15.7k tokens 2,012s → 53–103s (→57s with v0.3.0, vs Strata 58s); decode after long prompts 27–4.4 → 88–13 tok/s (Strata 68–6.4). Project Maya's DSv4.1 GGUFs are now supported.

**Why it matters:** this closes most of the remaining prefill gap between offload engines and Strata at single-midrange-GPU price points — TTFT is the gating complaint for 100k+ agent sessions on host-RAM rigs, and v0.3.0 claims parity with Strata on the author's hardware (single rig, author-run; needs third-party confirmation).

**Local inference:** built for exactly the 16GB-card + 64GB-DDR5 class (5060 Ti/5070 Ti); Ampere/Ada/Hopper supported; 24GB main-card minimum per the vLLM lineage.

Sources:
- r/LocalLLaMA release post (fetched in full), Nov 16 07:22 ET — https://www.reddit.com/r/LocalLLaMA/comments/1xxo0f5/tandemcpp_v030_prefill_speed_improved_project/
- GitHub — https://github.com/ewhacc/tandem.cpp

### llama.cpp overnight wave → b12430–b12436 (event date: Nov 16)

Six builds shipped (01:35–07:19 ET): kv-cells now avoids an O(n_cells) scan per sequence removal (#32380, Titaniumtown — long-running server memory hygiene), CUDA MUL_MAT_ID no longer aborts with odd src0 columns on AMD (#32385, liminfei-amd), CI docker images are tagged vX.Y.Z (#32388), and the Ripple DFlash2 NoPE metadata conversion fix (#32363, Beinsezii) landed 17:56 ET Sunday.

**Why it matters:** the AMD MUL_MAT_ID fix removes a crash class for MoE routing shapes on HIP builds, and the kv-cells fix targets idle-server fragmentation that long agent sessions trip over.

**Local inference:** all backends; the AMD fix matters most for RX 7900 XTX/R9700 ROCm rigs.

Source:
- GitHub releases (fetched via API) — https://github.com/ggml-org/llama.cpp/releases

### vLLM overnight: Rust-frontend parity batch + Kimi-K3.1-Pro MI355X recipe (event date: Nov 16)

Rust frontend caught up with the Python FE: `--default-mm-loras` (#67873), `--kv-sharing-fast-prefill` (#67872), `--hf-token` (#67871), Python-compatible multimodal cache key domain-tagging (#67870), and 500-on-engine-error semantics (#67869) — all 02:04 ET. Model-side: a Kimi-K3.1-Pro MI355X recipe with the kimi-k31-5c14fe8 image and SP prefill aligned to InferenceX #5089 (#67817, 18:09 ET Sun), Llama-4 per-request `max_patches` honored (#67813), and a ROCm CI fix following the #66842 DSv4.1 sparse-MLA change (#67845).

**Why it matters:** the K3.1 MI355X preset is AMD's flagship-inference proof-point maturing release-by-release; the Rust FE parity list means the Rust frontend is nearing feature-lock for agentic stacks.

**Local inference:** K3.1 remains datacenter (2.8T); Rust FE benefits memory-tight consumer boxes through kv-sharing-fast-prefill.

Sources:
- vllm-project/vLLM GitHub (fetched via merged-PR API) — https://github.com/vllm-project/vllm (PRs above)

### SGLang overnight: gateway consolidation to sgl-router, gfx1250 ring all-reduce, Blackwell/Rubin optimizations reverted (event date: Nov 16)

sgl-model-gateway is deleted as a product path (#50732, 02:54 ET) with every user moved to sgl-router (#50671) and the gateway gaining NPU router artifacts + multi-node KV-events warnings (#50751). AMD got `hier_ring_simple`, a custom all-reduce over partition pairs on gfx1250 (#50660, 18:34 ET Sun); the GLM-5.3 MI355X AgentX TP4 cookbook gained write-back HiCache (#50678); diffusion serving got end-to-end profiling (#50658). Notable negative: the Blackwell/Rubin "hybrid GDN + QSA + MoE serving optimizations" series was **reverted** (#50703, 23:14 ET Sun) — the flagship path regressed pending rework.

**Why it matters:** the revert is the story — the hybrid-attention serving fast path relevant to Qwen4-style GDN+QSA models isn't ready on Blackwell despite the merged PR, and the gfx1250 work keeps extending AMD's next-gen serving reach.

**Local inference:** consumer SM120 users are unaffected by the revert; router consolidation touches multi-engine homelab gateways.

Sources:
- sgl-project/sglang GitHub (fetched via merged-PR API) — https://github.com/sgl-project/sglang (PRs above)

### GLM-Tandem: GLM-5.3-Flash tensor-parallel across two Mac Studios, ~141–146 t/s fresh prose (event date: Nov 16)

icryo open-sourced GLM-Tandem: MLX tensor-parallel serving of GLM-5.3-Flash across two M-series Studios — 141–146 t/s on fresh prose, 157 t/s code (T=0), 223 t/s aggregate at concurrency 8, 122 t/s median decode at 80–200k context with 0.7–0.9s follow-up TTFT. Commenters flagged the long-context follow-up numbers as warm-prefix-cache cases and are already eyeing 1M-context GLM sessions on dual 256GB Macs.

**Why it matters:** first credible 2-Mac tensor-parallel decode numbers at triple-digit t/s on a GLM-5.3-class model — the Apple-cluster-offload lane (MoEShift, kapitsa-xl) is compounding fast.

**Local inference:** 2x M4/M5 Mac Studio (unified-memory pooling); numbers are Apple-Silicon MLX-specific.

Sources:
- r/LocalLLaMA post (fetched in full), Nov 15 22:46 ET — https://www.reddit.com/r/LocalLLaMA/comments/1xxfcr6/github_icryoglmtandem_glm53flash_on_two_mac/
- GitHub — https://github.com/icryo/GLM-Tandem

---

## Also noted

- **Berth v0.6.0** (update on the T-Crypt berth line) — per-card native schedules, uninterruptible `berth train` runs, learned engine size claims; a month on one RTX 4090 shows 1,136 queue-waits, 901 boundary drains, 23.9 TiB of VRAM handed between engines (llama-swap/NInfer/Strata/ComfyUI mix). — https://www.reddit.com/r/LocalLLaMA/comments/1xx9fjs/ui_native_scheduling_engine_swap_board_and_more/ (Nov 15 18:01 ET)
- **Inpherio Live** — iPhone/iPad becomes a LAN AI server: modified llama.cpp streams MoE weights from flash past the ~6GB iOS app cap; gpt-oss-120B (58.5GB) at 4.5 t/s on a 16GB M1 iPad Pro, Qwen3-30B at 10.9 t/s on a 12GB iPhone 17 Pro Max, OpenAI-compatible API; awaiting App Store. — https://www.reddit.com/r/LocalLLaMA/comments/1xxoabt/running_usable_30b_and_working_120b_models_on/ (Nov 16 07:36 ET)
- **Ignis** — Qwen3.8-27B 262k-context dev-machine server now runs on RTX 3090/4090 and 16GB RTX 40 cards: sizes context to free VRAM, unloads after 5 min idle, vision tower parked in system RAM, concurrent sub-agents on one card. — https://www.reddit.com/r/LocalLLaMA/comments/1xxdn31/my_ignis_server_qwen3827b_262k_context_now_runs/ (Nov 15 21:19 ET)
- **Qwen3.8-27B on an ESP32** — luvsheth streams the whole GGUF from a 32GB microSD as RAM on an ESP32-S3 N16R8 (8MB PSRAM): ~50 tokens/**week** for dense 27B, switching to Qwen3.6-35B-A3B MoE + 40MHz SD clock for "much faster" (still weekly-scale). Adorably useless, technically correct streaming demo. — https://luvsheth.com/p/running-qwen38-27b-on-an-esp32 (Nov 16)
- **HN/rL double-takes** (discussion-level, not events): "Did we hit a wall in context length?" and an AA-index thread debating Qwen3.8-FN now ranking above GLM-5.3-Flash — no verifiable index change confirmed this window.

## Just missed the window

- **AMD capacity-aware scheduling for hybrid CPUs** (Phoronix, Nov 15 09:20 ET — ~8h pre-window): kernel patch series for heterogeneous-core scheduling, relevant to Strix Halo/Arc hybrid inference boxes. Footnoted once, per policy.

## Coverage notes

- **Verified this edition:** 16 items (7 lead-class, 9 noted/footnote), each backed by a fetched page with an in-window timestamp; lead stories read in full.
- **Ledger dedupe:** 8 candidates suppressed as already-reported re-coverage — the Jensen-comeback warroom (re-surfaced on VideoCardz), RazorLake 12P die (TPU "Yesterday 20:13" re-run + VideoCardz duplicate), Intel Razor Lake ISA-reference add (Nov 13 item), RTX 5090 retail restock at 3x MSRP (already a `missed:` footnote — no second footnote), RTX 5080 24GB cancellation (Nov 11), AMD SSD-write driver fix (Nov 11/12), MEMPHIS i1 ladder (Nov 15), and "why abliterated when LoRA exists?" (discussion off an Oct 22 event).
- **Dead ends (two-strike rule):** VideoCardz RSS wall (`wall.captcha`); The Register on-prem RSS 404 (headlines.atom used instead); web_search backend returned zero results for the "Alibaba chip unit to float" and "RDNA5 GFX13 patches" leads — both parked as leads for the next edition rather than chased; Hy4 flash official page is JS-only (covered via Unite.AI + explainX + search-returned snippet).
- **Overnight reality check:** the 17:45→07:45 slot is Asia-daytime/US-overnight — no US hardware launches fired; STH's only in-window post was the Monday TE-Connectivity OCP interview (video roundup, excluded as non-event).
