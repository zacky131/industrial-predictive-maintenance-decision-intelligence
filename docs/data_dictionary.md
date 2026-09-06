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

The later economic decision quantity will be `P(RUL <= H)`. The dataset does not define how operational cycles map to the fictional manufacturer's calendar or maintenance windows, so `H` remains unresolved and must not be inferred from the data alone.

## 5. Feature Dictionary

Observed data types refer to the local FD001 training file. NASA's supplied documentation describes three operating settings and sensor measurements but does not provide physical names or units for those channels.

| Column | Meaning | Data Type | Unit | Role | Available at Prediction Time? | Leakage Risk | Decision | Notes |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `unit_number` | Engine identifier | Integer | None | ID | Yes | Medium | ID ONLY | Grouping, ordering, and split control only; do not use as a numeric predictor |
| `cycle` | Current operational age | Integer | Cycles | Feature | Yes | Medium | KEEP | Valid at prediction time, but an age-only baseline must test whether it dominates sensor information |
| `operational_setting_1` | Anonymized operating-condition setting 1 | Float | Not documented | Feature | Yes | Low | KEEP | 158 observed values; negative values appear to be encoded settings, not confirmed physical negatives |
| `operational_setting_2` | Anonymized operating-condition setting 2 | Float | Not documented | Feature | Yes | Low | KEEP | 13 observed values; five test rows are slightly above the training maximum |
| `operational_setting_3` | Anonymized operating-condition setting 3 | Float | Not documented | Feature | Yes | Low | REMOVE | Constant at 100 in FD001 |
| `sensor_1` | Anonymized sensor measurement 1 | Float | Not documented | Feature | Yes | Low | REMOVE | Constant at 518.67 |
| `sensor_2` | Anonymized sensor measurement 2 | Float | Not documented | Feature | Yes | Low | INVESTIGATE | Variable; two test values fall slightly below the training range |
| `sensor_3` | Anonymized sensor measurement 3 | Float | Not documented | Feature | Yes | Low | INVESTIGATE | Variable; three test values fall slightly below the training range |
| `sensor_4` | Anonymized sensor measurement 4 | Float | Not documented | Feature | Yes | Low | INVESTIGATE | Variable in the observed data |
| `sensor_5` | Anonymized sensor measurement 5 | Float | Not documented | Feature | Yes | Low | REMOVE | Constant at 14.62 |
| `sensor_6` | Anonymized sensor measurement 6 | Float | Not documented | Feature | Yes | Low | REMOVE | Only two values; 21.61 accounts for 98.03% of training rows |
| `sensor_7` | Anonymized sensor measurement 7 | Float | Not documented | Feature | Yes | Low | INVESTIGATE | Variable in the observed data |
| `sensor_8` | Anonymized sensor measurement 8 | Float | Not documented | Feature | Yes | Low | INVESTIGATE | Variable; one test value falls slightly below the training range |
| `sensor_9` | Anonymized sensor measurement 9 | Float | Not documented | Feature | Yes | Low | INVESTIGATE | Variable in the observed data |
| `sensor_10` | Anonymized sensor measurement 10 | Float | Not documented | Feature | Yes | Low | REMOVE | Constant at 1.30 |
| `sensor_11` | Anonymized sensor measurement 11 | Float | Not documented | Feature | Yes | Low | INVESTIGATE | Variable; two test values fall slightly below the training range |
| `sensor_12` | Anonymized sensor measurement 12 | Float | Not documented | Feature | Yes | Low | INVESTIGATE | Variable; three test values exceed the training range |
| `sensor_13` | Anonymized sensor measurement 13 | Float | Not documented | Feature | Yes | Low | INVESTIGATE | Variable in the observed data |
| `sensor_14` | Anonymized sensor measurement 14 | Float | Not documented | Feature | Yes | Low | INVESTIGATE | Variable in the observed data |
| `sensor_15` | Anonymized sensor measurement 15 | Float | Not documented | Feature | Yes | Low | INVESTIGATE | Variable in the observed data |
| `sensor_16` | Anonymized sensor measurement 16 | Float | Not documented | Feature | Yes | Low | REMOVE | Constant at 0.03 |
| `sensor_17` | Anonymized sensor measurement 17 | Integer | Not documented | Feature | Yes | Low | INVESTIGATE | Variable with 13 observed values |
| `sensor_18` | Anonymized sensor measurement 18 | Integer | Not documented | Feature | Yes | Low | REMOVE | Constant at 2,388 |
| `sensor_19` | Anonymized sensor measurement 19 | Float | Not documented | Feature | Yes | Low | REMOVE | Constant at 100 |
| `sensor_20` | Anonymized sensor measurement 20 | Float | Not documented | Feature | Yes | Low | INVESTIGATE | Variable in the observed data |
| `sensor_21` | Anonymized sensor measurement 21 | Float | Not documented | Feature | Yes | Low | INVESTIGATE | Variable; two test values exceed the training range |
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

- Physical meanings and engineering units for all 21 anonymized sensor channels.
- Units and exact semantics of the three operational settings.
- The authoritative row-to-engine mapping for `RUL_FD001.txt`; local counts align one-to-one with 100 test engines, but the supplied `readme.txt` does not explicitly state the ordering rule.
- An authoritative published checksum for `CMAPSSData.zip`, if one exists.
- The business-defined horizon `H` and how C-MAPSS cycles relate to an actionable maintenance window.
- Whether `sensor_6` should be excluded categorically or retained for compatibility with a documented benchmark protocol; its observed information content is negligible in FD001.

## 9. Notes for Future Modeling

- Modeling readiness is **CONDITIONALLY READY**.
- Freeze engine-disjoint development splits before fitting transformations or constructing windows.
- Exclude `unit_number`, all constant fields, near-constant `sensor_6`, and every target/post-outcome construct from predictors.
- Treat the remaining anonymized sensors as provisional until their source semantics and expected ranges are documented as far as the benchmark permits.
- Preserve the official test collection for one final assessment; do not use its RUL vector for tuning.
- Define `H` before creating a binary failure-within-window target or connecting results to the economic threshold.
