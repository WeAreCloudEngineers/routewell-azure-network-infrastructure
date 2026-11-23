# routewell-azure-network-infrastructure
Multi-Tier Azure VNet architecture with subnet isolation, NSG access control, and automated CLI provisioning — Cloud &amp; DevOps Bootcamp (Group 1 Capstone).

# RouteWell — Multi-Tier Azure VNet Architecture

**Cloud & DevOps Bootcamp — Group 1 Capstone**  
A design-first, multi-tier Microsoft Azure Virtual Network (VNet) architecture implementing strict subnet isolation, Network Security Group (NSG) access control, least-privilege routing, and automated Azure CLI provisioning.

---

## Project Overview

This repository houses the architecture design, security matrices, infrastructure-as-code (Azure CLI/Bash) scripts, and connectivity validation evidence for the RouteWell 3-tier network environment:

* **Web Tier:** Public-facing entry point handling inbound client traffic.
* **Application Tier:** Isolated business logic layer accessible only from the Web Tier.
* **Database Tier:** Strictly isolated data layer accessible only from the Application Tier.

> **Methodology:** This project follows a **Design-First (Phase 0)** approach. All CIDR allocations, NSG rule matrices, public access decisions, and architecture diagrams are reviewed and approved before any Azure resources are provisioned.

---

## Architecture Diagram

*(Updated upon completion of Phase 0 — Section 2.4)*

![RouteWell Azure Network Architecture](docs/architecture.png)

---

## Repository Structure

```text
routewell-azure-network/
├── .github/
│   ├── CODEOWNERS                       # Automated review routing
│   └── pull_request_template.md         # Standard PR checklist
├── .env.example                         # Placeholder environment variables (no secrets)
├── .gitignore                           # Blocks credentials, keys, and state files
├── CONTRIBUTING.md                      # Official Group 1 4-tier Git workflow
├── README.md                            # Project overview
├── docs/
│   ├── architecture.png                 # Final exported architecture diagram
│   ├── validation-report.md             # Connectivity tests & troubleshooting log
│   └── phase-0/
│       ├── cidr/                        # Section 2.1: Subnet sizing & CIDR allocation
│       ├── nsg/                         # Section 2.2: NSG rules & impact analysis
│       ├── public-access/               # Section 2.3: Public access comparison & recommendation
│       └── diagram/                     # Section 2.4: Editable diagram source & layout notes
├── scripts/                             # Azure CLI & Bash automation scripts
├── configs/                             # Linux VM & tier service configurations
└── screenshots/                         # Azure Portal & connectivity test evidence