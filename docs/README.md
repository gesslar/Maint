# Maint — Reusable GitHub Actions Workflows

## Introducing `Maint` an unlikely named repo from a downstream perspective

OK, Maint derives its name from the fact that its first ever two workflows were legitimate maintenance workflows. And they're still here, but they're not really intended to be re-used in a compositional way. You can copy/pasta them into your own repos if you want to. They're

- **[WorkflowCleanup.yaml](../.github/workflows/WorkflowCleanup.yaml)** - Trawl all your repos and delete old workflow runs. Necessary for me because I have more than 3 repos (😏), but for you? idk. You probably have a sane number.
- **[DependabotAutoComment.yaml](../.github/workflows/DependabotAutoComment.yaml)** - Whenever you get a Dependabot thingie that says "Hey, just so you know, ESLint bumped something. Yes, we know they bumped it 20 minutes ago, but this one is different, _they swear_, also, another one's coming before you finish merging this one." and you get these emails and calls-to-action in your Inbox because you have uhh more than 3 repos, then this will save you some time. Or it saves me some anyway.

> *It's worth noting that if you're going to use the above workflows, which I both encourage and discourage, for entirely separate reasons and no, I have no responses, that you should put them in a single repo to govern your empire of repositories. You could even call it `Maint`. You don't have to. But you could. IJS.*

Everything else in this repo is not _maintenance_ in the broom-and-dustpan-y or rubber-stamp-y sense, but could also be interpreted as _maintenance_ in the sense of low maintenance workflows for you and your cat. It's a stretch, but, I'm fine with it. And so should you.

## Description of the things

`gesslar/Maint` is a central home for **reusable GitHub Actions workflows**. Instead of
copy-pasting CI/CD YAML into every repository, each project calls one of these workflows
with a few lines and inherits the whole pipeline — quality gates, version bumping, tagging,
GitHub releases, and publishing.

These docs have **one page per workflow**, each with a plain-language explanation and the
**simplest example to get it running**.

## How a reusable workflow is used

Most workflows here are *reusable* — you call them from a tiny "caller" workflow in your
own repository. The pattern is always the same:

```yaml
# .github/workflows/Quality.yaml  (in YOUR repo)
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

The `uses:` line points at a workflow in this repo, pinned to `@main`. That's it — the job
runs the whole reusable pipeline.

## The workflows

### Quality (CI — lint & test)

| Workflow | What it checks |
| --- | --- |
| [Quality](Quality.md) | Node.js projects: lint + test across an OS × Node-version matrix |
| [Quality Mudlet](Quality-Mudlet.md) | Mudlet (Lua) packages: Busted specs via the `mudlet-busted` image |
| [Quality Theme](Quality-Theme.md) | VS Code themes: lints Sassy YAML with [`@gesslar/sassy`](https://www.npmjs.com/package/@gesslar/sassy) |

### Release (CD — tag, release, publish)

| Workflow | What it ships |
| --- | --- |
| [Release](Release.md) | Publishes a package to **npm** + GitHub Release |
| [Release Action](Release-Action.md) | A JavaScript **GitHub Action** with a committed `dist/` bundle |
| [Release Only](Release-Only.md) | Ecosystem-agnostic: just tag + GitHub Release (+ optional artefacts) |
| [Release Market](Release-Market.md) | A **VS Code extension** to the VS Marketplace and/or Open VSX |
| [Release Mudlet](Release-Mudlet.md) | A **Mudlet** `.mpackage` built with [`@gesslar/muddy`](https://www.npmjs.com/package/@gesslar/muddy) (optional Mupdate) |
| [Release Muddy Inject Mupdate](Release-Muddy-Inject-Mupdate.md) | Legacy alias of Release Mudlet with Mupdate always injected |
| [Release Docusaurus](Release-Docusaurus.md) | Builds a **Docusaurus** site and deploys it via rsync/SSH |
| [Release Starlight](Release-Starlight.md) | Builds a **Starlight** site and deploys it via rsync/SSH |

## Conventions shared across these workflows

A few patterns recur, so they're explained once here and referenced from the pages:

- **Quality → Release gating.** The Release workflows wait for a passing quality check
  before they tag and publish. The check name is the composite
  `"<caller-job> / <sentinel-job>"`, which defaults to `"Quality / Quality"`. That matches
  a caller whose job is named `Quality` calling a Quality workflow whose final sentinel job
  is also named `Quality`. Keep your quality caller job named `Quality` and it just works.
  Set `quality_check: ""` to disable the gate.

- **Repository variable overrides.** Almost every input can be overridden by a repository
  variable of the same name in UPPERCASE, without editing your caller workflow. Set them
  under **Settings → Secrets and variables → Actions → Variables**. For example,
  `PACKAGE_MANAGER`, `QUALITY_CHECK`, `GIT_USER_NAME`. The rule is "repo variable wins,
  otherwise the input value."

- **Package-manager auto-detection.** Workflows that install dependencies default to
  `package_manager: "auto"`, reading `packageManager` from `package.json`. `pnpm@x.y.z` is
  honoured; anything else falls back to `npm`.

- **Version bumping is automatic.** The Release workflows use
  [`gesslar/new-version-questionmark`](https://github.com/gesslar/new-version-questionmark)
  to detect whether the version in `package.json` (or `mfile` for Mudlet) changed in the
  merged PR. No bump → no tag, no release. So releasing is simply: bump the version in your
  PR, merge it.

- **Release triggers on PR merge.** Release workflows are meant to run on
  `pull_request: types: [closed]` against `main`, guarded by
  `if: ${{ github.event.pull_request.merged == true }}`.

> **Pinning:** examples pin `@main` for simplicity. For reproducible CI you can pin to a
> tag or commit SHA instead (e.g. `...Quality.yaml@v1`).
