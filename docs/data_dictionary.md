# Data Dictionary

## 1. Dataset Identity

| Attribute | Verified value |
| --- | --- |
| Dataset name | NASA C-MAPSS Turbofan Engine Degradation Simulation Data Set, FD001 |
| Source | NASA Prognostics Center of Excellence |
| Source reference | [NASA/Data.gov catalog](https://catalog.data.gov/dataset/cmapss-jet-engine-simulated-data) and local `readme.txt` supplied in the archive |
| Local archive | `data/raw/CMAPSSData.zip` (12,425,978 bytes) |
| Local files audited | `data/raw/cmapss/train_FD001.txt`, `test_FD001.txt`, and `RUL_FD001.txt` |
| Archive SHA-256 | `74bef434a34db25c7bf72e668ea4cd52afe5f2cf8e44367c55a82bfd91a5a34f` (local reproducibility baseline; no published checksum was found) |
| Training data | 20,631 rows × 26 raw columns; 100 engines |
| Test data | 13,096 rows × 26 raw columns; 100 engines |
| Test target file | 100 rows × 1 column |
| Target | Remaining Useful Life (RUL), measured in operational cycles |
| Real / synthetic | High-fidelity simulated degradation data |
| Time structure | Consecutive operational cycles within each engine |
| Entity structure | Repeated observations grouped by `unit_number` |

**Dataset identity check: PASS.** The ZIP integrity test succeeded, its manifest contains the expected four train/test/RUL groups and supporting documents, every FD001 row has 26 fields, and the observed engine counts match the supplied source documentation. The SHA-256 records the local copy but cannot establish source identity without an authoritative published checksum.

## 2. Dataset Grain

One raw row represents **one simulated engine observed during one operational cycle**, with three operational settings and 21 sensor readings measured at that cycle.

| Grain check | Training | Test |
| --- | ---: | ---: |
| Entity ID | `unit_number` | `unit_number` |
| Time field | `cycle` | `cycle` |
| Unique entities | 100 | 100 |
| Average rows per entity | 206.31 | 130.96 |
| Minimum rows per entity | 128 | 31 |
| Median rows per entity | 199.0 | 133.5 |
| Maximum rows per entity | 362 | 303 |
| Duplicate entity-cycle keys | 0 | 0 |
| Cycles monotonic and consecutive from 1 | Yes, all engines | Yes, all engines |

Training trajectories continue through simulated failure. Test trajectories stop before failure, and `RUL_FD001.txt` supplies one remaining-life value for each test endpoint.

## 3. Target Definition

The target is a **regression, future-state, retrospectively derived label**:

```text
RUL(u, t) = T_failure(u) - t
```

For training engine `u`, `T_failure(u)` is its final observed failure cycle and `t` is the current cycle. RUL ranges from 0 to 361 cycles in the local training file. The 100 official test endpoint targets range from 7 to 145 cycles.

There is no binary failure target, so positive prevalence is not applicable until an operational horizon `H` is approved. For context only, the training data contains 100 terminal rows with `RUL = 0`, representing 0.4847% of rows; this is not a failure-within-`H` prevalence estimate.

## 4. Prediction Unit

One future prediction represents the estimated remaining operational cycles for **one engine at one current cycle**, using only information observed through that cycle. The maintenance decision applies to that engine before the next feasible intervention window.

The later economic decision quantity will be `P(RUL <= H)`. The dataset does not define how operational cycles map to the fictional manufacturer's calendar or maintenance windows, so the business value of `H` remains unresolved and must not be inferred from the data alone. Day 16 uses `H = 30 cycles` solely as a transparent baseline-experiment assumption; it is not an approved maintenance window.

## 5. Feature Dictionary

Observed data types refer to the local FD001 training file. Sensor names and units follow Table 2 of the primary paper included in the NASA archive, *Damage Propagation Modeling for Aircraft Engine Run-to-Failure Simulation*. The mapping assumes the dataset's 21 numbered sensor columns preserve the published Table 2 order.

| Column | Meaning | Data Type | Unit | Role | Available at Prediction Time? | Leakage Risk | Decision | Notes |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `unit_number` | Engine identifier | Integer | None | ID | Yes | Medium | ID ONLY | Grouping, ordering, and split control only; do not use as a numeric predictor |
| `cycle` | Current operational age | Integer | Cycles | Feature | Yes | Medium | KEEP | Valid at prediction time, but an age-only baseline must test whether it dominates sensor information |
| `operational_setting_1` | Anonymized operating-condition setting 1 | Float | Not documented | Feature | Yes | Low | KEEP | 158 observed values; negative values appear to be encoded settings, not confirmed physical negatives |
| `operational_setting_2` | Anonymized operating-condition setting 2 | Float | Not documented | Feature | Yes | Low | KEEP | 13 observed values; five test rows are slightly above the training maximum |
| `operational_setting_3` | Anonymized operating-condition setting 3 | Float | Not documented | Feature | Yes | Low | REMOVE | Constant at 100 in FD001 |
| `sensor_1` | T2: total temperature at fan inlet | Float | °R | Feature | Yes | Low | REMOVE | Constant at 518.67 |
| `sensor_2` | T24: total temperature at LPC outlet | Float | °R | Feature | Yes | Low | KEEP | Variable; two test values fall slightly below the training range |
| `sensor_3` | T30: total temperature at HPC outlet | Float | °R | Feature | Yes | Low | KEEP | Variable; three test values fall slightly below the training range |
| `sensor_4` | T50: total temperature at LPT outlet | Float | °R | Feature | Yes | Low | KEEP | Variable in the observed data |
| `sensor_5` | P2: pressure at fan inlet | Float | psia | Feature | Yes | Low | REMOVE | Constant at 14.62 |
| `sensor_6` | P15: total pressure in bypass duct | Float | psia | Feature | Yes | Low | REMOVE | Only two values; 21.61 accounts for 98.03% of training rows |
| `sensor_7` | P30: total pressure at HPC outlet | Float | psia | Feature | Yes | Low | KEEP | Variable in the observed data |
| `sensor_8` | Nf: physical fan speed | Float | rpm | Feature | Yes | Low | KEEP | Variable; one test value falls slightly below the training range |
| `sensor_9` | Nc: physical core speed | Float | rpm | Feature | Yes | Low | KEEP | Variable in the observed data |
| `sensor_10` | epr: engine pressure ratio (P50/P2) | Float | Dimensionless | Feature | Yes | Low | REMOVE | Constant at 1.30 |
| `sensor_11` | Ps30: static pressure at HPC outlet | Float | psia | Feature | Yes | Low | KEEP | Variable; two test values fall slightly below the training range |
| `sensor_12` | phi: ratio of fuel flow to Ps30 | Float | pps/psi | Feature | Yes | Low | KEEP | Variable; three test values exceed the training range |
| `sensor_13` | NRf: corrected fan speed | Float | rpm | Feature | Yes | Low | KEEP | Variable in the observed data |
| `sensor_14` | NRc: corrected core speed | Float | rpm | Feature | Yes | Low | KEEP | Variable in the observed data |
| `sensor_15` | BPR: bypass ratio | Float | Dimensionless | Feature | Yes | Low | KEEP | Variable in the observed data |
| `sensor_16` | farB: burner fuel-air ratio | Float | Dimensionless | Feature | Yes | Low | REMOVE | Constant at 0.03 |
| `sensor_17` | htBleed: bleed enthalpy | Integer | Not stated | Feature | Yes | Low | KEEP | Variable with 13 observed values |
| `sensor_18` | Nf_dmd: demanded fan speed | Integer | rpm | Feature | Yes | Low | REMOVE | Constant at 2,388 |
| `sensor_19` | PCNfR_dmd: demanded corrected fan speed | Float | rpm | Feature | Yes | Low | REMOVE | Constant at 100 |
| `sensor_20` | W31: HPT coolant bleed | Float | lbm/s | Feature | Yes | Low | KEEP | Variable in the observed data |
| `sensor_21` | W32: LPT coolant bleed | Float | lbm/s | Feature | Yes | Low | KEEP | Variable; two test values exceed the training range |
| `rul` | Cycles remaining until failure | Integer (derived) | Cycles | Target | No | High if used as input | TARGET | Derived from the last cycle of each training trajectory; never a predictor |
| `rul_at_last_observation` | Official RUL at each test engine's endpoint | Integer | Cycles | Target | No | High if used as input | TARGET | Stored separately in `RUL_FD001.txt`; evaluation target only |
| `T_failure` / per-unit maximum cycle | Final failure cycle used to derive training RUL | Integer (derived) | Cycles | Post-outcome | No | High | REMOVE | Known only after failure and must never enter a predictive feature set |

`REMOVE` is a modeling-policy classification only. No raw columns or rows were changed on Day 9.

## 6. Feature Availability at Prediction Time

The current cycle, operating settings, and current or historical sensor readings are available at the decision point in the benchmark design. Engine ID is also known but is retained only to establish sequence and split boundaries.

RUL, final cycle, future sensor observations, full-trajectory statistics, centered windows, and any transformation using cycles after `t` are unavailable at prediction time. Their use as predictors would invalidate the maintenance decision.

## 7. Leakage Classification

| Leakage class | Fields / constructs | Control |
| --- | --- | --- |
| High | `rul`, official test RUL, `T_failure`, future observations, full-trajectory or centered aggregates | Targets or prohibited inputs only |
| High | Any engine represented in more than one development split | Split by `unit_number` before creating rows or windows |
| Medium | `unit_number` | Retain only as an ID and grouping key |
| Medium | `cycle` | Keep provisionally; compare later with an age-only baseline and document sensitivity |
| Low | Contemporaneous operational settings and sensor measurements | Eligible only after the stated quality decisions and with past-only preprocessing |

## 8. Fields Requiring Verification

- Confirm that the numbered dataset columns map exactly to the 21 Table 2 outputs in published order; the archive README does not repeat the names.
- Units and exact semantics of the three operational settings.
- The authoritative row-to-engine mapping for `RUL_FD001.txt`; local counts align one-to-one with 100 test engines, but the supplied `readme.txt` does not explicitly state the ordering rule.
- An authoritative published checksum for `CMAPSSData.zip`, if one exists.
- The business-defined horizon `H` and how C-MAPSS cycles relate to an actionable maintenance window; Day 16's provisional 30-cycle label does not resolve this question.
- Whether `sensor_6` should be excluded categorically or retained for compatibility with a documented benchmark protocol; its observed information content is negligible in FD001.

## 9. Notes for Future Modeling

- Modeling readiness is **CONDITIONALLY READY**.
- Freeze engine-disjoint development splits before fitting transformations or constructing windows.
- Exclude `unit_number`, all constant fields, near-constant `sensor_6`, and every target/post-outcome construct from predictors.
- Use the variable sensor channels with their Table 2 meanings, while documenting that their observed ranges remain benchmark-specific simulation outputs.
- Preserve the official test collection for one final assessment; do not use its RUL vector for tuning.
- Confirm a business-defined `H` before interpreting the provisional Day 16 binary target operationally or connecting its probabilities to the economic threshold.
