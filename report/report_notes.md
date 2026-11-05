# Notes for the author (NOT for submission)

## 0. Things that need your attention first

1. **Actual hours (Section 10 / Figure 10.1).** I cannot know how many hours *you* spent, and I will not invent them. Fill the `actual_hours` column in `docs/time_log.csv`, re-run `python src/make_figures.py` (the Gantt then shows actual bars), and replace the "(author to enter)" cells in the Section 10 table. The task brief asks for "planned vs actual".
2. **References marked †** in Section 12 are well-known works cited from memory (details not machine-verified). Check them, then delete the † marks and the "verify before submitting" heading. See list in Section 3 below.
3. **Word count.** Body prose is about 6,100 words (excluding tables, code, references, appendices); about 6,700 including references and appendices. It is slightly above the 4,000–6,000 target; trim Sections 4.3 or 9 if your college is strict.
4. **Tests that did not behave as hoped** (all reported honestly in the report): H4 is the weakest result (p = 0.074 when countries under 1 million people are excluded; the planned label-permutation p = 0.052). H5's observed effect is just below the minimum detectable effect (power analysis).
5. **Build the Word file from `report/report_content.md`.** Figures are referenced as `../figures/*.png`; insert them by hand. Tables are Markdown pipe tables (Word: paste as text, then Insert > Table > Convert Text to Table, or use pandoc: `pandoc report_content.md -o report.docx --resource-path=.`).

## 1. Figure-to-section map

| Figure | File | Report section |
|--------|------|----------------|
| 1.1 | fig_framework_flowchart.png | 1.1 Introduction |
| 4.1 | fig_test_selection_tree.png | 4.1 Test-selection logic |
| 4.2 | fig_assumption_checks.png | 4.2 Assumption checks |
| 4.3 | fig_power_curve.png | 4.6 Power analysis |
| 6.1 | fig_h1_scatter.png | 6.2 H1 |
| 6.2 | fig_h2_boxplot_by_income.png | 6.3 H2/H3 |
| 6.3 | fig_h4_slopes_by_income.png | 6.4 H4 |
| 6.4 | fig_h5_paired_change.png | 6.5 H5 |
| 6.5 | fig_h6_decoupling_heatmap.png | 6.6 H6 |
| 6.6 | fig_effect_size_forest.png | 6.8 |
| 10.1 | fig_timeline_gantt.png | 10 Time allocation |

## 2. URLs checked, failures, substitutions

| URL | Result (2026-11-05) |
|-----|---------------------|
| https://raw.githubusercontent.com/owid/co2-data/master/owid-co2-data.csv | HTTP 200, 14,377,942 bytes. Data run to 2024; restricted to 1990–2022. |
| https://raw.githubusercontent.com/owid/co2-data/master/owid-co2-codebook.csv | Fetched; used to confirm the `co2_per_capita` definition (fossil fuels + industry, excluding land-use change; Global Carbon Budget 2025 source). |
| https://api.worldbank.org/v2/country (metadata) | OK, 296 rows (79 aggregates, 217 countries). |
| https://api.worldbank.org/v2/country/all/indicator/{NY.GDP.PCAP.KD, NY.GDP.PCAP.PP.KD, SP.POP.TOTL, SP.URB.TOTL.IN.ZS, NV.IND.TOTL.ZS}?date=1990:2022 | All OK; 8,778 rows each, none needed a retry. |
| Crossref API (api.crossref.org) | Used only to verify references (below). |

**Failures: none. Substitutions: none.** (The retry/back-off code in `acquire.py` was never triggered.)

## 3. Items to double-check

**References**

* *Verified through Crossref by me* (title, journal, year, volume/pages, DOI matched): Benjamini & Hochberg 1995; Breusch & Pagan 1979; Dunn 1964; Robinson 1950; Seabold & Perktold 2010; Shapiro & Wilk 1965; Vallat 2018; Welch 1947; White 1980; Wilcoxon 1945.
* *Not machine-verified (marked † in the report) — please check authors, volume and pages:* Efron 1979 (Ann. Stat. 7(1), 1–26); Holm 1979 (Scand. J. Stat. 6(2), 65–70; the Crossref search returned only a later correction, not the original); Kruskal & Wallis 1952 (JASA 47(260), 583–621; Crossref returned the 1953 errata); Freedman & Lane 1983 (JBES 1(4), 292–298); Cohen 1988 (2nd ed.); Tomczak & Tomczak 2014 (Trends in Sport Sciences 21(1), 19–25); Virtanen et al. 2020 (Nature Methods 17, 261–272). Each † reference is cited in the text (effect-size benchmarks in 4.4, Freedman–Lane in 6.4, SciPy in Section 5).

**Statements in the report that are NOT directly printed by a test, i.e. read off a figure or simple arithmetic — re-check if challenged**

* "OLS slope 0.83" (Section 6.2): printed in the legend of Figure 6.1, computed in `make_figures.py` (scipy `linregress`), not saved to a CSV. "10^0.83 ≈ 6.8" is arithmetic.
* "LOWESS flattens above roughly 20,000 US$": read visually from Figure 6.1.
* Which countries lie above/below the H1 line (Qatar, Kuwait, Brunei, Mongolia, Venezuela above; Macao, Switzerland below): these are the labelled points of Figure 6.1 (chosen by code as the largest residuals/extremes).
* Which countries had the largest falls/rises in H5 (Luxembourg, Estonia, Nauru, Curaçao; Guyana, Oman, Panama, Seychelles, South Korea): read from the sorted dumbbell chart (Figure 6.4), derived from `paired_1990_2022.csv`.
* "about 2.2% more CO₂ per person per additional percentage point of industry share": 10^0.0095 = 1.022, arithmetic from the H7 coefficient.
* "about 26% chance of at least one false rejection": 1 − 0.95⁶ = 0.265, arithmetic.
* "36% of high-income countries did not decouple": 24/67 = 35.8%.
* Section 3.3 "7,656 duplicate OWID keys" and "165 blank-ISO3 World Bank rows" (troubleshooting items 3–4): found in an exploratory inspection (`duplicated(["iso_code","year"])` on the full raw file; five blank-ISO3 aggregate codes × 33 years). They are not stored in a results file.
* The interpretation that production-based accounting "flatters wealthy importers" (Section 9.5) is standard background knowledge, not tested here.
* Russia is "currently classified as high income" and Venezuela as "lower-middle income": read from the 2026 World Bank metadata used here (`cross_section_2022.csv`); the classification changes every July.
* Section 3.4 characterisations such as "mostly small territories": based on the exclusion lists in `data/processed/exclusions_by_test.csv`.
* The sentence "H3 d = 3.06 is larger than most would expect" and the verbal effect-size labels rely on the benchmarks in Section 4.4 (conventional rules of thumb, not universal standards).

**Plan wording I did not change after committing** (to keep the plan intact): in the H2 line the plan says "PPP-independent (CO2 only), so exclusions…" which reads awkwardly; the report states what was done (exclusions and permutation only, no PPP variant for H2).

## 4. Troubleshooting log (real problems, in order encountered)

| # | Problem | Cause | Fix |
|---|---------|-------|-----|
| 1 | OWID shows 7,656 "duplicate" (iso_code, year) keys. | All aggregates share a missing ISO code. | Drop rows with no ISO3 before checking keys; zero duplicates remain; `ValueError` guard in `clean.py` and a pytest check. |
| 2 | Kosovo has no ISO code in OWID (and its series starts in 1994). | OWID uses a name only. | One-row manual crosswalk to `XKX`; 33 rows re-keyed and logged; pytest asserts Kosovo has CO₂ and GDP after the join. Kosovo is later excluded from H6 only because it has no 1990 CO₂. |
| 3 | World Bank indicator files contain 165 rows with blank ISO3 (5 income-level aggregates × 33 years); pandas turns "" into NaN. | The API returns aggregate groups without ISO3 codes. | `keep_default_na=False`; aggregates identified via the metadata endpoint's region = "Aggregates"; the blank-ISO3 rows removed and logged. |
| 4 | 12 OWID territories (e.g. Taiwan, Anguilla) have no World Bank record; 10–12 World Bank economies (e.g. Puerto Rico, Guam) have no OWID CO₂. | Different country lists. | Outer join with indicator; counts reported (both 6,831 / OWID-only 396 / WB-only 330); no join on names. |
| 5 | 10 country-year rows have CO₂ per capita = 0 (Timor-Leste 1994–97, Kosovo 1994–99). | Early-record zeros. | Excluded from log transforms only; values kept; none in 1990 or 2022. |
| 6 | Shell redirect to `/tmp` failed with "Permission denied" when saving the first run log. | Environment restriction. | Logs written to the session scratchpad. (Cosmetic; not in the report.) |
| 7 | pandas 4 deprecation warnings (`sum(1)`). | Positional argument. | `sum(axis=1)`. |
| 8 | One-sided sign-test CI upper bound = 1.0. | `proportion_ci` inherits the test's one-sided alternative. | Compute the CI from a two-sided `binomtest`. |
| 9 | Planned H4 label permutation tests a stronger null than the interaction null (p = 0.052). | Shuffling group labels also destroys the group main effect. | Added Freedman–Lane residual permutation (p = 0.024); both reported; deviation listed in Section 6.11. |
| 10 | Assumption violations: raw data non-normal; Low-income log₁₀ CO₂ non-normal (p = 0.007); H3 variances unequal (p = 0.0002); H4 residuals non-normal (p = 0.001); H5 log ratios borderline (p = 0.046); H4 VIF 8.3. | Real data features. | Rank-based primaries, Welch, HC3, permutation tests; paired t as companion only. |
| 11 | Module name `src/tests.py` vs `tests/` package. | Name clash. | pytest loads `src/tests.py` with `importlib` by path. |
| 12 | Full test run takes 4 min 17 s. | Python-loop bootstraps (10,000) and Monte Carlo (100,000). | Accepted; seeds fixed so output is deterministic. |
| 13 | First decision-tree figure had overlapping boxes; assumption-check figure had uneven spacing and boxes covering bars; H1 labels (KWT/BRN/SOM) overlapped; H5 legend covered a row; power-curve annotations hit the legend. | Layout. | Re-laid out, added headroom, manual label offsets; every figure re-viewed after generation. |
| 14 | Report first drafted at ~7,200 words. | Over target. | Trimmed repeated numbers and secondary commentary. |
| 15 | A few claims in the first draft were not supported by the data (e.g. which countries lie above/below the H1 line; a vague price-base example). | Drafting errors. | Re-checked against the outputs/figures and rewritten. |

## 5. Likely viva questions (short answers)

1. **Why Welch's t-test and not Student's for H3?** The two groups have unequal variances (Levene/Brown–Forsythe p = 0.0002; SD ratio 0.63) and nearly equal sizes (74 vs 72) — Welch does not assume equal variance and loses almost nothing when variances happen to be equal, so it is the safe default. Student's t was run as a comparison (t = 18.46 vs 18.36): same conclusion.
2. **Why Spearman rather than Pearson for H1?** Raw GDP and CO₂ per capita are very skewed (skewness 2.2 and 2.6, Shapiro p < 0.0001); Spearman uses ranks, assumes only monotonicity, and is not driven by outliers such as Qatar. Pearson on log₁₀ values is reported as a companion and is nearly the same (0.852 vs 0.855).
3. **What does Holm correction do?** With six confirmatory tests, using 0.05 for each would give about a 26% chance of at least one false positive. Holm sorts the p-values, multiplies the smallest by 6, the next by 5, and so on (keeping the sequence non-decreasing), and rejects while the adjusted value is below 0.05. It controls the family-wise error rate at 5% and is never less powerful than Bonferroni. H4 and H5 both end at adjusted p = 0.0155.
4. **What is a p-value, and what is it not?** The probability of data at least this extreme if H0 and all other assumptions were true. It is *not* the probability that H0 is true, not the probability the result occurred "by chance", and not a measure of effect size — which is why every p-value in the report sits beside an effect size and a confidence interval.
5. **Why chi-square for H6 and when would Fisher/Monte Carlo be used?** Both variables are categorical (income group × decoupled yes/no). The chi-square approximation is reliable if expected counts are mostly ≥ 5; the minimum here is 6.41, so it is valid. If any expected count were below 5 the primary p-value would switch to the Monte Carlo (Fisher–Freeman–Halton-type) permutation p-value, which does not rely on the approximation. We computed it anyway: p = 1 × 10⁻⁵ (the smallest resolvable), same conclusion; a 2×2 Fisher test (High vs not High) agrees (OR = 11.3).
6. **Why is the income-group comparison partly circular?** The World Bank defines income groups from income per person; so if groups are used on the predictor side (H4, H6) they overlap with GDP by construction — see the VIF of 8.3. H2 and H3 compare groups on *emissions*, which is not circular, but the huge effect (d = 3.06) partly reflects that groups are formed by sorting on a variable correlated with emissions.
7. **Why not use all country-years as observations?** They are strongly dependent over time within a country; treating 6,447 country-years as independent would drastically understate standard errors. We use one row per country (cross-section or one paired change) and run the panel only with country-clustered SEs as a sensitivity check.
8. **Which result is weakest and why?** H4: the interaction explains only ΔR² = 0.010 of variance, the p-value is 0.0129 raw (0.0155 Holm), it becomes 0.074 when countries under one million people are excluded and the planned label-permutation gives 0.052. The honest wording is "evidence, but sensitive to country inclusion".
9. **Why use one-sided tests for H1, H3, H5?** The direction was stated in advance from theory/Week 1 (richer → more emissions; high income > poorer; high-income emissions fell). One-sided tests are justified only because the direction was fixed before looking at data; a result in the opposite direction would have been reported as non-rejection, not as a finding.
