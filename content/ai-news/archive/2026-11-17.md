# AI News Briefing — Tuesday, November 17, 2026

**Window: Monday, November 16, 17:00 ET → Tuesday, November 17, 07:00 ET** (previous fire → this fire)

## TL;DR

- **vLLM v0.34.0 shipped Monday evening** — a major release: extensible KV-cache sizing is now on by default (bigger caches, higher throughput), Model Runner V1 is deleted, ten new model families land (Mistral Large 4, MiniMax-M3.1-Flash, LFM3, Ripple-65B…), and Blackwell/MI355X decode gains reach 1.95x.
- **Hugging Face handed its open-weight LLM routing to OpenRouter** — every chat model call on Inference Providers now rides OpenRouter's live price/speed/reliability router with automatic failover; custom provider keys stop working for language models.
- **A suspected Optimizely supply-chain attack injected malware into 178 organizations' websites** — BleepingComputer's Monday-night disclosure details crypto-address swapping, card skimming, cookie/screenshot theft and iOS exploit-kit delivery via cdn.optimizely.com.
- **Nvidia unveiled Rubin HPC at Supercomputing 2026** — "more than 50,000" CUDA cores (current Rubin: 28,672), NVLink 7, arriving 2028 — alongside a clean-sweep MLPerf Training v6.1 debut for Vera Rubin.
- **OpenAI detailed its NATO Daybreak deployment** — up to 300 billion raw tokens/month for NCIA, with ENISA and CERT Polska crediting AI-assisted vulnerability findings.

---

## Models and local inference

**vLLM v0.34.0: KV-cache defaults flip, Model Runner V1 deleted, Blackwell up to 1.95x**
*Event date: Monday, Nov 16 (~18:22 ET, GitHub release publish).* vLLM's newest major version (1,061 commits from 412 contributors) turns on extensible KV cache by default — the cache is now sized from memory actually free after CUDA-graph capture and warmup rather than an estimate, growing MiniMax-M3's cache on an H200 from 433k to 672k tokens, with the trade-off of up to +6–18% TPOT at concurrency 128 (opt out via `--gpu-memory-utilization 0.9`). Model Runner V1 is removed outright (V2-only), default `--block-size` moves 16→64, ROCm artifacts are now 10.0-only, and `huggingface_hub>=2.0` is required. Ten model families are added: Mistral Large 4 (1T/52B-active, FP8+NVFP4), MiniMax-M3.1-Flash (Blackwell NVFP4 + DSpark draft), Nemotron-3.5-Transcribe, Ripple-65B Preview, ZAYA1, Unlimited-OCR-2, LFM3/LFM3-VL/LFM3-Audio, Canary-1B-v2, Ming3-ASR and Parakeet TDT. Measured gains include up to 1.95x decode for Step5 on 4xGB200, 1.6x output throughput for GLM-5.3-Flash via DeepGEMM FP8 MegaMoE, 1.34–1.59x Kimi-K3 MLA decode on GB300, and +35–57% interactivity on 8xMI355X; suffix decoding now runs natively on MRV2 and CRIU checkpoint/restore endpoints arrive in both frontends.
*Local inference:* extensible KV cache, NVFP4 paths and the new RDNA defaults all target exactly this reader's hardware class.
Source: vLLM GitHub release v0.34.0 (published 2026-11-16 23:22 UTC) — https://github.com/vllm-project/vllm/releases/tag/v0.34.0

**Hugging Face switches language-model inference to OpenRouter routing**
*Event date: Monday, Nov 16 (announced evening ET).* "Today, we are switching Inference Providers from our own custom router to OpenRouter's": HF chat-model calls are now placed by OpenRouter's live measurements across 80+ providers with automatic in-call failover (its rebuilt router measured 46% fewer capacity errors, 16.9% faster TTFT, 16.3% higher throughput for 6% more cost), `:fastest`/`:cheapest`/`:provider` model-id suffixes, and cache-aware session pinning via `X-HF-Session-Id`. Pricing is unchanged (provider price, no HF markup; PRO gets $2/month credits); image/video/audio generation stays on HF's own path. The breaking change: personal provider API keys no longer work for language models routed this way. HF cites OpenRouter data showing open-weight models went from 26.8% of tokens served (Oct 2025) to 71.7% (Sep 2026), with DeepSeek-V4.1-Flash-1017 the most-used single model at 59.2 trillion tokens, and open weights averaging $0.28/$1.62 per million input/output tokens versus $1.27/$6.36 for closed.
*Why it matters:* the two chokepoints of the open-weight economy — the Hub as discovery layer and OpenRouter as demand router — are now one pipe; hosting operators lose direct HF-widget traffic while getting live-measurement placement, and anyone using per-provider keys has to migrate.
Source: Hugging Face blog, Célina Hanouti, Nov 16, 2026 — https://huggingface.co/blog/openrouter
Second source: OpenRouter announcement, Sasha Blumenfeld, Nov 16, 2026 — https://openrouter.ai/blog/announcements/hugging-face-inference-providers/

**Nvidia raises Rubin HPC at SC26: "more than 50,000" CUDA cores, NVLink 7, 2028**
*Event date: Monday night/Tuesday 01:20 ET publication.* At Supercomputing 2026 Nvidia took the wraps off Rubin HPC, positioned for agentic scientific workflows — its highest CUDA-core count ever ("more than 50,000" versus 28,672 on today's Rubin, implying a multi-die package akin to the four-die Rubin Ultra over NVLink-C2C), linked by NVLink 7 (prior disclosures: 3.6 TB/s per GPU, bigger scale-up domain). HBM capacity, bandwidth and FP64 balance were withheld; availability is stated as 2028. In the same newsroom wave Nvidia claimed a sweep of MLPerf Training v6.1 and its first MLPerf Endpoints submission with Vera Rubin (>10x per-watt agent inference claimed vs prior generation) and expanded Vera CPU to a rack-scale HPC platform.
*Why it matters:* with Nvidia's leadership in turmoil, SC26 was its chance to steer the story back to the roadmap; the "agentic HPC" framing also tells you where the CUDA-X software investment is going.
Source: Tom's Hardware, Jeffrey Kampman (Techmeme river 2026-11-17 01:20 ET) — https://www.tomshardware.com/pc-components/gpus/nvidia-announces-rubin-hpc-gpu-with-record-number-of-cuda-cores-at-supercomputing-2026-new-chip-accelerates-agentic-hpc-workloads-arrives-in-2028
Second source: NVIDIA newsroom "Latest News" (Rubin HPC / MLPerf sweep / Vera CPU rack posts) — https://nvidianews.nvidia.com/

**Update on previously reported (anthropic-s1-ant): Anthropic Direct — $900.9B commitments to 2045, first TPU tranche priced**
*Event date: Tuesday, Nov 17 (~05:50 ET, FT).* The FT worked through Anthropic's data-center buildout: IPO filings show $900.9 billion of infrastructure commitments running to 2045, and contract docs price the first tranche of Google TPUs at $7.2B per year for five years — roughly $36B for ~875,000 TPUs spread across four sites, dedicated to Anthropic rather than shared cloud. Forbes: "Anthropic IPO Puts $900 Billion Of Compute Bills On Public View."
*Why it matters:* this converts the "diversifying beyond Nvidia/clouds" narrative into specific numbers — a five-year, four-site Google TPU commitment is the clearest evidence yet that Anthropic is buying its way out of single-vendor dependency ahead of its Nov 19 ANT debut.

**Update on previously reported (anthropic-s1-ant): Claude Mythos ported Claude to AMD MI355X in ~3 days, ~100% of kernels autonomous**
*Event date: Monday, Nov 16 (23:02 ET, Tom's Hardware; the underlying roadshow claim dates ~Nov 10).* Tom's Hardware reported what Anthropic told IPO investors: Claude Mythos Preview did the software work to get Claude running on AMD's Instinct MI355X in about three days with "basically no human intervention," with the model writing almost 100% of the kernels — versus the usual six to twelve months.
*Why it matters:* if the port time collapses, CUDA's switching cost — the durable half of Nvidia's moat — erodes fastest on the exact workloads (kernel authoring, bring-up) that lock vendors in; also a bullish datapoint for AMD-based local rigs over time.
*Local inference:* kernel-authoring-by-agent is what makes MI355X/ROCm boxes realistically maintainable for small teams.
Source: Tom's Hardware (Techmeme river 2026-11-16 23:02 ET) — https://www.tomshardware.com/tech-industry/artificial-intelligence/anthropic-says-claude-mythos-slashed-software-work-to-onboard-amd-gpus-from-up-to-12-months-to-around-three-days-writing-almost-100-percent-of-kernels-autonomously-chips-away-at-nvidias-cuda-moat

**Also noted (Models and local inference):**
- **llama.cpp** merged 18 PRs and shipped builds b12464–b12470 in-window (Tue 06:28–10:56 UTC): up to 18x Metal CONV_2D speedup #28069, K/V read-order rotation for L2 hit rates #32407, Minimax-Music3 mtmd #32435, qwen3next output-projection weight scaling #32393, Kokoro `tts_dict` G2P #32397, Vulkan flash-attention flip disabled on AMD/Windows driver bug #32415. https://github.com/ggml-org/llama.cpp/releases
- **SGLang** merged 91 PRs in the 14-hour window — notable: idle heap-freeze GC cluster (#50883/#50980/#50981), PD disaggregation first-token streaming (#50895), AMD DSA 64-bit token offsets (#50870). Ollama quiet (still v0.40.17-rc1). https://github.com/sgl-project/sglang
- **kitaniai/clover-2 open weights** (created Nov 17 02:03 UTC): 27.8B dense base with 7 LoRA experts routed per token rather than a static merge — brand new, unverified quality; on r/LocalLLaMA hours ago. https://huggingface.co/kitaniai/clover-2
- **nvidia/Qwen3.8-Flash-Next-NVFP4-QAD** (repo created Nov 17 09:32 UTC): official NVIDIA ModelOpt NVFP4/MXFP8 quantization-aware-distillation checkpoint of Qwen3.8-Flash-Next (qwen4_exp), multimodal image-text-to-text — just fresh enough that nobody has independently benchmarked it yet. https://huggingface.co/nvidia/Qwen3.8-Flash-Next-NVFP4-QAD
- **Artificial Analysis** (river 23:45 ET Nov 16): Qwen4-Max beats Qwen3.8-Max on the Intelligence Index at ~3x lower cost per task and trails Kimi K3.1 Pro while costing ~11% less. https://artificialanalysis.ai/models/qwen4-max

## Agent tools

**OpenAI: NATO's NCIA runs Daybreak at up to 300 billion tokens a month**
*Event date: Tuesday, Nov 17 (published ~06:55 ET).* OpenAI published details of its Daybreak program with NATO's Communications and Information Agency: NATO's core institutions have used up to 300 billion raw input+output tokens per month for authorized defensive work — software review, suspicious-activity investigation, vulnerability validation, and fix development/testing. Named outcomes: ENISA found four vulnerabilities in software used across EU institutions (all four patched), a joint ENISA/CERT-EU review of 27 projects saw maintainers confirm 95% of 88 AI-flagged issues as genuine, CERT Polska identified six MikroTik RouterOS vulnerabilities later fixed by the vendor, and Ukraine's Ministry of Digital Transformation is applying Daybreak to civilian-infrastructure defense; OpenAI also supported a new AI-agent training stream at the 2026 Crossed Swords exercise.
*Why it matters:* this is the first hard utilization figure for frontier "cyber defender" programs — 300B tokens/month is an operational tempo, not a pilot — and the 95% maintainer-confirmation rate is the kind of precision evidence both procurement officers and skeptics will quote.
Source: OpenAI, "Strengthening NATO and allied cyber defenses with AI," Nov 17, 2026 — https://openai.com/index/strengthening-nato-allied-cyber-defences-ai/
Second source: WSJ (Mauro Orru) via Techmeme river, Nov 17 06:55 ET.

**Also noted (Agent tools):**
- **Model ML** raised $250M at a $1.5B valuation (Bloomberg, Nov 17 06:30 ET) — AI agents integrated inside Excel and PowerPoint for pitch decks, research and due diligence.
- **HN (11:56 UTC Nov 17):** "The Source Code of Law" — agents rewrote and red-teamed 30,000 articles of French law; early, worth watching: https://thesourcecodeoflaw.com/en/

## AI security and governance

**Suspected Optimizely supply-chain attack served malware to visitors of 178 organizations**
*Event date: Monday, Nov 16 (BleepingComputer disclosure 21:12 ET).* Attackers exploited a vulnerability in Optimizely's Experimentation account-signup flow to inject malicious JavaScript into customer projects, so sites from Zoom, CNN, Fox News, BBC, Salesforce, GitLab, HSBC Insurance, Equifax, IKEA, Air France-KLM, Ledger and UNICEF USA unknowingly served attacker code from the trusted cdn.optimizely.com. Researcher Scott Helme identified 188 infected scripts across 178 organizations after analyzing 1,611 platform scripts. The payload (`1688360[.]cc/track.js`) swaps cryptocurrency wallet addresses on page, skims payment-card fields, exfiltrates cookies/URLs/screenshots, and delivers the Coruna and DarkSword iOS exploit kits; roughly $7,000 had reached attacker wallets within about nine hours. Optimizely said it blocked the vulnerable path, removed injected code, revoked tokens/sessions and blocked delivery via Cloudflare; Salesforce disabled its Optimizely scripts, Equifax stopped using the platform with no system compromise found. Helme's data also showed the earliest injection in the early morning of Nov 16 and, per his blog, 68 affected sites had no CSP at all and 50 more were permissive enough to run the script anyway.
*Why it matters:* another trusted-third-party compromise with no origin-server breach — and this one weaponizes experimentation tooling, whose whole purpose is executing arbitrary JS on checkout and login flows; the iOS exploit-kit attachment raises the stakes from skimming to potential device compromise.
Source: BleepingComputer, Lawrence Abrams, Nov 16, 2026 21:12 ET — https://www.bleepingcomputer.com/news/security/optimizely-supply-chain-attack-delivers-malware-to-cnn-fox-news-bbc-salesforce-sites/
Second source: Cybernews (Reuters contribution), Nov 17, 2026 — https://cybernews.com/security/178-company-websites-infected-malware/

**Update on previously reported (megaspeed-alice-huang): overnight cascade — board forms NS/Export-Control committee, exec letter, husband's $9B DC network**
*Event dates: Monday evening → Tuesday morning, Nov 16–17.* Five developments inside the window: (1) Nvidia's board established a National Security and Export Control Oversight Committee (newsroom, Nov 17 05:13 ET); (2) WSJ exclusive — Developer Technology Director Nader Khalil wrote the board that failing to reinstate Jensen Huang risks losing "irreplaceable talent" (river 05:40 ET); (3) WSJ (Lingling Wei) — Alice Huang's husband Kang Wei built a state-backed data-center network in China with proposed investment above $9B between 2021 and 2024 (06:00 ET); (4) Reuters — Commerce began probing Megaspeed in 2024 over possible export-rule violations, and Nvidia had put Megaspeed on its internal "no-sell" list in June 2025 (00:55 ET); (5) CNBC — Nvidia is warning customers that the proposed Remote Access Security Act could subject chip buyers to US legal action if Chinese companies access their chips (05:32 ET). NYT separately reported Madison Huang retains a senior role after her father's exit (22:20 ET).
*Why it matters:* the committee is the board's first structural answer to the scandal; the RASA customer-warning is the most immediately material line for anyone buying Nvidia silicon — contractual liability for downstream remote access changes deal risk, not just headlines.
Source: NVIDIA newsroom — https://nvidianews.nvidia.com/ ; WSJ via Techmeme river Nov 17 — https://www.techmeme.com/river ; CNBC — https://www.cnbc.com/

**Also noted (AI security and governance):**
- **FT (00:40 ET Nov 17):** Amazon's $17B+ of planned Ohio data-center investment is at risk after midterm voters passed restriction measures affecting three proposed projects.
- **CNBC (22:35 ET Nov 16):** 13F filings show Leopold Aschenbrenner's Situational Awareness reloaded on AI stocks at end of Q3 — positions that have since plunged.
- **Fortune (04:20 ET Nov 17):** Numa (AI platform for dealership customer service) raised a $45M Series C at a ~$500M post-money.

---

## Just missed the window
- **macOS 27.3 beta 2 AI app-control warning** (MacRumors, Nov 16 ~15:30 ET, published just before the previous fire's cutoff): Apple warns users against granting AI apps Full Disk Access, nudging a per-app "Choose Apps" model — first hardening aimed specifically at agentic apps. https://www.macrumors.com/2026-11-16/macos-27-3-full-desk-access/
- **Huawei Cloud open-sourced CloudRobo-WM1.2**, an Ascend-native embodied world model with weights + inference + training code and claimed #1 on RoboTwin 2.0 (88.98%) and LiberoPro (81.7%) — disclosed during China-hours Monday (pre-window).
- **DeepSeek imposed consumer App/Web usage caps** (7-day idle-conversation cutoff, ~40k-character input limits, daily quota prompts citing "insufficient compute") per STAR Market Daily, Monday China-hours (pre-window; API unaffected).

## Coverage notes
- **Verified: 20 manifest units** = 8 lead items + 9 also-noted + 3 footnotes (footnotes excluded from any qualifying-email count); every lead's page fetched and read; second independent sources cited for HF/OpenRouter, Optimizely, Rubin HPC and OpenAI/NATO.
- **Ledger dedupe: 13 candidates suppressed as already reported** — Anthropic IPO oversubscription/Altimeter $5B, CA AG v OpenAI suit, Anthropic CVD windows, OpenAI CSAM classifier, GTIG autonomous-exploit tracker, BIS remote-access IFR (as original event), Qwen4-Max launch (its new AA cost-per-task numbers admitted as an update), Tencent Hy4-flash, Sage-S2.1-Flash, tandem.cpp v0.3.0, MadPot/ARTEX key-hunting, "AI national champions," Meta India recommender changes; plus the Kress financing-rein item, already footnoted once, was not re-listed.
- **Dead ends / limitations:** OVH "Olala mini 7A1B" (trending on r/LocalLLaMA) could not be located on the HF API after two attempts — dropped, not reported. The DuckDuckGo search backend returned errors on three queries (Anthropic Direct, Nvidia RASA warning, OVH) — recovered via alternative phrasing except OVH. FT/WSJ originals are paywalled; cited via fetched aggregators (Techmeme river entries) plus fetched accessible outlets. r/LocalLLaMA anonymous feed returned no timestamps/scores, so community items were gated on HF/GitHub API creation times instead. Techmeme river, HN Algolia, GitHub release/merge APIs, HF model API all healthy.
