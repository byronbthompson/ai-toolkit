NETWORK FLOW BUILDER - ENTERPRISE DISTRIBUTED INFRASTRUCTURE & SIGNAL FLOW

Plan enterprise-scale distributed cloud infrastructure or document/analyze signal flow in existing network setups.

**CRITICAL**: This workflow adapts to the task type - discovery/documentation stays lightweight, new infrastructure gets full planning.

**ENTERPRISE FOCUS**:
- Multi-region, multi-cloud distributed architectures
- B2B customer connectivity to untrusted external infrastructure
- Diverse internal resource types (Web Apps, Container Apps, K8s clusters, VMs)
- Security boundaries for untrusted connections
- Scalability and high availability patterns

---

## QUICK START (Determine Task Type)

Ask user ONE question upfront to determine workflow type:

**"What networking task do you need help with?"**

**Option 1: Analyze/Document Existing Network** (Discovery Mode)
- Understand current network topology
- Document signal/traffic flow
- Troubleshoot connectivity issues
- Map data flows between services
- Security posture review

**Option 2: Plan New Network Infrastructure** (Planning Mode)
- Design new network architecture
- Add new subnets/VPCs/VNets
- Implement network security (NSGs, firewalls, ACLs)
- Set up connectivity (peering, VPN, ExpressRoute)
- Plan load balancing/ingress

**Based on answer, route to appropriate workflow**:

---

## DISCOVERY/DOCUMENTATION WORKFLOW (Analyze Existing)

**When to use**: Understanding current network setup, documenting flows, troubleshooting

### Step 1: Define Discovery Scope

Ask user ONE question at a time:

**Question 1**: "What aspect of the network do you want to analyze/document?"
- Full network topology (all resources and connections)
- Specific traffic flow (e.g., "How does traffic reach my web app?")
- Connectivity between specific services (e.g., "App to Database flow")
- Security boundaries (NSGs, firewalls, ACLs)
- Outbound/inbound internet connectivity
- Cross-region or cross-VNet/VPC connectivity

**Question 2**: "What cloud provider/environment?"
- Azure, AWS, GCP, On-premises, Multi-cloud, Kubernetes

**Question 3**: "Do you have access to query the network configuration?"
- If YES: Proceed to automated discovery
- If NO: Ask user to provide network diagrams, configuration exports, or specific command outputs

🛑 WAIT FOR ANSWER

**If YES to access**:

### DECISION REQUIRED: Discovery Depth

**Question 4**: "How comprehensive should the network discovery be?"

**Option A - Targeted Discovery** (RECOMMENDED for specific issues)
- **Why recommended**: Faster, focused on relevant components, less command output to parse
- **Best for**: Troubleshooting specific connectivity issue, documenting known flow
- **Scope**: Only query components related to specified aspect (from Question 1)
- **Time**: 5-15 minutes
- **Commands**: Minimal set (10-20 commands)

**Option B - Full Network Scan** (for complete documentation)
- **Best for**: Initial setup, comprehensive audit, security review
- **Scope**: Query all network components (VNets, subnets, NSGs, routes, peerings, etc.)
- **Time**: 15-45 minutes
- **Commands**: Complete set (50-100+ commands)
- **Output**: May be very large

**Option C - Custom Scope**
- Specify exactly what to discover: [user describes]

**Question**: "Which discovery depth? (A/B/C)"

🛑 WAIT FOR CONFIRMATION

---

### Step 2: Automated Network Discovery

**NOTE**: Commands below are for FULL discovery (Option B). If Option A (Targeted) chosen, only run subset relevant to Question 1 scope.

Based on cloud provider, run discovery commands:

#### Azure Discovery
```bash
# List all VNets
az network vnet list --output table

# Get specific VNet details with subnets
az network vnet show --name <vnet-name> --resource-group <rg> --output json

# List all subnets in a VNet
az network vnet subnet list --vnet-name <vnet-name> --resource-group <rg> --output table

# Network Security Groups
az network nsg list --output table
az network nsg show --name <nsg-name> --resource-group <rg> --output json

# NSG rules
az network nsg rule list --nsg-name <nsg-name> --resource-group <rg> --output table

# Route tables
az network route-table list --output table
az network route-table show --name <route-table> --resource-group <rg> --output json

# VNet peerings
az network vnet peering list --vnet-name <vnet-name> --resource-group <rg> --output table

# Private endpoints
az network private-endpoint list --output table

# Public IPs
az network public-ip list --output table

# Load balancers
az network lb list --output table

# Application gateways
az network application-gateway list --output table

# Network interfaces and their associations
az network nic list --output table
az network nic show --name <nic-name> --resource-group <rg> --output json

# Check effective routes for a NIC
az network nic show-effective-route-table --name <nic-name> --resource-group <rg> --output table

# Check effective NSG rules for a NIC
az network nic list-effective-nsg --name <nic-name> --resource-group <rg> --output table
```

#### AWS Discovery
```bash
# List all VPCs
aws ec2 describe-vpcs --output table

# Get specific VPC details
aws ec2 describe-vpcs --vpc-ids <vpc-id>

# List subnets
aws ec2 describe-subnets --filters "Name=vpc-id,Values=<vpc-id>" --output table

# Security Groups
aws ec2 describe-security-groups --output table
aws ec2 describe-security-groups --group-ids <sg-id>

# Network ACLs
aws ec2 describe-network-acls --output table

# Route tables
aws ec2 describe-route-tables --filters "Name=vpc-id,Values=<vpc-id>" --output table

# VPC peering connections
aws ec2 describe-vpc-peering-connections --output table

# NAT Gateways
aws ec2 describe-nat-gateways --output table

# Internet Gateways
aws ec2 describe-internet-gateways --output table

# VPN connections
aws ec2 describe-vpn-connections --output table

# Network interfaces
aws ec2 describe-network-interfaces --output table

# Elastic Load Balancers
aws elbv2 describe-load-balancers --output table
aws elbv2 describe-target-groups --output table

# VPC Flow Logs
aws ec2 describe-flow-logs --output table
```

#### GCP Discovery
```bash
# List VPC networks
gcloud compute networks list

# Get network details
gcloud compute networks describe <network-name>

# List subnets
gcloud compute networks subnets list --network=<network-name>

# Firewall rules
gcloud compute firewall-rules list

# Routes
gcloud compute routes list

# VPC peerings
gcloud compute networks peerings list --network=<network-name>

# Load balancers
gcloud compute forwarding-rules list
gcloud compute backend-services list

# Cloud NAT
gcloud compute routers nats list --router=<router-name>

# Network interfaces
gcloud compute instances list --format="table(name,networkInterfaces[].networkIP)"
```

#### Azure Web Apps / App Services Discovery
```bash
# List all Web Apps
az webapp list --output table

# Get Web App network details
az webapp show --name <app-name> --resource-group <rg> --query "{name:name, vnetConnection:virtualNetworkSubnetId, outboundIps:possibleOutboundIpAddresses}"

# Check VNet integration
az webapp vnet-integration list --name <app-name> --resource-group <rg>

# Check private endpoints
az network private-endpoint list --query "[?contains(privateLinkServiceConnections[0].privateLinkServiceId, 'Microsoft.Web')]" --output table

# Check IP restrictions (firewall rules)
az webapp config access-restriction show --name <app-name> --resource-group <rg>
```

#### Azure Container Apps Discovery
```bash
# List all Container Apps
az containerapp list --output table

# Get Container App network details
az containerapp show --name <app-name> --resource-group <rg> --query "{name:name, environmentId:managedEnvironmentId, ingressFqdn:configuration.ingress.fqdn}"

# Get Container App Environment (includes VNet integration)
az containerapp env show --name <env-name> --resource-group <rg> --query "{vnetId:vnetConfiguration.infrastructureSubnetId, internal:vnetConfiguration.internal}"

# List Container Apps in environment
az containerapp list --environment <env-name> --resource-group <rg> --output table
```

#### Kubernetes Discovery
```bash
# Services (expose endpoints)
kubectl get services --all-namespaces -o wide

# Ingress resources
kubectl get ingress --all-namespaces -o wide

# Network Policies
kubectl get networkpolicies --all-namespaces

# Pods with IPs and nodes
kubectl get pods --all-namespaces -o wide

# Service endpoints
kubectl get endpoints --all-namespaces

# ConfigMaps for network config
kubectl get configmaps --all-namespaces

# Describe service for detailed network info
kubectl describe service <service-name> -n <namespace>

# Check AKS network configuration
az aks show --name <cluster-name> --resource-group <rg> --query "{networkPlugin:networkProfile.networkPlugin, networkPolicy:networkProfile.networkPolicy, serviceCidr:networkProfile.serviceCidr, dnsServiceIP:networkProfile.dnsServiceIp, vnetSubnetId:agentPoolProfiles[0].vnetSubnetId}"

# Get load balancer services (customer entry points)
kubectl get svc --all-namespaces -o wide | grep LoadBalancer

# Service mesh (if installed)
# Istio
kubectl get virtualservices --all-namespaces
kubectl get destinationrules --all-namespaces
kubectl get gateways --all-namespaces

# Check egress gateway configuration
kubectl get pod -n istio-system -l istio=egressgateway
```

### Step 3: Traffic Flow Analysis

**For specific traffic flows**, trace the path:

**Question**: "What is the source and destination of the traffic flow?"
- Example: "External user → Load Balancer → Web App → Database"
- Example: "App in Subnet A → API in Subnet B"

**Trace the flow step-by-step**:

1. **Entry point**: Where does traffic enter? (Internet, VPN, peered network)
2. **Public IP/DNS**: Does it resolve to a public IP or load balancer?
3. **Load balancer/Gateway**: Is there an ALB, NLB, App Gateway, API Gateway?
4. **Network boundary crossing**: Does it cross VNet/VPC boundaries? (peering, transit gateway)
5. **Security checkpoints**: What NSGs, Security Groups, NACLs, firewalls apply?
6. **Routing**: What route tables direct the traffic?
7. **Target resource**: What subnet/NIC/pod receives the traffic?
8. **Response path**: Is the return path symmetric or asymmetric?

### Step 4: Generate Network Documentation

Create `/docs/network/NETWORK_FLOW_<name>.md`:

```markdown
# Network Flow Analysis - <Flow Name>

**Environment**: <Azure/AWS/GCP/K8s>
**Analyzed**: <date>
**Scope**: <what was analyzed>

---

## Network Topology Overview

### VPC/VNet Structure
- **Network Name**: <name>
- **Address Space**: <CIDR blocks>
- **Region(s)**: <regions>
- **Subnets**:

| Subnet Name | CIDR | Purpose | Resources |
|-------------|------|---------|-----------|
| <name> | <cidr> | <purpose> | <resource count/types> |

### Network Connectivity
- **Internet Gateway**: <Yes/No - details>
- **NAT Gateway**: <Yes/No - details>
- **VPN Gateway**: <Yes/No - details>
- **Peering Connections**: <list peerings>
- **Private Links/Endpoints**: <list>

---

## Traffic Flow: <Specific Flow Name>

### Flow Path Diagram

**Example 1: B2B Customer → Internal Service (Untrusted)**

```
[Customer Network: UNTRUSTED]
  ↓
[Internet / Customer VPN]
  ↓
[Azure Front Door / CloudFront - WAF Enabled]
  ↓ (DDoS Protection)
[Public IP / DNS: api.company.com]
  ↓
[API Management Gateway: <public-ip>]
  ↓ (Authentication, Rate Limiting, Throttling)
[DMZ Subnet: 10.1.0.0/24]
  ↓ (NSG: Allow 443 from Internet, Deny all outbound to internal)
[Azure Firewall: 10.1.255.4]
  ↓ (Layer 7 Inspection, Threat Intelligence)
[Internal Route: to App Tier]
  ↓ (NSG: Allow 443 from Firewall only)
[App Tier Subnet: 10.1.10.0/24]
  ↓
[Azure Container App: backend-api - 10.1.10.5]
  ↓ (Private Endpoint Connection)
[Data Tier Subnet: 10.1.20.0/24]
  ↓
[Azure SQL Database: <db-name> - Private Endpoint]
  ↓
[Response flows back through same path]
```

**Example 2: Internal Request Flow (Trusted) - Web App → K8s → Database**

```
[Azure Web App: frontend-app]
  ↓ (VNet Integration)
[Web Tier Subnet: 10.1.5.0/24]
  ↓ (NSG: Allow HTTPS to K8s subnet)
[Internal Load Balancer: k8s-ingress-lb]
  ↓
[K8s Ingress Controller: 10.1.11.10]
  ↓ (Service Mesh: Istio with mTLS)
[K8s Service: backend-api-svc]
  ↓ (Network Policy: Allow only from ingress)
[K8s Pod: backend-api - 10.1.100.45]
  ↓ (Managed Identity Auth)
[Private Endpoint: sql-private-endpoint]
  ↓
[Azure SQL Database: <db-name>]
```

**Example 3: Multi-Region Global Flow**

```
[Global User: Region-Agnostic]
  ↓
[Azure Front Door / AWS CloudFront with Geo-Routing]
  ↓
[Closest Region Entry Point: Region 1]
  ↓
[Regional WAF + Load Balancer]
  ↓
[Regional VNet: 10.1.0.0/16]
  ↓
[If Region 1 healthy]: Route to App Tier
[If Region 1 down]: Failover to Region 2 (10.2.0.0/16)
  ↓
[App Tier: Container Apps / K8s]
  ↓
[Global Database: Azure CosmosDB (Multi-Region)]
  OR
[Cross-Region DB Replication: Primary in Region 1, Read Replica in Region 2]
```

### Flow Details

**Step 1: Entry Point**
- **Source**: <external IP range / service name>
- **Protocol/Port**: <TCP/443, UDP/53, etc.>
- **Entry mechanism**: <Internet Gateway / Load Balancer / etc.>

**Step 2: Load Balancer (if applicable)**
- **Type**: <Application LB / Network LB / Gateway>
- **Public IP**: <IP address>
- **Backend Pool**: <target resources>
- **Health Probe**: <probe config>
- **Rules**: <listener rules, routing rules>

**Step 3: Network Security**
- **NSG/Security Group Applied**: <name>
- **Relevant Rules**:

| Priority | Direction | Source | Destination | Port | Protocol | Action |
|----------|-----------|--------|-------------|------|----------|--------|
| <num> | Inbound | <source> | <dest> | <port> | <proto> | Allow/Deny |

**Step 4: Routing**
- **Route Table**: <name>
- **Relevant Routes**:

| Destination CIDR | Next Hop Type | Next Hop |
|------------------|---------------|----------|
| <cidr> | <type> | <address/gateway> |

**Step 5: Target Resource**
- **Resource Name**: <name>
- **Subnet**: <subnet name - CIDR>
- **Private IP**: <IP>
- **Network Interface**: <NIC name>
- **Associated NSG**: <NSG name>

**Step 6: Outbound Dependencies**
- **Calls to**: <downstream services>
- **Outbound rules**: <relevant security rules>
- **Return path**: <how traffic returns>

---

## Security Posture

### Security Layers
- [ ] **Internet-facing**: Protected by <WAF/DDoS/etc>
- [ ] **Network segmentation**: Subnets isolated by <NSGs/NACLs>
- [ ] **Private endpoints**: <Yes/No - list>
- [ ] **Encryption in transit**: <TLS version>
- [ ] **Least privilege**: Security rules follow principle of least privilege

### Potential Risks
- **HIGH**: <identified high-risk items>
- **MEDIUM**: <identified medium-risk items>
- **LOW**: <identified low-risk items>

### Recommendations
1. <Security recommendation 1>
2. <Security recommendation 2>

---

## Network Configuration Summary

### IP Address Allocation
- **VNet/VPC CIDR**: <overall address space>
- **Subnets in use**: <list with allocation %>
- **Available address space**: <remaining IPs>

### DNS Configuration
- **DNS servers**: <custom or default>
- **Private DNS zones**: <list zones>
- **Public DNS**: <domains and records>

### Connectivity Summary
- **Inbound Internet**: <Yes/No - via what>
- **Outbound Internet**: <Yes/No - via NAT/IGW>
- **Cross-VNet/VPC**: <peering details>
- **Hybrid connectivity**: <VPN/ExpressRoute/Direct Connect>

---

## Troubleshooting Guide

### Common Issues and Checks

**Issue: Cannot reach resource from internet**
```bash
# Check public IP exists
<cloud-specific command>

# Check NSG/SG allows traffic
<cloud-specific command>

# Check route table
<cloud-specific command>

# Verify resource is running
<cloud-specific command>
```

**Issue: Cannot connect between subnets**
```bash
# Check route table has route to destination
<cloud-specific command>

# Check NSG/NACL allows traffic
<cloud-specific command>

# Check no asymmetric routing
<cloud-specific command>
```

**Issue: Internet access not working**
```bash
# Check NAT Gateway exists
<cloud-specific command>

# Check route to 0.0.0.0/0 points to NAT/IGW
<cloud-specific command>

# Check outbound rules allow traffic
<cloud-specific command>
```

---

## Appendix: Raw Configuration

<Include relevant raw outputs from discovery commands>

---

Generated using network-flow-builder.md (discovery workflow)
Last updated: <date>
```

### Step 5: User Review

Ask: **"Does this network flow documentation accurately represent your network setup? Any corrections or additional flows to document?"**

---

## PLANNING WORKFLOW (New Network Infrastructure)

**When to use**: Designing new network architecture, adding network resources

### PHASE 1: REQUIREMENTS GATHERING

#### Step 1A: Check for Existing Ticket

Ask user ONE question:

**"Do you have an existing ticket (ID or URL) for this network infrastructure change?"**

**IF YES**:
- Ask for ticket ID or URL
- Fetch ticket details if possible
- Extract requirements

**IF NO**:
- Proceed to Step 1B (manual definition)

#### Step 1B: Define Network Requirements

Ask user ONE question at a time:

**Question 1**: "What network infrastructure do you need to create or modify? (1-2 sentence summary)"
- Example: "Create new VNet with 3 subnets for dev/staging/prod"
- Example: "Add private endpoint for Azure SQL Database"
- Example: "Set up VPN connection between on-prem and cloud"

**Question 2**: "What is the business/technical driver for this network change?"
- Enable new application deployment
- Improve security (network isolation)
- Enable hybrid connectivity
- Disaster recovery / multi-region
- Compliance requirements

**Question 3**: "What cloud provider and region(s)?"
- Azure: <region(s)>
- AWS: <region(s)>
- GCP: <region(s)>
- Multi-cloud
- Hybrid (cloud + on-premises)

**Question 4**: "What internal resource types will use this network?"
- Azure Web Apps / App Services
- Azure Container Apps
- Kubernetes clusters (AKS, EKS, GKE)
- Virtual Machines
- Databases (SQL, PostgreSQL, CosmosDB, etc.)
- Storage accounts
- Functions / Serverless
- API Management
- Expected number of resources
- Expected traffic volume (GB/month, requests/second)

**Question 5**: "What are the connectivity requirements?"
- Internet-facing (inbound from public internet)
- Outbound internet access only
- Private only (no internet)
- Hybrid connectivity (VPN, ExpressRoute, Direct Connect)
- Cross-VNet/VPC connectivity
- **B2B customer connectivity** (connecting to external untrusted customer infrastructure)
- Multi-region connectivity (traffic between regions)

**Question 6**: "Do you need to connect to external customer infrastructure (B2B scenarios)?"
- If YES:
  - Customer network is UNTRUSTED - requires strict security boundaries
  - Connection method: VPN, ExpressRoute with customer, API Gateway, Private Link cross-tenant
  - Security requirements: DMZ, firewall inspection, traffic filtering
  - Data residency requirements
  - Customer IP ranges (may overlap with internal - needs NAT)
- If NO: Internal-only network

**Question 7**: "What are the security requirements?"
- Network isolation/segmentation
- Encryption requirements (TLS version, at-rest encryption)
- Firewall/WAF needed
- DMZ for untrusted connections (B2B customers)
- Compliance standards (PCI-DSS, HIPAA, SOC2, ISO 27001, etc.)
- Private endpoints required
- Zero Trust architecture principles
- DDoS protection

**Question 8**: "What are the scalability and availability requirements?"
- Target regions (for multi-region HA)
- Failover requirements (active-passive, active-active)
- Expected scale (concurrent connections, requests/second)
- Auto-scaling requirements
- Global load balancing needed

### PHASE 2: EXISTING NETWORK DISCOVERY

**CRITICAL**: Check for existing network infrastructure

#### Step 2A: Discover Existing Network Resources

**FIRST: Check for existing IaC templates**:

Run discovery commands:

```bash
# Search for BICEP network files
find . -name "*.bicep" -exec grep -l "Microsoft.Network\|vnet\|subnet" {} \; 2>/dev/null

# Search for Terraform network files
find . -name "*.tf" -exec grep -l "azurerm_virtual_network\|aws_vpc\|google_compute_network" {} \; 2>/dev/null

# Search for CloudFormation network templates
find . -name "*.yaml" -o -name "*.yml" | xargs grep -l "AWS::EC2::VPC\|AWS::EC2::Subnet" 2>/dev/null
```

**Report findings**:
- If network templates found: List files and what networks are defined
- Ask: "Should I extend these existing network templates or create new ones?"

**THEN: Check for existing network resources**:

Ask: **"Are there existing VNets/VPCs that I should connect to or build upon?"**

**If YES**:
- Run cloud-specific discovery commands (from Discovery Workflow Step 2)
- Document existing network topology
- Identify: Address space, subnets, existing peerings, security groups

**If NO**:
- Proceed with greenfield network design

#### Step 2B: Production Reference Check

Ask: **"Are there similar network setups already in production that I should reference?"**

**If YES**:
- Get production network name/ID
- Fetch configuration:
  ```bash
  # Azure
  az network vnet show --name <vnet-name> --resource-group <rg> --output json

  # AWS
  aws ec2 describe-vpcs --vpc-ids <vpc-id>
  aws ec2 describe-subnets --filters "Name=vpc-id,Values=<vpc-id>"

  # GCP
  gcloud compute networks describe <network-name>
  ```
- Learn patterns: Naming conventions, CIDR allocation, subnet structure, security groups

**If NO**:
- Proceed with best practices

### PHASE 3: ENTERPRISE ARCHITECTURE PATTERNS

#### Step 3A: Determine Architecture Pattern

**Ask**: "What enterprise architecture pattern fits your requirements?"

**Pattern 1: Hub-Spoke Topology** (Recommended for enterprise)
- Central hub VNet/VPC for shared services (firewall, VPN gateway, monitoring)
- Spoke VNets/VPCs for workloads (dev, staging, prod, customer zones)
- All traffic routes through hub for central security inspection
- Use cases: Centralized governance, multi-environment isolation

**Pattern 2: Multi-Region Hub-Spoke** (Global enterprise)
- Regional hub-spoke per region
- Inter-region connectivity via global peering or transit gateway
- Global load balancing for traffic distribution
- Use cases: Global presence, disaster recovery, data residency

**Pattern 3: Customer Isolation Zones** (B2B with untrusted customers)
- Separate VNet/VPC per customer or customer group
- DMZ zone for customer-facing services
- Firewall/NVA between customer zones and internal resources
- NAT for overlapping customer IP ranges
- Use cases: Multi-tenant B2B, customer data isolation

**Pattern 4: Zero Trust Micro-Segmentation**
- Granular network policies per service
- Service mesh for container/K8s environments
- Identity-based access (no network-based trust)
- Use cases: Highly secure environments, compliance-heavy

**Pattern 5: Hybrid Multi-Cloud**
- Workloads across multiple cloud providers
- Central hub for cross-cloud routing
- Use cases: Cloud vendor diversity, best-of-breed services

**Recommended pattern based on requirements**: <auto-suggest based on Q6 and Q8>

---

### PHASE 4: VPN AND EDGE DEVICE PLANNING (If B2B VPN Required)

#### Step 4A: VPN Gateway Planning

**Ask**: "Do you need Site-to-Site VPN or Point-to-Site VPN for customer connectivity?"

**Site-to-Site VPN (S2S) Planning**:

**Step 1: VPN Gateway Sizing**
| SKU | Throughput | Max S2S Tunnels | Max P2S Connections | BGP Support | Use Case |
|-----|------------|-----------------|---------------------|-------------|----------|
| VpnGw1 | 650 Mbps | 30 | 250 | Yes | Small B2B |
| VpnGw2 | 1 Gbps | 30 | 500 | Yes | Medium B2B |
| VpnGw3 | 1.25 Gbps | 30 | 1000 | Yes | Large B2B |
| VpnGw4 | 5 Gbps | 100 | 5000 | Yes | Enterprise |
| VpnGw5 | 10 Gbps | 100 | 10000 | Yes | High-throughput |

**Recommendation**: Start with VpnGw2, scale up if needed

**Step 2: Customer VPN Device Requirements**

Gather from customer:
- [ ] VPN device make/model (e.g., Cisco ASA, Fortinet FortiGate, pfSense)
- [ ] Public IP address of customer VPN device
- [ ] Does device support BGP? (for dynamic routing)
- [ ] Supported IPsec parameters:
  - IKE version (IKEv1 or IKEv2)
  - Encryption algorithm (AES256, AES128, 3DES)
  - Integrity/Hash algorithm (SHA256, SHA1, MD5)
  - DH Group (2, 14, 24, etc.)
  - PFS Group (None, PFS2048, etc.)

**Step 3: IP Address Planning for VPN**

**GatewaySubnet** (Required):
- Name MUST be "GatewaySubnet" (exact name, case-sensitive for Azure)
- Minimum size: /29 (8 IPs)
- Recommended: /27 (32 IPs) for future growth
- Example: 10.1.255.0/27

**Customer Local Network Gateway**:
- Customer on-premises address space: <customer CIDR ranges>
- Example: 192.168.0.0/16
- **Check for IP overlap** with your VNets - if overlap, plan NAT

**VPN Address Pools** (for Point-to-Site):
- Client address pool (for P2S users): <CIDR>
- Must NOT overlap with VNet or on-premises ranges
- Example: 172.16.0.0/24

**Step 4: Routing Design for VPN**

**Static Routing** (Simple):
- Define static routes to customer network
- Example: Route to 192.168.0.0/16 via VPN tunnel

**BGP Routing** (Dynamic - Recommended for production):
- Enables automatic route exchange
- Configure BGP ASN (Autonomous System Number):
  - Your BGP ASN: <ASN number, e.g., 65515>
  - Customer BGP ASN: <customer ASN>
- BGP peer IP addresses configured automatically

**Step 5: VPN Configuration Template**

**Azure VPN Gateway Configuration**:
```bash
# Create VPN Gateway subnet
az network vnet subnet create \
  --vnet-name <vnet-name> \
  --name GatewaySubnet \
  --resource-group <rg> \
  --address-prefixes 10.1.255.0/27

# Create Public IP for VPN Gateway
az network public-ip create \
  --name <vpn-gateway-pip> \
  --resource-group <rg> \
  --allocation-method Static \
  --sku Standard

# Create VPN Gateway
az network vnet-gateway create \
  --name <vpn-gateway-name> \
  --resource-group <rg> \
  --vnet <vnet-name> \
  --gateway-type Vpn \
  --vpn-type RouteBased \
  --sku VpnGw2 \
  --public-ip-addresses <vpn-gateway-pip> \
  --no-wait

# Create Local Network Gateway (represents customer network)
az network local-gateway create \
  --name <customer-lng-name> \
  --resource-group <rg> \
  --gateway-ip-address <customer-vpn-public-ip> \
  --local-address-prefixes <customer-cidr>

# Create VPN Connection (S2S)
az network vpn-connection create \
  --name <connection-name> \
  --resource-group <rg> \
  --vnet-gateway1 <vpn-gateway-name> \
  --local-gateway2 <customer-lng-name> \
  --shared-key <pre-shared-key> \
  --connection-protocol IKEv2
```

**Customer VPN Device Configuration** (Example - Cisco ASA):

Provide to customer:
```
! Azure VPN Gateway Public IP
crypto isakmp policy 10
 encryption aes 256
 hash sha256
 group 2
 lifetime 28800

crypto ipsec transform-set azure-ipsec-proposal-set esp-aes 256 esp-sha256-hmac
 mode tunnel

crypto ipsec profile azure-ipsec-profile
 set transform-set azure-ipsec-proposal-set
 set pfs group2

tunnel-group <azure-vpn-gateway-public-ip> type ipsec-l2l
tunnel-group <azure-vpn-gateway-public-ip> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <pre-shared-key>
 ikev2 local-authentication pre-shared-key <pre-shared-key>

interface Tunnel0
 nameif azure-tunnel
 ip address <customer-tunnel-ip> 255.255.255.252
 tunnel source interface outside
 tunnel destination <azure-vpn-gateway-public-ip>
 tunnel mode ipsec ipv4
 tunnel protection ipsec profile azure-ipsec-profile

route azure-tunnel <azure-vnet-cidr> 255.255.0.0 <tunnel-next-hop> 1
```

**Point-to-Site VPN Configuration**:
```bash
# Add P2S configuration to existing VPN Gateway
az network vnet-gateway update \
  --name <vpn-gateway-name> \
  --resource-group <rg> \
  --client-protocol OpenVPN SSTP \
  --vpn-auth-type AAD \
  --aad-tenant <azure-ad-tenant-id> \
  --aad-audience <application-id> \
  --aad-issuer <issuer-url>

# Or with certificate authentication
az network vnet-gateway root-cert create \
  --gateway-name <vpn-gateway-name> \
  --resource-group <rg> \
  --name <root-cert-name> \
  --public-cert-data <base64-encoded-cert>
```

---

#### Step 4B: Edge Device Planning (SD-WAN, Firewall, VPN Appliances)

**Ask**: "Do you need to deploy edge devices for customer connectivity?"

**Common Edge Device Types**:
- **VPN concentrator** (dedicated VPN device)
- **Next-Gen Firewall** (Palo Alto, Fortinet, Check Point)
- **SD-WAN appliance** (VMware VeloCloud, Cisco Meraki, Silver Peak)
- **Network Virtual Appliance (NVA)** in cloud

**Edge Device Sizing**:

| Expected Throughput | Concurrent Connections | Recommended Device |
|---------------------|------------------------|-------------------|
| < 500 Mbps | < 5,000 | Small VM NVA (Standard_D4s_v3) |
| 500 Mbps - 2 Gbps | 5,000 - 25,000 | Medium VM NVA (Standard_D8s_v3) |
| 2 Gbps - 10 Gbps | 25,000 - 100,000 | Large VM NVA (Standard_D16s_v3) |
| > 10 Gbps | > 100,000 | Dedicated hardware appliance |

**Edge Device Placement**:

**Option 1: DMZ Subnet (Recommended)**
```
[Internet / Customer VPN]
  ↓
[Edge Device in DMZ Subnet]
  ↓ (Inspection, Filtering)
[Azure Firewall / Internal Firewall]
  ↓
[Internal Resources]
```

**Option 2: Hub VNet Pattern**
```
[Hub VNet: 10.0.0.0/16]
  ├─ GatewaySubnet (VPN Gateway)
  ├─ FirewallSubnet (Edge Device / NVA)
  └─ Peering to Spoke VNets
      ├─ Spoke 1: Production (10.1.0.0/16)
      ├─ Spoke 2: Staging (10.2.0.0/16)
      └─ Spoke 3: Customer DMZ (10.3.0.0/16)
```

**Edge Device Configuration Requirements**:

**Network Interfaces**:
- [ ] External/WAN interface (public IP for internet-facing)
- [ ] Internal/LAN interface (connected to internal subnet)
- [ ] DMZ interface (for customer traffic) - optional

**Routing**:
- [ ] Default route (0.0.0.0/0) to internet via WAN interface
- [ ] Static routes to internal networks via LAN interface
- [ ] BGP peering with cloud VPN gateway (if applicable)

**High Availability**:
- [ ] Deploy at least 2 edge devices in HA pair
- [ ] Use Azure Load Balancer / AWS NLB in front
- [ ] Active-Passive or Active-Active configuration

**Example: Deploy Palo Alto NVA in Azure**:
```bash
# Create availability set for HA
az vm availability-set create \
  --name palo-alto-avset \
  --resource-group <rg>

# Deploy from Azure Marketplace
az vm create \
  --name palo-alto-fw-1 \
  --resource-group <rg> \
  --image paloaltonetworks:vmseries-flex:byol:latest \
  --size Standard_D8s_v3 \
  --availability-set palo-alto-avset \
  --nics <nic-external> <nic-internal> <nic-dmz>

# Repeat for second instance (HA)
```

---

### PHASE 5: NETWORK DESIGN

#### Step 5A: IP Address Planning

**Determine address space**:

Ask: **"What IP address range should I use for this network?"**

**Guidance**:
- **Azure VNet**: RFC 1918 private ranges
  - 10.0.0.0/8 (large deployments)
  - 172.16.0.0/12 (medium deployments)
  - 192.168.0.0/16 (small deployments)

- **AWS VPC**: /16 to /28 CIDR blocks
  - Recommended: /16 for flexibility (65,536 IPs)

- **GCP VPC**: Can use any RFC 1918 ranges
  - Subnets can be auto or custom mode

**Avoid conflicts**:
- Check existing network ranges
- Check on-premises ranges (if hybrid)
- Check peered networks
- Reserve space for future growth

**Enterprise IP Planning Considerations**:

**For B2B Customer Connectivity**:
- Assume customer IP ranges MAY OVERLAP with internal ranges
- Plan for NAT (Network Address Translation) at customer boundaries
- Allocate separate non-overlapping range for customer-facing services
- Document customer IP ranges in planning doc

**For Multi-Region**:
- Each region gets unique non-overlapping CIDR block
- Example: Region 1 = 10.1.0.0/16, Region 2 = 10.2.0.0/16
- Enables easy routing between regions

**Subnet design**:

Create subnet plan (enterprise template):

| Subnet Name | CIDR | Purpose | Resource Types | Reserved IPs | Available IPs |
|-------------|------|---------|----------------|--------------|---------------|
| GatewaySubnet | <cidr>/27 | VPN/ExpressRoute Gateway | Gateway only | <cloud> | <usable> |
| AzureFirewallSubnet | <cidr>/26 | Azure Firewall | Firewall | <cloud> | <usable> |
| DMZ-Subnet | <cidr>/24 | Customer-facing services | Web Apps, APIM, LBs | <cloud> | <usable> |
| Web-Tier-Subnet | <cidr>/24 | Internal web tier | Web Apps, Container Apps | <cloud> | <usable> |
| App-Tier-Subnet | <cidr>/24 | Application tier | Container Apps, K8s, VMs | <cloud> | <usable> |
| Data-Tier-Subnet | <cidr>/24 | Database tier | SQL, CosmosDB, Storage | <cloud> | <usable> |
| K8s-Node-Subnet | <cidr>/22 | Kubernetes nodes | AKS/EKS/GKE nodes | <cloud> | <usable> |
| K8s-Pod-Subnet | <cidr>/20 | Kubernetes pods | Pods (Azure CNI) | <cloud> | <usable> |
| PrivateEndpoint-Subnet | <cidr>/24 | Private endpoints | PaaS private endpoints | <cloud> | <usable> |
| Customer-Zone-A-Subnet | <cidr>/24 | Customer A isolated zone | Customer A resources | <cloud> | <usable> |

**Best practices**:
- Separate subnets by tier (web, app, data)
- Separate subnets by environment (if in same VNet)
- Create dedicated subnet for private endpoints (Azure)
- Create dedicated subnet for load balancers
- **DMZ subnet** for untrusted customer-facing services
- **Large K8s subnets** - pods consume many IPs (Azure CNI: 1 IP per pod)
- **Gateway subnet** - specific name required for VPN/ExpressRoute gateways
- Reserve address space for future subnets and scaling

#### Step 4B: Security Design

**CRITICAL for B2B**: Customer traffic is UNTRUSTED and must be strictly controlled

**Network Security Groups / Security Groups**:

For EACH subnet, define security rules:

**Example: DMZ Subnet (Customer-Facing) - UNTRUSTED**

**Inbound Rules**:
| Priority | Source | Destination | Port | Protocol | Action | Purpose |
|----------|--------|-------------|------|----------|--------|---------|
| 100 | Internet | DMZ | 443 | TCP | Allow | HTTPS from customers |
| 110 | CustomerVPN-Range | DMZ | 443 | TCP | Allow | Customer VPN access |
| 200 | * | * | * | * | Deny | Deny all other inbound |

**Outbound Rules**:
| Priority | Source | Destination | Port | Protocol | Action | Purpose |
|----------|--------|-------------|------|----------|--------|---------|
| 100 | DMZ | AzureFirewall | 443 | TCP | Allow | To internal via firewall only |
| 200 | DMZ | Internet | * | * | Deny | NO direct internet from DMZ |
| 300 | * | * | * | * | Deny | Deny all other |

**Example: Internal App Tier - TRUSTED**

**Inbound Rules**:
| Priority | Source | Destination | Port | Protocol | Action | Purpose |
|----------|--------|-------------|------|----------|--------|---------|
| 100 | Web-Tier-Subnet | App-Tier-Subnet | 443 | TCP | Allow | HTTPS from web tier |
| 110 | K8s-Node-Subnet | App-Tier-Subnet | 8080 | TCP | Allow | App port from K8s |
| 200 | DMZ-Subnet | * | * | * | Deny | NO direct access from DMZ |
| 300 | * | * | * | * | Deny | Deny all other |

**Example: Kubernetes Cluster Network Policies**

For K8s clusters, define NetworkPolicies in addition to NSGs:

```yaml
# Deny all ingress by default
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny-ingress
spec:
  podSelector: {}
  policyTypes:
  - Ingress

# Allow specific app-to-app communication
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 8080
```

**Security principles**:
- **Zero Trust**: Never trust, always verify
- **Default deny all** (explicit allow only required traffic)
- **Principle of least privilege**
- **No direct internet access to data tier**
- **DMZ isolation**: Customer-facing services isolated from internal
- **Firewall-mediated**: All DMZ-to-internal traffic through firewall/NVA
- Use service tags/security groups for dynamic references
- **Micro-segmentation**: Granular policies in K8s with NetworkPolicies

**B2B Security Layers**:

**Layer 1: Customer Entry Point**
- [ ] API Gateway with throttling and authentication
- [ ] WAF with OWASP top 10 protection
- [ ] DDoS Protection Standard
- [ ] Rate limiting per customer tenant

**Layer 2: DMZ Zone**
- [ ] Dedicated subnet for customer-facing services
- [ ] NSG blocking all except required ports
- [ ] No direct outbound to internet (force through firewall)
- [ ] Monitoring and logging all traffic

**Layer 3: Firewall/NVA Inspection**
- [ ] Azure Firewall / AWS Network Firewall / Palo Alto / Fortinet
- [ ] Inspect all traffic from DMZ to internal resources
- [ ] Application-level filtering (Layer 7)
- [ ] Threat intelligence feeds enabled
- [ ] IDS/IPS enabled

**Layer 4: Internal Network Segmentation**
- [ ] Separate NSGs per tier (web, app, data)
- [ ] Private endpoints for PaaS services (no public access)
- [ ] Service mesh for K8s (Istio, Linkerd) with mTLS

**Layer 5: Identity and Access**
- [ ] Managed identities (no credentials in code)
- [ ] Azure AD / IAM roles for service-to-service auth
- [ ] Customer access via federated identity (SAML/OIDC)

**Additional security**:
- [ ] WAF needed (for internet-facing apps) - REQUIRED for B2B
- [ ] DDoS Protection Standard - REQUIRED for customer-facing
- [ ] Private endpoints (Azure) / Private Link (AWS) - REQUIRED for PaaS
- [ ] Network flow logs enabled - REQUIRED for compliance
- [ ] Firewall (Azure Firewall, AWS Network Firewall) - REQUIRED for DMZ
- [ ] Customer traffic isolation (separate VNet/VPC per customer if high security)
- [ ] NAT for overlapping customer IP ranges
- [ ] Encryption in transit (TLS 1.2+) - REQUIRED
- [ ] Certificate pinning for customer APIs

#### Step 4C: Connectivity Design

**Internet connectivity**:
- **Inbound (customer traffic)**:
  - Global load balancer (Azure Front Door, AWS CloudFront, GCP Cloud CDN)
  - Regional load balancer (Azure App Gateway, AWS ALB/NLB, GCP Load Balancer)
  - API Management Gateway (Azure APIM, AWS API Gateway, GCP Apigee)
  - WAF in front of all public endpoints
- **Outbound (to internet)**: NAT Gateway, Internet Gateway, Cloud NAT
  - Route through firewall for inspection

**Cross-network connectivity (internal)**:
- **VNet-to-VNet**: VNet Peering (Azure), VPC Peering (AWS), VPC Peering (GCP)
- **Hub-spoke**: Central hub VNet with spokes
  - Hub: Shared services (firewall, VPN, monitoring)
  - Spokes: Workloads (dev, staging, prod, DMZ)
- **Transit**: Azure Virtual WAN, AWS Transit Gateway, GCP Network Connectivity Center
- **Multi-region**: Global VNet/VPC peering or transit gateway

**B2B Customer Connectivity (UNTRUSTED)**:

**Option 1: API Gateway (Recommended for most B2B)**
- Customer calls public API endpoint
- API Management handles auth, throttling, rate limiting
- API Gateway routes to internal services via private network
- No direct network connectivity to customer infrastructure
- **Pros**: Simple, secure, scalable, no IP overlap issues
- **Cons**: API-based only (not suitable for network-level integration)

**Option 2A: Site-to-Site VPN to Customer (S2S VPN)**
- IPsec VPN tunnel between your VPN Gateway and customer VPN device
- Customer traffic lands in DMZ subnet
- Firewall inspects all traffic from customer VPN
- **Requirements**:
  - VPN Gateway (Azure VPN Gateway, AWS VPN Gateway, GCP Cloud VPN)
  - Customer VPN device (public IP, supports IPsec)
  - Shared key / pre-shared key (PSK) for authentication
  - BGP support (optional, for dynamic routing)
- **Pros**: Network-level connectivity for legacy apps, secure encrypted tunnel
- **Cons**: IP overlap potential (requires NAT), complex management, throughput limits
- **Security**: REQUIRED firewall inspection, no direct access to internal
- **Throughput**: Up to 1.25 Gbps (Azure VPN Gateway VpnGw1), higher with VpnGw3 or VpnGw5
- **Use case**: B2B customer needs network-level access to specific services

**Option 2B: Point-to-Site VPN (P2S VPN) for Customer Users**
- Individual customer users connect via VPN client
- User authenticates (certificate, Azure AD, RADIUS)
- User traffic lands in DMZ subnet with restricted access
- **Requirements**:
  - VPN Gateway with P2S configuration
  - Client address pool (separate from your VNet CIDR)
  - Authentication method (certificates, Azure AD, RADIUS)
  - VPN client software (native or OpenVPN)
- **Pros**: No customer VPN device needed, user-based access control
- **Cons**: Per-user management, not suitable for high-volume B2B
- **Security**: User authentication required, access restricted by NSG/firewall
- **Use case**: Customer admins/support staff need access, not for production traffic

**Option 3: ExpressRoute with Customer (Azure) / Direct Connect (AWS)**
- Dedicated private connection to customer
- Customer traffic isolated in separate VNet/VPC
- Firewall between customer VNet and internal VNets
- **Pros**: High bandwidth, low latency, private connection
- **Cons**: Expensive, complex setup, long lead time
- **Security**: REQUIRED firewall inspection, dedicated customer VNet

**Option 4: Private Link Cross-Tenant (Azure)**
- Expose specific service via Private Endpoint
- Customer accesses via Private Link in their tenant
- No IP overlap, no VPN needed
- **Pros**: Secure, no IP management, service-specific access
- **Cons**: Azure-only, limited to supported services

**Option 5: Customer Isolation - Separate VNet/VPC per Customer**
- Fully isolated network environment per customer
- No shared resources with other customers
- Firewall/NVA between customer VNet and shared services
- **Pros**: Maximum isolation, no noisy neighbor
- **Cons**: Higher cost, management overhead
- **Use case**: High-security customers, compliance requirements

**Handling IP Overlaps (Common in B2B)**:
- **Problem**: Customer uses 10.0.0.0/16, you use 10.0.0.0/8 (overlap!)
- **Solution 1**: NAT (Network Address Translation)
  - Translate customer IPs to unique range at boundary
  - Example: Customer 10.1.0.5 → NAT to 100.64.1.5
- **Solution 2**: Use API Gateway (avoids IP-level connectivity)
- **Solution 3**: Require customer to use specific non-overlapping range

**Hybrid connectivity (on-premises)**:
- **VPN**: Site-to-Site VPN (encrypted over internet)
- **Dedicated connection**: ExpressRoute (Azure), Direct Connect (AWS), Interconnect (GCP)

**DNS**:
- **Private DNS zones** for internal name resolution
  - Azure Private DNS, Route 53 Private Hosted Zones, Cloud DNS Private Zones
  - Link to VNets/VPCs
- **Public DNS** for external/customer-facing services
  - Azure DNS, Route 53, Cloud DNS
  - A records for load balancers, CNAME for CDN
- **DNS forwarding** for hybrid scenarios
  - Forward on-prem queries to cloud DNS and vice versa

**Service Mesh (for Kubernetes)**:
- **Istio**: Full-featured service mesh with mTLS, traffic management, observability
- **Linkerd**: Lightweight, simple service mesh
- **Consul**: Multi-cloud service mesh
- **Use case**: Microservices in K8s needing service-to-service encryption and routing

#### Step 3D: Routing Design

Define route tables:

| Route Name | Destination CIDR | Next Hop Type | Next Hop | Purpose |
|------------|------------------|---------------|----------|---------|
| <name> | <cidr> | <type> | <address> | <reason> |

**Common routing scenarios**:
- Default route (0.0.0.0/0) → Internet Gateway / NAT Gateway
- VNet/VPC peering routes (automatic or manual)
- Force tunneling (route all traffic through firewall/VPN)
- Custom routes for Network Virtual Appliances

### PHASE 4: GENERATE NETWORK PLAN

Create `/specs/NETWORK_<change>.md`:

```markdown
# Network Infrastructure Plan - <Change Name>

**Ticket**: <ticket-id>
**Type**: <Greenfield/Brownfield/Hybrid>
**Cloud Provider**: <Azure/AWS/GCP/Multi-cloud>
**Region(s)**: <regions>
**Created**: <date>

---

## 1. OBJECTIVE

**Primary Goal**: <What this network infrastructure achieves>
**Business Context**: <Why this network is needed>

**Success Criteria**:
- [ ] Network resources deployed successfully
- [ ] Connectivity verified between all required resources
- [ ] Security rules in place and tested
- [ ] Documentation complete

---

## 2. REQUIREMENTS SUMMARY

### Functional Requirements
- <What the network must enable>
- <Connectivity requirements>
- <Performance requirements>

### Non-Functional Requirements
- **Security**: <security requirements>
- **Compliance**: <compliance standards>
- **Scalability**: <expected growth>
- **Availability**: <SLA requirements>

---

## 3. NETWORK ARCHITECTURE

### High-Level Design

```
┌─────────────────────────────────────────────┐
│  Internet / External Network                │
└────────────────┬────────────────────────────┘
                 │
                 ▼
         [Load Balancer]
                 │
                 ▼
┌─────────────────────────────────────────────┐
│  VNet/VPC: <name> - <CIDR>                  │
│  ┌─────────────────────────────────────┐   │
│  │ Subnet: Web Tier - <CIDR>           │   │
│  │ [Web Servers / App Gateway]         │   │
│  └─────────────┬───────────────────────┘   │
│                │                             │
│                ▼                             │
│  ┌─────────────────────────────────────┐   │
│  │ Subnet: App Tier - <CIDR>           │   │
│  │ [Application Servers]               │   │
│  └─────────────┬───────────────────────┘   │
│                │                             │
│                ▼                             │
│  ┌─────────────────────────────────────┐   │
│  │ Subnet: Data Tier - <CIDR>          │   │
│  │ [Databases / Storage]               │   │
│  └─────────────────────────────────────┘   │
│                                             │
│  [NAT Gateway] → Internet (outbound only)  │
└─────────────────────────────────────────────┘
```

### Network Details

**VNet/VPC Name**: <name>
**Address Space**: <CIDR block(s)>
**Region**: <region>
**Resource Group / Account**: <rg/account>

**Existing Network Integration**:
- **Extends existing network**: <Yes/No - name>
- **Peering required**: <Yes/No - to what>
- **Production reference**: <production network used as reference>

---

## 4. SUBNET DESIGN

| Subnet Name | CIDR | Purpose | Resources | NSG/SG | Route Table |
|-------------|------|---------|-----------|--------|-------------|
| <name> | <cidr> | <purpose> | <resource types> | <nsg-name> | <rt-name> |
| <name> | <cidr> | <purpose> | <resource types> | <nsg-name> | <rt-name> |

**Address Space Utilization**:
- Total available: <total IPs>
- Allocated to subnets: <allocated IPs>
- Reserved for future: <remaining IPs>

---

## 5. SECURITY CONFIGURATION

### Network Security Groups / Security Groups

#### <NSG/SG Name> - <Associated Subnet>

**Inbound Rules**:

| Priority | Source | Destination | Port | Protocol | Action | Purpose |
|----------|--------|-------------|------|----------|--------|---------|
| 100 | Internet | VirtualNetwork | 443 | TCP | Allow | HTTPS from internet |
| 110 | VirtualNetwork | VirtualNetwork | 1433 | TCP | Allow | SQL from app tier |
| 200 | * | * | * | * | Deny | Deny all other |

**Outbound Rules**:

| Priority | Source | Destination | Port | Protocol | Action | Purpose |
|----------|--------|-------------|------|----------|--------|---------|
| 100 | VirtualNetwork | Internet | 443 | TCP | Allow | HTTPS outbound |
| 200 | * | * | * | * | Deny | Deny all other |

### Additional Security

**Private Endpoints**:
- [ ] Azure SQL Database: <endpoint name>
- [ ] Storage Account: <endpoint name>
- [ ] Key Vault: <endpoint name>

**Firewall/WAF**:
- [ ] Azure Firewall / AWS Network Firewall: <name>
- [ ] WAF Policy: <policy name>

**DDoS Protection**: <Standard/Basic>

**Network Flow Logs**: <Yes/No - storage account>

---

## 6. CONNECTIVITY CONFIGURATION

### Internet Connectivity

**Inbound (from Internet)**:
- **Method**: <Load Balancer / App Gateway / Public IP>
- **Public IP(s)**: <IP addresses or allocation method>
- **Endpoint**: <DNS name>

**Outbound (to Internet)**:
- **Method**: <NAT Gateway / Internet Gateway>
- **Public IP(s)**: <IP addresses or allocation method>

### Cross-Network Connectivity

**VNet/VPC Peering**:
- [ ] Peering to: <vnet/vpc name>
  - Remote address space: <CIDR>
  - Allow gateway transit: <Yes/No>
  - Allow forwarded traffic: <Yes/No>

**Hybrid Connectivity**:
- [ ] VPN Gateway: <gateway name>
  - Type: Site-to-Site / Point-to-Site
  - On-premises ranges: <CIDR blocks>
  - VPN device IP: <IP>

- [ ] ExpressRoute / Direct Connect
  - Circuit name: <name>
  - Bandwidth: <Mbps/Gbps>
  - Peering location: <location>

### DNS Configuration

**Private DNS**:
- Zone name: <zone.private>
- VNet link: <linked vnets>
- Records: <A records, CNAME records>

**Public DNS**:
- Zone name: <domain.com>
- Records: <public endpoints>

---

## 7. ROUTING CONFIGURATION

### Route Tables

#### <Route Table Name> - <Associated Subnets>

| Route Name | Destination CIDR | Next Hop Type | Next Hop | Purpose |
|------------|------------------|---------------|----------|---------|
| default | 0.0.0.0/0 | Internet Gateway | <igw-id> | Default internet |
| to-app-tier | 10.1.1.0/24 | Virtual Network | - | Route to app subnet |
| to-firewall | 10.0.0.0/8 | Virtual Appliance | 10.0.0.10 | Force through firewall |

**BGP Route Propagation**: <Enabled/Disabled>

---

## 8. INFRASTRUCTURE AS CODE PLAN

### IaC Tool Selection
**Chosen Tool**: <Terraform/Bicep/CloudFormation>
**Version**: <tool version>
**Existing Templates**: <Yes/No - if yes, list paths>
**Approach**: <Extend existing/Create new>
**Production Reference**: <network name used as reference>

### Implementation Steps

**Step 1: Create VNet/VPC**
```bash
<IaC command to create network>
```

**Expected Output**: VNet/VPC created with address space <CIDR>

**Step 2: Create Subnets**
```bash
<IaC command to create subnets>
```

**Expected Output**: <N> subnets created

**Step 3: Create NSGs/Security Groups**
```bash
<IaC command to create security groups>
```

**Step 4: Associate NSGs to Subnets**
```bash
<IaC command to associate>
```

**Step 5: Create Route Tables**
```bash
<IaC command to create route tables>
```

**Step 6: Create Connectivity Resources**
```bash
# NAT Gateway / Internet Gateway
<IaC command>

# VNet Peering (if applicable)
<IaC command>

# VPN Gateway (if applicable)
<IaC command>
```

**Step 7: Configure DNS**
```bash
<IaC command for DNS zones>
```

---

## 9. VERIFICATION AND TESTING

### Connectivity Tests

**Test 1: Internet Outbound**
```bash
# From a VM in the network, test outbound internet
curl -I https://www.google.com

# Expected: 200 OK
```

**Test 2: Subnet-to-Subnet**
```bash
# From VM in subnet A, ping VM in subnet B
ping <private-ip-subnet-b>

# Expected: Successful ping
```

**Test 3: Inbound from Internet** (if applicable)
```bash
# From external client, access public endpoint
curl -I https://<public-endpoint>

# Expected: 200 OK
```

**Test 4: NSG Rules**
```bash
# Verify NSG blocks unauthorized traffic
# From subnet A, attempt connection on blocked port
nc -zv <ip-subnet-b> <blocked-port>

# Expected: Connection refused or timeout
```

**Test 5: DNS Resolution**
```bash
# From VM, resolve private DNS name
nslookup <private-dns-name>

# Expected: Resolves to private IP
```

### Acceptance Criteria

**Functional**:
- [ ] VNet/VPC created with correct address space
- [ ] All subnets created with correct CIDRs
- [ ] NSGs/Security Groups applied with correct rules
- [ ] Internet connectivity working (inbound/outbound as designed)
- [ ] Cross-subnet connectivity verified
- [ ] DNS resolution working

**Security**:
- [ ] All security rules tested and verified
- [ ] No unintended open ports or rules
- [ ] Private endpoints configured (if required)
- [ ] Network flow logs enabled

**Documentation**:
- [ ] Network diagram created
- [ ] Traffic flows documented
- [ ] Troubleshooting guide created

---

## 10. ROLLBACK STRATEGY

### Rollback Plan

**If deployment fails or issues arise**:

**Option 1: IaC Rollback** (preferred)
```bash
# Delete all resources
<IaC destroy command>
```

**Option 2: Manual Rollback**
```bash
# Delete in reverse order of creation
1. Delete peerings/connections
2. Delete route tables
3. Delete NSGs
4. Delete subnets
5. Delete VNet/VPC
```

**Dependencies to check**:
- [ ] No running VMs or services in subnets
- [ ] No active connections/peerings
- [ ] No DNS records pointing to resources

---

## 11. COST ESTIMATE

| Resource | Type/Size | Quantity | Monthly Cost (Est.) |
|----------|-----------|----------|---------------------|
| VNet/VPC | Standard | 1 | $0 (free) |
| NAT Gateway | Standard | 1 | $<X> |
| VPN Gateway | <SKU> | 1 | $<X> |
| Public IP | Static | <N> | $<X> |
| Load Balancer | <SKU> | 1 | $<X> |
| **TOTAL** | | | **$<X>** |

**Data transfer costs**: <estimate based on expected traffic>

---

## 12. MIGRATION PLAN (if modifying existing network)

### Phased Approach

**Phase 1: Create New Resources**
- Duration: <X hours>
- Deploy new VNet/VPC and subnets alongside existing
- No impact to existing resources

**Phase 2: Migrate Resources**
- Duration: <X hours>
- Move resources to new subnets
- Update DNS records
- Potential downtime: <X minutes per resource>

**Phase 3: Cutover**
- Duration: <X hours>
- Switch traffic to new network
- Monitor for issues
- Keep old network for <N days> as fallback

**Phase 4: Decommission Old Network**
- Duration: <X hours>
- Delete old network resources
- Update documentation

---

## 13. MONITORING AND ALERTS

**Metrics to Monitor**:
- [ ] VNet/VPC availability
- [ ] NAT Gateway connection count
- [ ] VPN Gateway throughput
- [ ] NSG flow logs (blocked/allowed traffic)
- [ ] Peering connection status

**Alerting Thresholds**:
- Critical: <threshold>
- Warning: <threshold>

**Dashboard**: <link to monitoring dashboard>

---

## 14. COMPLIANCE AND GOVERNANCE

**Network Tags/Labels**:
- Environment: <dev/staging/prod>
- Owner: <team>
- CostCenter: <cost center>
- Compliance: <compliance requirement>

**Compliance Requirements**:
- [ ] <Compliance standard>: <specific requirement met>
- [ ] Network isolation enforced
- [ ] Encryption in transit required
- [ ] Audit logging enabled

---

## 15. DOCUMENTATION DELIVERABLES

**Network Diagram**: <path to diagram file>
**Traffic Flow Documentation**: <path to flow docs>
**Runbook**: <path to operational runbook>
**IaC Code**: <path to terraform/bicep files>

---

Generated using network-flow-builder.md (planning workflow)
Last updated: <date>
```

### PHASE 5: IMPLEMENTATION

Follow standard implementation workflow:

1. **Use `/full-app-11-implement-milestone-n.md` for implementation**
2. **Quality Gates**:
   - IaC plan validation (check expected resources)
   - Deployment success (no errors)
   - Connectivity tests pass
   - Security tests pass
3. **Validation**: Test each connectivity scenario

### PHASE 6: DOCUMENTATION

After implementation, create final network flow documentation using Discovery Workflow format.

---

## KEY PRINCIPLES

**Discovery/Documentation Mode**:
- Optimize for speed and clarity
- Focus on understanding and documenting
- Generate visual diagrams where possible
- Provide troubleshooting guidance

**Planning Mode**:
- Security first (default deny, least privilege)
- Plan for growth (reserve address space)
- Learn from production (reference existing)
- Document everything (flows, rules, rationale)

**Network Design Best Practices**:
1. **IP planning**: Avoid overlaps, plan for future
2. **Security layers**: Defense in depth (NSG + firewall + WAF)
3. **Segmentation**: Separate tiers (web, app, data)
4. **Monitoring**: Enable flow logs and metrics
5. **Documentation**: Diagram and document all flows
6. **Testing**: Test all connectivity scenarios before production

**Hybrid Connectivity**:
- Use VPN for flexibility, dedicated connections for performance
- Never overlap on-premises and cloud address spaces
- Plan for bidirectional traffic (not just cloud-to-onprem)

**Cost Optimization**:
- Use VNet peering instead of VPN where possible (cheaper, faster)
- Right-size NAT Gateways and VPN Gateways
- Use private endpoints to avoid egress costs
- Monitor data transfer costs

---

## CLOUD-SPECIFIC QUICK REFERENCE

### Azure Networking Resources

**Core Resources**:
- Virtual Network (VNet)
- Subnet
- Network Security Group (NSG)
- Route Table
- NAT Gateway
- VNet Peering

**Connectivity**:
- VPN Gateway
- ExpressRoute
- Azure Firewall
- Application Gateway
- Load Balancer

**DNS**:
- Private DNS Zone
- Public DNS Zone

**Monitoring**:
- Network Watcher
- NSG Flow Logs
- Traffic Analytics

### AWS Networking Resources

**Core Resources**:
- Virtual Private Cloud (VPC)
- Subnet
- Security Group
- Network ACL
- Route Table
- NAT Gateway
- Internet Gateway

**Connectivity**:
- VPN Gateway
- Direct Connect
- Transit Gateway
- VPC Peering
- PrivateLink

**Load Balancing**:
- Application Load Balancer (ALB)
- Network Load Balancer (NLB)
- Gateway Load Balancer

**DNS**:
- Route 53 Private Hosted Zone
- Route 53 Public Hosted Zone

**Monitoring**:
- VPC Flow Logs
- CloudWatch Metrics

### GCP Networking Resources

**Core Resources**:
- VPC Network
- Subnet
- Firewall Rules
- Routes
- Cloud NAT
- Cloud Router

**Connectivity**:
- Cloud VPN
- Cloud Interconnect
- VPC Peering
- Private Service Connect

**Load Balancing**:
- Global HTTP(S) Load Balancer
- Regional Network Load Balancer

**DNS**:
- Cloud DNS (Private/Public Zones)

**Monitoring**:
- VPC Flow Logs
- Cloud Monitoring

### Kubernetes Networking

**Core Resources**:
- Service (ClusterIP, NodePort, LoadBalancer)
- Ingress
- NetworkPolicy
- ConfigMap (for network config)

**Service Mesh** (advanced):
- Istio
- Linkerd

---

## WORKFLOW DECISION TREE

```
START
  ↓
What's the networking task?
  ↓
DISCOVERY/DOCUMENTATION
  ├─ Define scope (topology/flow/troubleshooting)
  ├─ Automated network discovery
  ├─ Traffic flow analysis
  ├─ Generate documentation
  └─ Deliver diagrams and troubleshooting guide

PLANNING (New Infrastructure)
  ├─ Requirements gathering
  ├─ Discover existing networks
  ├─ Check production references
  ├─ Design: IP planning
  ├─ Design: Security (NSGs/SGs)
  ├─ Design: Connectivity
  ├─ Design: Routing
  ├─ Generate comprehensive plan
  ├─ Implementation
  └─ Post-deployment documentation
```

---

## VPN AND EDGE DEVICE TROUBLESHOOTING

### Site-to-Site VPN Troubleshooting

**Issue 1: VPN Tunnel Not Establishing**

**Symptoms**: Connection shows "Not connected" or "Unknown"

**Diagnostic Commands**:
```bash
# Azure: Check VPN connection status
az network vpn-connection show \
  --name <connection-name> \
  --resource-group <rg> \
  --query "{status:connectionStatus, egressBytes:egressBytesTransferred, ingressBytes:ingressBytesTransferred}"

# Check VPN Gateway health
az network vnet-gateway list-bgp-peer-status \
  --name <vpn-gateway-name> \
  --resource-group <rg>

# AWS: Check VPN tunnel status
aws ec2 describe-vpn-connections \
  --vpn-connection-ids <vpn-id> \
  --query "VpnConnections[0].VgwTelemetry"
```

**Common Causes and Fixes**:

| Cause | Check | Fix |
|-------|-------|-----|
| Mismatched pre-shared key | Verify PSK on both sides | Update to matching PSK |
| Firewall blocking UDP 500/4500 | Check customer firewall | Allow UDP 500 (IKE), UDP 4500 (NAT-T) |
| IP address mismatch | Verify customer public IP in LNG | Update Local Network Gateway |
| Incompatible IPsec parameters | Check IKE/IPsec settings | Align encryption, hash, DH group |
| Customer device not configured | Check customer VPN logs | Provide config template to customer |

**Step-by-Step Troubleshooting**:

1. **Verify Basic Connectivity**:
   ```bash
   # From your side, ping customer VPN public IP
   ping <customer-vpn-public-ip>

   # Check if customer can ping your VPN Gateway public IP
   # (Ask customer to test)
   ```

2. **Check VPN Gateway Logs** (Azure):
   ```bash
   # Enable diagnostics
   az monitor diagnostic-settings create \
     --name vpn-diag \
     --resource <vpn-gateway-resource-id> \
     --logs '[{"category":"GatewayDiagnosticLog","enabled":true}]' \
     --workspace <log-analytics-workspace-id>

   # Query logs
   # Go to Azure Portal → VPN Gateway → Diagnostic Logs → GatewayDiagnosticLog
   # Look for IKE negotiation failures
   ```

3. **Verify IPsec Parameters Match**:
   ```bash
   # Azure: Check IPsec policy
   az network vpn-connection ipsec-policy list \
     --connection-name <connection-name> \
     --resource-group <rg>

   # Compare with customer device config
   ```

4. **Check Routing**:
   ```bash
   # Verify VPN Gateway has route to customer network
   az network vnet-gateway list-learned-routes \
     --name <vpn-gateway-name> \
     --resource-group <rg>

   # Check effective routes on VPN Gateway
   az network vnet-gateway list-advertised-routes \
     --name <vpn-gateway-name> \
     --resource-group <rg> \
     --peer <bgp-peer-ip>
   ```

---

**Issue 2: VPN Connected But No Traffic Passing**

**Symptoms**: VPN shows "Connected" but cannot reach resources

**Diagnostic Commands**:
```bash
# Check data transfer
az network vpn-connection show \
  --name <connection-name> \
  --resource-group <rg> \
  --query "{egressBytes:egressBytesTransferred, ingressBytes:ingressBytesTransferred}"

# If bytes = 0, no traffic is flowing
```

**Common Causes**:

| Cause | Check | Fix |
|-------|-------|-----|
| NSG blocking traffic | Check NSG rules on DMZ subnet | Add allow rules for customer CIDR |
| Route table missing | Check routes in DMZ subnet | Add route to customer network via VPN Gateway |
| Firewall rules blocking | Check Azure Firewall / NVA rules | Add allow rules for customer traffic |
| Customer routes missing | Ask customer to check routes | Customer needs route to your VNet |
| IP overlap (NAT needed) | Check for overlapping CIDRs | Implement NAT at boundary |

**Step-by-Step Troubleshooting**:

1. **Test Connectivity from VM in your VNet**:
   ```bash
   # From VM in DMZ or internal subnet
   ping <customer-internal-ip>
   traceroute <customer-internal-ip>

   # Check if traffic hits VPN Gateway
   ```

2. **Check NSG Effective Rules**:
   ```bash
   # Check effective NSG rules on NIC
   az network nic list-effective-nsg \
     --name <nic-name> \
     --resource-group <rg>

   # Look for deny rules blocking customer CIDR
   ```

3. **Check Effective Routes**:
   ```bash
   # Check effective routes on NIC
   az network nic show-effective-route-table \
     --name <nic-name> \
     --resource-group <rg>

   # Verify route to customer network via VPN Gateway
   # Next hop type should be "VirtualNetworkGateway"
   ```

4. **Packet Capture** (Advanced):
   ```bash
   # Azure Network Watcher packet capture
   az network watcher packet-capture create \
     --name <capture-name> \
     --resource-group <rg> \
     --vm <vm-name> \
     --storage-account <storage-account> \
     --time-limit 60

   # Analyze capture for dropped packets
   ```

---

**Issue 3: BGP Peering Not Establishing**

**Symptoms**: VPN connected but BGP peer status shows "Unknown" or "Down"

**Diagnostic Commands**:
```bash
# Check BGP peer status
az network vnet-gateway list-bgp-peer-status \
  --name <vpn-gateway-name> \
  --resource-group <rg>

# Check learned routes via BGP
az network vnet-gateway list-learned-routes \
  --name <vpn-gateway-name> \
  --resource-group <rg>
```

**Common Causes**:

| Cause | Fix |
|-------|-----|
| BGP ASN mismatch | Verify ASN on both sides, update if needed |
| BGP peer IP incorrect | Check BGP peer IPs match on both sides |
| Firewall blocking TCP 179 | Allow TCP 179 for BGP |
| Customer BGP not configured | Provide BGP config to customer |

---

### Edge Device / NVA Troubleshooting

**Issue 1: Edge Device Not Passing Traffic**

**Diagnostic Steps**:

1. **Check Edge Device Interfaces**:
   ```bash
   # SSH into NVA
   ssh admin@<nva-ip>

   # Check interface status (Linux-based NVA)
   ip addr show
   ip link show

   # Check routes
   ip route show

   # Check iptables (if using Linux firewall)
   iptables -L -v -n
   ```

2. **Check Azure UDR (User-Defined Routes)**:
   ```bash
   # Verify route table forces traffic through NVA
   az network route-table route list \
     --route-table-name <route-table> \
     --resource-group <rg>

   # Next hop should be NVA private IP
   ```

3. **Enable IP Forwarding**:
   ```bash
   # Azure NIC must have IP forwarding enabled
   az network nic update \
     --name <nva-nic-name> \
     --resource-group <rg> \
     --ip-forwarding true

   # Inside NVA (Linux)
   echo 1 > /proc/sys/net/ipv4/ip_forward
   ```

**Issue 2: High Latency Through Edge Device**

**Diagnostics**:
```bash
# Check CPU/memory on NVA
ssh admin@<nva-ip>
top
free -m

# Check interface throughput
iftop -i eth0

# Check for packet drops
netstat -i
```

**Fixes**:
- Scale up NVA VM size if CPU/memory maxed out
- Check for unnecessary deep packet inspection rules
- Consider adding more NVAs in load-balanced configuration

---

### Point-to-Site VPN Troubleshooting

**Issue 1: Client Cannot Connect**

**Diagnostic Steps**:

1. **Verify Client Configuration**:
   - Correct VPN server address
   - Correct authentication method (cert, Azure AD, RADIUS)
   - Valid client certificate installed (if cert auth)

2. **Check VPN Gateway Configuration**:
   ```bash
   # Verify P2S configuration
   az network vnet-gateway show \
     --name <vpn-gateway-name> \
     --resource-group <rg> \
     --query "vpnClientConfiguration"
   ```

3. **Check Client Logs**:
   - Windows: Event Viewer → Application and Services Logs → Microsoft → Windows → VPN Client
   - macOS/Linux: Check OpenVPN logs

**Issue 2: Client Connected But Cannot Access Resources**

**Check**:
- NSG allows traffic from P2S address pool
- Route table includes route to P2S address pool
- DNS resolution working (try IP address directly)

---

### VPN Monitoring and Alerting

**Recommended Metrics to Monitor**:

```bash
# Azure: Set up alerts
az monitor metrics alert create \
  --name vpn-tunnel-down-alert \
  --resource-group <rg> \
  --scopes <vpn-gateway-resource-id> \
  --condition "avg TunnelIngressBytes < 1" \
  --window-size 5m \
  --evaluation-frequency 1m
```

**Key Metrics**:
- Tunnel connectivity status (up/down)
- Bytes in/out (should be > 0 if traffic flowing)
- Packet drop count
- BGP peer status (if using BGP)
- Tunnel bandwidth utilization

---

## STRUCTURED LOGGING & OBSERVABILITY

**CRITICAL**: All network infrastructure must implement comprehensive logging and observability from day one.

### Network Logging Requirements

**Structured Logging Format**:
- [ ] JSON-formatted logs (machine-parseable)
- [ ] Standard fields: timestamp, level, source, correlation_id, connection_id, trace_id
- [ ] Network-specific fields: source_ip, dest_ip, source_port, dest_port, protocol, action (allow/deny)
- [ ] No PII/secrets in logs (sanitize sensitive data)

**Log Aggregation**:
- **Azure**: Azure Monitor / Log Analytics Workspace, Network Watcher
- **AWS**: CloudWatch Logs, VPC Flow Logs to S3
- **GCP**: Cloud Logging, VPC Flow Logs
- [ ] All network components send logs to central aggregation
- [ ] Retention policy: <30/60/90 days for hot, archive for compliance>

**Network Flow Logs** (REQUIRED):

**Azure NSG Flow Logs**:
- [ ] Enable NSG Flow Logs version 2 (includes flow state)
- [ ] Storage account for flow logs
- [ ] Traffic Analytics enabled (Log Analytics integration)
- [ ] Capture frequency: 10 minutes (default) or 1 minute (high-frequency)
- [ ] Log format: JSON with fields: time, flowTuples, rule, mac

```bash
# Enable NSG Flow Logs
az network watcher flow-log create \
  --name <flow-log-name> \
  --nsg <nsg-id> \
  --storage-account <storage-account-id> \
  --workspace <log-analytics-workspace-id> \
  --format JSON \
  --log-version 2 \
  --traffic-analytics true \
  --interval 10
```

**AWS VPC Flow Logs**:
- [ ] Enable VPC Flow Logs for all VPCs
- [ ] Destination: CloudWatch Logs or S3
- [ ] Capture: Accepted, Rejected, or All traffic
- [ ] Format: Default or custom (recommend custom for efficiency)
- [ ] Fields: srcaddr, dstaddr, srcport, dstport, protocol, packets, bytes, action

```bash
# Enable VPC Flow Logs
aws ec2 create-flow-logs \
  --resource-type VPC \
  --resource-ids <vpc-id> \
  --traffic-type ALL \
  --log-destination-type cloud-watch-logs \
  --log-group-name /aws/vpc/flowlogs \
  --deliver-logs-permission-arn <iam-role-arn>
```

**GCP VPC Flow Logs**:
- [ ] Enable VPC Flow Logs per subnet
- [ ] Aggregation interval: 5 seconds (default) to 30 seconds
- [ ] Sample rate: 0.5 (50%) to 1.0 (100%)
- [ ] Metadata annotations: Include all metadata

```bash
# Enable VPC Flow Logs
gcloud compute networks subnets update <subnet-name> \
  --region=<region> \
  --enable-flow-logs \
  --logging-aggregation-interval=interval-5-sec \
  --logging-flow-sampling=1.0 \
  --logging-metadata=include-all
```

**VPN Gateway Logging** (REQUIRED for B2B):
- [ ] Connection status logs (tunnel up/down events)
- [ ] IKE negotiation logs (IPsec phase 1/2)
- [ ] BGP peering status logs (if BGP enabled)
- [ ] Data transfer metrics (bytes in/out per tunnel)
- [ ] Authentication attempts (successful/failed)

**Azure VPN Gateway Diagnostics**:
```bash
# Enable VPN Gateway diagnostic logging
az monitor diagnostic-settings create \
  --name vpn-gateway-diag \
  --resource <vpn-gateway-resource-id> \
  --logs '[
    {"category":"GatewayDiagnosticLog","enabled":true},
    {"category":"TunnelDiagnosticLog","enabled":true},
    {"category":"RouteDiagnosticLog","enabled":true},
    {"category":"IKEDiagnosticLog","enabled":true}
  ]' \
  --metrics '[{"category":"AllMetrics","enabled":true}]' \
  --workspace <log-analytics-workspace-id>
```

**Firewall / NVA Logging** (REQUIRED for DMZ):
- [ ] All allowed traffic (source, dest, port, protocol, action)
- [ ] All blocked traffic (detect attack patterns)
- [ ] Threat intelligence hits (malicious IPs, domains)
- [ ] Application-layer inspection results (Layer 7)
- [ ] IDS/IPS alerts

**Azure Firewall Logs**:
```bash
# Enable Azure Firewall diagnostic logging
az monitor diagnostic-settings create \
  --name azfw-diag \
  --resource <firewall-resource-id> \
  --logs '[
    {"category":"AzureFirewallApplicationRule","enabled":true},
    {"category":"AzureFirewallNetworkRule","enabled":true},
    {"category":"AzureFirewallDnsProxy","enabled":true}
  ]' \
  --workspace <log-analytics-workspace-id>
```

**Load Balancer Logging**:
- [ ] Request logs (timestamp, client IP, backend IP, response code)
- [ ] Health probe results (backend health status)
- [ ] Connection metrics (active connections, new connections/sec)
- [ ] Backend failures (when backend unreachable)

**DNS Logging**:
- [ ] DNS query logs (domain, client IP, response code)
- [ ] DNS failures (NXDOMAIN, SERVFAIL)
- [ ] DNS cache performance

**B2B/Customer-Specific Network Logging**:
- [ ] Per-customer VPN tunnel activity (customer_id in all logs)
- [ ] Customer traffic volume per tunnel (bytes in/out)
- [ ] Customer connection attempts (successful/failed)
- [ ] Customer IP ranges (source tracking)
- [ ] VPN tunnel status per customer
- [ ] Customer-specific NSG rule hits

### Metrics & Monitoring

**VPN Gateway Metrics** (REQUIRED):
- [ ] Tunnel connectivity status (0 = down, 1 = up)
- [ ] Tunnel bandwidth utilization (% of gateway capacity)
- [ ] Tunnel ingress/egress bytes
- [ ] Tunnel ingress/egress packet count
- [ ] Tunnel packet drop count
- [ ] BGP peer status (if BGP enabled)
- [ ] BGP routes received/advertised

**Network Gateway Metrics**:
- [ ] NAT Gateway: Connection count, SNAT port utilization
- [ ] Internet Gateway: Bytes in/out
- [ ] ExpressRoute/Direct Connect: Circuit bandwidth utilization

**Firewall / NVA Metrics** (REQUIRED):
- [ ] Throughput (Mbps/Gbps)
- [ ] Connection count (current active connections)
- [ ] New connections per second
- [ ] Blocked requests (firewall denies)
- [ ] Threat intelligence hits
- [ ] CPU/Memory utilization (for VM-based NVAs)
- [ ] Data processed (GB/hour)

**Load Balancer Metrics**:
- [ ] Request rate (requests/sec)
- [ ] Backend response time (p50, p95, p99)
- [ ] Backend healthy/unhealthy host count
- [ ] Connection count (active, new/sec)
- [ ] HTTP response codes (2xx, 4xx, 5xx)

**Network Performance Metrics** (RED Metrics):
- [ ] Request rate (flows/sec through network)
- [ ] Error rate (% of blocked/dropped connections)
- [ ] Duration/Latency (network latency measurements)

**Availability Metrics**:
- [ ] VPN tunnel uptime (% uptime per tunnel)
- [ ] VNet/VPC availability (should be 100%)
- [ ] Peering connection status
- [ ] Gateway health status

**Capacity Metrics**:
- [ ] Subnet IP address utilization (% used)
- [ ] VPN Gateway connection count (vs. SKU limit)
- [ ] NAT Gateway SNAT port utilization
- [ ] Firewall throughput (vs. capacity)

### Network Observability Tools

**Azure Network Watcher**:
- [ ] IP flow verify (test NSG rules)
- [ ] Next hop (verify routing)
- [ ] Connection troubleshoot (test connectivity)
- [ ] Packet capture (deep packet inspection)
- [ ] VPN troubleshoot (diagnose VPN issues)

```bash
# Test IP flow (check if traffic allowed)
az network watcher test-ip-flow \
  --vm <vm-name> \
  --resource-group <rg> \
  --direction Inbound \
  --protocol TCP \
  --local <vm-ip>:443 \
  --remote <source-ip>:*
```

**AWS VPC Reachability Analyzer**:
- [ ] Analyze network paths between source and destination
- [ ] Identify misconfigurations blocking traffic

```bash
# Create reachability analysis
aws ec2 create-network-insights-path \
  --source <eni-id> \
  --destination <eni-id> \
  --protocol tcp \
  --destination-port 443

aws ec2 start-network-insights-analysis \
  --network-insights-path-id <path-id>
```

### Distributed Tracing for Network

**REQUIRED for B2B customer traffic**:
- [ ] Correlation IDs in all logs (trace customer requests across network boundaries)
- [ ] VPN tunnel ID in all customer traffic logs
- [ ] Source customer identification (customer_id, tenant_id)
- [ ] End-to-end request tracing (customer → VPN → DMZ → Firewall → App → Database)

**Tracing Implementation**:
- Insert correlation ID at network entry point (API Gateway, Load Balancer)
- Propagate correlation ID through all network hops
- Log correlation ID at each network inspection point (firewall, NVA)
- Associate network logs with application logs via correlation ID

### Alerting

**Critical Alerts** (Immediate Response Required):

**VPN Alerts**:
- [ ] VPN tunnel down for > 5 minutes
- [ ] VPN Gateway unreachable
- [ ] VPN tunnel flapping (up/down repeatedly)
- [ ] BGP peer down (if BGP enabled)
- [ ] VPN Gateway CPU/memory > 90%

**Firewall / NVA Alerts**:
- [ ] Firewall/NVA unreachable
- [ ] Firewall throughput > 90% capacity
- [ ] High threat intelligence hits (> threshold)
- [ ] Firewall CPU/memory > 90%

**Network Connectivity Alerts**:
- [ ] Peering connection down
- [ ] NAT Gateway SNAT port exhaustion (> 80%)
- [ ] Load balancer all backends unhealthy
- [ ] ExpressRoute/Direct Connect circuit down

**Security Alerts**:
- [ ] Unusual traffic patterns (DDoS, port scan)
- [ ] High rate of blocked connections from single IP
- [ ] NSG rule denying expected traffic (misconfiguration)
- [ ] Unauthorized access attempts to VPN

**Warning Alerts** (Investigation Required):

**Capacity Alerts**:
- [ ] Subnet IP utilization > 80%
- [ ] VPN Gateway connection count > 80% of SKU limit
- [ ] Firewall throughput > 70% capacity

**Performance Alerts**:
- [ ] Network latency > threshold (p95 > Xms)
- [ ] Packet loss detected (> 1%)
- [ ] VPN tunnel bandwidth > 70% of capacity

**Alert Configuration Examples**:

**Azure Alert - VPN Tunnel Down**:
```bash
az monitor metrics alert create \
  --name vpn-tunnel-down-alert \
  --resource-group <rg> \
  --scopes <vpn-gateway-resource-id> \
  --condition "avg TunnelIngressBytes < 1" \
  --window-size 5m \
  --evaluation-frequency 1m \
  --severity 1 \
  --description "VPN tunnel has no traffic for 5 minutes"
```

**AWS CloudWatch Alarm - VPN Tunnel Status**:
```bash
aws cloudwatch put-metric-alarm \
  --alarm-name vpn-tunnel-down \
  --alarm-description "VPN tunnel is down" \
  --metric-name TunnelState \
  --namespace AWS/VPN \
  --statistic Average \
  --period 300 \
  --threshold 0 \
  --comparison-operator LessThanOrEqualToThreshold \
  --evaluation-periods 1 \
  --dimensions Name=VpnId,Value=<vpn-id>
```

### Dashboards

**Network Operations Dashboard** (REQUIRED):

**VPN Connectivity Dashboard**:
- [ ] VPN tunnel status (all tunnels, color-coded: green=up, red=down)
- [ ] VPN Gateway throughput (current vs. capacity)
- [ ] Tunnel bandwidth utilization per customer
- [ ] VPN connection count over time
- [ ] BGP peer status (if applicable)
- [ ] Failed connection attempts

**Firewall / Security Dashboard**:
- [ ] Firewall throughput over time
- [ ] Allowed vs. blocked traffic volume
- [ ] Top blocked source IPs
- [ ] Threat intelligence hits over time
- [ ] Application-layer rule hits (Layer 7)
- [ ] IDS/IPS alerts

**Network Performance Dashboard**:
- [ ] Network latency (p50, p95, p99) over time
- [ ] Packet loss percentage
- [ ] Load balancer request rate and error rate
- [ ] Backend health status
- [ ] NAT Gateway SNAT port utilization

**Customer Traffic Dashboard** (B2B):
- [ ] Traffic volume per customer (MB/hour)
- [ ] Active VPN tunnels per customer
- [ ] Customer connection failures
- [ ] Customer-specific latency metrics
- [ ] Customer SLA compliance (uptime, latency)

**Capacity Dashboard**:
- [ ] Subnet IP utilization (% used per subnet)
- [ ] VPN Gateway connection count (current vs. limit)
- [ ] Firewall throughput (current vs. capacity)
- [ ] ExpressRoute circuit utilization

**Azure Dashboard Example - Network Monitoring**:
```bash
# Create dashboard via Azure Portal or ARM template
# Include:
# - NSG Flow Analytics visualizations
# - VPN Gateway metrics charts
# - Azure Firewall log analytics queries
# - Network Watcher connection monitor results
```

### Log Queries & Analysis

**Pre-configured Essential Queries**:

**Top Blocked Connections**:
```kusto
// Azure Log Analytics - NSG Flow Logs
AzureNetworkAnalytics_CL
| where FlowType_s == "ExternalPublic" and FlowStatus_s == "D" // Denied
| summarize BlockCount=count() by SrcIP_s, DestPort_d
| order by BlockCount desc
| take 20
```

**VPN Tunnel Down Events**:
```kusto
// Azure VPN Gateway Logs
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| where status_s == "Disconnected"
| project TimeGenerated, remoteIP_s, status_s, stateChangeReason_s
| order by TimeGenerated desc
```

**Customer Traffic Volume**:
```kusto
// Track traffic per customer VPN tunnel
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog"
| summarize TotalBytes=sum(BytesSentToDestination_d + BytesReceivedFromDestination_d) by bin(TimeGenerated, 1h), SrcIP_s
| order by TimeGenerated desc
```

**AWS VPC Flow Logs Query** (using CloudWatch Insights):
```sql
fields @timestamp, srcAddr, dstAddr, srcPort, dstPort, action, bytes
| filter action = "REJECT"
| stats count(*) as rejectedConnections by srcAddr
| sort rejectedConnections desc
| limit 20
```

### Observability Checklist

**For EVERY new network component**:
- [ ] Network flow logs enabled (NSG/VPC flow logs)
- [ ] Metrics exported to monitoring system
- [ ] Dashboards created for key metrics
- [ ] Alerts configured for critical failures
- [ ] Runbooks linked to alerts
- [ ] Logs forwarded to central aggregation
- [ ] Correlation IDs implemented for tracing

**For B2B/Customer-Facing VPN**:
- [ ] Per-customer tunnel metrics tracked (customer_id tag)
- [ ] Customer VPN SLA metrics measured (tunnel uptime, latency)
- [ ] Customer traffic anomaly detection
- [ ] Customer-specific alert thresholds
- [ ] Per-customer logging segregation (for compliance)

**For Firewall / NVA**:
- [ ] All traffic logged (allowed and blocked)
- [ ] Threat intelligence enabled and logged
- [ ] Performance metrics monitored (CPU, throughput)
- [ ] High availability monitoring (if HA pair)
- [ ] Configuration change auditing

**For Load Balancers**:
- [ ] Request/response logging enabled
- [ ] Health probe results logged
- [ ] Backend failure alerts configured
- [ ] Performance metrics tracked (latency, throughput)

### Compliance & Audit Logging

**Network Audit Requirements**:

**HIPAA**:
- [ ] All network access logged (source, destination, timestamp)
- [ ] VPN access logs with user identification
- [ ] Network configuration change auditing
- [ ] Audit log retention: minimum 6 years
- [ ] Encryption in transit enforced (TLS 1.2+)

**SOC2**:
- [ ] Network boundary logging (inbound/outbound)
- [ ] VPN authentication logs
- [ ] Firewall rule change auditing
- [ ] Access control logs (NSG/security group changes)
- [ ] Incident response logs

**PCI-DSS**:
- [ ] Network segmentation enforced (cardholder data isolated)
- [ ] All network access to cardholder data logged
- [ ] Firewall configuration changes audited
- [ ] Quarterly network security scans logged
- [ ] Audit log retention: minimum 1 year, 3 months online

**Network Configuration Auditing**:
- [ ] NSG/Security Group rule changes logged (who, when, what)
- [ ] Route table modifications logged
- [ ] VPN configuration changes logged
- [ ] Firewall policy changes logged
- [ ] Network resource creation/deletion logged

**Azure Activity Logs** (for network config changes):
```bash
# Query network resource changes
az monitor activity-log list \
  --resource-group <rg> \
  --start-time 2024-01-01 \
  --query "[?contains(resourceId, 'Microsoft.Network')]" \
  --output table
```

**AWS CloudTrail** (for network config changes):
```bash
# Query VPC-related API calls
aws cloudtrail lookup-events \
  --lookup-attributes AttributeKey=ResourceType,AttributeValue=AWS::EC2::VPC \
  --start-time 2024-01-01
```

---

## DOCUMENTATION

**CRITICAL**: Comprehensive network documentation must be created once deployment and testing are complete. Network documentation is essential for troubleshooting, capacity planning, security audits, and knowledge transfer.

### Network Engineering Documentation

**Network Architecture Documentation** (REQUIRED):
- [ ] **Network Topology Diagram**: Visual representation of entire network
  - VNets/VPCs with address spaces
  - Subnets with CIDR blocks and purpose
  - VPN Gateways and connections
  - Edge devices (firewalls, NVAs) with placement
  - Load balancers and application gateways
  - Private endpoints and their connections
  - Peering relationships
  - Cross-region connectivity
  - Tool: draw.io, Lucidchart, or Visio
  - Store: `/docs/network/topology-diagram.png`
- [ ] **Traffic Flow Diagrams**: Visual representation of traffic patterns
  - B2B customer traffic flow (untrusted → DMZ → firewall → internal)
  - Internal application traffic flow
  - Data tier access patterns
  - Cross-region traffic flow
  - Failover traffic patterns
  - Store: `/docs/network/traffic-flows/`
- [ ] **IP Address Management (IPAM) Documentation**:
  - All VNet/VPC CIDR blocks with allocation
  - Subnet allocations and utilization percentage
  - Reserved IP ranges
  - Future growth planning (available address space)
  - IP overlap documentation (if NAT is used for B2B)
  - Store: `/docs/network/ipam.md`
- [ ] **VPN Configuration Documentation** (if applicable):
  - VPN Gateway specifications (SKU, throughput, capacity)
  - Site-to-Site VPN configurations per customer
  - Customer VPN device details (make, model, public IP)
  - IPsec parameters (IKE version, encryption, DH group)
  - Pre-shared keys storage location (Key Vault, Secrets Manager)
  - BGP configuration (if applicable)
  - Point-to-Site VPN configuration (if applicable)
  - Store: `/docs/network/vpn-configuration.md`
- [ ] **Edge Device Configuration Documentation**:
  - Firewall/NVA make, model, version
  - Interface configuration (external, internal, DMZ)
  - Routing configuration
  - High availability setup (active-passive, active-active)
  - Firewall rule documentation
  - NAT configuration (if applicable)
  - IDS/IPS configuration
  - Store: `/docs/network/edge-devices.md`

**Network Security Documentation** (REQUIRED):
- [ ] **NSG/Security Group Documentation**:
  - All NSGs/Security Groups with their purpose
  - Complete rule listing with justification for each rule
  - Source and destination specifications
  - Port and protocol mappings
  - Rule review date and owner
  - Store: `/docs/network/security-groups.md`
- [ ] **Firewall Rules Documentation**:
  - Layer 3/4 rules (IP/port-based)
  - Layer 7 rules (application-based)
  - Threat intelligence configuration
  - WAF rules and policies
  - Custom rules with business justification
  - Rule change history
  - Store: `/docs/network/firewall-rules.md`
- [ ] **Private Endpoint Documentation**:
  - All private endpoints and their target PaaS services
  - Private IP addresses assigned
  - DNS configuration for private endpoints
  - Access restrictions
  - Store: `/docs/network/private-endpoints.md`
- [ ] **DMZ Configuration Documentation** (if B2B):
  - DMZ subnet design and purpose
  - Services exposed in DMZ
  - Security controls protecting DMZ
  - Traffic flow from DMZ to internal network
  - Customer access patterns
  - Store: `/docs/network/dmz-configuration.md`

**DNS Documentation** (REQUIRED):
- [ ] **DNS Configuration**:
  - Public DNS zones and records
  - Private DNS zones and records
  - DNS forwarding configuration (if hybrid)
  - TTL values for all records
  - DNS failover configuration
  - Store: `/docs/network/dns-configuration.md`

**Routing Documentation**:
- [ ] **Route Tables Documentation**:
  - All route tables and associated subnets
  - Routes with next hop information
  - User-Defined Routes (UDRs) forcing traffic through NVA
  - BGP routing (if applicable)
  - Route propagation settings
  - Store: `/docs/network/routing.md`

### Operational Network Documentation

**Network Operations Runbooks** (REQUIRED):
- [ ] **VPN Tunnel Troubleshooting Runbook**:
  - Symptoms: VPN tunnel down, no traffic flowing
  - Diagnostic commands (Azure, AWS, GCP)
  - Common causes and fixes
  - Escalation procedures
  - Customer communication template
  - Store: `/docs/runbooks/vpn-troubleshooting.md`
- [ ] **Edge Device Incident Response Runbook**:
  - Firewall/NVA unreachable
  - High CPU/memory on edge device
  - Traffic not passing through NVA
  - Failover procedures (if HA)
  - Store: `/docs/runbooks/edge-device-incidents.md`
- [ ] **Network Connectivity Troubleshooting Runbook**:
  - Connectivity issues between subnets
  - Connectivity issues to private endpoints
  - Peering connection failures
  - NAT Gateway issues
  - Load balancer backend failures
  - Store: `/docs/runbooks/connectivity-troubleshooting.md`
- [ ] **Network Security Incident Response Runbook**:
  - DDoS attack response
  - Port scan detection response
  - Unusual traffic pattern investigation
  - NSG rule breach investigation
  - WAF threat detection response
  - Store: `/docs/runbooks/security-incidents.md`

**Network Maintenance Procedures**:
- [ ] **VPN Maintenance Procedures**:
  - Pre-shared key rotation schedule and procedure
  - VPN Gateway patching schedule
  - Customer VPN device coordination
  - Maintenance window communication template
  - Store: `/docs/operations/vpn-maintenance.md`
- [ ] **Edge Device Maintenance Procedures**:
  - Firmware update procedures
  - Configuration backup schedule
  - High availability failover testing
  - Performance baseline reviews
  - Store: `/docs/operations/edge-device-maintenance.md`
- [ ] **Certificate Management Procedures**:
  - SSL/TLS certificate inventory
  - Certificate expiration monitoring
  - Certificate renewal procedures
  - Certificate deployment procedures
  - Store: `/docs/operations/certificate-management.md`
- [ ] **Network Capacity Planning**:
  - Subnet IP utilization review schedule
  - VPN Gateway capacity review
  - Firewall throughput review
  - Bandwidth utilization trending
  - Growth projection and scaling plan
  - Store: `/docs/operations/capacity-planning.md`

**Network Monitoring Documentation**:
- [ ] **Network Dashboard Guide**:
  - VPN connectivity dashboard explanation
  - Firewall/security dashboard explanation
  - Network performance dashboard explanation
  - Customer traffic dashboard explanation (B2B)
  - Capacity dashboard explanation
  - Links to all dashboards
  - Store: `/docs/monitoring/network-dashboards.md`
- [ ] **Network Alert Reference**:
  - All network-related alerts documented
  - VPN tunnel down alert → runbook link
  - Firewall unreachable alert → runbook link
  - High bandwidth utilization alert → runbook link
  - NSG rule misconfiguration alert → runbook link
  - Store: `/docs/monitoring/network-alerts.md`
- [ ] **Network Log Query Library**:
  - Top blocked connections query
  - VPN tunnel down events query
  - Customer traffic volume query
  - Firewall threat intelligence hits query
  - NSG flow log analysis queries
  - Store: `/docs/monitoring/network-log-queries.md`

### B2B Customer Documentation

**Customer-Facing Network Documentation** (for B2B):
- [ ] **VPN Setup Guide for Customers**:
  - VPN device configuration templates (Cisco, Fortinet, Palo Alto, pfSense)
  - Your VPN Gateway public IP and configuration
  - IPsec parameters (IKE, encryption, DH group)
  - Pre-shared key delivery method (secure)
  - Troubleshooting common VPN issues
  - Support contact information
  - Store: `/docs/customer/vpn-setup-guide.md`
- [ ] **Network Connectivity SLA Documentation**:
  - VPN tunnel uptime commitment
  - Network latency targets
  - Bandwidth guarantees
  - Maintenance window schedule
  - Incident response times
  - Escalation procedures
  - Store: `/docs/customer/network-sla.md`
- [ ] **Customer IP Requirements Documentation**:
  - Your IP ranges (VNet/VPC CIDRs)
  - Customer IP ranges required
  - IP overlap handling (NAT if needed)
  - Firewall rule request process
  - Port and protocol requirements
  - Store: `/docs/customer/ip-requirements.md`

### Network Change Management Documentation

**Network Change Log** (REQUIRED):
- [ ] **Network Change History**:
  - All network changes with date, change type, and justification
  - Who requested the change
  - Who implemented the change
  - Impact assessment
  - Rollback procedures used (if applicable)
  - Format: Keep a Changelog standard
  - Store: `/docs/network/CHANGELOG.md`

**Network Configuration Backup**:
- [ ] **Configuration Backup Inventory**:
  - VPN Gateway configuration backups
  - Edge device configuration backups
  - NSG/Security Group configuration exports
  - Route table configuration exports
  - Backup schedule and retention policy
  - Backup restore procedures
  - Store: `/docs/network/configuration-backups.md`

### Network Documentation Standards

**Network Documentation Quality Requirements**:
- [ ] All network documentation in Markdown (`.md`) or diagrams (`.png`, `.svg`)
- [ ] Network diagrams updated with every change
- [ ] IP address allocations kept current
- [ ] NSG/Firewall rules reviewed quarterly
- [ ] VPN configurations reviewed annually
- [ ] All diagrams version-controlled in git
- [ ] No hardcoded secrets (use placeholders)
- [ ] Technical accuracy verified by network team
- [ ] Links to monitoring dashboards included

**Network Documentation Review**:
- [ ] Peer review for all network changes
- [ ] Security team review for security-related changes
- [ ] Customer-facing documentation reviewed by support team
- [ ] Quarterly documentation audit
- [ ] Annual comprehensive network documentation review

### Network Documentation Checklist

**Before Production Network Deployment** (MANDATORY):
- [ ] Network topology diagram complete and reviewed
- [ ] Traffic flow diagrams for all major patterns created
- [ ] IP address management documentation updated
- [ ] VPN configuration documented (if applicable)
- [ ] Edge device configuration documented (if applicable)
- [ ] NSG/Security Group rules documented with justification
- [ ] Firewall rules documented with justification
- [ ] Private endpoint configuration documented
- [ ] DNS configuration documented
- [ ] Routing configuration documented
- [ ] VPN troubleshooting runbook created and tested
- [ ] Network incident response runbooks created
- [ ] Network monitoring dashboards documented
- [ ] Network alert documentation complete with runbook links
- [ ] Customer VPN setup guide published (if B2B)
- [ ] Network SLA documentation published (if B2B)
- [ ] Network change log initialized
- [ ] Configuration backups taken and verified
- [ ] All documentation reviewed and approved
- [ ] Knowledge transfer session conducted with operations team

**Network Documentation Storage**:
- **Network Diagrams**: `/docs/network/diagrams/`
- **Network Configs**: `/docs/network/`
- **Network Runbooks**: `/docs/runbooks/network/`
- **Customer Docs**: `/docs/customer/`
- **Monitoring Docs**: `/docs/monitoring/network/`

---

## NOTES

**This workflow supports**:
- Discovery and documentation of existing networks
- Planning new network infrastructure
- All major cloud providers (Azure, AWS, GCP)
- Kubernetes networking
- Hybrid scenarios (cloud + on-premises)

**This workflow ensures**:
- Network flows are clearly documented
- New networks are properly designed
- Security is prioritized
- Existing patterns are leveraged
- Complete documentation for operations
- Comprehensive logging and observability from day one
- Thorough network documentation for maintainability and knowledge transfer

---

Generated using network-flow-builder.md
Last updated: <date>
