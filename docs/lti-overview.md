# LTI Applicant Tracking System (ATS) Overview

LTI Applicant Tracking System (ATS) is a next-gen recruiting platform that manages the full hiring lifecycle in one place, from job requisitions to onboarding handoff. Built for mid-to-large enterprises, LTI helps recruiters, HR teams, hiring managers, and admins automate and accelerate hiring while maintaining a consistent candidate experience.

The platform is designed around the complete workflow of seven stages, matching `docs/architecture.md`: (1) requisition setup & posting preparation, (2) publishing to job boards and LinkedIn, (3) candidate application intake, (4) resume parsing & enrichment (async), (5) pipeline routing & screening, (6) assessments + interview scheduling, and (7) offer & hiring completion + notifications. To reach faster time-to-hire, LTI prioritizes integration across the recruitment ecosystem, including LinkedIn, job boards, HRIS, calendars, and assessment tools.

## Value Proposition and Competitive Advantages

LTI’s value proposition is end-to-end automation of recruitment processes with integration-first workflows. Compared with Workday, Greenhouse, Lever, iCIMS, and Taleo, LTI focuses on unifying the entire pipeline (7 stages) into a single operational flow, reducing handoffs and operational friction between tools. [ASSUMPTION: the competitive advantage is primarily workflow unification and tighter integration depth; exact feature parity vs each vendor is not specified in the prompt].

Key functional areas aligned to the 7 stages are (same labels as `docs/architecture.md` §3):

1. **Requisition setup & posting preparation** — create and update requisitions, hiring plans, and posting metadata.
2. **Publishing to job boards and LinkedIn** — syndicate postings; company website and social channels where configured.
3. **Candidate application intake** — portal submissions and inbound applications; organize by role and pipeline stage.
4. **Resume parsing & enrichment (async)** — structured extraction from resumes, enrichment, and search-ready candidate profiles.
5. **Pipeline routing & screening** — stage assignment, screening rules, and collaboration between recruiters and hiring managers.
6. **Assessments + interview scheduling** — assessments and results, plus calendar-backed interview scheduling and execution.
7. **Offer & hiring completion + notifications** — offers, approvals, onboarding handoff to HRIS, and asynchronous notifications.

[ASSUMPTION: `docs/assets/ats-workflow.png` may be missing; the seven lifecycle stages match `docs/architecture.md` §3.]

## Lean Canvas

| Problem | Customer Segments | Unique Value Proposition | Solution | Channels | Revenue Streams | Cost Structure | Key Metrics | Unfair Advantage |
|---|---|---|---|---|---|---|---|---|
| Enterprises lose time-to-hire due to fragmented recruiting workflows, manual handoffs across systems, and slow coordination across stages (requisition setup, publishing, intake, resume parsing & enrichment, screening, assessments & interviews, offers & notifications). | Mid-to-large enterprises (recruiting teams, HR teams, hiring managers, admins). | One integrated ATS that automates the end-to-end 7-stage hiring lifecycle with integration-first workflows across LinkedIn, job boards, HRIS, calendars, and assessment tools. | (1) Requisition setup & posting preparation, (2) Publishing to job boards and LinkedIn, (3) Candidate application intake, (4) Resume parsing & enrichment (async), (5) Pipeline routing & screening, (6) Assessments + interview scheduling, (7) Offer & hiring completion + notifications. | Direct sales to HR/recruiting leadership; partner ecosystem for integration onboarding; targeted outreach to enterprises already running LinkedIn/job boards/HRIS. | Subscription per enterprise (tiered by usage/features), onboarding/integration services, optional support/SLA add-ons. | Engineering for workflow orchestration and integrations; integration maintenance; security/compliance; customer onboarding and support. | Time-to-hire, application-to-screened conversion rate, assessment completion rate, interview scheduling lead time, offer-to-acceptance rate, hiring funnel drop-off by stage, automation coverage (% of steps integrated). | Deep lifecycle orchestration across the 7 stages plus a practical integration surface (LinkedIn, job boards, HRIS, calendars, assessment tools) that reduces operational friction end-to-end. |
