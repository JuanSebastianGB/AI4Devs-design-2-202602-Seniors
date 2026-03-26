---
name: generate-artifacts
description: >
  Orchestrates full software definition artifact generation: Lean Canvas,
  use cases, data model, architecture diagram, C4 diagram, PRD, ADR.
  Use when the user asks to define or design a system from scratch,
  or to generate all definition artifacts in one pass.
---

# Generate Artifacts

This skill coordinates the complete software definition pipeline.

## When to use

- User asks to "define the system", "generate all artifacts", or
  "create the design documents"
- User provides a list of required artifacts to produce

## Instructions

1. Load project context from the user prompt (and any paths they name). Do not
   depend on `AGENTS.md` in this repo.
2. Read .cursor/rules/artifact-pipeline.mdc for pipeline sequence and delegation rules.
3. Read .cursor/rules/software-definition-standards.mdc for output format contracts.
4. For each artifact requested:
   - Delegate to the appropriate subagent as specified in artifact-pipeline.mdc.
   - Pass the full project context in the delegation prompt.
   - Confirm each file is saved before moving to the next artifact.
5. After all artifacts are generated, output a summary table:
   | Artifact | File | Status |
   with a one-line description of what was produced.

## Delegation map

| Artifact                       | Subagent             |
| ------------------------------ | -------------------- |
| PRD, user stories, Lean Canvas | requirements-analyst |
| Competitive analysis           | requirements-analyst |
| Use cases + PlantUML diagrams  | requirements-analyst |
| Data model + ER diagram        | data-architect       |
| Architecture diagram           | system-architect     |
| C4 diagram                     | system-architect     |
| ADR                            | adr-writer           |

**Contract alignment:** When the user prompt numbers artifacts (e.g. Artifact 0
for `docs/prd.md` plus Artifacts 1–5), the orchestrator must include every
numbered deliverable in: delegation lines, the stated total count, and the
final `| Artifact | File | Status |` table. PRD maps to **requirements-analyst**
(same subagent as Lean Canvas and use cases per the table above).
