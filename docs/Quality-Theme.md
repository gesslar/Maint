# Quality (Theme)

Lints a VS Code theme source file (Sassy YAML) with
[`@gesslar/sassy`](https://www.npmjs.com/package/@gesslar/sassy). Use this for theme
repositories that have no build or test phase — just a lint pass.

## What it does

Installs Node, then runs `npx -y @gesslar/sassy@<version> lint [--strict] <theme_files>`
for each file you point it at. With `strict: "yes"`, warnings are treated as failures.

## Simplest example

`theme_files` is the one required input — the path(s) to your theme entry file(s).

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
    uses: gesslar/Maint/.github/workflows/QualityTheme.yaml@main
    with:
      theme_files: "src/my-theme.yaml"
```

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `theme_files` | *(required)* | Path(s) to theme entry file(s). Space-separated; globs allowed, e.g. `themes/*.yaml`. |
| `strict` | `no` | `yes` passes `--strict` (warnings fail the build). |
| `sassy_version` | `latest` | npm version specifier for `@gesslar/sassy`, e.g. `5` or `5.20.2`. |
| `node_version` | `lts/*` | Node.js version to install for running sassy. |

## Repository variable overrides

`THEME_FILES`, `STRICT`, `SASSY_VERSION`, `NODE_VERSION` — each overrides the same-named input.

## See also

- [Release Market](Release-Market.md) — release a VS Code extension (including themes) to the marketplaces.
