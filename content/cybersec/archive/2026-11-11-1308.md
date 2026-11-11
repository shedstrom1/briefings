# Cybersecurity News Briefing — Wednesday, November 11, 2026 (12:00 ET edition)

**Window: Nov 11, 2026 07:20 → 12:20 EST** (previous fire → this fire). New items only; ledger-deduped against all prior editions.

## TL;DR

- **Node.js ships its November security release** — v26.13.1 / v24.22.3 / v22.23.5 published this morning (11:44 EST) fixing three HIGH flaws, including an HTTPS re-entrancy bug (CVE-2026-123864) that lets an unauthenticated client read other connections' heap memory, TLS private keys included.
- **Zoom patches a critical unauthenticated privilege-escalation bug** in Workplace for Windows — CVE-2026-108186, CVSS 9.6, fixed in 7.1.8.
- **Arctic Wolf: attackers are weaponizing AntiFox**, a Chinese AI agentic pentesting fork of ARTEX hosted on Alibaba Cloud, to plant webshells and harvest production AWS secrets.
- **Poland takes its ePUAP e-government platform offline** overnight as a precaution after detecting and stopping a cyberattack; no restoration timeline.
- **Update on Maersk (reported 07:20 today):** WhiteArmor ransomware is demanding $5 million for ~100 GB claimed from the legacy LF Logistics/OCF platform.

---

## Vulnerabilities and patches

**Node.js November security release: three HIGH flaws fixed — including cross-connection heap memory disclosure** — *event date: Nov 11 (releases published 16:44 UTC / 11:44 EST; pre-alert Nov 4)*
The Node.js project released v26.13.1, v24.22.3 and v22.23.5 today addressing three HIGH-severity issues. CVE-2026-123863 is a reachable assertion in HTTP/2 session teardown that lets any unauthenticated client abort the server (a variant of CVE-2024-27983 the earlier fix missed; affects 24.x/26.x). CVE-2026-123864 is the dangerous one: a zero-length TLS write issued from inside the HTTP parser's `on_body` callback re-enters `llhttp_execute()`, causing a use-after-free and out-of-bounds heap read — the server then copies freed or unrelated heap memory into the request-body handling, exposing other connections' data including TLS private keys (22.x/24.x/26.x). CVE-2026-123865 leaves HTTP/2 sessions uncleaned when a protocol error fires during connection shutdown. The release also updates llhttp, undici and sqlite dependencies. Notably, CVE-2026-123863 was reported by Chris Rohlf "in collaboration with Claude and Anthropic Research" — the latest in a string of AI-assisted findings in core runtimes.
*Why it matters:* The HTTPS leak is an unauthenticated, remote memory-disclosure primitive in a runtime that fronts APIs everywhere; handlers that call `res.flushHeaders()` before ending a request are the exploitable shape. EOL branches are assumed affected.
*Action:* Upgrade to 26.13.1 / 24.22.3 / 22.23.5 now; audit handlers that flush headers or echo request bodies; kill EOL branches.
Source: Node.js Project, "Wednesday, November 11, 2026 Security Releases" (Nov 11) — https://nodejs.org/en/blog/vulnerability/november-2026-security-releases
Source: GitHub nodejs/node releases — v26.13.1/v24.22.3/v22.23.5 published 2026-11-11T16:44Z — https://github.com/nodejs/node/releases

**Zoom fixes critical unauthenticated privilege escalation in Workplace for Windows** — *event date: Nov 11 (vendor advisory; THN 11:12 EST)*
Zoom released updates for two flaws: CVE-2026-108186 (CVSS 9.6), an improper input validation bug letting an unauthenticated remote attacker escalate privileges (affects 7.1.0 up to 7.1.8), and CVE-2026-108187 (CVSS 8.1), an origin-validation flaw with the same unauthenticated privilege-escalation outcome (before 7.1.5). Zoom says neither is exploited in the wild. This echoes CVE-2026-53412 (CVSS 9.8), patched in July, which allowed unauthenticated account takeover.
*Why it matters:* CVSS 9.6 unauthenticated Privesc on a near-universal enterprise desktop app is exactly the bug class that gets weaponized fast; the July precedent shows Zoom's Windows surface keeps yielding criticals.
*Action:* Move Zoom Workplace for Windows to 7.1.8 or later.
Source: The Hacker News, Ravie Lakshmanan, Nov 11, 2026 — https://thehackernews.com/2026/11/critical-zoom-workplace-flaw-could.html
Source: SecurityWeek, Ionut Arghire, Nov 11, 2026 8:58 AM ET — https://www.securityweek.com/asus-zoom-release-security-patches/

**Also noted (same vendor cycle):** Asus published an advisory covering four high/medium router-firmware flaws, some exploitable for root command execution or memory overwrite/crash (SecurityWeek, Nov 11, 8:58 AM ET — same URL as above). Cisco also updated advisories today adding Node2Shell impact detail, an ISE authorization-bypass update, and Meraki fix availability for seven critical/high CVEs.

---

## Attacks and campaigns

**AntiFox: Chinese AI-agent pentesting platform used to steal AWS production secrets** — *event date: Nov 11 (Arctic Wolf Labs disclosure)*
Arctic Wolf Labs observed intrusions where self-managed instances of **AntiFox** — an open-source, agentic AI penetration-testing platform (a fork of ARTEX 0.1.1 with a Chinese-language interface, now v2.0 on GitHub) — were hosted on **Alibaba Cloud** and tied to confirmed malicious activity against internet-facing web apps and AWS environments. In one case attackers dropped an ASPX webshell on an exposed IIS server, pulled configuration and database connection strings, then used valid IAM users and assumed EC2 roles to fire hundreds of AWS API requests in ~30 minutes, succeeding in obtaining production secrets; in another they read, copied and deleted S3 objects using a valid AWS identity. AntiFox's publishers ban live-system use but ship no technical enforcement. It follows CrowdStrike's VAPOR PANDA/ARTEX and CERT-In's Crafty Falcon findings — the same agentic-toolkit lineage.
*Why it matters:* Agentic pentest frameworks compress the exploit→cloud-credential-farming loop into minutes, and cloud hosting of the operator's console makes attribution harder. Correlating IIS webshell activity with bursts of denied/successful AWS API calls is the hunt.
*Action:* Hunt AntiFox interfaces/IOCs per Arctic Wolf guidance; rotate AWS keys exposed via app files; revoke sessions; alert on API-call denial bursts.
Source: BleepingComputer, Bill Toulas, Nov 11, 2026 10:12 AM EST — https://www.bleepingcomputer.com/news/security/hackers-use-antifox-ai-agents-in-aws-credential-theft-attacks/
Source: The Hacker News, Nov 11, 2026 (10:34 AM EST) — https://thehackernews.com/2026/11/antifox-ai-pentesting-platform-linked.html

**Poland shuts ePUAP e-government platform after detected cyberattack** — *event date: Nov 11 (disconnected overnight into Wednesday; minister statement Tue evening)*
Poland took ePUAP — the nationwide platform for filing official documents and transacting with agencies — offline as a precaution after Digital Affairs Minister Krzysztof Gawkowski said the attack on the state system "was detected and stopped." ePUAP electronic mailboxes remain unavailable to citizens and institutions, and the Trusted Profile digital-ID/signing service may be affected; the ministry gave no restoration timeline, no attack method, no attribution, and no confirmation of data theft. The Central IT Center's core infrastructure was said to remain secure. Context: Poland's MyDr healthcare-software breach potentially touched ~19M records' worth of data across ~12,000 healthcare organizations in September.
*Why it matters:* A precautionary shutdown of national e-government infrastructure is a big operational blast radius even with "no breach" claims — expect disruption of filings, e-signatures and mailbox-based official notices; identity-service spillover (Trusted Profile) is the tell to watch.
Source: The Record (Daryna Antoniuk), Nov 11, 2026 — https://therecord.media/poland-shuts-down-government-services-platform-cyberattack
Corroboration: Ministry spokesperson Marek Gieorgica to PAP; Wirtualnemedia.pl; MLQ.ai roundup (Nov 11).

**UPDATE on Maersk breach (reported 07:20 today):** WhiteArmor ransomware publicly demands $5 million from Maersk, giving the shipping giant 30 days to pay in bitcoin for ~100 GB it claims from the OCF/LF Logistics contract-logistics platform (acquired from Li & Fung in 2022). The gang's inventory claims include ~30,000 driver records, plaintext passwords, 33 SUPER_ADMIN accounts, signed delivery receipts, and 27,842 mobile-device logs with phone numbers, emails and IMEIs. Maersk's statement issued ~11:24 AM EST maintains the incident is isolated to one local legacy system inherited in an acquisition, that "no sensitive data has been disclosed" per current evidence, with no operational impact and a warning to treat post-incident communications as suspect.
Source: BleepingComputer, Bill Toulas, Nov 11, 2026 11:24 AM EST — https://www.bleepingcomputer.com/news/security/maersk-confirms-data-breach-as-whitearmor-ransomware-demands-5-million/

**Also noted — Qlik Sense targeted again (Cynet "ZeroQlik Returns"):** Blocked PowerShell reverse-shell attempts hit several EU Qlik Sense Enterprise servers in October within a three-minute window, executed under scheduler.exe — echoing the 2023 CACTUS campaign. The reverse shell (45.115.26[.]23:4455) maps to a Go backdoor on :4456, OpenSSH-based "WinUpdCheck" persistence, and a staging directory (:8012) holding an ActiveMQ CVE-2023-46604 exploit and modified Veeam CVE-2024-40711 toolchain — pre-positioning for adjacent infrastructure. Exploited vector still unconfirmed. Source: The Hacker News, Nov 11 (9:01 AM EST) — https://thehackernews.com/2026/11/hackers-target-qlik-sense-servers-with.html ; primary research: Cynet — https://www.cynet.com/security-foundations/attack-techniques/exploiting-qlik-sense-servers/

**Also noted — leak-site tape (verified in-window listings):** The Gentlemen listed **Esri South Africa** (ArcGIS distributor for southern Africa) ~08:50 EST (Ransomnews Ransomtracker, last-24h index; ransomware.live). N0n's previously-anonymized "6.3M gaming ad identifiers" listing (first seen this morning's edition) is now **named as DraftKings** (ransomware.live N0n page, discovered Nov 11; ervik.as tracker).

---

## Defensive tools and advisories

**Pall Mall Process adopts first global guidelines for commercial hacking vendors** — *event date: Nov 11 (adopted Wednesday at the initiative's third conference)*
Fourteen governments — led by Britain and France, with the US, Germany, Italy and Japan among signatories — adopted the first industry guidelines for vendors of commercial cyber-intrusion capabilities (spyware). The non-binding text calls on companies that develop, sell or finance CCICs to vet customers, build technical safeguards against misuse, monitor and report abuse, halt sales/support when serious misuse is a credible risk, and "consider" remedies for abuse victims; enforcement is left to buyer governments and export licensing. Of 43 countries at the conference, fewer than half endorsed. The French Foreign Ministry framed it as "the first global standard of responsible conduct" in a market where "any actor can now buy 'off the shelf' the ability to silently penetrate a phone."
*Why it matters:* Voluntary, penalty-free — but it creates a named compliance bar that procurement, export controls and incident-response scrutiny will reference, especially for firms targeting journalists/activists. Check any CCIC vendors in your threat model against it.
Source: The Record (Alexander Martin), Nov 11, 2026 — https://therecord.media/britain-france-set-out-guidelines-for-commercial-hacking-firms
Source: GOV.UK, "The Pall Mall Process Guidelines for Industry" — https://www.gov.uk/government/publications/the-pall-mall-process-guidelines-for-industry

---

## Just missed the window (footnotes)

- **US DOJ unsealed Tuesday (Nov 10)** an indictment accusing three men — Mohammad Baqer al-Saadi (Kata'ib Hezbollah commander, already jailed in Brooklyn), Mohajeri and Al-Hassani — of directing IRGC-tasked attacks on London's Jewish community and the RAF Fairford plot; prosecutors say al-Saadi ran the network over FaceTime from a US prison, including ordering a synagogue arson recorded for propaganda. (The Record, Nov 11 — https://therecord.media/iran-backed-network-directed-london-attacks-over-facetime ; Independent, Nov 11)
- **BYOD ransomware listed Toyota (Nov 10)** on its leak site claiming customer PII (names, emails, addresses, phones, birthdates) from Japanese "kiosk devices"; unverified, Toyota has not confirmed. (Cybersecurity News Nov 10; CTIWatch listing timeline Nov 10; WorldCyberNews)
- **INC Ransom's overnight mega-batch (Nov 10 evening)**: 47 victims in one sweep — American Council on Education, Turkish contractor Kolin, travel retailer Avolta, Shenwan Hongyuan (HK), law firms Richardson Pullen & Buck and Pomer & Boccia, MSPs Onmion/ZIRNET/ELITDATA (client-list dumps), Somervell County TX; its jnattorney claim (3 TB, 200,000 passports) was partly covered this morning. (Ransomnews Ransomtracker last-24h index as of 16:50 UTC)
- **Citrix + Ivanti batch advisories (Tuesday Nov 10)**: Ivanti fixed 13 flaws incl. two criticals — EPM CVE-2026-121653 (CVSS 9.8, unauthenticated SQL injection → RCE) and a Cloud Services Application critical — plus a Sentry RCE; Citrix shipped six XenServer fixes incl. CVE-2026-102705 (host compromise via Pool Secret inference) and AMD CPU mitigations. (SecurityWeek, Nov 11 — https://www.securityweek.com/citrix-ivanti-patch-critical-and-high-severity-vulnerabilities/)

## Coverage notes

- **Verified count: 10 qualifying in-window items** (6 lead stories + 4 also-noted lines), all fetched and page-timestamped inside 07:20–12:20 EST; plus 4 "just missed the window" footnotes. Unit: one entry per underlying event.
- **Ledger dedupe: ~16 candidates suppressed as already reported** (incl. repeat re-coverage of eMudhra revocations, Grok 35k exposure (already footnoted — not re-listed), N-able hotfix, BroadWorks/Openwave KEV, Patch Tuesday/Outlook, Chrome zero-day, Xen Orchestra, Fortinet ~100 CVEs, Railway post-mortem, iVerify DarkSword report, Turla Cereal, Microsoft sleeper-agent research, French blood bank (Nov 4, previously footnoted)). One stale SecurityWeek "Trending" item (Entra ID exploited bug) traced to August 2026 and dropped.
- Thin holiday-morning window (Veterans Day): no new CISA KEV entries found in-window (last batch Nov 10, already reported); BleepingComputer's 07:32 N-able article is coverage of yesterday's already-reported hotfix.
- Community lanes walked: HN Algolia river (only pre-alert coverage of Node.js release) and Reddit r/netsec + r/cybersecurity new-lists (surfaced a new "Electron apps still trust compromised eMudhra certs / unsigned auto-updates" research post — publication time not verifiable inside the window, listed here as a watch lead only).
- Dead ends (two-strike rule): The Record ePUAP/FaceTime slugs 404'd on first-guess URLs (resolved via exact-headline search on next attempt); Help Net Security EU IT-provider patching survey (Nov 11) 404'd on slug guess — logged, not chased.
