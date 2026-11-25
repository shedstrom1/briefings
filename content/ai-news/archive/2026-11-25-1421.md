# AI News Briefing — Wednesday, November 25, 2026 (12:00 ET)

**Window:** 2026-11-25 07:00–12:00 ET (previous fire → this fire). New items only; deduped against the reported ledger.

**TL;DR**
- **Sakana AI unveils Namazu Small**, a 30B reasoning model built on Nvidia's open Nemotron 3 Nano base — Sakana claims the 30B model outscores the 4×-larger 120B Nemotron 3 Super across 25 benchmarks, with on-prem delivery promised for early-to-mid December. Weights are not public yet.
- **Billions in China-linked AI data-center loans grind to a halt** as new US chip-access curbs spook lenders financing ByteDance, Alibaba and Tencent capacity (Bloomberg, 11:01 ET).
- **A public fight breaks out over "handing over" human decision-making to superintelligent AI** — Amodei's reported "life-critical" handover stance draws pushback from Meta's Alexandr Wang, Sam Altman and Elon Musk (Axios).
- **GitHub degraded Actions, Pull Requests, Packages, Pages and Webhooks** for close to an hour (10:55–11:52 ET) before mitigating; no root-cause analysis yet.

## Models and local inference

**Sakana AI announces Namazu Small — a 30B on-prem-bound reasoner on Nvidia's open Nemotron base** — Nov 25, 2026
Tokyo-based Sakana AI published the full spec and benchmark table for Namazu Small, a 30-billion-parameter reasoning LLM it says it continued-pretrained and post-trained in-house on the pretrained base of Nvidia's open Nemotron 3 Nano (a 30B-A3B MoE). Sakana's own 25-benchmark average puts Namazu Small ahead of both the same-size Nemotron 3 Nano and the roughly 120B Nemotron 3 Super — the largest gaps are on web search (BrowseComp 73.5 vs 36.1 for Nano) and grounded long-document work (LLM-jp LongBench 86.0, FaithEval 78.3). Parametric knowledge is the weak spot: SimpleQA Verified 6.8 vs Super's 18.1. The work drew support from Japan's Geniac programme (METI/NEDO). No Hugging Face weights or API SKU accompanied the post; Sakana says on-prem availability details arrive in early-to-mid December 2026.

 the December on-prem detail — licence, quant format, hardware floor — is the event to watch for local deployments.

Sources: Sakana AI, "Sakana Namazu Small" (Nov 25, 2026) — https://sakana.ai/namazu-small/ ; ExplainX, "Sakana Namazu Small is a 30B reasoning model" (Nov 25, 2026) — https://www.explainx.ai/blog/sakana-namazu-small-30b-nemotron-reasoning-november-2026 ; Emergent, "Sakana's 30B Namazu Small beats Nvidia's 120B Nemotron 3" (Nov 25, 2026) — https://emergent.sh/news/sakana-s-30b-namazu-small-beats-nvidia-s-120b

## Agent tools

**GitHub outage degraded agent-CI plumbing for nearly an hour** — Nov 25, 2026, 10:55–11:52 ET
GitHub's status page logged elevated failures from high database load starting 10:55 ET, cascading through Git Operations, API requests, Issues, Pull Requests, Actions, Packages, Pages and Webhooks. Mitigations rolled out product by product; the incident was marked resolved around 11:52 ET with a root-cause analysis promised but not yet published. The incident reached the Hacker News front page (116 points).

**Why it matters:** Agent workflows that lean on GitHub Actions, PR webhooks and Pages for build-and-review loops saw failed or delayed runs through most of the morning window. The all-products blast radius is a reminder that the agent CI layer inherits the availability of its host platform.

Sources: GitHub Status, incident qn8mfv6q5hlx (Nov 25, 2026) — https://www.githubstatus.com/incidents/qn8mfv6q5hlx ; Hacker News discussion (Nov 25, 2026, 15:59 UTC) — https://news.ycombinator.com/item?id=50643720

## AI security and governance

**Lenders freeze billions in China-linked AI data-center debt after US chip curbs** — Nov 25, 2026, 11:01 ET
Bloomberg reports that lenders have hit the brakes on billions of dollars in loans tied to ByteDance, Alibaba and Tencent, as new US restrictions on chip access threaten Asia's fast-growing AI debt-financing market. Deals that were near completion are now in doubt, and lenders trying to sell down exposure are struggling to find buyers. The pullback held even after Jensen Huang's surprise return briefly buoyed AI stocks earlier in the week — bankers remain focused on the legal risk of financing data centres and neoclouds that could face tighter export controls.

**Why it matters:** This is the financing-side consequence of the remote-access chip curbs, not another chip headline. If debt for China-linked compute dries up, the constraint on frontier build-out becomes capital rather than silicon — and it lands on the same neocloud balance sheets already under scrutiny in the US.

Sources: Bloomberg, "Billions in Data Center Deals Grind to Halt After AI Chip Curbs" (Nov 25, 2026, 16:01 UTC) — https://www.bloomberg.com/news/articles/2026-11-25/billions-in-data-center-deals-grind-to-halt-after-ai-chip-curbs ; Hacker News (Nov 25, 2026, 16:24 UTC) — https://news.ycombinator.com/item?id=50644105

**AI leaders clash publicly over surrendering human agency to superintelligence** — Nov 25, 2026
An Axios scoop reports a sharpening debate among top executives over a view it attributes to insiders at Anthropic — that humanity should hand some decisions to superintelligent systems. Axios recounts Amodei saying life-critical and safety-critical cases should be turned over to AI, and cites a litigation-revealed product slide describing a "utopia ruled by god in a box." Meta's Alexandr Wang wrote that "we should not be okay with a 'handover' of society to AI"; Sam Altman said people need agency over the future; Musk has said humans could become "like pet Labradors" at superintelligence. OpenAI's Dean Ball calls the handover view a "silent consensus" in AI-safety circles. An Anthropic spokesperson says Amodei has also advocated human control and that Claude is trained not to undermine oversight.

**Why it matters:** The argument has shifted from "how do we stop rogue systems" to "should humans voluntarily step aside" — and it is fuelling the political backlash the labs now face. This is the governance fault line to track as the "unreleased models too powerful to ship" debate plays out.

Sources: Axios, Madison Mills, "Scoop: AI leaders clash over surrendering human free will" (Nov 25, 2026) — https://www.axios.com/2026/11/25/anthropic-ai-human-control-debate-free-will ; Hacker News (Nov 25, 2026, 16:11 UTC) — https://news.ycombinator.com/item?id=50643888

### Also noted
- **Anthropic said its Messages API was ported from Python to Rust** using the live Python service as a verifier — an engineering note re-surfaced on Techmeme at 12:05 ET; the underlying disclosure predates this window.

### Just missed the window
- **vLLM launched vLLM-GR**, an open-source generative-recommendation serving framework (accelerator-side beam search, catalogue constraints, shared KV cache) — blog dated Nov 24 / X post 02:01 UTC Nov 25, just outside this window. Tagged `missed:`; it will not be footnoted again. — https://vllm.ai/blog/2026-11-24-vllm-gr-announcement

## Coverage notes
- **Verified items:** 3 substantive new items (1 model / 1 agent-tooling / 2 governance, one of which is the finance lead), plus 1 one-line also-noted and 1 just-missed footnote. This is a thin five-hour window — the 07:00 fire already swept the overnight stories — and the report is not padded to fill space.
- **Ledger dedupe:** roughly two dozen candidates surfaced by the discovery pages matched an existing `reported.md` line and were suppressed, including repeat "Just missed" footnotes (e.g. Ternary Bonsai 2.5 27B, the Check Point GT13 campaign, and the earlier FinCEN/rackspace/omega-9-4 items).
- **Dead ends / limitations:** the Reddit community lane was unavailable this run (search.rss returned 404 and 429 on the anonymous Atom backend); the Hacker News Algolia river and GitHub event feeds were used instead. The Guardian "Meta insiders on content moderation" story and several BleepingComputer/CNBC deep links returned 404 and were dropped under the two-strike rule rather than guessed.
