# EDA Findings

## Executive Summary

Exploratory analysis of 20,631 FD001 observations finds that simulated failure proximity is associated with coordinated changes across temperature, pressure, fuel-flow ratio, and coolant-bleed measurements. Joint sensor conditions separate late-life operation more clearly than individual conditions, supporting a future multi-signal risk approach. Engine failure ages nevertheless range from 128 to 362 cycles, so a universal age-based maintenance rule would be unreliable. Operating settings show little terminal-state separation within FD001's single operating regime and do not support an operating-policy recommendation. The next step should freeze engine-disjoint validation splits and define the business horizon before any model is trained. These findings support candidate signals and maintenance-prioritization logic, but they do not establish causality, out-of-sample prediction, production thresholds, or financial value.

## 1. Questions Investigated

1. Do operating settings distinguish terminal from non-terminal operation?
2. Do thermal measurements drift as simulated failure approaches?
3. Do pressure, fuel-flow ratio, and coolant-bleed measures show complementary degradation?
4. Do three domain-related sensor pairs add insight beyond either condition alone?
5. Does engine lifetime variation weaken a universal age rule?
6. Are extreme sensor bands supported by enough observations and engines?
7. Which patterns are predictive, actionable, and decision-relevant?

## 2. Top Three Findings

### Finding 1 — Failure proximity appears across a coherent family of condition signals

**Evidence:** From more than 125 cycles remaining to the retrospective final-30 band, median T50 rises from 1,403.04 to 1,422.80 °R and Ps30 rises from 47.36 to 47.97 psia. Over the same bands, phi falls from 521.89 to 520.29 pps/psi, HPT coolant bleed falls from 38.93 to 38.56 lbm/s, and LPT coolant bleed falls from 23.36 to 23.13 lbm/s. Each band includes all 100 engines.

**Why it matters:** Consistent movement across multiple physical families is stronger descriptive evidence than one isolated sensor excursion.

**Modeling implication:** Prioritize the validated variable temperature, pressure, ratio, and flow channels in an engine-disjoint baseline comparison.

**Maintenance implication:** Concordant deterioration could raise inspection priority before relying on a single reading or asset age alone.

**Limitation:** RUL bands are retrospective outcomes; repeated observations within each engine are dependent.

### Finding 2 — Concordant sensor states identify late-life operation more clearly

**Evidence:** Records with both high T50 and low phi contain 3,859 observations across all 100 engines; 68.96% are in the retrospective final-30 band, versus 15.32% for high T50 alone, 11.28% for low phi alone, and 0.60% when neither condition holds. This joint group contains 2,661 of the 3,100 final-30 rows. Similar concentration appears for high T30 plus high Ps30 and for low W31 plus low W32.

**Why it matters:** Related measurements provide mutually reinforcing condition evidence and reduce dependence on one extreme value.

**Modeling implication:** Test whether simple models capture these relationships without hard-coding exploratory quartile cutoffs.

**Maintenance implication:** A multi-signal review rule may provide an interpretable basis for escalating inspection priority.

**Limitation:** Quartiles are in-sample descriptive thresholds, not production alert rules or the approved business horizon.

### Finding 3 — Failure age varies too widely for one universal maintenance point

**Evidence:** Failure occurs between 128 and 362 cycles across the 100 simulated engines, with a median of 199 and standard deviation of 46.34 cycles. Thirteen engines fail before entering the oldest row-level age quartile.

**Why it matters:** A fixed age rule could intervene too early for long-lived engines and too late for shorter-lived engines.

**Modeling implication:** Retain cycle age as context, compare future models against an age-only reference, and test whether condition sensors add value.

**Maintenance implication:** Use condition evidence to refine age-based schedules and rank which assets deserve earlier attention.

**Limitation:** Failure-cycle variation is simulated and retrospectively observed; it does not establish the distribution for client equipment.

## 3. Implications for Modeling

- Begin with the Day 15 safe feature set; keep `unit_number`, constant channels, near-constant `sensor_6`, RUL, final-cycle values, and future information out of predictors.
- Compare future condition-based models with an age-only reference.
- Split by engine before transformations, cutoff selection, or sequence construction; never split rows randomly.
- Preserve continuous measurements rather than encoding exploratory quartiles or deciles as fixed rules.
- Evaluate joint behavior on held-out engines without prematurely constructing interaction features.
- Define horizon `H` before creating a failure-within-window target, and later assess calibration before applying the economic threshold.

## 4. Implications for Maintenance Decisions

- Prioritize human review when multiple thermal, pressure, and flow indicators deteriorate together.
- Treat cycle age as supporting context rather than a universal intervention trigger.
- Do not change operating policy based on FD001's settings; terminal and non-terminal observations show little separation.
- Design future alerts as capacity-constrained prioritization inputs, not automatic work orders.
- Require validated lead time and intervention evidence before claiming reduced downtime or avoided cost.

## 5. What We Cannot Conclude

- Association is not causation; EDA does not identify a causal failure mechanism.
- Descriptive separation does not prove prediction on unseen engines or realistic deployment data.
- Retrospective RUL bands are not operational labels, calibrated probabilities, or production thresholds.
- Large row counts do not remove the limitation of only 100 engines and 100 terminal events.
- FD001's simulated, single-regime, single-fault design limits external validity for manufacturing equipment.
- Observed patterns may weaken under field noise, maintenance interventions, censoring, and changing operating regimes.
- No model performance, actionable prediction horizon, intervention effectiveness, or business value has been demonstrated.

## 6. Next Analysis

Freeze engine-disjoint development splits and agree on business horizon `H`. In the next approved modeling session, compare an age-only reference with a simple condition-based baseline using training engines only, reserving the official test set for final assessment.
