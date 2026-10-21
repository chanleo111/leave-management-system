\# Leave Management System



A cloud-ready, enterprise-oriented leave request and approval platform for SMEs.



The system centralises leave applications, manager approvals, leave balances, audit trails and HR reporting. It is designed with a modular monolith architecture, Microsoft Entra ID authentication, role-based access control, observability and infrastructure-as-code.



!\[Build](https://img.shields.io/badge/build-passing-brightgreen)

!\[License](https://img.shields.io/badge/license-MIT-blue)

!\[Cloud](https://img.shields.io/badge/cloud-Azure-0078D4)

!\[Database](https://img.shields.io/badge/database-PostgreSQL-336791)

!\[Architecture](https://img.shields.io/badge/architecture-modular\_monolith-informational)



\## Business Problem



Many organisations still manage leave through email, spreadsheets or paper forms. This leads to unclear approval status, inaccurate leave balances, missing audit evidence and limited management reporting.



\## Proposed Solution



This project provides a secure leave management workflow with:



\- Employee self-service leave applications

\- Configurable manager approval workflow

\- Automatic leave balance validation

\- Role-based and attribute-based access control

\- Complete approval and audit history

\- HR reporting by department, leave type and period



\## Key Features



\- Employee, Manager, HR and Admin roles

\- Annual, sick and special leave types

\- Leave balance validation before submission

\- Approval, rejection and comment history

\- Audit logging for critical actions

\- CSV export for HR reporting

\- Docker-based local development

\- Azure-ready deployment architecture



\## Project Status



| Area | Status |

|---|---|

| Requirements \& design | Planned |

| Database schema | Planned |

| Backend MVP | Planned |

| Frontend MVP | Planned |

| Azure deployment | Planned |

| Security testing | Planned |



\## Architecture



!\[System Architecture](docs/diagrams/architecture.png)



The solution uses a \*\*modular monolith\*\* to keep the leave, approval, notification, reporting and audit domains cohesive, while maintaining clear module boundaries for future extraction into separate services.



```text

User / Corporate Portal

&#x20;       ↓ HTTPS

Azure API Management

&#x20;       ↓

Leave Management API

├── Identity \& Access

├── Leave Application

├── Approval Workflow

├── Notification

├── Reporting

└── Audit \& Compliance

&#x20;       ↓

Azure Database for PostgreSQL

&#x20;       ↓

Azure Blob Storage

```



\## Technology Stack



| Layer | Technology | Rationale |

|---|---|---|

| Frontend | React + TypeScript | Type safety, maintainable component-based UI |

| Backend | NestJS / Node.js | Modular structure, dependency injection, OpenAPI support |

| Database | Azure Database for PostgreSQL | Relational integrity, transactional workflow, managed backup and HA |

| Identity | Microsoft Entra ID | Enterprise SSO, MFA and centralised identity governance |

| API Gateway | Azure API Management | Authentication, rate limiting, versioning and monitoring |

| File Storage | Azure Blob Storage | Secure storage for supporting documents |

| Messaging | Azure Service Bus | Reliable asynchronous notifications and retry handling |

| Secrets | Azure Key Vault | Central secret management; no secrets in source control |

| IaC | Terraform / Bicep | Repeatable environments and auditable infrastructure changes |

| CI/CD | GitHub Actions | Automated test, build, security scan and deployment |

| Observability | Azure Monitor + Application Insights | Logs, metrics, traces and alerting |



\## Enterprise Design Principles



This project is aligned with the Azure Well-Architected Framework pillars: reliability, security, cost optimization, operational excellence and performance efficiency.



| Pillar | Design Approach |

|---|---|

| Reliability | Health checks, retry policy, managed database HA, backup and recovery runbook |

| Security | Entra ID SSO, MFA, RBAC/ABAC, least privilege, private networking, secret management |

| Cost Optimization | PaaS services, autoscaling, right-sized database tier, scheduled non-production shutdown |

| Operational Excellence | IaC, CI/CD, environment promotion, structured logging, alerting and runbooks |

| Performance Efficiency | Read replicas for reporting, Redis caching, database indexing and query optimisation |



\## Security Design



\- Authentication through Microsoft Entra ID with OAuth 2.0 / OpenID Connect

\- Multi-factor authentication for privileged roles

\- Role-based access control: Employee, Manager, HR, Admin

\- Attribute-based rules: managers can approve only their department's requests

\- TLS 1.2+ for data in transit

\- Encryption at rest for database and Blob Storage

\- Secrets stored in Azure Key Vault, accessed via managed identity

\- Private endpoints for database and storage where required

\- Audit logs for approval, rejection, policy changes and privileged operations

\- Dependency, container and infrastructure scanning in CI/CD



\## Observability



\- Structured JSON logs with correlation ID

\- Application Insights traces for API and database dependencies

\- Dashboards for request rate, error rate, latency and queue depth

\- Alerts for 5xx rate, P95 latency, failed logins and database saturation



\## Reliability and Operations



| Metric | Target |

|---|---:|

| Availability SLO | 99.9% |

| RTO | 4 hours |

| RPO | 15 minutes |

| Deployment strategy | Staging slot with automated smoke tests |

| Backup | Daily automated backup with point-in-time recovery |

| Monitoring | API latency, error rate, queue depth, database health |



Failure handling:



\- Notification failure is retried through Azure Service Bus and moved to a dead-letter queue after repeated failure

\- Database failover is supported by Azure Database for PostgreSQL high availability

\- Application uses exponential backoff for transient dependency failures

\- Infrastructure can be recreated through Terraform/Bicep



\## Database Design



Core entities:



\- `employees`

\- `departments`

\- `leave\_types`

\- `leave\_applications`

\- `approval\_history`

\- `audit\_logs`



Key design decisions:



\- `approval\_history` preserves the full approval trail

\- `row\_version` supports optimistic concurrency to prevent duplicate approvals

\- Leave balances use `NUMERIC`, not floating-point types

\- Sensitive attachments are stored in a private Blob container

\- Reporting queries are separated from transactional workloads



See \[Database Design](docs/05-database-design.md).



\## API Design



The API follows REST conventions and is documented with OpenAPI 3.1.



| Method | Endpoint | Description |

|---|---|---|

| `POST` | `/api/v1/auth/login` | Authenticate user |

| `GET` | `/api/v1/me/leave-balances` | View current leave balances |

| `POST` | `/api/v1/leave-applications` | Submit leave request |

| `GET` | `/api/v1/leave-applications` | List leave requests |

| `PATCH` | `/api/v1/leave-applications/{id}/decision` | Approve or reject request |

| `GET` | `/api/v1/reports/leave-summary` | HR leave summary report |



Standards:



\- Versioned endpoints: `/api/v1/`

\- Cursor or page-based pagination

\- `Idempotency-Key` for leave submission

\- `X-Correlation-ID` for distributed tracing

\- RFC 7807 Problem Details for error responses



See \[API Design](docs/06-api-design.md).



\## Getting Started



\### Prerequisites



\- Node.js 20+

\- Docker Desktop

\- PostgreSQL 16

\- Azure CLI (optional, for cloud deployment)



\### Run locally



```bash

git clone \[https://github.com/](https://github.com/)<your-username>/leave-management-system.git

cd leave-management-system

cp .env.example .env

docker compose up --build

```



Access:



\- Frontend: http://localhost:3000

\- API: http://localhost:8080/api/v1

\- OpenAPI docs: http://localhost:8080/api-docs



\### Run tests



```bash

npm run test

npm run test:e2e

```



\## Project Roadmap



\- \[x] Business requirements and user stories

\- \[x] Approval workflow design

\- \[x] Database ERD and schema

\- \[x] API contract design

\- \[x] Azure architecture design

\- \[ ] Authentication module

\- \[ ] Leave application module

\- \[ ] Manager approval workflow

\- \[ ] HR reporting dashboard

\- \[ ] Terraform deployment

\- \[ ] Load and security testing



\## Documentation



| Document | Purpose |

|---|---|

| \[Project Overview](docs/01-project-overview.md) | Business problem, users and MVP scope |

| \[Requirements](docs/02-requirements.md) | Functional and non-functional requirements |

| \[Workflow](docs/04-workflow.md) | Leave approval process |

| \[Database Design](docs/05-database-design.md) | ERD, schema and data rules |

| \[API Design](docs/06-api-design.md) | REST contract and error handling |

| \[Cloud Architecture](docs/07-cloud-architecture.md) | Azure components and network design |

| \[Security \& Compliance](docs/08-security-and-compliance.md) | Threat model and controls |

| \[Operations](docs/10-observability-and-slo.md) | SLO, alerting and runbooks |

| \[ADRs](docs/13-architecture-decision-records/) | Key architecture decisions |



\## Architecture Decision Records



Key decisions are recorded in ADRs, including alternatives considered and trade-offs.



| ADR | Decision | Status |

|---|---|---|

| \[ADR-001](docs/13-architecture-decision-records/ADR-001-modular-monolith.md) | Use modular monolith instead of microservices | Accepted |

| \[ADR-002](docs/13-architecture-decision-records/ADR-002-postgresql.md) | Use PostgreSQL for transactional data | Accepted |

| \[ADR-003](docs/13-architecture-decision-records/ADR-003-entra-id.md) | Use Entra ID for enterprise identity | Accepted |

| \[ADR-004](docs/13-architecture-decision-records/ADR-004-azure-paas.md) | Use Azure PaaS rather than self-managed VMs | Accepted |



\## License



MIT License.

