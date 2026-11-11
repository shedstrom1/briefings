# AI News Brief — Wednesday, November 11, 2026 (07:00 ET edition)

Window: 2026-11-10 17:00 EST → 2026-11-11 07:00 EST (previous fire → this fire; new items only).

**TL;DR**
- Alibaba opened a hosted preview of Qwen4-Max — first public surface of the Qwen4 generation: 1M-token context, image and video input, no weights.
- Microsoft Research published "Lazarus": fewer than ten edited dormant neurons backdoor an AI agent with no training — and Claude Code found the method by itself.
- VideoCardz reports NVIDIA has reportedly canceled the RTX 5080 24GB and will keep the 16GB in production, blaming 3GB GDDR7 module costs.
- Busy engine night: llama.cpp added CUDA OCP FP8 compute support; vLLM landed 40+ merges including DeepSeek-V4.1 Engram work and Kimi-K3.1 B300 presets.

## Models and local inference

**Alibaba opens Qwen4-Max preview — hosted only, 1M context**
Event: Tuesday November 10, 9:01 PM ET (official @Alibaba_Qwen post; HN front-page submission at 9:33 PM ET). Alibaba opened a preview of Qwen4-Max, flagship of the Qwen4 family: up to a one-million-token context window with image and video input, delivered through the Token Plan subscription plus the Qwen app, Qoder, QwenWork/Qwen Office and QwenStudio (TechNode, Nov 11). No weights are published — TechNode and explainx both characterize it as a hosted preview, not the four-tier family launch; xenospectrum adds that Token Plan preview access runs to November 25 and prohibits backend use. Community reports on the HN thread claim reverse-engineering and offensive-cyber prompts error out on Qwen4-Max-Preview (unverified).
Why it matters: This is the first production look at the Qwen4 architecture line that Qwen3.8-Flash-Next previewed. Watch for weights, pricing, and Artificial Analysis scoring of the Max preview.
Local inference: hosted-only today; Qwen3.8 line stays the local option.
Source: "Qwen4-Max is now in Preview" — @Alibaba_Qwen on X, Nov 11 02:01 UTC: https://twitter.com/Alibaba_Qwen/status/2120335993643684315
Source: "Alibaba opens Qwen4-Max preview with a million-token context window" — TechNode, Nov 11: https://technode.com/2026/11/11/alibaba-opens-qwen4-max-preview-with-a-million-token-context-window/
Source: "Qwen4-Max is now in Preview" — Hacker News item 50449130, Nov 11 02:33 UTC: https://news.ycombinator.com/item?id=50449130
Source: "Alibaba's Qwen4-Max Preview Offers a 1M-Token Context Window" — Xenospectrum, Nov 11: https://xenospectrum.com/en/qwen4-max-preview-specs-limitations/

**Reported: NVIDIA cancels the RTX 5080 24GB; 16GB stays in production**
Event: Wednesday November 11, 04:15 ET (VideoCardz, rumor-tagged, Chinese board-channel sources). NVIDIA has reportedly told AIB partners the GeForce RTX 5080 24GB will not launch (it had been tipped for January 2027), and that the existing 16GB model continues with 2GB GDDR7 modules. The blocker: 3GB GDDR7 chips reportedly cost about three times the 2GB modules, making a 24GB card unsellable at an acceptable price — the same memory-cost issue earlier reports tied to the shelved RTX 5080 SUPER 24GB.
Why it matters: the 24GB-class consumer card is the sweet spot for 27B/30B-A3B local models; a canceled 5080 24GB keeps the sub-$1,000 24GB tier empty and pushes local-inference demand toward used 3090 mods, the 5090 and RTX PRO cards — right as GDDR7/HBM pricing pressure is already inflating the whole stack. Treat as unconfirmed: it is a channel rumor, and VideoCardz is the only outlet so far.
Source: "NVIDIA reportedly cancels GeForce RTX 5080 24GB launch, brings back 16GB model over GDDR7 costs" — VideoCardz, Nov 11 09:15 UTC: https://videocardz.com/newz/nvidia-reportedly-cancels-geforce-rtx-5080-24gb-launch-brings-back-16gb-model-over-gddr7-costs

**llama.cpp overnight: CUDA OCP FP8 kernels land (15 new commits)**
Event: Tue 5:00 PM → Wed 7:00 ET, ggml-org/llama.cpp. Sixteen commits merged in-window, headlined by "CUDA: Add OCP FP8 support" (#28413) — the Open Compute FP8 E4M3 path on the CUDA compute side, following the v0.9.0 quantization/ftype work. Also: MMVF kernels for BF16 few-row matrices at small batch (#31959), sm70 FlashAttention config for DKQ/DV=256 (#27997), FA mma head sizes 40/72 (#30285), Vulkan dequantized-KV with CM2 (#31526), a fixed ROCm BLAS workspace instead of per-call rocBLAS allocation (#31838), CPU flash-attention V accumulation in F32 (#29810), and a quantize imatrix filepath-metadata leak fix (#32000).
Source: ggml-org/llama.cpp commits, Nov 10 22:00 UTC → Nov 11 12:00 UTC: https://github.com/ggml-org/llama.cpp/commits/master

**vLLM overnight: ~40+ merges — Engram perf cluster, B300/Rubin recipes (also-noted)**
Event: same window, vllm-project/vllm. Codex shipped rust-v0.167.1 stable at 4:32 PM ET (just pre-window) plus in-window alpha tags (0.169.0-alpha.9/10/11).
Source: vllm-project/vllm merged PRs (search merged:>=2026-11-10T22:00:00Z): https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged

**Update (previously footnoted Nov 6): Baidu puts a date on ERNIE 5.5 Flash — "two weeks"**
Event: Wednesday November 11, 00:42 ET, official @Baidu_Inc post: ERNIE 5.5 Flash will be released in two weeks (~Nov 25). This upgrades the Nov 5 ERNIE-5.5-Flash-Base base checkpoint (280B-A14B, 33.5T tokens; ledger entry) to a dated instruction/release track.
Source: @Baidu_Inc on X, Nov 11 05:42 UTC: https://x.com/Baidu_Inc/status/2120391732248637734 (via r/LocalLLaMA: https://www.reddit.com/r/LocalLLaMA/comments/1xted8w/ernie_55_flash_will_be_released_in_two_weeks/)

## Agent tools

No qualifying new in-window items. (Closest misses: IBM×Microsoft "Imagine Enterprise" and OpenClaw v2026.11.3 — both disclosed before 5:00 PM ET Tuesday; footnoted below.)

## AI security and governance

**Microsoft Research: "Lazarus" backdoors AI agents by reprogramming fewer than 10 dormant neurons**
Event: Tuesday November 10 (MSR blog "Published November 10, 2026"; official @MSFTResearch promotion at 6:18 PM ET). The Lazarus technique edits dormant neurons in a model's final feed-forward block — closed-form weight sets, optionally refined with a few hundred gradient steps — no retraining. A triggered model passes standard evals and behaves normally until a trigger (example given: the clock crossing midnight into 2027) makes chained neurons scream a payload token-by-token into the residual stream; an agent with terminal access then executes attacker commands. Key specifics: the attack needs only write access to a checkpoint and inference-grade compute ("if your machine has enough RAM to run a model, it has enough to backdoor it"); it applies equally to closed/internal models; agent-built detectors flag 91.4% of held-out edited checkpoints at a 9.7% false-positive rate without a reference model; and the core insight — dormant-neuron chaining — was discovered autonomously by Claude Code with Opus 4.5 in February 2026 before the formal study. Microsoft is tying defenses to provenance: Foundry publisher-tied onboarding and container scanning today, frontier-agent weight scanning, red-teaming harness and cryptographic model signing planned.
Why it matters: this is the practical supply-chain threat for exactly how the local-inference community gets models — downloaded GGUF quants and community finetunes. A malicious quant or a compromised hub/pipeline can ship a time-bombed agent that clears every behavior test; the defense that actually works is diffing against trusted original weights, i.e., signing and provenance, which the open model ecosystem mostly lacks today. Echoes the abliterated-model backdoor PoC and the LiteLLM release-pipeline hijack in recent weeks.
Source: "Lazare, veni foras: Reprogramming Dormant Neurons to Backdoor AI Agents" — Microsoft Research blog, Nov 10: https://www.microsoft.com/en-us/research/blog/lazare-veni-foras-reprogramming-dormant-neurons-to-backdoor-ai-agents/
Source: paper page "Lazare veni foras: Reprogramming Dormant Neurons to Backdoor AI Agents" — Microsoft Research (Zeng, Severi, Bullwinkel, Horvitz, Papailiopoulos): https://www.microsoft.com/en-us/research/publication/lazare-veni-foras-reprogramming-dormant-neurons-to-backdoor-ai-agents/
Source: r/LocalLLaMA thread (119 upvotes): https://www.reddit.com/r/LocalLLaMA/comments/1xt82mz/microsoft_research_backdoored_an_ai_agent_by/

## Also noted

- **Deletion Tuning goes public**: NYU/University of Calgary researchers released code supporting their NeurIPS 2026 paper "The Surprising Effectiveness of Deleting Weights in LLM Post-Training" — zeroing under 0.05% of weights with no other modification matches full fine-tuning across RLVR, SFT and on-policy distillation (Gate.com coverage Wed ~00:00 ET; project page agenticlearning.ai, NeurIPS poster 154971). Cheaper post-training is good news for small-lab and local fine-tuners.
- **LLMs Play Zachtronics' TIS-100**: benchmark harness having models write TIS-100 assembly, scored on a leaderboard — HN front page Tue 7:37 PM ET (75 pts): https://tis-100.wincon.dev/
- **osssaysno.com**: tracker cataloguing OSS projects banning AI-generated contributions hit the HN front page (52 pts) Wed 3:51 AM ET — fresh aggregation of the trend first surfaced via r/opensource's ban: https://osssaysno.com

## Just missed the window

- **VulnCheck's Glasswing November audit** (blog Nov 9; HN front page yesterday 5:17 PM ET, 411 pts): seven months into Project Glasswing — 442 Anthropic-attributed CVEs (~0.5% of 2026's CVEs), only 2 in KEV; ledger shows 37,966 findings but 217 fixed, 1,654 duplicates, 419 withdrawn while claimed true-positive rate rose to 94.2%; curl's maintainer has so far marked 19 of 34 published OSS-Scanner reports "not a vulnerability": https://www.vulncheck.com/blog/anthropic-glasswing-november-update
- **NEAR AI breach via SGLang RCE** (incident report published Monday Nov 9; THN/theblock coverage Tuesday): attacker got code execution on one SGLang inference server, exposing ~100 users' account details, 18 legacy agents' data and two customer API keys — and could observe "confidential" requests, since SGLang itself runs inside the TEE: https://near.ai/blog/security-incident-report-sglang-vulnerability — https://thehackernews.com/2026/11/sglang-rce-exploit-breaches-near-ai.html
- **Microsoft lobbies against Chinese optical-transceiver import ban** (Reuters, Tue morning Nov 10): Microsoft and industry groups pressed the Trump administration to drop the planned ban on Chinese transceivers used in AI data centers, warning of build-out delays: https://www.reuters.com/business/media-telecom/microsoft-presses-trump-scuttle-expected-ban-chinese-data-center-technology-2026-11-10/
- **IBM × Microsoft "Imagine Enterprise"** (announced Tue ~9 AM ET): agentic platform combining IBM Consulting's 6,000+ prebuilt agents with Azure/Copilot Studio/security plus forward-deployed engineers: https://newsroom.ibm.com/2026-11-10-ibm-and-microsoft-announce-a-new-ai-agent-platform-designed-for-industry-specific-transformation
- **OpenClaw v2026.11.3 + extended-stable v2026.9.37** (released Tue 1:53 PM / 3:42 PM ET, hours before this window): https://github.com/openclaw/openclaw/releases

## Coverage notes

- Verified in-window item entries: 9 (3 leads read in full, 2 minor items, 2 engine rows, 2 also-noted community/research rows), each from a fetched page with an in-window timestamp; Agent tools had zero qualifying items. Plus 5 "just missed" footnotes (all first-time; none previously footnoted).
- Ledger dedupe: ~9 candidates suppressed as already reported — vLLM #61498/#66892 (reported 11/10 17:00), llama.cpp #32017 (11/10), Ollama v0.40.17-rc0 (11/10), Codex 0.167.1 stable (pre-window), Apple local-AI Mac app (same event as apple-harbor 11/10), MiniMax H3-Image (missed-tagged), ERNIE 5.5-Flash-Base (missed-tagged; today's date-commit listed as an update), Anthropic IPO price range (reported 11/10 07:00), Meta Muse on Apple Watch (pre-window Tue).
- Excluded as unverifiable/out-of-scope: "Claude Mythos ported to AMD MI355X in 3 days" (no primary write-up — explainx's own fact-check found none), Tendrils $30M CPU round (general-purpose compute, outside local-inference scope; disclosure time unconfirmed), IFM PAN2 world model (repo dates Oct–Nov 9, re-surfaced), ZONOS3-Mini TTS and Expo Exact (both Monday Nov 9 disclosures — beyond "just missed").
- Index issues: Techmeme's Nov 11 river not yet published at 07:00 ET (404); HF newest-models API returned unfiltered hobby repos (r/LocalLLaMA + HN carried the overnight signal instead); DDGS search errored intermittently (Nous fallback used). Two-strike rule logged no dead candidates this run.
- Window honored: all timestamps derived from the ET helper (2026-11-11 07:00 EST), engine merge/commit dates from the GitHub API.
