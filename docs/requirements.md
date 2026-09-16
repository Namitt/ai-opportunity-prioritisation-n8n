# Requirements

> Requirements for the prototype. Where a requirement is a target for a production version rather than something the prototype implements, it is marked accordingly.

## Scope

**In scope for the prototype:** a single intake form, an AI-assisted first-pass assessment, a weighted priority score, rule-based routing to five outcomes, per-branch status, and storage of the record in a portfolio table.

**Out of scope for the prototype:** approval workflows, notifications, authentication and access control, a production data store, and any external system integration. These belong to the [production architecture](production-architecture.md).

## Stakeholders

| Stakeholder | Interest |
|-------------|----------|
| AI and Innovation team | Consistent intake, fair prioritisation, early risk visibility |
| Idea submitters | A simple way to put a problem forward |
| Governance / privacy / safeguarding reviewers | Early sight of risky ideas |
| Leadership | A single prioritised portfolio |

## Functional requirements

| ID | Requirement | Priority (MoSCoW) | Status in prototype |
|----|-------------|:--:|--------------------|
| FR-01 | Provide a web form to submit an opportunity | Must | Implemented (Form Trigger) |
| FR-02 | Capture title, business area, problem, current process, desired outcome, affected users, monthly volume, systems, data required and supporting evidence | Must | Implemented |
| FR-03 | Ask submitters to describe a problem, not propose an AI solution | Should | Implemented (form description) |
| FR-04 | Produce a structured first-pass assessment of each submission | Must | Implemented (Information Extractor) |
| FR-05 | Score six dimensions on a 1–5 scale | Must | Implemented |
| FR-06 | Combine the six scores into one weighted priority score | Must | Implemented (Code node) |
| FR-07 | Assign a priority band of High, Medium or Low | Must | Implemented |
| FR-08 | Route each opportunity to one of five outcomes by rule | Must | Implemented (Switch) |
| FR-09 | Check risk before value, so governance is never bypassed by a high score | Must | Implemented (rule order) |
| FR-10 | Set a branch-specific status for each outcome | Must | Implemented |
| FR-11 | Assign a branch-specific owning team and reviewer | Should | **Not implemented** — see [limitations](limitations-and-future-improvements.md) |
| FR-12 | Store the finished record in a portfolio table | Must | Implemented (Data Table) |
| FR-13 | Record the model's assumptions and governance concerns with each record | Should | Partially — produced by the assessment; not all fields are mapped into storage |
| FR-14 | Notify a named human when an item needs governance or pilot approval | Could | Not implemented (production) |
| FR-15 | Route pilot candidates to a named approver for sign-off | Could | Not implemented (production) |

## Data requirements

The intended portfolio record. Fields marked *target* are part of the data model but are not yet populated by the prototype.

| Field | Type | Source | Notes |
|-------|------|--------|-------|
| `opportunity_id` | string | Code node | `AIO-<timestamp>` |
| `submitted_at` | datetime | Code node | ISO timestamp |
| `title` | string | Form | |
| `business_area` | string | Form | |
| `problem` | string | Form | |
| `desired_outcome` | string | Form | |
| `strategic_alignment` | number (1–5) | AI assessment | |
| `expected_value` | number (1–5) | AI assessment | |
| `reach` | number (1–5) | AI assessment | |
| `evidence_strength` | number (1–5) | AI assessment | |
| `feasibility` | number (1–5) | AI assessment | |
| `risk` | number (1–5) | AI assessment | Higher = more risk |
| `priority_score` | number (1.0–5.0) | Code node | Weighted total |
| `priority` | string | Code node | High / Medium / Low |
| `decision` | string | Code node | One of five outcomes |
| `recommendation` | string | AI assessment | Single next action |
| `status` | string | Branch Set node | Branch-specific |
| `review_required` | boolean | *target* | Not yet populated |
| `assigned_team` | string | *target* | Not yet populated |
| `reviewer` | string | *target* | Not yet populated |

## Non-functional requirements

| ID | Requirement | Notes |
|----|-------------|-------|
| NFR-01 | Scores must be consistent for the same input | Model temperature set to 0.1 to reduce variance |
| NFR-02 | Scores must be clamped to the valid 1–5 range | Code node validates and clamps every score |
| NFR-03 | The model must not invent facts, costs, priorities or regulatory conclusions | Enforced in the system prompt |
| NFR-04 | Submissions must not leave the local environment in the prototype | Local model via Ollama |
| NFR-05 | The workflow must be understandable and maintainable by one analyst | Clear node names, single scoring code node |
| NFR-06 (production) | Access control, audit logging and data retention | Production only — see [production architecture](production-architecture.md) |

## Assumptions

- Submitters can describe their own problem clearly enough to assess.
- A small local model is adequate for a *first-pass* assessment that a human then reviews.
- The five routing outcomes cover the decisions the team needs at intake.

## Constraints

- Prototype only: free, locally run software.
- No real company, customer or learner data.
- No connection to live systems.
