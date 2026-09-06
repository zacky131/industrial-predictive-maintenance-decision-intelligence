# Business Case

## Client Context

The client is a fictional medium-sized Indonesian manufacturer operating approximately 100 industrial machines. Machine availability directly affects production capacity and delivery performance, while maintenance is currently conducted mainly in response to faults. Unexpected failures disrupt operations and create unplanned work for the maintenance team. Management wants to evaluate whether predictive maintenance could support earlier, better-targeted intervention.

## Business Problem

Management has limited visibility into the condition of individual machines and cannot consistently identify which assets require attention before failure. As a result, unplanned downtime causes lost production, emergency maintenance, and delayed customer orders. The reactive workload also makes it difficult to prioritize technicians, spare parts, and planned maintenance windows effectively. The business needs a more reliable basis for directing limited maintenance resources toward the machines that present the greatest near-term operational and financial risk.

## Decision to Improve

The key management decision is:

> Which machines should the maintenance team inspect or prioritize before the next maintenance window?

This decision must balance the expected consequence of a missed failure against the cost and disruption of an unnecessary intervention. A predictive model is useful only if it improves this operational decision.

## Business KPIs

The following KPIs define business success. Any future analytical metrics will be supporting measures used to assess whether an analytical approach can improve these outcomes; model accuracy alone is not the business objective.

| KPI | Type | Definition |
| --- | --- | --- |
| Unplanned downtime hours | Operational | Total production time lost because of unexpected machine stoppages during a reporting period. |
| Unexpected failures | Operational | Count of machine failures that were not addressed during a planned maintenance window. |
| Machine availability | Operational | Percentage of scheduled production time during which machines are available to operate. |
| Preventive intervention rate | Operational | Percentage of maintenance interventions completed before an anticipated failure. |
| Emergency maintenance events | Operational | Count of urgent, unplanned maintenance responses during a reporting period. |
| Downtime and maintenance cost | Financial | Estimated cost of lost production plus planned and emergency maintenance expenditure. |
| Estimated avoided loss and annual net savings | Financial | Estimated failure-related loss avoided through timely intervention, less the incremental cost of the predictive-maintenance program. |

## Analytical Question

Can machine sensor and operating data identify equipment at risk of failure early enough to enable preventive intervention, reduce unplanned downtime and financial losses, and avoid excessive unnecessary maintenance?

## Assumptions and Limitations

- This is currently a fictional portfolio case, not an engagement for a real company.
- No real client data has been used.
- At the time of the Day 1 business framing, no dataset had been selected.
- The machine count and all future operational or financial assumptions are illustrative unless explicitly supported by a cited source; they must not be treated as real company figures.
- Later project stages will assess whether suitable public data can support the use case and validate the proposed decision framework.
