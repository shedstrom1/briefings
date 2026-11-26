# Cybersecurity News Briefing — Nov 25, 2026, 5:00 PM ET

**Window:** 2026-11-25 12:00 → 17:20 EST (previous fire to now; new-items-only). All times ET.

**TL;DR**
- CISA added a CVSS 9.8 Critical Juniper Session Smart command-injection flaw (CVE-2026-103406) to its Known Exploited Vulnerabilities catalog on Nov 25, with a Nov 28 federal deadline; Juniper has confirmed at least one in-the-wild exploit.
- Microsoft published definitive ARTEX threat-actor research and hunting guidance, naming Salt Typhoon and Crystal Typhoon (China) and Onyx Sleet and Pearl Sleet (North Korea) among those abusing the agentic AI attack framework.
- FIFA confirmed a breach after the Handsome Boy Hacking Club leaked stolen data — "a limited set of personal and business information."
- Italy's CSIRT and Switzerland's NCSC warned of autonomous AI-agent attack waves against critical infrastructure; Switzerland advises patching internet-facing systems within 24 hours.
- A Securin study of 236 water-technology vendors found 20 exploited flaws absent from CISA's KEV catalog and 791 internet-exposed Rockwell controllers.

## Vulnerabilities and patches

**Juniper Session Smart OS command injection added to CISA KEV (CVE-2026-103406)** — Event date: Nov 25, 2026 (KEV addition; underlying flaw patched Oct 28).
Severity: CVSS 3.1 9.8 Critical (Juniper CNA, as listed by NVD).
This is an **update on previously reported** (ledger 2026-10-29, `veeam-juniper-oct29-criticals`), when Juniper's Oct 28 JSA112183 bulletin fixed 18 Session Smart flaws with no known exploitation. CISA added CVE-2026-103406 to the KEV catalog on Nov 25 and set a Nov 28 remediation deadline under BOD 26-04, with forensic triage required. The flaw is an OS command injection in the AutomatedProvisioner component that lets a remote, unauthenticated attacker run commands as root via a crafted salt minion ID. Juniper's Nov 24 bulletin update states "at least one instance of malicious exploitation" was reported to its SIRT; neither Juniper nor CISA has attributed it, and ransomware use is listed as unknown. Affected versions are below 7.0.5, 7.1.7 and 7.2.3.
**Why it matters:** routers and SD-WAN appliances sit at the network edge, and a no-authentication root RCE with confirmed exploitation and a three-day federal deadline is about as urgent as it gets.
**Action:** upgrade to 7.0.5, 7.1.7, 7.2.3 or later; Juniper lists no workarounds.
Sources: NVD (CVE-2026-103406 detail; KEV date added Nov 25, 2026) — https://nvd.nist.gov/vuln/detail/CVE-2026-103406 ; "CISA gives agencies until Nov. 28 to patch an exploited Juniper router flaw," MLQ.ai, Nov 25, 2026 18:53 GMT — https://mlq.ai/news/cisa-gives-agencies-until-nov-28-to-patch-an-exploited-juniper-router-flaw/ ; "Juniper Session Smart Conductor CVE-2026-103406 Exploited and in CISA KEV: Upgrade Now," Rescana, Nov 25, 2026 17:41 GMT.

## Attacks and campaigns

**Microsoft attributes ARTEX use to Salt Typhoon and Crystal Typhoon, publishes hunting guidance** — Event date: Nov 25, 2026.
Severity: Not specified by source (agentic attack framework, no CVSS).
Microsoft Threat Intelligence published a detailed breakdown of ARTEX, an AI-powered penetration-testing framework released in July 2026 that it says is now used by state-backed and financially motivated actors including Storm-0940, Storm-3358, Onyx Sleet, Pearl Sleet, Crystal Typhoon and Salt Typhoon. Operators set goals and ARTEX's agents work through reconnaissance, credential attacks and web-application exploitation at machine speed, reaching persistent privileged access in minutes. Microsoft stresses ARTEX introduces no new attack techniques — the durable detection signal is behavioral: bursts of varied requests and rapid retries from a single source. The report ships network, Entra, Graph, web-server and WAF hunting guidance and Perception prompts for Defender XDR. The Record notes the report emerged hours after the CIA/ODNI breach reports that named ARTEX.
**Why it matters:** the most-cited AI attack tool of the past month now has an authoritative actor roster and a concrete detection playbook, which is what defenders need to move from headline fatigue to actual hunting.
**Action:** enable automatic attack disruption in Defender XDR; reduce internet-facing exposure, secure credentials/secrets and limit lateral-movement paths.
Sources: "What defenders need to know about ARTEX, the agentic AI framework threat actors abuse," Microsoft Security Blog, Nov 25, 2026 18:09 GMT — https://www.microsoft.com/en-us/security/blog/2026/11/25/what-defenders-need-to-know-artex-agentic-ai-framework-threat-actors-abuse/ ; "Microsoft: China's Salt Typhoon hackers are using Artex," The Record, Nov 25, 2026 21:18 GMT — https://therecord.media/microsoft-china-salt-typhoon-using-artex-ai-tool

**FIFA confirms breach after extortion attempt failed** — Event date: Nov 25, 2026.
Severity: Not specified by source.
An **update on previously reported** (ledger 2026-11-19, `hbhc-fifa-uchealth-cluster`, then carried as an unverified leak-site claim). FIFA confirmed hackers gained "unauthorised access to a limited set of personal and business information" and says it is working with authorities and law enforcement. The Handsome Boy Hacking Club had claimed it stole more than 12 GB from FIFA's Microsoft environment — including identity and immigration documents, financial, legal and governance records — and failed to extort the organization before publishing the data on a cybercrime forum. FIFA did not say how many people are affected or answer follow-up questions.
**Why it matters:** it upgrades an unverified criminal claim to a confirmed victim disclosure, which matters for anyone whose data may be in the dump.
Sources: "FIFA says hackers accessed 'limited set of personal and business information'," The Record, Nov 25, 2026 20:40 GMT — https://therecord.media/fifa-data-breach-limited ; "FIFA says hackers accessed a limited set of personal and business data," MLQ.ai, Nov 25, 2026 21:38 GMT.

## Defensive tools and advisories

**Italy and Switzerland warn of autonomous AI-driven attacks on critical infrastructure** — Event date: Nov 25, 2026 (Italian CSIRT and combined coverage; the Swiss NCSC's own advisory was Nov 24).
Severity: Not specified by source.
CSIRT Italia says it detected large-scale campaigns in which autonomous AI agents found and exploited vulnerabilities and misconfigurations on exposed endpoints — in one case a DeepSeek Harness setup had a coordinator assign tasks to five agents spanning reconnaissance through persistence, chaining low-impact flaws and writing malicious code in real time to evade endpoint defenses. Switzerland's NCSC confirmed its first successful AI-assisted compromises, affecting organizations and critical-infrastructure operators in at least two sectors, and says the number of freely available AI-attack agents has nearly doubled since October. Both agencies named ARTEX, CyberStrikeAI, DeepSeek Harness – Red Team and PentAGI.
**Why it matters:** two national CERTs independently moving from "theoretical risk" to confirmed, successful autonomous intrusions is a meaningful threshold crossing for critical-infrastructure defenders.
**Action:** patch internet-facing systems (Switzerland: within 24 hours), add MFA or passkeys, enforce least privilege, disable unneeded remote-management interfaces and monitor control-plane and outbound traffic.
Sources: "Italy and Switzerland warn of autonomous AI-driven cyberattacks," BleepingComputer, Nov 25, 2026 14:39 EST — https://www.bleepingcomputer.com/news/security/italy-and-switzerland-warn-of-autonomous-ai-driven-cyberattacks/ ; "Switzerland Confirms First AI-Agent Cyberattacks on Local Organisations," Security Affairs, Nov 25, 2026 21:06 GMT.

**Securin "Water Supply Chain Watch": US water tech supply chain is leakier than public records show** — Event date: Nov 25, 2026.
Severity: Not specified by source (20 exploited flaws identified; individual CVSS scores not stated).
A study of 236 vendors serving US drinking-water, wastewater and industrial-water systems found almost twice as many vulnerabilities as public records show, including 20 flaws known to be exploited that were not in CISA's KEV catalog. Five vendors — Rockwell, Advantech, Schneider, Siemens and ABB — account for 191 of 376 water-tagged advisories. Securin found 791 Rockwell MicroLogix 1400 controllers exposed to the internet as of Nov 17, and cited Forescout data that seven in ten exposed US Rockwell controllers sit on mobile-carrier networks, where poorly secured cellular modems installed by contractors give attackers a path in. The report notes the July intrusions at more than 100 water systems did not require exploiting a software bug at all.
**Why it matters:** it reframes water-sector risk from "patch the CVEs" to "find the undocumented modems and change the default passwords" — the gap between advisory counts and real intrusions.
**Action:** inventory every cellular modem including contractor-installed ones, keep them off the open internet and put an MFA gateway in front of the modem/controller pairing.
Sources: "The security of US water systems is even leakier than we thought," The Register, Nov 25, 2026 19:40 GMT — https://www.theregister.com/security/2026/11/25/the-security-of-us-water-systems-is-even-leakier-than-we-thought/5312243 ; "Securin report identifies cybersecurity gaps across water supply chain," WaterWorld, Nov 25, 2026 14:54 GMT.

**Also noted**
- Meridian Capital Group, a multifamily debt brokerage, confirmed an intrusion after the Qilin ransomware group claimed it (MLQ.ai / Bisnow, Nov 25, 2026 13:28 EST).
- The n0n extortion crew claimed it hit the financial sector using AI agents (National Law Review, Nov 25, 2026 14:51 EST) — consistent with its earlier JPMorgan/Visa/Mastercard claims, with no new specifics.

**Just missed the window**
- Defang disclosed on Nov 24 that an attacker copied its production credentials and a copy of its main database on Nov 12, potentially exposing GitHub App tokens and deployment-state secrets; it hit the Hacker News front page in-window, but the disclosure itself predates the window (defang.io, Nov 24, 2026).

## Coverage notes
Verified count: 5 new/update items with fetched, in-window source pages (Juniper KEV addition, Microsoft ARTEX report, FIFA confirmation, Italy/Switzerland advisory, water-supply-chain study), plus one "just missed the window" footnote. Web search backend was down, so discovery ran entirely on publisher RSS/Atom feeds (The Hacker News, BleepingComputer, SecurityWeek, The Register, Help Net Security, Security Affairs, Dark Reading, Cyber Press, CyberSecurityNews, SecurityOnline, The Record), Google News and Bing News RSS, and the HN Algolia river; Reddit JSON endpoints returned CAPTCHA walls and CISA's all-advisories XML returned 404, so CISA's KEV entry was confirmed via NVD. Ledger dedupe: roughly 15 in-window candidates were suppressed as already reported at the 07:20 and 13:35 fires — GeoServer (CVE-2026-120705/129243), FinCEN, CIA/ODNI OSINT breach, 1Password, Drupal Fail2Install, Palo Alto Fragnesia, InfraTrust November Pulse, NTLM signing break, Todoist, the Palacio ransomware-recovery indictment, Guadeloupe hospital, Hex.pm, the Microsoft 365 Office bug and the original Juniper Session Smart bulletin. No padding: a thin new-items window is reported as such.
