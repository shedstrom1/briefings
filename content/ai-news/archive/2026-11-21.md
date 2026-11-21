# AI News Brief — Saturday, November 21, 2026, 7:00 AM ET

**Window:** Friday, November 20, 5:00 PM ET → Saturday, November 21, 7:00 AM ET (overnight sweep, 14 hours). New items only; events already carried by previous editions are not re-listed.

**TL;DR**
- Tencent published open weights for **WeMM-Video**, a 2B/4B/9B + 35B-A3B omni-modal (video+audio) family under Apache-2.0, with FP8, AWQ, GGUF and NVFP4 builds and a native GGUF runtime — the most capable open audio-visual understanding stack you can actually run locally.
- **Artificial Analysis scored Thinking Machines' Inkling 2 Mini at 20** on its Intelligence Index — the same rounded score as Qwen3.8-27B at xhigh, with ~5x fewer active parameters — and Goodfire published an interpretability study of the looped architecture.
- **vLLM merged a cluster of security fixes** overnight (auth middleware path matching, prompt-count bounds on pooling/scoring routes, int64 CUDA pointer arithmetic, shared media byte budgets) alongside ~14 other merges; llama.cpp and SGLang also pushed hard.
- **SoftBank's Masayoshi Son approached Wall Street about a ~$1 trillion buyout of SK Hynix**, then shelved it — the largest memory-supply acquisition idea of the AI buildout so far.
- **Oracle declared its OpenAI infrastructure contract "fully funded,"** the first hyperscaler to claim that for this investment cycle, pushing back on circular-financing scrutiny.

---

## Models and local inference

### Tencent ships WeMM-Video: open-weight omni-modal video models from 2B to 35B-A3B, with GGUFs for local GPUs

**Event date:** weights posted Friday night/Saturday pre-dawn (Hugging Face file timestamps for `tencent/WeMM-Video-9B` last modified 2026-11-21T02:52:56Z = 9:52 PM ET Friday); technical report (arXiv:2611.26409) submitted Nov 19.

Tencent's WeChat Vision team released **WeMM-Video**, a Chinese-English bilingual omni-modal family that ingests text, images, audio and video together and answers in text: three dense checkpoints (2B, 4B, 9B — the 9B carries ~10.5B parameters on its model card) plus a **35B-A3B mixture-of-experts** flagship. The release ships BF16, FP8, AWQ, GGUF and NVFP4 builds, a Dockerised patched-vLLM/SGLang route, and a **native runtime for the GGUF Q4_K_M files on CPU or CUDA**, with Transformers `trust_remote_code` as the third path. Training used ~1.2T tokens and the authors evaluated on 120+ benchmarks, claiming a new open-source state of the art in omni-modal video (beating Qwen3.5-Omni-Flash and Seed2.0-Mini-0428, narrowing but not closing the gap to Gemini-3.0-Flash), and that the **9B surpasses the larger open Qwen3-Omni-30B-A3B-Instruct** on most audio-visual tasks. All figures are the authors' own and not yet independently reproduced; each checkpoint's model card carries its own licence terms.

**Why it matters:** joint audio-visual understanding has been the clearest gap between closed and open models — video QA in open weights usually means silent frames plus a separate ASR pass. An Apache-2.0 family with an official GGUF runtime and a released evaluation/reproduction pipeline makes that testable rather than aspirational, and the 35B-A3B's 3B-active footprint is exactly the shape that runs well on consumer hardware.

Treat the vendor's quality claims as unverified until third-party runs land.

Sources:
- Tencent WeMM-Video-9B model repository (Hugging Face API; files last modified 2026-11-21 02:52 UTC) — https://huggingface.co/tencent/WeMM-Video-9B
- "WeMM-Video: Tencent Open Models Hear and See (2B–35B)," ExplainX, Nov 20–21, 2026 — https://explainx.ai/blog/wemm-video-tencent-open-omni-modal-video-models-2b-35b-a3b-november-2026
- "WeMM-Video Releases Open Omni-Modal Models From 2B to 35B-A3B," Let's Data Science, Nov 20, 2026 — https://letsdatascience.com/news/wemm-video-releases-open-omni-modal-models-from-2b-to-35b-a3-b572e4c8

### Update on previously reported — Artificial Analysis puts Inkling 2 Mini level with Qwen3.8-27B at xhigh

**Event date:** Friday, Nov 20, ~5:10 PM ET (Artificial Analysis published its Inkling 2 Mini index page; Techmeme river logged the statement at 5:10 PM ET). This is a material update on *Inkling 2 Mini* (reported Nov 20, 17:00 edition).

Artificial Analysis now scores Thinking Machines' **Inkling 2 Mini at 20 on its Intelligence Index v5.0.1** — the same rounded score it shows for Qwen3.8-27B at xhigh and Nanbeige4.3-33B, against a median of 10 for the class — from **26B total / 5.4B active parameters** under Apache-2.0 with a 1M-token context. Its hosted numbers: $0.15 in / $0.50 out per 1M tokens with a 90% cache discount, 280 tok/s output, 9.72 s time-to-first-answer-token, 9.0 minutes and $0.59 per index task, and 161M generated tokens across the evaluation versus an 85.7M median — i.e. very verbose. Separately, **Goodfire published an interpretability study of the same model** (Nov 20) after early weight access, describing 24 layers in four blocks where blocks 2–3 are looped three times with attention-residual reads, and reporting the counterintuitive finding that looped layers can write to themselves or read from "future" layers. Inference-engine support landed in-window too: SGLang added Inkling-2 cookbook/hardware tables (#52042) and an `rtx6000` hardware id for Inkling-2-Mini (#51953), and llama.cpp merged `inkling2_loop` loop-slot sharing after the scale pass (#32704).

**Why it matters:** an independent index score is the first non-vendor number on this model, and "matches a 27B at 5x fewer active parameters" is the specific claim that decides whether looped-MoE architectures get taken seriously for local agentic work. The verbosity finding is the practical caveat — token volume, not raw score, drives cost on agent loops.

**Local inference:** AA's class-relative positioning (above-average intelligence, quicker-than-average per task, priced above open-weight medians) plus day-0 engine support makes Inkling 2 Mini the open model to benchmark against Qwen3.8-27B on 16GB-class hardware — remembering it is 20/20 on *displayed* score, with AA's own small-model ranking placing Nanbeige4.3-33B first on tiebreak.

Sources:
- Artificial Analysis, "Inkling 2 Mini — Intelligence, Performance & Price Analysis," published Nov 20, 2026 — https://artificialanalysis.ai/models/inkling-2-mini
- Goodfire, "Interpreting Inkling 2 Mini," Nov 20, 2026 — https://www.goodfire.com/research/interpreting-inkling-2-mini/
- Techmeme river entry for the Artificial Analysis statement, 5:10 PM ET Nov 20, 2026 — https://www.techmeme.com/river

### A quiet-night inference-engine wave — headlined by five vLLM security merges

**Event date:** merges between 11:00 PM ET Friday and ~6:10 AM ET Saturday.

vLLM's in-window merge list is unusually security-weighted: **#52794 fixes root-path matching in the auth middleware**, **#53473 enforces a prompt-count bound on pooling and scoring routes**, **#68249 shares media byte budgets across batched requests**, **#53131 widens int32 pointer arithmetic to int64 in CUDA kernels**, and **#69101 adds a vLLM security-review skill** to the repo; alongside them came ZAYA1 CCA prefill checkpoints (#68921), a fused 16-bit recurrent-state MTP CUDA decode kernel (#69065), Qwen3-Omni/Qwen3-ASR audio-tower encoder CUDA graphs (#68831), HY4-flash HiSparse support (#69026) and a DSv4.1 DSpark mega-MoE flag fix (#69023). llama.cpp pushed 15 in-window commits including a grammar integer-overflow fix (#32551), Vulkan RDNA4 MMVQ speedups for Q3_K/Q5_K/IQ4_XS (#31804), an AMD-Windows driver-bug workaround unrolling FA coopmat1 (#32466), removal of the fused lightning-indexer gate (#32650) and a CUDA fastdiv UB abort (#32698). SGLang merged 59 commits across the window, heavily weighted to its Rust server (worker-management API #50242, `/internal/peer` now served only to the launch's own listeners #51946) plus NVFP4 tiny-FP32 router GEMM (#51688) and FlashInfer CuTe-DSL MoE/MegaMoE prefill CUDA graphs (#50684). **Ollama remains at v0.40.17 (Nov 10)** — no in-window release.

**Why it matters:** self-hosted inference servers are increasingly reachable services, and this batch is a rare case where the weekend diff is mostly hardening rather than kernels — worth pulling for anyone exposing vLLM/SGLang endpoints. The SGLang router change (#51946) is the same failure class (internal endpoints reachable from outside the launch) that has driven several recent agent-infrastructure incidents.

**Local inference:** the vLLM auth-middleware and pooling-bound fixes plus llama.cpp's grammar overflow fix are directly relevant to single-box deployments; RDNA4 MMVQ and NVFP4 router work target exactly the quantized paths consumer GPUs use.

Sources:
- vLLM merged pull requests (GitHub API, `merged_at` 2026-11-21T00:00Z–11:08Z) — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged
- ggml-org/llama.cpp commit log (GitHub API, since 2026-11-20T22:00Z) — https://github.com/ggml-org/llama.cpp/commits/master
- sgl-project/sglang commit log (GitHub API, since 2026-11-20T22:00Z) — https://github.com/sgl-project/sglang/commits/main

---

## Agent tools

### Friday-evening agent-CLI drops: Claude Code 2.1.330, Codex 0.173.0-alpha.3/.4, LiteLLM 1.108.14

**Event date:** Friday, Nov 20, between 5:23 PM and 7:35 PM ET.

Anthropic shipped **Claude Code v2.1.330** at 5:32 PM ET (tag `v2.1.330`, following 2.1.329 the previous evening); OpenAI tagged **Codex rust-v0.173.0-alpha.3** at 5:23 PM ET and **alpha.4** at 7:35 PM ET; **LiteLLM v1.108.14** was published at 6:43 PM ET, alongside a v1.111.0-rc.3 cut shortly before the window opened. SGLang's Rust server picked up a **runtime worker-management API (#50242)** in the same stretch, and vLLM's #69101 adds a repo-level **security-review skill** — the pattern of shipping reviewer agents as versioned artifacts inside the project keeps spreading. OpenClaw had no in-window release (its latest are v2026.11.10-beta.1 and v2026.11.6, both Nov 19).

**Why it matters:** the pace of alpha tags on the coding-agent CLIs (four Codex alphas in a day) is now the clearest live signal of how much engineering is going into agent harnesses rather than models — and it is the layer where tool-call, sandboxing and credential handling actually live.

Sources:
- anthropics/claude-code releases (GitHub API, `published_at` 2026-11-20T22:32:56Z) — https://github.com/anthropics/claude-code/releases
- openai/codex releases (GitHub API, tags 2026-11-20T22:23Z and 2026-11-21T00:35Z) — https://github.com/openai/codex/releases
- BerriAI/litellm releases (GitHub API, v1.108.14 published 2026-11-20T23:43:31Z) — https://github.com/BerriAI/litellm/releases

---

## AI security and governance

### SoftBank's Son sounded out Wall Street on a ~$1 trillion SK Hynix buyout, then shelved it

**Event date:** Friday, Nov 20, 9:28 PM ET (The Information); Reuters carried it Saturday.

Masayoshi Son **approached Wall Street institutions about financing a roughly trillion-dollar buyout of SK Hynix**, according to people familiar with the matter; a SoftBank spokesperson said, "SoftBank explores many opportunities, and this one is no longer under active consideration." The approach surfaced on the same week the memory maker reported record results and disclosed it is becoming Kioxia's largest shareholder, and days after SoftBank's own shares were hit by the AI selloff — the buyout would have been larger than SoftBank's entire market cap.

**Why it matters:** HBM is the binding constraint on frontier training, and a Japanese conglomerate trying to take control of one of the world's two major HBM suppliers would have been the single biggest consolidation move of the AI buildout — with direct implications for chip export policy and for Seoul's leverage over its own flagship semiconductor firm. That it was explored at all, on that time scale, tells you how memory is being priced as strategic infrastructure.

Sources:
- "SoftBank's Masayoshi Son Explored $1 Trillion Moonshot Buyout of SK Hynix," Benzinga (via The Information), Nov 20, 2026 9:28 PM ET — https://www.benzinga.com/content/63514543/softbank-s-masayoshi-son-explored-1-trillion-moonshot-buyout-sk-hynix
- "SoftBank's Son explored $1 trillion buyout of SK Hynix, the Information reports," Reuters, Nov 21, 2026 — https://www.reuters.com/world/asia-pacific/softbanks-son-explored-1-trillion-buyout-sk-hynix-information-reports-2026-11-21/

### Oracle declares its OpenAI infrastructure contract "fully funded"

**Event date:** Friday, Nov 20, 5:43 PM ET (statement carried by Yahoo Finance/Investing.com; Oracle's own post logged at 7:25 PM ET in the Techmeme river).

Oracle said it **has secured all funding needed to fulfil its cloud-infrastructure contract with OpenAI**, calling itself "the first hyperscaler fully funded for this investment cycle" after a final capital raise earlier in November that completed the framework laid out at its October 28 Investor Day — a cap of $20B of new capital-market funding for fiscal 2027–2030 ($10B equity / $10B gross debt) on top of a $20B at-the-market equity raise, plus vendor financing, customer-supplied hardware and prepayments. Oracle preemptively labelled contrary media reports incorrect, said further private vendor discussions are "additional options," and pointed at buildout markers: Abilene construction nearing completion and Shackelford fully permitted.

**Why it matters:** with Nvidia's own financing posture tightening and neocloud credit spreads blowing out, "who is actually paying for the compute" has become the central bear case. A hyperscaler publicly closing the question on its largest single contract — a five-year, ~$300B OpenAI agreement that dominates its remaining performance obligations — is a governance signal as much as a funding one, and the market was not reassured (ORCL -6.6% in the same report).

Sources:
- "Oracle says OpenAI contract is fully funded, addresses market financing scrutiny," Yahoo Finance / Investing.com, Fri Nov 20, 2026 5:43 PM EST — https://finance.yahoo.com/technology/ai/articles/oracle-says-openai-contract-fully-224306974.html
- "Oracle says OpenAI infrastructure deal is fully funded after final capital raise," Reuters, Nov 20, 2026 — https://www.reuters.com/business/oracle-says-openai-infrastructure-deal-is-fully-funded-after-final-capital-raise-2026-11-20/

**Also noted:** SGLang merged Hunyuan-4-Flash vision-language support (#51937, 00:24 UTC Sat) and a fix reading HY4-flash TP rank/fusion gate (#51969) — the day after Tencent's Hy4 flagship weights appeared on Hugging Face.

---

## Just missed the window

- **Tencent released the full Hy4 flagship open weights** (770B MoE, ~774B params on the model card, plus FP8 and base checkpoints, under the custom Tencent Hy License with a MaaS carve-out). Disclosure is out-of-window: Hugging Face repo timestamps read 2026-11-20T03:05Z = **Nov 19, 10:05 PM ET** — https://huggingface.co/tencent/Hy4 , https://www.nglobenews.com/en/article/china-tencent-hy4-open-weights-2026-11-20/
- **Tristan Buckmaster and Levent Alpöge published their AI-aided "unforced" Navier-Stokes blowup proof** (79-page PDF + Lean 4 package), claiming to close the forcing loophole in OpenAI's September result. Scientific American's write-up is dated **Friday, Nov 20** (before the 5:00 PM cutoff) — https://www.scientificamerican.com/article/ai-just-solved-the-real-navier-stokes-problem/

---

## Coverage notes

- **Verified: 6 in-window items** (3 under Models and local inference, 1 under Agent tools, 2 under AI security and governance), counting one entry per underlying event. The engine-sweep entry and the "Also noted" Hy4-flash/VL one-liner are folded into their parent entries and not double-counted. The two "Just missed the window" footnotes are excluded from the qualifying count.
- **Ledger dedupe: 11 candidates suppressed** as already reported — Cursor's "phasing out Chinese open-weight models" (already reported Nov 20 12:00; re-run 6:00 PM in the river), Nasdaq Friday close/AI selloff (reported Nov 20 17:00), SEC-probes-Nvidia follow-ons (reported Nov 20 17:00), Trump–Xi military-AI statement and Trump data-center-tax threat (reported Nov 20 17:00), Alice Huang/$3.6B network (reported Nov 20 17:00, river re-runs), Anthropic post-IPO price action (reported Nov 19–20), Alpöge Riemann/GRH thread (reported Nov 20 12:00/17:00, incl. Terence Tao quote), TSMC/Samsung China shipment suspensions incl. Nikkei's consumer-SoC breadth piece (Nikkei published 00:10 ET Friday; already covered Nov 19/20), the Hy4 flagship weights release (footnoted once above), llama.cpp Inkling-2-Mini mainline #32694 (reported Nov 20 17:00), and Nscale/ByteDance remote-access detail (Nov 20 pre-window).
- **Out of scope:** the FBI's apparent NCIC criminal-database breach and the USDA farm-payment intrusion (both reported Friday evening) are law-enforcement/civil-system breaches with no verified AI vector, so they are not carried here.
- **Dead ends / limitations:** Hugging Face API calls for `tencent/WeMM-Video-35B-A3B` (repo + `/commits`) returned an auth-wall error on two attempts, so the 35B checkpoint timestamp is sourced from the 9B repo plus two secondary write-ups rather than directly; Reuters article bodies for the Oracle and SK Hynix stories failed to fetch and are cited from the search result plus fetched second-outlet copies; TechCrunch's "honey-trap scheme" piece (Nov 20, surfaced on HN Saturday) 404'd on retrieval and is dropped rather than cited unread; TrendForce's export-control follow-up failed on a TLS error.
- **Community lanes:** the HN `search_by_date` river was walked for the full window (49 stories; the only AI-relevant traffic was the FBI/NCIC story and low-point Show HNs — e.g. an open-source shared-memory/Git-blame plugin for agents at 2 points). **r/LocalLLaMA was unavailable this fire** — both the `new.rss` and `top.rss` anonymous feed endpoints returned HTTP 404 from this host, so no Reddit lane was sampled; nothing should be read into that as a quiet community.
- **Indexes used:** Techmeme river (full window), HN Algolia `search_by_date`, GitHub APIs for llama.cpp / vLLM / SGLang / Ollama / OpenClaw / LiteLLM / Claude Code / Codex / LangGraph, Hugging Face model API, llm-releases.com and AIBriefs aggregators (leads only). DuckDuckGo returned empty results on several news queries, which narrowed the long tail; the weekend itself was genuinely thin, so this edition is short by design, not by truncation.
