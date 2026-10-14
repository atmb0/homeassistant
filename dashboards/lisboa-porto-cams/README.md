# Lisbon & Porto live cameras dashboard

A Home Assistant dashboard with public live cameras and traffic images for Lisbon and Porto (Portugal).

## Views
- **Live** – Lisbon live stream, Tagus River at Algés, Lisbon Airport (YouTube).
- **Traffic** – 25 de Abril and Vasco da Gama bridges (Windy + Lusoponte still images, auto-refreshing). The Windy images show a "Captured HH:MM (n min ago)" caption read from the image's `Last-Modified` header in the browser (needs `custom:button-card`); Lusoponte images have the time printed on them.
- **Porto** – Porto live stream (YouTube) and links to traffic platforms.

All sources are public. No credentials or private cameras are included.

## Requirements
- `youtube-video-card` custom card (install via HACS; the config uses `custom:youtube-video-card`).
- The `input_boolean.lisboa_live_refresh` helper – see [`helper.yaml`](helper.yaml).

## Install
1. Create the helper (`helper.yaml`, or via the UI).
2. Settings → Dashboards → Add dashboard (e.g. `lisboa-live`), open it, ⋮ → Raw configuration editor, and paste the contents of [`dashboard.yaml`](dashboard.yaml).

## Notes
- The Cacilhas camera YouTube broadcast ended on 2026-09-01; swap the video ID when a new one exists.
- The Lisbon Airport live changes ID every day (AVIAÇÃO TV starts a new live daily). An `iframe` embed fails with YouTube error 153, and `youtube-video-card` cannot take a template. So the card is a `custom:button-card` (HACS) that draws the player iframe itself with `referrerpolicy="strict-origin-when-cross-origin"` (the fix for error 153) and the video ID from `sensor.lisbon_airport_live_id`.
- Create that sensor with the **Scrape** integration (Settings → Devices & services → Add integration → Scrape): resource `https://www.youtube.com/@AVIA%C3%87%C3%83OTV/live`, headers `Accept-Language: en` and `Cookie: CONSENT=YES+1; SOCS=CAI`, then add a sensor named `Lisbon Airport live ID` with selector `link[rel=canonical]`, attribute `href` and value template `{{ value.split('v=')[-1] if 'v=' in value else 'unknown' }}`.
- [`script-tv-aeroporto.yaml`](script-tv-aeroporto.yaml) is an optional script that plays the same live on a TV.
- Video IDs and image URLs belong to their respective owners (YouTube channels, Windy, Lusoponte) and may change or go offline.

## License
MIT
