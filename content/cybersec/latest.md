# Cybersecurity News Briefing — Thursday, November 12, 2026 (07:00 ET Edition)

**Window:** Wednesday, November 11, 17:20 ET → Thursday, November 12, 07:44 ET (previous fire → now). New items only, reconciled against the reported.md ledger.

## TL;DR

- **ScreenConnect supply-chain attack on MSPs escalates:** attackers hit 6 MSPs and 288 endpoints across 48 orgs in a 90-minute burst, deploying a "SCKit" toolkit that intercepts session cookies and ambushes technician workstations; an authentication flaw in the RMM+ extension (blank access key accepted in 1.0.31/1.0.32) is the suspected way in. ConnectWise disabled five extensions in ~400 partner environments.
- **Massive GitHub Actions secret-stealer campaign:** 4,837 hijacked accounts pushed test PRs into 61,764 repos since Nov 10; a "wfx" CI stealer actually ran in 1,806+ repos (SafeDep counted 8,093 repos with malicious commits), harvesting cloud creds and installing AWS/Azure backdoors that survive rotation.
- **N-able N-central: new exploits defeat the emergency patch.** 4EverAble2Shell (unauth root RCE) and Agent2Shell (SYSTEM on managed Windows agents via 10+ zero-days) work against the just-released 2026.4.1 HF2 when the non-default Legacy API is exposed; probing began within hours of PoC release.
- **Red Hat Quay CVE-2026-123710 (CVSS 9.8):** unauthenticated takeover of registry token signing lets an attacker install a persistent signing key and mint tokens for any repository — passwords and robot-token resets don't kick them out; fixes merged Nov 11, no release yet.
- **Microsoft: GSocket backdoor detections on Linux servers up 356.2%** — attackers keep access through the relay network even after the initial web-app vulnerability is patched.

## Attacks and campaigns

**ScreenConnect campaign weaponizes MSP remote access with "SCKit" backdoor toolkit** — Event: campaign started Nov 11, 17:47 UTC (Huntress detection); ConnectWise advisory Nov 11 evening; SecurityWeek detail Nov 12.
Huntress tracked 6 compromised MSPs and 288 endpoints across 48 organizations — all 288 hosts hit within 90 minutes — where attackers used ScreenConnect's Run Command to install rogue clients plus SCKit, a self-replicating toolkit that provides persistent backdoor access, intercepts session cookies, and waits for an MSP technician to connect to an infected endpoint before pivoting to the technician's workstation and onward to other panels. Huntress analysis points to an authentication bypass in the third-party RMM+ extension: versions 1.0.31/1.0.32 accepted a blank access key if an admin never configured one. ConnectWise preemptively disabled five RMM+-family extensions (Labtech Plus, RMM Plus, RMM+, RMM Plus Password Link, RMM+ Password Link) in ~400 cloud partner environments and warned on-premises customers to disable them manually. Published IOCs include relay 185.99.255[.]28, C2 domains newnewnew.gotdns[.]org / soxnewwork.homedns[.]org / sc.payproofkit[.]com, stager ns.mo7sen[.]dev, rogue client IDs 2588ef5c15954b10 / ed630cc2c873b091, and files Panel-Lateral.ps1, ToolboxRelayUploader.exe. Severity: Not specified by source (active campaign, no CVSS).
*Why it matters:* this is the trusted-remote-access path that every MSP customer depends on — the same vector class that produced Kaseya — and the technician-workstation ambush converts one infected endpoint into full MSP-side compromise.
*Action:* disable the five extensions on on-prem ScreenConnect, audit Run Command/extension-install logs (even if unalerted — sequential attacker tags suggest earlier victims), review the IOCs, and don't open interactive sessions with suspected endpoints.
Source: BleepingComputer, Nov 11 2026 5:56 PM ET — https://www.bleepingcomputer.com/news/security/active-screenconnect-attacks-breach-msps-impact-dozens-of-organizations/
Source: SecurityWeek, Nov 12 2026 5:39 AM ET — https://www.securityweek.com/hackers-deploy-malware-in-fresh-screenconnect-campaign/

**"wfx" GitHub Actions secret stealer ran in 1,806+ repos via 4,837 hijacked accounts** — Event: campaign began Nov 10 ~12:46 UTC; Aikido/SafeDep disclosure published Nov 12.
Starting Tuesday, an attacker used stolen credentials for 4,837 GitHub accounts to open test pull requests ("docs: dependency audit notes" from a wfx-docs branch) in 61,764 repositories — including 10,431 reached via collaborator/organization access — then planted a ~1,500-line CI secret stealer that ran in at least 1,806 repos; SafeDep counted 8,093 repositories receiving malicious commits. The stealer collects Actions secrets, GITHUB_TOKEN/OIDC tokens, AI-service keys, cloud credential files and runner-process memory; exfil goes to 194.150.166[.]244:8080, 18.143.168[.]96:8080, 153.75.82[.]159 or Telegram; then it exchanges OIDC for AWS/GCP/Azure sessions and installs rotation-surviving backdoors (AWS trust-policy rewrites, Azure wfx-ci-* federated credentials). Cleanup within minutes and deleted run logs make missing run numbers the reliable indicator. It reached a Packagist release (digitelvis/neuronai-studio 3.14.3, treat installers as compromised) and two Go module versions; 50+ compromised users are package publishers. Accounts hit in October's GhostAction wave were ~1,000x more likely to be reused. Severity: Not specified by source (activity, not a CVE).
*Why it matters:* a credential-reuse harvest at this scale silently converts CI runners into cloud-account takeover points, and the persistence layer is designed to outlive the token rotation most teams will do.
*Action:* hunt the listed branches/PR titles/commit messages since Nov 10; if the stealer ran, rotate every workflow secret and cloud role, revoke the author's tokens/OAuth/SSH keys/sessions, check Entra for wfx-ci-* federated credentials and CloudTrail for wfx-* AssumeRole→CreateAccessKey chains, and treat any machine that ran npm install on an affected commit as compromised.
Source: The Hacker News, Nov 12 2026 — https://thehackernews.com/2026/11/4837-hijacked-github-accounts-ran-ci.html
Source: Cyber Security News, Nov 12 2026 — https://cybersecuritynews.com/github-accounts/

**Microsoft: GSocket relay backdoor keeps Linux servers compromised after patching** — Event: Microsoft Defender report published Nov 11 (publish time unverified); first broad coverage BleepingComputer Nov 12 4:38 AM ET.
Multiple unrelated threat actors are installing GSocket, an open-source tool that bridges two outbound connections through the Global Socket Relay Network, on compromised web servers — no inbound listener, no attacker-owned C2 to block, and re-access via a shared secret even after the initial vulnerability is patched. Detections rose 356.2% between March and August 2026; 87.1% of attributable incidents involved web apps/hosting (PHP+cPanel 63.9%, Apache 24.3%), and education/research (30.7%) was the most-hit sector. Attackers disguise the process as a kernel thread with cron/systemd/shell-profile persistence; some attempted privilege escalation via the Dirty Frag kernel flaw (CVE-2026-43284). Severity: Not specified by source.
*Why it matters:* patching is being treated as incident closure while a signature-clean relay tunnel survives it — containment has to include host forensics and egress control.
*Action:* remove GSocket binaries/persistence entries, verify relay connections stopped, apply deny-by-default egress, mount /dev/shm noexec, and patch CVE-2026-43284.
Source: BleepingComputer, Nov 12 2026 4:38 AM ET — https://www.bleepingcomputer.com/news/security/hackers-plant-gsocket-on-linux-servers-for-persistent-access/
Source: Cyber Security News, Nov 12 2026 — https://cybersecuritynews.com/gsocket-backdoors/

**Baker Tilly notifies 10,000+ clients of third-party platform breach** — Event: notifications out; BleepingComputer Nov 11 6:01 PM ET (in-window).
The accounting giant (147 countries, $6.8B revenue) says an unauthorized actor obtained client data stored on an unnamed third-party provider's platform; it became aware Sept 6 and finished identifying affected people Oct 28. Its notice omits a total count and data types, but the Texas AG filing lists 10,456 Texans exposed with SSNs, full names and addresses. No ransomware-group claim found; no evidence of activity on Baker Tilly's own network. Severity: Not specified by source.
*Why it matters:* another late, thin notification from a professional-services firm whose client data lives on a vendor platform — the state filing, not the vendor, supplied the real numbers.
*Action:* affected clients should enroll in the 24 months of Experian credit/identity monitoring being offered.
Source: BleepingComputer, Nov 11 2026 6:01 PM ET — https://www.bleepingcomputer.com/news/security/baker-tilly-notifies-clients-of-data-breach-at-third-party-provider/
Source: OSINTsights, Nov 11 2026 — https://osintsights.com/baker-tilly-exposes-client-data-through-third-party-breach

**Darktrace: stolen Claude API keys turn Claude Code into a remote-controlled implant** — Event: research published Nov 12.
In a lab AD environment, Darktrace used a developer's stolen Claude credentials plus Claude's memory-stores feature as a command channel: every Claude Code process checked into heartbeat/input/output files while the operator wrote plain-English jobs; the agent sprayed a harvested password, cracked a domain account, and uploaded a 1 GB file out through Anthropic's own Files API — all traffic indistinguishable from normal api.anthropic.com inference. No platform vulnerability was exploited and nothing was seen in the wild; standard API keys reach memory stores while OAuth credentials don't. Severity: Not specified by source (design trade-off, no CVE).
*Why it matters:* "legitimate actions, legitimate channels, legitimate credentials" with an anomalous pattern is exactly what signature and URL-reputation defenses miss; agent keys now deserve production-credential handling.
*Action:* prefer OAuth/federation over raw API keys, scope and expire keys, watch for agent processes with unusual CLI flags (e.g. --dangerously-skip-permissions) and anomalous API data ratios.
Source: Help Net Security, Nov 12 2026 — https://www.helpnetsecurity.com/2026/11/12/stolen-claude-api-keys-remote-controlled-implant/

**Ransomware leak sites, overnight (first seen ~04:30–06:00 ET Nov 12):** Raymond James Financial listed with an attack estimate of Nov 11 (group unconfirmed); Aidon Oy/Gridspertise (Enel Group smart-meter operations platform) breach disclosure; a University of Texas at Arlington listing alongside a "1.23 TB from ProLiant main file server" claim (pairing unconfirmed); J.E.T.S. Limited, operator of Jamaica's MultiLink/ACH payment switch; Florida MSP Gator Works Computing exposing thousands of residents; MSP ZIRNET LLC "full breach"; IT provider Onmion LLC (3 companies, 3,000+ victims). Groups largely unattributed; previously-covered 17:20 listings (Esri South Africa, DraftKings, healthcare cluster) excluded per ledger.
Source: Ransomware.live, Nov 12 2026 — https://www.ransomware.live/

## Vulnerabilities and patches

**Update on previously reported (nable-able2shell / ncentral-2026.4-hf2): 4EverAble2Shell and Agent2Shell beat the Nov 10 hotfix** — Event: new disclosure Nov 11–12 (N-able advisory Thursday; THN Nov 12).
Researcher Sean Bales' follow-up claims PoCs that work against N-central 2026.4.1 HF2 (build 2026.4.1.26) itself: 4EverAble2Shell gives unauthenticated root RCE on on-prem servers whose non-default Legacy API is internet-exposed, and Agent2Shell chains 10+ zero-days to run commands as NT AUTHORITY\SYSTEM on managed Windows agents when the appliance ID is known. Previdian saw targeted probing within hours of PoC release from 157.20.241[.]16 — initial execution attempted through Apache Axis CVE-2026-90436 (CVSS 9.8, patched Nov 6), the first documented attempts at that flaw — with watchTowr corroborating Legacy-API probes; no confirmed successful exploitation yet. N-able: Legacy API is off by default and not recommended; hosted environments unaffected.
*Action:* disable the Legacy API immediately on on-prem N-central, block the probe IP, and prioritize the Nov 6 Apache Axis patch.
Source: The Hacker News, Nov 12 2026 — https://thehackernews.com/2026/11/new-n-central-exploits-work-against.html

**Red Hat Quay signing-key takeover: CVE-2026-123710 (CVSS 9.8 Critical)** — Event: Red Hat advisory + Bugzilla; fixes merged to GitHub Nov 11; SecurityWeek Nov 12 4:23 AM ET.
Where the Quay service key server is reachable by untrusted clients, an unauthenticated attacker can abuse weaknesses in key rotation to install their own trusted signing key and mint registry tokens granting read/write on any repository — exposing private images and embedded secrets and poisoning images other systems pull. The attacker key can persist forever and delete legitimate replacement keys; disabling anonymous access doesn't help because any valid account can exploit it, and password/robot-token resets don't invalidate a rogue key. Discovered by Red Hat's Dave O'Connor; no fixed version published at fire time (merges cover this and CVE-2026-110697). No exploitation reported. Severity: CVSS 9.8 (Red Hat).
*Why it matters:* the registry is the software supply chain's trust anchor — signing-key compromise outlives every credential rotation responders will attempt.
*Action:* restrict key-management service access, review key-change logs, remove unauthorized keys (they can be re-added until patched), and pin/trust your image digests.
Source: SecurityWeek, Nov 12 2026 4:23 AM ET — https://www.securityweek.com/critical-red-hat-quay-vulnerability-could-expose-private-container-images/

**PTC ships fix for Windchill/FlexPLM deserialization RCE — CVE-2026-126325 (CVSS v4 9.2)** — Event: vendor patch + advisory, coverage Nov 12.
Improper input validation / deserialization of untrusted data in PTC Windchill and FlexPLM allows RCE via crafted requests; affected release lines span 11.0 M030 through 13.1.4.0 (Windchill) / 13.1.3.0 (FlexPLM). PTC-hosted environments are patched server-side; self-hosted customers are urged to patch ASAP. watchTowr notes PTC products are a standing favorite of initial-access brokers — the prior critical (CVE-2026-12569, 9.3) was actively exploited from July in a Cl0p-linked extortion campaign dropping JSP web shells. No exploitation yet for this flaw. Severity: CVSS v4 9.2 (CVE.org/NVD).
*Action:* patch exposed Windchill/FlexPLM now and hunt webshells/backdoor access; if you can't patch, front with network restrictions.
Source: The Hacker News, Nov 12 2026 — https://thehackernews.com/2026/11/ptc-patches-critical-windchill-and.html
Source: NVD CVE-2026-126325 — https://nvd.nist.gov/vuln/detail/CVE-2026-126325

**Update on previously footnoted (CVE-2026-119618 Apache mod_http2): Red Hat raises it to 9.8, cites a reliable public exploit** — Event: Red Hat assessment published Nov 11; THN write-up Nov 12.
Red Hat's rating for the mod_http2 assign_headers race (Apache httpd 2.4.55–2.4.70, fixed in 2.4.71) is 9.8 Critical, unauthenticated, full server-process control on HTTP/2-enabled hosts — and it states "public research has demonstrated a reliable exploit" against a default-config server with mod_http2. Wiz says later that day no exploitation in the wild had been reported. The package itself was footnoted yesterday; the severity/exposure assessment is the new material. Severity: CVSS 9.8 (Red Hat).
*Action:* upgrade to 2.4.71 (or drop HTTP/2 from the Protocols directive) on internet-facing httpd now.
Source: The Hacker News, Nov 12 2026 — https://thehackernews.com/2026/11/critical-apache-http2-flaw-could-let.html

## Defensive tools and advisories

- **CISA:** no new alerts or KEV additions since the Nov 10 six-CVE batch (verified on the advisories index) — a quiet overnight for federal guidance. Source: https://www.cisa.gov/news-events/cybersecurity-advisories
- **Funding wave for agentic/device security (SecurityWeek, Nov 12):** Finite State raised $27M Series C for connected-device software/firmware security; Bay (ex-Unit 8200) launched with $50M to control what enterprise AI agents can do; Dreeze emerged from stealth with $33M to rebuild cloud apps after cyberattacks. Source: https://www.securityweek.com/
- **Microsoft Entra soft-delete guardrail (Help Net Security, Nov 12):** new seven-day safeguard against irreversible identity deletion, closing an accidental/malicious hard-delete gap. Source: https://www.helpnetsecurity.com/

## Also noted

- **Humble Bundle's "vibecoded" site relaunch stripped 2FA and change confirmations** — community-verified reports (HN 305 points, Nov 12): new site has no 2FA, allows password/email changes without confirming the current credential or via email verification; no vendor statement yet. Remove saved payment methods and assume account-takeover risk until fixed. Source: Hacker News item 50463899 — https://news.ycombinator.com/item?id=50463899
- **ScreenConnect scale update** folded into the lead item above (Huntress: 6 MSPs / 288 endpoints / 48 orgs).

## Just missed the window

- **MongoDB CVE-2026-118383 (CVSS 4.0 9.5):** unauthenticated use-after-free that crashes a lagging replica-set member on failover; fixes released Nov 10–11, Datadog Security Labs repro published Nov 11 (pre-window); no known exploitation. Source: https://thehackernews.com/2026/11/critical-pre-auth-mongodb-flaw-cve-2026.html
- **Google Cloud warns three AMD Turin (5th-gen EPYC) TSA side-channels** — CVE-2024-36350/-36357 plus new CVE-2026-0004 on C4D/N4D/H4D/Z4D/G4: guest-OS patch + stop-and-start required; AMD guidance dated Nov 10, coverage Nov 12. Source: https://thehackernews.com/2026/11/google-cloud-warns-three-amd-turin-cpu.html

## Coverage notes

- **Verified: 15 items** (counting unit: one per underlying event; 2 are ledger-tagged updates on previously reported events; includes also-noted one-liners) **plus 2 just-missed footnotes.** Every item's page was fetched and carries an in-window date; five leads were read in full.
- **Ledger dedupe: 15 candidates suppressed** as already reported or previously footnoted: eMudhra re-cover (Register/BC), Maersk (SW/OSINTsights), Notion–Fireworks (SW), Node.js (THN), Zoom 9.6/8.1 (THN), Asus router batch (SW), Fortinet ~100-CVE batch (SW — same Nov 10 embargo-lifted release), Citrix/Ivanti (SW), Vanderbilt Hustler update, KB5132061 install failures (Reg), N-able hf2 status page, Cisco BroadWorks (BC), Chrome XML zero-day (SW trending), the 17:20 leak-site batch re-surfacing (Esri SA, DraftKings, Vinson, THERMO, Timber Rattlers, ENERGREEN), and THN's contributed re-cover of Material's Oct 28 Operation CloudFire takedown. One repeat 'Just missed' (Apache 2.4.71) was re-covered today and is counted here rather than re-footnoted.
- **Community lanes walked:** HN Algolia river (14 stories ≥40 pts in window; security-relevant items folded in or deduped) and Reddit r/netsec + r/cybersecurity new-post sweeps (zero in-window posts — overnight quiet). r/cybersecurity search.rss 403'd once; fell back to the sub listing per procedure.
- **Indexes healthy:** BleepingComputer, THN, SecurityWeek, Help Net Security, CISA advisories, ransomware.live, The Register. No dead ends beyond the two 404s (ransomnews slug, guessed THN slug — both recovered via exact-headline search per policy).
