# AI Hardware & Local Models Brief — Tuesday, November 24, 2026 (17:45 edition)

Window: 2026-11-24 12:45 → 17:45 EST. New items only, deduped against the reported ledger.

## TL;DR
- Dell posts record AI quarter: $132.3B AI-server backlog, $60.2B booked, AI-optimized server revenue $22.0B (+291% YoY).
- llama.cpp merges Inkling 2 DSpark speculative-decoding support — day-one drafting for Thinking Machines' model on consumer rigs.
- vLLM ships Hy4/Hy4-Flash ROCm enablement on MI355X, ReplaySSM Gated-DeltaNet decode kernel, and an integrated-GPU MoE batch path.
- SGLang adds an opt-in EGM-backed Mooncake KV pool letting NVL72 GPUs map other trays' host DRAM over NVLink.
- Tencent Hy-MT3 translation family (1.8B/7B/30B-A3B, 102 languages, Apache-2.0) lands day-0 llama.cpp quants — the 13.1GB 30B-A3B Q4_K_M fits a 16GB card.

## AI hardware and silicon

**Dell's record AI quarter pegs the rack-scale bar** — event date: November 24, 2026, 4:05 PM EST (in window)
Dell's fiscal 2027 Q3 results: record $54.9B revenue (+103% YoY), Infrastructure Solutions Group at $42.0B (+198%), record AI-Optimized Server revenue of $22.0B (+291%), and a record $132.3B AI backlog after booking $60.2B of orders. COO Jeff Clarke stressed "rigorously vetted" customers and a factory model: racks tested before shipping and stood up within six hours of delivery to shorten time-to-first-token; 7,500+ customers now run "Dell AI Factories." FY27 revenue guidance was raised to $202.0B (+78%).
Why it matters: this is the cleanest in-window datapoint that agentic-inference buildout is still demand-constrained, not supply-happy — and the backlog quality filter (capital, land, power) is how the big OEMs now ration GPUs. It also anchors the memory-price story from recent editions: every rack sold locks up LPDDR/HBM/GDDR allocation that eventually reprices desktop boxes like DGX Spark and RTX Spark machines.
Local inference: no direct desktop SKU here — implication is price/availability pressure on memory and a rising trickle-down of rack features (tested-rack "time-to-first-token" framing echoes DGX Station-class turnkey setups).
Source: Dell Technologies IR press release, "Dell Technologies Delivers Third Quarter Fiscal 2027 Results," November 24, 2026, 4:05 PM EST — https://investors.delltechnologies.com/news-events/press-releases/detail/1665/dell-technologies-delivers-third-quarter-fiscal-2027-results (also surfaced on Hacker News at 22:21 UTC)

## Inference software and benchmarks

**llama.cpp merges Inkling 2 DSpark speculator support** — event date: November 24, 2026, merged 15:11 EST
PR #32218 ("feat: Inkling 2 DSpark support") merged at 20:11:48 UTC with 30 comments, adding llama.cpp support for the DSpark speculative decoder for Thinking Machines' Inkling 2 family, building on the inkling2_loop base-model support merged November 21. No performance numbers shipped in the PR itself — the drafter wiring is the news; expect llama-bench follow-ups from the community.
Why it matters: Inkling 2 Mini has been the community's newest favorite for 16–24GB cards, and DSpark drafting is the same mechanism that delivered the 20–50% decode gains seen on DeepSeek and Qwen speculators — day-one parity for a brand-new architecture in llama.cpp is exactly the local-ecosystem speed the ledger keeps tracking.
Local inference: Inkling 2 Mini quants run 7.8–23GB (Unsloth UD-IQ1_S→Q8_K_XL; Q4_K_M ≈ 17.5GB on DGX Spark/GB10); the DSpark drafter lands in tonight's llama.cpp builds (b12676→b12686 line; b12686 published 17:45:15 EST, seconds past the window wall).
Source: ggml-org/llama.cpp PR #32218, merged November 24, 2026 20:11:48 UTC — https://github.com/ggml-org/llama.cpp/pull/32218
Same-window llama.cpp merges (minor wave): SpacemiT flash-attention null-mask/ALiBi/softcap fixes #32182 (13:47 EST), CUDA MMVF matrix-offset int-overflow fix #32965, llguidance header vendoring #32928, CI jobs moved to GB10 runners #32968 — https://github.com/ggml-org/llama.cpp

**vLLM afternoon wave: Hy4 goes ROCm, ReplaySSM lands, iGPUs get a MoE batch path** — event dates: November 24, 2026, 12:57–17:32 EST (21 PRs)
Headliners: #68839 (merged 13:03 EST) enables Tencent Hy4 and Hy4-Flash FP8 on 8x MI355X with TP8, CUDA-graph-free capture and DFly 5-token speculative decoding, claiming MMMU-Pro 80.5 / 77.0 (matching or beating Tencent's own numbers) and routing sparse-MLA through AITER's Triton kernel until the optimized AITER path merges; #62004 (merged 17:18 EST) lands ReplaySSM for Gated DeltaNet standard decode — the Dao AI Lab × NVIDIA trick that keeps a small ring buffer of recent SSM inputs (k, corrected value d, gate g) plus a periodic checkpoint instead of writing the recurrent state to HBM every step; #64163 switches integrated GPUs to the sorted fused-MoE path for multi-token batches. Minor: LM Format Enforcer backend removed (#69564), Transformers pinned to 5.24.1 (#69129), Rust frontend now fails fast on unserverable multimodal models (#68912), HiSparse reuses finished requests' GPU copies (#68820), GLM-4.7 tool-call parsing goes linear-time (#69637).
Why it matters: ReplaySSM attacks the exact memory-bound bottleneck that makes hybrid linear-attention models (Qwen3.8-Flash-Next class, LFM3) decode-bound on every VRAM class, and Hy4-on-ROCm means the new Tencent flagship isn't an NVIDIA-only story for local stacks.
Local inference: the ROCm recipe is datacenter AMD (8× MI355X), but DFly spec-decode and the AITER sparse-MLA path are the same kernels community maintainers port to Strix Halo/R9700 rigs; #64163 directly benefits 16GB-class iGPU/AIO machines running MoE batch workloads.
Sources: https://github.com/vllm-project/vllm/pull/68839 (merged 18:03:29 UTC); https://github.com/vllm-project/vllm/pull/62004 (merged 22:18:59 UTC); https://github.com/vllm-project/vllm/pull/64163 (merged 21:26:19 UTC); wave list https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged

**SGLang wires EGM-backed Mooncake KV pooling over NVLink** — event date: November 24, 2026, merged 17:40 EST (five minutes before the wall)
PR #51416 adds opt-in `enable_egm_store_pool` / `egm_numa_nodes` config so Mooncake 0.3.14 can back a Store segment with Extended GPU Memory: on GB200/GB300 NVL72 racks, a GPU maps another tray's host DRAM through `cuMemCreate` fabric handles, so HiCache L3 KV pages are copied over NVLink instead of the NIC. No data-path changes when disabled.
Why it matters: host-DRAM-as-KV-tier is the trick that keeps long-context serving cheap; doing it across trays over NVLink (rather than RDMA NICs) is the next step in NVL72-class disaggregation, and the same EGM pattern underlies the coherent-memory pitch of DGX Station/GB10 boxes.
Local inference: server-rack scope (NVL72), but it validates the "borrow the neighbor's RAM" pattern that engine authors keep porting downward to Spark/Strix Halo rigs.
Source: https://github.com/sgl-project/sglang/pull/51416 (merged 22:40:29 UTC). Same-window SGLang minor wave: #51631 AMD gfx1250 SPX TP2/TP4 all-reduce tables (17:13 EST), #47578 kv-shard batched PageInterleave pops, eplb packing vectorization #51345/#51346 — https://github.com/sgl-project/sglang

**TensorRT-LLM makes key-sharded DSA Top-K merge the Kimi K3.1 default under Helix** — event date: November 24, 2026, merged 14:25 EST
PR #22858 flips the `key_shard` DSA Top-K merge (per-rank local Top-512, then pack/merge_owned kernels) to default for Kimi K3.1 disaggregated generation servers at any batch size, replacing an NCCL all-gather of every rank's full local Top-K that was, per NVIDIA, "the largest part of the gap between a Helix decode step and a TEP8 decode step" on GB300. Also merged in-window: #22795 reports the profiled KV-cache sizing budget (follow-on to the global memory monitor), #22057 vocab-parallel temperature-only sampling for one-model spec workers, #22657 kills a per-step host sync in VisualGen FLUX serving, #22463 trims Gemma3 image-prefill peak memory with compact masks, #22859 registers MiniMax-H3 FP8 U4 tests in CI.
Why it matters: K3.1-Pro is the strongest open-weight coding flagship of the month, and closing the Helix context-parallel decode gap is what makes 1M-context serving economically sane on disaggregated Blackwell.
Local inference: GB300/Helix-class scope; the DSA Top-K merge trick follows the same indexer-fusion lineage the community already ports to consumer rigs (see the ledger's QSA/DSA waves).
Source: https://github.com/NVIDIA/TensorRT-LLM/pull/22858 (merged 19:25:58 UTC) and https://github.com/NVIDIA/TensorRT-LLM/pulls?q=is%3Apr+is%3Amerged

## Model releases
No new open-weight model disclosure verified inside this five-hour window. The day's release — Tencent Hy-MT3 — landed pre-window (see footnotes) with its community quant wave in-window below.

## Community spins and quantizations

**Day-0 Hy-MT3 quants: a 13.1GB Apache translation MoE with 262K context on a 16GB card** — event dates: November 24, 2026, 16:25–16:45 EST
mradermacher's Hy-MT3-1.8B-GGUF was created 21:25:36 UTC, joining Tencent's own release-time llama.cpp tier (Q8_0/Q4_K_M/IQ2 files; the 30B-A3B Q4_K_M set totals ~13.1GB with 262,144-token context; the 1.8B GGUFs total ~1.35GB). Hy-MT3 is the third-generation Hunyuan translation family — 1.8B, 7B, and 30B-A3B MoE (128 experts, 8 active), Apache-2.0, language coverage expanded from 38 to 102 languages. Tencent's claims, from the model card and arXiv 2611.27673: versus Hy-MT2, mistranslated samples −43%, omissions/additions −74%, instruction-following errors −40%; the 30B-A3B leads all evaluated non-thinking models including Qwen3.8-Max, Kimi-K3.1-Pro and DeepSeek-V4.1-Flash while beating models consuming 30× its inference compute; game-localization and OCR-cascaded image translation are the industrial showcases, plus an open-sourced rubric evaluator, TransRubric.
Why it matters: translation is the rare task where a 3B-active MoE on one consumer GPU beats frontier closed models — a textbook example of the Gartner-style "agentic work at frontier fraction cost" thesis the ledger has been tracking, with official GGUFs day one.
Local inference: 1.8B Q8 ≈ 1.4GB (any 4GB card, NPU boxes); 30B-A3B Q4_K_M ≈ 13.1GB fits the RTX 5060 Ti 16GB and DGX Spark classes with room for 262K context; imatrix included in Tencent's GGUF repo.
Sources: https://huggingface.co/mradermacher/Hy-MT3-1.8B-GGUF (created 2026-11-24 21:25 UTC); https://huggingface.co/tencent/Hy-MT3-30B-A3B-GGUF (official GGUF tier, lastModified 2026-11-24 07:03 UTC); model card https://huggingface.co/tencent/Hy-MT3-1.8B; arXiv https://arxiv.org/abs/2611.27673; HN thread 21:56 UTC (story 50635336)

**mradermacher in-window quant ladder (8 new repos, 13:11–17:43 EST)** — event dates: November 24, 2026
New GGUF/i1 uploads inside the window: WeMM-Ecom-4B-i1 (13:31 EST; i1 imatrix tier of Tencent's e-commerce VLM — update on the GGUF reported this morning), gemma-3-1b-csw-best-i1 (13:11 EST, African code-switching gemma), Rosa_v1_3.43B-i1 (16:46 EST), Heliactis-2-3.7B GGUF + i1 (16:10/16:27 EST, Greek K2-Horizon LoRA-merged small model), fara-9b-heretic-uncensored GGUF + i1 (16:38/16:55 EST, decensored Microsoft-style computer-use/browser-agent VLM), Qwen3.5-4B-Abliterated-Full (16:51 EST), G4-LuneLight-v1.1-31B-GGUF (16:49 EST), BioReason-Pro-2-9B-RL-GGUF (17:43 EST, biology-reasoning RL spin).
Why it matters: the computer-use VLM tier (fara) and the 3–4B RL science models are the quant classes that keep 8–16GB boxes busy as harnesses move toward local GUI agents.
Local inference: all ≤ 31B static quants — 3–9GB files run on single 16GB cards; the 31B tier targets 24GB+.
Sources: https://huggingface.co/mradermacher (repo timestamps via HF API, captured 22:45–22:48 UTC November 24, 2026); e.g. https://huggingface.co/mradermacher/fara-9b-heretic-uncensored-GGUF

## Also noted
- (No additional verified minor items beyond the ladder above this window.)

## Just missed the window
- Tencent Hy-MT3 base open-weights: public HF timestamps (tencent/Hy-MT3-30B-A3B lastModified 2026-11-24 07:02 UTC = 2:02 AM EST; collection updated ~17h before the fire) put the weights disclosure early Tuesday EST morning — the 12:45 run should have caught it; only its quant wave is in-window today. (Footnoted once, never again.)
- Ars Technica's Asus ProArt P16 RTX Spark review (Ryan Whitwam, November 23, 7:00 AM EST): N1X 20-core/6144-core, 128GB LPDDR5X capped to ~110GB for AI under Windows, $4,399–$6,999; loads the 90GB Qwen3.8-Flash-Next 125B; verdict "niche machine... local AI still slow." Re-surfaced on Hacker News in-window but the event is Nov 23. (Footnoted once, never again.)
- Shuttle NW10U 0.4-liter Wildcat Lake mini-PC — up to 40 TOPS NPU, 64GB DDR5, dual PCIe 4.0 SSDs, Core 7 config $3,034 (VideoCardz, Nov 24, posted ~08:20 AM EST, pre-window). (Footnoted once, never again.)

## Coverage notes
- Verified items with in-window source timestamps: 7 (Dell earnings; llama.cpp DSpark + wave; vLLM wave; SGLang EGM + wave; TRT-LLM wave; Hy-MT3 day-0 quants; mradermacher ladder), plus 3 once-only footnotes.
- Ledger dedupe: 9 candidate items suppressed as already reported (VideoCardz AGESA 1.3.0.1f Ryzen 8000 Vcore cap; MAXSUN Arc Pro B70 Newegg store; STH ASUS Turbo R9700 review; NVIDIA Jensen-reinstatement coverage resurfacing on STH front; bartowski morning ladders — Sorihon Radiant-Dawn, WeMM-0.8B, next3-pro, MoganOCR-TR-1.5; Unsloth Inkling-2-Mini native-8bit/bnb-4bit; first WeMM-Ecom-4B GGUF tier; mradermacher WePocketModel ladder). Repeat footnotes: 0.
- Lanes down this run: r/LocalLLaMA anonymous RSS 404'd on both `search` and `new` endpoints (community lane unavailable — noted, not treated as negative finding); The Register AI index 404 ×2; Hugging Face trending API failed ×2 (skipped per two-strike rule).
- No new tagged releases in-window from Ollama (v0.40.18 at 04:18 UTC was reported 08:12), vLLM (v0.34.0 Nov 17), or TensorRT-LLM (v1.3.0 Nov 20).
