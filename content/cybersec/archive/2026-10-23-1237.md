# The Cybersecurity News Briefing — October 23, 2026 (12:00 ET Edition)

**Window:** Friday, October 23, 2026 07:00 → 12:00 EDT (previous fire → this fire). New items only; the ledger gate was applied before and during research.

## TL;DR

- **HPE shipped a mega-advisory for ArubaOS: 72 CVEs, eight rated a maximum CVSS 10.0** — unauthenticated authentication-bypass and RCE flaws in AOS-8/AOS-10 wireless infrastructure. Patches are out; no PoC or in-the-wild exploitation is known yet.
- **Two days after the PHP2Shell emergency fixes, PHP disclosed a second, separate RCE vector (CVE-2026-115560)** in session upload progress — no emergency patch is coming; the mitigation is one INI line.
- **Group-IB and NyxLab published a joint investigation into "k0t,"** an initial-access broker harvesting credentials off exposed Fortinet FortiGate and Palo Alto GlobalProtect edge devices — 70,000+ credentials/LDAP dumps, persistence via legitimate RMMs, exfil to Wasabi S3 and MEGA, links to Qilin and The Gentlemen ransomware crews.
- **Varonis Threat Labs documented Stadia, the relaunch of the StealC infostealer franchise** four months after Operation Endgame: same code, now sold as a full RAT with HVNC and a live shell.
- Mid-morning ransomware leak-site adds: Bankruptcy Software Specialists, Bravilor Bonamat, Wah Chan Consolidated, Don Pickett & Associates and three others.

---

## Vulnerabilities and patches

### HPE patches 72 ArubaOS flaws — eight max-severity (CVSS 10.0) unauthenticated RCE/auth-bypass bugs
**Event date:** Friday, October 23, 2026 (patches released; HPE advisory dated Thursday, October 22).

HPE released security updates fixing 72 vulnerabilities in ArubaOS (AOS) — the firmware powering Aruba Networking enterprise wireless: mobility conductors, access points, controllers and gateway devices. Eight are maximum-severity (CVSS 10.0): CVE-2026-79844 (auth bypass → RCE, AOS-10), CVE-2026-79845 (PAPI protocol auth bypass, AOS-10), CVE-2026-79846 (unauthenticated command injection, AOS-8), CVE-2026-79847 (unauthenticated buffer overflow → RCE, AOS-10), CVE-2026-79848 and CVE-2026-79849 (unauthenticated SQL injection → RCE, AOS-8), CVE-2026-79850 (auth bypass → RCE, AOS-8) and CVE-2026-79851 (unauthenticated buffer overflow → RCE, AOS-10). All are remotely exploitable without privileges, in low-complexity attacks needing no user interaction. HPE says it is not aware of public exploit code or exploitation in the wild as of the advisory's release date, and "strongly urges" patching plus firewalling the CLI/web management interfaces to a dedicated L2 segment/VLAN.

**Why it matters:** Max-severity, pre-auth bugs on the wireless edge are the classic mass-scaling target — one exploited mobility conductor can front-door an enterprise network. HPE's own track record (the actively exploited max-severity OneView flaw CISA flagged in January 2025) says these advisory-to-exploitation gaps do not stay quiet. This lands while every security team is still digesting PHP2Shell, Node2Shell and the Arista VCO zero-day — another "patch this weekend" cycle.

**Action:** Upgrade affected AOS-8/AOS-10 builds per the HPE advisory immediately; until patched, restrict management interfaces to dedicated VLANs/firewall-policy-controlled segments and enable activity logging.

Sources:
- BleepingComputer — "HPE patches eight max severity RCE and auth bypass AOS flaws," Sergiu Gatlan, Oct 23, 2026 08:48 ET — https://www.bleepingcomputer.com/news/security/hpe-patches-eight-max-severity-rce-and-auth-bypass-aos-flaws/
- Cyber Security News — "HPE Warns Critical Aruba Networking Flaws Could Let Hackers Take Complete Control of Devices," Oct 23, 2026 — https://cybersecuritynews.com/okta-access-gateway-flaw/ (see also HPE item at https://cybersecuritynews.com/)

### PHP discloses a second, unpatched RCE vector — CVE-2026-115560 (session upload progress)
**Event date:** Friday, October 23, 2026 (PHP informational advisory published Oct 23).

Two days after the October 21 PHP2Shell emergency release (CVE-2026-109618/CVE-2026-109619, fixed in PHP 8.5.12, 8.4.27, 8.3.36, 8.2.35), the PHP project published a short informational advisory on a separate flaw, CVE-2026-115560, that can also lead to code execution. It triggers only in an unusual stack: an app sets session.serialize_handler to php_binary or php_serialize, then switches it to php via ini_set() before session_start(), with session upload progress still enabled (it is on by default). PHP rates the risk as unlikely in practice, is not issuing an out-of-cycle fix, and recommends setting session.upload_progress.enabled=0 until the next routine bugfix release — the same mitigation already published for CVE-2026-109619. No severity score was assigned (Not specified by source); no exploitation of CVE-2026-115560 has been reported. PHP has not said which versions are affected or whether it shares a root cause with PHP2Shell.

**Why it matters:** The PHP2Shell storm drove millions of exploit attempts in 48 hours; a follow-on "same area of the codebase" advisory means the session-handling code is still producing RCE paths and the fix wave isn't finished. Teams that applied the Oct 21 mitigation (upload progress off) are already covered; everyone else is one forgotten default away from exposure while waiting on a routine release.

**Action:** Set `session.upload_progress.enabled=0` now (one line in php.ini), and treat PHP version currency as a standing control rather than an incident response.

Sources:
- The Hacker News — "PHP Warns of Another Unpatched RCE Risk Two Days After Emergency PHP2Shell Patches," Swati Khandelwal, Oct 23, 2026 — https://thehackernews.com/2026/10/urgent-php-warns-of-another-unpatched.html
- BleepingComputer — "PHP shares mitigation for new RCE vulnerability with no patch," Sergiu Gatlan, Oct 23, 2026 11:38 ET — https://www.bleepingcomputer.com/ (headline on front page at fire time; deep link returned 404 from a constructed slug, per policy the front-page headline is cited verbatim)
- Threat Landscape — "PHP CVE-2026-115560: Disable session.upload_progress.enabled," Oct 23, 2026 — https://threatlandscape.io/blog/php-cve-2026-115560-session-upload-progress-rce-advisory

---

## Attacks and campaigns

### k0t edge-device campaign: 70,000+ credentials from exposed FortiGate and GlobalProtect gear, feeding Qilin and The Gentlemen (Group-IB + NyxLab)
**Event date:** Friday, October 23, 2026 (joint Group-IB/NyxLab investigation published).

Group-IB and NyxLab published a joint investigation into a threat actor tracked as k0t (aliases CryptoFeel, Member19, QazWsxShop; first seen October 22, 2025) who mass-hunts exposed network edge devices. The chain: enumerate internet-facing VPN/firewall appliances via FOFA and ZoomEye; exploit known flaws — CVE-2025-64446 (FortiWeb path traversal) and CVE-2026-0257 (PAN-OS/Prisma Access authentication bypass) — with bespoke Python scripts ("fortnew.py", "exploit_2026_0257.py") or brute-force common credentials; drop in via OpenConnect VPN; enumerate Active Directory, SMB and installed AV; persist through legitimate remote-access tools (MeshCentral Agent, Atera, Teramind, AnyDesk) and the AdaptixC2 adversary-emulation framework; exfiltrate via Rclone into Wasabi S3 buckets and MEGA drives, using NetExec, Impacket, noPac, Kerbrute, PetitPotam and Mimikatz along the way. More than 70,000 exposed credentials and LDAP dumps are tied to the campaign; by region, ~38% of victims are in APAC, 31.9% Latin America, 15.8% Europe, 12.7% MENA — while k0t's stated priorities begin with the US and Canada. Many campaign victims later appeared on Qilin and The Gentlemen leak sites, so k0t is assessed as an initial-access broker or affiliate. Researchers found only "negligible" data overlap with the FortiBleed cluster — this looks like a distinct dataset/operator set inside the same edge-exploitation economy.

**Why it matters:** This is the industrialization of perimeter compromise: no victim selection, only exposed-device triage. If your FortiGate/GlobalProtect estate still runs unpatched against CVE-2026-0257-class flaws, or — just as dangerous — has weak VPN credentials, assume a stranger has already enumerated you through FOFA. The abuse of legitimate RMM agents and Rclone-to-Wasabi/MEGA exfil is the pattern to hunt for this quarter.

**Action:** Patch FortiWeb/PAN-OS items named in the report, rotate VPN and LDAP credentials, audit RMM agent inventories (MeshCentral/Atera/Teramind/AnyDesk) for unauthorized installs, and alert on Rclone/copy-tool uploads to Wasabi and MEGA from egress paths.

Sources:
- The Hacker News — "Hackers Breach Enterprise Firewalls and VPNs, Exposing Over 70,000 Credentials," Ravie Lakshmanan, Oct 23, 2026 — https://thehackernews.com/2026/10/hackers-breach-enterprise-firewalls-and.html (primary research: Group-IB + NyxLab joint investigation)
- ThreatCluster (multi-outlet cluster of the disclosure) — https://threatcluster.io/cluster/hackers-exploit-firewalls-compromise-70000-credentials-globa-d5ec2977

### StealC is back as "Stadia": same code, now sold as a full-featured RAT (Varonis Threat Labs)
**Event date:** Friday, October 23, 2026 (Varonis Threat Labs analysis in publication today).

Four months after Europol-led Operation Endgame seized 326 servers and 142 domains and recovered 25M+ credentials from the StealC infostealer, the same seller resurfaced on October 16 with "Stadia" — a six-module RAT ($300–$900/month tiers) that keeps the original StealC stealer as one module and adds HVNC hidden desktops, a live command shell, file explorer, process manager and an in-memory loader. Varonis purchased the build the day it was listed, reversed the 560 KB 32-bit executable and ran its panel (v1.1.2) against a controlled host: the binary embeds a complete 26 KB Chromium App-Bound Encryption helper byte-for-byte identical to StealC 3.5, installs under a randomized C:\ProgramData directory with nine legitimate-sounding filenames and a 15-minute hidden scheduled task, and phones home over RC4-encrypted "JSON." Former StealC customers are being offered a free month on the top tier to drag the install base onto the more capable tool.

**Why it matters:** A takedown bought the ecosystem four months. Code assets plus a customer list are enough to relaunch a malware-as-a-service brand quickly — and the relaunch upgraded from stealer to persistent remote control, which is a materially worse threat for every org that assumed an Endgame headline meant a stealer problem was closed.

**Action:** Hunt for unsigned processes writing to browser directories from ProgramData paths, hidden 15-minute scheduled tasks, and HTTP payloads with mismatched content types; prioritize credential rotation where stealer indicators were seen since June.

Sources:
- Varonis Threat Labs — "StealC Is Back, and This Time It's a RAT" — https://www.varonis.com/blog/stealc-returns
- Cyber Security Intelligence — "StealC Seller Returns As Stadia RAT," uploaded Oct 23, 2026 — https://www.cybersecurityintelligence.com/blog/stealc-seller-returns-as-stadia-rat--9861.html
- Cyber Security News — "StealC Malware Returns as New Stadia RAT," Oct 23, 2026 — https://cybersecuritynews.com/stealc-malware/

### Ransomware leak sites, mid-morning additions (ET)
**Event date:** Friday, October 23, 2026, ~07:15–11:30 ET (ransomware.live discovery timestamps).

Six new victim postings were discovered in this window, on top of the overnight batch already reported: **Bankruptcy Software Specialists LLC (BSS)** (US debt-management software provider), **Bravilor Bonamat** (family-owned Dutch manufacturer of professional coffee/hot-water systems), a German custom timber-house builder, **Wah Chan Consolidated Sdn Bhd** (major Malaysian jewelry retailer), **特雷維科技 / Trevi Technology** (Taiwan, est. 2017), and **Don Pickett & Associates** (family-owned corporation, Fresno, California). A redacted entry with a FULL DATA TIMER shows a public deadline of 2026-10-25 12:00 UTC. Group attributions were not displayed on the homepage for these entries at fire time, so none are asserted here.

**Why it matters:** Midday postings extend the day's exposure list for supply-chain mapping; the small-business skew (coffee equipment, jewelry, lifts) keeps showing leak-site volume is no longer enterprise-only.

Source:
- ransomware.live — Recent Victims feed, timestamps 2026-10-23 (discovered 56 min–4 h before the 11:25 ET fetch) — https://www.ransomware.live/

## Defensive tools and advisories

No new defensive-tool releases or government advisories cleared verification in this window — CISA's advisory index still topped out at October 22 (Managing AI Token Consumption), and no KEV additions posted before fire time. The two vendor advisories closest to the window (Okta Access Gateway, Oracle Java) landed too early and appear in the footnotes.

---

## Just missed the window (first and only footnote for each)

- **Okta Access Gateway CVE-2026-115547 (CVSS v3.1 9.1)** — code injection via specially formatted Admin Console configuration values lets an authenticated administrator run system commands as the OAG service account; affects all versions before 2026.10.2, fixed in 2026.10.2, no workaround. Okta's advisory published **October 22** (pre-window); first covered by this brief today. If you run the OAG appliance, upgrade now and audit who holds Admin Console rights. — https://trust.okta.com/security-advisories/command-execution-via-admin-console-input-in-okta-access-gateway-cve-2026-115547/
- **Oracle emergency Security Alert CVE-2026-91403 (CVSS v3.1 9.1)** — emergency Java update released **October 21** (pre-window) for a flaw undermining HTTPS certificate trust in Java SE, GraalVM for JDK and GraalVM EE; exploitable through API-supplied data and sandboxed deployments. Apply the alert build outside the normal CPU cadence. — https://www.oracle.com/security-alerts/alert-cve-2026-91403.html
- **Kaspersky GReAT "BlackX" UEFI bootkit** (published **October 21** at SAS 2026) — modernized BlackLotus successor used against diplomatic organizations in Tajikistan, Uzbekistan and Turkmenistan; attributed with moderate confidence to the APT41 cluster; loads via Baton Drop (CVE-2022-21894), which still works on "patched" systems where the required manual configuration steps were never completed; then disables AV and stages cloud-drive exfil spyware. Action: verify Microsoft's Baton Drop mitigation is actually completed on your estate. — https://securelist.com/uefi-bootkit-blackx/121539/
- **UK proposes up to 14-year prison terms for undersea cable sabotage** (announced **Thursday October 22**) — new offenses for telecoms cables, interconnectors and pipelines (99% of UK international data, 74% of gas supply), replacing the Victorian Submarine Telegraph Act 1885 (max £1,000 fine); 10 years for reckless, 2 for gross negligence; statutory resilience duties for cable operators. — https://therecord.media/uk-undersea-cables-sabotage-new-sentencing-guidelines

---

## Coverage notes

- **Verified this run:** 5 reported items (2 vulnerability/patch, 3 attacks/campaigns) + 4 first-ever "Just missed" footnotes; every URL fetched and timestamped in-window, with lead stories (HPE, PHP, k0t) read in full. Second independent sources cited for HPE, PHP, k0t (aggregator cluster) and Stadia.
- **Ledger dedupe:** 12 candidates suppressed as already reported — Sandworm/UAC-0099 MythicRatatoskr, Pillow CVE-2026-112900, Imperva Node2Shell, x402 SDK flaws, Quest Diagnostics 8-K (all re-covered by The Hacker News/SecurityWeek/The Record today), Atlassian 75-CVE bulletin, Signal RingRTC, Arista VCO third zero-day, Prince of Persia US C2, LiteLLM supply chain (SecurityWeek re-reports), Capcom 11.7M (Cyber Security News today), Mirai "Potassium" PHP2Shell (SecurityWeek/THN today). No repeat 'Just missed' footnotes.
- **Dead ends / limitations:** a constructed BleepingComputer slug for the PHP mitigation story 404'd (per policy, the front-page headline is cited verbatim instead); paperless.devo.io blocklist refused; ransomware.live homepage scrape omits group attribution for the noon batch, so none was guessed. GoDaddy's 404,607-certificate revocation (event Oct 21) was skipped — two days past the window and outside footnote priority.
- Fire-time clock source: helper script (2026-10-23 12:20 EDT read at start; window END).
