---
name: comment-triage
description: Triages review comments on the user's own pull request, from people and bots (CodeRabbit, SonarCloud). Checks each comment is real, likely, and in scope, recommends fix now, fix later, or skip, then makes the fixes, drafts replies, and checks that all feedback is covered. Never commits, posts, or resolves without approval. Use when the user asks if a comment on their PR is valid or worth fixing, asks to address PR feedback, or asks if all feedback is covered.
---

# Comment triage

Use this skill for comments on the user's own pull request. To review someone else's PR, use `pr-review` instead.

**Ask, don't assume.** If it is not clear which PR, which comments, or which branch, ask.

## 1. Collect the comments

1. Get the comments from the PR. If the user gives one or more comment links, triage only those. If the user says "the open comments" or "the feedback", take all unresolved threads.
2. Skip comments the user says are already addressed or are deliberate.
3. Note the source of each comment: a person, CodeRabbit, SonarCloud, or another bot.
4. Read the code the comment points at. Line numbers can move after a refactor or rebase. Find the current line.
5. Check which local copy to use (e.g. a second checkout or a worktree). Check that the local branch is in sync with the remote before you change code.

## 2. Triage each comment

**Question every CodeRabbit comment.** Do not accept it as correct. CodeRabbit often flags edge cases that are not worth fixing, for example because they are very unlikely to happen. Always check each CodeRabbit comment for veracity, accuracy, and scope (see the list below). Be sceptical first, and agree only when the code proves it.

**Question comments from people too.** Do not accept a comment as correct only because a reviewer wrote it.

For every comment, check:

- **Meaning**: what does the comment ask for? If the wording is unclear (e.g. "key it on the error identity"), explain it in plain words and show what the change would look like.
- **Veracity**: is the problem real in this code, or only in theory? Show the code path that proves or disproves it. Look for guards that already exist (e.g. a function that already catches errors and does not rethrow).
- **Accuracy**: are the comment's facts about the code and the framework correct?
- **Scope**: is it about this PR, or about code or behaviour outside it?
- **Likelihood**: who can hit it: a real user, an e2e test, or only development (e.g. React StrictMode effect replays)?
- **Where the fix belongs**: sometimes the right fix is in another repo (e.g. the backend), not in this PR.

When the user gives an opinion (e.g. "I think this is fine because..."), check it against the code. Say plainly if you agree or not, and why.

Do not add guards for edge cases that are not known or reasonably expected.

## 3. Report and recommend

Report in chat, one block per comment:

```
<n>. <comment link> (<person | CodeRabbit | SonarCloud>)
What it says: <one line>
Real? <yes | no | partly> - <proof, one or two lines>
Likelihood: <who can hit it>
Verdict: <fix now | fix later (non-blocking) | skip (not real or adds bloat) | fix elsewhere>
Fix: <recommended solution, size, risk>
```

If there is more than one way to fix a comment, give 2 or 3 options and say which one you recommend and why. Then stop and wait for the user to pick.

## 4. Make the fixes

Only fix the comments the user picks, with the approach the user picks.

1. If the bug can be shown in the running app, reproduce it first (e.g. with Playwright). Then verify that the fix works.
2. Add or update a test that pins the behaviour, when it makes sense.
3. Add a short "why" code comment when the reason for the code is not obvious (e.g. why it waits and retries).
4. Run the checks (lint, types, tests).
5. Bundle small related fixes into one commit when the user asks.
6. Let the user look at the changes locally before you commit, if the user asks.

If the real fix is in another repo or needs more work, draft a ticket (e.g. under the same epic). Show it to the user. Do not create it until the user approves.

## 5. Commit and push

- Show the commit message before you commit.
- Ask, or follow the user's words, on a new commit or an amend of the last commit.
- The user may stage, commit, or push. If the user says "I've pushed", do not push again.

## 6. Reply

- Draft a reply for each comment you handled. Keep it short:
  - Fixed: what changed (and the commit, if pushed).
  - Skip or later: why, in one or two lines (e.g. the guard that already exists, or why the case cannot happen).
- **Never post** a reply until the user approves the text. Post after the fix is pushed, so the reply matches the code on the PR.
- Resolve threads only when the user says to.
- If the PR is already merged, recommend a follow-up PR or ticket instead of a reply only.

## 7. Check that all feedback is covered

When the user asks "is all the feedback covered?" or "did I miss anything?":

1. List every comment on the PR.
2. Mark each one: fixed in code, answered in a reply, or still open.
3. Check that each fix does what the comment asked for. Flag any comment that looks misinterpreted.
4. Check the local or recent commits, not only what is pushed, if the user asks.
