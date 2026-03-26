# 1. User Stories

Generated with the agentic pipeline from `master-prompt-user-stories.md`: **Task** `product-owner` → `backlog-manager` → `sprint-planner`. Sources: `docs/prd.md`, `docs/lti-overview.md`, `docs/use-cases.md`, `docs/data-model.md`, `docs/architecture.md`.

Work tickets for **US-001** use sequential ids **TICKET-001**–**TICKET-009** (project convention; aligns with `user-stories-standards.mdc` and `sprint-planner` agent).

### US-001: Create and configure job requisitions

**Type:** Feature  
**Epic:** Job requisition and pipeline configuration  
**Priority:** High  
**Estimate:** 5  

**User Story:**  
As a Recruiter, I want to create, edit, and configure a job requisition with hiring workflow settings so that I can launch hiring with validated role details and the right pipeline defaults.

**Description:**  
Requisitions are the internal source of truth before publishing and drive downstream screening, assessments, and interviews. The product requires required fields, validation before publishing, and requisition-level configuration of screening stages, assessments, and interview plan defaults.

**Acceptance Criteria (BDD format):**

- **Scenario 1: Save draft requisition**
  - Given I am an authorized recruiter with permission to create requisitions  
  - When I enter title, department, location, employment type, hiring manager, and target hiring workflow and save  
  - Then the requisition is stored in draft status with my changes retained  

- **Scenario 2: Validation blocks publishing readiness**
  - Given a requisition is missing one or more required fields  
  - When I attempt to mark it ready for publishing or start publishing  
  - Then the system blocks the action and indicates which required data is missing  

- **Scenario 3: Configure screening and downstream defaults**
  - Given I have a draft requisition  
  - When I configure screening stages, assessment requirements, and interview plan defaults at requisition level  
  - Then those settings are saved and will apply to new applications for that requisition  

**INVEST Evaluation:**

- Independent: Yes — delivers requisition authoring without requiring live postings.  
- Negotiable: Yes — exact field set and UI layout can evolve while keeping validation rules.  
- Valuable: Yes — directly supports faster, consistent hiring setup per PRD.  
- Estimable: Yes — bounded to CRUD, validation, and configuration surfaces described in sources.  
- Small: Yes — single primary object (requisition) and its configuration.  
- Testable: Yes — verify persistence, validation gates, and applied defaults on new applications.  

**Related Stories:** US-002, US-004  
**Notes:** PRD status labels (e.g. draft, ready to publish) differ from example enums in `docs/data-model.md`; align in implementation. [ASSUMPTION: “department” and related org fields follow enterprise configuration already implied by multi-tenant ATS docs.]

---

### US-002: Publish jobs to multiple channels from one workflow

**Type:** Feature  
**Epic:** Multi-channel publishing and application intake  
**Priority:** High  
**Estimate:** 8  

**User Story:**  
As a Recruiter, I want to publish a job posting to one or more configured channels in a single workflow so that I reach candidates faster and avoid duplicate manual publishing work.

**Description:**  
Publishing must cover company site and external job boards, including LinkedIn when configured, track per-channel outcomes, support unpublish/update with propagation where supported, and retain an audit trail of actions and channel responses.

**Acceptance Criteria (BDD format):**

- **Scenario 1: Successful multi-channel publish**
  - Given a validated requisition and at least three configured channels  
  - When I choose those channels and publish  
  - Then each selected channel shows success or explicit pending state and the posting becomes discoverable per channel rules  

- **Scenario 2: Partial channel failure**
  - Given one channel rejects or fails publication  
  - When publishing completes for other channels  
  - Then the system marks partial failure for the affected channel, keeps successful channels live, and surfaces failure context for follow-up  

- **Scenario 3: Unpublish or update with propagation**
  - Given an active multi-channel posting  
  - When I unpublish or update supported fields  
  - Then supported changes propagate to connected channels and the audit trail records the action and responses  

**INVEST Evaluation:**

- Independent: Yes — can be tested with stubbed or sandbox channel integrations.  
- Negotiable: Yes — channel set and retry UX are negotiable within integration constraints.  
- Valuable: Yes — matches core time-to-hire and efficiency objectives.  
- Estimable: Yes — scope tied to PRD publishing requirements and architecture integration pattern.  
- Small: Yes — focused on publish/update/unpublish and status tracking, not full intake.  
- Testable: Yes — assert per-channel states, audit records, and propagation behavior.  

**Related Stories:** US-001, US-003  
**Notes:** Architecture describes REST publishing and webhook status updates; exact LinkedIn behavior when throttled follows use-case exception flows.

---

### US-003: Submit a job application as a candidate

**Type:** Feature  
**Epic:** Multi-channel publishing and application intake  
**Priority:** High  
**Estimate:** 3  

**User Story:**  
As a Candidate, I want to complete and submit an application for a published role so that I can be considered without unnecessary friction or ambiguity.

**Description:**  
Candidates apply without an internal account; submissions must satisfy required fields, associate to the correct requisition/posting, and support the product goal of low abandonment on the application path.

**Acceptance Criteria (BDD format):**

- **Scenario 1: Successful submission**
  - Given I open a published job listing  
  - When I complete all required fields and submit  
  - Then my application is accepted, associated to the correct requisition pipeline, and I receive confirmation appropriate to the channel  

- **Scenario 2: Validation prevents invalid submission**
  - Given required application fields are missing or invalid  
  - When I attempt to submit  
  - Then the system blocks submission, prompts correction, and does not record an application until valid  

- **Scenario 3: Performance-sensitive public page**
  - Given I am on the application experience  
  - When I load and navigate the form under normal broadband conditions  
  - Then initial usable content meets the PRD candidate page performance target at the stated percentile  

**INVEST Evaluation:**

- Independent: Yes — depends only on a published posting, not on internal review features.  
- Negotiable: Yes — field set and UX can adjust within validation and compliance needs.  
- Valuable: Yes — core funnel entry; ties to abandonment KPI.  
- Estimable: Yes — narrow candidate-facing scope.  
- Small: Yes — single primary flow (apply).  
- Testable: Yes — functional tests plus basic performance checks against stated targets.  

**Related Stories:** US-002, US-004  
**Notes:** [ASSUMPTION: confirmation messaging uses email and/or in-app channel as implemented by the Notification Service; sources require notification to internal users on new applications, not full candidate notification design.]

---

### US-004: Review ingested applications and resolve duplicate candidates

**Type:** Feature  
**Epic:** Multi-channel publishing and application intake  
**Priority:** High  
**Estimate:** 5  

**User Story:**  
As a Recruiter, I want inbound applications normalized and likely duplicates flagged for my review so that the pipeline stays clean without losing legitimate applicants.

**Description:**  
Applications arrive from multiple channels; the system normalizes data, detects likely duplicates, and routes records into the review workflow by requisition and stage, while notifying the hiring team when new applications arrive.

**Acceptance Criteria (BDD format):**

- **Scenario 1: Normalize and associate application**
  - Given a new application arrives from a supported channel  
  - When the system processes it  
  - Then a standard candidate profile and application record are created and tied to the correct requisition initial stage  

- **Scenario 2: Duplicate flagged instead of silent double entry**
  - Given the system detects a likely duplicate application  
  - When the candidate submits  
  - Then the duplicate is flagged for recruiter review rather than creating an unreviewed duplicate pipeline entry  

- **Scenario 3: Notify recruiting stakeholders**
  - Given a new valid application is recorded for an active requisition  
  - When processing completes  
  - Then relevant internal users are notified per configured rules  

**INVEST Evaluation:**

- Independent: Yes — can be validated with synthetic inbound payloads and routing rules.  
- Negotiable: Yes — deduplication heuristics and notification rules can evolve.  
- Valuable: Yes — supports data quality and responsiveness KPIs.  
- Estimable: Yes — aligns to application processing and notification architecture.  
- Small: Yes — focused on intake quality and notifications, not screening decisions.  
- Testable: Yes — verify normalization fields, duplicate flags, and notification triggers.  

**Related Stories:** US-003, US-005  
**Notes:** Webhook intake from boards/LinkedIn is described in architecture; [ASSUMPTION: “relevant internal users” maps to recruiter and/or hiring manager assignments on the requisition.]

---

### US-005: Collaborate on screening with structured decisions

**Type:** Feature  
**Epic:** Review and screening  
**Priority:** High  
**Estimate:** 5  

**User Story:**  
As a Hiring Manager, I want to review candidates with structured feedback and clear screening decisions so that we advance or stop applicants consistently and quickly.

**Description:**  
Screening spans recruiters and hiring managers; decisions include rejection, advancement, hold, and assessment requests, with history retained. Incomplete mandatory screening inputs should block progression when configured.

**Acceptance Criteria (BDD format):**

- **Scenario 1: Record structured screening outcome**
  - Given applications are visible for a requisition in screening  
  - When I record a decision with required comments or criteria fields completed  
  - Then the candidate status updates, history captures rationale, and the application moves to the appropriate next state  

- **Scenario 2: Block advancement when requirements incomplete**
  - Given mandatory screening criteria or feedback inputs are incomplete  
  - When I attempt to advance the candidate  
  - Then the system blocks the transition and indicates what is missing  

- **Scenario 3: Request assessment from screening**
  - Given assessment is appropriate for the candidate  
  - When I choose a request-assessment decision  
  - Then the candidate enters the assessment path and downstream owners see the pending assessment state  

**INVEST Evaluation:**

- Independent: Yes — needs applications in pipeline but not live assessments or interviews.  
- Negotiable: Yes — exact decision taxonomy and forms can be tuned.  
- Valuable: Yes — addresses shared recruiter/hiring manager workflow in use cases.  
- Estimable: Yes — tied to PRD screening rules and pipeline stages.  
- Small: Yes — focused on screening workflow only.  
- Testable: Yes — verify guards, audit history, and state transitions.  

**Related Stories:** US-004, US-006  
**Notes:** Data model lists application statuses and pipeline stages; ensure UI states match PRD stage/decision language.

---

### US-006: Run online assessments with traceable results

**Type:** Feature  
**Epic:** Online assessments  
**Priority:** High  
**Estimate:** 8  

**User Story:**  
As a Recruiter, I want to trigger supported assessments and see status and results on the candidate record so that hiring decisions are evidence-based and auditable.

**Description:**  
Assessments move through invitation, in-progress, completed, expired, and failed states; results are ingested from providers and surfaced for recruiter and hiring manager review, with explicit exception handling when initiation or retrieval fails.

**Acceptance Criteria (BDD format):**

- **Scenario 1: Invite and complete assessment**
  - Given a candidate is selected for an online assessment  
  - When the system initiates the assessment and the candidate completes it  
  - Then completion and results are attached to the candidate record and visible to authorized reviewers  

- **Scenario 2: Initiation failure**
  - Given the provider or integration cannot create a session  
  - When initiation fails  
  - Then the candidate is marked in an assessment-pending/exception state and the recruiter is alerted  

- **Scenario 3: Results retrieval failure**
  - Given the candidate completed the assessment but results cannot be fetched  
  - When polling/webhook processing fails  
  - Then the system stores an error state, keeps results pending, and supports retry or escalation  

**INVEST Evaluation:**

- Independent: Yes — can be tested against provider sandboxes and mock webhooks.  
- Negotiable: Yes — provider-specific fields live in negotiable integration details.  
- Valuable: Yes — ties to assessment completion KPI and screening quality.  
- Estimable: Yes — bounded to initiation, state machine, and result attachment.  
- Small: Yes — single capability area (assessments) across one primary flow.  
- Testable: Yes — state transitions, attachments, and failure paths are verifiable.  

**Related Stories:** US-005, US-007  
**Notes:** [ASSUMPTION: supported providers expose creation and results callbacks as implied by use cases and architecture webhooks.]

---

### US-007: Schedule interviews with calendar integration and outcomes

**Type:** Feature  
**Epic:** Interview scheduling and outcomes  
**Priority:** High  
**Estimate:** 8  

**User Story:**  
As a Recruiter, I want to schedule interviews using connected calendars, handle conflicts, and capture structured interview outcomes so that we avoid delays and only extend offers when requirements are met.

**Description:**  
Interview scheduling creates events, invites participants, stores meeting details when available, detects conflicts with alternatives, and records outcomes and feedback. Offer generation must be blocked until required interview outcomes exist.

**Acceptance Criteria (BDD format):**

- **Scenario 1: Schedule with invites**
  - Given calendar integration is configured and a candidate is interview-ready  
  - When I schedule an interview session  
  - Then calendar events are created, participants are invited, and details are stored on the interview record  

- **Scenario 2: Conflict handling**
  - Given proposed slots conflict with participant availability  
  - When I attempt to schedule  
  - Then the system surfaces conflicts and proposes alternative times for selection  

- **Scenario 3: Outcomes gate offers**
  - Given interviews have occurred  
  - When required interview outcome fields are missing  
  - Then offer generation is blocked until authorized users complete required outcome capture  

**INVEST Evaluation:**

- Independent: Yes — can be tested with calendar sandboxes without executing offers.  
- Negotiable: Yes — which interviews are mandatory can be configured per requisition.  
- Valuable: Yes — aligns with scheduling lead-time KPIs and decision quality.  
- Estimable: Yes — mirrors interview service responsibilities in architecture.  
- Small: Yes — stays within scheduling + outcome recording, not offer content.  
- Testable: Yes — calendar artifacts, conflict UX, and gating rules are testable.  

**Related Stories:** US-006, US-008  
**Notes:** [ASSUMPTION: virtual meeting link generation follows the optional extend path in use case 3 when the integration supports it.]

---

### US-008: Extend offers, capture decision, and hand off to HRIS

**Type:** Feature  
**Epic:** Hiring, offer, and HRIS handoff  
**Priority:** High  
**Estimate:** 8  

**User Story:**  
As a Recruiter, I want to generate and send offers, track responses, record final hiring decisions, and trigger HRIS onboarding handoff for accepted candidates so that hiring closes cleanly and downstream HR systems stay in sync.

**Description:**  
Offer statuses include drafted, sent, accepted, declined, and expired; non-selected candidates are closed appropriately. Accepted offers trigger onboarding handoff with visible failure states for retry or admin intervention.

**Acceptance Criteria (BDD format):**

- **Scenario 1: Send and accept offer**
  - Given interview requirements are satisfied and a candidate is selected  
  - When I generate and send an offer and the candidate accepts  
  - Then offer status reflects acceptance, final hire decision is recorded, and HRIS onboarding handoff is triggered  

- **Scenario 2: Declined offer**
  - Given an offer is outstanding  
  - When the candidate declines  
  - Then the decline is recorded, the candidate is marked not hired, and the recruiter can proceed with another candidate  

- **Scenario 3: HRIS handoff failure**
  - Given a candidate accepted an offer  
  - When HRIS handoff fails  
  - Then the failure is logged, visible to authorized users, and supports retry or admin escalation  

**INVEST Evaluation:**

- Independent: Yes — can be tested with HRIS sandbox or mocks once offer states exist.  
- Negotiable: Yes — offer content structure is negotiable within “basic offer” v1 scope.  
- Valuable: Yes — completes lifecycle and integration success KPIs.  
- Estimable: Yes — mapped to offer entity and HRIS REST updates in architecture.  
- Small: Yes — limited to offer lifecycle and handoff, not full onboarding inside ATS.  
- Testable: Yes — statuses, audit logs, and handoff retries are verifiable.  

**Related Stories:** US-007  
**Notes:** PRD excludes complex multi-step offer approval chains for v1; [ASSUMPTION: “generate offer” covers standard templated content without enterprise-specific approval routing.]

---

### US-009: Configure enterprise integrations, permissions, and defaults

**Type:** Feature  
**Epic:** Enterprise administration and governance  
**Priority:** Medium  
**Estimate:** 8  

**User Story:**  
As an Admin, I want to configure integrations, permissions, and enterprise defaults so that the ATS runs securely and consistently across the organization.

**Description:**  
Admins enable publishing channels, assessment, calendar, HRIS, and identity integrations where applicable, enforce RBAC for recruiters, HR, hiring managers, and admins, and ensure integration failures are visible with enough context to retry or escalate.

**Acceptance Criteria (BDD format):**

- **Scenario 1: Configure a supported integration**
  - Given enterprise credentials and endpoints for a supported integration category  
  - When I save valid configuration  
  - Then the integration becomes active for permitted workflows and status checks show healthy versus failing states  

- **Scenario 2: Enforce role-based access**
  - Given users with different roles  
  - When they attempt privileged actions (for example, integration configuration or cross-tenant data access)  
  - Then access is denied unless their role permits it per policy  

- **Scenario 3: Surface integration failure context**
  - Given an integration event fails during publishing, assessments, calendars, or HRIS  
  - When an authorized user reviews integration health  
  - Then they see failure details sufficient to retry, troubleshoot, or escalate  

**INVEST Evaluation:**

- Independent: Yes — admin surfaces can be validated separately from end-user flows using test tenants.  
- Negotiable: Yes — exact admin UI and permission granularity can evolve.  
- Valuable: Yes — underpins security, uptime, and multi-tenant operations in PRD/NFRs.  
- Estimable: Yes — scope anchored to PRD integration and security requirements plus IAM/OAuth boundary in architecture.  
- Small: Yes — v1 admin scope focuses on configuration and visibility, not custom CMS or advanced analytics.  
- Testable: Yes — RBAC tests, integration health views, and failure logging.  

**Related Stories:** US-002, US-006, US-007, US-008  
**Notes:** [ASSUMPTION: enterprise authentication uses OAuth 2.0 with external IdP as stated in architecture, alongside PRD mention of SAML/OIDC-class providers; exact protocol matrix may be refined without changing the user-facing goal.]

---

### US-010: Monitor hiring funnel consistency as HR operations

**Type:** Feature  
**Epic:** Cross-lifecycle visibility and compliance  
**Priority:** Medium  
**Estimate:** 5  

**User Story:**  
As an HR Team member, I want to track candidates and decisions across all hiring stages in one system so that compliance, consistency, and reporting stay accurate.

**Description:**  
HR oversight depends on a single system of record across the seven lifecycle stages, aligned with audit expectations for decisions, integration events, and sensitive data access boundaries described in the PRD.

**Acceptance Criteria (BDD format):**

- **Scenario 1: View requisition funnel health**
  - Given active requisitions with applications in multiple stages  
  - When I open operational views for a requisition or portfolio  
  - Then I see stage distribution and key statuses without needing external spreadsheets  

- **Scenario 2: Access limited to authorized scope**
  - Given tenant, role, and recruiting scope rules  
  - When I attempt to view a candidate or decision outside my authorization  
  - Then access is denied and the attempt is auditable  

- **Scenario 3: Trace decision and integration history**
  - Given hiring decisions and integration-driven state changes occurred  
  - When I review audit or history surfaces for a candidate  
  - Then I can see stage changes, decision rationale where captured, and relevant integration outcomes  

**INVEST Evaluation:**

- Independent: Yes — read/reporting surfaces operate on data produced by other stories.  
- Negotiable: Yes — exact dashboards stay within “core operational KPI tracking” per out-of-scope boundaries.  
- Valuable: Yes — matches PRD actor needs for compliance and visibility.  
- Estimable: Yes — scope limited to visibility and access control, not executive analytics suites.  
- Small: Yes — no new hiring actions, primarily views and audits.  
- Testable: Yes — RBAC, filters, and audit log presence tests.  

**Related Stories:** US-004, US-005, US-008, US-009  
**Notes:** PRD excludes advanced executive dashboards; keep reporting to operational views implied by objectives and NFRs.

---

End of Section 1 (10 user stories; minimum 8 satisfied).

# 2. Product Backlog

| Priority | ID | Title | Epic | Business Value | Urgency | Complexity | Risk | Score |
| -------- | --- | ----- | ---- | ----- | ---- | ---- | ---- | ----- |
| 1 | US-001 | Job requisition creation and hiring plan configuration | Job Creation | 5 | 5 | 3 | 2 | 2.00 |
| 2 | US-003 | Candidate application experience (apply, validation, notifications) | Application Intake | 4 | 5 | 3 | 2 | 1.80 |
| 3 | US-005 | Review and screening workflow with structured decisions | Review & Screening | 5 | 5 | 4 | 2 | 1.67 |
| 4 | US-010 | HR funnel visibility and cross-stage candidate tracking | Operations & Compliance | 4 | 4 | 3 | 2 | 1.60 |
| 5 | US-002 | Multi-channel job publishing (status, updates, audit trail) | Multi-channel Publishing | 5 | 5 | 4 | 3 | 1.43 |
| 6 | US-009 | Admin configuration, integrations surface, and RBAC | Administration & Security | 4 | 5 | 4 | 3 | 1.29 |
| 7 | US-004 | Application intake, normalization, and duplicate detection | Application Intake | 5 | 5 | 5 | 3 | 1.25 |
| 8 | US-007 | Interview scheduling, calendar integration, and outcomes | Interview Scheduling | 4 | 4 | 4 | 3 | 1.14 |
| 9 | US-008 | Offers, hiring decision, and HRIS onboarding handoff | Hiring & Offer | 5 | 4 | 4 | 4 | 1.13 |
| 10 | US-006 | Online assessments (invite, state, provider results) | Online Assessments | 3 | 3 | 4 | 3 | 0.86 |

**MVP line:** **US-001, US-002, US-003, US-004, US-005**, plus a **minimal US-009** slice (tenants, roles, core integration hooks, audit expectations) so publishing and candidate data stay secure and operable. **US-010** is MVP only as a **thin operational view** (per-requisition pipeline and basic stage metrics), not full analytics. **US-006–US-008** sit **below the MVP line** for a first shippable “attract → apply → triage” release; they become the next horizon once the core funnel is stable.

**Methodology:** **Value vs Complexity** — `Score = (Business Value + Urgency) / (Complexity + Risk)` with each dimension rated **1–5**. It was chosen because the PRD stresses **speed** (publish, screen) and **automation** (intake) while integrations and provider variance inflate **complexity/risk** for later stages; the ratio surfaces **early ROI** without ignoring hard delivery cost.

**Top 3 rationale**

1. **US-001** — Requisitions are the **root entity** for workflow defaults, validation, and “ready to publish”; they unlock every downstream KR tied to **time-to-first-live-posting** and prevent building channels or intake against inconsistent job definitions. **Trade-off:** Deep workflow configuration early can slow MVP unless scope is capped to fields and statuses in the PRD.

2. **US-003** — Candidate apply path directly affects **KR3.2 abandonment** and perceived product quality; it can be prioritized **before** full integration-heavy features because it is mostly **product UX + validation + notifications**. **Trade-off:** A strong apply flow without **US-004** dedup risks messy recruiter queues, so **US-004 must follow closely** even though it scores lower on the ratio.

3. **US-005** — Screening is where hiring **decisions and compliance evidence** accrue; it addresses **KR1.2** (time to first screening decision) and is **less integration-heavy** than assessments, calendars, or HRIS. **Trade-off:** Strict “block advancement” rules increase **implementation and change-management risk** if hiring managers are not ready for structured inputs.

**Dependency chains (ordering caveats)**

- **Linear funnel:** **US-001 → US-002 → (US-003 + US-004) → US-005 → US-006/US-007 → US-008**; candidates and applications assume **published requisitions** and normalized **application records**.
- **Cross-cutting:** **US-009** is not always reflected in a pure funnel sort but is a **parallel early dependency** for **RBAC, integration failure visibility, and audit** (PRD NFRs).
- **US-010** gains value as stages **emit events**, but a **minimal funnel view** can ship once **US-001–US-005** produce consistent stage transitions.

**Trade-off (score vs delivery order):** The table **ranks US-003 above US-002** on the ratio (candidate experience vs integration cost). In execution, teams often **parallelize** **US-002** with **US-003** or **slightly favor US-002** so there is a **live surface** to apply against—accept **short-term schedule tension** between these two if marketing launch dates dominate.

[ASSUMPTION: Backlog-manager aligned titles to US-001–US-010 without verbatim Section 1 paste; numbers and ordering follow the scored table above.]

# 3. Work Tickets

Scope: **US-001** (highest priority in the scored backlog).

### TICKET-001: Persist extended `JobRequisition` model and status vocabulary

**Parent Story:** US-001  
**Type:** Technical Task  
**Assignee:** Backend Team / DBA  
**Priority:** High  
**Estimate:** 5 | T-shirt size: M  
**Sprint:** TBD  
**Labels:** database, job-management, multi-tenant  

**Description:**  
Extend the relational model so recruiters can store all US-001 fields (including organization context such as department and hiring manager) and stable requisition lifecycle states that match the PRD (e.g. draft vs ready-to-publish) while remaining backward-compatible with documented examples in `docs/data-model.md`.

**Technical Details:**

- Add/confirm columns or FKs on `JobRequisition`: `departmentId` (or `departmentCode` referencing org configuration), `hiringManagerUserId` (FK to `User`), optional `targetWorkflowTemplateId` if workflows are templated, `updatedAt` behavior on save.
- Define canonical `status` enum values and document mapping from PRD labels to stored values; include migration and data constraints (`CHECK` or app-level enum).
- Store requisition-level **default** configuration for assessments and interview plans as versioned `jsonb` (e.g. `assessmentDefaults`, `interviewPlanDefaults`) or normalized child tables if query needs justify it.

**Acceptance Criteria:**

- [ ] Migration(s) apply cleanly on PostgreSQL with tenant scoping via `organizationId` on all new FK paths.
- [ ] `JobRequisition` rows can be created in **draft** with nullable/optional fields where the story allows partial save.
- [ ] Documented status list is agreed with product and reflected in DB comments or shared enum package.
- [ ] Rollback/down migration strategy is defined for the change set.

**Dependencies:** None  
**Related Docs:** `docs/data-model.md` (JobRequisition, User, Organization), `docs/architecture.md` (Job Management → PostgreSQL)  
**Notes:** Story explicitly flags PRD status labels vs example enums in `data-model.md`—implementation must pick one source of truth and map UI copy.

---

### TICKET-002: Requisition CRUD and draft-save API (Job Management)

**Parent Story:** US-001  
**Type:** Feature  
**Assignee:** Backend Team  
**Priority:** High  
**Estimate:** 8 | T-shirt size: L  
**Sprint:** TBD  
**Labels:** backend, api, job-management, rbac  

**Description:**  
Expose REST endpoints (via API Gateway/BFF → Job Management) to create, read, update, and list requisitions for the authenticated organization, including draft persistence for title, department, location, employment type, hiring manager, and target hiring workflow selection.

**Technical Details:**

- Endpoints (illustrative): `POST /requisitions`, `GET /requisitions/{id}`, `PATCH /requisitions/{id}`, `GET /requisitions` with pagination/filter.
- Enforce `OAuth 2.0` subject → `organizationId` isolation and recruiter (or delegated) permission checks per architecture.
- Validate employment type and references (hiring manager belongs to org, department exists in org config).
- Emit domain events or outbox entries only if later services need them (keep minimal for US-001).

**Acceptance Criteria:**

- [ ] Authorized recruiter can create and update a requisition; unauthorized/forbidden roles receive `403`.
- [ ] Cross-tenant access is impossible (IDs from another org return `404` or `403` per API standard).
- [ ] Partial updates preserve existing fields; `updatedAt` advances on successful write.
- [ ] OpenAPI (or equivalent) spec updated for BFF consumers.

**Dependencies:** TICKET-001  
**Related Docs:** `docs/architecture.md` (§2 Job Management, §3 stage 1 data flow, IAM)  
**Notes:** Align error shape with validation ticket (TICKET-003) for consistent client handling.

---

### TICKET-003: Publish-readiness validation gate

**Parent Story:** US-001  
**Type:** Feature  
**Assignee:** Backend Team  
**Priority:** High  
**Estimate:** 3 | T-shirt size: S  
**Sprint:** TBD  
**Labels:** backend, validation, api  

**Description:**  
Implement a explicit validation layer that runs when the user attempts to mark a requisition “ready to publish” or starts a publish flow, blocking the transition and returning which required fields or configuration are missing.

**Technical Details:**

- Centralize rules (e.g. dedicated validator module or domain service) listing required attributes for “publish-ready” vs “draft-save”.
- API: either dedicated `POST /requisitions/{id}/validate` or enforced on `PATCH` when `status` transitions to ready/publish; return structured field-level errors (machine-readable codes + human messages).
- Ensure idempotency: failed validation does not mutate state.

**Acceptance Criteria:**

- [ ] Missing any required field blocks ready/publish transition with a clear, enumerable error payload.
- [ ] Draft save remains allowed when required-for-publish fields are empty.
- [ ] Unit tests cover at least three scenarios: all required present, one missing, multiple missing.

**Dependencies:** TICKET-002  
**Related Docs:** US-001 acceptance scenarios; `docs/data-model.md` (`JobRequisition`)  
**Notes:** Publishing itself may belong to US-002; this ticket only enforces the **gate** for US-001.

---

### TICKET-004: Requisition-scoped pipeline stage configuration API

**Parent Story:** US-001  
**Type:** Feature  
**Assignee:** Backend Team  
**Priority:** High  
**Estimate:** 5 | T-shirt size: M  
**Sprint:** TBD  
**Labels:** backend, api, pipeline-management  

**Description:**  
Allow recruiters to define and reorder screening/workflow stages for a draft requisition by persisting `PipelineStage` rows tied to `jobRequisitionId`, matching the data model’s requisition-owned stages.

**Technical Details:**

- CRUD/replace semantics for stages: `stageKind`, `stageName`, `sortOrder`, `isTerminal`; bulk reorder endpoint or transactional replace to avoid orphan ordering.
- Validate `stageKind` values and non-empty names; enforce org + requisition ownership.
- Coordinate with Pipeline Management bounded context if stages are owned there in implementation—architecture shows `PM_Service`; if Job Management owns authoring, define internal contract or shared library for stage shape.

**Acceptance Criteria:**

- [ ] Stages can be created, updated, deleted/replaced for a draft requisition and persist correctly.
- [ ] `sortOrder` defines deterministic stage order for downstream routing (documented contract).
- [ ] Attempts to modify stages for a requisition in a forbidden lifecycle state return a defined error (configurable rule).

**Dependencies:** TICKET-001, TICKET-002  
**Related Docs:** `docs/data-model.md` (`PipelineStage`, `JobRequisition`)  
**Notes:** `[REVIEW NEEDED]` markers in ER diagram do not block API design; treat cardinality as 1 requisition : N stages unless product overrides.

---

### TICKET-005: Requisition-level assessment and interview default settings

**Parent Story:** US-001  
**Type:** Feature  
**Assignee:** Backend Team  
**Priority:** Medium  
**Estimate:** 5 | T-shirt size: M  
**Sprint:** TBD  
**Labels:** backend, api, assessment, interview  

**Description:**  
Persist recruiter-configured defaults for assessment requirements and interview plan templates at requisition level so new applications for postings derived from this requisition can inherit them (consumption may be implemented in Application Processing in a follow-up, but storage and API belong here).

**Technical Details:**

- Expose fields under requisition `PATCH` or nested resource `PATCH /requisitions/{id}/defaults`.
- Schema for defaults: e.g. required `assessmentType` list, passing thresholds, default `interviewType` sequence, panel hints—stored as validated `jsonb` with JSON Schema or DTO validation.
- Version defaults on `updatedAt` for audit; document handoff contract for application creation (US-004 / intake).

**Acceptance Criteria:**

- [ ] Defaults save and reload correctly for the same requisition.
- [ ] Invalid combinations (e.g. unknown assessment type) are rejected with validation errors.
- [ ] API documentation describes how intake will resolve defaults when creating an application.

**Dependencies:** TICKET-001, TICKET-002  
**Related Docs:** `docs/data-model.md` (`Assessment`, `Interview`, `Application`), `docs/architecture.md` (stages 5–6)  
**Notes:** Actual “apply on new application” wiring can be a separate ticket if out of US-001 scope; minimum is durable storage + contract.

---

### TICKET-006: Recruiter UI — requisition authoring and draft save

**Parent Story:** US-001  
**Type:** Feature  
**Assignee:** Frontend Team  
**Priority:** High  
**Estimate:** 8 | T-shirt size: L  
**Sprint:** TBD  
**Labels:** frontend, recruiter-dashboard, ux  

**Description:**  
Build recruiter dashboard flows to create and edit a job requisition with the fields in the user story, autosave or explicit save to draft, and surface server validation errors inline.

**Technical Details:**

- Consume BFF REST endpoints from TICKET-002; handle loading/error/empty states.
- Form fields: title, department (from org config), location, employment type, hiring manager picker, target hiring workflow selector.
- Respect OAuth session; no PII beyond internal users in this form.
- Accessibility: labels, keyboard navigation, focus management on validation errors.

**Acceptance Criteria:**

- [ ] Recruiter can complete happy-path draft create and edit; data matches backend after refresh.
- [ ] Permission errors show a clear message without exposing other tenants’ data.
- [ ] Field-level errors map from API validation payload where provided.

**Dependencies:** TICKET-002  
**Related Docs:** `docs/architecture.md` (Recruiter Dashboard → API Gateway)  
**Notes:** Layout negotiable per INVEST; validation rules are not.

---

### TICKET-007: Recruiter UI — screening stages and downstream defaults

**Parent Story:** US-001  
**Type:** Feature  
**Assignee:** Frontend Team  
**Priority:** Medium  
**Estimate:** 5 | T-shirt size: M  
**Sprint:** TBD  
**Labels:** frontend, pipeline, configuration  

**Description:**  
Provide UI to configure screening stages (order and types), assessment requirements, and interview plan defaults for a draft requisition, calling APIs from TICKET-004 and TICKET-005.

**Technical Details:**

- Drag-and-drop or explicit ordering control wired to reorder API; confirm destructive changes (delete stage).
- Separate sections or tabs for stages vs assessment/interview defaults to reduce cognitive load.
- Optimistic UI only where safe; otherwise show save status per section.

**Acceptance Criteria:**

- [ ] User can configure stages and defaults for a draft requisition and see persisted state after reload.
- [ ] Stage reorder persists and reflects correct order from API.
- [ ] Validation errors from nested defaults APIs are visible and actionable.

**Dependencies:** TICKET-004, TICKET-005, TICKET-006  
**Related Docs:** US-001 scenario 3; `docs/data-model.md`  
**Notes:** Depends on stable API error format from TICKET-003/005.

---

### TICKET-008: Tests, contract tests, and developer documentation

**Parent Story:** US-001  
**Type:** Test  
**Assignee:** Backend Team / QA  
**Priority:** Medium  
**Estimate:** 5 | T-shirt size: M  
**Sprint:** TBD  
**Labels:** testing, documentation, quality  

**Description:**  
Add automated coverage and concise developer docs for requisition authoring so regressions in draft save, validation gates, and configuration are caught in CI.

**Technical Details:**

- API integration tests: tenant isolation, CRUD, validation gate, stage CRUD, defaults persistence.
- Frontend component/e2e tests for critical paths (minimum: create draft, failed publish-ready validation messaging).
- Document environment variables, feature flags (if any), and local runbook snippet for Job Management + DB migrations.

**Acceptance Criteria:**

- [ ] CI runs new tests green against migrated schema.
- [ ] At least one test per acceptance scenario in US-001 mapped to API or e2e level.
- [ ] README or internal doc section links to OpenAPI and main entities.

**Dependencies:** TICKET-002, TICKET-003, TICKET-004, TICKET-005, TICKET-006, TICKET-007  
**Related Docs:** US-001 acceptance criteria  
**Notes:** Performance/load testing not in scope here; see TICKET-009.

---

### TICKET-009: Non-functional hardening for requisition APIs

**Parent Story:** US-001  
**Type:** Technical Task  
**Assignee:** Platform / Backend Team  
**Priority:** Medium  
**Estimate:** 3 | T-shirt size: S  
**Sprint:** TBD  
**Labels:** security, scalability, observability  

**Description:**  
Apply cross-cutting NFRs for requisition endpoints: rate limiting at the gateway, structured audit logging for create/update/status attempts, and baseline performance targets for list/get hot paths under org-scoped indexes.

**Technical Details:**

- Gateway/BFF: per-org or per-user rate limits on mutating routes; idempotency keys optional for `POST` if duplicate creates are a risk.
- Audit: who changed what on requisition and stage tables (append-only audit or change log table).
- DB: indexes on `(organizationId, updatedAt)` for recruiter lists; query plan check for pagination.
- Align with architecture assumptions: PII-light on requisition, still least-privilege and audit per `docs/architecture.md` §5.

**Acceptance Criteria:**

- [ ] Mutating endpoints are protected from abusive burst traffic per agreed limit policy.
- [ ] Audit trail captures actor, timestamp, and changed entity id for requisition updates.
- [ ] Explain plan or metrics show list endpoint avoids full table scan for typical org sizes (document threshold assumptions).

**Dependencies:** TICKET-002  
**Related Docs:** `docs/architecture.md` §5 (NFRs), IAM integration  
**Notes:** Encryption-at-rest and full RBAC model may span platform-wide initiatives; this ticket scopes requisition-specific hooks.

---


# 4. Effort Estimation


**Methodology:** Fibonacci story points for delivery uncertainty; T-shirt size for batch planning; confidence reflects clarity of scope vs known gaps (PRD/status mapping, org department model).

| Ticket | Title | Story Points | T-Shirt Size | Confidence | Notes |
| --- | ----- | --- | --- | ---- | ----- |
| TICKET-001 | Persist extended JobRequisition model and status vocabulary | 5 | M | Medium | PRD vs data-model status alignment and new org fields. |
| TICKET-002 | Requisition CRUD and draft-save API | 8 | L | Medium | RBAC + BFF contract + error consistency. |
| TICKET-003 | Publish-readiness validation gate | 3 | S | High | Bounded rule set; depends on stable DTOs. |
| TICKET-004 | Requisition-scoped pipeline stage configuration API | 5 | M | Medium | Ownership split Job Mgmt vs Pipeline Mgmt. |
| TICKET-005 | Assessment and interview default settings | 5 | M | Low | Defaults schema and downstream contract still evolving. |
| TICKET-006 | Recruiter UI — authoring and draft save | 8 | L | Medium | UX patterns and org config dependencies. |
| TICKET-007 | Recruiter UI — stages and defaults | 5 | M | Medium | Depends on two backend tracks completing. |
| TICKET-008 | Tests, contract tests, and developer documentation | 5 | M | High | Straightforward once APIs stable. |
| TICKET-009 | Non-functional hardening for requisition APIs | 3 | S | Medium | Platform policies may dictate limits/audit format. |

**Total story points:** **47**

**Sprint allocation comment:** Plan **two sprints** for a single full-stack squad (e.g. **Sprint N:** TICKET-001 → TICKET-002 → TICKET-003 → TICKET-009 in parallel frontend spike on TICKET-006; **Sprint N+1:** TICKET-004 → TICKET-005 → TICKET-006 completion → TICKET-007 → TICKET-008), or **one dense sprint** (~47 points) only if the team historically completes 40+ points per sprint and backend/frontend capacity is split. Prefer pulling TICKET-005 and TICKET-007 into the second sprint if defaults schema remains ambiguous.

---
