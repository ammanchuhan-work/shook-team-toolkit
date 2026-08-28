# SHOOK Research mailer brand guide

Design-system rules only — extracted from the real, in-production house templates.
Apply these whenever generating a new mailer, regardless of shape (letter or event
invite).

## Colors

| Role | Hex |
|---|---|
| Navy — primary (headlines, table headers, footer background, top rule) | `#0D1F3C` |
| Brand blue — dividers, buttons, links, accents | `#2E7BBF` |
| Body copy grey | `#3a3a3a` |
| Secondary/meta text grey | `#5c5b5b` |
| Row rules / hairlines | `#eeeeee`, `#e8e8e8` |
| Light-blue tint (callout panels, info backgrounds) | `#eaf2fb`, `#f4f8fb`, `#f7f9fc` |
| Footer divider | `rgba(168,192,224,0.15)` |
| Footer social icon circles | `#1a3a6b` |

Only navy and brand blue are used for structural/brand elements. Don't introduce new
accent colors unless the user explicitly asks for a variant.

## Type

- **Headings and buttons:** Montserrat, always as an inline `font-family` attribute —
  never a CSS class (email clients strip `<style>` classes unreliably).
  `<mj-font name="Montserrat" href="https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400;500;600;700;800&display=swap" />`
- **Body copy:** Helvetica/Arial, 14–15px, line-height 1.45–1.7, color `#3a3a3a`.
- **Hero headline:** Montserrat 22px / weight 700 / navy `#0D1F3C`.
- **Section eyebrow/table headers:** uppercase, 11–13px, weight 800, letter-spacing 2–4px.

## Buttons

Montserrat weight 700–800, border-radius 2–4px. Inner padding `14px 32px` for a letter
CTA, `15px 36px` for an event CTA. Background `#2E7BBF`, text white, unless the letter
carries no ask — **a letter with no ask carries no button at all**, don't render an
empty CTA section.

## Structural rules (email-client compatibility — do not deviate)

- **Bulleted lists are hand-built tables, not `<ul>`** — Outlook's list-indent support is
  unreliable. One `<tr>` per bullet, bullet character in its own narrow `<td>`, last row
  uses `padding:0` (no trailing gap), every other row `0 0 6px 0`.
- **Responsive breakpoint:** `@media only screen and (max-width:479px)`.
- **Body width:** single column, 600–640px.
- Keep `<sup>` tags raw (e.g. `13<sup>th</sup>`) — a `<span>` workaround causes a visual
  offset in some clients.

## No eyebrow labels

One headline per email — no uppercase kicker line above it, even on event invites.

## Footer (identical on every mailer)

Navy `#0D1F3C` background, all text white 10px, divider `rgba(168,192,224,0.15)` above
and below the SHOOK logo row. Canonical boilerplate paragraph:

> SHOOK Research represents the industry standard for advisor quality and integrity and is the independent research organization responsible for conducting the annual rankings of Top Wealth Advisors, Teams, Women, Next Gen, RIAs and Financial Security Professionals, published in partnership with Forbes.

Followed by an unsubscribe line referencing `[[EMAIL_TO]]` and `[[UNSUB_LINK_EN]]`.
Three circular social icons (Facebook `f`, X, LinkedIn `in`) on `#1a3a6b` circles.

## Casing

- Company name: **"SHOOK" / "SHOOK Research"**, all caps, for letters and event
  mailers (this is the default this skill uses).
- People's names: title case (e.g. "Molly Bennard").
- The Forbes lockup is always `Forbes | SHOOK` with spaces around the pipe, wrapped in
  `white-space:nowrap` so it never splits across lines — don't use `&nbsp;` around the
  pipe, it breaks in some clients.

## Mailjet mechanics (leave these exactly as literal tokens — do not fill them in)

| Token | What it does |
|---|---|
| `[[PERMALINK]]` | "Having trouble viewing this email?" preheader link |
| `[[EMAIL_TO]]` | Recipient address in the unsubscribe line |
| `[[UNSUB_LINK_EN]]` | Unsubscribe URL |
| `[[data:firstname:"fallback"]]` | First-name personalization with a fallback string — use this exact syntax, not `[[FIRST_NAME]]`, which is not a valid Mailjet token |

Mailjet auto-wraps every URL in a tracked redirect — paste plain destination URLs into
the MJML, don't pre-wrap them. Subject lines live in Mailjet's send settings, not in the
file; `<mj-title>` is fallback/preview text only. `<mj-preview>` is the real inbox
preview snippet — one sentence, ~90 characters.

## Scope reminder

This skill generates `.mjml` (and, on request, compiled `.html`) files only. It does
not call the Mailjet API and does not send anything.
