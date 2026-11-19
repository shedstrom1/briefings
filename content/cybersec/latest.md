# Cybersecurity News Briefing — 2026-11-19 17:00 ET

**Window:** 2026-11-19 12:00 → 17:21 EST (5-hour 17:00 fire; new-items-only, deduped against the reported ledger through the 12:57 catch-up fire).

**TL;DR**
- **Newly patched VMware vCenter auth-bypass CVE-2026-123890 (CVSS 9.8) was a zero-day since early September** — GTIG/Mandiant tie it to China-linked ARMORY CASTLE and a BeyondTrust entry chain (material update to the 12:57 brief's "no ITW" item).
- **JPMorgan Chase, Visa and Mastercard confirmed third-party-vendor breaches** after N0n leaked ~16,000 rich clients' contact data — an update to the Nov 16 "unverified N0n blue-chip" ledger line; no payment data or own systems involved.
- **ASUS Control Center Express CVE-2026-114202 is a CVSS 10.0 hard-coded-key flaw** giving unauthenticated remote admin (fix v1.7.29); TfL disclosed a 265,000-customer Oyster photocard breach.
- **The EU Commission confirmed a >10-day disruption of HR/pay/leave/medical systems** involving the ARTEX AI pen-test framework (CERT-EU/POLITICO); no confirmed data theft.
- **CISA's KEV catalog added nothing new** (still 1,786, top entries unchanged); BackBox Linux 10 shipped as a defensive-tool release.

---

## Vulnerabilities and patches

**VMware vCenter CVE-2026-123890 exploited as a zero-day since September — update on previously reported**
*Event date: 2026-11-19 (GTIG/Mandiant + Broadcom disclosures)*
Broadcom's VMSA-2026-0009 (Nov 19) fixes CVE-2026-123890, an unauthenticated SRP-6a session-key disclosure over DCE/RPC in the vCenter Directory Service that yields a remote auth bypass as `administrator@vsphere.local`, letting an attacker run commands and pull `data.mdb` via the `vmdirdbcp` RPC interface. Rated **CVSS 9.8 (Critical)** by Broadcom; no workaround. Google Threat Intelligence Group and Mandiant report that suspected PRC-nexus ARMORY CASTLE (aka UNC5221/Warp Panda) exploited it as a zero-day in early September using a Go tool, BONESAW, after entering via the BeyondTrust Remote Support zero-day CVE-2026-102764 and moving to an internal Linux appliance. Five new malware families (SLIMSAVAGE, CAGEMATCH, BEARBONES, BONESAW, SAWSNAKE) plus LAZYBONES were deployed. This materially updates the 12:57-fire item that recorded the CVE with "no ITW." Fixes: vCenter 9.1.1.0200 / 9.0.2.0200 / 8.0 U3m and VCF 5.2.5; vCenter 7.0 is unsupported and must be upgraded.
**Why it matters:** vCenter controls every ESXi host and VM underneath it; this is a patch-and-hunt situation, since exploitation predates the fix by over two months and Broadcom's advisory itself does not say "exploited."
**Action (per Mandiant/Broadcom):** patch immediately, restrict vCenter management ports to an admin allowlist with a default-deny host firewall, block outbound internet from vCenter, and hunt for rogue SSO admin accounts, SSH changes and unusual internal logins.
Source: Google Cloud / GTIG & Mandiant, "ARMORY CASTLE Exploits vSphere," 2026-11-19 — https://cloud.google.com/blog/topics/threat-intelligence/armory-castle-exploits-vsphere
Source: SecurityWeek, "Newly Patched VMware vCenter Flaw Exploited as Zero-Day by Chinese APT," 2026-11-19 — https://www.securityweek.com/
Source: Red Tiger Security advisory, 2026-11-19 — https://www.redtigersecurity.com/advisory/vmware-vcenter-cve-2026-123890-armory-castle

**ASUS Control Center Express: three critical flaws incl. CVSS 10.0 hard-coded key**
*Event date: 2026-11-19*
ASUS shipped Control Center Express v1.7.29 fixing three critical flaws. CVE-2026-114202 (**CVSS 10.0**) is a hard-coded cryptographic key letting an unauthenticated remote attacker forge an authentication token and gain administrator privileges with no prerequisites. CVE-2026-114204 (another hard-coded key, requires a recent session) and CVE-2026-114205 (authorization flaw, low-privileged user can create an admin account) are also critical. Affected versions are earlier than v1.7.29 (CVE records list through v1.7.27, leaving v1.7.28 ambiguous). All three affect a dashboard used to remotely manage fleets of 200+ ASUS business/workstation motherboards. ASUS separately patched CVE-2026-126574 in Control Center Enterprise/Datacenter (Viewer→admin password change) and CVE-2026-112115 in Armoury Crate. No exploitation reported.
**Why it matters:** a max-severity, no-prerequisite remote-admin path into a tool that manages many endpoints at once.
**Action (per ASUS):** install v1.7.29, then update managed-device agents via Select Function → Client Agent Updater and verify versions.
Source: BleepingComputer, "ASUS warns of critical hard-coded key flaw in Control Center Express," 2026-11-19 16:15 ET — https://www.bleepingcomputer.com/news/security/asus-warns-of-critical-hard-coded-key-flaw-in-control-center-express/

**CISA KEV — no new additions in window (negative check)**
*Event date: 2026-11-19*
The KEV catalog still showed 1,786 entries with the newest additions unchanged from earlier fires (Apache Axis CVE-2026-90436, REDCap CVE-2026-90817, LiteLLM CVE-2026-40217, Synology DSM CVE-2024-10441, Puppet CVE-2026-115731, Check Point CVE-2026-123039, WordPress CVE-2026-119977). No new alerts were posted in the window.
**Why it matters:** confirms Thursday's round of PHP/ASUS/Splunk/vCenter flaws had not (yet) been elevated to "known exploited" by CISA.
Source: CISA Known Exploited Vulnerabilities Catalog, checked 2026-11-19 — https://www.cisa.gov/known-exploited-vulnerabilities-catalog

---

## Attacks and campaigns

**JPMorgan Chase, Visa and Mastercard confirm third-party-vendor breach; N0n claims it — update on previously reported**
*Event date: 2026-11-19 (confirmations 13:20–16:10 ET)*
JPMorgan Chase confirmed that email addresses and phone numbers of roughly 16,000 Wealth Management/Reserve clients (invitation-only Visa card for >$10M AUM) were published by the N0n ransomware crew after a third-party vendor was compromised. Visa said an Asia-Pacific third-party service provider was hit and its own systems/payment card numbers were unaffected; Mastercard said a "limited amount of information from a subset of cardholders enrolled in a specific benefit program" was exposed, contained, with no payment-network impact. SOCRadar attributes all three to N0n. Shares of V/MA/JPM fell in midday trading. The bank said it was unrelated to Monday's software-bug banking outage. This updates the 2026-11-16 ledger line that logged N0n's blue-chip claims (Walmart, Visa, Mastercard, JPMorgan, P&G et al.) as an "unverified pattern" — now confirmed by the named firms.
**Why it matters:** a textbook third-party/OAuth-style supply-chain exposure hitting rich clients; a named, confirmed victim set converts an earlier leak-site claim into a real incident.
**Action (per JPMorgan):** affected customers will be contacted directly and offered complimentary credit monitoring; watch for impersonation using the exposed contact data.
Source: New York Post, "Visa, Mastercard and Chase bank customer data hacked," 2026-11-19 (upd. 11:48 a.m. ET) — https://nypost.com/2026/11/19/business/jpmorgan-chase-says-vendors-hacked-emails-stolen-days-after-massive-outage/
Source: Organiser, 2026-11-19 11:37 — https://organiser.org/2026/11/19/427463/international/cyber-attack-hits-visa-and-mastercard-as-ransomware-group-claims-massive-customer-data-breach-in-us/

**European Commission hit by >10-day disruption involving ARTEX AI pen-test framework**
*Event date: 2026-11-19 (POLITICO report)*
The European Commission confirmed a cyberattack that disrupted staff services for more than 10 days — Sysper (payslips, timesheets, leave), MyPMO (medical reimbursements) and translation services. CERT-EU detected the activity earlier in November and identified use of ARTEX, an LLM-driven, publicly available AI penetration-testing framework (CERT-EU advisory Nov 5); an EU official said it was unclear whether ARTEX helped the intruders break in or was only used for reconnaissance. The Commission took individual systems offline, held an emergency Corporate Management Board meeting, and technician commissioner Henna Virkkunen briefed commissioners on Nov 18. No attackers named, no specific flaw or access vector disclosed, and no confirmed exfiltration or record count as of Nov 19. The case follows an FBI Nov 13 advisory that ARTEX-assisted campaigns reached privileged access in under three hours.
**Why it matters:** a confirmed, prolonged intrusion at a top-tier institution that used an off-the-shelf agentic attack tool — consistent with the ARTEX wave tracked across earlier editions.
**Action:** organizations should treat ARTEX-style agentic tooling as a real threat and verify identity-provider/app audit logs for unauthorized authentication, since service restoration does not prove data was not accessed.
Source: Cyber Security News (citing POLITICO), 2026-11-19 — https://cybersecuritynews.com/european-commission-cyberattack/
Source: Cyberpress (citing POLITICO), 2026-11-19 — https://cyberpress.org/european-commission-cyberattack-artex-ai/

**Transport for London breach exposes 265,000 Oyster photocard customers**
*Event date: 2026-11-19 (TfL announcement; detected Nov 16)*
TfL disclosed that names, email/home addresses, phone numbers, dates of birth and security questions/answers for ~265,000 Oyster photocard holders may have been accessed after "suspicious activity" — customer emails urging them to create accounts — was detected on the photocard website on Nov 16. The portal was taken offline, the ICO notified, and affected customers contacted. TfL says passwords, payment/bank details, journey data and children's Zip Oyster data were not compromised, the incident is contained, and no misuse has been seen; the count may rise. It is TfL's second photocard-system cyber incident in just over two years.
**Why it matters:** leaked security questions/answers are reusable for account-recovery attacks, and the exposure of children's-card-adjacent records makes this sensitive even absent payment data.
**Action (per TfL):** change password and security Q&A on the photocard site once restored, and rotate identical credentials/Q&A reused on other services; ignore the account-creation emails.
Source: BleepingComputer, 2026-11-19 13:20 ET — https://www.bleepingcomputer.com/news/security/transport-for-london-breach-exposes-265-000-oyster-card-users-data/
Source: BBC News, updated 2026-11-19 12:38 GMT — https://www.bbc.co.uk/news/articles/c6n4kz3lgdz5o

---

## Defensive tools and advisories

**BackBox Linux 10 ("Resolute Raccoon") released**
*Event date: 2026-11-19*
The open-source penetration-testing distribution shipped version 10 on Ubuntu 26.04 LTS with Linux 7.0 and Xfce 4.20. Notable hardening changes: in anonymous mode, DNS now goes through Tor and IPv6 is blocked (closing two leaks), plus a new anonymous-stop command restores original network settings. Tool updates include Metasploit 6.5, Nmap 7.98, Wireshark 4.6, sqlmap 1.10, hashcat 7.1 and ZAP 2.17, with new ffuf, gobuster, AFL++, smbclient/Kerberos/LDAP clients and Firejail. The team says BackBox AI (agentic ops) is in development.
**Why it matters:** a free, actively maintained red-team platform with improved opsec defaults for testers.
Source: Help Net Security, 2026-11-19 — https://www.helpnetsecurity.com/2026/11/19/backbox-linux-10-penetration-testing/

**Ransomware access brokers move deals off public forums (ZeroFox research)**
*Event date: 2026-11-19*
ZeroFox reports ~718 claimed extortion victims/month through October 2026 (up from ~573 in 2025) and Q3 2026's 2,381 incidents as its highest quarter, while public dark-web access listings fell from ~620/quarter to ~370 as brokers shifted to private channels (Tox). Qilin led with 1,104 of 7,239 incidents logged; basic access now sells under $500 while domain-admin credentials average ~$8,000. The research warns dark-web monitoring will see less and urges attention to standing vendor tokens, anomalous sessions and help-desk reset verification. Severity: not applicable (research).
**Why it matters:** the early-warning signal from public leak forums is degrading just as claim volume rises.
Source: Help Net Security, 2026-11-19 — https://www.helpnetsecurity.com/2026/11/19/ransomware-access-brokers-cybercrime/

---

## Also noted

- **SBC Medical Group 1.6M** — an SEC filing disclosed two incidents exposing personal data of ~1.6M customers and employees (BleepingComputer, 2026-11-19 17:25 ET, just outside the 17:21 window close — first in-window-adjacent mention; https://www.bleepingcomputer.com/news/).
- **NGINX2Root F5 patch coverage** (F5 patches five critical NGINX HTTP/2 flaws, PoCs public) and **Java4Shell exploitation attempts** (GreyNoise/WatchTowr/Cloudflare probing) are Thursday follow-ups to items already in the ledger and are not re-listed as new.
- **EggStreme passive-rebuild** (suspected Chinese APT, Philippine govt), **Wellington IT / ~40 Irish credit unions** saved-payee exposure, and **Coinbase's $4.7M deepfake-scam blocking** were reported earlier and are not repeated here.

## Just missed the window

- A cybernews investigation of a POS-terminal campaign exploiting 2024-era Windows CLFS CVE-2024-49138 to steal 1M+ cards across 20+ countries was dated 2026-11-18 (out of window).

## Coverage notes

- **Verified items this run: 8** (2 vulnerability/patch leaders + KEV negative check + 3 attacks/campaigns + 2 defensive/research items), each with in-window publication timestamps and full-text reads of the leading stories; independent second sources obtained for vCenter (GTIG + SecurityWeek + Red Tiger), JPMorgan/Visa/Mastercard (NY Post + Organiser), TfL (BC + BBC) and EU Commission (CSN + Cyberpress).
- **Ledger dedupe: 14 candidates suppressed** — items already reported (vCenter auth-bypass base, PHP/Ruby/Splunk Nov releases, NGINX2Root, Java4Shell base, Drupal, agent-framework PyPI, COLD RELIC, ShadowCloud, Ukraine/$40.5M arrests, NVIDIA denial, Cisco Expressway, Edge Lockdown, hotpatch MSI) or repeat "just-missed" footnotes were not re-listed.
- **Coverage limitations:** the search backend intermittently returned "No results found" on long/exact-phrase queries (retried with shorter framings, which worked); Reddit's public RSS/search endpoints returned 404/429 on this host, so the community lane was covered via the HN Algolia river instead (which surfaced the vCenter, JPMorgan and NIST/FCC discussions). BleepingComputer river and outlet homepages (THN, SecurityWeek, Help Net, Cybernews, securityonline) were swept directly.
- Severity reported as the literal published rating (e.g., CVSS 9.8/10.0) or "Not specified by source" where none was given.
