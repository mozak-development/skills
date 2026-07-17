---
name: commit
description: Creates a commit with a defined message format following the conventional commit standard. 
---

Create a git commit for the current changes using a concise Conventional Commits-style subject.

Only commit; do NOT push.

## Format

- use suggested format and structure for conventional commit messages
- Use these for: fix, feat, build, chore, ci, docs, style, refactor, perf, test
- always use a scope like so: "feat(scope):" determine scope from module directory or a condensed domain term
- The description of the commit message should be in the form of bullet points with a leading '-' letter.


## Description

- Generate a commit message for those changes.
- The commit message MUST use the imperative tense.
- The commit message should come with a description after the title with a blank line.
- description is **only optional** if the change is trivial, otherwise add a description
- the title of the commit should be very long, but it needs to summarize the problem that was fixed by this commit. If there are several mixed issues addressed, connect reasons with a ","
- The optional commit description should reflect the most important changes in the commit.
- don't include very small code changes in description
- use a bullet point list for the description. Avoid empty lines between them. Each bullet point should be prefixed with a dash.
- Treat any caller-provided arguments as additional commit guidance. Common patterns:
    - Freeform instructions should influence scope, summary, and body.
    - File paths or globs should limit which files to commit. If files are specified, only stage/commit those unless the user explicitly asks otherwise.
    - If arguments combine files and instructions, honor both.



## Steps

1. Infer from the prompt if the user provided specific file paths/globs and/or additional instructions.
2. Review `git status` and `git diff` to understand the current changes (limit to argument-specified files if provided).
3. (Optional) Run `git log -n 50 --pretty=format:%s` to see commonly used scopes.
4. If there are ambiguous extra files, ask the user for clarification before committing.
5. Stage only the intended files (all changes if no files specified).
6. Run `git commit -m "<subject>"` (and `-m "<body>"` if needed).
7. If the commit is successful, run `git log -1` and include its output in the final response.

