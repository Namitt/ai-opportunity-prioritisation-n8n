# Workflow

This folder contains the exported n8n workflow for the AI Opportunity Discovery and Prioritisation prototype.

- **File:** [`ai-opportunity-prioritisation.json`](ai-opportunity-prioritisation.json)
- **Built with:** n8n Community Edition (local Docker)
- **Model:** Ollama running `llama3.2:3b`

## What was sanitised

This is a **sanitised** copy of the export, safe to publish. The following were removed or cleared, and none affect the logic:

- The **Ollama credential reference** on the Ollama Chat Model node (you select your own credential on import).
- The **Data Table reference** (`dataTableId` value and cached URL) on the Insert row node, so no project-specific identifier is published. The expected table name (`ai_opportunity_portfolio`) and the full column schema are kept.
- The **webhook ID** on the form trigger (n8n regenerates this on import).
- The n8n **instance ID**, and the workflow's own **id** and **versionId**.

No API keys, passwords or tokens were present in the export. The scoring code, the assessment system prompt, the routing rules and all field mappings are unchanged.

## After importing

You will need to:

1. Select your **Ollama credential** on the Ollama Chat Model node.
2. Select your **`ai_opportunity_portfolio` data table** on the Insert row node.

See the [main README](../README.md#13-how-to-import-and-run-the-workflow) for the full import and run steps.

## Data table schema

Create a data table named `ai_opportunity_portfolio` with these columns. Types match the workflow's Insert row mapping.

| Column | Type |
|--------|------|
| `opportunity_id` | string |
| `submitted_at` | dateTime |
| `title` | string |
| `business_area` | string |
| `problem` | string |
| `desired_outcome` | string |
| `strategic_alignment` | number |
| `expected_value` | number |
| `reach` | number |
| `evidence_strength` | number |
| `feasibility` | number |
| `risk` | number |
| `priority_score` | number |
| `priority` | string |
| `decision` | string |
| `recommendation` | string |
| `status` | string |

The `assigned_team`, `reviewer` and `review_required` fields are part of the intended data model but are **not** populated by the current prototype. See [docs/limitations-and-future-improvements.md](../docs/limitations-and-future-improvements.md).

## Nodes

| Node | Type |
|------|------|
| Opportunity Intake | Form Trigger |
| Generate BA Assessment | Information Extractor (LangChain) |
| Ollama Chat Model | Ollama Chat Model (LangChain) |
| Assemble Portfolio Record | Edit Fields (Set) |
| Calculate Priority | Code |
| Route Decision | Switch |
| Set Governance Status1 | Edit Fields (Set) |
| Set Discovery Status | Edit Fields (Set) |
| Set Pilot Status | Edit Fields (Set) |
| Set Proceed Status | Edit Fields (Set) |
| Set Backlog Status | Edit Fields (Set) |
| Insert row | Data Table |
