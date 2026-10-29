# Cybersecurity News Briefing — Thursday, October 29, 2026

**Window:** Wednesday, Oct 28, 2026 17:00 EDT → Thursday, Oct 29, 2026 07:20 EDT (previous fire → now). New items only; ledger-deduped.

## TL;DR

- Hacktivists published the "Icepick" archive of 41,191 DHS staff records via DDoSecrets Wednesday — names, phone numbers, and internal-group memberships that out HSI undercover agents, covert operation names, and Palantir/Cellbrite tool access; Okta, named as the alleged source, denies any breach.
- Cisco patched CVE-2026-76418 (CVSS 8.6), an actively exploited ASA/FTD Remote Access SSL VPN flaw that lets an unauthenticated attacker force firewalls to reload; no IOCs published, ~51,000 ASA SSL VPN devices are internet-exposed.
- Palo Alto Networks confirmed "active exploitation" of PHP2Shell (CVSS 9.2) against internet-exposed PAN-OS management interfaces — the vendor strongly advises restricting the management UI to trusted internal IPs.
- CrowdStrike tied this week's KEV-added VMware ESXi logging flaw (CVE-2026-41709, CVSS 2.7) to China-nexus Warp Panda, used as a zero-day to erase audit trails in US law-firm intrusions.
- Cisco Talos published KeyDrift, a UAT-11473 malware framework that lived inside SD-WAN controllers and harvested site-to-site encryption keys, overlapping Volt Typhoon's Silent Train network.

## Attacks and campaigns

**"Icepick" leak outs 41,000+ DHS workers, including undercover ICE agents** — Event date: Wednesday, Oct 28 (DDoSecrets publication).
A hacktivist group calling itself the Department of Peace handed DDoSecrets and researcher maia arson crimew a dataset of 41,191 indexed DHS personnel and 55,603 internal groups; Help Net Security and the BBC report the records include names, offices, cell numbers, and job assignments, with Customs and Border Protection the largest cohort (21,329) and ICE second (6,598). Internal-group memberships expose HSI-certified undercover agents, previously unknown operation names, use-of-force instructors, and staff with access to digital forensics tooling; enrichments link personnel to Palantir and Cellbrite deployments. The leakers claim the data came from an Okta SSO service used by DHS — Okta says it has "no evidence our data or systems were compromised" — and DHS says it is "aware of a potential breach." Reuters validated six records against credit-bureau and breach data. It is the third US federal data exposure in roughly a month, after the FBI job-portal theft and the DMDC 3M-person notification.
**Why it matters:** doxxing risk for undercover operatives is the acute harm, but the durable damage is the searchable map of who runs DHS operations — the leak names officials overseeing covert programs, leak investigations, and deportation pipelines, and will feed FOIA and OSINT targeting for years. The disputed Okta attribution also keeps supply-chain pressure on SSO vendors.
**Action:** DHS-relevant teams should assume contact details are public and purge internal group names from any linked systems; nothing reader-patchable here.
Source: Hacktivists leak 41,000 DHS staff records, revealing undercover ICE agents — Help Net Security, Oct 29, 2026 — https://www.helpnetsecurity.com/2026/10/29/icepick-dhs-leak-ice-undercover-agents/
Source: Details of thousands of US immigration agents leaked in major hack — BBC News, Oct 29, 2026 — https://www.bbc.com/news/articles/cq8jzkpjr28vo

**Warp Panda used "low-severity" ESXi logging flaw as a zero-day (Update on previously reported: CVE-2026-41709 KEV addition, Oct 28 17:00 edition)** — Event date: Wednesday, Oct 28 (CrowdStrike report + CISA KEV).
The KEV entry for CVE-2026-41709 (CVSS 2.7 — admin actions go unlogged) hardened into a named-actor story Wednesday: CrowdStrike's 2026 Legal Threat Landscape Report says China-nexus WARP PANDA exploited it as a zero-day "as late as early 2026" during intrusions at US entities including law firms, deploying BRICKSTORM, TETANUSSTORM, SHARPSTORM, DUALGAUGE and GUESTCONDUIT in VMware vCenter environments after entering via internet-facing edge devices. SecurityWeek notes two other July-29 Broadcom flaws (CVE-2026-59309 auth bypass, CVE-2026-59310 traversal) were also exploited in the wild — Quirso tied ~360 compromised vCenter servers to the latter.
**Why it matters:** a CVSS 2.7 bug weaponized as a stealth layer is the argument that "exploited" beats "scored" — defenders triage by KEV and observed abuse, not vendor severity.
**Action:** apply the KEV deadline (Nov 11); patch to ESXi-9.1.0.0-25370933, ESXi-9.0.2.0100-25595025, or ESXi80U3j-25429389 or newer, and audit ESXi admin command logging for gaps.
Source: Chinese Hackers Quietly Used Low-Severity ESXi Logging Flaw as Zero-Day — SecurityWeek, Oct 29, 2026 4:01 AM ET — https://www.securityweek.com/chinese-hackers-quietly-used-low-severity-esxi-logging-flaw-as-zero-day/
Source: Inside the CrowdStrike 2026 Legal Threat Landscape Report — CrowdStrike Blog, Oct 28, 2026 — https://www.crowdstrike.com/en-us/blog/crowdstrike-2026-legal-threat-landscape-report/

**KeyDrift: SD-WAN controller malware built to steal encryption keys (Update on previously reported: Volt Typhoon 'Silent Train', Oct 28 12:00 edition)** — Event date: Wednesday, Oct 28 (Cisco Talos disclosure).
Cisco Talos published analysis of KeyDrift, a multi-component framework that UAT-11473 planted on Cisco Catalyst SD-WAN controllers via CVE-2026-20127 (CVSS 10.0 authentication bypass, patched in February). PolyFlame injects into the vdaemon process and hooks recvfrom/DTLS for in-band tasking with no hardcoded C2; LittleFilch hooks SSL_read to siphon data-plane keying material for predefined edge nodes into RSA-encrypted staging files; a packed aaamgrd variant masquerades as a legitimate service. Talos assesses with high confidence that UAT-11473's infrastructure overlaps Volt Typhoon's Silent Train relay network, and says observed targeting spans space/satellite services, defense and aerospace, and policy research; activity is described as historical. Snort SIDs 67387–67416 (v2) and 7:44–7:73 (v3) now cover the artifacts.
**Why it matters:** harvesting site-to-site keys from inside the control plane retroactively decrypts years of inter-site traffic, and the Silent Train overlap gives a China-nexus espionage frame to what Lumen detailed as disposable relay infrastructure.
**Action:** if not already done since February, verify patching for CVE-2026-20127, apply Cisco's Catalyst SD-WAN hardening guide, and hunt per Talos's IOCs — "immediately activate incident response" if post-compromise signs appear.
Source: Inside KeyDrift: Analyzing historical UAT-11473 activity on Cisco SD-WAN — Cisco Talos Blog, Oct 28, 2026 — https://blog.talosintelligence.com/uat-11473-keydrift/
Source: Hackers Exploited Cisco SD-WAN Flaw to Plant Malware Built to Steal Encryption Keys — The Hacker News, Oct 29, 2026 04:24 EDT — https://thehackernews.com/2026/10/hackers-exploited-cisco-sd-wan-flaw-to.html

**Leak-site round-up (overnight)** — Event dates: Wed evening → Thu morning.
Ransomware.live detected ten new leak-site postings in the window: Santiam Hospital & Clinics (Oregon, "major medical data breach"), Lowenstein Sandler and Morrison Foerster (both US national law firms), sign/graphics/print management software vendor, Fortrex (North American food-safety services), a redacted FULL-DATA-TIMER entry, plus early-Thursday MAA Group Consulting Engineers, ClaimTek Systems, XSpring Capital (Thailand), and the Susanne Cerdá group (Germany).
Source: Ransomware.live recent victims feed, fetched Oct 29, 2026 ~07:25 EDT — https://www.ransomware.live/

## Vulnerabilities and patches

**Cisco fixes actively exploited ASA/FTD SSL VPN zero-day (CVE-2026-76418, CVSS 8.6)** — Event date: Wednesday, Oct 28 (advisory + fixed builds); exploitation confirmed "in October 2026."
Cisco released patches for a logic error in Remote Access SSL VPN when secondary authentication is configured on a tunnel group; an unauthenticated remote attacker sending a crafted connection request forces the ASA/FTD device to reload (DoS). Cisco learned of active exploitation from its own PSIRT earlier this month but published no IOCs, attribution, or victimology — the third exploited ASA/FTD-family bug of 2026 after CVE-2026-20349 (August) and the SEG root-RCE CVE-2026-76519. Shadowserver tracks more than 51,000 internet-exposed ASA SSL VPN devices.
**Why it matters:** a crash-only bug on the box that authenticates your remote workforce is denial-of-access leverage and a distraction vector, and the Cisco ASA/FTD family has been the ArcaneDoor/Volt Typhoon favorite — expect a follow-on access story.
**Action:** upgrade to fixed releases; if you can't patch, disable secondary authentication on the DefaultWEBVPNGroup and any configured tunnel-group list (the only mitigation Cisco offers; there is no workaround).
Source: New Cisco firewall zero-day exploited to crash SSL VPN devices — BleepingComputer, Oct 29, 2026 07:10 EDT — https://www.bleepingcomputer.com/news/security/cisco-warns-of-firewall-zero-day-exploited-to-crash-ssl-vpn-devices/
Source: Cisco Security Advisory: Cisco Secure Firewall ASA and FTD Secondary Authentication DoS Vulnerability — Cisco PSIRT, Oct 28, 2026 — https://sec.cloudapps.cisco.com/security/center/content/CiscoSecurityAdvisory/cisco-sa-asaftd-tunnel-dos-s6mgkb75
Source: Cisco Patches Actively Exploited ASA and FTD VPN Flaw That Can Crash Firewalls — The Hacker News, Oct 29, 2026 07:14 EDT — https://thehackernews.com/2026/10/cisco-patches-actively-exploited-asa.html

**PAN-OS confirms active PHP2Shell exploitation of internet-exposed management interfaces (Update on previously reported: PHP2Shell chain, Oct 21 → Oct 28 editions)** — Event date: Wednesday, Oct 28 (advisory update).
Palo Alto Networks updated its advisory to state "active exploitation has been observed targeting the Palo Alto Networks management interface exposed to the internet" for PHP2Shell (CVE-2026-109618/109619, CVSS 9.2) — reaching the HTTPS web UI/XML API is enough; the dataplane is affected only where a management profile is attached. Fixed builds: 12.2.4-h1 already out, with 12.1.x/11.2.x/11.1.x/10.2.x hotfixes shipping Oct 29–30. The vendor ties its Frontier virtual-patch coverage (Threat ID 543004) to rules released Oct 20, 10:21 PM PT — and the advisory lands days after Dream said a suspected China-nexus operator used a private, offsets-tuned PHP2Shell+Fragnesia chain to root 25 firewalls "from a large vendor," three of them in a US government-affiliated environment. THN notes the timing suggests the Dream incident and PAN-OS are the same story; neither party has confirmed.
**Why it matters:** management-interface RCE on the firewall is network ownership, and PAN-OS joins Fortinet as a vendor confirming PHP2Shell in the wild on its own appliance web UI.
**Action:** apply the fixed builds (or route MGT traffic through an inspected DP interface, replace/decrypt inbound management traffic, and enable threat prevention); restrict management access to trusted internal IPs.
Source: Attackers Exploit PHP2Shell Flaws in Internet-Exposed Palo Alto Networks Management Interfaces — The Hacker News, Oct 29, 2026 01:50 EDT — https://thehackernews.com/2026/10/attackers-exploit-php2shell-flaws-in.html

**Attempted CVSS 10.0 GitLab exploit takes down Alpine Linux's own GitLab; full rebuild (CVE-2026-97229)** — Event date: attacks Oct 20–21; disclosed Wednesday, Oct 28.
Alpine Linux disclosed that a freshly registered account fired crafted CI-config regex exploits at gitlab.alpinelinux.org — still on 19.3.4, a week after GitLab's Oct 12 fix — crashing worker processes eight times and getting "partway through the exploit" four times; the attacker even pulled Alpine's container image, apparently to tune the exploit to the exact build. Because upgrading GitLab wasn't sufficient (the fix lives in patches to the bundled Ruby, which Alpine's standard-Ruby image lacked), and logs from the attack day are partially gone, Alpine treated the instance as compromised: it is rebuilt from scratch, all passwords and secret keys rotated, and it stays closed pending testing. Alpine verified its Oct 21 releases, ISOs, Docker base tarballs and all packages built Oct 20–24 against out-of-band records — no supply-chain tampering found.
**Why it matters:** it's the first credible real-world attack on a public GitLab via the Onigmo regex family (the fourth-Wednesday RCE series), and it proves the uncomfortable lesson that for GitLab self-hosted, "patched" means patching Ruby (3.3.13/3.4.12/4.0.9+) too — anything compiling untrusted regex is in scope.
**Action:** self-hosted GitLab on custom Ruby: upgrade Ruby, don't just bump GitLab; anyone who pulled alpinelinux/gitlab:19.3.6 before Oct 27 must re-pull; expect a password reset on gitlab.alpinelinux.org and change any reused password elsewhere.
Source: GitLab security incident: attempted exploitation of CVE-2026-97229 — Alpine Linux, Oct 28, 2026 — https://www.alpinelinux.org/posts/GitLab-security-incident-CVE-2026-97229.html
Source: Attacker Tried to Exploit Alpine Linux's GitLab via CVSS 10.0 Flaw Its Ruby Left Open — The Hacker News, Oct 29, 2026 05:05 EDT — https://thehackernews.com/2026/10/attacker-tried-to-exploit-alpine-linuxs.html

## Defensive tools and advisories

No net-new tool releases in this window beyond what the campaign items carry: Talos shipped Snort coverage for KeyDrift (SIDs above) plus Cisco's Catalyst SD-WAN hardening guide, PAN-OS virtual-patch Threat ID 543004 is live for Frontier customers, and Cisco's ASA/FTD fixed builds are out. The CISA KEV's next Thursday batch was not yet published at fire time.

## Also noted

- (Nothing else cleared verification this window.)

## Just missed the window

- Orange Tsai (DEVCORE) published the full no-memory-corruption Edge sandbox-escape chain that won $175,000 at Pwn2Own Berlin — The Hacker News posted at 12:23 EDT Wednesday, just before the window; all bugs were patched by Microsoft in May.

## Coverage notes

- Verified items this run: 7 (3 vulnerability/patch, 4 attack/campaign incl. the leak round-up), plus 1 one-time "just missed" footnote. All lead stories (Cisco zero-day, DHS Icepick, PAN-OS PHP2Shell, Alpine GitLab, KeyDrift, Warp Panda) were fetched and read in full; second independent sources cited for the Cisco, DHS, KeyDrift, Warp Panda and Alpine items.
- Ledger dedupe: ~24 candidates suppressed as already reported — 14 repeats of reported items (GitHub npm firewall, Discord indexing, Node2Shell KEV, Oracle EBS probing, Silent Train base story, Dream 25-root, Adobe Campaign, Nefilim sentencing, ScubaGear, InfraTrust Pulse, Tailscale fixes, EPICS re-cover, Serengeti III re-cover, Teams IT-support re-cover) and 10 re-covers of items already footnoted once (Virtualine, Sony So-net, EDK II, Chrome 156/Firefox 159, Anthropic exploit chains, Skyw4lker, Aokigahara, GoAhead TLS, Denver/SquareML adjacency) — none re-listed or re-footnoted.
- Dead ends / exclusions: Dark Reading's GhostAction "wave fizzles" analysis carried only an Oct 28 date with no resolvable in-window timestamp, so it was excluded rather than chased (GhostAction was reported Oct 19). The Hacker News and BleepingComputer RSS were healthy; no index was down. Thursday-morning items (CISA KEV batch, Google TAG) had not fired by 07:20 EDT.
