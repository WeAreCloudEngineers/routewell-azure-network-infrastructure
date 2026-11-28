# Phase 0 Design: Public Access Strategy & Public IP Research
**Author:** Member 2 (Subgroup 3)
**Project:** RouteWell Logistics Multi-Tier VNet Architecture
**Objective:** Define the public access mechanism for the Web Tier, justify the Azure Standard Public IP selection, and analyze alternatives and costs.

## 1. Executive Summary
To fulfill RouteWell's requirement of providing customers access to the public dispatch website while maintaining strict isolation for backend tiers, Subgroup 3 proposes utilizing an **Azure Standard SKU Static Public IP** directly associated with the Web Tier Virtual Machine's Network Interface Card (NIC). 

This approach balances cost-efficiency with enterprise-grade security, leveraging Azure's default-deny posture for Standard IPs and enforcing strict traffic filtering via Network Security Groups (NSGs). The Application and Database tiers will remain entirely private.

---

## 2. Technical Analysis: Azure Standard Public IP
For this architecture, we are explicitly selecting **Standard SKU** over the legacy Basic SKU. 

**Why Standard SKU is Mandatory:**
*   **Security Posture (Default-Deny):** Standard Public IPs are inherently secure. By default, they are "closed" and drop all inbound traffic unless explicitly allowed by an attached NSG. Basic IPs were open by default, which violates RouteWell’s core security requirement.
*   **Static Allocation:** Standard IPs are always static. This guarantees that the dispatch website's IP address will never change, preventing DNS record failures and ensuring reliable customer access.
*   **Availability Zones:** Standard IPs support Zone-Redundancy. While our Phase 0 design uses a single VM, a Standard IP can be made zone-redundant, ensuring the public endpoint survives a datacenter failure in the Azure region.
*   *(Note: Microsoft officially retired Basic SKU Public IPs for new deployments in 2025. Using Standard SKU ensures our design is modern and supported).*

---

## 3. NIC Association & Traffic Flow
A Public IP is **not** assigned to the OS inside the VM. It is a logical Azure resource associated with the VM's **Network Interface Card (NIC)**.

**How the connection works:**
1.  **Association:** The Standard Public IP is bound to the `ipConfig` of the Web Tier VM's NIC.
2.  **NAT Translation:** When an internet user hits the Public IP on port 80/443, Azure's underlying SDN (Software Defined Network) translates the destination to the VM's **Private IP** (e.g., `10.10.0.4`).
3.  **Filtering:** *Before* the translation even happens, the NSG attached to the Web Subnet or NIC inspects the packet. If it's not on port 80/443, it is dropped at the Azure edge.
4.  **Backend Isolation:** The App VM and DB VM will **not** have Public IPs assigned to their NICs. They are only reachable via their Private IPs from within the VNet.

---

## 4. Alternative Public Access Mechanisms (Analysis & Justification)
As Cloud Engineers, we must evaluate if there are better ways to expose the web tier. Here is why we rejected enterprise alternatives for this specific Phase 0 design:

| Alternative | How it Works | Why it was Rejected for RouteWell (Phase 0) |
| :--- | :--- | :--- |
| **Azure Load Balancer (Standard)** | A Layer 4 load balancer with a Public IP frontend that distributes traffic to backend VMs. | **Cost & Necessity:** We are deploying a single Web VM. A Load Balancer adds ~$18–$25/month in base costs and adds unnecessary complexity for Phase 0. (Though it would be the correct choice if we scaled to 3+ Web VMs). |
| **Azure Application Gateway** | A Layer 7 (HTTP/HTTPS) load balancer with Web Application Firewall (WAF). | **Overkill:** Excellent for enterprise production to block SQL injection and cross-site scripting. However, it is too expensive (~$250+/month) for a 3-week design capstone. |
| **Azure Bastion** | A managed jump-box service that allows secure RDP/SSH via the Azure Portal without Public IPs on the VMs. | **Purpose Mismatch:** Bastion is for **management access** (admin logging in), not for hosting a public website for RouteWell's customers. |
| **Azure Front Door / CDN** | Global HTTP load balancer and CDN. | **Complexity:** Unnecessary for a single-region VNet deployment. |

**Decision:** A direct Standard Public IP on the Web VM NIC is the most pragmatic, cost-effective, and secure choice for a single-instance Phase 0 architecture.

---

## 5. Cost Justification & Financial Impact

| Resource | SKU | Estimated Cost (Monthly) | Justification |
| :--- | :--- | :--- | :--- |
| **Static Public IP** | Standard, IPv4 | **~$3.65 / month** | A flat hourly rate ($0.005/hr) for reserving the IP. This guarantees a permanent, reliable endpoint for the dispatch site. |
| **Load Balancer** | *Not Used* | **$0.00** | We saved ~$18–$25/month by associating the IP directly to the VM. |

**Financial Rationale:** By utilizing a direct Public IP instead of a Load Balancer, we save roughly **85% of the networking cost** for the public layer while still meeting the security requirements (via NSGs). The ~$3.65 monthly cost is highly justifiable for maintaining a reliable public storefront.

---

## 6. Official Microsoft Documentation References
1.  **Public IP Address Types & SKUs:** [https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/public-ip-addresses](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/public-ip-addresses)
2.  **Standard SKU Security & Default Deny:** [https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/public-ip-basic-upgrade-guidance](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/public-ip-basic-upgrade-guidance)
3.  **Associating a Public IP to a VM:** [https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/associate-public-ip-address-vm](https://learn.microsoft.com/en-us/azure/virtual-network/ip-services/associate-public-ip-address-vm)
4.  **Azure IP Address Pricing:** [https://azure.microsoft.com/en-us/pricing/details/ip-addresses/](https://azure.microsoft.com/en-us/pricing/details/ip-addresses/)
