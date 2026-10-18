# The Perimeter — Daily Cybersecurity Brief

Automated briefing publication. Content repo + build-free static shell served by GitHub Pages.

- Site: https://shedstrom1.github.io/briefings/
- Content: `content/cybersec/latest.md` + dated `content/cybersec/archive/`
- Machine feed: `feed/manifest.json`

## How updates arrive
`C:/Users/sheds/AppData/Local/hermes/scripts/briefings_site/publish_brief.py cybersec <report.md>` —
invoked by the Cybersecurity News Briefing cron after each run. Best-effort; never blocks email.
More briefs can be added to the manifest feeds array when approved.
