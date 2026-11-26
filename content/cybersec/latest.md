# Cybersecurity News Briefing — November 26, 2026 (07:20 ET)

**Window:** Wednesday, November 25, 2026, 17:20 EST → Thursday, November 26, 2026, 07:21 EST (overnight sweep; new items only, deduped against the running ledger).

- Drupal disclosed a **second, actively exploited site-takeover flaw** — CVE-2026-136716, rated "Highly critical" — because last week's Fail2Install patch left some install states exposed. Roughly 31,500 internet-facing Drupal sites run vulnerable branches.
- CISA added **10 exploited vulnerabilities** to its KEV catalog, led by five Mitel MiCollab flaws including a CVSS 10.0 unauthenticated command-injection RCE, plus Vates Xen Orchestra and three Danfoss refrigeration-controller bugs.
- Bitdefender exposed **SYS010**, a Meta-ads malvertising campaign that forces malicious extensions onto Chrome, Edge and Firefox so it can read and delete Gmail and Outlook messages and loot Meta Business accounts.
- Socket found **13 malicious NuGet packages** pushed through a fake .NET take-home assessment that sideloads DLLs around Windows' On-Screen Keyboard and runs GitHub-hosted command-and-control.
- hunt.io published three **exposed attacker directories** holding hospital credentials, a 155,568-object Azure storage listing and VPN/portal maps for more than 60 law firms.

## Vulnerabilities and patches

**Drupal discloses a second actively exploited installer flaw — CVE-2026-136716 (disclosed November 25)**

Drupal's security team warned that CVE-2026-132165 ("Fail2Install"), patched on November 18, did not cover every installation state. Sites in unusual or incomplete install states — including live production sites — remained open to unauthenticated site takeover. The new flaw, tracked as CVE-2026-136716 (advisory SA-CORE-2026-019), has been exploited in the wild. Drupal rated it **Highly critical, 21/25 on the CMSS scale; CVSS not assigned by the source**. The admin UI does not reveal whether a site is in the affected state, and already-compromised sites may no longer show it.

**Why it matters:** This is a direct follow-on to the Fail2Install saga this briefing has tracked since November 24 — the first fix was incomplete, so the exposure window extended past the earlier patch. StepSecurity counts about 55,800 internet-facing Drupal sites, roughly 31,500 on branches vulnerable to the earlier installer bug and about 15,000 on end-of-life 8/9 branches with no fix coming.

**Action:** Update to Drupal 11.4.14, 11.3.23 or 10.6.23, then run `update.php` — Drupal says the update hook is required to remediate production sites already in the vulnerable state. Secure `install.php` (without blocking `update.php`), check logs for unauthorized access and unexpected changes to user 1, confirm control of the user 1 email address, and reset that password. Drupal 11.0–11.2 and 10.5 and earlier are EOL with no fix.

Source: "Drupal Installer Patch Left a Gap Attackers Are Already Exploiting," SecurityWeek, November 26, 2026 04:12 ET — https://www.securityweek.com/drupal-installer-patch-left-a-gap-attackers-are-already-exploiting/
Source: "Drupal Warns of New Highly Critical Site Takeover Vulnerability Under Active Exploitation," The Hacker News, November 26, 2026 — https://thehackernews.com/2026/11/drupal-warns-of-new-highly-critical.html
Source: "Drupal Warns of Active Attacks Exploiting New Critical Website Takeover Vulnerability," Cyber Security News, November 26, 2026 — https://cybersecuritynews.com/drupal-website-takeover-flaw/

**Update on previously reported: CISA adds 10 exploited flaws to KEV — Mitel, Xen Orchestra, Danfoss (November 25)**

*Names the prior ledger entries for Juniper CVE-2026-103406, Vates Xen Orchestra (VSA-2026-062) and Danfoss CVE-2026-129713.* CISA's November 25 alert added ten vulnerabilities with evidence of active exploitation. Five are in Mitel MiCollab's Audio, Web and Video (AWV) component: **CVE-2026-133323 (CVSS 10.0)** blind OS command injection, CVE-2026-133303 (9.0) XML injection, CVE-2026-133311 (5.9) weak authentication, CVE-2026-133275 (5.3) unrestricted file upload and CVE-2026-133324 (5.3) fail-open. Also added: Vates Xen Orchestra **CVE-2026-133469 (9.8)** — an empty JSON object is accepted as a login cookie and returns the first live token, giving an admin session — and three Danfoss AK-SM 800A flaws, CVE-2023-25915 (9.9), CVE-2023-25914 (8.8) and CVE-2025-41450 (8.2, the "FromageGate" auth bypass). HPE Juniper Session Smart CVE-2026-103406 (9.8) rounds out the batch; its exploitation was reported in the prior edition.

**Why it matters:** All ten are confirmed in-the-wild, and a public Nuclei exploit exists for the Xen Orchestra bug. The mix — voice collaboration gear, hypervisor management, edge routers and cold-chain controllers — means a single unpatched device in each layer can yield rapid takeover. CISA set a three-day remediation deadline under BOD 26-04 (November 28) and calls for log review on the top entries.

**Action:** Upgrade Mitel MiCollab to 10.3.0.18 and apply KB000129195 (or move to a newer build); if you cannot patch immediately, block web access to the AWV component. Update Xen Orchestra to 5.213.1 or 5.211.2, patch Juniper Session Smart to 7.0.5-14-lts / 7.1.7-15-sts / 7.2.3-8r2, and move Danfoss AK-SM 800A firmware to 4.2 or later and off the public internet. Review logs on every exposed host.

Source: "CISA Adds 10 Known Exploited Vulnerabilities to Catalog," CISA, November 25, 2026 — https://www.cisa.gov/news-events/alerts/2026/11/25/cisa-adds-10-known-exploited-vulnerabilities-catalog
Source: "Hackers Exploit 10 Vulnerabilities Across Mitel, Juniper, Vates, and Danfoss Products," The Hacker News, November 26, 2026 — https://thehackernews.com/
Source: "CISA Adds 10 Exploited Flaws to KEV: Mitel MiCollab, Xen Orchestra," SecurityOnline, November 26, 2026 — https://securityonline.info/cisa-kev-catalog-micollab-juniper-danfoss/

## Attacks and campaigns

**SYS010: fake wallpaper ads force browser extensions that read and delete your Gmail and Outlook (November 26)**

Bitdefender Labs disclosed SYS010, a major new version of the SYS01 infostealer (the name is binary for "version 2"). Ads on Meta promote free animated wallpaper apps that work as advertised while installing malware in the background. An `eclient.php` script enrolls Chrome into an attacker-controlled Google admin console, force-installs a Firefox add-on via an `ExtensionSettings` policy, or enrolls the Windows device in an attacker-controlled MDM server for Edge — using UAC prompts repeated every three seconds up to 99 times until the user approves. The extensions steal session cookies and Meta Business data; the Chrome version can read recent Gmail and Outlook messages and permanently delete selected ones, potentially erasing security alerts. Severity: **Not specified by source** (malware, not a CVE).

**Why it matters:** Abusing legitimate enterprise browser/device management makes the add-ons hard for victims to disable or remove, and inbox access undermines account recovery. Bitdefender saw requests from the Philippines, Thailand, Bangladesh, Poland, Mexico, Algeria, Belgium, Mongolia and the US; one ad reached 6,275 EU users. It did not observe activity from Vietnam, consistent with — but not proof of — Vietnamese-speaking operators.

**Action:** Avoid downloading software from social-media ads and refuse administrator prompts with an unclear purpose. An unexpected "managed by your organization" message is a red flag; check with IT on managed devices and investigate on personal ones. Businesses should review who can access Meta accounts, remove unneeded permissions, and check for unfamiliar ads, payment changes or spending.

Source: "SYS010 Malware Targets Meta Business Accounts," Bitdefender Labs, November 26, 2026 — https://www.bitdefender.com/en-us/blog/labs/sys010-reloaded-malvertising-campaign-targeting-meta-business-accounts
Source: "SYS010 malware abuses browser management to spy on Gmail, Outlook," BleepingComputer, November 26, 2026 06:50 ET — https://www.bleepingcomputer.com/news/security/sys010-malware-abuses-browser-management-to-spy-on-gmail-outlook/

**13 malicious NuGet packages turn a fake .NET take-home test into a DLL-sideloading trap (November 26)**

Socket found 13 malicious NuGet packages, mostly posing as WinUI 3 and WPF button controls, distributed through a GitHub repository (`stevenleedig-ux/WinUI3_Home_Assessment`) hosting a three-to-four-hour coding assessment. Building the project runs an MSBuild targets file that drops `OskSupport.dll` into `%LOCALAPPDATA%\osk\`, copies `C:\Windows\System32\osk.exe` alongside it, and sideloads the DLL when a FileButton control is clicked. Other packages download and run executables from GitHub. The same persona advertised an $8,500–10,000 WPF developer job on LaborX on November 7 and published a "Crypto Wallet Analyzer" release on November 13 whose bundled native DLL phones a separate C2 repo. Severity: **Not specified by source**.

**Why it matters:** The campaign buries its loader in a plausible take-home assessment that even encourages candidates to use GitHub Copilot, ChatGPT or Claude — a supply-chain and social-engineering hybrid aimed squarely at Windows .NET developers. Socket found no code overlap with North Korea's Contagious Interview or other named campaigns, so it is unattributed, though the recruitment-theme tradecraft resembles DPRK and Iranian operations. All 13 packages have been removed from NuGet.

**Action:** Treat unsolicited take-home projects as untrusted code — build them in an isolated, non-networked environment and inspect `.csproj` and MSBuild targets files before building. Audit developer machines for the `%LOCALAPPDATA%\osk\` artifact and unexpected `osk.exe` copies.

Source: "13 Malicious NuGet Packages Turn Fake .NET Job Assessments Into Malware Delivery Traps," The Hacker News, November 26, 2026 — https://thehackernews.com/2026/11/13-malicious-nuget-packages-turn-fake.html

**hunt.io: exposed attacker directories reveal hospital credentials, 155,568 cloud objects and law-firm VPN maps (investigation November 25)**

hunt.io analyzed three exposed web directories containing 2,115 files (1,981 distinct hashes) of scan results, attack logs and malware. One held 8,460 hospital email addresses (6,929 on the primary domain, 513 on an affiliated academic medical domain) and 3,570 authentication records; a separate log recorded a hospital VPN session, though no patient-data theft was shown. A second directory showed a crew using a valid Azure shared access signature token to list 155,568 objects at a manufacturer, including a QuickBooks company file — listing only, no confirmed download. A third held a 563-entry target list and scan results showing live VPNs, Citrix gateways and client portals at more than 60 named law firms. Analyst notes: a malicious Java database driver, an Apollo-derived Windows payload and a Poseidon-derived Linux implant, plus scripts indexing Claude and OpenAI Codex credential files. Severity: **Not specified by source**.

**Why it matters:** The files show which attempts worked, not just what attackers wanted. Credential stuffing against Simpson Thacher & Bartlett's two Citrix gateways (roughly 100 email/password pairs) failed — the gateways rejected them and no VPN session followed — a useful reminder that exposure of attacker infrastructure yields leads to verify, not proof of breach. hunt.io notified CISA before publication.

**Action:** Review VPN authentication logs for failed attempts followed by an unexpected session, Azure storage logs for unusual listing/read activity, and Java application inventories for unexpected database-driver archives. Review and rotate or revoke a suspected exposed SAS token. Treat infrastructure matches as leads, not proof of compromise.

Source: "Exposed Hacker Servers Reveal U.S. Hospital Credentials, Corporate Cloud Access and Law Firm Targets," Cyber Security News, November 26, 2026 — https://cybersecuritynews.com/exposed-hacker-servers/

## Defensive tools and advisories

**Vendor research: DOM-mirroring kit DOMDIFFER led phishing breakthrough rates (also noted, November 26)**

Push Security's contributed research names DOMDIFFER the top phishing kit by "breakthrough rate" — the share of attacks that pass email security, web gateways and safe browsing — every month from August to October 2026, breaking through two to four times as often as the next kit. The AiTM kit, first seen in February 2026 and likely descended from NakedPages, targets corporate Google and Microsoft accounts, usually via fake calendar or Google Voice voicemail lures. Its "DOM mirroring" relays the sign-in through the attacker's own browser, so Google sees genuine browser signals. Severity: **Not specified by source**.

**Why it matters:** DOM mirroring is the newest and fastest-growing AiTM class in 2026 — the main pixel-streaming kit Laurseraph has switched to it, and BlueKit and PhantomDOM use it too. Push notes passkeys stop AiTM where enforced, but kits now pair AiTM with device-code and remote-access-tool payloads that passkeys do not address. This is sponsored vendor content tied to a December 2 webinar, not an independent advisory.

Source: "Most Wanted: The Phishing Kits and Techniques Getting Past Security Controls," The Hacker News (contributed by Push Security), November 26, 2026 — https://thehackernews.com/2026/11/most-wanted-phishing-kits-and.html

**Also noted — ransomware leak-site roundup (November 25–26):** Ransomtracker logged 66 victims in 24 hours. A Hyflock listing claims 90 million Indonesian passports plus fingerprints from Imigarasi; Inception posted Restaurants Brands International (234 GB, November 27 deadline); Rhysida listed Groupe Antilles-Glaces, Koch Law Firm and Grua, Freeman, Tupper & Young; Puppeteer listed dygav.es and signaturelex.com. All are unverified operator claims — https://ransomnews.com/ransomtracker/.

## Coverage notes

**Verified: 6 qualifying new items** (5 lead + 1 also-noted), from at least eight independent outlets: CISA, The Hacker News, SecurityWeek, Cyber Security News, Bitdefender Labs, BleepingComputer, SecurityOnline and Ransomnews. Community lanes: Hacker News (Algolia by-date) and Reddit r/netsec were walked; no in-window primary material surfaced there beyond what outlets covered.

**Ledger dedupe: ~21 candidates suppressed** as already reported or already footnoted — including 1Password, GeoServer (CVE-2026-120705/129243), PAN Fragnesia, FinCEN, the CIA OSINT breach, NTLM-signing break (CVE-2026-118737), Hex.pm (CVE-2026-129514), GT13/Check Point, Com-affiliated vishing, WP Connect backdoors, Volant Manticore, VMware VMSA-2026-0011, Italy/Switzerland AI attacks, Danfoss CVE-2026-129713, InfraTrust, Fail2Install's original KEV/ITW coverage, ShinyHunters/Inception, FastUnlock, Chrome CVE-2026-134401 and FIFA. No "Just missed the window" footnote was added this run.

**Limitations:** The web-search backend intermittently returned no results, so discovery leaned on outlet index pages plus exact-headline searches. Reddit's r/netsec JSON endpoint was captcha-walled. No genuinely new defensive-tool release was disclosed in this overnight window; the one research item is explicitly vendor-sponsored and is labeled as such. Thin overnight days are normal — nothing was padded.
