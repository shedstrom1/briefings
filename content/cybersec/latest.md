# The Cybersecurity News Briefing

Window: Friday, November 20, 2026 17:00 ET → Monday, November 23, 2026 07:20 ET (coverage of last 24h/weekend gap: Fri 17:00 → Mon 07:20 ET).

**TL;DR**
- Amazon Threat Intelligence: China-nexus actor ran Cairn autonomous AI agents that found an unpatched path traversal in MONAI Label (CVE-2026-133968, CVSS 9.8) and reached root on a university research server — no patched release installable as of Nov 21.
- USDA and FBI both suffered intrusions last week: USDA farmer-payment system targeted (NBC News/Guardian, Nov 20-21); FBI's National Crime Information Center breached (second since September; CNN, Nov 21).
- Bell Canada confirms an "agentic" actor breached systems on Nov 17 and stole enterprise customer data; Bell's Maximo pre-production environment was in scope (BleepingComputer/CBC, Nov 20).
- CISA added the April-week KEV batch Nov 20 — SAP NetWeaver CVE-2025-42884 (new), plus three already on our ledger (VMware vCenter, Cisco Expressway, Apache Roller), all due Nov 23.
- Ransomware leak sites: ~46 victims in 24h (N0N 27, Space Bears 4, Pixelleaks 3); Yara Sanat Ilia (Iran) and five clinic databases listed by Whitearmor.

## Vulnerabilities and patches

**MONAI Label CVE-2026-133968 (CVSS 9.8) — AI agents exploited an unpatched medical-imaging zero-day**
Event date: 2026-11-20 (AWS disclosure; campaign began September 8, exploitation confirmed in the wild since September).
Per Amazon Threat Intelligence (Nov 20), a China-nexus operator ran Cairn-framework autonomous agents against a publicly exposed Cairn control panel (found unauthenticated on the internet, Sept 10); over ~48 hours the agents mapped the MONAI Label API and reached arbitrary file write in about 11 minutes. In a later session the same flaw was used to plant a Python reverse shell, then trigger a training endpoint to spawn a process that connected back as root on a university research server. AWS assesses with moderate confidence a China nexus (Simplified Chinese prompts, CST timestamps, university targets) and with high confidence the flaw was a zero-day; it does not name a threat group. The flaw affects PUT /datastore/image and /datastore/label in MONAI Label ≤ 0.8.5 (CWE-24); authentication is disabled by default, leaving any reachable instance exposed; with auth enabled, the low-privilege ANNOTATOR role suffices. A source fix was merged Oct 19 (PR #1915), but per ThreatAft/IONIX/VentureBeat reports as of Nov 21 the newest installable PyPI package was still 0.8.5 (Nov 2024) — no patched release ships in the wild as of this brief. Action: inventory every MONAI Label deployment; enable authentication and restrict ANNOTATOR to trusted users; isolate from untrusted networks; unprivileged service account; hunt for files written outside the datastore, Python startup files and unexpected processes; rebuild confirmed-compromised servers; check for a fixed release.
Sources: Amazon Threat Intelligence (Nov 20, per secondary coverage); ThreatAft (Nov 21); IONIX (Nov 20, per threataft.com); VentureBeat (Nov 21, per threataft.com); GitHub Project-MONAI/MONAILabel PR #1915.

**SAP NetWeaver CVE-2025-42884 (6.5 vendor / 10.0 per Onapsis) — newly added to CISA KEV Nov 20**
Event date: 2026-11-20 (CISA KEV addition).
CISA's Nov 20 batch added CVE-2025-42884 (SAP NetWeaver improper neutralization of special elements in data query logic, CWE-943) to its Known Exploited Vulnerabilities catalog, with a Nov 23 federal remediation deadline under BOD 26-04. Per Onapsis's Nov 20 advisory, the flaw is an unauthenticated JNDI injection in SAP NetWeaver Application Server for Java EP-RUNTIME/EP-BASIS: unprotected SOAP operations control JNDI lookup environment properties, bypass platform restrictions, and can drive the server to an attacker RMI registry returning a malicious serialized object — RCE if an exploitable gadget chain exists. Onapsis rated it 10.0 (Critical) in Aug 2025; SAP's associated CVE record carries a 6.5 (Medium) rating. Patch: SAP Note 3660969 + 3660659.
Source: CISA alert (Nov 20); Onapsis advisory ONAPSIS-2026-0100 (Nov 20).

**CISA KEV add-batch Nov 20 (three of four ALREADY on this ledger)** — CVE-2026-123890 (vCenter; reported 11/19), CVE-2026-76466 (Expressway; reported 11/19), CVE-2026-129448 (Roller; footnoted 11/18), plus SAP NetWeaver CVE-2025-42884 (new, above). CISA's alert confirms all four are exploited; federal teams must patch by Nov 23.
Source: CISA, cisa.gov (Nov 20).

## Attacks and campaigns

**USDA and FBI breached in fresh intrusions (Nov 20–21)**
Event date: 2026-11-20/21.
NBC News reported Friday (Nov 20) that hackers targeted a system the US Department of Agriculture uses to make farmer payments, which officials described as a potential back door into more sensitive material — "a bunch of scary biologic issues" worth guarding, per a former official. Separately, CNN reported Friday (Nov 20/21) that the FBI was investigating an intrusion of the National Crime Information Center (NCIC), a nationwide database linking federal, state and local law enforcement, used daily for warrants and background checks — the second breach of FBI systems since ShinyHunters targeted an FBI jobs site in September. An FBI official told CNN the bigger concern is whether hackers could compromise the accuracy of information officers rely on. FBI Director Kash Patel touted the FBI's cybersecurity work in a Fox News appearance the Thursday after officials learned of the incident, claiming Operation Winter Shield keeps systems updated, while Trump downplayed the NCIC hack on Fox.
Sources: The Guardian (Nov 21); CNN (Nov 21); Reuters (Nov 20).

**Bell Canada: agentic actor breached, enterprise data stolen (Nov 17, disclosed Nov 20)**
Event date: 2026-11-17 (disclosed 2026-11-20).
Bell Canada confirmed an "agentic threat actor" accessed systems on Nov 17 and exfiltrated enterprise customer data, including business contacts (names, job titles, corporate email, phone numbers, business addresses), ticket titles and some IP addresses from a Maximo pre-production environment used to order and provision telecom circuits. Bell said no credentials or sensitive personal information were exposed, denied impact on customer services, and is sharing IOCs with enterprise customers and the Canadian Centre for Cyber Security — though no public IOCs or attribution have been released. This is the second "agentic" breach in our surveillance window; Bell did not identify how many enterprise customers were affected.
Sources: BleepingComputer (Nov 20); CBC News (Nov 20); Bell Canada's customer notice.

**Ransomware leak-site batch (Nov 20–22): N0N 27+1 victims, ~46 total in 24h**
Event date: 2026-11-22 (report current Nov 23 12:08 UTC).
Ransomnews shows 46 organizations listed in the last 24 hours (vs 14 yesterday, 51.3 seven-day average), 12 operators led by N0N (27), Space Bears (4), Pixelleaks (3). Named listings: Yara Sanat Ilia (Iranian screw compressor manufacturer, seppitel/Sepidar, Pixelleaks, timer Nov 27); Piment Interim (France, Space Bears); Master Result Soluções Contábeis (Brazil, Space Bears); Digital Instruments & Control Systems (India, Space Bears); Staufen Baumaschinen GmbH (Germany, Space Bears); kinkisharyo.com (Everstake, 2.07 TB); Fisher & Paykel Healthcare (Falcon); Clinic Sagrada Familia + Casanare + Pajonal + Putumayo + Urus (Whitearmor, Armenia, five databases, no answer requested); clincal case: University Medical Imaging and Corrigan Moving Systems (Aoqin Leaks, US); cybage-software-pvt-ltd (INC Ransom — employees and family PII, contracts/NDAs); Suzlon Energy (Blackout). All listings are operator claims, not verified incidents.
Source: Ransomnews.com (updated Nov 23).

## Defensive tools and advisories

**Intel 471: ransomware/access-broker focus on manufacturing (Nov 22)**
Event date: 2026-11-22 (report published).
Intel 471 tracked 1,379 ransomware and data-extortion claims involving manufacturers Jan–Oct 2026 (16.8% of victims monitored), and separately observed initial-access brokers offering access affecting 172 manufacturing entities in the same period. It highlights remote-access and supply-chain exposure and movement from enterprise networks toward plant systems; the figures are claims, not confirmed incident counts.
Source: Security Boulevard, Nov 22 (Intel 471 research).

**Careers360 and Too Lost added to HIBP (Nov 20)**
Event date: 2026-11-20 (HIBP listing).
Careers360 (Indian education/careers platform): ~3.61M unique emails, 22M records, names/phones/castes/academic records; HIBP breach date Nov 14. Too Lost: ~724,323 accounts. Both added to Have I Been Pwned Nov 20. Background/context only — not verified incident reports; treat as breach-notification listings, not confirmed compromises.
Source: Have I Been Pwned (per dev.to / technobezz / privacyon, Nov 20-22).

**Negative checks**
- KEV: no additions after Nov 20 batch (no additions Nov 21–22). Count stands at 1,790 (Nov 22).
- No new "Month-end" bulletins, no new federal-lawsuit announcements in window.

## Coverage notes
- Verified items reported: 6 (1 lead-degraded: MONAI CVE-2026-133968; SAP NetWeaver CVE-2025-42884; USDA + FBI NCIC breaches; Bell Canada agentic intrusion; ransomware batch; Intel 471 manufacturing research + HIBP also-noted). Zero items were dropped mid-window; no deaths in discovery.
- Candidates suppressed as already reported (ledger): vCenter CVE-2026-123890 (reported 11/19), Cisco Expressway CVE-2026-76466 (reported 11/19), Apache Roller CVE-2026-129448 (missed-footnote 11/18), Puppet CVE-2026-115731 (11/17), Apache Axis CVE-2026-90436 (11/18), Synology CVE-2024-10441 (11/18), LiteLLM CVE-2026-40217 (11/18), Java4Shell updates (11/17–19), KPMG Canada breach (disclosed Nov 18, pre-window), "week in security" roundup pages (no primary source), HIBP 2026-11-22 lookalike domains (no in-window primary source). Roughly 10+ candidates suppressed.
- Not re-counted: JPM/Visa/Mastercard n0n-named clients (Nov 19, already reported) — not re-listed.
- Dead ends: The Hacker News, FaceBook/Reddit not directly searched this run; web searches for Nov 21–22 items were intermittently throttled — coverage relies on targeted search + index reads (ransomnews, dev.to KEV rollup, Security Boulevard daily OT list, Guardian/CNN/NYPost/Reuters).

*Report generated by the Cybersecurity News Briefing cron (ab710a183f1c).*
