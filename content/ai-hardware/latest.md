# AI Hardware & Local Models Brief — November 12, 2026, 12:45 ET edition

**Window:** 2026-11-12 07:45 → 12:47 ET (previous fire → now). New items only; ledger-deduped.

## TL;DR

- **NVIDIA loses Jensen Huang.** The Board announced this morning that co-founder and CEO Jensen Huang resigned as President, CEO and director "effective immediately"; CFO Colette Kress is Interim CEO and Harvey C. Jones is the company's first-ever Board Chair. Shares fell ~12% premarket Thursday; NVIDIA promises the product roadmap continues "without interruption."
- **Axelera AI unveiled Titania**, its third-generation chiplet inference architecture: LPDDR6 instead of HBM (128 GB per two-chiplet package), an air-cooled 4U reference server with 20 PFLOPS FP8 and 1 TB accelerator memory, and claims of up to 7x throughput-per-watt and 20x better TCO than data-center-grade GPU servers — backed by over $300M in confirmed commitments, demoed at SC26.
- **UCIe 4.0 shipped** today: the chiplet interconnect spec moves from die-to-die plumbing to system-level integration (manageability, security, memory connectivity, RAS) — the standardization step chiplet-based AI accelerators need.
- **Penguin Solutions launched the SMART CXL-8F2W-D4**, an eight-DIMM DDR4 CXL add-in card (Marvell StructeraX controller) that recycles DDR4 as expansion memory for AI servers — up to 512 GB per card, directly aimed at the DRAM squeeze.
- **Local stacks:** vLLM merged Kimi K3.1 Pro ROCm DCP (split 5/6) plus a GLM-5.3-Flash serving preset worth +27% E2E throughput; llama.cpp shipped b12319–b12320; SGLang landed a 13-PR wave; mradermacher and bartowski pushed an afternoon GGUF ladder.

---

## AI hardware and silicon

### NVIDIA: Jensen Huang resigns as CEO; CFO Colette Kress takes over as interim (event date: November 12, 2026)

The NVIDIA Board announced Thursday morning that Jensen Huang — CEO since founding the company in 1993 — has resigned as President, CEO and board member, effective immediately, days after disclosures of his personal conduct triggered a national-security furor (TechCrunch reports the board accepted the resignation Wednesday night). CFO Colette Kress, at NVIDIA since 2013 and formerly of Cisco and Microsoft, serves as Interim CEO while keeping the CFO role; Harvey C. Jones (ex-Synopsys CEO, a director since 1993) was appointed the company's first-ever Chair of the Board and will lead the successor search. The stock dropped nearly 12% in Thursday's early morning trading — down as much as 19%, roughly $855B of market cap, since Friday. The Board explicitly stated that "operations, customer commitments and product roadmap" continue without interruption; the first real test of continuity comes Tuesday, November 17, with the Q3 FY2027 earnings call.

**Why it matters:** This is the biggest leadership shock in the company that sets the cadence of the entire accelerated-computing ecosystem — Rubin/Vera Rubin ramp timing, DGX Spark and GeForce AI product lines, CUDA/ROCm competitive posture, and the ~$99B investment book (OpenAI $30B, Anthropic $10B, CoreWeave, Nebius, the $105B Ohio OpenAI campus credit line) all now sit with an interim CEO and no designated successor. For homelab and local-inference buyers, near-term roadmap risk is low (the board's continuity language plus a scheduled Nov 17 call), but partner and neocloud equity reacted hard (CoreWeave -9%, Nebius -16%, IREN -6.6%), and pricing/availability of NVIDIA-adjacent hardware should be watched for the knock-on.
**Local inference:** No immediate change to CUDA, GeForce, RTX Spark or DGX Spark support; the exposure is forward-looking roadmap certainty (next data-center generation and consumer AI boxes) and the health of the second-hand GPU market if the selloff persists.
**Source:** NVIDIA Newsroom, "NVIDIA Announces CEO Transition," November 12, 2026 — https://nvidianews.nvidia.com/news/nvidia-announces-ceo-transition
**Source:** TechCrunch, Ram Iyer & Julie Bort, November 12, 2026 — https://techcrunch.com/2026-11-12/nvidias-jensen-huang-resigns-chipmaker-names-cfo-colette-kress-as-interim-ceo/
**Source:** ServeTheHome, Ryan Smith, November 12, 2026 — "NVIDIA CEO and Co-Founder Jensen Huang Resigns, Effective Immediately" (https://www.servethehome.com/ front page)
**Source:** VideoCardz, November 12, 2026 (~09:45 ET) — "Jensen Huang officially resigns as NVIDIA CEO after 33 years" (videocardz.com news river)

### Axelera AI unveils Titania: LPDDR6 chiplet inference silicon claiming 7x perf/watt, air-cooled 4U (event date: November 12, 2026)

Axelera AI (Eindhoven) released the details of Titania, its third-generation architecture built on Digital In-Memory Computing with a chiplet design. Each Titania system-in-package pairs two chiplets (32 AI cores + 16 RISC-V compute cores, 128 MB cluster-shared SRAM each, per the architecture breakdown) on TSMC N4C, and the platform deliberately uses **LPDDR6 instead of HBM** — 128 GB capacity per package at lower memory power and no dependence on HBM supply. The flagship reference: eight SiP accelerators (codename AX8) in a standard **4U air-cooled server at 5–6 kW typical delivering 20 PFLOPS FP8, 1 TB of accelerator memory and 19.6 TB/s aggregate bandwidth** — Axelera says one server holds a 1.6-trillion-parameter model at FP4 with KV-cache headroom, two fit a legacy 15 kW rack. Claims: up to 7x the throughput-per-watt of the best data-center servers and up to 20x better TCO, with tensor/expert/pipeline parallelism, speculative decoding and KV-cache offload/disaggregation supported out of the box via the Voyager toolchain. Form factors span standard PCIe cards to OCP OAM modules; Axelera cites **over $300M in confirmed commitments**, with SC26 (booth 3251) demos this week.

**Why it matters:** The "inference doesn't need HBM or liquid cooling" thesis is the most credible challenger narrative to NVIDIA's rack-scale strategy, and it's backed by signed money rather than a slide. LPDDR6 capacity beats HBM capacity per dollar for sparse MoE (small active-parameter footprint), which is exactly where open-weight serving lives. Numbers above are vendor expectations, not independent measurements — treat the 7x/20x claims as targets until third-party tests land.
**Local inference:** Targets enterprise/neocloud racks rather than desktops, but the pricing pressure is real (Axelera explicitly positions below an RTX-6000-class server's cost/power budget); PCIe-card form factors and 4U air-cooled servers keep homelab-adjacent deployment plausible if OEM channels take them.
**Source:** Axelera AI press release, "Axelera AI Unveils Titania Architecture…," November 12, 2026 — https://axelera.ai/news/axelera-ai-unveils-titania-architecture-hpc-class-ai-responsiveness-without-liquid-cooled-racks-targeting-110b-underserved-market
**Source:** TechPowerUp press release post, November 12, 2026, 10:46 ET — https://www.techpowerup.com/354736/axelera-ai-unveils-titania-architecture
**Source:** Futurum Group analysis (per-chiplet core counts, 1.25 PFLOPS FP8 per chiplet, TSMC N4C), November 12, 2026 — https://futurumgroup.com/insights/axelera-titania-brings-native-fp8-and-fp4-to-in-memory-compute/

### UCIe Consortium releases the UCIe 4.0 specification — chiplets as a managed system (event date: November 12, 2026)

The Universal Chiplet Interconnect Express Consortium published UCIe 4.0 today (Beaverton, Nov 12), pushing the die-to-die standard "beyond chiplet-level interface design" to integrating multiple chiplets as one coherent on-package system: new capabilities for manageability and security, power/die-area efficiency, memory connectivity across chiplets, and reliability.

**Why it matters:** AI accelerators are increasingly chiplet mosaics (compute + HBM/LPDDR + I/O dies from potentially different vendors); 4.0's management/security plumbing is what makes multi-vendor chiplet designs serviceable and auditable at scale — a quiet unlock for the accelerator startups (Axelera above) and for CXL-attached memory topologies.
**Local inference:** Indirect — a healthier chiplet supply chain lowers cost pressure on the accelerators and client NPUs that local inference runs on.
**Source:** UCIe Consortium press releases index, November 12, 2026 — https://www.uciexpress.org/press-releases
**Source:** Business Wire via Yahoo Finance, November 12, 2026 — https://finance.yahoo.com/technology/articles/ucie-consortium-advances-chiplet-connectivity-130000549.html
**Source:** TechPowerUp, November 12, 2026, 10:39 ET — "UCIe Consortium Advances from Chiplet Connectivity to System-Level Integration with UCIe 4.0 Specification"

### Penguin Solutions launches SMART CXL-8F2W-D4: CXL memory expansion that recycles DDR4 for AI servers (event date: November 12, 2026, 09:30 ET)

Penguin Solutions introduced an eight-DIMM DDR4 Compute Express Link memory add-in card built on the Marvell StructeraX CXL controller: four memory channels support up to eight qualified DDR4 RDIMMs (DDR4-3200; eight 64 GB RDIMMs = **512 GB of CXL-attached capacity per card**), presenting retired/available DDR4 as additional system memory in compatible servers. Penguin cites TechInsights data that AI data centers account for 34% of DRAM demand in 2026, heading toward nearly 50% by 2029 — the card's pitch is capacity growth without buying scarce, expensive DDR5. Demoed under the CXL Consortium's banner at SC26.

**Why it matters:** With the November DRAM crunch already depressing GPU/laptop value (CPU-Z capacity declines, RTX price inflation), using dead DDR4 stock as KV-cache/expert-offload tier is a genuinely new supply valve for AI boxes, and CXL-attached RAM is the cheap expansion path for MoE expert streaming engines.
**Local inference:** CXL host-attached DDR4 is the same tier llama.cpp/vLLM expert-offload and KV-cache engines already consume (the Basalt/Strata/DeepSeek SSD-expert workloads); server-class, but it validates the "old RAM is the new capacity" playbook trickling down.
**Source:** TechPowerUp press release, November 12, 2026, 11:41 ET — https://www.techpowerup.com/354743/penguin-solutions-introduces-cxl-enabled-ddr4-module-to-help-address-memory-shortages-in-ai-data-centers
**Source:** Business Wire, November 12, 2026, 09:30 ET — https://markets.ft.com/data/announce/detail?dockey=600-202611120930BIZWIRE_USPRX____20261112_BW319858-1
**Source (technical spec):** CXL Consortium blog (Marvell StructeraX, 4 channels / DDR4-3200 / 512 GB config) — https://computeexpresslink.org/blog/cxl-gives-ddr4-a-second-life-penguin-solutions-demonstrates-memory-reuse-at-sc26-4883/

### Corsair launches HX1000i SHIFT CRYSTAL — first PSU with per-pin 12V-2x6 overcurrent protection (PinProtect+) (event date: November 12, 2026)

Corsair's new flagship 1000 W Cybenetics Platinum, ATX 3.1 PSU is its first with PinProtect+ per-pin overcurrent protection on the native 12V-2x6 GPU connector, plus side-mounted cables, an integrated iCUE LINK hub (up to 24 devices) and transparent panels; TechPowerUp's review went live the same morning.

**Why it matters:** The November 8 dual-RTX-PRO-6000 Seasonic cable-sleeving scare put 12V-2x6 failure modes back on the agenda; per-pin OCP is the first mainstream hardware mitigation sold to enthusiasts.
**Local inference:** Directly relevant to multi-GPU inference rigs (dual 5090/PRO-6000 builds) where connector overload is the classic failure; 1000 W suits 2x high-power-card workstations.
**Source:** TechPowerUp press release, November 12, 2026, 10:34 ET — Corsair HX1000i SHIFT CRYSTAL launch; review same day at https://www.techpowerup.com/ (Today's Reviews)

---

## Inference software and benchmarks

### vLLM: 19-PR in-window wave — Kimi K3.1 Pro split 5/6 (ROCm DCP) and GLM-5.3-Flash +27% E2E preset (event date: merges November 12, 12:48–17:40 UTC)

Highlights merged in the 07:45→12:45 ET window: **#65685 Kimi K3.1 Pro (split 5/6: ROCm DCP, P/D disaggregation, indexer top-k)** — continuing the day-0 K3.1 support arc first reported Nov 6; **#67099 GLM-5.3 Perf: `latency_specialized` on by default (+27.0% E2E throughput)** and **#67104 skip unused MTP post-attention rows (+4.2% E2E)**; **#67269 Muse Spark MegaMoE + B300 specialized-decode recipe**; **#66047 ROCm DSv4.1 sparse-indexer decode rows sharded across TP**; **#66318 Mamba2 ReplaySSM speculative decoding on the Triton backend**; **#67205 tensorized model loader** for cross-implementation loads; plus Mooncake re-admit fix #66384 and EPLB async-rearrangement fix #64485.

**Why it matters:** The GLM-5.3 defaults change is a free ~31% combined E2E uplift for GLM serving without hardware changes, and K3.1's ROCm DCP path keeps AMD in the frontier-open-model serving game.
**Local inference:** ROCm/DCP and B300 recipes are data-center-leaning, but ReplaySSM spec-decode and the sampler/loader fixes land in desktop SM120/SM89 builds too.
**Source:** GitHub merged-PR search, vllm-project/vllm, merged 2026-11-12T12:45Z–17:47Z — https://github.com/vllm-project/vllm (query: is:pr is:merged merged:2026-11-12T12:45Z..2026-11-12T17:47Z)

### llama.cpp b12319–b12320: Hexagon Q5_0/Q5_1, MCP tool-call cancellation, qwen3.5 tensor merge on convert (event date: builds published November 12, 16:34 and 17:44 UTC)

Two builds in the window ride on the b12302–b12318 batch already reported: **hexagon: add Q5_0 and Q5_1 support (#32094)** — Snapdragon NPU quant coverage expands mid-tier; **server: cancel MCP tool calls on client disconnect or timeout (#32025)** — kills orphaned agent tool executions; **qwen3.5: merge tensors on convert also (#32102)** — smaller/faster converted checkpoints. (A few-row CUDA MMQ improvement #31533 re-committed at 13:12Z was already reported.)

**Why it matters:** Q5-class Hexagon coverage makes 5-bit quants viable on Qualcomm silicon for the first time; MCP cancellation is a real agent-harness reliability fix for llama-server deployments.
**Local inference:** Runs on Snapdragon X2/8-Elite laptops and any llama.cpp host; the qwen3.5 converter change benefits every Qwen3.5 deployment.
**Source:** GitHub releases ggml-org/llama.cpp b12319 (2026-11-12T16:34:48Z), b12320 (2026-11-12T17:44:55Z) — https://github.com/ggml-org/llama.cpp/releases

### SGLang: 13-PR wave — runway praxis 1 diffusion, H3-Image CI, GLM-5.3-Flash cookbook pin (event date: merges November 12, 13:05–17:07 UTC)

In-window merges: **#49872 diffusion: runway praxis 1 support**; **#49706 run MiniMax-H3-Image T2I and edit in the B200 CI suite** and **#45730 keep ComfyUI patches in the plugin + refresh H3 compatibility**; **#50041 cookbook: pin GLM-5.3-Flash high-throughput recipes to mamba ratio 0.9**; **#49894 apply API keys in tokenizer workers and WebSocket routes** (security); reverts the Mamba GDS eviction policy (#50118); RoPE class support for DiT models (#44753); int4_w4a16 tuning-CLI accept (#49890).

**Why it matters:** H3-Image is now CI-tested on Blackwell with a maintained ComfyUI path — day-2 polish for the big open image model; the GLM-5.3-Flash recipe pin codifies the optimal hybrid-mamba ratio.
**Local inference:** #49894 matters for anyone exposing SGLang on LAN; H3/ComfyUI fixes help 16–24 GB image-gen rigs.
**Source:** GitHub merged-PR search, sgl-project/sglang, merged 2026-11-12T12:45Z–17:47Z — https://github.com/sgl-project/sglang

---

## Community spins and quantizations

### mradermacher afternoon GGUF ladder (event date: repos created November 12, 17:03–17:47 UTC)

A dense pre-close cluster: **Qwen3.5-4B-Arete-GGUF** — quants of wesjos/Qwen3.5-4B-Arete (a 4B Qwen3.5-4B SFT on NousResearch/arete-data-pairs, the small sibling of Nous's Arete-27B reported Nov 6; base landed 01:31 UTC today, mr's imatrix ladder 17:33 UTC); the **DeepSeek-V4-Pro-Qwen3.5-4B-UNCENSORED-heretic (+i1)** pair; **xtools-at MultiDistill megamix cluster** (4B 3x- and 8x-merge, 2B 6x-merge, all UNCENSORED-heretic, GGUF+i1); **Qwen3.5-4B-DeepThought-STEM re-upload**; **Qwen2.5-0.5B-PromptBus-v2-GGUF** (BRR1154's null-space-LoRA DAG-routing agent-orchestration SLM); **Qwen3-REAP-15B-A3B-GGUF** (pruned-MoE REAP); **LFM3-Omni-2B-RP-GGUF**; prose-rewriter-4b-v2.6; Andgit-0.6B-SFT (multilingual reasoning SFT); Lyric-135M (continual-pretrain toy).

**Why it matters:** The 4B Arete is the community's first small-footprint take on values-aligned SFT from the Arete data pair corpus, and PromptBus-v2 is an interesting DAG-routing experiment at 0.5B.
**Local inference:** All 4B/2B/0.5B ladders quantize under 4 GB (any 8 GB card / phone-class SoC); REAP-15B-A3B targets 12–16 GB cards.
**Source:** Hugging Face, mradermacher (createdAt 17:03–17:47Z) — e.g. https://huggingface.co/mradermacher/Qwen3.5-4B-Arete-GGUF , https://huggingface.co/mradermacher/Qwen2.5-0.5B-PromptBus-v2-GGUF , https://huggingface.co/mradermacher/Qwen3-REAP-15B-A3B-GGUF

### bartowski: Barcenas (Mexican-Spanish LFM3) and MoganOCR-TR (event date: repos created November 12, 17:19–17:23 UTC)

**bartowski/Danielbrdz_Barcenas-LFM3-1.7B-GGUF** — ladder of Danielbrdz's Mexican-Spanish SFT of Liquid AI's LFM3-1.7B (trained on OysterCoreAI/SFT-General-Spanish-50K); **bartowski/moganai_MoganOCR-TR-GGUF** — quants of moganai's Turkish document OCR VLM (document parsing to markdown, tr+en).

**Why it matters:** Continues the fast localization-of-LFM3 trend; Turkish OCR VLMs at GGUF sizes are practical edge document-AI.
**Local inference:** 1.7B models run on 4–6 GB quantized; the OCR VLM lands under 2–4 GB — Jetson/RPi5/phone class.
**Source:** Hugging Face — https://huggingface.co/bartowski/Danielbrdz_Barcenas-LFM3-1.7B-GGUF , https://huggingface.co/bartowski/moganai_MoganOCR-TR-GGUF

### Also noted (community): glitch401/qwen3.8-27B-terse (16:57 UTC) — a "concise-reasoning" weight-space-edit LoRA on Qwen3.8-27B (follow-on to yesterday's humanizer/terse cluster); richardyoung/OLMo-2-0325-32B-Instruct-DiM-heretic-GGUF (17:20 UTC) — imatrix ladder of ALLENAI heretic ablation.

---

## Model releases

No verified brand-new open-weight model releases landed inside this window (the day's flagship weight event, Translated's **Lara Mini**, missed it — see footnote).

---

## Just missed the window

- **Translated — Lara Mini Base (7.2B-A0.6B multilingual MoE):** from-scratch 6T-token pretrain, hybrid Gated DeltaNet/SLA/global (34 layers), 128k context; tops 36 open ≤2B-active base models with a 57.0 overall average and ~1,100 chars/s on one A100 (Pareto-fastest class); CC BY-NC-SA 4.0, gated. Repo created Nov 11, 14:38 UTC — yesterday's windows missed it; the HN post (story_50471156, 12:43 ET) is re-coverage. — https://huggingface.co/translated/Lara-4-Mini-Base (footnoted once, never again)

## Coverage notes

- **Verified items this edition: 11 report items** (5 hardware/silicon incl. launches, 3 software/benchmark waves, 2 community-quant items + 1 also-noted) plus 1 "Just missed" footnote — each backed by a fetched in-window page; lead stories read in full (NVIDIA Newsroom, TechCrunch, Axelera PR read end-to-end).
- **Ledger dedupe:** ~16 in-window/re-surfaced candidates suppressed as already reported or already-footnoted — RTX 5090 96GB listing, Beelink Panther Lake NAS, Siren Halo GDDR7, meta-encoder-v1, KQuant MLX quants, MACaQ-R Q4_K_XL, GLiDE-9B (all 07:45 today); unsloth/Qwen3.8-27B-GGUF and KarLexAI Qwen3.8-27B-GGUF (refresh updates); xtools DeepThought-STEM re-upload (Nov 4); ServeTheHome MetaRoCE + TPU 8tx/8i Max + "EPYC 9006 Knockout Part Deux" homepage re-crests (missed-tagged, Nov 9–11 disclosures); The New Stack Nemotron-Transcribe piece (Oct 28 event, reported Nov 3).
- **Logged exclusions/dead ends:** wici.ai "InferSSD 800B on consumer hardware" HN item (story_50471151) is a re-surfacing of an **October 29** article — too old for even a "just missed" footnote; The Register /AI_Data/ returned 404 (front page used instead — only non-hardware items in-window); AMD Adrenalin 26.11.1 driver page crawl stamps (~17h) put its availability just before the window, so the release itself is not listed (the Nov 11 ledger entry already covers the "fix in 26.11.1" plan); r/LocalLLaMA new queue returned zero posts inside 12:45–17:47 UTC (quiet midday window; community lane otherwise walked via HN river).
- Phase walls honored; two-strike rule applied (Register 404 not retried).
