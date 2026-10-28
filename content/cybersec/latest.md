# Cybersecurity News Briefing — October 28, 2026 (Morning Edition)

**Window: Tuesday, Oct 27 17:00 EDT → Wednesday, Oct 28 07:00 EDT** (previous fire: Tue 17:00). New items only, ledger-checked.

## TL;DR

- **CISA puts both Brotli (Node2Shell) and Adobe Commerce PolyShell on KEV** — confirmed exploitation begins, with one honeypot-observed Brotli attack coming from the same Dutch IP mass-exploiting PHP2Shell; federal deadline Nov 10.
- **Tailscale ships v1.104.4**, finally fixing the four "Tailworm" SSH-privilege-escalation flaws (root-socket forwarding); headscale remains unpatched.
- **Oracle EBS CVE-2026-101767 (CVSS 9.8) starts drawing real probes** just days after the October CPU, per Defused honeypots.
- **Adobe patches eight critical Campaign Classic flaws (up to CVSS 9.9)** plus a critical Adobe Connect code-injection bug.
- **South Korea triple breach**: Daishin Securities confirms 38,490 customers exposed; Bithumb and Mirae Asset also disclose.

## Vulnerabilities and patches

**CISA confirms exploitation of Node2Shell (Brotli) and Adobe Commerce PolyShell; both land on KEV** — event: CISA KEV update Tue Oct 27; coverage Oct 27–28 (Update on previously reported CVE-2026-111497 / CVE-2026-48356)
CISA added CVE-2026-111497 — the Brotli unchecked-return-value OOB write also called Node2Shell, Broccoli2Shell and HTTP2Shell, CVSS 9.2 — and CVE-2026-48356, the Adobe Commerce/Magento unrestricted file upload (CVSS 9.3, aka PolyShell), to the KEV Catalog, ordering FCEB agencies to remediate by Nov 10 under BOD 26-04. Concrete exploitation evidence emerged overnight: ELLIO recorded real-world CVE-2026-111497 exploit traffic from 94.154.43[.]7 (Netherlands) carrying a correctly-structured public RCE payload for node v24.22.0 — the same IP observed targeting PHP2Shell — calling it "RCE being adopted by an operator focused on mass exploitation." Previdian logged a Brotli exploitation attempt from a US-based IP as of Oct 27, and Wiz says 91% of cloud environments have resources vulnerable to the flaw while 82% have publicly exposed Node.js instances. No working public PoC had been confirmed against Brotli before this.
*Why it matters:* This ends the "no exploit yet" grace period on the Brotli saga — a library-level flaw (Node.js fixed in 22.23.4/24.22.1/26.12.1; .NET, PHP, Bun, Python, Ruby, Envoy, Istio, ClickHouse all patched) is now in the hands of the same mass-exploitation operator working PHP2Shell. PolyShell's Magento loader campaign (ongoing since July 2025, hundreds of stores, no next-stage recovered) gains KEV urgency.
*Action:* Patch per vendor guidance, then restart processes and rebuild/redeploy container images (BleepingComputer); if you can't patch, block incoming Brotli-compressed requests at proxy/firewall or disable Brotli decoding.
Sources: CISA alert, Oct 27, 2026 — https://www.cisa.gov/news-events/alerts/2026/10/27/cisa-adds-two-known-exploited-vulnerabilities-catalog · BleepingComputer, Sergiu Gatlan, Oct 28, 2026 06:46 AM — https://www.bleepingcomputer.com/news/security/cisa-warns-of-hackers-now-exploiting-critical-node2shell-flaw/ · The Hacker News, Ravie Lakshmanan, Oct 28, 2026 — https://thehackernews.com/2026/10/adobe-commerce-polyshell-attacks-breach.html · FortiGuard outbreak alert, Oct 27 — https://fortiguard.fortinet.com/outbreak-alert/node2shell

**Tailscale releases v1.104.4 fixing the four Tailworm SSH flaws; headscale still exposed** — event: Tailscale blog + release Wed Oct 28 (Update on previously reported Tailworm)
Tailscale shipped v1.104.4 addressing the flaws Erica Windisch (Cyberdione Labs) published uncoordinatedly as "Tailworm": under TS-2026-014 a permitted SSH session could reach root-only Unix sockets (Docker socket, tailscaled local API) via a TOCTOU swap during socket forwarding — her tests produced a root shell and /etc/shadow reads with no admin rights; TS-2026-015 covers a memory-exhaustion crash of the Tailscale process, TS-2026-016 a serve-mode socket path to remote node control, and TS-2026-017 a TLS-mount permission-header leak. The fix moves forwarding from the root daemon to the logged-in user and relies on OS file permissions. Tailscale stresses exploitation requires a foothold on a policy-authorized peer — not unauthenticated access — and that stable app connectors (the finding in unreleased conn25) are unaffected. The four bulletins carry no CVE IDs or severity scores.
*Why it matters:* The wormable <1s/hop lateral-movement claim gets an authoritative answer: real, but policy-gated. Self-hosted control-plane operators are still exposed — headscale 0.29.5 (Oct 13) does not fix the issues, and headscale grants SSH forwarding on every accepted session with no policy off-switch.
*Action:* Update to v1.104.4+ on every Tailscale SSH server; where you can't yet, set TS_SSH_DISABLE_FORWARDING=1 in the tailscaled environment and restart; review SSH policy grants and consider SSH check mode; treat compromised devices specially (stolen device keys stay valid until deleted at the control plane).
Sources: Tailscale blog, Mike Shaver, Oct 28, 2026 — https://tailscale.com/blog/tailscale-ssh-findings-fixes · The Hacker News, Swati Khandelwal, Oct 28, 2026 — https://thehackernews.com/2026/10/tailscale-patches-ssh-flaws-that-could.html · SecurityWeek, Oct 28, 2026 — https://www.securityweek.com/tailscale-rushes-patches-after-uncoordinated-disclosure-of-tailworm-flaws/

**Fresh Oracle EBS hole CVE-2026-101767 (CVSS 9.8) starts attracting live probes** — event: Defused telemetry Oct 27; SecurityWeek Oct 28 5:14 AM ET
Threat-intel firm Defused says its Oracle E-Business Suite decoys "recorded exploratory exploits against CVE-2026-101767" on the morning of Oct 27 — the first in-the-wild activity on a flaw patched only last week in Oracle's October CPU. The vulnerability, discovered and reported by Horizon3.ai, is an unauthenticated SQL injection in the Application Object Library TCF servlet (reachable under default EBS URL firewall settings) that chains to OS-command execution as the EBS application account. No other exploitation reports exist yet.
*Why it matters:* EBS is a Cl0p-style jackpot target — last year's EBS zero-day extortion wave hit 100+ organizations. Days between CPU patch and first probing means unpatched internet-facing EBS instances are already being hunted.
*Action:* Apply the Oct 2026 CPU now and hunt for probes against the TCF servlet path.
Sources: SecurityWeek, Ionut Arghire, Oct 28, 2026 05:14 AM ET — https://www.securityweek.com/hackers-start-probing-fresh-oracle-ebs-vulnerability/ · Horizon3.ai research — https://horizon3.ai/attack-research/vulnerabilities/cve-2026-101767/

**Adobe ships emergency-grade fixes for eight critical Campaign Classic flaws (up to CVSS 9.9) plus Adobe Connect** — event: APSB26-162 / APSB26-182 published Oct 27; coverage Oct 28
Adobe's APSB26-162 (Priority 2, published Oct 27, 2026) fixes eight critical vulnerabilities in Adobe Campaign Classic v7 ≤ 7.4.4 build 9402 on Windows/Linux, headed by code injection (CVE-2026-104351), OS command injection (CVE-2026-104353) and incorrect authorization (CVE-2026-105780) — each rated CVSS 9.9 by Adobe — plus four SQL-injection flaws (9.8–9.1) and a reflected-XSS privilege escalation (9.3). Adobe rates CVE-2026-105781 at 9.8; one aggregator lists it at 10.0. Separately, APSB26-182 fixes CVE-2026-48360, a critical code-injection flaw in Adobe Connect Desktop/Mobile. Adobe is aware of no exploitation of any of these.
*Why it matters:* ACC is a campaign-management platform sitting on customer-marketing databases; repeat 9.9-rated code-execution bugs (this is the third critical ACC batch since June) make unpatched ACC servers persistent RCE targets.
*Action:* Upgrade Campaign Classic to 7.4.4 build 9403; update Connect Desktop to 2026.9.61 (Win) / 2026.10.66 (Mac Silicon) / 2026.10.170 (Mac Intel) and Mobile 4.5.
Sources: Adobe bulletin APSB26-162, Oct 27, 2026 — https://www.adobe.com/trust/security/products/campaign/apsb26-162.html · The Hacker News, Oct 28, 2026 — https://thehackernews.com/ · SecurityOnline.info, Do Son, Oct 28, 2026 — https://securityonline.info/adobe-vulnerabilities-campaign-classic-connect/

## Attacks and campaigns

**South Korea: Daishin Securities confirms 38,490 customers breached; Bithumb and Mirae Asset also disclose** — event: disclosures Wed Oct 28 (KST), Yonhap
Daishin Securities said personal information — names, e-mail addresses, mobile numbers — of 38,490 customers was taken after it detected signs of unauthorized access "last week"; it notified customers, is investigating with authorities and will compensate proven damages. Crypto exchange Bithumb separately confirmed an intruder took personal data from its internal mobile groupware system, and financial authorities opened an inspection into Mirae Asset Global Investments' breach. Scope for Bithumb and Mirae was still unconfirmed at publication.
*Why it matters:* Three coordinated-feeling financial-sector disclosures in one morning (Bithumb's intrusion detected Oct 27) echo the pattern behind last year's Korean mass-hacking ring that used the ARTEX AI framework against ~90 organizations; regulator-led inspections suggest FSS sees a common vector.
Sources: Yonhap (LEAD), Oct 28, 2026 — https://en.yna.co.kr/view/AEN20261028002751320 · Korea Herald — https://m.koreaherald.com/article/10917306 · SBS Korea — https://news.sbs.co.kr/english/article.do?news_id=N1008818628

**Update on GMO Onamae: ransomware threat lands, attacker leak site vanishes, $1M bounty opens** — event: Oct 27–28 (Update on previously reported onamae ransomware standoff)
After the attackers' $1M ransom deadline passed with GMO refusing to pay, they posted claimed GMO customer material on their leak site on Oct 27 and threatened daily dumps plus ransomware against customers; GMO urged affected customers to shut down/disconnect PCs and consoles (BleepingComputer, Oct 27 07:35 PM ET). By ~4 a.m. Japan time Oct 28 the attackers' domain and mirrors were shut down (GMO says posted material contained inaccurate details), and GMO opened a "Cybercrime Bounty" offering up to $1M total for tips leading to arrests, plus up to ¥3M (~$19k) to confirmed-affected victims. Goldenway Japan and Ava Trade Japan reported customer impact to the FSA.
*Why it matters:* A broker-linked FX-trading cloud breach escalating to an explicit ransomware threat against end customers — plus a company-run bounty — is a new escalation pattern for Japanese cloud providers; watch for copycat extortion of Onamae customers.
Sources: Kyodo News, Oct 27–28 — https://english.kyodonews.net/articles/-/89496 · Japan Times, Oct 28, 2026 — https://www.japantimes.co.jp/news/2026/10/28/japan/crime-legal/gmo-hacker-website-shutdown/ · BleepingComputer, Bill Toulas, Oct 27, 2026 07:35 PM — https://www.bleepingcomputer.com/ · The Hacker News, Oct 28, 2026 — https://thehackernews.com/2026/10/japans-gmo-offers-1-million-bounty-to.html

## Defensive tools and advisories

**CISA ships ScubaGear v2.0 and ScubaGoggles v1.1.0** — event: CISA announcement Oct 27; coverage Oct 28
CISA's SCuBA project released major updates to its open-source configuration-checkers: ScubaGear 2.0 moves Microsoft 365 assessment to direct REST API calls for faster, more accurate reports, and ScubaGoggles 1.1.0 adds an assured-controls baseline aimed at resource-strapped Google Workspace admins plus enhanced checks for Google's Gemini AI settings. Both support BOD 25-01 compliance for federal agencies.
*Why it matters:* Direct-API assessment removes the CSV-export fragility that made M365 baselines audits painful — expect faster continuous-verification pipelines and more Gemini-config coverage in vendor tooling.
*Action:* Re-baseline M365/Google Workspace tenants with the new versions; federal teams, align to BOD 25-01.
Sources: CISA SCuBA project page (announced Oct 27, 2026) — https://www.cisa.gov/resources-tools/services/secure-cloud-business-applications-scuba-project · Help Net Security, Oct 28, 2026 — https://www.helpnetsecurity.com/2026/10/28/cisa-scubagear-scubagoggles-update/

## Also noted

- **Overnight ransomware leak-site adds (ransomware.live, detected in-window):** Echo Global Logistics (3PL, Chicago; attack est. Oct 27), Ryder (posted via WhiteArmor, ~19:25 ET Tue), Meleghy Automotive GmbH (DE, vehicle/visa dossiers incl. Tesla records), Machelen OCMW municipal welfare servers (BE, 1.3M files), plus a pending-exposure Malaysian telecom. Source: https://www.ransomware.live/
- **AbuseBox** — new open-source monitor for IP blacklists, DNS, SSL and email records (Help Net Security, Oct 28).
- **NIST starts the countdown to retire quantum-vulnerable encryption** — transition-timeline push covered by Help Net Security, Oct 28.

## Just missed the window

- **Anthropic's Frontier Red Team**: unreleased research model wrote five working exploit chains (Chrome one-click RCE — 62 CVEs fixed in Chrome 156, Safari SOP bypass, Android lockscreen bypass, Windows remote DoS, Linux); post dated Oct 27 (pre-window) — https://www.anthropic.com/research/discovering-vulnerabilities-with-claude
- **Sony Network Communications (Japan)** disclosed the So-net/plala breach: 2,585,481 records (names, DOB, phone, connection IDs), 54,401 legacy email passwords reset Oct 27, 16 bank-account rows; intrusion Oct 19, found Oct 23 — CyberInsider, Oct 27 — https://cyberinsider.com/sony-data-breach/
- **FBI/DOJ seized bulletproof host Virtualine's domain** (virtualine.net) in a Netherlands/UK/Hessen-coordinated operation; DoJ PR Tuesday Oct 27 — https://www.justice.gov/usao-edva/pr/bulletproof-website-domain-seized-international-law-enforcement-operation
- **ZDI breakdown: the PeopleSoft zero-day ShinyHunters exploited since May is three chained flaws** (loopback-trusting SSRF root → internal mgmt interface → deserialization) all shipped under one June CVE-2026-35273 (CVSS 9.8); ZDI analysis Oct 20, re-covered Oct 28 — https://www.zerodayinitiative.com/blog/2026/10/20/cve-2026-35273-ssrf-to-rce-in-oracle-peoplesoft
- **Skyw4lker**: ANY.RUN analysis (Oct 27) of a US-focused M365 device-code phishing kit — 618 sessions June–Oct 21, 46–47% US, same developer as Gabagool — https://any.run/cybersecurity-blog/skyw4lker-phishing-kit-analysis/
- **Aokigahara infostealer** (Check Point, reported Oct 27): targets Claude/ChatGPT Desktop/Codex/Cursor/Copilot/Gemini CLI sessions and developer keyrings for resale + extortion — https://www.darkreading.com/endpoint-security/aokigahara-stealer-developer-keyrings-ai-assistants

## Coverage notes

- **Verified: 7 items fully sourced** (all in-window pages fetched and read; leading stories read in full), plus 3 "also noted" and 6 "just missed the window" footnotes (each tagged once in the ledger).
- **Ledger dedupe: 6 candidates suppressed as already reported** — including the BleepingComputer re-cover of the Stryzhak sentencing (reported Oct 27 17:00 fire), a UNM Health roll-up piece (4 prior ledger entries), Dark Reading's Black Basta/Teams article (already footnoted), Dark Reading's Serengeti III follow-up (reported Oct 22), the Wagyu.xyz leak-site re-claim, and the eMudhra Chrome-distrust effective date (covered by the Oct 27 OOB update).
- **Dead ends:** one search-backend connect failure on the first Oracle EBS query (recovered via headline search on retry — no third attempt needed); Help Net Security's "Breach victims / unknown companies" study left unverified (no fetchable timestamp) and was dropped; Cisco's Wednesday advisory drop (16:00 UTC) falls after the window closes; EPICS research-facility flaws article published 07:15 ET — fifteen minutes past window end, excluded.
- Phase walls respected: anchor 07:20, discovery+verify through ~07:50, write 07:50+.
