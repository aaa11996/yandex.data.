# GROUP 4 — AGENT 3: JUDGE / FINAL COMPILATION

**Object:** the final `News(i,t)` control variable for the 75-company MOEX weekly panel, 2018-08-27 → 2026-08-31 (419 weeks, 31,425 company-weeks).
**Compiled by:** Agent 3, 2026-09-18, from the construction output (§13-revised), Agent 2's independent verification report, and Group-1's crisis/sanctions layer.
**Every number below is computed, not asserted.** Machine outputs: `work/judge_spike_attribution.csv` (592 spike rows with their attribution), `work/judge_summary.txt` (full run log), `lib/g4_judge.py` (reproducible).

---

## 1. What is being compiled

`News(i,t) ∈ {0,1}` — a **discrete-event dummy**, not a news-volume measure. It is 1 for the event week of a confirmed company-specific exceptional event and for the following weeks in which the company's own trading data stays elevated, up to a hard 4-week cap.

**Final state:** **31 confirmed events · 23 companies · 89 company-weeks `News=1` (0.283% of the panel) · 52 zero-event companies.**

| | |
|---|---|
| Events | 31 (`work/g4_events_final.csv`), categories: MA 10, CAPRET 8, EARN 5, LEGAL 4, OPS 2, LEAD 1, DEBT 1 |
| Companies with ≥1 event week | 23 of 75 |
| `News=1` weeks per company | ABRD 4, AFLT 4, BANE 1, CBOM 2, FEES 1, FESH 9, GAZP 8, GMKN 9, KMAZ 4, KZOS 3, LKOH 2, MGNT 1, MGTS 4, MTSS 4, MVID 8, NKNC 2, PLZL 2, SFIN 4, TRMK 4, UPRO 4, UTAR 4, VTBR 1, YNDX 4 |
| Company-weeks claimed by two events | **0** |
| Rule applied | trailing 52-week median RV *and* volume from strictly pre-event data; CONFIRMED iff RV **or** volume ≥2× in weeks t…t+3 (a fully missing week counts as confirming); duration = 1 from week t, extend while RV or volume ≥1.5× of the same frozen median, stop at first week below, cap 4 weeks; longer elevation flagged, not attributed |
| Deliverables | `output/News_i_t.csv` (419 × 75), `output/News_i_t_long.csv` |

**This series differs from the construction agent's original submission.** Agent 2's verification found 4 missed qualifying events and 1 inconsistently discarded one; they were re-admitted through the identical pipeline and the series was rebuilt (26 → 31 events, 69 → 89 cells). Full audit trail: construction report §13, Agent-2 report §1 and §5. **This report is written on the corrected series**, and the correction is carried into the numbers below rather than being hidden behind them.

---

## 2. THE CORE QUESTION, ANSWERED WITH NUMBERS

*Are the largest company-specific volatility spikes explained by the three controls, or not?*

**Method.** For each company, the 8 largest single-week RV spikes (RV = (High−Low)/Close, ratio to the trailing 52-week median computed from pre-event data only) across all 419 weeks. 74 companies × 8 = **592 spikes** (ROLO excluded: its RV is a 1-kopeck tick-quantisation artifact, Group-3 registry R4). 75 ≫ the required ≥15 companies. A spike is "explained by X" if its week falls inside X's coverage: Crisis = inside one of the four confirmed windows; Sanction = inside the company's confirmed anchor ±2 weeks; News = `News(i,t) = 1`.

**Headline result.**

| Coverage definition | Explained | Unexplained |
|---|---|---|
| **Padded** (crisis windows as used for dedup; sanction anchor ±2 weeks) | **368 / 592 = 62.2%** | **224 / 592 = 37.8%** |
| **Strict** (crisis windows exactly as dated; sanction = anchor week + 0…4 weeks) | **301 / 592 = 50.8%** | **291 / 592 = 49.2%** |

Breakdown (padded): Crisis alone 336 (56.8%), Crisis+Sanction 11 (1.9%), Sanction alone 4 (0.7%), **News alone 17 (2.9%)**, unexplained 224.

**Marginal reach:** Crisis touches 347 of 592 spikes, Sanction 15, **News 17**. The three never overlap: `News ∩ Crisis = 0`, `News ∩ Sanction = 0` company-weeks (guaranteed by the ±2-week dedup step, which discarded 11 candidates before any testing).

**Was building `News` worth it?** Yes for its purpose, but it is a small-number variable and the thesis must say so:
* News explains **17 of the 592 largest spikes (2.9%)** — small in aggregate, but it is the **only** control that covers company-specific discrete shocks: Sanction covers 15 spikes, and every one of News's 17 is a week that Crisis and Sanction both miss.
* At the event threshold itself (all weeks with RV ratio ≥2×: 4,041 weeks), News covers 45 (1.1%), Crisis 1,284 (31.8%), Sanction 53 (1.3%) → the three controls together cover **33.6%**, and **66.4% of ≥2× weeks remain unexplained**. Most of the ≥2× universe sits in the 2×–3× band (2,415 weeks; 75.4% unexplained), which is dominated by illiquid mid-caps whose weekly RV is noisy rather than news-driven — a known feature of this panel, not evidence of 1,800 hidden news events.
* Counterfactual: with the *pre-revision* series (26 events, 69 cells) the padded explained share would be 61.7% instead of 62.2%. **The 5 revision events move the unexplained count by 3 spikes.** The revision was made because the events met the bar, not because it changed the headline.

**The ≥15-company requirement is met 5× over.** Companies with the highest share of unexplained spikes among their 8 largest (full table in `work/judge_summary.txt`; all 74 in `work/judge_spike_attribution.csv`):

| ticker | top-8 spikes | explained | unexplained | largest unexplained spike |
|---|---|---|---|---|
| GCHE | 8 | 1 | 7 | 2019-04-01, 7.27× |
| MRKK | 8 | 1 | 7 | 2021-10-11, 7.19× |
| YAKG | 8 | 1 | 7 | 2019-10-07, 11.60× |
| BLNG | 8 | 2 | 6 | 2020-07-13, 9.99× |
| LSNG | 8 | 2 | 6 | 2021-10-11, 14.15× |
| MFGS | 8 | 2 | 6 | 2019-12-16, 7.78× |
| MSTT | 8 | 2 | 6 | 2023-02-06, 22.38× |
| UKUZ | 8 | 2 | 6 | 2021-09-27, 13.31× |
| UTAR | 8 | 2 | 6 | 2022-01-17, 9.04× |
| VJGZ | 8 | 2 | 6 | 2021-01-25, 7.68× |
| AVAN | 8 | 3 | 5 | 2022-06-06, 9.26× |
| CHMK | 8 | 3 | 5 | 2020-07-13, 7.37× |
| MRKS | 8 | 3 | 5 | 2021-10-11, 10.27× |
| MSRS | 8 | 3 | 5 | 2019-09-09, 6.50× |
| PIKK | 8 | 3 | 5 | 2019-11-04, 8.14× |
| RGSS | 8 | 3 | 5 | 2020-11-30, 10.11× |
| TTLK | 8 | 3 | 5 | 2019-12-30, 8.71× |

**The 25 largest unexplained spikes** (largest first): MSTT 2023-02-06 22.4× (41.6% week), ABRD 2021-02-15 16.0×, JNOS 2020-02-03 14.4× (47.5%), LSNG 2021-10-11 14.2× (57.6%, Rosseti-daughters speculative rally), UKUZ 2021-09-27 13.3×, SFIN 2025-12-22 12.1× (the mechanical ex-date of the RUB 902 one-off distribution, excluded by the bar), YAKG 2019-10-07 11.6×, UKUZ 2021-10-04 11.4×, RNFT 2020-08-31 11.4×, ABRD 2019-12-02 11.0×, SNGS 2019-09-02 11.0×, MVID 2019-05-13 10.6×, MRKS 2021-10-11 10.3×, YAKG 2019-09-23 10.3× (76.2%), KZOS 2019-10-07 10.1×, RGSS 2020-11-30 10.1× (62.1%), BLNG 2020-07-13 10.0×, MRKS 2021-10-18 10.0×, AKRN 2021-11-15 9.7×, AVAN 2022-06-06 9.3×, ABRD 2021-10-18 9.3×, UTAR 2022-01-17 9.0×, UKUZ 2021-09-06 8.9×, UTAR 2022-01-24 8.8×, MGTS 2023-02-06 8.8×.

**Interpretation of the residual (what the reader should conclude).** The unexplained block is dominated by (i) low-liquidity mid-caps where a large weekly high–low range appears without any discrete corporate news (MSTT/MGTS/BLNG/NKNC cluster around 2023-02-06 is the clearest case: three illiquid names with extreme volume ratios and no company news — a segment/liquidity episode), (ii) payout-mechanics weeks deliberately excluded by the bar (ex-dates; SFIN 2025-12-22), and (iii) genuinely unresolved cases where no source was found. The residual is **documented and left uncoded** — the variable under-covers rather than over-attributes, which is the conservative direction for a control.

---

## 3. CONSOLIDATED CONTROL-VARIABLE SUMMARY (Crisis / Sanction / News)

| | **Crisis** | **Sanction** | **News** |
|---|---|---|---|
| Nature | market-wide common shock | company-specific **permanent** regime shift | company-specific **discrete transient** shock |
| Coverage | 4 windows (2020-02-23→04-12; 2022-02-20→03-27; 2023-09-03→09-17; 2026-06-21→07-26) = **1,725 company-weeks (5.49%)** | 34 confirmed anchors (Group-1 §10) × 5-week reaction window = **170 company-weeks (0.541%)** | 31 confirmed events = **89 company-weeks (0.283%)** |
| Source of truth | Group-1 verified windows (§4.1), independently re-derived here and in Agent 2's Check 2b | Group-1 verified sanctions map (§5.2, §10) | Group-4 construction + §13 revision, independently verified |
| Distinct justification (why it cannot be replaced by the others) | Without it, market-wide volatility is mechanically attributed to company search attention (reverse causality through common factors) | A designation changes the investor base, settlement and financing **permanently** — a level shift, not a spike; only 15 of 592 largest spikes, but those are structural | The governance rule: attention may not be treated as causal when a company-specific event drives both search *and* volatility; only this variable covers discrete company-specific shocks (Sanction and Crisis miss all 17 of its spikes) |
| Overlap with News | **0 company-weeks** | **0 company-weeks** | — |
| Overlap Crisis ∩ Sanction | the 2022 packets fall inside W2's window for some names; they are not double-coded because Crisis is a window and Sanction is company-specific — where both apply, dedup gives priority to the confirmed Sanction date | | |
| Candidates discarded at Step 1 (already covered) | **11** in total — 10 by a confirmed Sanction anchor (`work/g4_candidates.csv`) and 1 by a Crisis window (`PHOR-1`, 2026-07-22, inside W4, in `work/g4_dedup_log.csv`); each recorded as *"found, already covered — not double-counted"* | | |

**Residual overlap after dedup: zero by construction.** Every candidate within ±2 weeks of a confirmed Crisis window or a company's confirmed sanction date was discarded *before* the data test, and every surviving event was re-tested to sit outside all pads.

**Coverage left unexplained** (the honest bottom line of the whole exercise): 224/592 = 37.8% of the largest spikes (padded definition), 49.2% (strict); 66.4% of all 4,041 weeks with RV ratio ≥2×. These are documented, not modelled.

---

## 4. MANDATORY INTEGRITY STATEMENT

1. **No regression was ever estimated in Group 4**, and no regression result influenced any inclusion, exclusion, category assignment or duration. No such result exists to influence anything. The two documented decision rules were mechanical: (a) the event bar, (b) the ≥2× test — applied identically to every candidate, including famous events that failed (NLMK's 2024-02-24 drone attack: 1.22/0.57/0.65/1.23 — recorded and **excluded**).
2. **Dedup was applied before verification testing, and before any verification result was seen.** Step 1 ran on all candidates first (log: `work/g4_dedup_log.csv`); only survivors reached Step 2. 11 candidates were discarded as already covered and are retained as "found, already covered by [Crisis/Sanction], not double-counted".
3. **The verification stage changed the data, and this is disclosed.** Agent 2 found 4 missed qualifying events (MGTS-2R, KMAZ-1R, SFIN-3R, MTSS-1R) and one bar-application inconsistency (GMKN-3R, a dividend-*policy* shock discarded with a "dividends excluded" reason while the same class had been admitted for VTBR-1). All five were re-admitted through the unchanged pipeline; the revision is documented in the construction report §13, and it is *not* hidden retrospectively — the pre-revision numbers (26 events / 69 cells) remain visible everywhere they were computed. The revision was not triggered by, and could not be optimised for, any outcome variable.
4. **Questionable judgment calls, disclosed:** (a) `CAPRET` is broader than the literal bar — payout-*policy* shocks in, mechanical ex-date gaps out — which costs the largest single-week RV ratio in the panel (SFI's −48.2% ex-date gap) and every ex-date drop (VTBR −26.8%, SBER −10.1%, MTSS −12.2%, TTLK −20.0%); (b) `MTSS-1R` is a boundary case (habitual payer vs payout resumption after suspension), included on the suspension-and-reaction tie-breaker; (c) `GMKN-3R`'s date is the news onset (2021-03-24) rather than the board date (2021-03-29); either passes the test, and the choice moves one week; (d) market-wide episodes (2022-09-19 mobilisation, 2026-03 third-tier oil rally) are excluded as *not company-specific* rather than as dedup; (e) the "a full missing price week counts as confirming" rule is prescribed and implemented but inert here (it fires for 0 of 31 events); (f) ROLO is excluded from RV-based analysis (tick-quantisation artifact) but kept in the panel; (g) the ±2-week pad around sanction anchors is a convention, not a finding.
5. **Known residual error and its direction.** ~39 zero-event companies were not individually re-researched (43 hold uncovered large spikes); the `CAPRET` class was researched non-exhaustively. Any further event found would *reduce* the unexplained share reported in §2, i.e. the reported 37.8% / 49.2% is an **upper bound on what is genuinely unexplainable and a lower bound on coverage**. Sourcing limits (e-disclosure.ru unreachable; Russian-language press and IR releases only) are unchanged from construction report §3.
6. **Reproducibility.** Every number here comes from `lib/g4_common.py` → `lib/g4_build_news.py` → `lib/g4_supplement.py` → `lib/g4_verify.py` → `lib/g4_verify_revision.py` → `lib/g4_judge.py`, all of which can be re-run from the raw `iqbal thesis/Data` panel; `g4_supplement.py` is idempotent.

---

## 5. HOW THE THESIS SHOULD USE THIS VARIABLE

* **Primary specification:** `News(i,t)` as shipped (31 events / 89 week-cells), entered as a contemporaneous control alongside the Crisis and Sanction dummies.
* **Report the coverage honestly in the methods section:** three controls cover 33.6% of all weeks with RV ratio ≥2×; 62.2% of the 592 largest company spikes (padded) / 50.8% (strict); a documented 37.8–49.2% residual remains unexplained, concentrated in low-liquidity names.
* **Sensitivity set (recommended, in order of importance):** (i) drop the 5 §13 revision events (they are recent, small-cap, payout-driven; the explained share falls only from 62.2% to 61.7%); (ii) widen every event window to t…t+5 — the 4-week cap is mechanical at the 1.5× threshold, and **21 of the 31 events carry the persistence flag** (≥1.5× in ≥3 of the weeks after the cap: recorded and flagged, never attributed); (iii) re-run the attribution with the *strict* crisis/sanction definitions (49.2% unexplained) to show the result does not hinge on the ±2-week cushion.
* **Do not** interpret a missing `News=1` as "no event occurred": for 52 companies the correct statement is "no *verified* discrete event was found".

---

## 6. FILE MANIFEST (final state)

| File | Contents |
|---|---|
| `output/News_i_t.csv` | **the deliverable** — 419 weeks × 75 companies, 89 ones |
| `output/News_i_t_long.csv` | same, long format (ticker, week, News) |
| `output/GROUP4_CONSTRUCTION_REPORT.md` | construction, 12 sections + Appendix A + **§13 post-verification revision** |
| `output/GROUP4_AGENT2_VERIFICATION_REPORT.md` | independent verification: PASS/FAIL/WARNING per check + readiness statement |
| `output/GROUP4_FINAL_REPORT.md` | this Judge/compilation report |
| `work/g4_events_final.csv` | the 31 confirmed events with every number |
| `work/g4_candidates.csv` + `g4_candidates_supplement.csv` | 93 + 5 candidate rows with sources and decisions |
| `work/g4_dedup_log.csv` (36) · `g4_verification_results.csv` (35) · `g4_duration_detail.csv` | dedup, test, duration audit trails |
| `work/spike_scan.csv` · `judge_spike_attribution.csv` | 900 top-12 spikes; the 592 top-8 spikes with attribution |
| `work/verify_run.txt` · `g4_verification_audit.txt` · `g4_verify_revision_audit.txt` · `g4_supplement.log` · `judge_run.txt` · `judge_summary.txt` | raw logs behind every claim above |
