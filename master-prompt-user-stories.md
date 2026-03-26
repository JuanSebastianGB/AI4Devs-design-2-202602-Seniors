/generate-user-stories

# Master prompt: user stories pipeline (agentic)

**Trigger:** Paste this file into the chat, or invoke skill `generate-user-stories`.

**Goal:** One coordinator turn that runs **three** `Task` subagents **in sequence**, then writes **one** markdown file with **four** sections. No inline generation of stories, backlog, or tickets by the coordinator.

---

## 1. Coordinator role (non-negotiable)

| Rule | Detail |
| ---- | ------ |
| Execution | Call **Task** with `subagent_type` in order; **wait** for each result before the next. |
| Forbidden | Writing Section 1–3 body content yourself without those Task runs. |
| Allowed | Merging subagent outputs, writing the final file, short reply + summary table. |
| Templates | Follow `.cursor/rules/user-stories-standards.mdc` for story, backlog table, ticket, and estimation formats. |
| Language | All artifact text in **English**. Mark gaps with `[ASSUMPTION: ...]`. |
| Truth | Do not invent requirements; only use what appears in the source files below (when they exist). |

---

## 2. Source documentation (read order)

**Coordinator:** Pass these paths into Task prompts (subagents read with repo tools). **Skip** paths that do not exist.

| Priority | Path |
| -------- | ---- |
| 1 | `docs/prd.md` |
| 2 | `docs/lti-overview.md` |
| 3 | `docs/use-cases.md` |
| 4 | `docs/data-model.md` |
| 5 | `docs/architecture.md` |

If a file is missing, subagents proceed with remaining sources only.

---

## 3. Task sequence (copy-shaped prompts)

**Placeholder:** In the three prompt bodies below, replace `{workspace-folder-name}` with this repository’s root folder name (Engram / mem project key).

### Task 1 — `product-owner`

**subagent_type:** `product-owner`

**Prompt body (adapt paths only if needed):**

```text
SKILL LOADING (do this FIRST):
Check for available skills: mem_search(query: "skill-registry", project: "{workspace-folder-name}")
Fallback: read .atl/skill-registry.md — load skills that match this task.

You are the product-owner subagent. Read these paths if they exist (repo root), in order:
docs/prd.md, docs/lti-overview.md, docs/use-cases.md, docs/data-model.md, docs/architecture.md

Output ONLY Section 1 material: user stories using the exact template in .cursor/rules/user-stories-standards.mdc.
Minimum 8 stories. English. [ASSUMPTION: ...] where the docs are silent. Do not output backlog or tickets.
If you discover something worth persisting for the project, mem_save to engram with project: "{workspace-folder-name}".
```

**Coordinator:** Store the returned markdown as **STORIES** for Task 2.

---

### Task 2 — `backlog-manager`

**subagent_type:** `backlog-manager`

**Prompt body:**

```text
SKILL LOADING (do this FIRST):
Check for available skills: mem_search(query: "skill-registry", project: "{workspace-folder-name}")
Fallback: read .atl/skill-registry.md — load skills that match this task.

You are the backlog-manager subagent. Below is the complete user stories markdown from the product-owner (verbatim). Do not re-derive requirements from other files unless needed to resolve an ID/title ambiguity.

--- USER STORIES (verbatim) ---
[PASTE FULL STORIES OUTPUT FROM TASK 1 HERE]
---

**Large outputs:** If pasting would exceed limits, write Task 1 output to e.g. `LTI-JSGB/_verbatim-stories.md` and replace the block above with: “Read `LTI-JSGB/_verbatim-stories.md` for verbatim stories.”

Output ONLY Section 2: product backlog table per user-stories-standards.mdc.
Score = (Business Value + Urgency) / (Complexity + Risk); sort descending.
Include MVP line and justify top 3 picks. English.
mem_save important decisions to engram with project: "{workspace-folder-name}" if applicable.
```

**Coordinator:** From this output, identify the **single highest-priority** story (first row after sort): full **US-xxx** block from **STORIES**. Keep **BACKLOG** markdown for the file.

---

### Task 3 — `sprint-planner`

**subagent_type:** `sprint-planner`

**Prompt body:**

```text
SKILL LOADING (do this FIRST):
Check for available skills: mem_search(query: "skill-registry", project: "{workspace-folder-name}")
Fallback: read .atl/skill-registry.md — load skills that match this task.

You are the sprint-planner subagent.

Priority user story (full block from product-owner output — must match backlog #1):
[PASTE FULL BLOCK FOR TOP-PRIORITY US-xxx HERE]

Read for technical context: docs/architecture.md, docs/data-model.md (skip if missing).

Output ONLY Sections 3–4: work tickets (minimum 5) + effort summary table per user-stories-standards.mdc. English.
Number tickets **TICKET-001**, **TICKET-002**, … in order; **Dependencies** must reference those ids.
mem_save important technical choices to engram with project: "{workspace-folder-name}" if applicable.
```

**Coordinator:** Store result as **TICKETS_AND_ESTIMATION**.

---

## 4. Required output file

| Field | Value |
| ----- | ----- |
| Path | `LTI-JSGB/UserStories-iniciales.md` |
| Note | Course delivery path. If this prompt is not used, default is `docs/agile/UserStories.md` (see `user-stories-pipeline.mdc`). |

**Create parent directories** if needed.

**File structure:**

```markdown
# 1. User Stories

[Paste STORIES from Task 1]

# 2. Product Backlog

[Paste BACKLOG from Task 2]

# 3. Work Tickets

[Paste ticket section from Task 3]

# 4. Effort Estimation

[Paste estimation table from Task 3]
```

Complete all sections in **one** write. Do not stop between sections.

---

## 5. Coordinator closing summary

After writing the file, reply with a short table:

| Section | Content | Status |
| ------- | ------- | ------ |
| User Stories | N stories | Done |
| Product Backlog | N rows, MVP noted | Done |
| Work Tickets | N tickets for [top story id] | Done |
| Effort Estimation | Total SP | Done |

---

## 6. Inline paste fallback (only if subagents cannot read the repo)

If policy blocks file access: coordinator reads each source file **once**, then in Task 1 paste under `--- SOURCE DOCUMENTS ---` instead of listing paths. Tasks 2–3 unchanged (still paste STORIES and top story). Prefer path-based delegation whenever possible.
