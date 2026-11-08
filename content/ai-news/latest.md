# AI News Briefing — Nvidia Board Probe; DeepSeek Agent App Hits 1.54M Daily Users

**Window:** Sun 2026-11-08 12:00 ET → 17:00 ET (5 hours, Sunday afternoon)

## TL;DR
- **Nvidia's board launched an independent investigation into CEO Jensen Huang** Sunday, citing reporting he was "the target of a foreign intelligence effort." The board said there is no evidence Nvidia secrets were compromised; Huang separately admitted an affair and said he "fully supports the review."
- **DeepSeek disclosed its open-source agent app "Harness" reached 1.54 million daily active users** by Nov 4 — under three months after its Aug 13 launch — burning roughly 92 trillion tokens per day (~60M per user).
- **Amazon formally organized a Personal Robotics Group** around Fauna Robotics' 3.5-ft humanoid "Sprout" and is hiring for a "new-to-world robot product," CNBC reported.
- **vLLM merged 8 PRs in-window**, led by DeepSeek-V4 sequence-parallel + pipeline-parallel serving support.

---

## Models and local inference

**vLLM in-window merges: DeepSeek-V4 sequence parallelism + maintenance cluster** — Event date: Sun Nov 8 (17:00–22:00 UTC).
Eight PRs merged between noon and 5 PM ET. The standout is **#65766**, which adds DeepSeek-V4 support for sequence parallelism combined with pipeline parallelism.

No versioned releases landed for llama.cpp, Ollama, SGLang, or Jan in the window — llama.cpp's last build was **b12211** at 11:48 AM ET, just before the window opened.

*Local inference:* The DeepSeek-V4 parallelism work extends vLLM's ability to serve DeepSeek's open-weight stack across multi-GPU configurations; the ROCm KV-cache fix and Python 3.14 tensorizer bump are housekeeping for local serving stacks.

Source: GitHub vLLM merged-PR search (in-window range) — https://github.com/vllm-project/vllm/pull/65766

---

## Agent tools

**DeepSeek Harness hits 1.54M daily users; 92T tokens per day** — Event date: Sun Nov 8 (disclosed on X).
Bloomberg reports DeepSeek revealed that **Harness** — its open-source agent application that debuted Aug 13 — reached **1.54 million daily active users by Nov 4**, less than three months after launch. Daily users were about **271,000 at the end of September**, right after the desktop version shipped. The platform now processes roughly **92 trillion tokens per day**, about **60 million tokens per user**.

Why it matters: This is one of the first hard consumer-scale adoption figures for an open-source agent harness, and the ~60M-token-per-user burn rate quantifies how hungry long-running agentic loops are for inference capacity — a direct signal for anyone running self-hosted or local agent stacks.

Source: Bloomberg — "DeepSeek's AI Agent Platform Hits 1.54 Million Daily Users" (Nov 8, 2026, 6:01 PM UTC / 1:01 PM ET) — https://www.bloomberg.com/news/articles/2026-11-08/deepseek-s-ai-agent-platform-hits-1-54-million-daily-users
Further: AInvest — https://www.ainvest.com/news/deepseek-ai-agent-platform-reaches-1-54-million-daily-users-2611/ ; Emergent — https://emergent.sh/news/deepseek-harness-hits-1-54m-daily-users-3

**Amazon stands up Personal Robotics Group around Fauna's Sprout** — Event date: Sun Nov 8.
CNBC reported Amazon formally organized a home-robotics team — the **Personal Robotics Group** — seeded by **Fauna Robotics** (which joined Amazon on March 24) and is hiring for a **"new-to-world robot product"** capable of navigating dynamic home environments. Much of the **Astro** team was redeployed and an **MIT roboticist** was brought in. The seed is Fauna's **3.5-foot humanoid "Sprout"**; Amazon continues to sell Sprout Creator Edition robots. Design, price, and release date for the new robot are undisclosed. This is Amazon's second home-robot attempt after the **$1,450 Astro** (2021), which shipped to only a few thousand invite-only customers and whose 2023 business version was discontinued within a year.

Why it matters: Amazon is explicitly rebetting on embodied/home agents after Astro's failure, with job-posting language hinting at robots meant to be "delightful" — a consumer humanoid push that lands inside a loud quarter for consumer-facing robotics.

Source: CNBC — "Amazon Spins Up Personal Robotics Group, Hires MIT Roboticist and Redeploys Astro Team Around Fauna's Sprout" (Nov 8, 2026, ~1:48 PM ET) — https://www.cnbc.com/2026-11-08/amazon-home-robot-fauna-sprout.html
Further: AI Weekly alert — https://aiweekly.co/alerts/amazon-spins-up-personal-robotics-group-hires-mit-roboticist-and-redeploys

---

## AI security and governance

**Nvidia board launches independent probe into Huang; CEO admits affair** — Event date: Sun Nov 8.
Nvidia's board said Sunday it is "initiating an independent investigation in light of recent reporting that founder and CEO Jensen Huang has been the target of a foreign intelligence effort." A **committee of independent directors, assisted by outside independent counsel**, will oversee it. The board said it has **"no reason to believe that the company's secrets or national security were compromised"** and that the review "prioritizes U.S. national and economic security, which is paramount to the interests of NVIDIA and its shareholders." In a separate message titled "A Message to Our Company," Huang wrote: **"I owe my family and all of you an apology. I was unfaithful to my wife… There is no excuse for what I did,"** adding he "fully supports the review" and is "committed to continuing to lead the company."

This is the governance culmination of the **Alice Huang / Megaspeed** national-security thread reported over the past two weeks — Alice Huang, a top Megaspeed executive, was believed by U.S. officials to have been recruited by China's Ministry of State Security; Megaspeed has bought more than $4.8B in Nvidia chips.

Why it matters: The probe formalizes board-level oversight of a foreign-intelligence concern at the top of the world's largest chipmaker, with direct relevance to the chip-export compliance scrutiny U.S. lawmakers have demanded and to Nvidia's board governance ahead of continued AI-capacity commitments.

Source: Nvidia Newsroom — "Statement from NVIDIA Board of Directors" + "A Message to Our Company" (Nov 8, 2026) — https://nvidianews.nvidia.com/news/nvidia-board-investigation-jensen-huang-foreign-intelligence
Further: CBS News (updated 4:58 PM ET) — https://www.cbsnews.com/news/nvidia-jensen-huang-investigation-china-intelligence/ ; CNN — https://edition.cnn.com/2026/11/08/business/nvidia-investigation-ceo-jensen-huang ; BBC — https://www.bbc.co.uk/news/articles/c3dxw10rjn9no

---

## Coverage notes
- **Verified items:** 4 (3 lead + 1 also-noted vLLM cluster), counted by underlying event.
- **Ledger dedupe (suppressed):** 9 candidates — Apple agentic Siri (ledger `ternus-agentic-siri-2027`); Anthropic OnePWR $3.4B gas-generator deal (ledger `OnePWR-3.4B-1.7GW`); Anthropic enterprise breach-notification email (ledger `missed:cathedral-anthropic-breach`; notice began Nov 7, out of window); Meta Wehner → Fireworks CFO (Nov 6–7, out of window); U.S. chip-import $1T annualized pace (247wallst, Nov 8 11:15 AM ET, pre-window); OpenAI/Hacktron partnership (Nov 6, out of window); Claude Code MCP-background + Google ADK 2.14 A2A stable (Nov 7, out of window); Claude Code settings-secret leak (ledger `missed:claude-code-settings-secrets`); Nov 8 Nvidia/Qualcomm stock slide (part of post-midterm selloff already tracked).
- **Repo/index sweeps:** GitHub releases checked for llama.cpp (none in window; last b12211 11:48 ET), Ollama, SGLang, Jan; vLLM merged-PR search returned 8. HN Algolia river walked (AI / local-inference / model) — nothing substantive in-window. Reddit lanes quiet.
- **Dead ends:** CNBC direct article URL 404'd on fetch (cited via search result + AI Weekly alert); Bloomberg DeepSeek article paywalled past the lede (figures corroborated via AInvest/Emergent).
