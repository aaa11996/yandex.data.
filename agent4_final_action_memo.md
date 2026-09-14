# FINAL ACTION MEMO — TO THE STUDENT

*Synthesis of the literature memo (Agent 1), diagnostic memo (Agent 2), and strategy memo (Agent 3). No statistics were recomputed here; this memo integrates and prioritizes only.*

## 1. What your data actually shows

Your weekly MOEX panel is **too small to detect attention effects of the size it actually contains**: the median firm would need roughly ten times its current ~375 weeks to detect its own observed coefficient, so "not significant" almost never means "no effect" in this study. What *is* detectable is not the hypothesized positive US-style effect: where the data has power, attention predicts **lower** next-week volatility (a robust, permutation-confirmed negative conditional slope in a handful of large firms, including Yandex), the attention–volatility slope **differs across the volatility distribution and across the 2022 sanctions boundary** (near-zero in high-volatility weeks, consistently negative in normal pre-2022 weeks), and the **February 2022 event genuinely changed the volatility regime** for about half the sample. Honest headline: *"no detectable positive attention effect in a market where the retail channel that transmits it in the US is contrarian and non-forecasting; an underpowered, regime- and tail-dependent, mostly-negative relationship; and a real 2022 structural break."* That is a defensible, citable finding — a documented non-transfer of a classic Western result to a sanctioned, retail-dominated market — not a failed thesis.

## 2. Next steps, in priority order

1. **Re-frame every null as "underpowered / inconclusive," with the MDE table and the ×10-sample arithmetic as a headline contribution** (hours of work, zero risk, converts your weakest section into your most citable one).
2. **De-contaminate H3: heteroscedasticity-robust break test / Giles–Lieberman bounds, report nominal vs robust vs bounds-survivor counts** (your strongest finding is also your most attackable; this makes the survivors bulletproof and honestly reclassifies the rest as variance-regime shifts).
3. **Build the two cheap controls: the Crisis time dummies (trivial) and the static SOE flag (≈1 day), unlocking H6; declare Sanction and (probably) Div as limitations** (completes the model where completion is actually feasible; SDT explicitly names ownership moderation as open emerging-market research, so even a null H6 is a first).
4. **Run the two pre-specified specification tests — quantile regression and pre/post-2021 split — with a written pre-specification and symmetric reporting rule** (the diagnostics show your linear mean specification averages opposite-signed regimes; both outcomes are publishable, and the India paper gives you the exact template).
5. **Write the YNDX section as a sign-reversal case study (conditional-suppression decomposition + alignment-sensitivity table + pilot discrepancy bounded to the pilot's specification), keeping YNDX in all aggregates** (closes the audit's open WARNING with interpretation instead of exclusion).

*Ordering reason:* each step is ordered by (value-to-committee ÷ effort) and by logical dependency — re-framing costs nothing and changes how everything else reads; the H3 fix protects the one strong result; controls and specification tests add the new evidence; YNDX write-up last because it only needs the R4/R2 outputs as inputs.

## 3. What changes in your thesis narrative, and what stays

**Change:**
- The implicit "we tested H1/H2 and found little" framing → "we bounded what is detectable; detectable effects are opposite-signed, tail-dependent, and regime-dependent; here is the required sample size."
- H3's "33/75 show a structural break" → "33/75 reject the homoscedastic Chow test; of these, N survive heteroscedasticity-robust/bounds tests as coefficient breaks, the remainder are variance-regime shifts; either way the pre/post relationship differs for ~half the panel."
- H4's sector story → demoted to "inference-dependent; single-firm sectors; real firm-level heterogeneity exists but sector is not its organizing dimension."
- The pooled-vs-per-company sign contradiction, currently "reported as-is and not reconciled" (§5.2) → reconciled as two different identifying variations (within-week cross-firm stress co-movement vs within-firm time-series calm-search effect); this reconciliation is new text you should write.
- Limitations: add Sanction/Div as *declared* limitations with bias directions, and add the quantile/regime evidence to the "why OLS mean is uninformative here" discussion.

**Keep, as originally framed and defensible:**
- The data construction, lag/alignment protocol, and audit trail (0 FAILs; the two WARNINGs are already handled exactly as an auditor would want — investigated, documented, not hidden).
- The multiple-testing discipline (BH/Holm within pre-specified blocks, expected-false-positive arithmetic stated).
- The power/permutation methodology (Method 5) — this becomes the spine of the thesis, not an appendix result.
- H5/H6 "untested, not confirmed or rejected" status — correct as stated; H6 simply becomes testable after step 3.
- The honest reporting of YNDX's negative sign and the pilot divergence.

## 4. If a committee member pushes back

**"Your main hypotheses failed — what exactly is the contribution?"**
"The contribution is that a well-established US attention effect is shown to be undetectable-to-absent in a sanctioned, retail-dominated market, with the detection floor quantified: this panel rules out firm-level attention effects above ≈0.025–0.03 and cannot adjudicate below that, and what it *can* detect is opposite-signed and regime-dependent. A documented, well-powered-where-it-matters non-transfer of a classic finding, with its boundary conditions (retail contrarianism per Djalilov–Ülkü, 2022 regime break), is a positive contribution to the international attention literature."

**"With 75 firms and no correction surviving, aren't your few significant results just false positives?"**
"The company-level counts near the null are within the expected false-positive range, and I say so in the text; but the four well-powered negative coefficients (power 0.88–0.99) and the permutation-confirmed subset are not chance alignments, and the 33 break rejections are ~8× the false-positive expectation. I report which results are evidential and which are inconclusive, rather than presenting all p-values as equal."

**"Why should we believe the negative signs? The pilot found positive for Yandex."**
"Because the negative conditional slope is robust to permutation, subsamples, outlier removal, and ticker-transition handling, and the raw correlation is positive — the sign appears only after conditioning on lagged volatility, i.e. it is the firm-specific attention component, not the market-stress component. No alignment variant reproduces the pilot's strong positive, which points to the pilot's specification rather than this pipeline; I report the divergence unresolved rather than resolving it by deletion."

**"You're missing four control variables — how can any coefficient be trusted?"**
"The two that matter most for bias (Crisis, Sanction) are now entered where constructible (Crisis) or declared with their likely bias direction (Sanction: likely attenuates the negative post-2022 slope, i.e. biases *against* my detectable finding). Omitted-variable bias that pushes against one's own significant result is a conservative configuration; I state this explicitly."

## 5. Research-integrity statement

I reviewed all upstream recommendations for result-conditional choices. **None of the recommended steps excludes data, firms, or periods because of their results, and none searches specifications for significance.** Specifically: YNDX stays in all aggregates (its exclusion was considered and rejected upstream, correctly); the pre/post split uses the exogenous 2022-02-24 event date, not a searched breakpoint; quantiles, lags, and corrections follow the literature template (SDT) and the study's pre-specified blocks; and every new test carries a pre-committed symmetric reporting rule under which a null outcome is written up as a result. **If, under deadline pressure, anyone (including you) proposes dropping Yandex, trimming 2020, adding lags until significance, or applying corrections selectively to rescue a hypothesis — that is p-hacking or sample-selection-on-results; do not do it, and the memos above give you the sentence to write instead.** The version of this thesis that reports bounded, regime-dependent, honestly-labeled evidence is stronger than any version with more stars.
