# LTI Applicant Tracking System (ATS) - C4 (Zoom: Application Processing)

## Level 1: System Context (C4Context)

```mermaid
C4Context
  title "LTI ATS - System Context"
  Person(candidate, "Candidate", "Applies to jobs and views application status")
  Person(recruiter, "Recruiter", "Manages postings and reviews applications")
  Person(hiringManager, "Hiring Manager", "Reviews/approves candidates and hiring progress")

  System(ats, "LTI ATS", "Applicant Tracking System")

  System_Ext(jobBoards, "Job Boards", "Publish jobs and forward candidate applications")
  System_Ext(linkedin, "LinkedIn", "Career platform for job publishing and application intake")
  System_Ext(hris, "HRIS", "Onboarding records and HR workflow")
  System_Ext(calendar, "Calendar Providers", "Scheduling and calendar event sync")
  System_Ext(assessments, "Assessment Platforms", "Test delivery and results")
  System_Ext(idp, "Identity Provider", "Authentication & authorization via OAuth 2.0")

  Rel(candidate, ats, "Applies / views status", "REST")
  Rel(recruiter, ats, "Recruiting workflows", "REST")
  Rel(hiringManager, ats, "Approvals & decisions", "REST")

  Rel(ats, jobBoards, "Publish postings", "REST")
  Rel(jobBoards, ats, "Application payloads & updates", "webhooks")

  Rel(ats, linkedin, "Publish postings", "REST")
  Rel(linkedin, ats, "Application payloads & updates", "webhooks")

  Rel(ats, calendar, "Create/update interviews", "REST")
  Rel(calendar, ats, "Calendar changes", "webhooks")

  Rel(ats, assessments, "Start/submit tests", "REST")
  Rel(assessments, ats, "Test results", "webhooks")

  Rel(ats, hris, "Onboarding updates", "REST")

  Rel(ats, idp, "Authenticate/authorize users", "OAuth 2.0")
```

This diagram shows the ATS in its business context: who uses it (Candidate, Recruiter, Hiring Manager) and how it integrates with external job sources, HRIS, calendar providers, assessment platforms, and the identity provider. The key design decision visible here is keeping external boundaries explicit and protocol-labeled (`REST`, `webhooks`, `OAuth 2.0`).

## Level 2: Container Diagram (C4Container)

```mermaid
C4Container
  title "LTI ATS - Containers (Application Processing in focus)"

  Person(candidate, "Candidate", "Browser/App")
  Person(recruiter, "Recruiter", "Browser/App")
  Person(hiringManager, "Hiring Manager", "Browser/App")

  Container(candidatePortal, "Candidate Portal", "Web UI", "Candidate-facing ATS UI")
  Container(recruiterDashboard, "Recruiter Dashboard", "Web UI", "Recruiter-facing ATS UI")
  Container(bff, "API Gateway / BFF", "Backend-for-Frontend", "Request routing + auth boundary")

  Container(appProcessing, "Application Processing Service", "Microservice", "Intake, parsing, deduplication, routing")
  Container(jobMgmt, "Job Management Service", "Microservice", "Requisitions, postings, publishing")
  Container(pipelineMgmt, "Pipeline Management Service", "Microservice", "Stages, screening, progression")
  Container(assessmentSvc, "Assessment Service", "Microservice", "Test delivery + results ingestion")
  Container(interviewSvc, "Interview Service", "Microservice", "Interview scheduling + calendar sync")
  Container(offerSvc, "Offer and Hiring Service", "Microservice", "Offer letters + approvals + HRIS updates")
  Container(notificationSvc, "Notification Service", "Microservice", "Email/SMS/In-app notifications")
  Container(iamSvc, "IAM Service", "Microservice", "OAuth 2.0 authorization + policy enforcement")

  ContainerDb(postgres, "PostgreSQL", "Relational DB", "Applications, candidates, requisitions")
  ContainerDb(redis, "Redis", "Cache", "Hot lookups and transient state")
  ContainerDb(s3, "S3", "Object storage", "Resume artifacts and parsed outputs")
  ContainerDb(es, "Elasticsearch", "Search", "Public job/search indexing and candidate search")

  Container(queue, "Message Queue", "Async broker", "Queued events for parsing + notifications")

  Container_Ext(jobBoards, "Job Boards", "External system")
  Container_Ext(linkedin, "LinkedIn", "External system")
  Container_Ext(hris, "HRIS", "External system")
  Container_Ext(calendar, "Calendar Providers", "External system")
  Container_Ext(assessments, "Assessment Platforms", "External system")
  Container_Ext(idp, "Identity Provider", "External system")

  Rel(candidate, candidatePortal, "Uses ATS UI", "REST")
  Rel(recruiter, recruiterDashboard, "Uses ATS UI", "REST")
  Rel(hiringManager, recruiterDashboard, "Approves/decides in ATS UI", "REST")

  Rel(candidatePortal, bff, "API requests", "REST")
  Rel(recruiterDashboard, bff, "API requests", "REST")
  Rel(bff, iamSvc, "OAuth2 token validation", "OAuth 2.0")
  Rel(iamSvc, idp, "Validate identities", "OAuth 2.0")

  %% Key zoom relationship
  Rel(bff, appProcessing, "Route intake + parsing triggers", "REST")
  Rel(appProcessing, queue, "Publish parsing/routing/notification events", "events")

  %% Other core services
  Rel(bff, jobMgmt, "Requisition/posting APIs", "REST")
  Rel(bff, pipelineMgmt, "Pipeline APIs", "REST")
  Rel(bff, assessmentSvc, "Assessment APIs", "REST")
  Rel(bff, interviewSvc, "Interview APIs", "REST")
  Rel(bff, offerSvc, "Offer/hiring APIs", "REST")
  Rel(bff, notificationSvc, "Notification APIs", "REST")

  %% Datastores
  Rel(jobMgmt, postgres, "Persist posting data", "REST")
  Rel(jobMgmt, es, "Index job documents", "REST")

  Rel(appProcessing, postgres, "Store application/candidate data", "REST")
  Rel(appProcessing, s3, "Store resumes/artifacts", "REST")
  Rel(appProcessing, es, "Update searchable representations", "REST")
  Rel(appProcessing, redis, "Cache hot lookups", "REST")

  Rel(pipelineMgmt, postgres, "Persist pipeline state", "REST")
  Rel(pipelineMgmt, es, "Expose read-optimized views", "REST")

  Rel(assessmentSvc, postgres, "Persist assessment results", "REST")
  Rel(interviewSvc, postgres, "Persist interview scheduling state", "REST")
  Rel(offerSvc, postgres, "Persist offers/approvals", "REST")

  %% External integrations (protocol-labeled)
  Rel(jobMgmt, jobBoards, "Publish jobs", "REST")
  Rel(linkedin, appProcessing, "Inbound application payloads", "webhooks")
  Rel(jobBoards, appProcessing, "Inbound application payloads", "webhooks")

  Rel(jobMgmt, linkedin, "Publish jobs", "REST")

  Rel(assessmentSvc, assessments, "Start tests", "REST")
  Rel(assessments, assessmentSvc, "Test results", "webhooks")

  Rel(interviewSvc, calendar, "Create/update interviews", "REST")
  Rel(calendar, interviewSvc, "Calendar changes", "webhooks")

  Rel(offerSvc, hris, "Onboarding updates", "REST")
```

This diagram shows the major containers and their responsibilities, with special emphasis on the **Application Processing Service** and its integration points (API Gateway/BFF and the message queue). The key design decisions visible are clear bounded-context separation and explicit async event coordination between intake/parsing/routing and notification flows.

## Level 3: Component Diagram (C4Component) - Application Processing Service

```mermaid
C4Component
  title "Application Processing Service - Components"

  Container_Ext(queue, "Message Queue", "Async broker")
  ContainerDb(postgres, "PostgreSQL", "Relational DB")
  ContainerDb(redis, "Redis", "Cache")
  ContainerDb(s3, "S3", "Object storage")
  ContainerDb(es, "Elasticsearch", "Search index")

  Container_Ext(notificationSvc, "Notification Service", "Microservice")
  Container_Ext(pipelineMgmt, "Pipeline Management Service", "Microservice")
  Container_Ext(emailProvider, "Email Provider", "SMTP")

  Component(appIntake, "Application Intake API", "REST entrypoint", "Receives application submissions and intake payloads")
  Component(resumeParser, "Resume Parser", "Worker", "Parses resumes into structured data asynchronously")
  Component(dedupe, "Duplicate Detector", "Internal component", "Checks existing candidate/application records to avoid duplicates")
  Component(router, "Pipeline Router", "Internal component", "Routes enriched applications to correct pipeline stage")
  Component(notificationDispatcher, "Notification Dispatcher", "Internal component", "Sends confirmation emails + internal alerts")

  %% Intake -> async parsing
  Rel(appIntake, queue, "Enqueue parse/enrichment requests", "events")
  Rel(queue, resumeParser, "Deliver parsing jobs", "events")

  %% Parsing persistence
  Rel(resumeParser, postgres, "Persist extracted structured data", "REST")
  Rel(resumeParser, s3, "Store parsed artifacts + originals", "REST")
  Rel(resumeParser, es, "Update search index documents", "REST")
  Rel(resumeParser, redis, "Cache derived lookups", "REST")

  %% Dedup + routing
  Rel(appIntake, dedupe, "Trigger deduplication", "REST")
  Rel(dedupe, postgres, "Read existing candidate/application data", "REST")

  Rel(dedupe, router, "Deduplication outcome", "events")
  Rel(router, pipelineMgmt, "Assign stage and update routing state", "gRPC")

  %% Notifications
  Rel(router, notificationDispatcher, "Stage/routing triggers", "events")
  Rel(notificationDispatcher, queue, "Enqueue notification dispatch requests", "events")
  Rel(notificationDispatcher, notificationSvc, "Dispatch internal alerts", "events")
  Rel(notificationSvc, emailProvider, "Send confirmation email", "SMTP")
```

This diagram zooms into the Application Processing Service and makes the internal pipeline explicit: intake accepts payloads, parsing is performed asynchronously via queued `events`, deduplication guards data quality, routing delegates progression to Pipeline Management, and notification dispatch is triggered as a downstream async dependency. The key visible design decision is isolating heavy/slow work (parsing) behind the queue while keeping the intake API responsive.

