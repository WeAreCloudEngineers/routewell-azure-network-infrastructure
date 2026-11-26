# Subnet Calculations

## Azure Reserved IP Addresses

Azure reserves 5 IP addresses in every subnet:

- First 4 addresses in the subnet
- Last address in the subnet

Formula:

Usable IPs = Total IPs − 5

---

## Virtual Network Summary

All subnets belong to the 10.10.0.0/16 VNet.

| Component | CIDR | Subnet Mask | Total IPs |
|------------|------------|-------------|-----------:|
| VNet | 10.10.0.0/16 | 255.255.0.0 | 65,536 |

---

## Tier Calculations

### Web Tier

**CIDR:** `10.10.0.0/27`

| Property | Value |
|-----------|--------|
| Prefix Length | /27 |
| Subnet Mask | 255.255.255.224 |
| Total IPs | 32 |
| Azure Reserved | 5 |
| Usable IPs | 27 |
| Address Range | 10.10.0.0 - 10.10.0.31 |

Calculation:

```text
2^(32-27) = 32 total IPs
32 - 5 = 27 usable IPs
```

---

### App Tier

**CIDR:** `10.10.1.0/26`

| Property | Value |
|-----------|--------|
| Prefix Length | /26 |
| Subnet Mask | 255.255.255.192 |
| Total IPs | 64 |
| Azure Reserved | 5 |
| Usable IPs | 59 |
| Address Range | 10.10.1.0 - 10.10.1.63 |

Calculation:

```text
2^(32-26) = 64 total IPs
64 - 5 = 59 usable IPs
```

---

### Database Tier

**CIDR:** `10.10.2.0/28`

| Property | Value |
|-----------|--------|
| Prefix Length | /28 |
| Subnet Mask | 255.255.255.240 |
| Total IPs | 16 |
| Azure Reserved | 5 |
| Usable IPs | 11 |
| Address Range | 10.10.2.0 - 10.10.2.15 |

Calculation:

```text
2^(32-28) = 16 total IPs
16 - 5 = 11 usable IPs
```

---

## Summary

| Tier | CIDR | Subnet Mask | Total IPs | Usable IPs |
|--------|--------|--------|--------:|--------:|
| Web | 10.10.0.0/27 | 255.255.255.224 | 32 | 27 |
| App | 10.10.1.0/26 | 255.255.255.192 | 64 | 59 |
| Database | 10.10.2.0/28 | 255.255.255.240 | 16 | 11 |
| **Total** | - | - | **112** | **97** |

## Validation

- Web tier has 27 usable IPs for public-facing application resources.
- App tier has 59 usable IPs for backend services and future scaling.
- Database tier has 11 usable IPs, sufficient for PostgreSQL and related database resources.
- No subnet ranges overlap.
- All subnets fit within the parent VNet address space.
