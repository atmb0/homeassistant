# Lisbon & Porto live cameras dashboard

A Home Assistant dashboard with public live cameras and traffic images for Lisbon and Porto (Portugal).

## Views
- **Live** – Lisbon live stream, Tagus River at Algés, Lisbon Airport (YouTube), and a live air-traffic map of Lisbon (Flightradar24 integration card) with a button that opens Flightradar24.
- **Traffic** – 25 de Abril and Vasco da Gama bridges (Windy + Lusoponte still images, auto-refreshing). The Windy images show a "Captured HH:MM (n min ago)" caption read from the image's `Last-Modified` header in the browser (needs `custom:button-card`); Lusoponte images have the time printed on them.
- **Porto** – Porto live stream (YouTube) and links to traffic platforms.

All sources are public. No credentials or private cameras are included.

## Requirements
- `youtube-video-card` custom card (install via HACS; used for the Lisbon, Algés and Porto streams).
- `button-card` custom card (HACS; used for the airport player and the Windy capture-time captions).
- The [Flightradar24 integration](https://github.com/AlexandrErohin/home-assistant-flightradar24) (HACS), which ships `custom:flightradar24-card`, configured for Lisbon – see [Air-traffic map](#air-traffic-map).
- The `input_boolean.lisboa_live_refresh` helper – see [`helpers.yaml`](helpers.yaml).
- `sensor.lisbon_airport_live_id`, created with the Scrape integration – see Notes below.

## Install
1. Install the HACS cards: `youtube-video-card` and `button-card`.
2. Create the helper (`helpers.yaml`, or via the UI): `input_boolean.lisboa_live_refresh`.
3. Create the airport sensor (see [Airport live sensor](#airport-live-sensor)).
4. Settings → Dashboards → Add dashboard (e.g. `lisboa-live`), open it, ⋮ → Raw configuration editor, and paste the contents of [`dashboard.yaml`](dashboard.yaml).
5. Optional: create the TV script from [`scripts.yaml`](scripts.yaml) (adapt the `media_player` entity) and expose it to your voice assistant.

## Airport live sensor
The AVIAÇÃO TV channel starts a new live with a new video ID every day, so a fixed ID goes stale. Its `/live` page always redirects to the current live, and the page's canonical link contains the ID.

Create the sensor with the **Scrape** integration (Settings → Devices & services → Add integration → Scrape):

| Field | Value |
|---|---|
| Resource | `https://www.youtube.com/@AVIA%C3%87%C3%83OTV/live` |
| Method | GET |
| Headers | `Accept-Language: en` and `Cookie: CONSENT=YES+1; SOCS=CAI` (avoids the cookie-consent page) |
| Timeout | 20 |

Then add a sensor to that entry:

| Field | Value |
|---|---|
| Name | `Lisbon Airport live ID` (gives `sensor.lisbon_airport_live_id`) |
| Select | `link[rel=canonical]` |
| Attribute | `href` |
| Value template | `{{ value.split('v=')[-1] if 'v=' in value else 'unknown' }}` |

The state is the current 11-character video ID. It is `unknown` if YouTube changes the page or shows a consent wall.

## Air-traffic map
The map card reads `sensor.flightradar24_current_in_area`, so it shows what the integration monitors. That area is set in the integration, **not** by the Home Assistant home location. For Lisbon: Settings → Devices & services → Flightradar24 → Configure, and set:

| Option | Value |
|---|---|
| Latitude | `38.7813` (Humberto Delgado Airport) |
| Longitude | `-9.1359` |
| Radius | `1000` (metres; the default). Increase it to see more of Lisbon, e.g. `15000` for about 30 km across |

The card is `custom:flightradar24-card` with `entity: sensor.flightradar24_current_in_area`, OpenStreetMap tiles, flight tracks and an interactive (drag and pinch) map. The map auto-fits the monitored area. A bigger radius means more flights and more API requests per update, so raise it with care. The button below the map opens Flightradar24's own Lisbon map, which cannot be embedded (it blocks iframes).

## How it works
- **Airport player** (Live view): a `custom:button-card` bound to `sensor.lisbon_airport_live_id` renders a YouTube iframe whose source uses `entity.state` as the video ID. The iframe has `referrerpolicy="strict-origin-when-cross-origin"`, which avoids YouTube error 153, and the URL has `controls=0` (no control bar or progress bar), `autoplay=1&mute=1`, `modestbranding=1`, `rel=0`, `disablekb=1` and `iv_load_policy=3`. The card only re-renders when the sensor changes, so the video does not reload by itself.
- **Windy capture time** (Traffic view): each Windy image is a `custom:button-card` that draws the `<img>` and, in its `onload` handler, fetches the same URL to read the `Last-Modified` response header (CORS-safelisted). It shows "Captured HH:MM (n min ago)" in Europe/Lisbon time and reloads the image every 60 seconds. The Lusoponte images are plain markdown and have the capture time printed on the image.
- **Refresh button**: toggles `input_boolean.lisboa_live_refresh`, which re-renders the cards that depend on it.

## Notes
- The Cacilhas camera YouTube broadcast ended on 2026-09-01; swap the video ID when a new one exists.
- The Lisbon Airport live changes ID every day (AVIAÇÃO TV starts a new live daily). An `iframe` embed fails with YouTube error 153, and `youtube-video-card` cannot take a template. So the card is a `custom:button-card` (HACS) that draws the player iframe itself with `referrerpolicy="strict-origin-when-cross-origin"` (the fix for error 153) and the video ID from `sensor.lisbon_airport_live_id`. The YouTube controls and progress bar are hidden (`controls=0`) and the video starts muted.
- Create that sensor with the **Scrape** integration (Settings → Devices & services → Add integration → Scrape): resource `https://www.youtube.com/@AVIA%C3%87%C3%83OTV/live`, headers `Accept-Language: en` and `Cookie: CONSENT=YES+1; SOCS=CAI`, then add a sensor named `Lisbon Airport live ID` with selector `link[rel=canonical]`, attribute `href` and value template `{{ value.split('v=')[-1] if 'v=' in value else 'unknown' }}`.
- [`scripts.yaml`](scripts.yaml) is an optional script that plays the same live on a TV.
- Video IDs and image URLs belong to their respective owners (YouTube channels, Windy, Lusoponte) and may change or go offline.

## Troubleshooting
| Symptom | Cause / fix |
|---|---|
| Airport player shows YouTube error 153 | The referrer is being stripped (privacy extension, reverse proxy, or a `Referrer-Policy: no-referrer` header). Do not use the built-in `iframe` card for YouTube: it triggers this error. |
| Airport player says the video cannot be played in embedded players | The channel disabled embedding for that live. Use the fixed-ID `youtube-video-card` or open `https://www.youtube.com/@AVIA%C3%87%C3%83OTV/live` directly. |
| `sensor.lisbon_airport_live_id` is `unknown` or `unavailable` | YouTube changed the page, the channel has no live right now, or a consent page was returned. Check the Scrape entry (resource, headers, selector). |
| Airport shows yesterday's live | The Scrape entry has not refreshed yet. Reload the entry or wait for its scan interval. |
| Windy images show no "Captured" caption | The `onload` handler did not run or the fetch failed (browser blocks inline handlers, offline, Windy CORS change). The image still shows; the caption stays blank. |
| Air-traffic map shows no flights | `sensor.flightradar24_current_in_area` is `unavailable` or `0`: the integration may be reloading after a settings change, or the area is outside Lisbon. Check latitude, longitude and radius in the integration options. |
| Dashboard looks stale after an update | Hard refresh (Ctrl+F5) or, in the Companion app, Settings → Reset frontend cache. |

## Changelog
- **2026-10-14** – Added the Lisbon air-traffic map (Flightradar24 integration card, monitored area centred on Lisbon airport, 1 km radius) and a Flightradar24 button to the Live view.
- **2026-10-14** – Airport card follows the daily live automatically (Scrape sensor + `button-card` player); first tried an `iframe` card (error 153) and a fixed-ID card before that. Controls and progress bar hidden. Windy images show their capture time. Optional TV script added.
- **2026-09-01** – Cacilhas YouTube broadcast ended; card kept with a note to swap the ID.
- **2026-09** – Initial dashboard: Lisbon and Porto lives, Windy and Lusoponte traffic images, refresh helper.

## License
MIT
