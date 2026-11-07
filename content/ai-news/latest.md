# AI News Briefing — Saturday, November 7, 2026

**Window:** 2026-11-06 17:00 EST → 2026-11-07 07:00 EST (overnight Friday→Saturday; 14 hours). New items only, deduped against the reported ledger.

## TL;DR

- **Anthropic published its first public account of Claude's agent memory architecture** — memory notes, learned salience judgement, and "dreaming" (sleep-time consolidation over session logs). On Continual Learning Bench, enabling memory took Opus 5.5 from a mid-0.6 reward to ~0.83; in Pokémon, a 2024-era model wrote 413 memory files for 1 badge while Fable 5 wrote 9 files for 4 badges.
- **A reproducible ChatGPT routing flaw put one connector's one-time confirmation code inside a different connector's OAuth context** — the user was never told the follow-up tool call switched apps; only a server-side binding stopped it. The researcher says OpenAI's bounty process demanded biometric ID verification up front, so he published instead.
- **Kimi K3.1 Pro is now mainlined in llama.cpp** (PR #31779, merged in-window) alongside nine other merges and eight builds through b12188 — the 2.8T/104B MoE is runnable in the reference local engine, if your hardware dares.
- **OpenAI opened outbound SIP telephony for GPT-Live to all organizations**, letting any org dial phone numbers through its own trunk for voice agents.
- **Update on the Nvidia/Alice Huang affair:** the FT reports US officials believe Jensen Huang had a romantic relationship with the Megaspeed executive intelligence agencies concluded was recruited by China's MSS.

---

## Models and local inference

### Kimi K3.1 Pro mainlined into llama.cpp; eight builds to b12188 overnight
**Event date:** November 6–7, 2026 (merges and builds inside the window).

`model: add Kimi-K3.1-Pro text model` (PR #31779) merged inside the window, giving Moonshot's 2.8T-parameter/104B-active MoE — whose open weights landed November 4 and which this brief has already covered — day-0 support in the reference local engine. Nine further llama.cpp PRs merged in the same 14 hours, including a CUDA fix for shared-memory bank conflicts in the lightning indexer, a Hexagon kernel fusing the post-norm residual add into `RMS_NORM_MUL`, a Metal `GROUP_NORM` fix for `ne03 > 1` non-contiguous inputs, `arange` on OpenCL, a WebGPU group-norm kernel, and a server change to visit parent slots before child slots. Eight releases shipped in-window (b12180 at 18:18 ET Friday, then b12182–b12188 between 22:30 ET Friday and 06:58 ET Saturday).

**Why it matters:** llama.cpp support is the gate that decides whether a frontier-class open-weight model is a curiosity or a fixture of the local ecosystem; day-0 mainlining of K3.1 Pro means GGUF quants and speculative-decode work will follow within days. The indexer and Hexagon kernel work is the more practically useful change for anyone decoding MoE models on consumer or Snapdragon silicon.

`Source:` GitHub — ggml-org/llama.cpp merged PRs 2026-11-06T22:00Z→2026-11-07T12:00Z (10 merges; PR #31779) — https://github.com/ggml-org/llama.cpp/pull/31779
`Source:` GitHub — llama.cpp releases b12180 (2026-11-06 23:18:52Z) through b12188 (2026-11-07 11:58:44Z) — https://github.com/ggml-org/llama.cpp/releases
`Source:` Reddit r/LocalLLaMA — "model: add Kimi-K3.1-Pro text model … now you can run Kimi K3.1 Pro locally" — https://www.reddit.com/r/LocalLLaMA/comments/1xpqm0b/model_add_kimik31pro_text_model_by_usrlocalben/

### vLLM merged 27 PRs in-window, led by the Kimi K3.1 bring-up series
**Event date:** November 6–7, 2026.

The window captured 27 vLLM merges, headed by "[Model] Kimi K3.1: model files and NVIDIA specialized decode (Kimi K3.1 split 1/5)" and "[Rust Frontend] Kimi K3.1 chat renderer (split 2/5)". Other notable work: fusing the DSpark Markov bias and block combine into Gumbel sampling, warming DeepGEMM NVFP4 MegaMoE kernels before serving MiniMax-M3, a MiniMax-M3.1-Flash MI355X AgentX nightly recipe, one host buffer per rank with opt-in HugeTLB pages in KV offload, and a hardening fix returning HTTP 400 for images rejected by Pillow's decompression-bomb check.

**Why it matters:** the "split 1/5" numbering shows Moonshot's K3.1 enablement arriving as a planned multi-PR series rather than a one-off, which historically predicts fast convergence on quantized and specialized-decode paths. The Pillow bomb-check fix is a small but real input-validation hardening item for anyone exposing vLLM to untrusted uploads.

`Source:` GitHub — vllm-project/vllm, merged PRs in window (total_count 27) — https://github.com/vllm-project/vllm/pulls?q=is%3Apr+is%3Amerged

---

## Agent tools

### Anthropic's first public anatomy of agent memory: notes, salience, and "dreaming"
**Event date:** November 6, 2026 (Anthropic research post; HN front-page pickup 01:39 ET Nov 7, 61 points).

Anthropic laid out a three-part memory stack for Claude: writing salient **memory notes** during a session, learning to **judge what is worth remembering**, and **dreaming** — replaying logs during idle time to consolidate, correct wrong rules and build wiki-style representations. The post is unusually concrete. On a Continual Learning Bench 1.0 database task where each question starts a fresh session with only a persistent memory directory, Sonnet 4.6 produced only untested guesses, Opus 4.7 consolidated into one reference document with confirmed/unconfirmed flags, and Opus 5 was the first model to actually close the learning loop; Opus 5.5 and Sonnet 5.5 now match Opus 5 with memory enabled. Secondary coverage of the same post reports Opus 5.5's mean CL-Bench reward rising from ~0.64–0.66 to ~0.84 with memory. In Pokémon Red at ~10,000 steps, Sonnet 3.7 wrote 413 memory files across 225 folders and earned one badge; Fable 5 wrote nine files plus a `global.md` and earned four.

**Why it matters:** this is the clearest published design document yet for giving agents persistent, self-correcting memory without touching weights — and the model-by-model progression tells you the capability is emerging from training, not scaffolding. The open questions Anthropic names (when to write, how to keep memory from drifting, how much compute dreaming deserves) map exactly onto what every agent framework team is currently guessing at.

**Local inference:** the memory stack is weight-agnostic — the salience and dreaming behaviours are demonstrated on Claude models, but the pattern (folder of notes + idle-time replay) is directly portable to a local Qwen3.8-27B or Kimi-class agent, and Anthropic links cookbooks for dreaming and knowledge wikis.

`Source:` Anthropic Research — "The components of agent memory," 2026-11-06 — https://www.anthropic.com/research/components-of-agent-memory
`Source:` Hacker News — front page, submitted 2026-11-07T06:39:04Z (61 points, 31 comments; story id 50402212) — https://news.ycombinator.com/item?id=50402212
`Source:` Let's Data Science — "Anthropic details how Claude uses dreaming…" (CL-Bench 0.655→0.838; wiki 13%→48%), 2026-11-07 — https://letsdatascience.com/news/anthropic-details-how-claude-uses-dreaming-to-organize-agent-86d1704a

### OpenAI opens outbound SIP calling for GPT-Live to all organizations
**Event date:** November 6, 2026 (surfaced on Hacker News at 18:03 ET; docs page undated).

OpenAI's Telephony and SIP guide now states plainly that "Outbound SIP calling is available to all organizations through the Live API, not the Realtime API call-creation endpoint." Operators create a session with an E.164 destination, a `sips:` trunk URL, SIP digest credentials and a caller number; GPT-Live carries the conversation while the customer's SIP provider handles the phone network. Inbound flows use a `live.transport.incoming` webhook plus accept/reject endpoints, a sideband WebSocket for transcripts/tools/commands, DTMF in both directions, and `refer` for transfers — with TLS signalling, mandatory SRTP media, GeoIP-routed `sip.api.openai.com` / `sip-eu.api.openai.com` endpoints and published EU data-residency paths.

**Why it matters:** this removes the waitlist-style gate on one of the last bottlenecks for production voice agents — dialing out and getting call control (transfer, keypad, hangup) from your own backend, with European residency available. It also makes the security surface of a phone-answering agent your problem: the docs explicitly tell you to treat `sip_headers` as untrusted caller metadata and to assign exactly one owner per side effect so duplicate webhook deliveries don't execute tools twice.

`Source:` OpenAI Developer docs — "Telephony and SIP" (Live API; GPT-Live), retrieved 2026-11-07 — https://developers.openai.com/api/docs/guides/voice-sip
`Source:` Hacker News — "Outbound SIP calling is now available to all organizations," submitted 2026-11-06T23:03:55Z (64 points, 54 comments; story id 50399695) — https://news.ycombinator.com/item?id=50399695

---

## AI security and governance

### ChatGPT silently routed a follow-up tool call into a different connector's OAuth context
**Event date:** November 6, 2026 (researcher's thread published and posted to Hacker News at 17:50 ET; 89 points).

A user with two ChatGPT connectors pointing at the same MCP server — one custom (developer mode), one from the app directory, each with its own OAuth client — requested an action requiring confirmation. The model called the confirmation tool through connector A, whose widget showed the change and produced a one-time code via `ui/message`; the model then made the follow-up call carrying that code **through connector B**, with nothing shown to the user. Only the MCP server's own binding of the code to connector A's OAuth client stopped the redemption, so every attempt in that conversation failed. The reporter's conclusion: connector identity, not shared MCP URL, is the authorization boundary, and follow-ups must be pinned to the client that started the flow. He also alleges OpenAI's bounty process demanded biometric identity verification — "even to another party I do nothing about" — before accepting the report, which is why he published instead.

**Why it matters:** this is the cross-app data-flow failure mode that MCP security work has been circling: an agent's tool router is an authorization boundary, and users cannot audit a switch they are never told about. The benign outcome here depended entirely on a third-party server doing the right thing — with an unrelated third-party connector behind the same URL, one app's confirmation token could have crossed to another provider. Until OpenAI addresses it, the practical mitigations are the ones in the thread: bind pending operations and confirmation codes to the originating connector and OAuth grant, forbid automatic connector switching mid-flow, and log routing decisions.

*Single primary source; OpenAI has not publicly responded. Treat the bounty-process claim as the reporter's account.*

`Source:` OpenAI Community — "Security: Follow-up tool call silently routed to a different connector" (full reproduction, expected behaviour) — https://community.openai.com/t/security-follow-up-tool-call-silently-routed-to-a-different-connector/1413053
`Source:` Hacker News — submitted 2026-11-06T22:50:43Z (89 points, 36 comments; story id 50399569) — https://news.ycombinator.com/item?id=50399569

### Update on previously reported: FT says US officials believe Huang had a romantic relationship with the MSS-recruited executive
**Event date:** November 6, 2026, 21:50 ET (Techmeme listing; FT report by Demetri Sevastopulo). **Ledger entry:** `megaspeed-alice-huang` (footnoted Oct 28; NYT MSS-recruitment development reported by this brief on Nov 6).

The FT reports that US officials believe Nvidia CEO Jensen Huang had a romantic relationship with Alice Huang, the Singapore-based Megaspeed executive that US intelligence concluded was recruited by China's Ministry of State Security to gather information on him — including pressure to lobby for looser export controls on advanced AI chips. Reporting on the relationship has circulated before (the NY Post described a "close relationship" on October 29); what is new in-window is the FT's attribution of the romantic-relationship assessment to US officials and the memo framing, on top of the November 6 NYT recruitment finding and the House Democrats' demand for a closed-door Huang briefing on November 18.

**Why it matters:** this moves the story from a corporate-intelligence leak concern to a personal-leverage vector attached to the single most consequential figure in AI compute supply — the policy question being whether export-restriction advocacy was ever Nvidia's own. For chip-supply and DGX-class hardware planning, the operative risk is now compliance scrutiny and congressional process, not the underlying business.

`Source:` Financial Times — Demetri Sevastopulo, "US officials believe Jensen Huang had a romantic relationship…" (via Techmeme river, 2026-11-06 9:50 PM ET) — https://www.ft.com/content/a55a2a22-4479-4f93-a445-6147a5c6d790
`Source:` OfficeChai — "NVIDIA CEO Jensen Huang Had A Romantic Relationship With Alleged Chinese Spy, FT Reports" — https://officechai.com/ai/jensen-huang-had-a-romantic-relationship-with-alleged-chinese-spy-alice-huang-ft-reports/

---

## Also noted

- **Google took over Crusoe's southeast Wyoming data-center site** (Crusoe newsroom + Bloomberg, November 6, 2026, 18:15 ET) — an **update on previously reported** `wyoming-17b`: the day after Google announced its $17B Wyoming investment, Google bought the site out itself after Crusoe failed to lock in customers; Crusoe (6+ GW contracted, 40+ GW pipeline) remains developer of Google's Armstrong County, Texas campus. https://www.crusoe.ai/resources/newsroom/crusoe-wyoming-data-center-campus-update
- **Harness Bench leaderboard is live and refreshing in-window** — a community tracker ranking coding-agent harnesses (Kimi Code, Hermes, Pi, Claude Code, Codex and others) against Terminal-Bench 2.1 (87/89 tasks) and DeepSWE v1.1 (111/113 tasks), holding model and reasoning effort constant and varying only the harness. Space created October 15, last modified 2026-11-07T11:59Z. https://huggingface.co/spaces/joelniklaus/harness-leaderboard
- **Ollama, SGLang and vLLM shipped no releases in-window** — latest remains Ollama v0.40.15 (Nov 3), SGLang v0.5.23 (Nov 2), vLLM v0.33.0 (Nov 2); only llama.cpp was active on the release channel.

## Just missed the window

- **Censys, "ARTEX: Agentic Asbestos" (November 5):** 1,465 internet-facing hosts exposing the ARTEX AI pentest console (~815 distinct deployments, 32 open directories leaking attack kits, 155 hosts co-running other threat tooling; scanned November 3–4); ARTEX was released in July and is already tied to two reported agentic intrusions this brief has covered, and its developers are moving closed-source. https://censys.com/blog/artex-agentic-asbestos
- **Preferred Networks released PLaMo 3 Translate open weights (November 4–5):** 0.8B/2B/8B/31B translation-specialized LLMs built on PLaMo 3 NICT, 53 languages, 32,768-token combined context, PLaMo Community License (free personal/academic; free commercial up to ¥1B revenue with registration).
- **Zellige AI's Tessera-27B-B-CJK-RL (repo November 4, last modified November 6 13:39 ET):** Apache-2.0 coding agent fine-tune of Qwen3.8-27B that thinks in compressed CJK, with eight RL updates on Tinker — 108/126 correct on AIME+HMMT (vs 97 for the SFT version) while keeping ~0.67x the base model's thinking tokens per agent turn; merged bf16 55.6 GB plus NVFP4/FP8 at 23.4 GB and untested GGUF quants. https://huggingface.co/ZelligeAI/Tessera-27B-B-CJK-RL
- **Apple's undocumented personal knowledge graph (post dated October 28; Hacker News front page 04:46 ET November 7, 120 points):** researchers documented `knowledgeconstructiond`/`knowledged` building a triple store of 181 entity classes and 278 predicates from Contacts, Photos, Calendar, Messages, Mail, Family Sharing, location and Screen Time across 51 plaintext SQLite databases in `~/Library/IntelligencePlatform`, served to LLMs — and released iKnow, a local dashboard to read it. https://8rank.ai/game/apple-intelligence-personal-knowledge-graph
- **Six pallets of Nvidia hardware worth $11.4M vanished in transit from Oakland to Texas** (ABC7 November 5; Ars Technica/Tom's Hardware November 6) — shipped August 29 on a truck posing as a food-transport company, reported to Oakland police September 13, no arrests or recovery. https://arstechnica.com/gadgets/2026/11/nvidia-hardware-worth-11-4m-vanished-between-oakland-and-texas/

## Coverage notes

**Verified and listed: 9 in-window items** — counting unit: one line per underlying event, comprising 6 in the category sections (one of which is an explicit "update on previously reported") and 3 "Also noted" lines (one of which is the community tracker) — each from a fetched page carrying an in-window date or timestamp, with leading items read in full. **5 further events footnoted as just-missed** on their original disclosure dates, for 14 ledger lines this fire.

**Ledger dedupe — 12 candidates suppressed as already reported:** Huawei Qinyun HM650 desktop (repeat "just missed"), Meta Muse-Wonder specialized models (repeat "just missed"), TSMC 3D DRAM at Nvidia's request, OpenAI's additional ~$11B at $1.25T, Google's $17B Wyoming announcement (reported here only as the Crusoe buyout update), Nous Arete-27B, Xing4.0-29B-A4B GGUFs, Cactus Mirage 22MB VLM, two re-shares of the weight-space-programming thread, the NYT/Alice Huang MSS-recruitment base story (reported only as the FT update), the Kimi K3.1 Pro weight release itself (reported only as the llama.cpp milestone), and Sequoia shutting down Scouts.

**Dead ends and limitations:** the TechCrunch scoop for the Censys ARTEX finding (`techcrunch.com/2026-11-06/hundreds-of-artex-ai-hacking-tools-found-publicly-exposed-to-the-web/`) 404s on direct fetch even though the headline resolves from TechCrunch's own index, so ARTEX is cited from Censys and secondary outlets; the Anthropic memory post carries a date but no clock time, so it is placed on its in-window HN front-page pickup; OpenAI's SIP guide is an undated docs page, corroborated by the timestamped HN submission; the Nikkei Asia item on Nasscom's AI-and-hiring warning appears only as a URL-less Techmeme river line and was not resolvable in budget; Reddit's anonymous feed returns no scores, so r/LocalLLaMA ranking is qualitative. Weekend overnight cadence: no closed-weight model releases were disclosed in the window, and no lab shipped a versioned inference release after 17:00 Friday except llama.cpp.
