# Cybersecurity News Briefing — Monday, October 26, 2026 (17:00 Edition)

**Window:** 2026-10-26 12:00 → 17:21 EDT (previous fire 12:00; new items only, ledger-deduped)

**TL;DR**
- PHP has rebuilt all official Windows packages for PHP 8.2–8.5 to finally ship the fixed Brotli library — the second emergency Windows fix in five days for the Brotli flaw (CVE-2026-111497, aka Node2Shell/HTTP2Shell/Broccoli2Shell). Google rates it CVSS v4 9.2 Critical.
- UK pentester Jaydip Modhwadia ("Killa") claimed the $2,000 Bitcoin prize in James Pavur's PHP2Shell "Steal My Bitcoin" CTF, achieving RCE with a single-file Python script that needs at most 20 HTTP requests and bypasses ASLR.
- Ransomware leak sites added a fresh batch this afternoon: a new group's debut (the third new group in 10 days), a "36,000 machines / both AD forests" hospital-campus claim, energy firm MISC Group, Spain's Ingelectus, crypto casino Gamdom, and a 214 GB plaintiff-law-firm extraction.
- Just missed the window (footnoted below): South Korea announced its first arrest for generative-AI-driven large-scale hacking — a former pentester who used the ARTEX AI framework to breach ~90 organizations.

## Vulnerabilities and patches

**PHP issues second Windows security update in five days for critical Brotli flaw (CVE-2026-111497)** — *Update on previously reported* (ledger 2026-10-22, CVE-2026-111497-brotli)
**Event date:** Monday, October 26, 2026 (packages released; THN published 13:45 EDT)

PHP has rebuilt its official Windows packages — numbered 8.5.12-1, 8.4.27-1, 8.3.36-1 and 8.2.35-1 — to fix CVE-2026-111497, the out-of-bounds write in the bundled Brotli compression library. The rebuilt installers use the same PHP source as last week's October 21 PHP2Shell emergency fixes but now include the patched Brotli 1.2.1; PHP's own software bill of materials for last week's x64 Non Thread Safe builds of all four versions showed Brotli 1.2.0, an affected version. The flaw is reachable through the cURL extension when it automatically decompresses HTTP responses — off by default in cURL, but Guzzle (PHP's dominant HTTP client) sets the empty `CURLOPT_ENCODING` default that turns it on, so a stock Guzzle configuration meets the reachability condition. Severity as published: Google rates CVE-2026-111497 **CVSS v4 9.2 (Critical)**; Red Hat rates it **CVSS 3.1 8.1 (Important)**. There is no known in-the-wild exploitation (CISA's October 23 assessment recorded none, and it is not in KEV). Because the PHP version string is unchanged, `php -v` looks identical after patching — the `-1` build suffix in the filename and the downloads-page SHA-256 are the only tells.

**Why it matters:** This is the supply-chain tail of the "2Shell" week — even administrators who faithfully deployed last week's PHP2Shell emergency builds were still running a vulnerable decoder inside the official php.net binaries. It also extends the Brotli blast radius to the huge PHP-on-Windows hosting estate, via an unusual attack path: a malicious compressed *response* from a server the PHP application itself calls.

**Action:** Re-download and redeploy the `-1` builds, verify SHA-256, restart processes and rebuild container images. If you can't patch: disable automatic Brotli decompression in HTTP clients (in Guzzle, `decode_content => false`; simply removing `br` from `Accept-Encoding` "alone does not stop a server from sending it" per OpenAI, who discovered the flaw). PHP offers no workaround of its own.

- Source: The Hacker News, "PHP Issues Second Windows Security Update in Five Days to Fix Brotli Code Execution Flaw," Oct 26, 2026 (~13:45 EDT) — https://thehackernews.com/2026/10/php-issues-second-windows-security.html
- Source: BleepingComputer, "Critical 'HTTP2Shell' Brotli vulnerability triggers emergency patches," Oct 26, 2026, 12:27 EDT — https://www.bleepingcomputer.com/news/security/critical-http2shell-brotli-vulnerability-triggers-emergency-patches/

## Attacks and campaigns

**UK pentester "Killa" claims $2,000 Bitcoin prize in PHP2Shell "Steal My Bitcoin" capture-the-flag**
**Event date:** Monday, October 26, 2026 (win claimed and detailed by The Register at 16:14 UTC / 12:14 EDT)

Jaydip Modhwadia, a 26-year-old UK developer and pentester, claimed the Bitcoin bounty security researcher James Pavur set on his "Steal My Bitcoin" challenge site, which Pavur launched October 24 to test whether the PHP2Shell flaws (CVE-2026-109618/CVE-2026-109619) were as hard to weaponize as skeptics claimed. The site initially served only static HTML, making RCE impossible; after Pavur exposed a PHP endpoint at ~13:00 UTC Sunday, Modhwadia achieved RCE at 15:47 UTC — the site now reads "My Bitcoin was stolen after 19.28 hours," though the real working time was roughly three hours. His proof of concept is a single-file Python script he says needs "at most" 20 HTTP requests, and it bypasses ASLR, placing it in the advanced tier alongside VulnCheck's earlier weaponization. The community pushed the prize from Pavur's initial $500 to $2,000; Modhwadia donated part of it to the Lupus Foundation. Hudson Rock CTO Alon Gal's caveat: this proves PHP2Shell "can be weaponized against a live target, even with meaningful technical constraints," but is "a sophisticated, target-specific exploit," not scalable autonomous compromise.

**Why it matters:** In-window, independently verifiable evidence that a competent operator can turn PHP2Shell into reliable RCE against a hardened live target (Nginx 1 MB request limit, traffic floods) in about an hour of active work — closing out last week's "working PoC or not" debate with a controlled demonstration.

**Action:** None new beyond patching — treat "weaponizable" as demonstrated; prioritize any remaining unpatched PHP 8.2–8.5 hosts.

- Source: The Register (Connor Jones), "UK pentester 'killa' claims $2K Bitcoin prize in PHP2Shell capture-the-flag contest," Oct 26, 2026, 16:14 UTC — https://www.theregister.com/security/2026/10/26/uk-pentester-killa-claims-2k-bitcoin-prize-in-php2shell-capture-the-flag-contest/5305429 (original interview; later copies are syndication)

## Defensive tools and advisories

No new in-window releases or advisories. CISA's latest alert remains October 23 (KEV additions) and its most recent ICS advisories are the October 22 Siemens batch; no security-tool releases disclosed in this five-hour window.

## Also noted

- Ransomware leak-site additions this afternoon (all "Discovered: 2026-10-26", per ransomware.live): a **new group's debut** (~16:59 EDT — the third new group in the last 10 days), a claim of **"36,000 machines compromised, both AD forests (main campus + hospital sector)"** (~16:34 EDT), **MISC Group** (Singapore-based energy-logistics firm), **Ingelectus Innovative Electrical Solutions** (Spain; "live electrical telemetry sampled from the plant control network"), **Gamdom** (unregulated crypto casino, hot-wallet mention), and a **plaintiff litigation law firm** (214 GB extraction) — https://ransomware.live/ (group attributions not captured in the page scrape; treat group names as unconfirmed)

## Just missed the window

- **South Korea announces first arrest for generative-AI-driven mass hacking.** Seoul Metropolitan Police said Monday (KST morning — announced before this window opened) that seven suspects breached ~90 organizations since September 2025 using the **ARTEX AI pentesting framework** — the same autonomous framework in the attacks on South Korean banks attributed to a Chinese-speaking actor — stealing 8.2 million records (hospitals, academies, corporations, golf courses) and demanding 360 million won (~$270k) across 38 extortion targets; ringleader is a self-taught 32-year-old ex-pentester, arrested Oct 15, referred to prosecutors Oct 23. BleepingComputer's English write-up landed at 17:08 EDT (in-window coverage of an out-of-window disclosure). — https://www.bleepingcomputer.com/news/security/former-pentester-used-artex-ai-to-steal-82-million-data-records/ ; ChosunBiz, Oct 26, 12:21 KST — https://biz.chosun.com/en/en-society/2026/10/26/YVB6WVFWLZAMXGQG6QBXPU56JY/

## Coverage notes

- **Verified:** 3 reported items (2 lead stories read in full) + 1 "just missed" footnote; 11 pages fetched this run, all citable URLs fetched.
- **Ledger dedupe:** 13 candidates suppressed as already reported — Gheist (Dark Reading 14:01 EDT re-cover), LiteLLM master-key story (The Register re-cover), Magento 953-store campaign (THN + SecurityWeek re-covers of the 12:00 edition), uCreator spyware, M365 token-replay, Wasmtime flaws (repeat of a missed: item), industrial-vendor PHP2Shell advisories, PHP2Shell probes→shells (both 12:00/07:00 editions), Xint compaction, Dutch NFC-relay arrests (missed: repeat), AGENTS.md markdown hijack (missed: repeat), and this morning's ransomware.live batch.
- **Out of window, footnote budget already spent** (one "just missed" footnote ever, used on the Korea/ARTEX story): DreamHost forced email/FTP-SFTP password resets — BleepingComputer's first report was 15:26 EDT (in-window), but DreamHost's own status page disclosed the forced resets between Oct 23, 5:20 PM PDT and Oct 25, 9:32 PM PDT, so the event anchors outside; Arctic Wolf's Microsoft Teams IT-support-scam report (The Hacker News published 11:52 EDT — eight minutes before the window opened); UK Biobank's phased-reopening announcement (The Register, 08:38 EDT; CEO statement Friday); UK's £600M AI cyber-defense package (announced Oct 24 per GOV.UK).
- **Limitations/dead ends:** The Record's RSS endpoint returned 404; The Register's "Skynet mode" autonomous-agent item could not be date-verified inside the phase wall (dropped per two-strike rule); ransomware.live page scrape omitted group attributions for the afternoon batch; BleepingComputer has not heard back from DreamHost on breach scope.
