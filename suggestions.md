# Missing Topics & Recommendations for PCA Study Plan

**Analysis Date:** December 20, 2025  
**Exam Date:** January 23, 2026

---

## 📊 Coverage Analysis

Your study plan is **comprehensive** covering ~85-90% of PCA exam topics. However, there are some important gaps and areas that need deeper focus based on the official PCA exam guide.

---

## ⚠️ CRITICAL GAPS (High Impact)

### 1. **Organization Policy & Resource Manager** ❗ MISSING
**Exam Weight:** 5-8%  
**Current Coverage:** Not explicitly covered

**What's Missing:**
- Organization Policy Service (constraints, inheritance)
- Policy evaluation (allow policies vs deny policies)
- Folder and organization hierarchy design patterns
- Resource Manager API
- Policy inheritance and override patterns
- Common organizational constraints (e.g., disableServiceAccountKeyCreation)

**Why It Matters:**
- Frequently tested in governance scenarios
- Critical for enterprise architecture questions
- Often combined with IAM questions

**Recommendation:**
- **Add to Week 1 or Week 4** as supplementary topic
- 2-3 hours study time
- Include in IAM cheat sheet or create mini-section

---

### 2. **VPC Peering & Shared VPC** ⚠️ PARTIALLY COVERED
**Exam Weight:** 5-7%  
**Current Coverage:** Mentioned briefly, needs depth

**What Needs More Depth:**
- **VPC Peering:**
  - Transitive peering limitations
  - VPC Peering vs VPN vs Interconnect comparison
  - Cross-project and cross-organization peering
  - IP range conflicts and resolution
  
- **Shared VPC:**
  - Host project vs service project architecture
  - IAM roles for Shared VPC (Host Admin, Network Admin, User)
  - Use cases: Centralized networking with distributed ownership
  - **Phased migration** (mentioned in Week 5 but needs depth)

**Why It Matters:**
- Common in enterprise scenarios
- Tested in multi-project architecture questions
- Critical for hybrid/migration scenarios

**Recommendation:**
- **Enhance Week 4 coverage** with dedicated section
- Add architecture diagrams for Shared VPC patterns
- Include in networking whitepaper/cheat sheet

---

### 3. **Cloud DNS & Private DNS** ⚠️ MISSING
**Exam Weight:** 3-5%  
**Current Coverage:** Not covered

**What's Missing:**
- Cloud DNS basics (public zones)
- Private DNS zones (internal resolution)
- DNS peering and forwarding
- DNSSEC
- Split-horizon DNS
- Integration with hybrid environments

**Why It Matters:**
- Tested in hybrid connectivity scenarios
- Important for multi-region architecture
- Often combined with VPC and Interconnect questions

**Recommendation:**
- **Add to Week 4** after VPC topics
- 2-3 hours study time
- Include in networking cheat sheet

---

### 4. **Cloud Composer (Managed Apache Airflow)** ⚠️ MISSING
**Exam Weight:** 3-5%  
**Current Coverage:** Not covered (Dataflow/Dataproc covered, but not orchestration)

**What's Missing:**
- Cloud Composer for workflow orchestration
- DAG (Directed Acyclic Graph) design
- When to use Composer vs Dataflow vs Scheduler
- Integration with BigQuery, Dataproc, Dataflow
- GKE-based architecture

**Why It Matters:**
- Critical for data pipeline questions
- Often tested in ETL/data architecture scenarios
- Complements BigQuery and data services

**Recommendation:**
- **Add to Week 5** with data services
- 2-3 hours study time
- Include in data services cheat sheet

---

### 5. **Transfer Service & Storage Transfer Service** ⚠️ MISSING
**Exam Weight:** 2-4%  
**Current Coverage:** Migration mentioned but not transfer services

**What's Missing:**
- **Storage Transfer Service:**
  - AWS S3 to GCS
  - HTTP/HTTPS sources
  - GCS bucket to bucket
  - Transfer scheduling and bandwidth management
  
- **Transfer Appliance:**
  - When to use (petabyte-scale offline transfer)
  - Use cases vs online transfer
  
- **BigQuery Data Transfer Service:**
  - Automated data loading from SaaS applications
  - Scheduling and configuration

**Why It Matters:**
- Tested in migration scenarios
- Important for data ingestion questions
- Cost optimization considerations

**Recommendation:**
- **Add to Week 4 or Week 5** with storage/migration topics
- 2 hours study time
- Include in migration strategies section

---

## 🔔 MODERATE GAPS (Medium Impact)

### 6. **Cloud Tasks & Cloud Scheduler Comparison** ⚠️ PARTIAL
**Exam Weight:** 2-3%  
**Current Coverage:** Cloud Scheduler mentioned, Cloud Tasks missing

**What's Missing:**
- **Cloud Tasks:**
  - Asynchronous task execution
  - Task queues and deduplication
  - Integration with App Engine and Cloud Run
  - Rate limiting and retries
  
- **Cloud Scheduler vs Cloud Tasks:**
  - When to use scheduled jobs vs task queues
  - Architecture patterns

**Recommendation:**
- **Add to Week 3 or Week 5**
- 1-2 hours study time
- Include in serverless cheat sheet

---

### 7. **Anthos Config Management & Policy Controller** ⚠️ MISSING
**Exam Weight:** 3-5%  
**Current Coverage:** Anthos clusters mentioned, but not Config Management

**What's Missing:**
- Config Sync (GitOps for Kubernetes)
- Policy Controller (OPA/Gatekeeper)
- Multi-cluster management
- Anthos Service Mesh (ASM) - mentioned in Week 2 but needs depth
- Config Connector (mentioned but needs more detail)

**Why It Matters:**
- Increasingly tested in enterprise scenarios
- Hybrid/multi-cloud architecture questions
- GKE Enterprise use cases

**Recommendation:**
- **Enhance Week 2 GKE section**
- 3-4 hours study time
- Include in GKE/Kubernetes materials

---

### 8. **Cloud Run for Anthos** ⚠️ MISSING
**Exam Weight:** 2-3%  
**Current Coverage:** Cloud Run covered, but not on-prem/Anthos variant

**What's Missing:**
- Cloud Run for Anthos (Knative on GKE)
- Hybrid serverless patterns
- When to use Cloud Run vs Cloud Run for Anthos

**Recommendation:**
- **Add to Week 3** with Cloud Run
- 1 hour study time
- Brief comparison in serverless cheat sheet

---

### 9. **Traffic Director** ⚠️ MISSING
**Exam Weight:** 2-3%  
**Current Coverage:** Not covered

**What's Missing:**
- Global load balancing for service mesh
- Traffic management across clouds
- Integration with Istio/Anthos Service Mesh
- Advanced traffic splitting and routing

**Recommendation:**
- **Add to Week 4** with Load Balancing
- 1-2 hours study time
- Include in networking cheat sheet as advanced topic

---

### 10. **Binary Authorization** ⚠️ MISSING
**Exam Weight:** 2-3%  
**Current Coverage:** Container security mentioned, but not Binary Authorization

**What's Missing:**
- Deploy-time security control
- Attestation and signature verification
- Integration with GKE and Cloud Run
- Policy enforcement

**Recommendation:**
- **Add to Week 2 or Week 3** with container topics
- 1-2 hours study time
- Include in GKE security section

---

### 11. **Cloud Asset Inventory** ⚠️ MISSING
**Exam Weight:** 2-3%  
**Current Coverage:** Not covered

**What's Missing:**
- Resource inventory and search
- Export to BigQuery for analysis
- Policy analysis
- Use cases for compliance and governance

**Recommendation:**
- **Add to Week 1 or Week 5** with governance/security
- 1 hour study time
- Include in IAM cheat sheet or security services

---

### 12. **Recommender & Active Assist** ⚠️ PARTIAL
**Exam Weight:** 2-3%  
**Current Coverage:** Cloud Recommender mentioned in Week 2, needs more

**What Needs More Depth:**
- IAM Recommender (covered in your materials ✓)
- Compute Engine Recommender (mentioned ✓)
- **Missing:**
  - BigQuery Recommender
  - Cloud SQL Recommender
  - Networking Recommender
  - Active Assist dashboard
  - Sustainability and cost optimization insights

**Recommendation:**
- **Enhance existing coverage** across multiple weeks
- Add to relevant service sections
- Include in cost optimization materials

---

## ✅ MINOR GAPS (Low Impact, Nice to Have)

### 13. **Assured Workloads** ⚠️ MISSING
**Exam Weight:** 1-2%  
**Current Coverage:** Not covered

**What's Missing:**
- Compliance frameworks (HIPAA, FedRAMP, etc.)
- Assured Workloads for regulated industries
- Organizational controls

**Recommendation:**
- **Optional:** Add brief mention in Week 5 security topics
- 30 minutes study time

---

### 14. **Apigee** ⚠️ MISSING
**Exam Weight:** 1-2%  
**Current Coverage:** Cloud Endpoints covered, Apigee not covered

**What's Missing:**
- Full lifecycle API management
- API gateway vs Apigee vs Cloud Endpoints
- Analytics and developer portal
- Hybrid deployment

**Recommendation:**
- **Optional:** Brief comparison in Week 3
- 1 hour study time
- Include Endpoints vs Apigee comparison

---

### 15. **Eventarc** ⚠️ MISSING
**Exam Weight:** 1-2%  
**Current Coverage:** Not covered

**What's Missing:**
- Event-driven architecture with Eventarc
- Integration with Cloud Run, Cloud Functions
- Event sources (Pub/Sub, Audit Logs, etc.)

**Recommendation:**
- **Optional:** Add to Week 3 with Cloud Functions
- 1 hour study time

---

### 16. **Workload Identity Federation for GKE** ⚠️ PARTIAL
**Exam Weight:** 2-3%  
**Current Coverage:** Workload Identity Federation covered for general use, needs GKE specifics

**What Needs Enhancement:**
- GKE Workload Identity (Kubernetes service accounts → GCP service accounts)
- Pod-level IAM
- Best practices for GKE security

**Recommendation:**
- **Enhance Week 2 GKE section**
- 1 hour additional study time

---

## 📋 Architecture & Design Patterns (Meta Topics)

These aren't specific services but architectural concepts that appear throughout the exam:

### 17. **Well-Architected Framework Principles** ⚠️ IMPLICIT
**Current Coverage:** Implicitly covered but not synthesized

**What to Add:**
- Cost optimization patterns
- Reliability patterns (HA, DR, failover)
- Performance optimization
- Security best practices
- Operational excellence

**Recommendation:**
- Create a **synthesis document** in Week 6
- Review architectural decision frameworks
- Common patterns across all services

---

### 18. **Case Study Analysis Skills** ⚠️ NEEDS FOCUS
**Current Coverage:** Practice tests in Week 6

**What to Add:**
- Practice analyzing case studies
- Identifying requirements (stated vs implied)
- Eliminating wrong answers
- Time management strategies

**Recommendation:**
- **Week 6 focus:** Dedicate time to case study practice
- Review sample case studies from official exam guide
- Practice under timed conditions

---

## 🎯 Prioritized Action Plan

### **MUST ADD (Before Week 4):**
1. ✅ Organization Policy & Resource Manager (Week 1 or 4) - 2-3 hours
2. ✅ Shared VPC deep-dive (Week 4) - enhance existing
3. ✅ Cloud DNS (Week 4) - 2-3 hours
4. ✅ VPC Peering patterns (Week 4) - enhance existing

### **SHOULD ADD (Week 4-5):**
5. ✅ Cloud Composer (Week 5) - 2-3 hours
6. ✅ Transfer Services (Week 4 or 5) - 2 hours
7. ✅ Anthos Config Management (Week 2) - enhance existing
8. ✅ Binary Authorization (Week 2 or 3) - 1-2 hours
9. ✅ Cloud Asset Inventory (Week 5) - 1 hour

### **NICE TO HAVE (Optional):**
10. Cloud Tasks vs Scheduler comparison - 1 hour
11. Traffic Director - 1-2 hours
12. Cloud Run for Anthos - 1 hour
13. Apigee comparison - 1 hour

### **Week 6 Focus:**
14. Architecture pattern synthesis
15. Case study practice
16. Review architectural decision frameworks

---

## 📚 Material Creation Suggestions

Based on these gaps, I recommend creating:

### **New Content to Create:**

1. **`cheatsheets/organization-governance.md`** (Week 1 or 4)
   - Organization Policy
   - Resource Manager
   - Folder structures
   - Policy inheritance

2. **Enhance `10-min-read/networking-hybrid-connectivity.md`** (Week 4)
   - Add dedicated sections for:
     - Shared VPC architecture patterns
     - VPC Peering vs alternatives
     - Cloud DNS and private zones
     - Traffic Director

3. **`cheatsheets/data-pipeline-orchestration.md`** (Week 5)
   - Cloud Composer
   - Dataflow vs Dataproc vs Composer
   - Workflow orchestration patterns

4. **Enhance `cheatsheets/compute-services.md`** (Week 2)
   - Add GKE security (Binary Authorization, Workload Identity)
   - Anthos Config Management

5. **`cheatsheets/migration-transfer.md`** (Week 4 or 5)
   - Migration strategies
   - Transfer Service
   - Migration Center
   - Migrate for Compute Engine

6. **`cheatsheets/architecture-patterns.md`** (Week 6)
   - HA/DR patterns
   - Cost optimization patterns
   - Security patterns
   - Common anti-patterns

---

## 📊 Updated Time Investment

**Additional study time needed:** ~20-25 hours
**Additional material creation:** ~12-15 hours
**Total additional effort:** ~35-40 hours

**Good news:** You have full-time availability in Weeks 4-5 when most gaps need to be filled!

---

## ✅ What You're Already Doing Well

Your plan DOES cover:
- ✅ IAM (comprehensive)
- ✅ Observability (comprehensive)
- ✅ Compute services (good coverage)
- ✅ Networking fundamentals (good base)
- ✅ Storage & databases (well covered)
- ✅ Data services (good coverage)
- ✅ Security services (solid)
- ✅ Kubernetes/GKE (solid base)
- ✅ Serverless (good coverage)

**Your foundation is strong. The gaps are specific, addressable, and mostly moderate-priority items.**

---

## 🎯 Final Recommendation

**Priority Order for Filling Gaps:**

1. **Week 1-3 (Now - Jan 4):** Don't stress about gaps, focus on mastering what's already planned
2. **Week 4 (Jan 5-10):** Add Organization Policy, enhance Shared VPC/Peering, add Cloud DNS
3. **Week 5 (Jan 11-17):** Add Cloud Composer, Transfer Services, Cloud Asset Inventory
4. **Week 6 (Jan 18-22):** Synthesize architecture patterns, case study practice

**You don't need to add everything.** Focus on the MUST-ADD items, and you'll be well-prepared. The exam tests breadth AND depth - you already have excellent depth in key areas. Adding these strategic gaps will give you the breadth needed.

---

**Bottom Line:** Your study plan is 85-90% complete. Adding ~30-40 hours of focused gap-filling in Weeks 4-5 (when you're full-time) will get you to 95%+ coverage. That's more than sufficient to excel on the exam.
