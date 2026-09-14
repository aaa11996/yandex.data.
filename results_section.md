# Results

**Study:** Investor attention (Yandex Wordstat search volume, ASVI) and volatility/returns of MOEX-listed equities, weekly, 2018–2026.
**Status of all estimates below: PROVISIONAL.** The four control variables required by the full model — `Crisis(t)`, `Sanction(i,t)`, `Div(i,t)`, `SOE(i)` — were unavailable in the input data (all-NaN placeholder columns produced by the data-construction step). Per the analysis protocol, the corresponding terms were omitted from every regression equation rather than zero-filled, and no values were invented. This omission affects which hypotheses could be tested (H5 and H6 could not be tested at all) and may bias the remaining coefficients if the omitted controls correlate with attention and volatility. All results should be re-estimated once the controls are supplied.

## 5.1 Data and estimation summary

The final panel contains 29,401 company-weeks: 75 MOEX-listed companies across 13 sectors, observed on a contiguous weekly grid from Monday 2018-08-20 (earliest available source week; 2018-08-27 for all companies except GMKN) through Monday 2026-02-23 (last week fully inside the 2018-01-01–2026-02-28 study window). Source search data extend to 2026-08-31 and price data to 2026-09-06; observations after February 2026 were discarded as specified. Combined search volume per company-week is SVI = ticker-file count + Cyrillic-file count (Yandex: YNDX + YDEX + Cyrillic counts summed as one company across its July 2024 ticker transition, with ASVI set to missing for the 8 weeks 2024-07-22 through 2024-09-09 as specified). ASVI is the log SVI deviation from its trailing 8-week median. Regressors enter all predictive specifications lagged one week; an independent audit verified the lag construction row-by-row and found no lookahead contamination (audit check 1, PASS).

Estimation: per-company OLS with Newey-West HAC standard errors (4-week bandwidth; Method 1); Granger-causality F-tests at lags 1, 2, 4 with Benjamini–Hochberg (BH) and Bonferroni–Holm corrections applied separately within the H1 and H2 test blocks (Method 2); pooled panel fixed-effects (entity and week) regression with entity-clustered and heteroskedasticity-robust standard errors (Method 3); per-company asymmetric Chow break tests around 2022-02-24 (Method 4); 1,000-draw permutation falsification tests and non-central-t power calculations (Method 5). Per-company estimation samples range from 255 to 379 complete cases (median 375).

## 5.2 Aggregate panel-level finding (Method 3)

The pooled two-way fixed-effects regression of RV(i,t) on ASVI(i,t−1) and RV(i,t−1), with company and week fixed effects, yields a positive but conventionally insignificant coefficient on lagged attention:

**Aggregate attention across the full panel does not predict volatility at the 5% level, at +0.00264, p = 0.066 (entity-clustered SE = 0.00143), based on N = 27,924 company-weeks.**

With heteroskedasticity-robust (non-clustered) standard errors the same coefficient is +0.00264, SE = 0.00136, t = 1.94, p = 0.052. Lagged volatility is strongly persistent (RV_lag1 = +0.340, p < 0.001); the within R² is 0.140. Economically, the point estimate implies that a one-standard-deviation higher log search-volume deviation is associated with a same-signed but small change in next-week range volatility; the estimate is marginally significant at the 10% level and its sign is positive, but the null of no aggregate effect cannot be rejected at 5%. Note that this pooled positive estimate contrasts with the per-company results below, where the majority of individual b1 estimates are negative; the contrast is reported as-is and is not further reconciled here.

## 5.3 H1 — Attention → Volatility

**Per-company OLS (Method 1, M1: RV on ASVI_lag1 + RV_lag1 + lnV_lag1, HAC SEs).** 2 of 75 companies show a significant *positive* ASVI coefficient at the 5% level:

| Ticker | b1 | HAC SE | p | n |
|---|---|---|---|---|
| MFGS (Mosenergo) | +0.0321 | 0.0125 | 0.010 | 373 |
| KAZT (KuybyshevAzot) | +0.0181 | 0.0089 | 0.042 | 373 |

12 of 75 companies show a significant *negative* coefficient at 5%: RTKM (−0.0339, p = 0.000006), SNGS (−0.0398, p = 0.0002), PHOR (−0.0159, p = 0.0011), LSRG (−0.0231, p = 0.0017), YNDX (−0.0226, p = 0.018; see §5.10), IRAO (−0.0181, p = 0.010), MRKS (−0.0273, p = 0.020), SIBN (−0.0144, p = 0.025), VSMO (−0.0188, p = 0.028), AFKS (−0.0138, p = 0.029), AKRN (−0.0136, p = 0.031), MAGN (−0.0214, p = 0.047). The remaining 61 companies are insignificant at 5% (MSRS is additionally positive-significant at 10%). Across the 75 companies, 22 b1 estimates are positive and 53 negative; the median b1 is −0.0068 (median |b1| = 0.0094).

**Granger cross-check (Method 2, H1 block).** At the primary lag of 1 week, 9 of 75 companies reject non-causality at raw p < 0.05 (in F-value order: SNGS 9.48, RTKM 9.19, MFGS 8.44, LSRG 6.92, MSRS 6.17, MRKS 5.48, YNDX 4.53, SIBN 4.45, MAGN 4.29); at lag 2, 8 of 75; at lag 4, 4 of 75. **After BH correction within the 225-test H1 block, no test survives at any conventional level** (smallest BH-adjusted p = 0.292); the more conservative Holm correction likewise leaves zero survivors (smallest Holm-adjusted p = 0.50). The overlap between methods is partial: of the two positive-significant M1 companies, MFGS is also raw-significant in the lag-1 Granger test (raw p = 0.0039, BH-adjusted p = 0.292); KAZT is not Granger-significant. Seven of the nine raw Granger-significant companies have negative M1 coefficients (the two exceptions are MFGS and MSRS).

**Assessment of H1.** H1 (positive attention→volatility effect) is supported for 2 of 75 companies at the nominal 5% level in Method 1 and for none after multiple-testing correction in Method 2. The significant company-level coefficients are predominantly negative, which is the opposite sign to H1; the pooled panel coefficient is positive but insignificant at 5% (§5.2). For the 61 insignificant companies the result is *inconclusive due to insufficient power* rather than evidence of no effect: 71 of 75 companies have statistical power below 0.80 at their observed effect size (median power 0.20; §5.9). No company-level result carries BH/Holm-corrected significance.

## 5.4 H2 — Attention → Returns

**Per-company OLS (Method 1, M2: R on ASVI_lag1 + R_lag1 + RV_lag1, HAC SEs).** 1 of 75 companies shows a significant positive ASVI coefficient at 5%: GCHE (+0.0115, SE 0.0052, p = 0.027, n = 376). 4 of 75 show significant negative coefficients: FEES (−0.0315, p = 0.016), PHOR (−0.0125, p = 0.019), MRKK (−0.0100, p = 0.026, n = 256 — below the 350-observation threshold, small-sample caveat applies), MTSS (−0.0138, p = 0.041). Signs: 28 positive, 47 negative; median b1 = −0.0036.

**Granger cross-check (Method 2, H2 block).** At lag 1, 6 of 75 companies are raw-significant (MSRS F = 7.11, PHOR 5.62, LSNG 5.22, OGKB 4.84, RNFT 4.79, FEES 4.26); at lag 2, 5 of 75; at lag 4, 6 of 75. **After BH correction within the separate 225-test H2 block, no test survives** (smallest BH-adjusted p = 0.379); Holm likewise yields zero survivors.

**Assessment of H2.** H2 is not supported: one nominally positive-significant company of 75, no corrected significance anywhere, and a predominantly negative sign distribution. As with H1, the 70 insignificant companies are *inconclusive due to insufficient power* rather than demonstrated nulls. (Method 5 power is computed for the M1 volatility specification per the protocol; company-level M2 power was not separately computed — see Limitations.)

## 5.5 H3 — Structural break at February 2022 (Method 4)

The asymmetric Chow test (pre-period: 3 parameters, no crisis dummy; post-period: 4 parameters including a crisis dummy for the lagged 2022-02-24–2022-04-25 window; q = 3, denominator df = n−7) rejects parameter stability at the 5% level for **33 of 75 companies** (22 at the 1% level; 36 at 10%). Against an expected ~3.75 false positives among 75 tests at a 5% nominal rate, the excess is substantial. The ten largest F-statistics:

| Ticker | F(3, n−7) | p |
|---|---|---|
| BANE | 15.37 | 1.9×10⁻⁹ |
| AFLT | 14.09 | 1.0×10⁻⁸ |
| ALRS | 11.04 | 5.9×10⁻⁷ |
| AFKS | 8.61 | 1.6×10⁻⁵ |
| MSTT | 7.18 | 1.1×10⁻⁴ |
| MSNG | 7.10 | 1.2×10⁻⁴ |
| CBOM | 7.05 | 1.3×10⁻⁴ |
| MRKU | 6.57 | 2.5×10⁻⁴ |
| MGNT | 5.44 | 1.1×10⁻³ |
| CHMF | 5.28 | 1.4×10⁻³ |

The remaining 5%-significant companies: SFIN, BSPB, YNDX (F = 5.11, p = 0.0018), IRKT, SIBN, SBER (F = 4.73, p = 0.0030), UPRO, YAKG, LSRG, KAZT, CHMK, AKRN, UNAC, MTSS, NVTK, HYDR, VTBR, MRKC, MFGS, SELG, VJGZ, NMTP, TGKA. All computed F-statistics were positive (no negative RSS differentials). **H3 is supported for a substantial minority-to-plurality of the sample**: the ASVI–volatility relationship changed around the February 2022 crisis for 33 companies, spanning 11 of the 13 sectors (all except Insurance and Industrial, whose single-company tests are insignificant: RGSS F = 0.28, p = 0.84; KMAZ F = 0.67, p = 0.57). No multiple-testing correction was specified for Method 4; the reported p-values are raw (see Limitations).

## 5.6 H4 — Sector heterogeneity (Method 3 interactions)

Adding 12 ASVI_lag1 × sector interactions (reference sector: Banking) to the pooled FE model produces a joint Wald test that is highly significant with entity-clustered covariance, χ²(12) = 623.4, p = 1.1×10⁻¹²⁵, but insignificant with heteroskedasticity-robust covariance, χ²(12) = 17.1, p = 0.146. The two covariance estimators disagree on the joint hypothesis; both are reported and neither is suppressed. Individual interaction estimates (clustered SEs; Banking slope = +0.0032, p = 0.124):

| Sector (vs Banking) | Interaction coef. | p (clustered) | p (robust) | Companies in sector |
|---|---|---|---|---|
| Industrial | −0.0200 | <0.001 | 0.029 | 1 (KMAZ) |
| Insurance | +0.0086 | <0.001 | 0.586 | 1 (RGSS) |
| Diversified | −0.0046 | 0.010 | 0.591 | 2 (AFKS, SFIN) |
| Real Estate | −0.0067 | 0.012 | 0.276 | 3 (LSRG, MSTT, PIKK) |
| Tech | +0.0094 | 0.093 | 0.249 | 3 (IRKT, UNAC, YNDX) |
| Energy | +0.0069 | 0.265 | 0.291 | 12 |
| Transportation | +0.0059 | 0.182 | 0.347 | 4 |
| Telecom | −0.0048 | 0.311 | 0.444 | 4 |
| Utilities | −0.0034 | 0.239 | 0.482 | 15 |
| Consumer&Retail | −0.0025 | 0.368 | 0.666 | 4 |
| Chemicals | −0.0019 | 0.617 | 0.730 | 5 |
| Metals&Mining | −0.0002 | 0.951 | 0.969 | 15 |

Under clustered inference, **H4 is supported**: the attention–volatility slope differs across sectors, being most negative relative to Banking for Industrial (a single company, KMAZ) and Real Estate, and most positive for Insurance (a single company, RGSS) and Tech. Because the two extreme sectors contain one company each, those interaction terms are company-specific slopes in substance; the sector interpretation is correspondingly weak for them. Under robust inference, only Industrial remains individually significant and the joint test does not reject.

## 5.7 H5 — Dividend season

The `Div(i,t)` variable (4 weeks preceding each firm's annual dividend record date) could not be constructed: no dividend-date reference data were present in the provided files, the panel column is entirely missing, and the term was omitted from all equations rather than proxied. **H5 could not be tested due to missing dividend-date data and is not reported as either confirmed or rejected.**

## 5.8 H6 — State ownership

The `SOE(i)` flag (government ≥25% voting control) could not be constructed from the provided files; the panel column is entirely missing, and the Method 3 ASVI × SOE extension was skipped per protocol. **H6 could not be tested due to missing ownership-structure data and is not reported as either confirmed or rejected.**

## 5.9 Statistical power, permutation falsification, and minimum detectable effects (Method 5)

Per-company power at the observed M1 effect size (non-central t, two-sided 5%): **only 4 of 75 companies reach 0.80** — RTKM (0.995), SNGS (0.964), PHOR (0.900), LSRG (0.881) — and all four have significant *negative* b1, so their negative findings are adequately powered. The other 71 companies have power below 0.80 (median 0.20; YNDX 0.65). The median minimum detectable effect at 80% power is 0.0250 in b1 units — approximately 2.7 times the median observed |b1| of 0.0094. Consequently, every non-significant company-level result in §§5.3–5.4 is properly read as **inconclusive due to insufficient power**, not as evidence that no effect exists; the study is powered to detect only comparatively large company-level attention effects.

Permutation falsification (1,000 shuffles of ASVI_lag1 within company, seed 20260913) rejects the no-relationship null at the 5% level for 15 companies: 11 with negative b1 (RTKM p = 0.001, MFGS p = 0.001 — positive, LSRG 0.002, MRKS 0.002, MAGN 0.005, SNGS 0.005, MSRS 0.009 — positive, VJGZ 0.009 — positive, AKRN 0.013, PHOR 0.013, YNDX 0.015, HYDR 0.027, UTAR 0.035, VSMO 0.040, ABRD 0.050 — positive). The permutation results align with the HAC-inference results for 10 of 15 companies (both < 0.05); MFGS is the only company that is simultaneously positive-signed, HAC-significant, and permutation-significant. No company's significant HAC result is contradicted by a permutation p near 1, i.e., none of the reported coefficients is attributable to chance time-alignment alone.

## 5.10 Yandex (YNDX) replication check

A prior pilot on a smaller sample reportedly found one of the strongest positive ASVI–volatility relationships in the panel for Yandex. In this full run, YNDX's M1 coefficient is significant but **negative**: b1 = −0.0226 (HAC SE 0.0096, t = −2.36, p = 0.018, n = 364), permutation p = 0.015, power = 0.65. The independent audit flagged this as a divergence requiring investigation before trusting other results (audit check 11, WARNING). The investigation found:

1. The 2024 ticker-transition window (YNDX→YDEX, 2024-07-24) was handled exactly as specified — ASVI missing for the 8 weeks 2024-07-22 through 2024-09-09, verified against an independent recomputation from the raw three-channel search files (audit check 2, PASS; max deviation 2.2×10⁻¹⁶).
2. The negative sign is **not** an artifact of that handling: re-estimating without the blanking gives b1 = −0.0214 (p = 0.019); excluding June–December 2024 gives −0.0229 (p = 0.018); the pre-2022-02-24 subsample gives −0.0380 (p = 0.107, n = 174) and the post-2022 subsample −0.0201 (p = 0.193, n = 190); excluding the five largest |ASVI_lag1| weeks gives −0.0165 (p = 0.126).
3. The raw unconditional correlation between ASVI_lag1 and RV for YNDX is slightly positive (+0.043); the negative partial slope arises after conditioning on RV_lag1 and lnV_lag1.
4. Under the alternative (literal `[W, W+6]`) search-to-price join — which pairs each search week with the *following* trading week's price bar — b1 = +0.0020 (p = 0.735): positive-signed but insignificant, i.e., no alignment variant tested here reproduces a strong positive Yandex result.

The divergence from the pilot is reported factually as unresolved and worth investigating (candidate explanations to be examined with the pilot's exact specification and sample dates, not adjudicated here). All other pipeline checks passed, so the divergence does not by itself invalidate the remaining results, but per the audit protocol the YNDX anomaly remains an open item that should be resolved before the panel-level conclusions are treated as final.

## 5.11 Limitations

The following limitations are stated in full, including every WARNING from the independent quality-control audit (no audit check returned FAIL).

**(a) Missing control variables (affects all methods; H5/H6 untestable).** `Crisis`, `Sanction`, `Div`, and `SOE` require external reference data not present in the provided files and must be supplied before Methods 1, 2, 3 and the Method-3 SOE extension can be run correctly. The terms were omitted (not zero-filled) from all equations; every estimate here is provisional. In particular: without the Crisis dummy, the Feb–Apr 2022 weeks — which combine extreme volatility and extreme attention — are not separately controlled in Methods 1–2 (they are absorbed only insofar as the panel week fixed effects in Method 3 capture market-wide shocks); without Sanction, firm-specific sanction shocks may load onto ASVI; H5 (dividends) and H6 (state ownership) could not be tested at all and are reported as neither confirmed nor rejected.

**(b) Audit WARNING 3 — date-alignment convention.** Verbatim from the audit: *"Price dates are 100% Sundays … NOT Fridays as the task spec assumed; the literal '[W, W+6]' rule would have matched each search week to the FOLLOWING trading week (1-week lookahead), so Agent 1 applied the spec's stated same-calendar-week criterion instead. … WARNING issued because the join deviates from the literal rule text (documented in Agent 1 report §2) even though same-calendar-week alignment is empirically confirmed."* The Sunday-label convention was established via the Feb–Mar 2022 MOEX suspension pattern (bars labeled 2022-02-27/03-06/03-13 absent; bar labeled 2022-02-20 carries the actual Friday 2022-02-25 invasion-week close of 131.12, −47.6%). Sampled weeks re-verified against raw files with zero mismatches. Nevertheless, all reported coefficients are conditional on this alignment judgment; §5.10 item 4 quantifies the sensitivity for YNDX (the only alignment-sensitive check performed).

**(c) Audit WARNING 6 — marginal samples.** Verbatim: *"M1 complete cases <350: MRKC 308, MRKK 255, MRKS 341, MRKU 348 (min=255, median=375, max=379). Marginal samples for per-company OLS; per-spec WARNING."* The cause is weeks with SVI = 0 in both search channels (MRKC 26, MRKK 17, MRKS/MRKU 4 each; also SFIN 2, UKUZ 1), which make log SVI — and hence ASVI for that week plus the following 8 weeks — missing by construction (log 0 undefined; no small-constant substitution per protocol). Per-company results for these four Rosseti subsidiaries carry a small-sample caveat; MRKK's significant negative M2 coefficient in particular rests on n = 256.

**(d) Audit WARNING 11 — Yandex replication divergence.** Verbatim (abridged where indicated, full text in the audit report): *"YNDX M1 b1=−0.02259 (HAC p=0.0181, n=364) is SIGNIFICANT but NEGATIVE — sign reversed vs the expected strong positive pilot result. … Pipeline mechanics verified correct; the divergence from the pilot is a substantive finding to report and investigate, not a computation error — unless the pilot itself used the literal (lookahead) alignment."* Per the audit protocol this WARNING requires investigation before other results are fully trusted; the investigation outcomes are documented in §5.10 and do not localize the divergence to any pipeline step.

**(e) Multiple testing.** BH/Holm corrections were applied only within the Method 2 blocks, as specified. The 150 Method-1 coefficients, 75 Method-4 Chow tests, and the Method-3 interactions carry raw p-values; at 75 tests per method, ~3.75 false positives per method are expected at a 5% nominal rate even under the global null. The H1/H2 company-level counts (2 and 1 positive-significant) are within or near that range; the H3 count (33) is far above it.

**(f) Inference choices in Method 3.** The sector-heterogeneity Wald test is significant under entity-clustered SEs (p ≈ 1×10⁻¹²⁵) and insignificant under robust SEs (p = 0.146); the conclusion for H4 is therefore inference-dependent. Two of the significant sector interactions (Industrial, Insurance) are single-company sectors and are effectively firm-specific slopes. Clustering on 75 entities is at the lower end of the conventional range for cluster-robust inference.

**(g) Power.** Post-hoc power at observed effect sizes (Method 5 specification) is below 0.80 for 71 of 75 companies; all null company-level findings are inconclusive rather than demonstrably zero, and the median MDE (0.025) exceeds the median observed |b1| (0.0094) by a factor of ~2.7. Company-level power for the M2 (returns) specification was not separately computed.

**(h) Data-quality items carried from construction.** ROLO trades near ₽0.20 with 2-decimal price resolution (one tick ≈ 5%); its returns and range volatility carry heavy quantization noise, and the source `Change %` column reads 0.00% in weeks where the rounded price moves a tick (max deviation 5.6pp from close-to-close change). Weekly log returns are computed across consecutive price-file rows and therefore span the Feb–Mar 2022 trading suspension (matching the source export's own `Change %`). 1–2 rows per company have missing volume ('-') → lnV missing there. All Wordstat files share an identical 419-week grid with explicitly reported zeros; the missing-date-as-zero rule was never invoked in practice.

**(i) Scope.** The sample is limited to the 75 companies with complete raw file triads in the provided archive; results describe this sample of mostly mid/large-cap MOEX names and not the Russian equity market as a whole.

---
*Prepared by the reporting agent from: `output/agent2/method{1..5}_*.csv` (validated results) and `output/agent3/audit_report.md` (QC audit; 0 FAIL, 3 WARNING, 8 PASS). No numbers were recomputed for this section.*
