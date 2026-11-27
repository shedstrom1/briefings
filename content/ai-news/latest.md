# AI News Briefing — November 27, 2026 (07:00 ET)

**Window:** 2026-11-26 17:00 EST → 2026-11-27 07:00 EST (previous fire → now). New-items-only; every candidate checked against the reported ledger, and the event date — not article freshness — governs inclusion.

**TL;DR**
- **SAP confirms a breach of its GitHub environment** — the "Project Granite" incident, in which stolen employee tokens were used to copy and alter source code. SAP and Der Spiegel say AI tools may have assisted; no customer business-data access found. This is the lead story of a thin overnight window.
- **Google DeepMind says AI agents now run part of the compute for Gemini experiments themselves**, with VP Koray Kavukcuoglu calling full recursive self-improvement still distant.
- **Agent-accountability startup Dante Security raised a $10M seed led by NFX**, betting that every enterprise AI agent should have a named human owner.
- **A viral community reproduction claims Claude quietly degrades SOTA ML-research tasks** — an unverified extension of an already-disclosed Anthropic safeguard, not a new vendor announcement.
- **No new open- or closed-weight model release qualified in-window**; local-inference engine activity was limited to routine commits.

## Models and local inference

**No new model release qualified in this window.** (Event date: n/a.) The Open Weights daily index and first-party lab feeds surfaced only models already reported in prior editions — GLM-5.4, Qwen4-Max, MiniMax H3-Image, Inkling 2 Mini and the like — or undated community quant repos that cannot be pinned inside the window. The invented-URL slug checks returned 404s and were retired under the two-strike rule. This is a coverage fact, not padding.

**In-window engine activity: housekeeping only.** (Event date: 2026-11-27, 03:51–06:57 ET.) llama.cpp shipped prerelease **b12811** (published 06:14 ET) with a converter fix preserving the pooler for `BertForSequenceClassification` (#33122) and a Llama Chat UI 0.4.0 refresh adding Hugging Face sign-in/upload and share controls to released builds. SGLang landed an attention fix defaulting to Triton when FlashInfer FA2 cannot serve the head dims (#51967, 06:57 ET) and a HiCache checkpoint-retention test change (#53513, 03:51 ET). No versioned GA release appeared in-window: Ollama's v0.40.19-rc0 was published 12:52 ET on 11-26, before the window opened.
*Why it matters:* No capability or benchmark movement here — just maintenance on the local stack.

## Agent tools

**DeepMind lets AI agents control part of the compute for Gemini experiments.** (Event date: remarks surfaced 2026-11-26 19:03 ET; podcast interview.) On the company's podcast, VP of Research Koray Kavukcuoglu says DeepMind now relies on a single general-purpose algorithm across science and products, that each Gemini generation is a hand-built one-off, and that agents themselves direct part of the compute to run experiments. He stresses the lab is far from full recursive self-improvement and says DeepMind deliberately does not reward agents' internal chain-of-thought during training so their intentions stay reviewable in natural language before they act. He also argues no benchmark captures the core of intelligence, and that AGI begins only when everyday products earn enough trust for people to hand decisions over.
*Why it matters:* It is a first-party confirmation that agent-directed experimentation is already partly operational inside a frontier lab — and an explicit claim that CoT is left unrewarded as a safety measure. Treat the "how much" as unverified: it is a podcast claim, not a published result.
Source: The Decoder, "AI agents now control part of DeepMind's compute for Gemini experiments," 2026-11-26 — https://the-decoder.com/ai-agents-now-control-part-of-deepminds-compute-for-gemini-experiments-but-recursive-self-improvement-remains-distant/ (HN surfaced 2026-11-26 19:03 ET, 55 points — https://news.ycombinator.com/item?id=50661218)

**Also noted — Dante Security exits stealth with a $10M seed led by NFX.** (Event date: CTech report 2026-11-26 22:15 ET; other outlets date the round to 11-24/11-25, so treat the event date as uncertain.) The Tel Aviv/New York startup sells a platform that discovers the AI agents running in a company, traces what each was told to do versus what it did, and assigns every agent a named human owner. Its founders (CEO Moshe Lipsker, ex-Imperva) say tests of agents from Anthropic, OpenAI, Meta and xAI found they could be steered into normally-refused tasks by treating faked web pages as real permission.
*Why it matters:* Agent-accountability tooling is a fast-forming category; the "who is responsible" framing is a direct response to agents acting under real employee credentials.
Source: The Next Web, 2026-11-25 — https://thenextweb.com/news/dante-security-10m-ai-agent-accountability ; Techmeme/CTech, 2026-11-26 22:15 ET.

## AI security and governance

**SAP confirms the "Project Granite" breach; AI assistance suspected but unconfirmed.** (Event date: SAP confirmation 2026-11-27 02:36–04:56 ET.) SAP told BleepingComputer and SecurityWeek it detected suspicious activity and took measures to protect customers, with "no evidence of unauthorized access to customer business data." Der Spiegel, citing an internal SAP memo, reports attackers stole employee authentication tokens and used them to copy and alter source code in SAP's GitHub environment. Handelsblatt reports SAP replaced signing keys, restricted GitHub access and suspended automated development operations. Sources told Der Spiegel the attack may have been AI-assisted; SecurityWeek notes that is unconfirmed. The incident is separate from a late-October Gentlemen-ransomware listing of SAP's Latin American operations, and no tampered code is known to have reached customers.
*Why it matters:* A 110,000-employee enterprise-software vendor had its source-control trust chain — tokens, signing keys, CI — targeted, and the suspected AI assistance fits the week's pattern of agent-driven intrusions (ARTEX, Mitel, Switzerland). It is the clearest in-window development for this brief.
Source: BleepingComputer, "SAP confirms security incident after suspicious GitHub activity," 2026-11-27 04:56 ET — https://www.bleepingcomputer.com/news/security/sap-confirms-security-incident-after-suspicious-github-activity/ ; SecurityWeek, "SAP Confirms Cyberattack, Source Code Reportedly Compromised," 2026-11-27 02:36 ET — https://www.securityweek.com/sap-confirms-cyberattack-source-code-reportedly-compromised/

**Also noted — viral reproduction claims Claude degrades SOTA ML-research tasks.** (Event date: 2026-11-27 03:28 ET on HN, 144 points.) A practitioner's X thread reports Claude producing far worse ML-research work than a competing model at a fraction of the training steps, and frames it as deliberate. The discussion points back to Anthropic's Fable 5.2 system card, which discloses "interventions that limit Claude's effectiveness for requests targeting frontier LLM development" via prompt modification, steering vectors or PEFT, and says those safeguards "will not be visible to the user." That policy was disclosed on 11-09 and is already reported; only this reproduction is new.
*Why it matters:* Whether or not the reproduction holds up, it revives a governance dispute — hidden, non-refusable capability limits inside a paid model — that bears directly on anyone using Claude Code for competitive technical work. Unverified; do not treat as a vendor admission.
Source: HN thread, 2026-11-27 03:28 ET — https://news.ycombinator.com/item?id=50664325

**Also noted — a Florida man was sentenced to 47 months for using AI to impersonate Kick streamer N3on**, defrauding a memecoin's organizers of about $260,000 (Aftermath, 2026-11-26 17:10 ET). A concrete AI-impersonation fraud outcome in an otherwise quiet legal window.

## Just missed the window

- **Dragos named MiniMax as the provider whose systems opened confidential links embedded in user prompts**, including roughly two-thirds of requests marked Zero Data Retention, with one link later shared on Feishu and private-repo clone attempts observed (Reuters exclusive, 2026-11-26 04:15 ET — https://www.reuters.com/world/data-chinese-ai-firm-minimax-accessed-by-unidentified-parties-researcher-says-2026-11-26/ ; Unite.ai follow-up — https://www.unite.ai/dragos-identifies-minimax-in-ai-prompt-privacy-investigation/). Predates this window; footnoted once, not re-listed.

## Coverage notes

- **Verified new in-window items:** 3 substantive leads/items (SAP breach; DeepMind agent-directed compute; Dante Security seed), plus 1 unverified community reproduction, 1 legal sentencing, 1 aggregated engine-activity note, and 1 "just missed" footnote. No new model release qualified.
- **Ledger dedupe:** roughly 15 candidate events were suppressed as already reported — including GLM-5.5/Kimi K4 delay, the Aaronson crypto-bombshell claims, the GPT-6.2 billing fix, Nvidia's SE-Asia shipment halt, Nvidia's Chinese-owned California partners, Character.AI's 42-AG challenge, the Italy/Switzerland AI-intrusion advisories, FCC Starmind, ChatGPT/Work tab merging, and Claude Artifacts "install as app." The Microsoft ARTEX architecture story was already footnoted as `missed:` and could not be re-listed.
- **Dead ends / limitations:** web_search failed intermittently (DDGS/Nous unavailable); the Reddit lane was down (anonymous `.rss` returned 404); Reuters, The Times and several first-party newsrooms were walled; guessed BleepingComputer/HF slugs 404'd and were not retried. Open Weights entries are undated on the homepage and were not promoted to items. Second independent sources were obtained for SAP and MiniMax; the DeepMind and Dante claims rest on single first-party reports.
