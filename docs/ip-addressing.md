# IP Addressing Plan

**Source of truth:** docs/design-baseline.md. VLAN roles and subnets in this document must match that baseline.

## HQ internal VLANs

| VLAN | Name | Subnet | HSRP gateway | CORE-SW1 SVI | CORE-SW2 SVI |
|---:|---|---|---|---|---|
| 10 | STAFF | 10.10.10.0/24 | 10.10.10.1 | 10.10.10.2/24 | 10.10.10.3/24 |
| 20 | FINANCE | 10.10.20.0/24 | 10.10.20.1 | 10.10.20.2/24 | 10.10.20.3/24 |
| 30 | IT-ADMIN | 10.10.30.0/24 | 10.10.30.1 | 10.10.30.2/24 | 10.10.30.3/24 |
| 40 | GUEST | 10.10.40.0/24 | 10.10.40.1 | 10.10.40.2/24 | 10.10.40.3/24 |
| 50 | SERVERS | 10.10.50.0/24 | 10.10.50.1 | 10.10.50.2/24 | 10.10.50.3/24 |
| 99 | MGMT | 10.10.99.0/24 | 10.10.99.1 | 10.10.99.2/24 | 10.10.99.3/24 |
| 999 | BLACKHOLE/native | No subnet | None | No SVI | No SVI |

## HQ devices and services

| Device | Interface/segment | Address | Gateway/peer |
|---|---|---|---|
| HQ-ACC-SW1 | Management SVI VLAN 99 | 10.10.99.10/24 | 10.10.99.1 |
| HQ-ACC-SW2 | Management SVI VLAN 99 | 10.10.99.11/24 | 10.10.99.1 |
| DMZ-SW1 | Management SVI VLAN 99 | 10.10.99.12/24 | 10.10.99.1 |
| AAA-SRV1 | NIC, VLAN 50 | 10.10.50.10/24 | 10.10.50.1 |
| HQ-FW1 | Outside | 203.0.113.2/30 | ISP-RTR 203.0.113.1 |
| ISP-RTR | HQ-facing interface | 203.0.113.1/30 | HQ-FW1 203.0.113.2 |
| HQ-CORE-SW1 | Routed link to HQ-FW1 | 10.10.254.1/30 | HQ-FW1 10.10.254.2 |
| HQ-FW1 | Inside routed link | 10.10.254.2/30 | HQ-CORE-SW1 10.10.254.1 |
| HQ-FW1 | DMZ interface, VLAN 60 segment | 172.16.60.1/24 | DMZ subnet gateway |
| DMZ-SW1 | Access ports, VLAN 60 | Layer 2 only | HQ-FW1 172.16.60.1 |
| WEB-SRV1 | NIC, VLAN 60 | 172.16.60.10/24 | 172.16.60.1 |

VLAN 60 is the DMZ and uses 172.16.60.0/24. It exists only on DMZ-SW1 and the firewall-connected DMZ segment; do not create a VLAN 60 SVI on either HQ core. DMZ-SW1 management uses VLAN 99 and 10.10.99.12/24 through a separate management-only access connection. That management connection must not carry VLAN 60.

## Branch addressing

| VLAN | Name | Subnet | Gateway | BR-SW1 SVI |
|---:|---|---|---|---|
| 10 | STAFF | 10.20.10.0/24 | 10.20.10.1 BR-RTR1 | No SVI |
| 99 | MGMT | 10.20.99.0/24 | 10.20.99.1 BR-RTR1 | 10.20.99.2/24 |
| 999 | BLACKHOLE/native | No subnet | None | No SVI |

| Device | Interface | Address | Notes |
|---|---|---|---|
| BR-RTR1 | WAN | 198.51.100.2/30 | ISP peer 198.51.100.1 |
| ISP-RTR | Branch-facing interface | 198.51.100.1/30 | BR-RTR1 peer |
| BR-RTR1 | Subinterface VLAN 10 | 10.20.10.1/24 | Staff gateway |
| BR-RTR1 | Subinterface VLAN 99 | 10.20.99.1/24 | Management gateway |
| BR-SW1 | SVI VLAN 99 | 10.20.99.2/24 | Management only |
| BR-PC1 | NIC, VLAN 10 | 10.20.10.10/24 | Gateway 10.20.10.1 |

The branch router-to-switch link is a trunk allowing VLANs 10 and 99 with native VLAN 999. BR-SW1 does not provide user VLAN gateway SVIs.

## Numbering rules

For HQ internal VLANs 10, 20, 30, 40, 50, and 99: .1 is the HSRP virtual gateway, .2 is CORE-SW1, and .3 is CORE-SW2. Use .10–.49 for planned static endpoints where possible and keep DHCP pools outside the static range. DMZ VLAN 60 is separate and uses the firewall as its gateway; it does not use the HQ HSRP numbering pattern.

## WAN subnets

| Link | Subnet | Device addresses |
|---|---|---|
| HQ-FW1 to ISP-RTR | 203.0.113.0/30 | ISP-RTR .1, HQ-FW1 .2 |
| BR-RTR1 to ISP-RTR | 198.51.100.0/30 | ISP-RTR .1, BR-RTR1 .2 |

Both ranges are reserved for documentation/testing and must not be treated as real public allocations.

## Routing intent

- HQ-CORE-SW1 default route points to HQ-FW1 at 10.10.254.2.
- HQ-CORE-SW2 uses HQ-CORE-SW1 as its default next hop. This is a known dependency and means external routing is not fully redundant if CORE-SW1 fails.
- HQ-FW1 has routes for internal HQ subnets through HQ-CORE-SW1 at 10.10.254.1 and a directly connected DMZ at 172.16.60.0/24.
- ISP-RTR routes between the separate HQ and branch WAN links. The IPsec VPN is planned and must be tested before encrypted reachability is claimed.
- Address assignment alone does not prove routing, NAT, ACLs, VPN, or HSRP are configured.
