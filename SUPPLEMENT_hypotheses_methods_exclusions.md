# Hypotheses, Methods, and Exclusion Registry — Supplement to the Unified Methodology Manual

This file exists specifically to close two gaps the Unified Methodology
Manual explicitly flagged as missing from the live repository (§5.1, §7,
§8). It should be added to the repository alongside the manual.

---

## A. Hypotheses H1–H6 (the correct, original specifications)

- **H1 (Attention → Volatility):** Abnormally high ASVI in week t−1
  positively predicts weekly volatility (RV) in week t, controlling for
  lagged volatility, lagged volume, and lagged Crisis/Sanction/Div.
- **H2 (Attention → Returns):** Abnormally high ASVI in week t−1
  positively predicts weekly returns (R) in week t (price-pressure
  effect), with any positive effect expected to reverse in subsequent
  weeks.
- **H3 (Structural Break):** The February 2022 crisis structurally
  altered the ASVI–volatility relationship — tested via an asymmetric
  Chow test at 2022-02-24, made heteroscedasticity-robust via
  Giles-Lieberman bounds.
- **H4 (Sector Heterogeneity):** The ASVI–volatility coefficient differs
  significantly across industry sectors — tested via a Wald test on
  ASVI×Sector interaction terms in the pooled panel model.
- **H5 (Dividend Season):** The ASVI–volatility relationship is stronger
  during the 4 weeks preceding a firm's dividend record date — tested via
  an ASVI×Div(i,t) interaction term in the pooled panel model.
- **H6 (State Ownership Effect):** The predictive power of ASVI on
  volatility is weaker for state-owned enterprises than private firms —
  tested via an ASVI×SOE(i) interaction term in the pooled panel model.

## B. Method Equations (M1–M6)

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

## C. Study window (critical — do not truncate)

The panel must run **2018-08-27 through 2026-09-06** (the full range for
which raw data exists), NOT an earlier cutoff. An earlier construction
run truncated at 2026-02-23 and lost the entire W4 crisis window
(2026-06-22 → 07-27) and the April 2026 EU sanctions package as a result
— this is a documented, known failure mode, not a hypothetical risk.

## D. Exclusion Registry (100-candidate universe → 75 final companies)

The following 19 companies were excluded from the original ~100-company
candidate universe. Every company below is confirmed ABSENT from the
final 75-ticker list in the Unified Methodology Manual §2.2:

| Ticker | Reason |
|---|---|
| TCSG | Name/ticker changed mid-period (Tinkoff → T-Technologies) |
| BELU | Name/ticker changed mid-period (Beluga → NovaBev) |
| AQUA | Name/ticker changed mid-period (Russian Aquaculture → Inarctica) |
| ELFV | Relisted 2022 after Enel Russia exit/restructuring |
| SMLT | IPO'd Oct 2020 — too late for full 2018-2026 coverage |
| SGZH | IPO'd Apr 2021 — too late for full 2018-2026 coverage |
| POSI | IPO'd Dec 2021 — too late for full 2018-2026 coverage |
| RENI | IPO'd Oct 2021 — too late for full 2018-2026 coverage |
| SPBE | IPO'd Nov 2022 — too late for full 2018-2026 coverage |
| GAZS | Both search channels too sparse to yield usable data |
| GAZT | Both search channels too sparse to yield usable data |
| GAZC | Both search channels too sparse to yield usable data |
| APTK | Cyrillic search channel entirely unavailable |
| DVEC | Cyrillic search channel entirely unavailable |
| FLOT | Price history starts Oct 2020 (confirmed real IPO date, not a gap) |
| IRGZ | Price data ends 2022-02-20 and never resumes |
| TGKD | Price data ends 2022-09-11 (~half the window missing) |
| LENT | Price history starts Dec 2021 only (~3.5 years missing) |
| AMEZ | Price data ends 2025-03-09 (final ~18 months missing) |

**Honest limitation:** this list accounts for 19 documented exclusions.
If the original candidate universe was exactly 100, roughly 6 additional
candidates were never successfully onboarded at the initial listing-date/
ticker-verification stage (i.e., they may have failed an early continuity
check before any data collection began, rather than being excluded after
collection). This supplement does not claim to reconstruct those with
certainty — if a fully exhaustive 100→75 reconciliation is needed, it
should be re-derived from the original top-100 candidate list against the
current 75, rather than assumed complete from this document alone.

## E. Named Sensitivity Checks to Run (from the Manual's own findings —
do not skip these; they are pre-identified, not optional extras)

1. Drop the 5 revision-affected News events; confirm the explained-share
   of large volatility spikes moves from 62.2% to the expected 61.7%.
2. Widen News windows to t...t+5 for the 21 events flagged with a
   persistence flag; check whether this changes any confirmed/
   disconfirmed status.
3. Re-run with strict Crisis/Sanction definitions; confirm the residual
   unexplained-spike share moves toward the documented 49.2% (strict)
   rather than 37.8% (padded).
4. Re-run H5 dropping the 11 documented robustness-check candidate
   companies (AVAN, SBER, USBN, VTBR, AKRN, GCHE, NVTK, TATN, LSRG, RTKM,
   AFLT) where Div overlaps substantially with Crisis or Sanction.
5. Re-run H6 using federal-only SOE classification (excluding
   regional-only state control) as an alternative to the primary
   federal+regional definition.
6. Flip-test VSMO's SOE classification (0 vs. 1) given its documented
   knife-edge 25%+1 ownership — confirm whether H6's conclusion is
   sensitive to this single company's coding.
