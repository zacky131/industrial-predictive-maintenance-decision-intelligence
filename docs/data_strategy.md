# Data Strategy

## 1. Decision the Data Must Support

The data must support the operational decision: **Which machines should be prioritized for preventive inspection or maintenance before failure?**

That requires information available before the decision point, a defensible definition of future failure risk, and enough lead time to act before the next feasible maintenance window. A dataset that identifies only an already-failed machine may support diagnosis, but it is weaker evidence for preventive maintenance.

## 2. Dataset Selection Requirements

### Must-have

- A clear failure, degradation, or remaining-life target with documented semantics.
- Measurements available at or before prediction time, not consequences of failure.
- Enough failure trajectories or positive events to support analysis.
- Plausible sensor or operating variables.
- Unit identifiers or another defensible basis for train, validation, and test separation.
- Transparent provenance and reproducible public access.
- Usable access or licensing terms that can be documented.
- A credible link from prediction to a maintenance action.

### Nice-to-have

- Natural temporal order and an explicit prediction horizon.
- Multiple machines, operating regimes, and failure modes.
- Maintenance history, intervention outcomes, asset criticality, and cost fields.
- A published benchmark protocol and held-out targets.
- Direct relevance to discrete manufacturing equipment.

## 3. Candidate Datasets

### 3.1 AI4I 2020 Predictive Maintenance Dataset

| Attribute | Verified profile |
| --- | --- |
| Dataset name | AI4I 2020 Predictive Maintenance Dataset |
| Source | [UCI Machine Learning Repository](https://archive.ics.uci.edu/dataset/601/ai4i+2020+predictive+maintenance+dataset), DOI [10.24432/C5HS5C](https://doi.org/10.24432/C5HS5C) |
| Source type | University data repository with an introductory conference paper |
| Real vs synthetic | Synthetic data designed to reflect industrial predictive-maintenance data |
| Approximate size | 10,000 rows; 14 columns in the published file, comprising two identifiers, six predictors, and six target fields |
| Feature groups | Product type, air and process temperature, rotational speed, torque, and tool wear; UID and Product ID are identifiers |
| Target | `Machine failure` records failure at that same data point; `TWF`, `HDF`, `PWF`, `OSF`, and `RNF` identify component failure modes |
| Temporal structure | UCI labels the dataset time-series, but it does not provide repeated, unit-level machine trajectories suitable for a future-failure horizon |
| Primary use case | Interpretable tabular current-state failure classification |
| License / access terms | CC BY 4.0; direct public download from UCI |
| Main strengths | Small, transparent, reproducible, understandable variables, and a binary outcome |
| Main weaknesses | Current-state rather than future-failure target, synthetic generation rules, limited validation realism, and direct target leakage from failure-mode fields |

### 3.2 NASA C-MAPSS Turbofan Engine Degradation Simulation

| Attribute | Verified profile |
| --- | --- |
| Dataset name | C-MAPSS Jet Engine Simulated Data / Turbofan Engine Degradation Simulation Data Set |
| Source | [NASA/Data.gov catalog](https://catalog.data.gov/dataset/cmapss-jet-engine-simulated-data) and [NASA DASHlink record](https://c3.ndc.nasa.gov/dashlink/resources/139/) |
| Source type | NASA Prognostics Center of Excellence benchmark repository |
| Real vs synthetic | High-fidelity simulated turbofan degradation, not field observations |
| Approximate size | Four subsets with 708 training and 708 test engine trajectories in total; each row has a unit number, cycle, three operating settings, and 21 sensor measurements |
| Feature groups | Engine identity, operational cycle, operating settings, and multivariate sensor channels |
| Target | Remaining Useful Life (RUL): operational cycles remaining before simulated failure |
| Temporal structure | Ordered, repeated observations by engine; training trajectories run to failure and test trajectories stop before failure with endpoint RUL supplied separately |
| Primary use case | RUL estimation and degradation-based maintenance prioritization |
| License / access terms | The official catalog marks access as public and provides a ZIP resource; it does not display an explicit dataset license, so redistribution terms must be confirmed before republishing raw data |
| Main strengths | Natural lead time, multiple units, explicit cycle order, run-to-failure trajectories, official train/test design, and sensor degradation patterns |
| Main weaknesses | Simulated aerospace domain, anonymized sensor meanings, no maintenance actions or costs, and greater modeling complexity |

The initial scope would use **FD001**, which contains 100 training and 100 test trajectories under one operating condition and one fault mode. This preserves the temporal decision problem while avoiding the additional operating-regime and multi-fault complexity of FD002–FD004 during the first implementation.

### 3.3 IMS Bearing Run-to-Failure Dataset

| Attribute | Verified profile |
| --- | --- |
| Dataset name | IMS Bearings / IMS bearing run-to-failure dataset |
| Source | [NASA Open Data Portal](https://data.nasa.gov/dataset/ims-bearings); experimental details are summarized in this [peer-reviewed open-access study](https://pmc.ncbi.nlm.nih.gov/articles/PMC9920053/) |
| Source type | University experimental test rig distributed by NASA's Prognostics Center of Excellence |
| Real vs synthetic | Real vibration measurements from accelerated run-to-failure bearing experiments |
| Approximate size | Three run-to-failure experiments with four bearings on a shared shaft; one-second vibration snapshots of 20,480 samples at 20 kHz, generally captured every 10 minutes |
| Feature groups | Bearing identity, acquisition time, and raw vibration channels; useful condition indicators would need to be derived later |
| Target | Experiment endpoint and observed bearing fault; no ready-made row-level future-failure label |
| Temporal structure | Timestamped vibration sequences through end of experiment |
| Primary use case | Bearing condition monitoring, degradation detection, and derived RUL estimation |
| License / access terms | NASA marks the dataset public, while the portal labels the license `other-license-specified`; redistribution terms require confirmation |
| Main strengths | Real sensor waveforms, physical degradation, and clear temporal order |
| Main weaknesses | Very few independent experiments and failed bearings, shared-rig dependence, large raw signals, ambiguous degradation onset, and no ready-made decision target |

### 3.4 Decision-fit audit

| Senior-level question | AI4I | C-MAPSS | IMS Bearings |
| --- | --- | --- | --- |
| 1. Business decision supported | Inspect when the current process state indicates failure | Rank engines by remaining life or near-term failure risk | Prioritize bearings showing late-life degradation |
| 2. Exact target | Same-row `Machine failure` state | Cycles remaining until failure | Failure endpoint; RUL or horizon label must be derived |
| 3. Target timing | During the recorded failure | Known after the trajectory; predictors are observed before failure | Known after the experiment; predictors exist before failure |
| 4. Features available at prediction time | Physical predictors are observable, but only at the same event | Current and prior settings/sensors are available | Current and prior vibration snapshots are available |
| 5. Temporal order | No defensible per-machine longitudinal sequence | Explicit unit-cycle order | Explicit acquisition-time order |
| 6. Realistic separation | Stratification is possible, but temporal or machine generalization is not | Official held-out engines plus group-based development splits | Experiment- or bearing-level separation is possible but statistically weak |
| 7. Meaningful horizon | None in the published target | Native RUL in operational cycles | Time/cycles to endpoint can be derived, but onset is ambiguous |
| 8. Actionability | Weak for prevention because failure is already present | Strong: lead time can be related to a maintenance window | Potentially strong after a defensible label policy is defined |
| 9. Data origin | Synthetic | High-fidelity simulation | Real test-rig measurements |
| 10. Important missing variables | Maintenance and failure history, costs, criticality, schedules | Maintenance actions, costs, criticality, calendar timing, and named sensor semantics | Maintenance actions, costs, varied loads, production schedules, and a large independent fleet |
| 11. Direct or indirect leakage | Failure-mode targets directly determine the overall target; identifiers need review | Future maximum cycle, RUL labels, unit overlap, or future-looking windows | End-of-run timestamps, post-failure records, or features computed over the full run |
| 12. Fit to economic framework | Binary output fits superficially, but lacks preventive lead time | Failure-within-window probability can connect RUL uncertainty to the economic threshold | Can connect to a horizon, but sparse failures and label ambiguity weaken evidence |

## 4. Dataset Comparison

Scores use `1 = poor` through `5 = excellent`. Weighted score is the sum of each score multiplied by its normalized criterion weight.

| Criterion | Weight | AI4I | C-MAPSS | IMS Bearings |
| --- | ---: | ---: | ---: | ---: |
| Business relevance | 30% | 4 | 4 | 4 |
| Target quality | 25% | 2 | 5 | 3 |
| Sensor / feature realism | 20% | 3 | 4 | 5 |
| Validation realism | 15% | 2 | 5 | 2 |
| Reproducibility / licensing | 10% | 5 | 4 | 3 |
| **Weighted score** | **100%** | **3.10** | **4.40** | **3.55** |

- **Business relevance:** All three support equipment-health decisions, but none includes the client's actual maintenance operations or economics.
- **Target quality:** C-MAPSS has an explicit future-oriented RUL target. AI4I labels the current failure event; IMS requires a derived label and a judgment about the failure endpoint.
- **Sensor / feature realism:** IMS contains real vibration signals. C-MAPSS provides multivariate degradation from a high-fidelity simulator. AI4I variables and failures are generated synthetically from simple rules.
- **Validation realism:** C-MAPSS provides multiple unit trajectories and official held-out engines. AI4I lacks meaningful machine or time separation; IMS has too few independent experiments for a robust holdout.
- **Reproducibility / licensing:** AI4I has the clearest license and smallest stable download. C-MAPSS and IMS are publicly accessible from NASA, but their catalog records do not provide equally clear redistribution licenses.

The ranking reflects a genuine trade-off: AI4I offers business simplicity, while C-MAPSS offers stronger industrial prognostic structure and validation realism. The latter matters more for demonstrating that a prediction is available early enough to change a maintenance decision.

## 5. Selected Dataset

**We select NASA C-MAPSS, beginning with FD001, because it provides the strongest balance of future-oriented target semantics, realistic unit-level validation, and temporal degradation signals for the current portfolio objective.**

AI4I is not primary because predicting a failure recorded at the current observation does not provide a defensible preventive horizon, and its failure-mode columns directly reveal the overall target. IMS Bearings is not primary because its real waveforms add useful physical realism but its few dependent run-to-failure experiments make evaluation and label construction fragile.

**Secondary / extension dataset:** IMS Bearings may later test whether the approach transfers from simulated multivariate telemetry to real vibration condition monitoring. That extension is outside the current scope.

## 6. Target Definition

The primary target is **Remaining Useful Life in operational cycles at the current engine observation**.

For training engine `u` observed at cycle `t`:

```text
RUL(u, t) = T_failure(u) - t
```

where `T_failure(u)` is the final failure cycle for that training trajectory. Only measurements observed at or before cycle `t` may be predictors. For each official test trajectory, the supplied RUL value applies to its final observed cycle.

The eventual decision quantity is:

```text
p_H(u, t) = P(RUL(u, t) <= H | information available through cycle t)
```

where `H` is the number of cycles until the next actionable maintenance opportunity. This probability can be compared with the illustrative economic break-even probability defined in `docs/decision_framework.md`. The project must not treat a point RUL estimate as a calibrated failure probability without modeling predictive uncertainty.

## 7. Prediction Horizon

- **What:** Remaining cycles until simulated engine failure; later expressed as the probability of failure within an action window.
- **How far ahead:** The native target spans the engine's remaining life rather than a fixed window.
- **Unit:** Operational cycles.
- **Operational value:** It supports ranking assets by urgency and can support a failure-within-`H` decision once `H` represents the next feasible maintenance window.

C-MAPSS does not define the fictional client's maintenance cadence, and an aircraft-engine cycle cannot be assumed to equal a manufacturing shift or day. Therefore `H` is intentionally not fixed on Day 8. A later business-design step must choose it using inspection lead time and maintenance-window constraints, then document the mapping.

## 8. Feature Groups

| Feature group | Business meaning | Available at prediction time? | Leakage assessment and planned treatment |
| --- | --- | --- | --- |
| Engine identity (`unit_number`) | Identifies the asset and its history | Yes | Do not use as a numeric predictor; use for grouping, ordering, and split control |
| Usage / age (`cycle`) | Accumulated operational exposure | Yes | Potentially useful, but may dominate predictions through benchmark-specific lifetime patterns; retain initially and test sensitivity later |
| Operational settings (3 channels) | Operating regime affecting sensor behavior | Yes | Keep; fit any regime transformations on training data only |
| Sensor measurements (21 channels) | Multivariate condition and degradation signals | Yes | Keep subject to post-load quality review; physical meanings are anonymized |
| Trailing history features | Recent trend or variability | Yes, if computed from cycles `<= t` | Investigate later; any centered or future-inclusive window is prohibited |
| RUL and failure-within-`H` labels | Supervision and decision outcome | No | Targets only; never predictive inputs |
| Dataset subset (`FD001`–`FD004`) | Encodes condition and fault-mode design | Known metadata | Use to define experiments, not as a shortcut feature when pooling subsets |

## 9. Leakage Risk Assessment

| Feature or construct | Leakage risk | Reason | Decision |
| --- | --- | --- | --- |
| C-MAPSS supplied RUL values | High | Directly contain the outcome for official test endpoints | Remove from predictors; use only as labels for final evaluation |
| Per-unit maximum / final cycle | High | It is known only after the engine reaches failure and directly yields RUL | Use only to construct training labels, never as a feature |
| Future sensor observations | High | They reveal degradation after the prediction cycle | Exclude; sequence windows must end at cycle `t` |
| Centered rolling or full-trajectory aggregates | High | They incorporate future observations | Remove; allow trailing-only transformations fitted independently within each unit |
| Same engine in multiple splits | High | Adjacent records share identity and degradation history | Split by engine before creating rows or windows |
| `unit_number` as a predictor | Medium | It may let a model memorize trajectory-specific lifetimes | Remove from predictive features; retain as a grouping key |
| `cycle` | Medium | Available in deployment, but may exploit benchmark lifetime distributions rather than condition | Keep initially; evaluate an age-only baseline and sensitivity later |
| Operational settings | Low | Contemporaneous operating context is available at the decision point | Keep; normalize using training data only |
| Current and past sensor readings | Low | Available before the action, provided preprocessing is past-only | Keep after data-quality checks |
| Subset label | Medium | It encodes experimental condition and fault-mode combinations | Model subsets separately initially; investigate any future pooled design |
| AI4I `TWF`, `HDF`, `PWF`, `OSF`, `RNF` | High | UCI states that `Machine failure` is set when at least one mode is true | Remove from features if AI4I is ever used; keep only as outcome diagnostics |
| AI4I `UID` / `Product ID` | Medium | Unique or sequence-like identifiers can encode generated order and product type | Remove IDs; use the documented `Type` field if needed |
| IMS failure endpoint or post-failure records | High | End-of-run knowledge directly reveals proximity to outcome | Exclude from predictors; define censoring and label rules before use |

The highest-priority controls are engine-level splitting, past-only feature construction, and strict separation of RUL/final-cycle information from predictors.

## 10. Proposed Validation Strategy

1. **Use FD001 first.** Preserve its official training and test collections; do not tune decisions on the official test targets.
2. **Create development folds by engine within the official training collection.** Hold out entire `unit_number` groups so no engine contributes observations or windows to more than one split.
3. **Respect sequence order.** At cycle `t`, construct features only from cycles `<= t`. Fit scaling, imputation, feature selection, or other learned transformations using training engines only.
4. **Use the official test set once for final assessment.** Generate predictions at each test engine's final observed cycle and compare them with the supplied endpoint RUL values. Any future rolling-horizon assessment must be specified separately.
5. **Validate the decision formulation separately from the RUL task.** Once an operational window `H` is approved, derive `failure_within_H` labels and evaluate calibrated risk at decision points without changing splits.

A random row-level or stratified split is prohibited because it would place strongly related observations from the same engine in both training and validation data. Engine-level separation best approximates deployment to unseen assets, though it does not prove transfer to a real manufacturing fleet or to future calendar periods.

## 11. Data Quality Risks

### Known risks

- All C-MAPSS observations are simulated rather than collected from fielded machines.
- Sensor channels are numbered rather than given business-readable physical names.
- FD001 contains one operating condition and one fault mode, limiting operating diversity.
- No maintenance interventions, repair outcomes, costs, schedules, or asset-criticality fields are present.
- Training trajectories end at simulated failure; this can make labels easy to construct but does not reproduce censoring and intervention policies in live operations.
- The dataset provides no direct mapping from operational cycles to the fictional client's maintenance windows.

### Risks to investigate after loading

- File integrity, expected files, row counts, column counts, ordering, and uniqueness of `(unit_number, cycle)`.
- Missing, malformed, duplicated, or non-finite values.
- Constant or near-constant sensor channels and differences in sensor scale.
- Length and target distributions by engine, without using those findings to contaminate splits.
- Whether operating settings are constant as expected in FD001.
- Whether the supplied test RUL vector aligns one-to-one and in order with test engine identifiers.
- Whether any preprocessing example from external sources accidentally uses future cycles or the full dataset.

## 12. Dataset Limitations

C-MAPSS is suitable for demonstrating degradation modeling and disciplined temporal validation, but it does not fully represent industrial maintenance operations. It simulates turbofan engines rather than the fictional manufacturer's machine population, its sensor semantics are anonymized, and it omits technicians, maintenance actions, spare parts, production schedules, criticality, and financial outcomes.

The native RUL target is stronger than a current-state failure label, but the link from operational cycles to an actionable maintenance window remains an explicit design assumption. The existing rupiah cost model therefore remains illustrative and cannot be validated from C-MAPSS. Results from this benchmark will be evidence about analytical feasibility, not proof of real savings or production readiness.

## 13. Business Fit

```text
Current and historical operating settings + sensor readings
        ↓
Remaining useful life / probability of failure within an agreed cycle window
        ↓
Prioritize engines for inspection before the next feasible maintenance window
        ↓
Potential reduction in missed failures and unplanned downtime
        ↓
Illustrative expected-value rule and break-even intervention threshold
```

C-MAPSS supports temporal risk ranking and lead-time reasoning well. It does not supply intervention effectiveness, maintenance capacity, production loss, or cost data, so the downstream KPI and economic layers remain scenario-based until real operational evidence is available.

## 14. Next Step

Acquire and validate the official C-MAPSS archive locally, create a source-faithful data dictionary, and perform a structured data-quality and leakage check before any modeling. Confirm the operational meaning of horizon `H` before deriving a binary failure-within-window target.

## Day 9 Validation Findings

The official archive is now present locally and passed ZIP integrity, manifest, dimensionality, and engine-count checks. FD001 contains 20,631 training rows and 13,096 test rows across 100 engines in each collection; every raw record has 26 fields. There are no missing or non-finite values, duplicate full rows, duplicate `(unit_number, cycle)` keys, or gaps in the observed per-engine cycle sequences.

The audit identified seven constant fields (`operational_setting_3`, `sensor_1`, `sensor_5`, `sensor_10`, `sensor_16`, `sensor_18`, and `sensor_19`) and one near-constant field (`sensor_6`, with 98.03% of training rows equal to 21.61). These are classified for removal from future predictors, without changing the raw files. A Day 15 review of Table 2 in the primary paper supplied with the NASA archive resolved the physical names and units of the 21 sensor channels; variable channels are now classified `KEEP`, subject to the existing leakage controls.

The RUL target remains well defined for training data, but two decision-layer questions remain open: the row-to-engine ordering of the separate official test RUL vector needs authoritative confirmation, and business horizon `H` must be approved before a failure-within-window label can drive maintenance decisions. Day 16 uses a provisional 30-cycle label only to test baseline predictive signal. Future development must preserve engine-level separation, construct past-only features, and isolate the official test targets from all tuning.

**Modeling readiness: CONDITIONALLY READY.** See `docs/data_dictionary.md` and `notebooks/01_data_quality_audit.ipynb` for the complete evidence and required controls.
