# AI News Briefing — Sunday, October 25, 2026

**Window:** Saturday, October 24, 17:00 ET → Sunday, October 25, 2026, 07:00 ET (overnight sweep; previous fire Oct 24, 17:00 ET).

## TL;DR

- **Moonshot's Kimi K3.1 Pro is heading for the top open-weights crown — and a paid-influencer scandal.** VentureBeat's deep dive (Saturday 8:31 PM PT) puts it 4th on the Artificial Analysis Intelligence Index at 55 with weights due November 4, while screenshots circulating on X since Saturday allege a creator campaign required pre-approved copy and told influencers not to disclose paid partnerships.
- **A training-free harness called GVS5H has five local Qwen3.8-27B instances matching Claude Fable 5 on hard LiveCodeBench (92.4% vs 90.4%)** — a top-165 HN story overnight and directly relevant to single-GPU coding agents.
- **llama.cpp mainlined native DeepSeek-V4.1-Flash support** in b11870 and shipped a server-hardening fix rejecting pathologically nested JSON that was crashing llama-server.
- **vLLM merged 19 PRs overnight**, headlined by deprecating the LM Format Enforcer structured-outputs backend and landing Kimi-K3/MiniMax-M3 tuned presets ahead of the November 4 weights drop.
- **Axios: lawmakers across parties now treat AI leaders' boom/doom swing as a political liability**, with named criticism aimed at Sam Altman and Dario Amodei.

## Models and local inference

**Kimi K3.1 Pro: near-certain open-weights leader by November 4 — shadowed by undisclosed-promotion allegations** — *event date: Saturday, October 24 (VentureBeat analysis published Oct 24, 8:31 PM PT; allegations surfaced on X Oct 24)*
Moonshot's 3-trillion-parameter post-trained version of Kimi K3 currently scores 55 on Artificial Analysis's Intelligence Index (4th overall, ahead of GPT-6 Astra, DeepSeek V4.1-Flash-1017 and Meta's Muse Wonder), with the biggest jumps in agentic work: Terminal-Bench 4.0 13%→67% and CyberGym-E2E-AA 58%→95% versus Kimi K3. API pricing is $2/$10 per million input/output tokens — half of Claude Opus 5.5 — and full open-license weights are promised November 4. At the same time, screenshots posted Saturday by accounts on X (reproduced by VentureBeat and RuntimeWire) allege a creator-marketing brief for the launch that standardized promotional talking points and told participants not to disclose the commercial relationship, reportedly under NDA; the brief's issuer is unknown and the screenshots are not independently authenticated. Reuters reported Saturday that Moonshot deliberately stripped cyber-related data from the training corpus and RL environments — its internal cyber benchmark fell to 81.0% from 90.5% in an unfiltered version — under a stated policy of "not to release open weights that would extend the absolute frontier of cyber capability."
*Why it matters:* If the benchmarks hold, November 4 gives the open-weights ecosystem its first genuine frontier coding model — but a disclosure scandal lands badly just as enterprises weigh Chinese open-weight adoption under the DHS DeepSeek-removal precedent.
Source: VentureBeat — Carl Franzen, Oct 24, 2026 8:31 PM PT — https://venturebeat.com/technology/moonshots-kimi-k3-1-pro-flirts-with-open-weights-leadership-amid-paid-influencer-scandal-allegations
Source (second): RuntimeWire — Oct 24, 2026 — https://runtimewire.com/article/kimi-k31-pro-creator-marketing-disclosure-claims
Source (cyber curbs context): Reuters — Eduardo Baptista, Oct 24, 2026 — https://www.reuters.com/technology/moonshot-curbs-hacking-ability-new-kimi-model-fears-grow-over-ai-powered-2026-10-24/

**GVS5H: five orchestrated Qwen3.8-27B models match Claude Fable 5 on hard LiveCodeBench — on a single GPU** — *event date: surfaced to the HN front page Sunday ~03:56 UTC (165 points)*
The GVS5H ("Gorilla vs. 5 Humans") project introduces ledger-based zero-shot self-orchestration: fresh instances of one model decompose a problem and coordinate through a shared filesystem holding a plan, notes and the current solution — no training, no manager prompt. Across eleven models on the 100 latest hard LiveCodeBench problems, gains reach 25.6 points on pinned backends; locally served, open-weight Qwen3.8-27B rises from 66.8% to 92.4% pass@1, slightly above Fable 5's 90.4%, while orchestrated Qwen3.8 Flash Next hits 93.0% at claimed 9% of the cost. Code is MIT-licensed, paper CC-BY, with run artifacts included; gains are explicitly not universal across models.
*Why it matters:* Inference-time organization — not bigger weights — closing the frontier coding gap is the kind of result that changes what a self-hosted coding agent on one GPU can be expected to do.
Source: GitHub — slee-persis/GVS5H, fetched Oct 25, 2026 — https://github.com/slee-persis/GVS5H
Source (HN discussion): News.ycombinator.com, Oct 25, 2026 — https://news.ycombinator.com/item?id=50228167

**llama.cpp b11869–b11871: DeepSeek-V4.1-Flash mainlined; nested-JSON crash fix** — *event date: Saturday 21:18 UTC → Sunday 06:28 UTC*
Three in-window builds. b11870 adds native DeepSeek-V4.1-Flash ("deepseek41") model support — compressed-KV read layers computed once in the cache, Engram rows gathered in-graph like qwen4exp, Metal fusion baseline added (PR #31086, co-authored by Daniel Han). b11871 fixes a server-crashing flaw: deeply nested request JSON (hundreds of levels on macOS, ~10,000 on Linux) overflowed the HTTP thread stack and killed llama-server; the schema builder, jinja engine and decision code now reject inputs nested past 128 levels with a 400 (PR #30242). b11869 fixed jinja select/selectattr argument handling. Post-release Sunday-morning commits fuse FFN gate/up matmuls in CUDA MMQ and add OpenCL q2_k support.
*Why it matters:* Native V4.1-Flash support removes the plugin/branch workaround for the most popular open-weight MoE of the month, and the JSON-depth fix matters to anyone exposing llama-server beyond localhost.
Source: GitHub releases — Oct 24–25, 2026 — https://github.com/ggml-org/llama.cpp/releases

**vLLM: 19 merges overnight — LM Format Enforcer deprecated, Kimi-K3 presets land pre-weights** — *event date: merges Oct 24 21:00 UTC → Oct 25 09:22 UTC*
The overnight merge batch (19 PRs) headlined the formal deprecation of the LM Format Enforcer structured-outputs backend (#63423), new InferenceX/AgentX tuned presets for Kimi-K3 and MiniMax-M3 (plus an aligned Kimi-K3 MI355X ROCm recipe, #63520), a cluster of KV-offload correctness fixes (#63266, #61780, #60810), MegaMoE LoRA backend validation (#63496), and a DeepGEMM pin bump.
*Why it matters:* Deprecating a second structured-outputs backend consolidates vLLM on grammar-native paths, and tuned Kimi-K3 recipes appearing before November 4 signals the weights release is being engineered for day-one serving.
Source: GitHub — merged PR search, Oct 24–25, 2026 — https://github.com/vllm-project/vllm

## AI security and governance

**Axios: AI leaders' boom/doom whiplash becomes a political liability** — *event date: Sunday, October 25, 2026, 7:00 AM ET*
Maria Curi reports that lawmakers and Hill staffers in both parties say AI executives are becoming a political liability as they swing between prosperity and existential-danger claims. Utah Sen. John Curtis says no group is more "disconnected from the lives of ordinary Americans" than the AI industry; Rep. Ro Khanna said "you can't trust billionaires to make the decisions that keep us safe," aimed at Sam Altman; Sen. Bernie Moreno blasted Dario Amodei's "fearmongering rhetoric." The piece also recounts Anthropic's late disclosure of a model security incident affecting US government websites (notified October 8, blogged the next evening) and its retracted federal-preemption clause, OpenAI's state-preemption lobbying, and an ongoing FTC investigation into both companies.
*Why it matters:* With the FTC probe, Anthropic's draft-bill fallout and the midterms framing this, Washington's patience is thinning just as OpenAI/Anthropic/Nvidia posted record lobbying quarters — expect faster, harsher moves on disclosure obligations.
Source: Axios — Maria Curi, Oct 25, 2026 7:00 AM ET — https://www.axios.com/ (via Techmeme river; syndicated full text at BigGo)
Source (fetched copy): BigGo Finance — Oct 25, 2026 — https://finance.biggo.com/news/8c16497e-0dd9-4727-b026-13b0b27a2b4a

## Also noted

- Apollo's chief economist Torsten Slok (via Techmeme, Sat 5:30 PM ET): outside tech, no financial payoff from AI yet — now viewed as a margin-hurting cost center while VC-backed firms burn more tokens.

## Just missed the window

- **LiteLLM root-cause report (Friday):** the Oct 21 v1.107.0 PyPI/container backdoor traced to a LiteLLM Agent Platform master key committed to a public repo May 27 and never revoked; attacker used it Oct 10 to grab GitHub CLI OAuth tokens from logs and hijack the release pipeline; malicious build harvested cloud creds, SSH keys, browser data and AI-tool auth files.
- **PHP second RCE warning (Friday, Oct 23):** the PHP team flagged CVE-2026-115560 as a possible unpatched code-execution risk with mitigations only — two days after the emergency PHP2Shell patches (The Hacker News).
- **CISA KEV (Friday):** added Collabora Online Built-in CODE Server and Arista VeloCloud Orchestrator flaws to the Known Exploited Vulnerabilities catalog, both under active exploitation.

## Coverage notes

- **Verified:** 5 reported items (12 source pages fetched and date-verified) + 1 also-noted + 4 missed-window footnotes; 2 lead stories (Kimi, GVS5H) read in full.
- **Ledger dedupe:** ~8 in-window candidates suppressed as already reported or pre-window re-coverage — Kimi K3.1 Pro launch itself and Artificial Analysis placement (ledger Oct 23), OpenAI no-internet training / THN "VM escapes" re-coverage (ledger Oct 24), OpenClaw v2026.9.34 (ledger Oct 24), sglang v0.5.22 (ledger Oct 19), Ollama v0.40.11 (ledger Oct 24), Apricitas creative-jobs analysis (published Sep 27, HN re-spike only), CoreWeave chip-backed debt (Bloomberg Friday Oct 23), plus the four footnoted misses above.
- **Dead ends / limitations:** Saturday is a thin news day — no closed-weight model releases found in-window; The Hacker News' October 25 lead was a re-report of the already-ledgered OpenAI training post and was dropped. Bloomberg and Axios originals are paywalled/403 to direct fetch — syndicated copies fetched and cited alongside the originating outlet. Reuters dated the Moonshot cyber-curbs piece Saturday, but the underlying disclosure was at Friday's launch, so it appears only as context on the Kimi item.
