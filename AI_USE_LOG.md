# AI Use and Verification Log

**Assignment:** DS-A4 — Model Comparison and Calibration Lab  
**Author:** Kseniia Mamushkina  
**Student ID:** 2026A8010351001  
**AI tool:** ChatGPT  

## Scope of AI assistance

ChatGPT assisted with drafting Python code and notebook text, explaining statistical concepts, troubleshooting, and preparing the PDF report.

The author executed the analysis in Google Colab, inspected outputs and discussed their interpretation. Numerical results were obtained through code execution using the original dataset.

## Accepted suggestions

- Reuse the DS-A3 dataset and documented cleaning rules.
- Compare logistic regression, random forest and gradient boosting using the same three predictors.
- Use shared five-fold outer and three-fold inner stratified cross-validation.
- Allocate six hyperparameter configurations to each model family.
- Examine hypothetical error costs and verification-capacity constraints.

The reference costs—FP = 1 and FN = 2—and 40% verification capacity are illustrative assumptions suggested during planning, not measured project costs.

## Verification performed

### Author execution

The author ran the notebook in Colab and inspected the resulting tables, figures and warnings.

Programmatic checks included:

- source-file SHA-256 verification;
- schema, missing-value and reference-sex checks;
- exclusion tracking and unique individual identifiers;
- separation of training and validation individuals;
- complete, valid outer-fold probabilities;
- consistent calibration-bin and ECE calculations;
- reproduction of the reference scenario within sensitivity analysis.

### Separate AI-assisted reproduction

A separate execution environment reran all 19 code cells in the reviewed notebook copy, using scikit-learn 1.6.1 and adapting input/output paths.

All cells completed successfully. Thirteen saved numerical tables matched the reproduced results at their displayed precision. The finalized logistic-regression setting, `C = 0.1`, and threshold, `0.71`, were also reproduced.

The recorded SciPy warnings concerning deprecated `disp` and `iprint` output-control options were inspected. They were not convergence warnings.

## Verification limits

Bootstrap intervals are conditional on the existing predictions and do not include model refitting. Subgroup diagnostics are exploratory. Previously examined DS-A3 data were reused, and the finalized model and threshold have not been externally validated.

AI assistance does not replace the author’s responsibility for the submitted analysis and its interpretation.
