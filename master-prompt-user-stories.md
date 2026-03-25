Use the generate-user-stories skill.

## Source documentation

Read the following files from docs/ to extract all project context.
Do not ask for additional input — derive everything from these files:

- docs/lti-overview.md
- docs/use-cases.md
- docs/data-model.md
- docs/architecture.md

## Required output

Generate a single file at LTI-JSGB/UserStories-JSGB.md
structured in 4 sections as defined below.
All content must be in English.

---

SECTION 1 — User Stories
Delegate to the product-owner subagent.

- Generate a minimum of 8 user stories covering the most impactful
  features of LTI's ATS across the full recruitment lifecycle.
- Cover all actor types: Recruiter, Candidate, Hiring Manager,
  Admin, and System.
- Group stories under named Epics aligned to these lifecycle stages:
  Job Creation | Publishing | Application Intake | Review & Screening |
  Assessments | Interview Scheduling | Hiring & Offer
- Apply the user story template from user-stories-standards.mdc exactly.
  Every field must be populated. No placeholders.
- Evaluate every story against INVEST criteria.
  Rewrite any story that fails any criterion before including it.
- Assign Fibonacci story point estimates to each story.

---

SECTION 2 — Product Backlog
Delegate to the backlog-manager subagent.

- Take all user stories from Section 1 as input.
- Apply Value vs Complexity scoring:
  Score = (Business Value + Urgency) / (Complexity + Risk)
  Rate each dimension 1-5.
- Produce the prioritized backlog table from user-stories-standards.mdc.
- Sort descending by Score.
- After the table:
  a) State the methodology used and why it was chosen.
  b) Justify the top 3 prioritized stories in 2-3 sentences each.
  c) Identify dependency chains that affect the ordering.
  d) Draw a clear MVP line indicating which stories form the MVP scope.

---

SECTION 3 — Work Tickets
Delegate to the sprint-planner subagent.

- Select the highest-priority user story from Section 2.
- Break it down into a minimum of 5 concrete work tickets covering
  all technical layers required:
  database schema | backend API | business logic | frontend |
  integrations | automated tests | documentation
- Apply the work ticket template from user-stories-standards.mdc exactly.
- Order tickets by dependency (blockers first).
- Include at least one ticket dedicated to non-functional requirements
  (performance, security, or scalability).

---

SECTION 4 — Effort Estimation
Handled by the sprint-planner subagent as part of Section 3.

- For every ticket from Section 3 assign:
  a) Fibonacci story points (1, 2, 3, 5, 8, 13, 21)
  b) T-shirt size (XS, S, M, L, XL)
  c) Confidence level (High, Medium, Low) with a one-line reason
- Produce the effort estimation summary table from
  user-stories-standards.mdc.
- Add a total story points sum at the bottom.
- Add a sprint allocation recommendation: how many 2-week sprints
  would the tickets in Section 3 require, and why.

---

## Execution instructions

- Follow user-stories-pipeline.mdc for sequence and delegation.
- Follow user-stories-standards.mdc for all format contracts.
- Do not stop between sections. Complete all 4 in one pass.
- Flag any ambiguity with [ASSUMPTION: ...] and continue.
- Do not invent requirements not present in the source documentation.
- When complete, output a summary table:

| Section           | Content                               | Status |
| ----------------- | ------------------------------------- | ------ |
| User Stories      | N stories generated                   | Done   |
| Product Backlog   | N stories prioritized, MVP line drawn | Done   |
| Work Tickets      | N tickets for [story title]           | Done   |
| Effort Estimation | Total: N story points                 | Done   |
