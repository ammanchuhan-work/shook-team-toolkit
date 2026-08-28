---
name: mjml-mailer-generator
description: Generate an on-brand SHOOK Research mailer as MJML (and optionally compiled HTML), for a generic letter/announcement or an event invite. Use when the user asks to draft, build, or generate a mailer, email invite, or announcement email. Does not call the Mailjet API and does not send anything — output is a file only.
department: Marketing, Events
---

# MJML Mailer Generator

Generates a new `.mjml` mailer file (and, on request, its compiled `.html`) following
the SHOOK Research house brand system documented in `reference/brand-guide.md`. Read
that file before writing any mailer — colors, type, button styles, footer, and the
Outlook-compatibility rules (hand-built bullet tables, not `<ul>`; the 479px breakpoint)
all apply regardless of which shape you're building.

**Scope: generation only.** Never call the Mailjet API, never send a test or live email.
The deliverable is a file the user pastes into Mailjet themselves.

## Step 1 — Determine the shape

Ask the user which of these two shapes fits (or infer from their request):

- **Letter** — a generic, non-event announcement or communication. Start from
  `assets/letter-template.mjml`.
- **Event invite** — an invite/confirmation for a specific event with a venue and
  agenda. Start from `assets/event-invite-template.mjml`.

## Step 2 — Gather the specifics

Ask for whatever isn't already given:

- **Letter:** sender name/title, headline, body copy (opening + optional second
  paragraph), optional bullet list, optional single CTA (only if there's an actual ask),
  closing line.
- **Event invite:** event name, date/time, venue (name + address), agenda items
  (time + session/speaker), audience description, registration URL, sender/reply-to
  address, any co-host/sponsor to credit.

Don't invent facts (dates, venues, speaker names) — ask rather than guess.

## Step 3 — Fill in the template

Copy the appropriate template asset to a new filename, then:

- Fill every `{{DOUBLE_BRACE}}` placeholder.
- **Delete every optional block that isn't used.** An empty CTA section is worse than no
  CTA section — a letter with no ask carries no button. An event invite with no
  agenda item for a given time slot should have that row removed, not left blank.
- **Leave `[[PERMALINK]]`, `[[EMAIL_TO]]`, `[[UNSUB_LINK_EN]]` and any
  `[[data:firstname:"..."]]` token exactly as-is** — these are Mailjet tokens, not
  placeholders for you to fill in.
- Keep the brand rules from `reference/brand-guide.md`: don't introduce new colors,
  don't use `<ul>` for lists, keep Montserrat inline (not a CSS class).

## Step 4 — Compile to HTML (only if asked)

If the user wants compiled HTML rather than just the `.mjml` source:

- If an `mjml` CLI is available, run `mjml <file>.mjml -o <file>.html`.
- Otherwise, tell the user to paste the `.mjml` into Mailjet's built-in editor (which
  compiles on save) or https://mjml.io/try-it-live, since there may be no local MJML
  compiler installed.
- A clean file compiles with no warnings — if compilation produces a warning, something
  in the MJML is malformed; fix it before treating the file as done.

## Step 5 — Confirm

Tell the user what was generated, what placeholders (if any) still need real copy, and
remind them this is Mailjet-ready source only — nothing has been sent or scheduled.
