# AI Hardware & Local Models Brief — 10-Day Catch-Up Edition, Nov 3 2026

**Window: 2026-10-23 17:45 ET → 2026-11-03 21:44 EST** (extended catch-up window — this job was dark from Oct 24 through Nov 3 after a hung run on a dead provider; prioritised Oct 31 → now at full depth, with still-material items from Oct 24–31 included).

## TL;DR

- **NVIDIA quietly cut shipping Rubin specs as first production hardware lands**: per-GPU HBM4 bandwidth drops 22 → 19.2 TB/s (−11%) and NVLink scale-up 3.6 → 3.0 TB/s (−17%), while scale-out networking rises 0.4 → 0.45 TB/s. The NVL72 rack follows in lockstep (1,580 → 1,400 TB/s).
- **Qualcomm completed first-silicon validation of HBC** (DRAM stacked directly on a compute die) and confirmed **Dragonfly AI250 sampling in 1H 2027** — an LPDDR5X-based near-memory stack pitched against HBM4 on bandwidth-per-watt and packaging cost.
- **Mistral released Mistral Large 4 "Le Chonk" open weights** (Oct 29): ~1T total / ~49B active multimodal MoE, 1M context, FP8 + NVFP4 checkpoints plus day-0 GGUFs, and a headline 82% on the Artificial Analysis Cyber Index.
- **Two inference-stack releases landed the same day**: SGLang v0.5.23 (Paged Experts — MoE larger than VRAM on one consumer GPU, up to 30× over CPU offload) and vLLM v0.33.0 (drops CUDA 12.9, moves ROCm to 10.0, DSpark spec-decode 1373 → 3390 tok/s on GB300).
- **Basalt 0.2.0** pushed Qwen 3.8 Flash Next NVFP4 to ~300 tok/s prose / ~600 tok/s structured on a 400 W RTX 5090 + 180 W RTX 5060 Ti 16 GB + 128 GB DDR5 rig, and **ISTA-DASLab pruned half the experts out of DeepSeek-V4.1-Flash** to make a ~110 GB coder quant.

---

## AI hardware and silicon

### NVIDIA revises Rubin GPU specifications as first production hardware arrives
**Event date: Oct 31, 2026 (ServeTheHome report of specs posted by NVIDIA in September; first NVL72 systems now generally available at hyperscalers.)**

NVIDIA's shipping datasheet now gives a single Rubin GPU 19.2 TB/s of HBM4 bandwidth instead of the 22 TB/s it promoted — 8 stacks × 2048 bits running at 9.375 Gbps/pin rather than 10.75 Gbps/pin, a ~11% cut. NVLink scale-up per GPU falls from 3.6 TB/s to a flat 3.0 TB/s (−17%), while scale-out networking goes the other way, up from 400 GB/s to 450 GB/s. Compute is untouched at 50 PFLOPS NVFP4 inference / 35 PFLOPS NVFP4 training and capacity stays at 288 GB HBM4 (8×36 GB), so clocks and capacity targets were hit — it is memory and interconnect rates that slipped. The Vera Rubin NVL72 rack tracks the per-GPU deltas: 1,580 → 1,400 TB/s aggregate HBM bandwidth and 260 → 216 TB/s NVLink, with networking up to 32.4 TB/s. For context, AMD's MI455X runs its HBM4 stacks at only 7.6 Gbps/pin, so NVIDIA's revised rate is still aggressive relative to the rest of the market.

**Why it matters:** decode throughput on bandwidth-bound LLM serving scales almost linearly with HBM rate, so an 11% bandwidth haircut is a real 11% off the top of tokens/sec-per-GPU versus the marketed number — and buyers who sized racks on the 22 TB/s figure need to re-derive. It also confirms the industry story that HBM4 parametric yield at speed, not logic die, is the binding constraint this generation.

**Local inference:** no direct consumer-hardware effect, but it resets expectations for what "NVL72-class" token pricing will cost — and any local stack that benchmarks against Rubin (vLLM/SGLang both added GB300/Rubin decode paths this window) should be measured against 19.2 TB/s, not 22.

Source: ServeTheHome — "NVIDIA Updates Rubin GPU Specs as First Production Hardware Arrives," Ryan Smith, Oct 31, 2026 — https://www.servethehome.com/nvidia-updates-rubin-gpu-specs-as-first-production-hardware-arrives/

### Qualcomm validates HBC stacked memory; Dragonfly AI250 to sample in 1H 2027
**Event date: Nov 2, 2026**

Qualcomm announced completion of initial hardware validation of High Bandwidth Compute — its variant of memory stacking where multiple LPDDR5X dies (Qualcomm uses wafers, not thin HBM cores) sit on top of a small compute die rather than on a passive base die. In San Diego labs the team exercised memory read/write paths and near-memory compute on a two-layer test stack (shipping parts will be four layers), characterising the same silicon as both an LPDDR5X device and an HBC device. Qualcomm claims the approach reaches HBM-class bandwidth at far better energy efficiency while dropping the CoWoS/EMIB-class packaging HBM demands — the memory-to-processor link can be built on conventional 2D organic substrates. First productisation is the Dragonfly AI250 accelerator sampling in the first half of 2027, later folded into Qualcomm Dragonfly Rack; the company has also said HBC is destined for Snapdragon client parts, without dates.

**Why it matters:** HBM4 supply and advanced packaging are the two hard limits on AI accelerator output right now (see the SK hynix item below), and HBC attacks both — LPDDR5X wafers instead of HBM stacks, organic substrate instead of CoWoS. If the efficiency claims hold at four layers, this is the most credible non-HBM path to cheap inference capacity since CXL. Note the caveat: no final bandwidth or capacity numbers, and validation used a half-height stack.

**Local inference:** nothing for a desktop today — sampling is 1H27 — but it is the counterweight to the CUDA/ROCm memory-price squeeze that is currently pricing 32 GB and 128 GB local boxes, and Qualcomm's stated target is cheaper-to-operate inference than GPUs.

Source: ServeTheHome — "Qualcomm Validates HBC Stacked Memory Ahead of 2027 Dragonfly AI250 Sampling," Ryan Smith, Nov 2, 2026 — https://www.servethehome.com/qualcomm-validates-hbc-stacked-memory-ahead-of-2027-dragonfly-ai250-sampling/
Source (vendor): Qualcomm — "Qualcomm HBC demonstrates AI inference leadership with validated silicon," Nov 2, 2026 — https://www.qualcomm.com/news/onq/2026/11/qualcomm-hbc-validated-silicon-ai-inference

### ASRock ships a $1,700 32 GB RDNA 4 inference card: Creator Radeon AI Pro R9700 reviewed
**Event date: Oct 26, 2026 (ServeTheHome review)**

ServeTheHome tested ASRock's top RDNA 4 workstation card on the explicit premise that discrete cards are now "the most effective way to add high-performance inference hardware to a standard-form-factor PC." Specs as configured: Navi 48 with 64 CUs / 4096 shaders and 128 AI accelerators, 2.92 GHz boost, 382.7 TFLOPS peak FP8 matrix throughput, 47.8 TFLOPS FP32 vector, 32 GB GDDR6 at 640 GB/s (20 Gbps × 256-bit), 64 MB Infinity Cache, PCIe Gen5 x16, 300 W total board power, dual-slot blower, rear-mounted 12V-2×6, and a $1,700 price. Matrix math runs on CU-integrated WMMA units rather than discrete CDNA matrix cores — RDNA 4 doubled FP and quadrupled INT8 rates versus RDNA 3. The reviewer's own build advice: throttle the card to ~210 W for a minor performance loss and buy more than one.

**Why it matters:** at $1,700 for 32 GB with FP8 support and 300 W, this undercuts equivalent-capacity workstation parts on price-per-GB and per-watt, and multi-card density is realistic because AMD enforces a uniform dual-slot blower design across partners. Commenters pushed vLLM-Radiance MXFP4 as the right quant target on this card.

**Local inference:** 32 GB GDDR6 class — comfortably holds a 27B dense model at Q6/Q8 or a 30B–35B-A3B MoE at 4-bit with room for KV cache; four cards in one chassis stay within a single PSU's envelope at 300 W each.

Source: ServeTheHome — "ASRock Creator Radeon AI Pro R9700 Review: Workstation Inference, AMD Style," Ryan Smith, Oct 26, 2026 — https://www.servethehome.com/asrock-creator-radeon-ai-pro-r9700-review-workstation-inference-amd-style/

### ASRock Industrial AI Box-A395: a Ryzen AI Max+ 395 "cheese grater" built for headless inference
**Event date: Oct 29, 2026 (ServeTheHome review)**

A Ryzen AI Max+ 395 (16C/32T, 5.1 GHz) with 128 GB of soldered LPDDR5X-8000 and the 40-CU Radeon 8060S iGPU, packaged not as a low-profile mini-PC but as a vertical mini-ITX micro-tower (200 × 100 × 232 mm, 2.8 kg) with an internal 400 W PSU, aluminium chassis and an optional carry handle — STH compares the venting to Apple's 2003 Power Mac G5. I/O is deliberately industrial: two 480 Mbps USB-A on top, and on the rear two USB4 40 Gbps plus one 10 Gbps USB-C, two 10 Gbps USB-A, two HDMI 2.1, a Marvell AQC113 10GbE RJ45 alongside a Realtek 2.5GbE port, and Wi-Fi 7 with external antennas. Two empty M.2 2280 Gen4 x4 slots and no 2.5-inch drive position. STH's verdict: ASRock's "AI workstation" marketing is optimistic — it is a portable, always-on Ryzen AI Max box for industrial environments.

**Why it matters:** the Strix Halo desktop class has become the de-facto local-inference appliance segment, and this is the first entry that prioritises headless industrial deployment (internal PSU, 10GbE, handle, no front I/O) over the gaming-adjacent styling of its rivals.

**Local inference:** 128 GB coherent memory with a 40-CU iGPU is the same class as Framework Desktop / DGX Spark-tier boxes — enough to keep large MoE expert pools resident, with 10GbE for offload and clustering; barebones (no OS, no storage) pricing/config flexibility is the point.

Source: ServeTheHome — "ASRock Industrial AI Box-A395 Review: Taking Strix Halo to Work," Ryan Smith, Oct 29, 2026 — https://www.servethehome.com/asrock-industrial-ai-box-a395-review-taking-strix-halo-to-work/

### SK hynix posts record quarter and warns memory shortage lasts through 2030
**Event date: Oct 28, 2026 (Q3 results and press release)**

SK hynix reported Q3 revenue of KRW 104.6 trillion — the first quarter above the 100 trillion mark — with KRW 83.3 trillion operating profit (80% operating margin) and KRW 39.8 trillion net profit. HBM4 is expected to overtake HBM3E in both volume and revenue next quarter, making it the mainstream product; server DDR5 and LPDDR5X SOCAMM for AI servers are also growing. New M15X capacity should reach 55,000–60,000 wafers/month of 1b DRAM by year-end, 321-layer NAND TLC passes half of domestic output, and the company is developing HBF (High Bandwidth Flash) — HBM-style stacking used as model-weight streaming storage for AI servers. Its press release warns the supply shortage persists through 2030.

**Why it matters:** an 80% operating margin plus a "shortage through 2030" statement is the clearest signal yet that memory, not compute, sets the price of AI hardware — and HBF is the first serious attempt to put flash *inside* the memory hierarchy for weight streaming, which is exactly what MoE expert offload needs.

**Local inference:** every unified-memory box (DGX Spark, Strix Halo, Mac Studio) is priced off LPDDR5X/DDR5 supply, and a 2030 shortage horizon means the recent 64 GB-vs-128 GB Spark repricing is structural, not a blip. HBF-style weight storage is the hardware answer to the expert-offload path llama.cpp, Strata and SGLang's new Paged Experts are all pursuing.

Source: GSMArena — "SK hynix reports record Q3 numbers, warns that the memory shortage will last through 2030," Oct 29, 2026 (Q3 released Oct 28) — https://www.gsmarena.com/sk_hynix_reports_record_q3_numbers_warns_that_the_memory_shortage_will_last_through_2030-news-75249.php
Source (secondary): The Register — "SK Hynix says memory shortages to persist until 2030" — https://www.theregister.com/ai_ml/

### Marvell agrees to acquire Lumentum in a $115B all-stock combination
**Event date: Nov 2, 2026**

Marvell Technology entered a definitive agreement to acquire Lumentum in an all-stock transaction with an implied enterprise value of $115 billion, merging Marvell's electrical/optical interconnect, network switching, custom-XPU and XPU-attach business with Lumentum's high-performance lasers, optical transceivers, optical circuit switching and compound-semiconductor manufacturing.

**Why it matters:** optical circuit switching and co-packaged optics are the next bottleneck for rack-scale AI, and consolidating the DSP and the photonic component under one roof changes who can supply an all-optical scale-up fabric. Announced the same day Lumentum landed a second co-packaged-optics design win.

**Local inference:** no desktop implication; relevant to how expensive cloud token capacity stays as racks grow past the copper domain.

Source: Marvell Investor Relations — "Marvell and Lumentum to Combine, Creating a Category Leader in AI Connectivity," Nov 2, 2026 — https://investor.marvell.com/news-events/press-releases/detail/1040/marvell-and-lumentum-to-combine-creating-a-category-leader-in-ai-connectivity
Source (secondary): CNBC — "Marvell to buy Lumentum in all-stock deal," Nov 2, 2026 — https://www.cnbc.com/2026-11-02/marvell-lumentum-ai-deal.html

### Microsoft shows the Maia 300 platform: four 950 W, 288 GB HBM4 accelerators per tray
**Event date: Oct 27, 2026 (OCP Summit 2026, Santa Clara)**

A few weeks after its Hot Chips deep-dive on Maia 200, Microsoft brought a real four-accelerator Maia 300 node to OCP. Per chip: eight HBM4 stacks for 288 GB and 18 TB/s of memory bandwidth, 3.2 TB/s of bidirectional scale-up bandwidth, and a 950 W TDP under a liquid-cooled cold plate. Microsoft claims 96 Maia accelerators per rack and roughly 12,000 in a scale-up domain. Architecturally it is the opposite of NVIDIA's cable-reduction push — each accelerator gets its own PCB with multiple UltraPass-style cabled links, hybrid air/liquid cooling (CPU, SSDs and OCP NIC 3.0 devices sit under a large fan partition), ~1.5× standard-OU width, and rear busbar power with blind-mate coolant.

**Why it matters:** it is the clearest hyperscaler statement that custom silicon is now a first-order strategy, and 288 GB/18 TB/s per chip puts it in the same memory class as Rubin while Microsoft controls the whole tray.

**Local inference:** none directly (not sold), but the 288 GB HBM4 + 950 W envelope is the reference point for what "single-accelerator frontier inference" costs — which is why the local market keeps reaching for 128 GB unified-memory boxes and aggressive weight pruning instead.

Source: ServeTheHome — "Microsoft Maia 300 Shown at OCP 2026," Patrick Kennedy, Oct 27, 2026 — https://www.servethehome.com/microsoft-maia-300-shown-at-ocp-2026/

### Amazon cancels four DGX Spark orders and closes the buyer's account; Tim Sweeney reports the same
**Event date: Nov 2, 2026**

X user @nicksaraev says he placed four separate NVIDIA DGX Spark orders on Amazon on Oct 31; all four were cancelled and Amazon subsequently closed his account after what he called a "vague verification" process. Epic Games CEO Tim Sweeney replied that Amazon kept accepting and then cancelling his orders just before delivery after he tried to buy two NVIDIA GPUs — his account stayed open. Neither Amazon nor NVIDIA has explained. The listings involved are the GB10 units (20-core Arm, 128 GB LPDDR5X unified memory, 4 TB NVMe).

**Why it matters:** the mechanism is unconfirmed — anti-scalper logic or a systems glitch — but the practical result is that the flagship local-inference appliance is now rationed at retail, and buyers can lose their account for ordering several units.

**Local inference:** the DGX Spark's 128 GB coherent memory is the reference 1-box target for ~100B-class models; account-level rationing is a purchase-risk item for anyone planning a two- or four-Spark cluster (NVIDIA's Sync Cluster pools up to eight units / 1 TB).

Source: Wccftech — "Amazon Is Allegedly Canceling GPU Orders And, In Rare Cases, Closing Accounts For Purchasing Compute Hardware; Tim Sweeney Also A Victim Of This Practice," Omar Sohail, Nov 2, 2026 03:25 EST — https://wccftech.com/amazon-allegedly-canceling-gpu-orders-closing-accounts-compute-hardware-tim-sweeney/
Source (secondary): TweakTown — "Amazon reportedly cancels four NVIDIA DGX Spark orders and closes buyer's account," Nov 2, 2026 — https://www.tweaktown.com/news/114455/amazon-reportedly-cancels-four-nvidia-dgx-spark-orders-and-closes-buyers-account/index.html

### GPU price inflation continues: AMD reportedly planning a second Radeon/GDDR6 hike in November
**Event date: Oct 30, 2026**

Per the Board Channels forum, AMD raised GPU and GDDR6 kit supply prices on Oct 1 and may increase again in November, with pricing now reviewed quarterly; GDDR6 rose roughly 8–10% during Q3. STH/VideoCardz contextualise it with Hardware Unboxed's 10-country retail tracker: global GPU prices are up ~35% in three months (+10% in October excluding the RTX 5090), with the Radeon RX 9060 XT 16GB up 17% and RX 9070 / 9070 XT up 12% month-over-month. AMD has not confirmed the November adjustment.

**Why it matters:** 16 GB cards are the entry ticket for local inference, and a 17% monthly move on a 16 GB card changes the build-vs-API calculus for exactly the buyers that market serves.

**Local inference:** the RX 9060 XT 16GB / RX 9070 class is the budget tier for 8B–27B quants under llama.cpp and Vulkan; sustained increases push budget builds toward 32 GB workstation cards and unified-memory mini-PCs instead.

Source: VideoCardz — "AMD reportedly plans another Radeon GPU price increase in November," WhyCry, Oct 30, 2026 — https://videocardz.com/newz/amd-reportedly-plans-another-radeon-gpu-and-gddr6-price-increase-in-november

### Automated UEFI unlocker extends the GeForce tensor-core rate unlock to RTX 20/30/40 on Windows
**Event date: Oct 31, 2026** *(update on the previously reported "gpu-unlock" line — see Coverage notes)*

Open-source project **nv-uefi-unlocker** (physicsdolphin) consolidates the recent GeForce compute unlocks into one UEFI utility that applies firmware-level register changes before the NVIDIA driver loads, booting from a prepared USB stick and then handing off to the OS. Because it is not a VBIOS flash, the unlock must be re-applied after a cold boot; Secure Boot must be off, and Turing needs a GSP-firmware step. Verified results: RTX 2070 +67–75% FP32 GEMM, RTX 4060 Laptop FP16 GEMM 29.89 → 49.33 TFLOPS (+65%), RTX 3060 Ti LHR and several other Ampere SKUs confirmed; RTX 50 (Blackwell) is unsupported because the method does not work there, and GTX 16 support is experimental.

**Why it matters:** half-rate tensor math on GeForce has been the single biggest free performance lever this autumn, and automation plus Windows support moves it from kernel-module territory (reboot risk, Linux-only) to something a normal user can run — which is exactly what the earlier kernel-module unlock could not offer.

**Local inference:** free tensor throughput on existing Turing/Ampere/Ada cards, which is where most homelab inference rigs still live — but note the caveat the project itself makes: LLM text generation is memory-bandwidth-bound, so decode may barely move; the gains show in prefill/GEMM-bound work.

Source: VideoCardz — "Modders create automated tool to unlock AI performance on NVIDIA GeForce RTX 20, 30 and 40 series GPUs," WhyCry, Oct 31, 2026 — https://videocardz.com/newz/modders-create-automated-tool-to-unlock-ai-performance-on-nvidia-geforce-rtx-20-30-and-40-series-gpus
Source (secondary): Tom's Hardware — "Nvidia RTX 20, 30, and 40-series GPUs hacked to remove a half-speed AI throttle," Oct 26, 2026 — https://www.tomshardware.com/pc-components/gpus/nvidia-rtx-20-30-and-40-series-gpus-hacked-to-remove-a-half-speed-ai-throttle-firmware-exploit-doubles-rtx-4090s-peak-tensor-rate-lifts-rtx-3090-ti-matrix-math-up-to-80-percent

### Repair shop claims working 64 GB and 96 GB RTX 5090 mods — no proof yet
**Event date: Nov 1–2, 2026**

Greg Sky, a Grand Rapids, Michigan GPU rework technician who already sells 48 GB RTX 4090 upgrades, posted "64 and 96GB 5090 confirmed." TweakTown notes no photos, no benchmarks and no evidence the cards work under NVIDIA drivers. For comparison, 48 GB RTX 4090 mods have sold in China for around $3,400.

**Why it matters:** VRAM capacity is the binding constraint for local inference, and the earlier reporting on Chinese shops converting RTX 5090s into blower-style AI cards — with 32 GB retained and no verified 64/96 GB mod — made a credible rework shop the missing evidence. It has not yet appeared, so treat as unverified.

**Local inference:** if real and driver-compatible, 64–96 GB on a 1,792 GB/s GDDR7 card would put 70B–120B models at usable precision inside a single consumer board — the segment currently owned by 128 GB unified-memory boxes.

Source: TweakTown — "RTX 5090 with 64GB and 96GB VRAM 'confirmed' by US GPU repair tech," Nov 2, 2026 (claim posted Nov 1) — https://www.tweaktown.com/news/114444/rtx-5090-with-64gb-and-96gb-vram-confirmed-by-us-gpu-repair-tech/index.html
Source (secondary): eTeknix — "GeForce RTX 5090 With 64 GB and 96 GB of VRAM Reportedly Exists" — https://www.eteknix.com/geforce-rtx-5090-with-64-gb-and-96-gb-of-vram-reportedly-exists/

---

## Model releases

### Mistral releases Mistral Large 4 "Le Chonk" open weights: ~1T MoE, 1M context, cyber-first
**Event date: Oct 29, 2026**

Mistral published the weights of Mistral Large 4 under the Mistral Ecosystem Open-Weight License (an MIT derivative with model-as-a-service restrictions). ~1T total parameters, ~49–52B active, 1M-token context, text+image in / text out, shipped as FP8 and NVFP4 checkpoints with day-0 GGUFs from Unsloth and the llama.cpp team. Architecture: 72 layers, hidden 6,144, 64 heads, MLA combined with a DeepSeek-style sparse-attention indexer selecting up to 2,048 key positions, a 3:1 RoPE-to-NoPE pattern with cross-layer KV sharing, first layer dense and the other 71 layers MoE selecting 4 of 128 experts plus one always-on shared expert, LeRope (YaRN with a learned multiplier per frequency), and a 1.6B-parameter Pixtral vision encoder. Reported results include 82% reproducing-then-patching a real open-source vulnerability on the Artificial Analysis Cyber Index (several closed frontier models score near zero because they refuse), 93% on Cybench, 61.7% DeepSWE v1.1, 28.3% Terminal-Bench 4, 49.8% combined Coding Agent Index — ahead of DeepSeek V4 Pro 0813 and Qwen3.8 Max — and 93.3% attack resistance on Lakera B3. It ranked fifth on the Artificial Analysis open-weights intelligence index as of Oct 29.

**Why it matters:** a genuinely frontier-adjacent *European* open-weight model with unusually strong defensive-security behaviour changes the self-hosted threat-modelling conversation — defenders can run it on their own infrastructure mid-incident without an API refusing, and open weights let third parties measure refusal behaviour rather than trust a provider's claim. It also keeps the MLA+DSA and 128-expert MoE design pattern (same direction as GLM 5.3) as the ecosystem default.

**Local inference:** the honest read is "data-center or cluster" — ~1T parameters even at NVFP4 is far past a 16 GB RTX 5060 Ti or a single 32 GB card, and HF's own endpoint numbers (≈4,300 tok/s aggregate prefill on 16K prompts, ≈480 tok/s aggregate generation over 1,000 ShareGPT conversations) reflect multi-GPU serving on H200/B200/GB200/GB300-class hardware with Dell's recipe deploying on four B300 GPUs. The practical local path is the GGUF/NVFP4 quant ladder for multi-GPU or 128 GB-class boxes, or the smaller distilled spins it is about to spawn.

Source: Mistral AI — "Mistral Large 4 (Le Chonk) is now open weight," Oct 29, 2026 — https://mistral.ai/news/mistral-large-4-oss/
Source: Hugging Face blog — "Welcome Le Chonk: Mistral Large 4 on Hugging Face," merve, Oct 29, 2026 — https://huggingface.co/blog/mistral-large4
Source (weights): https://huggingface.co/mistralai/Mistral-Large-4-1T-A52B

### Liquid AI opens the LFM3 family: six 2B-class multimodal models on one shared backbone
**Event date: Oct 27, 2026**

Liquid AI released LFM3 — six open-weight models all branched from a single LFM3-1.7B-Base backbone (trained on 22T tokens), each ~2B parameters: text, LFM3-VL-2B (vision), LFM3-Audio-2B (18 languages), LFM3-Omni-2B, and two decision models, d1-2B (text+image) and d1-Omni-2B (also listens). Liquid positions the omni model as an on-device agent that uses visual and auditory context to guide users, take voice commands and call tools. Validated across 14 device/backend combinations: Galaxy S26 Ultra and OnePlus 15 phones, Apple M4 Max and AMD Ryzen AI laptops, Raspberry Pi 5, Arm and x86 cloud CPUs, GPUs and the Qualcomm Hexagon NPU, with ~1 GB of memory at 4-bit quantization and up to 256K text context. AMD, Qualcomm, Ambarella, Mercedes-Benz and MacPaw are named launch partners.

**Why it matters:** this is the "small, specialised, on-device" counter-thesis to trillion-parameter open weights — one backbone, six task-shaped endpoints, roughly 1 GB each — and it is the clearest signal yet that decision models plus omni input are how agent frameworks will actually run at the edge.

**Local inference:** the best hardware fit of any release in this window: ~1 GB at 4-bit runs on phones, a Raspberry Pi 5, or the NPU of any 2026 laptop, i.e. far below the 16 GB RTX 5060 Ti floor; llama.cpp, vLLM and MLX paths are all validated (llama.cpp landed NVFP4 LFM3 GGUF support on Nov 3, PR #31443).

Source: Liquid AI — "Introducing LFM3: Every Modality for Specialized Intelligence," Oct 27, 2026 — https://www.liquid.ai/blog/lfm3
Source: Hugging Face blog — "Build specialized multimodal models with LFM3," LiquidAI, Oct 27, 2026 — https://huggingface.co/blog/LiquidAI/lfm3
Source (weights): https://huggingface.co/LiquidAI/LFM3-1.7B

### DeepSeek plans to pre-train DeepSeek V5 on Huawei Ascend silicon
**Event date: Nov 3, 2026 (STAR Market Daily / 科创板日报 report, unconfirmed by either company)**

Multiple supply-chain sources told STAR Market Daily that DeepSeek's next-generation model, V5, is planned for pre-training on Huawei Ascend chips — a step beyond Ascend's current inference-only role at the lab. Related reporting puts the order at roughly 160,000 Ascend 950DT parts for a ~1 GW Inner Mongolia data center with deliveries ramping in Q4 2026, and separately reports Huawei's Ascend 960DT moving to 1Q27 availability, three quarters early. Huawei's rotating chairman David Wang was cited on the roadmap. Founder Liang Wenfeng's earlier estimate — four Ascend 950s ≈ one GB300 — is the cost trade-off being accepted for supply-chain independence.

**Why it matters:** if a frontier open-weight lab completes pre-training on domestic Chinese silicon, the "open models are CUDA-shaped" assumption breaks — every Western stack (llama.cpp, vLLM, SGLang, Unsloth) would need an Ascend path to touch the next DeepSeek release, and export-control assumptions get stress-tested. Treat as reported-not-confirmed: no company has commented.

**Local inference:** indirect but real — DeepSeek's Flash/1017 line is the most-quantised family on 16 GB consumer cards right now, and an Ascend-trained successor raises the odds that day-0 GGUFs arrive later or with lower-quality kernels.

Source: TrendForce — "[News] DeepSeek V5 Could Complete Training on Huawei Ascend 950 in 2027, Moving Beyond Inference," Nov 3, 2026 — https://www.trendforce.com/news/2026-11-03/news-deepseek-v5-could-complete-training-on-huawei-ascend-950-in-2027-moving-beyond-inference/
Source (secondary): ChainCatcher — "DeepSeek V5 plans to train based on Huawei Ascend chips," Nov 3, 2026 — https://www.chaincatcher.com/en/article/2301282

### MIT CSAIL: an LLM can read and rewrite another network's weights — no gradients
**Event date: Nov 2, 2026**

Rikiya Takehi, Jayden Teoh, Alex Zhang and Omar Khattab (MIT CSAIL) published "Sparks of Weight-Space Programming: LLMs can natively read & write neural network weights." GPT-6 Astra was fed raw weight tensors in context and edited them from natural-language goals: it changed ~0.016% of Qwen3.5-0.8B's weights to remove a reasoning-loop failure direction, lifting MATH-500 from 7.9% to 48.1% with no gradient descent, and the authors released the edit diff so it can be rebuilt exactly and run locally.

**Why it matters:** a frontier model that can diagnose and patch a small open model's weights is the beginning of a new optimisation and interpretability layer — and because the artifact is a diff rather than a training run, it distributes like a quant file.

**Local inference:** r/LocalLLaMA reproduced it within a day on local models: one user had Claude rewrite 8 neurons of Gemma 4 12B to change how it translates an Italian tense; another showed DeepSeek V4.1 Flash 1017 doing the same weight-reading task on four anonymised 51k-parameter RL checkpoints. All runnable on 16 GB-class hardware.

Source: MIT CSAIL — "Sparks of Weight-Space Programming: LLMs can natively read & write neural network weights," Nov 2, 2026 — https://rikiyat.github.io/ (project link)
Source (secondary): DataCamp — "Weight-Space Programming: How LLMs Edit Network Weights," Nov 2026 — https://www.datacamp.com/blog/weight-space-programming
Source (community replication, Nov 3): r/LocalLLaMA — https://www.reddit.com/r/LocalLLaMA/comments/1xn1gs9/i_tried_mits_weightspace_programming_on_my_own/

### NVIDIA ships Nemotron 3.5 Transcribe 30B-A4B — promptable multilingual ASR
**Event date: Oct 29, 2026 (repo updated; NVIDIA blog Oct 28, llama.cpp day-0 support Nov 3)**

A 33.6B-parameter (A4B active) NeMo SpeechLM fine-tuned from Nemotron-3.5-Lightning-30B-A3B-BF16 for instruction-following transcription: speech-to-text, speech translation, speaker diarisation, timestamps, language identification, code-switching and audio QA across ~40 languages, trained against the Granary v2 dataset. It reached the HF ASR leaderboard top tier (741 likes, ~39k downloads in its first weeks), and Gerganov merged mtmd support into llama.cpp on Nov 3 (PR #31471).

**Why it matters:** "promptable" transcription — steer it with instructions for timestamps, speaker labels, domain vocabulary — replaces a pipeline of separate ASR/diarisation/alignment models with one checkpoint, and NVIDIA ships it under a permissive-style licence with a vLLM serving path.

**Local inference:** 30B total / 4B active means MoE-style offload makes it feasible on a 16–24 GB card at 4-bit; for a laptop or Jetson the smaller Granite/whisper-class options stay lighter.

Source: Hugging Face — nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B (lastModified Oct 29, 2026) — https://huggingface.co/nvidia/NVIDIA-Nemotron-3.5-Transcribe-30B-A4B
Source: Hugging Face blog — "NVIDIA Nemotron 3.5 Transcribe and Granary v2," nvidia, Oct 28, 2026 — https://huggingface.co/blog

---

## Community spins and quantizations

### Basalt 0.2.0: Qwen 3.8 Flash Next NVFP4 at ~300 tok/s prose on one 5090 plus a 5060 Ti
**Event date: Nov 3, 2026** *(update on the previously reported Basalt engine — see Coverage notes)*

jesdga95's Blackwell-only engine for Qwen 3.8 Flash Next shipped 0.2.0 with quality measured against a BF16 reference (Unsloth's BF16 GGUF under llama.cpp, reference PPL 3.2749), scored with `basalt-bench --quality`. KLD / same-top-1: Basalt Q8 0.0200 / 95.34%, new NVFP4-DQ8 0.0561 / 92.25%, NVFP4-DQ6_K 0.0624 / 91.65%, GSQ-RCO IQ3_S 0.1497 / 87.56% — versus llama.cpp Q8 at 0.0325 / 93.98%. Speed on the standing rig (RTX 5090 at 400 W + RTX 5060 Ti 16 GB at 180 W + 128 GB DDR5, 262K context, 4 parallel slots, dynamic vision, no lossy flags): structured 603.7 / 594.2, conversation 363.8 / 321.9, prose 295.0 / 299.9 tok/s; prefill 7,432 → 8,159 (16K) and 10,221 → 10,229 (64K) tok/s. Week-over-week that is prose 277 → ~300 and structured 558 → ~600, from a faster draft step, faster output head and better startup expert placement. The `.basalt` format is version 2 (same weights, faster start) with a migration tool, all lossy "hacks" default off with warnings, and a new `Qwen3.8-FN-NVFP4-DQ8` build on HF beside the default `-DQ6_K`.

**Why it matters:** the interesting part isn't the headline number, it's the discipline — every quant tier is published against BF16 with KLD and top-1 agreement, which is the standard the community keeps asking for after the hallucination-at-low-bpw controversies.

**Local inference:** directly on point for this reader's hardware class: the 16 GB RTX 5060 Ti is a *first-class participant* as an expert-compute device alongside a 400 W 5090, hitting ~300 tok/s prose at 262K context with vision enabled. Blackwell-only, so no Ampere/Ada path.

Source: r/LocalLLaMA — "Basalt 0.2.0: Qwen 3.8 Flash Next NVFP4, 300 t/s prose, 600 t/s struct, 10K t/s prefill on a 5090 + 5060 Ti and 128GB RAM," u/jesdga95, Nov 3, 2026 — https://www.reddit.com/r/LocalLLaMA/comments/1xn4qn1/basalt_020_qwen_38_flash_next_nvfp4_300_ts_prose/

### ISTA-DASLab prunes half the experts out of DeepSeek-V4.1-Flash-1017 for a coder-only build
**Event date: Nov 2, 2026**

ISTA-DASLab published DeepSeek-V4.1-Flash-1017-GSQ-RCO-Coder-GGUF: a capability-targeted compression in which half of the routed experts are *removed* rather than quantised, and the survivors keep the per-tensor quantization of the IQ2_XXS build (2.00 bpw) — roughly 1.05 bpw effective against the original expert pool, shipped as a five-shard backbone (~110.6 GB total payload) plus an IQ4_NL Engram file, a kept-experts JSON manifest and a BF16 mmproj for vision. Reported retention against the base model: 94.8% of SWE-bench Verified and 98.7% of LiveCodeBench v6. It combines GSQ scalar quantization with RCO (expert pruning) under per-layer budgets, from the same group behind widely-adopted GPTQ work, and it hit HF's 7-day trending list (126 likes, ~29k downloads within a day).

**Why it matters:** expert pruning at this aggressiveness — half the experts gone — is the most interesting quantisation direction this quarter, because it attacks the real cost of MoE (resident expert bytes) instead of just the bits. Note the ecosystem wrinkle: the sibling GSQ-RCO build ships in Strata's layout, and community repack tools exist to rewrite it for upstream llama.cpp.

**Local inference:** this is the class of quant that makes a 763B-class MoE tractable off-server. At ~110 GB it targets 128 GB-class unified memory (DGX Spark, Ryzen AI Max) or the 16 GB GPU + 128 GB DDR5 hybrid rigs that Strata/KTransformers drive — ISTA's own guidance and third-party recipes put the family at one 16 GB GPU plus one CPU socket with SGLang/KTransformers.

Source: Hugging Face — ISTA-DASLab/DeepSeek-V4.1-Flash-1017-GSQ-RCO-Coder-GGUF (created Nov 2, 2026 13:52 UTC) — https://huggingface.co/ISTA-DASLab/DeepSeek-V4.1-Flash-1017-GSQ-RCO-Coder-GGUF
Source (secondary): AlphaSignal — "ISTA Squeezes a 763B DeepSeek-V4.1 Coder Into One GPU," Nov 2026 — https://alphasignal.ai/news/ista-squeezes-a-763b-deepseek-v4-1-coder-into-one-gpu

### Clover-35B-A3B-Preview: a diffusion-LM MoE spin of Qwen3.6-35B-A3B, with day-0 llama.cpp and GGUF support
**Event date: Oct 24, 2026 (weights created; HF blog Oct 29; llama.cpp support merged Nov 2, bartowski quants Nov 3)**

Diffuspace's Clover-35B-A3B-Preview is an Apache-2.0 34.7B-parameter `clover_moe` model derived from Qwen/Qwen3.6-35B-A3B with a masked-token diffusion sampling path (`<|mask|>` token, custom `modeling_clover_moe.py` / `sampling_clover_moe.py`, 256 experts with 8 per token) and a Qwen-derived chat template with reasoning-effort handling. The HF community post ran Oct 29. Gerganov merged "feat: add Clover support" (PR #31306) on Nov 2 and bartowski published imatrix GGUFs on Nov 3.

**Why it matters:** diffusion-style text generation is the main non-autoregressive bet with real open-weight traction right now, and having upstream llama.cpp support plus imatrix quants within days is what separates a demo repo from something the community can actually run.

**Local inference:** 35B total / ~3B active in the Qwen3.6-35B-A3B shape is the known-good 16 GB-card target (Q4-class fits a single RTX 5060 Ti); diffusion decoding changes the speed profile, so early GGUF benchmarks are the thing to watch.

Source: Hugging Face — Diffuspace/Clover-35B-A3B-Preview (created Oct 24, 2026) — https://huggingface.co/Diffuspace/Clover-35B-A3B-Preview
Source: r/LocalLLaMA — "Clover-35B-A3B-Preview: A New Way for Intelligence," Oct 29, 2026 — https://huggingface.co/blog (community)
Source (quants): bartowski/Diffuspace_Clover-35B-A3B-Preview-GGUF, Nov 3, 2026 — https://huggingface.co/bartowski/Diffuspace_Clover-35B-A3B-Preview-GGUF

---

## Inference software and benchmarks

### SGLang v0.5.23: Paged Experts, fused megakernels, and a Google TPU path
**Event date: Nov 2, 2026 (release published 22:14 UTC; 1,007 PRs from 257 contributors)**

Headline features: **Paged Experts** (opt-in) runs MoE models *larger than GPU memory* on a single consumer GPU, up to **30× faster than CPU offload** (#42557, #45787); an opt-in low-latency mode where NVIDIA megakernel and AMD monokernel plugins fuse DeepSeek-V4.1-Flash decode into persistent kernels, for up to **44% faster single-user decode on GB300 and 75% on MI350X** (#44945, #45450, #44353, #44621); DeepSeek-V4.1 on GB300 gets up to **1.5× more prefill throughput** and ~28% lower first-token latency on 32K–64K prompts; MoE models (DeepSeek-V4.1-Flash, GLM-5.3-Flash, gpt-oss) reach the first token **30–58% sooner** on 8K prompts on H200; LoRA adapters can now be served on quantized MoE (FP8 and NVFP4) via a new opt-in engine (#45892); and SGLang now runs on Google TPU7x through a PyTorch-native TorchTPU plugin. New model cookbooks: Muse Spark Open, Beam-1.0, MiniMax-M3.1-Flash, MiniCPM-V 4.7, Kev, FastH3 Omni, FLUX.3 Image Klein, MOSS-Image. CUDA-13 and MI35x/MI30x/Xeon/XPU/TPU images all published.

**Why it matters:** Paged Experts is the answer to the exact problem the quant scene keeps hitting — MoE expert bytes that don't fit — done in the scheduler rather than the quantiser, at 30× CPU offload; and the megakernel results are the first large published single-user decode numbers on Blackwell-Ultra/MI350X-class parts.

**Local inference:** the single most consumer-relevant item in this window — "MoE larger than VRAM on one consumer GPU, 30× over CPU offload" is precisely the 16 GB-card use case (DeepSeek-V4.1-Flash and GLM-5.3-Flash at 4-bit with expert paging).

Source: GitHub — sgl-project/sglang v0.5.23 release notes, Nov 2, 2026 — https://github.com/sgl-project/sglang/releases/tag/v0.5.23

### vLLM v0.33.0: CUDA 12.9 dropped, ROCm 10.0 default, and a big spec-decode jump
**Event date: Nov 2, 2026 (release published 10:10 UTC; 927 commits, 389 contributors)**

Platform changes first: wheels/images are **CUDA 13.0 only** (the CUDA 12.9 artifacts are gone), PyTorch moves to 2.14.0 with Triton 3.8.0, the CUDA wheel is now libtorch-ABI-stable, the default ROCm image is **ROCm 10.0** (7.2 still published as `-rocm72`), and there's a new AWS-EFA image plus a shorter `vllm/vllm` image alias. Speculative decoding: the new **XPress** refiner for block-diffusion drafters (DFlash/DSpark); DFlash/DSpark no longer reserve draft slots in the token budget, taking **DeepSeek-V4.1-Flash + DSpark on GB300 from 1,373 → 3,390 output tok/s**; quantized draft `lm_head` (+18% decode at concurrency 1). NVIDIA decode: opt-in latency-specialised GB300 paths for Kimi-K3 (1.5–2× at TP8/TP16) and DeepSeek-V4.1-Flash (+18.3% at batch 1), FA4 sparse-MLA decode on Blackwell, FlashInfer DCP prefill (39–48% lower mean TTFT at 100k input on 8×B200), decode-only full CUDA graphs under MRV2 prefill CP (2.6× over eager). AMD: MonoKernel decode (2.32× P50 interactivity at concurrency 1 on MI300X/MI325X), a **ROCM_OCTAVE 3–4 bit KV cache giving 4.6× the KV tokens of fp16 on an RX 7900 XTX**, and gfx1250 enablement. New models include Reflection Beam-1.0 with its DFlash2 drafter, Recursive Flint, Muse Spark Open, MiniCPM-OCR 1.0 and NVIDIA Alpamayo 2 Super. Breaking: Medusa and the MLP speculator removed, legacy Ray compiled-graph executor gone, NIXL now needs 1.5+.

**Why it matters:** the 2.5× spec-decode jump from *not reserving draft slots* is a free win that applies to every DSpark/DFlash user, and the 3–4 bit KV cache on Radeon desktop silicon is the first serious long-context memory lever for AMD gamers-turned-homelabbers. Dropping CUDA 12.9 forces a driver-branch decision for anyone pinned to older data-center drivers.

**Local inference:** the OCTAVE 3–4 bit KV cache is a 4.6× context win on a 24 GB-class Radeon card; the GB300/H200 numbers are cloud-side, but the DSpark and quantized-lm_head techniques port to consumer Blackwell via the same code paths.

Source: GitHub — vllm-project/vllm v0.33.0 release notes (tag v0.33.0rc3), Nov 2, 2026 — https://github.com/vllm-project/vllm/releases/tag/v0.33.0rc3

### llama.cpp: Gemma 4 graph optimisation, multi-node tensor parallelism, and a wave of new architectures
**Event date: Oct 24 – Nov 3, 2026 (b11840 → b12094)**

The window's highest-impact merges: **gemma4 graph optimisations** from Google's max-krasnyansky (PR #31356, merged Nov 3) — "massive gains on pp for E2B and E4B"; **NCCL multi-node tensor parallelism** (#28967, Nov 3) and a **Vulkan tensor-parallel allreduce** (#25051, Nov 2), which together put real multi-GPU/multi-node paths into the consumer-facing engine; **SYCL `ggml_sycl_comm_exchange()` fast path worth +19% prompt-eval on Intel Arc Pro B70s** (#30956, Nov 2) plus a **DPAS flash-attention kernel for quantized KV** on SYCL (#31125, Oct 29); OpenCL gains initial **iq3 and iq4_xs** support (#31338/#31339); CUDA now gathers batched tensor reads into mapped host memory (#31391) — directly relevant to MoE offload; and the `cuda: prevent premature eviction of graphs about to be used` fix (#30662). New model support landed for Metis-1.6 (#31134), **NVFP4 LFM3 GGUFs** (#31443), **Clover** (#31306), **Nemotron 3.5 Transcribe** in mtmd (#31471), chatterbox TTS (#30993), qwen3-vl video prompt format (#31619), GLM-5.3 next SM-tensor metadata (#30499), plus Hexagon NPU work (DSV4_HC_PRE/POST, TOP_K_MASK, tiled HVX permute, DMA stride fix) and WebGPU/OpenCL sampler ops.

**Why it matters:** Gemma 4 E2B/E4B are the default on-device agent models and a "massive prefill" uplift there lands on every laptop; NCCL + Vulkan TP are the two missing pieces for clustering consumer GPUs, which is where the homelab is pushing as single-card capacity stalls.

**Local inference:** the offload and tensor-parallel merges are the practical story for 16 GB cards — mapped-host-memory batched reads and TP allreduce over Vulkan mean heterogeneous rigs (5090 + 5060 Ti, or AMD + Intel Arc) get faster rather than just bigger.

Source: GitHub — ggml-org/llama.cpp releases and merged PRs, Oct 24 – Nov 3, 2026 — https://github.com/ggml-org/llama.cpp/releases

### Ollama v0.40.11 → v0.40.14 and TensorRT-LLM v1.4.0rc2
**Event dates: Oct 24, 27, 28 (Ollama), Oct 28 (TensorRT-LLM), Nov 3 (Ollama v0.40.14)**

Ollama shipped four patches in the window. The most substantive is **v0.40.14** (Nov 3, 01:40 UTC): OpenAI-compatible endpoints no longer override a model's packaged sampler defaults — clients that omit temperature/top-p now get the model's own values instead of 1.0, matching `/api/chat`; `ollama launch claude` applies the model's output-token limit so cloud models aren't capped at 128k; cloud web-search responses on `/api/chat` decode correctly (including compressed); `/v1/responses` preserves readable input content including prior assistant refusals; ChatGPT routing is verified before `ollama launch codex` configures; and top-k sampling got faster on MLX for large vocabularies. NVIDIA published **TensorRT-LLM v1.4.0rc2** on Oct 28, the second release candidate of the 1.4 line.

**Why it matters:** the sampler-default fix is a correctness bug that silently degrades any agent harness that omits sampling parameters — the class of defect that produces "the local model is worse than the API" complaints. TensorRT-LLM's rc cadence is the leading indicator for the next NVIDIA serving line.

**Local inference:** Ollama is the default entry point for non-experts on 16 GB cards, and the MLX top-k path matters for Apple Silicon; TensorRT-LLM remains the highest-performant but least-flexible NVIDIA serving option.

Source: GitHub — ollama/ollama v0.40.14 release notes, Nov 3, 2026 — https://github.com/ollama/ollama/releases/tag/v0.40.14
Source: GitHub — NVIDIA/TensorRT-LLM v1.4.0rc2, Oct 28, 2026 — https://github.com/NVIDIA/TensorRT-LLM/releases

### Gartner: open-weight models can do ~90% of agentic work at ~1/20 the cost of a frontier LLM
**Event date: Nov 3, 2026**

Gartner published an analysis arguing that open-weight models already handle roughly nine of ten routine steps in enterprise agentic workflows at a quarter to a twentieth of frontier-LLM cost, with "models are commodities, harnesses are the valuable part" as the framing; frontier models are recast as a capital-expense R&D engine that produces the open models, which then become operational expense. The Register's write-up ran alongside a Revolut case study claiming a 10× coding-cost reduction by replacing Claude with a self-hosted open model.

**Why it matters:** this is the analyst-firm endorsement of the self-hosting argument that the local-inference community has been making with benchmark screenshots — and it shifts the purchasing conversation from model quality to harness quality, which is where tools like agent frameworks and serving stacks now compete.

**Local inference:** the economic case for buying a 32 GB card or a 128 GB unified-memory box is now being made by a mainstream analyst firm; note the caveat in the analysis itself — today's open models may need more retries, so the 20× saving is task-mix dependent.

Source: The Register — "Open models can do 90% of agentic work at a twentieth of the cost of a frontier LLM: Gartner," Simon Sharwood, Nov 3, 2026 06:19 UTC — https://www.theregister.com/ai-and-ml/2026-11-03/open-models-can-do-90-of-agentic-work-at-a-twentieth-of-the-cost-of-a-frontier-llm-gartner/5307132

---

## Also noted

- **DeepSeek V4.1-Flash-1017 stays the ecosystem's quant magnet** — Unsloth's GGUF repo (748B params, MIT, imatrix) was refreshed Nov 2, three weeks after the base weights landed. https://huggingface.co/unsloth/DeepSeek-V4.1-Flash-1017-GGUF
- **Strata keeps winning converts:** a user moving Qwen 3.8 Flash Next from plain llama-server (70–80 tok/s decode) to Strata reported ~150 tok/s decode — an update on previously reported Strata benchmarks. https://www.reddit.com/r/LocalLLaMA/comments/1xmt6kb/strata_is_the_shit/
- **Intel Q3 2026: data centre soars while the client business feels memory pressure** (ServeTheHome, Oct 30) — the PC-side memory squeeze is now visible in Intel's own numbers. https://www.servethehome.com/intel-q3-2026-earnings-data-center-foundry-client/
- **Qwen re-commits to open weights:** after two days of rumours that Qwen4 would be the last open-weight series, Qwen lead Liu Dayi Heng's "Official Statement: Qwen Models Will Stick to Open Source to the End" circulated on r/LocalLLaMA. Separately, a merged PR in `QwenLM/qwen-code` surfaced ids for `qwen4`, `qwen4-max`, `qwen4-plus`, `qwen4.5-max`, `qwen4-coder-plus` and `qwen4-omni-120b-think` at 262,144 context — but commenters note these may be parser test fixtures, so unconfirmed. https://www.reddit.com/r/LocalLLaMA/comments/1xm28e6/official_statement_qwen_models_will_stick_to_open/
- **balbal-v2**: Turkish-first CPU ASR — 313M `manas` (Turkish+English, ~123× real time on 8 threads) and 45M `balbal-v2` (~236× real time), both CTC+KenLM, each trained for under $250; second on Mu-Bench TR behind Google Chirp 3. https://www.reddit.com/r/LocalLLaMA/comments/1xmtlm7/balbalv2_turkish_speech_recognition_that_runs_on/
- **NVIDIA's local-AI optimisation push keeps compounding**: XQA/FlashInfer attention kernels plus llama.cpp and vLLM collaboration work are the mechanism behind NVIDIA's claimed up-to-1.9× uplift on 24 GB+ RTX/DGX cards (originally IFA, still rolling out through LM Studio and Ollama). https://wccftech.com/nvidia-local-ai-simple-optimizations-llama-vllm-up-to-1-9x-faster-rtx-dgx-platforms/
- **Library of Layers (arXiv 2611.03526)**: adaptively orders transformer layers at runtime — a scheduling-level throughput idea worth watching for consumer rigs. https://www.reddit.com/r/LocalLLaMA/comments/1xmzo2s/261103526_library_of_layers_lol_adaptively/

## Just missed the window

- **NVIDIA / Microsoft "RTX Spark" Windows PC agent stack** (personal-AI agent announcement plus RTX Spark creative-workflow acceleration) — coverage surfaced in this window, but the underlying launch predates Oct 23 17:45; treated as background, not a new item. https://nvidianews.nvidia.com/news/nvidia-microsoft-windows-pcs-agents-rtx-spark
- **NVIDIA RTX 5090 / GB202 order freeze** — the substantive reporting (board partners can no longer order GB202 kits with 32 GB GDDR7, even as backorders) ran **Oct 26, 2026**, inside the window, but it continues a discontinuation story already disclosed on Oct 16; listed here as context rather than a new event, per the event-date rule. https://videocardz.com/newz/nvidia-reportedly-not-accepting-rtx-5090-orders-from-board-partners-even-backorders-are-unavailable
- **"World's biggest PCIe graphics card"** — a quad-RX 470 prototype PCB (DYII-XQ570 V1.0, 4 × 8 GB GDDR5, PCIe x8, four 8-pin connectors) pictured Nov 3. Hardware-collectable, no AI angle; footnoted only because r/LocalLLaMA ran it as a tokens/sec joke. https://videocardz.com/newz/worlds-biggest-pcie-graphics-card-discovered-prototype-amd-radeon-rx-470-board-with-four-gpus-has-been-pictured

## Coverage notes

- **Verified: 21 items** (counting unit: one entry per underlying event — 14 full items with lead treatment, 4 short-form "also noted", 3 footnotes excluded from the qualifying count), each from a fetched page whose own timestamp places it in the window, with a second independent source cited wherever other outlets carried the event. Lead stories read in full: NVIDIA Rubin spec revision, Qualcomm HBC, R9700 review, AI Box-A395 review, Maia 300, SK hynix Q3, Mistral Large 4, LFM3, Basalt 0.2.0, SGLang v0.5.23, vLLM v0.33.0.
- **Ledger dedupe: 12 candidates suppressed as already reported** — Kimi K3.1 Pro (reported Oct 23; weights still not published at fire time, so no re-list), MiniMax M3.1-Flash, Strata benchmarks (x2, one folded in as an explicit update), gpu-unlock (the Oct 31 nv-uefi-unlocker is listed as an *update* naming the earlier entry, not as a new event), Basalt (0.2.0 listed as "update on previously reported"), SGLang v0.5.22 (already carried a `missed:` tag — not footnoted again), Nemotron-3.5-Bolt, Muse Spark Open, MiniCPM-OCR, DGX Spark 64 GB / Sync-Cluster pricing (Oct 23 pre-window), and the RTX 5090 AI-conversion factory story. No item from this report duplicates a ledger line as a *new* event.
- **Dead ends / limitations:** The Register's AI/ML index is dominated by industry and policy stories with no hardware tie (Anthropic IPO filings, data-centre planning, AI coding-cost pieces) — those were excluded by scope, not by retrieval failure. r/LocalLLaMA access is anonymous-feed throttled (no scores, no nesting), so community items were taken from titles plus targeted thread reads. Two "confirmed"-framed claims are explicitly flagged unverified: Greg Sky's 64/96 GB RTX 5090 mods (no photos or benchmarks) and the `qwen4-*` model ids (plausibly parser fixtures). The Basalt figures are author-reported by the engine's own developer, and the earlier FN-NVFP4 shootout was likewise Basalt-dev-authored — treat as self-measured. STH's AI Box-A395 review is a multi-page piece; the token-throughput pages were not retrieved, so only as-published specifications are cited here.
- **Phase discipline:** discovery and verification completed ~40 minutes into the run; no candidate was chased past two failed retrievals.
