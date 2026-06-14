# Quality

Lints and tests a Node.js project across a matrix of operating systems and Node.js versions.
This is the standard CI gate for JavaScript/TypeScript packages, and the workflow the
Release pipelines wait on by default.

## What it does

1. Detects your package manager (`npm` or `pnpm`) and required Node version from
   `package.json`.
2. **Lint** job — runs `<pm> run lint` on Ubuntu (skippable).
3. **Test** job — runs `<pm> test` across every OS × Node-version combination (skippable).
4. A final `Quality` sentinel job that succeeds only if lint and test all passed (or were
   skipped). This is the job downstream Release workflows wait for.

## Requirements

- `package.json` with `engines.node` set (e.g. `"engines": { "node": ">=22" }`) — **required**.
- A `lint` script (unless you disable linting) and a `test` script (unless you disable testing).

## Simplest example

```yaml
# .github/workflows/Quality.yaml
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

Keep the job named `Quality` — that produces the `Quality / Quality` check name the Release
workflows wait for. See [Overview](README.md) → *Quality → Release gating*.

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `package_manager` | `auto` | `auto` (detect from `package.json`), `npm`, or `pnpm`. |
| `perform_linting` | `yes` | `no` to skip the lint job. |
| `perform_testing` | `yes` | `no` to skip the test job. |
| `disabled_test_runners` | `""` | Comma-separated OS to exclude: `ubuntu`, `windows`, `macos`. |
| `disabled_node_versions` | `""` | Comma-separated Node majors to exclude, e.g. `24,25`. |

## Repository variable overrides

`PACKAGE_MANAGER`, `PERFORM_LINTING`, `PERFORM_TESTING`, `DISABLED_TEST_RUNNERS`,
`DISABLED_NODE_VERSIONS`. The first three override the input; the last two are **combined**
with the input (both sets of exclusions apply).

## Example with options

```yaml
jobs:
  Quality:
    uses: gesslar/Maint/.github/workflows/Quality.yaml@main
    with:
      disabled_test_runners: "windows"
      disabled_node_versions: "24"
```
