---
name: generate-user-stories
description: >
  Orchestrates the full agile artifact pipeline: user stories from existing
  project docs, prioritized product backlog, technical work tickets, and
  effort estimation. Use when the user asks to generate user stories,
  build a backlog, plan a sprint, or produce UserStories-JSGB.md.
---

# Generate User Stories

Coordinates the complete user stories and sprint planning pipeline.

## Instructions

1. Read existing project documentation from docs/ as source of truth.
   Read in this order:
   - docs/lti-overview.md
   - docs/prd.md
   - docs/use-cases.md
   - docs/data-model.md
   - docs/architecture.md
     Do not invent requirements not present in these files.

2. Apply user-stories-pipeline.mdc for sequence and delegation rules.

3. Apply user-stories-standards.mdc for all output format contracts.

4. Execute the pipeline in this exact order:

   SECTION 1 — User Stories
   Delegate to product-owner subagent.
   Pass full project context extracted from docs/.
   Generate a minimum of 8 user stories.
   Use the user story template from user-stories-standards.mdc exactly.
   Evaluate every story against INVEST. Rewrite any that fail.

   SECTION 2 — Product Backlog
   Delegate to backlog-manager subagent.
   Pass the full list of user stories from Section 1.
   Apply Value vs Complexity scoring methodology.
   Produce the prioritized backlog table.
   Draw a clear MVP line.
   Justify the top 3 prioritized stories.

   SECTION 3 — Work Tickets
   Delegate to sprint-planner subagent.
   The user must specify which user story to break down.
   If not specified, use the highest-priority story from Section 2.
   Generate a minimum of 5 work tickets covering all technical layers.
   Use the work ticket template from user-stories-standards.mdc exactly.
   Order tickets by dependency (blockers first).

   SECTION 4 — Effort Estimation
   Handled by sprint-planner subagent as part of Section 3.
   Produce the effort estimation summary table.
   Include Fibonacci story points, T-shirt size, and confidence level.
   Add total story points and sprint allocation recommendation.

5. Write all output to a single file: LTI-JSGB/UserStories-JSGB.md
   Create the LTI-JSGB/ folder if it does not exist.
   Structure the file with these top-level sections:

   # 1. User Stories

   # 2. Product Backlog

   # 3. Work Tickets

   # 4. Effort Estimation

6. When all sections are complete, output a summary table:

| Section           | Content                               | Status |
| ----------------- | ------------------------------------- | ------ |
| User Stories      | N stories generated                   | Done   |
| Product Backlog   | N stories prioritized, MVP line drawn | Done   |
| Work Tickets      | N tickets for US-NNN                  | Done   |
| Effort Estimation | Total: N story points                 | Done   |

## Delegation map

| Task                       | Subagent        |
| -------------------------- | --------------- |
| Generate user stories      | product-owner   |
| Prioritize product backlog | backlog-manager |
| Generate work tickets      | sprint-planner  |
| Estimate effort            | sprint-planner  |

## Output rules

- Single output file: LTI-JSGB/UserStories-JSGB.md
- All content in English.
- No placeholders. Every field in every template must be populated.
- Flag assumptions with [ASSUMPTION: ...] and continue.
- Complete all 4 sections in one pass without stopping.
