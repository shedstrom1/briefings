# Cybersecurity News Briefing — Monday, November 9, 2026 (Weekend Sweep)

**Window:** Friday, November 6, 2026, 17:20 ET → Monday, November 9, 2026, 07:20 ET (overnight + weekend sweep; new-items-only per ledger)

## TL;DR

- **SonicWall patched an exploited SSL-VPN MFA bypass** (CVE-2026-19868, CVSS 7.1) that lets attackers with valid credentials for one login method skip SAML-enforced MFA entirely; CISA added it to KEV Monday with a Nov 20 federal deadline.
- **LNbits ordered all operators to shut down** their Bitcoin Lightning wallet servers Sunday after a wave of account-creation exploits drained funds — one server lost ~0.023 BTC before the warning went out.
- **A Web3Auth infrastructure flaw breached Keplr wallets** using Google/Apple sign-in; private keys were stolen and $6M+ was moved into recovery wallets across 19 chains.
- **Frontline Education's breach scale tripled** to 3,085,266 people per an Indiana filing; **Amplifon Americas filed for 1.33 million** affected by a January intrusion.
- **Google DeepMind published AI-agent research cutting 1024-bit RSA attack costs** — 3.5x cheaper signature forgery and a ~30x cheaper bulk-factoring path (2048-bit and up unaffected).

## Vulnerabilities and patches

**SonicWall SonicOS SSL-VPN flaw bypasses SAML MFA; exploited in attacks (CVE-2026-19868)** — Event date: November 6, 2026 (advisory Friday; KEV addition November 9)

SonicWall published advisory SNWLID-2026-0014 on Friday fixing seven SonicOS flaws. The headline flaw, CVE-2026-19868 (vendor CVSS 7.1), is an authentication-enforcement vulnerability in the SonicOS SSL-VPN service: a user holding valid credentials for an alternate authentication method can bypass SAML authentication controls — including SAML-enforced MFA — on firewalls configured to use SAML. SonicWall said its PSIRT "investigated a report indicating potential exploitation" and is urging customers to upgrade immediately. Fixes ship in versions 7.3.4-7013 and 8.2.3-8015 (affecting Gen7 7.3.3-7015 and earlier, Gen8 8.2.2-8015 and earlier); no workarounds exist. The batch also fixes six other issues, including two management-interface flaws rated 8.8 that allow unauthenticated unauthorized operations, pre-auth buffer overflows in SSL-VPN and TACACS+ services, and weak password hashing on virtual appliances. The vulnerability was discovered by Ryan Harris of The Electronic Office. CISA added CVE-2026-19868 to its KEV catalog on November 9 and set a November 20 remediation deadline for federal civilian agencies.

**Why it matters:** MFA is usually the last line of defense on perimeter VPNs. This flaw turns a stolen or reused password for one login method into a full VPN session that never touches the identity provider, and SonicWall appliances have faced sustained zero-day campaigns all year (SMA1000 zero-days in July and September, CVE-2026-102255 attempts in October).

**Action:** Upgrade all SonicOS Gen7/Gen8 SSL-VPN firewalls to the fixed releases now; review SSL-VPN access logs for logins that bypassed SAML.

- Source: BleepingComputer, "SonicWall warns of new SSL-VPN MFA bypass flaw exploited in attacks," November 6, 2026, 07:00 PM ET — https://www.bleepingcomputer.com/news/security/sonicwall-warns-of-new-ssl-vpn-mfa-bypass-flaw-exploited-in-attacks/
- Source: CISA Known Exploited Vulnerabilities Catalog / advisory index, November 9, 2026 — https://www.cisa.gov/known-exploited-vulnerabilities-catalog
- Source: SonicWall PSIRT advisory SNWLID-2026-0014, November 6, 2026 (referenced in BleepingComputer coverage)

**LNbits issues emergency shutdown order for all Bitcoin Lightning wallet servers** — Event date: November 8, 2026

LNbits, the free open-source Bitcoin Lightning wallet system, told every operator to shut down their servers Sunday after "a wave of attacks." The first, narrower warning went out at 12:33 UTC asking operators to disable the Paywall add-on; about 80 minutes later the project issued the blanket shutdown notice as several security fixes are prepared. One operator, Kevin Ravensberg (who runs the BitTipBot Telegram bot), said attackers exploited a flaw on his server between 09:22 and 14:00 UTC on November 8 — more than three hours before the first warning — withdrawing 2,285,545 satoshis (~0.023 BTC) through new accounts before he stopped them. Developer Ben Arc said patches "should be merged and available within 24-48hrs," but as of 09:00 UTC Monday no fixed version had shipped; the project also switched off its hosted cloud instances (my.lnbits.com), with services expected back within 24 hours. Security testers confirmed the attack used "a specific extension exploit," though neither they nor the project named it. Severity: Not specified by source.

**Why it matters:** Self-hosted Lightning wallet nodes are now being actively drained in the wild, and there is no patched release to deploy yet — operators face a forced outage decision with no safe re-enable path until the fix lands.

**Action:** Take LNbits servers offline immediately; do not re-enable until the patched release is available; audit logs for unauthorized account creation and withdrawals.

- Source: The Hacker News, "LNbits Urges All Operators to Shut Down Their Bitcoin Lightning Wallet Servers Amid Attacks," November 9, 2026 — https://thehackernews.com/2026/11/lnbits-urges-all-operators-to-shut-down.html
- Source: Crypto Briefing, "LNbits urges users to shut down servers amid wave of attacks," November 8, 2026 — https://cryptobriefing.com/lnbits-urges-users-shut-down-servers/
- Source: Bitcoin.com News, "Lightning Network Security Scare Forces LNbits Servers Offline," November 8, 2026 — https://news.bitcoin.com/security/lightning-network-security-scare-forces-lnbits-servers-offline/

## Attacks and campaigns

**Web3Auth infrastructure flaw drains Keplr wallets; $6M+ moved to recovery wallets** — Event date: November 6–7, 2026

Web3Auth — the social-login layer that runs Google and Apple sign-in for embedded wallets, and a MetaMask-parent Consensys acquisition since June 2025 — said a security issue in its own infrastructure affected wallets it provides inside the Keplr app, discovering it on November 6 and suspending its embedded wallet service. Keplr went further, saying the flaw caused a "private key breach" for users who signed in with Google or Apple; it paused both login methods on November 7. Keplr co-founder Josh Lee estimated roughly 20% of Google/Apple login accounts were affected, with only a fraction exploited. Blockchain-data firm Bitquery counted at least $6.01 million moving from more than 44,000 wallets into recovery wallets across 11 Cosmos-family and 8 Ethereum-style chains — initially flagged as theft, later corrected to a rescue operation after Web3Auth used the attacker's own route to empty remaining at-risk wallets. GoPlus separately said at least ~$1 million had been stolen outright. Wallets created with a recovery phrase or hardware wallet are unaffected. Severity: Not specified by source.

**Why it matters:** This is an identity-provider breach with private-key exfiltration at scale — the recovery-wallet dance (first transfer 21 minutes before the public notice, 86% of wallets emptied only after Keplr warned users) makes it hard for victims to tell rescue from theft, and Web3Auth's embedded-wallet service is used by other apps beyond Keplr.

**Action:** Do not send funds to affected accounts or re-import them; move unstaked funds to recovery-phrase or hardware-wallet accounts; treat any "recovery" DMs or links as phishing.

- Source: The Hacker News, "Web3Auth Flaw Exploited to Steal Keplr Crypto, Provider Empties Other Wallets for Safety," November 9, 2026 — https://thehackernews.com/2026/11/web3auth-flaw-exploited-to-steal-keplr.html
- Source: BeInCrypto, "Keplr Wallet Hack Hits Google and Apple Login Users," November 8, 2026 — https://beincrypto.com/keplr-wallet-hack-google-apple-login-users/
- Source: crypto.news, "Keplr halts Google, Apple logins as $6M wallet drain hits Cosmos," November 8, 2026 — https://crypto.news/keplr-wallet-hack-web3auth-google-apple-login/

**Ransomware leak sites post weekend batch: pharma/biotech cluster plus firms across sectors** — Event date: November 8–9, 2026

The ransomware.live leak-site tracker (checked Monday morning) shows a steady weekend disclosure flow. A cluster of pharmaceutical and biopharmaceutical targets — oncology/cardiology pharma (US/European operations), precision oncology diagnostics, cancer-screening diagnostics, T-cell immunotherapy, and specialty pharma data analytics holding a patient-claims warehouse in the hundreds of millions of records — went up as "pending exposure" entries on November 9 alone, alongside a nonprofit regional hospital in the US. Other named disclosures discovered over the weekend: Calmont Wire & Cable (attack estimated November 6, discovered November 8), JTK Solutions (US, ~$5M revenue), JH Technology, Greentech Renewables (US solar distribution), Trinity Custom Homes (US), Chonlatee Innovation (Thailand), Kadick Integrated (Nigeria), Clínica La Sagrada Familia (Colombia, attack estimated November 7), Pelican ICT (Netherlands, attack estimated October 31), and UNIC Technology (Thailand). The tracker also lists a new-group disclosure for Fortox Security Group, a Colombian private-security firm, and notes six new ransomware groups appeared in the last 10 days. Group attribution for most weekend entries is redacted or pending in the tracker.

**Why it matters:** The pharma-targeting cluster is the standout pattern — multiple biotech/pharma firms hit within hours of each other suggests organized sector sweeps, and the tracker notes victim counts this month are running 67.4% below October, making the weekend's sustained flow notable.

**Action:** Pharma and biotech organizations should treat this as active sector targeting and verify their own leak-site exposure; general guidance is standard (rotate secrets, check backups).

- Source: Ransomware.live leak-site tracker, disclosures discovered November 8–9, 2026 (accessed November 9, 2026, ~07:30 ET) — https://www.ransomware.live/

**Claude Code "Don't Ask Again" files leak 2,500+ live secrets to GitHub; Anthropic responds** — Event date: November 5 (research); updated November 7–8, 2026

Apiiro research (published November 5, updated November 8) found that Anthropic's Claude Code writes approved commands — including the secrets typed into them — to `.claude/settings.local.json` when users pick "Yes, and don't ask again." Of 104,185 public copies of that file on GitHub, 1,122 hold at least one real secret; counting git history, shared `settings.json` files, and agent chat transcripts, Apiiro identified 2,539 real secrets across 1,994 public repositories — including a database vendor's own admin-level cloud key, a production Azure Postgres password, a Square production token, and the owner password of a medical-records database. Sixty-three organizations are affected; one confirmed its key had been live. The Hacker News covered the findings on Saturday, November 7. Anthropic's Boris Cherny (Head of Claude Code) responded on Hacker News November 7 and through the weekend: the file is meant to stay local and is auto-gitignored, but the team is "looking into what we can do," with prototypes planned for auto-mode secret catching and basic secret scanning. Severity: Not specified by source.

**Why it matters:** This is an agentic supply-chain failure mode — developers approve commands carrying production credentials, the agent persists them verbatim in a project file with no privacy marker, and the file gets committed. The Anthropic response makes it a live product-security issue, not just a researcher find.

**Action:** Search your GitHub organization for `.claude/settings.local.json` and `.claude/settings.json`, check git history even if removed, and rotate every password, token, or key those files ever held before cleaning up.

- Source: Apiiro, "Coding Agents Are Leaking Your Secrets to GitHub" (Shmulik Cohen, Matan Giladi), November 5, 2026; updated November 8 — https://apiiro.com/blog/coding-agents-leaking-secrets-github-dont-ask-again
- Source: Hacker News discussion with Anthropic response, item 50407882, November 7–8, 2026 — https://news.ycombinator.com/item?id=50407882

**Frontline Education breach scale triples to 3 million (update on previously reported)** — Event date: November 9, 2026 (Indiana filing/notice)

Update on the ledger entry first footnoted November 4 (`frontline-education-1.3m`). Frontline Education told the Indiana Attorney General that its August breach affected 3,085,266 people — well over twice the roughly 1.3 million summed from earlier state filings. The K-12 administration-software provider says attackers entered part of its environment via a vulnerability in an unnamed third-party software product; the intrusion was detected August 14 and contained August 15. The stolen data came from Frontline Central and School Health Management and may include names, Social Security numbers, dates of birth, driver's license and passport numbers, Medicaid and other health-insurance IDs, and student IDs. Affected US adults are getting two years of free credit monitoring and identity-theft protection; eligible minors get free dark-web monitoring. No group has claimed responsibility.

**Why it matters:** The scale jump (1.3M → 3.1M) makes this one of the largest US education-sector breaches of the year, and the third-party entry point is the same pattern seen in Frontline's ecosystem.

**Action:** Districts and individuals should treat Frontline-related credentials and Medicaid/SSN data as exposed; monitor for identity-theft indicators.

- Source: SecurityWeek, "Over 3 Million People Hit by Frontline Education Data Breach," November 9, 2026, 05:09 AM ET — https://www.securityweek.com/3-million-people-hit-by-frontline-education-data-breach/
- Source: Security Arsenal, "Frontline Education Data Breach: 3 Million K-12 Records," November 9, 2026 — https://securityarsenal.com/blog/frontline-education-data-breach-3-million-k-12-records-exposed-defense-and-identity-theft-response-guide
- Source: Frontline Education, "Notice of Data Breach" (company notice page, filed with Indiana AG) — https://www.frontlineeducation.com/news/frontline-education-notice-of-data-breach/

**Amplifon Americas breach impacts 1.33 million after January intrusion** — Event date: November 5–6, 2026 (notices/filings)

Amplifon Hearing Health Care — the Minneapolis-based US arm of the Milan-headquartered hearing-care giant — disclosed that hackers broke into internal systems in January 2026 and copied files between January 18 and February 8. The company needed eight months to review the stolen data before concluding that personal information was affected. Notices began November 5 (Vermont: 1,027 residents) and a November 6 filing with the Texas Attorney General put the company-wide total at 1,332,826 individuals, including 85,804 Texans. Compromised data includes names, contact details, driver's license or state ID numbers, dates of birth, hearing and medical information, health-insurance IDs and information, and — for some individuals — Social Security numbers and financial information. Amplifon says it is not aware of fraudulent use. Severity: Not specified by source.

**Why it matters:** Health-benefit administrators hold deep PII and medical data on hearing-program members; the eight-month review gap shows how long internal-system intrusions can sit before regulators are notified.

**Action:** Affected individuals should review health-insurance and bank statements for fraud; organizations using Amplifon-administered programs should verify credential hygiene.

- Source: SecurityWeek, "Amplifon Americas Data Breach Impacts 1.3 Million," November 9, 2026, 06:20 AM ET — https://www.securityweek.com/amplifon-americas-data-breach-impacts-1-3-million/
- Source: Edelson Lechtzin LLP (PR Newswire), "Amplifon Data Breach Investigation," November 8, 2026 — https://www.prnewswire.com/news-releases/amplifon-data-breach-investigation-edelson-lechtzin-llp-probes-class-action-claims-after-social-security-numbers-and-health-records-are-exposed-302932752.html

## Defensive tools and advisories

**Google DeepMind: AI agents cut 1024-bit RSA attack costs 3.5x and ~30x** — Event date: November 6, 2026 (papers published Friday)

Working with cryptographers from UC San Diego and Inria, Google DeepMind published two working papers Friday showing human-guided AI agents can make legacy 1024-bit RSA attacks cheaper than previously assumed. First, on digital signatures: for PKCS#1 v1.5 padding (used by PKCS#11 signing interfaces on HSMs), DeepMind showed that once an attacker controls roughly half the signed block or more, padding offers no protection at all. In a sandboxed rerun of an earlier academic forgery attack on the same 1024-bit key, two agents — with about 20 approval messages from the researcher — finished in under four days using 391 CPU core-years versus 1,380 in the original study (3.5x cheaper). The attack requires roughly 4 billion signing queries made in advance and does not work against devices that hash messages internally; it also does not recover the private key (factoring the key still takes an estimated 500,000–1,000,000 core-years). The agents also found an algorithm that by theoretical estimates is ~1,600x better than the previous best method against 32-byte hashes. Second, on factoring many keys of the same size: the agents devised an approach that makes each additional 1024-bit key ~30x cheaper to factor on average, though it assumes an upfront computation as costly as factoring one key and has not been implemented. VP Pushmeet Kohli stressed that modern protocols using 2048-bit and larger keys remain secure, and warned that "we cannot treat cryptographic hardness as a fixed assumption."

**Why it matters:** Legacy 1024-bit RSA still lives in old HSMs, embedded devices, and expired-certificate chains. This is the clearest AI-assisted demonstration yet that legacy crypto parameters are eroding faster than migration schedules assume.

**Action:** Audit for any 1024-bit RSA usage (endpoints, HSM configurations, legacy TLS) and prioritize migration to 2048-bit or stronger.

- Source: SecurityWeek, "Google DeepMind's AI Agents Make 1024-Bit RSA Keys Cheaper to Attack," November 9, 2026, 03:35 AM ET — https://www.securityweek.com/google-deepminds-ai-agents-make-1024-bit-rsa-keys-cheaper-to-attack/
- Source: WebProNews, "Google DeepMind's AI Agents Find Two Weak Spots in Legacy RSA," November 7, 2026 — https://www.webpronews.com/deepmind-ai-agents-legacy-1024-bit-rsa-weaknesses/

**Wirken: open-source AI agent gateway released** — Event date: November 9, 2026

Help Net Security profiled Wirken (github.com/gebruder/wirken), a free open-source gateway that sits between messaging apps and AI agents on the assumption that any agent can be turned against its owner. It connects Signal, WhatsApp, Telegram, Matrix, Discord, Slack, Teams, Google Chat, and iMessage plus a web chat, running each channel as a separate process that holds only that channel's credentials. Credentials live in an encrypted vault supplied at the edge (the agent and model never handle them); shell commands run in an ephemeral container with no network access, and if the sandbox is unavailable execution is refused outright. Writes require approval the first time (remembered thereafter), while deleting, installing, or touching credentials always asks. Every action is written to a signed, hash-chained log verifiable offline. HNS notes remaining gaps: MCP server child processes and the LLM client make their own outbound connections, injection detection flags but allows suspicious input, and in a shared channel one person's approval covers every sender until it expires.

**Why it matters:** As AI-agent breaches mount (WorkOS, Northflank, and others this week), Wirken offers a concrete defensive pattern — process-isolated agents, vault-held credentials, and tamper-evident logs — for teams running agents inside messaging workflows.

**Action:** Evaluate for agent-governance environments; requires Docker for the sandbox (Linux/macOS installer; Windows 11 has a separate path with a reduced feature set).

- Source: Help Net Security, "Wirken: Open-source AI agent gateway," November 9, 2026 — https://www.helpnetsecurity.com/2026/11/09/wirken-open-source-ai-agent-gateway/

## Also noted

- **Ukraine dismantles €4.3M crypto investment-scam network** — Event date: November 9, 2026 (report). Ukrainian Cyber Police and the National Police's Main Investigation Department, with German law enforcement, arrested four suspects — including the leader — running pig-butchering call-center operations across Kyiv, Vinnytsia, Zaporizhzhia, and Ivano-Frankivsk regions. The ring stole more than €4.3 million from 135 German victims via fake crypto/shares investment platforms; searches in August and September seized over $90,000, ~600,000 hryvnia, hardware wallets, and equipment. The four face up to 12 years. Source: BleepingComputer, "Ukraine dismantles crypto scam network that stole €4.3 million," November 9, 2026, 07:16 AM ET — https://www.bleepingcomputer.com/news/security/ukraine-dismantles-crypto-scam-network-that-stole-43-million/ ; Mezha.net, November 9, 2026 — https://mezha.net/eng/news/mezha-be3dddddc8d37ebdf891ebf1_ukraine_arrests_four/
- **CISA KEV activity:** No KEV additions other than CVE-2026-19868 (covered above). The CISA advisories index shows no new joint advisories inside the window beyond the Friday refrigeration fact sheet already reported.

## Just missed the window

- **Iris Ohyama / Iris Plaza breach (7.7M people):** Japanese retailer Iris Plaza disclosed on October 29 that unauthorized access to a third-party cloud service may have exposed personal data; the November 5 update put the scale at ~7.7 million (5.8M members, 1.9M non-members, including passwords for some fields). BleepingComputer reported it Sunday, November 8, but the disclosure predates the window.
- **ForgeRock/Ping Identity 'admin:admin' automaker research:** Shubham Shah and Sam Curry published their work "late last week" (~November 3–5) showing that the Apache Felix admin console behind two car brands' apps — reachable via an edge/server path-encoding split — still used its default `admin:admin` credentials, allowing full account takeover and remote unlock/start of test vehicles; both US systems were fixed within about two hours of reporting. The Hacker News covered it Monday, November 9.
- **Acronis BOSSAI / Taipei emergency dispatch:** Acronis TRU research (~November 4) detailed a single Taiwan-based attacker running 12 AI agents ("BOSSAI") who pulled emergency dispatch records across seven Taipei districts and accessed a fire-department platform exposing 2018 patient ECG and ambulance footage without login. The Hacker News covered it Monday, November 9.

## Coverage notes

- **Verified items:** 10 (8 full items across the three categories + 2 also-noted entries). Every cited page was fetched and carries an in-window timestamp or an in-window filing/disclosure date.
- **Ledger dedupe:** 18 candidates suppressed as already reported — WorkOS ARTEX agent report (Nov 6), OpenAI GPT-6 Cyber launch (Nov 6), fake GitHub security-tool repos (Nov 6), QNAP Plex2Shell NAS warning (Nov 6), Veeam Software Appliance EPYC boot failures (Nov 6), Chainalysis all-user credentials update (Nov 6), D-Link DGS-3130 zero-days (Nov 6), MetaMask staking post-mortem (Nov 6), FBI/CISA refrigeration joint advisory (Nov 5), fake OpenAI/SpaceX event phishing (Nov 5), Crédit Mutuel Arkéa $133M heist (Nov 4–5, re-covered Nov 7–9), CISA Nov 6 KEV addition (Apple CVE-2024-27834, reported Nov 6 run), Japan ¥10B package (footnoted Nov 6), DigiCert breach (Nov 5), OpenSSH SSHole in-the-wild exploitation (Nov 6), White House residential-proxy study (Nov 4), Ahold Delhaize €250M financial toll (Nov 4), Trigger.dev env-var exposure (disclosed Nov 1).
- **Footnotes:** 3 "Just missed" entries (Iris Ohyama, ForgeRock automaker study, Acronis BOSSAI).
- **Sources/indexes used:** BleepingComputer river, The Hacker News river, SecurityWeek river, Help Net Security, CISA advisories/KEV index, Ransomware.live tracker, HN Algolia river, Reddit (netsec/sysadmin — older threads only; no new primary material beyond the SonicWall discussion), Apiiro (primary research), vendor PSIRT (SonicWall).
- **Limitations:** Ransomware.group attribution for the weekend pharma cluster is redacted in the tracker; HN Algolia yielded no high-signal stories in-window; no third-party verification of the Web3Auth "20% of accounts" figure beyond the Keplr co-founder's estimate.
