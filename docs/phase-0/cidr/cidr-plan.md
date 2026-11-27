# **2.1 — CIDR Planning**

## **Goal and requirements**

Divide RouteWell's `10.10.0.0/16` VNet into three non-overlapping subnets. Each subnet must meet its tier's host needs without allocating much more address space than necessary.

| Tier | Hosts today | Hosts needed in 6 months |
| --- | --- | --- |
| Web | 12 | 12 |
| Application | 20 | 40 |
| Database | 6 | 6 |

The application tier is the only one with specified growth, so size it for 40 hosts.

## **How to calculate a subnet size**

For a CIDR prefix `/p`:

- **Total IPv4 addresses:** `2^(32 − p)`
- **Azure usable addresses:** `2^(32 − p) − 5`

Azure reserves five addresses in each IPv4 subnet. Choose the smallest subnet whose usable-address count meets the host target.

| Tier | Host target | Smaller size tested | Calculation (usable addresses) | Selected subnet |
| --- | --- | --- | --- | --- |
| Web | 12 | `/28` | `/28`: 16 − 5 = 11 (too small); `/27`: 32 − 5 = 27 | `/27` |
| Application | 40 | `/27` | `/27`: 32 − 5 = 27 (too small); `/26`: 64 − 5 = 59 | `/26` |
| Database | 6 | `/29` | `/29`: 8 − 5 = 3 (too small); `/28`: 16 − 5 = 11 | `/28` |

**Example — Web tier:** A `/28` has `2^(32 − 28) = 16` total addresses. After Azure's five reserved addresses, only `16 − 5 = 11` are usable, which is short of the 12-host requirement. A `/27` has `32 − 5 = 27` usable addresses, so `/27` is the smallest suitable choice.

## **Allocating the CIDR ranges**

| **Tier** | **Hosts Needed Today** | **Hosts Needed in 6 Months** | **Subnet Mask Chosen** | **CIDR Range** | **Assignable IP Range** | **Usable IPs** | **Why This Size (Not Smaller/Larger)** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **Web** | 12 | 12 | `255.255.255.224` | `10.10.0.0/27` | 10.10.0.4 - 10.10.0.30 | 27 | A /28 yields 16 - 5 = 11 usable IPs (too small for 12 hosts); /27 is the smallest valid fit (32 - 5 = 27). |
| **Application** | 20 | 40 | `255.255.255.192` | `10.10.0.32/26` | 10.10.0.68 - 10.10.0.126 | 59 | A /27 yields 32 - 5 = 27 usable IPs (too small for the 6-month doubling to 40 hosts); /26 is the smallest valid fit (64 - 5 = 59). |
| **Database** | 6 | 6 | `255.255.255.240` | `10.10.0.64/28` | 10.10.0.36 - 10.10.0.46 | 11 | A /29 yields 8 - 5 = 3 usable IPs (too small for 6 hosts); /28 is the smallest valid fit (16 - 5 = 11). |

These ranges are aligned to valid subnet boundaries and do not overlap. The block `10.10.0.48–10.10.0.63` remains unused and can be reserved for future expansion.

## **Why not use /24 for every tier?**

A `/24` provides 251 usable Azure addresses—far more than the database tier's six-host requirement. The smaller chosen subnets demonstrate deliberate capacity planning. A larger subnet does not by itself cost more; cloud resources such as VMs, storage, public IPs, and managed networking services are the larger cost drivers. Production planning may also require extra capacity for load balancers, new services, or other future changes.