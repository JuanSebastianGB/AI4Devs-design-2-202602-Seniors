---
name: orchestration-audit
description: >
  Audits Cursor orchestration glue: .cursor/rules, .cursor/agents, .agents/skills,
  and optional commands. Produces a structured findings report. (AGENTS.md is not
  a contract surface in this project when left empty.)
  Trigger: audit pipelines, verify Task/subagent alignment, pre-flight before
  teaching orchestrators, or user says orchestration audit, auditar agentes,
  verificar skills y rules.
---

# Orchestration audit

## When to use

- Before or after changing any pipeline skill, rule, or agent definition.
- When coordinators skip `Task` / subagents or outputs drift from contracts.
- When adding a new artifact pipeline or subagent type.

## Optional: Sequential Thinking (MCP)

For large repos or unclear scope, run **sequential-thinking** (`user-sequential-thinking` MCP) first: frame goals, list glob targets, and decide audit depth. Not required for a quick pass on this project layout.

## Instructions

1. **Inventory agents**  
   List `.cursor/agents/*.md` → derive allowed `subagent_type` values (stem of filename).

2. **Load checklist**  
   Read [assets/audit-checklist.md](assets/audit-checklist.md) and adapt rows if the project added custom agents or pipelines.

3. **Scan rules**  
   Read `.cursor/rules/*.mdc` (at minimum: `user-stories-pipeline.mdc`, `artifact-pipeline.mdc`, and any always-on orchestrator rules). Extract:
   - Tables mapping **Task** / `subagent_type` → responsibility
   - Output paths, formulas, forbidden behaviors

4. **Scan skills**  
   Read `.agents/skills/**/SKILL.md` (and any sibling project skill roots). Extract:
   - Delegation tables vs rules
   - Step order and prompts contract
   - References to `docs/` or output files

5. **AGENTS.md (optional)**  
   If the project uses a non-empty `AGENTS.md`, confirm it does not contradict rules/skills. If empty or absent, **skip** — contract is rule + skill (+ master prompt for user-stories overrides).

6. **Scan commands (if any)**  
   Glob `.cursor/commands/**/*.md`. Check they point to the correct skill/rule and do not contradict delegation order.

7. **Cross-validate**  
   - Every `subagent_type` referenced in markdown exists as `.cursor/agents/{type}.md`.  
   - Order of agents is identical across **rule and skill** (and `master-prompt-user-stories.md` when present); ignore off-by-one in “Order” column if semantics match.  
   - `generate-artifacts` map matches `artifact-pipeline.mdc`.  
   - User-stories output path: portable default `docs/agile/UserStories.md` consistent with `master-prompt-user-stories.md` / user override when present.

8. **Emit report** (required format)

```text
# Orchestration audit report
Date: ISO-8601
Scope: [paths scanned]

## Summary
- OK: N | Warnings: N | Errors: N

## Findings
| ID | Severity (error/warning/info) | File | Finding | Evidence (quote or line ref) | Suggested remediation |
|----|----------------------------------|------|---------|------------------------------|------------------------|

## Checked agents
[list with OK / missing / orphan]

## Follow-ups
- [ ] …
```

**Severity:** **error** = broken reference or contradictory contract; **warning** = drift or ambiguity; **info** = optional hardening.

## Output

- Return the report in chat **and**, if the user wants a paper trail, write to `docs/orchestration-audit-report.md` or path they specify.

## Resources

- [assets/audit-checklist.md](assets/audit-checklist.md) — row-by-row checklist
