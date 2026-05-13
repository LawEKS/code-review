---
description: Review a GitHub pull request and post inline comments via `gh`
argument-hint: [pr-url-or-number]
allowed-tools: Bash(gh pr view:*), Bash(gh pr diff:*), Bash(gh api:*), Bash(gh auth status:*), Read, Grep, Glob
---

Invoke the `pr-code-review` skill on the pull request identified by: $ARGUMENTS

If no arguments are provided, fall back to the `REPOSITORY`, `PULL_REQUEST_NUMBER`, and `ADDITIONAL_CONTEXT` environment variables. If the PR still cannot be resolved, ask the user for clarification.
