# AI News Briefing — Fri Nov 13, 2026 · 12:00–17:00 ET

**Window:** 2026-11-13 12:00 → 17:00 EST (previous fire → this fire). New items only; ledger dedupe applied.

## TL;DR

- **OpenAI disbands its stand-alone robotics team** — researchers fold into Mark Chen's research org and Greg Brockman's Applied org, a bet that general models (GPT-6 Astra) can drive robots directly; its CoRL booth sat empty.
- **Nvidia's board is "in the process of selecting a search firm"** for a permanent CEO and has "not formally engaged any candidates," it told CNBC — hours after FT reported it approached Arm CEO Rene Haas.
- **Tandem-vLLM 0.2.0** brings DeepSeek-V4.1-Flash to Ampere/Ada/Hopper cards and two-GPU rigs via 2.1-bit host-RAM vector-quantized experts.
- **Anthropic's IPO is closed to mainland China and Hong Kong institutional buyers**, per Bloomberg — mirroring the SpaceX playbook.
- Engines: vLLM 28 in-window merges (arbitrary DeepSeek-V4.1 pipeline-stage partitions), SGLang 14 (sgl-kernel 0.4.11), llama.cpp 4 (hexagon SSM_SCAN), Ollama quiet.

## Models and local inference

**OpenAI disbands its stand-alone robotics team as general models start running robots** — event date: Nov 13, 2026 (published 12:05 PM ET)
OpenAI has dissolved its stand-alone robotics team, reassigning robotics researchers to the main research organization under Chief Research Officer Mark Chen, and applied robotics/hardware staff to the Applied org under President Greg Brockman. The company confirmed the reorg and framed it as acceleration, not retreat: it now bets that general-purpose models like GPT-6 Astra can control robots — including humanoids — directly, without a specialized unit building dedicated "brain" software underneath. The second dissolution in six years (the first team was wound down in 2020, rebuilt 2024 alongside the Figure partnership that ended Feb 2025) landed late enough that OpenAI's registered booth #208 at this week's CoRL conference in Austin sat empty. Altman still targets a public humanoid demo in 2027.
*Why it matters:* This is the strongest signal yet that "general model + embodiment" is now the sanctioned roadmap at a frontier lab — the same logic driving NVIDIA's GR00T and world-model stacks, and a tailwind for open VLA/world-action releases rather than bespoke robotics labs.
*Local inference note:* OpenAI's humanoid ambitions are cloud/API-side, but the bet validates the community pattern of driving robots from locally-run general models plus small adapters.
Source: Fortune (Wen Shao), Nov 13, 2026 12:05 PM ET — https://fortune.com/2026/11/13/openai-disbands-robotics-team-as-its-general-models-start-running-robots/
Source: Seeking Alpha (rep.), Nov 13 — https://seekingalpha.com/news/4672622-openai-dissolves-stand-alone-robotics-team-report

**Update on previously reported (ledger megaspeed-alice-huang, Nov 13 07:00/12:00 editions): Nvidia board moves on CEO search — Haas approached, search firm not yet picked** — event date: Nov 13, 2026
Nvidia's board told CNBC Friday (statement published 2:18 PM ET): "We are in the process of selecting a search firm for the Nvidia CEO role… We have not formally engaged any potential candidates." That directly followed the Financial Times reporting earlier Friday that the board has approached Arm CEO Rene Haas — a seven-year Nvidia veteran (ex-VP of computing products, left 2013) who is considered one of few viable outsiders alongside AMD's Lisa Su. The board called candidate speculation "premature"; interim CEO Colette Kress held her first leadership meeting Thursday afternoon. NVDA fell another ~2% Friday, pushing market cap back below $4 trillion.
*Why it matters:* The formalization (search-firm selection) plus the Haas approach are the first concrete process steps since Kress was named interim Thursday — and SoftBank ties around Haas add a new governance wrinkle while the Wachtell probe and congressional testimony demands proceed.
Source: CNBC (Seema Mody, Annie Palmer), Nov 13, 2026 2:18 PM ET — https://www.cnbc.com/2026/11/13/nvidia-huang-ceo-replacement-search.html
Source: Financial Times, Nov 13, 2026 — https://www.ft.com/content/2a64f698-46dd-4178-a3f4-4c5f4c0a0926

**Tandem-vLLM 0.2.0: DeepSeek-V4.1-Flash on 3090/4090-class, A6000, Ada and Hopper — and on two GPUs** — event date: Nov 13, 2026 (posted 4:03 PM ET)
Tandem-vLLM, an expert-offload build of vLLM 0.31 for DeepSeek-V4.1-Flash, shipped 0.2.0 after 0.1.1 ran only on Blackwell. It stores routed experts in host RAM as 2-bit vector-quantized codes (2.1 bits/weight) behind a GPU cache of hot experts, and 0.2.0 adds Hopper, Ada and Ampere support plus a two-GPU mode where a second card acts as an "expert helper." The model package (ewhacc/DeepSeek-V4.1-Flash-1017-Tandem-VQ2 on Hugging Face) is 293 GB; the engine (github.com/ewhacc/tandem, Apache-2.0) was pushed today.
*Local inference note:* Ampere/Ada/Hopper + dual-GPU support is the headline; self-published by the author, so treat performance claims as unverified.
Source: r/LocalLLaMA, Nov 13, 2026 21:03 UTC — https://www.reddit.com/r/LocalLLaMA/comments/1xvjoa7/tandemvllm_020_deepseekv41flash_now_on/

**Also noted (Models and local inference)**

- **Nace "Weight Space Programming" quants claim up to 2x better KL divergence** for Qwen 9B/27B/35B and Flash-Next variants vs conventional quantization (r/LocalLLaMA, Nov 13, 2:23 PM ET; vendor self-published, no independent reproduction yet) — https://www.reddit.com/r/LocalLLaMA/comments/1xvh4ag/weight_space_programming_for_quantization_nace/
- **G-STAR: Group-Scaled Token Capacity for deeply compressed autoregressive generation** — paper plus model release from a community team (r/LocalLLaMA, Nov 13, 1:44 PM ET) — https://www.reddit.com/r/LocalLLaMA/comments/1xvg1xb/paper_model_gstar_groupscaled_token_capacity_for/
- **Tontaube V1.1**: open-weights TTS with voice cloning aimed at long-form generation; author says the 4-bit build runs about real time on a desktop GPU (r/LocalLLaMA, Nov 13, 12:14 PM ET; self-published) — https://www.reddit.com/r/LocalLLaMA/comments/1xvdmzb/tontaube_v11_openweights_tts_with_voice_cloning/
- **Vellium 1.9.5** — visual-novel generation tuned to "actually work" on a 12B local model (r/LocalLLaMA, Nov 13, 3:17 PM ET; self-published) — https://www.reddit.com/r/LocalLLaMA/comments/1xvihou/vellium_195_visual_novels_that_actually_work_on_a/
- **Inference engines (12:00–17:00 ET):** llama.cpp 4 commits — hexagon SSM_SCAN op #31921, GCN MMQ config tuning #30021, UI npmrc + CI timeout hygiene. vLLM 28 merges — arbitrary pipeline-stage partitions for DeepSeek-V4.1 #63106, startup OOM fix in KV-cache sizing warmup #67485, adaptive verification for FlashInferMLASparseSM90 #62515, Revert of FlashInfer all-reduce override #67041, Muse vision-encoder sync removal #64822, gfx1151 RDNA attention 16-token KV blocks #67344. SGLang 14 commits — sgl-kernel 0.4.11 bump #50369, aarch64 SHA-256 rust-server #50204, FlashInfer MegaMoE autotune simplification #45905, FA3 moved to sgl-fa3 wheel #50260, AMD DCP host-offset slicing #43136. Ollama quiet (latest v0.40.17-rc0, Nov 10). Sources: https://github.com/ggml-org/llama.cpp , https://github.com/vllm-project/vllm , https://github.com/sgl-project/sglang

## Agent tools

No verified in-window agent-tooling releases this cycle. (Cloudflare's Kitesurf agent-browser open-sourcing landed Thursday — see footnotes; the one HN submission in-window, fig.inc's "Models Are Outpacing Environment Creation," is a vendor blog with no release.)

## AI security and governance

**Update on previously reported (ledger anthropic-s1-ant, Nov 12 17:00 edition): Anthropic IPO closed to mainland China and Hong Kong institutional investors** — event date: Nov 13, 2026 (reported ~12:10 PM ET)
Bloomberg reports, citing people familiar, that institutional investors based in mainland China and Hong Kong have been excluded from placing orders in Anthropic's IPO — the same restriction applied to SpaceX's offering — partly due to US arms-export rules and the strategic sensitivity of frontier AI to both Washington and Beijing. It lands three business days before the Nov 18 pricing / Nov 19 Nasdaq debut (ticker ANT) already on the ledger.
*Why it matters:* The IPO is shaping into a geopolitically gated offering: exclusion rules that read like export controls on the share book itself, on top of the ledger's earlier reporting that Anthropic's IPO is ~6x oversubscribed.
Source: Bloomberg, Nov 13, 2026 — https://www.bloomberg.com/news/articles/2026-11-13/anthropic-ipo-share-orders-from-china-are-said-to-be-rebuffed (via Techmeme 12:10 PM ET; rep.: https://finance.yahoo.com/markets/stocks/articles/anthropic-ipo-reportedly-closed-china-174510000.html )

**Also noted (AI security and governance)**

- **Meta is testing "Takes,"** an internal prediction-market app where employees wager virtual "cred" (not real money) on event outcomes, per WSJ; employees began testing Friday (Techmeme 3:05 PM ET). Reported: WSJ (Meghan Bobrowsky), Nov 13 — https://www.wsj.com/tech/meta-testing-new-prediction-market-app-with-employees-bf7bacb2 ; Reuters rep., Nov 13 — https://www.reuters.com/business/meta-employees-test-prediction-market-app-wsj-reports-2026-11-13/

## Just missed the window

- **Four nonprofits sue to freeze frontier AI development** (Nov 12, filed San Francisco Superior Court): Irreplaceable Education Fund, Public Citizen, Heat Initiative and Alliance for Secure AI seek an emergency injunction under California's Unfair Competition Law barring Anthropic, Google, Meta, OpenAI and SpaceXSI from building/deploying systems more capable than current ones, citing bioweapon/cyberattack escalation and lab-leak incidents — https://www.reuters.com/business/openai-google-anthropic-hit-with-lawsuit-seeking-halt-frontier-ai-development-2026-11-12/
- **Runway open-sources Praxis-1** (released Nov 11, covered Nov 12): 27B MoE diffusion world-action model for robot control, Apache-2.0 incl. weights, inference + LoRA fine-tuning code; RoboLab-120 46.2% (top world-action model claim); runs on H100/B200, RTX 5090/PRO 6000, Jetson Thor; available via LeRobot and vLLM-Omni — https://www.therobotreport.com/runways-praxis-1-world-action-model-for-robotics-is-now-available/
- **Cloudflare open-sources Kitesurf** (Nov 12): Apache-2.0 agent-first browser engine on Workers — HTML/CSS/JS + CDP without Chrome containers; repo pushed again today and hit HN this afternoon — https://blog.cloudflare.com/kitesurf-open-source and https://developers.cloudflare.com/changelog/post/2026-11-12-kitesurf-open-source/

## Coverage notes

- **Verified: 10 items in-window** (4 full leads incl. 2 ledger-update items + 5 also-noted/one-liners + 1 engines bundle), each from a fetched page carrying an in-window date/timestamp; lead stories (Fortune, CNBC) read in full. Plus 3 "Just missed the window" footnotes (each footnoted once).
- **Ledger dedupe:** ~12 candidates suppressed as already reported — ChatGPT Wallet, Nscale amended S-1, Grok Imagine layoffs, $3.2B Chinese compute, xFusion ChiNext IPO, Samsung–Modine talks (all from the 07:00/12:00 fires today); Daybreak Red Team EA and Nvidia chip-tracking (repeat `missed:` footnotes, not re-listed); Wired PR-firm and NYT "lost ally" re-coverage of the megaspeed saga; Claude Duet review re-coverage.
- **Rejected as stale (event predates window, never reported):** Riot/Anthropic $28B Corsicana naming (Riot 10-Q, Nov 4–5), OpenAI Codex Security CLI open-sourcing (community post July 29), September antitrust "AI pacing" suit resurfacing, APRA System Risk Outlook (released Thursday).
- Dead ends: bloomberg.com paywall (Anthropic China exclusion corroborated via Techmeme + Yahoo rep. instead); r/LocalLLaMA `search` endpoint 403'd (fell back to `--json sub --sort new`, gated on `created`); WSJ lead behind paywall (Reuters rep. cited).
- Outlets represented: Fortune, CNBC, FT, Bloomberg, Reuters, WSJ, The Robot Report, Cloudflare, Hugging Face, GitHub, r/LocalLLaMA, HN — ≥10 distinct.
