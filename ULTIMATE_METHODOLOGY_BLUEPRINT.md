# ULTIMATE METHODOLOGY BLUEPRINT — 75-Company MOEX ASVI Panel

This is the single authoritative specification. Where anything here
conflicts with a prior report, this document governs. Where something is
genuinely unknown, it says so explicitly — no agent following this
blueprint should ever need to substitute, approximate, or guess.
**If a step below cannot be executed exactly as written, STOP and report
the specific blocker. Do not substitute, reconstruct, or approximate and
then continue as if the step succeeded.**

---

## 1. Study Window
2018-08-27 through 2026-09-06 (raw price bar range). Wordstat grid:
Mondays 2018-08-27 → 2026-08-31, 419 weeks. Do not truncate.

## 2. Company Universe
75 companies (list in the Unified Methodology Manual §2.2). Every
downstream table must report its exact N and reconcile it against 75 —
if a method uses fewer than 75, the specific missing companies and the
specific reason each is missing (not a category, the actual ticker and
cause: <300 observations, ROLO's RV exclusion, a data gap, etc.) must be
listed by name every time. **A method reporting "73 fits" with no named
list of which 2 companies are missing and why is incomplete, not final.**

## 3. Core Variable Construction

**SVI/ASVI:** For 74 non-Yandex companies: SUM RAW ticker + Cyrillic
counts week-by-week, then `ASVI(t) = ln(SVI(t)) − median(ln(SVI(t−1..t−8)))`.
SVI=0 → missing. For Yandex: sum ONLY YNDX ticker + Cyrillic — YDEX is
never used, by final decision (documented limitation: ~2 years of real
YDEX search volume is excluded).

**RV(i,t)** = (High(i,t) − Low(i,t)) / Close(i,t).
**R(i,t)** = ln(Price(i,t) / Price(i,t−1)).
**V(i,t)** = raw weekly volume; use ln(V(i,t)) in regressions.

**Date join:** Wordstat Monday date = price-file Sunday label + 1 day
(price Sunday label = the day BEFORE the Mon–Fri trading week it reports).

**ROLO:** excluded from every RV-dependent test (M1, M4, M5) due to
confirmed 1-kopeck tick-size mechanical RV inflation. It MAY still be
used in R- or volume-only tests (M2) unless a separate reason excludes it
there — state explicitly which tests include or exclude ROLO and why,
every time.

**Data-quality registry (mandatory handling, not optional):** exclude
week 2024-06-16 from cross-sectional detection; exclude week 2024-08-25
from volume metrics; drop the twelve 2022-02-27 flat placeholders (treat
as missing, never forward-fill); keep all documented company-specific
gaps (LSNG, MSTT, FEES, YNDX relisting, AVAN early gaps) as missing.

## 4. The Four Event/Structural Controls — Construction Order and Overlap Rules

**Build in this exact order, because later ones depend on earlier ones
for deduplication:**

1. **Crisis(t)** — market-wide, time-varying only (same value for every
   company in a given week). Confirmed via: eligible companies with ≥30
   trailing observations; a company "spikes" if RV_ratio≥2 AND
   Vol_ratio≥2 vs. its own trailing-52-week median; a week is a "stress
   week" if ≥25% of eligible companies spike; a window is confirmed if
   contiguous stress weeks (1-week bridge allowed) reach peak spike
   fraction ≥40%. Four confirmed windows exist (W1 COVID, W2
   invasion/suspension, W3 ruble/rate Sept 2023, W4 bear capitulation
   2026) — exact dates in the Manual §3.1. **Do not reconstruct Crisis
   from scratch if these four windows are retrievable from the source
   reports — extract them.**

2. **Sanction(i,t)** — company-specific, PERMANENT step function (0
   before, 1 from the confirmed first-designation date onward, forever).
   23 primary anchor events, full list with dates and Treasury/OFSI/EU
   source citations in the Manual §3.2. Verification: 2× trailing-median
   RV/Vol test in the 4 weeks following the anchor date — only 7 of 23
   confirmed a detectable reaction; this low rate is a documented,
   legitimate finding, not a failure to fix.

3. **News(i,t)** — company-specific, TRANSIENT (data-driven duration,
   capped at 4 weeks, using a 1.5× trailing-median threshold to extend
   and the first sub-threshold week to stop). Category bar: only
   discrete, exceptional events (M&A, surprise earnings, sudden
   leadership change, major legal/regulatory action, major operational
   disruption, debt distress, one-off major contract). **Mandatory
   deduplication BEFORE testing:** discard any candidate within ±2 weeks
   of a confirmed Crisis window OR that same company's confirmed Sanction
   date. This guarantees News∩Crisis=0 and News∩Sanction=0 by
   construction — these should be verified as exactly 0, not
   approximately 0. 31 confirmed events, 23 companies, 89 total weeks is
   the documented target — if a reconstruction produces a different
   count (e.g. 84), the specific missing events must be named and the
   cause diagnosed, not silently accepted as close enough.

4. **Div(i,t)** — company-specific, built INDEPENDENTLY of Crisis/
   Sanction/News, with NO deduplication against them. Confirmed-paid
   dividend record dates only (board approval + record date passed + ≥2
   independent sources + no reversal — reject anything only "announced").
   For each confirmed record date, Div=1 for the 4 calendar weeks
   immediately preceding the record week (record week itself NOT
   included); overlapping windows from multiple dividends in one year are
   unioned, not double-counted. 587 confirmed record dates, 63
   dividend-paying companies, 2,110 Div=1 weeks (6.71% of panel) is the
   documented target. **The full 587-row date-level table is required to
   build this correctly — a per-company aggregate week-count cannot
   substitute for it (an aggregate carries no positional information).**

**After all four are built, compute overlap as MEASUREMENT, not
construction:**
- Crisis∩Sanction, Crisis∩News, Sanction∩News: must equal exactly 0.
  If any is nonzero, the construction in step 3 has a bug — this is a
  build error to fix, not a finding to report.
- Div∩Crisis, Div∩Sanction, Div∩News: measure and report the actual
  overlap counts. Flag any company where overlap reaches ≥4 weeks OR
  ≥25% of that company's own Div=1 weeks as a "robustness-check
  candidate." **The documented target from prior work is 11 such
  companies — if a reconstruction produces a substantially different
  number (e.g. 30), the exact companies included/excluded under each
  definition must be listed side by side so the discrepancy is visible
  and diagnosable, not just noted as a gap.**

5. **SOE(i)** — time-invariant, per company, NOT date-indexed. No
   temporal overlap concept applies. The real substantive risk with SOE
   is confounding with SECTOR (13/14 Utilities and 8/13 Energy companies
   are SOE=1) — any H6 result must be reported alongside a check of
   whether it survives when sector fixed effects are included in the
   same specification, not treated as a temporal overlap issue.

## 5. Hypotheses and Exact Equations

**H1 (M1, per-company OLS):**
`RV(i,t) = α + β1·ASVI(i,t−1) + β2·RV(i,t−1) + β3·ln[V(i,t−1)] + β4·Crisis(t−1) + β5·Sanction(i,t−1) + β6·Div(i,t−1) + ε`
Newey-West HAC, bandwidth 4.

**H2 (M2, per-company OLS):**
`R(i,t) = α + β1·ASVI(i,t−1) + β2·R(i,t−1) + β3·RV(i,t−1) + β4·Crisis(t−1) + β5·Sanction(i,t−1) + β6·Div(i,t−1) + ε`

**H1/H2 robustness (M3, Granger):** restricted vs. unrestricted (+L lags
ASVI, L=1,2,4) F-test per company per hypothesis. Benjamini-Hochberg
applied SEPARATELY within the H1 block and the H2 block — never pooled.
Bonferroni-Holm reported alongside as a conservative check.

**H1 aggregate / H4 / H5 / H6 (M4, pooled panel FE):**
`RṼ(i,t) = β1·ÃSVI(i,t−1) + β2·RṼ(i,t−1) + β3·ln[Ṽ(i,t−1)] + β4·Dĩv(i,t−1) + ε̃`
(entity + time FE; β3 is the previously-missing lagged-volume term whose
addition corrected a documented pooled-vs-per-company sign contradiction
— this must be stated in every report using M4, not just coded silently.)
- H4: add ASVI×Sector interactions; Wald test on joint significance.
- H5: add ASVI×Div_lag1 interaction. Report both the full-sample result
  AND the result excluding the confirmed robustness-check-candidate
  companies from step 4 above.
- H6: add ASVI×SOE interaction. Report both the base result AND the
  result with sector fixed effects included alongside it (the confound
  check from Section 4.5 above).

**H3 (M5, Chow):** asymmetric Chow at 2022-02-24, reduced form
(ASVI_lag1, RV_lag1 only + crisis_chow in post-period), q=3, k_pool=4.
Apply Giles-Lieberman heteroscedasticity-robust bounds using the actual
published tabulation (source PDF `canterbury-nz-034.pdf`, already in the
books archive) — **do not use an approximate substitute bound without
first attempting to extract the real published values from that PDF.**
If genuinely unable to extract them, state this as an explicit,
named blocker, not a silent substitution.

**M6 (permutation + power, H1/H2 robustness):** 1,000 shuffles per
company, non-central-t power and MDE at 80% power for every company. Any
null result must be reported with its power — "inconclusive due to
insufficient power" whenever power < 0.80, never "no effect."

## 6. Named Sensitivity Checks (run these exact six, nothing substituted)
1. Drop the 5 revision-affected News events; confirm explained-spike
   share moves from 62.2% toward 61.7%.
2. Widen News windows to t...t+5 for persistence-flagged events.
3. Re-run with strict Crisis/Sanction definitions; confirm residual
   unexplained share moves toward 49.2%.
4. Re-run H5 excluding the confirmed robustness-check-candidate
   companies (target: 11 companies, per Section 4 above — use the
   correctly-reconstructed list, not a substitute).
5. Re-run H6 with federal-only SOE classification.
6. Flip-test VSMO's SOE classification (0 vs 1).

## 7. What To Do When Something Is Missing
Never reconstruct silently and present the result as equivalent to the
original. Instead: (a) state plainly what is missing, (b) state what was
tried to recover it, (c) if a substitute was used, state precisely how it
differs from the original and what the quantitative consequence is, (d)
flag the affected result as PROVISIONAL until the real data is recovered.
This applies with no exceptions to: the Giles-Lieberman tabulation, the
full 587-row dividend date table, the exclusion registry, and any other
gap not listed here that an agent discovers.
