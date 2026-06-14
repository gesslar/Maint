# Release Muddy Inject Mupdate

> **Legacy alias.** This workflow is a thin wrapper that calls [Release Mudlet](Release-Mudlet.md) with
> `inject_mupdate: true`. It exists only to preserve the historical filename and input
> surface for repos that already reference it. **New Mudlet packages should call
> [Release Mudlet](Release-Mudlet.md) directly** and set `inject_mupdate: true` if they want the auto-updater.

## What it does

Delegates entirely to [Release Mudlet](Release-Mudlet.md), always injecting Mupdate — it downloads the latest
`Updater.lua` from `gesslar/mupdate`, splices it into `scripts.json`, builds with [`@gesslar/muddy`](https://www.npmjs.com/package/@gesslar/muddy),
and publishes the GitHub Release. The optional quality gate is forwarded through.

## Simplest example (legacy callers)

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
    uses: gesslar/Maint/.github/workflows/ReleaseMuddyInjectMupdate.yaml@main
    permissions:
      contents: write
```

The Quality gate runs by default (waiting for `Quality / Quality`); add
`with: { quality_check: "" }` to disable it.

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `quality_check` | `Quality / Quality` | Workflow/check name to wait for. `""` disables the gate. |

`inject_mupdate` is not exposed here — it is always `true`. For control over it, use
[Release Mudlet](Release-Mudlet.md).

## Migrating

Replace the `uses:` line and add the input:

```yaml
  uses: gesslar/Maint/.github/workflows/ReleaseMudlet.yaml@main
  with:
    inject_mupdate: true
```
