# MASTER BUILD PROMPT — INCIDENTIQ

## 1. Your Role

Act as a principal software architect, senior Java backend engineer, senior React engineer, AI engineer, SaaS product designer, and DevOps engineer.

Your task is to design and build a production-ready, multi-tenant SaaS application called **IncidentIQ**.

Do not create a simple demo or a static dashboard. Build a functional application with a real backend, database, authentication, integrations, AI workflows, and a polished frontend.

Work incrementally. Make architectural decisions, document them, implement features, run tests, and fix issues before moving forward.

I am a backend engineer with experience in Java, Spring Boot, Redis, Kafka, PostgreSQL, MySQL, React and TypeScript. I will review the architecture and code.

---

## 2. Product Vision

Product name: IncidentIQ

Category: AI-powered production incident investigation platform.

Tagline:

"Stop guessing what broke production."

Product description:

IncidentIQ helps engineering teams investigate production incidents by automatically correlating Slack conversations, GitHub pull requests, deployments, and operational events.

It generates:
- Incident timelines.
- Potential root-cause hypotheses.
- Evidence-backed investigation reports.
- Recommended debugging steps.
- Automated postmortems.
- Corrective action items.

The product should reduce the manual effort involved in understanding production failures and documenting incidents.

IMPORTANT: IncidentIQ is initially an investigation assistant, not a replacement for PagerDuty, Datadog or other monitoring platforms.

AI-generated root causes must always be presented as hypotheses until verified by an engineer.

---

## 3. Target Customers

Initially target:

1. SaaS startups with 5–30 engineers.
2. Software development agencies.
3. Fintech engineering teams.
4. E-commerce engineering teams.
5. Small engineering organizations without dedicated SRE teams.

Target geography:
- United States.
- United Kingdom.
- India.
- Other English-speaking markets.

The application must support multiple organizations, with complete tenant-level data isolation.

---

## 4. Technology Stack

### Backend
- Java 21+
- Spring Boot 3+
- Spring Security
- Spring Data JPA
- Hibernate
- PostgreSQL
- Redis
- Maven
- REST APIs
- Webhook processing

### Frontend
- React
- TypeScript
- Vite
- Tailwind CSS
- shadcn/ui
- Recharts for charts

### AI
- Anthropic Claude API
- Structured JSON responses
- Tool calling where appropriate
- Prompt versioning
- Token and cost tracking

Use environment variables for all API credentials.

Never hardcode secrets.

### Infrastructure
- Docker
- Docker Compose
- PostgreSQL
- Redis
- GitHub Actions for CI
- Low-cost cloud deployment

Avoid Kubernetes and unnecessary infrastructure in the MVP.

---

## 5. Core Product Features

### A. Authentication and Organization Management

Implement:
- User registration.
- Login.
- Logout.
- Password reset.
- JWT-based authentication or secure session-based authentication.
- Organization creation.
- Organization invitations.
- User roles.

Roles:
- OWNER
- ADMIN
- ENGINEER
- VIEWER

Users must only access data belonging to their organization.

### B. Slack Integration

Implement Slack OAuth 2.0.

Requirements:
- Install IncidentIQ into a Slack workspace.
- Store workspace metadata.
- Securely store encrypted OAuth credentials.
- Support Slack slash commands.
- Support Slack event subscriptions.
- Support interactive messages.

Initial commands:

`/incident start <description>`

Creates a new incident.

`/incident status`

Returns active incidents.

`/incident investigate <incident-id>`

Starts AI investigation.

`/incident resolve <incident-id>`

Marks an incident as resolved.

`/incident report <incident-id>`

Returns the generated investigation report.

When an incident starts:
1. Create an incident record.
2. Record its start timestamp.
3. Associate the Slack channel.
4. Collect authorized relevant Slack messages.
5. Record incident-related events.
6. Notify the appropriate participants.

Respect Slack scopes, channel membership, privacy and data retention requirements.

### C. GitHub Integration

Implement GitHub OAuth or GitHub App integration.

Support webhooks for:

- Pull request opened.
- Pull request merged.
- Push events.
- Deployment events.
- Deployment status changes.

Persist:
- Repository name.
- Branch.
- Commit SHA.
- PR number.
- PR title.
- Author.
- Merge timestamp.
- Deployment timestamp.
- Deployment environment.

Support multiple repositories per organization.

Webhook processing must be idempotent and signature-verified.

### D. Incident Management

Every incident should have:

- Unique incident ID.
- Organization ID.
- Incident title.
- Description.
- Severity: P1, P2, P3, P4.
- Status: OPEN, INVESTIGATING, MITIGATED, RESOLVED.
- Started timestamp.
- Resolved timestamp.
- Slack channel.
- Assigned incident owner.
- Associated services.
- Related GitHub repositories.

Provide an incident history page.

### E. Incident Timeline Engine

Build an event correlation engine.

For each incident:
1. Establish an incident time window.
2. Retrieve relevant Slack messages.
3. Retrieve GitHub pull requests.
4. Retrieve deployments.
5. Identify relevant commits.
6. Normalize timestamps.
7. Correlate events using repository, service, time and other available metadata.
8. Produce a chronological timeline.

Initially use a configurable lookback period of 60 minutes.

Make the time window configurable.

Do not assume that temporal proximity alone proves causation.

Each timeline entry must retain its source and original reference.

### F. AI Investigation Engine

This is the most important product feature.

Use the Claude API.

The AI should receive structured incident context rather than an unrestricted dump of all collected messages.

Input:
- Incident description.
- Severity.
- Incident start time.
- Slack messages.
- GitHub PRs.
- Deployment events.
- Relevant commits.
- Available operational events.

Generate the following:

1. Incident summary.
2. Chronological timeline.
3. Potential root causes.
4. Supporting evidence.
5. Contradicting evidence.
6. Confidence level and rationale.
7. Recommended debugging steps.
8. Potential mitigation strategies.
9. Missing information required for investigation.
10. Suggested preventive actions.

Output must be structured JSON.

Example:

{
  "summary": "Payment API experienced elevated HTTP 500 errors.",
  "hypotheses": [
    {
      "description": "A recent deployment may have introduced a database connection issue.",
      "confidence": "MEDIUM",
      "supportingEvidence": [
        "Deployment occurred shortly before error-rate increase.",
        "Database connection errors were reported in Slack."
      ],
      "contradictingEvidence": [],
      "recommendedActions": [
        "Inspect connection pool configuration.",
        "Compare metrics before and after deployment."
      ]
    }
  ],
  "missingInformation": [
    "Database connection pool metrics."
  ]
}

The above is illustrative. Never generate fake evidence.

Important AI requirements:
- Clearly separate observed facts from inferred hypotheses.
- Include source references for evidence.
- Never fabricate logs, deployments, messages or metrics.
- Allow engineers to accept, reject or edit hypotheses.
- Never automatically execute remediation.
- Handle API failures and malformed JSON.
- Track model, prompt version, token usage and cost.
- Protect against prompt injection in Slack messages and repository metadata.

### G. Postmortem Generator

Generate a structured postmortem with:

1. Incident overview.
2. Customer impact.
3. Detection method.
4. Incident timeline.
5. Technical root cause.
6. Contributing factors.
7. Resolution.
8. What went well.
9. What went wrong.
10. Lessons learned.
11. Corrective action items.

Allow users to edit and approve the postmortem.

Support Markdown export.

### H. Action Item Management

Allow managers and engineers to create action items.

Fields:
- Title.
- Description.
- Owner.
- Due date.
- Priority.
- Status.
- Linked incident.

Statuses:
- OPEN
- IN_PROGRESS
- COMPLETED
- CANCELLED

Display overdue and incomplete action items.

---

## 6. SaaS Dashboard

Create a polished, professional dashboard.

Design inspiration:
- Linear.
- Incident.io.
- Vercel.
- Datadog.

Use a clean, modern dark/light theme.

Avoid excessive gradients, unnecessary animations and clutter.

### Dashboard overview

Display:
- Total incidents.
- Active incidents.
- Incidents by severity.
- Mean time to acknowledge, when data exists.
- Mean time to resolve, when data exists.
- Incidents over time.
- Pending action items.
- Recent investigations.

### Main navigation

1. Overview.
2. Incidents.
3. Investigations.
4. Postmortems.
5. Action Items.
6. Integrations.
7. Team.
8. Billing.
9. Settings.

### Incident detail page

This is the most important UI screen.

Include:

- Incident metadata.
- Status and severity.
- Chronological timeline.
- Slack discussion references.
- GitHub PRs and deployment history.
- AI investigation summary.
- Root-cause hypotheses.
- Supporting and contradicting evidence.
- Recommended actions.
- Postmortem.
- Action item tracking.

The UI should make it easy to distinguish actual evidence from AI-generated assumptions.

---

## 7. Multi-Tenancy and Security

Implement proper SaaS security.

Requirements:

- Organization-level data isolation.
- Secure authentication.
- Encrypted OAuth tokens.
- Secure webhook signature validation.
- Input validation.
- Rate limiting.
- Audit logs.
- Role-based access control.
- Secure secret management.
- Data deletion workflows.
- Configurable data retention.
- Protection against cross-tenant access.
- Protection against SSRF and injection attacks.

Never log OAuth tokens or sensitive credentials.

Use least-privilege integration permissions.

Document all data shared with Anthropic.

---

## 8. Billing and Monetization

Integrate Stripe initially for international subscriptions.

Proposed plans:

FREE:
- 2 investigations/month.
- Basic incident timeline.
- Slack + GitHub.

STARTER — $29/month:
- 15 investigations/month.
- AI investigation.
- Postmortem generation.
- Action item tracking.

GROWTH — $79/month:
- 60 investigations/month.
- Multiple repositories.
- Advanced investigation.
- Investigation history.

BUSINESS — $149/month:
- 200 investigations/month.
- Multiple workspaces.
- Advanced reporting.
- Priority support.

Requirements:
- Subscription creation.
- Plan management.
- Usage tracking.
- Monthly usage reset.
- Billing history.
- Stripe webhook handling.
- Subscription cancellation.
- Feature-level entitlements.

Do not rely solely on frontend checks for subscription enforcement.

---

## 9. Database Design

Design normalized PostgreSQL tables for:

- users
- organizations
- organization_members
- integrations
- incidents
- incident_events
- github_repositories
- github_pull_requests
- github_deployments
- slack_messages
- investigations
- investigation_hypotheses
- investigation_evidence
- postmortems
- action_items
- subscriptions
- usage_records
- audit_logs

Requirements:
- Proper foreign keys.
- Indexes for frequently queried columns.
- Organization-level scoping.
- Created and updated timestamps.
- Idempotency keys for external events.
- Appropriate JSONB usage for flexible provider payloads.

Generate database migrations using Liquibase.

---

## 10. API Design

Create REST APIs for:

Authentication:
- POST /api/v1/auth/register
- POST /api/v1/auth/login
- POST /api/v1/auth/logout

Organizations:
- GET /api/v1/organizations
- POST /api/v1/organizations
- GET /api/v1/organizations/{id}

Incidents:
- POST /api/v1/incidents
- GET /api/v1/incidents
- GET /api/v1/incidents/{id}
- PATCH /api/v1/incidents/{id}
- POST /api/v1/incidents/{id}/resolve

Investigations:
- POST /api/v1/incidents/{id}/investigate
- GET /api/v1/incidents/{id}/investigations
- GET /api/v1/investigations/{id}

Postmortems:
- POST /api/v1/incidents/{id}/postmortem
- GET /api/v1/incidents/{id}/postmortem
- PATCH /api/v1/postmortems/{id}

Integrations:
- GET /api/v1/integrations
- POST /api/v1/integrations/slack/connect
- POST /api/v1/integrations/github/connect

Billing:
- GET /api/v1/billing/subscription
- POST /api/v1/billing/checkout
- POST /api/v1/billing/cancel

Document APIs using OpenAPI/Swagger.

---

## 11. Testing Requirements

Implement:

- Unit tests.
- Repository tests.
- Integration tests.
- API tests.
- Authentication tests.
- Multi-tenant isolation tests.
- Webhook signature validation tests.
- Idempotency tests.
- Claude response parsing tests.
- Slack integration tests.
- GitHub integration tests.

Use mock external APIs in tests.

Create realistic test data for incidents, Slack messages, GitHub PRs and deployments.

Ensure tests do not require actual Slack, GitHub or Anthropic credentials.

---

## 12. Development Workflow

Do not attempt to build everything in one response.

Follow this order:

PHASE 1:
- Analyze requirements.
- Identify missing requirements.
- Produce PRD.md.
- Produce DESIGN.md.
- Produce ARCHITECTURE.md.
- Produce database ER diagram.
- Define API contracts.

PHASE 2:
- Initialize backend.
- Initialize frontend.
- Configure Docker Compose.
- Configure PostgreSQL.
- Configure Redis.
- Configure Liquibase.
- Implement authentication and organizations.

PHASE 3:
- Implement incident management.
- Implement Slack integration.
- Implement GitHub integration.
- Implement event ingestion.
- Implement timeline correlation.

PHASE 4:
- Implement Claude integration.
- Implement investigation engine.
- Implement evidence references.
- Implement postmortem generation.
- Implement action item management.

PHASE 5:
- Implement React dashboard.
- Implement billing.
- Add testing.
- Add monitoring and logging.
- Prepare deployment.

After each phase:
1. Explain what was implemented.
2. List files created or modified.
3. Run relevant tests.
4. Fix compilation and test failures.
5. Identify remaining gaps.
6. Update documentation.

Do not claim a feature is implemented if it is only mocked or represented by a placeholder.

---

## 13. MVP Constraints

The initial MVP must prioritize:

1. Slack integration.
2. GitHub integration.
3. Incident creation.
4. Event collection.
5. Timeline reconstruction.
6. AI investigation.
7. Evidence-backed hypotheses.
8. Postmortem generation.

Defer:
- Datadog integration.
- Grafana integration.
- Jira and Linear.
- On-call scheduling.
- Automatic remediation.
- Complex enterprise reporting.
- Kubernetes-native agents.

Keep the initial infrastructure and operating expenses below $500.

Prefer free and low-cost infrastructure where practical.

Do not sacrifice security, data isolation or integration correctness to meet the budget.

---

## 14. Expected Deliverables

I expect:

1. Complete source code.
2. Backend application.
3. Frontend application.
4. Database migrations.
5. Docker Compose configuration.
6. Environment variable example file.
7. API documentation.
8. Architecture documentation.
9. Unit and integration tests.
10. Local development instructions.
11. Deployment instructions.
12. Slack application setup guide.
13. GitHub application setup guide.
14. Anthropic API setup guide.
15. Pricing and subscription implementation.
16. Security checklist.

---

## 15. Final Instructions

Think like a startup CTO, not just a code generator.

Prioritize:
- Reliability.
- Simplicity.
- Security.
- Low operating costs.
- Fast iteration.
- Real customer value.

Do not overengineer.

Do not introduce Kafka or Kubernetes unless there is a demonstrated need.

Do not invent successful integrations or claim that an API works without testing it.

If external credentials are unavailable, build the integration interfaces and mocked tests, clearly documenting what requires real credentials.

Start with PHASE 1.

First produce the PRD, architecture, database design, API contracts and implementation roadmap.

Wait for my approval before moving to the next phase.
