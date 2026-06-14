# Release Action

Releases a **JavaScript GitHub Action** that ships a bundled `dist/` committed to the
repository. Unlike [Release](Release.md) (npm) and [Release Only](Release-Only.md) (artefact-agnostic), this workflow
guarantees the published tag's `dist/` matches the source.

## What it does

On a merged PR into `main`:

1. **Waits** for the quality check (default `Quality / Quality`).
2. **Determines the version** — no version bump, no release.
3. **Rebuilds the bundle** and **verifies the committed `dist/` is already up to date.**
   If it's stale, the release **fails** — the workflow never writes to your default branch,
   so a release can never surprise you with a commit you then have to pull.
4. **Tags and creates a GitHub Release** once `dist/` is verified, so consumers who pin
   `uses: owner/action@vX.Y.Z` always get the matching build.

## Requirements

- Build the bundle locally and commit it as part of your version bump. The recommended way
  is an npm `version` lifecycle hook:

  ```json
  { "scripts": { "version": "npm run build && git add dist" } }
  ```
- `package.json` with `engines.node` and a `build` script producing `dist/`.

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
    uses: gesslar/Maint/.github/workflows/ReleaseAction.yaml@main
    secrets: inherit
    permissions:
      contents: write
```

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `package_manager` | `auto` | `auto`, `npm`, or `pnpm`. |
| `quality_check` | `Quality / Quality` | Composite check name to wait for. `""` disables the gate. |
| `git_user_name` | `github-actions` | Git identity for the tag. |
| `git_user_email` | `github-actions@github.com` | Git identity for the tag. |
| `build_command` | `build` | Script run as `<pm> run <build_command>` to produce the bundle. |
| `dist_path` | `dist` | Built output verified against source and shipped at the tag. |

## Repository variable overrides

`PACKAGE_MANAGER`, `QUALITY_CHECK`, `GIT_USER_NAME`, `GIT_USER_EMAIL`, `BUILD_COMMAND`,
`DIST_PATH`.
