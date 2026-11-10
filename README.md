# DS-A4 | Model Comparison and Calibration Lab

## Predicting adult Red Knot sex from morphometric data

**Author:** Kseniia Mamushkina  
**Student ID:** 2026A8010351001  
**Course:** Data Science  
**Assignment:** DS-A4

This project compares logistic regression, random forest and gradient boosting for predicting adult Red Knot sex from morphometric measurements. It evaluates discrimination, probability calibration, uncertainty, subgroup errors and decision thresholds under hypothetical cost and resource constraints.

## Research question

How do the three model families compare in discriminating between males and females and producing reliable probabilities, and how should a decision threshold be selected under explicit error costs and verification-capacity constraints?

## Data source and preparation

The dataset contains adult Red Knots (*Calidris canutus canutus*) captured at Banc d’Arguin, Mauritania.

**Source:** Oortwijn, T., Lameris, T., Zhemchuzhnikov, M., et al. (2025). *Demand-resource mismatch explains body shrinkage in a migratory shorebird* [Dataset]. Dryad.  
https://doi.org/10.5061/dryad.3n5tb2rqb

**Input file:** `14._adults_Mauritania.csv`  
**Data dedication:** CC0. See [Dryad’s reuse policy](https://datadryad.org/help/guides/reuse).

The original file contains 2,165 records with no missing values or fully duplicated rows. Both records for individual Z062234 were excluded because of conflicting reference-sex labels. Subsequently, 33 later capture records were excluded, retaining the earliest capture per individual.

The analytical sample contains **2,130 unique birds: 1,263 females and 867 males**.

| Variable | Role |
|---|---|
| `Sex` | DNA-based reference sex; female = 1, male = 0 |
| `Wing` | Wing length, mm |
| `Bill` | Bill length, mm |
| `Tarsus` | Diagonal tarsus length, mm |
| `Ring` | Individual identifier |
| `Year`, `Month` | Sampling metadata |

All models use Wing, Bill and Tarsus. Identifiers and sampling metadata are not predictors.

**Source-file SHA-256:**

```text
6da0a6b44266f448393c92a293f24f145d803ee95b6246804a0185bbfd9869f6
```

## Evaluation protocol

- Five stratified outer folds provide evaluation predictions.
- Three stratified inner folds select hyperparameters within each outer training subset.
- All model families use identical folds and six predefined configurations.
- The random seed is 42.
- Configuration selection minimizes mean inner-validation Brier score.
- Standardization for logistic regression is fitted inside its pipeline.
- Threshold selection uses only outer-training data.
- Each bird receives one outer-fold probability from each model family.

The dataset was previously examined in DS-A3. This procedure separates training and evaluation within DS-A4 but does not provide validation on a new independent dataset.

## Main results

The following estimates use pooled outer-fold predictions. Classification accuracy uses the reference threshold of 0.50.

| Model | Brier ↓ | ECE ↓ | ROC-AUC ↑ | Average precision ↑ | Accuracy |
|---|---:|---:|---:|---:|---:|
| Logistic regression | 0.1586 | 0.0229 | 0.8412 | 0.8852 | 76.67% |
| Random forest | 0.1620 | 0.0230 | 0.8349 | 0.8784 | 75.73% |
| Gradient boosting | 0.1604 | 0.0280 | 0.8370 | 0.8769 | 76.81% |

Logistic regression has the lowest Brier score and the highest ROC-AUC and average precision. Gradient boosting correctly classifies only three more birds than logistic regression at threshold 0.50.

Calibration curves broadly follow the diagonal, although local deviations and high-confidence individual errors remain.

Errors are more frequent among males and birds in the central bill-length range. For logistic regression, error rates are 16.55% among females and 33.22% among males.

### Uncertainty

Approximate 95% percentile bootstrap intervals use 2,000 sex-stratified resamples. The same sampled birds are used for all models in each iteration, allowing paired comparisons.

Models are not refitted during bootstrap resampling. These intervals are conditional descriptive summaries and do not fully capture training variability or dependence from overlapping cross-validation training sets.

## Thresholds and decision costs

The hypothetical operational task is to select likely females for subsequent DNA confirmation.

The reference scenario assigns:

- false-positive cost = 1;
- false-negative cost = 2;
- verification capacity = 40% of a batch.

Total loss is `FP + 2 × FN`. Eligible birds meet the selected probability threshold. If eligibility exceeds capacity, the highest probabilities are retained, with ascending Ring identifier breaking ties.

| Model | Selected birds | Actual females selected | Precision among selected | Female recall | Loss per 100 birds |
|---|---:|---:|---:|---:|---:|
| Logistic regression | 823 | 729 | 88.58% | 57.72% | 54.5540 |
| Random forest | 825 | 732 | 88.73% | 57.96% | 54.2254 |
| Gradient boosting | 820 | 723 | 88.17% | 57.24% | 55.2582 |

These results evaluate rules selected separately within each outer training fold.

Random forest has slightly lower observed loss in the reference scenario despite logistic regression having better overall probability scores. Sensitivity analysis covers 12 combinations of false-negative cost and capacity, showing that the lowest-loss model depends on the scenario.

### Finalized rule

Logistic regression is retained as the primary probability model according to the predefined Brier criterion.

Final development using all available birds selects:

- **C = 0.1**;
- **P(female) ≥ 0.71** for eligibility;
- **maximum verification capacity = 40%**, rounding down.

The ranking and tie-breaking rules still apply. Non-selection does not establish that a bird is male.

The finalized model and cutoff have not been evaluated on a new external dataset. The cross-validation results above do not constitute an independent test of this exact final fit.

## Main files

| File or output | Description |
|---|---|
| `DS_A4_red_knot_calibration.ipynb` | Executable analysis with text, code and saved outputs |
| `DS_A4_Report.pdf` | Report summarizing methods, results and limitations |
| `14._adults_Mauritania.csv` | Unmodified source data |
| `requirements.txt` | Dependency versions |
| `AI_USE_LOG.md` | AI assistance and verification record |
| Exported CSV tables | Predictions, metrics, intervals, errors and decision results |
| Exported figures | ROC/PR, calibration and cost/workload curves |

## Reproduction in Google Colab

1. Open `DS_A4_red_knot_calibration.ipynb` in [Google Colab](https://colab.research.google.com/github/kseniia-mamushkina/DS-A4-red-knot-calibration/blob/main/DS_A4_red_knot_calibration.ipynb).
2. Upload the original CSV through the Files panel. The expected input path is:
   `/content/14._adults_Mauritania.csv`.
3. Check the environment against `requirements.txt`. If necessary, upload that file and install its dependencies before running the analysis:

   ```python
   %pip install -r /content/requirements.txt
   ```

4. Restart the runtime if required after installation, then run the notebook from top to bottom.
5. Outputs are written under `/content/ds_a4_outputs/`, with timestamped execution folders inside `cv_runs`.

The notebook checks the input-file hash, individual identifiers, fold separation and prediction completeness. Fresh runs create new timestamped result folders.

AI-assisted code reproduction and its scope are documented in [AI_USE_LOG.md](AI_USE_LOG.md).
