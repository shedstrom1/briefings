# Cybersecurity News Briefing — GeoServer Zero-Days Exploited, 1Password Intrusion

**Window:** Wednesday, November 25, 2026, 07:20–13:35 EST (previous fire → now). New-items-only: stories already carried in earlier editions today are suppressed via the reported-ledger.

**TL;DR**
- Attackers are actively probing internet-facing GeoServer instances with two newly patched flaws (CVE-2026-120705, CVE-2026-129243) that chain into a fully unauthenticated RCE path; error-based probes confirm exploitation, though no successful RCE has been observed yet.
- 1Password confirmed a backend intrusion in which an attacker used a hijacked employee GitHub account to push malicious code and download customer names, emails and account metadata. Vaults and secrets were never accessible.
- Eclypsium's November InfraTrust Pulse found that most exploited flaws in network appliances live in bundled third-party code — 33 of 38 KEV-linked advisories — with vendor patches lagging upstream fixes.

## Vulnerabilities and patches

**GeoServer WFS file-read and authorization-bypass flaws actively exploited; chain to unauthenticated RCE**
Event date: November 25, 2026 (exploitation confirmed by watchTowr; vendor advisories were published November 24). **Severity as published:** CVE-2026-120705 CVSS 8.6 (High), CVE-2026-129243 CVSS 8.8 (High); related chain component CVE-2026-129245 CVSS 9.2.
Threat actors sent error-based exploit probes — creating and invoking stored WFS queries — to validate vulnerable GeoServer servers, then read arbitrary local files or bypass service security rules. Eye Security and Rapid7 showed the two flaws combine with CVE-2026-129245 (predictable "remember me" signing key), CVE-2026-120704 and CVE-2026-129244 into a five-step, fully unauthenticated chain to forge an admin session and execute arbitrary code on both Linux and Windows. Rapid7's Jonah Burgess reported the chain in early August; PoC code is public. No successful exploitation has been confirmed so far. Fixed in 3.0.2, 2.28.6 and 2.27.7.
**Why it matters:** GeoServer is widely deployed by government agencies and utilities; Eye Security found more than 3,000 internet-accessible instances. An unauthenticated file-read-to-RCE chain with public PoC is a high-probability target for ransomware and state actors.
**Action:** Upgrade to 3.0.2 / 2.28.6 / 2.27.7 immediately; hunt for stored-query and file-read probes; take management interfaces off the internet.
Source: "Two Newly Disclosed GeoServer Vulnerabilities Under Attack," SecurityWeek — November 25, 2026, 10:27 AM ET — https://www.securityweek.com/two-newly-disclosed-geoserver-vulnerabilities-under-attack/
Source: "Hackers Exploit Two GeoServer Flaws as Researchers Warn of Unauthenticated RCE Chains," The Hacker News — November 25, 2026 — https://thehackernews.com/2026/11/hackers-exploit-two-geoserver-flaws-as.html
Source: "GeoServer's Five-CVE RCE Chain," Aviatrix Threat Research Center — November 25, 2026 — https://www.aviatrix.ai/threat-research-center/hackers-exploit-two-geoserver-flaws-unauthenticated-rce-chains-cve-2026-120705-cve-2026-129243/
Source: CCCS advisory AV26-1207 — November 25, 2026 — https://www.defconlevel.com/alert/2026-11-25-geoserver-security-advisory-av26-1207

## Attacks and campaigns

**1Password confirms backend intrusion via hijacked employee GitHub account**
Event date: November 25, 2026 (public disclosure; intrusion detected November 19). **Severity:** Not specified by source (data-theft incident, no CVE).
1Password said an attacker gained access to an employee's corporate GitHub account, used it to read private source code and push malicious code to backend services, which then downloaded administrative data about customers. Exposed fields: names, login email addresses, account and user identifiers, account-creation and last-sign-in times, public keys, SRP parameters, and tokens embedded in emailed invitation, provisioning and recovery links. Source code and the CI/CD system were accessed, and exposed credentials were rotated. Customer vaults, vault keys, account-recovery keys, apps and browser extensions were not accessed. Invitation and recovery codes were lengthened from six digits to ten alphanumeric characters; a small number of users in a pending-invite/recovery state were notified individually.
**Why it matters:** A trusted-developer compromise at a password manager is a supply-chain-style risk; even though vaults remain end-to-end encrypted, the exposed email addresses plus pending invite/recovery tokens are strong phishing material.
**Action:** Be wary of unexpected 1Password-branded emails, texts or calls; enter your password and Secret Key only in the official app or website; treat any invitation or recovery link as sensitive.
Source: "Update on a security incident involving 1Password's backend systems," 1Password — November 25, 2026 — https://1password.com/blog/update-on-nov-2026-security-incident
Source: "1Password said hackers broke into its internal systems, but customer passwords are safe," TechCrunch — November 25, 2026 — https://techcrunch.com/2026/11/25/1password-said-hackers-broke-into-its-internal-systems-but-customer-passwords-are-safe/
Source: "1Password breach exposes customer account data," AppleInsider — November 25, 2026 — https://appleinsider.com/articles/26/11/25/what-to-watch-for-after-the-new-1password-data-breach

## Defensive tools and advisories

**Eclypsium November InfraTrust Pulse: most exploited appliance flaws sit in bundled third-party code**
Event date: November 25, 2026, 10:00 AM ET. **Severity:** Not specified by source (aggregate report; 98 advisories rated critical, 38 tied to CISA KEV).
The report tracked 373 advisories from 23 infrastructure vendors between October 15 and November 19, covering 3,880 vulnerabilities. Of 38 advisories fixing KEV-listed flaws, 33 concerned bundled software — mainly PHP, Node.js and OpenSSH — leaving customers dependent on each vendor rebuilding its product after an upstream fix. As of November 20, PHP2Shell fixes were partial or absent for Fortinet, Juniper, Lenovo, HPE, Netgear, HP and Siemens. InfraTrust recommends prioritizing Cisco Expressway CVE-2026-76466, PHP2Shell plus Check Point CVE-2026-123039, Arista VeloCloud CVE-2026-114625 (CVSS 9.9), Cisco ASA/FTD CVE-2026-76418 and SonicWall CVE-2026-19868.
**Why it matters:** An upstream patch does not protect an appliance until the vendor ships a rebuilt image — an exposure window measured in weeks.
**Action:** Remove management interfaces from the internet, segment them internally, and inspect previously exposed devices for backdoors that persist after patching.
Source: "InfraTrust warns of exploited supply chain flaws in network appliances," BleepingComputer — November 25, 2026, 10:00 AM — https://www.bleepingcomputer.com/news/security/infratrust-warns-of-exploited-supply-chain-flaws-in-network-appliances/

## Also noted

- No further items verified as first-disclosed inside this 07:20–13:35 window.

## Just missed the window

- Todoist disclosed on November 24 that a stolen version-control credential gave an attacker production access; roughly 6% of users' emails and old salted-SHA1 hashes, plus smaller sets of API/Slack tokens and connected-Outlook mailbox previews, were exposed. A November 25 update expanded the detail, but the underlying disclosure predates the window. (https://www.todoist.com/help/todoist/get-started/security-incident-in-november-2026-cG0EIACNU)
- RansomwareHelp co-founder Juan Ricardo Palacio Escobar was indicted in an FBI sting in which he paid a ~$6,000 Bitcoin ransom while overcharging victims; first reported November 20, re-covered November 25. (https://krebsonsecurity.com/2026/11/cto-of-ransomwarehelp-indicted-for-wire-fraud/)
- Suspected ransomware at Guadeloupe University Hospital — surgeries and cancer treatment disrupted, ANSSI investigating — began November 21 and was confirmed November 24; covered November 25. (https://therecord.media/guadeloupe-ransomware-hospital-cyber)

## Coverage notes

- **Verified new items: 3** (1 vulnerability/exploitation item, 1 breach, 1 advisory report). Every item's page was fetched and carries an in-window timestamp; GeoServer and 1Password each cite multiple independent sources.
- **Ledger dedupe: ~15 candidates suppressed** as already reported or already footnoted — notably FinCEN systems-offline, Drupal Fail2Install, the NTLM signing-break auth issues, the CIA OSINT-system breach, Rackspace Spot wind-down, the Microsoft 365 blank-window bug, Fragnesia/PAN-OS, the Check Point GT13 campaign, and items already footnoted once (Danfoss AK-SM 8xxA, VulnCheck credential-harvesting, the Iran/tanker-ship wave). Three older stories appear as "Just missed the window" footnotes.
- **Coverage limitations:** Reddit's anonymous Atom backend returned HTTP 404 on both search and listing endpoints for r/netsec, so the community lane was unavailable (logged, not treated as a negative finding). Several securityonline.info article URLs 404'd and multiple DuckDuckGo queries returned no results — those leads were dropped after two strikes. The CISA alerts index fetch failed, so no in-window KEV addition was verified; this is not a claim that KEV was static.
