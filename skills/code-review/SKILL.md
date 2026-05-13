---
name: code-review
description: Reviews uncommitted or in-flight code changes on the current branch for bugs, security issues, performance bottlenecks, and clarity. Use when the user asks to review their changes, diff, branch, staged code, or work-in-progress before committing or opening a PR.
allowed-tools: Bash(git diff:*), Bash(git merge-base:*), Bash(git status:*), Bash(git rev-parse:*), Read, Grep, Glob
---

# Code Review (local diff)

## CONTEXT

**Code Changes**: run `git diff -U5 --merge-base origin/HEAD` to retrieve the changes on the current branch relative to its merge base with the remote default branch.

If `origin/HEAD` is not set, fall back to `git diff -U5 --merge-base origin/main` (or `origin/master`). If there is no remote, diff against the local default branch.

## PROTOCOL

Activate the `code-review-commons` skill for persona, objective, instructions, and critical constraints. Then follow the exact structure and rules in the `<OUTPUT>` section below.

## OUTPUT

The output **MUST** be clean, concise, and structured exactly as follows.

**If no issues are found:**

# Change summary: [Single sentence description of the overall change].
No issues found. Code looks clean and ready to merge.

**If issues are found:**

# Change summary: [Single sentence description of the overall change].
[Optional general feedback for the entire change, e.g., unrelated change that should be in a different PR, or improved general approaches.]

## File: path/to/file/one
### L<LINE_NUMBER>: [<SEVERITY>] Single sentence summary of the issue.

More details about the issue, including why it is an issue (e.g., "This could lead to a null pointer exception").

Suggested change:
```
    while (condition) {
      unchanged line;
-     remove this;
+     replace it with this;
+     and this;
      but keep this the same;
    }
```

### L<LINE_NUMBER_2>: [MEDIUM] Summary of the next problem.
More details about this problem, including where else it occurs if applicable (e.g., "Also seen in lines L45, L67 of this file.").

## File: path/to/file/two
### L<LINE_NUMBER_3>: [HIGH] Summary of the issue in the next file.
Details...
