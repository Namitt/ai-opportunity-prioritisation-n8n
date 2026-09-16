# Limitations and future improvements

An honest account of what this prototype does not do, and what would make it better. Being clear about the gaps is part of the point of the project.

## Limitations

### Model

- **Small local model.** `llama3.2:3b` is lightweight. Its scores vary between runs and it will misjudge some submissions. Temperature is set low (0.1) to reduce variance, but this does not make the judgement reliable enough for real decisions.
- **No grounding.** The model sees only the submission text. It cannot check a claim against any real strategy, cost or usage data, so its "strategic alignment" and "value" scores are judgements about the text, not the world.

### Workflow completeness

- **Ownership and reviewer not populated.** The data model defines `assigned_team`, `reviewer` and `review_required`, but the current branch nodes set only `status`. These fields are a defined next step, not a finished feature.
- **No approval loop.** "Pilot candidate — approval pending" is a status, not a routed request to a named approver.
- **No notifications.** Nobody is alerted when a high-risk item lands in governance.
- **Not all assessment fields are stored.** The model produces `assumptions` and `governance_concerns`, but not every field is mapped into the portfolio table. A reviewer currently sees the scores and recommendation more readily than the full reasoning.

### Data and storage

- **Prototype store.** The n8n Data Table is fine for a demonstration but is not a queryable, backed-up, access-controlled portfolio system.
- **No history.** The workflow inserts a new record; it does not track how an opportunity's status changes over time.

### Testing

- **End-to-end not captured.** The deterministic routing is verified, but the local model's live scoring has not been run and recorded here. See [test-plan-and-results.md](test-plan-and-results.md).
- **No adversarial testing.** The prototype has not been tested against deliberately misleading submissions designed to game a high score.

### Security and access

- **No authentication.** The prototype runs locally with no access control. Anyone who can reach the form can submit, and anyone who can open n8n can change the rules.

## Future improvements

Roughly in order of value.

### Close the ownership and approval gap

- Populate `assigned_team`, `reviewer` and `review_required` per branch.
- Route "Candidate for pilot" and "Governance review required" to named people and wait for an explicit decision before the status changes.
- Add a Microsoft Teams (or email) notification when an item needs attention.

### Store the full assessment

- Map `assumptions`, `governance_concerns`, `opportunity_type` and `summary` into the portfolio store, so a reviewer sees the reasoning, not only the score.
- Keep a status history so the journey of each opportunity is visible.

### Strengthen the model layer

- Move to an approved enterprise model for a production version.
- Add a lightweight second check on any submission the model scores as high-risk, so a rule and a model both have to agree before something is treated as low-risk.
- Log model and prompt versions with each assessment.

### Improve prioritisation over time

- Review the weights and thresholds with the team periodically.
- Once real outcomes exist (which pilots succeeded, which backlog items were later revived), compare them against the original scores to see whether the model is calibrated, and adjust.

### Harden for real use

- Single sign-on and role-based access.
- Secure secret management.
- Monitoring, error handling, backups, audit logs and data retention.
- The full responsible-AI, privacy, safeguarding, security and accessibility review before any learner-facing use.

## A note on scope

This is a portfolio proof of concept. The limitations above are not oversights to hide; they are the honest boundary of what a local, free, single-analyst prototype should claim. The value of the project is in the analysis, the scoring design and the governance thinking, all of which carry over to a production build described in [production-architecture.md](production-architecture.md).
