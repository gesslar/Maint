# Quality (Go)

Lints and tests a **Go module**. Formatting (`gofmt`) and vetting (`go vet`) run once on
Ubuntu; `go test` runs across an **OS matrix** (Ubuntu / Windows / macOS). The Go toolchain
version is read straight from your `go.mod`, so there's nothing to pin.

Linting is **all-or-nothing**: `perform_linting` turns the whole lint job on or off — it isn't
granulated per tool. All three checks below are read-only (they report and fail; they never
rewrite your files).

## What it does

1. **Lint** (Ubuntu, one Go version):
   - `gofmt -l .` — fails if anything isn't formatted (vendor/ excluded).
   - `go vet ./...`.
   - `golangci-lint` — honours a committed `.golangci.*` config if present, otherwise runs
     with its own defaults.
2. **Test** (matrix): `go build ./...` then `go test ./...` on each OS.
3. Ends with a `Quality` sentinel job so Release workflows can gate on it.

## Requirements

- A `go.mod` (its `go` directive sets the toolchain version — override with `go_version`).
- That's it. golangci-lint runs with sensible defaults; commit a `.golangci.*` only if you
  want to customise which linters run.

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
    uses: gesslar/Maint/.github/workflows/QualityGo.yaml@main
```

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `perform_linting` | `yes` | `no` to skip the lint job. |
| `perform_testing` | `yes` | `no` to skip the test job. |
| `go_version` | `""` | Go version for setup-go. Empty reads from `go.mod`. |
| `disabled_test_runners` | `""` | Comma-separated OS runners to exclude (`ubuntu`, `windows`, `macos`). |
| `test_args` | `""` | Extra flags appended to `go test ./...` (e.g. `-race`). |

## Repository variable overrides

`PERFORM_LINTING`, `PERFORM_TESTING`, `GO_VERSION` override the matching input.
`DISABLED_TEST_RUNNERS` combines with the `disabled_test_runners` input.

## See also

- [Release Go](Release-Go.md) — the matching release workflow for Go modules.
