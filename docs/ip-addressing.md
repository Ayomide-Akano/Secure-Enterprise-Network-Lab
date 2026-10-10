# IP Addressing Plan

**Source of truth:** docs/design-baseline.md. This file is the detailed interface/address register and must stay aligned with the baseline.

## HQ VLAN addressing

| VLAN | Name | Subnet | Gateway | CORE-SW1 | CORE-SW2 |
|---:|---|---|---|---|---|
| 10 | HQ-MGMT | 10.10.10.0/24 | 10.10.10.1 HSRP | 10.10.10.2 | 10.10.10.3 |
| 20 | HQ-USERS | 10.10.20.0/24 | 10.10.20.1 HSRP | 10.10.20.2 | 10.10.20.3 |
| 30 | HQ-SERVERS | 10.10.30.0/24 | 10.10.30.1 HSRP | 10.10.30.2 | 10.10.30.3 |
| 40 | HQ-GUEST | 10.10.40.0/24 | 10.10.40.1 HSRP | 10.10.40.2 | 10.10.40.3 |
| 50 | HQ-DMZ | 10.10.50.0/24 | 10.10.50.1 HQ-FW1 | No core SVI | No core SVI |
| 901 | HQ-FW-TRANSIT | 10.10.1.0/24 | 10.10.1.1 HSRP | 10.10.1.2 | 10.10.1.3 |
| 999 | NATIVE-UNUSED | No IP | None | No SVI | No SVI |
| 998 | PARKING-UNUSED | No IP | None | No SVI | No SVI |

## HQ device interfaces

| Device | Interface | VLAN/zone | Address | Host gateway VIP / routing note |
|---|---|---|---|---|
| HQ-CORE-SW1 | SVI VLAN 10 | 10 | 10.10.10.2/24 | Host gateway VIP 10.10.10.1 |
| HQ-CORE-SW1 | SVI VLAN 20 | 20 | 10.10.20.2/24 | Host gateway VIP 10.10.20.1 |
| HQ-CORE-SW1 | SVI VLAN 30 | 30 | 10.10.30.2/24 | Host gateway VIP 10.10.30.1 |
| HQ-CORE-SW1 | SVI VLAN 40 | 40 | 10.10.40.2/24 | Host gateway VIP 10.10.40.1 |
| HQ-CORE-SW1 | SVI VLAN 901 | 901 | 10.10.1.2/24 | Transit HSRP VIP 10.10.1.1; core default route via firewall 10.10.1.254 |
| HQ-CORE-SW2 | SVI VLAN 10 | 10 | 10.10.10.3/24 | HSRP 10.10.10.1 |
| HQ-CORE-SW2 | SVI VLAN 20 | 20 | 10.10.20.3/24 | HSRP 10.10.20.1 |
| HQ-CORE-SW2 | SVI VLAN 30 | 30 | 10.10.30.3/24 | HSRP 10.10.30.1 |
| HQ-CORE-SW2 | SVI VLAN 40 | 40 | 10.10.40.3/24 | HSRP 10.10.40.1 |
| HQ-CORE-SW2 | SVI VLAN 901 | 901 | 10.10.1.3/24 | HSRP 10.10.1.1 |
| HQ-FW1 | Outside | Outside | 203.0.113.2/30 | ISP-RTR 203.0.113.1 |
| HQ-FW1 | Inside | VLAN 901 transit | 10.10.1.254/24 | HQ transit |
| HQ-FW1 | DMZ | VLAN 50 | 10.10.50.1/24 | DMZ gateway |
| DMZ-SW1 | SVI VLAN 50 | 50 | 10.10.50.2/24 | 10.10.50.1 |
| WEB-SRV1 | NIC | 50 | 10.10.50.20/24 | 10.10.50.1 |
| AAA-SRV1 | NIC | 30 | 10.10.30.10/24 | 10.10.30.1 |
| HQ-CORE-SW1 | Loopback0 | Routed identity | 10.10.255.1/32 | N/A |
| HQ-CORE-SW2 | Loopback0 | Routed identity | 10.10.255.2/32 | N/A |

HSRP is planned on VLANs 10, 20, 30, 40, and 901, with CORE-SW1 preferred active. VLAN 50 is only on the firewall/DMZ segment.

## Branch addressing

| VLAN | Name | Subnet | BR-RTR1 gateway | BR-SW1 |
|---:|---|---|---|---|
| 110 | BR-MGMT | 10.20.10.0/24 | 10.20.10.1 | SVI 10.20.10.2/24 |
| 120 | BR-USERS | 10.20.20.0/24 | 10.20.20.1 | No SVI |
| 130 | BR-SERVICES | 10.20.30.0/24 | 10.20.30.1 | No SVI |
| 140 | BR-GUEST | 10.20.40.0/24 | 10.20.40.1 | No SVI |
| 999 | NATIVE-UNUSED | No IP subnet | None | No SVI |
| 998 | PARKING-UNUSED | No IP subnet | None | No SVI |

| Device | Interface | VLAN/zone | Address | Notes |
|---|---|---|---|---|
| BR-RTR1 | Gi0/0 | WAN | 203.0.113.6/30 | ISP-facing; planned VPN peer |
| BR-RTR1 | Gi0/1.110 | 802.1Q VLAN 110 | 10.20.10.1/24 | Management gateway |
| BR-RTR1 | Gi0/1.120 | 802.1Q VLAN 120 | 10.20.20.1/24 | User gateway |
| BR-RTR1 | Gi0/1.130 | 802.1Q VLAN 130 | 10.20.30.1/24 | Services gateway |
| BR-RTR1 | Gi0/1.140 | 802.1Q VLAN 140 | 10.20.40.1/24 | Guest gateway |
| BR-SW1 | SVI VLAN 110 | 110 | 10.20.10.2/24 | Management only |
| BR-PC1 | NIC | 120 | 10.20.20.10/24 | Gateway 10.20.20.1 |
| BR-RTR1 | Loopback0 | Routed identity | 10.20.255.1/32 | Optional if supported |

BR-RTR1 Gi0/1 to BR-SW1 is a trunk allowing VLANs 110, 120, 130, and 140 with native VLAN 999. Create VLANs 999 and 998 on BR-SW1 as native/unused and parking VLANs; neither has an SVI/IP. Do not assign gateway addresses to BR-SW1 in VLANs 120, 130, or 140.

## WAN addressing

| Link | Device/interface | Address | Peer |
|---|---|---|---|
| HQ edge | ISP-RTR Gi0/0 | 203.0.113.1/30 | HQ-FW1 outside 203.0.113.2/30 |
| Branch edge | ISP-RTR Gi0/1 | 203.0.113.5/30 | BR-RTR1 Gi0/0 203.0.113.6/30 |

| Subnet | Network | Usable addresses | Broadcast |
|---|---|---|---|
| HQ WAN | 203.0.113.0/30 | .1–.2 | .3 |
| Branch WAN | 203.0.113.4/30 | .5–.6 | .7 |

203.0.113.0/24 is reserved for documentation and must not be used as a real public allocation. The WAN /30 subnets are distinct.

## Routing intent

- HQ core default route next hop: 10.10.1.254 via transit VLAN 901.
- HQ-FW1 routes internal HQ subnets through HSRP transit VIP 10.10.1.1; the DMZ subnet is directly connected.
- ISP-RTR routes between the two lab WAN links. IPsec is a planned configuration and must be tested before encrypted branch-to-HQ reachability is claimed.
- PAT is planned for outbound Internet traffic. Inbound static NAT to WEB-SRV1, if enabled, is restricted to approved web ports.
- A listed address does not prove the route, NAT rule, ACL, VPN, or HSRP group is configured.
