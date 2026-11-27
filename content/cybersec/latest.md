# Cybersecurity News Briefing — Nov 27, 2026 (07:20 ET fire)

**Window:** Thu Nov 26, 17:20 ET → Fri Nov 27, 07:21 ET (overnight sweep, new-items-only). All times ET.

**TL;DR**
- **NFS2ROOT (CVE-2026-99362)** — a 12-year-old pre-auth heap overflow in Linux's NFS server hands remote attackers root on any host reachable on port 2049; rated 9.8, no in-the-wild use yet, distro patches still rolling out.
- **SAP confirms an intrusion** ("Project Granite"): attackers used stolen employee GitHub tokens to copy and alter source code; SAP has rotated signing keys and suspended automated development, and says no customer business data was accessed.
- **UK's Department for Work and Pensions** took services offline after a "cyber incident" — benefit and pension payments are unaffected, NCSC and GC3 are investigating.
- **Software-supply-chain hit:** trojanized Q-Dir and DesktopOK installers on the official SoftwareOK site deploy a Python remote-access trojan.
- **Two updates:** Switzerland confirms AI-agent attacks did breach critical-infrastructure operators, and Colorado puts its myColorado breach at ~100,000 people.

## Vulnerabilities and patches

**NFS2ROOT: 12-year-old Linux NFS flaw gives remote root (CVE-2026-99362)**
*Event: Nov 26–27.* A heap buffer overflow in the in-kernel NFS server (nfsd), present since Linux 3.16 (2014), lets an unauthenticated attacker corrupting kernel memory via a crafted NFSv4.1 request on TCP port 2049 gain root. Exploit details were published by OpenSec, which found the bug with its Antara AI system and disclosed it to NFSD maintainers on Sept 22; the flaw was independently found by ANSSI's Jérémy Jean, who submitted a fix in August. Fixed in kernels 6.12.117, 6.18.61, 7.3.2 and 7.4-rc1, but major distributions had not shipped builds at disclosure. No exploitation observed yet. Severity: **9.8 out of 10 (OpenSec rating); BleepingComputer calls it "critical."** No CVE-level vendor CVSS was consistently cited by the sources.
- **Why it matters:** Port 2049 is commonly exposed on file servers, NAS appliances, build farms and Kubernetes storage nodes, and the exploit bypasses KASLR and runs with no credentials or user interaction. This is a patch-every-host situation, not a wait-and-see one.
- **Action:** Apply distribution kernel updates and reboot; if patching cannot be immediate, block TCP 2049 from untrusted networks or disable the nfs-server service. Watch for nfsd4 kernel warnings, shells from kernel workers, and bursts of short NFSv4.1 sessions.
- Source: "New NFS2ROOT Linux flaw can let attackers gain root privileges," BleepingComputer, 03:07 ET Nov 27 — https://www.bleepingcomputer.com/news/security/new-nfs2root-linux-flaw-can-let-attackers-gain-root-privileges/
- Source: "Critical NFS flaw hands hackers root on NAS, Kubernetes storage nodes," Cybernews, Nov 26 — https://cybernews.com/security/nfs2root-linux-kernel-vulnerability-exposes-nas-kubernetes/

**Microsoft ships emergency hotpatch for Windows Server 2025 crashes (KB5138318)**
*Event: Nov 27.* Microsoft issued an out-of-band, non-security "hotpatch" to fix stop error 0x139 (KERNEL_SECURITY_CHECK_FAILURE) on a limited number of Windows Server 2025 systems with Router Advertisement enabled that receive time-bound IPv6 routes — triggered by the November security hotpatch update KB5131077. The update installs automatically through Windows Update and takes effect without a reboot. Severity: **Not applicable — non-security reliability fix.**
- **Why it matters:** It follows other November hotpatch fallout (Office/third-party app launch issues on Windows 11, printing failures with Forcepoint DLP), showing the hotpatch delivery model is still shaking out edge cases on real fleets.
- **Action:** Ensure affected Server 2025 hosts receive KB5138318; no restart required.
- Source: "Microsoft pushes emergency hotpatch to fix Windows Server crashes," BleepingComputer, 04:08 ET Nov 27 — https://www.bleepingcomputer.com/news/microsoft/new-out-of-band-oob-emergency-hotpatch-fixes-windows-server-2025-crashes/

## Attacks and campaigns

**SAP confirms intrusion; source code copied and altered on GitHub ("Project Granite")**
*Event: Nov 26–27.* SAP confirmed to press that it detected suspicious activity and took protective action after German outlet Der Spiegel reported, citing an internal memo, that attackers used stolen SAP employee authentication tokens to copy and modify source code on GitHub — activity the company tracks as "Project Granite." Handelsblatt reported emergency measures affecting many teams, including replaced signing keys, restricted GitHub access and suspended automated development operations. Insiders suggested AI tooling may have assisted, but that is unconfirmed; SAP says it has found no evidence of unauthorized access to customer business data and no indication tampered code reached shipped software. Severity: **Not specified by source (intrusion, not a CVE).**
- **Why it matters:** A source-code and signing-key incident at one of the world's largest enterprise-software vendors raises supply-chain questions even without proof of downstream compromise — and the AI-assist angle fits a pattern other enterprises have reported this week.
- **Action:** SAP has not published customer guidance; organizations should watch for vendor notices and verify the provenance/signatures of any SAP-sourced code or updates.
- Source: "SAP confirms security incident after suspicious GitHub activity," BleepingComputer, 04:56 ET Nov 27 — https://www.bleepingcomputer.com/news/security/sap-confirms-security-incident-after-suspicious-github-activity/
- Source: "SAP Confirms Cyberattack, Source Code Reportedly Compromised," SecurityWeek, 02:36 ET Nov 27 — https://www.securityweek.com/sap-confirms-cyberattack-source-code-reportedly-compromised/

**UK Department for Work and Pensions takes services offline after "cyber incident"**
*Event: Nov 27.* The DWP — Britain's largest government department by spending, which administers state pensions and benefits — said it took some online services offline "as a precaution" after identifying a cyber incident affecting its systems. Regular benefit and pension payments are unaffected and will be paid as normal. It is working with the NCSC and the Government Cyber Coordination Centre; the specific services down, timeline and whether data was accessed were not disclosed. Severity: **Not specified by source.**
- **Why it matters:** It lands amid an NCSC warning of a "marked rise" in AI-assisted attacks since September and renewed criticism of legacy IT in UK government; millions depend on these payments.
- **Action:** Claimants relying on affected online services were told to retry later; no other action stated.
- Source: "Cyber incident hits British government, services taken offline," The Record, Nov 27 — https://therecord.media/uk-department-work-pensions-cyber-incident
- Source: "British social security system techies yank services offline following 'cyber incident'," The Register, 06:01 ET Nov 27 — https://www.theregister.com/security/2026/11/27/british-social-security-system-techies-yank-services-offline-following-cyber-incident/5312522

**Trojanized Q-Dir and DesktopOK installers from official site deploy Python RAT**
*Event: Nov 27.* AhnLab disclosed a supply-chain attack tampering with installation files for Q-Dir (file manager) and DesktopOK distributed from the official SoftwareOK website. Malicious files were generated both on direct downloads and during the update process of already-installed legitimate copies; the tampering is hard to spot while the programs run and drops a Python-based remote-access trojan from an external server. Severity: **Not specified by source (no CVE).**
- **Why it matters:** The compromise is at the vendor's own distribution and update path, so users who did everything right — download from the official site, keep software updated — were still exposed.
- **Action:** Re-download affected tools from a verified source and scan for the Python RAT; treat update traffic from SoftwareOK as suspect.
- Source: "Hackers Trojanize Official Q-Dir and DesktopOK Downloads to Deliver Remote Access Malware," The Hacker News, 04:29 ET Nov 27 — https://thehackernews.com/2026/11/hackers-trojanize-official-q-dir-and.html

**Update on previously reported — Switzerland confirms AI-agent attacks breached critical-infrastructure operators**
*Event: Nov 27.* Following Tuesday's joint Swiss/Italian advisory on autonomous AI-driven attacks (reported in an earlier edition), the Swiss NCSC's Florian Schütz told public broadcaster SRF that four of five confirmed attacks in the country breached victims' systems; three targeted critical-infrastructure operators and two of those succeeded, with no major disruptions. The victims are unnamed and attribution is unknown; it is the first such confirmed cases in Switzerland. Severity: **Not specified by source.**
- **Why it matters:** This moves the earlier warning from "capability observed" to "breaches confirmed," including at critical operators — a real-world validation that agentic attack frameworks are landing intrusions.
- Source: "Switzerland Confirms AI-Assisted Cyberattacks Breached Critical Infrastructure Operators," The Hacker News, 01:04 ET Nov 27 — https://thehackernews.com/

**Update on previously reported — Colorado myColorado breach quantified at ~100,000**
*Event: Nov 27.* The Colorado Governor's Office of Information Technology formally disclosed a breach of the myColorado digital-ID/document app (previously flagged as just-missed when the app went offline). Attackers accessed the "myCards and Documents" section — user-uploaded PDFs and photos of government IDs, vehicle registrations and insurance cards — of approximately 100,000 individuals; no payment data was taken. Intrusion detection came through automated threat-detection in the state's AWS environment; the app remains offline and direct notifications began Nov 27. Severity: **Not specified by source (data breach).**
- **Why it matters:** It converts an unconfirmed outage into a defined exposure of identity-document images, which are high-value for fraud, and shows cloud-side detection can surface a breach months into an intrusion.
- Source: "100,000 Potentially Impacted by Colorado Data Breach," SecurityWeek, 04:30 ET Nov 27 — https://www.securityweek.com/100000-potentially-impacted-by-colorado-data-breach/

## Defensive tools and advisories

**Microsoft to remove WSUS — plus VBScript, WMIC and WINS — in Windows Server 2028**
*Event: Nov 27.* Microsoft confirmed in an updated support document that Windows Server 2028 removes the WSUS server role entirely, along with VBScript, WMIC and WINS — legacy components long abused by malware. WSUS remains supported through the Windows Server 2025 lifecycle; Microsoft is steering customers to Windows Autopatch, Intune and Azure Update Manager. Severity: **Not applicable (product/ hardening change).**
- **Why it matters:** Removing legacy, frequently abused components is a hardening win, but organizations still running WSUS for disconnected or tightly controlled environments must plan a migration path.
- Source: "Microsoft removes WSUS server role in Windows Server 2028," BleepingComputer, 06:18 ET Nov 27 — https://www.bleepingcomputer.com/news/microsoft/microsoft-removes-wsus-server-role-in-windows-server-2028/

**CISA KEV — no new additions located in this window.** The catalog's most recent batch remains the Nov 25 ten-flaw addition (five Mitel MiCollab flaws, Vates Xen Orchestra, three Danfoss flaws and Juniper CVE-2026-103406), already reported in prior editions. No in-window KEV notice surfaced.

## Also noted

- Microsoft says a bug that under-counted usage has been fixed, so GPT-6.2 Astra users will hit limits ~40% faster (BleepingComputer, 16:06 ET Nov 26 — just outside the window boundary, included for context only).
- SecurityWeek's weekly roundup recapped several items already covered in prior editions (USDA payment-system breach, RansomwareHelp indictment, Radware breach, McDonalds Japan, Bithumb, tanker attacks). No new in-window events surfaced there.

## Just missed the window

- AFP was hit by a cyberattack detected ~01:30 Paris time Thu Nov 26 (≈19:30 ET Nov 25), taking its AFP Forum content-distribution platform offline; it remained down Friday. In-window coverage: The Record (https://therecord.media/france-afp-hit-by-cyberattack) and Al Jazeera (https://www.aljazeera.com/news/2026/11/27/global-news-agency-afp-reports-cyberattack-shuts-down-content-platform).
- eSentire's disclosure of Vinci RAT delivered via the Microsoft Store "Setup Assistant" app dates to Nov 24; The Hacker News re-covered it in-window.

## Coverage notes

- **Verified items in this edition: 9** (2 vulnerabilities/patches, 5 attacks/campaigns — including 2 updates on previously reported events — and 1 defensive/advisory item, plus the KEV negative check). Each item's source page was fetched and carries an in-window timestamp; lead stories were read in full.
- **Ledger dedupe: ~28 candidates suppressed as already reported** in prior editions (notably 1Password, Radware, FIFA, FinCEN, Drupal CVE-2026-136716, the CISA 10-KEV batch, Cacti 4HTTP2ROOT, Binance withdrawal recovery, GeoServer, Mitel MiCollab/Juniper, the Microsoft ARTEX/Salt Typhoon report, Flight Centre, Remus, SYS010, GoodGuys, Keenadu, Google Workspace MCP mandate, Hex.pm, and the Italy/Switzerland base event). Several more were out-of-window re-reports (Vinci RAT, AFP, the $17B Operation Blackout anti-scam announcement from Nov 24).
- **Sources:** The Hacker News, BleepingComputer, SecurityWeek, Help Net Security, The Record, The Register, Cybernews, Al Jazeera, Reuters, CISA, and the HN/r-netsec community lanes. Search backend returned intermittent "no results" errors; direct outlet indexes, RSS feeds and community rivers were used to compensate. Reddit's JSON endpoint was CAPTCHA-blocked; CISA's advisory XML feed was unreachable — the KEV catalog page was used instead.
- No padding: a light overnight window around Thanksgiving produced a focused set, with borderline/late items footnoted rather than promoted.
