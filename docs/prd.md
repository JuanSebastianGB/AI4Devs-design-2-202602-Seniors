# Product Requirements Document: LTI Applicant Tracking System (ATS)

## Product Summary
LTI Applicant Tracking System (ATS) is an enterprise recruiting platform designed for mid-to-large organizations that need to manage the full hiring lifecycle in one system. This PRD defines the first release scope based on the existing LTI overview and use-case documentation, centered on the seven-stage lifecycle: Job Creation -> Publishing -> Application Intake -> Review & Screening -> Assessments -> Interview Scheduling -> Hiring & Offer.

## Problem Statement
Recruiting teams in mid-to-large enterprises often run hiring through fragmented tools, disconnected spreadsheets, email threads, external job boards, assessment platforms, and calendar systems. That fragmentation creates delays, duplicate data entry, inconsistent candidate handling, and poor visibility across the funnel.

The pain is especially visible across the seven-stage hiring lifecycle:

1. **Job Creation:** Recruiters and HR teams spend too much time collecting requisition details, approvals, and hiring-plan inputs across multiple systems.
2. **Publishing:** Job postings are manually copied into different channels, creating inconsistent listings and delayed launch times.
3. **Application Intake:** Applications arrive from multiple sources with inconsistent formats, making it harder to normalize, deduplicate, and route candidates correctly.
4. **Review & Screening:** Recruiters and hiring managers lack a shared, structured workflow to review applicants, apply screening criteria, and record decisions consistently.
5. **Assessments:** Assessment handoffs to external providers are often manual, resulting in delayed invitations, missing results, and poor traceability.
6. **Interview Scheduling:** Coordinating interviews across recruiters, hiring managers, candidates, and calendars creates avoidable delays and scheduling conflicts.
7. **Hiring & Offer:** Offer generation, decision tracking, and onboarding handoff are slow because data must be re-entered into downstream HR systems.

LTI ATS solves this by unifying the full enterprise hiring flow in one platform and integrating with job boards, LinkedIn, assessment tools, calendars, HRIS platforms, and identity providers to reduce operational friction, accelerate time-to-hire, and improve candidate experience.

## Measurable Objectives
### Objective 1: Reduce time-to-hire across the end-to-end recruiting lifecycle.
- **KR1.1:** Reduce median time from approved requisition to first published job posting to less than 15 minutes for 90% of requisitions.
- **KR1.2:** Reduce median time from application received to first screening decision to less than 3 business days.
- **KR1.3:** Reduce median time from interview-ready candidate to scheduled interview to less than 48 hours for 85% of cases.

### Objective 2: Increase recruiter and HR operational efficiency through automation.
- **KR2.1:** Enable 80% of requisitions to be published to at least three channels in a single workflow.
- **KR2.2:** Automatically ingest and normalize 95% of incoming applications without manual data re-entry.
- **KR2.3:** Reduce manual coordination steps for interview scheduling by 60% compared with the current fragmented workflow. [ASSUMPTION: baseline coordination effort will be measured against current enterprise recruiting operations because no existing baseline metric is documented.]

### Objective 3: Improve hiring quality, visibility, and candidate experience.
- **KR3.1:** Achieve at least 75% completion rate for invited online assessments.
- **KR3.2:** Keep candidate application abandonment below 20% from form start to submission.
- **KR3.3:** Reach 99.5% successful processing rate for supported outbound and inbound integration events across publishing, assessments, calendar scheduling, and HRIS handoff.

## Actors
### Primary Actors
- **Recruiter:** Creates requisitions, publishes jobs, manages candidate progression, initiates assessments, coordinates interviews, and sends offers.
- **HR Team:** Oversees recruiting operations, compliance, hiring workflow consistency, and final hiring/onboarding coordination.
- **Hiring Manager:** Reviews candidates, collaborates on screening, participates in interviews, and influences hiring decisions.
- **Admin:** Configures enterprise settings, permissions, integrations, channels, and operational policies.

### Secondary Actors
- **Candidate:** External applicant who views jobs, submits applications, completes assessments, attends interviews, and accepts or declines offers.
- **HRIS:** Downstream system that receives selected-hire data for onboarding handoff.
- **Job Boards:** External publishing channels used to distribute job postings and attract applicants.
- **Assessment Platforms:** External providers that host assessments and return completion/results data.
- **Calendar Providers:** External calendar systems used for availability lookup, event creation, and interview invitations.
- **Identity Providers:** Enterprise authentication systems used for secure internal user access. [ASSUMPTION: v1 supports enterprise SSO through standard SAML and/or OIDC integrations because identity providers are listed as a target integration category, but the exact protocol mix is not specified in source docs.]

## Main User Stories
1. As a Recruiter, I want to create and configure a job requisition with its hiring plan so that I can launch hiring quickly with the right role details and workflow settings.
2. As a Recruiter, I want to publish a job posting to multiple channels in one action so that I can reach candidates faster and avoid duplicate manual work.
3. As an HR Team member, I want to track candidates and hiring decisions across all seven stages in one system so that I can maintain compliance, consistency, and reporting accuracy.
4. As a Hiring Manager, I want to review candidates, provide structured feedback, and collaborate on screening decisions so that I can help identify the best applicants faster.
5. As an Admin, I want to configure integrations, permissions, and enterprise defaults so that the ATS operates securely and consistently across the organization.
6. As a Candidate, I want to apply easily, complete required assessments, and receive timely interview and offer updates so that I have a clear and professional hiring experience.

## Functional Requirements
### Job Creation
1. The system shall allow authorized recruiters to create, edit, save, and archive job requisitions.
2. The system shall support required requisition fields including job title, department, location, employment type, hiring manager, and target hiring workflow.
3. The system shall validate required requisition data before a job can move to publishing.
4. The system shall allow recruiters to configure screening stages, assessment requirements, and interview plan defaults at the requisition level.
5. The system shall maintain a status for each requisition such as draft, ready to publish, active, on hold, and closed.

### Multi-channel Publishing
6. The system shall allow recruiters to publish a requisition to one or more configured channels from a single workflow.
7. The system shall support publishing to the company site and external job boards, including LinkedIn when configured.
8. The system shall track per-channel publishing status, including success, pending, partial failure, and failed.
9. The system shall allow recruiters to unpublish or update a posting and propagate supported changes to connected channels.
10. The system shall preserve an audit trail of publishing actions and channel responses.

### Application Intake
11. The system shall receive applications from supported external channels and direct candidate flows.
12. The system shall associate each application with the correct requisition and initial lifecycle stage.
13. The system shall normalize inbound application data into a standard candidate profile and application record.
14. The system shall detect likely duplicate applications and flag them for recruiter review.
15. The system shall validate required candidate-submitted fields before accepting an application.
16. The system shall notify relevant internal users when new applications are received for an active requisition.

### Review and Screening
17. The system shall present candidate applications in a review workflow organized by requisition and lifecycle stage.
18. The system shall allow recruiters and hiring managers to record structured screening decisions, comments, and candidate status changes.
19. The system shall prevent advancement when required screening criteria or mandatory feedback inputs are incomplete.
20. The system shall support rejection, advancement, hold, and request-assessment decisions.
21. The system shall retain a history of candidate stage changes and decision rationale.

### Online Assessments
22. The system shall allow recruiters to trigger supported online assessments for selected candidates.
23. The system shall track assessment invitation, in-progress, completed, expired, and failed states.
24. The system shall ingest assessment completion signals and results from supported assessment providers.
25. The system shall attach assessment status and results to the candidate record for recruiter and hiring manager review.
26. The system shall surface exception states when assessment creation or result retrieval fails.

### Interview Scheduling
27. The system shall allow recruiters to schedule interviews for selected candidates using connected calendar providers.
28. The system shall create interview events, invite relevant participants, and store meeting details when available.
29. The system shall detect scheduling conflicts and present alternative time options.
30. The system shall allow recruiters and hiring managers to record interview outcomes and structured feedback.
31. The system shall prevent offer generation until required interview outcomes are recorded.

### Hiring and Offer
32. The system shall allow recruiters to generate and send offers to selected candidates.
33. The system shall track offer status including drafted, sent, accepted, declined, and expired.
34. The system shall record the final hiring decision and close non-selected candidates appropriately.
35. The system shall trigger onboarding handoff to the configured HRIS for accepted candidates.
36. The system shall surface and log HRIS handoff failures for retry or administrative intervention.

## Non-Functional Requirements
### Performance
- Core internal pages shall load initial usable content within 2.5 seconds at the 95th percentile under normal enterprise usage.
- Candidate application pages shall load within 3 seconds at the 95th percentile on broadband connections.
- Search, filter, and stage-transition actions shall return a user-visible response within 2 seconds for the 95th percentile of requests.
- The platform shall support at least 2,000 concurrent internal users and 10,000 concurrent candidate sessions across tenants. [ASSUMPTION: these starting concurrency targets are appropriate for the intended mid-to-large enterprise market because exact capacity targets are not provided in source docs.]

### Security
- The platform shall protect candidate and employee-related PII in transit using TLS 1.2+ and at rest using industry-standard encryption.
- The platform shall enforce role-based access control for recruiters, HR team members, hiring managers, and admins.
- The platform shall support enterprise authentication through external identity providers.
- The platform shall maintain audit logs for privileged actions, hiring decisions, integration events, and sensitive data changes.
- The platform shall minimize data exposure by limiting access to candidate records based on tenant, role, and authorized recruiting scope.

### Scalability
- The platform shall support multi-tenant enterprise deployment with logical data isolation between customers.
- The platform shall scale job publishing, application ingestion, screening workflows, and integration processing without requiring tenant-specific code changes.
- The platform shall support growth to high-volume recruiting periods without degradation of core workflow performance beyond stated targets.

### Availability
- The production service shall target 99.9% monthly uptime excluding planned maintenance windows.
- The platform shall degrade gracefully when a non-core integration is unavailable, preserving internal workflow visibility and retry capability.
- Critical hiring records, candidate data, and audit logs shall be backed up and recoverable according to enterprise operational standards. [ASSUMPTION: exact RPO/RTO targets will be defined with operations/security stakeholders in a later phase.]

### Integrations
- The platform shall provide supported integrations for job boards, LinkedIn, HRIS platforms, calendar providers, and assessment platforms.
- Integration failures shall be visible to authorized users with enough context to retry, troubleshoot, or escalate.
- The platform shall normalize data exchanged with external systems into consistent internal workflow states.
- Integration processing shall be designed to handle retries, duplicate events, and partial downstream failures safely.

## Out of Scope for v1
- Full employee onboarding workflow beyond HRIS handoff.
- Payroll, compensation management, or benefits administration.
- Native video interviewing platform capabilities beyond scheduling and meeting-link coordination.
- AI-based candidate scoring, ranking, or automated hiring recommendations.
- Advanced recruiting analytics, forecasting, or executive dashboards beyond core operational KPI tracking.
- Agency/vendor management and external recruiter commission workflows.
- Support for anonymous public career-site CMS customization beyond standard job publishing surfaces.
- Complex multi-step offer approval chains. [ASSUMPTION: v1 supports basic offer generation and status tracking, but not enterprise-specific approval workflows.]

## Success Criteria
1. At least 90% of approved requisitions are published to their first live channel within 15 minutes.
2. At least 95% of applications from supported channels are ingested and normalized without manual re-entry.
3. Median time from application receipt to first screening decision is under 3 business days.
4. At least 85% of interview-ready candidates receive a scheduled interview within 48 hours.
5. Assessment completion reaches at least 75% for candidates who are invited to complete an assessment.
6. Candidate application abandonment stays below 20%.
7. Supported integration events achieve at least 99.5% successful processing across publishing, assessments, calendar scheduling, and HRIS handoff.
8. Monthly service availability meets or exceeds 99.9%.

## Traceability to Source Documents
- The seven-stage lifecycle, enterprise audience, and integration-first positioning are grounded in `docs/lti-overview.md`.
- The operational flows for requisition publishing, screening plus assessments, and interview scheduling through hiring handoff are grounded in `docs/use-cases.md`.
- This PRD extends those documents into measurable business targets, enterprise non-functional expectations, and v1 product scope while preserving the same product direction.
