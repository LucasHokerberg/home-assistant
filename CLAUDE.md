# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Maintaining this file
Update this file when a fact here becomes wrong, or when new non-obvious conventions are introduced. Do **not** add anything that could be answered by reading the code.

Rules:
- Keep total length under 600 tokens
- One bullet or one sentence per fact — no elaboration
- No examples, no tutorials, no coding style
- Remove or replace entries rather than appending when space is tight

## What this repo is

- Personal Home Assistant config: pure YAML + Jinja2, no build/lint/test — validate via Home Assistant itself.
- Secrets via `!secret` into `secrets.yaml` (gitignored); never inline real values.
- `custom_components/`, `scenes.yaml`, UI-managed `automations.yaml` are gitignored.

## Structure

- `configuration.yaml` wires every include — check it before adding a new include path.
- Root `automations.yaml` is GUI-managed; hand-written automations live under `include/automations/` instead.

## Automation conventions

- One file per category under `include/automations/`, named `NN-category.yaml`; add to the matching file, new category only if none fit.
- IDs are `"NNxx"` (NN = category, xx = sequence); don't renumber to close gaps.
- Alias format: `<Category> - <Description>`, unquoted.
- Every automation needs a one-line `#` comment above it stating when/why it fires.
- All automations and scripts use modern syntax: plural `triggers`/`conditions`/`actions` keys, `action:` (not `service:`), `target: entity_id` (not bare `entity_id:`).
- Automations trigger each other via `automation.trigger` instead of duplicating logic.
- Quotes (Prettier-enforced): double-quote values that need quoting; plain identifiers (aliases, entity ids) stay unquoted. Inside Jinja templates, string literals are always single-quoted (`states('x.y')`), so the outer YAML quote stays double with no escaping needed.
- Indentation: 2 spaces per level; list items indent 2 further than their parent key.
- Mobile notifications go through `notify.home_mobile_apps`, title `'HalloNET Home'`, `data.channel` = category name; actionable buttons are handled in `01-action.yaml` via `mobile_app_notification_action` events.
- Alerts (`include/alerts/`) pair an `alert:` entry with an `acknowledge_*` handler in `01-action.yaml`.
- Entity ids: `<location>_<thing>` snake_case; cryptic hardware ids (e.g. car `fgh47k`) are kept as-is.
- `input_boolean.vacation_mode` / `night_mode` / `guest_mode` are recurring gating conditions — check whether new automations should respect them.

## HA MCP server

- An `ha-custom` MCP server connects to the live instance, read-only by design — use it to verify entity ids, states, areas, and existing automations/helpers before editing YAML; never rely on it to apply changes.

## Commit style

- Short, imperative, present-tense summary; no body; no conventional-commit prefixes.
