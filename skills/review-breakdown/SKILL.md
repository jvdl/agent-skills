---
name: review-breakdown
description: Rebuild a PR's commit history into small, logical, reviewable commits, starting fresh from the branch point with main. Ignores the old commit boundaries. Can propose a stacked PR set, but only creates it after explicit approval. Use when the user runs /review-breakdown, or asks to split up, restructure, re-chunk, or make a big or complex PR easier to review.
disable-model-invocation: true
argument-hint: "[base branch, default: main]"
---

# Review breakdown

Goal: the reviewer understands the PR with the least effort.
The final code must stay exactly the same. Only the history changes.

Base branch: `$ARGUMENTS` if given, otherwise `main`.

## 1. Check and protect

1. Run `git status`. If the working tree is not clean, stop and tell the user.
2. Record the current branch name and HEAD SHA.
3. Make a backup branch: `git branch review-breakdown-backup/<branch>`.
4. Fetch the base: `git fetch origin <base>`.
5. Find the branch point: `git merge-base HEAD origin/<base>`.

Do not continue without the backup branch.

## 2. Understand the final state

Study the full change, not the old commits:

- `git diff <merge-base>...HEAD --stat`
- `git diff <merge-base>...HEAD`
- Read the PR description if there is one (`gh pr view`).

Before you write any plan, understand what the PR does and why.
Find the dependencies between changes: what must exist before what.

## 3. Plan the chunks

Ignore the old commit boundaries. Group by meaning, not by file or by time.

Good chunk order, from first to last:

1. Pure refactors and renames. No behaviour change.
2. New types, interfaces, schemas, and migrations.
3. Core logic, one concept per commit.
4. Wiring: connect the new logic to callers, config, and routes.
5. Tests for each part. Put a test in the same commit as the code it tests, when that helps the reviewer.
6. Cleanup: remove dead code, update docs.

Rules for each commit:

- One idea per commit. The message title says that idea.
- Each commit must build and pass tests, if possible. If not possible, say so in the commit message.
- Keep mechanical changes (formatting, moves, bulk renames) apart from logic changes. A reviewer can skim mechanical changes.
- Prefer many small commits over a few big ones. But do not split one idea across commits.
- Commit body: what changed, why, and what the reviewer should look at closely.

Show the plan to the user before you rewrite anything:
a numbered list of commits, each with a title and a one-line summary.

## 4. Decide: one PR or a stack

A stacked PR is a chain of PRs, where each PR builds on the one before it.
Suggest a stack only if it really helps, for example:

- The change has clear layers that a reviewer can approve one at a time.
- Different parts need different reviewers.
- The total diff is too large for one sitting.

If you suggest a stack, show each PR: title, which commits it holds, and its base branch.
Then STOP and wait for the user to approve. Do not create branches or PRs before approval.

## 5. Rewrite

1. `git reset --soft <merge-base>`, then `git reset` to unstage everything. All changes are now unstaged in the working tree.
2. Build each commit with `git add -p` or `git add <paths>`. For a file that spans several commits, stage hunks, or edit the file to an intermediate state.
3. After each commit, run the build and tests if they are fast. Fix the chunk if it breaks.

## 6. Verify

The final tree must match the backup exactly:

```
git diff review-breakdown-backup/<branch> HEAD
```

This must print nothing. If it prints anything, find and fix the difference. Do not finish until it is empty.

Also check `git log --oneline <merge-base>..HEAD` reads as a clear story.

## 7. Publish

- Single PR: ask before you force-push. Then use `git push --force-with-lease`.
- Approved stack: make one branch per PR, each based on the one before. Create the PRs with `gh pr create --base <previous-branch>`. In each PR description, link the full stack and mark this PR's place in it. Ask before you close or retarget the original PR.

## 8. Report

Keep it short:

- What you did: the new commit list (and stack, if any).
- Did it work: the result of the verify step and the tests.
- What the user does now: for example, review the plan, approve the push, or delete the backup branch with `git branch -D review-breakdown-backup/<branch>`.
