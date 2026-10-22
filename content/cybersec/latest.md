# Cybersecurity News Briefing — October 22, 2026 (12:00 ET edition)

**Window:** 2026-10-22 07:00 → 12:20 EDT (new items only, deduped against the reported ledger)

**TL;DR**
- PHP2Shell goes mass-scale: Cloudflare says exploit attempts jumped from thousands to "millions"; Japan saw the biggest wave of precautionary shutdowns yet (Nomura, MUFG, ANA, JR East, National Tax Agency), while Finland's state IT agency says it has seen no actual exploitation.
- Microsoft published the fix path for broken EWS apps in Exchange Online — admins can re-enable the retired API, but only until the final shutdown on April 1, 2027.
- Searchlight Cyber open-sourced php2shell-scanner, an active host-checking tool for the two PHP flaws.
- Ransomware leak sites added seven victims during the window: three to Akira, 2.52 TB claimed from Germany's DR.SCHNELL by Rhysida, and three to Qilin.
- CISA's KEV catalog shipped no new additions with the October 21 14:04 UTC release (count 1,754).

## Vulnerabilities and patches

**Update on previously reported — PHP2Shell: Cloudflare reports "millions" of exploit attempts as Japan shuts services down (event date: Thursday, October 22, in-window)**
This continues the PHP2Shell story reported since Tuesday (ledger: PHP2Shell early leak, emergency release, and Amazon MadPot pre-disclosure attempt). Cloudflare CEO Matthew Prince posted late Wednesday that the network saw thousands of exploit attempts in the ten hours before public announcement, tens of thousands within 45 minutes of announcement, hundreds of thousands within two hours — and millions now. Reuters reports the surge Thursday as Japanese firms temporarily suspended internet services: brokerage Nomura, Mitsubishi UFJ, airline ANA, East Japan Railway, brewer Asahi, and insurer Tokio Marine, plus partial suspensions at the Finance Ministry, Meteorological Agency, and National Tax Agency. Orange paused some PHP-based sites; Finland's Valtori spent the night patching and began lifting blocks, stressing the outages were precautionary and it is not aware of any successful exploitation. Microsoft CEO Satya Nadella, speaking in Germany Thursday: "I've not seen anything like this in a long, long time."
Severity: both CVE-2026-109618 and CVE-2026-109619 are rated 9.2 on CVSS v4.0. VulnCheck's Target Intel pegs 1,474,985 internet-facing hosts (56.6%) advertising a PHP build below the fix, with another 1,133,000 running versionless PHP treated as likely vulnerable. Counterpoint worth noting: Searchlight Cyber tells Help Net Security most public exploit code does not work yet — mass attempts are not mass compromise.
**Why it matters:** This is the first test of how the internet handles an emergency in the language that runs ~69% of websites whose server-side language W3Techs can identify — and of how fast AI-discovered flaws (Wiz/Calif/OpenAI found these) move from disclosure to worldwide scramble. The EOL branches (PHP 8.1 and older) have no upstream fix at all.
**Action:** Update to PHP 8.5.12 / 8.4.27 / 8.3.36 / 8.2.35; per FBI and NSA guidance, take systems offline until fully mitigated if immediate patching isn't feasible; monitor for PHP processes spawning shells or downloading files (Newsweek, updated 9:50 a.m. ET today).
Sources:
- Reuters, "Millions of hacking attempts as companies, governments rush to patch PHP flaws," Oct 22, 2026 (~11:30 a.m. EDT) — https://www.reuters.com/world/asia-pacific/cloudflare-sees-millions-exploit-attempts-after-php-disclosure-2026-10-22/
- Newsweek, "FBI issues advice as security issue hits global websites," Oct 22, 2026 04:42 EDT, updated 09:50 EDT — https://www.newsweek.com/fbi-issues-advice-as-security-issue-hits-global-websites-12603155
- VulnCheck, "PHP2Shell Vulnerabilities" (Target Intel + IOCs) — https://www.vulncheck.com/blog/php2shell-vulnerabilities

## Attacks and campaigns

**Ransomware leak sites add seven victims in the five-hour window (discovered Oct 22, ~07:40–11:50 EDT)**
Akira posted three victims shortly before noon: Bethany Press International (US religious book publisher), Medienwerkstatt (German digital/print communications firm), and Annapath Inc. (a privately held US surgical and cytopathology laboratory). Rhysida claimed German facility-services group DR.SCHNELL GmbH & Co. KGaA with a claimed full network dump — 175,151 files / 2.52 TB. Qilin added three: Airtificial (Spanish applied-AI company), Bathurst RSL (Australian community/RSL club), and Creative Universe (Montreal audio-description and accessibility media producer). All are fresh leak-site claims — victim counts and "stolen data" figures are attacker-claimed and unverified.
**Why it matters:** Akira and Qilin were the No. 1/No. 2 tracked groups in Rapid7's Q3 report (342 and 339 incidents); a four-group, seven-victim single morning — including a healthcare lab and a community institution — shows the volume game continuing unabated through a news cycle dominated by PHP.
Source:
- Ransomware.live, Recent Victims (discoveries timestamped 2026-10-22) — https://www.ransomware.live/

## Defensive tools and advisories

**Microsoft publishes how to re-enable the retired EWS API in Exchange Online (reported Thursday 11:49 a.m. EDT)**
With EWS blocked-by-default since October 1, Microsoft told admins they can restore EWS-dependent apps by unchecking "Block your entire organization from using EWS" in Microsoft 365 Baseline Security Mode, or by running Set-OrganizationConfig -EWSEnabled $true in Exchange Online PowerShell (takes effect within about four hours). Microsoft is explicit that this is the first stage of disablement: re-enabling stops working at the final shutdown on April 1, 2027, and the long-term path is moving apps to Microsoft Graph. On-premises Exchange is unaffected. EWS underpins Outlook desktop add-ins, older Outlook versions, and Apple Mail/Calendar on macOS; the retirement's urgency traces to the 2024 Midnight Blizzard incident, which exploited EWS.
**Why it matters:** Four+ months of tenant breakage are now guaranteed to produce "temporarily re-enable EWS" helpdesk waves — every re-enabled tenant is re-opening the exact attack surface Microsoft is trying to close. Inventory EWS consumers now and push vendors for Graph-based builds.
**Action:** Test Baseline Security Mode behavior in a pilot tenant, list apps still on EWS, and set internal deadlines ahead of April 1, 2027.
Source:
- BleepingComputer, "Microsoft shares how to fix broken Exchange Online EWS apps," Sergiu Gatlan, Oct 22, 2026 11:49 AM EDT — https://www.bleepingcomputer.com/news/microsoft/microsoft-shares-how-to-fix-broken-exchange-online-ews-apps/

**Searchlight Cyber open-sources php2shell-scanner (released; covered Thursday)**
The tool actively tests whether a host is exposed to CVE-2026-109618/109619 by deliberately provoking the memory-exhaustion condition the bugs rely on and watching the host's reaction, falling back to the advertised PHP version banner only as a last resort. Exposure focus is the default-configuration killers: Apache mod_php and PHP-FPM, with confirmed exploitation on widely deployed stacks including WordPress, Drupal, Joomla, phpMyAdmin, Nextcloud, and Magento.
**Why it matters:** Version-banner scanning was producing false comfort on patched-but-branded hosts; a behavioral probe is the right way to triage internet-facing PHP inventories before the weekend.
**Action:** Run the scanner against all internet-facing PHP hosts, prioritized by the FBI's inventory guidance (internet-exposed systems first, then products that bundle PHP).
Sources:
- Help Net Security, "New PHP2Shell scanner checks your hosts for CVE-2026-109618 and CVE-2026-109619," Oct 22, 2026 — https://www.helpnetsecurity.com/2026/10/22/php2shell-scanner-tool/
- The Hacker News (same-day coverage), "New PHP2Shell scanner checks your hosts," Oct 22, 2026 — https://thehackernews.com/

## Also noted

- CISA KEV: the October 21 catalog release (14:04:39 UTC, count 1,754) added nothing new today — the newest entries remain the Oct 20 SiteGuard/Plex batch already in the ledger. — https://www.cisa.gov/sites/default/files/feeds/known_exploited_vulnerabilities.json
- Searchlight Cyber assessment (via Help Net Security, Oct 22): most publicly circulating PHP2Shell exploit code does not work yet. — https://www.helpnetsecurity.com/2026/10/22/php2shell-vulnerability-fortinet-cisco-palo-alto-networks/

## Just missed the window

- **Signal/RingRTC hang-up flaw (CVE-2026-113380, CVSS 9.3):** a malicious call participant can flood RingRTC's finite-state-machine event queue so the victim's hang-up never executes — audio, and possibly video, keep streaming to the caller while the user believes the call ended. Researcher Imahian published a PoC; fixed in RingRTC 2.72.0. Disclosure/coverage Wednesday, Oct 21 (The Register) — https://www.theregister.com/security/2026/10/21/signal-bug-could-keep-your-mic-and-camera-on-after-hangup/5304528
- **Columbia, South Carolina (state capital, ~150k residents) hit by a cyberattack** knocking out the MyColaSC citizen portal, GIS, and animal-services systems; the state's SLED is leading the investigation; the city hasn't confirmed ransomware and no group has claimed credit "as of Wednesday." The Record's report ran Thursday; initial city disclosure was ~Monday, Oct 20 — https://therecord.media/south-carolina-officials-offer-assistance-cyber-incident
- **Splunk + SolarWinds patch batches (released Wednesday, Oct 21; summarized Thursday 6:45 a.m. ET, just pre-window):** Splunk Enterprise 9.4.16/10.0.11/10.2.8/10.4.4 fix 16 CVEs including critical-severity CVE-2026-76299 and CVE-2026-76302 (improper access control / improper neutralization); SOAR 8.7.0 fixes 15 flaws including critical CVE-2026-76531 and CVE-2026-76407; SolarWinds Observability CVE-2026-28328 (broken authorization schema letting low-privileged users read restricted data) — https://www.securityweek.com/splunk-solarwinds-release-fresh-security-updates/
- **PingCAP / TiDB Cloud breach (disclosed Monday, Oct 19):** attacker access from Sept 9 to Oct 7 reached customers' billing/business data, cloud account and product-usage information, SQL editor text/errors, and an encrypted table of API keys; PingCAP's investigation found no evidence of direct customer-database access. Dark Reading's analysis ran Thursday — https://www.darkreading.com/cloud-security/tidb-breach-flawed-cloud-security

## Coverage notes

- **Verified in this edition:** 8 items (4 in-window, including the PHP2Shell update lead, plus 4 first-time "Just missed" footnotes) + 2 verified also-noted items. Sources read in full or substantially: Reuters, Newsweek, BleepingComputer (EWS), SecurityWeek (Splunk/SolarWinds), Help Net Security (scanner), The Record (Columbia), CISA KEV JSON, VulnCheck.
- **Ledger dedupe:** 10 candidates suppressed as already reported: THN/BC/SecurityWeek "PHP2Shell 4 days before disclosure" coverage (matches this morning's MadPot ledger line), BleepingComputer 7:41 a.m. GitLab CVSS 10.0 re-coverage, SecurityWeek Thursday re-coverage of Cisco's 34 advisories, SecurityWeek re-coverage of Chrome ClickFix/Gemini hardening, THN full article on the OpenCode MCP sign-in RCE (footnoted once at 07:00 — not re-footnoted), THN + BC Operation Serengeti III (reported 07:00), The Record re-coverage of the ESET Sandworm/Ukraine-grain report (reported 07:00), THN Railway re-coverage (reported 07:00), The Cyber Express re-coverage of the SafePay FBI/CISA advisory (reported Oct 20), THN OpenAI agent-monitoring re-coverage (reported Oct 21).
- **Aged out (Oct 14–15 disclosures, correctly not footnoted):** PAN-OS CVE-2026-0322, Cisco SEG CVE-2026-76519, Next.js out-of-band RCE pair, Langflow CVE-2026-105740 honeypot data, Nextcloud/Collabora CVE-2026-110447 backdoor, DOJ North Korean laptop-farm takedown, XBOW Chrome full-chain exploit.
- **Indexes used:** The Hacker News, BleepingComputer, SecurityWeek, The Record, Help Net Security, Dark Reading, ransomware.live, Bitsight breach pulse, CISA KEV JSON. **Down/dead ends:** one CISA KEV news search returned empty — resolved by direct KEV JSON fetch; no other retrieval failures.
- No KEV additions, no new vendor emergency patches beyond the PHP follow-through — a mid-day edition led by one escalation rather than a pile, with no padding.
