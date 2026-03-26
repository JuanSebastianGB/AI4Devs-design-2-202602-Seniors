Use the generate-artifacts skill.

## Project context

System name: LTI Applicant Tracking System (ATS)

Description: LTI is a startup building the next-generation ATS — software
that manages the full recruitment lifecycle in one platform.

Recruitment lifecycle (sequential stages):

1. Creating job requisitions
2. Publishing to job boards, company website, and social media
3. Receiving job applications from candidates
4. Reviewing and screening applications
5. Conducting online assessments
6. Scheduling and running interviews
7. Hiring selected applicants (offer through onboarding handoff)

Target customers: mid-to-large enterprises.
Primary users (internal): Recruiters, HR teams, Hiring Managers, Admins.
External users: Candidates (apply without an internal account).
Business goal: automate and accelerate hiring, reduce time-to-hire,
integrate with LinkedIn, job boards, HRIS, calendars, and assessment tools.

Reference image for context: docs/assets/ats-workflow.png

## Required artifacts

ARTIFACT 0 — docs/prd.md
Delegate to the requirements-analyst subagent.

Generate a complete PRD covering:

- Problem statement: what pain does LTI's ATS solve and for whom
- Measurable objectives (OKR format, minimum 3)
- Primary actors: Recruiter, HR Team, Hiring Manager, Admin
- Secondary actors: Candidate, HRIS systems, Job Boards, Assessment Tools
- Main user stories (minimum 5, format: As a [role], I want [action]
  so that [benefit])
- Functional requirements (numbered list, grouped by lifecycle stage:
  Job Creation | Publishing | Application Intake | Review & Screening |
  Assessments | Interview Scheduling | Hiring & Offer)
- Non-functional requirements covering:
  - Performance (page load, concurrent users)
  - Security (candidate PII, data privacy, authentication)
  - Scalability (enterprise load, multi-tenant)
  - Availability (uptime SLA)
  - Integrations (job boards, LinkedIn, HRIS, calendar, assessments)
- Out of scope for v1 (explicit list)
- Success criteria (measurable KPIs tied to the objectives)

ARTIFACT 1 — docs/lti-overview.md

- 2-3 paragraph description of LTI's ATS
- Value proposition and competitive advantages over Workday, Greenhouse,
  Lever, iCIMS, and Taleo
- Description of main functional areas aligned to the 7 lifecycle stages
- Lean Canvas covering all 9 sections rendered as a markdown table:
  Problem | Customer Segments | Unique Value Proposition |
  Solution | Channels | Revenue Streams | Cost Structure |
  Key Metrics | Unfair Advantage

ARTIFACT 2 — docs/use-cases.md

- The 3 most impactful use cases for a first release
- For each use case provide:
  a) Name and brief description (2-3 sentences)
  b) Primary actor and secondary actors
  c) Preconditions and postconditions
  d) Main flow (numbered steps)
  e) Alternative and exception flows
  f) PlantUML use case diagram (one diagram per use case)
- Differentiate external actors (Candidate, no account required) from
  internal authenticated actors (Recruiter, Hiring Manager, Admin, System)
- Use <<include>> for mandatory sub-flows
- Use <<extend>> for optional sub-flows

ARTIFACT 3 — docs/data-model.md

- Define all necessary entities to support the full recruitment lifecycle.
  Must include at minimum:
  JobRequisition, JobPosting, Application, Candidate, PipelineStage,
  Assessment, Interview, Offer, User, Organization
- For each entity provide:
  a) Key fields with name and data type
  b) All relationships with cardinality
  c) One-sentence business purpose
- Render as a single Mermaid erDiagram block covering all entities
- After the diagram add a summary table:
  Entity | Business Purpose | Key Relationships

ARTIFACT 4 — docs/architecture.md

- Written explanation covering:
  a) Architectural style chosen and justification
  b) Main services and their responsibilities
  c) Data flow across the 7 recruitment lifecycle stages
  d) External integrations: job boards, LinkedIn, email and calendar,
  HRIS, assessment tools, identity provider
  e) Non-functional considerations: high read traffic on public job pages,
  candidate PII security, async processing for notifications and
  resume parsing, scalability for enterprise load
- Mermaid architecture diagram showing:
  - Frontend layer (candidate portal + recruiter dashboard)
  - API Gateway / BFF
  - Core domain services grouped by bounded context in subgraphs:
    - Job Management (requisitions, postings, publishing)
    - Application Processing (intake, parsing, routing)
    - Pipeline Management (stages, screening, scoring)
    - Assessment Service (test delivery, results)
    - Interview Service (scheduling, calendar sync)
    - Offer and Hiring Service (offer letters, approvals)
    - Notification Service (email, SMS, in-app)
    - Identity and Access Management
  - Data stores with type labeled (PostgreSQL, Redis, S3, Elasticsearch)
  - External integrations (job boards, LinkedIn, HRIS, calendar, assessments)
  - Message queue / async workers
  - CDN and load balancer
  - Label every arrow with protocol or data type:
    REST, gRPC, events, SMTP, OAuth 2.0, webhooks

ARTIFACT 5 — docs/c4-diagram.md

- C4 model zooming into the Application Processing Service
  (the component responsible for receiving, parsing, and routing
  incoming job applications)
- Level 1 — System Context (Mermaid C4Context block):
  Show LTI ATS in context with all external actors and systems.
  Include: Candidate, Recruiter, Hiring Manager, job boards,
  LinkedIn, HRIS, calendar providers, assessment platforms.
- Level 2 — Container diagram (Mermaid block):
  Show the main containers of the LTI ATS and how Application
  Processing Service fits within the overall system.
  Include: Candidate Portal, Recruiter Dashboard, API Gateway,
  Application Processing Service, other core services,
  databases, message queue.
- Level 3 — Component diagram (Mermaid block):
  Zoom into Application Processing Service and show its
  internal components with data flows and dependencies:
  - Application Intake API (receives submissions via REST)
  - Resume Parser (extracts structured data, async via queue)
  - Duplicate Detector (checks for existing candidate records)
  - Pipeline Router (assigns application to correct pipeline stage)
  - Notification Dispatcher (triggers confirmation emails and
    internal alerts)
- After each level add a short paragraph explaining:
  - What the diagram shows
  - The key design decisions visible in it

## Execution instructions

- Use the generate-artifacts skill to coordinate all subagents.
- Delegate as follows:
  - Artifact 0 (PRD, docs/prd.md) and Artifacts 1 and 2 ->
    requirements-analyst subagent
  - Artifact 3 -> data-architect subagent
  - Artifacts 4 and 5 -> system-architect subagent
- Follow software-definition-standards.mdc for all output formats.
- Follow artifact-pipeline.mdc sequencing only for the artifact types explicitly requested in this prompt.
- Flag any ambiguity or missing information with [ASSUMPTION: ...]
  and continue without stopping.
- Complete all 6 artifacts (Artifacts 0–5) in one pass (skip competitive
  analysis and ADR generation unless explicitly requested).
- When all artifacts are saved, output a completion summary table:

| Artifact                   | File                 | Status |
| -------------------------- | -------------------- | ------ |
| PRD                        | docs/prd.md          | Done   |
| LTI Overview + Lean Canvas | docs/lti-overview.md | Done   |
| Use Cases                  | docs/use-cases.md    | Done   |
| Data Model                 | docs/data-model.md   | Done   |
| Architecture               | docs/architecture.md | Done   |
| C4 Diagram                 | docs/c4-diagram.md   | Done   |
