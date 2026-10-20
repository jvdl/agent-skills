---
name: blind-review
description: Reviews uncommitted or branch changes with fresh reviewer subagents that see only the change's claims and the code, never the author's reasoning, optionally checks the claims in a running app with Playwright, then verifies the findings and reports a concise ranked list with options and a recommendation. Use when the user asks to review work "as if you hadn't done it", for a blind or fresh-eyes review, or to check changes against their claims before committing or opening a PR.
---

# Blind review

The author knows why the code is the way it is, and that knowledge hides bugs. A reviewer that gets only **the claims** and **the code** has to check each claim against what the code really does.

**Ask, don't assume.** At every step, if something is not clear from the user's words, the code, the ticket, or the user's memory or CLAUDE.md, ask. When you have to assume something that is not based on clear facts, give it to the user as 2–3 options, with the context to pick fast and your recommendation. Do not settle it silently.

## Workflow

- [ ] 1. Pin the scope
- [ ] 2. Write the claims
- [ ] 3. Spawn blind reviewers (and a UI checker, if an app is running)
- [ ] 4. Verify the findings
- [ ] 5. Report, then ask

### 1. Pin the scope

For each repo, name the branch, the base (e.g. `origin/main`), and exactly what to review: `git diff`, untracked files, or `git diff <base>...HEAD`. List the files to leave out (e.g. a local `config/runtime.exs`) and say why.

Optional inputs from the user, for a UI check:
- where the app runs (e.g. "frontend is running at http://127.0.0.1:5174")
- where a test login lives (a file and key names, e.g. `.env.localdev.local` `LOCAL_E2E_USER`/`LOCAL_E2E_PASS`)
- which data the check may change

The user may name these loosely, e.g. "FE localhost:5174, admin credentials". Resolve each one from the user's memory or CLAUDE.md, and say which pointer you used. If nothing there resolves it, or it could mean more than one thing (e.g. "credentials" when the change touches both customer and admin pages), ask. Never guess a login, and never search the disk for credentials.

### 2. Write the claims

Write a numbered list of what the change says it does, in the requirement's words: behaviour per state, labels, error handling, API calls, translations, tests, and "no behaviour change" for refactors.

Take the claims from what the user asked for in this session. If this session did not make the change, take them from the ticket, the PR description, or the commit messages, never from reading the code: claims made from the code only describe what it already does.

Include agreed decisions as claims (e.g. "Turn Off is not shown for `reauth_required`, because the backend refuses it"), so the reviewer checks them and does not report them as bugs.

**Never include** how you built it, why you chose an approach, or anything you found while building it. Those are the assumptions under test.

Before you start reviewers, show the user the scope, the claims, and any open questions or assumptions (as options), then wait for their answer. A wrong claim wastes every reviewer's run.

### 3. Spawn blind reviewers

Use one `general-purpose` Agent per repo or independent area, all in one message so they run in parallel. Each prompt contains:

- "You are reviewing changes you did not write. Assume nothing; verify by reading code. Read-only: do not edit, commit, push, post, or start or stop servers."
- The repo path, branch, base, and diff command, and "read every new file in full".
- The standards files to check against (repo `CLAUDE.md`, user `CLAUDE.md`), and "skip what lint and type checks enforce".
- The claims list.
- "Where a claim or the code is ambiguous, do not pick an answer: report it under Open questions, with the options."
- The review dimensions:
  - **Logic bugs**, each with a concrete failure scenario. Name the risky areas: live props changing mid-flow, double submits, closing mid-request, state left over between actions, refactor parity with the base (`git show <base>:<file>`).
  - **Security**: tenant scoping, leaked data, untranslated backend text shown to users.
  - **Consistency**: naming, casing, error handling, test patterns.
  - **Reusability**: third copies that should be shared, abstractions that don't pay off.
  - **Reviewability**: mixed refactor and feature, renames, suggested commit split.
- The test and type-check commands, with any environment the repo needs (e.g. `DB_PORT=5433`).
- The report format: findings ranked most severe first, each with `file:line`, the problem in one sentence, a scenario, a severity (high/medium/low), and verified or suspected. Then "claims not fully true", then "open questions". Give a word limit (500–700).

If the user gave a running app, spawn one more agent in the same message: a **UI checker**. It gets the claims and the app address but **not the diff**, so it judges only what a user sees. See [UI-CHECKS.md](UI-CHECKS.md) for its prompt and safety rules.

### 4. Verify the findings

Reviewer output is a claim, not proof. Before reporting, read the code for each medium or higher finding, and mark it **verified** or **not reproduced**. Drop findings that are wrong, and say that you dropped them. For a failed UI claim, look at its screenshot, and find the cause in the code before you report it.

### 5. Report, then ask

Put the full report **in the chat message itself**, not only in a recap or a question box. The user has not seen the reviewers' output.

1. **TL;DR**: severity counts and whether the tests pass.
2. **Findings by severity**: number, title, repo, `file:line`, a 1–2 line problem with an example, and a proposed fix where obvious.
3. **UI checks** (if run): each claim as pass, fail or not checked (with the reason), and any data the checker changed.
4. **Reviewability**: suggested commit or PR split.
5. **Questions, not bugs**: decisions only the user can make.
6. **Recommendation**: what to fix now, what to leave, and why.

Then ask which findings to fix. Do not change code, commit, or post review comments until the user answers.

## Example claim lines

```
1. Details dialog shows per-status buttons: connect_failed → "Cancel Installation" + "Retry".
4. Refused requests show a translated message chosen by HTTP status, never the backend's text.
3. The hand-off logic was extracted into useInstallationHandoff with no change in install behaviour.
```
