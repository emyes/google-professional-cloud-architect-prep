# Google Cloud Observability – PCA Cheat Sheet

> Focus: Architectural decisions, not configuration details  
> Exam mindset: Prefer managed, simple, SRE- and security-aligned solutions

---

## 1. Big Picture (Memorize This)

| Signal | GCP Service | Primary Use |
|-----|------------|-------------|
| Metrics | Cloud Monitoring | Alerts, SLOs, dashboards |
| Logs | Cloud Logging | Audit, security, forensics |
| Traces | Cloud Trace | Latency analysis |
| Errors | Error Reporting | App crashes & regressions |

Correlation flow (EXAM FAVORITE):
Alert → SLO → Trace → Logs (traceId) → Error Reporting

Security investigation flow:
Suspicious event → Audit Logs → Data Access Logs → Resource context → Forensics

---

## 2. Cloud Logging (Logs)

Cloud Logging is both:
- **An observability system**
- **A core security telemetry system**

It answers:
> Who did what, where, when, and from where?

---

### Core Components
- **Logs Explorer** → Interactive investigation (ops & security)
- **Logs Router** → Central routing control plane
- **Log Analytics** → Advanced SQL-style log analysis
- **Error Reporting** → Application exceptions

---

### Log Categories (EXAM GOLD)

#### Cloud Audit Logs
- **Admin Activity**
  - Always enabled
  - IAM changes, resource configuration
  - Cannot be disabled
- **Data Access**
  - Records reads/writes of user data
  - Often disabled by default
  - Required for compliance
- **Policy Denied**
  - Records denied access attempts
  - Useful for threat detection and misconfiguration analysis

#### Other Log Types
- **System Event**
  - Infrastructure lifecycle events
- **Application Logs**
  - App-generated (stdout/stderr, custom logs)

Compliance almost always requires:
> Admin Activity + Data Access logs

---

### Default Log Buckets (EXAM IMPORTANT)

Every project has **two system-managed log buckets**.

#### `_Required` Log Bucket
- Stores:
  - Admin Activity logs
  - System Event logs
  - Access Transparency logs
- Always enabled
- **Cannot be disabled or excluded**
- Retention is fixed
- Designed for:
  - Audit
  - Compliance
  - Security investigations

PCA rule:
> You cannot reduce cost by modifying `_Required`

---

#### `_Default` Log Bucket
- Stores:
  - Application logs
  - Data Access logs (if enabled)
  - Policy Denied logs
- Retention is configurable (30–3650 days)
- Logs can be:
  - Filtered
  - Excluded
  - Routed using sinks

PCA rule:
> Cost optimization focuses on `_Default`, not `_Required`

---

### Data Governance (VERY IMPORTANT)

- **Log Buckets** → Where logs live (inside Cloud Logging)
- **Log Views** → Who can see which logs (IAM enforcement)
- **Retention Policies** → How long logs are kept
- **Exclusion Filters** → Reduce ingestion cost (except `_Required`)

Mental model:
Buckets = storage  
Views = access control  
Retention = compliance  

---

### Log Buckets vs External Storage (SECURITY CLARITY)

**Log buckets are NOT Cloud Storage.**

| Capability | Log Buckets | Cloud Storage |
|---------|------------|---------------|
| Indexed & queryable | Yes | No |
| Real-time investigation | Excellent | Poor |
| Long-term archive | Limited | Excellent |
| Legal hold / immutability | Limited | Excellent |
| External sharing | Limited | Excellent |

PCA principle:
> Cloud Logging = operational & security visibility  
> External storage = compliance & archival

---

### Log Routing (Sinks)

A **log sink** is the ONLY mechanism that moves logs out of Cloud Logging.

Sinks:
- Filter logs
- Duplicate logs
- Route logs in near real time

**Common destinations**
- **BigQuery**
  - Security analytics
  - Trend analysis
  - Compliance reporting
- **Cloud Storage**
  - Long-term retention
  - Legal hold
- **Pub/Sub**
  - SIEM ingestion
  - Threat detection pipelines
- **Another Log Bucket**
  - Separation of duties
  - Different retention policies

Important:
> Sinks COPY logs — they do not remove them  
> (removal requires exclusion filters)

---

### Log Analytics vs BigQuery (EXAM CONFUSION ZONE)

- **Log Analytics**
  - SQL-like syntax
  - Optimized for operators and security engineers
  - Logs only
  - Short-to-medium time ranges

- **BigQuery**
  - Full SQL
  - Large historical datasets
  - Joins across datasets
  - BI dashboards, compliance reports

PCA decision rule:
> Investigate incidents → Log Analytics  
> Analyze trends or compliance → BigQuery sink

---

## 3. Cloud Monitoring (Metrics)

### Metrics Model
- Metric Types: GAUGE, DELTA, CUMULATIVE
- Monitored Resources
- Labels (control cardinality!)

High cardinality:
- Increases cost
- Reduces alert reliability

---

### Reliability Engineering (EXAM CRITICAL)

- **SLI** → What is measured
- **SLO** → Reliability target
- **Error Budget** → Allowed failure
- **Burn Rate Alerts** → User-impact driven alerts

PCA prefers:
> SLO-based alerts over infrastructure metrics

---

### Health & Availability
- **Uptime Checks** → External availability
- **Synthetic Monitoring** → End-user journeys
- **Dashboards** → Visualization, not alerting

---

### Managed Prometheus
- Native PromQL
- Deep GKE integration
- Managed collection preferred

---

## 4. Cloud Trace (Distributed Tracing)

- Traces → Requests
- Spans → Service operations
- Identifies latency bottlenecks
- Auto-enabled for App Engine, Cloud Run
- OpenTelemetry compatible

**Sampling strategies:**
- High traffic → Sample (1% - 10%)
- Low traffic → Trace all requests

PCA rule:
> Use Trace for latency, NOT logs

---

## 5. Cloud Profiler

Continuous profiling for CPU and memory in production.

| What | When to Use |
|------|------------|
| CPU profiling | Identify expensive functions |
| Heap profiling | Memory leaks, allocation patterns |
| Wall time | Blocked threads, I/O waits |

Key points:
- Minimal overhead (<5%)
- Works in production
- Languages: Java, Go, Python, Node.js

PCA scenario:
> App slow but no obvious latency spike → Profile it

---

## 6. Cloud Debugger (Snapshot Debugger)

Debug production code without stopping it.

- **Snapshots** → Capture state at a line
- **Log points** → Add temporary logs
- No code changes or restarts
- Automatic timeout (prevents abuse)

PCA rule:
> Production bug you can't reproduce locally → Debugger

---

## 7. Error Reporting

- Aggregates uncaught exceptions
- Groups errors
- Tracks by version
- Alerts on new error types

Primary use:
> Detect regressions after deployments

---

## 6. Cost Management (Indirectly Tested)

Primary cost drivers:
- Log ingestion volume
- Metric cardinality
- Retention duration

Controls:
- Exclusion filters (except `_Required`)
- Label discipline
- Tiered retention strategy
- Export cold data to GCS

---

## 8. Logs-Based Metrics (Cost Efficiency)

Create custom metrics from log patterns.

**Use cases:**
- Business KPIs (orders, signups)
- Error rates by type
- Custom SLIs

**Why use them:**
- Cheaper than high-cardinality native metrics
- Leverage existing logs
- Distribution metrics (percentiles)

PCA rule:
> Data already in logs → Don't duplicate as custom metrics

---

## 9. Multi-Project Observability

### Metrics Scopes (formerly Stackdriver Workspaces)
- Monitor multiple projects from one dashboard
- Up to 375 monitored projects per scope
- Scoping project = host

### Log Aggregation
- **Aggregated sink** → Collect logs from all projects in org/folder
- Use case: Security team needs all audit logs

PCA pattern:
> Centralized security → Org-level sink to dedicated project

---

## 10. Hybrid & Migration Patterns

### Ops Agent (Current)
- Replaces legacy Monitoring + Logging agents
- Single unified agent
- YAML configuration
- Better performance

### BindPlane
- Third-party log/metric collection
- AWS, Azure, on-prem
- Protocol translation

PCA scenario:
> Migrating from on-prem → BindPlane during transition

---

## 11. Integration & Notifications

### Notification Channels
- Email, SMS, Slack, PagerDuty
- Webhooks for custom integrations
- Mobile app (Cloud Console app)

### SIEM Integration
- Pub/Sub sink → Splunk, Chronicle, QRadar
- Real-time threat detection

PCA rule:
> Security team uses SIEM → Pub/Sub sink required

---

## 12. GKE/Anthos-Specific Observability

### Service Mesh (Anthos Service Mesh)
- Automatic Trace/Metrics for all services
- Service topology visualization
- Golden signals (latency, traffic, errors, saturation)

### GKE Monitoring
- System metrics (auto-collected)
- Workload metrics (opt-in)
- Control plane logs

PCA decision:
> Microservices on GKE → Enable GKE Monitoring + ASM

---

## 13. Security Observability (Deep Dive)

### VPC Flow Logs
- Network traffic analysis
- Security forensics
- Cost: High volume

**Sampling:**
- 100% = forensics
- 10% = trends

### Firewall Rules Logging
- Which rules matched
- Blocked vs allowed
- Integrates with Cloud Armor

### Access Transparency Logs
- Google employee access to your data
- Compliance requirement (often)
- Only in `_Required` bucket

PCA compliance trigger:
> "Need proof of who accessed data, including Google" → Access Transparency

---

## 14. OpenTelemetry (Modern Standard)

Vendor-neutral instrumentation.

| Native GCP | OpenTelemetry |
|-----------|---------------|
| Easier setup | Portable |
| GCP-optimized | Multi-cloud |
| Auto-instrumentation | Requires config |

PCA scenario:
> Multi-cloud or vendor flexibility → OpenTelemetry  
> GCP-only, speed to market → Native instrumentation

---

## 15. Cost Optimization Strategies

### Log Cost Controls
1. Exclude verbose/debug logs at router
2. Sample high-volume logs (VPC Flow)
3. Reduce Data Access log scope
4. Shorter retention for `_Default`
5. Export cold data to GCS (cheaper)

### Metric Cost Controls
1. Reduce label cardinality
2. Use logs-based metrics for rare events
3. Sample traces aggressively
4. Delete unused custom metrics

PCA principle:
> Optimize `_Default`, NOT `_Required`

---

## 16. Exam Decision Triggers (MEMORIZE)

| If question says… | Choose… |
|------------------|--------|
| "Who changed access?" | Admin Activity logs |
| "Sensitive data access" | Data Access logs |
| "Compliance / audit" | `_Required` + retention |
| "Reduce logging cost" | `_Default` exclusions |
| "Investigate incident" | Logs Explorer / Analytics |
| "Analyze trends" | BigQuery sink |
| "Latency bottleneck" | Cloud Trace |
| "Memory leak" | Cloud Profiler |
| "Crashes after deploy" | Error Reporting |
| "Can't reproduce bug" | Cloud Debugger |
| "Microservices visibility" | Anthos Service Mesh |
| "Multi-cloud portable" | OpenTelemetry |
| "Centralized security logs" | Org-level aggregated sink |
| "SIEM integration" | Pub/Sub sink |
| "Network forensics" | VPC Flow Logs (100% sample) |
| "Google employee access" | Access Transparency logs |

---

## 17. Common Anti-Patterns (What NOT to Do)

| Anti-Pattern | Why It's Wrong | PCA Answer |
|-------------|----------------|------------|
| Logging everything | Cost explosion | Sample or exclude |
| No log retention policy | Compliance risk | Set org policy |
| High-cardinality metrics | Query timeouts | Reduce labels |
| Alerts on infrastructure | Alert fatigue | SLO-based alerts |
| Self-managed Prometheus | Toil | Managed Prometheus |
| Logs in Cloud Storage only | Can't investigate | Keep in Log buckets |

---

## 18. Quick Architecture Patterns

### Pattern 1: Security Operations
```
All projects → Org aggregated sink → Security project BigQuery → Chronicle
                                                         ↓
                                                   Security team access
```

### Pattern 2: Compliance
```
Admin + Data Access logs → `_Required` bucket (immutable)
                         → BigQuery sink (7 years retention)
                         → GCS archive (legal hold)
```

### Pattern 3: Cost-Optimized App Monitoring
```
Application logs → Exclude debug/verbose → `_Default` (30 days)
                → Logs-based metrics → Dashboards
                → Error Reporting → Alerts
```

### Pattern 4: Microservices (GKE)
```
GKE cluster → GKE Monitoring enabled
           → Anthos Service Mesh → Auto tracing
           → Managed Prometheus → PromQL dashboards
           → SLO-based alerts
```

---

## 19. PCA Golden Rule 🧠

> If not explicitly required:
> - Avoid active-active
> - Avoid self-managed
> - Avoid over-design

Choose the **simplest managed architecture** that meets requirements.

---

## 20. Final PCA Sanity Check

Ask yourself:
> "Does this design improve visibility, security, and reliability  
> while minimizing operational burden?"

If yes → likely the correct answer.

---

## 21. Pricing Quick Reference

| Service | Free Tier | Notes |
|---------|-----------|-------|
| Cloud Logging | 50 GB/month | After that: $0.50/GB |
| Cloud Monitoring | 150 MB metrics/month | Custom metrics cost more |
| Cloud Trace | First 2.5M spans free | $0.20 per million after |
| Error Reporting | Free | - |
| Profiler | Free | - |

**Cost killer:**
> VPC Flow Logs at 100% sampling in high-traffic VPC

**Cost saver:**
> Logs-based metrics instead of custom metric writes
