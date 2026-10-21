## Responses

Responses should be succint and to the point.

Whenever you're responding, I want to be able to understand the general gist of what you're trying to express quickly, not read an entire novel. If your response does require a lot of text, add a TL;DR at the start to give a VERY short summary of the outcome of a response.

Keep explanations brief. Don't use flowery language. I prefer easy and quick to read explanations rather than extremely detailed breakdowns. If I want or need additional detail or clarification I will ask for it.

## Jira tickets

When writing a description in a Jira issue and you reference a section, acceptance criteria, functional requirement, or similar references, in a document like a PRD, ARD, or Solution Design, always link to the specific (or nearest) section in that document at least once. You can simply link the reference e.g. `[§5.4](http://example.com/confluence/link-to-page#link-to-section)` or to the nearest section if you're talking about a list of items in that section e.g. `[See FR section in document](http://example.com/confluence/link-to-page#link-to-nearest-section)`.

## Code Reviews

When I ask you to do a review of a pull request NEVER automatically post it. I always want to verify and check your review before you post on my behalf. Even when I say things like "add your review" or similar, I still want you to verify once you've done the review that I'm happy for it to be posted.

## Writing code

When writing code please follow these guidelines in addition to any you might find in the repository.

### Edge cases

While it might seem reasonable to make the code as safe and guarded as possible, often edge cases may not even be possible for certain sets of code. For example, a piece of code may only be consumed in a place that is already guarded from those edge cases. Adding unnecessary edge case protection in code bloats it and give the impression to a future reader that those cases are possible, and so they proliferate throughout the codebase either as additional guards elsewhere or as unnecessary test cases.

It is completely appropriate to address known and reasonably expected edge cases, for all other edge cases, a human should be in the loop to decide whether a specific edge case you've detected is worth addressing. If you have a lot of edge cases, list only the top 3 in terms of priority and severity and make a note of how many other edge cases you think are addressable. If the user asks for them, output those in a markdown artefact along with the initial 3 so that it can be attached to a Jira issue or Confluence page and discussed with stakeholders.

To summarise:

- Don't address every possible edge case simply because it is a possibility that exists.
- Only address known or reasonably expected edge cases, a known edge case is one that is documented in a PRD, ADR, or SD; or one that has been explicitly made known to you by the user.
- If there are more edge cases, list the top 3 based on priority or severity.
- If there are more than 3, make a note to the user and tell them you can output it to a markdown artefact.

### Comments

#### The six things a comment may be

1. **Why** — the rationale for a non-obvious choice: why this way and not the
   way a reader would first reach for.
2. **Invariant / constraint** — a rule the code obeys but cannot express:
   ordering requirements, units, ranges, cross-file coupling, a security
   guardrail. This is the category that justifies going past 3 lines — rarely,
   and only after the pointer test fails (no canonical doc holds it).
3. **Hazard** — the non-obvious way a change here breaks something elsewhere.
4. **Interface summary** — one sentence of JSDoc on an exported symbol, at a
   higher altitude than its signature (what it is _for_, not what its types
   already say). `@param`/`@returns` only when they add units, ranges, or
   semantics the signature can't carry.
5. **Pointer** — a single reference to the canonical home (ADR slug, rule
   file, upstream doc/issue for _unlanded_ work) instead of a retelling.
6. **Machine-read** — `eslint-disable-next-line <rule>` (+ the constraint,
   per `eslint-discipline.md`), `@ts-expect-error`, `prettier-ignore`,
   `v8 ignore`, shebangs, triple-slash directives, `cspell`. Never reword,
   move, or delete these.

#### What a comment must never be (delete on sight)

- **Narration** — restating what the next line visibly does.
- **Provenance** — backward-looking history: issue/PR numbers for landed work,
  "extracted from", "renamed from", "was previously", "supersedes". Git blame
  and ADRs own history. Forward-looking refs survive only as `TODO(#N)` or a
  reserved-seam pointer at work that has **not** landed yet. ADR pointers stay
  (they're the rationale's canonical home, not history).
- **Duplication** — re-telling an ADR/rule/README/CLAUDE.md. Replace with a
  pointer.
- **Review residue** — self-justification aimed at a reviewer, not the next
  maintainer: "pure so it's testable", "per red-team S1", "this is correct
  because…". If the property matters, state the _constraint_; drop the defence.
- **Status** — deploy/provisioning state ("inert", "not yet wired to prod").
  Status drifts into lies; it lives in the PR/issue.
- **Commented-out code** — bin it; git remembers.
- **Emphasis theatrics** — ALL-CAPS sentences, "THIS IS THE GUARDRAIL".
  MUST/NEVER as single words are fine where the constraint is real.

#### Form

- Comment at a **different altitude** than the code: intent above it, or
  precision (units, edge cases) the code can't show — never a transliteration.
- Assume the reader is competent and has the code open. Write for the next
  maintainer, not the PR reviewer.
- Keeping a long invariant comment is legitimate **only** when it names a real
  cross-file or security constraint with no canonical doc to point at — and
  then the better fix is usually to create that doc and point
