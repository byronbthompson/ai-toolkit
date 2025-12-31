DEVOPS CHANGE BUILDER - INFRASTRUCTURE AS CODE

Build infrastructure changes with IaC best practices, brownfield awareness, and breaking change protection.

**CRITICAL**: This workflow adapts to complexity - quick changes stay quick, complex changes get proper depth.

---

## QUICK START (2 questions to determine depth)

Ask user TWO questions upfront to determine workflow depth:

**Question 1**: "What infrastructure change do you need? (1-2 sentences)"
- Example: "Add Redis cache to production"
- Example: "Scale up database tier from S1 to S2"
- Example: "Implement multi-region failover with geo-replication"

**Question 2**: "Is this a SIMPLE change (well-understood, low risk) or COMPLEX change (new territory, high risk)?"

**SIMPLE change indicators**:
- Single resource modification (resize, scale, config tweak)
- Well-known operation (done before, documented)
- Low risk (easily reversible, dev/staging only, non-breaking)
- Clear requirements (no ambiguity)

**COMPLEX change indicators**:
- Multiple resources or new architecture
- First time doing this type of change
- High risk (production, breaking changes, data migration)
- Unclear requirements or many unknowns

**Based on answer, route to appropriate workflow**:

---

## SIMPLE WORKFLOW (Quick Execution - 15-30 min)

**When to use**: Simple, low-risk, well-understood infrastructure changes

### Step 1: Minimal Context (1 question at a time)

**Ticket/Context**:
- Ask: "Do you have a ticket ID? (or should I auto-generate one?)"
  - If yes: Fetch ticket details or ask user to paste
  - If no: Generate ID: `INFRA-<date>-<short-name>`

**Environments**:
- Ask: "Which environment(s)? (dev/staging/prod/all)"
  - Default deployment order: dev → staging → prod

**Production Reference Check**:
- Ask: "Are there similar resources already running in production that I should reference?"
  - If YES: Ask for resource names/IDs or ask user to describe them
  - Fetch production resource details to understand patterns:
    ```bash
    # Azure: Get resource details
    az resource show --ids <resource-id> --output json

    # AWS: Describe resource
    aws <service> describe-<resource> --<resource-name> <name>

    # Get resource tags/labels for naming patterns
    ```
  - Use production patterns for: naming conventions, tags, sizing, configuration
  - Document in plan: "Based on production resource: <name>"
  - If NO: Proceed with best practices

**IaC Discovery**:
- **First**: Automatically search for existing IaC files:
  ```bash
  # Search for BICEP files
  find . -name "*.bicep" -o -name "*.bicepparam"

  # Search for Terraform files
  find . -name "*.tf" -o -name "*.tfvars"

  # Search for CloudFormation files
  find . -name "*.yaml" -o -name "*.yml" -o -name "*.json" | grep -i "cloudformation\|cfn\|stack"

  # Search for ARM templates
  find . -name "*.json" | grep -i "template\|arm"

  # Search for Pulumi files
  find . -name "Pulumi.yaml" -o -name "Pulumi.*.yaml"
  ```
- **If IaC files found**:
  - Report: "Found existing <tool> files at: <paths>"
  - Ask: "Should I use the existing <tool> setup, or switch to a different tool?"
  - **PREFER**: Using existing tool for consistency
- **If no IaC files found**:
  - Ask: "What IaC tool? (terraform/bicep/cloudformation/pulumi/manual)"
  - If manual: Recommend IaC tool based on cloud provider

### Step 2: Quick Plan

Create lightweight `/specs/DEVOPS_<change>.md`:

```markdown
# DevOps Change - <Change Name>

**Ticket**: <ticket-id>
**Type**: Simple (low-risk)
**Environments**: <list>
**Production Reference**: <resource name, or "None">
**Created**: <date>

## Objective
<1-2 sentence description of what and why>

## Production Reference (if applicable)
**Reference Resource**: <name/ID of similar production resource>
**Patterns Applied**:
- Naming: <pattern from production>
- Tags: <tags from production>
- Sizing: <SKU/tier from production>
- Config: <key config settings from production>

## Changes
- <Resource 1>: <what will change>
- <Resource 2>: <what will change>

## Commands

### Validation (read-only)
```bash
<IaC plan command>
```

### Apply
```bash
<IaC apply command>
```

### Verify
```bash
<verification commands>
```

### Rollback (if needed)
```bash
<rollback commands>
```

## Acceptance Criteria
- [ ] <Criterion 1>
- [ ] <Criterion 2>
- [ ] <Criterion 3>

---

Generated using devops-change-builder.md (simple workflow)
```

### Step 3: Execute

**For each environment**:
1. Run validation command (plan/what-if)
2. Review output with user: "Plan looks correct? Any unexpected changes?"
3. If approved: Apply changes
4. Run verification commands
5. Confirm acceptance criteria met

**Quality Gates** (lightweight):
- [ ] Plan shows only expected changes
- [ ] Apply succeeds (no errors)
- [ ] Verification commands pass
- [ ] Acceptance criteria validated

### Step 4: Commit and Document

**Commit message**:
```
infra: <short description>

Environment: <env>
Changes: <list changes>
Verified: <verification results>

Ticket: <ticket-id>
```

**Quick learning capture** (in LEARNINGS.md if part of larger project):
```markdown
### Infrastructure Change - <date>
- **Change**: <what was done>
- **Duration**: <X minutes>
- **Notes**: <any gotchas or tips for next time>
```

**Skip**: Brownfield analysis, breaking change analysis, multi-phase planning
**Why**: Simple changes don't need heavyweight process

---

## COMPLEX WORKFLOW (In-Depth Analysis - 1-3 hours)

**When to use**: Complex, high-risk, or novel infrastructure changes

### PHASE 1: TICKET DISCOVERY AND DEFINITION

#### Step 1A: Check for Existing Ticket

Ask user ONE question:

**"Do you have an existing ticket (ID or URL) for this infrastructure change?"**

**IF YES**:
- Ask for ticket ID or URL
- Attempt to fetch ticket details using available tools:
  - GitHub Issues: `gh issue view <number>`
  - Azure DevOps: `az boards work-item show --id <id>`
  - Jira: Use Jira API if configured
  - Or ask user to paste ticket content

**IF NO or fetch fails**:
- Proceed to Step 1B (manual ticket definition)

#### Step 1B: Define Infrastructure Change Ticket

If no ticket exists or fetch failed, gather information ONE question at a time:

**Question 1**: "What infrastructure change do you need to make? (1-2 sentence summary)"

**Question 2**: "What business problem does this solve or what feature does it enable?"

**Question 3**: "What environment(s) are affected?"
- Dev only, Staging, Production, All environments
- If multi-env: deployment order and validation strategy

**Question 4**: "Is this infrastructure NEW (greenfield) or modifying EXISTING resources (brownfield)?"
- Greenfield: Creating new resources from scratch
- Brownfield: Modifying/extending existing infrastructure
- Mixed: Some new, some modifications

**Question 5**: "What is the urgency and available time window?"
- Immediate (breaking production), Urgent (1-2 days), Standard (1-2 weeks), Planned (flexible)
- This determines backwards compatibility approach

**Question 6**: "Does this infrastructure involve networking components (VPN, firewalls, edge devices, B2B connectivity)?"
- If YES:
  - VPN Gateway (Site-to-Site or Point-to-Site for customer/partner connectivity)
  - Edge devices (Firewalls, SD-WAN, Network Virtual Appliances)
  - B2B customer connectivity (connecting to untrusted external customer infrastructure)
  - DMZ for customer-facing services
  - Private endpoints / Private Link
  - Network security groups / Security groups
  - Load balancers / Application gateways
- If NO: Skip network-specific planning

**Question 7**: "Are there any compliance, security, or cost constraints?"
- Compliance requirements (HIPAA, SOC2, GDPR, ISO 27001, etc.)
- Security policies (network isolation, encryption, access control, Zero Trust)
- DMZ requirements for B2B scenarios
- Budget constraints

Generate ticket structure:
```markdown
# Infrastructure Change Ticket

**ID**: <ticket-id> or AUTO-GENERATED
**Title**: <short-name>
**Created**: <date>
**Priority**: <Immediate/Urgent/Standard/Planned>

## Summary
<1-2 sentence description>

## Business Context
<Why this change is needed>

## Environments Affected
- [ ] Development
- [ ] Staging
- [ ] Production
- [ ] Other: ___

## Project Type
- [ ] Greenfield (new resources)
- [ ] Brownfield (existing resources)
- [ ] Mixed

## Time Constraints
- **Urgency**: <Immediate/Urgent/Standard/Planned>
- **Available time window**: <X hours/days/weeks>
- **Backwards compatibility required**: <Yes/No - based on time available>

## Constraints
- Compliance: <requirements>
- Security: <policies>
- Cost: <budget limits>

## Network Components (if applicable)
- [ ] VPN Gateway (Site-to-Site / Point-to-Site)
- [ ] Edge Devices (Firewall, NVA, SD-WAN)
- [ ] B2B Customer Connectivity
- [ ] DMZ for untrusted traffic
- [ ] Private Endpoints
- [ ] Network Security
```

---

### PHASE 2: BROWNFIELD CONTEXT DISCOVERY

**IF brownfield or mixed** (from Question 4):

#### Step 2A: Identify Existing Infrastructure

**CRITICAL**: Must understand current state before making changes.

**FIRST: Automatically discover existing IaC files**:

Run discovery commands to locate existing template files:

```bash
# Search for BICEP files
find . -name "*.bicep" -o -name "*.bicepparam" 2>/dev/null

# Search for Terraform files
find . -name "*.tf" -o -name "*.tfvars" 2>/dev/null

# Search for CloudFormation files
find . \( -name "*.yaml" -o -name "*.yml" -o -name "*.json" \) -exec grep -l "AWSTemplateFormatVersion\|Resources:" {} \; 2>/dev/null

# Search for ARM templates
find . -name "*.json" -exec grep -l "resources\|Microsoft\." {} \; 2>/dev/null

# Search for Pulumi files
find . -name "Pulumi.yaml" -o -name "Pulumi.*.yaml" -o -name "index.ts" -o -name "__main__.py" 2>/dev/null

# Search for Ansible playbooks
find . -name "*.yml" -o -name "*.yaml" -exec grep -l "hosts:\|tasks:" {} \; 2>/dev/null
```

**Report findings to user**:
- If IaC files found: "Found existing <tool> infrastructure files:"
  - List all discovered files with paths
  - Show sample of what's defined (resource types)
  - Ask: "Should I extend these existing templates or create new ones?"
- If no IaC files found: Proceed to manual questions below

**THEN: Ask user ONE question at a time** (only if discovery unclear):

**Question 1**: "What IaC tool/approach is currently used?"
- Terraform, Azure Bicep, ARM templates, AWS CloudFormation, Pulumi, Ansible
- Manual (no IaC - migration opportunity)
- Mixed (some IaC, some manual)
- **AUTO-ANSWER if files found**: Use discovered tool

**Question 2**: "Where is the current infrastructure code located?"
- Repository URL and path
- Or: "No existing code, infrastructure was created manually"
- **SKIP if files already discovered**

**Question 3**: "Are there similar resources already running in production that I should reference for patterns?"
- If YES:
  - Ask: "What are the resource names/IDs of similar production resources?"
  - Fetch details to learn patterns (naming, tags, sizing, configuration)
  - Document: "Production reference: <resource-name>"
- If NO: Proceed with standard best practices

**Question 4**: "Do you have read access to the target environment to inspect current state?"
- If YES: Proceed to automated discovery
- If NO: Ask user to provide output of specific commands

#### Step 2B: Automated Infrastructure Discovery

Based on cloud provider and available access, run discovery commands:

**Azure**:
```bash
# List resource groups
az group list --output table

# List resources in target resource group
az resource list --resource-group <rg-name> --output table

# Get specific resource details (example: App Service)
az webapp show --name <app-name> --resource-group <rg-name> --output json

# Export resource group to ARM template (snapshot of current state)
az group export --name <rg-name> --output json > current-state.json
```

**AWS**:
```bash
# List resources by region
aws resourcegroupstaggingapi get-resources --region <region>

# CloudFormation stack details
aws cloudformation describe-stacks --stack-name <stack-name>

# Export stack template
aws cloudformation get-template --stack-name <stack-name>
```

**GCP**:
```bash
# List resources by project
gcloud asset search-all-resources --project=<project-id>

# Deployment Manager details
gcloud deployment-manager deployments describe <deployment-name>
```

**Kubernetes**:
```bash
# Current deployments/services/ingress
kubectl get all --all-namespaces
kubectl get ingress --all-namespaces
kubectl get pvc --all-namespaces
```

#### Step 2C: Generate Brownfield Context Document

Create `/specs/00_BROWNFIELD_CONTEXT.md`:

```markdown
# Brownfield Infrastructure Context

**Project**: <ticket-id> - <short-name>
**Discovery Date**: <date>
**Environments**: <list>

## Current Infrastructure State

### IaC Tool and Existing Templates
- **Current Tool**: <Terraform/Bicep/Manual/etc>
- **Version**: <version if applicable>
- **Repository**: <URL and path>
- **Discovered Template Files**:
  - BICEP: <list .bicep files found>
  - Terraform: <list .tf files found>
  - CloudFormation: <list template files found>
  - ARM: <list .json template files found>
  - Pulumi: <list pulumi files found>
  - Other: <any other IaC files found>
- **Template Analysis**:
  - Resources defined: <list key resources in existing templates>
  - Modules/reusable components: <list any modules>
  - Parameter files: <list parameter/variable files>
- **Recommendation**: <Extend existing templates / Create new / Hybrid approach>

### Existing Resources Inventory

| Resource Type | Name | Location/Region | Configuration Notes | Dependencies |
|--------------|------|-----------------|---------------------|--------------|
| <type> | <name> | <region> | <config> | <deps> |

### Existing Patterns and Conventions
- **Naming convention**: <pattern discovered>
- **Tagging strategy**: <tags used>
- **Network topology**: <VNet structure>
- **Access control pattern**: <RBAC/IAM approach>

### Production Reference Resources
- **Similar resources in production**: <Yes/No>
- **Reference resource name(s)**: <list production resources used as reference>
- **Key patterns learned**:
  - Naming: <naming pattern from production>
  - Sizing/SKU: <tier/size from production>
  - Configuration: <key config settings from production>
  - Tags: <standard tags from production>
  - Security: <security configuration from production>
- **Why this reference was chosen**: <relevance to current change>

### Critical Dependencies
- **Upstream dependencies**: <what depends on this infrastructure>
- **Downstream dependencies**: <what this infrastructure depends on>
- **Data flows**: <how data moves through infrastructure>

### Risk Areas
- **HIGH RISK**: <resources that cannot have downtime>
- **MEDIUM RISK**: <resources with complex dependencies>
- **LOW RISK**: <resources safe to modify>

### Network Infrastructure (if applicable)
- **VPN Gateways**:
  - Existing VPN connections: <list Site-to-Site or Point-to-Site VPNs>
  - Customer/Partner VPN tunnels: <list B2B connections>
  - VPN throughput/SKU: <current capacity>
- **Edge Devices**:
  - Firewalls/NVAs: <list devices, make/model, version>
  - Placement: <DMZ subnet, Hub VNet, etc.>
  - High availability configuration: <active-passive, active-active>
- **B2B Connectivity**:
  - External customer networks: <list customer connections>
  - Connection methods: <VPN, ExpressRoute, API Gateway, etc.>
  - DMZ subnets: <list DMZ subnets for untrusted traffic>
  - IP overlap issues: <any NAT configurations>
- **Network Security**:
  - NSGs/Security Groups: <list critical security groups>
  - Private endpoints: <list private endpoint connections>
  - WAF policies: <Web Application Firewall configurations>
```

**MANDATORY**: User must review and confirm brownfield context before proceeding.

Ask: **"Does this brownfield context accurately represent your current infrastructure?"**

---

### PHASE 3: BREAKING CHANGE ANALYSIS

**CRITICAL**: Identify potential breaking changes BEFORE implementation.

#### Step 3A: Breaking Change Detection

Analyze proposed change for breaking characteristics:

**Infrastructure Breaking Changes Checklist**:
- [ ] **Resource deletion**: Deleting existing resources (data loss risk)
- [ ] **Resource recreation**: Forcing resource replacement (downtime risk)
- [ ] **Network changes**: Changing IP ranges, DNS, endpoints (connectivity risk)
- [ ] **VPN changes**: Modifying VPN Gateway, changing PSK, changing customer IP ranges (B2B connectivity loss)
- [ ] **Edge device changes**: Replacing firewall/NVA (traffic interruption)
- [ ] **DMZ modifications**: Changes to customer-facing DMZ subnet (B2B service disruption)
- [ ] **Access control changes**: Removing permissions (access denial risk)
- [ ] **NSG/Security Group changes**: Removing rules that customers depend on (connection failures)
- [ ] **Configuration changes**: Changing ports, protocols, TLS versions (compatibility risk)
- [ ] **Data migration**: Moving data between resources (data loss/corruption risk)
- [ ] **Scaling down**: Reducing capacity (performance degradation risk)
- [ ] **Version upgrades**: Upgrading databases, runtimes (compatibility risk)
- [ ] **API changes**: Modifying API endpoints or contracts (client breakage risk)
- [ ] **Dependency changes**: Changing upstream/downstream dependencies (integration risk)
- [ ] **Private endpoint changes**: Moving or removing private endpoints (PaaS connectivity loss)

**For EACH breaking change identified**:

```markdown
### Breaking Change: <change description>

**Category**: <Resource deletion/Network change/etc>
**Risk Level**: <HIGH/MEDIUM/LOW>
**Impact**: <What will break and for whom>

**Mitigation Options**:
1. **Backwards-compatible approach** (preferred):
   - Description: <How to achieve change without breaking>
   - Time required: <X hours/days>

2. **Blue-green deployment**:
   - Description: <Run old and new side-by-side>
   - Time required: <X hours/days>

3. **Breaking change with migration plan**:
   - Description: <Accept breaking change, plan migration>
   - Time required: <X hours/days>

**Recommended Approach**: <Option 1/2/3 based on time constraints>
```

#### Step 3B: Backwards Compatibility Strategy

Based on time constraints from Phase 1:

**IF time is FLEXIBLE (Standard/Planned)**:
- **PREFER**: Backwards-compatible approach (no breaking changes)
- Strategy: Blue-green deployment, feature flags, versioned APIs, additive changes only

**IF time is CONSTRAINED (Urgent/Immediate)**:
- **ACCEPT**: Breaking changes if backwards compatibility too costly
- **REQUIRE**: Comprehensive migration plan with rollback strategy

Ask user ONE question:

**"I've identified <N> potential breaking changes. Based on your time constraints (<urgency>), I recommend <backwards-compatible/breaking-with-migration> approach. Do you want to review breaking change details before proceeding?"**

**IF YES**: Present breaking change analysis from Step 3A
**IF NO**: Proceed with recommended approach

---

### PHASE 4: INFRASTRUCTURE DOCUMENTATION DISCOVERY

#### Step 4A: Determine Required Documentation

Ask user: **"What infrastructure resources/services are involved in this change?"**

Examples:
- Azure: App Service, SQL Database, Redis Cache, Virtual Network, Key Vault
- AWS: EC2, RDS, ElastiCache, VPC, Secrets Manager
- GCP: Compute Engine, Cloud SQL, Memorystore, VPC, Secret Manager
- Kubernetes: Deployment, Service, Ingress, PersistentVolumeClaim, ConfigMap

#### Step 4B: Fetch Official Documentation

For EACH resource type, attempt to fetch relevant documentation:

**Method 1: Web Search for Official Docs**
```
Search: "<cloud-provider> <resource-type> IaC documentation 2025"
Example: "Azure App Service Bicep documentation 2025"
```

**Method 2: Built-in CLI Help**
```bash
# Azure
az <service> <resource-type> create --help

# AWS
aws <service> create-<resource> help

# Terraform
terraform-docs show <resource-type>
```

**Method 3: Ask User for Documentation**

If automatic fetch fails, ask user ONE question:

**"I need documentation for <resource-type>. Can you provide:"**
- Official documentation URL, OR
- Paste relevant documentation section, OR
- Skip (proceed with best practices)

---

### PHASE 5: BUILD COMPREHENSIVE INFRASTRUCTURE PLAN

Create `/specs/DEVOPS_<change>.md`:

```markdown
# DevOps Change - <Change Name>

**Ticket**: <ticket-id>
**Change Type**: <Greenfield/Brownfield/Mixed>
**Complexity**: Complex (full planning required)
**Created**: <date>

---

## 1. OBJECTIVE

**Primary Goal**: <What this infrastructure change achieves>
**Business Context**: <Why this change matters>

**Success Criteria**:
- [ ] <Measurable outcome 1>
- [ ] <Measurable outcome 2>
- [ ] <Measurable outcome 3>

---

## 2. SCOPE

### In Scope
- <What IS included in this change>

### Out of Scope
- <What IS NOT included>

---

## 3. ENVIRONMENTS

| Environment | Subscription/Account | Resource Group/VPC | Region | Deployment Order |
|-------------|---------------------|-------------------|--------|------------------|
| Development | <sub-id> | <rg-name> | <region> | 1st |
| Staging | <sub-id> | <rg-name> | <region> | 2nd |
| Production | <sub-id> | <rg-name> | <region> | 3rd (after validation) |

---

## 4. RESOURCE INVENTORY

### Existing Resources (Brownfield Context)

Reference: `/specs/00_BROWNFIELD_CONTEXT.md`

| Resource | Current State | Planned Change | Breaking? | Mitigation |
|----------|--------------|----------------|-----------|------------|
| <name> | <current> | <new> | Yes/No | <strategy> |

### New Resources (Greenfield)

| Resource Type | Name | Purpose | Dependencies | Risk Level |
|--------------|------|---------|--------------|------------|
| <type> | <name> | <purpose> | <deps> | High/Med/Low |

---

## 5. BREAKING CHANGE ANALYSIS

### Summary
- **Breaking changes identified**: <N>
- **Backwards compatibility approach**: <Yes/No - rationale>
- **Migration strategy**: <How breaking changes handled>

### Detailed Analysis
<Include breaking change analysis from Phase 3>

---

## 6. INFRASTRUCTURE AS CODE PLAN

### IaC Tool Selection
**Chosen Tool**: <Terraform/Bicep/CloudFormation/Pulumi/Ansible>
**Version**: <tool version>
**Existing Templates**: <Yes/No - if yes, list paths>
**Approach**: <Extend existing/Create new/Hybrid>
**Production Reference**: <resource name(s) used as reference, or "None - using best practices">

### Repository Structure
```
<repository-name>/
├── infrastructure/
│   ├── modules/              # Reusable modules
│   ├── environments/
│   │   ├── dev/
│   │   ├── staging/
│   │   └── prod/
│   └── README.md
├── scripts/
│   ├── validate.sh
│   ├── deploy.sh
│   └── rollback.sh
└── docs/
```

### Implementation Steps

**Step 1: Validation (read-only)**
```bash
# Plan command (no changes made)
<IaC plan command>
```

**Expected Output**: <What the plan should show>

**Validation Checklist**:
- [ ] No unexpected resource deletions
- [ ] No forced resource replacements (unless planned)
- [ ] All required parameters present
- [ ] Tags/labels applied correctly

**Step 2: Apply Changes**
```bash
# Apply command
<IaC apply command>
```

**Step 3: Post-Deployment Verification**
```bash
# Resource existence check
<verification commands>

# Health check
<health check commands>

# Connectivity test
<connectivity test commands>
```

---

## 7. ROLLBACK STRATEGY

### Automatic Rollback Triggers
- [ ] Deployment fails with error
- [ ] Critical health check fails
- [ ] Dependent services report failures
- [ ] Monitoring alerts fire

### Manual Rollback Procedure

**Option 1: IaC Rollback** (preferred)
```bash
# Revert to previous state
git checkout <previous-commit>
<IaC apply command>
```

**Option 2: Point-in-Time Restore** (for data services)
```bash
# Restore to previous snapshot
<restore command>
```

---

## 8. ACCEPTANCE CRITERIA AND VERIFICATION

### Acceptance Criteria

**Functional**:
- [ ] <Resource exists and is accessible>
- [ ] <Configuration matches specification>
- [ ] <Integration with existing systems works>

**Non-Functional**:
- [ ] <Performance requirements met>
- [ ] <Security controls implemented>
- [ ] <Cost within budget>

**Backwards Compatibility** (if applicable):
- [ ] <Existing functionality still works>
- [ ] <No breaking changes to dependent systems>

### Verification Commands

```bash
# Verify resource created
<verification command>

# Verify configuration
<config check command>

# Verify health
<health check command>
```

---

## 9. COST ANALYSIS

| Resource | Tier/Size | Monthly Cost (Est.) | Annual Cost (Est.) |
|----------|-----------|---------------------|-------------------|
| <resource> | <tier> | $X | $Y |
| **TOTAL** | | **$X** | **$Y** |

---

## 10. MONITORING AND ALERTING

**Metrics to Monitor**:
- [ ] Resource health (up/down status)
- [ ] Performance (CPU, memory, latency)
- [ ] Errors (error rate, failed requests)
- [ ] Cost (spending rate)

**Alerting Thresholds**:
- Critical: <threshold>
- Warning: <threshold>

---

## 11. NETWORK INFRASTRUCTURE (if applicable)

### VPN Gateway Configuration

**VPN Type**: <Site-to-Site / Point-to-Site / Both / None>

**Site-to-Site VPN** (for B2B customer connectivity):
- **VPN Gateway SKU**: <VpnGw1/VpnGw2/VpnGw3/VpnGw4/VpnGw5>
- **Throughput**: <650 Mbps / 1 Gbps / 1.25 Gbps / 5 Gbps / 10 Gbps>
- **Customer VPN Device**: <make/model>
- **Customer Public IP**: <IP address>
- **Customer Network CIDR**: <CIDR ranges>
- **Shared Key Management**: <how PSK is stored securely>
- **IPsec Parameters**:
  - IKE version: <IKEv1 / IKEv2>
  - Encryption: <AES256 / AES128>
  - Integrity: <SHA256 / SHA1>
  - DH Group: <2 / 14 / 24>
- **BGP Configuration**: <Yes/No - if yes, ASN numbers>
- **Routing**: <Static / BGP dynamic>

**Point-to-Site VPN** (for customer users):
- **Client Address Pool**: <CIDR range, e.g., 172.16.0.0/24>
- **Authentication Method**: <Certificate / Azure AD / RADIUS>
- **VPN Protocols**: <OpenVPN / SSTP / IKEv2>
- **Max Concurrent Connections**: <based on SKU>

### Edge Device / Firewall / NVA Configuration

**Device Type**: <Azure Firewall / Palo Alto / Fortinet / Check Point / SD-WAN / None>
- **Placement**: <DMZ subnet / Hub VNet / Spoke VNet>
- **VM Size** (if NVA): <Standard_D4s_v3 / Standard_D8s_v3 / Standard_D16s_v3>
- **High Availability**: <Yes/No - Active-Passive / Active-Active>
- **Interfaces**:
  - External/WAN: <public IP>
  - Internal/LAN: <private IP, subnet>
  - DMZ: <private IP, subnet> (if applicable)

**Routing Through Edge Device**:
- [ ] User-Defined Routes (UDR) configured to force traffic through NVA
- [ ] IP Forwarding enabled on NIC
- [ ] BGP peering with VPN Gateway (if applicable)

**Firewall Rules** (high-level):
- Allow: <traffic patterns allowed>
- Deny: <traffic patterns denied>
- Inspection: <Layer 7 application filtering, IDS/IPS, threat intelligence>

### B2B Customer Connectivity

**Connection Method**: <API Gateway / Site-to-Site VPN / ExpressRoute / Private Link / None>

**DMZ Configuration**:
- **DMZ Subnet**: <subnet name, CIDR>
- **Customer-Facing Services**: <API Gateway, Web Apps, Load Balancer>
- **Security**:
  - [ ] NSG blocking all except required ports (443, etc.)
  - [ ] No direct outbound to internet (force through firewall)
  - [ ] WAF enabled with OWASP top 10 protection
  - [ ] DDoS Protection Standard enabled
  - [ ] Rate limiting per customer tenant

**IP Overlap Handling**:
- [ ] Customer IP ranges documented: <CIDRs>
- [ ] NAT configured at boundary: <customer IP → translated IP>
- [ ] No overlap issues

**Customer Traffic Flow**:
```
[Customer Network - UNTRUSTED]
  ↓
[VPN Gateway / API Gateway]
  ↓
[DMZ Subnet - Customer-Facing Services]
  ↓
[Firewall/NVA - Layer 7 Inspection]
  ↓
[Internal Network - TRUSTED]
```

### Network Security

**Network Security Groups / Security Groups**:
- DMZ Subnet NSG: <name>
  - Inbound: Allow 443 from Internet/CustomerVPN, Deny all else
  - Outbound: Allow to Firewall only, Deny all else
- Internal Subnet NSG: <name>
  - Inbound: Allow from Firewall only, Deny from DMZ
  - Outbound: Allow to required services

**Private Endpoints**:
- [ ] Azure SQL Database: <endpoint name>
- [ ] Storage Account: <endpoint name>
- [ ] Key Vault: <endpoint name>
- [ ] Other PaaS services: <list>

**Web Application Firewall (WAF)**:
- [ ] WAF Policy: <policy name>
- [ ] OWASP top 10 protection enabled
- [ ] Custom rules: <list>
- [ ] Bot protection: <Yes/No>

---

## 12. SECURITY AND COMPLIANCE

**Encryption**:
- [ ] Data at rest: <method>
- [ ] Data in transit: <TLS 1.2+ for all B2B connections>

**Network Security**:
- [ ] Network isolation: <private endpoints, DMZ>
- [ ] Firewall rules: <allowed ranges>
- [ ] Zero Trust principles applied

**Access Control**:
- [ ] RBAC/IAM roles: <who has access>
- [ ] Managed identities: <service identities>
- [ ] Customer access: <federated identity, SAML/OIDC>

**B2B Security Requirements**:
- [ ] Customer traffic treated as UNTRUSTED
- [ ] DMZ isolation enforced
- [ ] Firewall inspection for all customer traffic
- [ ] No direct customer access to internal resources
- [ ] Network flow logs enabled for audit

---

## 13. STRUCTURED LOGGING & OBSERVABILITY

**CRITICAL**: All infrastructure must implement comprehensive logging and observability from day one.

### Logging Requirements

**Structured Logging Format**:
- [ ] JSON-formatted logs (machine-parseable)
- [ ] Standard fields: timestamp, level, source, correlation_id, user_id, trace_id
- [ ] Consistent log levels: DEBUG, INFO, WARN, ERROR, FATAL
- [ ] No PII/secrets in logs (sanitize sensitive data)

**Log Aggregation**:
- **Azure**: Azure Monitor / Log Analytics Workspace
- **AWS**: CloudWatch Logs
- **GCP**: Cloud Logging
- [ ] All services send logs to central aggregation
- [ ] Retention policy: <30/60/90 days for hot, archive for compliance>

**Application Logging**:
- [ ] Request/response logging with correlation IDs
- [ ] Error logging with stack traces
- [ ] Performance metrics (request duration, DB query time)
- [ ] Business event logging (user actions, transactions)
- [ ] Dependency call logging (external APIs, databases)

**Infrastructure Logging**:
- [ ] VPN connection logs (Site-to-Site, Point-to-Site)
- [ ] Firewall/NVA traffic logs
- [ ] Network flow logs (NSG Flow Logs, VPC Flow Logs)
- [ ] Load balancer access logs
- [ ] WAF logs (blocked requests, threats detected)
- [ ] DNS query logs
- [ ] Database audit logs

**B2B/Customer-Specific Logging**:
- [ ] Per-customer request tracking (customer_id in all logs)
- [ ] API Gateway request/response logs per customer
- [ ] VPN tunnel activity per customer
- [ ] Customer traffic volume metrics
- [ ] Failed authentication attempts

### Metrics & Monitoring

**Infrastructure Metrics** (REQUIRED):
- [ ] CPU, Memory, Disk, Network I/O
- [ ] Request rate, error rate, latency (RED metrics)
- [ ] Saturation metrics (queue depth, connection pool utilization)
- [ ] VPN tunnel bandwidth utilization
- [ ] Firewall throughput and connection count
- [ ] Database connection pool usage
- [ ] Storage capacity and IOPS

**Application Metrics** (REQUIRED):
- [ ] Request duration (p50, p95, p99)
- [ ] Error rates by endpoint
- [ ] Dependency call success/failure rates
- [ ] Business metrics (orders/min, signups/hour)
- [ ] Cache hit/miss ratio
- [ ] Background job queue depth

**Availability Metrics**:
- [ ] Uptime percentage (per service)
- [ ] Health check success rate
- [ ] VPN tunnel uptime
- [ ] Customer-facing endpoint availability

### Distributed Tracing

**REQUIRED for microservices/distributed systems**:
- [ ] OpenTelemetry or similar standard
- [ ] Trace propagation across all services
- [ ] Correlation IDs in all logs and requests
- [ ] Trace all customer requests end-to-end

**Tracing Backend**:
- Azure: Application Insights
- AWS: X-Ray
- GCP: Cloud Trace
- Third-party: Datadog, New Relic, Honeycomb, Jaeger

### Alerting

**Critical Alerts** (REQUIRED):
- [ ] Service down/unhealthy (5xx errors > threshold)
- [ ] VPN tunnel down
- [ ] Firewall/Edge device unreachable
- [ ] Database connection failures
- [ ] Disk space > 80%
- [ ] Memory usage > 85%
- [ ] Certificate expiration (< 30 days)
- [ ] B2B customer connectivity failure

**Warning Alerts**:
- [ ] High latency (p95 > threshold)
- [ ] Error rate elevated (> 1%)
- [ ] High CPU/memory (> 70%)
- [ ] Unusual traffic patterns
- [ ] Failed login attempts spike

**Alert Routing**:
- Critical: PagerDuty/OpsGenie + Email + SMS
- Warning: Email + Slack/Teams
- Info: Slack/Teams only

### Dashboards

**Required Dashboards**:
- [ ] **Infrastructure Overview**: CPU, Memory, Network, Disk across all resources
- [ ] **Application Health**: Request rate, error rate, latency (RED)
- [ ] **Network Connectivity**: VPN status, firewall throughput, B2B customer connections
- [ ] **Customer Experience**: Per-customer latency, error rates, request volume
- [ ] **Security**: WAF blocks, failed auth, suspicious traffic patterns
- [ ] **Cost**: Spending by service, trending

**Dashboard Tools**:
- Azure: Azure Monitor Workbooks, Grafana
- AWS: CloudWatch Dashboards, Grafana
- GCP: Cloud Monitoring Dashboards, Grafana
- Third-party: Datadog, New Relic, Grafana Cloud

### Log Queries & Analysis

**Essential Log Queries to Pre-Configure**:
- [ ] Error rate by service/endpoint (last 1h, 24h, 7d)
- [ ] Slowest requests (p99 latency)
- [ ] Failed authentication attempts by IP/user
- [ ] VPN connection failures by customer
- [ ] Firewall blocked traffic (top sources/destinations)
- [ ] Customer API usage by customer_id
- [ ] Database slow queries
- [ ] Dependency failures (external API errors)

### Observability Checklist

**For EVERY new service/component**:
- [ ] Structured logging implemented (JSON format)
- [ ] Health check endpoint created (/health, /ready)
- [ ] Metrics endpoint exposed (Prometheus format or native)
- [ ] Distributed tracing instrumented
- [ ] Logs forwarded to central aggregation
- [ ] Dashboards created for key metrics
- [ ] Alerts configured for critical failures
- [ ] Runbooks linked to alerts

**For B2B/Customer-Facing Services**:
- [ ] Per-customer metrics tracked (customer_id tag)
- [ ] Customer SLA metrics measured (uptime, latency)
- [ ] Customer traffic anomaly detection
- [ ] Customer-specific alert thresholds

### Compliance & Audit Logging

**REQUIRED for compliance (HIPAA, SOC2, PCI-DSS)**:
- [ ] All access to sensitive data logged (who, what, when)
- [ ] All configuration changes logged (audit trail)
- [ ] All authentication/authorization events logged
- [ ] Logs immutable (write-once, tamper-proof)
- [ ] Log retention meets compliance requirements
- [ ] Regular log review/analysis for anomalies

**Audit Log Fields**:
- timestamp, user_id, action, resource, ip_address, user_agent, result (success/failure), reason (if failure)

---

## 14. DOCUMENTATION

**CRITICAL**: Comprehensive documentation must be created once development and testing are complete. Documentation ensures maintainability, enables knowledge transfer, and supports operational excellence.

### Engineering Documentation

**Architecture Documentation** (REQUIRED):
- [ ] **Architecture Diagram**: Visual representation of all components and their relationships
  - Include: Infrastructure resources, network topology, data flow, security boundaries
  - Tool: draw.io, Lucidchart, PlantUML, or C4 model diagrams
  - Store: `/docs/architecture/`
- [ ] **Infrastructure as Code Documentation**: Explanation of IaC structure
  - Module/template purpose and usage
  - Input parameters and their meaning
  - Output values and how to use them
  - Dependencies between modules
  - Store: README.md in each IaC directory
- [ ] **Network Architecture Documentation** (if applicable):
  - VNet/VPC topology and addressing
  - VPN configuration (Site-to-Site, Point-to-Site)
  - Edge device configuration (firewalls, NVAs)
  - B2B customer connectivity patterns
  - Traffic flow diagrams
  - Store: `/docs/network/`
- [ ] **Security Architecture Documentation**:
  - Authentication and authorization patterns
  - Network security (NSGs, firewalls, WAF)
  - Encryption (at-rest, in-transit)
  - Secret management approach
  - Compliance mappings (HIPAA, SOC2, PCI-DSS)
  - Store: `/docs/security/`
- [ ] **Data Architecture Documentation**:
  - Database schema and relationships
  - Data retention policies
  - Backup and recovery procedures
  - Data migration procedures
  - Store: `/docs/data/`

**API Documentation** (if applicable):
- [ ] **API Reference**: Complete API endpoint documentation
  - OpenAPI/Swagger specification
  - Endpoint descriptions, parameters, responses
  - Authentication requirements
  - Rate limiting policies
  - Example requests and responses
  - Store: `/docs/api/` or use tools like Swagger UI, Postman
- [ ] **Integration Guides**: How to integrate with the API
  - Authentication flow
  - Common use cases with code examples
  - SDKs/client libraries (if available)
  - Store: `/docs/integration/`

**Code Documentation**:
- [ ] **Inline Code Comments**: Explain WHY, not WHAT
  - Document complex business logic
  - Explain non-obvious decisions
  - Reference tickets/issues for context
- [ ] **README.md per Repository**:
  - Project overview and purpose
  - Prerequisites and dependencies
  - Local development setup
  - Build and test instructions
  - Deployment procedures
  - Contributing guidelines
  - License information

**Change Log** (REQUIRED):
- [ ] **CHANGELOG.md**: Maintain version history
  - Format: Keep a Changelog standard
  - Include: Added, Changed, Deprecated, Removed, Fixed, Security
  - Version numbers with dates
  - Link to tickets/issues
  - Store: Root of repository

### Operational Documentation

**Runbooks** (REQUIRED):
- [ ] **Deployment Runbook**: Step-by-step deployment procedures
  - Pre-deployment checklist
  - Deployment commands with explanations
  - Rollback procedures
  - Post-deployment validation steps
  - Expected duration for each phase
  - Store: `/docs/runbooks/deployment.md`
- [ ] **Incident Response Runbook**: How to respond to common incidents
  - Service down/degraded
  - High error rates
  - Database connection failures
  - VPN tunnel failures (if applicable)
  - Certificate expiration
  - Each runbook includes: Symptoms, Investigation steps, Resolution steps, Escalation path
  - Store: `/docs/runbooks/incidents/`
- [ ] **Troubleshooting Guide**: Common issues and solutions
  - Connectivity issues
  - Performance degradation
  - Configuration errors
  - Authentication failures
  - Store: `/docs/troubleshooting/`
- [ ] **Disaster Recovery Runbook**: Recovery procedures
  - RTO (Recovery Time Objective) and RPO (Recovery Point Objective)
  - Backup restoration procedures
  - Failover procedures (if multi-region)
  - Communication plan during disaster
  - Store: `/docs/runbooks/disaster-recovery.md`

**Operations Manual** (REQUIRED):
- [ ] **Daily Operations**: Routine operational tasks
  - Health check procedures
  - Log review procedures
  - Backup verification
  - Certificate rotation schedule
  - Monitoring dashboard reviews
  - Store: `/docs/operations/daily-tasks.md`
- [ ] **Maintenance Procedures**: Planned maintenance tasks
  - Database maintenance (reindexing, vacuuming)
  - Certificate renewal
  - Security patching schedule
  - Dependency updates
  - Infrastructure capacity reviews
  - Store: `/docs/operations/maintenance.md`
- [ ] **On-Call Guide**: Information for on-call engineers
  - Alert descriptions and severity levels
  - First response procedures
  - Escalation contacts
  - Access to systems (credentials, VPN)
  - Common queries and dashboards
  - Store: `/docs/operations/on-call-guide.md`

**Monitoring & Alerting Documentation**:
- [ ] **Dashboard Guide**: Explanation of all dashboards
  - What each dashboard shows
  - How to interpret metrics
  - Normal vs. abnormal patterns
  - Links to dashboards
  - Store: `/docs/monitoring/dashboards.md`
- [ ] **Alert Reference**: Documentation for all alerts
  - Alert name and description
  - Severity level (Critical, Warning, Info)
  - Trigger conditions
  - Impact if not resolved
  - Investigation steps
  - Resolution steps
  - Store: `/docs/monitoring/alerts.md`
- [ ] **Log Query Library**: Pre-built log queries
  - Common troubleshooting queries
  - Performance analysis queries
  - Security audit queries
  - Customer-specific queries
  - Store: `/docs/monitoring/log-queries.md`

**Configuration Management Documentation**:
- [ ] **Configuration Reference**: All configuration parameters
  - Environment variables and their purpose
  - Configuration file locations
  - Default values and recommended values
  - Configuration change procedures
  - Store: `/docs/configuration/`
- [ ] **Secret Management Guide**: How secrets are managed
  - Where secrets are stored (Key Vault, Secrets Manager, etc.)
  - How to rotate secrets
  - Access control for secrets
  - Emergency secret recovery procedures
  - Store: `/docs/security/secret-management.md`

### User Documentation

**End-User Documentation** (if applicable):
- [ ] **User Guide**: How to use the system
  - Getting started guide
  - Feature explanations with screenshots
  - Common workflows
  - FAQ
  - Store: `/docs/user-guide/` or external documentation site
- [ ] **Administrator Guide**: For system administrators
  - User management
  - Permission management
  - Configuration options
  - Reporting features
  - Store: `/docs/admin-guide/`

**Customer/Partner Documentation** (for B2B):
- [ ] **Integration Guide**: How customers integrate
  - Authentication setup
  - API usage examples
  - Webhook configuration
  - Rate limits and quotas
  - Store: `/docs/customer/integration-guide.md`
- [ ] **VPN Setup Guide** (if B2B VPN):
  - Customer VPN device configuration templates
  - IPsec parameters
  - Troubleshooting VPN connectivity
  - Support contact information
  - Store: `/docs/customer/vpn-setup-guide.md`
- [ ] **SLA Documentation**: Service level agreements
  - Uptime commitments
  - Support response times
  - Maintenance windows
  - Escalation procedures
  - Store: `/docs/customer/sla.md`

### Documentation Standards

**Documentation Quality Requirements**:
- [ ] All documentation written in Markdown (`.md`)
- [ ] Clear headings and table of contents
- [ ] Code examples are tested and working
- [ ] Diagrams are up-to-date and version-controlled
- [ ] Links are valid (no broken links)
- [ ] Consistent formatting and style
- [ ] Technical accuracy verified
- [ ] No hardcoded secrets or credentials
- [ ] Dates and versions clearly indicated

**Documentation Review Process**:
- [ ] Peer review before merging
- [ ] Technical accuracy verification
- [ ] Clarity and completeness check
- [ ] User testing (for user-facing docs)
- [ ] Regular updates (at least quarterly review)

**Documentation Versioning**:
- [ ] Documentation versioned alongside code
- [ ] Version number in documentation header
- [ ] Change history tracked in git
- [ ] Deprecated documentation clearly marked
- [ ] Archive old versions (don't delete)

### Documentation Checklist

**Before Production Deployment** (MANDATORY):
- [ ] Architecture diagram created and reviewed
- [ ] IaC documentation complete
- [ ] API documentation published (if applicable)
- [ ] Deployment runbook tested
- [ ] Incident response runbooks created
- [ ] Disaster recovery runbook tested
- [ ] On-call guide provided to on-call team
- [ ] Monitoring dashboards documented
- [ ] Alert documentation complete with runbook links
- [ ] Configuration reference created
- [ ] User documentation published (if applicable)
- [ ] Customer integration guide published (if B2B)
- [ ] All documentation reviewed and approved
- [ ] Documentation links shared with team
- [ ] Knowledge transfer session conducted (if applicable)

**Documentation Storage Locations**:
- **Engineering Docs**: `/docs/` in repository
- **Runbooks**: `/docs/runbooks/` in repository
- **API Docs**: `/docs/api/` or hosted API documentation site
- **User Docs**: `/docs/user-guide/` or external documentation portal
- **Wiki**: Team wiki (Confluence, Notion, GitHub Wiki) for operational knowledge

**Documentation Tools**:
- Markdown editors: VS Code, Typora, Obsidian
- Diagram tools: draw.io, Lucidchart, PlantUML, Mermaid
- API docs: Swagger/OpenAPI, Postman, Redoc
- Static site generators: MkDocs, Docusaurus, GitBook
- Wiki platforms: Confluence, Notion, GitHub Wiki

---

## 15. DEPLOYMENT TIMELINE

### Phased Rollout Plan

**Phase 1: Development Environment**
- **Duration**: <X hours>
- **Activities**: Deploy, validate, test
- **Gate**: <Criteria to proceed>

**Phase 2: Staging Environment**
- **Duration**: <X hours>
- **Activities**: Deploy, integration testing
- **Gate**: <Criteria to proceed>

**Phase 3: Production Environment**
- **Duration**: <X hours>
- **Activities**: Deploy, smoke test, monitor
- **Gate**: <Criteria for completion>

---

## 13. COMMUNICATION PLAN

**Before Deployment**:
- [ ] Notify: <stakeholders>
- [ ] Message: <deployment window, expected impact>

**After Deployment**:
- [ ] Completion notification
- [ ] Success metrics

---

Generated using devops-change-builder.md (complex workflow)
Last updated: <date>
```

---

### PHASE 6: GENERATE BUILD MAP

Create `/specs/BUILD_MAP.md`:

```markdown
# Build Map - Infrastructure Change

**Ticket**: <ticket-id> - <short-name>
**Type**: Infrastructure as Code
**Complexity**: Complex
**Created**: <date>

---

## MILESTONES

### Milestone 0: Context and Planning
**Duration**: 30-60 minutes
**Status**: Pending

**Tasks**:
- [ ] Brownfield context discovery (if applicable)
- [ ] Breaking change analysis
- [ ] Documentation review
- [ ] IaC specification created
- [ ] User approval of plan

**Quality Gate**:
- [ ] User reviewed and approved plan
- [ ] Breaking changes identified and mitigation planned

---

### Milestone 1: Development Environment Deployment
**Duration**: <X hours>
**Status**: Pending

**Tasks**:
- [ ] Write IaC code
- [ ] Run validation (plan)
- [ ] Deploy to dev
- [ ] Run verification
- [ ] Validate acceptance criteria

**Quality Gate**:
- [ ] Plan shows no unexpected changes
- [ ] Deployment successful
- [ ] Verification commands pass
- [ ] Acceptance criteria validated

---

### Milestone 2: Staging Environment Deployment
**Duration**: <X hours>
**Status**: Pending

**Tasks**:
- [ ] Review dev learnings
- [ ] Deploy to staging
- [ ] Run integration tests
- [ ] Validate acceptance criteria

**Quality Gate**:
- [ ] Deployment successful
- [ ] Integration tests pass
- [ ] No issues found

---

### Milestone 3: Production Environment Deployment
**Duration**: <X hours>
**Status**: Pending

**Tasks**:
- [ ] Get production approval
- [ ] Notify stakeholders
- [ ] Create backup (brownfield)
- [ ] Deploy to production
- [ ] Run smoke tests
- [ ] Monitor for alerts
- [ ] Notify completion

**Quality Gate**:
- [ ] Deployment successful
- [ ] Smoke tests pass
- [ ] No alerts firing
- [ ] Monitoring healthy

---

### Milestone 4: Documentation and Handoff
**Duration**: 1-2 hours
**Status**: Pending

**Tasks**:
- [ ] Update infrastructure documentation
- [ ] Create runbook
- [ ] Capture learnings in LEARNINGS.md
- [ ] Update architecture diagrams

**Quality Gate**:
- [ ] Documentation complete
- [ ] Runbook tested
- [ ] Learnings captured

---

Generated using devops-change-builder.md
```

---

### PHASE 7: IMPLEMENTATION

Follow standard implementation workflow:

1. **Use `/full-app-11-implement-milestone-n.md` for each milestone**
2. **Quality Gates at Each Milestone**:
   - Plan validation (no unexpected changes)
   - Deployment success (no errors)
   - Verification commands pass
   - Acceptance criteria validated
3. **Pause for Validation**:
   - Use `/full-app-11a-milestone-demo.md` after each environment
   - User approves proceeding to next environment

---

### PHASE 8: COMPLETION AND LEARNINGS

Use `/full-app-completion-summary.md` to create comprehensive handoff.

---

## WORKFLOW DECISION TREE

```
START
  ↓
Is this SIMPLE or COMPLEX?
  ↓
SIMPLE → Quick Workflow (15-30 min)
  ├─ Minimal context (ticket, env, tool)
  ├─ Lightweight plan (commands only)
  ├─ Execute (validate → apply → verify)
  └─ Commit and done

COMPLEX → In-Depth Workflow (1-3 hours)
  ├─ Full ticket definition
  ├─ Brownfield context discovery (if applicable)
  ├─ Breaking change analysis
  ├─ Documentation discovery
  ├─ Comprehensive planning
  ├─ Build map with milestones
  ├─ Phased implementation
  └─ Full documentation and learnings
```

---

## KEY PRINCIPLES

**Speed vs Depth**:
- Simple changes: Optimize for speed (15-30 min)
- Complex changes: Optimize for safety (1-3 hours planning)
- User chooses based on risk tolerance

**Infrastructure as Code Best Practices**:
1. **Discover before creating**: Always search for existing IaC files first (BICEP, Terraform, etc.)
2. **Learn from production**: Reference similar production resources for patterns (naming, sizing, tags, config)
3. **Consistency over novelty**: Extend existing templates rather than creating new ones
4. **Always use IaC**: Never manual portal changes (unless emergency)
5. **Plan before apply**: Always review plan, never blind apply
6. **Idempotent**: Running IaC multiple times should be safe
7. **Version control**: All IaC code in git

**Backwards Compatibility First**:
- Prefer additive changes over destructive
- Use blue-green deployments for risky changes
- Always have rollback plan tested

**Brownfield Awareness**:
- Understand current state before changing (complex workflow only)
- Follow existing patterns and conventions
- Validate dependent systems not broken

**One Question at a Time**:
- Never overwhelm user with multiple questions
- Ask most blocking question first
- Build context progressively

---

## IAC FILE DISCOVERY COMMANDS

**Quick discovery of existing template files** (run these first in any workflow):

```bash
# BICEP files
find . -name "*.bicep" -o -name "*.bicepparam" 2>/dev/null

# Terraform files
find . -name "*.tf" -o -name "*.tfvars" -o -name "terraform.tfstate" 2>/dev/null

# CloudFormation templates
find . \( -name "*.yaml" -o -name "*.yml" -o -name "*.json" \) -exec grep -l "AWSTemplateFormatVersion\|Resources:" {} \; 2>/dev/null

# ARM templates
find . -name "*.json" -exec grep -l "resources\|Microsoft\." {} \; 2>/dev/null

# Pulumi files
find . -name "Pulumi.yaml" -o -name "Pulumi.*.yaml" -o -name "index.ts" -o -name "__main__.py" 2>/dev/null

# Ansible playbooks
find . -name "*.yml" -o -name "*.yaml" -exec grep -l "hosts:\|tasks:" {} \; 2>/dev/null

# All at once (comprehensive search)
echo "=== BICEP ===" && find . -name "*.bicep" 2>/dev/null && \
echo "=== TERRAFORM ===" && find . -name "*.tf" 2>/dev/null && \
echo "=== CLOUDFORMATION ===" && find . -name "*.yaml" -o -name "*.yml" | xargs grep -l "AWSTemplateFormatVersion" 2>/dev/null && \
echo "=== PULUMI ===" && find . -name "Pulumi.yaml" 2>/dev/null
```

**What to do with discovered files**:
1. Read the files to understand what resources are already defined
2. Identify reusable modules or patterns
3. Check for parameter/variable files
4. Determine if you should extend existing templates or create new ones
5. Match naming conventions and structure from existing files

---

## CLOUD-SPECIFIC QUICK REFERENCE

### Azure
**Production Reference Commands**:
```bash
# Get details of similar production resource
az resource show --ids <resource-id> --output json

# Get specific resource type (e.g., App Service)
az webapp show --name <app-name> --resource-group <rg> --output json

# Get resource tags (for naming/tagging patterns)
az resource show --ids <resource-id> --query tags

# Get resource configuration
az webapp config show --name <app-name> --resource-group <rg>

# List all resources of a type to find similar ones
az resource list --resource-type "Microsoft.Web/sites" --output table
```

**Network Infrastructure Commands**:
```bash
# VPN Gateway
az network vnet-gateway list --resource-group <rg> --output table
az network vnet-gateway show --name <vpn-gw> --resource-group <rg>
az network vpn-connection list --resource-group <rg> --output table
az network vpn-connection show --name <connection> --resource-group <rg>

# Edge Devices / NVA
az vm list --resource-group <rg> --query "[?contains(name, 'firewall') || contains(name, 'nva')]" --output table

# Network Security Groups
az network nsg list --resource-group <rg> --output table
az network nsg rule list --nsg-name <nsg> --resource-group <rg> --output table

# Private Endpoints
az network private-endpoint list --resource-group <rg> --output table

# Load Balancers / Application Gateways
az network lb list --output table
az network application-gateway list --output table

# WAF Policies
az network application-gateway waf-policy list --output table
```

**Common Commands**:
```bash
# List resources
az resource list --resource-group <rg> --output table

# Plan (Bicep)
az deployment group what-if --resource-group <rg> --template-file main.bicep

# Apply (Bicep)
az deployment group create --resource-group <rg> --template-file main.bicep

# Verify
az resource show --ids <resource-id>
```

### AWS
**Production Reference Commands**:
```bash
# Get details of similar production resource (e.g., EC2)
aws ec2 describe-instances --instance-ids <instance-id>

# Get RDS database details
aws rds describe-db-instances --db-instance-identifier <db-name>

# Get resource tags (for naming/tagging patterns)
aws resourcegroupstaggingapi get-resources --resource-arn-list <arn>

# List resources of a type to find similar ones
aws ec2 describe-instances --filters "Name=tag:Environment,Values=production"

# Get CloudFormation stack that created a resource
aws cloudformation describe-stack-resources --physical-resource-id <resource-id>
```

**Network Infrastructure Commands**:
```bash
# VPN Gateway
aws ec2 describe-vpn-gateways
aws ec2 describe-vpn-connections
aws ec2 describe-customer-gateways

# VPC and Subnets
aws ec2 describe-vpcs
aws ec2 describe-subnets

# Security Groups
aws ec2 describe-security-groups
aws ec2 describe-security-group-rules --group-id <sg-id>

# Network ACLs
aws ec2 describe-network-acls

# NAT Gateways
aws ec2 describe-nat-gateways

# Load Balancers
aws elbv2 describe-load-balancers
aws elbv2 describe-target-groups

# WAF
aws wafv2 list-web-acls --scope REGIONAL
aws wafv2 get-web-acl --scope REGIONAL --id <web-acl-id> --name <name>

# VPC Endpoints (Private Link)
aws ec2 describe-vpc-endpoints
```

**Common Commands**:
```bash
# List resources
aws resourcegroupstaggingapi get-resources --region <region>

# Plan (CloudFormation)
aws cloudformation create-change-set --stack-name <name> --template-body file://template.yaml

# Apply (CloudFormation)
aws cloudformation execute-change-set --change-set-name <name>

# Verify
aws cloudformation describe-stacks --stack-name <name>
```

### Terraform (Multi-Cloud)
**Production Reference Commands**:
```bash
# Show current state of production resource
terraform show

# Get specific resource details from state
terraform state show <resource-type>.<resource-name>

# List all resources in state
terraform state list

# Pull remote state to inspect production
terraform state pull

# Show resource attributes to learn configuration patterns
terraform state show <resource-type>.<resource-name> | grep -A 5 "tags\|name\|sku"
```

**Common Commands**:
```bash
# Initialize
terraform init

# Plan
terraform plan -out=plan.tfplan

# Apply
terraform apply plan.tfplan

# Verify
terraform show
```

### Kubernetes
**Production Reference Commands**:
```bash
# Get similar production resource YAML
kubectl get <resource-type> <name> -n <namespace> -o yaml

# Get resource with labels to understand naming patterns
kubectl get <resource-type> --show-labels -n <namespace>

# Describe resource for full configuration
kubectl describe <resource-type> <name> -n <namespace>

# Find similar resources by label
kubectl get <resource-type> -l app=<app-name> --all-namespaces

# Export production resource as template
kubectl get <resource-type> <name> -n <namespace> -o yaml > reference.yaml
```

**Common Commands**:
```bash
# Plan (dry-run)
kubectl apply -f manifest.yaml --dry-run=server

# Apply
kubectl apply -f manifest.yaml

# Verify
kubectl get <resource-type> <name>
kubectl describe <resource-type> <name>
```

---

## NOTES

**This workflow supports**:
- **SIMPLE changes**: Quick execution (15-30 min) with minimal overhead
- **COMPLEX changes**: In-depth analysis (1-3 hours) with full safety net
- Greenfield infrastructure (new resources)
- Brownfield infrastructure (modifying existing)
- All major cloud providers (Azure, AWS, GCP)
- Container orchestration (Kubernetes)
- All major IaC tools (Terraform, Bicep, CloudFormation, Pulumi, Ansible)

**This workflow ensures**:
- Quick changes stay quick (no unnecessary overhead)
- Complex changes get proper depth (safety and planning)
- User chooses workflow based on risk/complexity
- Brownfield awareness when needed
- Breaking change protection when needed
- Quality gates appropriate to complexity
- Network infrastructure (VPN, edge devices, B2B) properly planned

---

## RELATED WORKFLOWS

**For detailed network infrastructure planning**:
- Use `/network-flow-builder.md` for:
  - VPN Gateway planning (Site-to-Site, Point-to-Site)
  - Edge device configuration (Firewalls, NVAs, SD-WAN)
  - B2B customer connectivity (DMZ, untrusted traffic handling)
  - Network architecture patterns (Hub-Spoke, Multi-Region, Customer Isolation)
  - Traffic flow documentation and analysis
  - VPN and edge device troubleshooting

**When to use network-flow-builder.md**:
- Infrastructure change involves networking components (Question 6 answered YES)
- Need to plan VPN connectivity to customers/partners
- Deploying edge devices or firewalls
- Setting up B2B connectivity with untrusted customer networks
- Documenting existing network flows
- Troubleshooting VPN or edge device issues

---

Generated using devops-change-builder.md
Last updated: <date>
