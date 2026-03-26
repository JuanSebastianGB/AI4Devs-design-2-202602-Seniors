# Orchestration audit report

**Date:** 2026-03-25  
**Scope:** `.cursor/agents/`, `.cursor/rules/user-stories-pipeline.mdc`, `.cursor/rules/artifact-pipeline.mdc`, `.agents/skills/orchestration-audit/`, `.agents/skills/orchestration-remediate/`, `.agents/skills/generate-user-stories/`, `.agents/skills/generate-artifacts/`, `master-prompt-user-stories.md`, `AGENTS.md` (empty by policy)

## Summary

- **OK:** Contract cross-checks after remediation (rule ↔ skill; no `AGENTS.md` dependency).
- **Warnings addressed:** F1 (checklist + audit skill), F2 (`generate-artifacts` step 1).
- **Info:** F4 — example path in `artifact-pipeline.mdc` generalized to `docs/domain-brief.md`.

## Remediation log

| Target | Change |
| ------ | ------ |
| `.agents/skills/orchestration-audit/assets/audit-checklist.md` | Removed `AGENTS.md` from required user-stories file set; §D uses project rules for override checks. |
| `.agents/skills/orchestration-audit/SKILL.md` | Frontmatter, step 5 optional `AGENTS.md`, step 7 cross-validate without `AGENTS.md` trio. |
| `.agents/skills/orchestration-remediate/SKILL.md` | Dropped `AGENTS.md` as sync target; preserve-intent bullet points at rules/skills. |
| `.agents/skills/generate-artifacts/SKILL.md` | Context from user prompt only; no `AGENTS.md` dependency. |
| `.cursor/rules/artifact-pipeline.mdc` | Neutral example path for overview override. |

## Checked agents (registry)

All expected `subagent_type` values have matching `.cursor/agents/{type}.md`:

`product-owner`, `backlog-manager`, `sprint-planner`, `requirements-analyst`, `data-architect`, `system-architect`, `adr-writer`.

## Follow-ups

- [ ] Re-run a full audit after adding agents, rules, or pipeline skills.
- [ ] Course-specific paths (e.g. `LTI-JSGB/`, `docs/lti-overview.md`) remain valid via `master-prompt-user-stories.md` and user prompts; they are not required by the orchestration contract files.
