---
name: pr-code-review
description: Reviews a GitHub pull request via the `gh` CLI and posts inline comments plus a summary review. Use when the user asks to review a pull request, PR, or provides a GitHub PR URL or number. Requires the `gh` CLI to be installed and authenticated.
allowed-tools: Bash(gh pr view:*), Bash(gh pr diff:*), Bash(gh api:*), Bash(gh auth status:*), Read, Grep, Glob
---

# PR Code Review

## CONTEXT

- **GitHub Repository**: `$REPOSITORY` (env var) or parsed from the user-provided PR URL.
- **Pull Request Number**: `$PULL_REQUEST_NUMBER` (env var) or parsed from the user-provided PR URL.
- **Additional User Instructions**: `$ADDITIONAL_CONTEXT` (env var) or anything else passed as arguments.
- **PR Details**: run `gh pr view <NUMBER> --repo <REPO> --json number,title,body,headRefOid,baseRefName,headRefName,author,labels,url` to get title, body, head commit SHA, and metadata.
- **Code Changes**: run `gh pr diff <NUMBER> --repo <REPO>` to retrieve the diff.

If `<REPOSITORY>` and `<PULL_REQUEST_NUMBER>` cannot be resolved from arguments or env vars, ask the user for clarification before proceeding. Accepted input forms: a full PR URL (`https://github.com/<owner>/<repo>/pull/<n>`), `<owner>/<repo>#<n>`, or just `<n>` when already inside a clone of the repo (then `gh pr view <n>` resolves automatically).

## PROTOCOL

Activate the `code-review-commons` skill for persona, objective, instructions, and critical constraints. Then submit the review following the exact structure and rules in the `<SUBMIT_REVIEW>` section.

## SUBMIT_REVIEW

**Review Comment Formatting**

- **Line Accuracy:** Suggestions must align with the line numbers and indentation of the code they replace.
    - Comments on the before (LEFT) side of the diff **MUST** use line numbers from the pre-diff and `"side": "LEFT"`.
    - Comments on the after (RIGHT) side of the diff **MUST** use line numbers from the post-diff and `"side": "RIGHT"`.

1. **Build the review payload locally.** Collect every inline comment as an entry in a `comments` array and compose the top-level summary `body`.

    1a. For each comment with a code suggestion, the comment `body` **MUST** use this exact template:

        [{{SEVERITY}}] {{COMMENT_TEXT}}

        ```suggestion
        {{CODE_SUGGESTION}}
        ```

    1b. When there is no code suggestion, the comment `body` **MUST** use:

        [{{SEVERITY}}] {{COMMENT_TEXT}}

2. **Submit the review in a single call** with `gh api`:

    ```
    gh api -X POST repos/<OWNER>/<REPO>/pulls/<NUMBER>/reviews \
      -f commit_id=<HEAD_REF_OID> \
      -f event=COMMENT \
      -f body="<SUMMARY_BODY>" \
      -f comments='[ {"path": "...", "line": N, "side": "RIGHT", "body": "..."}, ... ]'
    ```

    The `event` field **MUST** be `"COMMENT"`. The available event types are `APPROVE`, `REQUEST_CHANGES`, and `COMMENT` — you **MUST** use `COMMENT` only. **DO NOT** use `APPROVE` or `REQUEST_CHANGES`.

    For payloads that are awkward to pass as repeated `-f` flags (e.g. large `comments` arrays with nested JSON), write the body to a temporary file and pipe with `--input -`:

    ```
    gh api -X POST repos/<OWNER>/<REPO>/pulls/<NUMBER>/reviews --input - < /tmp/review.json
    ```

    where `/tmp/review.json` is the full JSON object with `commit_id`, `event`, `body`, and `comments`.

3. The summary `body` **MUST** use this exact markdown format:

    ## 📋 Review Summary

    A brief, high-level assessment of the Pull Request's objective and quality (2-3 sentences).

    ## 🔍 General Feedback

    - A bulleted list of general observations, positive highlights, or recurring patterns not suitable for inline comments.
    - Keep this section concise and do not repeat details already covered in inline comments.
