# Cybersecurity News Briefing — Friday, October 30, 2026, 5:00 PM ET Edition

**Window: 2026-10-30 12:00 → 17:20 EDT** (17:00 fire; new-items-only, deduped against the reported ledger)

## TL;DR

- **CISA put two more Ivanti Connect Secure flaws on KEV** — predictable-token file upload (CVE-2026-118527) and a missing-authentication privilege escalation (CVE-2026-111357), both chainable with the October 13 zero-day CVE-2026-104935; federal due dates Nov 2 and Nov 13.
- **SOCRadar published a teardown of "n0n"** — a new extortion crew that hit 23 orgs in 14 countries using only stolen credentials and RDP/FortiGate-SSL-VPN stuffing (zero vulnerability exploitation), spent $7,216+ on AI coding agents, and left its entire workspace exposed on a Swedish server; Register published Fri 19:37 UTC.
- **Anthropic denied any breach of its own systems** in the trojanized Cursor Claude Code extension (2.1.313): a "malicious actor" abused Cursor marketplace access to publish the tampered build; Cursor's own analysis says the SSH-tunnel payload mostly failed on lack of admin rights but key-dropping and persistence still landed.
- **A fourth and fifth Bay Street law firm confirmed breaches Friday** — Bennett Jones (two employee accounts, Oct 19) and Borden Ladner Gervais (client files in its Microsoft cloud, Mandiant engaged) join McCarthy Tétrault (~31,000 files) and Davies Ward Phillips & Vineberg; Stikeman Elliott and Gowling WLG say attacks against them failed.
- Afternoon leak-site adds: Suburban Orthopaedics (NJ), Cipsa Industrias (MX), Richland Resources, Société Ivoirienne de Télédiffusion + 2 pending/redacted.

---

## Vulnerabilities and patches

### CISA adds two chainable Ivanti Connect Secure flaws to KEV
**Event date: Friday, October 30, 2026** (KEV catalog version 2026.10.30, released 17:02 UTC / 1:02 PM EDT; CISA alert published Oct 30)

CISA added two Ivanti Connect Secure (ICS) vulnerabilities to the Known Exploited Vulnerabilities catalog based on evidence of active exploitation: **CVE-2026-111357** (missing authentication for a critical function, local-authenticated attacker escalates privileges; forensic-triage required, federal due **Nov 2**) and **CVE-2026-118527** (generation of predictable numbers/identifiers letting a remote authenticated attacker upload attacker-controlled files to a known disk location; due **Nov 13**). CISA's catalog entries state both "can be chained with CVE-2026-104935" — the authentication-bypass zero-day (CVSS 8.6) Ivanti patched and CISA KEV-listed on Oct 13, and the subject of a standing CISA alert urging immediate ICS patching. Severity for the two new CVEs: not specified by source. BOD 26-04's risk-based remediation and forensics-triage requirements attach to both.

**Why it matters:** ICS (formerly Pulse Connect Secure) is one of the most repeatedly exploited SSL-VPN products on the internet — 36 KEV-listed Ivanti flaws since Nov 2021 per tracking of today's entries. Adding the chain partners to KEV signals CISA has seen intrusions where the Oct 13 bypass alone was not enough: attackers are pairing it with privilege escalation and arbitrary file upload to reach persistence past the patch. Anyone who patched only CVE-2026-104935 on the Oct 13 deadline still has the exploitation path open.

**Action:** Update Connect Secure per Ivanti's advisory (hub.ivanti.com "Security Advisory Ivanti Connect Secure CVE-2026-104935"), verify you're on the fixed branch (25.1.3.x line; 22.7R2.14 and earlier unaffected per vendor trackers), and run CISA's forensics-triage steps on any internet-facing ICS appliance — the KEV entry for CVE-2026-111357 is flagged for compromise checks.

Sources:
- CISA, "CISA Adds Two Known Exploited Vulnerabilities to Catalog," Oct 30, 2026 — https://www.cisa.gov/news-events/alerts/2026-10-30/cisa-adds-two-known-exploited-vulnerabilities-catalog
- CISA KEV JSON feed, catalogVersion 2026.10.30 (dateAdded 2026-10-30 for both CVEs) — https://www.cisa.gov/sites/default/files/feeds/known_exploited_vulnerabilities.json
- Background: Ivanti Security Advisory CVE-2026-104935 — https://hub.ivanti.com/s/article/Security-Advisory-Ivanti-Connect-Secure-CVE-2026-104935?language=en_US ; CISA alert Oct 13 — https://www.cisa.gov/news-events/alerts/2026-10-13/cisa-urges-patching-newly-exploited-vulnerability-ivanti-connect-secure

---

## Attacks and campaigns

### SOCRadar: n0n extortion crew breached 23 orgs with zero exploits — then left its own operations server open
**Event date: Friday, October 30, 2026** (Register published Fri 30 Oct 2026 19:37 UTC / 3:37 PM EDT; SOCRadar investigation, leak-site snapshots Sept 25–26)

A "n0n" ransomware/extortion operator tried to break into **23 organizations across 14 countries** using only weak or stolen credentials — RDP scanning, automated FortiGate SSL-VPN credential stuffing (15-thread residential SOCKS5 pools against `/remote/login`), exposed AWS keys and reused hosting sessions — with SOCRadar finding **no evidence of any vulnerability exploitation** in any documented compromise. In one case (e-commerce site Fanatics), an exposed AWS access key let n0n pull data from **40 S3 buckets (up to 110 GB each)**, then delete bank-transaction archives and purge keys across 12 sensitive buckets; after breaching Netherlands fintech Transcom the crew even emailed PayPal's security team as secondary pressure. The operator burned **over $7,216 on AI services by Oct 22** — an OpenCode coding agent configured with 154 model entries across seven providers (Gemini, Claude, GPT-5.x, Grok, GLM, DeepSeek, plus "uncensored" variants) used to write and debug hundreds of attack scripts. SOCRadar found the whole workspace sitting on a publicly accessible Python HTTP server in Sweden (185.157.161[.]148:8877) with 1,719 files: ransomware source, extortion infra, hardcoded (reused) encryption keys. n0n has claimed 108 victims since September, including a 38-victim maritime/port/logistics batch on Oct 12 (≥21 linked to CyberLogitec customers, unconfirmed by the vendor) with $400K–$28M demands that produced zero on-chain payments.

**Why it matters:** This is the cleanest field evidence yet that the AI-intrusion scare and the credential-hygiene problem are separate tracks: the same actor used frontier AI agents as a dev team while entering exclusively through unrotated credentials — and destructive "delete data and backups" behavior makes recovery possible even without paying. The aggressiveness-10/lethality-0 profile and the self-doxxing workspace also make n0n an unusually tractable disruption target.

**Action:** Rotate/revoke any FortiGate portal and cloud keys exposed to the internet, audit S3 object permissions and CloudTrail for the listed IOCs, and treat SOCRadar's published snapshot indicators (server IP, key material) as hunt material now — the leak site is still live.

Source:
- The Register (Jessica Lyons), "Crim spent $7K+ on AI services in extortion attacks, then left evidence for all to see," Fri 30 Oct 2026 19:37 UTC — https://www.theregister.com/security/2026-10-30/crim-spent-7k-on-ai-services-in-extortion-attacks-then-left-evidence-for-all-see/5306754

### Update on previously reported — "Official Claude Code extension on Cursor Marketplace trojanized" (ledger: cursor-marketplace-claude-code-2.1.313, Oct 29)
**Event date: Friday, October 30, 2026** (Register published Fri 30 Oct 2026 16:27 UTC / 12:27 PM EDT)

Anthropic publicly denied that the trojanized **version 2.1.313** Claude Code extension came from any breach of its own systems, after industry figures alleged the malicious build was uploaded to Cursor's marketplace via a Claudeshop-side compromise. Anthropic technical staff Felix Rieseberg: "it wasn't a compromise of Anthropic's systems or employee laptops… A malicious actor used access to Cursor's marketplace to publish a tampered copy… The versions on our official channels are not affected." Cursor's and Phorion's analyses add material detail: the reverse-SSH-tunnel script (C2 134.209.82.255:8099 per yesterday's coverage) required admin rights on macOS/Windows and **failed in the vast majority of installs** — but the implant still exfiltrated usernames, Git emails and public IPs, dropped the attacker's SSH key and installed persistence regardless. Simply updating is not remediation: developers must threat-hunt and run Cursor's published cleanup scripts. Neither vendor has explained how the package reached the marketplace under the legitimate publisher identity.

**Why it matters:** Vendor-to-vendor finger-pointing (Anthropic: "malicious actor with marketplace access"; Cursor silent on the how) keeps the root cause — publisher-identity abuse in an AI-IDE marketplace — unresolved, which is exactly the vector that let a signed, official-name update ride for 11 hours to up to 5,000 developers.

**Action:** If you installed Cursor's Claude Code extension during the Oct 29 02:27–13:35 PT window, don't just upgrade to 2.1.314 — audit for the attacker SSH key/persistence per Cursor's remediation advisory and hunt for outbound SSH.

Sources:
- The Register (Connor Jones), "Anthropic: That Claude Code extension compromise… couldn't be us," Fri 30 Oct 2026 16:27 UTC — https://www.theregister.com/cyber-crime/2026-10-30/anthropic-that-claude-code-extension-compromise-couldnt-be-us/5306682
- Background (already reported): BleepingComputer Oct 30 10:36 EDT, "Malicious Claude Code extension on Cursor planted SSH backdoors" — https://www.bleepingcomputer.com/

### Bay Street law-firm breach campaign widens: Bennett Jones and BLG confirm compromises
**Event date: Friday, October 30, 2026** (Globe and Mail published ~14:35 EDT; intrusions Oct 19–22; McCarthy Tétrault/Davies first reported by Globe Thursday)

Toronto's corporate-law bar is now at four confirmed firms from one campaign: **Borden Ladner Gervais** said Friday an unauthorized third party accessed "a portion" of its Microsoft cloud environment including client-related files (Mandiant engaged; statement from CEO David Di Paolo), and **Bennett Jones** said two business-services employee accounts were compromised **Oct 19** as part of a "larger campaign targeting Canadian law firms" — stolen data includes client revenue reports and general descriptions of mandates, though its document-management and accounting systems were not breached. **McCarthy Tétrault** (~31,000 files, per the Globe's Thursday report) and **Davies Ward Phillips & Vineberg** were already dealing with breaches; **Stikeman Elliott** and **Gowling WLG** say attempted attacks failed. The Canadian Centre for Cyber Security warned Wednesday the actor group impersonates internal IT support over Microsoft Teams, luring employees to fraudulent sign-in pages; all breached firms have notified police and affected clients.

**Why it matters:** A single social-engineering pattern (Teams-based IT-support impersonation) walked into at least four of Canada's most commercially powerful firms inside two weeks, collecting client financial intelligence — law firms remain the soft underbelly of M&A confidentiality, and the cross-firm CIO intel-sharing now underway is a tacit admission no one caught it at the perimeter.

**Action:** Legal/spa sectors: block external-chat-initiated remote-access and sign-in redirects, retrain staff against IT-support impersonation (per CCCS guidance), and audit Microsoft cloud OAuth/app-consent grants now.

Sources:
- The Globe and Mail (Alexandra Posadzki), "Bennett Jones, Borden Ladner Gervais hit in data breach targeting Bay Street law firms," Oct 30, 2026 (~2:35 PM EDT) — https://www.theglobeandmail.com/business/article-bennett-jones-hit-in-data-breach-targeting-bay-street-law-firms/
- ThreatCluster cluster page (first seen Oct 30, 19:45 UTC; timeline "breach details disclosed 2026-10-30") — https://threatcluster.io/cluster/data-breaches-impact-multiple-bay-street-law-firms-ce450f39
- Law.com International, "4 Canadian Firms Fall Prey to Industry-Wide Cyberattack," Oct 30, 2026 — https://www.law.com/international-edition/2026-10-30/2-canadian-firms-fall-prey-to-industry-wide-cyberattack/

---

## Defensive tools and advisories

No qualifying standalone releases verified in this window; today's qualifying defensive-advisory content is the CISA KEV action above (BOD 26-04 due dates Nov 2 / Nov 13).

---

## Also noted

- **Ransomware leak sites, afternoon batch** (discovered on ransomware.live between ~12:20 and ~17:00 EDT today, groups unconfirmed): **Suburban Orthopaedics** (New Jersey medical center), **Cipsa Industrias S.A. de C.V.** (Compañía Industrial Papelera Poblana / Tubos y Embutidos, Mexico), **Richland Resources Corporation**, **Société Ivoirienne de Télédiffusion** (RTI, Côte d'Ivoire state broadcaster), plus one redacted FULL-DATA-TIMER entry and one pending-name victim — Source: https://www.ransomware.live/ (fetched 30 Oct 2026 ~17:25 EDT).

## Just missed the window

- **Revolve Group card-data breach (8-K filed Thursday Oct 29):** "automated means" harvesting saved payment-card numbers, expiries, names and contacts; The Record's updated write-up adds Friday's Texas notification putting **127,200 Texans** affected (1,328 in Vermont, MA filings too) — The Record, https://therecord.media/resolve-fashion-retailer-data-breach-payment-cards
- **Florida DOE student-financial-aid breach, ~4.9M people** (SSNs, DOB, addresses; discovered Oct 19, state notice to AG under §501.171, announced Thursday) — FLDOE page: https://www.fldoe.org/databreach.stml ; Security.io coverage Oct 30: https://www.security.io/articles/2026-10-30/florida-financial-aid-breach
- **STP Nuclear Operating Co. (South Texas Project) data breach** reported to the Texas AG Oct 28; San Antonio Express-News coverage Friday — https://databreach.watch/es/breach/south-texas-project-stp-2026-october

## Coverage notes

- **Verified: 5 qualifying items** (4 main — including one ledger-update item — plus one also-noted leak batch), all with fetched, in-window-timestamped pages; 3 one-time "just missed the window" footnotes (each never previously reported).
- **Ledger dedupe: ~18 candidates suppressed as already reported**, including repeat coverage of the Vapor Panda/ARTEX report, the GitLab KEV story (BleepingComputer 09:00 EDT today), the Amadeus airline wave's Friday Cathay statement (53,000 bookings, Aug 2023–Sep 2026 access traces — announced 04:11 EDT, before window open, on an already-reported event), MPTCP/Anthropic CVE-2026-98432 (Oct 27 disclosure; already footnoted once — no qualifying new development), GM OnStar settlement, GCHQ lost drives, Belgium MyMinfin, GLM-5.3, plus this morning's Binance/Cursor/Trezor/WordPress items.
- Dead ends: ransomwarenews.com is **down (404, domain in liquidation)** — leak attribution done via ransomware.live only; ransomware.live per-group JSON endpoint 404'd (groups left unconfirmed); CISA /news-events/alerts index 404'd (deep link from search worked).
- Late leads NOT chased (per quit rules): Help Net Security's Talos "fake IT help desk callers" piece and Register "GM petty cash" (publication times unresolved against window open — neither appears to post after 12:00 EDT).
