RouteWell — Final Integrated Architecture Explanation
Project: Multi-Tier VNet Architecture with Access Control (Group 1)
Phase: Phase 0, Section 2.4 — Architecture Diagram
Role: Final Architecture Integration and Documentation
Status: Proposed design for review and deployment validation 

1. Purpose and client requirements
RouteWell is redesigning its dispatch infrastructure after a near-miss involving a contractor's laptop on the same flat network as sensitive database systems. The proposed Azure design makes the dispatch web application accessible to internet users while keeping the database private and limiting communications between the three tiers to explicitly justified ports. The design is deliberately lean and reserves adequate application-subnet IP capacity for growth from 20 to 40 hosts over six months.

2. Architecture and resource allocation
The proposed deployment uses resource group rg-routewell and one virtual network, vnet-routewell, with address space 10.10.0.0/16. Three dedicated, non-overlapping subnets each contain one planned Linux VM and one subnet-associated Network Security Group (NSG):
Tier	Subnet	Azure-usable IPs	Planned VM / private IP	Service	NSG
Web	10.10.0.0/27	27	Web VM — 10.10.0.4	Nginx, TCP 80	nsg-web
Application	10.10.0.64/26	59	App VM — 10.10.0.68	Node.js API, TCP 3000	nsg-app
Database	10.10.0.32/28	11	DB VM — 10.10.0.36	PostgreSQL, TCP 5432	nsg-db


Azure reserves five IP addresses per IPv4 subnet, so the usable capacity is 2^(32 − prefix) − 5. The App tier requires 40 addresses within six months. A /27 would supply only 27 usable addresses, while a /26 supplies 59. The 10.10.0.48–10.10.0.63 block is not assigned to any of these three subnets; it remains unallocated in the proposed plan. The host counts are sizing requirements, not a claim that 12/20/6 VMs are being deployed; this capstone demonstration plans one VM per tier.

3. Public access and private-tier separation
The proposed public entry point is an Azure Standard SKU Public IP associated with the Web VM's network interface. Internet requests to the dispatch UI enter the Web tier over HTTP TCP 80, as required for the bootcamp demonstration. Neither the App VM nor the DB VM receives a public IP. A public IP on Web does not make the other tiers reachable directly; access to the internal services must still follow the defined NSG rules. For production handling real user data, HTTPS/TLS should replace plain HTTP.

4. Communication and inbound/outbound interpretation
Initiating source	Destination	Port	Decision	NSG viewpoint
Internet	Web VM	TCP 80	Allow	Inbound on nsg-web
Web VM	App VM	TCP 3000	Allow	Outbound from Web; inbound to App
App VM	DB VM	TCP 5432	Allow	Outbound from App; inbound to DB
Web VM	DB VM	TCP 5432	Deny	Not permitted by DB inbound / Web outbound policy
Other unnecessary intra-VNet attempts	Other tiers	Any not explicitly allowed	Deny	Explicit VNet-scoped rules, priority 200


An arrow represents the direction a connection is initiated, not a separate pair of inbound and outbound arrows. For example, Web → App is outbound at Web and inbound at App. Azure NSGs are stateful, so response traffic for established permitted connections is generally allowed without separate reverse-direction rules.

5. Security control design
The completed Phase 0 plan assigns nsg-web, nsg-app and nsg-db to the Web, App and DB subnets respectively. Specific inbound and outbound permissions use priority 100. Broader intra-VNet denies use priority 200 so that Azure's default AllowVNetInBound and corresponding VNet outbound behavior do not inadvertently permit unauthorized lateral traffic. Lower numbers have higher precedence. The diagram shows the principal permitted application pathways and a representative prohibited direct Web → DB pathway; the detailed NSG rule inventory remains in Section 2.2 of the worksheet.

These are proposed rules and require validation. DNS, Azure platform traffic, maintenance/management mechanisms, and update paths are not illustrated and must be checked during deployment before applying restrictive outbound rules. Subnet separation alone does not enforce least-privilege access; it is the effective network security rules that provide the restriction.

6. Visual legend and integration choices
Visual element	Meaning
Dashed blue outer boundary	Azure VNet (10.10.0.0/16)
Light blue Web container	Public-facing Web subnet (10.10.0.0/27)
Light green App container	Private Application subnet (10.10.0.64/26)
Light purple DB container	Private Database subnet (10.10.0.32/28)
Azure-style VM monitor icon	Linux VM inside the corresponding subnet
Blue shield icon and nsg-* label	NSG associated with that subnet
Solid green directional arrow	Permitted application traffic, labeled with TCP port
Dashed red arrow	Representative denied Web-to-DB path
Blue arrow from Public IP	Public HTTP entry to Web VM


These colors and styles are diagram conventions, not mandatory Azure iconography or automatic security controls. The final integrated layout adopts the Web/App/DB colors used in the corrected VM resource-placement contribution and incorporates the NSG security contribution's three NSGs and priority-based deny concept. The VNet and tier CIDRs remain those in the completed Phase 0 worksheet. Public access and traffic arrows are shown as integration components, subject to member review where their individual PRs are not yet approved.

7. Client-brief mapping
Client concern	Response in the architecture
Public dispatch service	One Web VM with Standard SKU Public IP; inbound TCP 80
DB must never be directly internet-reachable	DB VM has private IP only and restricted subnet NSG
Least-access between tiers	Web → App TCP 3000 and App → DB TCP 5432; other unnecessary paths denied
Cost constraint	Three Linux VMs, one VNet, three subnets and NSGs; no public load balancer required for this single-Web-VM demonstration
App demand doubles	App /26 accommodates 40 planned host addresses (59 usable)
Prior flat-network risk	Tier separation reinforced by explicit per-subnet NSG permissions


8. Verification and evidence plan
After the group's Azure CLI/Bash deployment, capture evidence of: (1) Web HTTP TCP 80 reachable using the assigned public IP; (2) Web VM reaching App VM on TCP 3000; (3) App VM reaching PostgreSQL on TCP 5432; (4) Web VM being blocked from DB VM on TCP 5432; (5) subnet and NSG associations, VM private/public IP allocation, and effective NSG rules matching the worksheet. Screenshots and command outputs must be taken from actual deployments, not from this proposed diagram.

9. Group collaboration and final delivery
- Abuya Collins: VNet boundary and three CIDR/subnet layouts.
- Akowe Usman: Linux VM placement and planned private IPs.
- Shokunbi Ayodamope: NSG placements, rule intent and security boundaries.
- Adebayo Ayobami: Standard Public IP and Web entry point.
- Cyril Amoke: Initiating traffic paths, allowed ports and denied access.
- Yewande Biobaku: Combine approved components, normalize visuals, validate against Sections 2.1–2.3, document the design, and provide editable .drawio plus exported .png.

