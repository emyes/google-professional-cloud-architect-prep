# Google Cloud IAM & Cloud Identity

## Modern Identity-Centric Security – 10-Minute Recollection Cheat Sheet

---

## 1. Identity Is the New Perimeter (Core Mental Model)

Traditional security:

* Trusted internal network
* VPN-based access
* Implicit trust once inside

Modern reality:

* Hybrid + multi-cloud
* SaaS everywhere
* Remote workforce
* APIs and automation dominate

**Modern model (Zero Trust):**

* Never trust, always verify
* Identity + context = access decision
* Location is irrelevant
* Credentials must be short-lived

👉 **IAM is the primary control plane**

---

## 2. Resource Hierarchy = Governance Backbone

IAM only makes sense **in the context of hierarchy**.

```
Organization
 └── Folder
     └── Project
         └── Resource
```

### Key Rules

* Policies **inherit downward**
* Effective permissions = **union of all allows**
* **IAM Deny overrides everything**
* Child cannot negate parent allows (except via deny)

### Strategic Use

* **Organization** → global guardrails
* **Folders** → environment or business isolation (prod/dev)
* **Projects** → workload-level access

🚨 Mistake to avoid:
Granting broad roles at Org/Folder level without constraints.

---

## 3. IAM Fundamentals (Who / What / How / When)

### Principals (WHO)

* Users (emails)
* **Groups (best practice)**
* Service Accounts
* Domains
* Workload identity principals
* Workforce identity principals

📌 Always bind roles to **groups**, not individuals.

---

### Roles (WHAT)

A role = collection of permissions.

| Role Type                   | Description          | Guidance         |
| --------------------------- | -------------------- | ---------------- |
| Basic (Owner/Editor/Viewer) | Broad, legacy        | ❌ Avoid in prod  |
| Predefined                  | Service-specific     | ✅ Default choice |
| Custom                      | Tailored permissions | ⚠️ Use sparingly |

Role lifecycle: ALPHA → BETA → GA → DEPRECATED

---

### Policies (HOW)

IAM policy = bindings of:

```
principal + role + (optional condition)
```

* Can exist at any level
* Conditions evaluated at **request time**
* Multiple bindings accumulate

---

### Policy Evaluation Logic (IMPORTANT)

1. Collect all applicable policies
2. Evaluate **IAM Deny policies first**
3. Evaluate conditions
4. If any allow matches → access granted

👉 **Explicit deny always wins**

---

## 4. Principle of Least Privilege (PoLP)

Core idea:

> Grant **only** what is needed, **only** when needed

How GCP enables PoLP:

* Granular predefined roles
* Custom roles
* IAM Conditions
* Recommender automation

Common failure mode:

* Permissions accumulate over time (permission drift)

---

## 5. Securing Human Access (Workforce)

### Cloud Identity

Identity platform for:

* Users
* Groups
* Devices
* SSO

Key capabilities:

* Single Sign-On (SSO)
* Multi-Factor Authentication (MFA)
* Endpoint management (corp + BYOD)

Cloud Identity vs Workspace:

* Cloud Identity = IAM only
* Workspace = IAM + Gmail/Docs

---

### BeyondCorp / Zero Trust

No VPN. No network trust.

Access based on:

* Identity
* Device posture
* Location
* Context

Implemented via:

* **Access Context Manager**
* IAM Conditions

Examples:

* Allow admin access only from managed devices
* Time-bound access for contractors
* IP + device posture enforcement

---

### Identity-Aware Proxy (IAP)

**"The VPN Killer"**

*   **Purpose**: Secure access to HTTP/HTTPS apps (and SSH/RDP) without a VPN.
*   **How it works**: Intercepts requests, authenticates identity, checks context (via Access Context Manager), then forwards traffic.
*   **Key Use Case**: Exposing internal admin panels or on-prem apps securely to internet.
*   **TCP Forwarding**: Can wrap SSH/RDP traffic (tunneling through IAP) → No public IP needed on VMs.

---

### Workforce Identity Federation

For **external human users**:

* Azure AD
* Okta
* ADFS

Characteristics:

* OIDC / SAML
* No user sync required
* External IdP remains source of truth

Use cases:

* Partners
* Contractors
* Multi-cloud workforce

---

## 6. Securing Workloads (Non-Human Identity)

### Service Accounts (In-GCP Workloads)

Best practices:

* One SA per workload
* Minimal permissions
* No shared service accounts
* Avoid default service accounts

---

### Service Account Keys (Legacy – Avoid)

Problems:

* Long-lived
* Leak easily
* Hard to rotate
* Poor attribution

🚨 Treat keys as **high-risk secrets**

---

### Service Account Impersonation (Modern In-GCP)

Preferred for:

* Humans running admin commands
* Automation inside GCP

Key roles:

* `roles/iam.serviceAccountUser`
* `roles/iam.serviceAccountTokenCreator`

Benefits:

* Short-lived credentials
* Clear audit trail
* No key storage

---

### Service Account Roles Deep Dive

#### `roles/iam.serviceAccountUser`

**Purpose:** Allows a principal to **use** a service account when creating/managing resources (effectively "acting as" the service account).

**Permissions Included:**
```
iam.serviceAccounts.actAs
iam.serviceAccounts.get
iam.serviceAccounts.list
```

**When Required:**
- Creating Compute Engine instances with a service account
- Deploying Cloud Run services with a service account
- Creating Cloud Functions with a service account
- Running Cloud Scheduler jobs as a service account
- Deploying App Engine with a service account

**Example:**
```bash
# Grant serviceAccountUser role
gcloud iam service-accounts add-iam-policy-binding SA_EMAIL \
  --member="user:developer@example.com" \
  --role="roles/iam.serviceAccountUser"

# Developer can now create VM with SA
gcloud compute instances create my-vm \
  --service-account=SA_EMAIL \
  --scopes=cloud-platform
```

**Exam Scenarios:**
- "Developer cannot attach service account to Cloud Run" → Missing `serviceAccountUser`
- "Implement least privilege for VM creation" → Grant `serviceAccountUser` at SA level (not project)
- "CI/CD pipeline needs to deploy with service account" → Grant `serviceAccountUser`

---

#### `roles/iam.serviceAccountTokenCreator`

**Purpose:** Allows generating short-lived OAuth2 access tokens and OpenID Connect ID tokens for a service account.

**Permissions Included:**
```
iam.serviceAccounts.getAccessToken
iam.serviceAccounts.getOpenIdToken
iam.serviceAccounts.implicitDelegation
```

**When Required:**
- Impersonating service account for API calls
- Generating access tokens programmatically
- Admin running commands as service account
- Cross-project service account access

**Example:**
```bash
# Grant tokenCreator role
gcloud iam service-accounts add-iam-policy-binding SA_EMAIL \
  --member="user:admin@example.com" \
  --role="roles/iam.serviceAccountTokenCreator"

# Admin can impersonate SA
gcloud compute instances list \
  --impersonate-service-account=SA_EMAIL
```

**Exam Scenarios:**
- "Admin needs to run commands as service account" → Grant `serviceAccountTokenCreator`
- "Generate short-lived credentials for external system" → Use `serviceAccountTokenCreator`
- "Enable service account impersonation" → Grant both `serviceAccountUser` + `serviceAccountTokenCreator`

---

#### `roles/iam.serviceAccountKeyAdmin`

**Purpose:** Create and delete service account keys (🚨 **Avoid in production!**)

**Permissions Included:**
```
iam.serviceAccountKeys.create
iam.serviceAccountKeys.delete
iam.serviceAccountKeys.get
iam.serviceAccountKeys.list
```

**When to Use (Rarely):**
- Legacy systems requiring key-based auth
- Temporary migration scenarios
- On-premises systems (use Workload Identity Federation instead)

**Security Warning:**
❌ Avoid service account keys whenever possible
✅ Use Workload Identity Federation or impersonation instead

---

#### Service Account Roles Comparison

| Role | Purpose | Grants Access To | Use Case |
|------|---------|------------------|----------|
| `serviceAccountUser` | Attach SA to resources | Resource creation APIs | Deploy Cloud Run with SA |
| `serviceAccountTokenCreator` | Generate tokens | Token generation APIs | Impersonate SA for gcloud |
| `serviceAccountKeyAdmin` | Manage SA keys | Key management APIs | Create keys (avoid!) |
| `serviceAccountAdmin` | Full SA management | All SA operations | Manage service accounts |

**Combined Usage Pattern:**
```
serviceAccountUser + serviceAccountTokenCreator = Full Impersonation
```
- User can both run commands as SA and deploy resources with SA

**Exam Decision Matrix:**

| Scenario | Required Role |
|----------|---------------|
| "Create VM with service account X" | `serviceAccountUser` on SA X |
| "Run gcloud as service account X" | `serviceAccountTokenCreator` on SA X |
| "Full impersonation of SA X" | Both `serviceAccountUser` + `serviceAccountTokenCreator` |
| "Deploy Cloud Run with SA X" | `serviceAccountUser` on SA X |
| "Generate access token for SA X" | `serviceAccountTokenCreator` on SA X |

**Best Practices:**
✅ Grant at service account level (not project-wide)
✅ Use principle of least privilege
✅ Audit role assignments regularly
✅ Prefer impersonation over keys
✅ Document why each role is needed

❌ Don't grant `serviceAccountUser` at project level
❌ Don't use `serviceAccountKeyAdmin` unless absolutely necessary
❌ Don't grant both roles unless full impersonation is required

---

### Workload Identity Federation (Modern Standard)

For **external workloads**:

* GitHub Actions
* AWS
* Azure
* On-prem

How it works:

* External workload authenticates to its IdP
* Presents OIDC token to Google STS
* Receives short-lived GCP token

Benefits:

* No static keys
* Token expiration limits blast radius
* Strong auditability

---

## 7. Enterprise Guardrails (Defense in Depth)

### IAM Deny Policies

Purpose:

* Establish **non-negotiable rules**

Characteristics:

* Override all allows
* Apply at Org / Folder / Project
* Can be conditional

Examples:

* Deny service account key creation
* Deny deletion of audit logs
* Deny IAM changes outside approved tags

IAM Deny = **Principal-centric control**

---

### Organization Policies

Purpose:

* Control **what resources are allowed to do**

Examples:

* Restrict regions
* Disable public IPs
* Disable external service accounts

Org Policy = **Resource-centric control**

📌 Complementary to IAM Deny, not a replacement.

---

### Organization Policy Service (Deep Dive)

**Purpose:** Centralized configuration management with policy constraints enforced across the resource hierarchy.

**Key Concepts:**

* **Policy Constraints:** Rules that restrict resource configurations
  - **List Constraints:** Allowed/denied values (e.g., `constraints/compute.vmExternalIpAccess`)
  - **Boolean Constraints:** Enforce or disable a behavior (e.g., `constraints/sql.restrictPublicIp`)
  - **Custom Constraints:** User-defined constraints using CEL (Common Expression Language)

* **Policy Inheritance:** Policies applied at organization level flow down to folders → projects → resources
  - Child policies can be more restrictive but not more permissive
  - Use `replace` to override parent policies (requires `orgpolicy.policy.set` permission)

* **Policy Evaluation:**
  1. Organization policy evaluated first
  2. Folder policies evaluated (hierarchically)
  3. Project policies evaluated last
  4. Most restrictive policy wins

**Common Constraints (Exam Favorites):**

| Constraint | Effect | Use Case |
|------------|--------|----------|
| `constraints/compute.vmExternalIpAccess` | Deny external IPs on VMs | Force Cloud NAT usage |
| `constraints/iam.disableServiceAccountKeyCreation` | Prevent service account key creation | Enforce Workload Identity |
| `constraints/compute.restrictLoadBalancerCreationForTypes` | Limit LB types | Enforce internal-only LBs |
| `constraints/gcp.resourceLocations` | Restrict regions | Data residency compliance |
| `constraints/sql.restrictPublicIp` | Prevent public Cloud SQL IPs | Security hardening |
| `constraints/storage.uniformBucketLevelAccess` | Enforce uniform bucket ACLs | Simplify access control |

**Organization Policy vs IAM:**

| Aspect | Organization Policy | IAM |
|--------|-------------------|-----|
| Controls | **What** can be done | **Who** can do it |
| Scope | Resource configuration | Identity permissions |
| Example | "No external IPs allowed" | "User X can create VMs" |
| Evaluation | Evaluated before IAM | Evaluated after Org Policy |

**Best Practices:**

* Set restrictive policies at org level, relax at folder/project for exceptions
* Use tagging with conditional org policies for granular control
* Test policies in dry-run mode before enforcement
* Document policy rationale for audit compliance

**Exam Scenarios:**

* "Prevent developers from creating VMs with external IPs" → `constraints/compute.vmExternalIpAccess`
* "Ensure all Cloud SQL instances are private" → `constraints/sql.restrictPublicIp`
* "Enforce data residency in EU regions only" → `constraints/gcp.resourceLocations`
* "Eliminate service account key sprawl" → `constraints/iam.disableServiceAccountKeyCreation`

---

### VPC Service Controls

Purpose:

* Prevent **data exfiltration**

How:

* Service perimeters around Google APIs

Protects against:

* IAM misconfigurations
* Credential compromise
* Insider threats

Advanced use:

* Protect IAM APIs themselves
* Ensure security admin actions only from trusted networks

---

## 8. Monitoring, Audit, and Continuous Governance

### Cloud Audit Logs

* **Admin Activity** (enabled by default)
* **Data Access** (must enable explicitly)
* System Events
* Policy Denied logs

Compliance note:

* Data Access logs may increase cost
* Enable selectively for sensitive services

---

### IAM Recommender

* Detects unused permissions
* Suggests role downsizing
* Automates least privilege

Solves:

* Permission drift
* Manual review fatigue

---

### Cloud Asset Inventory

Answers:

* Who has access to what?
* What changed and when?
* Point-in-time snapshots for audits

Critical for:

* Compliance
* Incident response
* Access reviews

---

## 9. Design Best Practices (Quick Recall)

✅ Groups over users
✅ Predefined roles over basic
✅ Custom roles only when necessary
✅ Federation over keys
✅ Impersonation over keys
✅ Org policies + IAM deny for guardrails
✅ Continuous monitoring and cleanup

---

## 10. Strategic Takeaway

Modern GCP security =
**Identity-first + context-aware + short-lived + layered controls**

Google Cloud IAM enables:

* Zero Trust at scale
* Keyless workloads
* Immutable guardrails
* Continuous governance

---

## 11. Additional Key Exam Topics (PCA Specific)

### Google Cloud Directory Sync (GCDS)
*   **Purpose**: Synchronize users and groups from on-premises LDAP/Active Directory to Cloud Identity.
*   **Direction**: One-way sync (On-prem → Google Cloud). Does not write back to AD.
*   **Mechanism**: Runs as a utility on-premises.

### Cloud Identity Editions
*   **Free Edition**: Core identity services, basic endpoint management, SSO.
*   **Premium Edition**: Advanced endpoint management, SLA (99.9%), audit logs, automated user provisioning.

### Troubleshooting & Debugging
*   **Policy Troubleshooter**: Visual tool to determine *why* a principal has (or doesn't have) a permission on a resource. Checks IAM policies and group memberships.
*   **Admin Activity Logs**: Who did what, where, and when.

### Just-in-Time (JIT) Access
*   **Privileged Access Manager (PAM)**: Request temporary, time-bound elevation of privileges.
*   **Custom Solutions**: Often built using Cloud Functions + Cloud Scheduler to grant/revoke roles temporarily.
