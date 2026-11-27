# Cybersecurity News Briefing — Air Force Cloud One breach; SVDB vulnerability database (Nov 27, 12:00 ET)

**Window: 2026-11-27 07:21 → 12:57 EST** (12:00 fire, run late; previous fire swept to 07:21). New-items-only — events already carried in earlier editions are excluded. This is a short midday window following a heavy 07:20 edition, so the verified new-item count is low.

- **US Air Force Cloud One breach (lead):** Reuters exclusive, broken ~mid-morning ET — hackers reached the control plane of the Air Force's flagship multi-cloud platform during an upgrade; attackers, scope and data loss all unknown. Severity not specified by source (breach disclosure, not a CVE).
- **Defensive tool:** Positive Security publishes SVDB, an LLM-enriched, structured CVE/GHSA vulnerability database with an open schema and daily updates.
- No new in-window CISA KEV additions and no new in-window exploited-vulnerability disclosure — most vuln news in this window was a re-report of items already carried (Cacti 4HTTP2ROOT, Drupal, SAP, NFS2ROOT, WSUS 2028).
- Two substantial disclosures landed just before the window and are footnoted once below (WeaveLotus espionage campaign; DIGIT e-government SQLi CVE-2026-136706).

## Attacks and campaigns

**US Air Force Cloud One platform breached via software flaw; control plane reached**
*Event date: 2026-11-27 (~09:00–10:00 ET, Reuters exclusive)*

Hackers breached Cloud One, the US Air Force's flagship enterprise cloud platform, by exploiting an unidentified software vulnerability, according to a Reuters exclusive carried widely by BleepingComputer, the Guardian, Forbes, The Hill, Bloomberg Law and others. The intrusion is reported to have been discovered within the past month during a platform upgrade; it disrupted application migrations and software updates, and one source said the attackers reached the platform's control plane. Cloud One hosts applications for military operations, personnel management, logistics and maintenance, and feeds the theater-integrated combat munitions system (TICMS), which tracks munitions inventories at more than 200 locations. US Cyber Command was called in to assess impact; the identity of the attackers, the full scope and whether any data was stolen remain unknown. The Air Force declined to comment on the status of the affected systems. It is not known whether this breach is connected to the recent run of federal intrusions (FBI NCIC, CIA/ODNI/NGA OSINT system, FinCEN). It also remains unknown whether ARTEX or another AI-driven attack framework was used.

- **Severity:** Not specified by source (breach disclosure; no CVSS rating published).
- **Why it matters:** Cloud One is a central hub for Air Force command-and-control and logistics, so control-plane access could in theory expose aircraft operational status or spare-parts movement. It is the latest in a cluster of federal breaches and underscores the exposure of large multi-cloud estates with many credentialed access points.
- **Action:** No vendor or agency remediation guidance published at time of writing; watch for an Air Force or Cyber Command statement and any indicators of compromise.

Sources:
- "US Air Force cloud platform reportedly hacked in latest federal breach," BleepingComputer, 2026-11-27 10:09 ET — https://www.bleepingcomputer.com/news/security/us-air-force-cloud-platform-reportedly-hacked-in-latest-federal-breach/
- "EXCLUSIVE: Hackers breach US Air Force flagship Cloud One computing platform," Reuters, 2026-11-27 — https://www.reuters.com/world/hackers-breach-us-air-force-flagship-cloud-one-computing-platform-2026-11-27/
- "Hackers reportedly breach US air force's flagship cloud computing platform," The Guardian, 2026-11-27 — https://www.theguardian.com/technology/2026/nov/27/hackers-breach-us-air-force-cloud-computing

## Defensive tools and advisories

**Positive Security releases SVDB — an LLM-enriched, structured vulnerability database**
*Event date: 2026-11-27 (public by ~08:20 ET)*

Positive Security published SVDB, a structured, LLM-enriched database that layers deterministic, machine-readable data over public CVE and GHSA records. The schema separates "effects" (code execution, data read, DoS, with sub-fields such as credentials and bounds) from nine "attack path" dimensions (vector, privileges, interaction, conditions, code location, exposure, exploitation-in-the-wild, protocol, file type), to help triage large scan reports by real attack surface rather than raw CVSS. The project argues that inflated severities, inaccurate vectors and slow dispute handling make raw CVE data hard to use at scale; it is releasing the data and tooling and promises daily updates. The accompanying analysis reports memory-management vulnerability rates by language and a sharp rise in new CVE publication in 2026.

- **Severity:** Not specified by source (a defensive data/tool release, not a vulnerability).
- **Why it matters:** Gives defenders a deterministic, openly documented way to reprioritize millions of scan findings — directly relevant where critical-severity volume far exceeds patching capacity.
- **Action:** The dataset and schema are available at https://svdb.positive.security/ (project blog: https://positive.security/blog/svdb).

Source:
- "SVDB: A structured database for better vulnerability management," Positive Security, 2026-11-27 — https://positive.security/blog/svdb

## Also noted

- Ransomware leak-site activity around midday (operator claims, unverified): Brit Ltd/Brit Insurance (INC Ransom), Friedl Richardson (The Gentlemen), Ferrometalco and a US CNC company (Soul), MedMira (Deadlock), and a cluster from Iah647. RansomNews tallied 29 listings in the 24 hours to 18:37 UTC — down from 34 the prior day. Source: https://ransomnews.com/ransomware-attacks-today/

## Just missed the window

- **WeaveLotus / Operation RedOps (Vietnam-linked espionage):** SOCRadar published its research on Nov 26 (before this window); The Hacker News carried the in-window writeup at 11:36 ET on Nov 27. The campaign compromised five government, military and legislative organizations in Thailand and the Philippines and ran reconnaissance against 30+ Indonesian agencies to collect South China Sea intelligence, exploiting GeoServer CVE-2024-36401 (CVSS 9.8), osTicket CVE-2021-42235 (CVSS 9.8), JasperReports default credentials and Laravel debug mode. Source: https://thehackernews.com/2026/11/suspected-vietnam-linked-hackers-target.html
- **India DIGIT e-government platform SQLi (CVE-2026-136706, CVSS 9.3 per CERT-IN):** The Register published at 01:19 UTC Nov 27 (20:19 ET Nov 26), pre-window. The unauthenticated SQL injection affects the User and Access Control Service of a platform used by governments serving 2+ billion people; a fixed source release is available. Source: https://www.theregister.com/security/2026/11/27/free-e-government-platform-that-serves-two-billion-people-in-ten-countries-has-critical-sql-injection-bug/5312449

## Coverage notes

- **2 items verified in-window** (1 breach disclosure + 1 defensive-tool release), plus 1 also-noted leak-site roundup and 2 just-missed footnotes. No padding: this is a short 5.5-hour midday window immediately after a heavy 07:20 edition.
- **Ledger dedupe: ~20 candidates suppressed as already reported** in prior editions, including Cacti 4HTTP2ROOT, Q-Dir/DesktopOK, Vinci RAT, Switzerland AI attacks, WSUS 2028 removal, Drupal CVE-2026-136716, SAP GitHub incident, the emergency Windows Server hotpatch, NFS2ROOT, FinCEN/CIA-ODNI/FBI-NCIC federal breaches, myColorado, 1Password, Flight Centre, FIFA, the CISA 10-KEV batch, ShinyHunters/Inception, GeoServer and Idunno AntiCsrf.
- **Negative check:** no new CISA KEV additions in-window (latest batch remains Nov 25); realinfosec logged 0 new KEV-linked exploits on Nov 27. CISA advisory pages did not fetch in this run.
- **Coverage limitations:** the Reddit lane was unavailable this fire (anonymous Atom feed returned 404/429; no credentials configured) — logged, not retried past one strike. Two Cyber Security News candidates (Slovenia freezer-100°C incident, 3,089 Telegram spy-recruitment posts) failed to fetch (404) and could not be verified; they were dropped rather than cited. Hacker News, multiple news rivers and 5+ independent outlets were swept.
