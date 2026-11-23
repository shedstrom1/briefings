# AI News Briefing — Monday, November 23, 2026 (07:00–12:00 ET)

Window: 2026-11-23 07:00–12:00 ET. Source timestamps verified in-window per source.

## TL;DR

- Nvidia's board unanimously reinstated Jensen Huang as CEO and board member after a weekend of meetings — it received input from senior levels of the U.S. government and reported no national-security risk; shares jumped 33% on the news.
- Applied Digital signed an 18-year, 150 MW lease with a tier-one investment-grade hyperscaler for ~$5.4B base-term revenue (~$12.7B with renewals), pushing contracted base-term revenue above $41B.
- SenseTime published SenseNova-Loop-9B-DFlash2, a 967M-parameter speculative-decoding draft checkpoint for its looped 9B target model, with 2.14x single-stream throughput on 4xH800 (LightLLM integration, Apache-2.0).
- Hugging Face published "How to build and maintain an agentic evaluation" — a builder-facing guide to Harbor/Terminal-Bench-style evaluation and where the tooling can mislead.
- Engine activity: llama.cpp in-window commits added BF16 src1 Metal kernels, an agent-trace UI, and a ggml-openvino overhaul; vLLM and SGLang shipped small in-window waves; Ollama quiet.

## Models and local inference

**Nvidia board reinstates Jensen Huang as CEO and board director** (2026-11-23)
The Nvidia board voted unanimously to reinstate Jensen Huang as President and CEO and as a member of the Board, effective immediately, after what it called a series of discussions and meetings. The Board received input from senior levels of the U.S. government and is confident his reinstatement poses no national-security risk. Huang will lead Nvidia during the period between Colette Kress's interim-CEO term and Harvey Jones continuing as chair of the Board; Huang is also committed to cooperating with the independent committee's ongoing investigation into his affair with Alice Huang (a businesswoman US authorities suspect of spying for China, who US reports say was in contact with a Chinese spy agency official).
Why it matters: It reverses the board's own decision days ago to rule out his return; the stock surged ~33%, adding ~$1T in market value. The company reserved Colette Kress as CFO; independent directors retain the export-control and national-security committee, and a Chief Compliance Officer position will be searched. Huang is due for a closed-door transcribed interview with the House Foreign Affairs panel on December 1; general counsel Tim Teter appears December 2.
Sources: nvidianews.nvidia.com | NVIDIA Board Reinstates Jensen Huang as President, CEO and Board Director (Nov 23, 2026); theguardian.com | Jensen Huang reinstated as Nvidia CEO (2026-11-23); cnbc.com | Jensen Huang reinstated as Nvidia CEO. Stock skyrockets 33% (2026-11-23)

**SenseNova-Loop-9B-DFlash2: open-weight speculative-decoding draft for the 9B looped model** (2026-11-23)
SenseTime published the 967M-parameter BF16 DFlash2 draft checkpoint (five decoder layers, five execution-layer hidden states feeding the shared loop) alongside SenseNova-Loop-9B. It is Apache-2.0, designed for deployment with the target checkpoint via LightLLM. Measured on 4xNVIDIA H800 with 3,030 fixed prompts and 2,048 max output tokens, DFlash2 produced 268.37 tok/s at concurrency 1 (2.137x over 125.61 tok/s target), 1,392.79 tok/s at concurrency 8 (1.847x), and 2,550.62 tok/s at concurrency 32 (1.343x). The draft uses shared token embeddings and output head with the target; default draft length five tokens per round (MTP_STEP=7 option, or METHOD=target for no speculative decoding).
Why it matters: This is a small, deployable speculative-decoding companion for a laptop-class looped model — the kind of draft that composes with quantized targets. Target and draft are meant to be served together; the LightLLM integration is PR-branch only (feat/sensenova-loop-dflash2), so it is not in merged master.
Sources: huggingface.co/sensenova/SenseNova-Loop-9B-DFlash2 (2026-11-23) — see also SenseNova-Loop-9B official release note (Nov 18) for the target model lineage.

**Applied Digital signs 150 MW, 18-year lease worth ~$5.4B** (2026-11-23)
Applied Digital (NASDAQ: APLD) entered a new 18-year lease with a tier-one investment-grade hyperscaler for 150 MW of critical IT capacity. The lease provides approximately $5.4 billion in base-term contracted revenue — approximately $12.7 billion if all renewal options are exercised over a 33-year total term. Bringing contracted base-term revenue above $41 billion (~$100B potential lifetime), Applied Digital's portfolio is now over 1.6 GW of contracted critical IT load and 2.2 GW of grid-connected peak load, with ~75% of base-term revenue attributed to investment-grade tenants. Delivery is anticipated in H1 2028, with Delta Forge 1 / Polaris Forge 3 project finance expected in Q1 2027 and Delta Forge 2 in Q2 2027.
Why it matters: A data-center lease is not a local-inference event, but it is the clearest current signal of where AI compute dollars are being committed: long-duration, investment-grade demand for 150 MW-scale capacity. The lease is in the same risk-imaginary as the JPMorgan/Oracle AI-infra cycle.
Sources: ir.applieddigital.com | Applied Digital Secures New 150 MW, 18-Year Lease with Tier-One Investment-Grade Hyperscaler (Nov 23, 2026); money.usnews.com | Applied Digital signs $5.4B AI data center lease with hyperscaler (2026-11-23)

**llama.cpp / vLLM / SGLang in-window engine activity** (2026-11-23)
In-window llama.cpp commits: Metal BF16 src1 support for MUL_MAT/MUL_MAT_ID (#32677), a server exec_shell_command description prompt (#32894), an agentic-reply agent-trace UI (#32879), an iPhone gpu-timeout fix for Bonsai (#31815), a ggml-openvino fixes + model support PR (#32861), a typed-request server RPC (#32651), and a zero-block few-row MMQ fix (#32566). vLLM: torchAO backend deprecation (#69066), Hy4 DFly on compressed-tensors NVFP4 targets (#69274), ROCm sliding-window prefix-prefill skip (#6386x), sleep/wake/reload lifecycle hooks for the model loader (#57011), MLA KVConnector split-rows (#59586), and the Qwen3-ASR config alias normalization (#69226). SGLang: Ling-3.2-air cookbook doc (#52598), linker slot-location resolution before the forward fence (#49463), published-prefetch KV coverage (#49467), gfx950 Qwen3.8 QSA AITER sparse decode (#45196), qsa padded-Q MQA logits (#48649), and a softcapped-router finite-weight fix (#51138). Ollama: no in-window commits. Releases: no new release in-window (latest releases were b12643–b12651, all pre-cutoff).
Why it matters: Metal BF16 src1 support (with LLAMA_MOE_AMAX_SKIP caveat) is directly relevant to Apple-chip local inference; the agent-trace UI change signals more agent-harness framing in the chat-serving stack; the SGLang gfx950 AITER decode work keeps ROCm inference of Qwen-class models moving.
Sources: github.com commits (per-repo feeds, Nov 23 12:00–17:30 UTC).

## Agent tools

**Hugging Face publishes "How to build and maintain an agentic evaluation"** (2026-11-23)
Hugging Face published a builder-facing guide to Harbor-style agentic evaluations, authored by Avijit Ghosh, Ben Burtenshaw and Irene Solaiman. The guide covers the three eras of evaluation (answer-checking → agentic → team/swarms), an eight-step walkthrough (claim → task → run unchanged → adapt → check → read traces → report score → compare models), and an openness strategy for what to share. It uses a raman-lab task and the open Harbor harness with HF Jobs and HF Sandboxes as worked examples. A notable data point: Terminal-Bench audits kept the model fixed and changed the agent harness, with accuracy varying up to 29.9 percentage points — harness, not model, is the dominant variable in agentic scoring. On gpt-oss-120b in the example, 22 of 24 rollouts called mark_task_complete in the first response.
Sources: huggingface.co/blog/agentic-evals (Published Nov 23, 2026); explainx.ai | Hugging Face's agentic evaluation guide (Nov 23, 2026)

## AI security and governance

**Nvidia board reinstates Jensen Huang; Huang's national-security exposure in motion** (2026-11-23)
Same event as the Models and local inference heading above, reported here under security. The reinstatement is notable for policy reasons: the board is confident his return poses no national-security risk and received senior U.S. government input; the board formed a National Security and Export Control Committee of independent directors; a Chief Compliance Officer position will be searched; and Huang's commitments to the investigation, plus the pending December 1 transcribed House Foreign Affairs interview, frame the regulatory exposure. Sen. Elizabeth Warren (D-Mass.) stated that the reinstatement does not change the fact that Huang and Nvidia owe Congress answers about export-control compliance; Sen. Andy Kim (D-N.J.) follows with "Americans deserve answers about what NVIDIA and the Trump administration knew." The independent investigation (Wachtell Lipton Rosen & Katz) proceeds to conclusion; public comments from Senate staffers indicate Nvidia faces direct Congress and SEC scrutiny over export compliance.
Why it matters: The CEO reversal inverts a decision that had been framed as a national-security event. The political and legal framing around Huang's return is the recurring live story; Congress is the next checkpoint.
Sources: nvidianews.nvidia.com (Nov 23, 2026); theguardian.com (2026-11-23); cnbc.com (2026-11-23)

## Also noted

- AI market selloff deepeners: Nvidia loses another round; global tech continued to sell off on exasperation with the AI trade ahead of the Nvidia board decision.
- MiniMax H3 Max dropped from #3 to #6 on Text-to-Video Arena (1137.4 vs 1141.3; HeyGen Video 1 tops at 1147.2) — vendor-reported, no independent benchmark citation.
- OpenAI release-notes and harness-capability claims continued several announcements in the 07:00 ET fire window (GLM-5.4 Loop Transformer flagship, OpenClaw v2026.11.7, Qwen3.8-compatible agent harness changes) — reported in the 07:00 edition; not repeated here.

## Just missed the window (footnotes)

- European Court of Human Rights press release barring a Romanian lawyer for two years over hallucinated AI citations (Nov 18) — out-of-window event; in-window coverage does not pull it in. Not reprinted as a new item.
- Bridgetown Special Summit on AI Safety (Nov 23, Barbados) — event happening today, no new disclosure in-window. Noted in coverage notes only.

## Coverage notes

- Ledger dedupe: candidates suppressed as already reported / out-of-window — Riemann/Alpöge (reported 2026-11-20/21), Pachocki perplexity limits (reported by the 07:00 fire 2026-11-23), MAI-Base-2 (Oct 15-19), Frostbite CPU (footnoted 2026-11-18), Huang resignation (2026-11-13, already the subject of the 2026-11-20/21 editions), GraniteShares Anthropic ETFs (Nov 21), AscendGYM benchmark (Nov 13-16), the AI bubble selloff (2026-11-20), Bridgetown summit (Nov 20 is a past event). Reddit lane: search.rss 404 and anonymous feeds dead — lane logged unavailable, not negative-found. HN items returned mostly tiny show-and-ask posts — no lead item. web_search flaky on several queries today (DuckDuckGo/Yahoo fallbacks returned empty or errors) — coverage is Web/HN-index-driven, not reddit-driven.
- Verified count: 4 in-window substantive items + engine activity (llama.cpp/vLLM/SGLang) + market also-noted. No padding.
