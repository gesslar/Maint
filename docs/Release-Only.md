# Release Only

The **ecosystem-agnostic** release workflow: it waits for quality, checks for a version
bump, creates a git tag, and publishes a GitHub Release — with **no build steps and no
package-manager assumptions**. Use it when you don't need to publish to a registry, or when
you build your own artefacts and just want them attached to a release.

## What it does

On a merged PR into `main`:

1. **Waits** for the quality check (default `Quality / Quality`).
2. **Determines the version** from `package.json` — no bump, no release.
3. **Tags** `vX.Y.Z` (skipped if it already exists).
4. **Creates a GitHub Release** with generated notes, attaching any artefacts you produced
   in earlier jobs and named via the `artefacts` input.

Building artefacts is the caller's job: build them in a prior job, upload with
`actions/upload-artifact`, then list the artifact names here.

## Simplest example

Tag + release, no artefacts:

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
    uses: gesslar/Maint/.github/workflows/ReleaseOnly.yaml@main
    permissions:
      contents: write
```

## Example with artefacts

The caller builds and uploads; this workflow downloads and attaches everything.

```yaml
# .github/workflows/Release.yaml
name: Release

on:
  pull_request:
    types: [closed]
    branches: [main]

jobs:
  build:
    if: ${{ github.event.pull_request.merged == true }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
      - run: npm ci && npx vsce package
      - uses: actions/upload-artifact@v4
        with:
          name: extension
          path: "*.vsix"

  Release:
    needs: build
    if: ${{ github.event.pull_request.merged == true }}
    uses: gesslar/Maint/.github/workflows/ReleaseOnly.yaml@main
    permissions:
      contents: write
    with:
      artefacts: '["extension"]'
```

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `quality_check` | `Quality / Quality` | Composite check name to wait for. `""` disables the gate. |
| `git_user_name` | `github-actions` | Git identity for the tag. |
| `git_user_email` | `github-actions@github.com` | Git identity for the tag. |
| `artefacts` | `[]` | JSON array of artifact names (uploaded by prior jobs) to attach. |

## Repository variable overrides

`QUALITY_CHECK`, `GIT_USER_NAME`, `GIT_USER_EMAIL`.
