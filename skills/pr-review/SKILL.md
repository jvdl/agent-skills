---
name: pr-review
description: Reviews someone else's pull request against its claims and the ticket's ACs, re-reviews addressed feedback, or triages a single bot or reviewer comment (e.g. CodeRabbit). Reports in chat and never posts without approval. Use when the user asks to review, re-review, or give a light pass on a PR, or asks if a PR comment is valid or worth fixing.
---

# PR review

Use this skill to review a pull request that someone else wrote. To review your own work before you open a PR, use `blind-review` instead.

**Ask, don't assume.** If the scope, ticket, or depth is not clear from the user's words, the PR, or the user's memory or CLAUDE.md, ask.

## 1. Pick the mode

Read the request and pick one mode. If it is not clear, use **Full**.

- **Full**: a new PR. Check claims, ACs, and code.
- **Light pass**: the user says "light", "quick", "once-over", or "already approved". Check claims and ACs. Report only clear problems.
- **Re-review**: "feedback was addressed", "re-review", "verify the changes". See section 5.
- **Comment triage**: a link to one comment from a bot or a person. See section 6.
- **Docs review**: the PR is mostly documentation. See section 7.

## 2. Collect context before you read code

1. Read the PR description. List each claim it makes.
2. Find the ticket (Jira, Linear, or other). If the user names one source, use that one. List each AC.
3. Note what is out of scope: descoped items, follow-up tickets, exemptions in the PR body, and other PRs in a stack. Do not flag these.
4. Read the reviews and comments already on the PR, from people and bots. Do not repeat a point that is already raised. If you agree or disagree with one, say so in one line. Question CodeRabbit comments before you agree with them (see section 6).
5. Check for a related PR in another repo (e.g. backend and frontend). If the user says a separate review is running for it, use it only to verify claims. Do not review it.

## 3. Review the code

Stay inside the PR diff. Read other code only to verify a claim or a call path. Do not trawl the codebase.

Check these points:

- **Claims and ACs**: does the code do what the PR and ticket say? Mark each AC as met, partly met, or not met.
- **Logic bugs and edge cases**: wrong branches, missing states, null or empty input. Report only known or reasonably expected edge cases. If there are many, give the top 3 and say how many more you found.
- **Race conditions**: double submits, in-flight requests, stale state after save, effect replays.
- **Security**: can user input break or go around a check? Look for auth and permission gaps between roles (e.g. admin and customer endpoints).
- **API usability** (backend): are the endpoints complete and documented? Does every 4xx (client error) response have a clear message that tells the client which field or rule failed?
- **Frontend/backend consistency**: business rules must come from the backend. Flag a rule that the frontend makes again on its own. Flag frontend filtering of lists that the backend already scopes.
- **Consistency and reuse**: duplicated logic, or an existing helper that the PR ignores.
- **Reviewability**: PR size, mixed concerns, commits that should be split.

If the user names a special focus, check it first and report it first.

## 4. Verify in the running app (when asked or when useful)

- Get the app URL, test login, and mock data setup from the user's words, memory, or CLAUDE.md. Say which pointer you used.
- Never guess a login. Never search the disk for credentials.
- Use Playwright to reproduce findings and to check the flows the PR changes.
- Assume the author will resolve merge conflicts, unless the user says otherwise.

## 5. Re-review

1. List each earlier comment. Mark it: fixed in code, answered in a reply, or still open.
2. Check that each fix does what the comment asked for, not only that something changed.
3. Check the new code for new problems. Do not re-review unchanged code.
4. Follow the user's notes for this round (e.g. "ignore currency rendering" or "the convention breaks are deliberate"). Do not raise those points again.

## 6. Comment triage

**Question every CodeRabbit comment.** Do not accept it as correct. CodeRabbit often flags edge cases that are not worth fixing, for example because they are very unlikely to happen. Check each comment for:

- **Veracity**: is the problem real in this code, or only in theory?
- **Accuracy**: are its facts about the code correct (call paths, guards, types, framework behaviour)?
- **Scope**: is it about this PR, or about code or behaviour outside it?

Apply the same checks when CodeRabbit comments are already on the PR during a full review (section 2, step 4).

For one comment, answer:

1. Is it correct? Show the code path that proves or disproves it.
2. How likely is the problem in real use (a person, an e2e test, development only)?
3. Verdict: fix now, fix later (non-blocking), or skip (not real, or it adds bloat).
4. If fix: the size of the change and the risk.

## 7. Docs review

- Check that the doc does not contradict the documents it links to.
- Check the facts against the code.
- Read it as the target reader (e.g. an external partner). Is there enough to build with? Does something extra or missing make their job harder? Is the level of detail right?
- Flag unclear wording.

## 8. Report

Report in chat. Keep it short. Give only important and actionable points.

```
Verdict: <approve / approve with comments / request changes>

ACs: <met / partly / not met, one line each>

Findings (most severe first):
1. [blocking | non-blocking | nit | question] <file:line> - <problem>. <why it matters>. <suggested fix>.

Checked and fine: <one line>

Options: <2 or 3, with the one you recommend and why>
```

## 9. Posting rules

- **Never post** a review, comment, approval, or resolved thread until the user approves the exact text. This applies even when the user says "add your review".
- When asked to draft: write each comment as an inline comment with its file, line, and label (blocking, non-blocking, nit, question).
- Post only the points the user picks. Use the review type the user names (comment, request changes, approve) and the summary text the user names.
