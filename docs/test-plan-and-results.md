# Test plan and results

This describes how the workflow is tested, what has been verified, and what has not. It separates two things carefully:

- **Deterministic routing** — the `Calculate Priority` code and the `Route Decision` switch. Given a set of scores, the outcome is fixed and reproducible. This has been **verified**.
- **End-to-end behaviour** — which depends on the local model's own scoring of a submission. This has **not been run and captured** for this repository, so it is recorded as **Not yet tested**.

**No test is claimed to have passed unless it was actually run.** Where a result is missing, it says so.

## What was verified, and how

The five scenarios below were run through the exact scoring and routing logic taken from the workflow (the same formula, thresholds and rule order as the `Calculate Priority` node, plus the switch-to-status mapping). This confirms that, *given the illustrative scores*, each scenario routes to the expected branch. It does not test the model that would produce those scores in a live run.

The verification is reproducible: the routing produced the priority scores and decisions shown here exactly.

## Test scenarios

The intake text for each scenario is in [`../sample-data/test-cases.csv`](../sample-data/test-cases.csv).

### TC-01 — Low-risk internal automation

- **Idea:** auto-draft first-line replies to repetitive internal IT-support tickets, with an agent reviewing before sending.
- **Illustrative scores:** SA 4, EV 5, RE 4, ES 4, FE 4, RI 2
- **Priority score:** 4.25 (High)
- **Expected decision:** Candidate for pilot → status "Pilot candidate — approval pending"
- **Routing verified:** ✅
- **End-to-end:** Not yet tested

### TC-02 — High-risk learner-facing biometric use

- **Idea:** facial-recognition proctoring that watches learners through their webcams during online exams and automatically flags suspected cheating.
- **Illustrative scores:** SA 5, EV 5, RE 5, ES 3, FE 4, RI 5
- **Priority score:** 4.05 (High)
- **Expected decision:** **Governance review required** → status "Awaiting governance review"
- **Routing verified:** ✅
- **End-to-end:** Not yet tested
- **Why it matters:** the priority score of 4.05 would qualify for a pilot, but risk 5 routes it to governance. This is the design working as intended: high priority does not override governance. The scoring rubric also *mandates* risk 5 for biometric surveillance of children and automated cheating allegations, so a live run should reach the same outcome.

### TC-03 — Vague proposal with insufficient evidence

- **Idea:** "use AI to improve engagement" with no baseline, no affected numbers and no supporting evidence.
- **Illustrative scores:** SA 2, EV 1, RE 1, ES 1, FE 2, RI 2
- **Priority score:** 1.80 (Low)
- **Expected decision:** More discovery required → status "Discovery required"
- **Routing verified:** ✅
- **End-to-end:** Not yet tested

### TC-04 — Medium-priority opportunity requiring discovery

- **Idea:** summarise recurring themes in customer-support conversations to guide help-content updates.
- **Illustrative scores:** SA 3, EV 3, RE 3, ES 3, FE 4, RI 3
- **Priority score:** 3.15 (Medium)
- **Expected decision:** Proceed to discovery → status "Ready for discovery"
- **Routing verified:** ✅
- **End-to-end:** Not yet tested

### TC-05 — Low-value opportunity suitable for the backlog

- **Idea:** auto-generate a light-hearted internal newsletter intro each week.
- **Illustrative scores:** SA 2, EV 2, RE 2, ES 3, FE 3, RI 2
- **Priority score:** 2.55 (Low)
- **Expected decision:** Innovation backlog → status "Innovation backlog"
- **Routing verified:** ✅
- **End-to-end:** Not yet tested

## Results summary

| Case | Priority score | Expected decision | Routing verified | End-to-end tested |
|------|:--:|------------------|:--:|:--:|
| TC-01 | 4.25 | Candidate for pilot | ✅ | Not yet tested |
| TC-02 | 4.05 | Governance review required | ✅ | Not yet tested |
| TC-03 | 1.80 | More discovery required | ✅ | Not yet tested |
| TC-04 | 3.15 | Proceed to discovery | ✅ | Not yet tested |
| TC-05 | 2.55 | Innovation backlog | ✅ | Not yet tested |

All five deterministic routing paths are covered and verified. Every branch of the switch is exercised by at least one case.

## Actual end-to-end results (local run)

A local run of the full workflow (form → local model → scoring → routing → storage) produced five real portfolio records. The raw output is in [`../sample-data/example-portfolio-output.csv`](../sample-data/example-portfolio-output.csv). These are genuine end-to-end results, not the illustrative TC cases above, so they are recorded here separately.

The submissions were different fictional scenarios from the TC set, so they are listed on their own terms. Every recorded `priority_score` and `decision` was checked against the scoring logic and matched exactly, which confirms the deployed workflow scores and routes as designed.

| Submission | Scores (SA/EV/RE/ES/FE/RI) | Priority score | Decision (recorded) | Branch exercised | Logic check |
|------------|:--:|:--:|--------------------|------------------|:--:|
| Facial recognition for learner identity verification | 3/3/5/3/5/4 | 3.45 | Governance review required | Governance | ✅ matches |
| AI assistant for improving company meetings | 3/3/3/1/3/2 | 2.95 | More discovery required | More Discovery | ✅ matches |
| Automate supplier invoice matching | 3/3/3/4/4/2 | 3.40 | Proceed to discovery | Proceed to Discovery | ✅ matches |
| Automatically classify customer-support tickets | 3/3/3/3/5/2 | 3.45 | Proceed to discovery | Proceed to Discovery | ✅ matches |
| Automate the annual office equipment survey | 3/3/3/4/5/1 | 3.70 | Proceed to discovery | Proceed to Discovery | ✅ matches |

Two observations worth recording honestly:

- **Governance override confirmed on real data.** The facial-recognition submission scored 3.45 (Medium priority) but was routed to governance because its risk score of 4 is checked first. This is the governance-before-value rule working end-to-end, not just in the deterministic check.
- **The small model under-scored a mandatory risk.** The scoring rubric *mandates* risk 5 for facial recognition and biometric identification, but the local model assigned risk 4. The routing outcome was still correct (risk ≥ 4 goes to governance), which is exactly why the threshold sits at 4 rather than 5 — it leaves headroom for the model to under-score. It is also a concrete reminder that a small local model is imperfect and that the rule floor and human review, not the model's own number, are the real safeguards.

**Branches with live end-to-end evidence:** Governance, More Discovery, Proceed to Discovery (3 of 5).
**Branches not yet captured end-to-end:** Candidate for pilot and Innovation backlog. Their routing is verified deterministically above, but no live run has been recorded for them yet.

## How to run the end-to-end tests and record real results

To complete this section with genuine end-to-end evidence:

1. Set up the workflow following the [README](../README.md#12-local-setup-instructions).
2. For each scenario, submit the intake text from `sample-data/test-cases.csv` through the form.
3. Open the `ai_opportunity_portfolio` data table and read the `strategic_alignment`, `expected_value`, `reach`, `evidence_strength`, `feasibility`, `risk`, `priority_score`, `decision` and `status` for the new row.
4. Record the **actual** model scores and the **actual** decision next to the expected ones.
5. Note that a small model varies between runs. If a live score differs from the illustrative one, check that the *routing rule* still behaved correctly for the score the model actually produced — that is the property under test, not the model's exact number.
6. Capture a screenshot of the results table (see [`../assets/architecture-diagram.md`](../assets/architecture-diagram.md)) and update the table above, replacing "Not yet tested" with the observed outcome.

## What is deliberately not claimed

- No claim that the local model reproduces any exact score between runs. A small model varies, and the facial-recognition run above (risk 4 where the rubric mandates 5) shows it.
- No end-to-end evidence yet for the Candidate-for-pilot or Innovation-backlog branches; their routing is verified deterministically only.
- No performance, accuracy or reliability metrics, because none have been measured across enough runs to be meaningful.
- The five live records are from fictional submissions run locally. They are not real company opportunities.
