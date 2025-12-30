# GCP Data & Security Cheat Sheet (PCA Exam)

This cheat sheet summarizes the most important Google Cloud data processing and security services for the Professional Cloud Architect (PCA) exam, with concise notes on architecture, features, and best practices.

---

## Messaging and Orchestration
**Cloud Pub/Sub**
- Decouples producers/consumers with control (routers) and data (forwarders) planes
- At-least-once delivery (exactly-once for pull subscriptions in a single region)
- Publisher-side batching for cost optimization (1,000-byte minimum billing unit)
- Message storage policies for data locality
- Pull & Push subscriptions; event-driven microservices, data pipelines

---

## Data Processing
**Cloud Dataflow**
- Unified batch/stream processing (Apache Beam)
- Windowing: Tumbling, hopping, session
- Watermarks & triggers for late data
- Autoscaling, exactly-once processing (idempotent sinks)
- Integrates with Pub/Sub, BigQuery, Cloud Storage, DLP API

**Cloud Dataproc**
- Managed Spark/Hadoop/Presto clusters
- Ephemeral clusters (decouple storage/compute with GCS)
- Enhanced Flexibility Mode for preemptible VMs
- Multi-region, autoscaling, open-source analytics

**Cloud Dataprep**
- Visual, serverless data preparation
- ML-powered transformations; integrates with BigQuery, Cloud Storage

---

## Content Delivery & API Management
**Cloud CDN / Media CDN**
- Global edge caching; cache modes: CACHE_ALL_STATIC, USE_ORIGIN_HEADERS, FORCE_CACHE_ALL
- Custom cache keys for optimization

**Cloud Endpoints**
- Distributed API management (ESP/ESPv2)
- Supports concurrent API versions; proxy choice based on environment

---

## Security & Governance
**Cloud Armor**
- DDoS protection, WAF (OWASP Top 10, Adaptive Protection)
- Edge security; integrates with Cloud CDN

**Identity-Aware Proxy (IAP)**
- Zero Trust access (user identity, context)
- TCP forwarding for SSH/RDP to VMs without public IPs

**Cloud KMS**
- Central key management (CMEK, CSEK, HSM, EKM)
- Envelope encryption (KEK/DEK); integrates with GCP services

**Secret Manager**
- Secure, versioned storage for secrets (API keys, passwords)
- IAM, audit logging, rotation

**Cloud DLP API**
- Discover, classify, de-identify PII (masking, tokenization, hashing)
- Integrates with Dataflow, BigQuery, Cloud Storage

**Security Command Center (SCC)**
- Centralized threat detection & compliance
- Security Health Analytics, Event & Container Threat Detection

**Model Armor**
- AI-native firewall for LLMs
- Screens prompts/responses for injection, jailbreaks, sensitive data leaks

---

## Quick Reference Table

| Service         | Data | Security | Key Features/Notes                  |
|----------------|------|----------|-------------------------------------|
| Pub/Sub        |  ✓   |          | Event ingestion, decoupling         |
| Dataflow       |  ✓   |          | Stream/batch, exactly-once, Beam    |
| Dataproc       |  ✓   |          | Spark/Hadoop, open-source analytics |
| Dataprep       |  ✓   |          | Visual prep, no code                |
| KMS            |      |    ✓     | Key management, CMEK/CSEK/HSM       |
| Secret Manager |      |    ✓     | Secrets, versioning, IAM            |
| DLP API        |  ✓   |    ✓     | Sensitive data, masking, redaction  |
| Armor          |      |    ✓     | WAF, DDoS, edge security            |
| SCC            |      |    ✓     | Threat detection, compliance        |
| Model Armor    |      |    ✓     | LLM firewall, DLP for AI            |
