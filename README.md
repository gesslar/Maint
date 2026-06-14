# Maint

A central home for **reusable GitHub Actions workflows**. Instead of copy-pasting CI/CD YAML
into every repository, each project calls one of these workflows with a few lines and inherits
the whole pipeline — quality gates, version bumping, tagging, GitHub releases, and publishing.

```yaml
# .github/workflows/Quality.yaml  (in your repo)
name: Quality

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  Quality:
    uses: gesslar/Maint/.github/workflows/Quality.yaml@main
```

## Documentation

Full docs live in **[`docs/`](docs/README.md)** — one page per workflow, each with a
plain-language explanation, a copy-pasteable simplest example, and tables for inputs, secrets,
and repository-variable overrides.

### Quality (CI — lint & test)

- [Quality](docs/Quality.md) — Node.js lint + test across an OS × Node-version matrix
- [Quality (Mudlet)](docs/Quality-Mudlet.md) — Mudlet (Lua) Busted specs
- [Quality (Theme)](docs/Quality-Theme.md) — VS Code theme linting with [`@gesslar/sassy`](https://www.npmjs.com/package/@gesslar/sassy)

### Release (CD — tag, release, publish)

- [Release](docs/Release.md) — publish a package to npm + GitHub Release
- [Release Action](docs/Release-Action.md) — a JavaScript GitHub Action with committed `dist/`
- [Release Only](docs/Release-Only.md) — ecosystem-agnostic tag + release (+ optional artefacts)
- [Release Market](docs/Release-Market.md) — a VS Code extension to VS Marketplace / Open VSX
- [Release Mudlet](docs/Release-Mudlet.md) — a Mudlet `.mpackage` built with [`@gesslar/muddy`](https://www.npmjs.com/package/@gesslar/muddy)
- [Release Muddy Inject Mupdate](docs/Release-Muddy-Inject-Mupdate.md) — legacy Mudlet + Mupdate alias
- [Release Docusaurus](docs/Release-Docusaurus.md) — build a Docusaurus site, deploy via rsync/SSH
- [Release Starlight](docs/Release-Starlight.md) — build a Starlight site, deploy via rsync/SSH

## License

[0BSD](LICENSE.txt) (BSD Zero Clause License).
