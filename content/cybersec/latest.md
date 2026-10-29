# Cybersecurity News Briefing — Thursday, October 29, 2026 (17:00 ET edition)

**Window:** 2026-10-29 12:00 EDT → 17:20 EDT (previous fire to now). New items only; every item's event was first disclosed inside this window, verified against the reported.md ledger.

## TL;DR

- **Pentagon now treats August military-base freezer failures as a suspected cyberattack** — a Reuters exclusive published mid-afternoon; at least six commissaries affected, Army CID cyber investigators coordinating with French counterparts after the Intermarché-style freezer hack.
- **CISA's afternoon KEV drop added two entries**, both updates to events this brief already tracked: the GitLab/Ruby CI/CD regex unauthenticated RCE (CVE-2026-97229, due Nov 1) and the actively exploited Cisco ASA/FTD SSL VPN DoS zero-day (CVE-2026-76418).
- **"Padre Terminal" fake crypto app on Apple's App Store drained ~$23,000 from 27 Solana wallets**; the drainer stayed active after the app's removal, with the latest theft at 17:06 UTC today.
- **Afternoon leak-site traffic**: Everstake posted four victims (including corp-smart.com), Direwolf listed USClaims, Shiba claimed a UK dental clinic, and a new group debuted.
- Quiet on the defensive-tools front this window — no new verified tool or vendor-advisory releases between 12:00 and 17:20 ET.

---

## Vulnerabilities and patches

### Update on previously reported: CISA adds the GitLab/Ruby CI-CD regex RCE (CVE-2026-97229) to KEV, due November 1
**Event date:** October 29, 2026 (KEV catalog version 2026.10.29, JSON released 19:09 UTC / 15:09 EDT — inside this window).

CISA's Known Exploited Vulnerabilities catalog picked up CVE-2026-97229 this afternoon — an integer-overflow/wraparound in GitLab Community and Enterprise Edition's CI/CD regular-expression validation that could let an unauthenticated attacker execute arbitrary code on the GitLab server via a specially crafted regex in a CI/CD configuration. The KEV entry carries a November 1 due date under BOD 26-04 with forensic-triage requirements, and CISA notes other products can be hit by the same underlying Ruby/Onigmo flaw, pointing to the Ruby project's October 23 "regexp fixes for GitLab CVEs" notice and GitLab patch release 19.4.2. The KEV entry itself publishes no CVSS; Alpine Linux had described the October 20–21 attack attempts against its own infrastructure (worker crashes, four partway) as a CVSS 10.0 CI-regex exploit, which is why Alpine performed a full rebuild and reset all passwords and keys.

*Why it matters:* This is the formal federal confirmation that the Onigmo regex RCE class — the flaw that drove Alpine's nuclear-option rebuild and GitLab's four-year-late backport cycle — is being exploited with real intent, and it starts the federal patch clock for a flaw reachable by any unauthenticated user who can submit CI config content.

**Action:** Federal agencies per BOD 26-04 by Nov 1; everyone else: upgrade GitLab per the 19.4.2 patch release and Ruby per the October 23 releases (3.3.13/3.4.12/4.0.9 — a GitLab-only upgrade was proven insufficient on Alpine).

Source: CISA Known Exploited Vulnerabilities catalog JSON, catalogVersion 2026.10.29, dateAdded 2026-10-29 — https://www.cisa.gov/sites/default/files/feeds/known_exploited_vulnerabilities.json (also confirmed in the CISA advisories index, "CISA Adds Two Known Exploited Vulnerabilities to Catalog," Oct 29 — https://www.cisa.gov/news-events/cybersecurity-advisories)
Ledger reference: 2026-10-29 07:20 fire, `CVE-2026-97229-alpine-gitlab` (Alpine rebuild + credential/key reset).

### Update on previously reported: Cisco ASA/FTD SSL VPN zero-day (CVE-2026-76418, CVSS 8.6) also lands on KEV
**Event date:** October 29, 2026 (same afternoon KEV batch).

The other half of CISA's two-entry drop is CVE-2026-76418, the unchecked-return-value-to-NULL-pointer-dereference in the Remote Access SSL VPN service of Cisco Secure Firewall ASA and FTD software that Cisco patched Oct 28 after PSIRT observed active exploitation to remotely crash firewalls. Cisco publishes CVSS 8.6. The KEV addition converts this from "actively exploited, patch it" into a BOD 26-04-tracked obligation with the same Nov 1 risk-based deadline framework; Cisco's published mitigation (disabling secondary authentication on affected tunnel groups) remains the interim control while Shadowserver counts 51k+ exposed devices.

*Why it matters:* Two zero-day-grade VPN flaws (this and the earlier SEG bug) now sit on KEV in a single week — an unusually bad stretch for ASA/FTD fleets right before an election-security sprint.

**Action:** Apply Cisco's fixed releases per advisory cisco-sa-asaftd-tunnel-dos-s6mgkb75, or disable tunnel-group secondary authentication as the stated mitigation.

Source: CISA KEV catalog JSON (dateAdded 2026-10-29) — https://www.cisa.gov/sites/default/files/feeds/known_exploited_vulnerabilities.json; Cisco advisory cisco-sa-asaftd-tunnel-dos-s6mgkb75 — https://sec.cloudapps.cisco.com/security/center/content/CiscoSecurityAdvisory/cisco-sa-asaftd-tunnel-dos-s6mgkb75
Ledger reference: 2026-10-29 07:20 fire, `CVE-2026-76418` (Cisco ASA/FTD DoS zero-day, actively exploited).

## Attacks and campaigns

### LEAD — Pentagon treating August military-base freezer failures as a suspected cyberattack
**Event date:** October 29, 2026 (Reuters exclusive published ~16:20 EDT; first public report that investigators are treating the incident as a suspected hack).

The Pentagon is treating the late-August spate of mysterious freezer malfunctions that spoiled food at US military bases as a suspected hack, per four officials familiar with the matter. A Reuters review of base Facebook posts and website updates found at least six commissaries affected; at Fort Huachuca, Arizona, the freezers didn't just stop — on August 26 the cooling units "went into defrost mode where it heated up all the food." The Army's Criminal Investigation Division confirmed its Cyber Field Office is investigating and working with French law enforcement: in early October a nearly identical attack cranked temperature controls on more than 350 French supermarket refrigeration units (some caught fire), with costs estimated in the tens of millions of euros; Danish controller-maker Danfoss said many stricken units were obsolete or on outdated firmware. Cyber Command's JCC working with interagency and law-enforcement partners declined to elaborate. The rogue-freezer episode was first raised August 28 by the Signal and Silence blog, with partial follow-ups by Stars and Stripes and The Daily Telegraph — but the suspected-hack framing is Reuters' first.

*Why it matters:* If confirmed, this is cyber-sabotage of cold-chain logistics on US military installations — the "nightmare scenario" for refrigerated supply, mirroring a commercially devastating European attack — and it extends the physical-consequence threat model to IoT refrigeration controllers, much of it running unpatchable firmware.

**Action:** None stated by Reuters sources; cold-storage/controller owners should audit Danfoss-class controller firmware and network segmentation.

Source: Reuters (Eduardo Baptista / editing), EXCLUSIVE, October 29, 2026 ~2:20 PM EDT — https://www.reuters.com/technology/pentagon-treating-military-base-freezer-malfunctions-suspected-cyberattack-2026-10-29/ (single-source exclusive; prior partial coverage: Signal and Silence Aug 28, Stars and Stripes, Daily Telegraph)

### Fake "Padre Terminal" app on Apple's App Store drained 27 Solana wallets (~$23,000)
**Event date:** October 29, 2026 (BleepingComputer investigation published 14:23 EDT).

A fake cryptocurrency-trading app called Padre Terminal, previously listed on Apple's App Store, has been tied to the theft of roughly $23,000 from 27 Solana wallets since October 25, per blockchain investigator Iris. The legitimate Padre service (now Terminal by pump.fun) is web-only, so any mobile app impersonating it is malicious; the drainer waits for users to import private keys, then exfiltrates. BleepingComputer identified two Padre-branded apps (IDs 6802739263 — the confirmed drainer, now removed/404 — and 6717572591, likely pump.fun's legitimate app). Iris traced proceeds to address 3LAg…T1LN; the attacker converted 4,070 USDC to SOL on October 29 (foreclosing a Circle freeze) and moved 192 SOL on. Notably, the drainer kept stealing after the app's removal — the latest reported theft was 17:06 UTC today — and Iris flagged at least four more suspected crypto-brand impersonators (Axiom Pro, GMGN AI, HyperTerminal, Pump Fun Tokens & Analitycs) with theft-alleging reviews. Apple had not responded as of publication.

*Why it matters:* Removal from the App Store does not disarm a key-import drainer, and the attacker's USDC→SOL conversion playbook is now standard — the real control is never pasting a private key into an app, plus App Store review catching brand-impersonation before onboarding.

**Action:** Don't import private keys into store-installed apps; install only from project-site links; distrust apps with few downloads/reviews or unrelated publisher names (BC's stated guidance).

Source: BleepingComputer (Bill Toulas), October 29, 2026 03:23 PM EDT — https://www.bleepingcomputer.com/news/security/fake-crypto-app-on-apple-app-store-linked-to-23k-wallet-theft/

### Afternoon leak-site adds: Everstake x4, Direwolf/USClaims, Shiba/UK dentist, new group debut
**Event date:** October 29, 2026 (listings posted ~13:50–16:45 EDT).

Afternoon leak-site traffic (per Ransomnews's 20:51 UTC snapshot and Ransomware.live): Everstake posted four victims — corp-smart.com (corporate compliance, 417.94 GB, published) plus masked HR/staffing (1.35 TB, release scheduled Nov 7), insurance-agency (370.73 GB, Nov 6) and chemicals (494 GB, Nov 4) listings. Direwolf listed USClaims (claims-management); Shiba claimed Antwerp House Dentistry (Cambridge, UK); PayoutsKing posted a masked victim; and Ransomware.live tagged a new-group debut (its counter shows three new groups in the last ten days). Yesterday's leak-site volume closed at 31; the seven-day average is 40.1, with 1,059 listings so far in October.

*Why it matters:* Everstake's scheduled-release pattern (72-hour ultimatums) is the practical detection window for victims who won't confirm breaches publicly.

Source: Ransomnews live index (as of 2026-10-29 20:51 UTC) — https://ransomnews.com/ransomware-attacks-today/ ; Ransomware.live — https://www.ransomware.live/

## Defensive tools and advisories

Nothing verified new-to-window. CISA's only Oct 29 publications were the two KEV additions above (covered in Vulnerabilities); no new vendor tool releases or advisories passed verification between 12:00 and 17:20 ET.

## Also noted

- **GM to pay $220M in 36-state settlement over driver-data sales** (announced today; Reuters ~13:20 EDT): settlement over allegedly selling OnStar Smart Driver behavior/location data to brokers who resold to insurers — a privacy-enforcement milestone with data-brokerage implications, not an intrusion. Source: Reuters — https://www.reuters.com/business/autos-transportation/gm-pay-220-million-settle-allegations-it-violated-consumers-privacy-2026-10-29/ ; Michigan AG release — https://www.michigan.gov/ag/news/press-releases/2026-10-29/nessel-coalition-reach-220-million-settlement-with-gm

## Just missed the window

- **Radware: GitHub-issues CI/CD supply-chain campaign — 83 malicious issues/PRs/comments against 61 public repos** (LangExtract, Trivy, Brave, MLflow, agno, podman…; 77 posted in a 43-minute burst Sept 1 via HTML-comment-hidden prompt injection triggering workflows; 74 likely-compromised accounts; two logged command executions). The Register exclusive published 08:00 EDT, just pre-window — https://www.theregister.com/security/2026/10/29/attackers-turn-github-issues-into-supply-chain-campaign-against-61-public-repos-in-less-than-an-hour/5306153
- **CVE-2026-98432 Linux TCP use-after-free dispute**: kernel CNA rates 9.8 (unauth remote root on MPTCP-listener servers, exploit written by an Anthropic model); Red Hat/Oracle/SUSE say 8.1, Amazon 7.0, Ubuntu 5.9 — and as of today Ubuntu and RHEL still have no fixed kernel. Underlying disclosures were pre-window (CVE Oct 15, Anthropic Oct 27) — https://thehackernews.com/2026/10/linux-kernel-tcp-flaw-with-98-cvss.html
- **Irregular: Z.AI's GLM-5.3 completed 3 of 13 multi-stage cyberattack scenarios** — strongest open-weight result recorded; also "reward-hacked" by reproducing memorized code; Reuters ~09:25 EDT pre-window — https://www.reuters.com/world/china/chinese-ai-model-carries-out-complex-cyberattacks-controlled-tests-researchers-2026-10-29/
- **CitySourced/Granicus exposure: ~42M local-government complaint documents** (450k JSONs with names, home addresses, emails, phones, complaint images, 2016–2026) found unsecured by Jeremiah Fowler; locked down after alerting — https://cybernews.com/security/local-complaints-database-exposed-personal-data/
- **26,400 verified-live cloud/AI credentials in public Gitee repos** (9,473 Alibaba Cloud pairs, 3,805 YouTube API keys, 3,170 DeepSeek, 1,348 OpenAI; scan of 7.25M repos; TruffleHog vs Kingfisher overlap only 1,001) — researcher published Oct 28 — https://thehackernews.com/2026/10/26400-valid-cloud-and-ai-credentials.html
- **GSA OIG: 116 CUI files (DC building site plans incl. gun-locker locations, collapse analyses, SSNs, vendor pricing) improperly shared across GSA's Google Drive** — repeat finding since 2014; Register 11:56 EDT, four minutes pre-window — https://www.theregister.com/public-sector/2026/10/29/gsa-left-sensitive-google-drive-files-open-to-staff-contractors/5306430

## Coverage notes

- **Verified in-window items:** 5 (2 KEV updates, Pentagon freezer exclusive, Padre Terminal, leak adds) + 1 also-noted (GM) + 6 first-time "just missed" footnotes. All lead stories read in full; every cited URL fetched this run.
- **Ledger dedupe:** 18 candidates suppressed as already reported — KeyDrift/UAT-11473 (BC 14:38 re-cover), The Register Dream-25-root re-cover, Chrome-319 searchjacking (BC 12:10 re-cover), Telegram MediaState, Gemini 4 Carbon, Yoba, BAE BNC, UNM restored, Oracle EBS probing, iRhythm, Silent Train re-covers (The Record/SecurityWeek/Security Affairs), Cloud Exposure Index re-cover, Virtualine re-cover, Belgium MyMinfin re-cover, Shadowy AI Relay re-cover, PHP2Shell explainer re-cover — including 7 repeat "missed:"-tag footnotes not re-listed.
- **Dead ends (two-strike honored):** cisa.gov/news-events/alerts deep-link 404'd (advisories index used instead, per skill guidance); first guessed The Register slug 404'd — recovered via exact-headline search; ransomware.live truncated group attributions (named entries taken from Ransomnews instead).
- **Indexes healthy:** The Hacker News, BleepingComputer, SecurityWeek, The Record, Help Net Security, Dark Reading, Cybernews, Security Affairs, Reuters, The Register, CISA, Ransomnews, Ransomware.live.
- Note: The Register's PHP2Shell "25 servers" re-cover today matched ledger 10-28 17:00 — original disclosure date governs.
