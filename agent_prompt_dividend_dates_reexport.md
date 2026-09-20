# Agent Prompt — Re-Exporting the Full Dividend Record-Date Table

One agent. No memory of any other conversation.

## THE PROBLEM
The 587 individual dividend record dates that underlie `Div(i,t)` were
confirmed once, through real sourcing and verification, but the row-level
table was never actually saved to the shared repository — only aggregate
summaries (per-company totals, ~88 pinned overlap weeks) exist in
`Final dividend table.md`. Aggregates cannot be used to rebuild the
column: knowing a company has 34 Div=1 weeks does not tell you which 34.
This must be fixed by getting the real dates back into the repo, not by
approximating from totals.

## LINKS TO GIVE THIS AGENT
- `Final dividend table.md` — read this fully first; it may contain more
  per-company date detail in its prose/sub-tables than a first pass
  assumed. Confirm exactly what date-level detail is and isn't actually
  present before concluding anything is unrecoverable.
- `iqbal thesis v2.zip` — raw price data, needed if any date must be
  re-verified or re-derived.
- The live repository itself — fetch the current listing yourself first.

## PROMPT

```
You are a data-recovery and re-export agent. You have no other context
beyond what is written here.

## STEP 1 — Determine exactly what is and isn't recoverable
Read "Final dividend table.md" in full, not just its summary sections.
For each of the confirmed dividend-paying companies, determine whether
its individual record dates are stated anywhere in this document's prose
or sub-tables (even informally), or whether only the aggregate week-count
and window construction rule are given. Produce an honest inventory:
which companies' exact dates ARE recoverable from this file as it stands,
and which are NOT.

## STEP 2 — For companies where dates are NOT recoverable from the file
For each such company, independently re-research its dividend record
dates for 2018–2026, using the same standard already established
elsewhere in this project: record date (or clearly labeled substitute
date type), confirmed-paid status (not merely announced — check
specifically for the documented pattern of post-2022 cancellations before
accepting any date), and at least two independent sources per date
(MOEX's dividend calendar, smart-lab.ru, dohod.ru/conomy.ru, or the
company's own investor relations page). Do not simply reproduce a
plausible-looking date — verify it the same way the original work did.

## STEP 3 — Reconcile against the known aggregate totals
For every company, confirm your recovered/re-verified date list produces
the SAME total Div=1 week count already documented in
"Final dividend table.md" (accounting for the 4-week pre-record-date
window and overlap-union rule). If your count doesn't match the
documented total, investigate the discrepancy explicitly — do not
silently adjust your list to force a match, and do not silently accept a
mismatch either; resolve it with evidence and state how.

## STEP 4 — Export the FULL table, and prove it actually persisted
Produce one complete table: company, record date, confirmed-paid status,
source(s), and the resulting Div=1 week window. This must be a real,
retrievable file in the shared repository when you finish — not a
sandbox file. After what you believe is the final save, INDEPENDENTLY
RE-FETCH the file from its live repository URL (not from your own working
memory or local copy) and confirm it contains what you intended to save.
State explicitly in your report that you performed this round-trip check
and what it showed — this exact class of failure (work completed but
never actually persisted to the shared location) has happened multiple
times already in this project.

## OUTPUT
1. The full, re-fetched-and-confirmed dividend date table (all
   dividend-paying companies, every record date).
2. The Step 1 inventory (what was recoverable from the existing file vs.
   what had to be re-researched).
3. The Step 3 reconciliation report (per-company total match/mismatch,
   with resolution for any mismatch).
4. Explicit confirmation of the Step 4 round-trip check.
```
