# Cybersecurity News Briefing — Monday, November 2, 2026, 12:00–17:20 ET

Window: 2026-11-02 12:00 EST → 2026-11-02 17:20 EST (previous fire → now; new items only, deduped against the reported ledger).

## TL;DR

- **ARTEX AI-attack escalation gets specifics + a new victim:** BleepingComputer (4:40 PM ET) detailed CrowdStrike's Vapor Panda campaign — 200+ orgs in **14 countries** since Oct 17 — and the Dutch institute DIVD confirmed its own September breach was executed by autonomous ARTEX agents: initial access to **root in 12 minutes** by chaining a Zammad zero-day with a local privilege escalation zero-day.
- **Mercado Bitcoin confirms cyberattack:** Brazil's largest crypto exchange suspended all deposits and withdrawals Monday morning as blockchain trackers put **$46M+** in outflows across at least 10 chains; Tether froze about $4.5M of it.
- **Abrigo's Sageworks partially restored:** the lending-risk platform serving 2,400+ US banks and credit unions came back online Monday afternoon — except borrower-facing features — after "irregular activity" took it down Oct 30.
- **Michigan State lands on BYOD's leak site:** the gang behind the University of New Mexico attack posted MSU data claims (1.03M student records) that The Record tied to the campus incident Sunday night/Monday.
- Afternoon leak-site batch: settra, a new HYFLOCK debut, pear, qilin (Chile's Isapre Consalud) and a redacted SilentRansomGroup post; 50 orgs listed in 24h, The Gentlemen leading with 24.

---

## Vulnerabilities and patches

**No new verified vulnerability or patch disclosures fell inside this window.** The only notable patch news — Microsoft fixing Smart App Control activation broken by the October KB5127648 update — was published at 8:57 AM ET, before the 12:00 window start (see footnotes). Active vulnerability stories from earlier runs (GitLab CVE-2026-97229 exposure, Ivanti Connect Secure chain, BTCPay Server) were already reported and are suppressed by the ledger.

---

## Attacks and campaigns

### Update on previously reported (ledger 2026-10-29, vapor-panda-artex-deepseek): Vapor Panda ARTEX campaign spans 14 countries; DIVD confirms 12-minute autonomous root

**Event date:** November 2, 2026 (CrowdStrike briefing detail via BleepingComputer, 4:40 PM ET; DIVD update same day).

BleepingComputer's afternoon write-up adds material detail to the CrowdStrike report covered this morning: the AI-agent operation began October 17 and targeted government ministries, telecoms, pharma, semiconductor, defense, nuclear and financial organizations across **14 countries**, with at least nine confirmed compromises and password-spraying/credential-scanning against 12 more high-value targets. Confirmed victims include US and Belgian telecom providers, government organizations in Greece, the Middle East and Latin America, and US pharmaceutical firms. In the Greek government intrusion, ARTEX's agents forged an administrator token and read mailboxes using authentication keys recovered from a 2022 mail-server directory export. Separately, the **Dutch Institute for Vulnerability Disclosure (DIVD)** published new detail on its own September compromise: the tool was ARTEX acting autonomously, it chained a **Zammad zero-day with a second zero-day for local privilege escalation to reach root in 12 minutes**, and it ran 450,000+ requests and 2,500 scripts simultaneously — activity DIVD called "super loud and messy." Operators instructed the agents in Chinese to skip Windows hosts protected by Falcon.

**Why it matters:** This is the clearest public forensic record yet of a state-linked actor using AI agents end-to-end — zero-day chaining, mailbox reading, lateral movement — with a human only at decision points. The Falcon-evasion instruction also means EDR coverage gaps now directly steer agent pathing.

**Action:** Inventory exposed Zammad instances and unpatched helpdesk/LPE-prone Linux hosts; treat agent-speed intrusion windows (minutes, not days) as the planning baseline for detection and containment SLAs.

Severity: Not specified by source (no CVEs assigned to the DIVD zero-days as of publication).

Source: "Chinese hackers use ARTEX AI in automated attacks against 200 orgs," BleepingComputer, Bill Toulas, November 2, 2026 04:40 PM ET — https://www.bleepingcomputer.com/news/security/chinese-hackers-use-artex-ai-in-automated-attacks-against-200-orgs/
Second source (original attribution): CrowdStrike VAPOR PANDA report, October 29, 2026, via NBC News (reported this run's 07:20 edition).

### Brazil's largest crypto exchange Mercado Bitcoin confirms attack; trackers put outflows at $46M+

**Event date:** November 2, 2026 (exchange statement Monday morning; The Record 12:00–17:20 ET window).

Mercado Bitcoin — which calls itself Latin America's largest digital-asset platform with ~4.5M customers — suspended all deposits and withdrawals Monday after detecting a security incident in one of its systems. Blockchain security firms put **more than $46 million** out of the platform late Sunday night across at least 10 networks (Bitcoin, Ethereum, Solana, Tron, Polygon, Arbitrum, Avalanche, BNB Chain, Stellar and more; preliminary counts ~2,852 ETH, 106 BTC, 10,700 SOL, 6.7M XLM); most movement happened in a 34-minute burst between 06:19–06:53 UTC. Tether CEO Paolo Ardoino said Tether froze about **$4.5M**, with another ~$2M frozen by other platforms; Chainalysis is tracking the coins. The exchange says no customer harm and no data breach, but several blockchain researchers publicly disputed that, pointing to on-chain evidence the attackers drained some **customer** wallets. Researchers characterized the thefts as ordinary, properly signed transfers — not a smart-contract exploit — so the open question is how signing access was obtained. The exchange published destination addresses and pledged restoration "in the coming days."

**Why it matters:** Coming days after the $6.3B Binance heist, this is the second exchange custody failure in a week, and the "no customer harm" claim is contradicted by chain evidence — a reminder that warm-wallet custody, not just hot wallets, is a live attack surface.

**Action:** MB users: assume the freeze extends past "coming days" for anything you cannot afford to lock, and don't rely on the no-customer-impact claim. Exchange operators: audit who and what can produce valid signatures on warm wallets.

Severity: Not specified by source.

Source: "Largest crypto exchange in Brazil confirms cyberattack as millions flow out of platform," The Record (Jonathan Greig), November 2, 2026 — https://therecord.media/mercado-bitcoin-brazil-cryptocurrency-heist
Second source: "Mercado Bitcoin Suffers Cyberattack as Researchers Flag $40 Million in Suspicious Crypto Transfers," Cyber Security News, November 2, 2026 — https://cybersecuritynews.com/mercado-bitcoin-suffers-cyberattack/

### Abrigo restores most of Sageworks after suspected cyberattack took lending platform down since Oct 30

**Event date:** November 2, 2026 (Abrigo status update; BleepingComputer 2:00 PM ET).

Abrigo, whose Sageworks risk-management platform is used by more than 2,400 US community banks and credit unions for loan underwriting and portfolio monitoring, said Monday most functionality is restored — borrower-facing features stay offline — after it disabled the application at 11:25 AM ET on October 30 "out of abundance of caution" upon detecting **irregular activity** in its environment. Affected services were Online Portal Now, Sageworks Analyst and the Sageworks API. Abrigo has not said whether this was a breach or a technical fault and did not respond to BleepingComputer before publication. Context that raises the temperature: earlier this year, an account claiming to be ShinyHunters alleged theft of 1.7 million Salesforce records from Abrigo containing PII and internal corporate data; that claim was never confirmed, and researchers had advised client banks to rotate Sageworks API keys.

**Why it matters:** Sageworks sits in the loan-decision path of hundreds of lenders — the exact kind of single-vendor concentration where an unconfirmed incident at one SaaS provider becomes a thousand availability and data-exposure problems.

**Action:** Bank/credit-union customers should treat the borrower-feature outage as an active incident: rotate Sageworks API keys, monitor for anomalous analyst/API activity, and demand a written incident disposition from Abrigo.

Severity: Not specified by source.

Source: "Fintech platform Sageworks taken offline after suspected cyberattack," BleepingComputer, Bill Toulas, November 2, 2026 02:00 PM ET — https://www.bleepingcomputer.com/news/security/fintech-platform-sageworks-taken-offline-after-suspected-cyberattack/
Second source (restoration timeline): OsintSights, "Abrigo Disrupts Sageworks After Suspected Cyberattack," November 2, 2026 — https://osintsights.com/abrigo-disrupts-sageworks-after-suspected-cyberattack

### Update on previously reported (ledger 2026-11-02 07:20, msu-byod-103m): BYOD actually posts Michigan State to its leak site

**Event date:** November 1–2, 2026 (leak-site listing surfaced Sunday evening; The Record 2:54 PM ET).

The BYOD ransomware gang — which this morning's edition covered claiming 1.03M student PII plus current/old passwords — has now **listed Michigan State University on its leak site** (ransomware.live logged the entry November 2, attack dated November 1). The Record connects the post to the security incident that already forced MSU to make every NetID user reset their password, and notes BYOD "drew attention in October for an attack on the University of New Mexico" — the same gang that offered UNM free decryption keys last week. Two independent trackers time the listing between ~03:50 UTC November 2 and Sunday evening ET.

**Why it matters:** A claim is rhetoric; a leak-site row is the gang's own evidence commitment. MSU moving from "claimed" to "posted" — and BYOD's UNM playbook — suggest a data-theft operation, not wiper theater, aimed at education with high-frequency re-victimization.

**Action:** MSU affiliates: assume student PII and old passwords are exposed — change reused passwords anywhere, watch for targeted phishing citing your Spartan credentials.

Severity: Not specified by source (unverified by MSU as of publication).

Source: "Michigan State cyber incident claimed by ransomware gang behind recent hospital attack," The Record (Jonathan Greig), November 2, 2026 2:54 PM EST — https://therecord.media/michigan-state-university-byod-gang-cyber-incident
Second source: ransomware.live daily summary, November 2, 2026 — https://www.ransomware.live/summary/

### Leak-site afternoon batch: settra triples up, HYFLOCK debuts a Turkish victim, qilin hits Chilean health insurer

**Event date:** November 2, 2026 (afternoon postings).

The afternoon tranche on Julien Mousqueton's ransomware.live tracker added: **settra** posted three US domains (kingdomtitle.net, mackinengineering.com, insurancehouse.com — attack dates Oct 6–8); **HYFLOCK** made its debut with Turkish packaging firm Ateşsan Ambalaj; **pear** posted Neiman & Company, Inc.; **qilin** added Chile's Isapre Consalud (health insurance); SilentRansomGroup added a redacted victim. ransomnews puts the last 24 hours at 50 organizations across 15 operators, led by **The Gentlemen with 24**, with N0n (5), Orova (4) and akira (3) next; seven-day average 36.9/day. (N0n #113–117, the akira and everstake pairs, Orova's Harry's International, and Holland & Knight were reported in the 07:20/12:00 editions and are not repeated.)

**Why it matters:** The Gentlemen's 24-in-24h burst — all with October 26 attack dates — shows a backlog dump after the Oct 31 spree, i.e., victims should expect leak-site exposure weeks after intrusion, not days.

**Action:** None published by sources; standard practice is checking trackers for your domain.

Severity: Not specified by source.

Source: ransomware.live daily summary, November 2, 2026 — https://www.ransomware.live/summary/
Second source: "Ransomware attacks today: 50 victims listed, 2 Nov 2026," RansomNews, November 2, 2026 — https://ransomnews.com/ransomware-attacks-today/

---

## Defensive tools and advisories

No new defensive-tool releases or advisories verified with an in-window (12:00–17:20 ET) timestamp. CISA's Ivanti remediation reminder, Google's XCPE botnet takedown coverage, and two interesting-but-morning-published items (Byoky AI-key wallet; Google's $250K root-in-cloud bounty) are all either already reported or time-excluded — see Coverage notes.

---

## Just missed the window

- **VOLTA infostealer uses Claude, Kimi and Gemini to rank victims by net worth** — Cisco Talos's research actually published October 31; The Hacker News and Help Net Security ran it only today. Self-deleting Windows stealer ships an "evidence bundle" per victim to OpenRouter (Claude Opus 4.6 default, Kimi K2/Gemini 2.5 Flash fallbacks on refusal) for monetization scoring. Not ledger-tagged → footnoted once here. — https://blog.talosintelligence.com/appraisal-by-ai-model-driven-victim-prioritization-in-the-volta-infostealer/
- **Unit 42's SysGoRAT / CL-UNK-1211 government-Linux espionage report** (AI-generated payloads with conversational LLM comments left in scripts; Oct 2025–Apr 2026 activity) published October 15; The Hacker News re-covered it November 2. Footnoted once. — https://unit42.paloaltonetworks.com/ai-campaign-targets-linux-infrastructure/
- **Microsoft fixes Smart App Control broken by the October Windows update** — BleepingComputer, November 2, 2026 8:57 AM ET (3 hours before window start): SAC activation failures caused by KB5127648 are resolved; affected Windows 11 devices no longer need workarounds. — https://www.bleepingcomputer.com/news/microsoft/microsoft-fixes-smart-app-control-broken-by-october-windows-update/

---

## Coverage notes

- **Verified and reported this run: 5 items** (3 new events, 2 updates on ledger events) + 3 first-time "Just missed" footnotes. Counting unit: one entry per underlying event/development, per the reconciled manifest.
- **Ledger dedupe: 19 candidates suppressed as already reported**, including re-coverage from today's own outlets: BleepingComputer (ARTEX base story, XCPE, BTCPay, EU-fridge wave 2, GitLab 1,000+ unpatched, Synology C3RB3R, Ivanti patches, FBI/TraderTraitor, Chainalysis, Alpine GitLab), Help Net Security (XCPE, Trezor implant, Aur0ra/GOLD OGRE, CISA-Ivanti), SecurityWeek (FLDOE 4.9M re-cover, Revolve 180k, Cursor/Claude extension, Polymarket $3.9M, Northstar), Dark Reading (PapRAT), plus the already-footnoted Teams/AD CS story and Langflow (cannot re-footnote).
- **Excluded on timing (morning publication, in-window time unverifiable):** Google's $250K root-in-cloud VRP reward and HNS's Byoky open-source AI-key wallet — both URL-dated November 2 but with crawl metadata pointing to pre-12:00 ET publication.
- **Excluded as out-of-window disclosures with no new twist:** DarkBlinders Iran-nexus Israel/Kurdistan espionage write-up (Dream Research Labs disclosed Oct 8–11; THN re-covered today).
- Dead ends: none material; all planned index fetches (THN, BleepingComputer, The Record, SecurityWeek, HNS, Dark Reading, ransomware.live/ransomnews) succeeded. Phase walls held; no reworded-query loops.
- No new CISA KEV entries today (weekly additions were the Oct 30 Ivanti pair, already reported).
