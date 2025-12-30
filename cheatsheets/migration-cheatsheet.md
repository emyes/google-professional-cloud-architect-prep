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
