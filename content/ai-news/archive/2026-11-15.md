# AI News Briefing — Sunday, November 15, 2026 (early sweep)

**Window: Sat Nov 14, 17:00 EST → Sun Nov 15, 07:00 EST** (previous fire → this fire). New-items-only; deduped against the reported.md ledger.

## TL;DR

- The Wall Street Journal details how Jensen Huang built a multibillion-dollar Southeast Asia data-center business that kept China in the AI race — published hours after his resignation; Singapore-linked accounts now dispute how their statements were used.
- The New York Times reports Commerce Secretary Howard Lutnick pushed to weaken the pending chip rules covering Southeast Asian data centers while his family firm, Cantor Fitzgerald, banks the very operators — including Bitdeer's A201 facility in Johor, Malaysia.
- OpenAI staff confirm Codex's Plan Mode is being deprecated — "we don't plan to make any new investments in it."
- The 8×V100 lawyer is back: his DeepSeek-V4.1-Flash server now runs ~39% of experts at near-NVFP4 precision (Volta Max uPLIft), trading throughput for fidelity.
- Engines busy overnight: llama.cpp mainlined Inkling 2 (text+vision) support and shipped through b12412; vLLM landed ROCm SDMA pipeline hand-off and DSv4.1 FlyDSL FP8 kernels; SGLang took 42 commits.

## AI security and governance

**WSJ: inside the "backdoor route" — Huang's Southeast Asia data-center machine for Chinese customers** — Event date: Nov 14, 2026 (9:00 PM ET).
Reporting from Senawang, Malaysia — a site stretching 117 football fields where Chinese customers plan to rent Nvidia's most advanced AI chips — the Journal details how Jensen Huang cultivated a network of Asian partners that served Chinese firms "far from Washington," exploiting the gap left after the US banned direct advanced-chip sales to China. The piece ties the ecosystem to Megaspeed and the Alice Huang affair, notes Nvidia reported $12.4B in China revenue in the six months through July 26 (~7% of global), and argues the scandal now threatens to "implode" the whole structure.
*Why it matters:* This is the first outlet to map the commercial architecture behind the export-control loophole as its own story, days before Trump–Xi meet in Shenzhen — and it raises the stakes on the remote-access rule Commerce is finalizing.
*Local inference:* No direct connection.
*Note:* An HN-submitted X post from a Singapore-linked account (@brianchau57) alleges the WSJ fabricated statements attributed to the Singapore government; unverified.
Source: The Wall Street Journal — "How Jensen Huang Built the Backdoor Route That Keeps Beijing in the AI Race" (Nov 14, 2026, 9:00 PM ET) — https://www.wsj.com/tech/jensen-huang-backdoor-ai-data-centers-a5e4499d
Source (HN discussion) — https://news.ycombinator.com/item?id=50504083
(Update on previously reported: ledger megaspeed-alice-huang)

**NYT: Lutnick pushed to weaken the chip rules his family firm has stakes in** — Event date: Nov 15, 2026 (~6:40 AM ET).
The Times finds Commerce Secretary Howard Lutnick has been advocating the least stringent version of the pending rules on chip sales to Southeast Asian data centers serving Chinese customers — a scaled-back proposal that would cover only Singapore, Malaysia, Thailand, Indonesia, Brazil, South Africa and Norway, apply only to future shipments, and require notification (not licensing) when Chinese customers train there. Meanwhile Cantor Fitzgerald, now run by his sons, was designated sales agent for Bitdeer's up-to-$1B equity offering — Bitdeer operates the A201 data center in Johor that is expected to serve Tencent — and has had financial ties to data-center operators (Gorilla Technology, Freyr AI) and Nvidia partners (IREN, Lancium) that the rules would touch. The proposal was first reported by The Information.
*Why it matters:* The rule Commerce is finalizing (ledger: missed:commerce-remote-access-rule) is the main remaining lever on the China compute loophole; a country-limited, notification-only version is trivially routed around, and the conflict-of-interest layer now has named facilities and dollar figures.
Source: The New York Times — "Lutnick Pushed to Weaken Chip Rules That Overlap With His Family Business" (Nov 15, 2026) — https://www.nytimes.com/2026/11/15/us/politics/lutnick-pushed-to-weaken-chip-rules-that-overlap-with-his-family-business.html
Source (full-text mirror read) — https://dnyuz.com/2026/11/15/lutnick-pushed-to-weaken-chip-rules-that-overlap-with-his-family-business/

## Agent tools

**OpenAI confirms Codex Plan Mode is being deprecated** — Event date: Nov 15, 2026 (1:44 AM ET).
Responding to a feature request for a "compact context and implement plan" option, OpenAI's Ethan Traut wrote: "We will be deprecating plan mode soon, so we don't plan to make any new investments in it." The issue (openai/codex#18490) was closed with that comment.
*Why it matters:* Plan Mode is a workflow many Codex users have built muscle memory around; an official statement of intent to remove it — with no announced replacement — is the signal to restructure agent prompting habits (e.g., external plan files or approval-gated tool use) before it disappears.
Source: GitHub, openai/codex issue #18490 comment by etraut-openai (Nov 15, 2026 06:44 UTC) — https://github.com/openai/codex/issues/18490#issuecomment-6890525683

## Models and local inference

**The 8×V100 lawyer's server returns: DeepSeek-V4.1-Flash at "Volta Max uPLIft" — 39% of experts near-NVFP4** — Event date: Nov 14–15, 2026 (measurements dated Nov 12; Tom's Hardware follow-up published no later than Nov 15).
Nick Mermiges (PLI Labs), the South Carolina trial lawyer running DeepSeek-V4.1-Flash on eight used V100s for legal work, has re-cut his 2-bit "Volta sPLIt" build: about 38.7% of the model's 15,360 routed experts (5,949) are upgraded to 4-bit NVFP4, chosen by usage frequency from his harness's traffic, with the rest staying 2-bit. He added 1TB of RAM and an RTX 3090, and traced earlier crashes to a bad PSU. Against Nvidia's own NVFP4 reference, same-next-token agreement rose to 95.1% (from 92.2%) and KL divergence fell to 0.079 nats (from 0.196); multiple-choice answers that flip dropped from 79 to 8 of 513. Cost: code output slowed to ~150–180 tok/s (from 215), legal writing to 105–93 tok/s ranges; running two concurrent requests lifts total throughput to 159 tok/s. He's now eyeing eight more V100s and a second server.
*Why it matters:* The most rigorous community fidelity measurement yet of mixed-precision expert promotion — real KL and token-agreement numbers against a vendor reference, not vibes — and a working recipe for getting near-4-bit quality out of unsupported (Volta) hardware.
Source: Tom's Hardware — "Lawyer upgrades eight-V100 private AI server to run 80% of DeepSeek V4.1 Flash at 4-bit NVFP4 precision…" (Nov 14–15, 2026) — https://www.tomshardware.com/tech-industry/artificial-intelligence/lawyer-upgrades-eight-v100-private-ai-server-to-run-80-percent-of-deepseek-v4-1-flash-at-4-bit-nvfp4-precision-adds-1tb-of-ram-an-nvidia-rtx-3090-and-fixes-crashes-caused-by-a-bad-psu
(Update on previously reported: ledger v100-lawyer-215tps)

**Engine round-up (overnight)** — llama.cpp 29 commits and builds to b12412, headline: Inkling 2 (text + vision) model support mainlined (#32217, 23:51 ET Sat), plus DFLASH2 speculative decode for Recursive Ripple-65B-Preview (#31904), RDNA3/RDNA4 prompt-processing Flash Attention optimizations (#31323) and narrow-N matmul improvements on Vulkan, and a CUDA cooperative-softmax register-keeping change (#28982). vLLM landed 17 merges: DeepSeek-V4.1-Flash dense MXFP8 GEMMs on AITER's tuned FlyDSL kernels (ROCm), a new ROCm pipeline-parallel SDMA copy-engine hand-off (--pp-sdma-handoff, #65658), and DeepGEMM/Triton block-FP8 MoE allowed with FlashInfer NVLink all2all (#67592). SGLang took 42 commits (GLM-5.3-Flash PD prefill context-parallel with native MTP #49089, MiniMax-H3 diffusion gated-residual fusion #50391, pluggable KV-cache dtypes #49717). Ollama quiet; OpenClaw churned 100+ commits, all fixes/refactors.
*Local inference:* The Vulkan RDNA3/4 prefill work and vLLM's ROCm SDMA/FlyDSL changes are the ones to watch for 50-series and MI355X-class boxes.
Source: GitHub — https://github.com/ggml-org/llama.cpp/releases ; https://github.com/vllm-project/vllm ; https://github.com/sgl-project/sglang

## Also noted

- **Anthropic CVP "Defense Access" reportedly reduced to KYC-only for individuals** (unverified community report, HN Nov 15 ~12:49 AM ET): applicant jcomp says the application no longer requests CVEs or business-registration details for individual Defense Access, now requiring only third-party KYC; the support doc (dated Nov 11) doesn't reflect a change. — https://news.ycombinator.com/item?id=50504103
- **Leech-lattice quantization claimed at ~2.7 bpw on Qwen3 4B/8B/14B**, reported to beat IQ2_S at equal size (r/LocalLLaMA, Sat 5:10 PM ET; self-published, no third-party KL curves yet). — https://www.reddit.com/r/LocalLLaMA/comments/1xwerbo/qwen3_4b_8b_14b_at_27_bits_per_weight_with_leech/
- **Show HN: Nano-Edge-TTS** — Kokoro-based TTS claimed at 70× realtime, lightweight (HF: drmhse/nano-edge-tts; HN Sun 6:53 AM ET). — https://huggingface.co/drmhse/nano-edge-tts
- **Show HN: Saylark** — free, open-source, fully-local Mac voice dictation as a Wispr Flow alternative (HN Sun 2:50 AM ET). — https://github.com/btahir/saylark

## Just missed the window

- **Cursor attributes the trojanized Claude Code extension to a stolen Fireworks AI key** — BleepingComputer published the confirmation Nov 14 at 12:27 PM ET (~4.5h before this window opened); The Hacker News followed Nov 15, saying the key exposed in the Fireworks breach let an attacker pull read-only bucket data and plant the backdoored extension (up to ~5k users). Related ledger entries: claude-code-2.1.313-rat, llmjacking-second-hack.

## Coverage notes

- Verified items this run: 10 (4 lead items incl. 1 engine cluster, 4 also-noted, 1 footnote) — counting units are per underlying event.
- Ledger dedupe: 9 candidates suppressed as already reported or previously footnoted (Italy Garante/Meta €805M; "engineered scarcity"/wccftech repost; Inkling-2 AA-31 rescore; Qwen4-Max preview chatter; Domyn EUROPA 1T-param [Nov 12]; TSMC $500M fund [Nikkei Nov 13]; doubleAI self-redesign post [Nov 6]; LFM2.5-24B-A2B hype [Oct 13]; Nov 13 aggregator re-coverage of ChatGPT Wallet/Hydrogen).
- Dead ends (two-strike rule): Bloomberg's Applied Digital tenant-guarantees story (7:05 PM ET Sat) never resolved to a fetchable copy; WSJ original is auth-walled (read via syndicated copies).
- Community lanes walked: HN river (both halves of the window), r/LocalLLaMA new (16 in-window posts), HF created/trending feeds (no in-window vendor weight drops — Chinese labs quiet on the weekend), GitHub releases/commits for llama.cpp/vLLM/SGLang/Ollama/OpenClaw.
