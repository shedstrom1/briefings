# Cybersecurity News Briefing — November 12, 2026 (Midday Edition)

**Window:** Thursday, Nov 12, 2026, 07:44 → 12:20 ET (new items only since the 07:44 edition; ledger-deduplicated)

## TL;DR
- **Europol's Operation ENDGAME strike:** 235 servers and 257 domains seized across the Gootloader, CastleRAT and StealC malware networks; over 6 million victim credentials identified — and StealC's second takedown of 2026, after it rebuilt on the Virtualine hosting the FBI seized in October.
- **"SignDrop":** a new Windows zero-day PoC from Abdelhamid Naceri abuses Microsoft's October-2025 CLFS authentication mitigation itself to reach SYSTEM and can defeat any antimalware engine's tamper protection; no patch, ~80% of installs estimated vulnerable.
- **Citrix ships CTX697305:** 12 named NetScaler ADC/Gateway/Console and Secure Access Client flaws patched — nine criticals up to CVSS 9.5 plus an unauthenticated Console auth bypass (9.4); no known exploitation.
- **Apify discloses an internal breach** (Nov 9–10 intrusion, detected Nov 11): API tokens and Proxy passwords rotated, GitHub OAuth revoked, some customers told to reset 2FA.
- **Update — SCKit/ScreenConnect MSP campaign:** Blackpoint confirms the toolkit exploits the now-patched CVE-2026-84869 (CVSS 9.9) and aims RelayTrap at technicians' workstations to pivot across MSP environments.

---

## Vulnerabilities and patches

### SignDrop: Windows zero-day weaponizes the CLFS anti-exploit mitigation to grant SYSTEM
**Event date:** Nov 12, 2026 (public PoC release; no patch, no CVE). BleepingComputer published at 11:04 AM ET.
Security researcher Abdelhamid Naceri ("Nightmare Eclipse") released SignDrop, a working local privilege escalation exploit that attacks the mitigation Microsoft introduced in October 2025 to block authentication attacks against Common Log File System (CLFS) vulnerabilities. The flaw is in how CLFS creates the authentication file (".cnpf") when a log file is opened: if a privileged process — such as Windows Defender — can be induced to open the log, the authentication file is created with SYSTEM privileges while inheriting the original file's access permissions. Naceri says the technique works against any antimalware engine's self-defense/tamper-protection, since the kernel sees the file creation as coming from the engine's own legitimate process. He tested it on Windows 11 26H2 and Windows Server 2025 and estimates ~80% of Windows installations are vulnerable; machines are checkable via HKLM\SYSTEM\CurrentControlSet\Services\CLFS\Authentication\Mode (0 or 1 = vulnerable). Disabling the mitigation is not a safe workaround — it re-exposes devices to the CLFS memory-corruption attacks it was built to stop. Severity: Not specified by source (unpatched zero-day, no CVSS).
**Why it matters:** a defense feature turned privilege-escalation primitive is a worst-case pattern — and the same primitive disables EDR self-protection, the control defenders lean on for containment. It also lands in Naceri's ongoing single-handed disclosure campaign (MiniPlasma, RoguePlanet, ShieldCrash…), several of which remain unpatched.
**Action:** run the PoC in a sandbox to test exposure, audit the CLFS Authentication\Mode registry value fleet-wide, and prioritize the next Microsoft advisory covering CLFS.
Source: BleepingComputer — "New Windows SignDrop zero-day gives hackers SYSTEM privileges," Nov 12, 2026 11:04 AM ET — https://www.bleepingcomputer.com/news/security/new-windows-signdrop-zero-day-gives-hackers-system-privileges/
Source: Cybersecurity News — "Windows SignDrop 0-Day Exploit Lets Attackers Gain SYSTEM," Nov 12, 2026 — https://cybersecuritynews.com/windows-signdrop-zero-day-exploit/
Source: Cyberpress — "New Windows SignDrop Zero-Day Flaw Lets Attackers Gain SYSTEM," Nov 12, 2026 — https://cyberpress.org/new-windows-signdrop-zero-day-flaw/

### Citrix patches 12 NetScaler and Secure Access Client flaws; bulletin tops out at CVSS 9.5
**Event date:** Nov 12, 2026 (Citrix security bulletin CTX697305 published). No known exploitation.
The nine critical NetScaler ADC/Gateway issues are memory overflow/corruption flaws (CVSS 9.3–9.5) spanning packet processing (CVE-2026-120868, all deployments), SMPP, DNS/DoH, NetScaler Push, Gateway/AAA (CVE-2026-107412, 9.5), Email OTP, Endpoint Analysis (CVE-2026-111344) and ICA proxy (CVE-2026-120864). NetScaler Console CVE-2026-120869 (9.4) is an unauthenticated improper-authentication bypass. Secure Access Client gets two privilege-escalation fixes: CVE-2026-118268 (Windows, 8.5, low-privileged user → SYSTEM, fixed 26.11.1.6) and CVE-2026-118269 (Linux, 8.5, → root, fixed 26.10.4). Fixed ADC/Gateway builds: 14.1-73.57+, 13.1-64.39+, 14.1-FIPS 14.1-73.58+, 13.1-FIPS/NDcPP 13.1-37.293+. Community count of the full CTX697305 bulletin is 28 CVEs. Citrix frames the drop as output of a deeper AI-assisted codebase review to harden NetScaler before threats materialize.
**Why it matters:** NetScaler remains among the most-targeted edge appliances (this month's Exploited chain of NetScaler/ADC advisories is well documented), and the config-dependent criticals — EPA, Email OTP, ICA proxy, SMPP — mean every feature deployment is a distinct exposure surface. The AI-assisted- discovery framing also mirrors Fortinet's embargoed AI-found flaws from last week.
**Action:** upgrade ADC/Gateway/Console to the fixed builds and update Secure Access Client on endpoints; if you can't patch immediately, review which virtual-server features (EPA, DoH, Push, SMPP) you actually run and disable what you don't.
Source: The Hacker News — "Citrix Patches 12 NetScaler and Secure Access Client Flaws, Including Authentication Bypass," Nov 12, 2026 — https://thehackernews.com/2026/11/citrix-patches-12-netscaler-and-secure.html
Source: Poppelgaard — "CTX697305 and CVE-2026-120868: 28 new NetScaler vulnerabilities," Nov 12, 2026 — https://www.poppelgaard.com/ctx697305-and-cve-2026-120868-28-new-netscaler-vulnerabilities-what-you-should-know-and-how-to-fix-your-netscaler-adc-netscaler-gateway

### Update on previously reported (Red Hat Quay service-key takeover, CVE-2026-123710): fixed builds now shipped
**Event date:** Nov 12, 2026 (fix builds; advisory merged Nov 11). Reported this morning at 07:44 as "fixes merged, no release."
Red Hat has released fixed builds across eight Quay 3 branches for the CVSS 9.8 / NVD 9.8 flaw that lets an unauthenticated client install a rogue, no-expiry signing key on the Quay key server — then mint valid tokens for every repository in the registry, private ones included. Red Hat's remediation doc adds operational guidance: restrict the /keys endpoint at the proxy/load balancer to trusted internal components, and audit logs for service_key_rotate / service_key_delete events with a null expiration_date — and it warns deleting a rogue key is not durable until a fixed build is deployed, because the anonymous request can re-install it.
**Why it matters:** "unpatched" became "patch available" — the fix-now clock starts today for any Quay registry reachable beyond trusted networks, especially ones with anonymous access at its default-on state.
**Action:** deploy the fixed builds, gate /keys, and hunt null-expiry approved service keys.
Source: The Hacker News — "Critical Red Hat Quay Flaw Lets Attackers Read and Overwrite All Private Container Images," Nov 12, 2026 (11:27 AM ET) — https://thehackernews.com/2026/11/critical-red-hat-quay-flaw-lets.html
Source: Red Hat Security — CVE-2026-123710 — https://access.redhat.com/security/cve/cve-2026-123710

### Update on previously reported (SCKit–ScreenConnect MSP campaign): exploited flaw identified as patched CVE-2026-84869 (CVSS 9.9)
**Event date:** Nov 12, 2026 (Blackpoint APG details shared with THN at 08:08 AM ET; blog published Nov 11).
Blackpoint Cyber's Adversary Pursuit Group says the recovered SCKit toolkit's guest-to-host file-execution attempt targets CVE-2026-84869 (9.9) — a now-patched ScreenConnect client flaw allowing unauthorized file transfer and execution through an active remote session — rather than a new vulnerability, and testing against a fully patched Host client confirmed the patch blocks payload execution. The residual danger is session abuse: SCKit's RelayTrap replaces a legitimate Guest to fire a payload back at the connecting technician, then digs .ASPXAUTH cookies from browser data and process memory to reuse the technician's management permissions against other customer endpoints. Staging was pulled from ns.mo7sen[.]dev (EadF.php) with attacker infrastructure at 185.99.255[.]28, sc.payproofkit[.]com, cdn007-digitaldownloads[.]com. ConnectWise's response: limited number of partners affected, initial assessment points to abuse of independent third-party extensions, which it has disabled and is telling on-prem partners to disable immediately; no evidence of ConnectWise infrastructure compromise.
**Why it matters:** patching kills the exploit path but not the operator path — compromised instances plus Run Command plus technician-session theft is enough to hop MSP boundaries. This sits directly on the ConnectWise/remote-management attack surface your orgs already watch.
**Action:** verify ScreenConnect Host/Guest clients are patched against CVE-2026-84869, disable third-party extensions per the ConnectWise Trust Center, rotate technician sessions, and hunt the published IOCs.
Source: Blackpoint Cyber — "ScreenConnect Attack: SCKit Turns Remote Support Into an Attack Platform," Nov 11, 2026 — https://blackpointcyber.com/blog/screenconnect-attack-sckit/
Source: The Hacker News — "SCKit Malware Turns ScreenConnect Support Sessions Into Potential MSP Attack Paths," Nov 12, 2026 — https://thehackernews.com/2026/11/sckit-malware-targets-screenconnect.html

---

## Attacks and campaigns

### Apify confirms internal-systems breach; rotates API tokens, revokes GitHub OAuth, tells customers to reset 2FA
**Event date:** Nov 12, 2026 (disclosure; intrusion Nov 9–10, detected Nov 11 at 12:13 UTC).
The Czech web-scraping and browser-automation platform (80,000 customers, including Microsoft, Samsung, T-Mobile, Accenture, Siemens, the European Commission and Roche) said an unauthorized third party gained access to parts of its internal systems. Response: internal credentials invalidated, API tokens and Apify Proxy passwords rotated, all active sessions reset, and OAuth connections to external integrations — GitHub among them — revoked "out of an abundance of caution," with affected users to be emailed when it's safe to reconnect. Apify found no indication that account data stored on its platform was affected, but one customer notice warns their 2FA codes may have been exposed and asks them to reset 2FA via the Apify Console; passwords remain safe per the vendor.
**Why it matters:** scraping/automation platforms sit in CI pipelines and AI-agent toolchains with long-lived tokens — exactly the pattern behind this month's Railway and Northflank env-var incidents. Forced token rotation is a supply-chain event for every pipeline calling Apify.
**Action:** check whether you use Apify; rotate/validate any Apify API tokens and Proxy passwords you issued, re-authorize the GitHub integration only after Apify's all-clear, and reset 2FA if notified.
Source: BleepingComputer — "Apify rotates API tokens, prompts 2FA resets after breach," Nov 12, 2026 09:36 AM ET — https://www.bleepingcomputer.com/news/security/apify-rotates-api-tokens-prompts-2fa-resets-after-breach/
Source: OSINT Insights — "Apify Breach Prompts API Token Rotations, 2FA Resets," Nov 12, 2026 — https://osintsights.com/apify-breach-prompts-api-token-rotations-2fa-resets

### "TypoPrompting": 186 lookalike AI domains route typos to ClickFix lures, Store-signed malware, search hijackers
**Event date:** Nov 12, 2026 (Island research published).
Island traced 186 typosquat domains spanning 27 AI brands (ChatGPT 25 variants, Claude 18, DeepSeek 16, plus Cursor, Perplexity, Midjourney, ElevenLabs clones). A mistyped cloude[.]ai lands Windows visitors on a fake Cloudflare-verification ClickFix page whose command downloads PowerShell from 185.138.88[.]3 (W901.ps1); the same gate serves 211 entry domains including bank and enterprise-software lookalikes. Other AI typos feed fake "Security Check" landers pushing "Net Guard," a Microsoft Store-signed app (package 9MX3CH3BQ44Q v1.0.9.0, analyzed) whose Store-matching hash contains browser-hijacking malware able to run remotely supplied scripts via DevTools; a third path installs one of 30 search-hijacking extensions from a shared kit — 13 removed, the 17 still live claiming 972,000 users combined. Registry records tie an 81-domain portfolio to redirector a11[.]click with rotating Adsterra keys.
**Why it matters:** typopsquatting AI tools is the new SEO-poisoning, and the Microsoft Store signing route neutralizes download-reputation defenses — expect this in phishing-assisted intrusions against teams adopting AI stacks fast.
**Action:** block/monitor the published domains and package IDs, treat "verify your browser/extend" prompts as hostile, and audit for the listed Chrome extension IDs.
Source: Island — "TypoPrompting: From AI Typos to Malware," Nov 12, 2026 — https://www.island.io/blog/typoprompting-from-ai-typos-to-malware
Source: The Hacker News — "186 Fake AI Domains Redirect Users to ClickFix Lures, Browser Malware, and Search Hijackers," Nov 12, 2026 10:15 AM ET — https://thehackernews.com/2026/11/186-fake-ai-domains-redirect-users-to.html

---

## Defensive tools and advisories

### Europol's Operation ENDGAME strikes Gootloader, CastleRAT and StealC: 235 servers, 257 domains, 6M+ victim credentials
**Event date:** Nov 12, 2026 (Europol press release; law-enforcement actions ran this week).
Europol's EC3, with police from Bulgaria, Canada, Denmark, Germany, Moldova, the Netherlands, Poland, the UK and the US (DCIS, FBI Denver, Army CID, DOJ CCIPS) and 30+ public/private partners, seized 235 servers and 257 domains across three toolkits: Gootloader (14 servers, 218 domains, 681 infected — mostly hacked WordPress — sites remediated; the dropper distributes fake legal documents that end in ransomware), CastleRAT (120 servers, 39 domains; the 2025-era RAT whose use led to data encryption and ransoms) and StealC (102 servers dismantled — the second takedown this year; German BKA traced the rebuilt network to the Virtualine bulletproof hosting the FBI seized in October). Over 6 million victim credentials were identified in the seized infrastructure (The Record: ~6.4 million from 40,000+ victims). No arrests or crypto seizures this round; victim data flows to HaveIBeenPwned, DIVD, CheckjeHack, NoMoreLeaks, Sekoia, Shadowserver and NL-NCSC.
**Why it matters:** the rebuild pattern is the headline — StealC reconstituted within months of June's Operation ENDGAME action, on hosting already flagged, confirming takedowns degrade but don't kill "cybercrime-as-a-service" lineages. The captured credential dumps are the raw material for the next ransomware wave, so the HIBP/CheckjeHack check is a real to-do this week.
**Action:** individuals and admins should check published breach tools for affected accounts; WordPress site owners are urged to update credentials, enable MFA and patch — the infection vector Europol says drove Gootloader distribution.
Source: Europol — "6.4 million victim datasets secured in international operation against malware networks," Nov 12, 2026 — https://www.europol.europa.eu/media-press/newsroom/news/64-million-victim-datasets-secured-in-international-operation-against-malware-networks
Source: The Record — "European authorities take down SteaIC malware servers for second time this year," Nov 12, 2026 — https://therecord.media/european-authorities-take-down-stealic-malware-second-time
Source: Dutch Police — "International action disables cybercriminal network," Nov 12, 2026 — https://www.politie.nl/en/news/2026/november/international-action-disables-cybercriminal-network.html

---

## Also noted
- **Ransomware leak tape, Nov 12:** ransomware.live's only fresh in-window listings are Ceytech Makina (Turkish industrial machinery, ~09:20 ET) and ProMantra (US healthcare IT/BPO, seen ~07:20, moments before the window); ransomnews counts 15 listings in 24h as of 11:50 ET — Curious Worm 9 (the already-reported healthcare cluster), Akira 3, The Gentlemen 2, Deadlock 1 — against a 51/day seven-day average.
- **No new CISA KEV entries this morning** — the last 7-day set (Barracuda pair, Openwave, Outlook zero-day, Chrome CVE-2026-125350, BroadWorks, GPUStack, FreeSWITCH, SSHole, IBM Aspera, SonicWall, Apple CVE-2024-27834) is unchanged and fully reported; today's KEV due dates were GPUStack/FixLinux/SSHole's Nov 12 federal deadline.

## Just missed the window
- Microsoft confirmed Windows updates since the October Patch Tuesday are causing SQL Server performance and stability issues — BleepingComputer, Nov 12, 07:11 AM ET (33 minutes before this run's window opened).
- Material Security's "Operation CloudFire" — Google Calendar invites plus a live MFA proxy stealing corporate Google sessions since mid-August, hundreds of attacker domains taken down — The Hacker News posted 06:51 AM ET (first reported Nov 4).
- The 2013 YouHack cybercrime-forum breach was catalogued on HaveIBeenPwned Wednesday with 7.2 million unique email addresses (mostly unsalted MD5 passwords) — The Register, Nov 11, 2:07 PM ET.
- Daylight's $80M Series B led by Growth Equity at Goldman Sachs Alternatives ($120M total, >40x revenue growth) was announced Nov 10; SecurityWeek carried it Nov 11.
- Pre-window leak listings from Tuesday evening: Akira (Leware Construction, R3 Composites, Quality Air), Deadlock (Kadick, Nigeria), The Gentlemen (EiCOH, Japan).

## Coverage notes
- **Verified count:** 8 qualifying event entries (4 leads, 2 "update on previously reported," 2 also-noted), each with a fetched, in-window-dated source; leads read in full; plus 5 "just missed the window" footnotes.
- **Ledger dedupe:** ~20 candidates suppressed as already reported — GSocket (04:38 re-cover), Baker Tilly, eMudhra re-covers, Notion/Fireworks re-covers, wfx-loot "60,000 repos" repost, N-able re-covers (SW 04:39 + THN 06:51 — new substance landed in the 07:44 4EverAble2Shell entry), Xen Orchestra trending, Node.js/PTC/MongoDB/Apache reposts, Chrome 157/Firefox 160 recap of Nov 10 releases, Curious Worm healthcare nine and Vinson (reported Nov 11 17:20), Maersk repeats; plus 1 repeat "missed" footnote (AMD Turin TSA) not re-listed.
- **Dead ends / limitations:** BleepingComputer's Europol-takedown URL 404'd on fetch (covered via Europol's own release + The Record instead — no third strike); reddit-reading's anonymous feed returned no created_utc, so the two community leads surfaced there (Home Assistant/Mosquitto pre-auth RCE; Pass-ta-key passkey research) were date-checked and rejected as pre-window (Nov 8 and August respectively); THN's dated archive page carries no article list — the site RSS feed was used for timestamped enumeration.
