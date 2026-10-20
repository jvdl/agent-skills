# UI checks

A UI checker tests the claims in a running app with Playwright, the way a user would. It gets **no diff**: if a claim is true only in the code, the UI checker finds that out.

Run one only when the user has said where the app runs. Run it in parallel with the code reviewers.

## What the prompt contains

- "You are checking claims about a running app. You have not seen the code, and you must not read the diff. Check each claim the way a user would."
- The app address the user gave, and the page or route to start from.
- Where the login lives: the file path and key names only, never the values.
- The claims that a user can see in the app. Leave out claims about code structure or tests.
- The data rules below, word for word.
- "If a claim could mean more than one thing in the UI, check what you can, and list the options under Open questions. Do not pick one."
- The report format below.

## Data rules

1. **Only the addresses the user gave**, and only local dev or test hosts. Do not start, stop or restart servers. If the app or the login does not work, stop and report "not checked" with the reason.
2. **Credentials:** read them inside the script, from the named file, when it runs. Never print, log, screenshot, or repeat them in the report. Log in through the app's own login form.
3. **Look before you change.** Prefer checks that only read. Change data only when a claim cannot be checked any other way, and then only:
   - data the checker creates itself in this run,
   - data the user named as free to change, or
   - a reversible change (e.g. turn off, then turn back on) that it reverses in the same run.
4. **Never** delete, uninstall, cancel or send anything on other data. If a claim needs that, report it as "not checked: needs a destructive action".
5. **Record state.** Note the starting state of anything it changes, put it back before the script ends, and list each change in the report, including anything it could not put back.
6. **Do not force states.** If no data is in the state a claim needs (e.g. no connector is "Couldn't connect"), report "not checked: no data in that state". Do not call the API directly to create that state.

## Script mechanics

- Write each script so it runs from the repo root, where `playwright` resolves: e.g. `./.ui-check.tmp.mjs`. Delete it in the same command that runs it, even if the run fails. Save screenshots to the scratchpad.
- Pass the credentials file as an argument, and parse only the needed keys.
- Select elements by role and visible text, as a user would. Scope to the dialog when a label repeats (e.g. an icon "Close" and a text "Close").
- Wait about 700 ms after a dialog opens or closes before a screenshot. Mid-animation screenshots show two layers at once.
- Log the requests that matter (method, path with ids masked, status) as evidence.

## Report format

A table with one row per claim:

| # | Claim | Result | Evidence |
|---|-------|--------|----------|
| 1 | connect_failed shows "Retry" | pass / fail / not checked | text seen, request + status, screenshot path, or the reason it was not checked |

Then:
- **Data changed:** what changed, and whether it was put back.
- **Other things seen:** console errors, broken layouts, wrong text.
- **Open questions:** claims that could mean more than one thing, with the options.

Under 400 words.
