# Workflow Cleanup

Deletes old workflow runs on a schedule to keep your Actions history tidy, using the
[`gesslar/workflow-run-cleaner`](https://github.com/gesslar/workflow-run-cleaner) action.

> **This is a standalone workflow, not a reusable one.** You don't `uses:` it — you **copy the
> file** into your repo's `.github/workflows/` and configure a secret and a variable.

## What it does

Runs daily at midnight UTC (and on manual dispatch) and invokes the `workflow-run-cleaner`
action to delete workflow runs older than a threshold number of days.

## Setup

1. Copy the workflow file (below) into `.github/workflows/` in your repo.
2. Add a repository secret **`WORKFLOW_MAINT`** — a token with permission to delete runs.
3. Set a repository variable **`CLEANUP_DAYS_THRESHOLD`** — the age (in days) beyond which
   runs are deleted.

## The workflow file

```yaml
# .github/workflows/WorkflowCleanup.yaml
name: Cleanup Old Workflow Runs

on:
  schedule:
    - cron: '0 0 * * *'  # Daily at midnight UTC
  workflow_dispatch:     # Manual trigger

jobs:
  cleanup:
    runs-on: ubuntu-latest
    steps:
      - name: Run Workflow Cleanup Action
        uses: gesslar/workflow-run-cleaner@main
        with:
          token: ${{ secrets.WORKFLOW_MAINT }}
          days-threshold: ${{ vars.CLEANUP_DAYS_THRESHOLD }}
```

## Configuration reference

| Name | Kind | Description |
| --- | --- | --- |
| `WORKFLOW_MAINT` | secret | Token used to delete old workflow runs. |
| `CLEANUP_DAYS_THRESHOLD` | variable | Runs older than this many days are deleted. |

## See also

- [Dependabot Auto Comment](Dependabot-Auto-Comment.md) — the other scheduled maintenance workflow.
