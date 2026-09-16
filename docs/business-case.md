# Business case

> Portfolio proof of concept. The company, figures and scenarios below are fictional and used to frame the design. Nothing here describes a real organisation.

## The situation

An EdTech company has a small AI and Innovation team. Interest in AI across the business is high, and ideas arrive constantly from product, engineering, learning content, support, operations, sales, marketing and other areas. The team cannot act on all of them, and it has no consistent way to decide which ones deserve attention.

## The problem

Three things make the current situation difficult.

**Ideas arrive in inconsistent shapes.** One person sends a paragraph, another a slide, another a quick message. The detail needed to assess an idea (the problem, who is affected, what evidence exists) is often missing, so the team spends time chasing basics before it can even compare two ideas.

**There is no shared basis for prioritisation.** Without a common yardstick, decisions drift towards whoever is most persistent or most senior. Good ideas with quiet sponsors get lost, and weak ideas with strong advocates get through.

**Risk is spotted too late.** Some ideas touch sensitive territory: children's data, biometrics, decisions that affect a learner's access to education. When these are assessed informally, the risk sometimes only becomes clear after a pilot has started, which is expensive and, in a safeguarding context, unacceptable.

## Who is affected

- The **AI and Innovation team**, who carry the cost of inconsistent intake and unclear priorities.
- **Idea submitters**, who have no clear way to put an idea forward or to know what happened to it.
- **Governance, privacy and safeguarding reviewers**, who are sometimes brought in too late.
- **Leadership**, who want a portfolio view but see a scattered list.

## What a good solution looks like

- A single, consistent front door for ideas that captures enough to assess them.
- The same assessment and scoring applied to every submission.
- Governance concerns flagged at intake, before any build begins.
- A human in control of every real decision.
- A portfolio record that supports review and prioritisation.

## Options considered

**Do nothing.** Keep handling ideas informally. Cheap now, but the inconsistency, weak prioritisation and late-surfacing risk all continue, and they get worse as AI interest grows.

**A form plus a spreadsheet.** A submission form feeding a shared spreadsheet would standardise intake and give a single list. It does not, on its own, produce an assessment or apply a consistent score, so the prioritisation and early-risk problems remain.

**A structured intake with an AI-assisted first-pass assessment (this prototype).** A form captures the idea, a language model produces a structured first-pass assessment, a transparent model scores it, and routing rules surface risk early. A human still makes every decision. This addresses all three problems while keeping people in control.

**A commercial innovation-management platform.** These exist and are capable, but they carry licensing cost and a procurement process, and they still need a scoring model and governance rules designed around them. For a first step, a lightweight prototype is a cheaper way to prove the approach before committing budget.

## Why the prototype approach

The prototype exists to prove the *process*, not to be the final tool. It shows that structured intake plus a transparent scoring and routing model can bring order to AI idea management, and it makes the governance thinking concrete. If the approach proves useful, the [production architecture](production-architecture.md) describes how it would be rebuilt on secured, supported components.

## Expected benefits

- **Consistency.** Every idea is captured and assessed the same way.
- **Fairer prioritisation.** A transparent score gives quiet-but-strong ideas a fair hearing.
- **Earlier risk visibility.** Privacy and safeguarding concerns are raised at intake.
- **A usable portfolio.** One place to see and prioritise the pipeline.
- **Time saved.** Less manual chasing and reformatting of half-formed ideas.

The benefits are described qualitatively on purpose. Putting invented numbers against them would work against the honesty this project is trying to demonstrate. In a real setting, the team would baseline the current effort and set measurable targets before claiming a return.

## Cost and effort

The prototype uses free, locally run software (n8n Community Edition and a local model), so its only real cost is build time. A production version would carry licensing, hosting, model usage and review costs, set out in the [production architecture](production-architecture.md).

## Risks of the solution itself

- **Over-trust in the score.** The main risk is that people treat an AI-generated score as fact. The design mitigates this by keeping the model advisory and requiring human decisions, but the cultural point matters as much as the technical one.
- **Model error.** A small model misjudges some submissions. Human review is the control.
- **Governance bypass.** If the risk rules are weakened, high-risk ideas could slip through. The rules are deliberately strict and checked before value.

## Recommendation

Use the prototype to demonstrate the intake, scoring and routing process to the AI and Innovation team and its governance stakeholders. If it proves useful, plan a production rebuild on the lines described in [production-architecture.md](production-architecture.md), starting with the privacy, safeguarding and security review that any real learner-facing AI process requires.
