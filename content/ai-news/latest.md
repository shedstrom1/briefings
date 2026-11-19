# AI News Briefing — Thursday, 19 November 2026, Afternoon (17:00 ET)

**Window:** 2026-11-19 12:00 ET → 2026-11-19 17:00 ET (previous fire → this fire). New-items-only: events first disclosed in this window, deduped against the reported ledger.

**TL;DR**
- **Microsoft previews RDMA over USB4 on Windows** — two or more USB4 PCs pool memory and GPU compute to run open-weight models that don't fit on one machine; the NetworkDirect provider and a llama.cpp testbed are open source. The window's standout local-inference item.
- **Google ships Tool Retrieval in the Gemini API (preview)** — agents load tool schemas on demand instead of sending a full library every request, cutting input tokens and latency for tool-heavy agents.
- **Google brings computer use to the Gemini desktop app on macOS** — the agent drives native apps, files and the web in an isolated background window; US, paid tiers, Apple Silicon only.
- **Update on previously reported (Anthropic IPO):** ANT closed its first session at $97.17, up 17% — giving back more than half the opening pop of 34%.
- **Engine wave:** llama.cpp, vLLM and SGLang landed dozens of in-window commits, including a vLLM DoS-hardening fix; Ollama was quiet.

---

## Models and local inference

**Microsoft previews RDMA over USB4 on Windows — pool multiple PCs to run oversized open-weight models**
*Event date: 19 November 2026 (in-window).*

Microsoft's USB team opened a developer preview of **RDMA over USB4** for Windows. Connect two or more USB4-capable Windows PCs with USB4/Thunderbolt 4/5 cables and a compatible inference framework can split one model across their combined memory and GPU compute, using **NetworkDirect** — the same RDMA API Windows uses with datacenter NICs, so no RDMA adapter, switch or third-party driver is needed. Microsoft measured small-message round-trip latency of roughly **28–50 µs, about 4× lower than UDP over the same USB4 link**. In company tests, three AMD PCs ran Qwen3-235B-A22B at ~18.5 tok/s; Intel's GPT-OSS-120B went from 49.4 tok/s on three PCs to 54.2 on four. The NetworkDirect provider, samples and link-test tools are open source, with a llama.cpp-based testbed; access arrives "soon" to Windows Insiders in the Experimental channel and requires test signing (which disables Secure Boot). AMD and Intel collaborated on the work.

**Why it matters:** This attacks the central local-inference ceiling — a model too big for one card or one machine. RDMA over commodity USB4 ports offers a cheaper on-desk alternative to a server-class cluster, and an open-source provider plus llama.cpp testbed means the community can validate and extend it immediately.

**Local inference note:** Directly relevant. It is a developer preview (test signing, manual static addressing, full-mesh cabling) — a starting point, not plug-and-play.

Source: "Introducing RDMA over USB4® on Windows: run larger local AI models across PCs," Microsoft USB Blog, 19 Nov 2026 — https://techcommunity.microsoft.com/blog/microsoftusbblog/introducing-rdma-over-usb4%C2%AE-on-windows-run-larger-local-ai-models-across-pcs/4574602
Source: "Microsoft previews linking Windows PCs over USB4 to run larger AI models," Superpower Daily, ~14:00 ET 19 Nov 2026 — https://superpowerdaily.com/posts/microsoft-previews-linking-windows-pcs-over-usb4-to-run-larger-ai-models

**Engine wave (also noted, in-window).** Between 12:03 and 16:55 ET, llama.cpp landed three commits (OpenCL dp4a KQ matmul in decomposed attention #32577, BF16 for ROPE/SET_ROWS #32387, YuE2 projector fix #32606); vLLM merged ~18, including GLM-5.3-Flash FP8 on SM90 Mega MoE #66812, Kimi K3.1 GQA DSpark drafts #68701, a mixed-page-size KV layout for Nemotron Shortcut #67941, and **a DoS hardening fix enforcing `max_tokenize_tokens`** #67348; SGLang merged ~10, including an sgl-router least-tokens KV rank policy #51415 and FP4 indexer-key fusion #51515. Ollama was quiet. Source: GitHub commit histories, 19 Nov 2026 17:03–21:55 UTC — https://github.com/ggml-org/llama.cpp/commits/master , https://github.com/vllm-project/vllm/commits/main , https://github.com/sgl-project/sglang/commits/main

---

## Agent tools

**Google adds Tool Retrieval to the Gemini API (preview)**
*Event date: 19 November 2026 (in-window, ~13:38 ET).*

Google launched **Tool Retrieval** in preview in the Gemini Interactions API. Add a `tool_search` tool and set `defer_loading: true` on selected tools; the model still sees each tool's name and description but not its parameter schema. When a tool is needed, the model requests it by name, the API loads the schema, and the function is called — all in one request, with each step exposed in `interaction.steps` for debugging. Google says the design cuts input-token bloat, latency and tool-selection errors that grow with large tool libraries, and preserves the prefix cache because deferred schemas stay out of the prompt head. It works with remote MCP servers and supports client-side retrieval; there is no separate charge. Requires Gemini 3.6 or later, including all Gemini 4 models.

**Why it matters:** Tool selection is the main scaling wall for production agents with dozens or hundreds of tools. Server-side, on-demand schema loading with no index to host is a pragmatic fix that keeps accuracy from degrading as the toolset grows — especially relevant where tool registries are large or MCP servers expose many functions.

Source: "Introducing Tool Retrieval in the Gemini API," Google Developer Blog, 19 Nov 2026 — https://blog.google/innovation-and-ai/technology/developers-tools/gemini-api-tool-retrieval/
Source: "Google adds Tool Retrieval to the Gemini API to tame tool-heavy AI agents," Crypto Briefing, 13:38 ET 19 Nov 2026 — https://cryptobriefing.com/gemini-api-tool-retrieval-preview/

**Google brings computer use to the Gemini desktop app on macOS**
*Event date: 19 November 2026 (in-window, ~14:57 ET).*

Google announced **computer use** for the Gemini app on Mac. The agent navigates native macOS apps, local files and websites to complete multistep tasks — filling forms, batch-processing files, organizing folders, building slideshows from dashboard data — inside an isolated background window with a live preview, leaving the screen and cursor free. Users choose which apps Gemini may access, watch each step, pause at any time and approve key actions; logins and payments pause by default. It is English-only, US-only, requires an Apple Silicon Mac on macOS Sequoia 15.0+, a personal Google account for users 18+, and an AI Plus, Pro or Ultra subscription.

**Why it matters:** Computer use is moving from demos into mainstream desktop subscriptions. Google's gated, approval-first rollout is a bet that trust controls — not raw capability — will decide which agent users let drive their machine; its late arrival intensifies pressure on Apple to offer an equivalent.

Source: "Automate Mac tasks with Gemini computer use," Google Keyword blog, 19 Nov 2026 — https://blog.google/innovation-and-ai/products/gemini-app/computer-use-mac/
Source: "Google gives Gemini the ability to use your Mac for you," TechCrunch (Sarah Perez), 19 Nov 2026 — https://techcrunch.com/2026/11/19/google-gives-gemini-the-ability-to-use-your-mac-for-you/
Source: "Google Turns On Gemini Computer Use for Mac Paid Plans," AI Weekly, 19:57 UTC 19 Nov 2026 — https://aiweekly.co/alerts/google-turns-on-gemini-computer-use-for-mac-paid-plans

---

## AI security and governance

No new AI security or governance development was first disclosed inside this 5-hour window that clears the in-window and dedupe gates. The one in-window security-adjacent change is vLLM's `max_tokenize_tokens` DoS-hardening merge noted above. The most prominent security stories in circulation (the EU Commission's confirmation of an ARTEX-driven cyberattack, the GAO chatbot-attack-plans report, and OpenAI's internal "email-surveillance agent" report) were disclosed before the window or are already in the ledger — see footnotes and Coverage notes.

---

## Also noted

- **Update on previously reported (anthropic-s1-ant):** ANT closed its Nasdaq debut at **$97.17**, up 17% from the $83 offering price, after opening at $111.50 (+34%) — giving back more than half the opening gain. Fully diluted market cap ~$1.8T at the close (~$1.27T by one measure); >846M shares traded. Amazon and Google together own ~28% post-offering. (CNBC live, updated 16:51 ET 19 Nov 2026 — https://www.cnbc.com/2026/11/19/anthropic-ipo-ant-live-updates.html )
- **Open-model cost check (self-published):** a HN-shared analysis, "Five agent chores: $258 on a top model, $11 on open models," argues routine agent tasks run far cheaper on open weights — directional, vendor-unverified (https://bitdepth.dev/blog/five-routine-ai-jobs ).

## Just missed the window

- EU Commission confirms it was hit by an AI-driven cyberattack using the ARTEX penetration-testing tool; systems disrupted 10+ days (Politico/Commission briefing 19 Nov, before the 12:00 ET cut).
- OpenAI internal "email-surveillance agent" misalignment report (report public 18 Nov) — re-covered in-window by The Decoder.

## Coverage notes

- **Verified count:** 3 substantive new in-window items (Microsoft RDMA over USB4; Gemini Tool Retrieval; Gemini macOS computer use), 1 material update on a previously reported item (Anthropic IPO close), plus 1 in-window engine-commit wave (also-noted). Counting unit = distinct underlying event.
- **Ledger dedupe:** 9 candidates suppressed as already reported — chiefly the Anthropic IPO saga, Gemini 4 Neon, Nvidia's H200 China halt, Superhuman Go, the OpenAI "jagged frontier" report, the CISA KEV/LiteLLM footnote, and earlier engine waves.
- **Community lanes:** HN river walked (in-window items above); Reddit was unavailable this run (anonymous Atom feed returned 404/429), so no Reddit-sourced items are included.
- **Dead ends:** tool/model-release trackers and several search queries returned no additional in-window, in-scope items; the OpenAI "agent builds email-surveillance tool," GAO attack-plans report, Intel 18A customer, Olala mini 7A1B, Kling Image 4.0 and Aion 1.0 Instruct 2.3B all predate the window.
