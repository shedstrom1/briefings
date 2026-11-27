# Cybersecurity News Briefing — Friday, November 27, 2026 (Afternoon Edition)

**Window:** 2026-11-27 12:57 EST → 2026-11-27 17:21 EST (new items only; prior fire 12:00, ran 12:57). Times below are ET unless noted.

## TL;DR
- **pCloud confirms a cyberattack on its pCloud Pass password manager** — a "small number" of user email addresses were exposed and login attempts made, triggering confusing "unknown device" alerts that bypassed 2FA and pointed at pCloud's own Luxembourg infrastructure.
- **Boston Scientific discloses a second 2026 breach** — a May voice-phishing attack reached cloud business applications, exposing personal and medical data of roughly 117,789 people (Texas AG portal figure); the company says it is unrelated to August's operational outage.
- **CISA adds three CVEs to its KEV catalog** — two GeoServer flaws and Drupal's CVE-2026-136716. All three are material updates to events this briefing already covered; federal remediation deadlines fall Nov 27–30.
- No genuinely new in-window defensive-tool release was verified. Kenya's reported 740GB eCitizen claim and the Japan MEXT procurement breach could not be confirmed inside the window and are logged as limitations.

## Vulnerabilities and patches

**CISA adds GeoServer pair and Drupal site-takeover flaw to KEV (update on previously reported)** — Event date: 2026-11-27, catalog released 18:03 UTC (13:03 ET).
CISA's KEV catalog version 2026.11.27 (count 1,808) added **CVE-2026-120705** (OSGeo GeoServer XML injection, readable local files / SSRF via WFS GetFeatureById), **CVE-2026-129243** (GeoServer incorrect authorization, service-level role-check bypass), and **CVE-2026-136716** (Drupal Core improper handling of exceptional conditions — the second highly-critical installer site-takeover flaw, a follow-on to Fail2Install CVE-2026-132165). The two GeoServer flaws are part of a five-bug chain to unauthenticated RCE; both were reported here on Nov 25 as probed in the wild, and CVE-2026-136716 was reported Nov 26 as exploited. Severity: **Not specified by source** (the CISA alert assigns no CVSS). BOD 26-04 due dates are Nov 30 for the GeoServer entries and Nov 27 for Drupal.
**Why it matters:** A KEV listing is CISA's highest-priority exploitation signal — it converts previously "ITW-probed" findings into a mandatory remediation clock for federal civilian agencies and a strong prioritization cue for everyone else. That all three were already on our radar shows how fast advisory status can escalate.
**Action:** Apply vendor mitigations per BOD 26-04 and CISA's "Forensics Triage Requirements" — check for compromise before patching, since these flaws may already have been used. For Drupal, verify the site is not in an incomplete/unusual install state, which the UI does not reveal.
Source: CISA, "CISA Adds Three Known Exploited Vulnerabilities to Catalog," Nov 27, 2026 — https://www.cisa.gov/news-events/alerts/2026/11/27/cisa-adds-three-known-exploited-vulnerabilities-catalog
Source: CISA KEV catalog JSON (v2026.11.27, released 2026-11-27T18:03:12Z) — https://www.cisa.gov/sites/default/files/feeds/known_exploited_vulnerabilities.json
Source: InfoSecNexus, "Live Cybersecurity Brief," updated Nov 27, 2026 (IST timestamps) — https://infosecnexus.com/live-cybersecurity-brief/

## Attacks and campaigns

**pCloud confirms attack on pCloud Pass password manager** — Event date: 2026-11-27 (first mainstream report 14:26 ET).
Users worldwide received alerts that an unfamiliar device named "pd" had connected to their pCloud Pass accounts from Luxembourg IPs (45.131.244.7 / 45.131.247.10) that resolve to pCloud's own AS51154 — even when 2FA was enabled, and in some cases to people who never used Pass or to an address tied to a storage account deleted a year ago. The "Open settings" link pointed to the QA host passqa1.pcloud.com, and pCloud's MX records briefly went missing Nov 27. pCloud confirmed "a cyberattack targeting pCloud Pass occurred, which was promptly contained," with a small number of user email addresses exposed and login attempts made; it says cloud storage and zero-knowledge-encrypted vault contents were unaffected. Severity: **Not specified by source**. No count, timeline, root-cause analysis, or statement on whether encrypted vault blobs were copied has been published.
**Why it matters:** It is the third password-manager incident this month (after mSecure and 1Password) and the least transparent. "Client-side encryption protects the plaintext but not a stolen ciphertext backup" — the unanswered question is whether vault blobs left the servers, which would enable offline cracking as in the 2022 LastPass breach.
**Action:** Don't click links in unexpected security alerts; open the app or type pcloud.com directly. Audit active sessions for "pd," revoke anything unrecognized, rotate any short/reused master password, confirm app-based 2FA, and expect targeted phishing against the exposed email addresses.
Source: BleepingComputer, "pCloud confirms password manager hack after suspicious login alerts," Nov 27, 2026 2:26 PM ET — https://www.bleepingcomputer.com/news/security/pcloud-confirms-password-manager-hack-after-suspicious-login-alerts/
Source: WindowsForum, "pCloud Pass Cyberattack Exposes User Emails," Nov 27, 2026 14:40 ET — https://windowsforum.com/news/pcloud-pass-cyberattack-exposes-user-emails-triggers-unusual-login-alerts.454368/
Source: CyberMonit, "pCloud confirms password manager hack," Nov 27, 2026 — https://cybermonit.com/en/news/pcloud-confirms-password-manager-hack-after-suspicious-login-alerts

**Boston Scientific discloses second 2026 breach via vishing** — Event date: 2026-11-27 (notification letters going out; first mainstream report 13:57 ET).
A voice-phishing attack gave attackers access to the medical-device maker's cloud business applications and hosted data between May 7 and 12; the company learned of the unauthorized access May 12 and determined a breach occurred Nov 5. The Texas AG breach portal lists 117,789 affected people; exposed fields include full name, address, SSN, government-issued ID number, medical and health-insurance information, and date of birth. Patient records, remote-monitoring systems, connected devices, and clinical workflow apps were not touched, and there was no operational or product-safety impact. A spokesperson confirmed this is unrelated to the Aug 25 cyberattack that disrupted manufacturing and shipping. Severity: **Not specified by source**. No free credit monitoring is offered.
**Why it matters:** A second breach in one year at a top-20 medical-device manufacturer shows the sector's exposure is not limited to operational disruption — the vishing-to-cloud path reaches regulated personal and health data directly, with SSNs and government IDs in the haul.
**Action:** Affected individuals should use free credit reports and consider fraud alerts and credit freezes; treat any "Boston Scientific support" follow-up as hostile.
Source: BleepingComputer, "New Boston Scientific data breach exposes personal, medical info," Nov 27, 2026 1:57 PM ET — https://www.bleepingcomputer.com/news/security/new-boston-scientific-data-breach-exposes-personal-medical-info/
Source: WindowsForum, "Boston Scientific Vishing Breach Exposes Personal Data," Nov 27, 2026 — https://windowsforum.com/news/boston-scientific-vishing-breach-exposes-personal-data-in-cloud-business-apps.454370/
Source: BreachRecord, "Boston Scientific Corporation Data Breach (November 2026)" — https://breachrecord.com/breach/boston-scientific-2026-11/

## Defensive tools and advisories

No genuinely new, in-window defensive-tool release, open-source utility, or nontrivial advisory was verified in this short afternoon window. The CISA KEV action above is cross-listed under Vulnerabilities and patches rather than counted twice.

## Also noted
- **Mid Michigan Medical Billing Service** — a data-breach claim surfaced via a class-action claim-depot notice at ~16:08 ET; scope and primary filing unverified. Treated as a lead, not a confirmed event.

## Coverage notes
- **Verified items this run: 3** (2 new events + 1 "Update on previously reported"). Counting unit = distinct underlying event/advisory action; the CISA KEV batch is one update even though it covers three CVEs and overrides two earlier entries.
- **Outlets swept (12+):** BleepingComputer, The Hacker News, SecurityWeek, The Record, CISA, Daily Security Review, InfoSecNexus, WindowsForum, Ransomnews, The Perimeter Site, Reuters, breachrecord/Bing index. Community lanes: HN Algolia river (queried; only older/duplicate items); Reddit r/netsec not separately surfaced this run.
- **Dedupe gate:** roughly 24 in-window or near-window candidates were suppressed as already reported in the ledger — including USAF Cloud One, SAP/GitHub source-code theft, NFS2ROOT CVE-2026-99362, WSUS removal in Server 2028, KB5138318 hotpatch, Q-Dir/DesktopOK trojanizing, Swiss AI-attack confirmation, myColorado (~100k), Vinci RAT, WeaveLotus, DIGIT CVE-2026-136706, AFP outage, Japan MEXT, FIFA, Gatwick, Flight Centre, 1Password, Todoist, KEPCO, McDonald's Japan, Nikkei, Microsoft's Chinese phishing-kit seizure, UK DWP outage, and Zodia Custody. The CISA KEV additions are logged as updates to the Nov 25 GeoServer and Nov 26 Drupal entries, not as new items.
- **Notable dead ends / limitations:** Kenya's reported "740GB eCitizen" breach (Biometric Update) and South Africa's Purple Group/EasyEquities incident could not be confirmed inside the window from a fetchable primary page — Purple Group's JSE SENS notice was dated 16:51 SAST (09:51 ET), before the window, and its "unauthorised access" was found Nov 25. Both were killed under the two-strike rule and not listed. Web_search returned intermittent backend failures; Bing used as fallback. No ransomware leak-site roundup with in-window, independently confirmed victims was produced this run.
- **CISA negative check:** no additional CISA alerts beyond the three-CVE KEV action were observed in-window.
