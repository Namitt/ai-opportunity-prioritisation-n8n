# Scoring framework

This describes how an opportunity is scored and routed. The six dimension scores come from the local model, following the rubric in the workflow's system prompt. The weighted total and the routing rules are computed by the `Calculate Priority` code node.

## An important caveat first

**The scores are AI-generated preliminary recommendations. They are not objective facts and they are not final business decisions.** A small local model produces them from the text of a submission, and it will sometimes be wrong. The score is there to help a human compare ideas and start a conversation. A person in the AI and Innovation team decides what actually happens.

## The six dimensions

Each is scored 1 to 5 by the model.

| Dimension | Meaning | 1 | 5 |
|-----------|---------|---|---|
| Strategic alignment | How clearly the idea supports a stated objective | No clear connection | A validated strategic priority with evidence of direct alignment |
| Expected value | Size and credibility of the benefit | Negligible or undefined | Substantial company-wide or customer outcome, well evidenced |
| Reach | How many people or how large a group is affected | Unknown or very small | A large proportion of employees, customers or learners, evidenced |
| Evidence strength | How well the problem and benefit are supported | No measurements or research | Validated across multiple reliable sources |
| Feasibility | Whether data, systems and delivery look available | Unknown | Data, systems, integration, ownership and process all clearly available |
| Risk | Potential for harm (higher = more risk) | Internal, reversible, low impact | Biometric surveillance, children's sensitive data, high-impact automated decisions |

### Anti-inflation rules built into the rubric

The prompt deliberately resists common ways a score gets inflated:

- A high score is not awarded just because an idea involves AI, innovation or "many users".
- "All employees", "all customers" or "all learners" is not treated as evidence of reach.
- Transaction volume is not assumed to equal the number of people who benefit.
- High feasibility does not raise the expected-value score.
- Similar technology existing elsewhere does not raise feasibility.

### Evidence constraints

Weak evidence caps the other scores, so an idea cannot look strong on the strength of unsupported claims.

- **Evidence strength 1:** strategic alignment ≤ 3, expected value ≤ 2, reach ≤ 2, feasibility ≤ 3.
- **Evidence strength 2:** expected value ≤ 3, reach ≤ 3, feasibility ≤ 3 unless technical availability is explicitly evidenced.

### Mandatory risk floors

The rubric forces high risk scores where they belong, regardless of anything else in the submission:

- **Risk 5** for facial recognition, facial images, biometric identification, webcam surveillance of children, automated cheating allegations, or automatically blocking learners from assessments or services.
- **At least risk 4** when children's personal data is processed, high-impact decisions are made, or access to education could be materially affected.
- **At least risk 3** for customer-facing generative AI that could give inaccurate or misleading information.
- Missing information does **not** raise risk. It lowers evidence strength and is recorded as an assumption.

## The weighted priority score

```
priority_score =
    strategic_alignment × 0.20
  + expected_value      × 0.25
  + reach               × 0.15
  + evidence_strength   × 0.10
  + feasibility         × 0.15
  + (6 − risk)          × 0.15
```

| Component | Weight |
|-----------|:------:|
| Strategic alignment | 20% |
| Expected value | 25% |
| Reach | 15% |
| Evidence strength | 10% |
| Feasibility | 15% |
| Risk-adjusted component `(6 − risk) × 0.15` | 15% |

The risk term is inverted, so a higher risk score reduces the total. With all inputs on a 1–5 scale, the priority score always lands between **1.0 and 5.0**.

### Priority bands

| Band | Priority score |
|------|----------------|
| High | 4.0 and above |
| Medium | 3.0 to 3.99 |
| Low | below 3.0 |

The code also validates every score: anything non-numeric or out of range is clamped into 1–5 before the calculation, so a malformed value cannot distort the result.

## Routing rules

The `decision` is set by the first matching rule, in this exact order:

| Order | Condition | Decision |
|:--:|-----------|----------|
| 1 | `risk` is 4 or 5 | Governance review required |
| 2 | `evidence_strength` is 1 or 2 | More discovery required |
| 3 | `priority_score` ≥ 4.0 | Candidate for pilot |
| 4 | `priority_score` ≥ 3.0 | Proceed to discovery |
| 5 | none of the above | Innovation backlog |

### Why the order matters

Risk is checked **first**. This is the key governance property of the whole design: **a high priority score never overrides a governance requirement.** An opportunity can score 4.5 and still be routed to governance if its risk is 5. The reverse can never happen — the workflow will not send a high-risk idea to a pilot because it looked valuable.

The two "discovery" outcomes are different and worth distinguishing:

- **More discovery required** means the *evidence* is too thin to judge the idea at all. The next step is to gather evidence.
- **Proceed to discovery** means the idea has enough evidence and a medium priority, so it earns a proper discovery phase as the next step.

## Decision-to-status mapping

Each branch sets a status:

| Decision | Switch output | Status set |
|----------|---------------|------------|
| Governance review required | Governance | Awaiting governance review |
| More discovery required | More Discovery | Discovery required |
| Candidate for pilot | Pilot Candidate | Pilot candidate — approval pending |
| Proceed to discovery | Proceed to Discovery | Ready for discovery |
| Innovation backlog | Backlog (fallback) | Innovation backlog |

## Worked examples

These use illustrative scores and were verified by running the actual code node logic.

| Scores (SA/EV/RE/ES/FE/RI) | Priority score | Band | Decision | Why |
|--:|:--:|:--:|----------|-----|
| 4/5/4/4/4/2 | 4.25 | High | Candidate for pilot | Low risk, strong evidence, score ≥ 4 |
| 5/5/5/3/4/5 | 4.05 | High | Governance review required | Risk 5 checked first, overrides the high score |
| 2/1/1/1/2/2 | 1.80 | Low | More discovery required | Evidence strength 1 |
| 3/3/3/3/4/3 | 3.15 | Medium | Proceed to discovery | Enough evidence, score in 3.0–3.99 |
| 2/2/2/3/3/2 | 2.55 | Low | Innovation backlog | Enough evidence, but score below 3.0 |
