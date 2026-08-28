# SHOOK Team Toolkit

A Claude Code plugin bundling skills that get reused across SHOOK Research
departments (HR, Legal, Finance, Marketing, Events, Research). Built incrementally —
this is v0.1 with two skills.

## Skills

### `dd-expense-documentation` (Research)

Renames a batch of due-diligence trip receipt files by date/category and builds the
standard travel expense workbook (with per-day columns, category rollups, and itemized
backup sections) from them. Works in any folder — ask it to process a folder of
receipts and it will read them first to infer the trip's dates, location, and traveler,
then confirm with you (sponsor and wholesaler name usually has to be asked directly).

### `mjml-mailer-generator` (Marketing, Events)

Generates an on-brand SHOOK Research mailer as MJML (optionally compiled to HTML),
either a generic letter or an event invite. Generation only — it never calls the
Mailjet API or sends anything.

## Adding a new department skill

Each skill is self-contained under `skills/<skill-name>/`:

```
skills/<skill-name>/
├── SKILL.md              # when to use it, step-by-step instructions
├── reference/*.md         # detailed rules the skill points to (formats, brand, etc.)
└── assets/*                # starter/template files the skill copies and fills in
```

To add a department-specific variant of an existing skill (e.g. an Events version of
expense documentation with a different sheet layout), create a new sibling skill
folder (e.g. `events-expense-documentation`) rather than branching inside the
existing one — keep each skill's rules and template assets self-contained so they
don't drift into each other.

## Packaging

This plugin is distributed as a single installable `.plugin` file
(`shook-team-toolkit.plugin`, a zip of this directory) — see the repo's build step
for how it's produced.
