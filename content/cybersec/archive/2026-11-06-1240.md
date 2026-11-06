# Cybersecurity News Briefing — November 6, 2026 (12:00 ET edition)

**Window:** Friday, November 6, 2026, 07:00 → 12:21 EST (previous fire at 07:20 ET; new items only, ledger-deduped).

## TL;DR

- **OpenSSH "SSHole" is under active attack** — exploitation attempts began less than nine hours after CVE publication, with confirmed root on an Alpine Linux honeypot; the flaw now carries CVE-2026-123158 (CVSS 8.1).
- **D-Link disclosed multiple unpatched zero-days in DGS-3130 stackable switches** — no CVE IDs, no firmware, no timeline; admins are told to disable DDP and lock down management planes.
- **QNAP says its NAS devices are being breached in Plex2Shell attacks** — first vendor confirmation of compromises on QNAP hardware; exposed boxes need a clean reinstall, not just a Plex update.
- **WordPress sites can be owned while installing the fix for the actively exploited upgrade-window flaw (CVE-2026-119977)** — Picus researcher: block web access to wp-content/upgrade before updating.
- **Veeam Software Appliance VMs kernel-panic on AMD EPYC hosts** — a FIPS-mode jitterentropy boot check intermittently bricks backup appliances after reboot; redeploy or hand-patch advised.

---

## Vulnerabilities and patches

### OpenSSH "SSHole" exploited in the wild within hours of disclosure; root confirmed on honeypot (Update on previously reported item)
**Event date:** November 6, 2026 (exploit activity observed November 5–6).
Threat actors are attacking CVE-2026-123158 (CVSS 8.1, "SSHole"), the OpenSSH flaw in musl-based Linux with PAM disabled where any password of 257–1,024 bytes logs into accounts whose password hash is an asterisk (key-only/password-disabled accounts). Previdian founder Ryan Dewhurst said the first exploitation attempt came less than nine hours after the CVE was published, and the firm confirmed successful **root** access on an Alpine Linux honeypot — eight attempts total from 89.116.89[.]18 (geolocated to Japan), running `id`, `uname` and `hostname` over non-interactive SSH sessions. OpenSSH 10.6p2 fixes the bug; Alpine additionally urges admins to grep `/var/log/messages` for "Accepted password for" lines on accounts that should never accept password logins and treat machines as compromised if a login cannot be ruled out. This upgrades yesterday's "no known exploitation" entry to actively hunted.
**Why it matters:** Alpine is the default SSH server config on a huge install base of containers, edge boxes and minimal VMs, and the attack needs no credentials at all — the window between disclosure and mass scanning collapsed to under a day, continuing October's pattern of same-day exploitation.
**Action:** Install OpenSSH 10.6p2 (or distro-patched package), restart sshd, and audit auth logs for unexpected "Accepted password" events on key-only accounts.
Source: "Hackers Target OpenSSH SSHole Flaw Within Hours of Disclosure, Gain Root on Honeypot" — The Hacker News, Nov 6, 2026 — https://thehackernews.com/2026/11/hackers-exploited-openssh-sshole-flaw.html
Source: "OpenSSH flaw exploited in the wild just hours after disclosure (CVE-2026-123158)" — Help Net Security, Nov 6, 2026 — https://www.helpnetsecurity.com/2026/11/06/openssh-sshole-cve-2026-123158-exploitation/

### D-Link warns of multiple zero-days in DGS-3130 series switches — no CVEs, no patch, no timeline
**Event date:** November 6, 2026 (vendor advisory; BleepingComputer coverage 07:37 ET).
D-Link disclosed several unpatched zero-day flaws in the DLink Discovery Protocol (DDP) and parts of the firmware security architecture of its DGS-3130 managed Gigabit stackable switches (SMB/metro-Earth deployments, multi-gig uplinks). Researcher Giresh Kumar Enugu (Spectrum Community Solutions) found bugs in DDP authentication, access control and information handling; successful exploitation can leak sensitive device data and SNMP communities, allow unauthorized administrative operations and configuration changes, or cause device resets and service disruption. CVE IDs are not yet assigned and severity was not specified by the source. Until firmware lands, D-Link tells admins to **disable DDP**, strip unnecessary/default SNMP community strings, restrict SSH/web UI/SNMP to isolated management networks and designated admin hosts, keep offline config backups, and watch logs for surprise config changes, reboots or factory resets. The vendor is still investigating impact on other switching platforms. This follows September's max-severity, still-unpatched DIR-822A zero-day (CVE-2026-86296).
**Why it matters:** Switches sit on the trusted side of the perimeter; a network-adjacent unauthenticated bug with vendor-confirmed mitigations-only status is a persistent foothold risk for SMB networks, and D-Link's slow patch history (see DIR-822A) doesn't inspire fast remediation confidence.
**Action:** Disable DDP now, firewall the management plane, remove default SNMP strings, and prepare for a firmware-driven rebuild later.
Source: "D-Link warns of multiple zero-days in DGS-3130 Series switches" — BleepingComputer, Nov 6, 2026 — https://www.bleepingcomputer.com/news/security/d-link-warns-of-multiple-zero-days-in-dgs-3130-series-switches/
Source: "D-Link Warns of Multiple Unpatched Zero-Day Flaws in DGS-3130 Switches, Urges Administrators to Disable DDP" — Undercode News, Nov 6, 2026 — https://undercodenews.com/d-link-warns-of-multiple-unpatched-zero-day-flaws-in-dgs-3130-switches-urges-administrators-to-disable-ddp/

### Installing the fix for actively exploited WordPress flaw can expose sites one last time (Update on previously reported item)
**Event date:** November 6, 2026.
Picus Security researcher Umut Bayram reports that WordPress 4.7 through 7.1.3 sites remain attackable **while they apply the patch** for CVE-2026-119977 (CVSS 3.1 8.1 per Wordfence CNA; added to CISA KEV Nov 3 with a Nov 6 federal deadline). During a full-package core upgrade, WordPress unpacks a copy of the new version — installer included — into a predictable, web-reachable folder under wp-content/upgrade, and that copy runs on the site's old, still-vulnerable code until deletion. An unauthenticated attacker who guesses the folder name and hits the staged installer in that race can execute code on the server. Bayram's fix: block web access to wp-content/upgrade before starting the update.
**Why it matters:** This is the fourth material twist on the same upgrade-window TOCTOU class (Oct 30 report, Nov 3 KEV) — and the first with a concrete, pre-update mitigation for the patching act itself. Fed agencies' BOD 26-04 deadline for this CVE is today.
**Action:** Deny web access to wp-content/upgrade before updating, then upgrade to 7.1.4 or your branch's patched release; review web-server logs around update time for suspicious requests to staging paths.
Source: "Installing the Fix for an Exploited WordPress Flaw Can Expose Some Sites One Last Time" — The Hacker News, Nov 6, 2026 — https://thehackernews.com/2026/11/installing-fix-for-exploited-wordpress.html

## Attacks and campaigns

### QNAP says hackers are breaching NAS devices in Plex2Shell attacks (Update on previously reported item)
**Event date:** November 6, 2026 (QNAP security notice, Taipei).
QNAP published a security notice saying it has received user reports of Plex Media Server on QNAP NAS appearing compromised via the Plex2Shell chain (CVE-2026-96652/-96654/-96655/-96656, all in Plex Media Server ≤1.43.2; unauthenticated remote code execution when chained, public PoC Oct 16, mass attacks since Oct 19). QNAP PSIRT confirmed QTS and QuTS hero themselves are not affected — the exposure is the third-party Plex app. The notice is blunt: if your Plex server was internet-reachable (Remote Access on, port 32400 forwarded via UPnP/router/myQNAPcloud), updating alone will not remove attacker-installed software — perform a clean reinstall without restoring the old Codecs folder, check for unfamiliar files there, outbound connections to proxystream[.]st and sustained high CPU while idle, sign out all Plex devices before changing the account password (the owner admin token may be leaked), rotate NAS and stored backup/cloud credentials, and prefer VPN over Plex Remote Access.
**Why it matters:** This is the first named-hardware-vendor confirmation of in-use Plex2Shell compromises beyond Plex's own disclosure — NAS boxes hold backups and credentials, so a media server becoming an entry point is an extinction-level event for small-business recovery posture.
**Action:** Update Plex to 1.43.3.10861+ from App Center now; treat previously internet-exposed servers as compromised and rebuild per QNAP's FAQ.
Source: "QNAP Security Notice: Plex Media Server Vulnerabilities (Plex2Shell) – Update to 1.43.3.10861 or Later Immediately" — QNAP, Nov 6, 2026 — https://www.qnap.com/en/security-news/2026/qnap-security-notice-plex-media-server-vulnerabilities-plex2shell-update-to-1-43-3-10861-or-later-immediately
Source: "QNAP warns of ongoing Plex2Shell attacks on NAS devices" — CyberInsider, Nov 6, 2026 — https://cyberinsider.com/qnap-plex2shell-attacks-nas-devices/

### Friday-morning ransomware leak-site adds: body armor, furniture, retail-AI
**Event date:** November 6, 2026 (detections ~07:20–11:50 ET).
Ransomware.live logged a fresh morning batch: a Phoenix, Arizona company selling body armor to police, firefighters and military (attack est. Sep 29); a firm under Delavan Life parentage showing negative equity (est. Oct 6); debt-collection software vendor Metalloid (est. Oct 16); French furniture maker Ligne Roset; Houston Machine and Thread; retail-analytics/AI vendor Algonomy; Hudson & Hudson SignProducts (Houston); Boyle, Pecharich, Cline, Whittington and Stallings law firm (Arizona, since 1950); an Italian state comprehensive school in Cuneo; one redacted FULL-DATA-TIMER entry; and a new group debut (status: resolved, data destroyed). The overnight N0N/Qilin batch was already covered in this morning's edition.
**Why it matters:** The morning cadence — law firms, a hospital-adjacent claims-data target and now physical-security suppliers — keeps the small-mid-market extortion drumbeat steady between the big headline campaigns.
Source: "Recent Victims" — Ransomware.live, Nov 6, 2026 — https://www.ransomware.live/

## Defensive tools and advisories

### Veeam Software Appliance VMs fail to boot (kernel panic) on AMD EPYC hosts; redeploy or hand-patch
**Event date:** November 6, 2026 (BleepingComputer coverage; Veeam KB4960 published earlier this week).
Veeam warned that its Linux-based Software Appliance (Veeam Backup & Replication) version 13.1+ intermittently fails to boot after a reboot — including post-update kernel reboots — on certain AMD EPYC processors (7262, 7302, 7F72), crashing with "Kernel panic - not syncing: jitterentropy: Initialization failed with host not compliant with requirements: 9". The cause is the appliance's FIPS-mode early-boot jitterentropy health test failing on those CPUs. Mitigation: redeploy the appliance to a host with a non-affected processor; otherwise disable automatic updates and install manually while watching the boot — and if the panic hits, a full power-off/power-on is required (a guest reboot does not recover). Once booted, operation is unaffected. Separately, Veeam confirmed today it is pushing the patched kernel for the critical Linux MPTCP RCE (CVE-2026-98432) to affected components via private fixes and the Veeam Updater.
**Why it matters:** This is an availability footgun on the backup tier itself — 550,000+ Veeam customers, and a silently unavailable appliance is exactly the scenario ransomware crews count on. Pair with the still-unpatched-on-Ubuntu MPTCP flaw below and "backups ready" needs an actual boot test on EPYC hosts.
**Action:** Identify Veeam Software Appliance 13.1+ VMs on EPYC 7262/7302/7F72, disable auto-update, plan manual kernel updates with console monitoring, and rehearse the power-cycle recovery.
Source: "Veeam warns of Software Appliance boot failures on AMD EPYC hosts" — BleepingComputer, Nov 6, 2026 (10:25 ET) — https://www.bleepingcomputer.com/news/security/veeam-software-appliance-vms-fail-to-boot-on-amd-epyc-hosts/
Source: "Veeam Software Appliance Fails to Boot with Kernel Panic on AMD EPYC Hosts" (KB4960) — Veeam Knowledge Base, week of Nov 4, 2026 — https://www.veeam.com/kb4960

## Also noted

- **99% of repository access tokens never expire** — BlueFlag Security's State of Developer Risk 2026 report (published Nov 6): measured non-human identities outnumber developers 8:1, 84% of repo tokens can change code, 47% went unused >90 days without revocation, 68% of agent identities hold admin rights (Help Net Security, Nov 6 — https://www.helpnetsecurity.com/2026/11/06/blueflag-ai-agents-access-risks-report/).
- **November Patch Tuesday forecast (Nov 10):** Ivanti's Todd Schell expects the usual big Microsoft month with legacy software still driving risk (Help Net Security, Nov 6 — https://www.helpnetsecurity.com/2026/11/06/november-2026-patch-tuesday-forecast/).
- **Ubuntu still has no fix for the MPTCP kernel RCE (CVE-2026-98432) ~a week after RHEL shipped one** — per a widely upvoted Ask HN thread this morning; ubuntu.com now lists the flaw as "work in progress" with a mitigation. Watch for a long tail on Ubuntu LTS MPTCP listeners (Hacker News, Nov 6 — https://news.ycombinator.com/item?id=50393105).

## Just missed the window

- **Ahold Delhaize puts a price on its October cyberattack** — with Wednesday (Nov 4) Q3 results: €200–250M off Q4 sales, €75–100M off operating income, lowered 2026 outlook (Ahold Delhaize newsroom Nov 4; SecurityWeek covered Nov 6).
- **Residential-proxy exits found on the White House network** — Layer3 Intel's Nov 4 research: 236 exits in 30 days via 28 proxy services across six AS26205-family addresses, mostly 5 p.m.–midnight ET, suggesting an infected staff device (Layer3 Intel, Nov 4; SecurityWeek/Help Net Security Nov 6).
- **Era Security's "Greeter"** — Chinese-language framework bombarding Flowise honeypots with "hello" messages while abusing CVE-2024-58351 for command execution, cloud-metadata theft and persistence; 4,372 exposed Flowise instances counted (research published Nov 3; SecurityWeek Friday roundup Nov 6).

## Coverage notes

**Verified this run:** 9 events (4 in Vulnerabilities and patches including two updates on ledger events; 2 in Attacks and campaigns; 1 in Defensive tools plus 3 also-noted), each from a fully fetched page dated inside the 07:00–12:21 EST window; leading stories read in full. Plus 3 never-before-footnoted "Just missed the window" items (excluded from the qualifying count).
**Ledger dedupe:** ~20 in-window candidates were suppressed as already reported, including this morning's own 07:20 items (GPT-6 Cyber launch, Paint Store-only fix, Claude Code User-Agent email leak, Push/Kali365 fake-event phishing, Truffle revocation program, refrigeration fact sheet, MetaMask post-mortem) and yesterday/two-days-ago items re-surfacing on front pages (Chainalysis all-users update, DigiCert breach, Zyxel GS1900 zero-day, Cisco UCS Central/WatchGuard patches, APT29 mobile watering holes + CVE-2024-27834 KEV, EFS cold-room attack, Synology C3RB3R, Trezor/CryptoBilis implant, LLMjacking $1M, Harvard HIBP load, bank fake-'suspended'-pages report). Android's November 2026 bulletin (200+ fixes) was disclosed Monday Nov 2 — outside window, not repeated.
**Dead ends / limitations:** CISA published no KEV/alert entries this morning (last alert Oct 30; Nov 3–5 additions already reported). BleepingComputer's QNAP article URL did not resolve through search, so the QNAP item cites the primary vendor notice plus CyberInsider instead. No new CISA KEV additions; no confirmed new advisory from Fortinet (its AI-discovered CVE embargo still holds to Nov 10). Reddit lanes (r/netsec, r/cybersecurity) surfaced no lead beyond the above.
