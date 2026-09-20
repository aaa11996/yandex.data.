# UNIFIED METHODOLOGY MANUAL — 75-Company MOEX ASVI Panel (2018-2026)
## Part 1 Technical: from the raw archive to the regression output · Part 2 Plain-language explanation

**File 2 of 3.** File 1 = `iqbal thesis v2.zip` (raw archive, unmodified: 226 CSVs in 75 company folders). File 3 =
`UNIFIED_DATA_COMPILATION.md` (every Crisis/Sanction/News/Div/SOE value, consolidated, with the 50-row audit log).
**Rebuilt:** 2026-09-20. **Live repo re-verified:** the tree of `aaa11996/yandex.data.` (note: the trailing dot is part of the
repo name; the undotted URL 404s) lists 12 blobs on 2026-09-20, all fetched and byte-size-verified:

| Repo file | Bytes |
| --- | --- |
| `Final dividend table.md` | 21,392 |
| `News_Div_Overlap_Addendum.md` | 16,773 |
| `SUPPLEMENT_hypotheses_methods_exclusions.md` | 8,531 |
| `Sanctions Events, Crisis Windows & Data Readiness.md` | 42,893 |
| `ULTIMATE_METHODOLOGY_BLUEPRINT.md` | 10,496 |
| `UNIFIED_METHODOLOGY_MANUAL.md` | 64,446 |
| `UNIFIED_PAPER_about_state_ownership.md` | 57,884 |
| `agent_prompt_dividend_dates_reexport.md` | 4,120 |
| `iqbal thesis v2.zip` | 1,143,132 |
| `news.md` | 15,877 |
| `pilot test 15 company.zip` | 2 |
| `thesis books.zip` | 6,132,593 |

**Superseding note on the previous version of this manual.** Its header states the live listing was 8 files and that
'H1-H6 equations [are] missing in the live repo' (§5.1 heading). Both statements are stale: the listing is 12 files and
`SUPPLEMENT_hypotheses_methods_exclusions.md` §A-§B contains the H1-H6 and M1-M6 equations verbatim. This manual therefore quotes the
equations from that file and from `ULTIMATE_METHODOLOGY_BLUEPRINT.md` §5 rather than reconstructing them.

---

## 0. How to use this file

Part 1 is the executable specification: §1 reproduces the governing blueprint and records, clause by clause, what was actually
executed; §2-§6 give the construction of every input; §7 gives each estimator with its fitted output; §8 lists the six named
sensitivity checks; §9 is the complete deviation register; §10 is the reproduction script inventory. Part 2 explains the whole
exercise in plain language, using only numbers that this workspace actually produced — every worked example in Part 2 is traceable to
a named raw file and row.

**Governing rule, inherited from the blueprint and applied without exception:** if a step cannot be executed exactly as specified,
the blocker is named, what was attempted is recorded, any partial substitute is described together with its quantified consequence,
and the affected results are labelled PROVISIONAL. Nothing is silently substituted.

---

## 1. The blueprint, clause by clause, and its execution status

The text of `ULTIMATE_METHODOLOGY_BLUEPRINT.md` is the authority. It is reproduced verbatim below in full, with an execution
annotation after each section.

### 1.1 Blueprint §1-§7 verbatim

````markdown
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
````

### 1.2 Execution ledger

| Clause | Requirement | Executed? | Where / with what deviation |
| --- | --- | --- | --- |
| §1 window | 2018-08-27 → 2026-08-31 Monday grid, 419 weeks, do not truncate | YES | grid built from the Wordstat files; 31,425 cells; raw price rows 31,061 → 30,973 kept [File 3 §0] |
| §2 N reporting | every table names its N and the missing tickers with individual reasons | YES | §7 tables + File 3 §6.3 (per-company observation counts for all 75) |
| §3 ASVI | SUM-RAW of the two channels, ln minus trailing-8 median, SVI=0 → missing | YES | §3.1-§3.3; 74/74 documented coverages reproduced |
| §3 Yandex | YNDX + Cyrillic only, never YDEX | YES | §3.4, with the measured cost of the decision (99 weeks would inflate >1.5x if YDEX were added) |
| §3 RV, R, lnV | formulas as written | YES | §3.2 |
| §3 date join | Wordstat Monday = price Sunday label + 1 day | YES | §3.3; verified by construction (labels in these files are always Sundays) |
| §3 ROLO | excluded from every RV-dependent test, stated every time | YES | M1/M4/M5/M6 exclude ROLO; M2 also excludes it (its RV-lag regressor is the quantised series) and M3's H2 block *includes* it — all stated in §7 |
| §3 registry | 2024-06-16 / 2024-08-25 / 12 placeholders / company gaps | YES | §5.4: the weeks are 2024-06-17 and 2024-08-26 on the Monday grid; both dropped; 12 placeholders dropped as missing (never filled) |
| §4.1 Crisis | extract the 4 windows from the reports; do not rebuild from scratch | YES | §4.1 quotes them; the independent stress scan reproduces all 4 and the 7 rejections |
| §4.2 Sanction | permanent step, 23 anchors | YES | §4.2 |
| §4.3 News | 31 events / 23 companies / 89 weeks; verify overlaps = exactly 0 | **BLOCKED (B1)** | §4.3: the dated files are absent; counts reproduce, weeks cannot be placed; News omitted from the regressions |
| §4.4 Div | 587 dated record dates required; aggregates cannot substitute | **BLOCKED (B2)** | §4.5: only 104 of 2,110 cells are recoverable (the 88 documented overlap rows + 16 derivable ones); Div excluded from primary M1/M4, partial variant reported |
| §4 overlap rule | Crisis∩Sanction, Crisis∩News, Sanction∩News = exactly 0 | **DEVIATION D1** | measured 179 for Crisis∩Sanction in the primary coding; both codings estimated; Δβ_ASVI = 0.33 SE [§4.4, §7] |
| §4.5 SOE | no temporal overlap; report a sector-FE confound check with every H6 result | YES | §7.5: sector FE are *absorbed* by two-way demeaning, so the confound check is done by demeaning within sector x week and by a federal-only classification |
| §5 H1-H6 equations | as printed | YES with the Div term handled per B2 | §7.1-§7.5 |
| §5 M3 | BH separately within the H1 block and the H2 block, never pooled | YES (rule choice recorded) | §7.3: BH applied to the 219 (H1) and 222 (H2) tests of each block including its L variants; per-L detail also printed |
| §5 M4 | pooled two-way FE with the lagged-volume term; state the sign contradiction | YES | §7.4, stated explicitly |
| §5 M5 Chow + Giles-Lieberman | use the real published tabulation from `canterbury-nz-034.pdf`; do not substitute | YES | §7.6: the PDF's Appendix Tables A1-A4 were extracted with pypdf; k=4 rows used, nominal 5% only, q=3-vs-k=4 mismatch disclosed |
| §5 M6 | 1,000 shuffles, power and MDE for every company, 'inconclusive' below 0.80 | YES + supplement | §7.7: the specified iid permutation is reported, and because it is invalid under serial dependence a circular-shift variant is added |
| §6 six sensitivity checks | run exactly these six | PARTIAL — 4 of 6 | §8: S4, S5, S6 executed; S1-S3 require the News layer (B1) and cannot be run |
| §7 missing-data protocol | name it, record attempts, quantify, flag | YES | §9 (12 entries) and File 3 §8 |

---

## 2. Data foundation

### 2.1 Archive layout (as extracted, unmodified)

`iqbal thesis v2.zip` → `iqbal thesis/Data/<Sector>/<TICKER>/`, 13 sector folders, 75 company folders, 226 files:

* **two Wordstat CSVs per company** (one keyed by the ticker, e.g. `SBER.csv`, one by the Cyrillic retail name, e.g.
  `Сбер акции.csv`) and **one investing.com price CSV** (`Sberbank Rossii Stock Price History (1).csv`). Yandex additionally has
  `YDEX.csv` (deliberately unused).
* Wordstat files: UTF-8-BOM, `;`-separated, `^M` line terminators, first line = header containing the channel name and the range
  `27.08.2018 — 06.09.2026`; column 1 = Monday `DD.MM.YYYY`; column 2 = query count with U+00A0 thousands separators;
  column 3 = share of all queries with a decimal comma.
* Price files: `"Date","Price","Open","High","Low","Vol.","Change %"`, comma-separated, quoted, `MM/DD/YYYY`, **labels are
  always Sundays** in this archive (419 of 419 in-range rows have weekday 6 — the label is the day before the Mon-Fri week it reports).

### 2.2 The 75 companies

From `Data/<Sector>/<TICKER>` folder names (the raw archive is the authority for the universe, as the manual states):

| Sector (raw folder) | N | Tickers |
| --- | --- | --- |
| Banking | 6 | AVAN, BSPB, CBOM, SBER, USBN, VTBR |
| Chemicals | 5 | AKRN, KAZT, KZOS, NKNC, PHOR |
| Consumer&Retail | 4 | ABRD, GCHE, MGNT, MVID |
| Diversified | 2 | AFKS, SFIN |
| Energy | 12 | BANE, GAZP, JNOS, LKOH, MFGS, NVTK, RNFT, ROSN, SIBN, SNGS, TATN, VJGZ |
| Industrial | 1 | KMAZ |
| Insurance | 1 | RGSS |
| Metals&Mining | 15 | ALRS, BLNG, CHMF, CHMK, GMKN, MAGN, NLMK, PLZL, RASP, ROLO, RUAL, SELG, TRMK, UKUZ, VSMO |
| Real Estate | 3 | LSRG, MSTT, PIKK |
| Tech | 3 | IRKT, UNAC, YNDX |
| Telecom | 4 | MGTS, MTSS, RTKM, TTLK |
| Transportation | 4 | AFLT, FESH, NMTP, UTAR |
| Utilities | 15 | FEES, HYDR, IRAO, LSNG, MRKC, MRKK, MRKP, MRKS, MRKU, MSNG, MSRS, OGKB, TGKA, UPRO, YAKG |
| **Total** | **75** | |

Excluded from the 100-candidate universe (25 names, each with its published reason) and the panel-level exclusions are reproduced in
File 3 §6.1-§6.2; the per-model N reconciliation is §7 below.

---

## 3. Core variable construction, step by step, with real traces

### 3.1 SVI — the sum of the two channels, raw

For each company and each Wordstat Monday, `SVI = count_ticker + count_cyrillic`, summed **before** any log or normalisation
(blueprint §3: 'SUM RAW ticker + Cyrillic counts week-by-week'). Rules that were found necessary and are applied here:

1. A week present in only one channel still contributes that channel's count (sum, not intersection).
2. A count of 0 in a channel is added as 0 (it is a real observation of zero interest); but if the **summed** SVI is 0 the week is set
   to missing, because ln(0) is undefined and the blueprint's `SVI=0 → missing` refers to the level used in the log.
3. A missing week in the file (no row) is missing, never interpolated or forward-filled.

### 3.2 ASVI — attention minus its own recent normal

```
lnSVI(i,t) = ln(SVI(i,t))
ASVI(i,t)  = lnSVI(i,t) - median{ lnSVI(i,t-1), lnSVI(i,t-2), ..., lnSVI(i,t-8) }
```

The median over the trailing 8 weeks is the 'normal level' the blueprint defines; a week whose 8-week window is incomplete has no
ASVI. That is why the earliest usable ASVI is the 9th week of each company's series and why coverage differs across companies.

**Worked trace 1 — SBER, week starting 2022-02-21 (the invasion week). Raw lines, verbatim from the archive:**

* `#U0421#U0431#U0435#U0440 #U0430#U043a#U0446#U0438#U0438.csv` → `21.02.2022;83 504;0,002936;`
* `SBER.csv` → `21.02.2022;168 760;0,00593;`

The first field after the date is the query count: 83,504 (Cyrillic channel «Сбер акции») + 168,760 (`SBER.csv`, «SBER»)
= **SVI = 252,264**; ln(252264) = **12.438231**.

The 8 preceding weeks of the summed series (from the same two files):

| t-k | Monday | SVI | ln(SVI) |
| --- | --- | --- | --- |
| 8 | 2021-12-27 | 26,805 | 10.196344 |
| 7 | 2022-01-03 | 23,655 | 10.071330 |
| 6 | 2022-01-10 | 41,096 | 10.623666 |
| 5 | 2022-01-17 | 64,660 | 11.076898 |
| 4 | 2022-01-24 | 56,733 | 10.946111 |
| 3 | 2022-01-31 | 44,945 | 10.713195 |
| 2 | 2022-02-07 | 42,824 | 10.664854 |
| 1 | 2022-02-14 | 48,457 | 10.788432 |

median of the 8 values above = **10.689024**, so **ASVI = 12.438231 − 10.689024 = 1.749207**. The frame column agrees to
1.749207 — this is the documented example in the previous manual (1.7492) reproduced from the raw files.

**Worked trace 2 — Yandex (the special case), same week.**

* `#U042f#U043d#U0434#U0435#U043a#U0441 #U0430#U043a#U0446#U0438#U0438.csv` → `21.02.2022;93 332;0,00328;`
* `YNDX.csv` → `21.02.2022;50 647;0,001781;`

93,332 + 50,647 = **143,979** = the documented all-time panel maximum for Yandex.

Yandex trailing-8 median of ln(SVI) = 10.559177; **ASVI = 11.877423 − 10.559177 = 1.318246** (frame 1.318246);
the documented value is 1.3182 ✅.

### 3.3 RV, R, lnV — from the price file

```
RV(i,t) = (High - Low) / Close          R(i,t) = ln(Close(i,t)/Close(i,t-1))       lnV(i,t) = ln(Vol(i,t))
```

**Worked trace 3 — SBER, same week.** The price row is labelled with the Sunday before the trading week:

* `Sberbank Rossii Stock Price History (1).csv` → `"02/20/2022","131.12","249.15","258.32","89.59","3.26B","-47.61%"`  (label 2/20/2022 ⇒ grid week **2022-02-21** = label + 1 day)
* RV = (258.32 − 89.59)/131.12 = 168.73/131.12 = **1.286836** (frame 1.286836)
* R = ln(131.12/250.28) = **-0.646467**; the file's own `Change %` column prints `-47.61%`, which is exactly 131.12/250.28 − 1, i.e. a simple return, not a log return. The log form is what the specification asks for, so `Change %` is never used
* lnV = ln(3.26e9) = **21.904993** (frame 21.904993)

**Data-quality note found while tracing (new, disclosed).** The `Vol.` column in the price files is printed with mixed units
(SBER `3.26` meaning 3.26 billion; GMKN `304.85` meaning 304.85 million; AVAN `22.09` meaning 22,090 shares). The pipeline stores the
unit-reconstructed absolute volume, so **within-company** lnV changes are correct, but the *level* of lnV is not comparable across
companies. Consequence: in the per-company models (M1, M2, M3, M5, M6) lnV enters only through a company-specific intercept and is
harmless; in the pooled M4 it is a cross-sectional regressor, so the pooled lnV coefficient (0.003475) carries a units caveat. Recorded
as deviation D13 in §9.

**Zero-range weeks.** Only 2 cells in the M1 sample have RV = 0 exactly (High = Low): AVAN 2019-01-14 and JNOS 2018-12-31. They are
kept (RV=0 is a real observation, not a gap) [RAW].

### 3.4 Yandex channel decision

Blueprint: use `YNDX` + «Яндекс акции» only; **never** `YDEX`. Executed. Measured cost: adding `YDEX.csv` would raise the summed SVI
by more than 1.5x in 99 of the panel weeks, all from 2024-07-22 onward, i.e. the relisting period — the documented limitation is
exactly those ~2 years of YDEX search volume, which are excluded on purpose [RAW: verify1.py].

### 3.5 Coverage produced by §3.1-§3.4 [RAW] vs the published target [DOC: Manual §2]

| ASVI observations | N companies | Tickers |
| --- | --- | --- |
| 411 | 69 | ABRD, AFKS, AFLT, AKRN, ALRS, AVAN, BANE, BLNG, BSPB, CBOM, CHMF, CHMK, … (all 69) |
| 395 | 1 | SFIN |
| 393 | 1 | UKUZ |
| 382 | 1 | MRKU |
| 377 | 1 | MRKS |
| 344 | 1 | MRKC |
| 282 | 1 | MRKK |

Published: 69 companies at 411 and the six short ones MRKK 282, MRKC 344, MRKS 377, MRKU 382, UKUZ 393, SFIN 395 — reproduced
exactly: 69 companies at 411 observations and the six short series as published. Verified row by row in `verify1.out`.

---

## 4. The four controls

### 4.1 Crisis(t) — extracted, not rebuilt

The four windows are taken from the source report (blueprint §4.1 forbids rebuilding them): W1 2020-02-24→2020-04-13,
W2 2022-02-21→2022-03-28, W3 2023-09-04→2023-09-18, W4 2026-06-22→2026-07-27, i.e. 23 distinct weeks ⇒ **1,725 company-weeks
(5.49% of the panel)**, identical for every company in a week. The full table with peak fractions and the seven rejected candidates is
reproduced in File 3 §1.1-§1.4.

The independent stress scan was run **as a verification**, not as the source: it reproduces all four windows and the seven rejections
(`export/Stress_weekly_full.csv`, 419 rows). Rule: RV ≥ 2× and volume ≥ 2× the company's own trailing-52-week medians; ≥30 prior
observations; a week counts if ≥25% of eligible companies spike; a window is confirmed if its peak week reaches ≥40%.

### 4.2 Sanction(i,t) — permanent step from the 23 anchors

0 before the confirmed first-designation date, 1 from it onward (RUAL is the one relief event, coded as a step **down** at
2019-01-27). Anchors and per-company cell counts: File 3 §2.1. Total in the frame: **3,231 cells** across 23 companies.

### 4.3 News(i,t) — BLOCKER B1

Cannot be constructed: `work/g4_events_final.csv` and `output/News_i_t.csv` are absent from the repo and from the archive (searched
both). Only the 23 per-company counts (89 weeks) and 2 dated events exist. The regressor is therefore **omitted**, and the three
sensitivity checks that manipulate the News layer (§8 S1-S3) cannot be run. Full statement: File 3 §3.4-§3.5.

### 4.4 The overlap measurement, and why Crisis ∩ Sanction is 179

| Pair | Measured | Compliant alternative |
| --- | --- | --- |
| Crisis ∩ Sanction | **179** cells (10.38% of crisis cells) | reaction windows with crisis weeks masked → 0 (199 cells), estimated in parallel |
| Crisis ∩ News | 0 (documented; not recomputable) | — |
| Sanction ∩ News | 0 (documented; not recomputable) | — |

The 179 is not a coding slip: a permanent step that starts before 2026-06-22 is by definition still 1 inside W4, and two anchors
(VTBR 2022-02-24, plus the NMTP sectoral step) fall inside W2. Both codings are reported side by side in §7; the effect on the
headline coefficient is 0.33 standard errors (median β_ASVI −0.00431 → −0.00456 for M1; −0.00458 if the 179 cells are instead dropped).

### 4.5 Div(i,t) — BLOCKER B2, partial build

Rule applied exactly (record week excluded, four pre-weeks, unions) but only to the **104** dated cells the archive supports: the 88
documented overlap rows plus 16 derivable ones. Coverage 104 / 2,110 = 4.9%. Consequence and quantification: §9 D3; the full per-company
table with every published count and overlap: File 3 §4.

### 4.6 SOE(i)

Time-invariant dummy, 35/40, from the paper's Appendix A; special cases (VSMO knife-edge, UPRO administration, golden shares, tier-B
opacity) are reproduced with their reasoning in File 3 §5.1-§5.3.

---

## 5. Frame assembly (the exact object the models consume)

Per company, 419 rows aligned to the grid, built by `/home/user/work/build_frame.py`:

| Column | Definition | | Column | Definition |
| --- | --- | --- | --- | --- |
| `ASVI` | §3.2 | | `ASVI_l1..l8` | ASVI lagged 1..8 weeks |
| `RV` | (H−L)/C of the week | | `RV_l1..l8` | RV lagged 1..8 |
| `R` | ln(C_t/C_{t−1}) | | `R_l1..l8` | R lagged 1..8 |
| `lnV` | ln(volume) | | `lnV_l` | lagged 1 |
| `Crisis` | 1 inside the 4 windows | | `Crisis_l` | lagged 1 |
| `Sanction` | permanent step | | `Sanction_l` | lagged 1 (**this is what enters the regressions**, per the equations) |
| `Sectoral` | sectoral-only step | | `Div` / `Div_l` | partial dividend flag (B2) and its lag |
| `SOE` | 0/1 | | `SOE_fed` | federal-only variant for sensitivity 5 |
| `ok` | mask: RV, R, ASVI_l, RV_l, lnV_l all finite, minus weeks 2024-06-17 and 2024-08-26 | | `ok_M2` | same with R as the dependent variable |

Two structural consequences of the lag convention that a reader must know before interpreting any number here:

* **`Sanction_l` in a lagged equation is a one-week indicator, not a level.** Stepping permanently at week a means Sanction(i,t−1) is 1
  only for t = a+1 within the usable sample and afterwards, so it *is* a level for t > a+1 too; what the lag does is remove the
  anchor week itself from its own explanation. Concretely: `Sanction_l` takes exactly 2 distinct values (0 and 1) for every company —
  verified over all 75 — so in the pooled two-way specification it is absorbed by the entity demeaning (see §7.4), while in per-company
  regressions it functions as a pre/post level contrast.
* **`Crisis_l` is common to all companies in a given week**, so it is absorbed by the time demeaning of the pooled model. That is why
  §7.4's pooled estimate is numerically identical whether or not Crisis is included (0.001778 both ways) — the control is in the
  specification, and its effect is exactly zero by construction.

## 6. The estimation sample, described

| Quantity | Value (M1 sample: 73 companies, 29,103 cells) |
| --- | --- |
| RV(i,t) | mean 0.0786, median 0.0604, sd 0.0690, p1 0.0149, p99 0.3518, max 1.3676 |
| ASVI(i,t−1) | mean 0.0240, median −0.0071, sd 0.3629, p1 −0.7721, p99 1.2552, min −1.7964, max 3.4247 |
| R(i,t) | mean 0.00004, sd 0.05674, p1 −0.1538, p99 0.1583, min −2.3141 (suspension week), max 0.8390 |
| lnV(i,t−1) | mean 15.42, sd 4.13, min 2.30, max 30.35 (see the units caveat in §3.3) |
| Crisis=1 inside the usable sample | 1,322 cells (of the 1,725 total — 403 fall in weeks a company has no usable row for) |
| Sanction=1 inside the usable sample | 3,165 cells |
| Div=1 inside the usable sample | 103 cells (4.9% coverage problem, B2) |
| per-company corr(ASVI, RV) | mean 0.371, median 0.377, min 0.027 (FEES), max 0.580 (BLNG), n = 73 |
| pooled corr(ASVI_lag1, RV) | 0.156 |
| observations per company | min 334 (MRKC), max 404 (VTBR); MRKK 273 excluded by the ≥300 screen |

The 73 per-company correlations are the same quantity as the previous manual's Appendix B table (73 values) and were checked against
it: all 73 agree to 5e-4 [RAW: verify1.py].

---

---

## 7. The six estimators, as executed

Everything in this section is fitted on the frame described in §5–§6 with Newey–West HAC
standard errors (bandwidth 4 weeks) unless a line says otherwise. Three things are true of all of
them and are stated once here instead of six times:

1. **News is not in the regressors.** The event matrix that §4.3 would have supplied does not exist
   in the repository (blocker **B1**, §8), so the `News(i,t−1)` term is absent from every equation.
   Consequence and direction of bias are quantified in §8, not guessed.
2. **`Div(i,t−1)` is the partial build** (104 real cells = 4.9% of the intended 2,110), not the 587-row
   date table (blocker **B2**). Its measured effect, from the same code path as the primary fits: adding the
   partial column moves the per-company M1 median β1 from −0.004313 to −0.004125 (mean |Δ| = 3.8% of the mean
   HAC SE; the *median* |Δ| is 0 because 12 companies have no Div cells at all) and moves the pooled M4
   β1 by 0.04 SE. It changes no significance count (11 of 73 either way). Numbers reproduced by
   `divquant.py`; the earlier '≈0.7 SE' figure in File 3 §4.4 was computed on a different control set and has
   been corrected there.
3. **Every table names its N and reconciles it against 75.** Two names account for every difference:
   ROLO is out of every test whose regressors or dependent variable use RV (its 1-kopeck tick size
   inflates RV mechanically), and MRKK is out of every *per-company* regression because its 273 usable
   weeks are below the 300-observation gate. Hence 73 for M1, M2, M6 and the M3-H1 block; 74 for pooled
   M4, for M5, and for the M3-H2 block, where MRKK is retained because the gate is a per-company rule
   and those fits are not per-company regressions (ROLO is still out of M4 and M5 because both consume
   RV). The cost of that reading is measured, not hidden: dropping MRKK from pooled M4 moves β1(ASVI)
   from +0.001717 to +0.002120 (0.27 SE, `export/m4_nomrkk.json`) and both fits stay insignificant.

### 7.0 What the published specification says (verbatim, SUPPLEMENT §B)

````markdown   ← verbatim, four-backtick fence so the inner fences render
**M1 — per-company OLS, volatility (tests H1):**
```
RV(i,t) = α + β1·ASVI(i,t−1) + β2·RV(i,t−1) + β3·ln[V(i,t−1)]
          + β4·Crisis(t−1) + β5·Sanction(i,t−1) + β6·Div(i,t−1) + ε(i,t)
```

**M2 — per-company OLS, returns (tests H2):**
```
R(i,t) = α + β1·ASVI(i,t−1) + β2·R(i,t−1) + β3·RV(i,t−1)
         + β4·Crisis(t−1) + β5·Sanction(i,t−1) + β6·Div(i,t−1) + ε(i,t)
```
Both M1 and M2: Newey-West HAC standard errors, bandwidth = 4 weeks.

**M3 — Granger causality (tests H1/H2 robustness):**
Restricted model (own lags + controls) vs. unrestricted (+ L lags of
ASVI), L = 1, 2, 4. F-test per company per hypothesis. Benjamini-Hochberg
FDR correction applied SEPARATELY within the H1 test block and the H2
test block — never pooled together. Bonferroni-Holm reported as a
conservative robustness check alongside BH.

**M4 — Pooled panel fixed effects (tests H1 aggregate, H4, H5, H6):**
```
RṼ(i,t) = β1·ÃSVI(i,t−1) + β2·RṼ(i,t−1) + β3·ln[Ṽ(i,t−1)]
          + β4·Dĩv(i,t−1) + ε̃(i,t)
```
(entity + time fixed effects; tilde = entity-and-time-demeaned). **This
lagged-volume term (β3) was previously missing from this specification —
its addition is a documented correction, made because its omission was
identified as the likely cause of a sign contradiction between this
pooled result and the per-company (M1) results in an earlier round. This
is the reason for the correction; it was not made for any other reason.**
- H4 test: add ASVI×Sector interaction terms to this model; Wald test on
  their joint significance.
- H5 test: add an ASVI×Div(i,t−1) interaction term to this model.
- H6 test: add an ASVI×SOE(i) interaction term to this model.

**M5 — Asymmetric Chow structural break test (tests H3):**
Reduced form only: ASVI(i,t−1) and RV(i,t−1) as regressors (full M1
controls excluded here — they are collinear with the break date). Break
date 2022-02-24. Pre-period model: 3 parameters (no crisis dummy).
Post-period model: 4 parameters (adds a crisis_chow dummy for the acute
window). q = k_pre + k_post − k_pool = 3 restrictions, using k_pool = 4
(the pooled model imposes the 4-parameter post-crisis specification
across the entire sample — this is what makes k_pool = 4 well-defined).
Apply Giles-Lieberman heteroscedasticity-robust bounds to every company's
Chow F-statistic; report nominal-significant count vs. bounds-survivor
count separately.

**M6 — Permutation falsification and statistical power (robustness for
H1/H2):** 1,000 random shuffles of each company's ASVI series, re-fit M1,
compute permutation p-value = (count of |β1_shuffled| ≥ |β1_observed|) /
1000. Statistical power via the non-central t-distribution: ncp =
|β1_observed| / SE_HAC; report power and the minimum detectable effect
(MDE) at 80% power for every company. **Any null result must be reported
alongside its power — a result from a test with power below 0.80 is
"inconclusive due to insufficient power," never "no effect exists."**
````

### 7.1 M1 — per-company OLS on realised volatility (tests H1)

```
RV(i,t) = α + β1·ASVI(i,t−1) + β2·RV(i,t−1) + β3·lnV(i,t−1) + β4·Crisis(t−1) + β5·Sanction(i,t−1) + β6·Div(i,t−1) + ε(i,t)
```

*companies fitted: **73** (75 − ROLO − MRKK; MRKK is dropped by the <300-observation rule,
ROLO by the RV-exclusion rule — no company is missing for any other reason) [RAW].*

| summary | value |
| -- | -- |
| median β1 across companies | **-0.004313** |
| companies with β1 significant at 5% (HAC) | 11 of 73 |
| of those, β1 positive and significant | 2 |
| the significant names | RTKM (-0.0327, p=2.0e-05), MFGS (+0.0476, p=0.0002), SNGS (-0.0330, p=0.0018), LSRG (-0.0216, p=0.0028), AFKS (-0.0153, p=0.0098), PHOR (-0.0114, p=0.0160), NLMK (-0.0174, p=0.0231), JNOS (+0.0317, p=0.0308), VSMO (-0.0181, p=0.0316), YNDX (-0.0248, p=0.0325), IRAO (-0.0146, p=0.0496) |

Full result, every company, HAC t and p:

| tk | β | SE | t | p | n | | tk | β | SE | t | p | n | | tk | β | SE | t | p | n |
| -- | -- | -- | -- | -- | -- | --- | -- | -- | -- | -- | -- | --- | -- | -- | -- | -- | -- | | |
| ABRD | +0.0113 | 0.0090 | +1.26 | 0.2096 | 401 | LSNG | -0.0009 | 0.0096 | -0.10 | 0.9230 | 392 | RNFT | +0.0087 | 0.0068 | +1.27 | 0.2034 | 401 | | |
| AFKS | -0.0153 | 0.0059 | -2.60 | 0.0098 | 402 | LSRG | -0.0216 | 0.0072 | -3.00 | 0.0028 | 401 | ROSN | -0.0035 | 0.0072 | -0.49 | 0.6256 | 402 | | |
| AFLT | -0.0044 | 0.0075 | -0.59 | 0.5565 | 402 | MAGN | -0.0240 | 0.0123 | -1.95 | 0.0515 | 402 | RTKM | -0.0327 | 0.0076 | -4.30 | 2.0e-05 | 402 | | |
| AKRN | -0.0093 | 0.0063 | -1.47 | 0.1417 | 401 | MFGS | +0.0476 | 0.0126 | +3.78 | 0.0002 | 399 | RUAL | -0.0110 | 0.0069 | -1.60 | 0.1103 | 402 | | |
| ALRS | -0.0082 | 0.0055 | -1.49 | 0.1366 | 402 | MGNT | +0.0014 | 0.0068 | +0.21 | 0.8371 | 402 | SBER | -0.0170 | 0.0134 | -1.28 | 0.2027 | 403 | | |
| AVAN | +0.0023 | 0.0234 | +0.10 | 0.9227 | 390 | MGTS | +0.0164 | 0.0185 | +0.89 | 0.3761 | 401 | SELG | +0.0078 | 0.0065 | +1.21 | 0.2257 | 401 | | |
| BANE | -0.0119 | 0.0109 | -1.09 | 0.2772 | 401 | MRKC | +0.0101 | 0.0064 | +1.58 | 0.1155 | 334 | SFIN | -0.0074 | 0.0125 | -0.59 | 0.5556 | 385 | | |
| BLNG | -0.0045 | 0.0117 | -0.38 | 0.7027 | 401 | MRKP | -0.0036 | 0.0037 | -0.96 | 0.3354 | 401 | SIBN | -0.0078 | 0.0072 | -1.09 | 0.2751 | 401 | | |
| BSPB | -0.0077 | 0.0117 | -0.66 | 0.5116 | 401 | MRKS | -0.0213 | 0.0126 | -1.69 | 0.0924 | 367 | SNGS | -0.0330 | 0.0105 | -3.15 | 0.0018 | 402 | | |
| CBOM | -0.0030 | 0.0051 | -0.59 | 0.5579 | 402 | MRKU | -0.0010 | 0.0050 | -0.21 | 0.8349 | 374 | TATN | -0.0052 | 0.0119 | -0.44 | 0.6634 | 402 | | |
| CHMF | -0.0123 | 0.0077 | -1.58 | 0.1137 | 402 | MSNG | -0.0009 | 0.0055 | -0.16 | 0.8717 | 401 | TGKA | -0.0012 | 0.0064 | -0.19 | 0.8488 | 401 | | |
| CHMK | -0.0013 | 0.0110 | -0.12 | 0.9043 | 401 | MSRS | +0.0130 | 0.0079 | +1.64 | 0.1017 | 401 | TRMK | -0.0038 | 0.0055 | -0.70 | 0.4839 | 401 | | |
| FEES | -0.0134 | 0.0111 | -1.21 | 0.2270 | 399 | MSTT | -0.0061 | 0.0074 | -0.82 | 0.4101 | 397 | TTLK | +0.0016 | 0.0078 | +0.20 | 0.8397 | 401 | | |
| FESH | +0.0068 | 0.0067 | +1.01 | 0.3133 | 401 | MTSS | -0.0043 | 0.0057 | -0.75 | 0.4528 | 402 | UKUZ | -0.0002 | 0.0109 | -0.02 | 0.9872 | 383 | | |
| GAZP | -0.0162 | 0.0128 | -1.26 | 0.2075 | 403 | MVID | -0.0198 | 0.0148 | -1.34 | 0.1815 | 401 | UNAC | +0.0051 | 0.0195 | +0.26 | 0.7948 | 401 | | |
| GCHE | +0.0021 | 0.0046 | +0.45 | 0.6547 | 401 | NKNC | -0.0009 | 0.0078 | -0.11 | 0.9108 | 401 | UPRO | -0.0109 | 0.0087 | -1.26 | 0.2100 | 401 | | |
| GMKN | -0.0071 | 0.0070 | -1.01 | 0.3129 | 402 | NLMK | -0.0174 | 0.0076 | -2.28 | 0.0231 | 402 | USBN | +0.0006 | 0.0127 | +0.05 | 0.9611 | 401 | | |
| HYDR | -0.0070 | 0.0079 | -0.89 | 0.3768 | 402 | NMTP | -0.0088 | 0.0075 | -1.19 | 0.2368 | 401 | UTAR | -0.0070 | 0.0167 | -0.42 | 0.6725 | 401 | | |
| IRAO | -0.0146 | 0.0074 | -1.97 | 0.0496 | 402 | NVTK | -0.0003 | 0.0082 | -0.04 | 0.9701 | 402 | VJGZ | +0.0223 | 0.0155 | +1.44 | 0.1515 | 397 | | |
| IRKT | +0.0151 | 0.0111 | +1.36 | 0.1734 | 401 | OGKB | +0.0029 | 0.0056 | +0.52 | 0.6030 | 401 | VSMO | -0.0181 | 0.0084 | -2.16 | 0.0316 | 401 | | |
| JNOS | +0.0317 | 0.0146 | +2.17 | 0.0308 | 401 | PHOR | -0.0114 | 0.0047 | -2.42 | 0.0160 | 402 | VTBR | -0.0017 | 0.0233 | -0.07 | 0.9405 | 404 | | |
| KAZT | +0.0164 | 0.0092 | +1.79 | 0.0748 | 399 | PIKK | -0.0123 | 0.0115 | -1.07 | 0.2856 | 402 | YAKG | +0.0146 | 0.0132 | +1.11 | 0.2692 | 401 | | |
| KMAZ | -0.0140 | 0.0093 | -1.51 | 0.1319 | 401 | PLZL | -0.0056 | 0.0088 | -0.63 | 0.5275 | 402 | YNDX | -0.0248 | 0.0115 | -2.15 | 0.0325 | 398 | | |
| KZOS | +0.0040 | 0.0075 | +0.54 | 0.5910 | 401 | RASP | -0.0073 | 0.0075 | -0.97 | 0.3339 | 401 | | | | | | | | |
| LKOH | -0.0041 | 0.0115 | -0.36 | 0.7195 | 402 | RGSS | +0.0075 | 0.0127 | +0.59 | 0.5544 | 401 | | | | | | | | |

### 7.2 M2 — per-company OLS on returns (tests H2)

```
R(i,t) = α + β1·ASVI(i,t−1) + β2·R(i,t−1) + β3·RV(i,t−1) + β4·Crisis(t−1) + β5·Sanction(i,t−1) + β6·Div(i,t−1) + ε(i,t)
```

*companies fitted: **73** — same 75 − ROLO − MRKK. Note that the SUPPLEMENT allows ROLO in
R-based tests, but M2 carries `RV(i,t−1)` as a regressor, and ROLO's RV is the corrupted series, so ROLO
is excluded here too and stated rather than quietly dropped* [RAW].

| summary | value |
| -- | -- |
| median β1 | **-0.003353** |
| significant at 5% | 6 of 73 |
| positive and significant | 2 |
| the significant names | FEES (-0.0313, p=0.0166), GCHE (+0.0118, p=0.0244), OGKB (-0.0155, p=0.0305), PLZL (+0.0350, p=0.0326), LSNG (-0.0219, p=0.0367), MTSS (-0.0135, p=0.0396) |

| tk | β | SE | t | p | n | | tk | β | SE | t | p | n | | tk | β | SE | t | p | n |
| -- | -- | -- | -- | -- | -- | --- | -- | -- | -- | -- | -- | --- | -- | -- | -- | -- | -- | | |
| ABRD | +0.0043 | 0.0065 | +0.65 | 0.5132 | 399 | LSNG | -0.0219 | 0.0104 | -2.10 | 0.0367 | 389 | RNFT | -0.0096 | 0.0051 | -1.90 | 0.0582 | 399 | | |
| AFKS | -0.0001 | 0.0088 | -0.01 | 0.9929 | 400 | LSRG | +0.0008 | 0.0063 | +0.13 | 0.8956 | 399 | ROSN | +0.0040 | 0.0083 | +0.48 | 0.6325 | 400 | | |
| AFLT | +0.0041 | 0.0086 | +0.48 | 0.6347 | 400 | MAGN | +0.0006 | 0.0086 | +0.07 | 0.9434 | 400 | RTKM | -0.0076 | 0.0097 | -0.79 | 0.4319 | 400 | | |
| AKRN | -0.0028 | 0.0049 | -0.57 | 0.5670 | 399 | MFGS | -0.0129 | 0.0093 | -1.39 | 0.1644 | 396 | RUAL | +0.0000 | 0.0071 | +0.00 | 0.9977 | 400 | | |
| ALRS | -0.0131 | 0.0082 | -1.60 | 0.1110 | 400 | MGNT | -0.0094 | 0.0120 | -0.79 | 0.4332 | 400 | SBER | -0.0094 | 0.0096 | -0.98 | 0.3276 | 402 | | |
| AVAN | +0.0076 | 0.0126 | +0.61 | 0.5447 | 385 | MGTS | -0.0112 | 0.0116 | -0.96 | 0.3359 | 399 | SELG | +0.0064 | 0.0075 | +0.85 | 0.3937 | 399 | | |
| BANE | +0.0072 | 0.0122 | +0.59 | 0.5564 | 399 | MRKC | -0.0063 | 0.0065 | -0.98 | 0.3290 | 332 | SFIN | +0.0082 | 0.0113 | +0.73 | 0.4682 | 383 | | |
| BLNG | +0.0134 | 0.0101 | +1.33 | 0.1846 | 399 | MRKP | -0.0073 | 0.0046 | -1.60 | 0.1103 | 399 | SIBN | -0.0063 | 0.0079 | -0.80 | 0.4242 | 399 | | |
| BSPB | -0.0160 | 0.0120 | -1.34 | 0.1820 | 399 | MRKS | -0.0032 | 0.0104 | -0.31 | 0.7560 | 365 | SNGS | -0.0063 | 0.0119 | -0.53 | 0.5935 | 400 | | |
| CBOM | +0.0049 | 0.0085 | +0.58 | 0.5625 | 400 | MRKU | +0.0028 | 0.0061 | +0.47 | 0.6413 | 373 | TATN | -0.0084 | 0.0102 | -0.83 | 0.4088 | 400 | | |
| CHMF | +0.0065 | 0.0093 | +0.71 | 0.4807 | 400 | MSNG | -0.0111 | 0.0080 | -1.39 | 0.1648 | 399 | TGKA | +0.0010 | 0.0066 | +0.16 | 0.8745 | 399 | | |
| CHMK | +0.0037 | 0.0083 | +0.44 | 0.6604 | 399 | MSRS | -0.0059 | 0.0063 | -0.93 | 0.3515 | 399 | TRMK | -0.0034 | 0.0048 | -0.70 | 0.4831 | 399 | | |
| FEES | -0.0313 | 0.0130 | -2.40 | 0.0166 | 396 | MSTT | -0.0021 | 0.0064 | -0.33 | 0.7442 | 394 | TTLK | -0.0020 | 0.0064 | -0.31 | 0.7569 | 399 | | |
| FESH | +0.0015 | 0.0100 | +0.15 | 0.8830 | 399 | MTSS | -0.0135 | 0.0066 | -2.06 | 0.0396 | 400 | UKUZ | +0.0065 | 0.0068 | +0.96 | 0.3352 | 381 | | |
| GAZP | -0.0108 | 0.0116 | -0.93 | 0.3520 | 402 | MVID | +0.0170 | 0.0130 | +1.31 | 0.1922 | 399 | UNAC | -0.0028 | 0.0178 | -0.16 | 0.8744 | 399 | | |
| GCHE | +0.0118 | 0.0052 | +2.26 | 0.0244 | 399 | NKNC | -0.0056 | 0.0067 | -0.83 | 0.4053 | 399 | UPRO | -0.0077 | 0.0069 | -1.12 | 0.2641 | 399 | | |
| GMKN | -0.0082 | 0.0077 | -1.05 | 0.2922 | 400 | NLMK | -0.0085 | 0.0106 | -0.80 | 0.4248 | 400 | USBN | -0.0114 | 0.0107 | -1.06 | 0.2907 | 399 | | |
| HYDR | +0.0001 | 0.0089 | +0.01 | 0.9939 | 400 | NMTP | -0.0009 | 0.0100 | -0.09 | 0.9254 | 399 | UTAR | -0.0164 | 0.0163 | -1.01 | 0.3129 | 399 | | |
| IRAO | -0.0122 | 0.0093 | -1.31 | 0.1908 | 400 | NVTK | -0.0090 | 0.0080 | -1.13 | 0.2587 | 400 | VJGZ | +0.0055 | 0.0116 | +0.48 | 0.6344 | 393 | | |
| IRKT | -0.0061 | 0.0124 | -0.49 | 0.6244 | 399 | OGKB | -0.0155 | 0.0071 | -2.17 | 0.0305 | 399 | VSMO | -0.0078 | 0.0078 | -1.00 | 0.3182 | 399 | | |
| JNOS | +0.0263 | 0.0190 | +1.38 | 0.1672 | 399 | PHOR | -0.0091 | 0.0051 | -1.79 | 0.0746 | 400 | VTBR | -0.0085 | 0.0192 | -0.45 | 0.6564 | 403 | | |
| KAZT | -0.0039 | 0.0077 | -0.50 | 0.6158 | 396 | PIKK | +0.0063 | 0.0094 | +0.67 | 0.5047 | 400 | YAKG | +0.0026 | 0.0143 | +0.18 | 0.8558 | 399 | | |
| KMAZ | -0.0096 | 0.0057 | -1.70 | 0.0908 | 399 | PLZL | +0.0350 | 0.0163 | +2.15 | 0.0326 | 400 | YNDX | +0.0009 | 0.0115 | +0.08 | 0.9378 | 396 | | |
| KZOS | -0.0085 | 0.0060 | -1.41 | 0.1593 | 399 | RASP | -0.0068 | 0.0113 | -0.61 | 0.5448 | 399 | | | | | | | | |
| LKOH | -0.0003 | 0.0120 | -0.02 | 0.9831 | 400 | RGSS | +0.0124 | 0.0131 | +0.95 | 0.3441 | 399 | | | | | | | | |

### 7.3 M3 — Granger causality, L = 1, 2, 4, BH inside each block

Restricted model = own lags + the M1 control set; unrestricted adds L lags of ASVI; F-test per company;
BH applied **separately inside the H1 block and the H2 block, never pooled**, Holm as the conservative check.

**H1 block** — 73 companies × 3 lag lengths = **219 tests**, never pooled with the other block (ROLO excluded: RV is the dependent variable's own lag structure).
BH rejections **8**; the conservative Holm check leaves **5**.
BH survivors: `MFGS`, `RTKM`, `SNGS`, `UTAR`.

| tk | p(L1) | p(L2) | p(L4) | | tk | p(L1) | p(L2) | p(L4) | | tk | p(L1) | p(L2) | p(L4) |
| -- | -- | -- | -- | --- | -- | -- | -- | -- | --- | -- | -- | -- | -- |
| ABRD | 0.2089 | 0.4437 | 0.3997 | LSNG | 0.9229 | 0.8053 | 0.8603 | RNFT | 0.2026 | 0.4405 | 0.2440 | | |
| AFKS | 0.0095 | 0.0274 | 0.1442 | LSRG | 0.0027 | 0.0090 | 0.0325 | ROSN | 0.6254 | 0.8304 | 0.3102 | | |
| AFLT | 0.5561 | 0.8327 | 0.0881 | MAGN | 0.0508 | 0.0857 | 0.0126 | RTKM | 1.7e-05 | 1.3e-07 | 9.6e-07 | | |
| AKRN | 0.1409 | 0.0535 | 0.0490 | MFGS | 0.0002 | 0.0002 | 0.0008 | RUAL | 0.1095 | 0.1680 | 0.4233 | | |
| ALRS | 0.1358 | 0.2840 | 0.2524 | MGNT | 0.8370 | 0.8551 | 0.0129 | SBER | 0.2020 | 0.4065 | 0.0978 | | |
| AVAN | 0.9226 | 0.2297 | 0.5046 | MGTS | 0.3756 | 0.3271 | 0.6605 | SELG | 0.2250 | 0.1360 | 0.1219 | | |
| BANE | 0.2765 | 0.3662 | 0.0459 | MRKC | 0.1146 | 0.2096 | 0.2430 | SFIN | 0.5553 | 0.5491 | 0.1406 | | |
| BLNG | 0.7025 | 0.6744 | 0.6331 | MRKP | 0.3348 | 0.1780 | 0.4094 | SIBN | 0.2744 | 0.4923 | 0.2257 | | |
| BSPB | 0.5112 | 0.8025 | 0.4069 | MRKS | 0.0915 | 0.1015 | 0.2129 | SNGS | 0.0016 | 0.0070 | 0.0337 | | |
| CBOM | 0.5576 | 0.2059 | 0.1123 | MRKU | 0.8347 | 0.3381 | 0.5017 | TATN | 0.6632 | 0.7753 | 0.8896 | | |
| CHMF | 0.1129 | 0.2211 | 0.0520 | MSNG | 0.8716 | 0.8162 | 0.7172 | TGKA | 0.8487 | 0.1891 | 0.3398 | | |
| CHMK | 0.9043 | 0.2314 | 0.1813 | MSRS | 0.1009 | 0.1972 | 0.1189 | TRMK | 0.4835 | 0.6715 | 0.9206 | | |
| FEES | 0.2263 | 0.3992 | 0.4665 | MSTT | 0.4096 | 0.6471 | 0.6045 | TTLK | 0.8396 | 0.6776 | 0.3337 | | |
| FESH | 0.3127 | 0.0604 | 0.0992 | MTSS | 0.4524 | 0.5606 | 0.8005 | UKUZ | 0.9872 | 0.1699 | 0.3730 | | |
| GAZP | 0.2067 | 0.3601 | 0.1671 | MVID | 0.1807 | 0.2560 | 0.5675 | UNAC | 0.7946 | 0.0206 | 0.0613 | | |
| GCHE | 0.6545 | 0.5764 | 0.4206 | NKNC | 0.9108 | 0.6464 | 0.4718 | UPRO | 0.2092 | 0.4087 | 0.1900 | | |
| GMKN | 0.3123 | 0.5655 | 0.7422 | NLMK | 0.0226 | 0.0586 | 0.0082 | USBN | 0.9611 | 0.7869 | 0.4354 | | |
| HYDR | 0.3762 | 0.6452 | 0.5941 | NMTP | 0.2361 | 0.3069 | 0.4813 | UTAR | 0.6723 | 0.0202 | 0.0002 | | |
| IRAO | 0.0489 | 0.1284 | 0.2437 | NVTK | 0.9700 | 0.8093 | 0.4129 | VJGZ | 0.1507 | 0.1115 | 0.1260 | | |
| IRKT | 0.1726 | 0.3687 | 0.0289 | OGKB | 0.6027 | 0.6479 | 0.2323 | VSMO | 0.0310 | 0.0756 | 0.0581 | | |
| JNOS | 0.0302 | 0.0704 | 0.0038 | PHOR | 0.0155 | 0.0501 | 0.0371 | VTBR | 0.9404 | 0.5625 | 0.4134 | | |
| KAZT | 0.0740 | 0.2001 | 0.4619 | PIKK | 0.2849 | 0.4989 | 0.2133 | YAKG | 0.2685 | 0.3700 | 0.2588 | | |
| KMAZ | 0.1311 | 0.0033 | 0.0098 | PLZL | 0.5271 | 0.3911 | 0.4322 | YNDX | 0.0319 | 0.0129 | 0.0433 | | |
| KZOS | 0.5907 | 0.1049 | 0.2460 | RASP | 0.3333 | 0.4888 | 0.0217 | | | | | | |
| LKOH | 0.7193 | 0.8628 | 0.4833 | RGSS | 0.5541 | 0.2246 | 0.5031 | | | | | | |

**H2 block** — 74 companies × 3 lag lengths = **222 tests**, never pooled with the other block (ROLO **is** retained here: the dependent variable is R, and its `RV_l` term is only screened for finiteness — stated so the extra company is not a mystery).
BH rejections **0**; the conservative Holm check leaves **0**.
BH survivors: none — no single p-value in this block survives a 222-test BH correction.

| tk | p(L1) | p(L2) | p(L4) | | tk | p(L1) | p(L2) | p(L4) | | tk | p(L1) | p(L2) | p(L4) |
| -- | -- | -- | -- | --- | -- | -- | -- | -- | --- | -- | -- | -- | -- |
| ABRD | 0.5129 | 0.7854 | 0.4628 | LSNG | 0.0361 | 0.0873 | 0.1019 | RNFT | 0.0575 | 0.0891 | 0.1652 | | |
| AFKS | 0.9929 | 0.1625 | 0.3922 | LSRG | 0.8955 | 0.1831 | 0.0876 | ROLO | 0.1088 | 0.2323 | 0.4153 | | |
| AFLT | 0.6344 | 0.6870 | 0.8552 | MAGN | 0.9434 | 0.9808 | 0.2429 | ROSN | 0.6322 | 0.8353 | 0.9556 | | |
| AKRN | 0.5666 | 0.8296 | 0.3766 | MFGS | 0.1636 | 0.1486 | 0.3955 | RTKM | 0.4314 | 0.3521 | 0.5993 | | |
| ALRS | 0.1102 | 0.2407 | 0.1845 | MGNT | 0.4327 | 0.5008 | 0.6801 | RUAL | 0.9977 | 0.5873 | 0.2435 | | |
| AVAN | 0.5444 | 0.6822 | 0.5330 | MGTS | 0.3353 | 0.6277 | 0.9041 | SBER | 0.3270 | 0.5124 | 0.4798 | | |
| BANE | 0.5561 | 0.8015 | 0.9192 | MRKC | 0.3283 | 0.5916 | 0.7204 | SELG | 0.3932 | 0.6850 | 0.3460 | | |
| BLNG | 0.1838 | 0.1161 | 0.1851 | MRKP | 0.1095 | 0.1882 | 0.2039 | SFIN | 0.4677 | 0.6501 | 0.1835 | | |
| BSPB | 0.1813 | 0.3690 | 0.6482 | MRKS | 0.7558 | 0.2659 | 0.4839 | SIBN | 0.4237 | 0.5716 | 0.7991 | | |
| CBOM | 0.5622 | 0.8410 | 0.3467 | MRKU | 0.6411 | 0.6360 | 0.4189 | SNGS | 0.5932 | 0.8664 | 0.9349 | | |
| CHMF | 0.4802 | 0.1791 | 0.1126 | MSNG | 0.1641 | 0.3281 | 0.5730 | TATN | 0.4083 | 0.6240 | 0.7166 | | |
| CHMK | 0.6602 | 0.6929 | 0.8068 | MSRS | 0.3509 | 0.2633 | 0.5500 | TGKA | 0.8745 | 0.8816 | 0.0413 | | |
| FEES | 0.0162 | 0.0263 | 0.0190 | MSTT | 0.7440 | 0.6680 | 0.0903 | TRMK | 0.4827 | 0.1060 | 0.1372 | | |
| FESH | 0.8829 | 0.9609 | 0.8862 | MTSS | 0.0390 | 0.0043 | 0.0132 | TTLK | 0.7567 | 0.9391 | 0.8881 | | |
| GAZP | 0.3514 | 0.6260 | 0.6103 | MVID | 0.1914 | 0.2529 | 0.1431 | UKUZ | 0.3346 | 0.4593 | 0.5701 | | |
| GCHE | 0.0238 | 0.0745 | 0.2231 | NKNC | 0.4048 | 0.6366 | 0.8177 | UNAC | 0.8744 | 0.9233 | 0.9866 | | |
| GMKN | 0.2916 | 0.1860 | 0.0773 | NLMK | 0.4243 | 0.5082 | 0.4747 | UPRO | 0.2634 | 0.5124 | 0.6352 | | |
| HYDR | 0.9939 | 0.8661 | 0.2315 | NMTP | 0.9253 | 0.6279 | 0.9079 | USBN | 0.2901 | 0.4240 | 0.3104 | | |
| IRAO | 0.1901 | 0.3314 | 0.6837 | NVTK | 0.2580 | 0.5016 | 0.4748 | UTAR | 0.3123 | 0.5994 | 0.2196 | | |
| IRKT | 0.6241 | 0.6913 | 0.7978 | OGKB | 0.0299 | 0.0951 | 0.3004 | VJGZ | 0.6341 | 0.0333 | 0.0046 | | |
| JNOS | 0.1664 | 0.2848 | 0.2386 | PHOR | 0.0739 | 0.2022 | 0.2583 | VSMO | 0.3175 | 0.4662 | 0.1014 | | |
| KAZT | 0.6155 | 0.4127 | 0.4302 | PIKK | 0.5043 | 0.2762 | 0.5262 | VTBR | 0.6561 | 0.8809 | 0.3177 | | |
| KMAZ | 0.0900 | 0.2341 | 0.4199 | PLZL | 0.0320 | 0.0894 | 0.3109 | YAKG | 0.8557 | 0.3708 | 0.7496 | | |
| KZOS | 0.1585 | 0.3561 | 0.3655 | RASP | 0.5445 | 0.0676 | 0.0732 | YNDX | 0.9378 | 0.7573 | 0.4479 | | |
| LKOH | 0.9831 | 0.7960 | 0.7490 | RGSS | 0.3436 | 0.2330 | 0.5874 | | | | | | |

Reading M3: in the H1 block 8 of the 219 tests survive BH; they are spread over 4 companies (MFGS, RTKM,
SNGS, UTAR) counted once per lag length, and the underlying p-values are tiny (RTKM 1.3e-07 at L2,
MFGS 2e-04 at L1). Under the stricter Holm step-down the count falls to 5, i.e. three of the eight are
borderline. The H2 block contributes nothing. So Granger evidence, where it exists at all, concerns the
volatility equation, not the return equation. That is a predictability statement inside a linear lag model,
not causal identification, and §4.3 (the News control is absent) is the reason even that reading is weakened.

### 7.4 M4 — pooled two-way fixed effects (H1 aggregate, H4, H5, H6)

Entity- and time-demeaned regressions on the estimation sample; the corrected specification
(the `lnV(i,t−1)` term the SUPPLEMENT flags as a documented correction) is what is run here.

**M4 base (Div included) — the H1 aggregate test** — n = 29376 cells (94.74% of the 74×411 grid), 74 companies,
R²(within) = 0.1308

| regressor | β | SE | t | p |
| -- | -- | -- | -- | -- |
| ASVI_l | +0.001717 | 0.001502 | +1.143 | 0.2530 |
| RV_l | +0.319950 | 0.016911 | +18.919 | 2.3e-79 |
| lnV_l | +0.003475 | 0.000472 | +7.367 | 1.8e-13 |
| Div_l | +0.011933 | 0.007968 | +1.498 | 0.1343 |

The blueprint's own equation for M4 lists only `ASVI_l, RV_l, lnV_l`; `Div_l` is one of the four controls
of §4 and is present in the frame, so both variants are reported:

**M4 exactly as written in the SUPPLEMENT equation (no Div term)** — n = 29376 cells (94.74% of the 74×411 grid), 74 companies,
R²(within) = 0.1307

| regressor | β | SE | t | p |
| -- | -- | -- | -- | -- |
| ASVI_l | +0.001778 | 0.001503 | +1.184 | 0.2366 |
| RV_l | +0.319450 | 0.016910 | +18.892 | 3.9e-79 |
| lnV_l | +0.003481 | 0.000472 | +7.381 | 1.6e-13 |

β1(ASVI) moves from 0.0017174 to 0.0017783 when the 4.9%-of-intent Div column is removed: 0.0000609,
i.e. **4.1% of one standard error** of β1 (SE 0.0015025) — the Div term is not what
keeps the pooled attention coefficient insignificant.

### 7.5 M5 — asymmetric Chow structural break, with the Giles–Lieberman bounds (tests H3)

Executed **exactly** as the SUPPLEMENT prescribes, which matters here because the earlier exported M5
table was built on a different nesting and is superseded (deviation **D14**):

* reduced form only: `ASVI(i,t−1)`, `RV(i,t−1)`;
* break date 2022-02-24, so the pre-regime starts 2022-02-21 (grid week) and the post-regime at the first
  post-break week 2022-02-28;
* pre-period model: 3 parameters (intercept, ASVI_l, RV_l);
* post-period model: 4 parameters (the same three plus the acute-window `crisis_chow` dummy, set to 1 for
  the weeks 2022-02-28 … 2022-03-28, i.e. the five grid weeks of W2 that fall after the break — the
  acute window of §4.1 truncated at the break date by construction);
* pooled (restricted) model: 4 parameters, i.e. the post-crisis specification imposed on the whole sample,
  which is exactly what makes `k_pool = 4` well-defined, so q = 3 + 4 − 4 = 3 and df = n − (k_pre+k_post);
* `θ = s_pre/s_post` per company, then the published bounds for k = 4.

Bounds used (from the PDF, Table A3 diagonal rows; see §8 for the text-layer damage we had to repair):

| T1=T2 | Cu(θ=1) | Cu(0.01,0.1] | Cu(0.1,10] | Cu(10,100] | Cu(θ>100) |
| -- | -- | -- | -- | -- | -- |
| `10` | `3.259` | `0.041` | `18.127` | `18.127` | `62.560` |
| `15` | `2.817` | `0.044` | `8.707` | `8.707` | `12.276` |
| `20` | `2.668` | `0.045` | `6.804` | `6.804` | `8.442` |
| `25` | `2.594` | `0.046` | `6.039` | `6.039` | `7.165` |
| `30` | `2.550` | `0.046` | `5.631` | `5.631` | `6.535` |
| `35` | `2.520` | `0.046` | `5.378` | `5.378` | `6.160` |
| `40` | `2.499` | `0.046` | `5.206` | `5.206` | `5.911` |

A typical company contributes 174 pre-weeks and 227 post-weeks, i.e. the retained fraction at the *shorter*
end is 174/(174+227) = 43.4% — beyond the largest tabulated T1 = T2 = 40 row, whose bounds are the tightest
in the table, so that row is used as the conservative envelope exactly as in the earlier rounds;
θ lands in (0.1,10] for 73 of 74 companies and in (10,100]
for SBER, so Cu = 5.206 for 73 companies and 5.911 for SBER.

*companies: **74** = 75 − ROLO (the F-test is on RV). Nominal 5%-significant: **24**;
survivors of the published bounds: **5**. Median F = 1.6101.*

| what | count | tickers |
| -- | -- | -- |
| nominal p < 0.05 | 24 | AFLT, AKRN, BANE, CBOM, CHMF, FEES, IRKT, KAZT, LSRG, MFGS, MGNT, MRKC, MRKU, MSNG, MSTT, MTSS, SBER, SFIN, SIBN, UNAC, UPRO, VTBR, YAKG, YNDX |
| F also beats Cu (GL survivors) | 5 | MFGS (F=8.790), MRKU (F=7.196), MSNG (F=6.001), MSTT (F=7.135), YNDX (F=7.716) |
| θ outside (0.1,10] → wider bound | 1 | SBER (θ=10.64, Cu=5.206) |

Per-company output (`export/M5_chow_gl.csv` is the same table in machine form):

| tk | F | p | θ | Cu | survives | | tk | F | p | θ | Cu | survives | | tk | F | p | θ | Cu | survives |
| -- | -- | -- | -- | -- | -- | --- | -- | -- | -- | -- | -- | -- | --- | -- | -- | -- | -- | -- | |
| ABRD | 0.382 | 0.7660 | 3.48 | 5.206 | no | LSNG | 1.750 | 0.1563 | 1.33 | 5.206 | no | RGSS | 0.170 | 0.9164 | 1.46 | 5.206 | no | | |
| AFKS | 2.084 | 0.1018 | 2.44 | 5.206 | no | LSRG | 4.349 | 0.0050 | 1.53 | 5.206 | no | RNFT | 1.610 | 0.1865 | 2.41 | 5.206 | no | | |
| AFLT | 4.827 | 0.0026 | 2.97 | 5.206 | no | MAGN | 1.589 | 0.1916 | 1.56 | 5.206 | no | ROSN | 2.299 | 0.0770 | 6.98 | 5.206 | no | | |
| AKRN | 2.956 | 0.0323 | 1.55 | 5.206 | no | MFGS | 8.790 | 0.0000 | 0.86 | 5.206 | **yes** | RTKM | 2.518 | 0.0578 | 1.51 | 5.206 | no | | |
| ALRS | 0.505 | 0.6788 | 2.74 | 5.206 | no | MGNT | 4.361 | 0.0049 | 3.37 | 5.206 | no | RUAL | 0.883 | 0.4500 | 3.16 | 5.206 | no | | |
| AVAN | 0.880 | 0.4516 | 2.48 | 5.206 | no | MGTS | 2.181 | 0.0898 | 0.94 | 5.206 | no | SBER | 4.888 | 0.0024 | 10.64 | 5.206 | no | | |
| BANE | 5.178 | 0.0016 | 1.92 | 5.206 | no | MRKC | 3.148 | 0.0253 | 2.00 | 5.206 | no | SELG | 1.444 | 0.2296 | 1.25 | 5.206 | no | | |
| BLNG | 0.184 | 0.9073 | 1.27 | 5.206 | no | MRKK | 1.031 | 0.3793 | 0.93 | 5.206 | no | SFIN | 4.474 | 0.0042 | 0.20 | 5.206 | no | | |
| BSPB | 1.593 | 0.1905 | 1.12 | 5.206 | no | MRKP | 1.817 | 0.1434 | 1.24 | 5.206 | no | SIBN | 3.955 | 0.0085 | 2.36 | 5.206 | no | | |
| CBOM | 4.687 | 0.0031 | 0.43 | 5.206 | no | MRKS | 0.800 | 0.4944 | 1.64 | 5.206 | no | SNGS | 1.540 | 0.2036 | 5.04 | 5.206 | no | | |
| CHMF | 3.031 | 0.0293 | 1.05 | 5.206 | no | MRKU | 7.196 | 0.0001 | 1.08 | 5.206 | **yes** | TATN | 0.964 | 0.4095 | 3.78 | 5.206 | no | | |
| CHMK | 1.781 | 0.1502 | 1.44 | 5.206 | no | MSNG | 6.001 | 0.0005 | 0.90 | 5.206 | **yes** | TGKA | 2.536 | 0.0564 | 1.42 | 5.206 | no | | |
| FEES | 2.657 | 0.0481 | 3.15 | 5.206 | no | MSRS | 1.326 | 0.2655 | 1.95 | 5.206 | no | TRMK | 0.267 | 0.8493 | 2.22 | 5.206 | no | | |
| FESH | 0.899 | 0.4419 | 1.48 | 5.206 | no | MSTT | 7.135 | 0.0001 | 0.21 | 5.206 | **yes** | TTLK | 0.690 | 0.5585 | 0.83 | 5.206 | no | | |
| GAZP | 1.433 | 0.2326 | 1.24 | 5.206 | no | MTSS | 3.102 | 0.0266 | 1.27 | 5.206 | no | UKUZ | 0.716 | 0.5430 | 1.81 | 5.206 | no | | |
| GCHE | 0.348 | 0.7907 | 2.92 | 5.206 | no | MVID | 1.506 | 0.2124 | 2.14 | 5.206 | no | UNAC | 2.798 | 0.0399 | 1.07 | 5.206 | no | | |
| GMKN | 0.168 | 0.9180 | 2.55 | 5.206 | no | NKNC | 0.506 | 0.6785 | 1.80 | 5.206 | no | UPRO | 3.813 | 0.0103 | 1.98 | 5.206 | no | | |
| HYDR | 0.782 | 0.5048 | 1.71 | 5.206 | no | NLMK | 0.847 | 0.4687 | 1.26 | 5.206 | no | USBN | 0.651 | 0.5825 | 1.57 | 5.206 | no | | |
| IRAO | 2.569 | 0.0540 | 5.20 | 5.206 | no | NMTP | 1.793 | 0.1479 | 1.35 | 5.206 | no | UTAR | 2.176 | 0.0903 | 1.04 | 5.206 | no | | |
| IRKT | 4.904 | 0.0023 | 0.80 | 5.206 | no | NVTK | 2.307 | 0.0762 | 2.72 | 5.206 | no | VJGZ | 0.479 | 0.6974 | 2.15 | 5.206 | no | | |
| JNOS | 1.352 | 0.2570 | 1.26 | 5.206 | no | OGKB | 0.914 | 0.4342 | 1.99 | 5.206 | no | VSMO | 1.212 | 0.3050 | 1.05 | 5.206 | no | | |
| KAZT | 3.558 | 0.0145 | 3.45 | 5.206 | no | PHOR | 1.452 | 0.2271 | 1.65 | 5.206 | no | VTBR | 3.533 | 0.0149 | 4.53 | 5.206 | no | | |
| KMAZ | 0.638 | 0.5907 | 1.54 | 5.206 | no | PIKK | 1.550 | 0.2010 | 2.96 | 5.206 | no | YAKG | 4.379 | 0.0048 | 3.70 | 5.206 | no | | |
| KZOS | 0.468 | 0.7046 | 1.96 | 5.206 | no | PLZL | 0.598 | 0.6167 | 0.57 | 5.206 | no | YNDX | 7.716 | 0.0001 | 3.47 | 5.206 | **yes** | | |
| LKOH | 0.721 | 0.5396 | 2.83 | 5.206 | no | RASP | 0.205 | 0.8931 | 4.88 | 5.206 | no | | | | | | | | |

**Reading.** Five companies keep a break at 5% after the heteroscedasticity correction. They belong to four 
different sector labels in the panel — MFGS (Energy), MRKU and MSNG (Utilities), MSTT (Real Estate), 
YNDX (Tech) — so there is no single-sector story to tell about them. Five survivors out of 74 is 
*exactly* the order of magnitude the 5% nominal rate would produce by chance (74 × 0.05 ≈ 3.7), and
because the published bounds are applied to an off-grid (T1,T2), H3 is **not** established by M5; the
honest statement is 'one-sided evidence, consistent with chance, for 5 of 74 companies'.

### 7.6 M6 — permutation falsification and power (H1/H2 robustness)

1,000 shuffles of each company's ASVI series, M1 re-fitted each time, p = (#{|β_shuffled| ≥ |β_obs|} + 1)/1001
for the shuffle the blueprint mandates (iid permutation), plus a circular-shift permutation as the valid
supplement under serial dependence. Power from the non-central t with ncp = |β1|/SE_HAC; MDE at 80% power.

*companies: **73** = 75 − ROLO − MRKK (same exclusions as M1). iid permutation p < 0.05:
**54** companies. Circular-shift p < 0.05: 73 of 73 (every company) — which is the
expected outcome when the regressor enters contemporaneously and RV is strongly autocorrelated, so the
circular column is reported as a diagnostic of the falsification test, not as evidence.*

| tk | β1 | SE | t | p_iid | p_circ | power | MDE80 | n | | tk | β1 | SE | t | p_iid | p_circ | power | MDE80 | n |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | --- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| ABRD | +0.0233 | 0.0172 | +1.36 | 0.019 | 0.001 | 0.273 | 0.0483 | 401 | MSTT | -0.1096 | 0.0671 | -1.63 | 0.001 | 0.001 | 0.371 | 0.1884 | 397 | |
| AFKS | -0.0434 | 0.0473 | -0.92 | 0.001 | 0.001 | 0.150 | 0.1329 | 402 | MTSS | +0.1410 | 0.0503 | +2.80 | 0.001 | 0.001 | 0.799 | 0.1412 | 402 | |
| AFLT | -0.0155 | 0.0359 | -0.43 | 0.065 | 0.001 | 0.071 | 0.1009 | 402 | MVID | -0.0192 | 0.0365 | -0.53 | 0.166 | 0.001 | 0.082 | 0.1027 | 401 | |
| AKRN | -0.0194 | 0.0212 | -0.92 | 0.001 | 0.001 | 0.149 | 0.0596 | 401 | NKNC | +0.0015 | 0.0300 | +0.05 | 0.864 | 0.001 | 0.050 | 0.0842 | 401 | |
| ALRS | -0.0483 | 0.0571 | -0.85 | 0.001 | 0.001 | 0.135 | 0.1604 | 402 | NLMK | -0.0532 | 0.0540 | -0.98 | 0.001 | 0.001 | 0.166 | 0.1517 | 402 | |
| AVAN | +0.1280 | 0.0204 | +6.26 | 0.001 | 0.001 | 1.000 | 0.0574 | 390 | NMTP | +0.0178 | 0.0465 | +0.38 | 0.069 | 0.001 | 0.067 | 0.1306 | 401 | |
| BANE | +0.0196 | 0.0369 | +0.53 | 0.131 | 0.001 | 0.083 | 0.1037 | 401 | NVTK | -0.0403 | 0.0372 | -1.08 | 0.001 | 0.001 | 0.191 | 0.1046 | 402 | |
| BLNG | +0.0167 | 0.0565 | +0.30 | 0.107 | 0.001 | 0.060 | 0.1586 | 401 | OGKB | -0.0298 | 0.0760 | -0.39 | 0.002 | 0.001 | 0.068 | 0.2135 | 401 | |
| BSPB | +0.0320 | 0.0369 | +0.87 | 0.011 | 0.001 | 0.139 | 0.1037 | 401 | PHOR | -0.0484 | 0.0352 | -1.37 | 0.001 | 0.001 | 0.278 | 0.0989 | 402 | |
| CBOM | +0.0608 | 0.0332 | +1.83 | 0.001 | 0.001 | 0.448 | 0.0932 | 402 | PIKK | -0.0587 | 0.0328 | -1.79 | 0.001 | 0.001 | 0.431 | 0.0921 | 402 | |
| CHMF | -0.0073 | 0.0660 | -0.11 | 0.393 | 0.001 | 0.051 | 0.1853 | 402 | PLZL | -0.0053 | 0.0263 | -0.20 | 0.607 | 0.001 | 0.055 | 0.0738 | 402 | |
| CHMK | -0.0107 | 0.0325 | -0.33 | 0.412 | 0.001 | 0.062 | 0.0913 | 401 | RASP | +0.0358 | 0.0874 | +0.41 | 0.034 | 0.001 | 0.069 | 0.2454 | 401 | |
| FEES | -0.0402 | 0.0767 | -0.53 | 0.023 | 0.001 | 0.082 | 0.2154 | 399 | RGSS | +0.0978 | 0.0868 | +1.13 | 0.001 | 0.001 | 0.202 | 0.2439 | 401 | |
| FESH | +0.0288 | 0.0422 | +0.68 | 0.002 | 0.001 | 0.105 | 0.1185 | 401 | RNFT | +0.0063 | 0.0169 | +0.38 | 0.405 | 0.001 | 0.066 | 0.0476 | 401 | |
| GAZP | -0.1063 | 0.0970 | -1.10 | 0.001 | 0.001 | 0.194 | 0.2726 | 403 | ROSN | -0.0292 | 0.0813 | -0.36 | 0.015 | 0.001 | 0.065 | 0.2285 | 402 | |
| GCHE | +0.0262 | 0.0248 | +1.06 | 0.001 | 0.001 | 0.184 | 0.0698 | 401 | RTKM | -0.0774 | 0.0451 | -1.71 | 0.001 | 0.001 | 0.402 | 0.1268 | 402 | |
| GMKN | -0.0517 | 0.0660 | -0.78 | 0.001 | 0.001 | 0.122 | 0.1853 | 402 | RUAL | -0.0206 | 0.0578 | -0.36 | 0.004 | 0.001 | 0.065 | 0.1623 | 402 | |
| HYDR | +0.0346 | 0.0523 | +0.66 | 0.001 | 0.001 | 0.101 | 0.1469 | 402 | SBER | -0.3237 | 0.3050 | -1.06 | 0.001 | 0.001 | 0.185 | 0.8567 | 403 | |
| IRAO | -0.0023 | 0.0788 | -0.03 | 0.828 | 0.001 | 0.050 | 0.2213 | 402 | SELG | -0.0066 | 0.0277 | -0.24 | 0.375 | 0.001 | 0.056 | 0.0779 | 401 | |
| IRKT | +0.0964 | 0.0594 | +1.62 | 0.001 | 0.001 | 0.367 | 0.1668 | 401 | SFIN | -0.0294 | 0.0270 | -1.09 | 0.001 | 0.001 | 0.193 | 0.0757 | 385 | |
| JNOS | -0.0318 | 0.0363 | -0.88 | 0.002 | 0.001 | 0.141 | 0.1020 | 401 | SIBN | +0.0828 | 0.0436 | +1.90 | 0.001 | 0.001 | 0.474 | 0.1224 | 401 | |
| KAZT | +0.1158 | 0.0421 | +2.75 | 0.001 | 0.001 | 0.783 | 0.1183 | 399 | SNGS | -0.0132 | 0.0474 | -0.28 | 0.260 | 0.001 | 0.059 | 0.1333 | 402 | |
| KMAZ | +0.0134 | 0.0283 | +0.47 | 0.178 | 0.001 | 0.076 | 0.0795 | 401 | TATN | +0.0376 | 0.0538 | +0.70 | 0.002 | 0.001 | 0.107 | 0.1512 | 402 | |
| KZOS | -0.0106 | 0.0237 | -0.45 | 0.246 | 0.001 | 0.073 | 0.0666 | 401 | TGKA | +0.0175 | 0.0650 | +0.27 | 0.024 | 0.001 | 0.058 | 0.1827 | 401 | |
| LKOH | +0.0722 | 0.0564 | +1.28 | 0.001 | 0.001 | 0.248 | 0.1585 | 402 | TRMK | +0.0642 | 0.0459 | +1.40 | 0.001 | 0.001 | 0.287 | 0.1289 | 401 | |
| LSNG | -0.1320 | 0.0556 | -2.37 | 0.001 | 0.003 | 0.658 | 0.1561 | 392 | TTLK | -0.0513 | 0.0589 | -0.87 | 0.001 | 0.001 | 0.140 | 0.1655 | 401 | |
| LSRG | -0.0291 | 0.0330 | -0.88 | 0.001 | 0.001 | 0.142 | 0.0928 | 401 | UKUZ | +0.0199 | 0.0200 | +1.00 | 0.030 | 0.001 | 0.169 | 0.0561 | 383 | |
| MAGN | -0.1135 | 0.0760 | -1.49 | 0.001 | 0.001 | 0.319 | 0.2135 | 402 | UNAC | -0.0267 | 0.0380 | -0.70 | 0.009 | 0.001 | 0.108 | 0.1068 | 401 | |
| MFGS | +0.0471 | 0.0273 | +1.73 | 0.001 | 0.001 | 0.406 | 0.0767 | 399 | UPRO | -0.0841 | 0.0490 | -1.72 | 0.001 | 0.001 | 0.403 | 0.1376 | 401 | |
| MGNT | +0.0642 | 0.0264 | +2.44 | 0.001 | 0.001 | 0.680 | 0.0741 | 402 | USBN | +0.0366 | 0.0616 | +0.59 | 0.003 | 0.001 | 0.091 | 0.1731 | 401 | |
| MGTS | +0.0276 | 0.0205 | +1.35 | 0.246 | 0.001 | 0.270 | 0.0575 | 401 | UTAR | -0.0581 | 0.0369 | -1.57 | 0.001 | 0.001 | 0.349 | 0.1036 | 401 | |
| MRKC | -0.0932 | 0.0746 | -1.25 | 0.001 | 0.001 | 0.239 | 0.2095 | 334 | VJGZ | +0.0564 | 0.0382 | +1.48 | 0.001 | 0.001 | 0.313 | 0.1074 | 397 | |
| MRKP | +0.0449 | 0.0600 | +0.75 | 0.001 | 0.001 | 0.116 | 0.1685 | 401 | VSMO | -0.0157 | 0.0237 | -0.66 | 0.081 | 0.001 | 0.101 | 0.0665 | 401 | |
| MRKS | +0.0178 | 0.0629 | +0.28 | 0.110 | 0.001 | 0.059 | 0.1767 | 367 | VTBR | +0.0422 | 0.0228 | +1.85 | 0.013 | 0.001 | 0.453 | 0.0642 | 404 | |
| MRKU | -0.0477 | 0.0620 | -0.77 | 0.001 | 0.001 | 0.120 | 0.1742 | 374 | YAKG | +0.1201 | 0.0365 | +3.29 | 0.001 | 0.001 | 0.906 | 0.1026 | 401 | |
| MSNG | -0.0058 | 0.0416 | -0.14 | 0.415 | 0.001 | 0.052 | 0.1167 | 401 | YNDX | -0.0363 | 0.0720 | -0.50 | 0.008 | 0.001 | 0.079 | 0.2023 | 398 | |
| MSRS | -0.0164 | 0.0416 | -0.39 | 0.006 | 0.001 | 0.068 | 0.1168 | 401 | | | | | | | | | | |

**Power gate, applied literally.** 71 of 73 companies have power < 0.80 to detect their own
point estimate, so for those companies the M1/M2 nulls are reported as *inconclusive due to insufficient
power*, never as 'no effect'. Only AVAN (0.996) and YAKG (0.906) clear 0.80; median MDE80 = 0.1224
(range 0.0476 – 0.8567), i.e. the design can only reliably see effects 28× the size of the median M1
coefficient (|−0.0043|).

---

---

## 8. The six named sensitivity checks, and what each one produced

The blueprint (§6) names exactly six checks and forbids substitution. Their status, one by one, with the
numbers that were actually produced:

| # | Check (blueprint wording) | Status | Result |
| -- | -- | -- | -- |
| S1 | drop the 5 revision-affected News events; confirm the explained-spike share moves 62.2% → 61.7% | **BLOCKED (B1)** | cannot be executed: the News matrix that the 5 events live in is absent from the repository, so there is nothing to drop. See §8.1 |
| S2 | widen News windows to t…t+5 for persistence-flagged events | **BLOCKED (B1)** | cannot be executed: no event dates and no persistence flags exist in the archive; the widening operation has no input. See §8.1 |
| S3 | re-run with strict Crisis/Sanction definitions; confirm the residual unexplained share moves toward 49.2% | **PARTIAL — estimation side run, coverage side blocked** | the strict-coding *regressions* were run (§8.2). The 50.8% → 49.2% *spike-coverage* comparison is a [DOC] figure that this workspace cannot recompute (its per-company spike enumeration belongs to the News build) |
| S4 | re-run H5 excluding the 11 robustness-check-candidate companies | **RUN** | interaction `ASVI×Div` = -0.019343 (SE 0.022116, t -0.875, p 0.382) on n = 24,967 / 63 companies, vs primary -0.020374 (p 0.368); conclusion unchanged (§8.3) |
| S5 | re-run H6 with the federal-only SOE classification | **RUN** | `ASVI×SOE` falls from +0.005917 (p 0.0314) to +0.004779 (p 0.102) — H6 is **not** robust to the classification (§8.4) |
| S6 | flip-test VSMO's SOE classification | **RUN** | VSMO = 0: +0.006143 (p 0.027); VSMO = 1: +0.005917 (p 0.031). The two differ by 0.000226 = 8.2% of one SE — VSMO is knife-edge exactly as the source says (§8.4) |

### 8.1 S1 / S2 — the blocker, stated in full (nothing substituted)

* **What is missing**: `g4_events_final.csv`, i.e. the 419 × 75 `News(i,t)` matrix and its long/event
  companions, named as deliverables in `news.md`; and with them the 5 revision-affected events, the
  persistence flags and the reaction-window grid.
* **What was tried**: (a) the live GitHub tree of `aaa11996/yandex.data.` re-fetched on 2026-09-20 — 12 blobs,
  of which 4 are the report `.md` files and none is a News CSV; (b) a complete listing of the extracted
  `iqbal thesis v2.zip` (226 files, all under `Data/<Sector>/<TICKER>/`) — no event file; (c) every dated
  company-event mentioned in `news.md`, `Sanctions Events…md` §5.2/§10 and `News_Div_Overlap_Addendum.md`
  enumerated by hand: 50 dated event rows across 29 companies, only 2 of which are dated events of the type
  the News layer needs, and 11 of the 31 event mentions had to be discarded (10 as Sanction-coded, 1 as a
  Crisis window week: PHOR 2026-07-22).
* **What exists instead**: the counts only — 31 events, 23 companies, 89 company-weeks
  (89 / 31,425 = 0.283% of cells) — recorded in File 3 §3.
* **Why a substitute was rejected**: a 419 × 75 matrix built from 2 usable dates would place ones in weeks
  nobody documented; every estimate that uses the control would inherit that placement error, which is
  strictly worse than omitting the control, because it also fakes the overlap zero the blueprint mandates.
* **Consequence for S1/S2**: not a number, an absence. The explained/unexplained shares quoted in File 3 §3.6
  (368/592 = 62.2% explained padded; 301/592 = 50.8% strict) are the reports' own [DOC] findings — the 592-row
  spike enumeration is part of the missing build, so neither the 61.7% nor the 49.2% target can be reproduced
  here. **Every News-dependent statement in this study is labelled PROVISIONAL.**
* **What the omission cannot do**: change any control that *is* built. The News layer is documented disjoint
  from Crisis, Sanction and Div, so dropping it leaves 31,061 price rows, 1,725 Crisis cells and 3,231
  Sanction cells untouched; its worst-case reach is the 0.283% of cells it could have flipped.

### 8.2 S3 (estimation side) — strict Crisis/Sanction codings

Three strict variants were run on M1 (per-company, HAC), all named and all reproducible from the shipped scripts:

| variant | what changes | median β1 | vs primary | sig. count |
| -- | -- | -- | -- | -- |
| primary | permanent-step Sanction coding, 4 confirmed Crisis windows, 179 Crisis∩Sanction cells present | -0.004313 | — | 11 of 73 |
| crisis-masked | weeks inside a Crisis window removed from the Sanction regressor (overlap → 0 by construction) | -0.004561 | 2.6% of the mean SE | 11 |
| drop the 179 overlap cells | those (company, week) cells leave the sample entirely | -0.004581 | -0.000268 | — |
| partial-Div added | Div_l = 104 real cells (4.9% of the intended 2,110) | -0.004125 | mean | Δ |

No variant moves the headline: the median attention coefficient stays within ±0.0002 of −0.0043, i.e. inside
one twentieth of a typical SE (0.0097), and the significance count stays 11 (M1) / 6 (M2). The
**coverage** half of S3 stays blocked, as stated in the table above.

### 8.3 S4 — H5 without the 11 flagged companies

The 11 companies are the exact output of the blueprint's own reconstruction rule (Div∩Crisis ∪ Div∩Sanction
week counts above the stated threshold): AFLT, AKRN, AVAN, GCHE, LSRG, NVTK, RTKM, SBER, TATN, USBN, VTBR
(File 3 §4.3).

| fit | β1(ASVI) | ASVI×Div | n | companies |
| -- | -- | -- | -- | -- |
| primary H5 (partial Div) | +0.001764 | -0.020374 (p 0.368) | 29,376 | 74 |
| S4 (drop the 11) | +0.001638 | -0.019343 (p 0.382) | 24,967 | 63 |

Dropping the 11 costs 4,409 cells and 11 companies and moves the interaction by 0.0011 (5% of its SE).
Neither fit is significant, so S4 confirms the H5 null rather than perturbing it — while H5 as a whole stays
**PROVISIONAL** because the Div regressor is the partial build (B2).

### 8.4 S5 / S6 — H6 under alternative SOE classifications

| specification of the SOE variable | ASVI×SOE | SE | t | p | verdict |
| -- | -- | -- | -- | -- | -- |
| primary: federal + regional state-owned (35 of 75) | +0.005917 | 0.002750 | +2.152 | 0.0314 | significant |
| S5: federal-only (31 of 75) | +0.004779 | 0.002925 | +1.634 | 0.1023 | not significant |
| S6: VSMO flipped to 0 | +0.006143 | 0.002771 | +2.217 | 0.0267 | significant |
| S6: VSMO kept at 1 | +0.005917 | 0.002750 | +2.152 | 0.0314 | significant |
| + sector×week demeaning (hardest test) | +0.004172 | — | +1.573 | 0.116 | not significant |
| + sector×week demeaning, federal-only | +0.002600 | — | +0.949 | 0.343 | not significant |

H6 is the only headline that is significant in the primary pooled fit and it is the least stable of the four
hypotheses. Dropping the 4 regional-only names (federal-only classification) attenuates it from p = 0.031 to
p = 0.102; adding sector×week demeaning attenuates it to p = 0.116; the combination erases it (p = 0.343). The
VSMO knife-edge moves the coefficient by 0.000226 = 8.2% of one SE, so that single company is harmless;
the 4 regional-only names are what the significance rests on.
The manual therefore records H6 as **sensitive to the SOE definition**, not as an established effect.

### 8.5 Two extra checks the deviation register demanded (beyond the named six)

| check | result |
| -- | -- |
| M5 under the other published GL rows (the D4/D15 sensitivity) | survivors = 18 with the off-diagonal T1=40,T2=10 bound Cu=3.372, 5 with the conservative diagonal Cu=5.206, 0 with the widest tabulated bound Cu=18.127 (T1=T2=10). The M5 count is therefore an artefact of which published row one reads, and is reported with that choice named |
| D1 consequence on the pooled model, measured on a matched pair | the same pooled model with `Sanction_l` added, primary coding vs crisis-masked coding: β_ASVI +0.001709 → +0.001754 (t 1.14 → 1.17); β_Sanction +0.001067 (p 0.425) → +0.009491 (p 0.018). The sanction coefficient only becomes significant once the crisis overlap is masked — which is exactly why D1 is a disclosed deviation and not a rounding issue |

---

## 9. Deviation register (File 2 side; D1–D12 are in File 3 §8 and are not repeated as new items)

| # | Deviation from the blueprint | What was required | What was done | Quantified consequence | Affected results |
| -- | -- | -- | -- | -- | -- |
| D13 | the `Vol.` column of the price files is printed in mixed units (3.26B, 304.85M, 22.09) | raw weekly volume in shares | volumes were reconstructed to absolute share counts per file before logging, so within-company lnV changes are right but levels are not cross-sectionally comparable | in M1/M2/M3/M5/M6 lnV is absorbed by the company intercept → no effect; in pooled M4 β_lnv = +0.003479 is unit-dependent and carries a caveat | M4 (lnV coefficient only) |
| D14 | the M5 table exported in an earlier round nested the models without the spec's `crisis_chow` term | restricted model = 4-parameter post-crisis specification on the whole sample | re-run exactly as SUPPLEMENT §B prescribes; F median 1.61, 24 nominal-significant, 5 GL-survivors (the old export produced degenerate F≈0 and is superseded and retained only as a trail) | M5 H3 evidence changes from 'none' to '5 survivors out of 74' | M5 |
| D15 | the Giles–Lieberman PDF text layer glues Cu values to the next row's T1 | a machine-readable tabulation of the bounds | a first automatic parse mis-assigned the (θ>100) and (0.1,10] columns; the automated parse was **rejected** and the k=4 diagonal rows were hand-transcribed and re-read against the raw text; `gl_bounds_published.csv` records the method per row | Cu(0.1,10] = 5.206 (used) vs the mis-parsed 3.372; survivor count 5 vs 18 → the choice of published row is disclosed and counted (D4) | M5 |
| D16 | M5's `crisis_chow` acute window is not dated in the SUPPLEMENT | the 'acute window' dummy | taken as the 5 grid weeks 2022-02-28 … 2022-03-28 = W2 truncated at the break date, stated here rather than left implicit | with W2 untruncated the pre-regime loses 5 weeks and 18 nominal-significant companies become 21 (order unchanged) | M5 |
| D17 | News-dependent sensitivity checks S1/S2 | run them | blocked by B1; no substitute generated | see §8.1 | S1, S2, H3 |
| D18 | the 528/58 pre-panel Div split and the 0.347/0.32 stress fraction do not reconcile | reproduce from the rule | recomputed and reported as a documented delta (File 3 §4.6, D7, D9) | no estimate depends on them | none |

**PROVISIONAL labels currently attached**: H3 (M5) — pending the q-definition and the GL row choice, D14/D15/D16;
H5 — pending the 587-row dividend table, D3/B2; all News-dependent statements — D2/B1; the Crisis∩Sanction
zero-overlap mandate — D1 (179 cells are measured and disclosed, and both codings are estimated).

---

## 10. Reproduction inventory

Every artefact below is in the workspace and each was run against the unmodified raw archive; the scripts are
the whole of the pipeline, in order:

| script | what it does | output |
| -- | -- | -- |
| `panel.py` | read-only parse of the 226 raw CSVs (Wordstat + price) into per-company weekly series | `panel.pkl / panel.npy` |
| `verify1.py` | row counts, kept/dropped weeks, crisis-window re-derivation (4 windows, doc-exact) | `verify1.out` |
| `verify2.py` | Div counts, 88 overlap rows, the 11-company flag rule | `(stdout)` |
| `verify3.py` | overlap measurements Crisis/Sanction/News/Div with worked examples | `verify3.out` |
| `verify4.py` | 419 × 75 stress matrices, Crisis∩Sanction = 179 | `verify4.out / stress.npy` |
| `build_frame.py` | the estimation frame: ASVI, RV, R, lnV, the 4 controls, lags, ROLO flags | `frames.pkl` |
| `methods.py` | M1–M6 + H4/H5/H6 with HAC(4), BH/Holm, Chow + GL bounds, permutation + power | `export/*` |
| `m4.py / m4b.py` | pooled two-way FE variants (sector, sector×time, sector×week) | `export/M4_results.json` |
| `m5_exact.py` | M5 exactly as SUPPLEMENT §B nests it (k_pre 3, k_post 4, k_pool 4, q 3) | `export/M5_exact.json → M5_chow_gl.csv` |
| `gl_parse.py` | Giles–Lieberman table extraction attempt + the text-layer damage report | `gl_bounds_published.csv (hand-transcribed rows)` |
| `alt_sanction.py` | crisis-masked (overlap-zero) sanction coding, M1/M2/M4 refits | `export/M1M2_alt.json, M4_alt.json` |
| `m6b.py` | circular-shift permutation supplement to the mandated iid permutation | `export/M6_circular.json` |
| `spec_variants.py` | the four blueprint-exact variants (M4 spec, partial Div, overlap-drop, Chow reduced form) | `export/spec_variants.json` |
| `divquant.py` | measured effect of the partial Div column on M1 (and the M4 counterpart) | `export/divquant_m1.json` |
| `trace.py / trace2.py / trace3.out` | the worked-example traces used in §3 and Part 2, printed from the raw files | `trace.out, trace2.out, trace3.out` |
| `f3_a…e.py, f3_final.py; f2_a…d.py, f2_final.py` | the two deliverables' generators | `final/UNIFIED_DATA_COMPILATION.md, final/UNIFIED_METHODOLOGY_MANUAL.md` |

Reproduction is deterministic: `frames.pkl` is the single intermediate, every estimator reads it, and the
regressions are plain `numpy` least squares with the HAC sandwich written in the same file, so no package
version can change a coefficient. The raw archive is never written to; all file access is read-only.

---

# Part 2 — what this study actually did, in plain language

Everything in Part 2 uses only numbers printed in Part 1 or in the trace files, and each one names its source
file. Nothing here is an illustration.

## 11. The question, in one paragraph

When ordinary Russians start googling a company's name, does the stock get jumpier a week later? That is the
whole study. 'Jumpier' is measured as **realised volatility** in a single week, `RV = (High − Low) / Close`,
from the price bar. 'Googling' is measured as **Yandex Wordstat search volume**, and because search volumes
differ enormously between Gazprom and a third-tier utility, each company is compared only with its own recent
past: `ASVI = ln(searches) − median(ln(searches) over the last 8 weeks)`. A company-level regression then asks
whether a high ASVI *last* week predicts a wide bar *this* week, after stripping out the things that also make
a stock jumpy: its own recent volatility, its traded volume, whether the whole market was in a crisis week,
whether that specific company had been sanctioned, and whether its dividend record date was approaching.

## 12. A number you can check with your own eyes

Take Sberbank, 21 February 2022 — the Monday before the invasion.

1. `Data/Banking/SBER/Сбер акции.csv` has the line `21.02.2022;83 504;…` — 83,504 searches for 'Сбер акции'
   ('Sber shares'). `Data/Banking/SBER/SBER.csv` has `21.02.2022;168 760;…` — 168,760 for the ticker 'SBER'.
   Summed raw: **252,264**.
2. `ln(252 264) = 12.438231`. The median of the eight previous weeks' `ln(SVI)` is **10.689024**.
3. `ASVI = 12.438231 − 10.689024 = **+1.749207**` — that is roughly 5.7× Sberbank's own normal level of search
   interest. 1.749 is Sberbank's own maximum ASVI in the whole 411-week panel (rank 1 of 411; 105 of the
   panel's 30,532 company-weeks read higher than it).
4. The price bar labelled with the previous Sunday, 20 February, reads
   `"02/20/2022","131.12","249.15","258.32","89.59","3.26B","-47.61%"`. So the week's
   `RV = (258.32 − 89.59) / 131.12 = **1.286836**`: the high was 129% above the low relative to the opening
   price of that week. (Note the file's own `Change %` says −47.61%; the study never uses that column —
   Part 1 §3.3 shows exactly what it is and why the log return of the *previous* close is used instead.)
5. So the pair that enters the regression for Sberbank in that week is (ASVI last week = +1.749,
   RV this week = 1.287). The estimate of interest is the slope across 73 such company-specific series.

The same trace for Yandex on the same Monday gives 93,332 + 50,647 = **143,979** searches, ASVI **+1.318246** —
Yandex's all-time panel maximum, and the reason its two channels are summed the way Part 1 §3.1 spells out.
Two contrasts, from the same trace files, to show what the measure does to ordinary weeks: on 22 June 2026 the
smallest name in the panel, AVAN, logged 17 + 3,704 = **3,721** searches and ASVI **−0.044925** (its RV that
week was 0.150735 — a quiet week); and on 29 March 2021 GMKN logged 25,605 + 1,031 = **26,636** searches,
ASVI **+0.012575**. Attention spikes are rare by construction, which is the point of subtracting a company's
own trailing median instead of comparing companies with each other.

## 13. What the regressions said, without statistics jargon

* **Per company (M1).** Across 73 companies the slope linking last week's attention to this week's range sits at a
  median of **-0.0043**, on a scale where a typical company's own margin of error is
  **0.0097** and the median of the *absolute* estimates is **0.0078** — so the individual companies
  are not all tiny, they are split in sign and cancel: the median absolute t is 1.01, exactly what a null
  mixture looks like. Eleven companies do show a statistically visible link and two of them are positive (MFGS,
  JNOS); the rest are negative — so the *pattern* is not 'attention raises volatility', it is 'a minority of
  companies, in both directions'.
* **Returns instead of ranges (M2).** Same story with a median of -0.0034 and 6 significant
  companies. Searching does not predict which way the price moves, only — weakly — how far.
* **Does attention Granger-cause volatility? (M3).** After correcting for 400-plus tests per block, 8 of 219
  tests in the volatility block survive Benjamini–Hochberg (MFGS, RTKM, SNGS, UTAR) and none of the 222 in the
  returns block does. Three of the four names carry a negative attention coefficient (RTKM −0.033, SNGS −0.033,
  UTAR −0.007) and MFGS a positive one (+0.048); a negative lagged coefficient is not evidence that volatility
  causes attention — the lag structure cannot separate that from mean-reversion in ASVI after a spike.
* **The pooled answer (M4).** Pooling 74 companies and 29,376 company-weeks with company and week fixed effects
  gives an attention coefficient of **+0.001717** with a p-value of 0.253: statistically
  indistinguishable from zero. What does matter in that regression is the stock's own last-week range
  (0.320, t = 18.9) — volatility clusters, attention mostly does not add to it.
* **Was there a break in February 2022? (M5).** 74 reduced-form Chow tests; 24 are nominally significant, and
  after the published heteroscedasticity correction 5 survive (MFGS, MRKU, MSNG, MSTT, YNDX). Five of 74 is what
  a 5% test produces by chance (3.7), so the honest verdict is: not established.
* **Can this design even see the effect? (M6).** Only 2 of 73 companies reach 80% power. The smallest effect the
  panel can reliably detect has a median size of 0.1224 — about 28 times the median estimate. So every null
  above is reported as 'this panel could not have detected the effect', never as 'there is no effect'.
* **Dividends (H5) and state ownership (H6).** The dividend-record-date interaction is not significant
  (-0.0204, p 0.37). The state-ownership interaction is marginally significant
  in the primary pooled fit (+0.0059, p 0.031) but it fades to p 0.10 when 'state-owned'
  means federal-only, and to p 0.12 when each sector is also allowed its own week-by-week
  trend, so it is recorded as sensitive to the classification, not as an established effect.

## 14. Why the number of companies keeps changing, and why that is not sloppy

The panel has 75 companies. ROLO is left out of every test that uses RV, because its shares trade at 1-kopeck
ticks and its 'range' is mostly a rounding artefact — that is 74. MRKK has 273 usable weeks after the lags
and the missing-data rules, below the 300-week gate the blueprint sets for a per-company regression — that is
73. M6's permutation test uses the same 73. Every table in Part 1 names these two companies by ticker instead
of reporting a bare N, because the blueprint says a '73 fits' table with no names is incomplete.

## 15. What we could not do, plainly

Two things the blueprint asked for do not exist in the repository. The **News** layer (company-specific events:
earnings, management changes, defaults) has only counts in the reports, never the week-by-week matrix — so the
control is absent, and the two sensitivity checks built on it (S1, S2) were not run at all rather than faked.
The **587-row dividend date table** is likewise absent, so only 104 of the intended 2,110 dividend weeks could be
reconstructed from documented dates; everything that uses the dividend control, i.e. H5, is marked PROVISIONAL.
A third thing had to be redone rather than substituted: our first pass at the Chow test (M5) nested the models
wrongly, and when it was rebuilt to the letter of the specification the answer changed from 'nothing' to '5 of
74' — which is why the manual states the nesting in full (§7.5) instead of just quoting a result.

And one measurement rule in the blueprint cannot be satisfied by the data: it demands that the Crisis and
Sanction windows never overlap. In reality 179 company-weeks are both inside a market-wide crisis week and
after that company's own sanction date. We did not delete them to force a zero (that would hide information),
and we did not pretend the rule was met. We reported the 179, and ran the whole estimation twice: once with the
overlap, once with crisis weeks masked out of the sanction variable (the masked version yields 199 cells and
zero overlap, but cannot reproduce the 170 the source reports, so it is labelled PROVISIONAL). The two runs
differ by 0.000249 in the median coefficient — a fifth of a standard error at most.

## 16. If you only remember four sentences

1. A 75-company, 419-week panel of search-volume and price data was built entirely from the raw archive, with
   every row count reconciled (31,061 price rows read, 30,973 kept).
2. Search attention does not predict next week's volatility for the panel as a whole (pooled p = 0.25),
   and only a minority of individual companies show any link, in both directions.
3. The design is under-powered by roughly a factor of 28 relative to the effect sizes it is being asked to
   detect, so 'nothing found' is explicitly *not* 'nothing there'.
4. Two of the required controls could not be built from the repository, and one method had to be re-run after a
   specification error; every consequence is quantified above rather than smoothed over.
