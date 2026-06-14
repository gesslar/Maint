# Release Market

Releases a **VS Code extension** when a PR is merged: builds the `.vsix` once, creates a
GitHub Release with it attached, and publishes to the **Visual Studio Marketplace** and/or
**Open VSX**.

## What it does

On a merged PR into `main`, it mirrors [Release](Release.md)'s flow (wait for quality → version-bump
check → tag) and then:

1. Builds the `.vsix` once (via your `package` script) and uploads it as a workflow artifact.
2. Creates the GitHub Release with the `.vsix` attached.
3. Publishes across a matrix of **only the marketplaces whose token is set.** Each target is
   gated on token presence — if neither token is configured, publishing is skipped entirely
   (you still get the tag and GitHub Release).

## Requirements

- An npm script (default `package`) that produces a `.vsix` in a known directory (default
  `vsix/`). Typically this wraps `vsce package` plus any build steps.
- At least one publishing token as a repository secret (both optional):
  - **`VSX_MARKETPLACE_ACCESS_TOKEN`** — VS Marketplace PAT (Azure DevOps, "Marketplace > Manage").
  - **`OVSX_ACCESS_TOKEN`** — Open VSX PAT (https://open-vsx.org/user-settings/tokens).

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
    uses: gesslar/Maint/.github/workflows/ReleaseMarket.yaml@main
    secrets: inherit
    permissions:
      contents: write
```

`secrets: inherit` forwards whichever marketplace tokens you've configured. Pair with a
[Quality](Quality.md) or [Quality Theme](Quality-Theme.md) workflow named `Quality`.

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `package_manager` | `auto` | `auto`, `npm`, or `pnpm`. |
| `quality_check` | `Quality / Quality` | Composite check name to wait for. `""` disables the gate. |
| `git_user_name` | `github-actions` | Git identity for the tag. |
| `git_user_email` | `github-actions@github.com` | Git identity for the tag. |
| `package_script` | `package` | npm script that builds the `.vsix`. |
| `vsix_glob` | `vsix/*.vsix` | Glob used to locate the built `.vsix` (first match wins). |

## Secrets

| Secret | Required | Description |
| --- | --- | --- |
| `VSX_MARKETPLACE_ACCESS_TOKEN` | optional | Publish to VS Marketplace if set. |
| `OVSX_ACCESS_TOKEN` | optional | Publish to Open VSX if set. |

## Repository variable overrides

`PACKAGE_MANAGER`, `QUALITY_CHECK`, `GIT_USER_NAME`, `GIT_USER_EMAIL`, `PACKAGE_SCRIPT`,
`VSIX_GLOB`.

> **Note:** VS Code extensions usually declare `engines.vscode` but not `engines.node`; in
> that case the workflow falls back to the active Node LTS automatically.
