# Cybersecurity News Briefing — Monday, October 26, 2026 · 12:00 ET edition

**Window:** 2026-10-26 07:00 → 12:00 EDT (previous fire 07:00; new-items-only, ledger-deduped)

## TL;DR
- **Fortinet breaks silence on its silent patches:** CISO Carl Windsor confirmed the critical/high-severity flaws behind last week's FortiOS/FortiManager/FortiAnalyzer/FortiMail/FortiAuthenticator hardening releases were found with frontier-AI testing — and the CVE details stay embargoed until November 10. Patch now, he says, because the diff reveals the bug.
- **PHP2Shell hits the plant floor:** Siemens, Schneider Electric, ABB/B&R, Rockwell, Johnson Controls, Belden, HMS and Wibu published OT impact advisories this morning. Belden's NetModule NRSW is affected in all versions with no patch — the workaround is disabling the web interface.
- **953 online stores, 15 months, one skimmer loader:** Sansec maps a Magento-centric campaign now served from deepveindrilling.com — even fully patched stores are re-infected via rogue admin accounts.
- **uCreator (a.k.a. YingGai):** rentable Android spyware hiding inside tampered Telegram/WeChat copies steals Telegram 2FA passwords and clones WeChat accounts; direct lineage to Doctor Web's Baohuo backdoor.
- **Qilin on a rampage this morning:** 17 new victim postings in the past 24 hours, including Natural Energy Laboratory of Hawaii Authority, Burnaby Schools (SD 41) and Premier Medical Group.

---

## Vulnerabilities and patches

### Update on previously reported (ledger: fortinet-oct22-coordinated-release) — Fortinet says AI found the critical flaws its patches fix; CVEs embargoed until November 10
**Event date:** October 26, 2026 (CISO on the record; hardening releases shipped October 21)
Fortinet CISO Carl Windsor explained last week's quiet "security hardening" releases for FortiOS, FortiManager, FortiAnalyzer (branches 8.0, 7.6, 7.4, 7.2), FortiMail (8.0, 7.6, 7.4 — 7.2 users must move up) and FortiAuthenticator (8.0, 6.6): the fixed vulnerabilities were uncovered with frontier AI models plus Fortinet's own on-prem models, validated by humans, and rated critical and high by Fortinet. Details stay under embargo until November 10 — and Windsor's own argument for patching before then is that "once updates are public, the fixes themselves reveal where changes are," making unpatched devices easier to target. Fortinet's numbers: across 100+ AI-assisted tests spanning 30 product families, roughly 40% of AI findings were real vulnerabilities, 30% false positives, 30% hardening opportunities; the models proved especially good at diffing firmware versions and building PoCs for already-fixed bugs. Fortinet also confirmed none of these products are affected by PHP2Shell. Fixed builds reported by Cybernews include FortiOS 8.0.2, 7.6.8, 7.4.13 and 7.2.14.
**Severity:** Critical/High per Fortinet's published rating; numeric CVSS not specified by source.
**Why it matters:** Fortinet is normalizing "patch now, CVEs later" — a model that helps until someone reverse-engineers the diff. Windsor's admission that AI excels at weaponizing fixed-but-not-yet-patched code is the whole argument for treating these updates as emergency-grade this week, not November 10.
**Action:** Upgrade internet-facing FortiOS/FortiManager/FortiAnalyzer/FortiMail/FortiAuthenticator to the fixed branches before Nov 10; admin-IP restrictions, offline FortiManager and MFA are stopgaps, not substitutes.
Sources:
- Fortinet patches critical FortiOS flaws found with AI help — Help Net Security, Oct 26, 2026 (07:22 ET) — https://www.helpnetsecurity.com/2026/10/26/fortinet-patches-critical-fortios-flaws-found-with-ai-help/
- Fortinet Releases Security Hardening Release for Multiple Products — Fortinet CISO Collective blog, Oct 21, 2026 — https://www.fortinet.com/blog/ciso-collective/fortinet-releases-security-hardening-release-for-multiple-products
- Fortinet says patch now, details later, but experts warn of hacks before Nov 10 disclosure — Cybernews, Oct 26, 2026 — https://cybernews.com/security/fortinet-critical-flaws-patches-november-disclosure/

---

## Attacks and campaigns

### 953 online stores infected with a JavaScript loader in a 15-month Magento-centric campaign (Sansec)
**Event date:** October 26, 2026 (Sansec disclosure; campaign active since July 2025)
Sansec reported a single threat actor has injected e-commerce skimmer/loader code into 953 online stores, most running Magento, across five loader domains (manulfie.click, neswnsssn.click, lanhd6549tdhse.top, additionalver.com, and since October 2026, deepveindrilling.com behind Cloudflare, which only answers requests from scripts on infected stores' own visitors). Entry was often exploited e-commerce flaws — 471 Magento stores were injected in March via PolyShell (CVE-2026-48356), and 100+ more via PolyShell or SessionReaper (CVE-2025-54236) file uploads — but persistence is now through rogue admin accounts and backdoors: one fully patched 2.4.7-p10 store was re-injected an hour after a clean scan. The loader fingerprints visitors so only real shoppers get the payload (earlier versions served crypto-themed scripts, including a Ledger-mimicking site); the current final payload hasn't been captured. Sansec's CSP telemetry logged ~52,000 visitor loads of the older additionalver.com loader alone.
**Severity:** Not specified by source.
**Why it matters:** "Patched but still infected" is the operational lesson — vulnerable entry points (PolyShell/SessionReaper) handed over admin credentials that outlive any patch, and payload gating on real visitors blunts scanner-based detection.
**Action:** Sweep CMS content for the five loader domains, audit for rogue admin accounts and backdoors (e.g., GSocket), clean, rotate all credentials, and confirm current patches.
Sources:
- 953 Online Stores Infected in Long-Lasting Malware Campaign — SecurityWeek, Oct 26, 2026 (08:46 ET) — https://www.securityweek.com/953-online-stores-infected-in-long-lasting-malware-campaign/

### uCreator (YingGai) rentable Android spyware hijacks Telegram 2FA and WeChat accounts via tampered app copies
**Event date:** October 26, 2026 (research disclosure)
Researcher Ovi detailed uCreator, a commercial, rentable Android surveillance platform that hides its code inside working, repackaged copies of Telegram and WeChat distributed outside official app stores — or runs WeChat inside the kit vendor's own container app. It steals Telegram two-factor passwords (account takeover of the messaging app itself) and clones logged-in WeChat accounts. The Telegram component matches "Baohuo," a backdoor Doctor Web documented in October 2025 in modified Telegram X builds on 58,000+ devices — The Hacker News' comparison of the two investigations found matching code namespaces and command names. Where Doctor Web framed Baohuo as subscriber-inflation fraud, Ovi assesses uCreator is run for broader financially motivated cybercrime.
**Severity:** Not specified by source (commercial spyware; targeted distribution, no mass worm).
**Why it matters:** This is Chinese-market surveillance-as-a-service maturing through rebranded lineage: the same code keeps resurfacing under new product names, so defenses keyed to old names (Baohuo) won't catch the new one. Sideloading hygiene and app-integrity checks are the control point.
**Action:** Only install Telegram/WeChat from official stores; treat any third-party container app as hostile; look for unexpected 2FA resets.
Sources:
- uCreator Android Spyware Can Steal Telegram 2FA Passwords and Clone WeChat Accounts — The Hacker News, Oct 26, 2026 (09:54 ET) — https://thehackernews.com/2026/10/ucreator-android-spyware-can-steal.html
- Researchers uncover rentable Android spyware uCreator targeting WeChat and Telegram — Security Affairs, Oct 26, 2026 — https://securityaffairs.com/201764/malware/researchers-uncover-rentable-android-spyware-ucreater-targeting-wechat-and-telegram.html

### Leak-site roundup (Oct 26 morning): Qilin posts 17 victims in 24h; payload, Majinahanashi, Doommageddon, aurora, emperador all add claims
**Event date:** October 26, 2026 (as of 12:00 ET)
ransomware.live counted 29 new victim postings today, headlined by a 17-victim Qilin spree including Natural Energy Laboratory of Hawaii Authority, Burnaby Schools (School District 41, BC), UK-based Premier Medical Group, German building-products group FIRMENGRUPPE APPL, Taubensee Steel & Wire, Smith Feddeler & Smith, Zachary Confections, Power Funding and Mountain Rheumatology. Also new today: payload listed three German industrial firms (Glückauf-Vermessung, Miletic, Edelstahl-Mechanik); Majinahanashi posted Bi-silque; Doommageddon claimed Referans Lab; aurora claimed Ernesto Malvestiti S.p.A.; emperador added TRANSPORTES JIMENEZ. (Whitearmor's LoveSac/J.B. Hunt and DragonForce's Inside Passage EMC were reported in the 07:00 edition and are not repeated.)
**Severity:** N/A (extortion claims as posted; unverified).
**Why it matters:** Qilin is currently the most active affiliate group across the October cohort; US mid-market manufacturers and education remain the densest sectors.
**Action:** No specific action stated by source; monitor for extortion contact if you're in the listed sectors.
Source:
- Ransomware.live — daily activity summary, accessed Oct 26, 2026 (12:00 ET) — https://www.ransomware.live/summary/

---

## Defensive tools and advisories

### Update on previously reported (PHP2Shell, CVE-2026-109618/CVE-2026-109619) — the OT vendor advisory wave lands: Siemens, Schneider, ABB, Johnson Controls patch; Belden says "disable the web interface"
**Event date:** October 26, 2026 (vendor advisories published; PHP2Shell itself first reported Oct 21)
Industrial vendors published PHP2Shell impact advisories this morning. Confirmed affected: Schneider Electric (EcoStruxure Security Expert — patched in 1.48.4.0; ProLeiT LINEoptimize — remediation via support; EcoStruxure Control Engineering — mitigations only), Siemens (Ruggedcom APE1808 running PAN-OS 10.2/11.1/11.2/12.1/12.2 — use PAN-OS workarounds; plus a "not affected" list spanning Teamcenter, Simcenter, Polarion and others), ABB (MES deliveries bundling DokuWiki on PHP; B&R APROL through R 4.4-65 — patches expected Oct 26 and Oct 29), Johnson Controls (Illustra Flex4/Pro4/Gen5/Essentials Gen 4 patched; Visonic PowerManage fixed in 5.0.45), HMS Networks (Netbiter firmware update; Talk2M/Argos cleaned, no IOCs found), and Wibu-Systems (CodeMeter License Central web UI updates). Belden is the sore spot: all NetModule NRSW versions including the latest mainline are affected with no patch — disable the web interface; macmon 7.x is patched, 6.x users must upgrade. Rockwell says nothing confirmed yet; Hitachi Energy still investigating; Moxa unaffected.
**Severity:** PHP2Shell CVEs rated 9.2 CVSS v4 by the PHP security team (vendor rating, previously reported).
**Why it matters:** This is the "second wave" of a mega-flaw: your PHP patch calendar is done, but OT appliances embed PHP in management planes that can't be patched as fast as the engine — and several of these devices sit on plant networks where an RCE is a physical-safety issue, not just a data issue.
**Action:** Cross-check the named product list against your asset inventory today; for Belden NRSW, disable the web interface now; calendar the ABB APROL Oct 26/29 patches.
Sources:
- Industrial Vendors Check Products for PHP2Shell Vulnerabilities — SecurityWeek, Oct 26, 2026 (10:52 ET) — https://www.securityweek.com/industrial-vendors-check-products-for-php2shell-vulnerabilities/

## Also noted
- **Extradition:** A Chinese national was arraigned in California after extradition from the Netherlands over a ~$37M cryptocurrency investment scam targeting 174 US victims (BleepingComputer, Oct 26, 2026, 09:56 ET) — https://x.com/BleepinComputer/status/2114717938980626748
- **AI/model security:** Xint disclosed "compaction smuggling" — a flaw in OpenAI's compaction process that let researchers move reasoning traces between models, potentially cheapening rival-model distillation; OpenAI confirmed and fixed it, paying a $6,000 bounty on Oct 23 (SecurityWeek, Oct 26, 2026, 10:30 ET) — https://www.securityweek.com/stolen-ai-reasoning-could-help-china-build-cheaper-rivals/

## Just missed the window (footnoted once)
- **Genesys confirms customer business data stolen from internal LMS/CRM** — names, phone numbers, business emails, job titles, account data; Genesys Cloud/Engage platforms unaffected; containment announced Oct 23, ScarlettGroup claims it; original disclosure Oct 19 (SecurityWeek wrap-up today, Oct 26 10:40 ET) — https://www.securityweek.com/genesys-hack-exposes-customer-data-from-internal-systems/
- **Wasmtime Cranelift double sandbox escape** — CVE-2026-115889 (all arch, 24-bit field overflow → type confusion) and CVE-2026-115893 (ARM64 br_table long-branch bug, present since 2020); both CVSS 9.9 per the Bytecode Alliance advisories published Oct 19; fixed in 36.0.19/48.0.7/49.0.4/50.0.0; GitHub Dependabot won't alert for the ARM64 one (unreviewed). The Hacker News wrote it up today (11:52 ET) — https://thehackernews.com/2026/10/two-critical-wasmtime-flaws-could-let.html
- **Ukraine's CERT-UA warns of active exploitation of Dahua cameras** via guessed Easy4IP serial numbers, weak/default passwords and old auth-bypass CVEs (CVE-2021-33044/33045); The Record write-up today (Oct 26), CERT-UA notice was late last week — https://therecord.media/ukraine-cert-ua-warning-chinese-dahua-cameras-hacked
- **Dutch police arrested two** over an AI-voice "fake bank helpdesk" NFC-relay fraud that drained ~€380,000 from 170 victims (police release ~Oct 22; SecurityWeek coverage today) — https://www.securityweek.com/dutch-police-arrest-two-over-nfc-relay-attacks-that-drained-400000/
- **AGENTS.md Downloads-folder hijack** — Forever Security's case study (disclosed Sun Oct 25) of a planted instruction file trying to get AI coding agents to install a known-malicious npm package; re-covered across outlets this morning.

## Coverage notes
- **Verified this run:** 7 items (5 listed + 2 also-noted), each from a fetched, in-window page; 3 lead stories read in full (Fortinet/HNS, SecurityWeek industrial-vendor + Sansec). Plus 5 first-time "Just missed" footnotes.
- **Ledger dedupe — 12 candidates suppressed as already reported:** Microsoft emergency .NET/Broccoli2Shell patches (BC 11:38 re-cover of the Oct-25 advisory reported at 07:00); PHP2Shell first-confirmed-ITW exploitation (BC 05:19 + SW 09:30 re-covers); Kali Linux 2026.3 (BC 06:26 + HNS); Tailworm Tailscale flaws (THN re-cover); Huntress M365 token-spray (THN 08:35 re-cover of a `missed:` item); Wiz Gheist (THN 08:09 re-cover); DPRK PHP2Shell scanning (reported 07:00); Arista "third VCO zero-day" (Oct 22 ledger + KEV); THN weekly recap items (OpenAI no-internet RL, AGENTS.md); SW "Mirai Potassium" trending item (Oct 23 ledger); NIST/CISA/NSA agent-IR guide op-ed (BC 10:00 — ledger `missed:` item); weekend leak adds resurfacing in today's ransomware.live totals (Whitearmor LoveSac/JB Hunt, Inside Passage).
- **Dead ends / limitations:** The Record's RSS feed 404s (homepage used instead); one SecurityWeek slug guess 404'd (Dutch NFC) — resolved via exact-headline search, per two-strike rule no further attempts; CISA published no KEV additions or alerts in the window (Monday); no new BleepingComputer/THN breach scoops surfaced between 07:00–12:00 ET beyond those listed.
