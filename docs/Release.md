# Release

Publishes a Node.js package to **npm** and creates a matching **GitHub Release** when a PR
is merged. This is the standard CD pipeline for npm packages.

## What it does

On a merged PR into `main`:

1. **Waits** for the quality check (default `Quality / Quality`) to pass — see [Overview](README.md).
2. **Determines the version** via
   [`gesslar/new-version-questionmark`](https://github.com/gesslar/new-version-questionmark).
   If `package.json`'s version didn't change, everything below is skipped.
3. **Creates and pushes a git tag** `vX.Y.Z` (skipped if it already exists).
4. **Creates a GitHub Release** with auto-generated notes and the `npm pack` tarball attached.
5. **Publishes to npm** — but only if that exact version isn't already published and is
   greater than the current latest.

## Requirements

- `package.json` with `engines.node`, a valid `version`, and `lint`/`test` scripts (for the
  paired Quality workflow).
- An npm token stored as the repository secret **`NPM_GITHUB_CD_ACCESS_TOKEN`**.

## Simplest example

```yaml
# .github/workflows/Release.yaml
name: Release

on:
  pull_request:
    types: [closed]
    branches: [main]

jobs:
  Release:
    if: ${{ github.event.pull_request.merged == true }}
    uses: gesslar/Maint/.github/workflows/Release.yaml@main
    secrets: inherit
    permissions:
      contents: write
```

`secrets: inherit` passes `NPM_GITHUB_CD_ACCESS_TOKEN` through. Pair this with a [Quality](Quality.md)
workflow whose job is named `Quality`. **To release: bump the version in your PR and merge it.**

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `package_manager` | `auto` | `auto`, `npm`, or `pnpm`. |
| `quality_check` | `Quality / Quality` | Composite check name to wait for. `""` disables the gate. |
| `git_user_name` | `github-actions` | Git identity for the tag. |
| `git_user_email` | `github-actions@github.com` | Git identity for the tag. |

## Secrets

| Secret | Required | Description |
| --- | --- | --- |
| `NPM_GITHUB_CD_ACCESS_TOKEN` | ✅ | npm auth token used to publish. |

## Repository variable overrides

`PACKAGE_MANAGER`, `QUALITY_CHECK`, `GIT_USER_NAME`, `GIT_USER_EMAIL`.

## Choosing a Release workflow

| Use | Workflow |
| --- | --- |
| npm package | **Release** (this page) |
| JavaScript GitHub Action with committed `dist/` | [Release Action](Release-Action.md) |
| Just a tag + release, any ecosystem, optional artefacts | [Release Only](Release-Only.md) |
| VS Code extension | [Release Market](Release-Market.md) |
| Mudlet package | [Release Mudlet](Release-Mudlet.md) |
