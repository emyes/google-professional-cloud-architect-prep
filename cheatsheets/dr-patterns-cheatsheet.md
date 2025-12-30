# Disaster Recovery (DR) Patterns Cheat Sheet (PCA Exam)

---

## DR Concepts
- **RPO (Recovery Point Objective):** Max data loss allowed
- **RTO (Recovery Time Objective):** Max downtime allowed
- **DR Plan:** Documented, tested process for recovery
- **Backup:** Regular, automated, offsite, immutable
- **Failover:** Automatic/manual switch to standby system
- **Testing:** Regular DR drills and validation

---

## Common DR Patterns
- **Backup & Restore:** Periodic backups, restore on demand (lowest cost, highest RTO)
- **Pilot Light:** Minimal core infrastructure always running, scale up on failover
- **Warm Standby:** Scaled-down version of prod always running, quick failover
- **Active-Active:** Full prod in multiple regions, instant failover (highest cost, lowest RTO)
- **Geo-Redundancy:** Data and services replicated across regions

---

## GCP DR Services
- **Backup & DR Service:** Centralized, managed backups for VMs, DBs, hybrid
- **Cloud Storage:** Cross-region replication, object versioning
- **Cloud SQL/Spanner:** Automated backups, point-in-time recovery, multi-region
- **Compute Engine:** Snapshots, instance templates, MIGs
- **Cloud DNS:** Fast failover with health checks
- **Load Balancing:** Global/regional, auto-failover

---

## Best Practices
- Classify workloads by criticality (mission/business/non-critical)
- Align DR pattern to RPO/RTO and budget
- Automate backup and failover
- Test DR regularly and update plans
- Document and train teams

---

**Exam Triggers:**
- Choose DR pattern based on scenario (cost, RPO/RTO)
- Know which GCP services support DR and HA
- Understand trade-offs between patterns
