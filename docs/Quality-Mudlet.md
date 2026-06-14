# Quality (Mudlet)

Runs the test suite for a **Mudlet (Lua) package** using the `gesslardev/mudlet-busted`
Docker image. Use this instead of [Quality](Quality.md) for Mudlet packages — it skips the Node/OS
matrix and linting, which don't apply to Lua delivered through Mudlet.

## What it does

A single `npm test` invocation inside the `mudlet-busted` image:

1. Builds the package with [`@gesslar/muddy`](https://www.npmjs.com/package/@gesslar/muddy) (reads `mfile`, `scripts.json`, etc.).
2. Installs the built `.mpackage` into a throwaway Mudlet profile.
3. Runs Busted specs from `src/resources/test/`.

It ends with a `Quality` sentinel job so Release workflows can gate on it.

## Requirements

- `package.json` with a `test` script that invokes the image, e.g.
  `docker run --rm -v "$PWD":/workspace gesslardev/mudlet-busted`.
- `mfile` with `"ignore": ["resources/test/*"]` so specs stay out of builds.
- Specs at `src/resources/test/*_spec.lua`.

## Simplest example

```yaml
# .github/workflows/Quality.yaml
name: Quality

on:
  workflow_dispatch:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  Quality:
    uses: gesslar/Maint/.github/workflows/QualityMudlet.yaml@main
```

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `perform_testing` | `yes` | `no` to skip testing entirely. |

## Repository variable overrides

`PERFORM_TESTING` — overrides the `perform_testing` input.

## See also

- [Release Mudlet](Release-Mudlet.md) — the matching release workflow for Mudlet packages.
