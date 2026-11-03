# Handoff: To-dos and email to-dos

Notes for picking up work on Aggie Hub's **To-dos** section and its email import in a new session.

## Where things stand

- **Repo:** `Pickles9929/aggie-hub` (GitHub also shows it as `TAMU-hub`, its new name). The whole app is one file, `index.html`: HTML, CSS and JS, no build step.
- **Open pull request:** #2 "Turn dorm errands into to-dos with repeats and email import", from branch `claude/blissful-clarke-m656ud` into `main`. It's ready to merge. If it has been merged by the time you read this, start new work from the latest `main`.
- **Live preview:** the page is published as a private Claude artifact at https://claude.ai/artifact/M2sN7c2wBgn45bqGhcwFLh. Email to-dos only work there.
  - It declares two capabilities: `mcp` (server `Gmail`, tool `search_threads` only) and `downloads`.
  - Republishing from a new session means passing that URL as `url` and reading the artifact first.
- **The user** works on an iPad and doesn't want to comment on GitHub. Take requests in chat, then open a pull request for them to merge.

## How To-dos work (`index.html`)

- **State:** everything lives in `localStorage` under the key `aggieHub.v1`. `normalize()` validates both saved state and loaded backups.
- **A to-do looks like** `{id, text, done, due: "YYYY-MM-DD" | "", repeat: "none"|"daily"|"weekly"|"biweekly"|"monthly", day?, email?: {url, from, rule}}`.
  - `day` is the day of the month, used for monthly repeats.
  - `email` is set only on to-dos created from an email.
- **Repeating to-dos:**
  - Checking one off calls `toggleTodo()`, which moves `due` forward with `nextDue()` / `stepDate()` instead of marking it done.
  - Monthly repeats use the last day of the month when the month is shorter.
  - A repeating to-do whose `due` is after today shows under **Coming up** (`isUpcoming()`).
- **One-off to-dos** go to **Finished** when checked.
- **Migration:** older saves stored to-dos as `errands`. The seven starter dorm errands (ids `e1`–`e7` with their original text) are dropped, and any other errands become to-dos.
- **Main functions:** `renderTodos()` and `todoRow()`. The add form is `#todo-form`.

## How email to-dos work

- **Setup:** in the page, open Settings (**Edit courses & class times**), then **Email to-dos**, then **Turn on email to-dos**. This sets `state.email.on` and sets `since` to 3 days ago.
- **Rules:** `state.email.rules` is a list of `{id, name, q, on}`. Each `q` is a Gmail search.
- **The search:** `checkEmail()` calls `callTool("Gmail", "search_threads", {query: "(<q>) after:YYYY/MM/DD", pageSize: 25, view: "THREAD_VIEW_SUMMARY"})`.
  - Each new message becomes a to-do due today. "Fwd:" and "Re:" are stripped from the subject.
  - Duplicates are skipped using the newest message id, stored in `state.email.seen` (last 500 kept).
- **When it runs:** on page load, every 15 minutes while the page is visible, and when the user taps **Check now**.
- **Errors:** each connector error code gets its own message in `emailErrorText()`.
- **Outside Claude** (a plain file or GitHub Pages), `window.claude` is missing, so the email section explains that it only works from the Claude link.

### Facts checked against the real Gmail connector

- **The connected inbox is the user's TAMU Google account.** TAMU mail arrives there directly.
- **`after:` must be a `YYYY/MM/DD` date.** A Unix timestamp silently returns nothing.
- **`from:me` works.**
- **`viewUrl` was missing in the results seen so far.** So the "Open email" link falls back to `https://mail.google.com/mail/#all/<threadId>`. That opens whichever Google account the browser has as account 0, which might not be the TAMU one.

### Starter rules

1. **Forwarded to myself:** `from:me to:me todo`
2. **Package ready for pickup:** `subject:(package OR parcel) (ready OR pickup OR "pick up")`. This is a guess, because no package emails were in the TAMU inbox.

## What the user was told to do (email instructions)

- **Package emails go to the user's personal Gmail,** not TAMU. Claude can't see that account.
- **Optional automatic setup** in the personal Gmail, which needs the desktop website (in Safari, tap **aA**, then **Request Desktop Website**):
  1. Go to Settings, then **Forwarding and POP/IMAP**, then **Add a forwarding address**. Add their TAMU address and confirm it from the TAMU inbox. Leave **Disable forwarding** selected.
  2. Open a package email, then tap **⋮**, then **Filter messages like these**, then **Create filter**. Check **Forward it to** and choose the TAMU address.
- **Forwarding by hand** (works without the automatic setup): forward any email to the TAMU address with **todo** in the message.
  - For this to work from the personal account, the user must change the **Forwarded to myself** rule in the page to `from:(me OR <personal address>) to:me todo`.
  - The personal address was deliberately **not** put in the code, in case the repo is ever made public. Ask the user for it if you need it.

## Open items / next steps

- **Get the real sender or subject of a package email** from the user, then make the package rule match it exactly. A filtered auto-forward keeps the original sender, so a `from:` rule works.
- **Consider a setting for extra "my addresses"** so the forwarded-to-myself rule covers the personal account without hand-editing.
- **The "Open email" link may open the wrong Google account** (see above). Consider a better link if that's a problem.
- **Email dedupe is per device.** It lives in `localStorage`, so opening the hub on a second device can re-add recent email to-dos.

## Testing approach

- **Browser:** Playwright and Chromium are preinstalled (`require('playwright')`). Load `file:///home/user/aggie-hub/index.html`.
- **Mocking Claude:** use `page.addInitScript` to stub `window.claude = {use: name => Promise.resolve(name === "mcp" ? {callTool: async (...) => ({payload: {threads: [...]}})} : null)}`. This lets you test email import without the real connector.
- **Dates:** use `page.clock.install(...)` to test repeats and what happens at midnight.
- **Weather:** the weather API is blocked in the cloud sandbox, so only the fallback message shows there.

## Ground rules

- **Git:** commit to the designated `claude/...` branch and open a pull request. Don't force-push.
- **Keep data compatible:** keep the storage key and backup format working, and extend `normalize()` for any new fields.
- **Be clear about where things work:** tell the user plainly what only works from the Claude link and what works everywhere.
