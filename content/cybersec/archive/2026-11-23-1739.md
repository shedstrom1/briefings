# The Cybersecurity News Briefing — Monday, Nov 23, 2026

**Window:** Monday, November 23, 2026, 12:00 ET → 17:20 ET (UTC-05:00) — previous fire → now. New items only; items already ledgered by earlier editions are excluded.

**TL;DR**
- Microsoft closed an exploited Azure API Management (APIM) flaw after attackers raided Classic-tier instances for secrets — Norway's National Security Authority ties the attack to its energy-sector incidents (Equinor + Skagerak Energi) 1–2 days before the fix; rotate every Classic-tier secret.
- The Gentlemen ransomware group posts a ~12-victim evening batch (HT Global Circuits, Cimmino, New Teeth Now, Nuova PSC, Mechanix Wear, Stadtwerke Achim AG, University Goce Delcev At Stip, Groupe Gagneraud, TC Industries, Arendal, Steinecke, Berkeley Orinda Oral Surgery) — 75 victims in 24h vs 14 yesterday.
- Check Point Research: record 2,830 ransomware victims claimed in Q3 2026 (117 brands, up from 91), ten largest groups only 49% of victims, average ransom down ~45% to a hair over $1M.
- Negative check: CISA KEV still at 1,786 entries (last addition Nov 18); no fresh KEV additions or new exploited-CVE advisories in this window.

## Vulnerabilities and patches

**Azure API Management Classic flaw exploited — secrets at risk (update on `norway-energy-double-breach`, missed Nov 17).** Microsoft identified the flaw on Nov 18 and auto-applied a mitigation on Nov 19; customers exposed to the path should treat secrets and named values in Classic-tier instances as compromised, rotate all tokens and secrets, and check which data is reachable through each APIM instance. The register note: exposure affects Classic tiers with non-recommended network security guidance, not v2 tiers; NSM credits Norway's energy sector with early detection.
- Event date: Nov 17–18 disclosure (NSM advisory Nov 20; Microsoft advisory Nov 18/19)
- Severity: not disclosed by source (Microsoft did not publish a CVE/CVSS)
- Why it matters: An attacker with listable APIM secrets (client secrets, subscription keys, direct-management-API tokens) can pivot outside APIM with whatever those credentials reach — Anders Kristiansen warns stolen credentials may be usable against the service they protect, not just the gateway. NSM's advice applies to all Norway, and suspects the exposure is broader.
- Action: check every Classic-tier instance against NSM's patch thresholds, rotate named values/client secrets/subscription keys, review specifically which APIM instances use app-specific secrets stored in APIM rather than Key Vault; report unpatched instances to the provider.
- Source: the Register, Nov 23, 2026 (12:29 UTC) — https://www.theregister.com/security/2026/11/23/norway-links-energy-firm-cyberattacks-to-critical-azure-api-management-flaw/5311598
- Source: SecurityWeek, Nov 23, 2026 (10:47 AM ET) — https://www.securityweek.com/azure-flaw-exploited-to-target-secrets-at-norwegian-energy-firms/
- Source: SQ Magazine, Nov 23, 2026 — https://sqmagazine.co.uk/azure-api-flaw-exploited-norway-energy/

## Attacks and campaigns

**The Gentlemen ransomware — 12 new evening listings (Nov 23, 16:00–17:20 ET).** Ransomnews' live index shows HT Global Circuits (US, St. Petersburg, ~$70.4M revenue, PCB maker), Cimmino (IT, Naples, fabric distributor), New Teeth Now (US dental implant center, 2420 files over 707k), Nuova PSC (IT, Milan), Mechanix Wear (US glove maker, 630GB), Stadtwerke Achim AG (municipal energy utility, DE), University Goce Delcev At Stip (MK state university), Groupe Gagneraud (FR construction, d.1880), TC Industries (CA, d.1881, ~$242.9M), Arendal (MX EPC contractor, ~$751.1M), Steinecke (DE family bakery, d.1945) and Berkeley Orinda Oral Surgery (US) listed in the past ~2 hours. All are unverified operator claims.
- Event date: Nov 23, 2026 (listings published ~16:00–17:20 ET)
- Severity: not specified (operator claims, no public ransom demand)
- Why it matters: A $70M-class PCB maker and a university are both in the same batch under the same operator that posted 12 victims yesterday and quietly hundreds per quarter — team-level attribution to US-geographic clusters is consistent with prior months' pattern (The Gentlemen 124 in 30 days, just one of 74 operators posting last month).
- Action: no action required for non-listed parties; if your org appears in the listing, treat claims with caution and validate independently before paying or engaging the operator.
- Source: Ransomnews Ransomtracker, live index, updated Nov 23, 2026 21:51 UTC — https://ransomnews.com/ransomware-attacks-today/

## Defensive tools and advisories

**Check Point Research — record 2,830 ransomware victims claimed in Q3 2026.** Q3 saw a record 2,830 victims claimed by 117 brands (up from 91 in Q2); the ten largest groups accounted for just 49% of victims, and average ransom payments fell ~45% to slightly over $1M. Also flagging a 28% weekly-attack increase vs consumer goods/services ahead of Black Friday, with 4,000+ newly registered sales-related domains plus fake retail storefronts and delivery phishing.
- Event date: Nov 23, 2026 (report published)
- Severity: not specified
- Why it matters: High victim count with falling per-victim payments signals wider, cheaper attacks rather than a handful of high-value targets; boards should not read it as "less profitable, therefore less dangerous" — the Q3 source class is more heterogeneous, not smaller.
- Action: feed-sector-specific fraud and insider-risk models planned against this data; ransom-negotiation postures unchanged; keep backup and validation of payment-vs-leak discipline for orgs in the high-listings cohorts (financial, pharma/biotech, small utilities).
- Source: Check Point Research, Nov 23, 2026 — https://research.checkpoint.com/2026/23rd-november-threat-intelligence-report/

## Also noted
- The Register lists follow-up reporting on the Microsoft 365 mega outage: internal testing-gap and email-delivery-failure post-mortem; status of remediation and full root cause remain unpublished. Not newly confirmed as a security event; no cyberattack evidence has been noted.
- Negative check: KEV version unchanged at 1,786 entries (last additions Nov 18); no fresh KEV additions this window. Cisco Expressway CVE-2026-76466 (KEV, due Nov 23) remains on the same date.

## Just missed the window
- None added this fire.

## Coverage notes
- **Verified:** 1 vuln/advisory item (Azure APIM, update on missed event), 1 attack-campaign listing item (Gentlemen evening batch), 1 defensive-research item (Check Point Q3). 3 counted items total; no padding.
- **Ledger dedupe:** ~12 candidates suppressed or footnoted by earlier editions this week — monitor: MONAI/Cairn CVE-2026-133968 (ledgered 07:20), FBI NCIC (ledgered 07:20), CrowdStrike↔Zscaler (ledgered 12:20), ENTR-/Entra-Connect (still unmodeled, but out-of-window Nov 19–22 and not in any prior ledger line — suppressed), Cleverbridge card-CVV breach (out-of-window; notifications Nov 20, no ledger entry — suppressed), Ghostscript CVE-2026-125396 Upload2Shell (published Nov 21–22, out-of-window), OpenRouter/Dragos AI-inference prompt-theft (published Nov 22, out-of-window), Signal iOS/Android AI-blocks (published Nov 22 evening, out-of-window), SBC Medical 2M (ledgered as 1.6M missed Nov 18, no new in-window event), Cisco Expressway KEV addition (ledgered Nov 18–19).
- **Dead ends / caveats:** Web search was intermittently unreliable today — multiple queries returned zero results; all items above were verified directly against the cited URLs and the live ransomnews index.
- **Indexes checked:** HN front (no in-window security headlines), ransomnews live index, SecurityWeek/Register/Check Point/BleepingComputer-linked sources. No index was down.
