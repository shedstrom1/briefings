# The Cybersecurity News Briefing — Saturday, October 17, 2026

**Window:** October 16, 2026 12:46 EDT → October 17, 2026 12:46 EDT (rolling 24 hours, anchored to this run's 12:46 PM ET fire time)

## TL;DR

- **ConnectWise Cloud Backup (ex-SkyKick) vendor credential abused in an active Microsoft 365 supply-chain attack** — confirmed unauthorized sign-ins across at least 173 tenants, one tenant saw a ConnectWise app grant itself Global Administrator; ConnectWise has shut the backup service off entirely.
- **CISA put two vulnerabilities in its KEV catalog on Friday:** the BeyondTrust Remote Support zero-day (CVE-2026-102764, vendor CVSSv4 9.3) exploited by China-nexus Armory Castle since August, and a Fortinet path traversal (CVE-2025-61624) used to drop the PEERSTITCH implant.
- **MALFEX, the long-running npm supply-chain campaign, resurfaced with "imgdecode"** — a still-live package hiding a shellcode loader in a compiled Windows DLL; nearly 1,000 downloads.
- **A full root exploit for OpenBSD 7.9 was published,** chaining a ~37-year-old shared-memory integer overflow past KARL, SMAP and SMEP (both flaws patched in September, no CVEs assigned).
- **Weekend research:** ANY.RUN logged 1,258 fake-Claude/ChatGPT campaigns targeting developers, and Armis Labs found 73% of organizations own building-automation systems that have appeared in ransomware incidents.

---

## Vulnerabilities and patches

**CISA orders remediation of BeyondTrust Remote Support zero-day exploited by Armory Castle since late August (CVE-2026-102764)**
*Event date: October 16, 2026 (CISA KEV listing; BeyondTrust advisory BT26-05 issued October 15).*
CISA added CVE-2026-102764 to its Known Exploited Vulnerabilities catalog on Friday, a critical improper-origin-validation flaw (CWE-290) in an internal messaging component of BeyondTrust Remote Support that lets an unauthenticated remote attacker perform file operations on the appliance. BeyondTrust's advisory rates it **Critical, CVSSv4 9.3** (vendor-published rating), affecting all Remote Support versions up to 26.2.3; SaaS tenants were already patched as of October 9 and self-hosted instances must apply the BT26-05-RS patch manually. Google's Mandiant and Threat Intelligence Group attribute in-the-wild exploitation to ARMORY CASTLE (formerly UNC5221 / Warp Panda), which BeyondTrust's own hunting first caught exploiting the flaw on August 29 — the actor staged a SAWFLY PHP webshell (mangoapi.php) responding only to a magic parameter, escalated to root by copying /bin/sh to /usr/bin/dbus-authd with the SUID bit, used the legitimate bt-secrets utility to attempt harvesting the appliance's credential vault, persisted via a zz-root-persist post-boot script and a modified sudoers template, and tunneled out through a custom SOCKS5-over-WebSocket proxy (BARECLAW) silently redirected by the appliance's own HAProxy config.

Why it matters: a Remote Support appliance brokers privileged access and caches admin credentials and API secrets — one exploited box becomes a launchpad into every environment support sessions touch, and Armory Castle demonstrably pivoted downstream from compromised appliances.

**Action:** Patch self-hosted Remote Support (≤ 26.2.3; upgrade anything older than 24.3.1 first). Hunt for mangoapi.php webshells, SUID /usr/bin/dbus-authd, /home/bomgar/sbin/post_boot.d/zz-root-persist, sudoers-template edits, and unusual HAProxy WebSocket paths; treat internet-exposed unpatched appliances as compromised until proven otherwise, rotate privileged credentials after containment, and rebuild where intrusion cannot be excluded.

Source: CISA, "CISA Adds Two Known Exploited Vulnerabilities to Catalog," October 16, 2026 — https://www.cisa.gov/news-events/alerts/2026/10/16/cisa-adds-two-known-exploited-vulnerabilities-catalog
Source: BeyondTrust advisory BT26-05, issue date October 15, 2026 — https://www.beyondtrust.com/trust-center/security-advisories/bt26-05
Source: SOC Prime, "CVE-2026-102764: Critical BeyondTrust Remote Support Zero-Day Exploited by China-Linked Hackers," October 16, 2026 — https://www.socprime.com/blog/cve-2026-102764-beyondtrust-zero-day-exploited/

**Fortinet FortiOS-family path traversal (CVE-2025-61624) also added to CISA's KEV after PEERSTITCH deployments**
*Event date: October 16, 2026 (CISA KEV listing).*
The same CISA alert added CVE-2025-61624, a path traversal in the command-line interpreter of FortiOS, FortiPAM, FortiProxy and FortiSwitchManager, to the KEV catalog — a year-old, modestly scored flaw (CVSS 5.4 as reported by The Hacker News when it covered the exploitation) that was reportedly abused by a suspected state-linked actor to deliver PEERSTITCH, a memory-resident ARM64 implant that evades checks on tagged VPN sessions. Under Binding Operational Directive 26-04, federal agencies must prioritize remediation of KEV-listed, high-risk vulnerabilities on publicly exposed assets, and CISA encourages the same risk-based approach for everyone else.

Why it matters: KEV additions of older, "lower-severity" bugs keep proving that exploitation, not score, decides urgency — a 5.4 CVSS that's in the KEV outranks a stack of 9s nobody is touching.

**Action:** Confirm the fix for CVE-2025-61624 is applied across FortiOS/FortiPAM/FortiProxy/FortiSwitchManager and re-check exposed management/CLI surfaces; treat VPN session metadata as a forensic source.

Source: CISA, "CISA Adds Two Known Exploited Vulnerabilities to Catalog," October 16, 2026 — https://www.cisa.gov/news-events/alerts/2026/10/16/cisa-adds-two-known-exploited-vulnerabilities-catalog
Source: Senserva KEV tracker ("added to CISA's actively exploited catalog as of October 16"), updated October 16–17, 2026 — https://senserva.com/exploited-this-week.html

**Researchers publish full OpenBSD 7.9 root exploit chaining a 37-year-old shared-memory bug**
*Event date: October 16, 2026 (proof-of-concept published; underlying flaws patched in September errata).*
Security firm Calif released a detailed writeup and working local privilege escalation exploit for OpenBSD 7.9 that chains an integer overflow in the shmat(4) attach path — the shm_nattch counter has been a signed 16-bit short since 1988 BSD sources — with a reference-counting use-after-free fixed separately in September. Mapping 127 shared-memory segments across 259 forked processes wraps the counter and triggers the UAF; a follow-on out-of-bounds write to multicast counters lets the exploit flip a page-table entry into supervisor access, defeating SMAP/SMEP, and deterministic kernel allocators mean KARL randomization never covers the targeted function pointers. Both flaws are fixed via syspatch (errata 018 of September 14, shipped as a reliability fix, and errata 026 of September 30); no CVEs were assigned and no in-the-wild abuse is known.

Why it matters: the chain shows even OpenBSD's much-copied mitigations have structural gaps (deterministic allocators, an uncalled 1988 type width) and that "reliability fix" errata can carry exploitable risk — syspatch hygiene on BSD boxes isn't optional.

**Action:** If you run OpenBSD (especially 7.9, and 7.8 for the overflow), verify syspatch has delivered errata 018 and 026; Calif also proposed kernel hardening (safe-unlink, page-table and allocator randomization, compiler bounds checking) worth tracking.

Source: The Hacker News, "Researchers Demonstrate OpenBSD Root Exploit Chaining 37-Year-Old Shared-Memory Bug," October 16, 2026 — https://thehackernews.com/2026/10/researchers-demonstrate-openbsd-root.html
Source: Security Arsenal, "OpenBSD 7.9 Local Privilege Escalation," October 2026 — https://securityarsenal.com/blog/openbsd-79-local-privilege-escalation-37-year-old-shared-memory-bug-chained-to-root-detection-and-remediation-guide

---

## Attacks and campaigns

**ConnectWise/SkyKick backup vendor credential in hostile hands: 173+ Microsoft 365 tenants hit, Global Admin hijack in one partner tenant — backup service shut down**
*Event date: October 16–17, 2026 (Huntress disclosure October 16 with an October 17 10:30 AM ET update; ConnectWise and Microsoft advisories same window; abuse observed October 15).*
A compromised client-assertion credential for ConnectWise Cloud Backup (formerly SkyKick), the MSP-oriented backup app holding broad consent to mailboxes, OneDrive, SharePoint, Teams and directory data in every tenant where it's installed, was used from hosting IP 144.172.64[.]128 (AS14956, RouterHosting) on October 15 between 06:13 and 08:34 UTC to mint Microsoft Graph tokens tenant-by-tenant in sorted GUID order — a scripted hit-list. Huntress confirmed unauthorized sign-ins in 173 partner/customer tenants, and in one partner tenant the ConnectWise Cloud Management – MS Teams app added itself to Global Administrator, then used a Python script (User-Agent Python-urllib/3.12) to plant new, 2028-expiry client secrets on an MSP management app and on Huntress's own security-platform application — trusted tools that reach into customer tenants. Huntress's overnight update (October 17, 10:30 AM ET) added high-confidence abuse of three more ConnectWise Cloud Management apps — Exchange Online (b78f6a77-…), MS Teams (24205314-…) and SharePoint Online (b2b55f5b-…) — which at time of writing don't appear to have been disabled by ConnectWise, and noted a possible alphabetical victim-ordering pattern. Microsoft confirmed an "active supply chain attack" including attempts to add persistent credentials on the abused service principals; ConnectWise has taken Cloud Backup application access offline pending investigation, and Todyl independently observed the same Graph abuse. The same infrastructure password-sprayed four law-firm tenants October 13–14.

Why it matters: this is the vendor-app skeleton-key problem in its purest form — a client assertion lives in the vendor's tenant, so no customer MFA, password policy or session revocation stops it; directory reads via Graph leave no audit trail, so waiting for "proof of access" isn't a strategy, and added secrets survive app disablement.

**Action (per Huntress/ConnectWise/Microsoft):** Disable (don't delete) ConnectWise Cloud Backup/SkyKick app (ID 506e08f9-6e9a-4da4-942b-a099a03644ae), Cloud Management – MS Teams (24205314-…), and now Exchange Online (b78f6a77-…) and SharePoint Online (b2b55f5b-…) app IDs; review service-principal sign-ins (Entra → Sign-in logs → Service principal tab) for 144.172.64[.]128 or any non-Azure egress; audit every application's credentials for ones added since October 13; enable Microsoft Graph activity logging; check Entra audit logs for ConnectWise/SkyKick apps taking directory roles; do not re-enable until ConnectWise confirms credential rotation.

Source: Huntress, "ConnectWise (SkyKick) Vendor Credentials Abused in M365 Attacks," Jonathan Semon, published October 16, updated October 17, 2026 10:30 AM ET — https://www.huntress.com/blog/vendor-credential-enables-unauthorized-access
Source: The Hacker News, "Hackers Use Compromised ConnectWise Credential to Sign In to 173 Microsoft 365 Tenants," October 17, 2026 — https://thehackernews.com/2026/10/hackers-use-compromised-connectwise.html

**MALFEX npm campaign returns with "imgdecode": shellcode loader inside a compiled Windows DLL, still on the registry**
*Event date: October 16–17, 2026 (SafeDep discovery disclosed; The Hacker News coverage October 17).*
The npm supply-chain campaign tracked as MALFEX — active since August 2023 and first documented by Checkmarx and CloudSEK — has a new package, "imgdecode" (v1.0.3), uploaded September 30 by user "malfexteste8" and still live with ~979 downloads. Its nine-line JavaScript entry point loads a prebuilt MSVC .node DLL that, when required from a Windows console, checks it's in a developer terminal, pulls an encrypted banner.jpg from a GitHub repo, decrypts it with the key "nif-runtime-2027" and injects shellcode into a new dllhost.exe process; the operator rotates the payload off by serving content that decrypts to an image. The loader is the third evolution after takedowns — npm removed native-runner and img-to-native on September 28, replacements imgbundle and runhelper appeared within hours, and imgdecode followed six hours after runhelper was unpublished. Its siblings chained a Go downloader into an infostealer/RAT (C2 104.234.65[.]75) that audits for VMs/sandboxes, grabs seed phrases, .kdbx/.pem/.ppk/.env files, browser, Discord, Telegram and crypto-wallet data, and keeps WebSocket-based control with a scheduled task masquerading as "MicrosoftEdgeUpdateService."

Why it matters: native Node addons sidestep the install-script scrutiny (and postinstall-blocking protections) that most npm supply-chain defenses focus on, and the same-day replacement cadence shows takedowns alone won't stop this operator.

**Action (per SafeDep via THN):** Treat any Windows dev box that installed imgdecode, runhelper, imgbundle, native-runner, img-to-native or cdn-img-fetch as potentially compromised — isolate, check Run-key persistence and the fake Edge scheduled task, rotate credentials and tokens, and move funds to fresh wallets if seed phrases were stored.

Source: The Hacker News, "MALFEX npm Package 'imgdecode' Hides Shellcode Loader in Windows Native Addon," October 17, 2026 — https://thehackernews.com/2026/10/malfex-npm-package-imgdecode-hides.html
Source: Cloud Security Alliance research note on MALFEX native-addon loader, October 17, 2026 — https://labs.cloudsecurityalliance.org/research/csa-research-note-malfex-npm-native-addon-loader-20261017-cs/

**Fake Claude and ChatGPT campaigns hit developers 1,258 times in six months, ANY.RUN logs**
*Event date: October 16, 2026 (ANY.RUN report covered by Dark Reading).*
ANY.RUN researchers tied 1,258 platform analyses over six months to fake-AI campaigns impersonating Claude (60% of analyses) and ChatGPT/Codex (33%), delivered through SEO poisoning, malvertising, fake download pages and AI-themed artifacts. Nearly half involved ClickFix-style social engineering — 27% were fake CLI "InstallFix" prompts and 17% classic fake-download ClickFix, while 25% began with abused AI chats or shared artifacts (Push Security's Dan Green flags shared chats and custom GPTs served from the genuine claude.ai/chatgpt.com domains as the nastiest current variant). The playbook depends on the victim pasting a command; targets are developer workstations (source code, SSH keys, CI/CD tokens, cloud and AI API keys) with marketing staff drawn in via fake ads aimed at ad-account and billing access.

Why it matters: installing AI CLIs legitimately involves pasting terminal commands, which makes malicious instructions indistinguishable from onboarding docs — and hosting lures on trusted platforms (Cloudflare Pages, GitHub, even AI platforms themselves) burns reputation-based URL filtering.

**Action:** Pin AI tool installation to vendor-documented channels and enforce it (block pasted curl|bash-style flows via EDR/policy where possible), warn developers about fake install prompts on copied docs, and monitor for credential use anomalies from AI API keys and CI/CD tokens.

Source: Dark Reading, "Fake Claude, ChatGPT Attacks Surge, Putting Developers at Risk," Rob Wright, October 16, 2026 — https://www.darkreading.com/application-security/fake-claude-chatgpt-attacks-developers-risk
Source: Security Arsenal, "Fake Claude and ChatGPT ClickFix Campaigns Targeting Developers," October 17, 2026 — https://securityarsenal.com/blog/fake-claude-and-chatgpt-clickfix-campaigns-targeting-developers-detection-and-defense-guide

---

## Defensive tools and advisories

**Armis Labs: 73% of organizations own building-automation systems that have featured in ransomware attacks — criminals are weaponizing physical access as extortion leverage**
*Event date: October 17, 2026 (The Register analysis published Saturday 12:30 UTC / 8:30 AM ET, drawing on ServiceNow's Armis Labs report published earlier this month).*
The Register's weekend deep-dive catalogs a shift from data-theft extortion toward physical-systems pressure: criminals spiked refrigeration temperatures across hundreds of French grocery stores in one weekend, ransomware hit a Canadian hospital's building-management systems in August (door access, HVAC monitoring, badge issuance), and a Semperis responder recounted a refuse-to-pay victim whose office locks were turned against its staff until police broke windows to get them inside. Armis Labs found 73% of organizations own building automation systems (BAS) that have been used in ransomware attacks and 73% have at least one known-exploited BAS vulnerability, while 91% of BAS still communicate over insecure protocols; ServiceNow researchers say actors hide inside BMS/OT for persistence — one financial institution's intruders rebuilt access through BMS backdoors after repeated IT ejections — and increasingly use AI to master obscure control OSes like VxWorks. Compliance-driven internet connectivity (e.g., refrigeration temperature logging) is flagged as a key avoidable exposure.

Why it matters: BAS/OT typically has fewer controls, cannot be patch-scanned like IT estates, and its compromise produces safety-relevant, board-level leverage — the French supermarket attack is the template, not the ceiling.

**Action (per report):** Inventory BAS/BMS assets, patch firmware (the supermarket units ran outdated firmware), segment building systems from IT networks, and question every compliance-driven internet connection on a physical control device.

Source: The Register, "Cybercrims go beyond stealing data to attack physical building systems," Jessica Lyons, published October 17, 2026, 12:30 UTC — https://www.theregister.com/security/2026/10/17/cybercrims-go-beyond-stealing-data-to-attack-physical-building-systems/5303677

---

## Just missed the window (footnotes)

- **BlockStrap (CVE-2026-103849, CVSS 10.0) Magento/Adobe Commerce RCE** — Sansec's analysis was published October 15 (Adobe patched October 13 in APSB26-153); BleepingComputer's in-window coverage (Friday 4:00 PM EDT) of a pre-window disclosure: https://www.bleepingcomputer.com/news/security/critical-blockstrap-flaw-lets-hackers-run-code-on-magento-servers/
- **Drupal's twin critical RCE fixes (CVE-2026-108097 Field UI / CVE-2026-108099 Entity List)** — advisories dated October 14 (SA-CORE-2026-014); BleepingComputer's Saturday exploit-warning piece covered a pre-window disclosure: https://www.bleepingcomputer.com/news/security/drupal-patches-critical-rce-flaws-warns-exploits-may-emerge-soon/
- **F5 (NGINX) and Juniper (Junos OS) mega-patch rounds** — SecurityWeek's piece ran Friday 5:54 AM ET, 52 minutes before the window opened: https://www.securityweek.com/f5-juniper-networks-patch-critical-vulnerabilities/
- **FBI "Uncle Scam" midterm-fraud PSA** — The Register covered it Friday 11:30 AM ET (76 minutes early): https://www.theregister.com/cyber-crime/2026/10/16/uncle-scam-wants-you-to-cough-up-your-cash-and-personal-data-fbi-warns-ahead-of-midterms/5303512
- **AOMEI Backupper "ephemeral supply-chain tampering" by UNC7017** — Google GTIG/Mandiant/ManageEngine report coverage crossed the wire three minutes before the window opened (also recapped in THN's ConnectWise article cited above).
- **Burger King Japan breach exposing 11.18 million customer records** — disclosed BleepingComputer Friday 8:01 AM EDT (via the BC feed): https://www.bleepingcomputer.com/feed/
- **Citizen Lab/Confiant exposé of Discover-Tech's zero-click ad-spyware pipeline (Candiru at Al Jazeera)** — disclosure Thursday October 15; follow-up coverage continued into the window (e.g., Al Jazeera's October 16 explainer): https://therecord.media/spyware-citizen-lab-advertising

## Coverage notes

**Verified:** 7 in-window items — every one backed by a page fetched in this run carrying an in-window date; the five lead stories (ConnectWise, BeyondTrust KEV, Fortinet KEV, MALFEX, OpenBSD) were read in full, with second independent sources cited for ConnectWise, BeyondTrust, Fortinet KEV, OpenBSD, MALFEX and the fake-AI campaigns. Severity figures are literal published ratings (BeyondTrust CVSSv4 9.3 "Critical" per BT26-05; Fortinet CVE-2025-61624 CVSS 5.4 per THN reporting; OpenBSD flaws have no CVE; campaign stories carry no CVSS — Not specified by source).

Discovery touched 10+ independent outlets via both indexes (BleepingComputer RSS, The Hacker News feed, The Register security atom feed, Dark Reading RSS, SecurityWeek front page) and multi-angle search (KEV, zero-day campaigns, breach disclosures, ICS advisories, supply chain, vendor research). Saturday is a light news day: most Friday-morning stories (BAE bankruptcy-court outage, Burger King Japan, Candiru, FBI PSA) fell just outside the window and are footnoted rather than pulled in.

Dead ends: GitLab's max-severity CVE-2026-97229 proved to be an October 12 disclosure (dropped); the FortiMail zero-day (CVE-2026-104286) dates to October 1 (dropped); one Register weekend headline resisted exact-headline resolution after two attempts and was logged and left out; a Dark Reading URL-only sidebar item was excluded as unfetchable-attribution. No index was down.
