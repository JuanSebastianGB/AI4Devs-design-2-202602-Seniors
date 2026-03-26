---
name: orchestration-remediate
description: >
  Applies minimal edits to align rules, agents, and skills after an
  orchestration-audit. Trigger: fix pipeline contract drift, user says remediate
  orchestration, reparar skills/agentes/rules, apply audit findings.
---

# Orchestration remediate

## When to use

- After **orchestration-audit** (or equivalent manual findings).
- When the user authorizes **concrete edits** to fix Task order, broken paths, or mismatched delegation tables.

## Preconditions

1. Have a **findings table** (from `orchestration-audit` or pasted by user).  
2. **User must confirm** apply: e.g. “aplicá los fixes”, “apply HIGH and MEDIUM only”, or “dry-run only”.  
   - If no confirmation → output proposed patches as unified diff or bullet list **without** writing files.

## Hard rules

- **Minimal diff:** touch only files required by each finding; no drive-by rewrites.
- **Single source of truth:** Prefer updating the **skill** OR the **rule** and then mirroring the other—never leave them diverged.
- **Preserve intent:** Pipeline-specific coordinator allowances (e.g. merge + write for user stories) live in `.cursor/rules` and skills — mirror those in paired files instead of inventing new surfaces. This repo keeps `AGENTS.md` empty; do not use it as a sync target.
- **No noise in artifact outputs:** Do not add “stale/regenerated” banners to user-facing markdown artifacts unless the user asks.

## Instructions

1. **Parse findings**  
   Group by target file. Sort by severity: error → warning → info. Drop **info** unless the user included them in scope.

2. **Plan edits**  
   For each finding:
   - **Missing agent file:** create minimal `.cursor/agents/{name}.md` from a sibling agent as template (role, constraints, skill loading line) **or** remove dead references from rules/skills—choose the option the user confirmed.
   - **Task order mismatch:** sync the markdown table across `user-stories-pipeline.mdc` and `generate-user-stories/SKILL.md` (and `master-prompt-user-stories.md` if it duplicates the sequence).
   - **Wrong output path:** align portable default in `user-stories-pipeline.mdc` +
     `generate-user-stories/SKILL.md` (currently `docs/agile/UserStories.md`); keep
     course- or project-specific paths **only** in master prompts or explicit user requests.
   - **generate-artifacts drift:** sync `.agents/skills/generate-artifacts/SKILL.md` delegation table with `artifact-pipeline.mdc`.
   - **Broken path:** fix or remove the reference.

3. **Apply**  
   Edit files. One commit-worthy logical change per finding where possible.

4. **Verify**  
   Re-run greps:
   - `subagent_type` / backtick agent names vs `ls .cursor/agents`
   - Duplicate conflicting sentences between rule and skill for the same step

5. **Report**  
   Return a short **remediation log**:

```text
# Orchestration remediation log
- [file] — [1-line what changed] (Finding ID)
…
Re-audit: recommended (run orchestration-audit)
```

## Optional: Sequential Thinking (MCP)

Use **sequential-thinking** when many findings interact (e.g. renaming a subagent type across 6 files) to order edits and avoid half-updated state.

## Pairing

| Step | Skill |
| ---- | ----- |
| 1 — Discover | `orchestration-audit` |
| 2 — Fix | `orchestration-remediate` (this) |
