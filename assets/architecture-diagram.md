# Architecture diagram and screenshot guide

## Workflow architecture (Mermaid)

```mermaid
flowchart TD
    A[Opportunity Intake<br/>n8n Form Trigger] --> B[Generate BA Assessment<br/>Information Extractor]
    LLM[Ollama Chat Model<br/>llama3.2:3b, temp 0.1] -.provides model.-> B
    B --> C[Assemble Portfolio Record<br/>Edit Fields]
    C --> D[Calculate Priority<br/>Code node: weighted score + rules]
    D --> E{Route Decision<br/>Switch on decision}

    E -->|Governance review required| F1[Set status:<br/>Awaiting governance review]
    E -->|More discovery required| F2[Set status:<br/>Discovery required]
    E -->|Candidate for pilot| F3[Set status:<br/>Pilot candidate — approval pending]
    E -->|Proceed to discovery| F4[Set status:<br/>Ready for discovery]
    E -->|Fallback: backlog| F5[Set status:<br/>Innovation backlog]

    F1 --> G[(Insert row<br/>ai_opportunity_portfolio)]
    F2 --> G
    F3 --> G
    F4 --> G
    F5 --> G
```

## Routing logic (Mermaid)

```mermaid
flowchart TD
    S[Assessment scores] --> R1{"risk ≥ 4?"}
    R1 -->|Yes| G[Governance review required]
    R1 -->|No| R2{"evidence strength ≤ 2?"}
    R2 -->|Yes| M[More discovery required]
    R2 -->|No| R3{"priority score ≥ 4.0?"}
    R3 -->|Yes| P[Candidate for pilot]
    R3 -->|No| R4{"priority score ≥ 3.0?"}
    R4 -->|Yes| D[Proceed to discovery]
    R4 -->|No| B[Innovation backlog]
```

## Screenshots

Two screenshots are included (`workflow-canvas.png` and `intake-form.png`). Two remain. Screenshots are never fabricated. To complete the visuals, capture the remaining images below and save them in this `assets/` folder, then update the placeholders in the [README](../README.md#19-screenshots).

| # | Screenshot | Save as | What to capture |
|---|------------|---------|-----------------|
| 1 | Workflow canvas | `assets/workflow-canvas.png` | The whole workflow open in the n8n editor, showing all nodes and the five branches |
| 2 | Opportunity intake form | `assets/intake-form.png` | The rendered intake form a colleague fills in |
| 3 | Data Table results | `assets/portfolio-results.png` | Rows in `ai_opportunity_portfolio`, showing `decision`, `priority_score` and `status` |
| 4 | Governance routing | `assets/governance-routing.png` | A high-risk submission (for example TC-02) landing at "Awaiting governance review" |

### Before you capture

Please check that none of the following are visible in any screenshot:

- API keys, tokens, passwords or credential values
- Email addresses or account names
- Your local username or machine name in a path or title bar
- n8n credential details
- Any unrelated browser tabs, bookmarks or notifications
- Any real company, customer or learner information

Crop or blur anything sensitive before saving. Once saved, replace the placeholder lines in the README's Screenshots section with standard image links, for example:

```markdown
![Workflow canvas](assets/workflow-canvas.png)
```
