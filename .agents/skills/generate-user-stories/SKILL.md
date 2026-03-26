---
name: generate-user-stories
description: >
  Orchestrates the full agile artifact pipeline: user stories from existing
  project docs, prioritized product backlog, technical work tickets, and
  effort estimation. Use when the user asks to generate user stories,
  build a backlog, plan a sprint, or produce a combined agile markdown file.
---

# Generate User Stories

## When to Use

- User asks to generate user stories, build a product backlog, or plan a sprint.
- User asks for a single markdown deliverable (path may be set in a master prompt).

## Instructions

Follow this exact sequence. Complete all steps in one pass.

### Orchestration contract (mandatory — do not skip)

The model handling this skill is a **coordinator**. It **must** run the
pipeline by invoking **three separate subagent runs** via the **Task** tool
(Cursor), in order:

| Step | `subagent_type` (Task) | Purpose |
| ---- | ------------------------ | ------- |
| 2 | `product-owner` | User stories only (Section 1 body) |
| 3 | `backlog-manager` | Prioritized backlog table + MVP notes (Section 2 body) |
| 4 | `sprint-planner` | Work tickets + effort table (Sections 3–4 body) |

**Hard rules**

1. **Forbidden:** Writing user stories, backlog rows, or work tickets inline
   in the coordinator’s own turn **without** having run the matching Task
   subagent above. Merging subagent outputs into the final file **is** allowed.
2. **Order:** Run Task **sequentially** and **wait** for each subagent to
   finish before starting the next (backlog needs stories; sprint-planner
   needs the top story id/title + technical docs).
3. **Context for subagents (pick one per step, prefer A):**
   - **A — File paths (preferred):** Subagents read the repo with their own
     tools. The coordinator passes **explicit paths** and instructions to read
     them first (see steps below).
   - **B — Inline paste:** If the user or policy requires it, the coordinator
     reads the files once and pastes full content under a
     `--- SOURCE DOCUMENTS ---` header in the Task prompt (for subagents
     without repo access).

**Why this exists:** Without naming `Task` + `subagent_type`, coordinators
often collapse the whole pipeline into one response and **never** spin up
`product-owner`, `backlog-manager`, or `sprint-planner`.

### Step 1 — Source documentation paths (coordinator checklist)

These paths are the usual **source of truth** under `docs/`. Subagents must not
invent requirements outside files that exist and are listed here or in the Task prompt.

**Core (read each path that exists):**

- docs/prd.md
- docs/use-cases.md
- docs/data-model.md
- docs/architecture.md

**Additional:** Include any extra paths from the user request or
`master-prompt-user-stories.md` (e.g. product overview, domain brief). Skip
paths that do not exist.

The coordinator **does not** need to load full file contents into its own
context if using orchestration pattern **A**; it only passes these paths into
Task prompts.

### Step 2 — Generate User Stories

**Task** → `subagent_type: product-owner`

Prompt must include:

- The source paths from Step 1 (or pasted content under
  `--- SOURCE DOCUMENTS ---`).
- Instruction: output **only** the user-stories markdown (minimum 8
  stories), using the template in `user-stories-standards.mdc`, English,
  `[ASSUMPTION: ...]` where needed.

Save the subagent return as the raw input for Step 3.

### Step 3 — Build Product Backlog

**Task** → `subagent_type: backlog-manager`

Prompt must include:

- The **complete** user stories markdown from Step 2 (verbatim).
- Instruction: apply **Score = (Business Value + Urgency) / (Complexity + Risk)**,
  table format from `user-stories-standards.mdc`, sort by score descending,
  MVP line, top-3 justification.

### Step 4 — Generate Work Tickets

**Task** → `subagent_type: sprint-planner`

Prompt must include:

- The **single highest-priority user story** (full story block from Step 2,
  as identified from Step 3 ordering — include **US-xxx** id).
- Paths: `docs/architecture.md` and `docs/data-model.md` (or pasted excerpts).
- Instruction: minimum **5** work tickets, template from
  `user-stories-standards.mdc`, effort summary table, English.
  **Ticket ids:** `TICKET-001`, `TICKET-002`, … (sequential, three-digit) for
  this story; dependencies must use those ids.

### Step 5 — Write output file

Collect all results from Steps 2, 3, and 4.
Write everything to the path from the user request or
`master-prompt-user-stories.md` if either names one; otherwise use the portable
default **`docs/agile/UserStories.md`** (same as `user-stories-pipeline.mdc`).
Create parent directories for that path if they do not exist.
Structure the file with these sections:

# 1. User Stories

# 2. Product Backlog

# 3. Work Tickets

# 4. Effort Estimation

### Step 6 — Output summary table

| Section           | Content                               | Status |
| ----------------- | ------------------------------------- | ------ |
| User Stories      | N stories generated                   | Done   |
| Product Backlog   | N stories prioritized, MVP line drawn | Done   |
| Work Tickets      | N tickets for [story title]           | Done   |
| Effort Estimation | Total: N story points                 | Done   |
