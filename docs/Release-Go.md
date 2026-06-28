# Release (Go)

Builds a **Go module** into cross-compiled artefacts and publishes a **GitHub Release** when
a version bump lands on `main`. Same flow as the other Release workflows — wait for quality,
detect a version change, tag, release — except it **cross-compiles the binary itself**. With
`CGO_ENABLED=0`, a single Ubuntu runner builds Linux, Windows, and macOS binaries from source;
no per-OS runner matrix needed.

The version comes from a plain **`VERSION` file** in the repo root (Go has no `package.json`).
Bump it in your PR and merge → tag + release. No bump → nothing happens.

## What it does

On a merged PR into `main`:

1. **Waits** for the quality check (default `Quality / Quality`).
2. **Determines the version** from the `VERSION` file — unchanged → no tag, no release.
3. **Tags** `vX.Y.Z` (skipped if it already exists).
4. **Builds** one binary per target (default: linux/amd64, linux/arm64, windows/amd64,
   windows/arm64, darwin/amd64, darwin/arm64), packaging each as a `.tar.gz`
   (`.zip` for Windows) named `<binary>_v<version>_<goos>_<goarch>`.
5. **Creates a GitHub Release** with generated notes, a `SHA256SUMS` file, and every archive.

## Requirements

- A `VERSION` file at the repo root containing the version (e.g. `1.2.3`). Bumping it is what
  triggers a release.
- A `go.mod` (sets the toolchain version and, by default, the binary name).
- Buildable with `CGO_ENABLED=0` (pure Go). If you need cgo, set `cgo_enabled: "1"` and limit
  `targets` to platforms your runner can build.

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
    uses: gesslar/Maint/.github/workflows/ReleaseGo.yaml@main
```

## Binary in `cmd/` and a version-reporting build

```yaml
jobs:
  Release:
    uses: gesslar/Maint/.github/workflows/ReleaseGo.yaml@main
    with:
      main_package: "./cmd/mytool"
      binary_name: "mytool"
      version_ldflag: "main.version"   # bakes -X main.version=vX.Y.Z into the binary
```

## Inputs

| Input | Default | Description |
| --- | --- | --- |
| `quality_check` | `Quality / Quality` | Quality check to wait for. `""` disables the gate. |
| `version_source` | `VERSION` | File whose trimmed contents are the version. |
| `main_package` | `.` | Import path of the main package (e.g. `./cmd/foo`). |
| `binary_name` | `""` | Output binary name. Empty derives it from `go.mod`. |
| `targets` | six platforms | JSON array of `{goos, goarch}` objects to build. |
| `go_version` | `""` | Go version for setup-go. Empty reads from `go.mod`. |
| `ldflags` | `-s -w` | Linker flags passed to `go build -ldflags`. |
| `version_ldflag` | `""` | If set (e.g. `main.version`), appends `-X '<path>=v<version>'`. |
| `cgo_enabled` | `0` | `CGO_ENABLED` for the build. Keep `0` for static cross-compilation. |
| `git_user_name` | `github-actions` | Git identity for the tag. |
| `git_user_email` | `github-actions@github.com` | Git identity for the tag. |

### Customising targets

```yaml
with:
  targets: '[{"goos":"linux","goarch":"amd64"},{"goos":"linux","goarch":"arm64"}]'
```

## Repository variable overrides

`QUALITY_CHECK`, `VERSION_SOURCE`, `MAIN_PACKAGE`, `BINARY_NAME`, `TARGETS`, `GO_VERSION`,
`GIT_USER_NAME`, `GIT_USER_EMAIL` override the matching input.

## See also

- [Quality Go](Quality-Go.md) — the matching quality workflow for Go modules.
