# State Ownership and Investor Attention in the Russian Equity Market
## Unified Data-Construction & Verification Report — SOE Classification (Group 2) and Attention-Channel Selection with Data-Quality Registry (Group 3)

**Panel:** 75 MOEX-listed companies · 13 sectors · weekly data 2018-08-27 → 2026-08-31 (419 weeks)
**Date:** 2026-09-18 · **Status:** FINAL — both workstreams constructed and independently verified
**Companion machine-readable outputs:** `group2/soe_classifications.csv` · `group3/channel_selection.csv` · `group3/yandex_final_series.csv` · `group3/data_quality_registry.csv`

---

## Abstract

This report consolidates two independent data-construction workstreams supporting a thesis on
investor attention (measured by Yandex Wordstat search volume) and state ownership in the Russian
equity market, together with their independent verifications.

**Part I (SOE classification).** Each of the 75 panel companies is classified as a state-owned
enterprise (SOE=1) if and only if the Russian government — federal or sub-federal, directly or via
a state-controlled holding (Rosneftegaz, Rostec, Rosseti, Gazprom, Transneft, Rosatom, VTB,
regional bodies, etc.) — holds **≥25% of voting shares** at the most recent reliable disclosure.
Result: **35 SOE=1, 40 SOE=0, 0 UNDETERMINED**. Golden shares, sub-threshold state blocks and
temporary state administration are recorded separately and never count toward SOE=1. An
independent verification agent re-checked **22 companies with fresh sources** (plus two chain
inheritances and two official-source re-confirmations — 27 of 75 tickers touched), including every
temporary-administration and indirect-chain case: **all PASS, 0 FAIL**. The verification also
caught and repaired a structural defect in 9 CSV rows and applied six content corrections, none of
which changed a determination.

**Part II (attention channels & data quality).** For every company, an Abnormal Search Volume
Index (ASVI) is built from two Wordstat channels (Latin-ticker queries and Cyrillic
"<name> акции" queries). The pre-specified rule — keep one channel if the two ASVI series
correlate ≥0.85, otherwise sum the two **raw** SVI series and compute a single ASVI — sends
**all 74 non-Yandex companies to the SUM-RAW branch** (maximum observed correlation 0.8066, TRMK;
median 0.573). Yandex uses the pre-decided combination YNDX + Cyrillic with **YDEX excluded**
(quantified: including it would inflate 99 weeks >1.5× starting exactly at the July-2024
relisting). A consolidated **17-item data-quality registry** (R1–R17) documents every known
artifact, gap and quantization issue in the price and search data, confirming/correcting eight
Group-1 findings and adding seven new ones. An independent verification agent (separate parser,
separate ASVI implementation) recomputed 20 correlations to 4 decimals, re-derived four registry
items from raw files, and ran false-negative scans on 12 unflagged companies: **7/7 PASS**.

Both deliverables are ready for downstream modeling; §4 gives the exact handling rules the
regression stage must apply.

---

## 1. Data foundation and governance

### 1.1 The panel

75 companies with MOEX-listed equity, spanning 13 sector directories in the raw dataset
(`iqbal thesis/Data/`). Sector composition (N, SOE=1, SOE=0):

| Sector | N | SOE=1 | SOE=0 |
|--------|---|-------|-------|
| Banking | 6 | 2 | 4 |
| Chemicals | 5 | 0 | 5 |
| Consumer & Retail | 4 | 0 | 4 |
| Diversified | 2 | 0 | 2 |
| Energy | 13 | 8 | 5 |
| Industrial | 1 | 1 | 0 |
| Insurance | 1 | 1 | 0 |
| Metals & Mining | 15 | 2 | 13 |
| Real Estate | 3 | 0 | 3 |
| Tech | 3 | 2 | 1 |
| Telecom | 4 | 2 | 2 |
| Transportation | 4 | 4 | 0 |
| Utilities | 14 | 13 | 1 |
| **Total** | **75** | **35** | **40** |

Each company folder contains exactly three files (Yandex has a fourth):

1. **Wordstat ticker-search file** (`<TICKER>.csv`) — weekly counts of Yandex searches for the
   Latin ticker string; semicolon-separated; 419 rows; week-start Mondays 2018-08-27 → 2026-08-31.
2. **Wordstat Cyrillic-search file** (Cyrillic filename, e.g. `Сбер акции.csv`) — same grid,
   counts for the Russian-language "<name> акции" ("shares") query.
3. **investing.com weekly price file** (`<Name> Stock Price History.csv`) — quoted CSV,
   MM/DD/YYYY descending; bars relabelled to week-start Sundays; columns Date, Price, Open, High,
   Low, Vol., Change %. Panel total: **31,061 company-week bars** (verified to match Group 1).
4. *Yandex only:* `YDEX.csv` — a second ticker channel appearing after the July-2024 MOEX
   relisting (YNDX→YDEX). **Excluded from all analysis by pre-decided rule** (§3.4).

> **Explanation — why two search channels.** Russian retail investors search both ways: traders
> and terminal users type the Latin ticker ("SBER"), while news-driven retail investors type the
> Cyrillic company name plus "акции" ("Сбер акции"). The two query populations respond to
> different events, which is precisely why the correlation test (§3.2) matters and why it fails
> the 0.85 bar everywhere.

> **Ticker-mapping caution (learned the hard way).** Two tickers in this dataset do not mean what
> they suggest: **KAZT = KuybyshevAzot** (not Kazan-related) and **KZOS = Kazanorgsintez**. Entity
> identity was confirmed from each folder's price-file header before any classification.

### 1.2 Governance: construction + independent verification

Each part was built by a construction agent and then re-checked by a **separate verification
agent** with a fixed mandate:

- **Group 2 verifier:** re-check ≥20 of 75 determinations with a mixed sample using *fresh*
  searches (not the construction log's own citations); re-check **all** indirect-chain and
  temporary-administration cases; enforce the citation-support rule — *a citation that does not
  support the claim it is attached to is a FAIL, not a PASS-with-caveat*; verify CSV↔report
  structural consistency.
- **Group 3 verifier:** recompute the ASVI correlation for ≥15 companies with an **independent
  code stack** (different parser, different ASVI implementation); confirm no YDEX residue;
  independently re-derive ≥3 registry items from raw files; run false-negative checks on ≥10
  companies *not* flagged in the registry.
- **Decision hygiene (both parts):** all decisions are criteria-based. No regression output was
  consulted or produced at any construction stage; channel decisions never consider effects on
  future results; every company is treated as unclassified until its own evidence is found.
- **Crash-safety:** both methodology reports were written continuously (batch-by-batch /
  section-by-section), never reconstructed at the end.

Group 1's sanctions/crisis verification report (2026-09-17) is an **upstream input only**: it
seeded the data-quality registry (§3.5), which confirmed, refined or corrected each of its claims
against raw files.

---

## 2. Part I — State-ownership (SOE) classification

### 2.1 Definition and decision rules

**Definition.** `SOE = 1` iff the Russian government holds **≥25% of voting shares**, directly or
indirectly through a state-controlled holding, **as of the most recent reliable disclosure**.
`SOE = 0` otherwise; `UNDETERMINED` where evidence is genuinely absent or contradictory (a valid
output — never guess).

> **Explanation — why 25% of *voting* shares.** A ≥25% block is a blocking stake under Russian
> corporate law (qualified-majority decisions require 75%) and is the standard control proxy in
> the state-ownership literature. Preferred shares are counted only where they carry votes
> (e.g., in cumulative-vote or default scenarios they generally do not; state blocks consisting
> solely of non-voting prefs — RNFT — therefore do not trigger SOE=1).

The seven rules, fixed **before** any company was classified:

1. **Evidence-first.** Every company starts unclassified. Determinations rest on company annual
   reports / IR disclosures, EGRUL-type data, MOEX filings; press/data providers serve as
   cross-checks and never as the sole source for a borderline call.
2. **Indirect control traced ≥1 holding layer.** A listed company controlled through Rosneftegaz,
   Rostec, Rosseti, Gazprom, Transneft, Rosatom, VTB, Sberbank, regional bodies, etc. is SOE=1
   even when the direct shareholder is a legal entity (e.g., Gazprom Neft: Gazprom 95.68%;
   Gazprom itself: RF 50.23%).
3. **Golden shares / special rights are recorded separately** and do NOT set SOE=1.
   > **Explanation.** A "golden share" gives the state veto/board rights without equity (or with
   > a token stake). It is political control, not the ownership channel the thesis measures.
   > Panel cases: TATN, BANE, YNDX (two golden shares: FOI + Fond menedzherov).
4. **Temporary state administration ≠ ownership.** Decree-based external management of
   foreign-owned assets confers administrative control (management powers *except disposal*)
   while title stays with the foreign owner → does NOT set SOE=1 unless the state also holds
   ≥25% equity. Panel cases: UPRO (Uniper's 83.73% under Decree 302), TGKA (Decree 302 covers
   98.23% of PAO Fortum — the holding — not directly Fortum's 29.45% TGK-1 stake).
5. **Sub-federal ownership counts, but is flagged.** Republic/krai/oblast/city stakes qualify as
   "Russian government … or similar state-controlled entity", but every such case carries a
   `REGIONAL` marker (state_level ∈ {federal, federal+regional, regional}) so downstream work can
   restrict to federal-only if desired. This interpretation was documented before classification.
6. **UNDETERMINED is available** where disclosure cannot be found or is contradictory.
   (In the event it was never needed: every company resolved to 1 or 0 on documented evidence —
   including the opaque cases LKOH/SNGS/CBOM, where *no source documents any state block ≥25%*
   and the best available evidence points to private control; these are tier-B determinations with
   explicit opacity caveats rather than UNDETERMINED, because the evidence rule for SOE=1 requires
   *positive* proof of a state block.)
7. **Reference year recorded per company.** Post-2022 Russian disclosure is partial (many issuers
   stopped publishing shareholder registers under the 2022 disclosure relief), so "most recent
   reliable" is often the 2021–2024 annual report or an official/news confirmation that the
   structure is unchanged.

**Evidence tiers.** A = issuer/official disclosure or ≥2 concordant quality sources;
B = documented opacity (LKOH, SNGS, CBOM after verification). Final distribution: A=72, B=3.

### 2.2 Results

**35 × SOE=1 · 40 × SOE=0 · 0 × UNDETERMINED.**

- **State level (of the 35):** federal-primary 28 · federal+regional dual route 4 (ALRS, BANE,
  LSNG, MSNG) · regional-primary 3 (TATN — SINKH/Tatarstan; TTLK — SINKH; UTAR — KhMAO).
- **Route types:** direct federal (SBER 50%+1 = 52.32% of votes; GAZP 50.23%; AFLT 73.77%…),
  one-layer indirect (SIBN ← Gazprom 95.68%; MSNG/OGKB ← GPH; TGKA ← GPH 51.8%),
  two-layer indirect (MFGS/JNOS/VJGZ ← Slavneft ← Rosneft+GPN parity; IRKT ← UAC ← Rostec 92.31%;
  ROLO-style private chains classified 0), holding-company aggregates (NMTP: Transneft 60.62% +
  Rosimushchestvo 20% + RZhD-managed 5.3%; RTKM: four state vehicles summing ≈71%).
- **Sector pattern** (table in §1.1): Utilities 13/14, Transportation 4/4, Energy 8/13 are the
  state-heavy sectors; Chemicals, Consumer & Retail, Diversified, Real Estate are 0-for-14;
  Metals & Mining is 2/15 (ALRS, VSMO) — the privatized-oligarch core of the market.

The full 75-row determination table (owner, route, reference year, evidence tier) is
**Appendix A**; the machine-readable version is `group2/soe_classifications.csv`
(75 rows × 14 columns; validated — see §2.5).

### 2.3 Special cases (each explained)

These are the rows a reader should not take at face value from the dummy alone:

| Case | Companies | What it means for the dummy |
|---|---|---|
| **Knife-edge threshold** | VSMO (Rostec exactly 25%+1; Shelkov 65.27% controls operationally) | SOE=1 by the letter of the ≥25% rule; economically it is private-controlled. Robustness: flip-test candidate. |
| **Temporary administration** | UPRO (Uniper retains title to 83.73%; Rosimushchestvo manages, cannot dispose), TGKA (Fortum's 29.45% block frozen; decree covers PAO Fortum itself) | SOE=0 for UPRO (no state equity) despite state *management*; TGKA is SOE=1 via GPH 51.8% regardless. |
| **Golden shares only** | TATN (also SINKH 27.23% charter = 29% votes → SOE=1 anyway), BANE (also Rosneft 57.7% → SOE=1 anyway), YNDX (no state equity → SOE=0) | Golden share recorded separately; decides nothing by itself — it matters only for YNDX, where it coexists with SOE=0. |
| **State prefs, no votes** | RNFT (Bank Trust 19.23% + VTB 8.48% — preferred only; Gutseriev 37.15% of voting) | SOE=0: the state blocks are non-voting. |
| **Sub-threshold state blocks** | KZOS (SINKH 19.87%), NKNC (Tatarstan residual), NVTK (Gazprom ≈9.9%) | Recorded in the CSV with explicit "below threshold" tags; SOE=0. |
| **Time-varying ownership** | MGNT — the panel's only case: VTB (state) held 29.1% from early-2018, exited fully by Jan-2022 (final 17.3% sold Nov-2021; FAS approval for the last 4.23% on 2022-01-14) | SOE=0 "at most recent disclosure" — but a mid-sample SOE dummy for 2018–2021 would differ. Flagged for the thesis. |
| **Documented opacity (tier B)** | LKOH (register closed post-2022; Alekperov ≈30% private), SNGS (no >5% holder disclosed since 2003; ≈76–81% in non-profit/mgmt structures), CBOM (2025 change of controlling owner *undisclosed* under the bank non-disclosure regime; last documented owner: Region/Sudarikov, private; ex-VBRR management influx noted) | SOE=0 on the evidence rule (no positive proof of a ≥25% state block in any source), each with an explicit caveat. CBOM is the single most monitoring-worthy SOE=0 row. |
| **Announced transactions ≠ completed** | RGSS (VTB >99%; sale targeted end-2026, hard deadline 2027-04-01 — not completed as of 2026-09-18), AFLT (2026 plan to sell 23.76%; RF would keep ≈50% even if completed), BANE (Bashkortostan 25%+1 partial sale through mid-2026; federal route via Rosneft unaffected), FESH (Rosatom 92.5%; Nov-2025 pledge ≠ sale; FAS-approved DP World JV 51/49 pending — Rosatom indirect ≈47.2% either way) | Determinations use *completed* ownership states only; each open deal carries a FLAG. |
| **Private-control chains mistaken for state** | TRMK (OFAC annotation says "state" — but the register shows 90.64% held by TMK management since Mar-2022, after Pumpyansky's sanctioned exit), MSTT (Rotenberg 96.97%; VEB.RF link only via non-listed Natproektstroy), RUAL (En+ 56.88% ← Deripaska; VTB's temporary 21.7% En+ stake 2019–20 was a sanctions-compliance vehicle, fully exited Feb-2020) | Sanctions annotations and historical state-bank stakes do not override the documented register. |

### 2.4 Independent verification (Group 2)

**Design (locked before searching):** 21 companies + 2 chain families, mixing SOE=1/SOE=0 across
sectors; all temporary-administration cases (UPRO, TGKA); all indirect chains (Slavneft family
MFGS/JNOS/VJGZ; SIBN; RUAL; KZOS; RGSS; NMTP; Rosseti subsidiaries MSRS/MRKP). Every check used
**fresh 2026-09-18 searches**, not the construction log's citations. A bonus check (GMKN) and a
17th construction-batch re-confirmation (VSMO/MSRS/MRKP against official sources) were added.

**Result: 22 fresh re-checks — all PASS, 0 FAIL** (verdict table: Appendix D.1). Notable outcomes:

- **Primary-source confirmations:** ALROSA AR2024 (RF 33.0256% / Yakutia 25.0002% / uluses
  8.0003%) and Nornickel AR2024 (Interros 37.0 / En+ 26.4) match the classification exactly.
- **Evidence strengthened:** MFGS ≈96.9% state-related (Slavneft 69.117% + TOC Investments
  14.03% [Rosneft] + GPN-Invest 13.75%), vs the 56.4% Slavneft-direct figure used at construction.
- **Six content corrections applied** to the construction report and CSV (none changed a
  determination): (1) TGKA Decree-302 scope; (2) YAKG structure updated to post-2023 (A-TEK 45% /
  A-Property 20.16% / ZPIF Trust Yunion 25%; Avdolyan ≈44% direct+indirect — the "90.6%" was the
  2019 vintage); (3) MFGS evidence upgrade; (4) PIKK updated to the 2026 squeeze-out state;
  (5) CBOM 2025 undisclosed control change + tier A→B; (6) RGSS deadline detail.
- **Structural defect found and repaired:** strict re-parsing revealed **9 of 75 CSV rows were
  malformed** (unescaped commas inside unquoted fields: TGKA, UPRO, MTSS; duplicated spurious
  stake/route field pairs: CHMK, RASP, ROLO, RUAL, UKUZ, MGTS, MTSS; plus a TGKA column
  misplacement that parked the temp-admin note in the evidence-tier column). The construction-phase
  validation had missed these because `csv.DictReader` silently absorbs extra fields. All rows
  were repaired and the file rewritten with proper quoting via `csv.writer`.
- **Post-repair consistency (all PASS):** 75 rows × 14 columns; ticker set exactly matches the
  panel; every SOE=1 row has a named holder, numeric stake ≥25, valid state_level
  (federal 28 / federal+regional 4 / regional 3); the six SOE=0 rows that mention a state-related
  holder carry explicit sub-threshold / prefs-only / title-only tags; golden_share = {TATN, BANE,
  YNDX}; temp_admin = {UPRO, TGKA}; report determination table ↔ CSV agree **75/75**.

### 2.5 Monitoring list (re-check before locking the thesis panel)

| Ticker | Open issue | Trigger date / condition |
|---|---|---|
| RGSS | VTB sale of Rosgosstrakh to unnamed private buyers | Target end-2026; hard deadline 2027-04-01 → would flip to SOE=0 |
| PIKK | Squeeze-out by AO «Недвижимые активы» (98.0071%; beneficiaries undisclosed) + delisting | Record date 2026-10-20 → flip only if a state owner emerges (none documented) |
| CBOM | 2025 change of controlling owner undisclosed; CRKI shows 54.35% of votes changed hands Jul-2026 | Any disclosure naming a state-linked owner → flip |
| BANE | Bashkortostan 25%+1 partial sale | Through mid-2026; SOE=1 robust either way (Rosneft 57.7%) |
| AFLT | Announced sale of 23.76% federal stake | 2026 plan; RF keeps ≈50% even if completed |

---

## 3. Part II — Attention-channel selection, ASVI construction & data-quality registry

### 3.1 Concepts: SVI and ASVI

- **SVI(t)** = raw weekly Wordstat search count for a query in week t.
- **ASVI(t)** = `ln(SVI(t)) − median( ln(SVI(t−1)), …, ln(SVI(t−8)) )` — the log deviation of
  current search attention from the trailing 8-week median baseline.

> **Explanation — why this form.** Search counts are strongly right-skewed and multiplicative
> (attention spikes scale with a stock's baseline popularity), so the log transform stabilizes
> variance and makes attention comparable across companies of very different search popularity.
> The *median* (not mean) baseline is robust to one-off spikes inside the window. `ln(0)` is
> undefined, so any week with SVI=0 or a missing week is **invalid**: ASVI(t) exists only if
> SVI(t) and all 8 prior grid weeks are valid. The first possible ASVI week is 2018-10-22, so the
> maximum attainable count is 419 − 8 = **411 weeks** per company-series. A zero week costs that
> week *plus up to 8 following weeks* of ASVI — which is why dead/sparse channels matter (§3.3).

### 3.2 The two-channel problem and the decision rule

For each company, ASVI is computed **separately** on the ticker channel and the Cyrillic channel,
and the two ASVI series are correlated (Pearson) on overlapping valid weeks. Pre-specified rule:

- **corr ≥ 0.85** → the channels measure the same attention construct; keep ONE channel (the one
  with fewer missing/zero weeks; documented tie-break in favour of the ticker channel — never
  invoked because the branch never triggered).
- **corr < 0.85** → the channels carry independent information; **sum the two RAW SVI series
  week-by-week** (a week counts if either channel is positive; zero/missing contributes 0), then
  compute a **single final ASVI from the summed raw series**.

> **Explanation — why sum raw SVI, never sum computed ASVI.** ASVI is a nonlinear transform
> (log-deviation from a rolling median). Summing two ASVI series double-counts baseline variation
> and mishandles weeks where only one channel is valid; summing raw counts *before* the transform
> produces one well-defined attention series whose baseline is the median of the *total* attention.
> All decisions were criteria-based; no regression outcome was consulted at any point.

### 3.3 Results — 74 non-Yandex companies

**Headline: no company reaches the 0.85 keep-one threshold; all 74 decisions are SUM RAW.**

| Statistic (74 companies) | Value |
|---|---|
| corr ≥ 0.85 → keep one channel | **0** |
| corr < 0.85 → SUM RAW | **73** |
| corr undefined (overlap n < 3) → SUM RAW by documented special case | **1** (SFIN) |
| Correlation range | 0.0598 (AVAN) … **0.8066 (TRMK, max)** |
| Correlation median / mean | 0.573 / 0.521 |
| Companies reaching full 411 ASVI weeks after summation | **68 / 74** |
| Below 411 | MRKK 282 · MRKC 344 · MRKS 377 · MRKU 382 · UKUZ 393 · SFIN 395 |

> **Explanation — what the correlations say.** The two channels share a moderate common component
> (median ρ ≈ 0.57) but carry largely independent noise: Latin-ticker queries skew
> trader/terminal-style, Cyrillic "<name> акции" queries skew retail/news-driven. The lowest
> correlations (AVAN 0.06, BANE 0.08, RASP 0.09, FEES 0.13, MGTS 0.16, UTAR 0.16) are cases where
> one channel tracks a different information pattern (e.g., corporate-action news searched by name
> vs ticker). Summation is therefore the rule-consistent — and information-preserving — outcome
> everywhere.

**Coverage benefit of SUM RAW.** Summing raises ASVI coverage relative to either channel alone:
68/74 companies reach the full 411 weeks; the six exceptions lose weeks only because a channel was
dead for a multi-year stretch (Rosseti-subsidiary Cyrillic queries were zero pre-rebrand;
SFIN's Cyrillic query «СФИ акции» returns 0 in 389 of 419 weeks after the SAFMAR→SFI rename — its
sum is effectively the ticker channel alone, the least-destructive option, requiring no
discretion). **Every company ends with ≥282 valid ASVI weeks — no company is unusable.**

Zero-week channel patterns (>100 zeros; full detail in registry row R16): single early contiguous
blocks for renamed/low-attention queries (IRKT-Cyrillic 257 zeros to 2023-07-24, pre-dating the
Irkut→Yakovlev rename; KMAZ-ticker 176 to Jan-2022; MRK-family Cyrillic pre-rebrand blocks) and
persistent thin-search sparsity (VJGZ-ticker 226 scattered, UKUZ-ticker 206, YAKG-ticker 171,
SFIN-Cyrillic 389).

The full per-company table (corr, overlap, per-channel zero counts, final ASVI coverage, decision)
is **Appendix B** / `group3/channel_selection.csv`.

### 3.4 Yandex — pre-decided combination, confirmed and quantified

**Applied exactly as pre-decided (not re-derived):** final SVI(t) = raw «YNDX» ticker-search +
raw «Яндекс акции» Cyrillic-search. **`YDEX.csv` is never read by the summation code** (routed to
a separate slot by the enumerator; verified structurally by code inspection and empirically by the
verifier's leakage test — adding YDEX changes the sum, proving exclusion).

Continuity checks (all recomputed from raw files):

| Check | Result |
|---|---|
| Weeks in summed raw series | 419 / 419 |
| Zero-count weeks in the sum | **0** (minimum weekly total: 3,381 in the 2018-12-31 holiday week) |
| Valid ASVI weeks | **411 / 411 possible** (2018-10-22 → 2026-08-31) |
| Soft-gap runs (sum < 50% of trailing 26-week median) | **0** |
| ASVI range / std | [−0.60, +2.08] / 0.298 — peak at the 2022-02-21 invasion week (raw total 143,979) |
| 2024 relisting transition | «YNDX» query shows **no level break** across 2024-07-24 (6,261 → 6,471 → 6,389 spanning weeks); Cyrillic declines gradually; sum continuous |

> **Explanation — what excluding YDEX avoids.** YDEX search volume is 0 until Nov-2021, sporadic
> and small through 2023 (<500/wk), then explodes to 30k–92k/week from 2024-07-22 (the MOEX
> relisting). Including it would multiply the series ≈2.5–3× mid-sample — an artificial level
> shift creating spurious positive ASVI for 8 weeks and distorting every median baseline spanning
> it. The verifier quantified this precisely: **99 weeks would be inflated >1.5×, starting exactly
> 2024-07-22**. Since YNDX shows no behavior change at the transition, YDEX is redundant.
> Final series: `group3/yandex_final_series.csv` (419 weeks, SVI + ASVI).

### 3.5 Consolidated data-quality registry (R1–R17)

Machine-readable: `group3/data_quality_registry.csv`; supporting scans: `tick_rv_scan.csv`,
`gap_scan.csv`, `dq_scan_output.txt`. Each Group-1-reported issue was re-derived from raw files
and either CONFIRMED, CONFIRMED-&-REFINED, or CORRECTED; systematic scans then added new items.
Full text of all 17 rows: **Appendix C**. Summary:

**Confirmed/refined from Group 1:**
- **R1 — Artifact week 2024-06-16.** The weekly bar is *entirely absent* from **72 of 75** files
  (Group 1 said "~55"); only GAZP, SBER, VTBR carry genuine bars (SBER −1.67%, GAZP −5.35% — the
  market demonstrably traded, so absence is a data artifact, not a holiday). → Exclude the week
  label from any cross-sectional event/crisis detection.
- **R4 — ROLO quantization.** All 414 closes sit on a 1-kopeck grid at prices 0.18–1.77₽ → tick =
  0.6–5.6% of price (1.54% at median); 19.8% of weeks have H−L ≤ 2 ticks (RV purely mechanical);
  median RV = 1.39× panel. Group 1's "₽0.20 tick" corrected: 0.20₽ was the *price level*, the tick
  is 1 kopeck. → Exclude ROLO from RV-based tests or use tick-adjusted RV = (H−L−tick)/C.
- **R5 — USBN reclassified.** Sub-kopeck *price scale* confirmed (2022 closes ≈0.059–0.077₽) but
  the file quotes 4 decimals throughout → **no tick quantization** (0% tick-bound weeks; median RV
  1.26× panel). "Quantization" label corrected to "small absolute price scale" — watch item only.
- **R8/R9/R10 — gaps confirmed:** LSNG 8 weeks (2020-03-22…05-10), MSTT 3 weeks
  (2020-09-20…10-04), FEES 2 holiday weeks (2022-12-25, 2023-01-01). → Keep as gaps, no
  forward-fill.
- **R11 — YNDX relisting gap refined:** last real bar 2024-06-09; 2024-06-23 is a flat
  placeholder (4071.2 all fields, empty volume); labels 06-16/06-30/07-07/07-14 absent; real bars
  resume 2024-07-21 → 5 consecutive labels without a real bar. The *search* series is unaffected
  (411/411 ASVI weeks).
- **R14/R3 — Feb–Mar 2022 suspension structure detailed:** labels 2022-03-06 and 2022-03-13 exist
  in **no** file; 2022-02-27 exists only as flat empty-volume placeholders in **12** files (AVAN,
  BSPB, CBOM, AKRN, ABRD, AFKS, BANE, ALRS, BLNG, CHMF, CHMK, AFLT) — these must be treated as
  missing or they inject RV=0 into trailing baselines; the reopening bar 2022-03-20 is present in
  26 / absent in 49 (refines Group 1's "~55").

**New items (not in Group 1):**
- **R2 — Panel-wide volume artifact 2024-08-25:** bar present 75/75 with valid non-flat OHLC, but
  **Vol. empty in 73/75** (only VTBR, YNDX retained). → Exclude from volume-based metrics; price
  metrics usable.
- **R6/R7 — Systematic tick/RV scan of all 75:** after ROLO, the largest tick-to-price ratios are
  0.28% (MGTS, ABRD), 0.27% (JNOS), 0.22% (UKUZ) — immaterial. Sub-kopeck TGKA (6 decimals) and
  FEES (4 decimals) have no quantization. The high-RV cohort (VJGZ 1.51×, MRKS 1.50×, BLNG 1.46×,
  IRKT 1.46×, FESH 1.42×, YAKG 1.41×, RGSS 1.39×, UNAC 1.37×) has ~0% tick-bound weeks — genuinely
  thin/volatile names, not artifacts. **No company beyond ROLO needs RV exclusion.**
- **R12 — AVAN early illiquidity:** 11 missing weeks in Sep-2018–Feb-2019 incl. three ≥2-week
  runs; AVAN trailing-52w baselines before ~Sep-2019 use <52 observations.
- **R13 — Single-week omissions (informational):** KAZT 2018-10-28, ABRD 2018-09-30, MFGS
  2018-12-30, VJGZ 2018-12-30 & 2019-01-27. Complete scan confirms **no other ≥2-week
  undocumented gap exists anywhere in the panel**.
- **R15 — GMKN** is the only company with a bar at panel-start label 2018-08-19.
- **R16 — Wordstat zero-week channels** (search side; §3.3) — mitigated by SUM RAW.
- **R17 — File integrity: clean.** 0 parser-dropped rows; 0 unparseable OHLC; 0 rows where the
  recomputed weekly change deviates from the file's own Change % by >2pp; no duplicate week
  labels; K/M/B volume suffixes consistent.

### 3.6 Independent verification (Group 3)

**Method:** a from-scratch harness (`g3_verify.py`) with a *different* parser (pandas+regex vs the
construction agent's hand-rolled line parser) and a *different* ASVI implementation (vectorized
`shift(1).rolling(8).median()` with an explicit 8-valid-prior gate vs a loop). Agreement between
two independent stacks is itself evidence.

**Result: 7/7 PASS, 0 FAIL, 0 WARNING** (detail: Appendix D.2):

1. **Correlations:** 20 companies recomputed (stratified: lowest AVAN, highest TRMK, dead-channel
   SFIN, quantized ROLO, sparse-channel KMAZ/VJGZ/UKUZ/IRKT/MRKU, plus SBER, BSPB, GAZP, MGNT,
   PLZL, AFLT, RTKM, HYDR, PIKK, NMTP, TGKA) — **20/20 match to 4 decimals**; rule-consistency
   swept across all 74 decisions — **0 violations** (max corr 0.8066 < 0.85 ⇒ all-SUM-RAW is the
   only rule-consistent outcome).
2. **Yandex:** verifier-rebuilt sum identical at every one of 419 weeks (max abs diff 0.0);
   0 zero weeks; 411/411 ASVI; YDEX leakage test proves exclusion and quantifies the 99 inflated
   weeks from 2024-07-22.
3. **Registry:** R1 (72/75 missing, 3 present), R4 (ROLO every figure), R9 (MSTT 3 weeks), R2
   (volume empty 73/75) all re-derived from raw files — exact matches.
4. **False negatives:** 12 unflagged companies (SBER, LKOH, GMKN, NLMK, MAGN, MTSS, TATN, NVTK,
   CHMF, MSNG, OGKB, ABRD) scanned for missing runs, duplicates, placeholders, empty volume,
   change-% inconsistency — **no undocumented issues**.
5. Both Group-1 corrections (R1 count; R5 reclassification) independently **upheld**.

**Readiness statement (verifier):** Group 3's output is ready for final modeling; downstream users
must apply the registry's handling recommendations (consolidated in §4.3).

---

## 4. Integrated usage guidance for the downstream analysis

### 4.1 The SOE dummy

- Use `soe` from `group2/soe_classifications.csv` (0/1, no missing). Reference vintages vary by
  company (`ref_year` column) because post-2022 disclosure is partial — the dummy means "state
  ownership at the most recent reliable disclosure", not a full time-varying panel.
- **Federal-only robustness:** restrict to `state_level == "federal"` (drops TATN, TTLK, UTAR to
  0; keeps the 4 federal+regional dual-route cases). The `REGIONAL` flag makes this one filter.
- **Knife-edge robustness:** VSMO (Rostec exactly 25%+1) is the single flip-test candidate.
- **If a time-varying dummy is ever needed:** MGNT is the only documented mid-sample change
  (state 2018–2021, private thereafter).
- **Before locking the panel:** re-check the §2.5 monitoring list (RGSS, PIKK, CBOM, BANE, AFLT).

### 4.2 The attention variable (ASVI)

- For the 74 non-Yandex companies: build ASVI from the **summed raw SVI** per `channel_selection.csv`
  (all decisions SUM RAW); coverage per company is in the `asvi_final_n` column (≥282 weeks,
  68/74 at the full 411).
- For Yandex: use `group3/yandex_final_series.csv` as-is (411/411 weeks; YDEX excluded).
- Do not sum computed ASVI series; do not average channels; do not re-derive the Yandex rule.

### 4.3 Mandatory data-handling rules (from the registry)

1. **Exclude week label 2024-06-16** from any cross-sectional event/crisis detection (only 3 of
   75 companies observable). The 3 existing bars may stay in single-company series. (R1)
2. **Exclude week 2024-08-25 from volume-based metrics** (Vol. empty in 73/75); price metrics
   usable. (R2)
3. **Drop the twelve 2022-02-27 flat placeholder bars** (treat as missing; never forward-fill)
   before any RV/volume/return computation. (R3)
4. **Exclude ROLO from RV-based tests** or use tick-adjusted RV = (H−L−tick)/C. (R4)
5. **Keep all documented gaps as gaps** (no forward-fill): LSNG 2020, MSTT 2020, FEES 2022-23
   holidays, YNDX Jun–Jul 2024 (incl. the 2024-06-23 placeholder), AVAN early illiquidity,
   suspension weeks 2022-02-27…2022-03-20 per R14 structure. (R8–R12, R14)
6. **USBN, TGKA, FEES sub-ruble scales:** fine-decimal quotes — no quantization adjustment
   needed; remember ratio-based metrics are noise-sensitive at these price levels. (R5, R6)
7. Suspension-week event tests must use the reopening bar (2022-03-20, present in 26 files) and
   be flagged. (R14)

### 4.4 How the two parts interact in the thesis design

The SOE dummy partitions the panel 35/40; ASVI is the attention treatment variable. The registry
guarantees that attention-episode windows (RV/volume spikes, crisis weeks) are not contaminated by
data artifacts: the 2022-02-27 placeholders and the 2024-06-16/2024-08-25 artifact weeks are
exactly the kind of weeks that would otherwise manufacture spurious "abnormal" episodes. The SOE
special cases (§2.3) identify the rows where the state channel is administrative (UPRO, TGKA),
symbolic (golden shares), or in flux (RGSS, CBOM) — heterogeneity results involving these names
should be read with their flags.

---

## 5. Deliverables and reproducibility

| File | Content |
|---|---|
| `group2/GROUP2_SOE_REPORT.md` | Part I methodology + per-company evidence log (17 batches) + 75-row determination table + verification-complete note (723 lines) |
| `group2/soe_classifications.csv` | **Primary SOE deliverable** — 75 rows × 14 cols (ticker, company, sector, soe, state_level, state_holder, state_stake_pct, route, ref_year, golden_share, temp_admin, evidence_tier, notes, primary_sources); repaired & validated |
| `group2/GROUP2_VERIFICATION_REPORT.md` | Part I verification: 5 batches (V-A…V-E), 22 fresh re-checks, verdict table, consistency checks, monitoring list (359 lines) |
| `group3/GROUP3_CONSTRUCTION_REPORT.md` | Part II methodology + all computed results (§A–§E) |
| `group3/channel_selection.csv` | **Primary channel deliverable** — 74 rows + YNDX row: corr, per-channel zero counts, decision, ASVI coverage |
| `group3/yandex_final_series.csv` | Final Yandex SVI+ASVI, 419 weeks (YDEX excluded) |
| `group3/data_quality_registry.csv` | **Primary registry deliverable** — R1–R17 with evidence + handling |
| `group3/tick_rv_scan.csv`, `gap_scan.csv`, `dq_scan_output.txt` | Supporting scans (per-company tick/RV; every gap; console evidence) |
| `group3/g3_common.py`, `g3_channels.py`, `g3_registry.py`, `g3_verify.py` | All code — `python3 g3_channels.py && python3 g3_registry.py` regenerates every Part-II number; `python3 g3_verify.py` reproduces the verification |
| `VERIFICATION_REPORT.md` (thesis_work root) | Group 1 sanctions/crisis report (upstream input) |

Every number in this paper traces to one of the files above; Part I citations are listed per
company in the CSV `primary_sources` column and the construction evidence log; Part II numbers are
deterministic functions of the raw `Data/` folder.

---

## Appendix A — SOE determination table (all 75)

Legend: SOE **1** = state ≥25% of voting shares (bold); "REG" = regional/sub-federal primary
route; tier A = official/≥2 concordant sources, B = documented opacity.

| # | Ticker | Company (from raw Data/ folder) | Sector | SOE | Route / owner (state block or controller) | Ref. year | Evidence tier |
|---|--------|--------------------------------|--------|-----|-------------------------------------------|-----------|---------------|
| 1 | AVAN | AKB Avangard PAO | Banking | 0 | K. Minovalov 99.27%+0.72% via Alkor Holding | 2018–19 | A (CBR control list) |
| 2 | BSPB | Bank Saint-Petersburg | Banking | 0 | Saveliev ~24% + UK Vernye Druzya ~28% + dispersed | 2016–18 | A (bank disclosure) |
| 3 | CBOM | Moskovskiy Kreditnyi Bank | Banking | 0 | Rossium/Sudarikov ("Region") control since Oct-2024 | 2024–25 | A (news+disclosure) |
| 4 | SBER | Sberbank Rossii | Banking | **1** | RF (MinFin) 50%+1 = 52.32% of votes | 2020–25 | A |
| 5 | USBN | Bank Uralsib | Banking | 0 | L. Kogan 81.81% (inherited Dec-2019), Tsvetkov 11.35% | 2019–23 | A (bank disclosure) |
| 6 | VTBR | Bank VTB | Banking | **1** | RF/Rosimushchestvo 60.9% of common | 2016–24 | A |
| 7 | AKRN | Akron | Chemicals | 0 | Kantor structures ~70% (45.1% in mgmt trust mgmt since 2022) | 2022 | A |
| 8 | KAZT | KuybyshevAzot | Chemicals | 0 | Gerasimenko family + mgmt vehicles (36.29%+…) | 2023–25 | A (company IR) |
| 9 | KZOS | Kazanorgsintez (Organicheskiy Sintez Kazan) | Chemicals | 0 | SIBUR via TAIF ~64%; SINKH (Tatarstan) 19.87% <25% | 2021–22 | A |
| 10 | NKNC | Nizhnekamskneftekhim | Chemicals | 0 | SIBUR via TAIF ~83% | 2021–22 | A |
| 11 | PHOR | PhosAgro | Chemicals | 0 | Guryev family 43.7%, T. Litvinenko 20.6% | 2022–26 | A |
| 12 | ABRD | Abrau-Durso | Consumer&Retail | 0 | Titov family ~90% (Aktiv Kapital 57.75% + P. Titov 31.95%) | 2025 | A |
| 13 | GCHE | Cherkizovo Group | Consumer&Retail | 0 | Mikhailov/Babayev family ~82% | 2019–24 | A |
| 14 | MGNT | Magnit | Consumer&Retail | 0 | Marathon Group (Vinokurov) 29.75%; **VTB held 29.1% 2018–21, exited** | 2025 | A + special note |
| 15 | MVID | M.Video-Eldorado | Consumer&Retail | 0 | B. Uzhakhov 53.633% (MBO Jul-2024); PSB talks 2025 not completed | 2024–25 | A (company disclosure) |
| 16 | AFKS | AFK Sistema | Diversified | 0 | V. Evtushenkov 49.2% + F. Evtushenkov 15.2% | 2022–25 | A |
| 17 | SFIN | SFI (SAFMAR Fin. Investments) | Diversified | 0 | S. Gutseriev structures ~85% | 2021 | A |
| 18 | BANE | Bashneft | Energy | **1** | Rosneft 57.7%; Bashkortostan 25%+1 until mid-2026 (partial sale); golden share | 2023–26 | A |
| 19 | GAZP | Gazprom | Energy | **1** | RF 50.23% (Rosimushch. 38.37 + Rosneftegaz 10.97 + Rosgazifikatsiya 0.89) | 2018–24 | A |
| 20 | JNOS | Slavneft-YANOS | Energy | **1** | via NGC Slavneft (99.7% Rosneft+GPN parity) | 2021 | A (exact sub-% for verifier) |
| 21 | LKOH | LUKOIL | Energy | 0 | private; Alekperov ~30%, Fedun sold ~10% (2025, buyer undisclosed) | 2021–25 | B (register opaque) |
| 22 | MFGS | Slavneft-Megionneftegaz | Energy | **1** | Slavneft 56.424% ← Rosneft+GPN | 2021 | A |
| 23 | NVTK | NOVATEK | Energy | 0 | Mikhelson 24.76%, Timchenko 23.49%, Total 19.4%, **Gazprom only 9.9%** | 2018–26 | A |
| 24 | RNFT | RussNeft | Energy | 0 | S-S Gutseriev 37.15% voting; state banks hold PREFS only (Trust 19.23%, VTB 8.48% — non-voting) | 2021–24 | A + special note |
| 25 | ROSN | Rosneft | Energy | **1** | Rosneftegaz (100% RF) 40.4% | 2021–23 | A |
| 26 | SIBN | Gazprom Neft | Energy | **1** | Gazprom 95.7% (Gazprom = RF 50.23%) | 2024 | A |
| 27 | SNGS | Surgutneftegas | Energy | 0 | ownership never disclosed; ~70% closed mgmt structures; no state block ≥25% in any disclosure | 2017–25 | B (opaque; caveat documented) |
| 28 | TATN | Tatneft | Energy | **1 REG** | SINKH (100% Tatarstan) 27.23% charter = 29% votes; Republic ≈34% common; golden share | 2023 | A |
| 29 | VJGZ | Varyoganneftegaz | Energy | **1** | via Slavneft-Megionneftegaz ← Slavneft ← Rosneft+GPN | 2021 | A (exact sub-% for verifier) |
| 30 | KMAZ | KAMAZ | Industrial | **1** | Rostec 47.1% (49.9% earlier); Avtoinvest 23.54% private | 2019–23 | A |
| 31 | RGSS | Rosgosstrakh | Insurance | **1** | VTB Group >99% (via Otkritie, Dec-2022); sale announced 2025–26, not completed | 2023–26 | A + FLAG |
| 32 | ALRS | ALROSA | Metals&Mining | **1** | RF/Rosimushchestvo 33.03% (+Yakutia 25%+1, uluses 8% — dual route) | 2024–26 | A |
| 33 | BLNG | Belon | Metals&Mining | 0 | MMK group >95% ← Rashnikov | 2013 | A |
| 34 | CHMF | Severstal | Metals&Mining | 0 | Mordashov 77.03% via Severgroup | 2024–26 | A |
| 35 | CHMK | Chelyabinsk Met. Kombinat | Metals&Mining | 0 | via Mechel (control) ← Zyuzin family >50% | 2023 | A |
| 36 | GMKN | Nornickel | Metals&Mining | 0 | Interros 37%, Rusal 26.4%, Crispian ~4% — all private | 2025–26 | A |
| 37 | MAGN | MMK | Metals&Mining | 0 | Rashnikov 79.76% (OOO Altair since 2022) | 2022–23 | A |
| 38 | NLMK | NLMK | Metals&Mining | 0 | Lisin via Fletcher ~79.4% | 2020 | A |
| 39 | PLZL | Polyus | Metals&Mining | 0 | Islamic Support Fund 46.35% + Akropol 29.99% (Said Gutseriev) | 2022 | A |
| 40 | RASP | Raspadskaya | Metals&Mining | 0 | Evraz group 90.9–93.24% ← Abramovich/Abramov/Frolov | 2021–25 | A |
| 41 | ROLO | Rusolovo | Metals&Mining | 0 | Seligdar 85.12% ← OOO Maximus 50.62% (Beyrit/Tatarinov/Vasilievs) | 2024 | A |
| 42 | RUAL | UC Rusal | Metals&Mining | 0 | En+ 56.88% ← Deripaska 44.95% (VTB exited En+ Feb-2020) | 2019–20 | A + special note |
| 43 | SELG | Seligdar | Metals&Mining | 0 | OOO Maximus ~50.6% (4 private individuals) | 2024 | A |
| 44 | TRMK | TMK | Metals&Mining | 0 | 90.6% Pumpyansky stake re-registered to TMK top managers Mar-2022; OFAC "state" tag = supplier note | 2022–24 | A + FLAG |
| 45 | UKUZ | UK Yuzhnyi Kuzbass | Metals&Mining | 0 | via Mechel >95% ← Zyuzin family | 2008–23 | A |
| 46 | VSMO | VSMPO-AVISMA | Metals&Mining | **1** | Rostec (RT-Razvitie biznesa) 25%+1 — exactly at threshold; Shelkov 65.27% | 2023–25 | A (knife-edge) |
| 47 | LSRG | LSR Group | Real Estate | 0 | A. Molchanov 55.1% (+E. Molchanov 11%, mgmt 6.9%) | 2024–25 | A |
| 48 | MSTT | Mostotrest | Real Estate | 0 | Stroyproektholding (A. Rotenberg) 96.97%; VEB.RF link only via non-listed Natproektstroy | 2024 | A + special note |
| 49 | PIKK | PIK | Real Estate | 0 | VTB fully exited 2023-08-22 (12.36% sold); Gordeev 15.15% (2025), register partly opaque | 2023–25 | A + caveat |
| 50 | IRKT | Yakovlev (ex-Irkut) | Tech | **1** | via UAC ≥85% ← Rostec 92.31% | 2016–23 | A |
| 51 | UNAC | UAC | Tech | **1** | Rostec 92.31% (+VEB.RF 5.6%) | 2020–26 | A |
| 52 | YNDX | Yandex (MKAO) | Tech | 0 | ZPIF Konsortsium.Pervyi (mgmt-led private consortium); **golden shares: FOI + Fond menedzherov — recorded separately, no state equity** | 2024 | A + golden-share note |
| 53 | MGTS | Moscow City Telephone Network | Telecom | 0 | MTS 94.7% (99.1% of common) ← Sistema 42.09% | 2025 | A (company IR) |
| 54 | MTSS | MTS | Telecom | 0 | AFK Sistema 42.09% (private) | 2022–26 | A |
| 55 | RTKM | Rostelecom | Telecom | **1** | Rosimushchestvo 38.2% common + VTB 8.44% + Telecom Investitsii 20.98% + VEB 3.36% | 2025 | A |
| 56 | TTLK | Tattelekom | Telecom | **1 REG** | SINKH (Tatarstan) 87.21% | 2025 | A |
| 57 | AFLT | Aeroflot | Transportation | **1** | RF/Rosimushchestvo 73.77% (post-2022; 51.17→57.34 earlier); 2026 sale plan ≠ completed | 2022–26 | A |
| 58 | FESH | DVMP (FESCO) | Transportation | **1** | Rosatom 92.5% consolidated (post-2018 seizure of Magomedov's stake); Nov-2025 pledge; Jun-2026 JV 51/49 w/ DP World — state ≥25% under either | 2023–26 | A |
| 59 | NMTP | NMTP | Transportation | **1** | Transneft 60.62% + Rosimushchestvo 20% direct + RZhD 5.3% | 2018–23 | A |
| 60 | UTAR | UTair Aviation | Transportation | **1 REG** | KhMAO 38.8% + Tyumen Oblast 8.4% (AK-Invest/NPF 50.1% private) | 2020 | A (verifier: post-2020 check) |
| 61 | FEES | Rosseti (FSK-Rosseti) | Utilities | **1** | RF 77.02% placed (88.04% pre-merger; AR2023: 75.28%) | 2023–26 | A |
| 62 | HYDR | RusHydro | Utilities | **1** | Rosimushchestvo 62.2% (+VTB 12.37%) | 2024 | A |
| 63 | IRAO | Inter RAO | Utilities | **1** | Rosneftegaz 26.37–27.63% + FSK 8.57% (≈35% federal combined) | 2022–24 | A (spread noted) |
| 64 | LSNG | Lenenergo (Rosseti Lenenergo) | Utilities | **1 REG** | Rosseti 69.36% + Saint Petersburg 28.80% (dual route) | 2023–26 | A |
| 65 | MRKC | Rosseti Centr | Utilities | **1** | Rosseti 50.69% | 2023–25 | A |
| 66 | MRKK | Rosseti Severnyi Kavkaz | Utilities | **1** | Rosseti 99.76% | 2023 | A |
| 67 | MRKP | Rosseti Centr i Privolzhye | Utilities | **1** | Rosseti 50.41% | 2023–24 | A |
| 68 | MRKS | Rosseti Sibir | Utilities | **1** | Rosseti 57.90% | 2023 | A |
| 69 | MRKU | Rosseti Ural | Utilities | **1** | Rosseti 55.23% | 2023 | A |
| 70 | MSNG | Mosenergo | Utilities | **1** | GPH 53.5–53.8% (←Gazprom←RF) + Moscow 26.4% | 2019–24 | A |
| 71 | MSRS | Rosseti Moscow Region | Utilities | **1** | FSK-Rosseti 50.90% (+GPB 9.38%) | 2026 | A (official IR) |
| 72 | OGKB | OGK-2 | Utilities | **1** | GPH ~73.4% (50.3–77% across sources) | 2019–25 | A |
| 73 | TGKA | TGK-1 | Utilities | **1** | GPH 51.8%; Fortum's 29.45% foreign-owned (Decree-302 temp admin covers PAO Fortum itself, not this block) | 2024–25 | A + temp-admin note |
| 74 | UPRO | Unipro | Utilities | 0 | **Uniper SE retains title to 83.73%; RF temporary administration (Decree 302, 2023) — NOT ownership transfer** | 2023–24 | A + TEMP-ADMIN flag |
| 75 | YAKG | YATEK | Energy | 0 | A. Avdolyan ≈44% direct+indirect (A-TEK 45% / A-Property 20.16% / ZPIF Yunion 25%, post-2023) — all private | 2019–26 | A |

## Appendix B — Channel selection (74 non-Yandex companies)

corr = Pearson correlation of ticker-channel vs Cyrillic-channel ASVI on overlapping weeks;
"zeros ticker/cyr" = missing-or-zero weeks per raw channel; ASVI weeks (final) = valid weeks of the
single ASVI built from the summed raw series. All decisions: SUM RAW (max corr 0.8066 < 0.85).
Yandex is excluded here — its combination was pre-decided (§3.4).

| Ticker | Sector | corr | Overlap n | ASVI weeks (final) | Zero/missing weeks: ticker / cyr | Decision |
|--------|--------|------|-----------|--------------------|-----------------------------------|----------|
| AVAN | Banking | 0.0598 | 356 | 411 | 0 / 16 | SUM RAW |
| BSPB | Banking | 0.2102 | 411 | 411 | 0 / 0 | SUM RAW |
| CBOM | Banking | 0.5001 | 411 | 411 | 0 / 0 | SUM RAW |
| SBER | Banking | 0.5586 | 411 | 411 | 0 / 0 | SUM RAW |
| USBN | Banking | 0.4282 | 366 | 411 | 6 / 0 | SUM RAW |
| VTBR | Banking | 0.7083 | 411 | 411 | 0 / 0 | SUM RAW |
| AKRN | Chemicals | 0.4571 | 364 | 411 | 12 / 0 | SUM RAW |
| KAZT | Chemicals | 0.6647 | 302 | 411 | 25 / 0 | SUM RAW |
| KZOS | Chemicals | 0.2188 | 411 | 411 | 0 / 0 | SUM RAW |
| NKNC | Chemicals | 0.5890 | 366 | 411 | 15 / 0 | SUM RAW |
| PHOR | Chemicals | 0.6538 | 411 | 411 | 0 / 0 | SUM RAW |
| ABRD | Consumer&Retail | 0.4440 | 411 | 411 | 0 / 0 | SUM RAW |
| GCHE | Consumer&Retail | 0.6705 | 339 | 411 | 30 / 0 | SUM RAW |
| MGNT | Consumer&Retail | 0.2317 | 411 | 411 | 0 / 0 | SUM RAW |
| MVID | Consumer&Retail | 0.5115 | 411 | 411 | 0 / 0 | SUM RAW |
| AFKS | Diversified | 0.7104 | 411 | 411 | 0 / 0 | SUM RAW |
| SFIN | Diversified | undef. (n<3) | 0 | 395 | 2 / 389 | SUM RAW (corr undefined, n<3; dead channel, documented) |
| BANE | Energy | 0.0818 | 411 | 411 | 0 / 0 | SUM RAW |
| GAZP | Energy | 0.6749 | 411 | 411 | 0 / 0 | SUM RAW |
| JNOS | Energy | 0.3443 | 411 | 411 | 0 / 0 | SUM RAW |
| LKOH | Energy | 0.6268 | 411 | 411 | 0 / 0 | SUM RAW |
| MFGS | Energy | 0.3347 | 234 | 411 | 31 / 0 | SUM RAW |
| NVTK | Energy | 0.7193 | 411 | 411 | 0 / 0 | SUM RAW |
| RNFT | Energy | 0.7380 | 334 | 411 | 22 / 0 | SUM RAW |
| ROSN | Energy | 0.6662 | 411 | 411 | 0 / 0 | SUM RAW |
| SIBN | Energy | 0.6703 | 411 | 411 | 0 / 0 | SUM RAW |
| SNGS | Energy | 0.6474 | 411 | 411 | 0 / 0 | SUM RAW |
| TATN | Energy | 0.5647 | 411 | 411 | 0 / 0 | SUM RAW |
| VJGZ | Energy | 0.6741 | 40 | 411 | 226 / 0 | SUM RAW |
| KMAZ | Industrial | 0.5821 | 235 | 411 | 176 / 0 | SUM RAW |
| RGSS | Insurance | 0.3404 | 411 | 411 | 0 / 0 | SUM RAW |
| ALRS | Metals&Mining | 0.5306 | 411 | 411 | 0 / 0 | SUM RAW |
| BLNG | Metals&Mining | 0.4725 | 411 | 411 | 0 / 0 | SUM RAW |
| CHMF | Metals&Mining | 0.6947 | 411 | 411 | 0 / 0 | SUM RAW |
| CHMK | Metals&Mining | 0.2950 | 375 | 411 | 5 / 0 | SUM RAW |
| GMKN | Metals&Mining | 0.6407 | 411 | 411 | 0 / 0 | SUM RAW |
| MAGN | Metals&Mining | 0.4497 | 411 | 411 | 0 / 0 | SUM RAW |
| NLMK | Metals&Mining | 0.4605 | 411 | 411 | 0 / 0 | SUM RAW |
| PLZL | Metals&Mining | 0.6712 | 411 | 411 | 0 / 0 | SUM RAW |
| RASP | Metals&Mining | 0.0949 | 411 | 411 | 0 / 0 | SUM RAW |
| ROLO | Metals&Mining | 0.3708 | 411 | 411 | 0 / 0 | SUM RAW |
| RUAL | Metals&Mining | 0.6202 | 411 | 411 | 0 / 0 | SUM RAW |
| SELG | Metals&Mining | 0.6020 | 393 | 411 | 2 / 0 | SUM RAW |
| TRMK | Metals&Mining | 0.8066 | 411 | 411 | 0 / 0 | SUM RAW |
| UKUZ | Metals&Mining | 0.5730 | 115 | 393 | 206 / 6 | SUM RAW |
| VSMO | Metals&Mining | 0.6649 | 374 | 411 | 7 / 0 | SUM RAW |
| LSRG | Real Estate | 0.7009 | 411 | 411 | 0 / 0 | SUM RAW |
| MSTT | Real Estate | 0.6006 | 399 | 411 | 2 / 0 | SUM RAW |
| PIKK | Real Estate | 0.6520 | 411 | 411 | 0 / 0 | SUM RAW |
| IRKT | Tech | 0.7257 | 154 | 411 | 0 / 257 | SUM RAW |
| UNAC | Tech | 0.7169 | 411 | 411 | 0 / 0 | SUM RAW |
| MGTS | Telecom | 0.1590 | 411 | 411 | 0 / 0 | SUM RAW |
| MTSS | Telecom | 0.4660 | 411 | 411 | 0 / 0 | SUM RAW |
| RTKM | Telecom | 0.6453 | 411 | 411 | 0 / 0 | SUM RAW |
| TTLK | Telecom | 0.6620 | 281 | 411 | 54 / 0 | SUM RAW |
| AFLT | Transportation | 0.6287 | 411 | 411 | 0 / 0 | SUM RAW |
| FESH | Transportation | 0.4823 | 316 | 411 | 0 / 55 | SUM RAW |
| NMTP | Transportation | 0.6299 | 411 | 411 | 0 / 0 | SUM RAW |
| UTAR | Transportation | 0.1619 | 411 | 411 | 0 / 0 | SUM RAW |
| FEES | Utilities | 0.1290 | 411 | 411 | 0 / 0 | SUM RAW |
| HYDR | Utilities | 0.3625 | 411 | 411 | 0 / 0 | SUM RAW |
| IRAO | Utilities | 0.2898 | 411 | 411 | 0 / 0 | SUM RAW |
| LSNG | Utilities | 0.6336 | 356 | 411 | 8 / 0 | SUM RAW |
| MRKC | Utilities | 0.6869 | 255 | 344 | 26 / 155 | SUM RAW |
| MRKK | Utilities | 0.4812 | 112 | 282 | 23 / 197 | SUM RAW |
| MRKP | Utilities | 0.6534 | 258 | 411 | 0 / 152 | SUM RAW |
| MRKS | Utilities | 0.5450 | 268 | 377 | 5 / 116 | SUM RAW |
| MRKU | Utilities | 0.6382 | 170 | 382 | 4 / 213 | SUM RAW |
| MSNG | Utilities | 0.5651 | 411 | 411 | 0 / 0 | SUM RAW |
| MSRS | Utilities | 0.4808 | 283 | 411 | 0 / 122 | SUM RAW |
| OGKB | Utilities | 0.7392 | 402 | 411 | 1 / 0 | SUM RAW |
| TGKA | Utilities | 0.5125 | 411 | 411 | 0 / 0 | SUM RAW |
| UPRO | Utilities | 0.5358 | 411 | 411 | 0 / 0 | SUM RAW |
| YAKG | Utilities | 0.2947 | 115 | 411 | 171 / 0 | SUM RAW |

## Appendix C — Data-quality registry (R1–R17, condensed)

Full evidence and exact week lists: `group3/data_quality_registry.csv`.

| ID | Issue | Affected | Handling | Status vs Group 1 |
|----|-------|----------|----------|-------------------|
| R1 | Week 2024-06-16 bar entirely absent (files jump 06-09→06-23) | 72 of 75 (all but GAZP, SBER, VTBR) | Exclude week label from cross-sectional event/crisis detection | Confirmed & refined (G1 said ~55) |
| R2 | Week 2024-08-25: valid OHLC but Vol. empty | 73 of 75 (all but VTBR, YNDX) | Exclude from volume-based metrics | NEW |
| R3 | 2022-02-27 flat suspension placeholders (OHLC equal, vol empty) | 12 (AVAN BSPB CBOM AKRN ABRD AFKS BANE ALRS BLNG CHMF CHMK AFLT) | Treat as missing; never forward-fill | NEW detail |
| R4 | ROLO 1-kopeck grid at 0.18–1.77₽; 19.8% of weeks H−L ≤ 2 ticks; median RV 1.39× panel | ROLO | Exclude from RV tests or tick-adjust RV | Confirmed, description corrected |
| R5 | USBN sub-kopeck price scale but 4-decimal quotes — no quantization (0% tick-bound; RV 1.26×) | USBN | Watch item only | Corrected (was "quantization") |
| R6 | TGKA sub-kopeck prices, 6 decimals, RV normal (0.98×) | TGKA | No action | NEW (informational) |
| R7 | Panel-wide tick scan: after ROLO max tick/price 0.28%; high-RV cohort is genuine volatility | all 75 | No action beyond R4 | NEW |
| R8 | Gap 2020-03-22…2020-05-10 (8 weeks) | LSNG | Keep as gap | Confirmed |
| R9 | Gap 2020-09-20…2020-10-04 (3 weeks) | MSTT | Keep as gap | Confirmed |
| R10 | Holiday gaps 2022-12-25, 2023-01-01 | FEES | Keep as gap | Confirmed |
| R11 | Relisting gap: last real bar 2024-06-09; placeholder 06-23; labels 06-16/06-30/07-07/07-14 absent; resumes 07-21 (5 labels without real bar); search series unaffected (411/411) | YNDX | Keep as gap; placeholder = missing | Confirmed & refined |
| R12 | 11 missing weeks Sep-2018–Feb-2019 incl. three ≥2-week runs; early baselines <52 obs | AVAN | Keep as gaps; flag early event tests | NEW |
| R13 | Single-week omissions: KAZT 2018-10-28; ABRD 2018-09-30; MFGS 2018-12-30; VJGZ 2018-12-30 & 2019-01-27 | 5 companies | No action (informational) | NEW |
| R14 | Suspension structure: 2022-03-06 & 03-13 absent panel-wide; 02-27 placeholder/absent (R3/63); reopening 03-20 present in 26, absent in 49 | all 75 | Use reopening bar, flag suspension tests | Confirmed & refined |
| R15 | Only company with a bar at panel-start label 2018-08-19 | GMKN | No action (informational) | NEW |
| R16 | Wordstat zero-week channels >100 (SFIN-cyr 389; IRKT-cyr 257; VJGZ-tick 226; UKUZ-tick 206; MRKK-cyr 197; KMAZ-tick 176; YAKG-tick 171; MRK-family cyr blocks) | 12 channels | Mitigated by SUM RAW (all ≥282 ASVI weeks) | NEW |
| R17 | File integrity: 0 dropped rows, 0 unparseable OHLC, 0 change-% mismatches >2pp, no duplicate labels | all 75 | No action | NEW |

## Appendix D — Verification verdict tables

### D.1 Group 2 (SOE) — locked sample, all re-checked 2026-09-18

| # | Ticker | Construction call | Fresh-check sources | Verdict |
|---|--------|-------------------|---------------------|---------|
| 1 | UPRO | SOE=0 (temp admin ≠ ownership) | Unipro IR, Rosimushchestvo, RIA 2024 | **PASS** |
| 2 | TGKA | SOE=1 (GPH 51.8%) | TASS/Interfax/BBC 2023-04-26 | **PASS** (citation corrected, applied) |
| 3 | MFGS | SOE=1 (Slavneft chain) | fin-plan.org register | **PASS** (evidence strengthened ≈96.9% state-related) |
| 4 | JNOS/VJGZ | SOE=1 (Slavneft 99.7%) | inherit #3 chain | **PASS** |
| 5 | YAKG | SOE=0 (private, Avdolyan) | RBC/neftegaz 2023-10 | **PASS** (structure updated, applied) |
| 6 | RGSS | SOE=1 (VTB >99%) | ridus/Izvestia/RBC 2026 | **PASS-with-FLAG** (sale deadline 2027-04-01) |
| 7 | TATN | SOE=1 regional (SINKH 27.23% charter ≈29% votes) | investing.com 2024, ruwiki 2026 | **PASS** |
| 8 | NMTP | SOE=1 (Transneft ≈60.6 + RF 20) | ruwiki 2025, smart-lab register | **PASS** |
| 9 | UTAR | SOE=1 regional (KhMAO 38.8) | TASS 2021 (FY2020 annual report) | **PASS** (flag narrowed) |
| 10 | MGNT | SOE=0 (VTB fully exited; Marathon 29.75%) | Interfax 2022-01-14 | **PASS** |
| 11 | PIKK | SOE=0 | Interfax 2026-09-04, alfabank, TAdviser | **PASS-with-CAVEAT escalated** (98% holder's beneficiaries undisclosed; delisting) |
| 12 | TRMK | SOE=0 (management 90.64%) | abnews 2023, Kommersant/eanews 2022 | **PASS** |
| 13 | CBOM | SOE=0 (Region/Sudarikov) | Expert RA 2026-06, Interfax 2026-04, CRKI 2026-07 | **PASS-with-CAVEAT escalated** (2025 control change undisclosed; tier A→B) |
| 14 | SIBN | SOE=1 (Gazprom 95.7%) | Kommersant top-100, GPN review 2024 | **PASS** |
| 15 | RUAL | SOE=0 (En+ 56.88%) | GPN review 2024, Kommersant 2020 | **PASS** |
| 16 | KZOS | SOE=0 (SIBUR control; SINKH 19.87% <25%) | Interfax 2021 ×2 | **PASS** |
| 17 | ALRS | SOE=1 (RF 33.03 + Yakutia 25) | **ALROSA AR2024 (primary)** + FY2025 | **PASS** |
| 18 | GMKN | SOE=0 (Interros 37 / En+ 26.4) | **Nornickel AR2024 (primary)** | **PASS** (bonus) |
| 19 | FESH | SOE=1 (Rosatom 92.5%) | Vedomosti 2025-11, FAS approval 2026-06 | **PASS** |
| 20 | VSMO | SOE=1 (Rostec 25%+1 knife-edge) | smart-lab 2023-12, Kommersant 2023-01 | **PASS** |
| 21 | KMAZ | SOE=1 (Rostec 47.1%) | Interfax 2024-04 (FY2023 reports) | **PASS** |
| 22 | SNGS | SOE=0 (tier B opaque) | dossier.center, ruwiki 2025-12 | **PASS** (standing opacity flag) |
| 23 | MSRS | SOE=1 (Rosseti 50.90) | official IR page 2026 (batch 17) | **PASS** |
| 24 | MRKP | SOE=1 (Rosseti 50.41) | official AR2023 (batch 17) | **PASS** |

Fresh independent re-checks: 22 tickers (requirement ≥20) + JNOS/VJGZ chain inheritance +
MSRS/MRKP official-source re-confirmations = 27 of 75 tickers touched. **FAILs: 0.**
Citation-support rule: 1 citation found imprecise (TGKA temp-admin wording) → corrected; no
citation failed to support its determination.

### D.2 Group 3 (channels/registry) — 7/7 PASS

| # | Check | Verdict |
|---|-------|---------|
| 1 | Recompute ASVI correlations (≥15 required; 20 done, stratified) + rule-consistency sweep of all 74 decisions | PASS — 20/20 match to 4 decimals; 0 violations |
| 2 | Yandex continuity + no-YDEX confirmation (structural + empirical leakage test) | PASS — sum identical at all 419 weeks; 99 weeks would inflate >1.5× if YDEX added |
| 3a | Re-derive R1 (2024-06-16 artifact week) from raw data | PASS — 3 present / 72 absent, exact match |
| 3b | Re-derive R4 (ROLO) + spot-check R5 (USBN) | PASS — every figure matches; reclassification sound |
| 3c | Re-derive a small gap (R9, MSTT) | PASS — 3 missing weeks exactly |
| 3d | Re-derive NEW R2 (2024-08-25 volume artifact) | PASS — 75/75 bars, 73/75 empty volume |
| 4 | False-negative scan of 12 unflagged companies | PASS — no undocumented issues |

---

*Unified report compiled 2026-09-18 from: `GROUP2_SOE_REPORT.md`, `GROUP2_VERIFICATION_REPORT.md`,
`GROUP3_CONSTRUCTION_REPORT.md`, `GROUP3_VERIFICATION_REPORT.md` and the four machine-readable
CSVs. Where this paper and a source report differ, the CSVs (post-repair) are authoritative.*
