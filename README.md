# Claude Code — Code Review Plugin

A Claude Code plugin that adds high-quality, opinionated code review for both **local branch changes** and **GitHub pull requests**.

This plugin is brought to you by the authors of the [Gemini Code Assist GitHub App](https://github.com/apps/gemini-code-assist), which provides code reviews directly in your GitHub pull requests.

## Requirements

- [Claude Code](https://docs.claude.com/en/docs/claude-code) installed.
- `git` available on `PATH`.
- For pull request review only: the [`gh` CLI](https://cli.github.com/) installed and authenticated (`gh auth login`).

## Installation

Clone this repo somewhere local, then load it as a Claude Code plugin from its local path. For example:

```bash
git clone https://github.com/gemini-cli-extensions/code-review.git ~/.claude/plugins/code-review
```

Restart Claude Code (or reload plugins). Confirm the plugin is loaded with `/plugin`. The `code-review`, `pr-code-review`, and `code-review-commons` skills should be listed, along with the `/code-review` and `/pr-code-review` slash commands.

## Usage

### Review local changes

```text
/code-review
```

Reviews the diff between the current branch and its merge base with `origin/HEAD` and prints structured findings (severity-tagged, with suggested patches).

You can also just ask Claude in natural language — the `code-review` skill auto-triggers on phrases like *"review my changes"*, *"review this diff"*, or *"review my branch"*.

### Review a pull request

```text
/pr-code-review https://github.com/owner/repo/pull/123
```

Or pass the number directly when inside a clone of the target repo:

```text
/pr-code-review 123
```

The `pr-code-review` skill fetches the PR via `gh pr view` / `gh pr diff`, produces inline severity-tagged comments and a summary, and submits a `COMMENT`-type review to GitHub.

Configuration via environment variables (any/all of these can be set instead of passing arguments):

- `REPOSITORY` — `owner/repo` of the PR.
- `PULL_REQUEST_NUMBER` — the PR number.
- `ADDITIONAL_CONTEXT` — extra instructions or focus areas for the reviewer.

The skill never submits `APPROVE` or `REQUEST_CHANGES` events — only `COMMENT` reviews.

## How it works

- `skills/code-review-commons/SKILL.md` — shared reviewer persona, objective, instructions, and severity classification (CRITICAL / HIGH / MEDIUM / LOW). Activated by both review skills.
- `skills/code-review/SKILL.md` — local-diff review skill.
- `skills/pr-code-review/SKILL.md` — pull request review skill (uses `gh`).
- `commands/code-review.md`, `commands/pr-code-review.md` — slash command entry points.

## Legal

- License: [Apache License 2.0](LICENSE)
