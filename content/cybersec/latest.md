# Cybersecurity News Briefing — Friday, October 30, 2026 (12:00 ET Edition)

**Window:** 2026-10-30 07:20 → 12:20 EDT (since the 07:20 edition). New items only, ledger-deduped.

## TL;DR

- **WordPress Core upgrade-window RCE under mass attack:** Wordfence blocked 62,969 exploitation attempts in 24 hours against the unauthenticated race condition in the core update process — patched October 28 in 7.1.4, but attackers are polling the staged installer path to catch sites mid-upgrade.
- **Trezor warns of suspected cellular hardware implant in a Safe 5 sold by CryptoBilis:** an extra circuit board between mainboard and screen reads the recovery phrase off the display and exfiltrates it over a mobile network; every buyer since March 2026 is told to move funds to a new seed.
- **Shelly Store UK (Shelly smart-home reseller) warns payment data was stolen** after malware (Stripe alert) was found on its website; one customer reported a fraudulent £221.28 charge in Australia hours before the notification.
- **Ransomware leak sites quiet in-window:** no new victim posts discovered between 07:20 and 12:20 EDT; a small pre-dawn batch is logged below.

## Vulnerabilities and patches

**WordPress Core upgrade-window RCE draws 62,969 exploit attempts in 24 hours** — event date: attempts observed through October 30 (flaw patched October 28; CVSS 8.1 High per Wordfence, no CVE assigned)

Threat actors are actively attempting to exploit a remote-code-execution flaw in WordPress Core versions 4.7 through 7.1.3, fixed on October 28 in the 7.1.4 security release (22 fixes total). The bug is a race condition: full-package core updates unpack into a predictable, web-accessible staging directory under `wp-content/upgrade`, including the WordPress installer, so an unauthenticated attacker who requests the staged installer at the right moment can reach PHP code execution. Wordfence says its firewall blocked 62,969 attempts in the past 24 hours, with two of the largest sources repeatedly polling small groups of sites for roughly 14.5 hours — behavior consistent with deliberately trying to hit the brief upgrade window — while other sources fuzzed staging-directory and version-name variations. The first probe arrived nine minutes after Wordfence's blocking rule deployed; free Wordfence users don't get the rule until November 27.

**Why it matters:** WordPress powers a huge share of the web, and this is a rare case where the patch itself is the trigger moment — the vulnerable window opens precisely when a site runs its core update, so unpatched sites re-enter the danger zone every time an upgrade fires, and attackers have clearly reverse-engineered the race in near-real time.

**Action:** confirm the site actually updated to 7.1.4 (or the corresponding branch security release: backports reach back to 4.7.39), enable Extended Protection so the WAF runs before direct PHP requests, audit for unfamiliar admin accounts, plugins, and stray PHP under `wp-content/upgrade`; one commenter's hardening tip is worth weighing — deny HTTP access to `/wp-content/upgrade/` at the server/CDN layer before initiating the update.

Sources:
- The Hacker News — "WordPress RCE Flaw Draws Nearly 63,000 Attack Attempts in Just 24 Hours" (Oct 30, 2026) — https://thehackernews.com/2026/10/wordpress-rce-flaw-draws-nearly-63000.html
- Wordfence — "WordPress 7.1.4 Patches Unauthenticated Upgrade-Window RCE; Exploitation Attempts Detected After Release" (Oct 29, 2026) — https://www.wordfence.com/blog/2026/10/wordpress-7-1-4-security-release-rce/

## Attacks and campaigns

**Trezor: suspected cellular spy implant found in a Safe 5 sold by reseller CryptoBilis** — event date: October 30, 2026 (Trezor security team received the responsible-disclosure report October 28)

A Trezor customer in Indonesia reported an extra circuit board fitted inside his Safe 5 — bought in August from CryptoBilis via Tokopedia — positioned between the main board and the screen, complete with a cellular module and antenna; per the report it reads everything shown on the screen, including the wallet recovery phrase, and transmits it over a mobile network whenever the device is powered. Trezor has not yet received the device but is treating the report as credible based on photos and a video interview, and is telling everyone who bought a Trezor from CryptoBilis since March 2026 to move funds to a brand-new wallet with a new seed (restoring the old backup elsewhere does not help). No fund losses have been reported; affected buyers get a replacement device or voucher. Context: Ledger had already confirmed tampered devices from the same reseller on October 9, at which point Trezor de-authorized CryptoBilis — this is the first credible implant report on Trezor hardware itself.

**Why it matters:** screen-reading, cellular-exfiltrating implants in supply-chained hardware wallets are the class of attack self-custody was supposed to eliminate, and the same distributor has now surfaced in both Ledger's and Trezor's investigations — reseller-channel tampering is an active, escalating campaign, not a one-off.

**Action:** if you bought any Trezor model from CryptoBilis (including Tokopedia) since March 2026: move funds to a new wallet with a new seed immediately, don't set up an unused unit, don't trust weight-checks or DIY teardowns, contact Trezor Support for a replacement, and expect follow-on phishing exploiting the news.

Sources:
- Trezor Blog — "Implant reported in a Trezor sold by CryptoBilis" (Oct 30, 2026) — https://trezor.io/blog/news/implant-reported-in-a-trezor-sold-by-cryptobilis
- The Hacker News — "Trezor Warns of Suspected Hardware Implant in One of Its Wallets Sold by CryptoBilis" (Oct 30, 2026) — https://thehackernews.com/2026/10/trezor-warns-of-suspected-hardware.html
- BleepingComputer — "Trezor warns of suspected crypto-theft implant in hardware wallet" (Oct 30, 2026, 11:58 AM) — https://www.bleepingcomputer.com/news/security/trezor-warns-of-suspected-crypto-theft-implant-in-hardware-wallet/

**UK Shelly smart-home reseller warns payment data stolen after website malware** — event date: October 30, 2026 (Register exclusive; customer charge dated October 26)

Shelly Store UK, an independent authorized reseller, emailed customers that after a warning from payment provider Stripe it found "unauthorized software" on its website and server, and cannot rule out that payment card details, names, billing addresses, and contact info were accessed or copied. One customer launched a card dispute over a £221.28 fraudulent transaction that was charged in Australia on October 26 — hours before receiving the breach notice — and suspects card capture between an October 12 order and the October 27 notification. Shelly Group stressed the reseller is wholly independent of the manufacturer.

**Why it matters:** classic e-commerce skimming with a slow-drip notification — fraud preceded the warning — and a reminder that vendor-authorized resellers are soft targets that the brand itself neither audits nor protects.

**Action:** if you ordered from Shelly Store UK, watch card statements, request a card reissue, and reset the site password; treat any "refund" outreach as suspect.

Source:
- The Register — "UK Shelly reseller warns payment data stolen after malware found on website" (Oct 30, 2026) — https://www.theregister.com/cyber-crime/2026/10/30/uk-shelly-reseller-warns-payment-data-stolen-after-malware-found-on-website/5306250

## Defensive tools and advisories

No new tool releases or advisories verified in this window. CISA published no KEV additions or ICS advisories today as of fire time (latest alert remains the Oct 29 KEV pair — GitLab CVE-2026-97229 and Cisco ASA/FTD CVE-2026-76418 — both already reported).

## Also noted

- **Pre-dawn ransomware leak adds (first coverage on this brief; discovered ~00:30–02:33 EDT, before this window):** Rapid Manufacturing (Anaheim, CA electronics contract manufacturer), FrenchCreek Fall Safety, Phillips Nizer LLP (law firm; attack est. Oct 11), Hudson & Hudson Sign Products (Houston), Service One Transportation (WI trucking/logistics), and CP Manufacturing (San Diego waste-sorting equipment). Groups unconfirmed at scrape time; nothing new was posted 07:20–12:20 EDT (source: ransomware.live, Oct 30).

## Just missed the window

- **GCHQ cannot account for thousands of lost storage devices** (missed: once) — the IPCO annual report published **Thursday Oct 29** disclosed a "very significant number" of USB sticks, external drives, CDs and memory cards unaccounted for at Britain's signals-intelligence agency, possibly holding Investigatory Powers Act material; The Register's write-up landed this morning. (https://www.theregister.com/security/2026/10/30/british-intelligence-agency-misplaced-thousands-of-storage-devices-years-later-still-cant-say-how-many/5306605; https://therecord.media/gchq-cannot-account-for-thousands-of-data-devices)
- **Anthropic-themed phishing slips past Proofpoint SEG and Microsoft Defender for Office 365** (missed: once) — Dark Reading's test of deliberately obvious phishing lures beating leading gateways published **Oct 29**. (https://www.darkreading.com/cyberattacks-data-breaches/anthropic-phishing-emails-bypass-security-tools)

## Coverage notes

- **Verified this run:** 4 fully-read/fetched items published (WordPress ×2 sources, Trezor ×3 sources, Shelly UK), plus 1 also-noted leak batch and 2 "just missed" footnotes. Discovery across 8+ outlets: The Hacker News, BleepingComputer, SecurityWeek, The Register, Dark Reading, Wordfence, Trezor blog, ransomware.live/RansomNews, CISA.
- **Ledger dedupe:** ~15 candidates suppressed as already reported — Binance/TraderTraitor, VAPOR PANDA ARTEX, Cursor Marketplace trojan (incl. BC's 10:36 AM re-cover), Polymarket/DebugBear, Hugging Face miner, AOVPN fix, TeamViewer CVE-2026-111585 (THN re-cover), Drupal CVE-2026-118539 (BC 06:16 re-cover), Agha sentencing (THN re-cover), GitLab CVE-2026-97229 KEV (THN + BC 09:00 + SW triple re-cover), "second Cisco VPN zero-day" (= already-reported CVE-2026-76418), SW's PAN-OS/PHP2Shell firewall-rooted piece (already-reported panos-mgmt, and pre-window anyway).
- **Age-rejected, not ledger matches:** Proofpoint–Varonis $6.3B (announced Oct 15), Oracle Health ~20M tally (Oct 8), SW "In Other News" roundup (~Oct 29 20:30 EDT; its UEFI/EDK2 item is already ledgered as missed).
- **Dead ends / limits:** CISA alerts index 404s (advisories index used instead); ransomware.live homepage served a stale cached copy (API used as ground truth); the overnight leak batch's group attributions weren't exposed by the API at scrape time; several pre-window leak items (e.g. rolly.io) belonged to earlier runs' windows and were not re-listed per new-items-only. No defensive-tool releases surfaced in the 5-hour Friday-morning window.
