---
name: finishing-a-development-branch
description: Use when implementation is complete, all tests pass, and you need to
  review the work before integration. Verifies tests and presents the
  commit-by-commit history; never pushes, opens PRs, or merges.
---

# Finishing a Development Branch

## Overview

**Core principle:** Verify tests → Present commits → Leave the branch in place for the user to review and integrate.

**Announce at start:** "I'm using the finishing-a-development-branch skill to complete this work."

The agent does not push, open pull requests, or merge. The human partner reviews
the work commit-by-commit and handles all integration and merging themselves.

## Step 1: Verify Tests

Run the project's full test suite (`npm test` / `cargo test` / `pytest` / `go test ./...`).

**If tests fail**, report the failures and stop — do not present the work as finished:

```
Tests failing (<N> failures). Must fix before finishing:

[Show failures]
```

**If tests pass:** continue to Step 2.

## Step 2: Present the Commits

Present the commit-by-commit history of the branch so the human partner can
review each change in order. Show the oneline log for the work:

```bash
git log --oneline <base>..HEAD
```

Where `<base>` is the commit the work forked from (or `git merge-base <base> HEAD`).
For each commit, note its scope so the reviewer knows what to look at.

Do NOT squash, amend, or rewrite history without being asked. Leave the commits
as the implementer created them.

## Step 3: Leave the Branch In Place

Report the state and stop. Do not push, open a PR, or merge:

```
Implementation complete. <N> tests passing. Branch <name> left in place.
Commits (newest first):
<commit list>

Review them and integrate when ready.
```

The human partner reviews the commits and handles integration and merging
themselves. If they later ask for a local merge, you may run it then — but never
unprompted.

## Common Rationalizations

| Excuse | Reality |
|--------|---------|
| "They obviously want it merged" | Integration is the human partner's decision. Present the commits and stop. |
| "I'll just push so it's backed up" | The work is already in git commits. Pushing changes shared state without consent. |
| "I'll open a PR to make review easier" | PRs are a forge action the partner didn't ask for. Leave it to them. |
| "Tests passed earlier this session" | Run the suite on the tree you're finishing. A green run only proves the tree it ran on. |
| "I'll clean up the branch myself" | Leave the branch in place. Cleanup is part of integration, which the partner owns. |
