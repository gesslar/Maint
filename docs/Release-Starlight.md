# Release Starlight

Builds a **Starlight** (Astro) documentation site and deploys it to a remote server via
**rsync over SSH**. Intended for push to `main`, PR merge, or manual dispatch.

## What it does

1. Reads the required Node version from `package.json`.
2. `npm ci` + `npm run build` in your docs directory (with `NODE_ENV=production`).
3. Connects over SSH and `rsync -avz --delete`s the built `dist/` directory to your server.

This is identical to [Release Docusaurus](Release-Docusaurus.md) except Starlight emits `dist/` rather than `build/`.

## Requirements

- A Starlight project directory (default `docs`) with `engines.node` in its `package.json`.
- Deployment secrets configured on the repo (see below).

## Simplest example

```yaml
# .github/workflows/Docs.yaml
name: Deploy Docs

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  deploy-docs:
    uses: gesslar/Maint/.github/workflows/ReleaseStarlight.yaml@main
    secrets: inherit
```

`secrets: inherit` forwards the four SSH/SFTP secrets. Override the project location with
`with: { docs_directory: "site" }` or the `DOCS_DIRECTORY` variable.

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `docs_directory` | `docs` | Path to the Starlight project, relative to repo root. |

## Secrets

| Secret | Required | Description |
| --- | --- | --- |
| `SSH_PRIVATE_KEY` | ✅ | Private key for the deployment server. |
| `SFTP_SERVER` | ✅ | Hostname or IP of the server. |
| `SFTP_USERNAME` | ✅ | SSH username. |
| `SFTP_TARGET_DIR` | ✅ | Absolute path on the server to deploy into. |

## Repository variable overrides

`DOCS_DIRECTORY` — overrides the `docs_directory` input.
