# Agent Toolkit

A collection of various docs like skills and output styles to use with agents to improve productivity and reduce slop.

Sometimes working with AI tooling can be exhausting, the output is verbose, they generate a LOT of code where a human might have been more judicious about it. Machines will produce for machines until you tell them not to.

## Skills

[review-breakdown](./skills/review-breakdown/) aims to break down a review by breaking the changes up in to logically chunked commits and for larger more compex changes will propose a stacked pull request.

[pr-review](./skills/pr-review/) reviews someone else's pull request against its claims and ticket ACs, re-reviews addressed feedback, and triages single review comments. It never posts without your approval.

[comment-triage](./skills/comment-triage/) triages review comments on your own pull request, from people and bots. It recommends fix now, fix later, or skip, then fixes, drafts replies, and checks all feedback is covered.

## Output styles

Agentic output can be verbose, full of jargon, tell you lots of stuff that isn't necessarily relevant.

[ELI5](./output-styles/ELI5.md) aims to keep language simple and remove a lot of the fluff.

## Claude guidance

Don't leave out guidance for your own agent. I've included my [CLAUDE.md](./.claude/CLAUDE.md) here for example. Customise it to how you want it to work for you. Don't like how it does something? "Tell it" by encoding it in your or your team's CLAUDE.md and save future headaches.

## Other packages

I use skills from both [matpocock/skills](https://github.com/mattpocock/skills) and [pstack](https://github.com/backnotprop/pstack).
