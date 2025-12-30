# Google Cloud Well-Architected Framework Cheat Sheet (PCA Exam)

---

## The Five Pillars
- **Operational Excellence:** Monitoring, incident response, automation, continuous improvement
- **Security:** Identity, access, data protection, threat detection, compliance
- **Reliability:** Resilience, failover, backup/restore, DR, capacity planning
- **Performance Efficiency:** Resource selection, scaling, monitoring, optimization
- **Cost Optimization:** Cost control, right-sizing, billing, FinOps, automation

---

## Key Principles
- Design for failure and automate recovery
- Implement strong identity and access controls (IAM, VPC-SC)
- Use managed services where possible
- Monitor everything (Cloud Monitoring, Logging, Error Reporting)
- Test DR and backup plans regularly
- Use labels and budgets for cost control
- Review architecture for anti-patterns and improvement

---

## GCP Service Mapping
- **Operational Excellence:** Cloud Monitoring, Logging, Error Reporting, SLOs, Recommender
- **Security:** IAM, VPC Service Controls, CMEK, DLP, Security Command Center, Shielded VMs
- **Reliability:** Multi-region, HA, Backup & DR, Cloud Load Balancing, Cloud DNS
- **Performance:** Autoscaling, Caching (Cloud CDN, Memorystore), Compute types, BigQuery slots
- **Cost:** Billing, Budgets, Recommender, Committed Use Discounts, Preemptible VMs

---

**Exam Triggers:**
- Know the five pillars and which GCP services support each
- Apply the framework to scenario questions (e.g., "How to improve reliability?")
