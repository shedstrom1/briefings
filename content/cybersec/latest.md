# Cybersecurity News Briefing — Monday, November 16, 2026 (Weekend Edition)

**Window:** Friday, Nov 13, 2026 17:20 EST → Monday, Nov 16, 2026 07:20 EST (weekend sweep; new items only, deduped against the reported ledger)

## TL;DR
- **Coles Group (Australia's #2 retailer) confirmed a "cyber security incident"** over the weekend — stores unaffected, and the company says there is no evidence customer data was compromised; global incident-response firms are engaged.
- **Microsoft shipped emergency out-of-band Windows updates** to fix the activation/licensing failures (0x80041008) caused by November Patch Tuesday — and separately **ruled out a cyberattack** in the Nov 12 M365 outage while warning that phishers exploited the outage with three attacker domains.
- **NoisyTCP (CVE-2026-99288)** — an 11-year-old Linux kernel NULL-pointer race — lets unauthenticated remote attackers crash servers running default-configured Nginx, Apache, and OpenSSH.
- **Uber's Fireworks AI-linked breach ballooned to ~150,000 drivers**: 149,346 people in the Texas AG filing, with driver's-license images, names, addresses, DOBs taken; Uber's spokesperson confirmed Fireworks AI was the compromised provider.
- **Weekend leak-site surge:** N0N opened Monday with a rapid-fire blue-chip listing spree (Walmart, JPMorgan, Visa, Mastercard, P&G, Nielsen, Medline, AS Watson — unverified), PixelLeaks debuted an India/US batch, and ransomware.live logged 42 new in-window victim listings.

---

## Vulnerabilities and patches

### Microsoft pushes emergency out-of-band Windows updates for activation failures
**Event date:** Sun Nov 15 – Mon Nov 16 (OOB release) — *Update on "0x80041008-activation" (reported Nov 13)*

Microsoft released emergency out-of-band (OOB) updates to fix the Windows activation and licensing failures introduced by this month's Patch Tuesday security updates. The original confirmation (Nov 13) covered slmgr/KMS failures with error 0x80041008 on Windows 10 21H2/22H2, Windows 11 23H2, and Server 2019/2022/2025; the OOB fixes land just over two days later.

**Why it matters:** Activation breakage isn't a security hole itself, but mass OOB remediation consumes the same patching windows security teams need for the 1,200+ November CVEs — and machines stuck in a failed-activation state often drift out of update compliance.
**Action:** Apply the OOB updates (or defer the November CU chain) per Microsoft's release-health guidance.
**Severity:** Not specified by source (servicing bug, not a CVE).

Source: BleepingComputer — "Microsoft pushes Windows emergency updates to fix activation failures," Nov 16 2026 03:26 EST (verified via bleepingcomputer.com RSS; Bing News dated same article Sun 19:26 GMT) — https://www.bleepingcomputer.com/feed/

### NoisyTCP (CVE-2026-99288): 11-year-old Linux kernel flaw remotely crashes Nginx/Apache/SSH servers
**Event date:** Mon Nov 16 (disclosure by Hyunwoo Kim)

A pre-authenticated remote NULL-pointer dereference in the kernel's TCP subsystem — present for about 11 years — can be triggered by crafted traffic that races SYN-cookie handling against lockless socket hashing, panicking the kernel on servers receiving TCP directly. The researcher (Hyunwoo Kim, who also disclosed Dirty Frag and Januscape this year) showed crashes against default-configured Nginx, Apache and sshd; Red Hat's analysis describes the race as stale ehash lookup concurrent with req-socket free/realloc across multiple RX contexts. A mainline patch was merged ~two weeks ago, before public disclosure.

**Why it matters:** Remote, unauthenticated DoS against stock Linux server configs — and the same race primitive that lands crashes today is the kind of lockless-path bug that can later be groomed into something worse. Exploitation reliability is lower on quiet bare-metal (narrow race window).
**Action:** Patch/backport now; where not possible, disable SYN cookies (weakens SYN-flood defense) or serialize to a single RX queue to close the race, per the researcher.
**Severity:** Not specified by source (no CVSS published in coverage).

Source: BleepingComputer — "New NoisyTCP vulnerability lets attackers crash Linux servers," Nov 16 2026 04:30 EST — https://www.bleepingcomputer.com/news/linux/new-noisytcp-linux-kernel-flaw-lets-attackers-crash-servers/

---

## Attacks and campaigns

### Coles investigates a "cyber security incident"; says no evidence of customer data compromise
**Event date:** Sun Nov 15 (company statement) – Mon Nov 16

Coles Group, Australia's second-largest retailer, confirmed Sunday it is investigating a cyber security incident. Stores and supply chain are unaffected — the company told customers to "shop as normal" and apologized — and Coles says there is no evidence customer data was compromised. It has engaged global incident-response specialists; reporting linked the news to a fresh wave of retail-sector pressure, including digital gift-card fraud at rival Woolworths. As of the cutoff the initial-access vector and scope are undisclosed.

**Why it matters:** The disclosure cadence (fast, low-detail, "no evidence yet") is the now-standard playbook, and Australia's retail sector has been under sustained attack pressure all year. The real test is what the forensics say in two weeks.
**Action:** Watch for a follow-up disclosure from Coles/Notifiable Data Breaches regime; retailers should re-check third-party and gift-card flows.
**Severity:** Not specified by source.

Sources: Reuters — "Coles probes cyber security incident, says no evidence customer data compromised," Nov 15 2026 21:31 GMT (via Google News; search backend down — verify via https://news.google.com/rss/search?q=%22Coles%22%20%22cyber%20security%20incident%22) ; The Guardian — "Coles investigates cyber-attack and apologises to customers," Nov 16 2026 00:25 GMT (same route); BleepingComputer — "Australian retail giant Coles investigates cybersecurity incident," Nov 16 2026 06:18 EST (https://www.bleepingcomputer.com/feed/)

### Microsoft: Nov 12 M365 outage was not an attack — and phishers rode the incident with fake "Outage -XXXXXX" notices
**Event date:** Fri Nov 13 statement / Sun–Mon coverage — *Update on "M365-outage-MO1550543" (reported Nov 12–13)*

Microsoft stated Friday the outage that disrupted Exchange Online, Teams, SharePoint and Copilot for over a day was "a technical issue not related to cyber or AI / agents," traced to an earlier network-capacity upgrade with a subsequent network change exposing the flaw; EX1550543 was marked resolved Nov 13. Microsoft Threat Intelligence then reported a credential-phishing campaign that spoofed M365 service-health notices referencing the real incident ID, with subjects "Outage -" plus a six-character code, sent from moments-notice[.]com, planetmaison[.]com and newbooker[.]com via recipient-specific SendGrid tracking links redirecting to reallifeaccounting[.]com.

**Why it matters:** Outage-servicing impersonation is a high-conversion phishing pretext precisely because recipients expect email about the outage — and the SendGrid-tracked infrastructure makes it look like legitimate transactional mail until the redirect.
**Action:** Block/report the four domains; audit Nov 13–16 for "Outage -" subject clicks; verify incidents only in the M365 admin center; review Safe Links policies.
**Severity:** Not specified by source.

Source: SecurityWeek — "Microsoft Rules Out Cyberattack in Outage, Warns of Phishing Campaign," Nov 16 2026 03:15 ET — https://www.securityweek.com/microsoft-rules-out-cyberattack-in-outage-warns-of-phishing-campaign/

### Uber breach via Fireworks AI grows to ~150,000 drivers
**Event date:** Mon Nov 16 (state notifications) — *Update on "uber-fireworks-dl-breach" (reported Nov 13)*

Uber is notifying roughly 150,000 drivers — 149,346 per its Texas AG filing — that driver's-license images, names, addresses, dates of birth and government ID numbers were stolen. Attackers used compromised credentials at a third-party service provider on Oct 7; Uber detected it Oct 9, and its spokesperson confirmed to TechCrunch the provider was Fireworks AI — the same vendor breach that exposed Notion AI chats and the stolen key behind Cursor's trojanized Claude Code extension. The data sat in an Uber-managed cloud environment whose alerts never fired because the actor presented valid credentials.

**Why it matters:** One vendor-credential blast radius now spans three high-profile platforms (Fireworks→Cursor→Notion→Uber), while "valid credentials, no alerts" keeps defeating cloud telemetry. Driver ID images are prime material for synthetic-identity fraud.
**Action:** Affected drivers should enroll in the offered credit monitoring/fraud assistance; orgs sharing data with AI-inference vendors should treat those connections as production trust boundaries and validate alert coverage.
**Severity:** Not specified by source.

Source: SecurityWeek — "Uber Data Breach Impacts 150,000 People," Nov 16 2026 06:02 ET — https://www.securityweek.com/uber-data-breach-impacts-150000-people/ (second source cited therein: TechCrunch)

### Leak-site roundup: N0N's Monday blue-chip spree plus 42 weekend listings
**Event date:** Nov 13 22:44 → Nov 16 07:20 (site timestamps); N0N spree 07:07–07:20 EST pre-cutoff

ransomware.live logged 42 new victim listings in-window: everest and interlock opened Friday night; EndZone, Barracuda, Scarlettgroup and qilin posted Saturday; PixelLeaks ran a Sunday batch of ~20 mostly US/India/HK firms (Godrej & Boyce, Four Points Financial, Boston Institute of Finance); incransom stayed the most active group (54 in 7 days) and qilin added a US SMB cluster. Then, minutes before the 07:20 cutoff, N0N — with a documented history of backdated and fabricated postings — rapid-fired ~13 mega-brand listings (Walmart, JPMorgan Chase, Visa, Mastercard, P&G, Nielsen, Medline, AS Watson, ODP Corp., EssilorLuxottica, Pandora, Airbnb, BoxLunch) at one-per-minute cadence. Treat every N0N claim as unverified absent a corporate acknowledgment.

**Why it matters:** Weekend leak batches are how extortion groups set the Monday news cycle; the one-per-minute posting pattern is a visibility grab, not new intrusion activity.
**Action:** Cross-check any listed brand of interest against vendor statements before treating as a breach.
**Severity:** Not specified by source.

Sources: ransomware.live (homepage + victims API, verified timestamps) — https://ransomware.live/ ; ransomnews (roundup corroboration)

---

## Defensive tools and advisories

### Cloudflare open-sources Kitesurf, its agent-first browser engine
**Event date:** Mon Nov 16

Cloudflare released the full source of Kitesurf — its stateless browser that parses HTML, applies CSS, runs page JS and renders screenshots on Workers with no Chromium container — under Apache 2.0 on GitHub, so teams can inspect, test locally and self-host against their own Cloudflare account. It speaks Chrome DevTools Protocol, so Puppeteer-class clients work unchanged.

**Why it matters:** Headless-Chromium agents are a chronic supply-chain and resource-hungry attack surface; a CDP-compatible engine with no browser binary shrinks that footprint and gives defenders an auditable alternative for agent web access.
**Action:** Agent-platform teams should evaluate it as a lower-risk execution path for automated browsing.

Sources: Help Net Security — "You can now self-host Kitesurf, Cloudflare's open-source browser for AI agents," Nov 16 2026 07:00 UTC (feed-verified) ; GitHub — https://github.com/cloudflare/kitesurf

### Also noted
- **vm2's own repo lists its next sandbox escapes** — deferred nesting fix and a monitoring entry in ATTACKS.md, r/netsec Mon 06:32 EST: https://www.reddit.com/r/netsec/comments/1xxibhv/
- **Gcore: 1,698 internet-visible AI offensive-security endpoints** (ARTEX/PentAGI/Cairn family) — gcore.com research, surfaced on HN Mon 09:26 UTC: https://gcore.com/blog/ai-offensive-security-endpoints-artex-pentagi-cairn (HN id 50515662)

---

## Just missed the window (footnotes)
- **Copeland/Dixell XWEB EVO root chain** — VulnCheck's 14-vulnerability disclosure (unauthenticated root on legacy builds; MAC+date "code of the day" creds surviving to current 5.8.8 firmware; NVD record Nov 12 20:18 UTC → first in-the-wild exploitation of CVE-2026-124014 by 22:00 UTC; five worst flaws will NOT be fixed — EOL March 16, 2026) was published Fri Nov 13, hours before this window; SecurityWeek's coverage (Nov 16 06:05 ET) confirms ~2,000 exposed EVO hosts (Onyphe) vs 108 XWEB Pro. Not related to the Iran-blamed October sabotage per VulnCheck. Action: never expose XWEB EVO to the internet; segment; migrate. — https://www.vulncheck.com/blog/copeland-xweb-evo-unauthenticated-root ; https://www.securityweek.com/hackers-probe-newly-disclosed-flaws-in-copeland-refrigeration-controllers/
- **Amazon MadPot: ARTEX AI agents' own records captured** (Oct 3–Nov 5; self-sustaining AI-credential-theft loop targeting LiteLLM/One API/Dify/New API/RAGFlow; moderate-confidence Chinese-nexus attribution; 11 IOCs published) — AWS Security Blog posted Fri Nov 13: https://aws.amazon.com/blogs/security/amazon-threat-intelligence-observes-threat-actors-running-cybercrime-campaigns-with-artex-ai-agents/ (in-window write-up: The Hacker News, Nov 16)
- **Tor 0.4.9.17 rapid-response DoS fixes** for the ongoing relay-flood attacks (IPv6-aware DoS defenses; Goulet: relays hit by "massive flood of client circuits"; operators report the attacker switching techniques mid-attack) — announced Fri Nov 13, per SecurityWeek Nov 16: https://www.securityweek.com/tor-updates-dos-defenses-again-as-relay-attacks-continue/
- **Kaspersky ICS CERT: BO Team (Ukraine-aligned hacktivist cluster) hit a Russian software developer's vCenter 7.0.3 via Log4Shell (CVE-2021-44228)**, reached the code-signing server; hourly pgcache cron backdoor fetching GSocket; supply-chain intent assessed, attack disrupted. Report published Nov 5; The Hacker News re-covered Mon Nov 16: https://thehackernews.com/2026/11/bo-team-exploits-log4shell-in-vmware.html
- **Cursor's post-mortem** (stolen Fireworks-issued key → <0.01% customer API credentials exposed → archived non-production source, support data, agent-trace summaries; Oct 29 trojanized Claude Code 2.1.312/313 build served ~11h; last attacker activity Nov 1, contained) — posted Fri Nov 13: https://cursor.com/blog/security-incident-update (in-window: BleepingComputer Nov 14, THN Nov 15)
- **Huntress: exposed GeoVision camera-management software abused to break into 13 orgs, disable Defender, deploy XMRig** (activity from Nov 8; IOCs 45.32.117[.]34, 156.246.94[.]183) — advisory Fri Nov 13: https://www.huntress.com/blog/geovision-camera-software-xmrig-cryptominer (in-window: Help Net Security Nov 16)

---

## Coverage notes
- **Verified count:** 8 reportable events (5 in-window items incl. 2 updates + 2 also-noted + roundup) plus 6 "just missed the window" footnotes — counted as distinct underlying events. Every in-window item's URL was fetched and read; lead stories read in full.
- **Ledger dedupe:** ~16 candidates suppressed as already reported or already footnoted once — incl. FBI ARTEX FLASH re-coverage (BleepingComputer Sun), TSMC Sunday confirmation (missed: tag used), UnrealIRCd KEV (missed:), PostgreSQL 52 CVEs (missed:), Mior/Skyfall AMD SEV-SNP (missed:), SilentAir (missed:), Trezor implants, FaceStealer takedown, wfx-loot, SignDrop, Operation Endgame, Project Zero "Full X" re-covers, Quantum Systems, 4EverAble2Shell, FAA ground-stop original.
- **Dead ends / limitations:** the search backend failed intermittently (5 queries returned no results/timeouts); Reuters/Guardian/HNS per-article URLs could not be resolved to slugs (Google News and Bing routes returned no usable links), so those items are cited via the outlet RSS feeds actually fetched; Tor's forum was cert-blocked; no CISA KEV additions were made Sat/Sun (catalog check: last adds Nov 13, both already footnoted); ransomware.live's recentvictims API lagged N0N's pre-cutoff spree, verified from the homepage instead.
