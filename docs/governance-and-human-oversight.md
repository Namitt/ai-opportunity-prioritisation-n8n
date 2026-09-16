# Governance and human oversight

This project treats the language model as a fallible assistant and builds the controls around that assumption. The model helps a Business Analyst assess an idea quickly and consistently. It does not decide anything.

## The core principle

**The AI recommends. A human decides.** Every routing outcome leads to a human step, never to an automatic go-ahead:

| Outcome | The human step that follows |
|---------|----------------------------|
| Governance review required | Privacy, safeguarding, security, legal and responsible-AI review before anything proceeds |
| More discovery required | An analyst gathers the missing evidence |
| Candidate for pilot | A named approver decides whether to run a human-supervised pilot |
| Proceed to discovery | A discovery phase with defined questions and stakeholders |
| Innovation backlog | Periodic review of the backlog by the team |

The workflow stops at "here is the suggested next step and why". Nothing is approved, procured, deployed or made customer-facing by the automation itself.

## Governance is checked before value

The routing rules check risk first. A high priority score cannot buy a shortcut past review. If risk is 4 or 5, the opportunity goes to governance regardless of how valuable it looks. This is the single most important control in the design, because the failure it prevents (an attractive but risky idea slipping into a pilot) is exactly the failure the business case is worried about.

## Hard risk floors

The model is not left to decide for itself whether something is high-risk. The system prompt forces the issue:

- **Risk 5** is mandatory for facial recognition, facial images, biometric identification, webcam surveillance of children, automated cheating allegations, or automatically blocking learners from assessments or services.
- **Risk of at least 4** is mandatory when children's personal data is processed, high-impact decisions are made, or access to education could be materially affected.
- **Risk of at least 3** applies to customer-facing generative AI that could provide inaccurate or misleading information.

Because these floors push risk to 4 or 5, they also force the routing to governance. The safeguarding-sensitive cases that matter most in EdTech are handled by rule, not by the model's discretion.

## Guardrails against fabrication and inflated confidence

The system prompt instructs the model to:

- not invent facts, evidence, strategic priorities, costs, user numbers, technical capabilities or regulatory conclusions;
- base every score only on what is in the submission;
- treat unsupported claims as assumptions, not facts;
- reflect missing information as **lower evidence strength and explicit assumptions**, not as inflated risk;
- return exactly one recommended next action, and never recommend immediate full implementation.

It is also told that human review is required before any pilot, procurement, deployment or customer-facing release, and that human oversight does not remove underlying privacy, safeguarding, bias, accessibility or legal risk.

## Transparency

Each assessment carries the model's `assumptions` and `governance_concerns` alongside the scores, so a reviewer can see the reasoning and challenge it, rather than being handed a number with no context.

## Data handling in the prototype

In the prototype, submissions are assessed by a model running locally through Ollama, so nothing is sent to an external service. Only fictional data is used. This is a property of the local setup, not a guarantee that would hold in production — a production version using a hosted model would need a data-processing agreement and the privacy controls described in the [production architecture](production-architecture.md).

## Where the prototype's oversight is incomplete

Honesty matters more than a tidy story here:

- **No approval loop.** "Pilot candidate — approval pending" is a status, not a routed request to a named approver. A production version would route the item to a person and wait.
- **No notifications.** Nobody is alerted when a high-risk item reaches governance. In production, this would trigger a Microsoft Teams message to the governance group.
- **No named reviewers or owners yet.** The `assigned_team` and `reviewer` fields are defined but not populated. Accountability should be explicit, and wiring these is a defined next step.
- **Model limits.** A 3-billion-parameter local model is adequate for a first-pass assessment that a human reviews, and no more than that.

## Responsible-AI checklist for a production version

Before any real learner-facing use, the following would need to be completed and evidenced:

- Data protection impact assessment, with particular attention to children's data.
- Safeguarding review for anything affecting learners.
- Bias and fairness assessment of any model used for scoring or decisions.
- Accessibility review of the intake form and any learner-facing output.
- Security review, including credential management and access control.
- Legal review of any automated decision that affects a person.
- A named human accountable for each high-risk decision.
- Clear records of what the AI recommended and what the human decided.

None of these are claimed to be complete in this repository. They are the work that would sit between this prototype and any real deployment.
