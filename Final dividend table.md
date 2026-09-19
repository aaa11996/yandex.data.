# Agent 3 — Final dividend table, Div(i,t) construction and overlap disclosure (Group 5 judge report)

**Compiled:** 2026-09-19 · **Inputs:** Agent 1a (`AGENT1A_dividends_2018_2021.md`), Agent 1b (`AGENT1B_dividends_2022_2026.md`), Agent 2 (`AGENT2_verification_report.md`) · **Panel:** 75 companies × 419 weeks (Monday week-starts 2018-08-27 → 2026-08-31, the Yandex Wordstat grid; the price files label the same weeks by the preceding Sunday — both labels are carried in every output file).

## 1. Task 1 — Merge and final table

**Procedure.** The two collector tables (336 candidate events 2018-2021, 262 candidate events 2022-2026, 598 in total) were merged; every verifier correction (Agent 2 §5) was applied; only rows meeting the confirmed-paid standard were retained — *the same standard in both eras*: meeting approval + record date passed + ≥2 independent sources listing the payment as closed + no reversal/cancellation report. Result: **587 confirmed-paid record dates** (334 in 2018-2021, 253 in 2022-01-01…2026-08-31) across **63 companies**; **12 companies with zero confirmed dividends** in the whole sample (JNOS, MFGS, RNFT, VJGZ, BLNG, CHMK, ROLO, UKUZ, UNAC, FESH, UTAR, MRKK). Per year: 2018: 81, 2019: 90, 2020: 81, 2021: 82, 2022: 45, 2023: 57, 2024: 64, 2025: 49, 2026: 38.

**Date type.** Every date in `final_dividend_table.csv` is a **record date** (дата закрытия реестра). No ex-dividend or last-day-to-buy date was substituted anywhere; the three cases where a collector source carried a last-trading-day in its record-date field (GCHE 2023-10-01, PHOR 2024-09-22, VSMO 2023-06-05) were resolved to the legal record date by the verifier. 38 confirmed record dates fall on a Saturday/Sunday (legal and common in Russia) — e.g. AKRN 2024-05-19, ALRS 2018-07-14, ALRS 2021-07-04, ALRS 2024-10-19, AVAN 2018-07-08, AVAN 2021-12-12, GCHE 2019-04-07, GCHE 2021-10-03.

**Source support.** 525 rows are supported by four independent sources (smart-lab, dohod, T-Bank, ЗакрытияРеестров.рф), 54 by three, 8 by two. The two-source rows and the extra evidence behind each:

* AVAN 2018-07-08 (6.2 RUB; DH,ZR) — DH only; DH + ЗакрытияРеестров.рф + investfuture.ru meeting calendar (AGM 27.06.2018 -> record 08.07.2018)
* AVAN 2019-06-25 (11.15 RUB; DH,ZR) — DH only; DH + ЗакрытияРеестров.рф + investfuture.ru meeting calendar (EGM 14.06.2019 -> record 25.06.2019)
* RGSS 2021-01-12 (0.025 RUB; SL,ZR) — SL only; SL + ЗакрытияРеестров.рф (0.025013777 RUB from retained earnings)
* RGSS 2024-03-18 (0.004101 RUB; SL,ZR) — SL only; SL + ЗакрытияРеестров.рф (0.00410062 RUB from retained earnings)
* RGSS 2026-06-15 (0.004086 RUB; SL,ZR) — SL only; SL + ЗакрытияРеестров.рф (0.00408577 RUB from retained earnings)
* USBN 2024-06-04 (0.027379 RUB; SL,ZR) — SL only; SL + ЗакрытияРеестров.рф (0.027378645 RUB, FY2023); Finmarket/Bosfera 2025-26 confirm FY2023 was paid and FY2024 was skipped
* USBN 2026-07-06 (0.019993 RUB; SL,ZR) — SL only; SL + ЗакрытияРеестров.рф; Bosfera 29.06.2026: AGM approved ~0.02 RUB (7.2 bn RUB) for FY2025
* VSMO 2022-07-11 (563.76 RUB; DH,ZR) — DH only; DH + ЗакрытияРеестров.рф + investfunds.ru; Interfax 03.07.2025 citing NSD data: 563.76 RUB accrued for FY2021. smart-lab omits the row (its annual table is also inconsistent)

**Every exclusion, explicitly (18 entries — `excluded_entries.csv`):**

| Ticker | Announced record date | Amount | Period | Category | Evidence / reason |
|---|---|---|---|---|---|
| AVAN | 2018-06-12 | SL:34.42 | 4кв 2017 | unverifiable-single-source | smart-lab-only row (34.42 RUB, 12.06.2018) contradicted by AGM calendar (investfuture.ru: AGM 27.06.2018 -> record 08.07.2018, 6.20 RUB, confirmed by DH and ЗакрытияРеестров.рф); treated as a smart-lab data error |
| CHMF | not set (recommendation withdrawn) | 109.81 | Q4-2021 | announced-not-paid | Board recommendation for Q4-2021 (109.81 RUB) withdrawn in spring 2022; AGM 2022 resolved no dividend; no record date was ever established |
| GAZP | 2022-07-20 | SL:52.53 | 2021 год | announced-not-paid | Board recommended 52.53 RUB (FY2021); AGM 30.06.2022 voted against; never paid (SL row flagged cancelled; absent from DH/TB/ЗакрытияРеестров.рф) |
| MAGN | 2022-04-01 | SL:3.55 | 4кв 2021 | announced-not-paid | Board (25.02.2022) recommended 3.55 RUB for Q4-2021; recommendation cancelled 24.05.2022 (RBC); AGM July 2022 decided no FY2021 dividend |
| MGNT | 2025-01-09 | 560 | 9M-2024 | announced-not-paid | Board recommended 560 RUB, record 09.01.2025; EGM 26.12.2024 declared not held (no quorum, Alfa-Investments / AVO); never paid. FY2024 and FY2025: AGMs resolved no dividend |
| MRKU | 2022-10-14 | TB:0.0249 |  | source-error-duplicate | TB-only row 0.0249 RUB with recordDate 14.10.2022 duplicates the FY2021 dividend whose record date was 28.06.2022 (SL, DH, ЗакрытияРеестров.рф); dropped as a T-Bank data error |
| MSNG | 2025-07-08 | 0.226 | FY2024 (1st attempt) | announced-not-paid | Board recommended 0.226 RUB (record 08.07.2025 per ЗакрытияРеестров.рф); AGM June 2025 did not obtain required votes; re-proposed and rejected again at EGM 19.02.2026 (see MSNG 2026-03-03) |
| MSNG | 2026-03-03 | SL:0.226064 | 2024 год | announced-not-paid | Board recommended 0.226 RUB (FY2024) a second time; EGM 19.02.2026 voted against (>80% against, Kommersant/Peretok); never paid |
| MSTT | 2019-12-23 | SL:11.29/TB:11.29 | 3кв 2019 | announced-not-paid | Board (06.11.2019) recommended 11.29 RUB for 9M-2019; EGM 12.12.2019 declared not held (no quorum, BCS Express); never paid. TB lists it as paid -> TB error |
| NLMK | 2023-01-11 | SL:2.6 | 3кв 2022 | announced-not-paid | Board recommended 2.6 RUB for 9M-2022; EGM 31.12.2022 rejected (98% against, Interfax); never paid |
| NLMK | not set (recommendation withdrawn) | 12.18 | Q4-2021 | announced-not-paid | Board recommendation for Q4-2021 (12.18 RUB) withdrawn in 2022; AGM 2022 resolved no dividend for 2021 |
| OGKB | 2025-07 (planned, not set) | 0.0598 | FY2024 (1st attempt) | announced-not-paid | AGM June 2025 did not obtain required votes; board re-recommended 0.0598 RUB in Sept 2025 and the EGM approved it in Oct 2025 -> paid with record date 05.11.2025 (included in the confirmed table) |
| PHOR | 2023-10-11 | SL:126.0 | 2кв 2023 | announced-not-paid | Board recommended 126 RUB for H1-2023; EGM 30.09.2023 did not adopt the decision (Interfax/TASS); later replaced by 291 RUB (record 25.12.2023, paid) |
| PHOR | 2025-07-05 | SL:201.0 | 1кв 2025 | announced-not-paid | Board recommended 201 RUB for Q1-2025; EGM 27.06.2025 did not adopt (Reuters/Vedomosti); never paid |
| PLZL | 2023-06-16 | SL:42.87 | 2022 год | announced-not-paid | Board recommended 436.79 RUB (FY2022); AGM 06.06.2023 failed for lack of quorum; repeat AGM 07.07.2023 resolved not to pay (Interfax/AKM) |
| TGKA | 2022-07-18 | SL:0.001125 | 2021 год | announced-not-paid | Board recommended 0.001125 RUB (FY2021); AGM (results 04-05.07.2022) did not obtain required votes (RBC); never paid |
| TGKA | 2025-07-08 | 0.000828802 | FY2024 | announced-not-paid | Board recommended ~3.2 bn RUB (0.000828802 RUB/sh, record 08.07.2025 per ЗакрытияРеестров.рф); AGM 19.06.2025 did not obtain required votes (Peretok/Neftegaz); never paid |
| YNDX | 2026-09-21 (outside window; pending) | n/a | H1-2026 | outside-window-pending | ЗакрытияРеестров.рф queue lists a Yandex record date 21.09.2026 (after the 2026-08-31 cut-off and after today's date); not confirmable -> not used. Its 4-week pre-window would cover panel weeks 2026-08-24 and 2026-08-31 (edge effect, documented in the Agent-3 report) |

Nothing else was excluded. In particular, no row was removed because of its amount, its source count (all ≥2 after verification) or its timing relative to any other variable.

**Amount conventions (documentation only — amounts are not used by Div):** per-share amounts are as reported by the agreeing sources (split/consolidation-adjusted where the sources are: VTBR ×5000 consolidation basis, GMKN and PLZL post-split basis); same-day multi-component approvals are summed; RUAL 2022 is recorded in USD as declared. Open amount ambiguity: AVAN 2025-04-28 (28.50 vs 21.07 RUB) — flagged, date not in doubt.

## 2. Task 2 — Construction of Div(i,t)

**Definition implemented.** For each confirmed record date R of company i, let W_R be the Monday of the calendar week containing R. `Div(i,t) = 1` for the four calendar weeks W_R−28d, W_R−21d, W_R−14d, W_R−7d (the four full Monday-to-Sunday weeks immediately preceding the record-date week), and 0 elsewhere. The record-date week itself is **not** in the window (it contains the ex-dividend gap under both T+2 and T+1 settlement and is the natural boundary of a "pre-record-date" window); it is shipped separately as `RecordWeek(i,t)` (`Div_record_week.csv`, and a column in the long file) so that the convention can be changed downstream without re-collecting anything. Overlapping pre-windows of consecutive dividends of the same company are **unioned**: a week is 1 or 0, never 2.

**Numbers.** 587 confirmed record dates → 529 have at least one pre-window week inside the panel (528 have all four; the remaining 58 are 2018 record dates whose windows lie entirely before the first panel week); naive sum of window-weeks 2113, of which 3 company-weeks were claimed by two consecutive windows and counted once → **2110 company-weeks with Div = 1 (6.71% of the 31,425 panel cells)**; 61 companies have at least one Div=1 week (IRKT and MSTT have confirmed dividends only in mid-2018, before the panel starts; the 12 zero-dividend companies have none).

**Files.** `Div_i_t.csv` (419 rows × 75 ticker columns, first two columns = Monday and Sunday week labels), `Div_i_t_long.csv` (ticker, week, Div, RecordWeek), `Div_window_log.csv` (one row per record date with the four window weeks), `Div_record_week.csv`.

**Edge effects, disclosed.** (a) Record dates 2018-01-01…2018-09-23 have windows partly/entirely before week 1 — truncated, not shifted. (b) Record dates after 2026-08-31 are outside the collection brief; the only one known for a panel company is YNDX 2026-09-21 (pending, not confirmable today) — its window would set Div=1 for YNDX in the last two panel weeks (2026-08-24, 2026-08-31). Those two cells are 0 in the shipped file; if the Yandex dividend is later confirmed paid, the author may set them to 1 — a documented, mechanical change that does not depend on any result.

## 3. Task 3 — Cross-reference with Crisis, Sanction and News layers (documentation only)

Nothing below changed a single Div cell or any other variable. Definitions used: Crisis = the four Group-1 windows converted to Monday labels (W1 2020-02-24→04-13, W2 2022-02-21→03-28, W3 2023-09-04→09-18, W4 2026-06-22→07-27); Sanction = each of the 34 Group-1 anchors' week plus the four following weeks (the "strict" 5-week reaction window used by Group 4; ROSN/LKOH anchored at the first (UK, 2025-10-15) designation); News = `News_i_t.csv` from Group 4 — **that week-level file was not available to this group** (it is not in the shared repository), so the News column is reported as n/a; `lib/build_final.py` computes it automatically if `output/News_i_t.csv` is dropped into the folder. From Group 4's published event list the only dividend-related News events (GMKN-3R 2021-03-24 policy shock, MTSS-1R payout resumption, VTBR-1 payout-policy event) fall outside the affected companies' 4-week pre-windows by construction of Group 4's own bar (ex-date mechanics excluded), so material News overlap is not expected, but it must be verified with the file.

**Totals:** Div=1 ∩ Crisis = **85 company-weeks** (4.0% of Div=1 cells; 4.9% of the 1,725 crisis cells); Div=1 ∩ Sanction = **3 company-weeks**. The crisis overlap is concentrated in **W4 (2026-06-21→07-26)**, which coincides with the AGM dividend season: 55 of the 85 overlapping cells; W1 (Feb-Apr 2020) 15, W3 (Sept 2023) 13, W2 (Feb-Mar 2022) 2 — the 2022 window overlaps almost nothing because dividends collapsed in spring 2022.

Crisis overlap detail (company week-start):
* **W1** (15 company-weeks): AVAN 2020-03-30, AVAN 2020-04-06, AVAN 2020-04-13, AKRN 2020-03-16, AKRN 2020-03-23, AKRN 2020-03-30, AKRN 2020-04-06, GCHE 2020-03-09, GCHE 2020-03-16, GCHE 2020-03-23, GCHE 2020-03-30, NVTK 2020-04-06, NVTK 2020-04-13, LSRG 2020-04-13, TTLK 2020-04-13
* **W2** (2 company-weeks): AKRN 2022-02-21, AKRN 2022-02-28
* **W3** (13 company-weeks): AVAN 2023-09-04, AVAN 2023-09-11, AVAN 2023-09-18, BSPB 2023-09-11, BSPB 2023-09-18, GCHE 2023-09-04, GCHE 2023-09-11, GCHE 2023-09-18, NVTK 2023-09-11, NVTK 2023-09-18, TATN 2023-09-11, TATN 2023-09-18, ALRS 2023-09-18
* **W4** (55 company-weeks): SBER 2026-06-22, SBER 2026-06-29, SBER 2026-07-06, SBER 2026-07-13, USBN 2026-06-22, USBN 2026-06-29, VTBR 2026-06-22, VTBR 2026-06-29, VTBR 2026-07-06, VTBR 2026-07-13, AKRN 2026-07-13, AKRN 2026-07-20, AKRN 2026-07-27, KZOS 2026-06-22, NKNC 2026-06-22, ABRD 2026-06-22, ABRD 2026-06-29, BANE 2026-06-22, BANE 2026-06-29, BANE 2026-07-06, ROSN 2026-06-22, ROSN 2026-06-29, SIBN 2026-06-22, SIBN 2026-06-29, SNGS 2026-06-22, SNGS 2026-06-29, SNGS 2026-07-06, TATN 2026-06-22, TATN 2026-06-29, TATN 2026-07-06, PLZL 2026-06-22, PLZL 2026-06-29, PLZL 2026-07-06, LSRG 2026-06-22, LSRG 2026-06-29, LSRG 2026-07-06, MTSS 2026-06-22, MTSS 2026-06-29, RTKM 2026-06-22, RTKM 2026-06-29, RTKM 2026-07-06, RTKM 2026-07-13, AFLT 2026-06-22, AFLT 2026-06-29, AFLT 2026-07-06, NMTP 2026-06-22, NMTP 2026-06-29, NMTP 2026-07-06, MRKC 2026-06-22, MRKP 2026-06-22, MRKU 2026-06-22, MSNG 2026-06-22, MSNG 2026-06-29, MSNG 2026-07-06, MSRS 2026-06-22

Sanction overlap detail:
* **anchor 2022-10-06**: GAZP 2022-10-03, TATN 2022-10-03
* **anchor 2026-04-22**: AVAN 2026-04-20

**Robustness-check candidates (flag rule: an entire 4-week pre-window inside a crisis/sanction window, i.e. ≥4 overlapping weeks, or ≥25 % of the company's Div=1 weeks overlapping):** AVAN, SBER, USBN, VTBR, AKRN, GCHE, NVTK, TATN, LSRG, RTKM, AFLT. For SBER, VTBR, RTKM, TATN, AFLT, LSRG and USBN the overlap is the 2026 AGM-season window inside W4; for GCHE, LSRG, NVTK, AKRN and AVAN it is the spring-2020 window (W1) and/or W3; AKRN also touches W2 (record 2022-03-09). The recommended check — re-estimating H5 with these company-windows dropped or with a Crisis×Div interaction — is **not run here** and is not a Group-5 deliverable.

Full per-company overlap table (`overlap_table.csv`, detail in `overlap_detail.csv`):

| Ticker | Sector | Confirmed dividends 2018-2021 | 2022-2026 | Total | Div=1 weeks | Crisis-overlap weeks | Sanction-overlap weeks | Flag |
|---|---|---|---|---|---|---|---|---|
| AVAN | Banking | 13 | 12 | 25 | 92 | 6 (W1,W3) | 1 (2026-04-22) | ROBUSTNESS-CHECK CANDIDATE |
| BSPB | Banking | 4 | 8 | 12 | 44 | 2 (W3) | 0 |  |
| CBOM | Banking | 1 | 0 | 1 | 4 | 0 | 0 |  |
| SBER | Banking | 4 | 4 | 8 | 28 | 4 (W4) | 0 | ROBUSTNESS-CHECK CANDIDATE |
| USBN | Banking | 0 | 2 | 2 | 8 | 2 (W4) | 0 | ROBUSTNESS-CHECK CANDIDATE |
| VTBR | Banking | 4 | 2 | 6 | 20 | 4 (W4) | 0 | ROBUSTNESS-CHECK CANDIDATE |
| AKRN | Chemicals | 11 | 5 | 16 | 56 | 9 (W1,W2,W4) | 0 | ROBUSTNESS-CHECK CANDIDATE |
| KAZT | Chemicals | 8 | 8 | 16 | 56 | 0 | 0 |  |
| KZOS | Chemicals | 5 | 5 | 10 | 36 | 1 (W4) | 0 |  |
| NKNC | Chemicals | 4 | 5 | 9 | 36 | 1 (W4) | 0 |  |
| PHOR | Chemicals | 17 | 10 | 27 | 96 | 0 | 0 |  |
| ABRD | Consumer&Retail | 4 | 5 | 9 | 32 | 2 (W4) | 0 |  |
| GCHE | Consumer&Retail | 8 | 6 | 14 | 52 | 7 (W1,W3) | 0 | ROBUSTNESS-CHECK CANDIDATE |
| MGNT | Consumer&Retail | 8 | 2 | 10 | 36 | 0 | 0 |  |
| MVID | Consumer&Retail | 4 | 0 | 4 | 16 | 0 | 0 |  |
| AFKS | Diversified | 4 | 2 | 6 | 20 | 0 | 0 |  |
| SFIN | Diversified | 2 | 6 | 8 | 28 | 0 | 0 |  |
| BANE | Energy | 3 | 5 | 8 | 28 | 3 (W4) | 0 |  |
| GAZP | Energy | 4 | 1 | 5 | 16 | 0 | 1 (2022-10-06) |  |
| JNOS | Energy | 0 | 0 | 0 | 0 | 0 | 0 |  |
| LKOH | Energy | 8 | 8 | 16 | 60 | 0 | 0 |  |
| MFGS | Energy | 0 | 0 | 0 | 0 | 0 | 0 |  |
| NVTK | Energy | 8 | 9 | 17 | 64 | 4 (W1,W3) | 0 | ROBUSTNESS-CHECK CANDIDATE |
| RNFT | Energy | 0 | 0 | 0 | 0 | 0 | 0 |  |
| ROSN | Energy | 7 | 9 | 16 | 60 | 2 (W4) | 0 |  |
| SIBN | Energy | 8 | 9 | 17 | 64 | 2 (W4) | 0 |  |
| SNGS | Energy | 4 | 5 | 9 | 32 | 3 (W4) | 0 |  |
| TATN | Energy | 9 | 14 | 23 | 88 | 5 (W3,W4) | 1 (2022-10-06) | ROBUSTNESS-CHECK CANDIDATE |
| VJGZ | Energy | 0 | 0 | 0 | 0 | 0 | 0 |  |
| KMAZ | Industrial | 2 | 2 | 4 | 12 | 0 | 0 |  |
| RGSS | Insurance | 1 | 2 | 3 | 12 | 0 | 0 |  |
| ALRS | Metals&Mining | 7 | 3 | 10 | 36 | 1 (W3) | 0 |  |
| BLNG | Metals&Mining | 0 | 0 | 0 | 0 | 0 | 0 |  |
| CHMF | Metals&Mining | 13 | 3 | 16 | 60 | 0 | 0 |  |
| CHMK | Metals&Mining | 0 | 0 | 0 | 0 | 0 | 0 |  |
| GMKN | Metals&Mining | 8 | 3 | 11 | 40 | 0 | 0 |  |
| MAGN | Metals&Mining | 13 | 3 | 16 | 53 | 0 | 0 |  |
| NLMK | Metals&Mining | 16 | 1 | 17 | 60 | 0 | 0 |  |
| PLZL | Metals&Mining | 8 | 6 | 14 | 52 | 3 (W4) | 0 |  |
| RASP | Metals&Mining | 5 | 1 | 6 | 24 | 0 | 0 |  |
| ROLO | Metals&Mining | 0 | 0 | 0 | 0 | 0 | 0 |  |
| RUAL | Metals&Mining | 0 | 1 | 1 | 4 | 0 | 0 |  |
| SELG | Metals&Mining | 4 | 4 | 8 | 32 | 0 | 0 |  |
| TRMK | Metals&Mining | 5 | 5 | 10 | 36 | 0 | 0 |  |
| UKUZ | Metals&Mining | 0 | 0 | 0 | 0 | 0 | 0 |  |
| VSMO | Metals&Mining | 4 | 3 | 7 | 24 | 0 | 0 |  |
| LSRG | Real Estate | 5 | 4 | 9 | 32 | 4 (W1,W4) | 0 | ROBUSTNESS-CHECK CANDIDATE |
| MSTT | Real Estate | 1 | 0 | 1 | 0 | 0 | 0 |  |
| PIKK | Real Estate | 4 | 0 | 4 | 13 | 0 | 0 |  |
| IRKT | Tech | 1 | 0 | 1 | 0 | 0 | 0 |  |
| UNAC | Tech | 0 | 0 | 0 | 0 | 0 | 0 |  |
| YNDX | Tech | 0 | 4 | 4 | 16 | 0 | 0 |  |
| MGTS | Telecom | 2 | 0 | 2 | 4 | 0 | 0 |  |
| MTSS | Telecom | 9 | 5 | 14 | 52 | 2 (W4) | 0 |  |
| RTKM | Telecom | 5 | 5 | 10 | 36 | 4 (W4) | 0 | ROBUSTNESS-CHECK CANDIDATE |
| TTLK | Telecom | 4 | 5 | 9 | 32 | 1 (W1) | 0 |  |
| AFLT | Transportation | 2 | 2 | 4 | 12 | 3 (W4) | 0 | ROBUSTNESS-CHECK CANDIDATE |
| FESH | Transportation | 0 | 0 | 0 | 0 | 0 | 0 |  |
| NMTP | Transportation | 5 | 5 | 10 | 40 | 3 (W4) | 0 |  |
| UTAR | Transportation | 0 | 0 | 0 | 0 | 0 | 0 |  |
| FEES | Utilities | 5 | 0 | 5 | 16 | 0 | 0 |  |
| HYDR | Utilities | 4 | 2 | 6 | 20 | 0 | 0 |  |
| IRAO | Utilities | 4 | 5 | 9 | 32 | 0 | 0 |  |
| LSNG | Utilities | 4 | 6 | 10 | 36 | 0 | 0 |  |
| MRKC | Utilities | 4 | 6 | 10 | 36 | 1 (W4) | 0 |  |
| MRKK | Utilities | 0 | 0 | 0 | 0 | 0 | 0 |  |
| MRKP | Utilities | 4 | 6 | 10 | 36 | 1 (W4) | 0 |  |
| MRKS | Utilities | 3 | 0 | 3 | 8 | 0 | 0 |  |
| MRKU | Utilities | 4 | 6 | 10 | 36 | 1 (W4) | 0 |  |
| MSNG | Utilities | 4 | 4 | 8 | 28 | 3 (W4) | 0 |  |
| MSRS | Utilities | 5 | 6 | 11 | 40 | 1 (W4) | 0 |  |
| OGKB | Utilities | 4 | 3 | 7 | 24 | 0 | 0 |  |
| TGKA | Utilities | 4 | 0 | 4 | 12 | 0 | 0 |  |
| UPRO | Utilities | 8 | 0 | 8 | 28 | 0 | 0 |  |
| YAKG | Utilities | 1 | 0 | 1 | 4 | 0 | 0 |  |

## 4. Cross-era consistency check

Both eras were processed with the same pipeline (`lib/merge2.py` → `lib/build_final.py`), the same clustering rule (±4 days on record date), the same confirmed-paid standard and the same 4-week convention. 2022-2026 required more exclusions (16 of the 18 excluded entries belong to 2022-2026, 2 to 2018-2021) because failed and withdrawn dividend decisions were common in that era; that is a property of the period, not of the method. Multiple record dates per company-year occur in both eras (2018-2021: 85 company-years with ≥2 confirmed record dates; 2022-2026: 54).

## 5. Integrity statement (Agent 3)

1. **No record date was included, excluded or adjusted on the basis of what it does to any regression, to ASVI, to volatility or to H5.** No regression was estimated and no outcome data were opened during the construction of this variable. Every inclusion follows the documentary rule in §1; every exclusion is listed in §1 with its evidence; every date change is listed in Agent 2 §5 with its source.
2. **Identical standard in both periods:** the confirmed-paid standard (meeting approval + record date passed + ≥2 independent listings + no reversal) and the date type (record date) were applied to 2018-2021 and 2022-2026 alike; the 2022-2026 table separates announced-but-unpaid items rather than dropping them silently.
3. **Ambiguities were documented, not resolved for convenience:** the AVAN 2025 amount, the depositary-sourced VSMO 2024 amount, the pending YNDX September-2026 record date and the News-layer overlap (file unavailable) are stated as open items above.
4. **Overlaps with Crisis, Sanction and News layers are disclosed and left in place**; no window was trimmed, merged or moved because of them, and the robustness check is flagged, not executed.
5. **Reproducibility:** `work/*_rows.json` (raw scrapes of the three collector sources), `work/zr_company.json` (register), `lib/merge2.py`, `lib/build_final.py` (all corrections as explicit tables), `lib/write_reports_*.py`; re-running the two build scripts regenerates every file in `output/`.
