# Home Assistant Config

My personal [Home Assistant](https://www.home-assistant.io/) configuration — hand-written YAML automations, scripts, alerts, and supporting config for things that aren't (yet) manageable from the UI.

## Why this exists

I prefer writing automations in YAML over the UI editor, and keeping them in git gives me history, review, and an easy way to back out a bad change. I'm sharing this publicly in case any of the automations, patterns, or conventions are useful to someone else running Home Assistant. Feel free to browse, copy, and adapt anything here to your own setup.

This is a live config for my own home, not a generic starter kit — entity IDs, areas, and some automations are specific to my house. Treat it as a reference rather than something to drop in wholesale.

## Structure

```
configuration.yaml           # Wires up every include below
include/
  automations/               # Hand-written automations, one file per category (NN-category.yaml)
  alerts/                    # alert: entries, paired with acknowledge handlers in 01-action.yaml
  scripts/                   # Reusable scripts (bedtime, wake up, dishwasher, etc.)
  templates/                 # Template sensors
  groups/                    # Entity groups
  zones/                     # Location zones
  customizations/            # Per-entity customizations
  recorder.yaml              # Recorder config (MariaDB instead of the built-in SQLite DB)
  influxdb.yaml              # InfluxDB config, used for Grafana dashboards
  notify.yaml                # Notification targets
  fans.yaml                  # Template fan config
automations.yaml             # GUI-managed automations (gitignored, not part of this repo)
```

Automation categories, numbered `NN-category.yaml`:

| #   | Category                              |
| --- | ------------------------------------- |
| 01  | Action (notification action handling) |
| 02  | Appliance                             |
| 03  | Climate                               |
| 04  | Lighting                              |
| 05  | Media                                 |
| 06  | Motion                                |
| 07  | Presence                              |
| 08  | Reminder                              |
| 09  | Routine                               |
| 10  | Security                              |
| 11  | Car                                   |
| 12  | 3D printing                           |

Each automation has an ID like `"0301"` (category `03`, sequence `01`) and a one-line comment above it explaining when/why it fires.

## Conventions

- Modern HA syntax throughout: plural `triggers` / `conditions` / `actions`, `action:` instead of `service:`, `target: entity_id` instead of bare `entity_id:`.
- Automations call each other via `automation.trigger` rather than duplicating logic.
- Mobile notifications go through `notify.home_mobile_apps` with actionable buttons handled centrally in `01-action.yaml`.
- `input_boolean.vacation_mode`, `night_mode`, and `guest_mode` are common gating conditions used across automations.
- Secrets are referenced via `!secret` and kept out of git in `secrets.yaml`.

See [CLAUDE.md](CLAUDE.md) for the full set of conventions this repo follows (used to guide AI-assisted edits, but equally useful as a style guide for humans).

## What's not here

A few things are gitignored because they're either generated, environment-specific, or UI-managed:

- `custom_components/` — HACS/custom integrations
- `secrets.yaml`, `scenes.yaml` — local secrets and UI-managed scenes
- `automations.yaml` — automations created through the Home Assistant UI

## Using this

This isn't designed to be installed as-is — it's a reference. If something here is useful:

1. Copy the relevant automation, script, or template file(s).
2. Adjust entity IDs, areas, and helper names to match your own setup.
3. Add any missing helpers (`input_boolean`, `input_number`, etc.) referenced by the automation.
4. Validate through Home Assistant's own config check before reloading.

## License

No license is specified — feel free to use any of this for your own personal Home Assistant setup.
