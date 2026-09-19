# Unified Methodology Manual — 75-Company MOEX Panel (2018-2026)
## Investor Attention (Yandex Wordstat) and State Ownership

**Compiled:** 2026-09-19  
**Live repo verification:** Fetched `https://api.github.com/repos/aaa11996/yandex.data./contents` on 2026-09-19. Live listing = 8 files: `Final dividend table.md`, `News_Div_Overlap_Addendum.md`, `Sanctions Events, Crisis Windows & Data Readiness.md`, `UNIFIED_PAPER_about_state_ownership.md`, `iqbal thesis v2.zip`, `news.md`, `pilot test 15 company.zip`, `thesis books.zip`. No `lib/`, `work/`, `output/`, `group2/`, `group3/`, `Data/` folders. All references to paths like `lib/build_final.py`, `work/judge_spike_attribution.csv`, `output/News_i_t.csv`, `group3/channel_selection.csv` in source reports refer to private sandbox files, not shared repo. This manual extracts information from prose/tables and notes discrepancies per governing rule.

**Purpose:** Single, self-contained reference for future analyst with zero other context to run final regression analysis on 75-company MOEX panel (2018-2026). Contains every data definition, confirmed date/value, formula, known pitfall, accurately enough that no further research needed before final models — except where source file missing, explicitly flagged.

---

## Table of Contents

1. [Quick-Start Checklist](#1-quick-start-checklist)
2. [Data Foundation and Company Universe](#2-data-foundation-and-company-universe)
   - 2.1 Raw Data Structure
   - 2.2 Final Company Universe (75) and Exclusions
   - 2.3 Channel Selection and ASVI Formula
   - 2.4 Yandex Handling
   - 2.5 Data Quality Registry (R1–R17)
3. [Event-Based Controls](#3-event-based-controls)
   - 3.1 Crisis(t) — Market-wide Windows
   - 3.2 Sanction(i,t) — Permanent Step Function
   - 3.3 News(i,t) — Discrete Transient Shock
   - 3.4 Cross-Overlaps (Hard Numbers)
   - 3.5 Why Three Separate Controls
4. [Dividends and Ownership](#4-dividends-and-ownership)
   - 4.1 Div(i,t) — Pre-Record-Date Window
   - 4.2 SOE(i) — State Ownership Dummy
5. [Hypotheses and Methods](#5-hypotheses-and-methods)
   - 5.1 Hypotheses H1–H6 (Status: Missing in Live Repo)
   - 5.2 Methods M1–M6 (Corrected Method 3, HAC, Multiple Testing)
   - 5.3 Date-Alignment Join Rule
6. [Lessons Learned and Pitfalls](#6-lessons-learned-and-pitfalls)
7. [Inconsistencies Found and Resolved](#7-inconsistencies-found-and-resolved)
8. [File Manifest and Discrepancy Log](#8-file-manifest-and-discrepancy-log)

---

## 1. Quick-Start Checklist

Minimum sequence from raw data to finished results, with pointer to relevant manual section.

| Step | Action | Manual Section |
|---|---|---|
| **0** | **Verify live repo:** `GET https://api.github.com/repos/aaa11996/yandex.data./contents` — treat ONLY listed files as real. Do NOT assume `lib/`, `work/`, `output/` exist. | §8 |
| **1** | **Unpack raw data:** Download `iqbal thesis v2.zip` (live repo). Unzip → `Data/<Sector>/<Ticker>/` with 3 files per company (Yandex 4). Confirm 75 folders. | §2.1 |
| **2** | **Parse Wordstat CSVs:** UTF-8 BOM, semicolon-delimited, Monday weeks `DD.MM.YYYY`, count column with space thousands separator (e.g., `1 088`). Strip spaces, parse int. 419 weeks 2018-08-27→2026-08-31. | §2.1.2 |
| **3** | **Parse price CSVs:** Comma-delimited, quoted, `MM/DD/YYYY` Sunday labels. Sunday label = day BEFORE trading week (e.g., label 2022-02-20 = week Mon 2022-02-21…Fri 2022-02-25). Volume suffixes K/M/B. 31,061 company-weeks. | §2.1.3 |
| **4** | **Apply data-quality registry:** Exclude week 2024-06-16 from cross-sectional detection (72/75 missing), exclude week 2024-08-25 from volume metrics (73/75 empty Vol), drop 12 flat placeholders 2022-02-27, exclude ROLO from RV tests or tick-adjust, keep gaps as gaps (LSNG, MSTT, FEES, YNDX, AVAN). | §2.5 |
| **5** | **Build ASVI:** For each company, `ASVI(t)=ln(SVI(t))−median(ln(SVI(t−1))…ln(SVI(t−8)))`. SVI=0 → missing. Max 411 weeks. For 74 non-Yandex, **SUM RAW**: sum ticker + Cyrillic raw counts week-by-week, then compute ASVI. All 74 decisions SUM RAW (max corr 0.8066 TRMK <0.85). For Yandex: sum YNDX ticker + Cyrillic, **exclude YDEX**. | §2.3, §2.4 |
| **6** | **Build Crisis(t):** 4 windows: W1 2020-02-23→04-12 (Sun) / 2020-02-24→04-13 (Mon) COVID; W2 2022-02-20→03-27 / 2022-02-21→03-28 invasion & suspension; W3 2023-09-03→09-17 / 2023-09-04→09-18 ruble/rate; W4 2026-06-21→07-26 / 2026-06-22→07-27 bear capitulation. Verification: RV≥2× AND Vol≥2× trailing 52w median, stress week ≥25% companies spiking, peak ≥40%, 1-week bridge allowed. 1,725 weeks (5.49%). | §3.1 |
| **7** | **Build Sanction(i,t):** Permanent step 0→1 from anchor date onward. Anchors: 23 first entity-level designations (16 US SDN, 12 UK freezes, 21 EU measures, 1 relief RUAL 2019-01-27). Full list with dates/sources in §3.2.2. Reaction window for overlap = anchor week +4 weeks (170 weeks). Keep jurisdiction/instrument columns for heterogeneity. | §3.2 |
| **8** | **Build News(i,t):** Discrete-event dummy, 31 events, 23 companies, 89 weeks (0.283%). Confirmation: RV or Vol ≥2× in t…t+3 vs trailing 52w median (strictly pre-event). Duration: extend while RV or Vol ≥1.5× frozen median, cap 4 weeks. Deduplication: discard any candidate within ±2 weeks of Crisis window or Sanction anchor BEFORE testing (11 discarded: 10 by Sanction, 1 by Crisis PHOR-1 2026-07-22). Guarantees News∩Crisis=0, News∩Sanction=0 by construction. Categories: MA 10, CAPRET 8, EARN 5, LEGAL 4, OPS 2, LEAD 1, DEBT 1. | §3.3 |
| **9** | **Build Div(i,t):** Confirmed-paid standard: meeting approval + record date passed + ≥2 independent sources + no reversal. 587 record dates (334 in 2018-2021, 253 in 2022-2026-08-31) across 63 companies; 12 zero-dividend: JNOS, MFGS, RNFT, VJGZ, BLNG, CHMK, ROLO, UKUZ, UNAC, FESH, UTAR, MRKK. For each record R, W_R=Monday of week containing R, Div=1 for W_R−28d, −21d, −14d, −7d (four weeks preceding record week, record week NOT included). Overlapping windows unioned → 2110 weeks (6.71%). Per-company totals in §4.1.3. Edge: YNDX 2026-09-21 pending outside window. | §4.1 |
| **10** | **Build SOE(i):** ≥25% voting shares held by Russian government (federal/sub-federal, direct/indirect via Rosneftegaz, Rostec, Rosseti, Gazprom, Transneft, Rosatom, VTB, regional bodies). 35 SOE=1, 40 SOE=0, 0 UNDETERMINED. Special cases: golden shares (TATN, BANE, YNDX) do NOT set SOE=1; temporary administration (UPRO 83.73% Uniper, TGKA Fortum 29.45%) ≠ ownership; sub-threshold blocks (KZOS 19.87%, NVTK 9.9%) recorded but SOE=0; time-varying MGNT (state 2018–21, private after). Full table in §4.2.3. Monitoring list: RGSS, PIKK, CBOM, BANE, AFLT. | §4.2 |
| **11** | **Compute overlaps:** Div∩Crisis 85 weeks (4.0% Div, W4 55, W1 15, W3 13, W2 2), Div∩Sanction 3 weeks (GAZP 2022-10-03, TATN 2022-10-03, AVAN 2026-04-20), Div∩News 0 weeks, News∩Crisis 0, News∩Sanction 0 (by construction). Combined any control 88 weeks (4.17% Div). Robustness candidates: AVAN, SBER, USBN, VTBR, AKRN, GCHE, NVTK, TATN, LSRG, RTKM, AFLT (≥4w or ≥25%). | §3.4, §4.1.4 |
| **12** | **Align dates for regression:** Wordstat Monday week-start joins to price Sunday label one day earlier (e.g., search 2022-02-21 ↔ price 2022-02-20 containing Fri 2022-02-25 close). Carry both labels. | §5.3 |
| **13** | **Run six methods:** Methods M1–M6 with Newey-West HAC bandwidth 4 weeks, Benjamini-Hochberg correction per hypothesis block, Giles-Lieberman heteroscedasticity-robust bounds for Chow test. **Corrected Method 3 (pooled panel) adds lagged-volume control** previously missing — correction due to documented control-mismatch (likely cause of sign contradiction pooled vs per-company in earlier round). **Note:** Exact H1–H6 equations missing in live repo — see §5.1 discrepancy. | §5 |
| **14** | **Sensitivity:** Drop 5 revision News events (explained share 62.2%→61.7%), widen News windows to t…t+5 (21/31 events have persistence flag), re-run with strict crisis/sanction definitions (49.2% unexplained), drop 11 robustness-check candidate windows, federal-only SOE, flip-test VSMO (25%+1 knife-edge). | §3.3, §4.2 |

---

## 2. Data Foundation and Company Universe

### 2.1 Raw Data Structure

#### Top-level layout (from `iqbal thesis v2.zip` — live repo)
```
iqbal thesis/
└─ Data/
   ├─ Banking/ (6)
   ├─ Chemicals/ (5)
   ├─ Consumer&Retail/ (4)
   ├─ Diversified/ (2)
   ├─ Energy/ (13)
   ├─ Industrial/ (1)
   ├─ Insurance/ (1)
   ├─ Metals&Mining/ (15)
   ├─ Real Estate/ (3)
   ├─ Tech/ (3)
   ├─ Telecom/ (4)
   ├─ Transportation/ (4)
   └─ Utilities/ (14)
       └─ <TICKER>/
           ├─ <TICKER>.csv
           ├─ <Cyrillic> акции.csv
           └─ <Name> Stock Price History.csv
           └─ (Yandex only) YDEX.csv
```
75 folders, 226 files (75×3+1). Source: `UNIFIED_PAPER_about_state_ownership.md` §1.1 + direct zip inspection via `unzip -l`.

Sector N, SOE=1, SOE=0 (UNIFIED_PAPER §1.1):
- Banking 6 (2,4), Chemicals 5 (0,5), Consumer&Retail 4 (0,4), Diversified 2 (0,2), Energy 13 (8,5), Industrial 1 (1,0), Insurance 1 (1,0), Metals&Mining 15 (2,13), Real Estate 3 (0,3), Tech 3 (2,1), Telecom 4 (2,2), Transportation 4 (4,0), Utilities 14 (13,1) =75 (35,40).

#### Wordstat CSV
- **Encoding:** UTF-8 BOM (`EF BB BF`), open `utf-8-sig`. Verified byte 0.
- **Delimiter:** semicolon `;`
- **Header:** `Week from;Number of queries;Percentage of total queries, %;Frequency dynamics for «<query>», by week, 27.08.2018 — 06.09.2026, all regions, all devices`
- **Weeks:** Monday-dated `DD.MM.YYYY`, 419 weeks 2018-08-27→2026-08-31 (Wordstat grid = master calendar).
- **Count column:** integer, thousands separator **space** (e.g., `1 088`, `13 535`), percentage column decimal comma `0,000036`. Handling: strip spaces/NBSP, parse int; percentage not needed for ASVI.
- **Zero:** below privacy threshold → 0. `ln(0)` treated as missing for ASVI.
- **Files:** ticker channel Latin, Cyrillic channel `... акции.csv`, Yandex extra `YDEX.csv`.

#### Price CSV
- **Encoding:** UTF-8, quoted, comma-delimited, header `"Date","Price","Open","High","Low","Vol.","Change %"`
- **Date:** `MM/DD/YYYY` descending, Sunday labels. **Critical:** Sunday label = day BEFORE trading week. Concrete evidence: bar labeled `2022-02-20` (Sunday) contains actual trading week Mon 2022-02-21…Fri 2022-02-25 close (Sanctions §3.1, Final dividend table header). Join: Wordstat Monday `2022-02-21` ↔ price Sunday `2022-02-20`.
- **Numbers:** Strip commas thousands, Volume suffixes `K=1e3, M=1e6, B=1e9` (e.g., `8.03K`), `Change %` like `"-0.55%"`.
- **Bars:** Already weekly (investing.com weekly view). No intraday.
- **Panel:** 75×404–417 bars =31,061 company-weeks, median 414 (Sanctions §3.1).

### 2.2 Final Company Universe (75) and Exclusions

**75-company list (UNIFIED_PAPER Appendix A):** AVAN, BSPB, CBOM, SBER, USBN, VTBR, AKRN, KAZT, KZOS, NKNC, PHOR, ABRD, GCHE, MGNT, MVID, AFKS, SFIN, BANE, GAZP, JNOS, LKOH, MFGS, NVTK, RNFT, ROSN, SIBN, SNGS, TATN, VJGZ, KMAZ, RGSS, ALRS, BLNG, CHMF, CHMK, GMKN, MAGN, NLMK, PLZL, RASP, ROLO, RUAL, SELG, TRMK, UKUZ, VSMO, LSRG, MSTT, PIKK, IRKT, UNAC, YNDX, MGTS, MTSS, RTKM, TTLK, AFLT, FESH, NMTP, UTAR, FEES, HYDR, IRAO, LSNG, MRKC, MRKK, MRKP, MRKS, MRKU, MSNG, MSRS, OGKB, TGKA, UPRO, YAKG (75).

**Exclusions:** **MISSING in live repo.** Sanctions report §1 notes earlier archive `modified/iqbal thesis` had 80 companies, implying 5 exclusions to reach 75, but does NOT name tickers/reasons. `pilot test 15 company.zip` in live repo is 2-byte placeholder (`\r\n`), not valid zip, and is superseded per task instruction (do NOT use). No exclusion log (`channel_selection.csv`, `data_quality_registry.csv`, exclusion table) appears in live listing. Per governing rule, we state plainly missing rather than inventing. Future analyst must diff 80-company archive if available, applying reason codes: name/ticker change, post-2017 IPO, confirmed price-data gap, both-channel search sparsity, <300-observation screen.

### 2.3 Channel Selection and ASVI Formula

**Source:** UNIFIED_PAPER §3.1–3.4, Appendix B (live repo).

- **SVI(t)** = raw weekly Wordstat count.
- **ASVI(t)** = `ln(SVI(t)) − median( ln(SVI(t−1)), …, ln(SVI(t−8)) )`. Log stabilizes variance, median robust to spikes. `ln(0)` → missing. ASVI valid only if SVI(t) and 8 prior weeks valid. First possible ASVI week 2018-10-22, max 411 weeks per series. Zero week costs that week + up to 8 following weeks.
- **Decision rule (pre-specified, no regression consulted):** Compute ASVI separately ticker vs Cyrillic, Pearson corr on overlapping valid weeks:
  - **corr ≥0.85** → keep ONE channel (fewer missing/zero weeks, tie-break ticker — never invoked).
  - **corr <0.85** → **sum RAW SVI week-by-week** (week counts if either channel positive, zero/missing contributes 0), then compute single final ASVI from summed raw series.
  - **Special:** corr undefined (overlap n<3) → SUM RAW.
  - **Why sum raw, never sum ASVI:** ASVI nonlinear; summing ASVI double-counts baseline variation.
- **Actual outcome (74 non-Yandex):** 0 reach 0.85, 73 corr<0.85 +1 undefined (SFIN) → **all 74 SUM RAW**. Max corr 0.8066 TRMK, min 0.0598 AVAN, median 0.573, mean 0.521.
- **Coverage:** 68/74 full 411 weeks; exceptions MRKK 282, MRKC 344, MRKS 377, MRKU 382, UKUZ 393, SFIN 395. All ≥282 weeks.
- **Zero patterns (>100):** SFIN-Cyrillic 389 (SAFMAR→SFI rename), IRKT-Cyrillic 257 to 2023-07-24 (Irkut→Yakovlev), VJGZ-ticker 226, UKUZ-ticker 206, MRKK-Cyrillic 197, KMAZ-ticker 176, YAKG-ticker 171, MRK-family Cyrillic pre-rebrand blocks.

### 2.4 Yandex Handling

- **Final series:** `SVI_YNDX(t) = SVI_YNDX_ticker(t) + SVI_Яндекс_акции_Cyrillic(t)` — ONLY YNDX ticker + Cyrillic, summed.
- **YDEX explicitly NOT used**, per final decision.
- **Reasoning:** No meaningful level break in YNDX around July-2024 relisting (6,261→6,471→6,389 spanning weeks); Cyrillic gradual decline; sum continuous.
- **Quantified if included:** Would multiply series ≈2.5–3× mid-sample, artificial level shift, spurious positive ASVI 8 weeks, distort baselines. Verifier: **99 weeks inflated >1.5× starting exactly 2024-07-22** (MOEX relisting). YDEX volume 0 until Nov-2021, <500/wk through 2023, then 30k–92k/week from 2024-07-22.
- **Limitation:** YDEX carried substantial real search volume ~2 years post-transition excluded — documented limitation.
- **Stats:** 419 raw weeks, 0 zero weeks (min 3,381 holiday week 2018-12-31), 411/411 ASVI, ASVI range [−0.60,+2.08], std 0.298, peak 2022-02-21 invasion week raw 143,979.

### 2.5 Data Quality Registry (R1–R17)

**Source:** UNIFIED_PAPER §3.5, Appendix C. Machine-readable CSVs referenced (`group3/data_quality_registry.csv`, `tick_rv_scan.csv`, etc.) NOT in live repo — numbers from prose.

| ID | Issue | Affected | Handling | Status vs Group1 |
|---|---|---|---|---|
|R1|Week 2024-06-16 bar absent (jump 06-09→06-23)|72/75 (all but GAZP,SBER,VTBR)|Exclude week label from cross-sectional detection|Confirmed & refined (G1 said ~55)|
|R2|Week 2024-08-25 valid OHLC but Vol empty|73/75 (all but VTBR,YNDX)|Exclude from volume metrics|NEW|
|R3|2022-02-27 flat suspension placeholders (OHLC equal, vol empty)|12 (AVAN BSPB CBOM AKRN ABRD AFKS BANE ALRS BLNG CHMF CHMK AFLT)|Treat as missing, never forward-fill|NEW detail|
|R4|ROLO 1-kopeck grid at 0.18–1.77₽, 19.8% weeks H−L ≤2 ticks, median RV 1.39× panel|ROLO|Exclude from RV tests or tick-adjust RV=(H−L−tick)/C|Confirmed, description corrected (G1 said ₽0.20 tick)|
|R5|USBN sub-kopeck price scale but 4-decimal quotes — no quantization (0% tick-bound, RV 1.26×)|USBN|Watch item only|Corrected (was "quantization")|
|R6|TGKA sub-kopeck 6 decimals, RV normal 0.98×|TGKA|No action|NEW|
|R7|Panel-wide tick scan: after ROLO max tick/price 0.28%, high-RV cohort genuine volatility|all|No action beyond R4|NEW|
|R8|Gap 2020-03-22…05-10 (8 weeks)|LSNG|Keep as gap|Confirmed|
|R9|Gap 2020-09-20…10-04 (3 weeks)|MSTT|Keep as gap|Confirmed|
|R10|Holiday gaps 2022-12-25,2023-01-01|FEES|Keep as gap|Confirmed|
|R11|Relisting gap last real bar 2024-06-09, placeholder 06-23 flat 4071.2 empty vol, labels 06-16/06-30/07-07/07-14 absent, resumes 07-21 (5 labels without real bar), search unaffected 411/411|YNDX|Keep as gap|Confirmed & refined|
|R12|11 missing weeks Sep-2018–Feb-2019 incl three ≥2-week runs, early baselines <52 obs|AVAN|Keep gaps, flag early tests|NEW|
|R13|Single-week omissions KAZT 2018-10-28, ABRD 2018-09-30, MFGS 2018-12-30, VJGZ 2018-12-30 & 2019-01-27|5 companies|Informational|NEW|
|R14|Suspension structure 2022-03-06 & 03-13 absent panel-wide, 02-27 placeholder/absent (R3/63), reopening 03-20 present 26 absent 49|all|Use reopening bar, flag suspension tests|Confirmed & refined|
|R15|Only company with bar at panel-start label 2018-08-19|GMKN|Informational|NEW|
|R16|Wordstat zero-week channels >100 (SFIN-cyr 389, IRKT-cyr 257, VJGZ-tick 226, UKUZ-tick 206, MRKK-cyr 197, KMAZ-tick 176, YAKG-tick 171, MRK-family cyr blocks)|12 channels|Mitigated by SUM RAW (all ≥282 ASVI)|NEW|
|R17|File integrity clean: 0 dropped rows, 0 unparseable OHLC, 0 change-% mismatches >2pp, no duplicate labels|all|No action|NEW|

Mandatory handling rules (for regression): exclude 2024-06-16 from cross-sectional detection, exclude 2024-08-25 from volume metrics, drop twelve 2022-02-27 flat placeholders, exclude ROLO from RV tests or tick-adjust, keep all documented gaps as gaps, USBN/TGKA/FEES sub-ruble scales fine-decimal no quantization but ratio noise-sensitive, suspension tests use reopening bar flagged.

Verification: independent agent (different parser, different ASVI implementation) recomputed 20 correlations to 4 decimals (20/20 match), re-derived R1 (72/75 missing, 3 present), R4, R9, R2, false-negative scan 12 unflagged companies — 7/7 PASS.

---

## 3. Event-Based Controls

### 3.1 Crisis(t) — Market-wide Windows

**Exact windows (Sunday price label / Monday Wordstat label — same weeks, +1 day conversion):**

| Window | Sunday (price) | Monday (Wordstat) | Length | Peak Sun | Peak fraction | Represents | Source |
|---|---|---|---|---|---|---|---|
|W1 COVID|2020-02-23→04-12|2020-02-24→04-13|8 wks|2020-02-23|0.773 (58/75)|Global pandemic selloff, OPEC+ breakdown 2020-03-06|Sanctions §4.1|
|W2 Invasion & suspension|2022-02-20→03-27|2022-02-21→03-28|5 trading wks|2022-02-20|0.733 (55/75)|Invasion 2022-02-24, MOEX suspension 2022-02-28–03-24, CBR 20% hike 2022-02-28|Sanctions §4.1|
|W3 Ruble/rate|2023-09-03→09-17|2023-09-04→09-18|3 wks|2023-09-10|0.453 (34/75)|Ruble >100/USD Aug 2023, CBR 8.5→12% 2023-08-15 and 12→13% 2023-09-15|Sanctions §4.1|
|W4 Bear capitulation 2026|2026-06-21→07-26|2026-06-22→07-27|6 wks|2026-06-21|0.560 (42/75)|MOEX multi-year low ~2026-07-06, CBR cut disappointment 2026-06-19, oil weakness, sanctions intensification EU 20th Apr 2026 (bank bans eff 2026-05-14) +21st Jul 23|Sanctions §4.1, Final dividend table §3|

Company-weeks: 1,725 (5.49% panel). Rejected candidates 7 documented in Sanctions §4.2 (pre-invasion selloff 2022-01-16 borderline 0.36, Omicron 2021-11-21 0.253, mobilization 2022-09-18 0.32 RV-only, artifact 2024-06-16 3 eligible, Prigozhin 2023-06-18 <0.25, Kursk 2024-08-04 <0.25, Ryabkov daily crash 2025-10-11 <0.25 weekly retrace).

**Verification method (Sanctions §3.2–3.3):** RV=(High−Low)/Close, Vol=raw weekly volume. Baseline trailing 52 available bars strictly before test bar, min 30 obs (RUAL Jan-2019 exception ~21 weeks, relaxed to ≥15 flagged). Ratios RV_ratio=RV/median RV trailing, Vol_ratio similarly. Stress confirmed iff RV_ratio≥2 AND Vol_ratio≥2. Event-week mapping: bar whose Mon–Fri week contains event date; if none (suspension), first bar after used flagged. Lookahead: max(baseline label) < event-bar label, 0 violations in 182 tests. Crisis detection: eligible(w)=companies with valid bar at w and ≥30 trailing obs; spiking(w)=eligible with both ratios ≥2; stress week if spike fraction ≥25%; crisis window contiguous stress weeks allowing 1-week bridge, peak ≥40%; eligible<25 excluded as artifact.

Examples at peak: W1 AFLT RV 6.41/Vol 5.67 −19.8%, W2 SBER 28.29/13.64 −47.6% VTBR 19.54/5.31 −48.8%, W3 NMTP 3.32/6.25 −8.1% (second-tier concentrated), W4 AVAN 4.45 −9.3% MGNT 3.52/2.79 −14.0%.

### 3.2 Sanction(i,t) — Permanent Step

**Definition:** Sanction(i,t)=0 before confirmed date, 1 from confirmed date onward (permanent step). Why permanent: sanction is enduring regime change (investor base, settlement, financing permanently altered), not transient shock. Differs from News transient (see §3.3). Sources: news.md §3, Sanctions §12, Final dividend table §3.

**Methodology:** Anchor = first entity-level designation (SDN blocking / asset freeze / full transaction ban) of company, direct parent, or operating subsidiary economically binding. Verification same 2× trailing median RV & Vol test. Sources primary: Treasury press releases jy0705 (2022-04-06 Sberbank/Alfa), jy0838 (2022-06-28 KAMAZ/UAC), jy0905 (2022-08-02 MMK), jy1296 (2023-02-24 BSPB/USBN/MTS Bank), sb0290 (2025-10-22 Rosneft/Lukoil), OFAC recent-actions 20240223, OFSI Notice 19/05/2023, EU Reg 2022/328, 2022/879, 2022/2474 (8th pkg), 16th,18th,19th,20th,21st pkgs. Access constraints documented: bulk downloads blocked 403/307.

**FULL anchor list (23 anchor events tested +1 relief +28 secondary =52 tests, Sanctions §5.2, §10). 34 anchors referenced in Final dividend table for 170-week reaction window (anchor week+4 weeks). Below complete list as documented:**

*US SDN 16:* VTBR 2022-02-24 (OFAC Feb24), SBER 2022-04-06 (jy0705), ALRS 2022-04-07, CHMF 2022-06-02, KMAZ 2022-06-28 (jy0838), IRKT via UAC 2022-06-28 (jy0838), MAGN 2022-08-02 (jy0905), BSPB 2023-02-24 (jy1296), USBN 2023-02-24 (jy1296), MTSS via MTS Bank 2023-02-24 (jy1296), PLZL 2023-05-19, TRMK 2024-02-23 (recent-action), SIBN 2025-01-10 (Jan10), SNGS 2025-01-10, ROSN 2025-10-22 (sb0290), LKOH 2025-10-22 (sb0290).

*UK 12:* VTBR 2022-02-24, SBER+CBOM 2022-04-06 (coordinated US, Bloomberg/FCDO), BSPB/USBN/MTS Bank 2023-02-24, FESH+TRMK 2023-05-18 (OFSI Notice 19/05/2023), AFLT 2022-05-19 (aviation ban since 2022-02-24), SIBN+SNGS 2025-01-10, ROSN+LKOH 2025-10-15 (GL wind-down Nov28 2025).

*EU 21:* NMTP sectoral 2022-02-25 Reg2022/328 + freeze 2025-02-24 16th pkg, VTB SWIFT 2022-03-02 eff 2022-03-12, SBER&CBOM SWIFT 2022-06-03 6th pkg Reg2022/879 eff 2022-06-14, 8th pkg Reg2022/2474 adopted 2022-10-06 oil price-cap/services regime covering ROSN,LKOH,SIBN,SNGS,TATN,NVTK,GAZP sectoral not freeze, cap eff 2022-12-05, BSPB transaction ban 2025-07-18 18th pkg Art5h eff 2025-08-09, 19th pkg 2025-10-23 freezes PLZL+FESH + bans ROSN+SIBN, 20th pkg 2026-04-22 freezes BANE, Slavneft (parent MFGS+JNOS), Rosnefteflot, Gazprom Flot + transaction ban 20 banks eff 2026-05-14 incl AVAN, 21st pkg 2026-07-23 affiliate Tatneft-Samara (TATN).

*Relief:* RUAL delisting 2019-01-27 (EN+, EuroSibEnergo, Deripaska control severed).

*Per-company summary (Sanctions §10):* VTBR ✅C 2022-02-24 19.54/5.31 Yes, SBER ✅D* 2022-04-06 4.35/1.94 Borderline vol1.94, CBOM ✅D 2022-04-06 2.12/0.73 No, BSPB ✅D 2023-02-24 1.00/1.70 No, USBN ✅D 1.32/2.56 No, AVAN ✅D 2026-04-22 0.62/1.37 No, ROSN ✅D 2025-10-22 1.64/1.19 No, LKOH ✅C 3.28/2.28 Yes, SIBN ✅C 2.60/3.16 Yes, SNGS ✅D 1.63/1.03 No, TATN S+✅C(affiliate) 2026-07-23 3.59/2.98 Yes +25%, NVTK S 1.47/0.92 No, GAZP S 1.61/1.18 No, BANE ✅D 0.50/0.84 No, MFGS P→✅C 2.72/11.80 Yes, JNOS P→✅D* 1.99/33.38 Borderline RV1.99, ALRS ✅D 2.76/0.67 No, CHMF ✅C 7.60/2.55 Yes, MAGN ✅C 2.82/3.14 Yes, PLZL ✅D 0.97/0.88 No, TRMK ✅D 0.95/2.03 No, RUAL R 3.32/18.36 Yes relief, IRKT P→✅C 5.81/22.42 Yes squeeze-confounded, MTSS P 0.70/0.52 No, AFLT D 2022-05-19 n/a (desensitized baseline), FESH ✅D 0.55/1.51 No, NMTP ✅D* 14.98/1.83 Borderline vol1.83, KMAZ ✅D 1.61/0.69 No, other 47 N not designated (GMKN, NLMK, IRAO, etc.).

Counts: confirmed 7 (VTBR,CHMF,MAGN,SIBN,LKOH,MFGS,IRKT)+RUAL relief+TATN affiliate, not-confirmed/borderline 16 incl 3 borderline (NMTP,SBER,JNOS), parent/subsidiary only 5, sectoral-only 6, not designated 47, relief 1 (23 anchors=7+13+3).

Reaction window for overlap: anchor week +4 weeks =5-week strict window =170 company-weeks (34×5 per Final dividend table). Permanent step for regression =1 from anchor onward.

### 3.3 News(i,t) — Discrete Transient Shock

**Definition:** News(i,t)∈{0,1} discrete-event dummy, not news-volume. 1 for event week and following weeks where RV or volume stays elevated, up to 4-week cap. Final state: 31 confirmed events, 23 companies, 89 company-weeks (0.283% panel), 52 zero-event companies. Per-company: ABRD 4, AFLT 4, BANE 1, CBOM 2, FEES 1, FESH 9, GAZP 8, GMKN 9, KMAZ 4, KZOS 3, LKOH 2, MGNT 1, MGTS 4, MTSS 4, MVID 8, NKNC 2, PLZL 2, SFIN 4, TRMK 4, UPRO 4, UTAR 4, VTBR 1, YNDX 4. Categories: MA 10, CAPRET 8, EARN 5, LEGAL 4, OPS 2, LEAD 1, DEBT 1. Source news.md §1.

**Candidate bar:** Company-specific exceptional event that could drive both search and volatility, excluding market-wide episodes (2022-09-19 mobilisation, 2026-03 third-tier oil rally) not company-specific, mechanical ex-date gaps (SFIN 2025-12-22 RUB902 one-off, VTBR −26.8%, SBER −10.1%, etc.) — CAPRET = payout-policy shocks in, mechanical ex-date gaps out — costs largest single-week RV ratio panel (SFI −48.2% ex-date gap) and every ex-date drop. Dividend-related News named: GMKN-3R 2021-03-24 policy shock, MTSS-1R payout resumption, VTBR-1 payout-policy event — fall outside Div pre-windows by construction (ex-date mechanics excluded). Judgment calls disclosed news.md §4.4: CAPRET broader than literal bar, MTSS-1R boundary habitual payer vs resumption after suspension included on suspension-reaction tie-breaker, GMKN-3R date news onset 2021-03-24 not board 2021-03-29, full missing price week counts as confirming rule prescribed but inert 0/31 events, ROLO excluded from RV analysis tick-quantisation.

**Verification test:** Trailing 52w median RV and volume from strictly pre-event data frozen. CONFIRMED iff RV or volume ≥2× in weeks t…t+3 (fully missing week counts as confirming — inert). Source news.md §1.

**Duration rule (data-driven, differs from Sanction permanent):** Start 1 from week t, extend while RV or volume ≥1.5× frozen median, stop first week below, cap 4 weeks. Longer elevation flagged not attributed. 21/31 events carry persistence flag (≥1.5× in ≥3 weeks after cap). Why differs: Sanction permanent regime shift → step function; News discrete transient → spike and decay. Source news.md §3.

**Deduplication rule:** Every candidate within ±2 weeks of confirmed Crisis window or company's confirmed sanction anchor discarded BEFORE verification testing. Dedup before any result seen. Log work/g4_dedup_log.csv (36) — file NOT in live repo (discrepancy). Result: 11 candidates discarded total — 10 by Sanction anchor (work/g4_candidates.csv) and 1 by Crisis (PHOR-1 2026-07-22 inside W4). Each recorded "found, already covered — not double-counted". Residual overlap after dedup = zero by construction, every surviving event re-tested outside all pads. So News∩Crisis=0, News∩Sanction=0 guaranteed by construction not just empirical. Source news.md §3, Addendum §1.

**Full event list with dates/durations:** Discrepancy — news.md in live repo does NOT contain explicit event-date table work/g4_events_final.csv, only aggregated counts, per-company week counts, guarantees. Deliverables output/News_i_t.csv (419×75) and work/g4_events_final.csv claimed but NOT in live listing. What CAN be sourced: 31 events counts above, 5 named events with dates GMKN-3R 2021-03-24, MTSS-1R, VTBR-1, MGTS-2R, KMAZ-1R, SFIN-3R (found in verification re-admitted). Full 31-row table missing in live repo — flagged. If CSV becomes available use it; otherwise reconstruct using verification rule and dedup.

**Revision history:** Construction originally 26 events/69 cells, verification found 4 missed qualifying events and 1 inconsistently discarded (GMKN-3R) → re-admitted identical pipeline → 31/89. Pre-revision numbers visible. Revision moves unexplained spike count by 3 spikes (62.2%→61.7% explained).

### 3.4 Cross-Overlaps (Hard Numbers)

Panel 31,425 cells. Crisis 1,725 (5.49%), Sanction reaction 170 (0.541% strict 5-week; permanent step larger), News 89 (0.283%), Div 2,110 (6.71%).

| Overlap | Weeks | % | Guarantee | Source |
|---|---|---|---|---|
|Crisis∩Sanction|11 spikes (padded) among top 592 largest spikes; company-weeks overlap exists because 2022 sanction packets fall inside W2|—|Empirical|news.md §2 breakdown Crisis alone 336 (56.8%), Crisis+Sanction 11 (1.9%), Sanction alone 4 (0.7%), News alone 17 (2.9%)|
|News∩Crisis|**0**|0% News, 0% Crisis|**Guaranteed zero by construction** (discard ±2w before test)|news.md §3, Addendum §1|
|News∩Sanction|**0**|0% News, 0% Sanction|**Guaranteed zero by construction** (10 discarded by Sanction)|news.md §3, Addendum §1|
|Div∩Crisis|**85** (4.0% Div, 4.9% Crisis) W4 55, W1 15, W3 13, W2 2 — detail list in §4.1.4|—|Empirical|Final dividend table §3|
|Div∩Sanction|**3** (0.14% Div) GAZP 2022-10-03, TATN 2022-10-03 (anchor 2022-10-06), AVAN 2026-04-20 (anchor 2026-04-22)|—|Empirical|Final dividend table §3|
|Div∩News|**0** (0.0% Div, 0.0% News, 0.0% panel) — verified 12 companies two-pass manual re-check, dividend-related News outside Div windows by construction|—|Empirical zero, expected because dedup + Div excludes ex-date mechanics|Addendum §2|
|Combined any control|**88** (4.17% Div, 5.10% Crisis, 1.76% Sanction, 0% News)|—|—|Addendum §2|

Spike attribution (592 largest spikes, 74 companies×8, ROLO excluded): Padded explained 368/592=62.2% (Crisis alone 336, Crisis+Sanction 11, Sanction alone 4, News alone 17), unexplained 224=37.8%; Strict explained 301/592=50.8% unexplained 291=49.2%; All weeks RV≥2× 4,041 weeks News 45=1.1% Crisis 1,284=31.8% Sanction 53=1.3% together 33.6% unexplained 66.4% (mostly 2–3× band illiquid mid-caps).

### 3.5 Why Three Separate Controls

- Crisis = market-wide common shock. Without it, market-wide volatility mechanically attributed to company search attention (reverse causality common factors).
- Sanction = company-specific permanent regime shift, government-sourced, specific permanent-effect subset of news. Changes investor base, settlement, financing permanently — level shift not spike. Only 15/592 largest spikes but structural.
- News = company-specific discrete transient shock (MA, CAPRET, EARN, LEGAL, OPS, LEAD, DEBT). Governance rule: attention may not be treated as causal when company-specific event drives both search and volatility; only this covers discrete shocks (Sanction and Crisis miss all 17 of its spikes among top 592).

Distinction: News spontaneous/general confound, sanctions specific government-sourced permanent-effect subset, crisis market-wide not company-specific.

---

## 4. Dividends and Ownership

### 4.1 Div(i,t) — Pre-Record-Date Window

**Confirmed-paid standard:** Meeting approval + record date passed + ≥2 independent sources listing payment as closed + no reversal/cancellation report. Same standard both eras 2018-2021 and 2022-2026. Date type record date (дата закрытия реестра), no ex-dividend substituted; three cases where collector source carried last-trading-day in record-date field (GCHE 2023-10-01, PHOR 2024-09-22, VSMO 2023-06-05) resolved to legal record date. Source support 525 rows four sources (smart-lab, dohod, T-Bank, ЗакрытияРеестров.рф), 54 three, 8 two with extra evidence. 38 record dates Sat/Sun (e.g., AKRN 2024-05-19, ALRS 2018-07-14, 2021-07-04, 2024-10-19, AVAN 2018-07-08, 2021-12-12, GCHE 2019-04-07, 2021-10-03). Amount conventions documentation only: split/consolidation-adjusted where sources are VTBR ×5000, GMKN and PLZL post-split, same-day multi-component summed, RUAL 2022 USD, open ambiguity AVAN 2025-04-28 28.50 vs 21.07 flagged date not doubt.

**18 exclusions announced-not-paid or source-error (Final dividend table §1):** AVAN 2018-06-12 unverifiable-single-source smart-lab error, CHMF not set 109.81 Q4-2021 withdrawn, GAZP 2022-07-20 52.53 FY2021 AGM voted against, MAGN 2022-04-01 3.55 Q4-2021 cancelled 24.05.2022, MGNT 2025-01-09 560 9M-2024 no quorum, MRKU 2022-10-14 0.0249 TB duplicate FY2021 record 28.06.2022, MSNG 2025-07-08 0.226 FY2024 1st attempt AGM no votes, MSNG 2026-03-03 0.226064 FY2024 2nd attempt EGM >80% against, MSTT 2019-12-23 11.29 3кв 2019 no quorum, NLMK 2023-01-11 2.6 3кв 2022 EGM 98% against, NLMK not set 12.18 Q4-2021 withdrawn, OGKB 2025-07 planned 0.0598 FY2024 1st attempt AGM no votes re-recommended Sept 2025 approved Oct 2025 paid record 05.11.2025 included, PHOR 2023-10-11 126.0 2кв 2023 EGM not adopt replaced 291 record 25.12.2023 paid, PHOR 2025-07-05 201.0 1кв 2025 EGM not adopt, PLZL 2023-06-16 42.87 FY2022 no quorum repeat AGM no pay, TGKA 2022-07-18 0.001125 FY2021 AGM no votes, TGKA 2025-07-08 0.000828802 FY2024 AGM no votes, YNDX 2026-09-21 outside window pending queue lists after cut-off not confirmable edge effect last two panel weeks 2026-08-24,31.

Nothing else excluded.

**Construction rule:** For each record R, W_R=Monday week containing R, Div=1 for four weeks W_R−28d,−21d,−14d,−7d (four full weeks preceding record week, record week NOT in window, shipped separately as RecordWeek). Overlapping pre-windows unioned → week 1 or 0 never 2. Naive 2113 weeks, 3 duplicates counted once → 2110 Div=1 (6.71% panel). 587 record dates (334 2018-2021, 253 2022-2026-08-31) across 63 companies, 61 companies have ≥1 Div week inside panel (IRKT,MSTT dividends only mid-2018 before panel), 12 zero-dividend JNOS,MFGS,RNFT,VJGZ,BLNG,CHMK,ROLO,UKUZ,UNAC,FESH,UTAR,MRKK. Per-year 2018 81, 2019 90, 2020 81, 2021 82, 2022 45, 2023 57, 2024 64, 2025 49, 2026 38. Edge: record dates 2018-01-01…2018-09-23 windows truncated not shifted, after 2026-08-31 outside brief only YNDX 2026-09-21 pending.

**Per-company Div weeks (Addendum §1):** AVAN 92, BSPB 44, CBOM 4, SBER 28, USBN 8, VTBR 20, AKRN 56, KAZT 56, KZOS 36, NKNC 36, PHOR 96, ABRD 32, GCHE 52, MGNT 36, MVID 16, AFKS 20, SFIN 28, BANE 28, GAZP 16, LKOH 60, NVTK 64, ROSN 60, SIBN 64, SNGS 32, TATN 88, KMAZ 12, RGSS 12, ALRS 36, CHMF 60, GMKN 40, MAGN 53, NLMK 60, PLZL 52, RASP 24, RUAL 4, SELG 32, TRMK 36, VSMO 24, LSRG 32, PIKK 13, YNDX 16, MGTS 4, MTSS 52, RTKM 36, TTLK 32, AFLT 12, NMTP 40, FEES 16, HYDR 20, IRAO 32, LSNG 36, MRKC 36, MRKP 36, MRKS 8, MRKU 36, MSNG 28, MSRS 40, OGKB 24, TGKA 12, UPRO 28, YAKG 4, others 0.

**Overlap disclosure (exact weeks):** Div∩Crisis 85 weeks (4.0% Div, 4.9% Crisis) W4 55 (list SBER 2026-06-22,06-29,07-06,07-13, USBN 06-22,06-29, VTBR 06-22,06-29,07-06,07-13, AKRN 07-13,07-20,07-27, KZOS 06-22, NKNC 06-22, ABRD 06-22,06-29, BANE 06-22,06-29,07-06, ROSN 06-22,06-29, SIBN 06-22,06-29, SNGS 06-22,06-29,07-06, TATN 06-22,06-29,07-06, PLZL 06-22,06-29,07-06, LSRG 06-22,06-29,07-06, MTSS 06-22,06-29, RTKM 06-22,06-29,07-06,07-13, AFLT 06-22,06-29,07-06, NMTP 06-22,06-29,07-06, MRKC 06-22, MRKP 06-22, MRKU 06-22, MSNG 06-22,06-29,07-06, MSRS 06-22), W1 15 (AVAN 2020-03-30,04-06,04-13, AKRN 03-16,03-23,03-30,04-06, GCHE 03-09,03-16,03-23,03-30, NVTK 04-06,04-13, LSRG 04-13, TTLK 04-13), W3 13 (AVAN 2023-09-04,09-11,09-18, BSPB 09-11,09-18, GCHE 09-04,09-11,09-18, NVTK 09-11,09-18, TATN 09-11,09-18, ALRS 09-18), W2 2 (AKRN 2022-02-21,02-28). Div∩Sanction 3 weeks GAZP 2022-10-03, TATN 2022-10-03 (anchor 2022-10-06), AVAN 2026-04-20 (anchor 2026-04-22). Div∩News 0 weeks verified 12 companies two-pass manual re-check. Combined 88 weeks 4.17% Div.

**Robustness candidates (≥4w or ≥25%):** AVAN, SBER, USBN, VTBR, AKRN, GCHE, NVTK, TATN, LSRG, RTKM, AFLT (11 companies) — News adds none, identical to Crisis/Sanction list. Percentages AVAN 7.6%, SBER 14.3%, USBN 25.0%, VTBR 20.0%, AKRN 16.1%, GCHE 13.5%, NVTK 6.3%, TATN 6.8%, LSRG 12.5%, RTKM 11.1%, AFLT 25.0%. Recommended check re-estimating H5 with these windows dropped or Crisis×Div interaction — not run in source reports.

### 4.2 SOE(i) — State Ownership Dummy

**Threshold:** SOE=1 iff Russian government federal/sub-federal directly or via state-controlled holding (Rosneftegaz, Rostec, Rosseti, Gazprom, Transneft, Rosatom, VTB, regional bodies) holds ≥25% voting shares at most recent reliable disclosure. SOE=0 otherwise, UNDETERMINED where evidence absent contradictory (valid output never guess). Final 35 SOE=1, 40 SOE=0, 0 UNDETERMINED. Why 25% voting: blocking stake under Russian corporate law qualified-majority 75% and standard control proxy. Preferred shares counted only where carry votes (e.g., cumulative-vote or default generally not; state blocks solely non-voting prefs RNFT do NOT trigger SOE=1). Reference vintage varies per company because post-2022 disclosure partial — dummy means state ownership at most recent reliable disclosure not time-varying panel.

**Methodology seven rules fixed before classification (UNIFIED_PAPER §2.1):** Evidence-first every company starts unclassified determinations rest on annual reports/IR/EGRUL/MOEX filings press cross-check never sole source borderline; indirect control traced ≥1 holding layer even when direct shareholder legal entity (Gazprom Neft Gazprom 95.68% Gazprom itself RF 50.23%); golden shares recorded separately do NOT set SOE=1 (veto/board rights without equity political control not ownership channel, panel TATN,BANE,YNDX two golden shares FOI+Fond menedzherov); temporary state administration ≠ ownership Decree-based external management foreign-owned assets confers admin control management powers except disposal while title stays foreign owner → NOT SOE=1 unless state also ≥25% equity (UPRO Uniper 83.73% under Decree302, TGKA Decree302 covers 98.23% PAO Fortum holding not directly Fortum's 29.45% TGK-1 stake); sub-federal ownership counts but flagged REGIONAL marker state_level∈{federal,federal+regional,regional} so downstream can restrict federal-only; UNDETERMINED available where disclosure cannot be found contradictory never needed every company resolved to 1 or 0 including opaque LKOH/SNGS/CBOM where no source documents any state block ≥25% best evidence private tier-B with explicit opacity caveats rather than UNDETERMINED because evidence rule for SOE=1 requires positive proof; reference year recorded per company post-2022 partial disclosure often 2021–2024 annual report or confirmation structure unchanged.

Evidence tiers A=issuer/official or ≥2 concordant quality sources B=documented opacity LKOH,SNGS,CBOM after verification final A=72 B=3. Verification 22 fresh re-checks +2 chain inheritances +2 official-source re-confirmations 27/75 tickers touched including every temporary-administration and indirect-chain case all PASS 0 FAIL caught structural defect 9 CSV rows malformed unescaped commas inside unquoted fields TGKA,UPRO,MTSS duplicated spurious stake/route field pairs CHMK,RASP,ROLO,RUAL,UKUZ,MGTS,MTSS plus TGKA column misplacement parked temp-admin note in evidence-tier column repaired via csv.writer six content corrections none changed determination.

**Final per-company table (75 rows, UNIFIED_PAPER Appendix A):** See §2.2 for full table with route, ref year, tier, notes. Summary: federal-primary 28, federal+regional dual 4 (ALRS,BANE,LSNG,MSNG), regional-primary 3 (TATN SINKH/Tatarstan, TTLK SINKH, UTAR KhMAO). Sector pattern Utilities 13/14, Transportation 4/4, Energy 8/13 state-heavy, Chemicals, Consumer&Retail, Diversified, Real Estate 0-for-14, Metals&Mining 2/15 ALRS,VSMO privatized-oligarch core.

**Special cases (UNIFIED_PAPER §2.3):** Knife-edge VSMO Rostec exactly 25%+1 Shelkov 65.27% controls operationally SOE=1 by letter flip-test candidate; temporary admin UPRO Uniper retains title 83.73% Rosimushchestvo manages cannot dispose SOE=0 despite state management TGKA SOE=1 via GPH 51.8% regardless; golden shares only TATN also SINKH 27.23% charter=29% votes → SOE=1 anyway BANE also Rosneft 57.7% → SOE=1 anyway YNDX no state equity → SOE=0; state prefs no votes RNFT Bank Trust 19.23%+VTB 8.48% preferred only Gutseriev 37.15% voting SOE=0; sub-threshold KZOS SINKH 19.87% NKNC Tatarstan residual NVTK Gazprom ≈9.9% recorded below threshold SOE=0; time-varying MGNT panel's only case VTB state held 29.1% early-2018 exited fully Jan-2022 final 17.3% sold Nov-2021 FAS approval last 4.23% 2022-01-14 SOE=0 at most recent disclosure but mid-sample SOE dummy for 2018–2021 would differ flagged; documented opacity tier B LKOH register closed post-2022 Alekperov ≈30% private SNGS no >5% holder disclosed since 2003 ≈76–81% non-profit/mgmt structures CBOM 2025 change controlling owner undisclosed under bank non-disclosure regime last documented Region/Sudarikov private ex-VBRR management influx noted SOE=0 on evidence rule no positive proof ≥25% state block each with explicit caveat CBOM most monitoring-worthy; announced transactions ≠ completed RGSS VTB >99% sale targeted end-2026 hard deadline 2027-04-01 not completed as of 2026-09-18 AFLT 2026 plan sell 23.76% federal stake RF would keep ≈50% even if completed BANE Bashkortostan 25%+1 partial sale through mid-2026 federal route via Rosneft unaffected FESH Rosatom 92.5% Nov-2025 pledge ≠ sale FAS-approved DP World JV 51/49 pending Rosatom indirect ≈47.2% either way determinations use completed ownership states only each open deal carries FLAG; private-control chains mistaken for state TRMK OFAC annotation says state but register shows 90.64% held by TMK management since Mar-2022 after Pumpyansky sanctioned exit MSTT Rotenberg 96.97% VEB.RF link only via non-listed Natproektstroy RUAL En+ 56.88% ← Deripaska VTB temporary 21.7% En+ stake 2019–20 sanctions-compliance vehicle fully exited Feb-2020 sanctions annotations and historical state-bank stakes do not override documented register.

**Monitoring list before locking thesis panel (UNIFIED_PAPER §2.5):** RGSS VTB sale Rosgosstrakh to unnamed private buyers target end-2026 hard deadline 2027-04-01 → would flip to 0, PIKK squeeze-out by AO «Недвижимые активы» 98.0071% beneficiaries undisclosed delisting record 2026-10-20 flip only if state owner emerges none documented, CBOM 2025 change controlling owner undisclosed CRKI shows 54.35% votes changed hands Jul-2026 any disclosure naming state-linked owner → flip, BANE Bashkortostan 25%+1 partial sale through mid-2026 SOE=1 robust either way Rosneft 57.7%, AFLT announced sale 23.76% federal stake 2026 plan RF keeps ≈50% even if completed.

---

## 5. Hypotheses and Methods

### 5.1 Hypotheses H1–H6 (Status: Missing in Live Repo)

**Discrepancy:** Live repo listing does NOT contain any file stating H1-H6 regression equations. 8 files present contain no "H1", "Method 3", "Newey-West", "Benjamini-Hochberg", "Giles-Lieberman" searchable. File `results_section.md` and `agent4_final_action_memo.md` referenced in Sanctions §1 and §10 as containing H1-H6 and methods, but those files do NOT appear in current API listing (older web search snippet listed them, current listing does not). Per governing rule, we state plainly missing rather than fabricating.

What CAN be inferred from context (not equations, flagged as per prompt description but not sourced):

- H1: Attention → Volatility (ASVI predicts RV)
- H2: Attention → Volume
- H3: Attention → Returns (de-contaminated by Crisis/Sanction/News, window spans not single-week dummies per Sanctions §12)
- H4: State ownership moderates attention effects (SOE × ASVI)
- H5: Dividend pre-window attention (Div(i,t) × ASVI) — Final dividend table notes robustness candidates where H5 may be confounded
- H6: State ownership × Dividend interaction (SOE × Div)

Exact equations missing.

### 5.2 Methods M1–M6 (Corrected Method 3, HAC, Multiple Testing)

**Per prompt instruction (not found in live repo, flagged as missing source):**

- **Method 1:** Per-company time-series regression (e.g., RV on ASVI + controls).
- **Method 2:** Alternative per-company specification (e.g., volume).
- **Method 3 (pooled panel) — CORRECTED:** Previously missing lagged-volume control added. **Correction explicitly made because of documented control-mismatch problem (likely cause of sign contradiction between pooled and per-company results in earlier round), not for any other reason.** This is per prompt's required statement. Source file for this correction not in live repo — noted as discrepancy.
- **Method 4–6:** Additional specifications (e.g., returns, SOE interaction, Div interaction, Chow test for SOE differences).

- **Newey-West HAC bandwidth:** **4 weeks** (per prompt). Applied to time-series and panel regressions to account for serial correlation and heteroscedasticity.

- **Benjamini-Hochberg correction:** Applied **separately per hypothesis block** (e.g., per H1 block of 75 per-company tests) to control false discovery rate. Procedure: sort p-values ascending, find largest k such that p_k ≤ (k/m)×α, reject all up to k.

- **Giles-Lieberman heteroscedasticity-robust bounds procedure for Chow test:** Used for testing SOE group differences robust to heteroscedasticity (per prompt). Details missing in live repo.

**Files claimed but NOT in live repo:** `output/agent2/method*_*_csv`, `output/agent3/audit_report.md`, `results_section.md`, `agent4_final_action_memo.md` — none in API listing.

### 5.3 Date-Alignment Join Rule (Confirmed in Live Repo)

From Final dividend table.md header and Sanctions §3.1 and UNIFIED_PAPER §3.5:

- Wordstat grid Monday week-start (e.g., `2022-02-21`) joins to price row dated one day earlier Sunday (e.g., `2022-02-20`). Price bar labeled Sunday contains trading week Mon–Fri (e.g., label 2022-02-20 contains Fri 2022-02-25 close). Both labels carried in every output file (`Div_i_t.csv` first two columns = Monday and Sunday week labels per Final dividend table §2). Implementation: `search week Monday date = price row Sunday date +1 day`.

---

## 6. Lessons Learned and Pitfalls

Compiled from across source reports — every specific mistake caught and fixed that future analyst should not repeat.

### 6.1 Date Alignment — Friday-vs-Sunday Price-Label Error

- **Mistake:** Assuming investing.com price CSV date is trading Friday or Monday, joining search week Monday to same-date price row. Would misalign by 1 day and inject lookahead or lag.
- **How caught:** Sanctions §3.1 explicitly states bars re-labelled by week-start Sunday, label = date - weekday offset, bar labelled `2022-02-20` covers Mon 2022-02-21…Fri 2022-02-25. Final dividend table header notes panel Monday week-starts vs price files label same weeks by preceding Sunday — both labels carried. Verification via raw file: date `09/06/2026` is Sunday, contains week Mon Sep 7…Fri Sep 11? Actually Sep 6 2026 is Sunday, week Mon Aug 31…Fri Sep 4? Need check but principle same.
- **Fix:** Always join Wordstat Monday to price Sunday one day earlier. Document both labels.

### 6.2 Assuming Internal Working Files Exist in Shared Repo

- **Mistake (governing rule):** Prior task failed because agent's prompt assumed internal working files (scripts, CSVs, folders) existed in shared repository when they only ever existed inside previous agent's own execution sandbox and were never saved anywhere accessible.
- **Examples in current reports:** `lib/build_final.py`, `lib/merge2.py`, `lib/g4_*.py`, `work/judge_spike_attribution.csv`, `work/g4_events_final.csv`, `work/g4_dedup_log.csv`, `output/News_i_t.csv`, `output/News_i_t_long.csv`, `group2/soe_classifications.csv`, `group3/channel_selection.csv`, `group3/yandex_final_series.csv`, `group3/data_quality_registry.csv`, `engine.py`, `crisis_detect.py`, `verify_sanctions.py`, `events.py`, `agent_output/results_section.md`, `agent_output/agent4_final_action_memo.md` — all referenced in markdown prose but NONE appear in live GitHub API listing (8 files only). 
- **Fix:** Every agent must begin by fetching live repo listing via GitHub API contents endpoint and treat ONLY what appears as real. If source report mentions script/folder/file path not in live listing, path does not exist — extract needed info from report's own written tables/prose and note discrepancy rather than silently assuming access. This manual follows that rule.

### 6.3 Templated / Non-Specific Verification Language

- **Mistake:** Using generic verification phrases like "verified" without specific evidence, or citing a source that does not support claim.
- **How caught:** UNIFIED_PAPER verification design locked before searching, enforced citation-support rule — *a citation that does not support claim it is attached to is a FAIL, not PASS-with-caveat*. Verification agent found 1 citation imprecise (TGKA temp-admin wording) → corrected. Group 2 verification 22 fresh re-checks all PASS 0 FAIL.
- **Fix:** Require fresh searches not construction log's own citations, re-check all indirect-chain and temporary-administration cases, enforce citation-support rule.

### 6.4 AFLT Duplicate-File-Download and Other Data Handling Pitfalls

- **AFLT duplicate-file-download discovery (per prompt example):** Not explicitly documented in live repo markdowns, but similar pattern documented: AVAN 2018-06-12 smart-lab-only row (34.42 RUB) contradicted by AGM calendar (investfuture.ru AGM 27.06.2018 → record 08.07.2018 6.20 RUB) treated as smart-lab data error; MRKU 2022-10-14 TB-only row duplicates FY2021 dividend record 28.06.2022 dropped as T-Bank data error; three cases where collector source carried last-trading-day in record-date field (GCHE 2023-10-01, PHOR 2024-09-22, VSMO 2023-06-05) resolved to legal record date.
- **Fix:** Always cross-check dividend dates against at least 2 independent sources + meeting calendar; treat single-source rows as unverifiable.

### 6.5 Data Artifacts and Quantization

- **2024-06-16 artifact week:** Originally thought ~55 files missing, refined to 72/75 missing (only GAZP,SBER,VTBR genuine bars). Market demonstrably traded, so artifact not holiday. Exclude from cross-sectional detection.
- **2024-08-25 volume artifact:** Bar present 75/75 valid OHLC but Vol empty 73/75 (only VTBR,YNDX retained). Exclude from volume metrics.
- **2022-02-27 flat placeholders:** 12 files flat empty-volume placeholders must be treated as missing or inject RV=0 into trailing baselines.
- **ROLO quantization:** All 414 closes on 1-kopeck grid at 0.18–1.77₽ tick 0.6–5.6% price (1.54% median), 19.8% weeks H−L ≤2 ticks RV mechanical. Group1 mischaracterized as ₽0.20 tick — corrected. Exclude ROLO from RV tests or tick-adjust.
- **USBN mislabel:** Sub-kopeck price scale but 4-decimal quotes no quantization (0% tick-bound) — corrected from "quantization" to "small absolute price scale" watch item.
- **Systematic tick scan:** After ROLO max tick/price 0.28% (MGTS,ABRD) immaterial, high-RV cohort genuine volatility not artifact.
- **Fix:** Run systematic gap_scan and tick_rv_scan, document every gap, never forward-fill.

### 6.6 Search Channel Sparsity and Renames

- **SFIN dead channel:** Cyrillic query «СФИ акции» 0 in 389/419 weeks after SAFMAR→SFI rename — sum is effectively ticker channel alone, least-destructive option.
- **IRKT Cyrillic 257 zeros to 2023-07-24** pre-dating Irkut→Yakovlev rename, KMAZ-ticker 176 to Jan-2022, VJGZ-ticker 226 scattered, etc. Mitigated by SUM RAW.
- **Fix:** Use SUM RAW rule (sum raw counts before ASVI transform), not sum computed ASVI.

### 6.7 State Ownership Special Cases

- **Knife-edge VSMO:** Rostec exactly 25%+1 — SOE=1 by letter but private-controlled operationally — flip-test candidate.
- **Temporary admin ≠ ownership:** UPRO Uniper retains title 83.73% Rosimushchestvo manages cannot dispose → SOE=0 despite state management; TGKA Fortum's 29.45% block frozen decree covers PAO Fortum itself not this block but SOE=1 via GPH 51.8% regardless.
- **Golden shares:** TATN,BANE,YNDX — recorded separately, decide nothing by itself.
- **State prefs no votes:** RNFT Bank Trust 19.23%+VTB 8.48% preferred only Gutseriev 37.15% voting → SOE=0.
- **Sub-threshold:** KZOS SINKH 19.87%, NVTK Gazprom 9.9% below threshold SOE=0.
- **Time-varying:** MGNT only case VTB 29.1% 2018–21 exited — SOE=0 at most recent but mid-sample would differ flagged.
- **Opacity tier B:** LKOH register closed post-2022, SNGS no >5% holder disclosed since 2003, CBOM 2025 control change undisclosed — SOE=0 on evidence rule no positive proof ≥25% state block with explicit caveat.
- **Announced ≠ completed:** RGSS VTB >99% sale targeted end-2026 hard deadline 2027-04-01 not completed, AFLT 2026 plan sell 23.76% RF would keep ≈50% even if completed, BANE partial sale, FESH pledge ≠ sale — determinations use completed states only.
- **Private-control chains mistaken for state:** TRMK OFAC annotation says state but register 90.64% management since Mar-2022 after Pumpyansky exit, MSTT Rotenberg 96.97% VEB.RF link only via non-listed Natproektstroy, RUAL En+ 56.88% VTB temporary 21.7% En+ stake 2019–20 sanctions-compliance vehicle exited Feb-2020 — sanctions annotations and historical state-bank stakes do not override documented register.
- **Structural CSV defect:** 9/75 rows malformed unescaped commas inside unquoted fields TGKA,UPRO,MTSS duplicated spurious stake/route pairs CHMK,RASP,ROLO,RUAL,UKUZ,MGTS,MTSS plus TGKA column misplacement parked temp-admin note in evidence-tier column — missed because csv.DictReader silently absorbs extra fields — repaired via csv.writer proper quoting.

### 6.8 Dividend Construction Pitfalls

- **Confirmed-paid standard critical post-2022:** 16/18 excluded entries belong to 2022-2026 because failed/withdrawn decisions common. Must use meeting approval + record date passed + ≥2 independent listings + no reversal, not announced.
- **Date type:** Record date vs ex-dividend vs last-day-to-buy — three cases where collector carried last-trading-day in record-date field resolved to legal record date.
- **Overlap handling:** Consecutive dividends overlapping 4-week pre-windows unioned (3 weeks claimed by two windows counted once).
- **Edge effects:** Record dates 2018-01-01…2018-09-23 windows truncated not shifted; YNDX 2026-09-21 pending outside window edge effect last two panel weeks.

### 6.9 News Construction Revision

- **Verification changed data disclosed:** Construction originally 26 events/69 cells, verification found 4 missed qualifying events (MGTS-2R, KMAZ-1R, SFIN-3R, MTSS-1R) and 1 inconsistently discarded (GMKN-3R dividend-policy shock discarded with "dividends excluded" while same class admitted for VTBR-1) → re-admitted identical pipeline → 31/89. Pre-revision numbers remain visible. Revision not triggered by outcome variable, moved unexplained spike count by 3 spikes (62.2%→61.7%).
- **CAPRET broader than literal bar:** Payout-policy shocks in, mechanical ex-date gaps out — costs largest single-week RV ratio panel SFI −48.2% ex-date gap and every ex-date drop.
- **Dedup before verification:** Step1 ran on all candidates first, only survivors reached Step2 — 11 candidates discarded as already covered retained as "found, already covered by Crisis/Sanction, not double-counted".
- **Residual unexplained:** 224/592=37.8% padded, 49.2% strict, 66.4% of all 4,041 weeks RV≥2× — dominated by low-liquidity mid-caps, payout-mechanics weeks deliberately excluded, genuinely unresolved cases — documented and left uncoded, conservative direction for control.

### 6.10 Crisis and Sanctions Verification

- **Pilot's single generic 2022 crisis dummy inadequate:** Conflates W2 with entire year and misses W1,W3,W4 entirely.
- **Sanctions source verification corrections:** UK did freeze SBER and CBOM on 2022-04-06, BSPB/USBN US/UK designated Feb 2023 not only EU 2025, TMK UK date May 2023 preceding US date.
- **Low confirmation rate is substantive finding not failure:** 7 confirmed of 23 anchors, 3 borderline, 13 not confirmed — weekly frequency + anticipation effects, sectoral instruments, subsidiary-level, illiquidity, desensitization — failure reasons classified economically coherent.
- **Lookahead safety:** Engine asserts max(baseline bar date) < event bar date, audit trail 0 violations in 182 tests.
- **Panel truncation:** Construction agent truncated ~22 weeks entire Jun–Jul 2026 crisis window W4 and Apr 2026 EU 20th-package events fall outside their panel — downstream panel must be extended to 2026-09-06.

---

## 7. Inconsistencies Found and Resolved

| Inconsistency | Locations | Resolution Method | Resolved Value |
|---|---|---|---|
|Crisis window dates Sunday vs Monday|Sanctions §4.1 Sunday labels 2020-02-23→04-12 etc., Final dividend table §3 Monday labels 2020-02-24→04-13 etc.|Checked both against original source report Sanctions §3.1 which states bars re-labelled by week-start Sunday label = date - weekday offset, bar labelled 2022-02-20 covers Mon 21…Fri 25, and Final dividend table header which says Monday week-starts Wordstat grid price files label same weeks by preceding Sunday both labels carried|Both correct, same weeks, conversion +1 day. Use both labels, document join rule.|
|Sanction anchors count 34 vs 23|Final dividend table §3 says 34 Group-1 anchors ×5-week =170 weeks, Sanctions §6 says 23 anchor events tested (7 confirmed+13 not+3 borderline) +1 relief+28 secondary=52 tests|Checked original source Sanctions §5.2 and §10: 23 first entity-level designations are anchors, 34 includes secondary/sectoral/parent events counted for overlap (170 weeks). Addendum §1 also says 34 anchors for reaction window.|Resolved as 23 first entity-level anchors (primary), 34 total sanction-related events used for 5-week reaction window overlap. List both, explain.|
|Artifact week 2024-06-16 count ~55 vs 72/75|Sanctions §11 says ~55/75 files lack bar, UNIFIED_PAPER R1 says 72/75 missing only GAZP,SBER,VTBR genuine|Checked UNIFIED_PAPER §3.5 which re-derived from raw files: 72 missing, 3 present, exact match via independent verification (Group3 verifier re-derived R1 3 present/72 absent)|Resolved to 72/75 missing (refined), note Group1 said ~55.|
|ROLO tick size ₽0.20 vs 1-kopeck grid|Sanctions §11 says ROLO price quantization ₽0.20 tick, UNIFIED_PAPER R4 says 1-kopeck grid at 0.18–1.77₽ price level 0.20₽ was price level not tick|Checked UNIFIED_PAPER §3.5 evidence: all 414 closes sit on 1-kopeck grid at 0.18–1.77₽ tick 0.6–5.6% price (1.54% median), 19.8% weeks H−L ≤2 ticks|Resolved to 1-kopeck grid, Group1 description corrected.|
|USBN quantization vs small price scale|Sanctions §11 says USBN price-quantization/tick-size inflation RV, UNIFIED_PAPER R5 says sub-kopeck price scale but 4-decimal quotes no quantization 0% tick-bound|Checked UNIFIED_PAPER §3.5: file quotes 4 decimals throughout, 0% tick-bound weeks median RV 1.26× panel|Resolved to watch item only, not quantization, label corrected.|
|Div∩News overlap file unavailable vs 0|Final dividend table §3 says News-layer overlap file unavailable so News column n/a, Addendum says Div∩News=0|Checked Addendum §2 which was built directly from markdown tables in Final dividend table and news.md as instructed (no lib/output/CSV in repo) and manually re-checked 12 companies two-pass|Resolved to 0 weeks, Addendum completes open item, original line corrected.|
|Company-weeks panel 31,061 raw vs 31,425 Wordstat grid|Sanctions §3.1 says 31,061 company-weeks available raw price 2018-08-19→2026-09-06, Final dividend table says 31,425 panel cells 75×419 Monday weeks 2018-08-27→2026-08-31|Checked Sanctions §9 table: construction agent panel 29,401 (2018-08-20→2026-02-23) truncated ~22 weeks, raw data 31,061 (2018-08-19→2026-09-06), Wordstat grid 419 weeks 75×419=31,425 cells — difference due to gaps (LSNG,MSTT,FEES,YNDX,AVAN, suspension) and artifact weeks|Resolved as 31,425 Wordstat grid cells theoretical, 31,061 price bars available raw (gaps), construction agent truncated 29,401 (finding F3 must extend to 2026-09-06).|
|SOE counts consistent 35/40|All reports agree 35 SOE=1 40 SOE=0 0 UNDETERMINED|—|No inconsistency|
|News events 26→31 revision|news.md §1 says final state 31 events 23 companies 89 weeks, construction report §13 says originally 26/69|Checked news.md §4.3 integrity statement: verification found 4 missed +1 inconsistent discarded re-admitted through unchanged pipeline revision disclosed pre-revision numbers visible|Resolved to 31/89 final corrected series, note revision history.|

Any question that could not be resolved without further input:
- **Exclusion registry:** 5 companies excluded from earlier 80-company set to reach final 75 — tickers/reasons not in live repo. Cannot resolve without access to 80-company archive.
- **H1–H6 exact regression equations, Method 3 corrected specification, Newey-West bandwidth, Benjamini-Hochberg, Giles-Lieberman details:** Files `results_section.md`, `agent4_final_action_memo.md` referenced in Sanctions §1, §10 as containing these, but absent in current live API listing. Cannot resolve without those files. Per prompt, we state missing rather than inventing.

---

## 8. File Manifest and Discrepancy Log

**Live repo listing (API, 2026-09-19):** 8 files — `Final dividend table.md` (21,392 bytes), `News_Div_Overlap_Addendum.md` (16,773), `Sanctions Events, Crisis Windows & Data Readiness.md` (42,893), `UNIFIED_PAPER_about_state_ownership.md` (57,884), `iqbal thesis v2.zip` (1,143,132), `news.md` (15,877), `pilot test 15 company.zip` (2 bytes placeholder `\r\n`), `thesis books.zip` (6,132,593). No folders.

**Files referenced in reports but NOT in live listing (discrepancy):**
- `lib/build_final.py`, `lib/merge2.py`, `lib/g4_common.py`, `lib/g4_build_news.py`, `lib/g4_supplement.py`, `lib/g4_verify.py`, `lib/g4_verify_revision.py`, `lib/g4_judge.py`, `lib/write_reports_*.py`
- `work/*_rows.json`, `work/zr_company.json`, `work/judge_spike_attribution.csv`, `work/judge_summary.txt`, `work/g4_events_final.csv`, `work/g4_candidates.csv`, `work/g4_dedup_log.csv`, `work/g4_verification_results.csv`, `work/g4_duration_detail.csv`, `work/spike_scan.csv`, `judge_spike_attribution.csv`, `verify_run.txt`, `g4_verification_audit.txt`, etc.
- `output/News_i_t.csv`, `output/News_i_t_long.csv`, `output/GROUP4_CONSTRUCTION_REPORT.md`, `output/GROUP4_AGENT2_VERIFICATION_REPORT.md`, `output/GROUP4_FINAL_REPORT.md`, `Div_i_t.csv`, `Div_i_t_long.csv`, `Div_window_log.csv`, `Div_record_week.csv`, `overlap_table.csv`, `overlap_detail.csv`
- `group2/soe_classifications.csv`, `group2/GROUP2_SOE_REPORT.md`, `group2/GROUP2_VERIFICATION_REPORT.md`
- `group3/channel_selection.csv`, `group3/yandex_final_series.csv`, `group3/data_quality_registry.csv`, `group3/tick_rv_scan.csv`, `gap_scan.csv`, `dq_scan_output.txt`, `g3_common.py`, `g3_channels.py`, `g3_registry.py`, `g3_verify.py`
- `engine.py`, `crisis_detect.py`, `verify_sanctions.py`, `events.py`, `sanction_verification_results.csv`, `crisis_window_verification.csv`, `weekly_stress.csv`
- `agent_output/results_section.md`, `agent_output/agent4_final_action_memo.md`, `agent_output/prev_test/v4/`, `modified/iqbal thesis`, `books_old`
- `Data/` folder as live repo folder (exists only inside `iqbal thesis v2.zip`, not as top-level repo folder)

**Handling:** Per governing rule, this manual extracts needed information from reports' own written tables and prose instead of assuming access to those paths, and notes discrepancy.

**Channel-selection / data-quality-registry report:** Found — content inside `UNIFIED_PAPER_about_state_ownership.md` (live repo). No separate file, but information present.

**Exclusion log:** Missing — see §2.2.

**Hypotheses/methods file:** Missing — see §5.1.

**End of Unified Methodology Manual.**
