# Serverless & Container Registry Cheat Sheet – PCA Exam Focus

> **Exam Strategy:** Know the serverless spectrum, deployment patterns, security, and cost triggers for Cloud Run, App Engine, Cloud Functions, and Artifact Registry.
> **Time to review:** 8-10 minutes

---

## 1. Cloud Run

| Feature | Key Points |
|---------|------------|
| **Abstraction** | Fully managed serverless containers (HTTP, gRPC) |
| **Stateless** | Each request handled in isolation; no local state |
| **Autoscaling** | Scales to zero, up to 1,000+ instances; cold starts possible |
| **Concurrency** | Default 80 requests/container; can tune for latency/cost |
| **CPU/Memory** | Configurable per service (up to 32 vCPU, 128GB RAM) |
| **Environment** | Set env vars at deploy; supports Secret Manager integration |
| **Secrets** | Use Secret Manager for env secrets (no plaintext in code) |
| **Auth** | Supports IAM (invoker roles), OIDC tokens, unauthenticated if allowed |
| **Networking** | VPC Connector for private egress; supports NEGs for load balancer integration |
| **Ingress** | Restrict to internal, external, or both; integrates with HTTPS LB |
| **Custom Domains** | Map custom domains, manage SSL certs automatically |
| **Traffic Splitting** | Route % of traffic to different revisions (blue/green, canary) |
| **HA** | Multi-zone by default; no regional failover (use GCLB for global) |
| **Observability** | Cloud Logging, Monitoring, Trace; request/latency metrics |
| **Jobs** | Run-to-completion workloads (batch, ETL); separate from services |

**Exam Triggers:**
- Stateless HTTP API, scale to zero → Cloud Run
- Need custom domain, traffic splitting → Cloud Run
- Private egress, VPC access → VPC Connector
- Secure secrets → Secret Manager

---

## 2. Container Registry & Artifact Registry

| Feature | Key Points |
|---------|------------|
| **Artifact Registry** | Successor to Container Registry; supports Docker, Maven, npm, PyPI, etc. |
| **Repository Types** | Docker (container), Maven, npm, PyPI, APT, YUM |
| **Deployment Pipeline** | Push image (Cloud Build), scan for vulnerabilities, tag, deploy |
| **Tagging** | Use semantic versioning, latest, commit SHA for traceability |
| **IAM** | Fine-grained access control per repo; supports GCP IAM roles |
| **Vulnerability Scanning** | Built-in for container images; block deploy on critical CVEs |
| **Retention/Cleanup** | Policies for old images, untagged images, cleanup automation |
| **Regional/Multiregional** | Choose for compliance, latency, DR |
| **Migration** | Tools to migrate from Container Registry to Artifact Registry |
| **Image Signing** | Binary Authorization, provenance for supply chain security |

**Exam Triggers:**
- Multi-language artifact storage → Artifact Registry
- Enforce image scanning → Vulnerability Scanning
- Restrict access by team/project → IAM policies
- Cleanup old images → Retention policies

---

## 3. App Engine

| Feature | Key Points |
|---------|------------|
| **Abstraction** | PaaS for web/mobile apps; Standard (sandboxed) & Flexible (custom runtime) |
| **Scaling** | Automatic (Standard), manual/basic (Flexible); scales to zero (Standard) |
| **Service Architecture** | Multiple services, versions, traffic splitting |
| **Versioning** | Deploy multiple versions, split traffic for canary/blue-green |
| **Dependency Mgmt** | App.yaml for config; requirements.txt, package.json, etc. |
| **State** | Stateless by default; use Datastore, Memcache, Cloud SQL for state |
| **Memcache** | In-memory cache for Standard; not in Flexible |
| **Task Queues** | Background jobs, async processing |
| **Cron Jobs** | Scheduled tasks via cron.yaml |
| **Request Routing** | URL mapping, dispatch.yaml for service routing |
| **Firewall** | IP allow/deny rules for app access |
| **Custom Domains** | Map domains, manage SSL certs |
| **Logging/Monitoring** | Integrated with Cloud Logging, Monitoring |
| **Local Dev** | Emulators for local testing (dev_appserver.py, etc.) |

**Exam Triggers:**
- Multi-version, traffic splitting → App Engine
- Need background jobs → Task Queues, Cron Jobs
- Custom domain, SSL → App Engine
- Stateful app → Use Cloud SQL, Datastore

---

## 4. Cloud Functions

| Feature | Key Points |
|---------|------------|
| **Gen 1 vs Gen 2** | Gen 2: GKE/Cloud Run backend, more triggers, better scaling |
| **Triggers** | HTTP, Pub/Sub, Storage, Firestore, Eventarc, etc. |
| **Runtime** | Node.js, Python, Go, Java, .NET, Ruby, PHP |
| **Cold Starts** | Gen 2 faster, but still present; keep warm with scheduled triggers |
| **Env Vars/Secrets** | Set at deploy; integrate with Secret Manager |
| **IAM** | Invoker roles, per-function IAM; OIDC for auth |
| **Networking** | VPC Connector for private egress; supports private IPs |
| **Error Handling** | Retries, dead-letter topics, try/catch in code |
| **Rollbacks** | Deploy previous version if needed |
| **Observability** | Cloud Logging, Monitoring, Error Reporting |
| **Deployment** | gcloud, Terraform, CI/CD; blue/green, canary via traffic splitting (Gen 2)

**Exam Triggers:**
- Event-driven, stateless → Cloud Functions
- Need HTTP endpoint, Pub/Sub, Storage trigger → Cloud Functions
- Secure secrets → Secret Manager
- Private egress → VPC Connector
- Rollback on error → Versioned deploys

---

## 5. Exam Anti-Patterns (What NOT to Do)

| Anti-Pattern | Why It's Wrong | PCA Solution |
|--------------|----------------|--------------|
| Store secrets in code | Security risk | Use Secret Manager |
| No traffic splitting | No canary/blue-green | Use traffic splitting features |
| No IAM on registry | Anyone can push/pull | Set IAM policies per repo |
| No image scanning | Vulnerable images | Enable vulnerability scanning |
| No retention policy | Registry fills up | Set cleanup policies |
| Use Standard App Engine for custom runtime | Not supported | Use Flexible or Cloud Run |
| No VPC Connector for private egress | Data exfiltration risk | Always use VPC Connector |
| Ignore cold starts | Latency spikes | Use concurrency, keep-warm patterns |

---

**Study Tip:** Focus on when to use each service, security best practices, and deployment patterns. PCA is about architecture, not just syntax!
