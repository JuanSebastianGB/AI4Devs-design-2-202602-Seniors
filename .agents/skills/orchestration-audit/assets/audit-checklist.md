# Orchestration audit checklist

Use this list when scanning the repo. Mark each row **OK**, **GAP**, or **N/A**.

## A. Agent registry

| Expected `subagent_type` | File must exist |
| ------------------------ | --------------- |
| `product-owner` | `.cursor/agents/product-owner.md` |
| `backlog-manager` | `.cursor/agents/backlog-manager.md` |
| `sprint-planner` | `.cursor/agents/sprint-planner.md` |
| `requirements-analyst` | `.cursor/agents/requirements-analyst.md` |
| `data-architect` | `.cursor/agents/data-architect.md` |
| `system-architect` | `.cursor/agents/system-architect.md` |
| `adr-writer` | `.cursor/agents/adr-writer.md` |

Add rows for any **extra** agents under `.cursor/agents/*.md` that nothing references (orphan) or any **referenced** name missing a file.

## B. User stories / backlog pipeline (cross-file)

Files: `.cursor/rules/user-stories-pipeline.mdc`, `.cursor/rules/user-stories-standards.mdc`, `.agents/skills/generate-user-stories/SKILL.md`, optional `master-prompt-user-stories.md`.

`AGENTS.md` is **not** part of the contract in this repo (kept empty / untouchable). Do not require content there; validate **rule ↔ skill ↔ master prompt** only.

| Check | Detail |
| ----- | ------ |
| Task order | Same three agents in same order: `product-owner` → `backlog-manager` → `sprint-planner` |
| Forbidden inline prose | Rule + skill state coordinators must not write sections 1–3 without Task |
| Backlog formula | `Score = (Business Value + Urgency) / (Complexity + Risk)` if cited |
| Sprint input | `sprint-planner` receives top story + `docs/architecture.md` + `docs/data-model.md` |
| Output path | Default (`docs/agile/UserStories.md`) vs master-prompt/user override consistent across rule + skill + master prompt |
| Step numbering | Table “Order” columns may start at 0/1/2 — ensure **semantic** order matches, not only numbers |

## C. Definition artifacts pipeline

Files: `.cursor/rules/artifact-pipeline.mdc`, `.agents/skills/generate-artifacts/SKILL.md`.

| Check | Detail |
| ----- | ------ |
| Delegation map | Rows in skill **Artifact → Subagent** match `artifact-pipeline.mdc` |
| Sequence | Skill references reading `artifact-pipeline.mdc` before delegating |
| Doc paths | Output paths (`docs/...`) align with rule |

## D. Global orchestrator rules vs project overrides

Files: workspace `agent-teams-lite` / coordinator rules, `.cursor/rules/*.mdc` (e.g. `user-stories-pipeline.mdc`).

| Check | Detail |
| ----- | ------ |
| Override explicit | Where a **project rule** allows coordinator reads/writes for a pipeline (e.g. user-stories merge + file write), it must not be silently contradicted by another rule without a clear priority note |
| Task tool naming | Instructions use **Task** + `subagent_type` literally (Cursor contract) |

## E. Commands (optional)

Glob `.cursor/commands/*.md` if present. Each command should reference the skill or rule it implements and not duplicate conflicting steps.

## F. Skills index

| Check | Detail |
| ----- | ------ |
| `SKILL.md` frontmatter | `name` matches folder purpose; `description` + Trigger clear |
| Broken paths | No references to deleted files |
| Registry | If `.atl/skill-registry.md` exists, new skills are listed or note to run skill-registry skill |
