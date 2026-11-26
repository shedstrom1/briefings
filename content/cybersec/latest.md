# Cybersecurity News Briefing — November 26, 2026 (Afternoon Edition)

**Window:** 2026-11-26 13:06 → 17:22 EST (fired 17:20; previous fire 13:06). New-items-only — the event date, not article freshness, governs inclusion.

**TL;DR**
- **Cacti zero-day, no patch:** Researcher "ek0ms savi0r" published 4HTTP2ROOT, an unauthenticated four-request RCE chain against Cacti Core 1.2.31, with a working PoC. No CVE, no vendor fix; disable guest access now.
- **Binance finishes recovery:** Binance says withdrawal recovery is complete and the suspension imposed after October's record $6.34B heist is fully lifted, 25 days after phased reopenings began. Update on a previously reported event.
- **Thin afternoon:** BleepingComputer published no new security stories after 12:58 PM, CISA posted no new KEV entries or advisories, and most index coverage was re-reporting of events disclosed earlier in the day.

## Vulnerabilities and patches

**Cacti 4HTTP2ROOT: four-request unauthenticated RCE zero-day, PoC public, no fix**
Event date: 2026-11-26 (PoC published; watchTowr FAQ 15:09 EST, SecurityOnline.info 16:24 EST).
Severity: Not specified by source — no CVE assigned; watchTowr's tracker shows CVSS 0.0 (v3.1), and no vendor advisory or patch exists yet.

Researcher "ek0ms savi0r" published the 4HTTP2ROOT chain on GitHub on November 26, demonstrating four unauthenticated HTTP requests that end in command execution on a Cacti Core 1.2.31 server. The chain stores a crafted string in the guest-accessible `preview_graphs_per_page` profile setting, which Cacti later concatenates into a SQL LIMIT clause; because the PDO driver accepts stacked queries, an injected UPDATE rewrites a `poller_item` row, and the guest-reachable `graph_realtime.php` endpoint hands that row to `popen()`. No memory corruption or deserialization gadget is involved — the write is a prepared statement, but the consumer is not. In the lab the web tier ran as root and produced a root shell; normal deployments run as `www-data`/`apache`, but a Cacti host holds SNMP strings, device logins and database credentials, making a foothold a pivot into the monitoring layer. No in-the-wild exploitation has been reported.

Why it matters: Cacti sits in the monitoring layer, trusted by network devices everywhere and often unaudited; a shell on it exposes stored credentials and lets an attacker suppress alerts. The requests resemble ordinary guest traffic and leave only a PHP non-numeric-value warning in logs.
Action: Disable the Cacti guest account to break the chain, restrict the web UI to a trusted network/VPN, audit `settings_user` and `poller_item` for anomalous strings or commands, ensure the web server/poller never runs as root, and watch PHP logs for non-numeric-value warnings on `graph_view.php`.
Sources:
- watchTowr, "4HTTP2ROOT FAQ: Cacti Zero-Day Exposure and Fix" (Nov 26, 20:09 UTC) — https://watchtowr.com/intelligence/4http2root-cacti-zero-day-faq/
- Daily CyberSecurity / SecurityOnline.info, "Cacti Unauthenticated RCE Zero-Day (4HTTP2ROOT): PoC Published" (Nov 26, 21:24 UTC) — https://securityonline.info/cacti-zero-day-rce-4http2root-poc/
- Researcher's PoC and technical notes — https://github.com/ekomsSavior/Sad-Girls-0day-Club/blob/main/4http2shell/README.md

## Attacks and campaigns

**Binance fully lifts withdrawal suspension; recovery complete after $6.34B heist (update on previously reported)**
Event date: 2026-11-26 (in-window coverage: Reuters 2:55 PM ET, crypto.news 2:24 PM ET).
Severity: Not applicable — a recovery milestone, not a vulnerability.

Binance said its withdrawal recovery work is complete and the suspension imposed after the October security incident has been fully lifted, ending a phased reopening that began November 1 and took 25 days. Withdrawals are open across nearly all networks; a small number remain paused for routine maintenance or planned upgrades, which Binance says affects a very small share of regular traffic. The exchange says it replaced affected assets using more than $6.34B in company funds drawn from its treasury, part of the Secure Asset Fund for Users and part of the futures insurance fund, and published a Proof-of-Reserves snapshot covering 198 tokens taken at 00:00 UTC November 12. Onchain Lens tracked a further $35.52M in ether moves from hack-linked wallets on November 26, with roughly $1.9B still unfrozen; the DOJ's separate civil forfeiture action seeks $1.1B in USDC. Attribution of the original heist to North Korea's TraderTraitor was previously reported.

Why it matters: It closes the operational aftermath of the largest crypto theft on record, restoring full service after nearly a month of restrictions — but hack-linked wallets remain active.
Action: Binance advises users to check the network status page per asset/network before withdrawing and to stay alert for withdrawal-themed phishing; the exchange will never ask for passwords, 2FA codes or seed phrases.
Sources:
- Reuters, "Binance says it has finished its recovery work and fully lifted its withdrawal suspension" (Nov 26, 2:55 PM ET) — https://www.reuters.com/technology/binance-lifts-withdrawal-suspension-after-63-billion-crypto-heist-2026-11-26/
- Binance, "Service Recovery Update: Withdrawals Recovery Work Complete" (Nov 26) — https://www.binance.com/en/support/announcement/detail/95c463396a034f1ab3994814225e6d69
- crypto.news, "Binance lifts withdrawal suspension after $6.34B hack" (Nov 26, 19:24 UTC) — https://crypto.news/binance-lifts-withdrawal-suspension-after-hack/

## Defensive tools and advisories

No new defensive-tool release or advisory was first disclosed inside the window. CISA's advisories page showed no additions after the November 25 ten-KEV batch (already reported), and no new CISA alert or ICS advisory appeared during the window.

## Also noted

None in-window.

## Just missed the window

- A Florida man was sentenced to 47 months for using AI to clone the voice of Kick streamer N3on in a ~$260,000 memecoin-investor fraud (DOJ, Nov 24; re-surfaced in the Techmeme river Nov 26, 5:10 PM ET). The sentencing predates the window — footnote only, not counted. Source: https://www.justice.gov/usao-sdfl/pr/port-st-lucie-man-sentenced-47-months-prison-using-ai-impersonate-popular-streamer

## Coverage notes
- Verified in-window items: 2 (one new disclosure, one update on a previously reported event), plus 1 "just missed the window" footnote. A thin Thanksgiving afternoon — no padding added.
- Ledger dedupe: roughly two dozen candidates suppressed as already reported, including Drupal CVE-2026-136716, the CISA ten-KEV batch, 1Password, Zodia Custody, Flight Centre, SYS010, Remus, Juniper CVE-2026-103406, FinCEN, FIFA, Google Workspace MCP mandate, Keenadu, GoodGuys phishing, GeoServer, Hex.pm, GT13 Check Point, and the Inception leak-site brand-bait wave.
- Retrieval notes: Reddit's anonymous Atom lanes returned HTTP 404 for both r/netsec and r/cybersecurity (logged as unavailable, not a negative finding); HN Algolia was reachable but carried no security stories in-window; web_search (DuckDuckGo) failed intermittently; Reuters full text was paywalled (verified via the Techmeme ET river timestamp, Binance's primary notice and crypto.news). CISA advisories and BleepingComputer's feed had no in-window security additions.
