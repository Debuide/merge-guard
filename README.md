# Merge Guard Action

A GitHub Action that helps maintain code quality by ensuring base branches are stable before allowing pull request merges. It also automatically reruns PR checks when the target branch is updated.

## Features

- 🔍 Validates that the base branch's checks are passing before allowing PR merges
- 🔄 Automatically reruns PR workflows when the target branch is updated
- 🚦 Provides clear status indicators and console output
- 🔕 Optional silent mode to skip checks

## Usage

Add this action to your workflow:

```yaml
name: Merge Guard
on: 
  pull_request:
    types: [opened, synchronize, reopened]
  push:
    branches:
      - main
      - develop
      
jobs:
  check-base:
    runs-on: ubuntu-latest
    steps:
      - name: Run Merge Guard
        uses: debuide/merge-guard@v1
        with:
          silent: false  # Optional (defaults to false)

```

## Inputs

| Input    | Description | Required | Default |
|----------|-------------|----------|---------|
| `silent` | When set to `true`, skips all checks and only logs warnings | No | `false` |

## Outputs

| Output | Description |
|--------|-------------|
| `check-status` | Status of PR checks: `success`, `failure`, or `skipped` |
| `rerun-status` | Status of rerun operation: `success` or `failure` |
| `rerun-count` | Number of PR workflows that were rerun |

## How It Works

### Pull Request Events

When a pull request is opened or updated, the action:
1. Checks the status of the base branch's workflows
2. Ensures all checks are passing before allowing the merge
3. Provides clear console output about the status
4. Fails the check if the base branch is not in a stable state

### Push Events

When code is pushed to a branch (e.g., main/develop), the action:
1. Identifies all open pull requests targeting the updated branch
2. Automatically reruns the workflow checks for each affected PR
3. Provides a summary of rerun operations

### Silent Mode

When `silent: true` is set:
- Skips all validation checks
- Outputs warnings about potential risks
- Useful for testing or special circumstances where checks need to be bypassed

## Example Console Output

### Pull Request Event
```
=== Pull Request Event ===
Checking PR #123
Title: Add new feature
Base Branch: main
✓ Base branch 'main' is green and ready to accept your PR #123
```

### Push Event
```
=== Push Event on Branch: main ===
Found 2 open PRs targeting main

Processing PR #124: Update documentation
Branch: feature/docs-update
✓ Triggered rerun for PR #124 (feature/docs-update)

Processing PR #125: Fix login bug
Branch: bugfix/login-issue
✓ Triggered rerun for PR #125 (bugfix/login-issue)

Summary: Triggered 2 PR workflow reruns
```

### Silent Mode
```
⚠️ WARNING: Checks are disabled!
This action is running in silent mode. No checks will be performed.
This may lead to merge issues if the base branch is failing.
```
