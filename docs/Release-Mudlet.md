# Release Mudlet

Builds a **Mudlet package** with [`muddy`](https://github.com/gesslar/muddy) and publishes a
GitHub Release with the resulting `.mpackage`. Optionally gates on a Quality workflow and
optionally injects [Mupdate](https://github.com/gesslar/mupdate) (a self-updater for Mudlet
packages).

## What it does

1. **Optional quality gate** — if `quality_check` (or the `QUALITY_CHECK` variable) is set to
   a workflow name, that workflow must complete first. Empty string (the default behaviour
   here) disables the gate.
2. **Optional Mupdate injection** — with `inject_mupdate: true`, it downloads the latest
   `Updater.lua` from `gesslar/mupdate` and splices it into `scripts.json` before building.
3. Reads `package` and `version` from your `mfile`, builds with `muddy`, and publishes a
   GitHub Release tagged with the version, attaching the `.mpackage` and a version file.

## Requirements

- An `mfile` with `package` and `version` fields.
- A `README.md` (used as the release body).
- Source laid out for `muddy` (e.g. `src/scripts/`).

## Simplest example

```yaml
# .github/workflows/Release.yaml
name: Release

on:
  pull_request:
    types: [closed]
    branches: [main]

jobs:
  release:
    if: ${{ github.event.pull_request.merged == true }}
    uses: gesslar/Maint/.github/workflows/ReleaseMudlet.yaml@main
    permissions:
      contents: write
```

## With a quality gate and Mupdate

```yaml
jobs:
  release:
    uses: gesslar/Maint/.github/workflows/ReleaseMudlet.yaml@main
    permissions:
      contents: write
    with:
      quality_check: "Quality"
      inject_mupdate: true
```

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `quality_check` | `Quality / Quality` | Workflow/check name to wait for. `""` disables the gate. |
| `inject_mupdate` | `false` | Download `Updater.lua` and splice it into `scripts.json` before building. |

## Repository variable overrides

`QUALITY_CHECK` — overrides the `quality_check` input.

## See also

- [Quality Mudlet](Quality-Mudlet.md) — the matching CI workflow for Mudlet packages.
- [Release Muddy Inject Mupdate](Release-Muddy-Inject-Mupdate.md) — legacy alias with Mupdate always on.
- Mupdate runtime integration: https://github.com/gesslar/mupdate
