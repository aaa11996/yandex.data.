---

# FINAL COMPLETE METHODOLOGY — 75-Company MOEX ASVI Panel

## Version 2.0 — Corrected Freeze (supersedes v1.0 and the ULTIMATE\_METHODOLOGY\_BLUEPRINT)

This document is the single authoritative methodology. Where anything elsewhere conflicts with this document, this document governs. All corrections C1–C8 from the review round are incorporated at their governing clauses; the Amendment Register (§9) records each change and its section.

**Standing rule:** if any step below cannot be executed exactly as written, stop and report the specific blocker. Never substitute, approximate, or silently reconstruct missing data and present the result as equivalent to the original.

## 1\. Study Window, Company Universe, and Frozen Sector Mapping

**Window:** 2018-08-27 through 2026-09-06 (raw price range). Wordstat grid: Mondays 2018-08-27 → 2026-08-31, 419 weeks. Never truncate.

**Universe:** 75 companies (full list in UNIFIED\_METHODOLOGY\_MANUAL §2.2). Every result table must reconcile its exact N against 75 by name — a table using fewer than 75 must list which specific companies are absent and why (insufficient observations, ROLO's exclusion, a data gap), never just a smaller count with no explanation.

**Frozen sector mapping (E3, per C2):** before any sector analysis (H4, Section 5), publish the frozen 75-row mapping (ticker → sector), taken from the raw archive folder names `Data/<Sector>/<Ticker>` and reconciled against UNIFIED\_METHODOLOGY\_MANUAL §2.2. Any ticker on which folder and table disagree is resolved by the folder (the raw archive is the universe authority) and the disagreement is logged. Sector-wise below-threshold lists are **computed from this frozen mapping at execution time**; hard-coded sector names are prohibited, because prior rounds produced inconsistent categorizations (e.g., IRKT/UNAC placed in Tech in one pass and Industrial in another).

## 2\. Core Variable Construction

**SVI/ASVI.** 74 non-Yandex companies: `SVI(t) = ticker_count(t) + cyrillic_count(t)` (raw counts summed before any log transform). Yandex: `SVI(t) = YNDX_count(t) + cyrillic_count(t)` — YDEX is never used, by final decision; documented limitation: this excludes roughly two years of real YDEX search volume post-2024. `ASVI(t) = ln(SVI(t)) − median(ln(SVI(t−1)), …, ln(SVI(t−8)))`. `SVI = 0` → missing (never a small constant); requires 8 valid prior weeks, else ASVI missing.

**Price variables (C4 corrected).** `RV(i,t) = ln[High(i,t) / Low(i,t)]` — the range measure specified in the study prospectus (cf. Alizadeh, Brandt & Diebold, 2002). A week whose high equals its low carries no range information and is **missing, not zero**. The `(High−Low)/Close` variant that appeared in an interim draft is **reverted**; it survives only as robustness r3 (§4.7). `R(i,t) = ln(Price(i,t)/Price(i,t−1))`. `V(i,t)` \= raw weekly volume; regressions use `ln(V(i,t))`.

**Date join:** Wordstat Monday date \= price file's Sunday-labeled date \+ 1 day (a price bar's Sunday label is the day BEFORE the Mon–Fri week it reports — verified directly from raw data).

**ROLO exclusion (C3 corrected, extended as E1).** ROLO is excluded from **every inferential specification in which an RV term appears as dependent variable or regressor**: M1, M2, M3 (both hypothesis blocks), M4 (all variants), M5, M6 (which re-fits M1), and the pooled analyses of Sections 5 and 6\. Its 1-kopeck price grid makes RV mechanically tick-bound, and M2 carries `RV(t−1)` as a regressor, so the quantized series contaminates M2 identically to M1; the same logic reaches M3, whose restricted and unrestricted models carry RV terms. ROLO appears only in descriptive tables and the data-quality registry. This replaces both the earlier enumeration ("M1, M4, M5") and the earlier permission to retain ROLO in M2.

**Mandatory data-quality handling:** exclude week 2024-06-16 from cross-sectional event detection; exclude week 2024-08-25 from volume-based metrics; drop the twelve 2022-02-27 flat placeholder rows (missing, never forward-filled); all other documented company-specific gaps (LSNG, MSTT, FEES, the YNDX relisting gap, AVAN's early gaps) remain missing, never filled.

## 3\. The Four Event/Structural Controls

Build in this exact order — later controls depend on earlier ones for deduplication.

**(a) Crisis(t)** — market-wide, time-varying, identical across all companies in a given week. A company "spikes" in week t if `RV_ratio(t) ≥ 2` AND `Vol_ratio(t) ≥ 2` relative to its own trailing 52-week median. A week is a "stress week" if ≥25% of eligible companies (≥30 trailing observations) spike. A window is confirmed if contiguous stress weeks (one-week bridging allowed) reach a peak spike fraction ≥40%. Four confirmed windows: W1 (COVID onset), W2 (invasion/MOEX suspension), W3 (September 2023 ruble/rate), W4 (2026 bear capitulation) — exact dates as documented in the source reports.

**(b) Sanction(i,t)** — company-specific, PERMANENT step (0 before, 1 from the confirmed first-designation date onward, forever). 23 primary anchors from OFAC/OFSI/EU primary lists, verified via the 2×-trailing-median RV/Volume test over the 4 weeks following the anchor. Only 7 of 23 anchors show a confirmed reaction — retained as a documented finding, not something to fix.

**(c) News(i,t)** — company-specific, TRANSIENT, data-driven duration (on at event week, stays on while RV or Volume ≥1.5× trailing median, cap 4 weeks). Category bar: discrete exceptional events only. Mandatory deduplication before testing: discard any candidate within ±2 weeks of a confirmed Crisis window or that company's confirmed Sanction date; this guarantees `News ∩ Crisis = 0` and `News ∩ Sanction = 0` by construction — verify exactly 0, not approximately 0\. **Role statement (C7):** News enters **no headline equation** (M1–M6, Sections 5–6). Its functions are: (i) spike-attribution accounting (explained/unexplained shares of the largest RV spikes); (ii) input to sensitivity checks 1–2; (iii) the deduplication guarantee that protects Crisis and Sanction from double-counting firm events. It enters estimation only through robustness r2 (§4.7).

**(d) Div(i,t)** — built INDEPENDENTLY, no deduplication against Crisis/Sanction/News. Confirmed-PAID record dates only (reject announced-only or later-cancelled/withdrawn decisions — a real post-2022 pattern). Div \= 1 for the 4 calendar weeks immediately preceding each confirmed record week (record week excluded); overlapping same-year windows unioned, never double-counted. Requires the full row-level date list (587 confirmed dates, 63 paying companies target); a per-company aggregate cannot substitute (an aggregate carries no positional information). **Lag-window disclosure (C8):** when Div enters lagged (§4), the effective window shifts forward one week and silently includes the record week; see §4.

**(e) SOE(i)** — time-invariant (1/0/UNDETERMINED), Russian government ≥25% voting control, direct or via state-controlled holding, from primary disclosure. Not date-indexed. **Reconciliation mandate (C6):** before any execution of H6, Section 6, or sensitivity checks 5–6, publish a reconciliation of the two surviving source tables (35/40 over 75 in the UNIFIED\_PAPER appendix vs 34/41 over 75 pre-screen in `agent2b_soe.csv`, 33/41 over the 74 modelled). The reconciliation names every firm whose label differs, shows each side's primary citation and vintage, and declares the governing table under the evidence-first rule (valid primary citation governs; ties broken by most recent reliable disclosure). The 34-vs-33 difference over 75 vs 74 is a screen artifact (MRKK is an SOE and is screen-excluded) and must be reported as such, not as a disagreement. **H6, Section 6, and sensitivities 5–6 are blocked until this row is published.**

**Overlap measurement (after all controls are built):** `Crisis ∩ Sanction`, `Crisis ∩ News`, `Sanction ∩ News` must equal exactly 0; nonzero is a construction bug to fix, not a finding. `Div ∩ Crisis`, `Div ∩ Sanction`, `Div ∩ News` are measured and disclosed, never removed. Flag any company whose overlap reaches ≥4 weeks or ≥25% of its own Div=1 weeks as a robustness-check candidate (target 11 companies; a materially different reconstruction requires the exact company lists under each definition side by side).

## 4\. Hypotheses and Exact Equations

**H1 (M1, per-company OLS):** `RV(i,t) = α + β1·ASVI(i,t−1) + β2·RV(i,t−1) + β3·ln[V(i,t−1)] + β4·Crisis(t−1) + β5·Sanction(i,t−1) + β6·Div(i,t−1) + ε(i,t)` — Newey-West HAC, bandwidth 4\. **Crisis-lag interpretation (C5):** β4 estimates whether *last week's* market-wide stress state predicts this week's volatility (spillover/decay). It is not the contemporaneous crisis-week premium; that premium is quantified only in robustness r1 and labelled descriptive, because Crisis(t) is constructed from week-t cross-sectional outcomes and therefore carries a mechanical link with the dependent variable. **Div-lag disclosure (C8):** Div(i,t−1) \= 1 in {W\_R−21d, W\_R−14d, W\_R−7d, W\_R} — the lag shifts the raw pre-record window forward one week and includes the record week the raw construction excludes. The count of Div\_l1 weeks that are record weeks is reported at execution; robustness r4 quantifies the consequence.

**H2 (M2, per-company OLS):** `R(i,t) = α + β1·ASVI(i,t−1) + β2·R(i,t−1) + β3·RV(i,t−1) + β4·Crisis(t−1) + β5·Sanction(i,t−1) + β6·Div(i,t−1) + ε(i,t)` — NW HAC bw 4\. ROLO excluded (§2).

**H1/H2 robustness (M3, Granger):** restricted (own lags \+ controls) vs unrestricted (+ L lags of ASVI, L \= 1, 2, 4), F-test per company per hypothesis. Benjamini-Hochberg applied SEPARATELY within the H1 block and the H2 block — never pooled; Bonferroni-Holm alongside as conservative check. ROLO excluded from both blocks (§2).

**H1 aggregate / H4 / H5 / H6 (M4, pooled panel, full sample; C7 corrected):** `RṼ(i,t) = β1·ÃSVI(i,t−1) + β2·RṼ(i,t−1) + β3·ln[Ṽ(i,t−1)] + β4·Dĩv(i,t−1) + β5·S̃anction(i,t−1) + ε̃(i,t)` Entity \+ time fixed effects; tilde \= entity-and-time-demeaned.

* β3 (lagged volume) is a documented correction — its earlier omission was the likely cause of the sign contradiction between this pooled model and M1; state this wherever the equation is reported, not just implement it silently.  
* **Sanction is included (C7):** it varies within firm at the anchor date and is therefore *not* absorbed by entity fixed effects; omitting it here while retaining it in M1/M2 would recreate the control-set mismatch this document exists to prevent. It is identified only from firms whose anchor falls inside the sample; report that count (`n_with_term`) beside every Sanction coefficient; firms with a constant in-sample Sanction drop automatically.  
* **Crisis is omitted, and this is the reason (C7):** Crisis is constant across firms within a week and hence perfectly collinear with — absorbed by — the time fixed effects. Its absence is design, not oversight.  
* H4: add ASVI×Sector interactions; Wald test on joint significance.  
* H5: add ASVI×Div\_l1 interaction; report full sample and excluding the confirmed robustness-check candidates.  
* H6: add ASVI×SOE interaction; report base and the sector-confound check. Because entity FE absorb sector main effects, the operative sector control is **sector×week fixed effects** (E4); report both side by side, and if the interaction attenuates materially, state plainly that the apparent ownership effect may be partly or wholly a sector-time effect.

**H3 (M5, Chow; C8 corrected):** asymmetric Chow at 2022-02-24, reduced form (ASVI\_l1, RV\_l1 only, plus a crisis\_chow dummy in the post-period), q \= 3, k\_pool \= 4\. Apply Giles-Lieberman heteroscedasticity-robust bounds from the actual published tabulation (`canterbury-nz-034.pdf`); no approximate substitute without first attempting genuine extraction (a named blocker if extraction fails). **Chosen row:** the k \= 4 diagonal row, Cu(0.1,10\] \= 5.206, as hand-transcribed in `gl_bounds_published.csv` (automated parse rejected, D15). **Disclosures:** (i) q \= 3 against a k \= 4 tabulation is a mismatch and is stated; (ii) survivor counts under the two alternative published rows (off-diagonal T1=40,T2=10, Cu \= 3.372; widest T1=T2=10, Cu \= 18.127) are reported alongside the chosen row, because the count is an artefact of which published row one reads.

**M6 (permutation \+ power; C1 corrected):** primary null \= **circular (block) shift within company**: 1,000 random rotations of each company's ASVI series, which preserve its serial correlation and destroy only its alignment with the week being explained; re-fit M1; test statistic \= the HAC t-statistic of β1 (respecting the standard error); permutation p \= share of placebo |t| ≥ actual |t|. Secondary: the naive i.i.d. shuffle, reported alongside with the explicit caveat that it understates the null's variance and manufactures significance; coefficient-based placebo p (which takes the SE as given) is reported but never quoted alone. Power and MDE at 80% via the non-central t, for every company; any null with power \< 0.80 is reported as "inconclusive due to insufficient power," never "no effect exists."

**4.7 Pre-registered robustness specifications (E2; never primary, never substituted for the named six):**

* **r1 (C5):** M1/M2 with Crisis(t) contemporaneous — descriptive crisis-week premium, mechanical-link caveat attached.  
* **r2 (C7):** M1/M2 with News(i,t−1) added — polices the firm-event confound News was built to detect.  
* **r3 (C4):** M4 re-estimated on the alternative volatility family ((High−Low)/Close) and on |weekly return| — the documented measure-family disagreement; the Parkinson ln(H/L) remains primary.  
* **r4 (C8):** M1/M2/M4 with Div(i,t) contemporaneous (pure pre-record window, record week excluded) versus Div(i,t−1).

## 5\. Sector-Wise Pooled Panels

Purpose: direct subgroup complement to H4. Run the **corrected M4 equation of §4 (including β5·S̃anction)** separately within each sector: identical specification, one sector's companies at a time.

**Minimum-N rule (C2 corrected):** only run a sector pool for a sector with ≥4 companies **as computed from the frozen §1 mapping at execution time**. Every below-threshold sector is listed by name with its count as "insufficient N for a separate sector pool" (computed, never hard-coded); its ASVI effect is captured only through H4's interactions.

**Report:** each qualifying sector's β1, SE, p, and N (companies × weeks), beside the corresponding H4 interaction coefficient. The two approaches should tell a consistent story; material disagreement for a sector is itself reported, not resolved by choosing the prettier number.

## 6\. Government / Non-Government (SOE) Pooled Split

**Gated on the §3(e) reconciliation (C6).** Split into Group G (SOE \= 1\) and Group P (SOE \= 0); UNDETERMINED companies excluded from both for this analysis only — report how many and which. Run the **corrected M4 equation of §4** once per group.

**Mandatory sector-confound check (E4):** re-estimate Group G with **sector×week fixed effects** (entity FE absorb sector main effects, so sector×week is the operative control), if Group G holds enough within-group sector diversity. Report both versions side by side; if Group G's β1 moves materially, state plainly that the apparent government-ownership effect may be partly or wholly a sector effect.

**Report:** β1, SE, p, N for G and P side by side, plus the sector-controlled G re-estimate, beside H6's interaction coefficient; disagreement is a reportable finding.

## 7\. Named Sensitivity Checks (run exactly these six; nothing substituted)

1. Drop the 5 revision-affected News events; confirm explained-spike share moves 62.2% → 61.7%.  
2. Widen News windows to t…t+5 for persistence-flagged events.  
3. Re-run with strict Crisis/Sanction definitions; confirm residual unexplained share moves toward 49.2%.  
4. Re-run H5 excluding the confirmed robustness-check candidates (target 11).  
5. Re-run H6 (and the Section 6 Group-G re-estimate) with federal-only SOE.  
6. Flip-test VSMO's SOE classification (0 vs 1), given its knife-edge 25%+1 stake. Checks 5–6 and all of Section 6 remain gated on the §3(e) reconciliation row.

## 8\. Protocol for Missing or Unrecoverable Data

Never reconstruct silently and present the result as equivalent to the original. When something is missing: (a) state plainly what is missing; (b) state what was tried to recover it; (c) if a substitute was used, state precisely how it differs and its quantitative consequence; (d) flag the affected result PROVISIONAL until the real data is recovered. This applies without exception to: the Giles-Lieberman tabulation (and the chosen-row disclosure), the full 587-row dividend date table, the company exclusion registry, the frozen sector-mapping table, and the SOE reconciliation row — and to any further gap discovered.

## 9\. Amendment Register (v1.0 → v2.0)

| ID | Correction | Change | Sections touched |
| :---- | :---- | :---- | :---- |
| C1 | Permutation null | Circular/block-shift primary, t-statistic based; i.i.d. demoted to secondary with over-tight-null caveat | §4 M6 |
| C2 | Minimum-N list | Below-threshold sectors computed from a frozen, published 75-row sector mapping at execution; hard-coded names prohibited | §1, §5 |
| C3 | ROLO in M2 | Full exclusion from M2 (RV\_l1 regressor contaminates it) | §2 |
| C4 | RV formula | Reverted to ln(High/Low) per prospectus (Alizadeh et al. 2002); (H−L)/Close survives only as r3 | §2, §4.7 |
| C5 | Crisis lag | Interpretation stated (spillover/decay, not premium); contemporaneous premium demoted to descriptive r1 | §4 H1, §4.7 |
| C6 | SOE counts | Reconciliation row mandated and gating H6/Section 6/sensitivities 5–6 | §3(e), §6, §7 |
| C7 | News role; M4 omissions | News role stated; **Sanction restored to M4 and Sections 5–6** (not FE-absorbed); Crisis omission reasoned (time-FE collinearity) | §3(c), §4 M4 |
| C8 | GL row; Div lag shift | Chosen GL row \+ q/k mismatch \+ alternative-row sensitivity disclosed; Div\_l1 record-week inclusion disclosed with r4 | §4 H1, §4 M5, §4.7 |
| E1 | Extension of C3 | ROLO exclusion extended to M3 (both blocks) by the same contamination logic | §2 |
| E2 | Extension of C4/C5/C7/C8 | Pre-registered robustness subsection r1–r4 | §4.7 |
| E3 | Extension of C2 | Frozen sector-mapping table published before any sector analysis | §1 |
| E4 | Extension of C7/H6 | Sector×week FE defined as the operative sector control (entity FE absorb sector mains) | §4 H6, §6 |

**Freeze protocol:** this is v2.0. Any future change is a numbered amendment with reason and affected results, published in §9 — never a silent edit.

---

