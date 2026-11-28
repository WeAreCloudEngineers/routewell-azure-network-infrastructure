# App Tier Host Requirements

**Author:** Emenike
**Branch:** task/emenike-app-tier-hosts
**Subgroup:** 2.1 CIDR Planning

## Host Counts

| Timeframe | Hosts Needed | Basis |
|-----------|--------------|-------|
| Today | 20 | RouteWell client brief (Section 2.1) |
| 6 months | 40 | Client expects to double app-tier headcount/traffic within 6 months |

## Growth Calculation

- Current app-tier hosts: 20
- Growth factor: 2x (doubling of dispatch staff and internal app-tier traffic)
- Projected hosts in 6 months: 20 × 2 = **40**

## Notes for Subnet Math (Imobighe)

- Size the App subnet for **40 hosts**, not 20, so the subnet doesn't need re-addressing when growth hits.
- Deduct Azure's 5 reserved IPs per subnet from the usable count.
- The App tier is the only tier expected to grow in 6 months, so it is the tightest sizing constraint.
- Some headroom above 40 is recommended for scaling or replacing VMs.

## Traffic Context

- App tier receives traffic from the Web tier and sends traffic to the DB tier only.
- Port details belong in the NSG worksheet (2.2), not here.

