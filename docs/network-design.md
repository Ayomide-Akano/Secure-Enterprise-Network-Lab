# Network Design

**Source of truth:** docs/design-baseline.md. The Packet Tracer simulation has not yet been built.

## Objective

Build a segmented HQ and branch network with Layer-3 HQ cores, HSRP, dual-homed access switches, restricted finance and guest segments, a management VLAN, an isolated firewall-controlled DMZ, and a branch router-on-a-stick design.

## Device inventory and endpoint assignments

| Device | Role | Address/VLAN |
|---|---|---|
| ISP-RTR | Simulated ISP | HQ link 203.0.113.1/30; branch link 198.51.100.1/30 |
| HQ-FW1 | HQ firewall | Outside 203.0.113.2/30; inside routed link 10.10.254.2/30; DMZ gateway 172.16.60.1/24 |
| HQ-CORE-SW1 | Preferred active Layer-3 core | SVIs VLANs 10,20,30,40,50,99 ending .2; routed firewall link 10.10.254.1/30 |
| HQ-CORE-SW2 | Standby Layer-3 core | SVIs VLANs 10,20,30,40,50,99 ending .3 |
| HQ-ACC-SW1 | HQ access switch | PC-STAFF1 VLAN 10; PC-FIN1 VLAN 20; management 10.10.99.10 |
| HQ-ACC-SW2 | HQ access switch | PC-ADMIN1 VLAN 30; PC-GUEST1 VLAN 40; management 10.10.99.11 |
| AAA-SRV1 | Internal server | VLAN 50, 10.10.50.10/24 |
| DMZ-SW1 | DMZ access switch | VLAN 60 for DMZ endpoints; separate management SVI VLAN 99 at 10.10.99.12 |
| WEB-SRV1 | Public-facing DMZ server | VLAN 60, 172.16.60.10/24; gateway 172.16.60.1 |
| BR-RTR1 | Branch router | WAN 198.51.100.2/30; subinterfaces VLANs 10 and 99 |
| BR-SW1 | Branch access switch | VLAN 99 management SVI 10.20.99.2/24 |
| BR-PC1 | Branch staff workstation | VLAN 10, 10.20.10.10/24 |

## VLAN and gateway map

| Site | VLAN | Name | Subnet | Gateway |
|---|---:|---|---|---|
| HQ | 10 | STAFF | 10.10.10.0/24 | 10.10.10.1 HSRP |
| HQ | 20 | FINANCE | 10.10.20.0/24 | 10.10.20.1 HSRP |
| HQ | 30 | IT-ADMIN | 10.10.30.0/24 | 10.10.30.1 HSRP |
| HQ | 40 | GUEST | 10.10.40.0/24 | 10.10.40.1 HSRP |
| HQ | 50 | SERVERS | 10.10.50.0/24 | 10.10.50.1 HSRP |
| HQ | 99 | MGMT | 10.10.99.0/24 | 10.10.99.1 HSRP |
| HQ | 60 | DMZ | 172.16.60.0/24 | 172.16.60.1 HQ-FW1 |
| HQ and branch | 999 | BLACKHOLE/native | No IP subnet | None |
| Branch | 10 | STAFF | 10.20.10.0/24 | 10.20.10.1 BR-RTR1 |
| Branch | 99 | MGMT | 10.20.99.0/24 | 10.20.99.1 BR-RTR1 |

HQ HSRP uses .1 as the virtual gateway, CORE-SW1 .2, and CORE-SW2 .3 for VLANs 10, 20, 30, 40, 50, and 99. VLAN 60 has no core SVI. VLAN 999 has no IP address or SVI.

## Physical cable and port map

Port names below are proposed labels. Confirm them against the selected Packet Tracer device models before building and update the diagram if necessary.

| # | From | To | Link purpose |
|---:|---|---|---|
| 1 | HQ-CORE-SW1 Gi1/0/23 | HQ-CORE-SW2 Gi1/0/23 | LACP member 1; trunk VLANs 10,20,30,40,50,99; native 999 |
| 2 | HQ-CORE-SW1 Gi1/0/24 | HQ-CORE-SW2 Gi1/0/24 | LACP member 2; same Port-channel trunk policy |
| 3 | HQ-CORE-SW1 Gi1/0/1 | HQ-ACC-SW1 Gi1/0/1 | Trunk VLANs 10,20,30,40,50,99; native 999 |
| 4 | HQ-CORE-SW1 Gi1/0/2 | HQ-ACC-SW2 Gi1/0/1 | Trunk VLANs 10,20,30,40,50,99; native 999 |
| 5 | HQ-CORE-SW2 Gi1/0/1 | HQ-ACC-SW1 Gi1/0/2 | Redundant trunk; same allowed VLANs/native VLAN |
| 6 | HQ-CORE-SW2 Gi1/0/2 | HQ-ACC-SW2 Gi1/0/2 | Redundant trunk; same allowed VLANs/native VLAN |
| 7 | HQ-CORE-SW1 routed port Gi1/0/10 | HQ-FW1 inside | Routed /30 link; 10.10.254.1/30 to 10.10.254.2/30 |
| 8 | HQ-FW1 outside | ISP-RTR HQ-facing port | 203.0.113.2/30 to 203.0.113.1/30 |
| 9 | HQ-FW1 DMZ interface | DMZ-SW1 access port | Access VLAN 60; DMZ subnet 172.16.60.0/24 |
| 10 | DMZ-SW1 management-only access port | HQ-CORE-SW1 access port | Access VLAN 99 only; do not carry VLAN 60 on this link |
| 11 | ISP-RTR branch-facing port | BR-RTR1 WAN | 198.51.100.1/30 to 198.51.100.2/30 |
| 12 | BR-RTR1 Gi0/1 | BR-SW1 Gi1/0/1 | Trunk VLANs 10,99; native 999 |
| 13 | BR-SW1 Gi1/0/2 | BR-PC1 NIC | Access VLAN 10 |
| 14 | HQ-ACC-SW1 access port | PC-STAFF1 NIC | Access VLAN 10 |
| 15 | HQ-ACC-SW1 access port | PC-FIN1 NIC | Access VLAN 20 |
| 16 | HQ-ACC-SW2 access port | PC-ADMIN1 NIC | Access VLAN 30 |
| 17 | HQ-ACC-SW2 access port | PC-GUEST1 NIC | Access VLAN 40 |
| 18 | HQ-CORE-SW2 access port | AAA-SRV1 NIC | Access VLAN 50 |
| 19 | DMZ-SW1 access port | WEB-SRV1 NIC | Access VLAN 60 |

Use copper straight-through Ethernet for unlike Ethernet endpoints unless the chosen Packet Tracer device auto-MDIX/cabling guidance says otherwise. VLAN 999 is the native VLAN on trunks and the parking VLAN for unused access ports; administratively shut down unused ports. No user-facing trunk should carry VLAN 60. The DMZ-SW1 management connection carries only VLAN 99, so the DMZ subnet remains isolated from the HQ core.

## Logical topology

~~~mermaid
flowchart TB
  ISP["ISP-RTR: HQ 203.0.113.1; branch 198.51.100.1"]
  subgraph HQ["Headquarters"]
    FW["HQ-FW1: outside .2; inside 10.10.254.2; DMZ 172.16.60.1"]
    C1["HQ-CORE-SW1: SVI .2; HSRP preferred active; routed firewall link .1"]
    C2["HQ-CORE-SW2: SVI .3; HSRP standby"]
    A1["HQ-ACC-SW1: STAFF VLAN 10; FINANCE VLAN 20"]
    A2["HQ-ACC-SW2: IT-ADMIN VLAN 30; GUEST VLAN 40"]
    AAA["AAA-SRV1: VLAN 50, 10.10.50.10"]
    DSW["DMZ-SW1: VLAN 60 isolated; management VLAN 99"]
    WEB["WEB-SRV1: VLAN 60, 172.16.60.10"]
    C1 ===|"2-link LACP trunk VLANs 10,20,30,40,50,99; native 999"| C2
    C1 ---|"Trunk VLANs 10,20,30,40,50,99"| A1
    C1 ---|"Trunk VLANs 10,20,30,40,50,99"| A2
    C2 ---|"Redundant trunks"| A1
    C2 ---|"Redundant trunks"| A2
    C1 ---|"Routed 10.10.254.0/30"| FW
    C2 ---|"VLAN 50"| AAA
    FW ---|"DMZ access segment VLAN 60"| DSW
    DSW ---|"Access VLAN 60"| WEB
    DSW ---|"Separate management-only VLAN 99 link"| C1
  end
  subgraph BRANCH["Branch"]
    BRR["BR-RTR1: WAN 198.51.100.2; VLAN 10/99 subinterfaces"]
    BSW["BR-SW1: management 10.20.99.2"]
    BPC["BR-PC1: VLAN 10, 10.20.10.10"]
    BRR ---|"802.1Q trunk VLANs 10,99; native 999"| BSW
    BSW --- BPC
  end
  ISP ---|"203.0.113.0/30"| FW
  ISP ---|"198.51.100.0/30"| BRR
  FW -. "Planned IPsec VPN; not yet tested" .- BRR
~~~

## Routing and availability

- CORE-SW1 default route points to HQ-FW1 at 10.10.254.2.
- CORE-SW2 default route points to CORE-SW1. If CORE-SW1 or its routed firewall link fails, external routing from the HQ core pair is impaired; HSRP alone does not remove this dependency.
- HQ-FW1 routes internal HQ subnets via 10.10.254.1; the DMZ subnet is directly connected.
- BR-RTR1 provides gateways for branch VLANs 10 and 99. The branch WAN uses a distinct /30 from HQ.
- The planned IPsec VPN must be configured and tested before branch-to-HQ encrypted reachability is claimed.

## Build order

1. Confirm device models, interface names, and support for HSRP, LACP, routed ports, firewall interfaces, and IPsec.
2. Place devices and cable according to the port map.
3. Create VLANs, access-port assignments, trunks, native VLAN 999, unused-port shutdown, EtherChannel, and STP.
4. Configure HQ SVI addresses and HSRP; configure the routed firewall link.
5. Configure HQ-FW1 outside, inside, and DMZ interfaces, routes, NAT, and policy.
6. Configure branch VLANs, router subinterfaces, management, and ISP routing.
7. Apply ACLs, SSH/AAA, and IPsec where supported.
8. Run the test matrix and record observed evidence.
