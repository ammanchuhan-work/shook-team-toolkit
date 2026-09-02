---
name: dd-expense-documentation
description: Rename a batch of due-diligence trip receipt files (images/PDFs) by date and category, and build the standard SHOOK travel expense workbook from them. Use when the user has a folder of DD-trip business receipts to process into an expense report — e.g. "process my receipts from the Stamford trip" or "build my expense report for this trip."
department: Research
---

# Expense Documentation

Turns a folder of loosely-named trip receipts (photos and PDFs, however they came off a
phone or inbox export) into: renamed receipt files, organized into a per-trip subfolder,
plus a filled-in `.xlsx` expense workbook matching the house format.

This skill works in **any** folder the user points it at — it is not tied to one
person's machine or one specific "Expense Folder." Multiple trips' receipts can be
sitting loose together in the same folder, distinguished only by date, so always
confirm trip scope before touching files (see Step 1).

## Step 0 — Locate the receipts

Default to the current working directory — the folder Claude Code is already open in —
without asking. Look for image/PDF files that look like receipts there. Only ask the
user for a different path if the working directory has no candidate receipt files in
it, or if the user's request names a different folder explicitly.

## Step 1 — Establish trip scope

Before renaming anything, skim the receipts themselves first to infer as much of the
scope as you can, then confirm with the user rather than asking blind:

1. **Read the flight receipts first, if any.** Their departure/arrival cities and dates
   are the most reliable source for the trip's overall departure and return dates, and
   for the home city vs. destination city.
2. **Infer the trip location** from receipts dated on the days the traveler wasn't in
   transit — those cluster around one destination city/state. Receipts dated on the
   travel days themselves (a home-city breakfast before the outbound flight, a home-city
   rideshare after the inbound flight) reflect the home city, not the destination — don't
   use those to infer location.
3. **Look for the traveler's name** on the receipts themselves (a hotel folio's guest
   name, a signed restaurant check) to confirm it rather than asking outright if it's
   already evident.
4. **Sponsor and wholesaler name is almost never on a receipt** — this is the one field
   you'll typically have to ask for directly.

Then present what you inferred back to the user in one message for confirmation —
traveler name, trip location, departure/return dates, and sponsor and wholesaler name —
and ask only about whatever genuinely wasn't inferable or looks ambiguous/conflicting
across receipts. Don't proceed to renaming until the user has confirmed or corrected
this scope, since it determines which loose receipts in the folder belong to this run
if others are mixed in.

## Step 2 — Rename each receipt

**Read receipts one at a time, strictly sequentially — do not batch multiple reads in
parallel.** This is a financial reimbursement document: a misclassified receipt (wrong
date, category, or amount attributed to the wrong file) is a much costlier error than
the time saved by parallelizing, since images and PDFs can come back in an order that
doesn't match the order requested, risking exactly that kind of mix-up. Read one file,
extract and note its date/category/amount immediately, then move to the next.

For every candidate receipt file (image or PDF) in scope:

1. **Read the file** to find the date printed on the receipt itself and what it's for.
   Do not use the file's own timestamp or any date embedded in an auto-generated
   filename (e.g. `20260713_019f5ca0-....jpeg`) — that's an upload/export timestamp,
   not the transaction date. If a total is handwritten or circled (e.g. a tip added
   after the printed subtotal), use that final handwritten amount — not a printed
   suggested-tip schedule — as the receipt's amount.
2. Rename to: `MM-DD-YY {Category}`, keeping the original extension.
3. Category is one of: `Breakfast`, `Lunch`, `Dinner`, `Snack`, `Drinks`, `Hotel`,
   `Flight`, `Rideshare`, `Parking`, `Toll`, `Rental Car`, `Gas`, or another short
   Title Case noun if genuinely nothing else fits (e.g. `Baggage`). Don't invent a new
   category when an existing one covers it.
4. If two receipts land on the same date + category, disambiguate with a trailing
   number: `Snack`, `Snack 2` — never location text.
5. If a file is a byte-identical duplicate of another (same size and content), flag it
   and confirm with the user before deleting rather than renaming both.

## Step 3 — Create the trip subfolder and move files in

Create a new subfolder in the same parent directory, named:

```
{Traveler First Name} - {City/Area}, {State} {Month} {StartDay} to {EndDay}
```

e.g. `Amman - Stamford, CT July 13 to 15`. Inside it, create a `Receipts` subfolder and
**move** (don't copy) each renamed receipt that belongs to this trip into `Receipts` —
the parent folder should end up clean of this trip's files, ready for the next trip's
fresh drop. The final layout is:

```
{Traveler} - {City}, {State} {dates}/
├── Receipts/
│   ├── MM-DD-YY Category.ext
│   └── ...
└── {Traveler First Name} - Expenses.xlsx
```

## Step 4 — Build the expense workbook

Copy `assets/expense-sheet-base.xlsx` into the trip subfolder (next to `Receipts/`, not
inside it) as `{Traveler First
Name} - Expenses.xlsx`, then fill it in on a single sheet named `Sheet1`:

**Header block:**

| Cell | Content |
|---|---|
| `A1:E2` (merged) | `Travel Expenses ` (already styled in the base template) |
| `B3:E3` | Traveler's full name |
| `B4:E4` | Trip location — city/area + state, **never the hotel name** |
| `B5:E5` | Departure date, format `mmmm d, yyyy` |
| `B6:E6` | Return date, format `mmmm d, yyyy` |
| `B7:E7` | Sponsor and Wholesaler name |
| `C8` onward | One date per **day that had an expense**, format `m/d/yy`. **Not a contiguous range** — skip any day with zero receipts entirely, don't leave a blank styled column for it, and don't renumber sequentially. A receipt's amount goes in the column whose header date equals the receipt's date. |

**Category rows (fixed order, rows 9–16):** `Airfare`, `Ground Transportation`,
`Parking and Tolls`, `Rental Car`, `Lodging`, `Meals and Tips`, `Gas`, `Other (...)`
(parenthetical is trip-specific, e.g. `Other (checked bag)` — leave the row blank with
a generic label if there's no such expense). Amount cells: `"$"#,##0.00` format, and
**leave a cell blank (not `0`) when there's no expense in that category for that day.**

**Receipt category → row mapping:**
- `Flight` → **Airfare**, under the date of that flight leg (not the booking date)
- `Rideshare`/`Toll` → **Ground Transportation**; standalone `Parking` → **Parking and Tolls**
- `Rental Car` → **Rental Car**; `Gas` → **Gas**
- `Hotel` → **Lodging** — read the folio itself to find the date the full charge was
  actually **settled/accredited** (look for a `Payments` line item and its date), and
  put the folio's full total under that column. This is usually the checkout date, but
  confirm it from the folio's own payment line rather than assuming. Don't split the
  total across the nights it covers, even for a multi-night stay.
- `Breakfast`/`Lunch`/`Dinner`/`Snack`/`Drinks` → **Meals and Tips**, summed per date
- Anything else (baggage fees, etc.) → **Other (...)**, with the parenthetical describing it

**Subtotal / total:** Row 17 `Sub Total:` — each used day column gets
`=SUM(<col>9:<col>16)`. Row 19 `Total Expenses:` — `=SUM(C17:<lastcol>17)` where
`<lastcol>` is the **last day column actually in use**, not a fixed-width range.

**Itemized backup sections (below row 19):** whenever a single day has one or more
receipt in the same category, add a labeled section (e.g. `Transportation`, `Meals`)
listing each receipt's amount under its date column, with a `SUM` formula per column
that must equal the corresponding rolled-up cell above (e.g. a Transportation section's
column-C sum must equal `C10`). A category must have a backup section.

If a receipt sits on a transitional/gap day (e.g. a ride to or from an off-itinerary
stretch) and it's unclear which column it belongs under, ask the user — that's a
judgment call, not a mechanical one. Double-check every amount landed in the column
matching its own date — this is the most common way these sheets go wrong, especially
around a gap day that shifts which physical column corresponds to which date.

Deliverable stays an editable `.xlsx` — never a PDF, never a Claude Artifact.

## Step 5 — Confirm

Summarize what was renamed, what moved where, and the totals per category/day. Flag
anything you had to guess at (illegible receipt, ambiguous category, no sponsor and
wholesaler name given) so the user can correct it before submitting. Also remind the
user it is their responsibility to review and ensure accuracy before submitting.
