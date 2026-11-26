# Cybersecurity News Briefing — Flight Centre and Zodia Custody Breaches (Nov 26, 07:21–13:06 EST)

**Window:** Thursday, 2026-11-26, 07:21 → 13:06 EST (midday edition; new items only, deduped against the reported ledger). The window is frozen at fire time; later developments are not pulled in.

**TL;DR**
- **Flight Centre Travel Group** disclosed a breach of a third-party accommodation-booking system: a "significant number" of customer records may have been accessed, a small fraction including passport details. Flight bookings were unaffected; Mandiant is investigating.
- **Standard Chartered's crypto custodian Zodia Custody** confirmed a breach exposing client and employee contact data. It took its institutional B2B API offline as a precaution; no private keys, no unauthorized transactions.
- **No new in-window exploited-vulnerability disclosures.** The CISA 10-CVE KEV batch (Mitel MiCollab, Danfoss, Vates, Juniper) and the Drupal Fail2Install follow-on were already carried in the 07:20 edition and are suppressed as already reported.
- Ransomware leak sites showed a Nov 26 brand-bait wave from the Inception crew claiming GoDaddy, McDonald's, Mattel, Verisk and Paramount — all unverified self-posted claims.

---

## Vulnerabilities and patches

No new vulnerability or patch disclosure first announced inside this window survived the ledger dedupe gate. The items dominating the morning feeds — CISA's addition of 10 flaws to the Known Exploited Vulnerabilities catalog (five Mitel MiCollab issues, three Danfoss AK-SM 800A controller bugs, Vates Xen Orchestra and HPE Juniper Session Smart) and Drupal's second highly critical installer site-takeover flaw (CVE-2026-136716) — were all reported in the 07:20 edition earlier today and are not repeated here. The KEV catalog stood at 1,805 entries with no new in-window additions beyond that batch.

*Severity: not applicable — no in-window vulnerability item qualified.*

## Attacks and campaigns

**Flight Centre Travel Group discloses third-party booking-system breach**
*Event date: November 26, 2026 (ASX filing and public disclosure).*
Flight Centre Travel Group told the Australian Securities Exchange that "a significant number of customer records" may have been accessed by unknown persons following a cyberattack on a third-party accommodation-booking platform used by its Flight Centre, Travel Associates and Cruiseabout brands and some independent agents. The company could not yet say how many records were taken; its initial review found most contained only customer names, with a "very small percentage" including dates of birth, email addresses, limited passport details, phone numbers and, in rare cases, health or mobility information. Flight and corporate-travel bookings run on separate systems and were unaffected. Flight Centre has contained the incident, rotated keys and credentials, increased monitoring, and is working with the supplier and Mandiant.

*Why it matters:* Passport details, names and travel history form a durable identity profile that enables document fraud and highly convincing pre-trip phishing — and a shared supplier platform means one intrusion exposes data across multiple brands at once. *Action (source-stated):* customers should independently verify unexpected messages about their bookings and avoid clicking links or sharing personal or payment details in unsolicited requests. Severity: Not specified by source.
Source: The Register — "‘Significant number of customer records' pwned in Flight Centre Travel Group breach," published 2026-11-26 15:29 UTC (10:29 EST). https://www.theregister.com/security/2026/11/26/significant-number-of-customer-records-pwned-in-flight-centre-travel-group-breach/5312360
Source: Security Affairs — "Australia's Flight Centre Data Breach Exposes Customer Records and Passport Details," 2026-11-26. https://securityaffairs.com/204049/data-breach/australias-flight-centre-data-breach-exposes-customer-records-and-passport-details.html

**Standard Chartered's Zodia Custody confirms breach of client and employee data**
*Event date: November 26, 2026 (disclosure); intrusion detected November 23–24.*
Zodia Custody, the digital-asset custodian owned by Standard Chartered, confirmed that an unauthorized party accessed information about some clients and employees, including company details, business contact names and email addresses. CEO Richard Clark said the firm detected probing of its platform on Monday November 23, temporarily took its business-to-business API offline as a precaution, and added security measures before restoring service; by November 24 it had confirmed the data access. Zodia notified affected clients and regulators but did not disclose a victim count or the entry point. No private keys were exposed and the investigation found no unauthorized transactions. The disclosure comes weeks after Standard Chartered raised its stake in Zodia to 100%.

*Why it matters:* Even without fund movement, real employee names and business emails make impersonation and fake-support phishing credible against institutional clients — the same pattern seen in other crypto-adjacent contact-data breaches. *Action (source-stated):* firms using Zodia should verify any unexpected request to reconnect the API, share credentials or approve a transfer through an existing support contact rather than a link or number in the message. Severity: Not specified by source.
Source: CoinDesk — "Standard Chartered's Zodia Custody suffers data breach, exposing client information," 2026-11-26 07:38 EST. https://www.coindesk.com/tech/2026/11/26/standard-chartered-s-zodia-custody-suffers-data-breach-exposing-client-information
Source: Cyber Security News — "Standard Chartered's Zodia Custody Hacked, Client and Employee Data Exposed," 2026-11-26. https://cybersecuritynews.com/zodia-custody-data-breach/

**Also noted (leak-site roundup, unverified):** ransomware.live logged a Nov 26 wave of self-posted claims from the Inception crew naming GoDaddy, McDonald's, Mattel, Verisk and Paramount, plus aurora (Arlberger Bergbahnen, AIM Consulting), qilin (Sweet Darling Sales) and rhysida (Jackson District Library, Koch Law Firm). These are unconfirmed actor claims, not verified intrusions; Inception has been linked in reporting to a ShinyHunters-affiliated vishing campaign. Overall site activity was down — 54 victims in 24 hours (−29%). Source: ransomware.live, accessed 2026-11-26. https://ransomware.live/

## Defensive tools and advisories

No defensive-tool release was first disclosed inside the window and verified from a primary source. A Help Net Security item on Google Workspace agent controls was a re-report of a November 24 Google announcement (below).

## Just missed the window

- **Google Workspace MCP/CLI mandate for AI agents** — Google made its Workspace MCP servers and CLI generally available and set February 1, 2027 as the date agents must reach Workspace through those gateways; announced Nov 24, re-reported Nov 26.
- **Remus stealer's "Gmail zero-day"** — GenDigital and Invoke RE showed the claimed zero-day is a fake "Google Sheets" Chrome extension; research published Nov 25, BleepingComputer Nov 26 10:23 EST.
- **GoodGuys phishing campaign** — Push Security's teardown of the paid-interview lure targeting Google/Okta corporate logins was published Nov 20; BleepingComputer Nov 26 12:15 EST.
- **Keenadu preinstalled Android backdoor** — Kaspersky's finding of 220,000+ infected devices was reported Nov 25; Help Net Security Nov 26.
- **Iceland's 1984.is hosting outage** — targeted attack began Nov 21 with investigation findings Nov 25; restoration update Nov 26.

## Coverage notes

Verified and reported: 2 new in-window items (both breaches). Sources fetched: The Register, Security Affairs, CoinDesk, Cyber Security News, BleepingComputer, The Hacker News, Help Net Security, SecurityWeek, CyberInsider, Security Affairs, CISA KEV, ransomware.live, HN river. Community lanes: HN river walked; Reddit r/netsec JSON returned a CAPTCHA wall (blocked). Ledger dedupe suppressed a high volume of candidates as already reported — principally the CISA 10-KEV batch, Drupal CVE-2026-136716, 1Password, FinCEN, FIFA, Rackspace Spot, Peninsula Energy, SYS010, NTLM-signing break, Bithumb 15-day update, and the GoodGuys/Remus/PolinRider/Keenadu/Google-MCP re-covers counted above. No "Just missed" footnote here has appeared before. This is a thin midday window; the brief is not padded. Web search returned intermittent backend errors; index pages were fetched directly as the fallback path.
