# GCP Migration & Modernization Cheat Sheet (PCA Exam)

---

## RPO & RTO (Business Continuity)
- **RPO (Recovery Point Objective):** Max data loss allowed (e.g., 15 min for mission-critical)
- **RTO (Recovery Time Objective):** Max downtime allowed (e.g., <15 min for mission-critical)
- **Tip:** Tighter RPO/RTO = higher cost/complexity

---

## Backup & DR Service
- **Centralized, managed backup for VMs, DBs, hybrid workloads**
- **Incremental-forever backups** (after initial full backup)
- **Mount & Migrate:** Near-instant recovery for DBs
- **Immutable backup vaults** for compliance
- **Cross-region support** for DR

---

## Migration Concepts
- **Migration Center:** Inventory, assess, and plan migrations (TCO, dependencies)
- **Migrate to VMs:** Six-phase lifecycle (Onboard, Replicate, Target Config, Test-Clone, Cut-over, Finalize)
- **Shared VPC:** Centralized network for secure, multi-project migrations
- **Phased migration:** Test in isolated subnets before cut-over

---

## Application Integration (iPaaS)
- **Serverless, managed integration platform**
- **Drag-and-drop editor, 90+ connectors**
- **Event-driven triggers:** API, Pub/Sub, schedule
- **Visual data mapping**

---

## Data Transfer Services

### Storage Transfer Service
**Purpose:** Managed, scalable data transfers to Cloud Storage from various sources.

**Supported Sources:**
- **AWS S3** → Cloud Storage (cross-cloud migration)
- **Azure Blob Storage** → Cloud Storage
- **HTTP/HTTPS sources** → Cloud Storage (e.g., public datasets)
- **Cloud Storage bucket** → Cloud Storage bucket (cross-region, cross-project)
- **On-premises** → Cloud Storage (via Transfer Service for on-premises data)

**Key Features:**
- **Scheduling:** One-time or recurring transfers (daily, weekly)
- **Filtering:** Include/exclude by prefix, creation date, file extension
- **Bandwidth Control:** Throttle transfer rate to avoid network saturation
- **Deletion Options:** Delete source files after transfer (migration mode)
- **Metadata Preservation:** Retain timestamps, ACLs, storage class
- **Event Notifications:** Pub/Sub notifications on transfer completion

**Use Cases:**
- Migrate data from AWS S3 to GCS (cloud-to-cloud)
- Periodic backups from on-prem to Cloud Storage
- Data lake ingestion from HTTP sources
- Cross-region bucket replication

**Exam Trigger:** *"Migrate 100TB from AWS S3 to Cloud Storage"* → **Storage Transfer Service**

---

### Transfer Appliance
**Purpose:** Physical, rack-mountable device for offline data transfer (petabyte-scale).

**Specifications:**
- **Capacity:** 40TB, 100TB, or 480TB models
- **Process:** Ship appliance → Load data → Return to Google → Upload to GCS
- **Encryption:** AES-256 encryption at rest
- **Transfer Speed:** Faster than network for large datasets (weeks → days)

**When to Use:**
- **Network constraints:** Limited bandwidth, high cost for egress
- **Massive datasets:** Multi-petabyte migrations (>100TB)
- **Time-sensitive:** Faster than network transfer (calculate: data size / bandwidth)

**Decision Formula:**
```
Network Transfer Time = Data Size (GB) / (Bandwidth (Mbps) × 0.125 × 86400 seconds)
If > 1 week && > 100TB → Consider Transfer Appliance
```

**Exam Trigger:** *"Migrate 500TB in 2 weeks, limited bandwidth"* → **Transfer Appliance**

---

### BigQuery Data Transfer Service
**Purpose:** Automated data loading into BigQuery from SaaS applications and Google services.

**Supported Sources:**
- **Google Ads, Campaign Manager, YouTube Analytics** (marketing data)
- **Amazon S3** (via scheduled transfers)
- **Teradata, Amazon Redshift** (data warehouse migrations)
- **Cloud Storage** (scheduled imports)
- **Google Play** (app analytics)

**Features:**
- **Scheduled Transfers:** Daily, weekly, on-demand
- **Backfill:** Load historical data automatically
- **Notifications:** Email, Pub/Sub on transfer completion/failure
- **Managed Service:** No infrastructure, auto-retry on failure

**Use Cases:**
- Daily ingestion of Google Ads data into BigQuery
- Automated loading of Cloud Storage CSV files
- SaaS application data warehousing

**Exam Trigger:** *"Automate daily Google Ads data into BigQuery"* → **BigQuery Data Transfer Service**

---

## Transfer Service Decision Matrix

| Scenario | Solution | Why |
|----------|----------|-----|
| AWS S3 → GCS (100TB, good bandwidth) | Storage Transfer Service | Network-based, automated |
| On-prem → GCS (500TB, limited bandwidth) | Transfer Appliance | Offline, faster than network |
| Daily Google Ads → BigQuery | BigQuery Data Transfer Service | SaaS automation |
| HTTP dataset → GCS | Storage Transfer Service | Supports HTTP/HTTPS sources |
| Petabyte-scale migration (<1 week) | Transfer Appliance | Physical device, fastest |
| Cross-region GCS replication | Storage Transfer Service | Scheduled bucket-to-bucket |

---

## Cloud Scheduler
- **Managed cron job service**
- **Automate scheduled tasks/workflows**
- **No infra to manage**

---

## GCP Service Emulators
- **Local emulators for Pub/Sub, Bigtable, Spanner, etc.**
- **Enable local dev/testing, CI/CD reliability, no cloud cost**

---

## Chrome Enterprise
- **Core:** Centralized browser management, 500+ policies
- **Premium:** Adds DLP, Zero Trust, advanced threat protection

---

## SOC 2 Certification
- **Google Cloud is SOC 2 Type II compliant**
- **Regular third-party audits**
- **Covers security, availability, integrity, confidentiality, privacy**

---

**Exam Triggers:**
- Know when to use Migration Center vs. Migrate to VMs
- RPO/RTO definitions and trade-offs
- Backup/DR for compliance and recovery
- Application Integration for connecting SaaS, GCP, and on-prem
- Use emulators for local dev/testing
- Chrome Enterprise for endpoint security
- SOC 2 for compliance scenarios
