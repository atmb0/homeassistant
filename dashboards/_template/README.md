# <Dashboard name>

One or two sentences: what it shows and who it is for.

## Views
- **<View>** – what is on it.

All sources are public. No credentials or private devices are included.

## Requirements
- Home Assistant version tested: <x.y>
- HACS cards: <list, with the version tested>
- Integrations: <list>
- Helpers: see [`helpers.yaml`](helpers.yaml) (remove if unused)
- Scripts: see [`scripts.yaml`](scripts.yaml) (remove if unused)

## Install
1. Install the HACS cards.
2. Create the helpers and scripts.
3. Settings → Dashboards → Add dashboard, open it, ⋮ → Raw configuration editor, and paste [`dashboard.yaml`](dashboard.yaml).
4. Replace the placeholder entity IDs (list them here) with your own.

## Notes
- Anything that may change or go offline (external URLs, video IDs).

## Troubleshooting
| Symptom | Cause / fix |
|---|---|

## Changelog
- **YYYY-MM-DD** – Initial dashboard.

## Publishing checklist
Check this before committing a new dashboard or changing one.

- [ ] No tokens, passwords, API keys, webhook IDs or long-lived access tokens.
- [ ] No internal IPs or hostnames, and no private URLs (Nabu Casa, reverse proxy, VPN).
- [ ] No private cameras, RTSP URLs or snapshot URLs.
- [ ] No names of people, addresses, home coordinates, zones or device trackers.
- [ ] Entity IDs are generic or documented as placeholders.
- [ ] Screenshots do not show any of the above (check the browser tab, sidebar and URL bar too).
- [ ] The commit author is the GitHub noreply address: `git config user.email "<id>+<user>@users.noreply.github.com"`.
