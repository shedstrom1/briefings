# Cybersecurity News Briefing — Monday, November 2, 2026 (12:00 ET edition)

**Window:** 2026-11-02 07:00 → 12:00 ET (previous fire 07:20; new items only, ledger-deduped)

## TL;DR

- **Second wave of suspected attacks on supermarket refrigeration hits six European countries** — 115+ stores across Belgium, Sweden, Luxembourg, Ireland, Estonia and the Netherlands had freezers forced into defrost mode overnight, weeks after the France incident.
- **Google publishes takedown of the XCPE botnet** — 1.3M+ Android TV set-top boxes backdoored in firmware by OEM Videostrong, which Google says intentionally sold criminal access and has now banned from certified Android.
- **BTCPay Server incident report (Monday)**: hot-wallet drains target instances ≤2.4.1; update to 2.4.7 now, take older instances offline.
- **GitLab CVE-2026-97229 update**: Shadowserver still tracks 1,000+ internet-exposed unpatched servers three weeks after the fix, with exploitation ongoing.
- **Fortinet FortiGuard publishes PapRAT research**: India-linked Bitter (TA397) runs three Windows infection chains, one with no malware binary at all.

---

## Vulnerabilities and patches

### Over 1,000 GitLab servers still unpatched against actively exploited max-severity RCE (Update on previously reported — ledger: CVE-2026-97229 KEV addition, Oct 29)
**Event date:** November 2, 2026 (Shadowserver counts published Sunday; BleepingComputer 07:47 AM ET).
Shadowserver reported it is tracking "over 1000 GitLab instances vulnerable to CVE-2026-97229 (on @CISACyber KEV) and over 11300 instances vulnerable to CVE-2026-110264 (out of around 39K GitLab instances we see daily)." The flaw — an integer overflow in GitLab CI/CD's Regular Expression Compiler allowing unauthenticated RCE via a crafted regex in a CI/CD config (CVSS 10.0) — was patched October 12 in GitLab CE/EE 19.4.2, 19.3.5 and 19.2.8, added to CISA's KEV October 29 with a BOD 26-04 remediation date of November 1. Exploitation is ongoing; Rapid7 has reproduced the chain and a Metasploit module has reportedly been merged, and self-managed installs built from source need a Ruby-version check because the vulnerable Onigmo handling comes from Ruby itself.
**Why it matters:** Three weeks of public patch plus a KEV deadline have still left ~2.5% of scanned internet-facing GitLab servers exploitable — DevSecOps platforms are high-value targets (CI runner code execution = supply-chain pivot), and the second regex-compiler flaw (CVE-2026-110264) shows the same attack surface remains under-maintained.
**Action:** Self-managed GitLab → upgrade to 19.4.2/19.3.5/19.2.8 immediately; verify the Ruby build on custom/source installs; hunt for exploitation since October 12.
Source: BleepingComputer (original reporting), "Over 1,000 GitLab servers vulnerable to ongoing RCE attacks," Nov 2, 2026 07:47 AM ET — https://www.bleepingcomputer.com/news/security/ (article: over-1-000-gitlab-servers-vulnerable-to-ongoing-rce-attacks)
Source: WindowsForum news desk, "GitLab CVE-2026-97229: Over 1,000 Servers Exposed to Exploited RCE; Custom Builds Need Ruby Fix," Nov 2, 2026 08:40 EST — https://windowsforum.com/news/gitlab-cve-2026-97229-over-1-000-servers-exposed-to-exploited-rce-custom-builds-need-ruby-fix.450595/
Source: OSINTSights, "GitLab Servers Targeted in Ongoing RCE Attacks," Nov 2, 2026 — https://osintsights.com/gitlab-servers-targeted-in-ongoing-rce-attacks

---

## Attacks and campaigns

### Suspected cyberattacks hit supermarket refrigeration across six European countries (second wave)
**Event date:** overnight November 1–2, 2026.
Hackers remotely changed temperature settings on freezers and cooling systems at stores run by multiple chains, shutting down refrigerators or forcing defrost cycles and spiking temperatures as high as 50 °C. BleepingComputer tallied more than 115 affected stores: Ahold Delhaize Belgium/Luxembourg (~40), Carrefour Belgium (15), Intermarché Belgium (~15), SuperValu Ireland (13 of 223, a specific refrigeration model), Spar/Colruyt (5), Coop Sweden (6–7), ICA Sweden (4+), Selver Estonia (1), plus an Axfood home-delivery distribution centre near Gothenburg. Sweden's national cybersecurity centre head John Billow confirmed the agency is responding to multiple incidents, pointing at exposed operational-technology devices; EU agriculture commissioner Christophe Hansen called it "a deliberate attempt to disrupt" EU food supplies. There is no claim of responsibility for this wave or for the October 11 France attack that preceded it. Severity: Not specified by source.
**Why it matters:** This is the second coordinated disruption of store-level refrigeration controllers in three weeks — a repeatable, low-sophistication attack against internet-exposed OT that spoils food, risks fires, and needs no data theft to cause public harm. Retail refrigeration gateways are now a named hybrid-warfare concern for the EU.
**Action:** Audit internet exposure of refrigeration controllers/BMS (the Swedish NCSC explicitly flagged unpatched exposed OT); segment and require authentication/monitoring on defrost-mode and setpoint changes.
Source: BleepingComputer, "Hackers disrupt refrigeration systems at European supermarkets," Bill Toulas, Nov 2, 2026 09:28 AM ET — https://www.bleepingcomputer.com/news/security/hackers-disrupt-refrigeration-systems-at-european-supermarkets/
Source: Al Jazeera/Reuters, "Suspected cyberattacks trigger supermarket fridge failures across Europe," Nov 2, 2026 — https://www.aljazeera.com/news/2026/11/2/freezers-in-sweden-belgium-overheat-following-possible-cyberattacks

### Google disrupts 1.3M-device XCPE Android botnet; bans TV-box maker Videostrong for intentional backdoor
**Event date:** November 2, 2026 (Google Threat Intelligence Group disclosure; takedown executed late October).
Google published its account of dismantling XCPE (x.cpe), a backdoor built into the firmware of 1.3M+ Android TV set-top boxes — plus telehealth devices, karaoke machines and digital signage — and says with high confidence Shenzhen-based OEM/ODM Videostrong intentionally developed and operated it, selling access to criminal affiliates who ran ad fraud and enrolled homes into residential-proxy networks. The backdoor runs as a system app, disables Play Protect package verification, reinstalls from a protected partition on reboot, and since August 2026 operators pushed droppers exploiting a privilege-escalation bug to write into the system partition. Google's legal action killed Videostrong's control infrastructure and the C2 domains of a dozen-plus affiliate malware families — severing access to an estimated 10M+ additional compromised devices — and Videostrong is banned from building certified Android devices. TrendAI counted 1,472,554 infected devices across 206 models; Synthient published a 182-model list and says owners cannot remove the implant — dispose of affected boxes.
**Why it matters:** A device manufacturer deliberately implanting its own users flips the trusted-supply-chain assumption — Google's own certification tests were gamed by post-first-boot payload loading. The residential-proxy monetization pipeline also means each box was quietly relaying other criminals' traffic through victims' homes.
**Action:** Enterprises: use Synthient's published IOCs to find MECOOL/Videostrong-built boxes, disconnect and discard them, block the listed C2 domains/IPs, segment IoT away from sensitive networks.
Source: Google Cloud Blog (Google Threat Intelligence Group), "Shutting the Door on the XCPE Botnet," Nov 2, 2026 — https://cloud.google.com/blog/topics/threat-intelligence/shutting-the-door-on-the-xcpe-botnet
Source: The Hacker News, "Google Disrupts 1.3 Million-Device Android Botnet, Says TV Box Maker Sold Criminal Access," Nov 2, 2026 — https://thehackernews.com/2026/11/google-disrupts-13-million-device.html
Source: Help Net Security, "Google disrupts XCPE botnet, bans the company behind it," Nov 2, 2026 — https://www.helpnetsecurity.com/2026/11/02/xcpe-botnet-android-backdoor/

### BTCPay Server: hot wallets drained on instances ≤2.4.1; project publishes Monday incident report
**Event date:** thefts began Sunday evening UTC; attack vector identified in the maintainer's security-incident report published Monday, November 2.
The maintainer of the self-hosted crypto payment processor urged users to update to 2.4.7 after attackers logged in as administrators on outdated instances and swept hot wallets (Bitcoin and Litecoin). "Early Monday, the maintainer said it had identified an attack vector targeting instances running version 2.4.1 (July 23, 2026) or earlier"; initial guidance Sunday was to shut servers pending, and VPS hosts were asked to take affected instances offline. Founder Nicolas Dorier said attacks did not target public servers, and investigation clues point to flaws fixed in 2.4.2 (released August 7) — including the critical flaw Anthropic's Frontier OSS scanner flagged on October 29 that prompted the emergency 2.4.7 release. No credible reports of drains on 2.4.7; totals remain undisclosed. Severity: Not specified by source (no CVE yet).
**Why it matters:** Self-hosted merchants hold live keys; an admin-login vector on unpatched payment infrastructure is direct monetary theft with no ransomware intermediary. The episode also shows an AI-assisted vendor scanner finding (Frontier OSS) pre-dating in-the-wild abuse by three days.
**Action:** Update to 2.4.7 (Server Settings → Maintenance → Update, or ./btcpay-update.sh for Docker); if on ≤2.4.1, take the instance offline first; report losses to security@btcpayserver.org and local authorities.
Source: BleepingComputer, "BTCPay Server warns of hot wallet crypto thefts on outdated instances," Bill Toulas, Nov 2, 2026 10:03 AM ET (updated 10:23 ET) — https://www.bleepingcomputer.com/news/security/btcpay-server-warns-of-hot-wallet-thefts-targeting-outdated-instances/
Source: Cointelegraph, "BTCPay Server warns of hot wallet thefts running on outdated versions," Nov 2, 2026 — https://cointelegraph.com/news/btcpay-server-hot-wallet-thefts-older-versions

### Revolve breach grows to at least 179,259 notified customers (Update on previously reported — ledger: "missed: Revolve 8-K card data," Oct 29)
**Event date:** notifications filed with four state AGs, counted November 2, 2026 (SecurityWeek 8:35/9:35 AM ET).
The fashion retailer is notifying at least 179,259 people that their names, addresses, payment card numbers and expiry dates were exfiltrated from its e-commerce platform in the September 11 intrusion — up from the 127,200 figure in Friday's Texas AG notice. Letters were filed with the attorneys general of Iowa, Massachusetts, Texas and Vermont; Revolve's SEC filing says no operational disruption and no expected material impact, and it is not offering free credit monitoring. Attribution and method remain undisclosed.
**Why it matters:** The consolidated count nearly doubles the initial footprint and shows card-on-file retention as the core exposure; no-credit-monitoring is a growing post-breach cost signal for consumers.
**Action:** Card-holders: review statements for fraud; merchants: audit stored-PIN/card-on-file scope.
Source: SecurityWeek, "Revolve Data Breach Impacts at Least 180,000 People," Ionut Arghire, Nov 2, 2026 9:35 AM ET — https://www.securityweek.com/revolve-data-breach-impacts-at-least-180000-people/
Source: The Paypers, "Revolve reports payment card data breach," Nov 2, 2026 — https://thepaypers.com/fraud-and-fincrime/news/revolve-discloses-payment-card-data-breach-in-sec-filing

### Fortinet: Bitter/TA397 runs three PapRAT infection chains in South Asia — one with no malware binary
**Event date:** November 2, 2026 (FortiGuard Labs research published Monday; Dark Reading 9:00 AM ET).
FortiGuard Labs (Cara Lin) detailed three distinct infection chains attributed with high confidence to the India-linked espionage group Bitter (TA397), active since 2013, targeting South Asian governments/energy/engineering with telemetry also mapping Southeast Asia and South Korea. Chain one is living-off-the-land: an LNK file abuses built-in ftp.exe to run scripts and create scheduled tasks whose curl-pulled commands change server-side — no executable ever lands. Chains two and three deliver PapRAT via MSI installers (a VHDX-embedded decoy spreadsheet; an internet shortcut in a RAR); chain three also drops WmRAT. PapRAT was rebuilt from C++ in Rust with HTTPS (up from plaintext HTTP) and its command set expanded from 8 to 11 (added PowerShell execution, sleep, clean exit). Lures included a Belt and Road Summit letter and a 2026 gala invitation.
**Why it matters:** A binary-less LOTL chain blinds signature-based controls entirely, and Rust re-platforming signals sustained operator investment — detection must move to behavior: scripting engines, scheduled tasks and installer activity, not just RAT hashes.
**Action:** Monitor ftp.exe/curl child-process spawning and new scheduled tasks; restrict native scripting engines and standing admin rights; treat unexpected LNK/MSI/VHDX/RAR artifacts as hostile.
Source: Dark Reading, "PapRAT Cyber Espionage Campaign Leaves a 'Bitter' Taste in South Asia," Elizabeth Montalbano, Nov 2, 2026 9:00 AM ET — https://www.darkreading.com/cyberattacks-data-breaches/paprat-cyberespionage-campaign-bitter-south-asia
Source: Infosecurity Magazine, "Bitter Deploys PapRAT in Asian Cyber-Espionage Campaign," Nov 2, 2026 — https://www.infosecurity-magazine.com/news/paprat-bitter-cyberespionage/

### Ransomware leak sites, Monday 07:00–12:00 ET adds
**Event date:** listings posted Nov 2, ~12:00–17:00 UTC (operator claims, unverified).
New in-window postings: **Holland & Knight** (major US law firm) claimed by Redact; **Mitsubishi Motors Ukraine** and European Motorcars (The Gentlemen); MARDEC Berhad, Harry's International, Hydrogel Vision Corp, Raffles United (Orova); deom.be (SafePay); Princeton Management, Usnext, Howells Motor Freight (akira); Astino Berhad (qilin); two new Everstake victims. ransomnews counts 40 listings in the trailing 24h (The Gentlemen 23), seven-day average 36.9/day.
**Why it matters:** Redact's claim against a top-20 US law firm extends the legal-sector targeting seen last week (Kirkland & Ellis, Morrison Foerster); The Gentlemen's volume spike continues to lead 2026 posting rates.
Source: Ransomnews Ransomtracker, "Ransomware attacks today: 40 victims listed," as of Nov 2, 2026 16:50 UTC — https://ransomnews.com/ransomware-attacks-today/
Source: Ransomware.live summary, Nov 2, 2026 — https://www.ransomware.live/summary/

---

## Defensive tools and advisories

No fully verified in-window releases this cycle — see Coverage notes.

---

## Just missed the window
- **Apple's new mercenary-spyware notification wave** — notifications began Friday **October 30** to targeted users in 99 countries (per Costin Raiu and Apple), including a Turkey-based reporter and a Lebanese filmmaker; The Record's write-up ran Nov 2 but the wave predates the window.
- **Air-alert app hacks (Ukraine + Russia)** — "unauthorised parties" pushed a fake imminent-nuclear-strike alert to Kyiv users ~23:00 local Sunday (Nov 1), and Russia's Emercom app was hacked early Monday to blast a fake "nuclear warheads on P1-SUN" warning to 33 regions; an unverified Telegram channel says group Hitcore did it. Events occurred pre-window; reporting landed Monday.
- **Rosstat breach confirmed** — Belarusian Cyber Partisans + Silent Crow say they spent months inside Russia's statistics agency before destroying servers overnight Saturday (Nov 1), claiming 23–25 TB including military-casualty data; Rosstat confirmed an attack via TASS on Sunday while disputing the data-theft claims.
- **LXD/MicroCloud CVE-2026-112816 (CVSS 10.0)** — Canonical's mutual-TLS flaw lets an attacker impersonate a trusted client (root on the host if the account is privileged); fixed releases out, but The Hacker News' write-up published Nov 1, pre-window.

---

## Coverage notes
- **Verified items this run: 8** (1 vulnerability update, 5 attack/campaign items — counting the leak-site digest as one — and 1 breach update), each from fully-fetched pages dated inside the 07:00–12:00 ET window; leads read in full. 4 additional "just missed" footnotes (first-time footnotes, none repeated).
- **Ledger dedupe — candidates suppressed as already reported: 10** — Ivanti two-more-zero-days patch story (covered Nov 2 07:20), Synology C3RB3R warning (footnoted Nov 2 07:20), Polymarket $3.9M return / DebugBear DNS-hijack (Nov 2 07:20), FBI TraderTraitor attribution + blocklist (Nov 2 07:20), Chainalysis agentic breach (Nov 2 07:20), Microsoft Teams fake-IT-support → AD CS blog (THN re-cover of the Oct 30 'missed' entry), Langflow IBM bulletin (re-cover of Oct 28 'missed' entry), The Gentlemen ~15-victim weekend batch + N0n #113–117 (Nov 2 07:20), MSU/BYOD claim (Nov 2 07:20), THN Weekly Recap (aggregate, not a story). Repeat 'Just missed' footnotes: 0 (all four above are first-time).
- **Dropped as out-of-window research re-covers (not footnoted — too stale):** DarkBlinders (Dream Research Labs report Oct 8), SysGoRAT/CL-UNK-1211 (Unit 42 report Oct 15), VOLTA infostealer (Talos published Oct 31) — THN re-published all three this morning.
- **Not verifiable in-window, dropped:** Google's $250,000 "Root-in-Cloud" bug-bounty reward (SecurityWeek published ~12:1x ET, past the 12:00 cutoff; announcement time unverifiable), Byoky AI-key wallet (timestamp unverified), Northstar Security $7M stealth exit (PR dated Oct 28).
- **Dead ends (two-strike rule):** BleepingComputer's GitLab article URL never surfaced verbatim through search — cited via fetched rewrites with BC credited as original reporting (Nov 2 07:47 AM ET); Dark Reading PapRAT slug guess 404'd before the real URL was found; SecurityWeek sidebar items (Entra ID exploited flaw, Microsoft AI/cloud patches) had unverifiable dates.
