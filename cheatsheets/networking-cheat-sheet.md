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

## Cloud DNS
- Managed DNS service.
- Public and Private DNS zones.
- DNS forwarding enables hybrid name resolution.

**Forwarding Zones:** Forward DNS queries to on-prem servers.
**Peering Zones:** Enable cross-VPC DNS resolution.

**Exam trigger:** *On-prem ↔ GCP name resolution*

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
