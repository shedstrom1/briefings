# AI News Briefing — Saturday, November 14, 2026 (12:00–17:00 ET)

**Window:** 2026-11-14 12:00 → 17:00 EST (previous fire 12:00). New items only; all entries verified against fetched, in-window-timestamped pages.

## TL;DR

- **Nvidia board under open pressure to bring Jensen Huang back:** a reinstatement petition passed 115,000 signatures (incl. ~6,000 current Nvidia employees), with Musk, Epic's Tim Sweeney and Replit's Amjad Masad signed on; Altimeter's Brad Gerstner and Atreides's Gavin Baker — holders of ~$2.1B NVDA — urged a "slow, measured and deliberate" CEO search that keeps Huang's return on the table.
- **The Friday nationwide FAA ground stop is being traced to a router, not AI:** Transportation Secretary Sean Duffy said an L3Harris crew converting copper lines to fiber triggered a router that flooded the ATC network; the FBI earlier probed whether an update caused it, and the SMART AI tool's vendor denies any involvement.
- **tandem.cpp adds AMD (ROCm/HIP) support** and posts >2x llama.cpp decode for DeepSeek-V4.1-Flash IQ2_XS (139 GiB of experts) on a 16 GB RTX 5070 Ti + 64 GB RAM desktop — a big consumer-hardware result, self-published.
- **512k-context speculative decoding measured on one 96 GB RTX PRO 6000:** Qwen3.8-Flash-Next gains +27.9% decode with MTP Q8_0 drafting (75.6% acceptance); DeepSeek-V4.1-Flash's DSpark gains are task-dependent (+26% JSON, -11% C++).
- Quiet engine afternoon otherwise: llama.cpp shipped b12385–b12387, vLLM merged dual-batch-overlap under pure TP, SGLang landed NPU DSA token-sharding 2/2.

---

## Models and local inference

**tandem.cpp update: DeepSeek-V4.1-Flash on a 16 GB card + 64 GB RAM, now with AMD ROCm** — event date: Nov 14, 2026 (Reddit r/LocalLLaMA, 14:12 ET)
Update on previously reported (ledger 2026-11-13 `tandem-vllm-0.2.0`, same author's vLLM line): the author of tandem.cpp — a llama.cpp patch set that keeps hot MoE experts on GPU and lets the CPU help with the rest — published new numbers and HIP builds. DeepSeek-V4.1-Flash IQ2_XS (ISTA GSQ-RCO, 139 GiB experts) on an RTX 5070 Ti 16 GB / i5-14600KF / 64 GB RAM: 14.2 tok/s vs llama.cpp's 7.4 (Strata 10.4); on an RTX 5090 / 128 GB RAM it hits 33.1 tok/s vs 11.5. Qwen3.8-Flash-Next IQ3_S + MTP on a 32 GB RAM PC runs 34.3 vs 14.9 tok/s. On an RX 7900 XTX the HIP build is level with stock Strata (68.6 vs 69.7 tok/s), with RDNA4 builds reportedly buildable via `-DGPU_TARGETS=gfx1201`.
**Why it matters:** host-RAM/offload MoE serving is converging on 2–3x throughput gaps versus vanilla llama.cpp for models far larger than VRAM+RAM — and the CUDA/HIP kernel work is now shared, so AMD owners get the same wins.
Source: https://www.reddit.com/r/LocalLLaMA/comments/1xwan2j/update_tandemcpp_deepseekv41_on_a_64_gb_pc_with_a/ (posted Nov 14, 14:12 ET; thread read in full)

**512k-context spec-decode benchmark on a single 96 GB RTX PRO 6000 Blackwell** — event date: Nov 14, 2026 (Reddit r/LocalLLaMA, 14:20 ET)
A reproducible 3×5 benchmark (15 runs/config, temp 0, seed 42, q8_0 KV, FlashAttention, llama.cpp master build 12315) found Qwen3.8-Flash-Next UD-Q4_K_XL decode improves from 107.2 to 137.1 tok/s (+27.9%, 75.6% draft acceptance) with an MTP Q8_0 drafter at 512k context — at the cost of ~24% prompt-processing. DeepSeek-V4.1-Flash-1017 (UD-Q2_K_XL) with DSpark Q8_0 was task-dependent: +26% on JSON generation, −11% on C++ code.
**Why it matters:** careful single-user numbers with stated methodology are rarer than engine marketing; the MTP-vs-DSpark split (always-on vs task-dependent gains) should inform how people configure long-context agents on Blackwell workstation cards.
Source: https://www.reddit.com/r/LocalLLaMA/comments/1xwatyu/benchmarks_512k_speculative_decoding_on_a_single/ (posted Nov 14, 14:20 ET; thread read in full)

**tiny-voca: fully open 4.5M-parameter TTS — code, weights, and training dataset** — event date: Nov 14, 2026 (Reddit r/LocalLLaMA, 12:26 ET)
A from-scratch English TTS with 4,537,291 parameters (18 MB total, waveform included), two 24 kHz voices, ~70x real-time on four CPU threads, trained in 7.7 hours on one RTX 4090. It skips a neural vocoder entirely (source-filter synthesis) and ships a custom phonemizer with a 125k-word dictionary plus number/date/money rules; everything including the dataset is open.
**Why it matters:** a reminder that the low end of the TTS stack is becoming trivially local — no vocoder, CPU-only, browser-runnable (PyTorch/ONNX/WebGPU-style) — relevant for voice agents where latency and auditability beat fidelity.
**Local inference:** genuinely zero-GPU; English-only with synthetic training audio (made with Qwen3-TTS) caps fidelity.
Source: https://www.reddit.com/r/LocalLLaMA/comments/1xw7zrg/tinyvoca_a_fully_open_45mparameter_tts_code/ (posted Nov 14, 12:26 ET; thread read in full)

**Inference engine round-up (12:00–17:00 ET)**
llama.cpp: 3 in-window merges — ctz64 fix on 32-bit (#32273), Hexagon flash-attention no-mask tail fix (#32243), Qwen2-VL FFN weight-scales (#32241) — plus builds b12385–b12387 (13:48–14:26 ET). vLLM: 3 merges — dual batch overlap (DBO) under pure tensor parallelism (split of the MiniMax-M3.1 PR, #63483, 14:22 ET), a dummy-run max_model_len bugfix (#67545) and a CI stub (#67636). SGLang: 6 commits, headline NPU DSA token-sharding part 2/2 (#42978, 15:27 ET) plus a dp-controller router_l3_layout handshake (#50544) and refreshed AMD MI355X AgentX cookbooks for DeepSeek-V4.1 and GLM-5.2 aligned to InferenceX. Ollama quiet; no versioned releases from SGLang/Ollama/vLLM in-window.
Sources: https://github.com/ggml-org/llama.cpp/releases (timestamps verified via GitHub API) · https://github.com/vllm-project/vLLM (commits verified via GitHub API) · https://github.com/sgl-project/sglang (commits verified via GitHub API)

## Agent tools

No verified new items in this window. (OpenClaw, LangGraph, MS Agent Framework, pydantic-ai, CrewAI: no in-window releases per GitHub release feeds; the morning's Cowork MCP mission-control HN debut was already reported in the 12:00 edition.)

## AI security and governance

**Update (ledger 2026-11-13 `faa-smart-fbi-probe`): Secretary Duffy blames L3Harris router during copper-to-fiber conversion for the nationwide ground stop** — event date: Nov 14, 2026
Speaking Saturday, Transportation Secretary Sean Duffy said the Friday five-hour nationwide ground stop — ~10,000 cancellations, 7,000+ delays — began when a router being replaced by FAA telecom contractor L3Harris during copper-to-fiber modernization at a remote facility started flooding other routers on the ATC network, disrupting radar feeds; officials told Reuters routers in Arkansas and Texas may have been involved, and it's undetermined whether it was an installation error or a faulty unit. L3Harris (which runs the FAA Telecommunications Infrastructure, 4,400+ facilities, >70% fiber-converted) said it is "supporting the FAA in its investigation"; Duffy raised his ATC modernization ask from $30B to $50B, calling it "an emergency." Air Space Intelligence again denied its newly nationwide SMART AI advisory system had any role.
**Why it matters:** the episode closes the loop on the FBI's update-causation probe with a conventional IT supply-chain explanation while SMART — just expanded nationwide on Nov 10 — exits as a suspect; the practical fallout is a $50B modernization ask and renewed scrutiny of single-router SPOFs in national infrastructure.
Sources: https://www.usatoday.com/story/news/nation/2026-11-14/faa-ground-stop-probe-l3harris/92780406007/ (updated Nov 14, 4:18 PM ET; read in full) · https://finance.yahoo.com/markets/stocks/articles/duffy-says-l3harris-crew-working-170900518.html (Nov 14, ~12:09 PM ET; L3Harris -4.2% after hours)

**Update (ledger 2026-11-08 `update:megaspeed-alice-huang`): "Stand with Jensen" petition tops 115K; $2.1B-holding investors ask Nvidia's board to keep his return on the table** — event date: Nov 14, 2026
The reinstatement petition hit >115,000 signatures by Saturday afternoon — the site counts 5,965 current and 1,108 former Nvidia employees and 63,063 shareholders — with Elon Musk, Epic CEO Tim Sweeney and Replit CEO Amjad Masad among signers; its demands are an independent investigation, disclosure of findings, and Huang restored "while" that runs ("Jensen deserves due process, not mob justice"). Separately, Altimeter's Brad Gerstner and Atreides's Gavin Baker (funds holding ~$2.13B NVDA as of June; CNBC reported the letter Saturday afternoon, after they posted it to X late Friday) urged a "slow, measured and deliberate" CEO search that explicitly leaves open "the potential return of Jensen Huang" while CFO-era veteran Colette Kress serves as interim.
**Why it matters:** this is now a two-front legitimacy challenge (public + shareholder) to a board that was just reported to be "selecting a search firm," and the open-weight ecosystem's biggest patron's fate has direct compute-funding consequences (Nvidia's $165B in cloud backstops and $48B in private stakes per NYT).
Sources: https://techcrunch.com/2026-11-14/elon-musk-other-tech-execs-sign-petition-calling-on-nvidia-to-reinstate-jensen-huang/ (Nov 14, 1:41 PM PST = 4:41 PM ET; read in full) · https://www.cnbc.com/2026-11-14/nvidia-investors-jensen-huang-return-board-letter.html (Nov 14, ~2:55 PM ET) · https://aiweekly.co/alerts/altimeter-atreides-urge-nvidia-board-to-keep-huang-return-open (Nov 14, 3:05 PM ET)

---

## Just missed the window

- **Lovable security report (HN front page 12:43 PM ET, 492 points):** Crissy Field's English-language update of its Handelsblatt-published Nov 2 investigation — 159,041 Lovable-built projects surveyed, 24,664 exposing database records without login, including 1,000+ passport-number records and 200,000+ private chat messages — re-surfaced in-window, but the underlying disclosure is Nov 2. (Already-footnoted events are never re-listed; this one is footnoted here once.)
- **Google Research /teamwork results post (Nov 13):** Antigravity's multi-agent harness + Gemini 4 Argon/Carbon/Hydrogen proved the Courtade–Kumar conjecture (Lean 4 formalization ~50M lines), formalized the Weil conjectures (1.8M Lean lines, 28,800 subagent invocations) and Navier–Stokes blowup, with 6 months free top-tier access opened to 10,000 academic researchers — published Nov 13, minutes past the previous fire's cutoff.
- **Pentagon renames CDAO the "Super Intelligence Office" (Nov 13):** DoD redesignated its Chief Digital and AI Office as the SIO; head Cameron Stanley's mandate is to "harness the full power of sovereign Super Intelligence" for military superiority.

## Coverage notes

- **Verified count: 8 qualifying items** (3 lead/updates, 3 engine/community also-noted, counted per the reconciled event manifest) + 3 "just missed the window" footnotes. Lead stories read in full: USA Today (FAA), TechCrunch (petition), crissyfield.de (Lovable), research.google (/teamwork), plus three Reddit threads read in full and GitHub commit/release timestamps verified via API.
- **Ledger dedupe:** ~9 candidates suppressed as already reported — Italy Garante €805M Meta fine (12:00 fire today), Microsoft idle-GPU credit patent (Nov 11), Nathan Lambert Interconnects piece (already footnoted `missed:`), tandem vLLM base story (folded into update), M365 outage resolution (Nov 13 event), Quanta introspection feature (Nov 4, re-spike), PyPI security-signal pages (Nov 6 event), AMD "Siren Halo" GDDR7 rumor (Nov 12–13), Databento $100M Series C (Nov 12–13 disclosure), Nex-N2.6 (OpenRouter listing Nov 10), CISA KEV two-additions alert (Nov 13).
- **Dead ends / limitations:** Reddit `.json` endpoints blocked (403) — recovered via the reddit-reading skill's anonymous feeds (no scores available); Ars Technica FAA feature URL 404'd on first fetch (recovered via exact-headline search, then excluded as an out-of-window Nov 13 piece); HF daily-papers API returned empty; AutoGen releases endpoint 404 (repo moved). No verified Agent-tool category items this window — Saturday afternoon, engines-only traffic.
