# Modeling Strategy

## 1. Prediction Objective

Estimate the probability that a C-MAPSS FD001 engine has **30 or fewer operational cycles remaining** at a given observed cycle. The 30-cycle horizon is a provisional Day 16 experiment assumption chosen to make the baseline classification exercise concrete; it is not a client-approved maintenance window or production policy.

## 2. Unit of Prediction

One prediction corresponds to one engine-cycle observation. The model receives the current cycle, operating settings, and contemporaneous sensor readings and returns a risk score. The eventual business action would be to prioritize the engine for inspection or continue normal operation, subject to maintenance capacity and an approved horizon.

## 3. Target Definition

For training engine `u` at cycle `t`:

```text
RUL(u, t) = T_failure(u) - t
failure_within_30_cycles(u, t) = 1 when RUL(u, t) <= 30, otherwise 0
```

RUL and `T_failure` are retrospective outcomes used only to construct and evaluate the target. They are never predictors. The label includes cycles 0 through 30, so each complete training trajectory contributes 31 positive observations. This repeated-row label is useful for a baseline experiment but does not create independent failure events.

## 4. Deployment Scenario

At each decision cycle, the system receives only information observable for an engine through that moment and produces a near-term failure-risk score for inspection prioritization. Validation approximates scoring observations from previously unseen engines operating under FD001's single regime and fault mode. It does not establish performance for known engines after online adaptation, other C-MAPSS regimes, or real manufacturing assets.

## 5. Leakage Controls

- Split engines before fitting preprocessing or models; no engine appears in more than one development partition.
- Exclude `unit_number` from predictors and use it only for grouping and audit checks.
- Exclude RUL, per-engine maximum cycle, official test RUL, terminal indicators, and all target-derived fields from predictors.
- Exclude constant and near-constant fields previously marked `REMOVE`.
- Use only the current cycle, current operating settings, and current sensor readings; no future observations, centered windows, or full-trajectory aggregates are features.
- Fit median imputation and scaling inside scikit-learn pipelines using training engines only.
- Keep the official NASA FD001 test trajectories and their RUL file untouched for later final assessment.

## 6. Validation Strategy

The 100 complete FD001 training engines are split by `unit_number` with random seed 42:

| Partition | Engines | Purpose |
|---|---:|---|
| Train | 60 | Fit pipelines and model parameters |
| Validation | 20 | Compare the fixed baseline specifications |
| Test | 20 | Open once for the final Day 16 baseline comparison |

This group-based holdout asks whether a model transfers to different engines rather than memorizing adjacent rows from the same trajectory. The split is not a temporal backtest and only one partition realization is used, so uncertainty across alternative engine cohorts remains unmeasured.

## 7. Baseline Model Ladder

1. **Most-frequent Dummy Classifier** — establishes the no-signal reference.
2. **Age-only Logistic Regression** — tests how much signal is supplied by cycle age alone, as required by the Day 15 findings.
3. **Logistic Regression** — interpretable linear condition baseline using all 17 approved features, median imputation, standardization, and balanced class weights.
4. **Random Forest** — the sole nonlinear baseline, using 200 trees, minimum leaf size 5, and balanced subsample weights. These parameters were fixed before validation; no search was run.

Every classifier uses the common default probability threshold of 0.50. No feature selection or threshold optimization is performed.

## 8. Evaluation Metrics

Precision, recall, F1, ROC-AUC, and PR-AUC are reported alongside positive prevalence. Accuracy is not the primary metric because predicting the majority class would appear approximately 85% accurate while missing every positive observation. PR-AUC is especially useful because its no-skill reference is approximately the positive prevalence. Recall measures missed-failure exposure, while precision measures unnecessary-alert burden.

## 9. Business-Oriented Evaluation

The Day 2 illustrative assumptions are retained:

```text
C_FN = Rp 58 million per missed failure-risk observation
C_FP = Rp 6 million per unnecessary intervention alert
Illustrative Cost = FN * C_FN + FP * C_FP
```

Costs are compared at the same 0.50 threshold. The calculation is an error-weighted scenario, not realized portfolio value: repeated positive cycles are not independent failures, interventions are not simulated, and true-positive intervention costs and capacity constraints are omitted to match the prescribed simple comparison.

## 10. Probability and Calibration Considerations

Brier score and five-bin reliability summaries provide a basic probability check. Balanced class weights intentionally alter the fitted class distribution and can weaken probability calibration even when ranking is strong. The resulting probabilities are therefore suitable for comparative diagnosis only; they must not yet be inserted into the Day 2 expected-value rule. Calibration should later be assessed on a separate calibration partition or through group-aware cross-validation.

## 11. Error Analysis Plan

Inspect at most ten false negatives and ten false positives for each condition model. Report their engine IDs, current cycles, retrospective RUL values, and predicted probabilities, then summarize how many engines contribute errors and whether errors cluster just inside or outside the provisional 30-cycle boundary. This bounded review is diagnostic and must not be used to tune the threshold against the test set.

## 12. Model Complexity Decision Rule

The Random Forest is justified only if it delivers a material and consistent improvement over Logistic Regression in PR-AUC, recall/precision balance, illustrative cost, or probability quality that warrants reduced interpretability and greater operating complexity. One favorable metric is insufficient when other decision-critical measures deteriorate.

## 13. Risks and Limitations

- The 30-cycle horizon is provisional and has no validated mapping to a maintenance window.
- FD001 is simulated and covers one operating regime and one fault mode.
- The split contains only 100 engines; one holdout cannot quantify cohort uncertainty.
- Rows within each engine are temporally dependent, and the row-level cost calculation can count one engine repeatedly.
- Training trajectories run to failure and may not reflect censoring or intervention behavior in deployment.
- Class weighting can distort probabilities; no post-hoc calibration was fitted.
- Results at the default threshold do not establish the economically optimal decision rule.
- No claim is made about causal mechanisms, field generalization, or realized business value.

## 14. Day 16 Conclusion

The dataset contains substantial signal beyond cycle age under an engine-disjoint holdout: both condition models materially outperform the dummy and age-only references. Logistic Regression provides the stronger Day 16 decision baseline because it achieves higher recall, PR-AUC, and lower illustrative error cost than Random Forest at the common threshold. Random Forest improves precision, F1, and Brier score, but its additional complexity is **NOT YET** justified. The next experiment should validate horizon choice and probability calibration without touching the official NASA test collection.
