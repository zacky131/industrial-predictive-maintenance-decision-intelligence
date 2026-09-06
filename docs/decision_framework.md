# Maintenance Decision and Economic Framework

## 1. Decision Being Optimized

For each machine, management must decide whether to continue normal operation or initiate preventive inspection and maintenance before the next available maintenance window.

- **Action A — Continue operating:** Take no preventive action before the next maintenance window.
- **Action B — Preventive inspection / maintenance:** Inspect the machine and perform preventive maintenance where indicated.

A predictive model has business value only when its output improves this decision by helping management avoid costly failures without causing excessive unnecessary interventions.

## 2. Decision Consequences

| Actual machine condition | Continue operating | Preventive intervention |
| --- | --- | --- |
| Healthy | No additional intervention cost | Unnecessary inspection or maintenance cost |
| Approaching failure | Potential failure, downtime, and emergency repair | Potentially avoided failure, but intervention cost is incurred |

This decision framing will later connect to model outcomes as follows:

| Outcome | Decision meaning |
| --- | --- |
| True positive | Useful intervention on a machine approaching failure |
| False positive | Unnecessary intervention on a healthy machine |
| False negative | Costly missed failure after continuing operation |
| True negative | Correctly continuing operation of a healthy machine |

These categories describe business consequences; no machine-learning metrics are calculated at this stage.

## 3. Illustrative Economic Assumptions

| Assumption | Illustrative value |
| --- | ---: |
| Downtime per failure | 6 hours |
| Production loss per downtime hour | Rp 8,000,000 |
| Emergency repair cost | Rp 10,000,000 |
| Preventive inspection cost | Rp 2,000,000 |
| Preventive maintenance cost | Rp 4,000,000 |
| Probability intervention successfully prevents failure | 70% |

> These values are hypothetical assumptions created only to define the decision framework. They are not based on confidential or real client data and will later be subjected to sensitivity analysis.

## 4. Failure Cost

The simplified cost of a failure is:

```text
Failure Cost = Downtime Cost + Emergency Repair Cost
```

Using the illustrative assumptions:

```text
Downtime Cost
= 6 hours × Rp 8,000,000/hour
= Rp 48,000,000

Failure Cost
= Rp 48,000,000 + Rp 10,000,000
= Rp 58,000,000
```

This simplified model includes direct production loss and emergency repair only. Indirect costs such as reputational loss and contract penalties could be considered in a future extension but are excluded here.

## 5. Preventive Intervention Cost

The simplified preventive intervention cost is:

```text
Preventive Intervention Cost
= Inspection Cost + Preventive Maintenance Cost
= Rp 2,000,000 + Rp 4,000,000
= Rp 6,000,000
```

## 6. Expected Decision Value

Let:

```text
p  = predicted probability of failure
s  = probability that preventive intervention successfully prevents the failure
Cf = failure cost
Ci = preventive intervention cost
```

The simplified expected avoided loss from intervention is:

```text
Expected Avoided Loss = p × s × Cf
```

Intervention is economically attractive when:

```text
p × s × Cf > Ci
```

This is a simplified decision rule. A later version may incorporate partial prevention effectiveness, machine-specific failure severity, intervention capacity, different failure modes, maintenance-window constraints, and uncertainty in cost assumptions. None of these extensions is implemented at this stage.

## 7. Break-Even Failure Probability

Starting from the decision rule:

```text
p × s × Cf > Ci
```

the break-even predicted failure probability is:

```text
p* = Ci / (s × Cf)

p*
= Rp 6,000,000 / (0.70 × Rp 58,000,000)
≈ 0.148
```

Under these illustrative assumptions, preventive intervention begins to become economically attractive at a predicted failure probability of roughly **14.8%**.

> This value is not yet a production threshold. It depends strongly on assumptions, capacity constraints, model calibration, and how failure probability is defined.

## 8. Threshold Selection Principle

A default classification threshold of 0.5 would ignore the imbalance between the cost of a missed failure and the cost of an unnecessary intervention. The final threshold should instead maximize expected business value:

```text
t* = argmax Expected Business Value(t)
```

The final model threshold should be selected by balancing avoided failure loss, unnecessary maintenance, missed failures, and operational capacity. Threshold optimization is not implemented at this stage.

## 9. Operational Constraints

- **Maintenance capacity:** The maintenance team may be able to inspect only a limited number of machines per day, requiring alerts to be prioritized.
- **Production scheduling:** Some machines cannot be stopped immediately and may only be inspected during planned maintenance windows.
- **False-alarm burden:** Excessive alerts can consume labor, increase maintenance cost, and disrupt production.
- **Spare-parts availability:** A recommended intervention may not be actionable if the required parts are unavailable.
- **Machine criticality:** Machines with greater production or safety consequences may require different priorities and thresholds.

## 10. Illustrative Machine Decisions

For each fictional machine:

```text
Expected Avoided Loss = p × 0.70 × Rp 58,000,000
```

The result is compared with the illustrative intervention cost of Rp 6,000,000.

| Machine | Predicted failure probability | Expected avoided loss | Comparison with intervention cost | Illustrative decision |
| --- | ---: | ---: | --- | --- |
| M01 | 5% | Rp 2.03M | Below Rp 6.00M | Likely do not intervene |
| M02 | 20% | Rp 8.12M | Above Rp 6.00M | Intervention may be justified |
| M03 | 70% | Rp 28.42M | Well above Rp 6.00M | Intervention strongly justified |

These are illustrative decisions only. They assume calibrated probabilities, available maintenance capacity, and identical costs and intervention effectiveness across machines.

## 11. Assumptions and Limitations

- All values are hypothetical and do not represent real company figures or financial impact.
- At the time of the Day 2 decision framing, no dataset had been selected.
- Predicted failure probability is assumed to be calibrated.
- Intervention effectiveness is simplified as a single 70% value.
- Machine criticality and differences in failure severity are not yet modeled.
- Capacity constraints are described conceptually but are not optimized.
- The framework does not yet account for uncertainty in cost estimates.
- The assumptions and decision threshold will later be validated through sensitivity analysis.
