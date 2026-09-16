# Proposed production architecture

This describes how the local prototype would be rebuilt if a company decided to adopt it. It is a **proposed** design. **Nothing in this repository shows that any of it was implemented.** The external systems named below (Azure, Jira, Microsoft Teams and so on) are examples of what a production version would use; they are not connected to this project.

## Prototype versus production at a glance

| Concern | Prototype (this repo) | Production (proposed) |
|---------|-----------------------|-----------------------|
| Hosting | n8n Community Edition, local Docker | Secured n8n Cloud or managed self-hosting |
| Environments | One local instance | Development, test and production |
| Model | Local `llama3.2:3b` via Ollama | Approved enterprise model: Azure OpenAI, OpenAI or AWS Bedrock, under a data-processing agreement |
| Storage | n8n Data Table | PostgreSQL, Jira, Azure DevOps or an enterprise portfolio platform |
| Notifications | None | Microsoft Teams alerts to the right group |
| Approvals | Status field only | Named human approvers with a routed sign-off step |
| Access | Local, single user | Role-based access and single sign-on |
| Credentials | Local credential | Secure secret management |
| Oversight | Manual, informal | Monitoring, error handling, backups, audit logs |
| Data lifecycle | None | Defined retention and deletion |
| Review | None enforced | Responsible-AI, privacy, safeguarding, security and accessibility review |

## Target design

### Intake

Keep the form, but place it behind single sign-on so submissions are attributable and the business area can be pre-filled from the submitter's profile. Validate inputs and guide submitters to describe a problem rather than a solution.

### Assessment

Replace the local model with an approved enterprise model accessed under a data-processing agreement. Keep the same structured system prompt and output schema, so the assessment logic is portable. Log the model version and the prompt version with each assessment for traceability. Where children's data or high-risk categories are involved, consider withholding the raw submission from any external model and routing straight to human review.

### Scoring and routing

The scoring and routing logic is deliberately deterministic and lives in a single code step, so it moves to production unchanged. Version the weights and thresholds, and review them periodically with the AI and Innovation team so the model of "what matters" stays current.

### Storage

Move the portfolio to a proper store. Options, depending on how the team already works:

- **PostgreSQL** for a dedicated, queryable portfolio database.
- **Jira or Azure DevOps** if the team wants each opportunity to become a tracked work item with its own workflow.
- **An enterprise portfolio or innovation-management platform** if one is already in use.

### Notifications and approvals

- Send a Microsoft Teams message to the governance group when an item is routed to "Governance review required".
- Route "Candidate for pilot" to a named approver and wait for an explicit decision before the status can change.
- Record who approved what, and when.

### Environments and release

Run development, test and production instances. Promote changes through them with review, rather than editing a live workflow. Keep the workflow definition in version control.

### Security and access

- Single sign-on and role-based access, so only the right people can see submissions, change scoring rules or approve pilots.
- Secure secret management for model and system credentials, never stored in the workflow export.
- Least-privilege connections to any integrated system.

### Reliability and operations

- Monitoring and alerting on failures.
- Error handling and retries on the assessment and storage steps.
- Regular backups of the portfolio store.
- Audit logs covering submissions, assessments, decisions and approvals.

### Data protection

- A defined retention period for submissions and assessments, with automatic deletion afterwards.
- Data minimisation in what is stored.
- Handling of children's data assessed specifically, given the EdTech context.

## Governance gate before any real use

Before a production version handled real learner-facing ideas, it would need the reviews listed in [governance-and-human-oversight.md](governance-and-human-oversight.md): data protection impact assessment, safeguarding review, bias and fairness assessment, accessibility review, security review and legal review of any automated decision. These are the work between this prototype and a deployment, and none of them are claimed to be complete here.

## What would carry over unchanged

The genuinely reusable parts of this prototype are the analysis and the rules, not the plumbing: the intake structure, the assessment schema and system prompt, the weighted scoring model, and the governance-before-value routing. Those are the parts worth keeping. The hosting, model, storage and integrations would all be replaced with secured, supported equivalents.
