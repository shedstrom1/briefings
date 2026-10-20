# The Cybersecurity News Briefing

**Window: Tuesday, October 20, 2026, 07:00 → 12:01 EDT** (new items only since the 07:00 edition; ledger dedupe applied)

**TL;DR**
- A public PoC exploit has landed for the critical Fortinet FortiSIEM unauthenticated RCE (CVE-2026-106173, CVSS 9.4) — the standalone PoC repository went live at 08:29 EDT. FortiSIEM 7.5.0 is vulnerable; 7.5.1 fixes it.
- Ahold Delhaize confirmed this morning that its cyberattack disrupted services across both the US and Europe; the company says no customer data was compromised, European sites are largely restored, and US e-commerce returns "in days."
- Ransomware leak sites added new victims this morning, including OpenText and Aveva, both claimed by the newly catalogued Mimori group, plus a Booba Project posting spree across US healthcare and municipal bodies.
- Just missed the window: Unit 42's Monday update attributing continued PAN-OS CVE-2026-0257 exploitation to Qilin and INC ransomware affiliates bypassing MFA.

## Vulnerabilities and patches

**Public PoC exploit released for Fortinet FortiSIEM unauthenticated RCE (CVE-2026-106173) — CVSS 9.4**
*Event date: PoC code published today, Oct 20, at 08:29 EDT (GitHub repository creation timestamp; verified via GitHub API).*

Horizon3.ai has released a standalone proof-of-concept exploit for CVE-2026-106173, a critical path-traversal flaw in Fortinet FortiSIEM 7.5.0 that Fortinet patched last week (fixed in 7.5.1 and above; FortiSIEM 7.6 is not affected). Per Horizon3.ai's deep dive, an unauthenticated attacker connects to the gRPC service listening on TCP port 27918 and abuses the "query-id" parameter handled by the SendQueryResult() function: path-traversal sequences combined with NUL-byte injection inside gRPC's HTTP/2 and HPACK encoding layers yield an arbitrary file write. Horizon3 weaponized the write by overwriting /opt/charting/redishb.sh — a Redis health-check shell script executed by a cron job every 60 seconds — turning the file write into remote code execution; the overwrite preserves the original file's owner and permissions, and a second variant creates a brand-new file with an exact name in two requests. The Hacker News carried the PoC release today. The research write-up itself went out last evening; the working exploit code is what landed inside this window.

**Why it matters:** SIEM appliances are prime targets — they aggregate an organization's security telemetry, hold collector credentials, and are often reachable from every network segment. A pre-authentication RCE with public code means the exploit-to-mass-scanning pipeline is already running; unpatched FortiSIEM consoles should be treated as compromised-adjacent.

**Action:** Upgrade FortiSIEM 7.5.0 deployments to 7.5.1 or above now, and verify TCP port 27918 is not reachable from untrusted networks.

Source: Horizon3.ai — "CVE-2026-106173: FortiSIEM gRPC Path Traversal RCE" (deep dive) — https://horizon3.ai/attack-research/cve-2026-106173-deep-dive-fortisiem-grpc-path-traversal-file-write-rce/
Source: GitHub — horizon3ai/CVE-2026-106173 PoC repository — created 2026-10-20 12:29:09 UTC (08:29 EDT) — https://github.com/horizon3ai/CVE-2026-106173
Source: The Hacker News — "Critical FortiSIEM Flaw Gets Public Exploit PoC for Unauthenticated Remote Code Execution" — Oct 20, 2026 — https://thehackernews.com/2026/10/critical-fortisiem-flaw-gets-public.html

## Attacks and campaigns

**Update on previously reported — Ahold Delhaize confirms disruption across US *and* Europe; recovery timeline, "no customer data compromised"**
*Event date: confirmation and restoration status published today, Oct 20. Ledger entry: Ahold Delhaize cyberattack (incident first disclosed Oct 12; confirmation footnoted Oct 19).*

Ahold Delhaize confirmed to BleepingComputer this morning (published 07:18 EDT) that last week's cyberattack "disrupted services at stores across Europe and the United States" — broadening the confirmed scope beyond the original Oct 12 disclosure of "some systems" taken offline. SecurityWeek reports the retailer has restored many websites and apps for its European brands, expects its US e-commerce brands (the grocer operates Food Lion, Giant Food and Stop & Shop in the US) to be restored in days, and says no customer data was compromised.

**Why it matters:** The "no customer data compromised" line, if it holds, separates this from a data-theft incident and points to an availability/operational-technology disruption at one of the world's largest food retailers — the second security snafu in two years. Recovery timelines for the US e-commerce stack are the metric to watch.

Source: BleepingComputer — "Ahold Delhaize: Cyberattack affects services across US and Europe" (Sergiu Gatlan) — Oct 20, 2026, 07:18 AM EDT — https://www.bleepingcomputer.com/tag/ahold-delhaize/ (tag page carrying today's item; article URL not yet indexed)
Source: SecurityWeek — "Ahold Delhaize Restoring Services Disrupted by Cyberattack" — Oct 20, 2026 — https://www.securityweek.com/ahold-delhaize-restoring-services-disrupted-by-cyberattack/

**Morning leak-site adds: Mimori claims OpenText and Aveva; Booba Project sprees across US healthcare and municipalities**
*Event date: postings appeared on leak sites today, Oct 20 (new since the 07:00 edition's overnight roster).*

Ransomware.live's summary (captured ~12:25 EDT) shows 17 victims posted today. New since the overnight run: OpenText and Aveva — both major software/industrial-software vendors — were posted by Mimori, tagged "New" (newly catalogued group). The Booba Project accounted for six posts, including the Research Foundation of The City University of New York, the City of Mt. Angel (Oregon), Everett (Washington) City Hall, the Chicago Vein Institute, Pearland Pediatrics and Coastal Carolina Neuropsychiatric Center. Also new today: 51Ridge (Newark, NJ hosting/IT firm) via N0n and Duarte Nursery via aurora. Yesterday closed at 31 victims; ransomnews counts 26 in the trailing 24 hours as of 14:51 UTC, running below the seven-day average of 37.6/day.

**Why it matters:** Two established enterprise software vendors claimed by an emerging group in one morning is a pattern worth early attention — if Mimori has real data from OpenText or Aveva, downstream customers of their platforms (including OT tooling from Aveva) could face secondary exposure and extortion pressure.

Source: Ransomware.live — victim summary (captured Oct 20, 2026 ~12:25 EDT) — https://www.ransomware.live/summary/
Source: RansomNews — "Ransomware attacks today: 26 victims listed" (as of 14:51 UTC, Oct 20) — https://ransomnews.com/ransomware-attacks-today/

## Defensive tools and advisories

**Microsoft ships Quick Machine Recovery to Windows Server 2025**
*Event date: announced today, Oct 20 (BleepingComputer 08:16 EDT).*

IT administrators can now use Quick Machine Recovery on Windows Server 2025 after installing the October 2026 security update, BleepingComputer reports. QMR delivers targeted Windows Update fixes to servers stuck in a boot failure, without physical or console access. Windows Report ties the capability to KB5126646 (build 26100.33650).

**Why it matters:** Faster, remote recovery from bad updates matters a lot this month, given the October Patch Tuesday rollout already broke Exchange OWA sign-in and mail flow on some servers (previously reported) — QMR is exactly the class of control that shortens those incidents.

Source: BleepingComputer — "Windows Server 2025 gets support for quick machine recovery" (Sergiu Gatlan) — Oct 20, 2026, 08:16 AM EDT — https://www.bleepingcomputer.com/ (homepage item)
Source: Windows Report — "Windows Server 2025 Gets Quick Machine Recovery With KB5126646" — https://windowsreport.com/windows-server-2025-gets-quick-machine-recovery-with-kb5126646/

## Also noted
- No CISA KEV additions since the Oct 15 "CISA Adds One Known Exploited Vulnerability" alert; no new CISA alert observed this morning (the /news-events/alerts index returned a 404; KEV print page used as the cross-check).
- Oracle's October CPU remains pre-announcement only as of 12:01 EDT (1,283 new security patches; highest CVSS 9.9 on Essbase, 9.8 on APEX/AHF/Solaris, 8.8 on Database Server; release promised "Tuesday, October 20") — the full advisory should land before the 17:00 edition.

## Just missed the window (first disclosed Monday, Oct 19 — footnoted once, per ledger rules)
- **PAN-OS CVE-2026-0257 (CVSS 7.8) ransomware exploitation:** Unit 42's Monday threat-brief update attributes continued exploitation of the GlobalProtect authentication bypass to ransomware operators Spikey Scorpius (aka Qilin) and Tarnished Scorpius (aka INC), using the flaw for initial access and MFA-bypassing VPN sessions; ReliaQuest earlier named Settra. https://unit42.paloaltonetworks.com/active-exploitation-of-pan-os-cve-2026-0257/
- **Redis RESTORE CVE-2026-25243 exploited in the wild:** Defused honeypots recorded repeated scripted attempts on Oct 16–17 (one attacker wiped the keyspace after each run); Canada's Cyber Centre flagged active exploitation in AV26-423 Update 1. Fixed in Redis 8.6.3; not yet in CISA KEV. https://securityonline.info/redis-restore-cve-2026-25243-exploited/
- **Autonomous AI-agent intrusions via the Cairn tool:** NorForge's Snorre Fagerland reported Monday (named victims via Reuters) that a Chinese-speaking operator's agents attacked 1,200+ sites and reached 100+ companies including Deere, SharkNinja and Cummins. https://thehackernews.com/
- **Pegasus on a Serbian rector's iPhone:** Citizen Lab confirmed a zero-click infection of the University of Belgrade rector's phone, days before Serbia's Oct 25 snap election. https://thehackernews.com/

## Coverage notes
- Verified this run: 3 full items + 1 defensive-tools item + 4 "just missed" footnotes. Every cited page was fetched; the lead item's release time was verified against GitHub API metadata (PoC repo created 2026-10-20 12:29:09 UTC).
- Ledger dedupe: 9 candidates suppressed as already reported — OVHcloud CLI hijack (re-surfaced by BleepingComputer 07:31), DHS DeepSeek order (BleepingComputer 10:19 + The Register re-coverage), Bullhorn (Register follow-up), Siemens CVE-2026-110936 ClickFix re-surfacing (Help Net Security), Plex2Shell re-surfacing, ConnectWise re-coverage, Nikkei third breach (BleepingComputer re-cover), Rank Math PRO (BleepingComputer re-cover), Ahold Delhaize (carried as an Update, not a new item). Repeat 'Just missed' footnotes suppressed for all previously tagged entries (Nikkei, DHS/DeepSeek, Rank Math, Exchange/KB5126646, F5 NGINX, Broadcom WPS, Plex2Shell, Ahold, ETS, PeerStitch).
- Out-of-window skips (not near-misses): MITRE ATT&CK v20 (released Oct 13), "Infinity Core" Linux kernel PoCs (disclosed Oct 16), Sourcenext 22.5M-record breach (disclosed Oct 15), Akande tax-fraud sentencing (event dates to a Feb 2026 IRS release despite today's BleepingComputer post).
- Dead ends: CISA alerts index 404 (one attempt, KEV print page substituted); BleepingComputer's today-morning Ahold article URL not yet search-indexed (tag page cited instead).
- Quiet midday slice by design: 5-hour window between the 07:00 and 17:00 editions.
