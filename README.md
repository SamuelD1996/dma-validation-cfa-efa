
# Data Maturity Assessment: CFA, EFA, and Sensitivity Analysis

This repository contains the R Markdown analysis used to investigate the factor structure of a Data Maturity Assessment questionnaire. It combines **confirmatory factor analysis (CFA)** and **exploratory factor analysis (EFA)** with sensitivity analyses to assess whether repeated assessments from the same organisation influence the findings.

The **primary analysis** includes all assessments. The **sensitivity analysis** retains only the first assessment for each organisation, identified by `Respondent id` and ordered by `Jaar` (with ties resolved by the order in the input file).

## Analysis script

The updated analysis is provided in:

[`scripts/data_maturity_assessment_validation_full_including_sensitivity_analysis_v4.Rmd`](scripts/data_maturity_assessment_validation_full_including_sensitivity_analysis_v4.Rmd)

The script produces an HTML report when knitted and exports CSV tables for the main results and comparisons.

## Methods

### Data preparation

The script reads an Excel workbook containing the assessments, selects the questionnaire items (columns 32 onward), and assigns descriptive variable names to the 77 items. Dutch response categories are recoded as:

| Value | Response |
| --- | --- |
| `0` | Applicable (`Van toepassing`) |
| `1` | Not applicable, but targeted for the coming year |
| `2` | Not applicable (`Niet van toepassing`) |

The script checks that recoding does not introduce additional missing values.

### Confirmatory factor analysis (CFA)

A prespecified seven-factor model is fitted to both the primary and sensitivity samples using `lavaan`, with the **WLSMV** estimator and ordered questionnaire items.

Model fit is assessed using:

- Comparative Fit Index (CFI)
- Root Mean Square Error of Approximation (RMSEA)
- Standardized Root Mean Square Residual (SRMR)

The script specifies the following thresholds:

| Fit index | Threshold |
| --- | --- |
| CFI | ≥ 0.95 |
| RMSEA | ≤ 0.06 |
| SRMR | ≤ 0.08 |

Scaled CFI and RMSEA should be reported for WLSMV estimation.

### Exploratory factor analysis (EFA)

The same EFA procedure is applied to the primary and sensitivity samples:

1. Compute Spearman correlations and remove items with an absolute correlation below 0.20 with every other item.
2. Assess factorability using the Kaiser–Meyer–Olkin (KMO) measure and Bartlett's test.
3. Examine factor retention using parallel analysis and eigenvalue criteria.
4. Fit a seven-factor solution using minimum residual extraction (`minres`) and oblimin rotation.
5. Iteratively remove the item with the weakest maximum absolute factor loading until all retained items load at least 0.32 on one factor.
6. Summarise factor loadings, cross-loadings, explained variance, and Cronbach's alpha per factor.

The script also prepares loading tables corresponding to Multimedia Appendices 2 and 4.

### Sensitivity and robustness analyses

To assess the effect of organisations contributing more than one assessment, the script compares:

- **Primary sample:** all assessments.
- **Sensitivity sample:** only the first assessment per organisation.

Comparisons include:

- CFA fit statistics.
- EFA summary statistics.
- Factor congruence using Tucker's congruence coefficient.
- Factor congruence with the primary analysis's retained items held constant.
- Factor congruence after sample-specific item removal.

A further analysis compares **six-factor and seven-factor EFA solutions**, including factor congruence and item assignments, to explore the alternative factor structure indicated by parallel analysis.

## Requirements

The analysis uses R and the following packages:

- `readxl`
- `dplyr`
- `psych`
- `lavaan`
- `GPArotation`
- `ggplot2`
- `rmarkdown`
- `knitr`

For reproducibility, the script is configured by default to use:

- **psych 2.5.3**
- **lavaan 0.6-19**

These versions are identified in the analysis as matching the manuscript.

The script expects these packages to be installed in a separate library folder:

`../r_lib_manuscript_versions`

The `install_manuscript_versions` chunk contains installation commands but has `eval=FALSE`. Run that chunk manually once before knitting.

Installing archived source packages may require appropriate build tools and dependencies.

Alternatively, set:

```r
use_manuscript_versions <- FALSE
```

in the `packages` chunk to use locally installed versions. Results may differ between package versions.

## Running the analysis

1. Clone or download this repository and open the `.Rmd` file in RStudio.
2. Obtain the required input Excel workbook. **The raw dataset is not provided here**; access is required to reproduce the results.
3. Ensure the path in the `settings` chunk points to the input workbook. The current setting is:

   ```r
   file <- "./data/Ruwe resultaten 2022 tot met dec 2024 sdw gecombineerd.xlsx"
   ```

4. Install the required R packages and, if using the manuscript versions, run the `install_manuscript_versions` chunk once.
5. Knit the R Markdown file to HTML, or run its chunks in sequence.

**Path note:** The script uses relative paths for both its input (`./data/...`) and output (`./results/...`). Set the R working directory to the repository root, or adjust these paths to your local setup, before running the analysis.

The separate manuscript package library path (`../r_lib_manuscript_versions`) is also relative to the working directory.

The input file must contain the columns `Respondent id` and `Jaar`, with the questionnaire items in the expected column order.

## Outputs

The script writes CSV files to:

`results/sensitivity_repeated_assessments/`

| File | Description |
| --- | --- |
| `cfa_fit.csv` | CFA fit measures for the primary and sensitivity samples |
| `efa_key_results_primary_vs_sensitivity.csv` | Side-by-side EFA summary statistics |
| `congruence_same_items.csv` | Factor congruence using the primary solution's retained items |
| `congruence_own_item_removal.csv` | Factor congruence after sample-specific item removal |
| `appendix_2_loadings_before_removal.csv` | Initial EFA loadings |
| `appendix_4_loadings_final.csv` | Final EFA loadings |
| `loadings_primary_final.csv` | Final primary-sample factor loadings |
| `loadings_sensitivity_same_items.csv` | Sensitivity-sample loadings using the primary retained items |
| `loadings_sensitivity_own_item_removal.csv` | Final sensitivity-sample loadings after its own item removal |
| `loadings_six_factors_final.csv` | Final six-factor loadings |
| `item_assignment_7_vs_6_factors.csv` | Item-to-factor assignments in the six- and seven-factor solutions |

The rendered HTML report also includes printed diagnostics, comparison tables, and plots, as well as `sessionInfo()` for the R environment.

## Reproducibility and data access

The script sets a seed (`20261007`) for parallel analysis.

Reproducing the analyses requires:

- The original dataset.
- The expected questionnaire column order.
- Compatible R package versions.

Do not upload confidential or identifiable assessment data to this public repository without appropriate permission.

## Citation

If you use this analysis, please cite the associated manuscript when its bibliographic details are available.
