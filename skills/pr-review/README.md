# PR review skill

This skill reviews a pull request that someone else wrote. It checks the PR against its own claims and the ticket's acceptance criteria (ACs), and stays inside the PR's scope.

It has five modes: full review, light pass, re-review of addressed feedback, triage of a single comment (e.g. from CodeRabbit), and docs review.

It reports in chat and never posts on the PR until you approve the exact text.

To review your own work before you open a PR, use [blind-review](../blind-review/) instead.

Examples:

- `/pr-review https://github.com/org/repo/pull/123 light pass, check against the ticket ACs`
- `/pr-review the feedback was addressed, re-review and check nothing new broke`
- `/pr-review is this CodeRabbit comment worth fixing? <comment url>`
