# Cybersecurity Briefing — Nov 19, 2026 midday: vCenter 9.8 auth bypass, PHP's seven critical RCEs, $40.5M ransomware arrests

**Window:** 2026-11-18 17:20 EST → 2026-11-19 12:57 EST (catch-up sweep; the preceding 07:00 fire is absent from the reported ledger, so this edition covers the last actually-fired edition through now). New items only — every event below was first disclosed inside this window and checked against the reported ledger.

**TL;DR**
- **VMware vCenter CVE-2026-123890 (CVSS 9.8):** unauthenticated authentication bypass in the VMware Directory Service; emergency-change advisory VMSA-2026-0009, no workaround, no observed attacks yet.
- **PHP's November security drop:** 18 CVEs, seven rated Critical (CVSS 9.2 under CVSS 4.0), including the newly named "phpAfterShell" CVE-2026-126714 — fixing the file-session RCE primitive that the October PHP2Shell patch left behind.
- **Ukraine + FBI arrest four** in a Conti-descended crew credited with $40.5M+ in ransom from 28 companies, using AI-generated voice calls posing as IT support.
- **Two trust-but-verify updates:** exploitation attempts against Java4Shell (CVE-2026-114938) have begun, and Cisco Expressway CVE-2026-76466 is now exploited in the wild after a PoC landed.
- **Ruby** ships fixes for seven regex/string memory-safety flaws, five with remote-code-execution proof-of-concepts and no CVEs assigned yet.

---

## Vulnerabilities and patches

**VMware vCenter authentication bypass — CVE-2026-123890, CVSS 9.8 (Broadcom).** Disclosed Nov 19 in advisory VMSA-2026-0009 (reported to Broadcom by Mandiant). An attacker with mere network access to vCenter can bypass authentication through the VMware Directory Service and gain unauthorized access; Broadcom calls it an "emergency change" and lists no workaround. It affects vCenter 8.0/9.0/9.1 and the vCenter builds inside Cloud Foundation, vSphere Foundation and Telco Cloud — whether or not Enhanced Linked Mode is used. Broadcom reports no attacks and no public exploit. Four lower-severity flaws (6.7–8.1) in SDDC Manager and VMware Tools ship in the same update, which is cumulative and cannot be installed via Quick Patch.
**Why it matters:** vCenter is the single control plane for ESXi hosts and VMs; a pre-auth bypass there is a domain-controller-class compromise. The July vCenter auth-bypass (CVE-2026-59309) drew scanning within days and a Babuk-derived ESXi ransomware campaign within weeks.
**Action:** Inventory every vCenter, restrict management access to trusted admin networks, and apply the fixed builds (9.1.1.0200, 9.0.2.0200, 8.0 U3m; VCF 5.2.5). Watch for the "Back-in-Time" upgrade conflict with VCF 9.1.1.
Source: The Hacker News, "Critical VMware vCenter Flaw (CVSS 9.8) Lets Network Attackers Bypass Authentication," Nov 19, 2026 — https://thehackernews.com/2026/11/critical-vmware-vcenter-flaw-cvss-98.html
Source: Cyber Security News, "Critical VMware vCenter Flaws Let Hackers Bypass Authentication," Nov 19, 2026 — https://cybersecuritynews.com/vmware-vcenter-authentication-bypass/

**PHP November release — 18 CVEs, seven rated Critical (CVSS 9.2 under CVSS 4.0).** Released Nov 19 (14:00–14:30 UTC) across all supported branches — 8.5.13, 8.4.28, 8.3.37, 8.2.36, plus 8.6.0. Aikido counts 18 CVEs across the engine, sessions, PHP-FPM and CGI. The headliner is **CVE-2026-126714 ("phpAfterShell," CVSS 9.2 v4)**, found by Depthfirst: an interrupted file-session write can leave a malformed session that `session_start()` later deserializes into attacker-chosen objects — the underlying primitive the October PHP2Shell patch did not fix. Other criticals include CVE-2026-129289 (PHP-FPM request smuggling via reused backend connections, 9.2), CVE-2026-129474, CVE-2026-129481, CVE-2026-129496 (use-after-frees), and CVE-2026-129182. CVE-2026-115560 (9.2) — previously flagged here as an unpatched session-serializer RCE on Oct 23 — is now fixed. No attacks against phpAfterShell are reported.
**Why it matters:** PHP powers most of the web, and the October PHP2Shell bugs were mass-exploited within days; an incomplete patch that left a second RCE path is exactly the pattern attackers reuse. Appliances with embedded PHP management UIs are the highest-risk surface.
**Action:** Upgrade to a fixed branch and rebuild/redeploy container images. If you cannot patch, set `session.upload_progress.enabled=0` and restart PHP workers; for CVE-2026-129289, disable persistent web-server→PHP-FPM connection reuse. Note the update also flips upload-progress off by default.
Source: BleepingComputer, "PHP security updates fix 18 flaws, including critical RCE bugs," Nov 19, 2026, 11:28 AM — https://www.bleepingcomputer.com/news/security/php-security-updates-fix-18-flaws-including-critical-rce-bugs/
Source: The Hacker News, "PHP Fixes 7 Critical Flaws, Including Bugs That Could Allow Remote Code Execution," Nov 19, 2026 — https://thehackernews.com/2026/11/php-fixes-7-critical-flaws-including.html
Source: The Hacker News, "phpAfterShell Explained," Nov 19, 2026 — https://thehackernews.com/2026/11/phpaftershell-explained-how-hackers.html

**Ruby — seven regex/string memory-safety flaws, five with RCE proof-of-concepts.** The Ruby team posted 7 advisories on Nov 18/19 (advisory dated 2026-11-19 00:00 UTC), rating three Critical, three High and one Moderate, with fixes in Ruby 4.0.10, 3.4.13 and 3.3.14. Five have PoCs showing remote code execution — a one-byte heap overflow in string case conversion, a regex-compilation double free, a compiled-regex size overflow, a `Regexp#to_s` heap overflow and a case-insensitive match out-of-bounds read. CVE numbers have been requested but not assigned; track by GHSA ID. No attacks reported. Ruby 3.2 and earlier are EOL and unfixed.
**Why it matters:** These sit in Ruby itself, so every app on an affected interpreter carries them — exposure depends on whether an app compiles attacker-supplied regexes or case-converts outside text. GitLab's October regex bugs were followed by an attempted attack on Alpine's own GitLab server, showing the path is real.
**Action:** Update the interpreter on app servers, workers and container images, then restart or rebuild — bumping gems alone does not replace a vulnerable Ruby.
Source: The Hacker News, "Ruby Releases Fixes for 7 Regex and String Flaws, Warns 5 Have Proof-of-Concept RCE," Nov 19, 2026 — https://thehackernews.com/2026/11/ruby-releases-fixes-for-7-regex-and.html
Source: Ruby-lang, "Security advisories: multiple vulnerabilities in Regexp and String case conversion," posted 18 Nov 2026 — https://www.ruby-lang.org/en/news/2026/11/18/multiple-vulnerabilities-in-regexp-and-string/

**Splunk patches nearly 140 flaws across three products.** Announced Wednesday, covered Nov 19 (5:41 AM ET). Splunk Enterprise 10.6.1/10.4.5/10.2.9/10.0.12/9.4.17 fix 38 flaws including two Critical — CVE-2026-76536 and CVE-2026-76535 (clustering-config read/write with an empty shared secret; arbitrary SPL searches to read/modify data). Enterprise Security 8.7.0 fixes 42 flaws including Critical CVE-2026-131202 (RCE via lookup-file upload + Configuration Checker). SOAR 8.8.0 fixes 59 flaws including two Critical authenticated RCEs, CVE-2026-131253 and CVE-2026-131287. No in-the-wild exploitation reported.
**Why it matters:** Splunk sits on the log data defenders rely on; an auth-bypass or RCE there is both a compromise and a way to hide one.
**Action:** Prioritize the Enterprise and Enterprise Security criticals; no workaround is stated in the coverage.
Source: SecurityWeek, "Splunk Patches Nearly 140 Flaws Across Three Core Products," Nov 19, 2026, 5:41 AM ET — https://www.securityweek.com/splunk-patches-nearly-140-flaws-across-three-core-products/

*Also noted — vulnerabilities:* Researcher Zheng Yu (Depthfirst) published low-privilege-to-RCE chains against PostgreSQL (CVE-2026-116331, fixed Nov 12), MySQL (CVE-2026-101786), Oracle Text (CVE-2026-114819, CVSS 9.9, fixed Nov 17) and ClickHouse (no CVE/fix); no attacks observed. Severity as published; the ClickHouse chain has no confirmed fix. Source: The Hacker News, "Researcher Demonstrates Database RCE Exploits Affecting PostgreSQL, MySQL and Oracle," Nov 19, 2026 — https://thehackernews.com/2026/11/researcher-demonstrates-database-rce.html

---

## Attacks and campaigns

**Ukraine + FBI arrest four in a $40.5M ransomware crew using AI-voice IT-support calls.** Announced Nov 18 (BleepingComputer 6:08 PM ET), after a two-year investigation with 66 searches across Kyiv and 11 regions. Investigators link the group to Conti descendants BlackSuit/Chaos and a wider 60-member network: at least 28 companies paid over $40.5M (~UAH 1.8B), including Warren Oil (1.4 TB stolen, 84.5 BTC paid), Newpark Resources ($1.5M) and Quantum Health ($5M after a $15M demand). The crew built a platform with AI-generated voice messages for help-desk impersonation vishing, paired with fake VPN phishing pages and TeamViewer/AnyDesk. Four are in custody; $370,000 and €20,000 seized; up to 15 years under Ukrainian law.
**Why it matters:** AI-voice vishing at scale defeats the "recognize the voice" control and is a direct evolution of the IT-support impersonation campaigns now hitting enterprises.
**Action:** Re-train help desks to verify callers out-of-band and scrutinize remote-access software installs requested by phone.
Source: BleepingComputer, "Ukraine, FBI arrest ransomware gang members tied to $40M in ransoms," Nov 18, 2026, 6:08 PM — https://www.bleepingcomputer.com/news/security/ukraine-fbi-arrest-ransomware-gang-members-tied-to-40m-in-ransoms/

**N-able confirms breach of hosted email-security cloud; management access disabled in three regions.** Disclosed Nov 19 (N-able's "Wednesday" statement), following a service disruption the company first flagged to customers Nov 16. Unknown actors gained unauthorized access to one server in the Mail Assure / SpamExperts Hosted Cloud; N-able disabled management interfaces, dashboards, APIs and quarantine tools across the Americas, APAC and Europe while inbound/outbound filtering kept working. At 08:00 UTC Nov 19 restoration was still underway with no ETA; no evidence of customer-data or mailbox compromise, and N-able says it is unrelated to the separate N-central incident.
**Why it matters:** MSPs using these tools cannot review quarantined mail, check logs or change domains during the outage — and "mail still flows" does not tell them whether a specific message was held.
**Action:** Track N-able's incident page; do not assume management access is restored just because delivery looks normal.
Source: Cyber Security News, "N-able Confirms Server Breach, Disables Mail Assure and SpamExperts Management Access," Nov 19, 2026 — https://cybersecuritynews.com/n-able-confirms-server-breach/
Source: The Hacker News, "Hackers Breach N-able Cloud Server, Disrupting Email Security Management Across 3 Regions," Nov 19, 2026 — https://thehackernews.com/

**Update on previously reported — Cisco Expressway CVE-2026-76466 (CVSS 9.8) now exploited in the wild.** (Prior entry: ledger "cisco-34-oct21-advisories," 10/21.) F6 reports attackers breached clustered Cisco Expressway X15.5.3 devices at a Russian organization shortly after a public PoC appeared, then pivoted to internal cluster nodes and staged AI-assisted Python tooling for SMB/SQL Server/WinRM/GitLab recon and credential theft. The flaw is unauthenticated root command execution via persistent cookies in the database service; X15.5.20 fixes it, no workaround. Script comments indicated use of an AI assistant.
**Why it matters:** This is the Oct-21 Expressway RCE moving from advisory to active intrusion — the same "PoC then breach" pattern seen across this year's edge-device campaign wave.
**Action:** Upgrade to X15.5.20; if patching is delayed, block TCP 4371–4372 except from trusted cluster-node IPs. Detect via the Erlang system log on Expressway.
Source: SecurityWeek, "Hackers Exploit Cisco Expressway Vulnerability Soon After PoC Release," Nov 19, 2026, 8:12 AM ET — https://www.securityweek.com/hackers-exploit-cisco-expressway-vulnerability-soon-after-poc-release/

**Update on previously reported — Java4Shell CVE-2026-114938 exploitation attempts begin.** (Prior entries: ledger "java4shell+oracle-cspu-1117," 11/17; "full-disclosure," 11/17; "cisa-alert," 11/18.) As of early Nov 19 GreyNoise logged 150+ attack-shaped requests from five IPs, WatchTowr saw "java4shell-recon" User-Agent spraying and blind exploitation attempts, and Previdian logged nine attempts from six IPs on Nov 18–19. Cloudflare saw one scanner return ~17 hours later with the exploit trigger disguised as Chrome, and observed an IP used for both Artex and Java4Shell activity. VulnCheck released a full RCE exploit; no successful intrusion confirmed. Cisco, Siemens, Elastic, Red Hat, Atlassian and Jenkins published impact guidance; exploitation needs an x86-64 CPU with AVX-512 VBMI.
**Why it matters:** The flaw is out-box exploitable on affected CPUs (Red Hat: "no special configuration"), so unpatched Java services in Jira/Confluence/Jenkins/Keycloak environments are live targets.
**Action:** Apply November OpenJDK builds and restart services; where patching lags, disable AVX-512 support (Atlassian's Data Center workaround).
Source: SecurityWeek, "Java4Shell Exploitation Attempts Begin as Vendors Rush Out Advisories," Nov 19, 2026, 4:10 AM ET — https://www.securityweek.com/java4shell-exploitation-attempts-begin-as-vendors-rush-out-advisories/

*Also noted — attacks:* NVIDIA says it found no indication its systems were compromised after the Gentlemen ransomware group listed it — a response to the "leak-adds-overnight-1118" entry. Source: BleepingComputer, Nov 19, 2026, 4:23 AM — https://www.bleepingcomputer.com/

---

## Defensive tools and advisories

**Microsoft tests "Edge Lockdown Mode" in Canary.** Disclosed Nov 19 (6:46 AM ET). Inspired by Apple's Lockdown Mode and building on Edge Enhanced / Super Duper Secure Mode (which disables V8 JIT), the experimental mode also blocks WebGL, WebGL2 and WebGPU APIs to shrink the attack surface of the privileged GPU process. It is aimed at journalists, executives, researchers and admins; Microsoft estimates it would have stopped about 60% of in-the-wild Edge privilege-escalation attacks. Testable now via the `#edge-sdsm-plus` flag in Canary.
**Why it matters:** It targets the GPU process — a favorite sandbox-escape route — with a concrete, opt-in hardening control for high-risk users.
**Action:** Only for Canary testers; expect site-compatibility trade-offs until site exceptions ship.
Source: BleepingComputer, "Microsoft tests new Edge Lockdown Mode that disables GPU APIs," Nov 19, 2026, 6:46 AM — https://www.bleepingcomputer.com/news/microsoft/microsoft-tests-new-edge-lockdown-mode-that-disables-gpu-apis/

*Also noted — advisories:* Microsoft confirmed that November 2026 Windows 11 hotpatch updates (24H2+) can break installation/launch of apps loading 32-bit `msi.dll`, throwing `ERROR_ALLOTTED_SPACE_EXCEEDED (0xC0000099)`; workaround is setting `HotPatchTableSize` to 2000 in the registry and rebooting (standard Patch Tuesday updates unaffected). Standard severity: not applicable. Source: BleepingComputer, Nov 19, 2026, 5:35 AM — https://www.bleepingcomputer.com/news/microsoft/microsoft-confirms-app-installation-launch-issues-after-hotpatch/

---

## Just missed the window

- **Drupal core — CVE-2026-132165 (Critical, 18/25) installer RCE + CVE-2026-132166 gadget chain.** Drupal shipped 10.6.22 / 11.3.22 / 11.4.13 in its Nov 18 core security release (17:00–21:00 UTC, i.e. before this window opened). The installer left in `/core/install.php` can be used by anonymous users to reinstall a site → takeover/RCE; found by Anthropic's OSS Scanner, no attacks reported. It was not reported by the preceding edition; this is its one permitted footnote. (Covered Nov 19 by BleepingComputer and The Hacker News.)

## Coverage notes

- **Verified items:** 13 underlying events qualified — 8 new (vCenter, PHP release, Ruby, Splunk, Ukraine arrests, N-able breach, Edge Lockdown, and the database-RCE research also-noted) plus 2 reportable updates (Cisco Expressway, Java4Shell), plus 2 also-noted advisories (NVIDIA response, Microsoft hotpatch known issue) and 1 just-missed footnote. Counting unit = distinct underlying event, not headline; the PHP release bundles 18 CVEs and the Splunk release ~140 into single items.
- **Ledger dedupe:** 17 candidates were suppressed as already reported, including the CISA KEV-4 batch (Axis/REDCap/LiteLLM/Synology, already in the 11/18 17:20 edition), the COLD RELIC chained-breach report (11/18 07:20), the NGINX2Root and F5 BIG-IP re-covers (11/18 17:20), Apache Roller exploitation, the Microsoft Agent Framework PyPI compromise, Coinbase's $4.7M deepfake block, and the Wellington IT / Scattered Spider / BubbleLoader items. Repeat "just missed" candidates were not re-footnoted.
- **DEAD ENDS / limitations:** The search backend failed intermittently (DuckDuckGo "No results found" across multiple queries); discovery leaned on index/river pages and exact-headline searches instead. Reddit r/netsec JSON was blocked by a captcha, so the community lane was walked via the Hacker News river only. Exact URLs could not be resolved for the Help Net Security "SonicWall Gen 6" and "BackBox Linux 10" items (fetch 404/failed) — logged, not chased. CISA's KEV catalog does not enumerate a dated in-window addition beyond the already-reported four-CVE batch; no new KEV addition was verified this run.
- **Window note:** This is a catch-up edition — the ledger shows no 07:00 fire on Nov 19, so the window runs from the last actually-fired edition (11/18 17:20) to 12:57 EST.
