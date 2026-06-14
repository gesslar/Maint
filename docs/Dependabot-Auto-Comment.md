# Dependabot Auto Comment

Automatically processes Dependabot PRs (auto-comment / auto-merge) on a schedule, using the
[`gesslar/dependabot-auto-merge`](https://github.com/gesslar/dependabot-auto-merge) action.

> **This is a standalone workflow, not a reusable one.** You don't `uses:` it — you **copy the
> file** into your repo's `.github/workflows/` and configure a secret and a variable.

## What it does

Runs every 6 hours (and on manual dispatch) and invokes the `dependabot-auto-merge` action
to act on open Dependabot pull requests according to your chosen merge type.

## Setup

1. Copy the workflow file (below) into `.github/workflows/` in your repo.
2. Add a repository secret **`WORKFLOW_MAINT`** — a token with permission to act on PRs.
3. Set a repository variable **`DEPENDABOT_MERGE_TYPE`** (e.g. `squash`, `merge`, `rebase`).

## The workflow file

```yaml
# .github/workflows/DependabotAutoComment.yaml
name: Auto-merge Dependabot PRs

on:
  schedule:
    - cron: "0 */6 * * *" # Every 6 hours
  workflow_dispatch: # Manual trigger

jobs:
  auto-comment:
    runs-on: ubuntu-latest
    steps:
      - name: Run Dependabot Auto Comment Action
        uses: gesslar/dependabot-auto-merge@main
        with:
          token: ${{ secrets.WORKFLOW_MAINT }}
          merge-type: ${{ vars.DEPENDABOT_MERGE_TYPE }}
```

## Configuration reference

| Name | Kind | Description |
| --- | --- | --- |
| `WORKFLOW_MAINT` | secret | Token used to comment on / merge Dependabot PRs. |
| `DEPENDABOT_MERGE_TYPE` | variable | Merge strategy passed to the action. |

## See also

- [Workflow Cleanup](Workflow-Cleanup.md) — the other scheduled maintenance workflow.
