# Lisbon & Porto live cameras dashboard

A Home Assistant dashboard with public live cameras and traffic images for Lisbon and Porto (Portugal).

## Views
- **Live** – Lisbon live stream, Tagus River at Algés, Lisbon Airport (YouTube).
- **Traffic** – 25 de Abril and Vasco da Gama bridges (Windy + Lusoponte still images, auto-refreshing).
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
- The Lisbon Airport card is an `iframe` pointing at `youtube.com/embed/live_stream?channel=<channel id>` (AVIAÇÃO TV). That URL always resolves to the channel's current live, so the daily-changing video ID no longer needs updating. If the channel disables embedding, the card shows a YouTube error.
- Video IDs and image URLs belong to their respective owners (YouTube channels, Windy, Lusoponte) and may change or go offline.

## License
MIT
