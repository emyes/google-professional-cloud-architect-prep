# Compute Services Cheat Sheet – PCA Exam Focus

> **Exam Strategy:** Know the **compute spectrum**, **when to use what**, and **common architectural patterns**  
> **Time to review:** 8-10 minutes

---

## 1. The Compute Spectrum (MEMORIZE THIS)

| Service | Abstraction Level | When to Use | Management Burden |
|---------|-------------------|-------------|-------------------|
| **Compute Engine** | IaaS (VMs) | Full OS control, legacy apps, lift-and-shift | High (OS, patches, scaling) |
| **GKE** | CaaS (Containers) | Microservices, portability, Kubernetes skills | Medium (cluster management) |
| **Cloud Run** | Serverless Containers | Stateless HTTP services, auto-scale to zero | Low (just containers) |
| **Cloud Functions** | FaaS | Event-driven, single-purpose functions | Very Low (just code) |
| **App Engine** | PaaS | Web apps, mobile backends, legacy PaaS | Very Low (managed platform) |

**PCA Decision Rule:**
> Choose the **highest abstraction** that meets requirements  
> More abstraction = Less management = Preferred answer

---

## 2. Compute Engine (Virtual Machines)

### Machine Types & Selection

**Machine Type Families:**

| Family | Use Case | Example |
|--------|----------|---------|
| **E2** | General purpose, cost-optimized | Web servers, dev environments |
| **N2/N2D** | Balanced compute and memory | Application servers, databases |
| **C2/C2D** | Compute-optimized | HPC, scientific computing, gaming |
| **M2/M3** | Memory-optimized | In-memory databases, SAP HANA |
| **A2/A3** | GPU-optimized | ML training, rendering, video transcoding |
| **T2D/T2A** | Scale-out, ARM-based | Microservices, web serving |

**PCA Exam Triggers:**

| Scenario | Choose |
|----------|--------|
| "Cost-effective web server" | E2 (shared-core) or E2-standard |
| "High-performance computing" | C2 or C2D |
| "In-memory database (Redis, SAP)" | M2 or M3 |
| "ML model training" | A2 with GPUs |
| "Windows Server licensing" | Sole-Tenant Nodes |

---

### VM Configuration Essentials

**Boot Disk:**
- **Default:** 10 GB pd-standard
- **Exam tip:** Choose **pd-balanced** for most workloads (balanced IOPS/cost)
- **pd-ssd** only when high IOPS required
- Boot disk can be resized but NOT shrunk

**Persistent Disks:**
- **pd-standard**: Cheap, low IOPS (HDD-backed)
- **pd-balanced**: Recommended default (SSD-backed)
- **pd-ssd**: High IOPS applications
- **pd-extreme**: Highest IOPS (configurable)

**Local SSDs:**
- Physically attached, **ephemeral** (data lost on VM stop)
- Very high IOPS
- Use for: Temporary data, caching, scratch space
- **Cannot** be attached to preemptible VMs

**Filestore:**
- Managed NFS for shared file access
- Tiers: Basic (1-63.9 TB), High Scale (10+ TB), Enterprise

---

### Preemptible & Spot VMs

**Preemptible VMs:**
- Up to **80% cheaper**
- Can be terminated any time (24-hour max)
- No SLA
- Use for: Batch jobs, fault-tolerant workloads

**Spot VMs:**
- Similar to preemptible but **more flexibility**
- No 24-hour limit
- Slightly different pricing model

**PCA Rule:**
> Batch processing + cost optimization = Preemptible/Spot VMs  
> **Never** for critical production workloads

---

### High Availability & Maintenance

**Live Migration:**
- Automatic VM migration during maintenance
- **Default:** Enabled (on-host maintenance = MIGRATE)
- Minimal downtime (~1 second)

**Maintenance Options:**
- **MIGRATE**: Live migrate (default, recommended)
- **TERMINATE**: Stop VM during maintenance

**When to choose TERMINATE:**
- VMs with local SSDs (can't migrate)
- Licensing requirements
- Specific workload constraints

**Availability Strategies:**

| Pattern | Availability | Cost | Use Case |
|---------|--------------|------|----------|
| Single VM | ~95-99% | Lowest | Dev/test |
| MIG (single zone) | ~99.5% | Low | Production apps |
| MIG (multi-zone) | ~99.9% | Medium | Critical apps |
| MIG (multi-region) | ~99.99%+ | High | Mission-critical |

---

## 3. Managed Instance Groups (MIGs)

### MIG Fundamentals

**Why MIGs?**
- Auto-scaling
- Auto-healing
- Load balancing
- Rolling updates
- Multi-zone deployment

**MIG Types:**

| Type | Use Case |
|------|----------|
| **Zonal MIG** | Single zone, simpler management |
| **Regional MIG** | Multi-zone, higher availability |

**PCA Preference:**
> **Regional MIGs** for production (automatic multi-zone distribution)

---

### Auto-Healing

**How it works:**
- Health check monitors VM health
- Unhealthy VMs automatically recreated
- **Critical:** Configure appropriate health check

**Health Check Configuration:**
- Check interval: 10s (default)
- Timeout: 5s (default)
- Healthy threshold: 2 consecutive successes
- Unhealthy threshold: 3 consecutive failures

**PCA Exam Trigger:**
> "Ensure failed VMs are automatically replaced" → MIG with auto-healing

---

### Auto-Scaling Policies

**Scaling Metrics:**

| Metric | When to Use |
|--------|-------------|
| **CPU utilization** | CPU-bound workloads |
| **HTTP(S) load balancing** | Web applications |
| **Pub/Sub queue depth** | Message processing |
| **Custom metrics** | Application-specific |

**Scaling Configuration:**
- **Target utilization:** Typically 60-80% (leaves headroom)
- **Cool-down period:** Prevent thrashing
- **Min/max instances:** Set boundaries

**PCA Rules:**
- Target CPU utilization = **60-70%** (not 90%+)
- Always set **minimum instances ≥ 2** for availability
- Use **schedule-based scaling** for predictable load

---

### Update Policies

**Rolling Update Types:**

| Type | Description | Use Case |
|------|-------------|----------|
| **Proactive** | Immediate replacement | Quick updates, maintenance |
| **Opportunistic** | Replace on resize/recreation | Gradual rollout |

**Update Strategies:**
- **Maximum surge:** How many VMs to create above target size
- **Maximum unavailable:** How many VMs can be down during update
- **Minimum wait time:** Time before marking instance updated

**PCA Pattern:**
```
Max surge: 3 (or 10%)
Max unavailable: 0
→ Zero-downtime updates
```

---

## 4. VM Connectivity & Security

### Connection Methods

| Method | Use Case | Port |
|--------|----------|------|
| **SSH** (Linux) | Direct connection | 22 |
| **RDP** (Windows) | Remote desktop | 3389 |
| **SSH-in-browser** | No local client needed | N/A (via console) |
| **IAP (Identity-Aware Proxy)** | VPN-less, secure access | N/A (tunneled) |
| **OS Login** | Use GCP identities instead of SSH keys | 22 |

**PCA Security Best Practices:**
- ✅ Use **IAP** for bastion-less access
- ✅ Enable **OS Login** (tie access to IAM)
- ✅ **Disable** external IPs where possible
- ❌ Don't use SSH keys directly (use OS Login)
- ❌ Don't allow direct public SSH access

---

### Network Configuration

**Multi-NIC (Multiple Network Interfaces):**
- Up to 8 NICs per VM (depending on machine type)
- Each NIC in different VPC network
- Use case: Network appliances, firewalls, segmentation

**Private Google Access:**
- VMs without external IP can access Google APIs
- Enable on subnet level
- Use case: Secure access to GCS, BigQuery, etc.

---

### Service Accounts

**Best Practices:**
- Assign **least-privilege** service account to VM
- Use **default service account** only for testing
- Create custom service accounts per application
- **Never** download service account keys for VMs (use metadata service)

**PCA Rule:**
> VMs should use service accounts, NOT service account keys

---

## 5. Backup & Disaster Recovery

### Backup Methods

| Method | Scope | Use Case |
|--------|-------|----------|
| **Snapshots** | Disk-level | Point-in-time backup |
| **Machine Images** | VM-level | Complete VM config + disks |
| **Custom Images** | OS-level | Reusable OS templates |
| **Persistent Disk Clones** | Disk-level | Fast disk duplication |

**Snapshot Strategies:**
- **Incremental:** Only changed blocks (automatic)
- **Scheduled snapshots:** Automated backups
- **Snapshot location:** Multi-regional for DR

**PCA Backup Pattern:**
```
Daily snapshots (7-day retention)
Weekly snapshots (4-week retention)
Monthly snapshots (12-month retention)
```

---

### Disaster Recovery Patterns

**RTO/RPO Considerations:**

| Pattern | RTO | RPO | Cost | Complexity |
|---------|-----|-----|------|------------|
| **Snapshots** | Hours | 1-24 hours | Low | Low |
| **Active-Standby** | Minutes | Minutes | Medium | Medium |
| **Active-Active** | Seconds | Seconds | High | High |

**Active-Standby Pattern:**
- Primary region: Active MIG
- Secondary region: Standby MIG (min=0, can scale up)
- Failover: DNS/LB redirect + scale up standby

**PCA Rule:**
> Choose **simplest pattern** that meets RTO/RPO requirements

---

## 6. Monitoring & Operations

### Ops Agent

**What it does:**
- Collects logs and metrics
- Replaces legacy Logging + Monitoring agents
- **Recommended** for all VMs

**Installation:**
```bash
curl -sSO https://dl.google.com/cloudagents/add-google-cloud-ops-agent-repo.sh
sudo bash add-google-cloud-ops-agent-repo.sh --also-install
```

**PCA Rule:**
> Always mention Ops Agent for comprehensive monitoring

---

### Cloud Recommender

**VM Recommendations:**
- **Rightsizing:** Suggests smaller/larger machine types
- **Idle VMs:** Identifies underutilized VMs
- **Snapshot recommendations:** Clean up old snapshots

**PCA Trigger:**
> Cost optimization → Cloud Recommender

---

## 7. Sole-Tenant Nodes

**When to use:**
- **Regulatory requirements** (physical isolation)
- **Licensing** (Windows Server, SQL Server, per-core)
- **Performance** (predictable, no noisy neighbors)

**Cost:**
- Pay for entire node (even if not fully utilized)
- More expensive than regular VMs

**PCA Trigger:**
> "BYOL Windows licensing" → Sole-Tenant Nodes

---

## 8. Google Kubernetes Engine (GKE)

### GKE vs Compute Engine

| Aspect | Compute Engine | GKE |
|--------|----------------|-----|
| **Management** | Manual | Kubernetes-managed |
| **Scaling** | MIG auto-scaling | HPA + Cluster Autoscaler |
| **Deployment** | Manual/scripts | Declarative (YAML) |
| **Best for** | VMs, legacy apps | Microservices, containers |
| **Portability** | GCP-specific | Kubernetes (portable) |

**PCA Decision:**
> Microservices + containers + need portability → GKE  
> Legacy apps + OS-level control → Compute Engine

---

### GKE Cluster Types

**Standard vs Autopilot:**

| Feature | Standard | Autopilot |
|---------|----------|-----------|
| **Management** | You manage nodes | Google manages nodes |
| **Flexibility** | Full control | Opinionated config |
| **Cost** | Pay for nodes | Pay for pods |
| **Use Case** | Custom requirements | Simpler operations |

**PCA Preference:**
> **Autopilot** for "managed" scenarios  
> **Standard** when customization needed

---

### GKE Networking

**Network Modes:**
- **VPC-native (Alias IPs):** Recommended, native VPC integration
- **Routes-based:** Legacy, not recommended

**Load Balancing:**
- **ClusterIP:** Internal cluster access
- **NodePort:** External access via node IP
- **LoadBalancer:** GCP external load balancer
- **Ingress:** HTTP(S) load balancing

**PCA Pattern:**
> External HTTP service → Ingress  
> External TCP/UDP → LoadBalancer service

---

### GKE Security

**Workload Identity:**
- Maps Kubernetes service accounts → GCP service accounts
- **No service account keys needed**
- **Best practice** for GKE security

**Binary Authorization:**
- Deploy-time security control
- Ensures only trusted images deployed
- Attestation-based policy enforcement

**GKE Sandbox:**
- gVisor-based runtime
- Enhanced container isolation
- Use for: Untrusted workloads, multi-tenant clusters

**PCA Security Stack:**
```
✅ Workload Identity (no keys)
✅ Binary Authorization (trusted images)
✅ GKE Sandbox (untrusted workloads)
✅ Private clusters (no public IPs)
✅ Shielded GKE nodes (Secure Boot)
```

---

### GKE Auto-Scaling

**Horizontal Pod Autoscaler (HPA):**
- Scales number of pod replicas
- Based on CPU, memory, or custom metrics
- Fast response (seconds to minutes)

**Vertical Pod Autoscaler (VPA):**
- Adjusts CPU/memory requests
- Requires pod restart
- Use for: Right-sizing pod resources

**Cluster Autoscaler:**
- Adds/removes nodes based on pod demand
- Slower response (minutes)
- Works with HPA

**PCA Pattern:**
> HPA + Cluster Autoscaler = Complete auto-scaling solution

---

### GKE Observability

**Built-in Integrations:**
- **Cloud Logging:** Container stdout/stderr
- **Cloud Monitoring:** Cluster and pod metrics
- **Cloud Trace:** Request tracing (with instrumentation)

**Managed Prometheus:**
- Managed collection, storage, querying
- PromQL support
- Recommended for GKE workloads

**PCA Rule:**
> GKE + observability → Enable GKE Monitoring + Managed Prometheus

---

### Anthos & GKE Enterprise

**Anthos Service Mesh (ASM):**
- Managed Istio
- Service-to-service encryption
- Traffic management, observability
- Use for: Microservices communication

**Anthos Config Management:**
- GitOps for Kubernetes
- Policy Controller (OPA/Gatekeeper)
- Multi-cluster management

**Config Connector:**
- Manage GCP resources via Kubernetes
- Infrastructure as Code via K8s YAML

**PCA Trigger:**
> "Hybrid Kubernetes" or "multi-cluster management" → Anthos

---

## 9. Exam Decision Triggers (MEMORIZE)

| If Exam Says... | Choose... |
|-----------------|-----------|
| "Cost-effective, auto-scale web app" | Cloud Run or App Engine |
| "Legacy Windows app, needs full OS access" | Compute Engine |
| "Microservices, need Kubernetes" | GKE |
| "Event-driven, single function" | Cloud Functions |
| "Batch processing, cost-sensitive" | Preemptible VMs |
| "High availability required" | Regional MIG |
| "Automatic failure recovery" | MIG with auto-healing |
| "Zero-downtime updates" | MIG rolling updates (max unavailable = 0) |
| "Windows licensing (BYOL)" | Sole-Tenant Nodes |
| "No external IP, needs Google API access" | Private Google Access |
| "Secure VM access without VPN" | Identity-Aware Proxy (IAP) |
| "In-memory database" | Memory-optimized (M2/M3) |
| "ML model training" | A2 VMs with GPUs |
| "Kubernetes security, no service account keys" | Workload Identity |
| "Ensure only trusted container images" | Binary Authorization |
| "Multi-cloud Kubernetes" | Anthos |

---

## 10. Common Anti-Patterns (What NOT to Do)

| Anti-Pattern | Why It's Wrong | PCA Solution |
|--------------|----------------|--------------|
| Single VM in production | No HA, single point of failure | Regional MIG (min=2) |
| SSH keys stored in metadata | Security risk | OS Login + IAP |
| Service account keys on VMs | Key leakage risk | Use VM service account |
| No health checks on MIG | Failed VMs not detected | Configure health checks |
| Target CPU = 95% | No headroom for spikes | Target CPU = 60-70% |
| Manual VM scaling | Slow, error-prone | Auto-scaling with MIG |
| Routes-based GKE clusters | Legacy, poor VPC integration | VPC-native clusters |
| Downloading service account keys for GKE | Security risk | Workload Identity |
| No monitoring on VMs | Can't detect issues | Ops Agent + Cloud Monitoring |
| Persistent data on local SSD | Data loss on stop | Persistent Disk |

---

## 11. Architecture Patterns

### Pattern 1: Highly Available Web Application
```
Regional MIG (min=3, multi-zone)
  ↓
External HTTP(S) Load Balancer
  ↓
Cloud CDN (optional)
  ↓
Cloud Armor (DDoS protection)
```

### Pattern 2: Batch Processing
```
Preemptible VMs (MIG)
  ↓
Pub/Sub queue for job distribution
  ↓
Cloud Storage for input/output
  ↓
Auto-scaling based on queue depth
```

### Pattern 3: Microservices on GKE
```
GKE Autopilot Cluster
  ↓
Anthos Service Mesh (Istio)
  ↓
Workload Identity (security)
  ↓
Horizontal Pod Autoscaler
  ↓
Ingress (external access)
```

### Pattern 4: Secure Bastion-less Access
```
Private VMs (no external IP)
  ↓
Identity-Aware Proxy (IAP)
  ↓
OS Login (GCP identities)
  ↓
Cloud Console or gcloud SSH
```

---

## 12. PCA Exam Golden Rules 🧠

1. **Prefer managed over self-managed**
   - Autopilot GKE > Standard GKE (when flexibility not needed)
   - Cloud Run > GKE > Compute Engine (when requirements allow)

2. **High availability by default**
   - Regional > Zonal
   - Multi-zone MIG for production
   - Min instances ≥ 2

3. **Security first**
   - IAP > VPN > Direct SSH
   - OS Login > SSH keys
   - Workload Identity > Service account keys
   - Private IPs > External IPs

4. **Cost optimization**
   - Preemptible/Spot for batch/fault-tolerant
   - Cloud Recommender for rightsizing
   - Committed Use Discounts (long-term workloads)

5. **Operational simplicity**
   - MIG with auto-scaling > Manual scaling
   - Ops Agent for monitoring
   - Regional MIGs for multi-zone deployment

---

## 13. Quick Reference: VM Types by Use Case

| Use Case | Recommended Type |
|----------|------------------|
| General web server | E2-standard-2 |
| Development/testing | E2-micro (free tier) or E2-small |
| Database server | N2-highmem or M2 |
| Batch processing | E2 + Preemptible |
| HPC/Scientific | C2 or C2D |
| In-memory cache (Redis) | M2 or M3 |
| ML training | A2 with GPUs |
| SAP HANA | M2 or M3 |
| Video encoding | C2 or with GPUs |
| File server (NFS) | Filestore (not VM) |

---

## 14. Final Checklist Before Exam

- [ ] Understand compute spectrum (when to use each service)
- [ ] Know machine type families and use cases
- [ ] Memorize MIG patterns (auto-healing, auto-scaling, updates)
- [ ] Understand preemptible/spot VM use cases
- [ ] Know HA patterns (single zone vs multi-zone vs multi-region)
- [ ] Understand GKE vs Compute Engine decision criteria
- [ ] Know GKE security (Workload Identity, Binary Authorization)
- [ ] Memorize connectivity patterns (IAP, OS Login)
- [ ] Understand backup/DR strategies
- [ ] Review decision triggers and anti-patterns

---

**Study Tip:** Focus on **when to use what** over **how to configure**. PCA is an architect exam, not an operations exam!
