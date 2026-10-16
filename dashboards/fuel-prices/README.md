# Fuel prices near home (Portugal)

A Home Assistant dashboard that compares petrol prices at the stations around your home, using the official DGEG price data. It is not tied to any place: it finds the fuel-price sensors by itself and measures distance from **your** Home Assistant home location.

## Views
- **Nearby Stations** – summary (cheapest, nearest, average, most expensive, and what a 50 L fill-up saves), Unleaded 95 ranked by price, Unleaded 98 ranked by price (only stations that sell it), and a list ordered by distance. Each station links to Google Maps.

An optional price-history graph is in [Price history (optional)](#price-history-optional).

## Requirements
- The `precoscombustiveis` integration (DGEG fuel prices, Portugal), one config entry per station.
- Your home location set in Settings → System → General (used for the distance).
- No HACS cards: the dashboard only uses built-in `markdown` and `heading` cards.

## Install
1. Add the stations you care about: Settings → Devices & services → Add integration → `precoscombustiveis`. The flow asks for district, municipality, brand, station and fuel types. Repeat for each station near you and choose gasoline 95 (and 98 if you want it).
2. Settings → Dashboards → Add dashboard, open it, ⋮ → Raw configuration editor, and paste [`dashboard.yaml`](dashboard.yaml).
3. Optional: change the search radius. Each markdown card starts with `{%- set radius = 10 -%}` (km); edit it in all four cards.

## How it works
Each station fuel is a sensor whose state is the price and whose attributes include `GasStationId`, `Name`, `Brand`, `Address`, `FuelName`, `LastPriceUpdate`, `Latitude` and `Longitude`. The cards loop over all sensors that have `GasStationId`, keep the ones with the right `FuelName` within the radius (`distance(Latitude, Longitude)` is measured from the Home Assistant home location), and sort them. Nothing in the dashboard file contains a place, a station list or home coordinates.

## Price history (optional)
`history-graph` cannot take templates, so the entities must be listed by hand. Add a second view with your own sensors:

```yaml
  - title: Price History
    path: history
    icon: mdi:chart-line
    type: sections
    max_columns: 1
    sections:
      - type: grid
        cards:
          - type: history-graph
            hours_to_show: 168
            grid_options: {columns: full, rows: 8}
            entities:
              - entity: sensor.<your_station>_gasolina_simples_95
                name: <label>
```

## Notes
- Prices and station data belong to DGEG and the stations; they may change or be delayed.
- The attribute names `Latitude` and `Longitude` are capitalised, so the built-in map card cannot plot these sensors. Use the Google Maps links instead.
- Do not publish screenshots of your own dashboard: the station names and addresses reveal the area you live in.

## Troubleshooting
| Symptom | Cause / fix |
|---|---|
| "No fuel price data available yet" | No sensor is within the radius, or the integration has not loaded. Check the sensors exist and raise `radius`. |
| Distances are wrong or missing | The Home Assistant home location is not set. Set it in Settings → System → General. |
| 98 table is empty | None of your stations sell 98, or you did not select that fuel when adding the station. |
| Station missing from the list | Its sensor has no price (state not a number) or its `FuelName` is not `Gasolina simples 95`. |

## Changelog
- **2026-10-16** – Initial shareable version (place-independent).
