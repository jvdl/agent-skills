# Comment triage skill

This skill handles review comments on your own pull request. It works for comments from people and from bots like CodeRabbit and SonarCloud.

For each comment it checks if the problem is real, likely, and in scope. It then recommends fix now, fix later, or skip. After you pick, it makes the fixes, drafts the replies, and checks that all feedback is covered.

It never commits, posts a reply, or resolves a thread until you approve.

To review someone else's PR, use [pr-review](../pr-review/) instead.

Examples:

- `/comment-triage <comment url> is this valid and worth fixing?`
- `/comment-triage look at the open comments on my PR and recommend a fix for each`
- `/comment-triage check my commits cover all the feedback on the PR`
