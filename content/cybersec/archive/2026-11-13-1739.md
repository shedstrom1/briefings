# Cybersecurity News Briefing — Friday, November 13, 2026, 17:00 Edition

**Window:** 2026-11-13 12:20 EST → 2026-11-13 17:20 EST (5-hour Friday-afternoon sweep; new items only, ledger-deduped)

## TL;DR

- **FBI FLASH-20261113-001 (Friday):** the FBI formally warns that the open-source ARTEX autonomous pentest framework is driving intrusions from targeting to privileged access in under three hours; some 1,600+ ARTEX consoles are publicly exposed and attacker instances overwhelmingly run DeepSeek-Flash-class models.
- **FAA nationwide ground stop (Friday afternoon):** the first since January 2023, after communications problems hit the 22 regional air-traffic control centers; DHS says there is no indication yet of a "nefarious attack," FAA technicians are investigating.
- **Microsoft 365 outage resolved:** after ~27 hours of failed fixes, Microsoft said the underlying Exchange Online issue was fixed, with "broad recovery and stability" — most services back by about 3 p.m. ET.
- **Quantum Systems confirms active cyberattack:** the German drone maker says its late-October incident was an attack by "hostile actors" via a malicious QBase ground-control update; counterintelligence (MAD), BfV and BSI are responding.
- **Friday KEV batch (pre-window footnote):** CISA added an actively exploited UnrealIRCd server-authority flaw (CVE-2026-114713) and the Check Point Gaia LPE (CVE-2026-123039) at 07:00 ET this morning.

---

## Attacks and campaigns

### FBI FLASH-20261113-001: ARTEX autonomous intrusions reach privileged access in under three hours
**Event date:** Friday, November 13, 2026 (FBI FLASH, TLP:CLEAR)

The FBI released a FLASH alert Friday detailing TTPs and IOCs for ARTEX, the open-source LLM-driven agentic platform weaponized since at least August 2026 across financial services, government, technology, defense, semiconductor, energy, healthcare, manufacturing, telecom and legal sectors. The alert says ARTEX "significantly reduces the time required to chain attack techniques, with some intrusions progressing from initial targeting to privileged access in under three hours," and cites the ~67,000-person South Korean financial-sector exfiltration and the ConnectWise Cloud Backup supply-chain compromise (>2,250 downstream tenants). Although original developer Li Puhua ("Autumn-27") removed the GitHub repo in October, the FBI stresses the framework "continues to proliferate through community forks, localized variations, archived copies, and a commercial version (ARTEX PRO)" — with researchers counting over 1,600 publicly exposed consoles as of early November. Analysis of attacker-controlled instances found Chinese-origin models most prevalent, "with DeepSeek Flash family models dominating usage due to their balance of capabilities, relatively low cost, and lack of observable guardrails when framed for penetration testing." The FBI says ARTEX agents map trust relationships between organizations and preferentially hit under-hardened estate — partner/supplier portals, mobile backends, employee apps, sales-support systems, legacy services.

**Why it matters:** This is the US government's most authoritative statement to date that agentic AI attack frameworks are an industrialized, criminal-and-state-accessible capability — validating the ARTEX campaign trail this brief has tracked since October (VAPOR PANDA, Storm-0940, WorkOS, 800–1,428 exposed consoles). The "machine speed" sub-3-hour dwell-to-privilege window breaks human-paced IR, and the console-exposure numbers imply the operator population is far larger than the targeted victims reported so far.
**Action:** The FBI urges organizations — especially those with internet-exposed applications, self-service portals, cloud infrastructure and partner/supplier systems — to implement the FLASH's Mitigations section; defenders should also hunt for unauthenticated ARTEX-family consoles per the Censys/GitGuardian IOCs already published.

Source: Threat Overview of ARTEX Autonomous Penetration Testing Framework — FBI/IC3 FLASH-20261113-001, November 13, 2026 — https://www.ic3.gov/CSA/2026/261113.pdf
Source: FBI flash alert says Artex tool automating cyberattacks at 'machine speed' — The Record, November 13, 2026 — https://therecord.media/artex-ai-tool-fbi-flash-alert

### FAA orders first nationwide ground stop since 2023 over ATC communications failure
**Event date:** Friday, November 13, 2026 (ground stop imposed ~3:45 p.m. ET)

The FAA grounded all departing US flights Friday afternoon "due to extraordinary circumstances and for reasons of safety" after communications problems struck the 22 regional air-traffic control centers that direct en-route traffic — the first nationwide ground stop since the January 2023 NOTAM system failure (and before that, only after 9/11). Transportation Secretary Sean Duffy said technicians are investigating the comms issue; Homeland Security Secretary Markwayne Mullin said there was "no indication yet that this was a nefarious attack against us. It's just a telecommunications outage." No cause had been announced as of the 17:20 ET fire time.

**Why it matters:** A nationwide ATC comms outage is exactly the scenario cyber-adjacent risk teams must treat as a potential hostile act until disproven — and the last unexplained NOTAM outage became a national-security story. Whether the root cause is configuration, equipment or intrusion will shape critical-infrastructure comms-hardening requirements; treat "no indication yet" as provisional.
**Action:** Track the NTSB/FAA cause determination; CNI operators relying on FAA feeds or airspace APIs should validate their fail-open behavior.

Source: Flights halted at airports across US due to air traffic control communications problems — AP News, November 13, 2026 — https://apnews.com/article/flights-halted-airports-faa-f81b0904331dafad6133f291e5f531a8
Source: FAA Ground Stop Issued Due to 'Extraordinary Circumstances' — SecurityWeek, November 13, 2026 — https://www.securityweek.com/

### Update — Quantum Systems confirms "active cyberattack by hostile actors" via compromised QBase update server
**Event date:** Friday, November 13, 2026 (vendor confirmation; underlying intrusion late October 2026). Ledger: quantum-systems-drone-relift (reported today 12:20 fire).

BleepingComputer reports the German reconnaissance/interceptor-drone maker confirmed its late-October incident "was part of an active cyberattack by 'hostile actors'," and that the compromise point was the update-distribution server for QBase, its ground-control and mission-planning software — a malicious QBase update file, per Der Spiegel and FT reporting citing internal sources. Quantum Systems says it was likely targeted for its Ukraine support (tens of thousands of interceptor drones funded in October); German military counterintelligence (MAD), the BfV and BSI are engaged. Der Spiegel identified the Vector/Falke drone as one of the affected platforms; how many customers installed the rogue update is unclear.

**Why it matters:** The Bundeswehr lifted the precautionary grounding today (covered at 12:20), but the vendor's own confirmation upgrades this from "malicious update scare" to an acknowledged supply-chain attack on European defense ground-control software — with no attribution and unknown install counts, every QBase customer is still in scope.
**Action:** QBase/Vector operators should verify update provenance and integrity against vendor hashes and hunt for the rogue update window (late October).

Source: German military drone operations disrupted by cyberattack — BleepingComputer, Bill Toulas, November 13, 2026 12:31 PM ET — https://www.bleepingcomputer.com/news/security/german-military-drone-operations-disrupted-by-cyberattack/

### Update — Microsoft 365 outage fixed after ~27 hours; "broad recovery and stability," cyberattack not ruled out
**Event date:** Friday, November 13, 2026 (fix announced ~2:00–3:00 p.m. ET). Ledger: M365-outage-MO1550821 (Nov 12 17:20 fire) → m365-outage-20h-nofix (Nov 13 12:20 fire).

Microsoft announced Friday afternoon it had fixed the underlying issue behind the longest-running M365 outage of the year (MO1550821/EX1550543), which began Thursday ~13:00 ET when a failed server-health-check component took Exchange Online — and dependent Outlook, Teams, SharePoint, Copilot connectors — down for 120,000+ Downdetector reporters across ~74 countries. Recovery had repeatedly failed under traffic through this morning; by ~3 p.m. ET most services were back with "broad recovery and stability." Microsoft has still not published a root cause and has not ruled out a cyberattack.

**Why it matters:** The five-hour delta between "fixes keep failing" (12:20 fire) and "fixed" (15:00) closes an incident that disrupted security operations (Defender portal, agent connectors) as much as mail. The unresolved root cause — and Microsoft's refusal to exclude hostile activity — leaves a post-mortem obligation; SLA-credit claims are now the practical action for affected orgs.
**Action:** File SLA-credit claims for MO1550821/EX1550543 windows; re-validate any mail-flow or connector failover changes made during the outage.

Source: Microsoft 365 Outage Resolving After Nearly 24 Hours — MacRumors, November 13, 2026 (~2:20 p.m. ET) — https://www.macrumors.com/2026-11-13/microsoft-365-outage-resolving/
Source: Microsoft 365 services slowly come back online after global outage — Computerworld, November 13, 2026 — https://www.computerworld.com/article/4248590/m365-services-slowly-come-back-online-after-global-outage.html
Source: Microsoft Outlook, 365 mostly fixed after 24-hour outage — USA Today, November 13, 2026 — https://www.usatoday.com/story/tech/2026-11-13/microsoft-outage-outlook-updates-friday/92752697007/

---

## Vulnerabilities and patches

No new in-window vulnerability disclosures survived verification this cycle. The morning's CISA KEV batch and this morning's Check Point PHP2Shell-ESXi exploitation story predate the 12:20 boundary (see footnotes); SecurityWeek's "Citrix Patches 10 Critical NetScaler Vulnerabilities" (Nov 13) is the same Thursday CTX697305 release already reported on Nov 12 and is suppressed.

## Defensive tools and advisories

No new tool releases verified inside the window beyond the advisories above.

---

## Also noted

- **Walgreens (Walgreen Co.) listed on a ransomware leak site** — ransomware.live detected the pharmacy-chain listing ~1:20 p.m. ET Friday; group name not confirmed at fire time. Source: https://www.ransomware.live/ (November 13, 2026).
- **Friday afternoon leak batch (ransomware.live, ~1:20–4:50 p.m. ET):** Sportsman's Guide (outdoor e-commerce), Advantage Archives (community digital records), Frey Properties of Highland LLC (real estate), Tidewater Inn (Maryland hospitality), a new-group debut claiming financial/broker records + "86GB of Outlook" data, and two Pixelleaks pending-publication timers (Nov 16–17) naming International Utility Specialists Inc., BEAM Society (iBeam.hk) and SPI Corp/SPI Energy (Republic of Congo). Groups otherwise unconfirmed; claims unverified. Source: https://www.ransomware.live/ and https://www.recentbreaches.com/ (November 13, 2026).

## Just missed the window

- **UnrealIRCd CVE-2026-114713 added to CISA KEV (Nov 13, 07:00 ET):** labeled-response module flaw (6.0.0 < 6.2.7.1) lets any ordinary user inject server-to-server commands executed with full server/IRCOp authority on linked-server networks (KILL, GLINE, config changes); maintainer reports active exploitation; fix 6.2.7.1 / `hot-patch bundle-20261107`. Severity: "critical" per maintainer — CVSS not specified by source. CISA alert (two KEV additions) published 2026-11-13T12:00Z, before the 12:20 boundary. Sources: https://www.cisa.gov/news-events/alerts/2026-11-13/cisa-adds-two-known-exploited-vulnerabilities-catalog ; https://forums.unrealircd.org/viewtopic.php?t=9487 ; https://ccb.belgium.be/advisories/warning-actively-exploited-cve-2026-114713-affects-unrealircd-patch-immediately
- **Same KEV batch — Check Point Gaia CVE-2026-123039 LPE added Nov 13, 07:00 ET** (base PHP2Shell-chain story reported this morning's 07:20 fire). Source: https://www.cisa.gov/news-events/alerts/2026-11-13/cisa-adds-two-known-exploited-vulnerabilities-catalog

## Coverage notes

- **Verified count: 7 qualifying items** (3 new events + 2 material updates + 2 also-noted leak-batch entries), each from fetched pages dated inside 12:20–17:20 EST, plus 2 "Just missed" footnotes (first-time use for both).
- **Ledger dedupe:** ~13 candidates suppressed as already reported — Trezor 3-implant confirmation (BC 2:38 p.m. re-cover of the Nov 12 `missed:` item), Citrix "10 critical NetScaler" (= Nov 12 CTX697305), Quantum Systems grounding-lift (12:20), M365 MO1550821/EX1550543 ongoing thread, Check Point PHP2Shell-ESXi (11:21 a.m. BC, = 07:20 CVE-2026-123039), plus r/netsec re-covers of wfx-loot, NvRoot/CVE-2026-47591, PostgreSQL CVE-2026-6460, ARTEX sig-pack, OWASP Neocloud Top 10, Qlik reverse shells, PHP2Shell deep-dive, N0n #216.
- Dead ends / limitations: r/netsec `search.rss` 403'd on the fallback sweep (skill-documented intermittent failure), so post timestamps for two promising community items — "Pwning Home Assistant Green: Pre-Auth RCE in Eclipse Mosquitto" and "Claude Code VSCode Plugin: Workspace Escape Via Misleading Permission Prompt" — could not be pinned to the window; both excluded and logged here rather than chased. The FBI FLASH page shows no publication time (dated November 13); Friday release is corroborated by The Record ("released... on Friday") and the IC3 document ID. BleepingComputer's Trezor story was skipped per the one-time `missed:` rule. DDGS result ages ("22 hours ago") proved unreliable this run (mis-timed on the clearly-afternoon FAA stories) and were disregarded in favor of page bylines.
