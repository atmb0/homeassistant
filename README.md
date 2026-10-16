# Home Assistant

Public, shareable Home Assistant configuration. Everything here is sanitized: no credentials, tokens or private devices.

## Dashboards

| Dashboard | Description | Needs |
|---|---|---|
| [`lisboa-porto-cams`](dashboards/lisboa-porto-cams) | Public live cameras and traffic images for Lisbon and Porto. The airport player follows the daily live via a Scrape sensor; Windy images show their capture time. | HACS: `youtube-video-card`, `button-card`, Flightradar24 integration. Scrape integration. 1 helper, 1 optional script |

Each dashboard folder has its own README with requirements, install steps, troubleshooting and a changelog.

## Layout

```
dashboards/
  <name>/          one self-contained folder per dashboard
    README.md      requirements, install steps, notes, changelog
    dashboard.yaml Lovelace dashboard (raw configuration YAML)
    helpers.yaml   optional: input_* helpers it needs
    scripts.yaml   optional: scripts it needs
    automations.yaml, sensors.yaml   optional, same idea
    screenshot.png optional: preview
  _template/       copy this to start a new dashboard
```

A dashboard folder carries everything it depends on, so copying one folder is enough to reuse it. Shared items that are not tied to a dashboard (blueprints, packages) can get their own top-level folder later.

## Adding a dashboard

1. Copy `dashboards/_template` to `dashboards/<name>` and fill in its README.
2. Keep the file names above so every dashboard looks the same.
3. Add a row to the table in this README.
4. Before committing, check the [publishing checklist](dashboards/_template/README.md#publishing-checklist).

## License
MIT
