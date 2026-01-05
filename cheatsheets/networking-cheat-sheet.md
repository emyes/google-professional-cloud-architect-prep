# GCP Networking – PCA Exam Cheat Sheet

## VPC (Virtual Private Cloud)
- Global, logical network spanning all GCP regions.
- Subnets are **regional**; firewall rules and routes are **global**.
- IP ranges **must not overlap**.
- **Custom vs. Auto Mode:** Custom mode is recommended for production for full control over IP ranges and subnets. Auto mode creates subnets in every region automatically.
- **Routing priority:** Longest prefix match → lowest priority number wins.
- **Private Google Access:** Allows VMs without public IPs to access **Google APIs** only.
- **Serverless VPC Access:** Connects Cloud Run, Cloud Functions, App Engine to VPC.
- **Direct VPC Egress:** Preferred for Cloud Run egress control.
- Predefined roles:
  - `roles/compute.networkAdmin`
  - `roles/compute.securityAdmin`

**Exam trigger:** *Global network, regional subnets*

---

## Shared VPC
- Allows multiple projects to share a common VPC.
- Centralized network control with decentralized resource deployment.
- Requires **Organization node**.
- IAM separation of duties:
  - Network Admin → Host project
  - Service Project Admin → Deploy resources only
- Preferred over peering in **enterprise-scale environments**.

**Exam trigger:** *Centralized control + many projects*

---

## VPC Peering
- Private connectivity using Google backbone.
- No transitive routing.
- No overlapping CIDR ranges.
- No centralized firewall control.
- Not suitable for hub-and-spoke designs.

**Exam trigger:** *Simple, low-latency connectivity between two networks*

---

## VPC Service Controls
- Security perimeter for Google-managed services.
- Protects against **data exfiltration**.
- Common services: BigQuery, Cloud Storage.
- Complements IAM; does not replace it.

**Exam trigger:** *Prevent data exfiltration*

---

## VPC Flow Logs
- Network telemetry captured at **subnet level**.
- Used for monitoring, troubleshooting, and forensics.
- Sampling impacts **cost vs visibility**.
- Not real-time enforcement.

**Exam trigger:** *Why is traffic dropped?*

---

## Cloud VPN
- Encrypted connectivity between GCP and on-prem / other clouds.
- **HA VPN**:
  - Active/Active tunnels
  - Requires BGP
  - 99.99% SLA applies only with 2 tunnels + 2 gateways
- Fast to deploy; not ideal for high throughput.

**Exam trigger:** *Encrypted, quick to deploy*

---

## Cloud Interconnect
- **Dedicated Interconnect:**
  - Highest throughput
  - Lowest latency
  - Requires colocation
- 99.99% SLA with redundant connections
- **Partner Interconnect:**
  - No colocation required
  - Uses service providers
- 99.99% SLA with redundant connections (VLAN attachments in different edge availability domains)
- Requires **Cloud Router (BGP)**.
- Best for predictable, long-term traffic.

**Exam trigger:** *Low latency, high throughput, 99.99% SLA*

---

## Network Connectivity Center
- Hub-and-spoke solution for managing complex, global-scale connectivity.
- Supports up to 250 VPC spokes.

**Exam trigger:** *Global hub-and-spoke, hybrid or multi-cloud*

---

## Load Balancing
- **Global Load Balancers:**
  - HTTP(S), SSL Proxy, TCP Proxy
  - Anycast IP + global failover
- **Regional Load Balancers:**
  - Internal TCP/UDP, Network LB
- OSI Layers:
  - L7 → HTTP(S)
  - L4 → TCP/UDP
- **Internal Load Balancer:** Private traffic only.
- SSL termination reduces backend CPU load.
- Supports health checks, session affinity, path-based routing.
- **Direct Server Return (DSR):** Passthrough Network Load Balancer preserves client source IPs.
- **Direct VPC Egress for Cloud Run:** Low-latency path for serverless to VPC, replacing legacy connectors.

## Network Service Tiers
- **Premium Tier:** Uses Google’s global backbone for best performance and reliability.
- **Standard Tier:** Optimizes for cost by routing over the public internet sooner.

**Exam trigger:** *Performance vs. cost tradeoff*

## Cloud CDN
- Caches content at Google’s edge locations to reduce latency and egress costs.

**Exam trigger:** *Reduce latency, offload origin*

**Exam trigger:** *Single IP, global users*

---

## Network Endpoint Groups (NEGs)
- Abstract backend endpoints.
- Required for **Cloud Run / serverless backends**.
- Enables canary and blue-green deployments.

**Exam trigger:** *Hybrid or serverless backend*

---


## Cloud NAT
- Outbound internet access for private VMs.
- No inbound traffic support.
- Scales automatically without IP exhaustion.
- Often paired with Private Google Access.

**Exam trigger:** *Private VMs need internet access*

---

## Private NAT
- Enables RFC1918-to-RFC1918 (private IP to private IP) address translation for internal-only connectivity.
- Used for private communication between VPCs, or between on-prem and GCP, without public internet exposure.
- No public IPs involved; supports hybrid and multi-VPC designs.
- Complements Cloud NAT (internet egress) for internal-only use cases.

**Exam trigger:** *Private connectivity between VPCs or hybrid environments without public IPs*

---

## Cloud Firewall
- Stateful and distributed.
- Evaluated by priority (lower number wins).
- Allow and deny rules evaluated equally.
- Supports tags and service accounts.

## Alias IPs
- Allow a VM’s network interface to have secondary IP ranges.
- Essential for scaling GKE pod networking.

**Exam trigger:** *GKE pod IP management*

**Exam trigger:** *Why is traffic blocked?*

---

## Hierarchical Firewall Policies
- Applied at Org / Folder / Project level.
- Enforced downward; cannot be overridden.

**Exam trigger:** *Org-wide security enforcement*

---

## Cloud Armor
- DDoS protection and WAF.
- Layer 7 protection.
- Works only with HTTP(S) Load Balancer.

## Identity-Aware Proxy (IAP)
- Provides zero-trust access to VMs and apps by verifying user identity and context.
- No VPN required for secure access.

**Exam trigger:** *Zero-trust, user-based access*

**Exam trigger:** *L7 attack, minimal app changes*

---

## Private Service Connect
- Private access to Google APIs, SaaS, and producer services.
- No public IP exposure.
- Preferred over legacy private API access patterns.

**Exam trigger:** *Private access to managed services*

---

## Cloud DNS (Deep Dive)

**Purpose:** Managed, authoritative DNS service with high availability, low latency, and DNSSEC support.

**Zone Types:**

1. **Public Zones**
   - Authoritative DNS for internet-facing domains
   - Globally distributed, anycast service (100% SLA)
   - Supports A, AAAA, CNAME, MX, TXT, NS, SOA, CAA records
   - Use case: Host public website DNS

2. **Private Zones**
   - Internal name resolution within VPC networks
   - Not visible to the internet
   - Automatically integrated with VPC (no resolver config needed)
   - Use case: Internal service discovery (e.g., `db.internal.example.com`)

3. **Forwarding Zones**
   - Conditional forwarding to on-premises or external DNS servers
   - Queries for specified domains forwarded to target nameservers
   - Use case: Hybrid cloud DNS resolution
   - Example: Forward `*.corp.local` to on-prem DNS at `10.1.1.53`

4. **Peering Zones**
   - Enable DNS resolution across VPC networks
   - VPC A can resolve private zones in VPC B
   - Use case: Multi-project architectures, Shared VPC alternatives
   - Limitation: One-way peering (must configure both directions for bidirectional)

**Key Features:**

* **DNSSEC:** Cryptographic authentication of DNS responses (prevents cache poisoning)
  - State: `on`, `off`, `transfer` (for migrations)
  - Requires key signing and zone signing keys

* **Split-Horizon DNS:** Different responses for internal vs external queries
  - Public zone: `api.example.com` → External IP (34.x.x.x)
  - Private zone: `api.example.com` → Internal IP (10.x.x.x)
  - Use case: Reduce egress costs, improve latency

* **Routing Policies:** (Geo-based, weighted, failover)
  - Geo-location: Route based on client location
  - Weighted round-robin: Distribute traffic across backends
  - Use case: Global load distribution, DR failover

* **Cloud DNS Policies:**
  - Apply DNS rules (forwarding, alternative name servers) to specific VPCs
  - Centralized policy management across projects

**Architecture Patterns:**

**Pattern 1: Hybrid DNS Resolution**
```
On-Premises DNS ←→ Cloud DNS Forwarding Zone ←→ Private Zones (VPC)
      ↓                      ↓                         ↓
*.corp.local          *.gcp.example.com        VPC Resources
```
- Forward `*.gcp.example.com` from on-prem to Cloud DNS
- Forward `*.corp.local` from Cloud DNS to on-prem

**Pattern 2: Multi-VPC DNS Peering**
```
VPC A (Shared VPC)          VPC B (Service Project)
  Private Zone                 Peering Zone
  └── db.shared.local   ←──── Points to VPC A
```
- VPC B can resolve `db.shared.local` defined in VPC A
- Requires DNS peering configuration

**Exam Decision Matrix:**

| Requirement | Solution | Key Points |
|-------------|----------|------------|
| Public domain hosting | Public Zone | Internet-facing, anycast |
| Internal service discovery | Private Zone | VPC-scoped, automatic |
| On-prem ↔ GCP name resolution | Forwarding Zone | Hybrid cloud DNS |
| Cross-VPC DNS resolution | Peering Zone | Multi-project DNS sharing |
| Prevent DNS spoofing | DNSSEC | Enable on public zones |
| Internal/external split DNS | Split-Horizon | Same name, different IPs |
| Multi-region failover | Routing Policies | Geo/weighted routing |

**Best Practices:**

* Use private zones for internal services (avoid hardcoded IPs)
* Enable DNSSEC for public-facing domains
* Use forwarding zones for hybrid connectivity (not VPN DNS servers)
* Implement split-horizon DNS to reduce egress costs
* Set appropriate TTLs (low TTL for dynamic services, high for static)

**Common Pitfalls:**

* Forgetting bidirectional DNS forwarding in hybrid setups
* Overlapping private zone names across VPCs (causes conflicts)
* Not configuring DNS peering for multi-VPC architectures
* Using public zones for internal service names (security risk)

**Exam Triggers:**

* "On-prem needs to resolve GCP private DNS" → **Forwarding Zone + Cloud VPN/Interconnect**
* "Cross-project DNS resolution without VPC peering" → **DNS Peering Zones**
* "Prevent DNS cache poisoning" → **Enable DNSSEC**
* "Different IPs for internal vs external clients" → **Split-Horizon DNS**
* "Route users to nearest region" → **Geo-based routing policy**

---

## Traffic Director

**Purpose:** Global load balancing and traffic management for service mesh architectures.

**Key Capabilities:**

* **Service Mesh Control Plane:** Manages Envoy proxies (sidecar or standalone)
* **Global Traffic Management:** Route traffic across regions, clusters, and clouds
* **Advanced Routing:** Header-based, weighted, locality-aware routing
* **Health Checking:** Proactive health checks and automatic failover
* **Multi-Cloud Support:** Works with GKE, Compute Engine, and external endpoints

**Architecture:**

```
Traffic Director (Control Plane)
        ↓
    Envoy Proxies (Data Plane)
        ↓
   Backend Services (GKE, GCE, On-prem)
```

**Use Cases:**

* Multi-region service mesh with intelligent routing
* Hybrid/multi-cloud traffic management
* Advanced traffic splitting (canary deployments, A/B testing)
* Locality-aware routing (prefer closest backends)

**Integration:**

* **GKE:** Integrates with Anthos Service Mesh (ASM)
* **Compute Engine:** Envoy proxies on VMs
* **Cloud Run:** Can route traffic to Cloud Run services

**Exam Scenarios:**

* "Global traffic management for microservices across GKE clusters" → **Traffic Director**
* "Service mesh with Envoy proxies" → **Traffic Director + ASM**
* "Intelligent routing based on HTTP headers" → **Traffic Director routing rules**
* "Multi-cloud load balancing" → **Traffic Director with external backends**

**Exam trigger:** *Advanced service mesh, multi-region routing, Envoy-based load balancing*

---

## Network Intelligence Center
- Network observability and troubleshooting suite.
- Includes:
  - Connectivity Tests
  - Firewall Insights
  - Performance Dashboard

- **Network Topology:** Visualizes VPC fabric and traffic.
- **Flow Analyzer:** Simplifies VPC Flow Log analysis for cost/performance.

**Exam trigger:** *Validate connectivity before deployment*

---

## Cost-Driven Design Decisions
- Avoid public IPs → Cloud NAT.
- Prefer global load balancers over custom failover logic.
- Use Shared VPC to reduce duplicated networking cost.
- Peering and internal LBs reduce egress costs.

## IoT Networking
- Use TCP/SSL Proxy Load Balancers and MQTT brokers (e.g., EMQX) for device fleets.
- Cloud IoT Core is deprecated—use third-party solutions.

**Exam trigger:** *IoT device management at scale*

**Exam trigger:** *Minimize cost and operational overhead*
