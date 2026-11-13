# Cybersecurity News Briefing — Friday, November 13, 2026 (Overnight Sweep)

**Window:** 2026-11-12 17:20 EST → 2026-11-13 07:20 EST (previous fire → now). New-items-only; deduped against the reported ledger.

## TL;DR

- **Check Point confirmed an exploited Gaia OS zero-day (CVE-2026-123039, CVSS 7.8)** — chained with PHP2Shell on Mobile Access portals to root unpatched Security Gateways, install the TSGA/TSProxy backdoor (PhiliKit lineage), pivot over SSH to a VMware ESXi host and run a recursive wiper leaving "DREAM_ANNEXATION.txt" notes; emergency hotfixes are out.
- **The Microsoft 365 outage stretched past 15 hours with fixes failing** — restored servers keep going unresponsive under traffic; Microsoft added incident EX1550543, is throttling/rerouting/rebuilding infrastructure, and has not fully ruled out a cyberattack.
- **Microsoft confirmed November Patch Tuesday updates break Windows activation** — slmgr/KMS commands fail with 0x80041008 on Win10 21H2/22H2, Win11 23H2 and Server 2019/2022/2025; fix promised "over the coming days."
- **Europol/DOJ "Operation TorNado" arrested 354 dark-web vendors and buyers** across five seized marketplaces, with €60M + $150M seized.
- **Lego's BrickLink came back online Friday** — Lego says passwords and payment data were never accessed after the week-long "unusual activity" shutdown.

---

## Vulnerabilities and patches

### Check Point zero-day CVE-2026-123039 exploited in gateway intrusions ending in ESXi wiping
**Event date:** November 12–13, 2026 (Check Point Research report Nov 12; advisory + emergency hotfixes Thursday; press Nov 13) — **Severity: CVSS 7.8 (High)** per Check Point/advisory as reported.

Check Point Research documented an unknown actor exploiting unpatched PHP2Shell (CVE-2026-109618/CVE-2026-109619) against Check Point Mobile Access portals, then escalating portal-service privileges to root via a previously unreported local privilege escalation in the Gaia OS DynamicCLI IPC interface — CVE-2026-123039. The root-level `dynamic_server` daemon listens on a world-writable socket (`/tmp/dynamic_socket`) with no caller authentication, so any authenticated local user can execute arbitrary commands as root with a single socket write. Wave one (Oct 30, from 130.94.19[.]204) hit Israeli organizations, including destructive follow-on activity; wave two (Nov 2) was opportunistic and worldwide. Attackers deployed PHP webshells (`zz*.tpl`, key "zz2026"), the persistent **TSGA** backdoor (`slot_CPconfigdaemon` — encrypted remote access, config theft, backup/snapshot persistence, process injection, firewall-log tampering) with its **TSProxy** concealed-access component, ran the gogo scanner, then pivoted via SSH using the vpxuser account to a VMware ESXi host and executed the `destroy_v6` recursive wiper, leaving politically themed `DREAM_ANNEXATION.txt` notes. Check Point said it "observed an active exploit of this vulnerability against a couple of dozen customers." Impacted: Force Firewalls, Management/Log Servers, Multi-Domain SM/Log, Maestro Orchestrator across R81–R82.20. TSGA/TSProxy share code, configuration and cryptographic material with PhiliKit (ESET, June 2026, attributed to China-nexus UNC5221/Armory Castle) — but Check Point declines high-confidence attribution, citing the wiper, the Israel focus and the Dream Security branding of the note (Dream had just published PHP2Shell-vs-PAN research), leaving initial-access-broker and false-flag scenarios open.

**Why it matters:** This is PHP2Shell's first confirmed chain into a major firewall vendor's perimeter appliance to a destructive payload — a niche LPE (no CVE until now, requires only local code) is enough to fully own the gateway and reach the hypervisor behind it, and the vendor-hunts-evidence guidance (commands using `system()` outside Clish) is actionable immediately.

**Action:** Deploy the emergency hotfixes (R81/R82 lines); where not immediate, Check Point's interim mitigation is to stop the `dynamic_server` process and prevent restart. Hunt for `zz*.tpl` webshells, `/tmp/CPconfigdaemon`, `/tmp/.bd.log`, gogo scanning and vpxuser SSH logins; IOCs are published.

- Source: Check Point Research, "PHP2Shell Exploitation on Check Point Gateway," Nov 12, 2026 — https://research.checkpoint.com/2026/php2shell-exploitation-on-check-point-gateway/
- Source: BleepingComputer, Sergiu Gatlan, "Check Point warns of zero-day flaw exploited for root access," Nov 13, 2026 05:44 ET — https://www.bleepingcomputer.com/news/security/check-point-patches-zero-day-that-lets-hackers-get-root-access/
- Source: The Hacker News, Ravie Lakshmanan, "Hackers Exploit PHP2Shell Flaws in Check Point Gateways to Deploy TSGA Backdoor and Wipe ESXi Files," Nov 13, 2026 — https://thehackernews.com/2026/11/hackers-exploit-php2shell-flaws-in.html
- Source: SecurityWeek, "PHP2Shell Exploited in Destructive Check Point Gateway Attacks," Nov 13, 2026 — https://www.securityweek.com/php2shell-exploited-in-destructive-check-point-gateway-attacks/

### Microsoft confirms November updates break Windows activation (0x80041008)
**Event date:** November 13, 2026 (BC confirmation 04:36 ET; service alert Wednesday) — **Severity: not specified by source** (quality bug, not a CVE).

Microsoft acknowledged that the November 10 Patch Tuesday updates cause activation/licensing operations to fail with error **0x80041008 "Invalid parameter (SWbemObjectEx)"** when running `slmgr.vbs /skms-domain` or `/ipk` on Windows 10 21H2/22H2, Windows 11 23H2, and Windows Server 2019/2022/2025. Devices configuring KMS activation can fail to initiate activation; already-activated devices (including KMS clients with the key baked into the image that never run the affected commands) are unaffected. A fix is promised "over the coming days"; organizations needing immediate relief are told to open a Support for Business case. This is the third confirmed follow-on defect from this Patch Tuesday, after the November-CU install failures on October-preview Windows 11 systems and the SQL Server performance regressions.

**Why it matters:** KMS key rotations and imaging pipelines stall silently — activation failures at deploy time (or the 180-day KMS renewal) surface as compliance/activation debt weeks later, and admins juggling three concurrent November CU regressions may consider a targeted uninstall.

**Action:** Defer slmgr/KMS reconfiguration work on affected builds until the fix; already-activated fleets need no action today.

- Source: BleepingComputer, Sergiu Gatlan, "Microsoft confirms Windows activation failures after November updates," Nov 13, 2026 04:36 ET — https://www.bleepingcomputer.com/news/microsoft/microsoft-confirms-windows-activation-failures-after-november-updates/
- Source: WindowsForum megathread, Nov 13, 2026 — https://windowsforum.com/news/november-2026-windows-updates-break-slmgr-activation-commands-with-error-0x80041008.452146/

---

## Attacks and campaigns

### Update on previously reported (M365-outage-MO1550821, ledgered 2026-11-12): outage runs past 15 hours overnight as restored servers keep failing
**Event date:** overnight Nov 12–13, 2026 — **Severity: not applicable** (availability incident; Microsoft and EVP Charles Lamanna say no evidence of a cyberattack, though The Register notes the company "hasn't completely ruled out" one).

Microsoft's global Microsoft 365 outage continued into Friday with recovery stalling: in updated admin-center advisories **EX1550543** (Exchange Online) and **MO1550821**, restored servers "can become unresponsive again once they receive traffic." Engineers are running parallel workstreams — throttling which traffic types reach affected infrastructure, rebuilding a subset of servers on a previous configuration (a step implying suspicion of a recent change, unconfirmed), and rerouting/redistributing load with "strategic traffic blocking"; partial recovery is holding only in regions where those measures applied. Peak Downdetector reports topped 120,000 across 74 countries; affected services now listed include Exchange Online, Teams, SharePoint, Copilot, Defender XDR, Planner, To Do, Intune, Purview and Power BI. Third-party fallout included OpenAI and Anthropic Microsoft 365 connector degradation and Workhorse Group postponing Q3 results to Nov 16. Microsoft still has not announced a root cause or a full-restoration ETA and has promised a post-incident report.

**Why it matters:** A health-check-triggered cascade that resists restart-and-shift-left recovery is the failure mode that turns a routine Tuesday patch week into a multi-day availability crisis — and traffic-rebuild loops make "wait it out" planning unreliable for SLA-bound orgs.

**Action:** Track EX1550543/MO1550821 at status.cloud.microsoft; expect delayed mail queues after restoration; local troubleshooting cannot fix this fault.

- Source: Cyber Security News, "Microsoft 365 Massive Outage - Fixes Fail as Restored Servers Keep Going Unresponsive," Nov 13, 2026 — https://cybersecuritynews.com/microsoft-365-massive-outage-fixes-fail-as-restored-servers-keep-going-unresponsive/
- Source: The Register, "Microsoft's massive M365 outage stretches past 15 hours as its fixes keep failing," Nov 13, 2026 — https://www.theregister.com/ (front page, ongoing coverage)

### Lego's BrickLink restored after week offline: "passwords and payment information were not accessed"
**Event date:** November 13, 2026 (Lego Friday statement; site down since Nov 6) — **Severity: not specified by source.**

Lego-owned BrickLink, the spare-parts marketplace with thousands of storefronts, returned online Friday after nearly a week dark following "unusual activity on the platform." Lego's added update says the investigation found passwords and payment information were not accessed and "you do not need to take any action at all," though the Studio Forum remains down and more detail is promised "over the coming days." AFOLs had criticized the week of silence on a splash page; The Register's Nov 12 20:08 UTC piece captured the pre-resolution state.

**Why it matters:** A rare benign resolution to a mystery-outage disclosure — but the week of no-comm is the cautionary tale for anyone relying on a single-marketplace channel, and the still-down Studio Forum shows the incident isn't fully closed.

**Action:** No user action per Lego; watch for the promised follow-up and treat "payment data not accessed" as vendor-stated pending the full report.

- Source: The Register, Connor Jones, "Lego marketplace BrickLink in pieces after 'unusual activity' takes it offline for almost a week," published Nov 12, 2026 20:08 UTC, updated Nov 13, 2026 — https://www.theregister.com/security/2026-11-12/lego-marketplace-bricklink-in-pieces-after-unusual-activity-takes-it-offline-for-almost-a-week/5309425

---

## Defensive tools and advisories

### Operation TorNado: 354 arrests across five dark-web drug markets; €60M + $150M seized
**Event date:** November 12–13, 2026 (Europol/DOJ announcement; BleepingComputer Nov 13 03:34 ET) — **Severity: not applicable** (law-enforcement operation).

Europol, 14 national agencies and US authorities concluded "Operation TorNado" (May 2025–October 2026) with **354 arrests** of vendors and buyers on the already-dismantled Archetyp, Nemesis, Tor2Door, Bohemia and Kingdom marketplaces — plus forums, single-vendor shops and encrypted-messaging sellers. Seized: over €60M in cash/crypto, 950 kg of drugs and 57 firearms in Europe; over 4.8 tonnes of drugs, 152 firearms and $150M in the US. Europol pointed to "the evolving resilience of dark web marketplaces, which continue to adapt despite takedowns"; DOJ framed it as the successor line to JCODE/DisrupTor/RapTor actions against the opioid trade.

**Why it matters:** Vendor/buyer identification from prior marketplace seizures keeps producing mass-arrest waves even after infrastructure takedowns — a deterrence signal to the darknet economy, while resilience language signals continued supply-side churn that feeds ransomware and infostealer ecosystems.

- Source: BleepingComputer, Sergiu Gatlan, "Police arrest 354 suspects in dark web cybercrime crackdown," Nov 13, 2026 03:34 ET — https://www.bleepingcomputer.com/news/security/police-arrest-354-vendors-buyers-in-global-cybercrime-crackdown/
- Source: Help Net Security, "354 dark web vendors and buyers arrested in Operation TorNado," Nov 13, 2026 — https://www.helpnetsecurity.com/2026-11-13/europol-dark-web-arrests-cybercrime/

---

## Also noted (verified one-liners)

- **NATO issued then retracted Yandex Go travel guidance** for 2,000+ NATO Edge 26 attendees in Izmir after a researcher flagged FSB access to taxi data; corrigendum sent within about two hours, NATO called it "an oversight." (The Record, Alexander Martin, Nov 13, 2026 02:53 ET — https://therecord.media/nato-issues-and-withdraws-guidance-over-russian-taxi-app)
- **Overnight leak-site batch (ransomware.live / recentbreaches):** Carsem (semiconductor OSAT) and Turkish law firm Koray Topaç (deadline Nov 20) discovered ~06:20 ET Friday (groups unconfirmed); Botswana prepaid-utilities platform as N0N #216 (pending exposure, ~23:20 ET Thu); Thursday-evening additions Franc-Oil (Nigeria), Upper Web (Brazil), osiiminppal.com; overnight INC Ransom listings (BCP Systems, Pomer & Boccia, La Filipina Uy Gongco, HONG BAO GROUP, coeur mining, Överum Agro, Dunbar & Boardman); Qilin listed York University. (ransomware.live, Nov 12–13; recentbreaches.com, Nov 12–13 — https://ransomware.live/)
- **Community (r/netsec):** practitioner mmd0xFF published an ARTEX detection signature pack drawn from the ongoing IOC/playbook work (Fri 06:08 ET — https://www.reddit.com/r/netsec/comments/1xv53bm/sharing_artex_detection_signature_pack_from_ioc/); an "OWASP Top 10 Neocloud and AI Data Center Security Risks" project-release was posted Thu 17:58 ET (https://www.reddit.com/r/netsec/comments/1xurha8/owasp_top_10_neocloud_and_ai_data_center_security/).

## Just missed the window

- **SilentAir (Enea):** a suspected state-level actor using compromised equipment inside six+ Asian telcos to send SS7/Diameter attacks over the IPX and intercept calls/SMS 2FA of selected targets, mostly in Taiwan; high-confidence state actor, low-confidence China link; assessed disrupted for now. Enea's underlying report dated Nov 11 (in-window re-coverage by THN Nov 13 06:30 ET — https://thehackernews.com/2026/11/silentair-hijacks-asian-telecom.html — and SecurityWeek).

## Coverage notes

- **Verified count:** 4 new items + 1 previously-reported update were fully fetched and date-checked (units: report items, not events); plus 3 also-noted lines and 1 window footnote.
- **Ledger dedupe:** ~12 in-window candidates suppressed as already reported or already footnoted — N-central/4EverAble2Shell Friday re-coverage (THN/SW), SCKit "worm-like" BC 17:53 (RelayTrap already ledgered), wfx-loot BC 17:25 + Reddit re-post, Operation Endgame THN re-cover, Fortinet ~100 flaws (Nov 10), Chrome CVE-2026-125350 (Nov 10), Harvard HIBP re-cover (Nov 3), AhsayCBS implant re-cover (Nov 10), Electroneum indictment (footnoted Nov 5), DOJ Binance/FaceStealer (Nov 12), M365 outage original (Nov 12), N0N/INC items already in the 17:20 batch. CISA KEV cross-check (Senserva 7-day list): all 12 recent additions were ledgered — no in-window KEV news. Two "November 12" hits (Ivanti EPMM, FortiMail KEV) proved to be April/October articles re-indexed by aggregators and were rejected.
- **Dead ends / limitations:** none fatal; r/security new-listing carried no in-window posts at check time; ransomware.live group attributions for the two newest Friday entries (Carsem, Koray Topaç) were truncated in the listing — marked unconfirmed. HN front page (07:30) carried no additional new cyber items (Claude.ai outage chatter noted as non-security).
- Indexes swept: BleepingComputer, The Hacker News, SecurityWeek, The Record, The Register rivers; ransomware.live; recentbreaches.com; HN front page; r/netsec + r/security (Reddit new-only).
