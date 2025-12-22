# Serverless & Artifact Registry Architecture: Designing Modern Cloud-Native Workloads on Google Cloud

> **Target Audience:** Professional Cloud Architect exam preparation  
> **Reading Time:** 10-12 minutes  
> **Exam Weight:** High (Serverless, CI/CD, registry, ~15-20% of exam)

---

## Table of Contents

- [Serverless \& Artifact Registry Architecture: Designing Modern Cloud-Native Workloads on Google Cloud](#serverless--artifact-registry-architecture-designing-modern-cloud-native-workloads-on-google-cloud)
  - [Table of Contents](#table-of-contents)
  - [1. Introduction: The Serverless Paradigm](#1-introduction-the-serverless-paradigm)
  - [2. Cloud Run: Managed Containers at Scale](#2-cloud-run-managed-containers-at-scale)
    - [2.1 Stateless Containers \& Autoscaling](#21-stateless-containers--autoscaling)
    - [2.2 Concurrency, CPU/Memory, and Cold Starts](#22-concurrency-cpumemory-and-cold-starts)
    - [2.3 Configuration, Secrets, and Environment](#23-configuration-secrets-and-environment)
    - [2.4 Authentication, Authorization, and Networking](#24-authentication-authorization-and-networking)
    - [2.5 Load Balancing, NEGs, and Custom Domains](#25-load-balancing-negs-and-custom-domains)
    - [2.6 Traffic Splitting, High Availability, and Observability](#26-traffic-splitting-high-availability-and-observability)
    - [2.7 Jobs vs Services](#27-jobs-vs-services)
  - [3. Artifact Registry \& Container Registry](#3-artifact-registry--container-registry)
    - [3.1 Repository Types and Deployment Pipelines](#31-repository-types-and-deployment-pipelines)
    - [3.2 Tagging, Retention, and Cleanup](#32-tagging-retention-and-cleanup)
    - [3.3 IAM, Vulnerability Scanning, and Provenance](#33-iam-vulnerability-scanning-and-provenance)
  - [4. App Engine: Platform as a Service](#4-app-engine-platform-as-a-service)
    - [4.1 Standard vs Flexible, Scaling, and Service Architecture](#41-standard-vs-flexible-scaling-and-service-architecture)
    - [4.2 Versioning, Traffic Splitting, and Routing](#42-versioning-traffic-splitting-and-routing)
    - [4.3 State, Memcache, Task Queues, and Cron Jobs](#43-state-memcache-task-queues-and-cron-jobs)
    - [4.4 Security, Custom Domains, and Observability](#44-security-custom-domains-and-observability)
  - [5. Cloud Functions: Event-Driven Compute](#5-cloud-functions-event-driven-compute)
    - [5.1 Gen 1 vs Gen 2, Triggers, and Runtime](#51-gen-1-vs-gen-2-triggers-and-runtime)
    - [5.2 Cold Starts, Env Vars, and Secrets](#52-cold-starts-env-vars-and-secrets)
    - [5.3 IAM, Networking, and Error Handling](#53-iam-networking-and-error-handling)
    - [5.4 Observability and Deployment Strategies](#54-observability-and-deployment-strategies)
  - [6. CI/CD Patterns for Serverless](#6-cicd-patterns-for-serverless)
  - [7. Anti-Patterns and Exam Triggers](#7-anti-patterns-and-exam-triggers)
  - [8. Conclusion: Architecting for Agility and Security](#8-conclusion-architecting-for-agility-and-security)

---

## 1. Introduction: The Serverless Paradigm

Serverless computing abstracts away infrastructure management, enabling teams to focus on code and business logic. Google Cloud offers a spectrum of serverless and managed services—Cloud Run, App Engine, Cloud Functions—plus Artifact Registry for secure, scalable artifact storage. The PCA exam tests your ability to choose the right service, design for scale, security, and cost, and implement modern CI/CD patterns.

---

## 2. Cloud Run: Managed Containers at Scale

Cloud Run is a fully managed platform for running stateless containers. It combines the flexibility of containers with the simplicity of serverless.

### 2.1 Stateless Containers & Autoscaling
- Each request is handled in isolation; no local state between requests.
- Scales from zero to thousands of instances based on demand.
- Cold starts can occur when scaling from zero; design for fast startup.

### 2.2 Concurrency, CPU/Memory, and Cold Starts
- Default concurrency: 80 requests/container (tune for latency/cost).
- CPU/memory configurable per service (up to 32 vCPU, 128GB RAM).
- Concurrency and resource settings impact cold start and cost.

### 2.3 Configuration, Secrets, and Environment
- Set environment variables at deploy; supports Secret Manager for sensitive data.
- Use configuration for per-environment settings (dev, prod).

### 2.4 Authentication, Authorization, and Networking
- Supports IAM (invoker roles), OIDC tokens, or unauthenticated access.
- VPC Connector enables private egress; restrict ingress to internal/external.
- Integrates with Network Endpoint Groups (NEG) for load balancer.

### 2.5 Load Balancing, NEGs, and Custom Domains
- Integrates with HTTPS Load Balancer for custom domains, SSL, and global routing.
- NEGs allow Cloud Run to be a backend for load balancers.

### 2.6 Traffic Splitting, High Availability, and Observability
- Route % of traffic to different revisions (blue/green, canary deploys).
- Multi-zone by default; use GCLB for global HA.
- Cloud Logging, Monitoring, and Trace for observability.

### 2.7 Jobs vs Services
- Cloud Run Jobs: Run-to-completion workloads (batch, ETL).
- Cloud Run Services: HTTP/gRPC endpoints, always-on or scale-to-zero.

---

## 3. Artifact Registry & Container Registry

Artifact Registry is the successor to Container Registry, supporting Docker, Maven, npm, PyPI, and more.

### 3.1 Repository Types and Deployment Pipelines
- Supports Docker, Maven, npm, PyPI, APT, YUM.
- Typical pipeline: Build (Cloud Build), scan, tag, push, deploy.

### 3.2 Tagging, Retention, and Cleanup
- Use semantic versioning, commit SHA, or latest for tags.
- Set retention/cleanup policies for old or untagged images.

### 3.3 IAM, Vulnerability Scanning, and Provenance
- Fine-grained IAM per repo; restrict push/pull by team/project.
- Built-in vulnerability scanning for containers; block deploy on critical CVEs.
- Image signing and provenance for supply chain security (Binary Authorization).

---

## 4. App Engine: Platform as a Service

App Engine provides a fully managed PaaS for web/mobile apps.

### 4.1 Standard vs Flexible, Scaling, and Service Architecture
- Standard: Sandboxed, scales to zero, supports limited runtimes.
- Flexible: Custom runtime, manual/basic/auto scaling, more control.
- Multiple services, versions, and traffic splitting supported.

### 4.2 Versioning, Traffic Splitting, and Routing
- Deploy multiple versions, split traffic for canary/blue-green.
- URL mapping and dispatch.yaml for service routing.

### 4.3 State, Memcache, Task Queues, and Cron Jobs
- Stateless by default; use Datastore, Memcache, Cloud SQL for state.
- Task Queues for background jobs; Cron Jobs for scheduled tasks.

### 4.4 Security, Custom Domains, and Observability
- IP firewall rules, custom domains, SSL management.
- Integrated logging, monitoring, and local development tools.

---

## 5. Cloud Functions: Event-Driven Compute

Cloud Functions is a serverless FaaS for event-driven workloads.

### 5.1 Gen 1 vs Gen 2, Triggers, and Runtime
- Gen 2: GKE/Cloud Run backend, more triggers, better scaling.
- Supports HTTP, Pub/Sub, Storage, Firestore, Eventarc, etc.
- Multiple runtimes: Node.js, Python, Go, Java, .NET, Ruby, PHP.

### 5.2 Cold Starts, Env Vars, and Secrets
- Gen 2 has faster cold starts, but still present.
- Use scheduled triggers to keep warm if needed.
- Set env vars at deploy; integrate with Secret Manager.

### 5.3 IAM, Networking, and Error Handling
- Invoker roles, OIDC for auth; VPC Connector for private egress.
- Retries, dead-letter topics, try/catch for error handling.

### 5.4 Observability and Deployment Strategies
- Cloud Logging, Monitoring, Error Reporting.
- Deploy via gcloud, Terraform, CI/CD; blue/green, canary via traffic splitting (Gen 2).

---

## 6. CI/CD Patterns for Serverless
- Use Cloud Build for build/test/deploy automation.
- Triggers for push, PR, or schedule.
- YAML pipelines with substitutions for environment/config.
- Cloud Deploy for progressive delivery, approvals, rollbacks.
- Integrate vulnerability scanning and image signing in pipeline.

---


---

## 7. Cloud Deployment Manager: Infrastructure as Code (IaC)

Cloud Deployment Manager (CDM) is Google Cloud's native IaC tool, enabling declarative management of cloud resources using YAML, Jinja2, or Python templates.

### 7.1 CDM Intro & Core Concepts
- **Declarative Templates:** Define resources and their properties in YAML or Jinja2/Python templates.
- **Resource Types:** Supports most GCP resources (Compute, Storage, Networking, IAM, etc.).
- **Configuration Files:** Group templates and variables for reusable deployments.
- **Preview Mode:** See planned changes before applying.

### 7.2 Migration Use Case
- **Lift-and-Shift:** Migrate existing manual or scripted deployments to declarative templates for repeatability.
- **Multi-Environment:** Use parameterized templates for dev, test, prod.
- **Integration:** Combine with CI/CD for automated, version-controlled infrastructure changes.

### 7.3 Potential Challenges
- **Resource Coverage:** Not all new GCP features are immediately supported.
- **Complexity:** Large deployments can become complex; modularize templates.
- **State Management:** No built-in drift detection; use preview and audit logs.
- **Alternatives:** Consider Terraform for multi-cloud or advanced state management.

**Exam Triggers:**
- Need for repeatable, version-controlled GCP infrastructure → CDM
- Declarative, template-driven deployments → CDM
- Multi-environment, parameterized deployments → CDM
- If asked about IaC migration or GCP-native IaC, prefer CDM unless multi-cloud is required

---

## 8. Anti-Patterns and Exam Triggers

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

## 9. Conclusion: Architecting for Agility and Security

Serverless and managed registry services on Google Cloud enable rapid, secure, and scalable application delivery. The PCA exam rewards architects who understand when to use each service, how to secure and monitor workloads, and how to design for cost and operational excellence. Focus on decision triggers, anti-patterns, and CI/CD best practices to maximize your exam performance and real-world impact.
