# Network Design

**Source of truth:** docs/design-baseline.md and docs/ip-addressing.md. This is the physical/logical architecture plan; the Packet Tracer lab has not yet been built.

## Objective and architecture

Build a segmented HQ and Branch network with Layer-3 HQ core switching, HSRP, dual-homed access switches, a firewall-controlled DMZ, branch router-on-a-stick, and a planned site-to-site IPsec VPN.

## Device inventory and endpoint VLANs

| Device | Role/site | VLAN/zone or address |
|---|---|---|
| ISP-RTR | Provider simulation | Gi0/0 203.0.113.1/30; Gi0/1 203.0.113.5/30 |
| HQ-FW1 | HQ perimeter firewall | Outside 203.0.113.2/30; inside 10.10.1.254/24; DMZ 10.10.50.1/24 |
| HQ-CORE-SW1 | Preferred active Layer-3 core | VLAN 10/20/30/40/901 SVIs ending .2 |
| HQ-CORE-SW2 | Standby Layer-3 core | VLAN 10/20/30/40/901 SVIs ending .3 |
| HQ-ACC-SW1 | HQ access switch | PC-STAFF1 and PC-FIN1, VLAN 20 |
| HQ-ACC-SW2 | HQ access switch | PC-ADMIN1 VLAN 10; PC-GUEST1 VLAN 40 |
| DMZ-SW1 | DMZ access switch | VLAN 50; management 10.10.50.2/24 |
| WEB-SRV1 | DMZ web server | VLAN 50, 10.10.50.20/24 |
| AAA-SRV1 | HQ server | VLAN 30, 10.10.30.10/24 |
| BR-RTR1 | Branch router | WAN 203.0.113.6/30; router-on-a-stick gateways |
| BR-SW1 | Branch access switch | VLAN 110 management SVI 10.20.10.2/24 |
| BR-PC1 | Branch workstation | VLAN 120, 10.20.20.10/24 |

PC-FIN1 is in HQ users VLAN 20; this baseline has no separate Finance VLAN. PC-ADMIN1 is in HQ management VLAN 10, with administrative access restricted by policy.

## VLAN and gateway map

| Site | VLAN | Name | Subnet | Gateway |
|---|---:|---|---|---|
| HQ | 10 | HQ-MGMT | 10.10.10.0/24 | 10.10.10.1 HSRP |
| HQ | 20 | HQ-USERS | 10.10.20.0/24 | 10.10.20.1 HSRP |
| HQ | 30 | HQ-SERVERS | 10.10.30.0/24 | 10.10.30.1 HSRP |
| HQ | 40 | HQ-GUEST | 10.10.40.0/24 | 10.10.40.1 HSRP |
| HQ | 50 | HQ-DMZ | 10.10.50.0/24 | 10.10.50.1 HQ-FW1 |
| HQ | 901 | HQ-FW-TRANSIT | 10.10.1.0/24 | 10.10.1.1 HSRP |
| HQ | 999 | NATIVE-UNUSED | No IP | None |
| HQ | 998 | PARKING-UNUSED | No IP | None |
| Branch | 110 | BR-MGMT | 10.20.10.0/24 | 10.20.10.1 BR-RTR1 |
| Branch | 120 | BR-USERS | 10.20.20.0/24 | 10.20.20.1 BR-RTR1 |
| Branch | 130 | BR-SERVICES | 10.20.30.0/24 | 10.20.30.1 BR-RTR1 |
| Branch | 140 | BR-GUEST | 10.20.40.0/24 | 10.20.40.1 BR-RTR1 |
| Branch | 999 | NATIVE-UNUSED | No IP | None |
| Branch | 998 | PARKING-UNUSED | No IP | None |

HQ HSRP runs on VLANs 10, 20, 30, 40, and 901. VLAN 50 is behind HQ-FW1 and has no HQ core SVI. BR-RTR1 subinterfaces provide the branch VLAN gateways; BR-SW1 routes none of the user VLANs.

## Physical cable and port map

These are planned interface labels; confirm the selected Packet Tracer device models before building. If the actual ports differ, revise the table, diagram, and configuration notes together.

| # | From | To | Cable/link purpose |
|---:|---|---|---|
| 1 | HQ-CORE-SW1 Gi1/0/23 | HQ-CORE-SW2 Gi1/0/23 | Copper straight-through; LACP member 1; trunk VLANs 10, 20, 30, 40, 901; native 999 |
| 2 | HQ-CORE-SW1 Gi1/0/24 | HQ-CORE-SW2 Gi1/0/24 | Copper straight-through; LACP member 2; trunk VLANs 10, 20, 30, 40, 901; native 999 |
| 3 | HQ-CORE-SW1 Gi1/0/1 | HQ-ACC-SW1 Gi1/0/1 | Copper straight-through; trunk VLANs 10, 20, 30, 40; native 999 |
| 4 | HQ-CORE-SW1 Gi1/0/2 | HQ-ACC-SW2 Gi1/0/1 | Copper straight-through; trunk VLANs 10, 20, 30, 40; native 999 |
| 5 | HQ-CORE-SW2 Gi1/0/1 | HQ-ACC-SW1 Gi1/0/2 | Copper straight-through; trunk VLANs 10, 20, 30, 40; native 999 |
| 6 | HQ-CORE-SW2 Gi1/0/2 | HQ-ACC-SW2 Gi1/0/2 | Copper straight-through; trunk VLANs 10, 20, 30, 40; native 999 |
| 7 | HQ-CORE-SW1 Gi1/0/10 | HQ-FW1 inside | Copper straight-through; access in transit VLAN 901 |
| 8 | HQ-FW1 outside | ISP-RTR Gi0/0 | Copper straight-through; HQ WAN |
| 9 | HQ-FW1 DMZ | DMZ-SW1 Gi1/0/1 | Copper straight-through; isolated VLAN 50 segment |
| 10 | ISP-RTR Gi0/1 | BR-RTR1 Gi0/0 | Copper straight-through; branch WAN |
| 11 | BR-RTR1 Gi0/1 | BR-SW1 Gi1/0/1 | Copper straight-through; trunk VLANs 110, 120, 130, 140; native 999 |
| 12 | BR-SW1 Gi1/0/2 | BR-PC1 NIC | Copper straight-through; access VLAN 120 |
| 13 | HQ-ACC-SW1 access port | PC-STAFF1 NIC | Copper straight-through; access VLAN 20 |
| 14 | HQ-ACC-SW1 access port | PC-FIN1 NIC | Copper straight-through; access VLAN 20 |
| 15 | HQ-ACC-SW2 access port | PC-ADMIN1 NIC | Copper straight-through; access VLAN 10 |
| 16 | HQ-ACC-SW2 access port | PC-GUEST1 NIC | Copper straight-through; access VLAN 40 |
| 17 | HQ-CORE-SW2 access port | AAA-SRV1 NIC | Copper straight-through; access VLAN 30 |
| 18 | DMZ-SW1 access port | WEB-SRV1 NIC | Copper straight-through; access VLAN 50 |

Use VLAN 999 as the native VLAN on trunks and VLAN 998 for unused access ports. Trunks should carry only required VLANs. The core Port-channel carries VLANs 10, 20, 30, 40, and 901 with native VLAN 999; it excludes VLAN 50. Core-to-access trunks carry only VLANs 10, 20, 30, and 40. The branch trunk carries VLANs 110, 120, 130, and 140. VLAN 999 is native and VLAN 998 is the unused-port parking VLAN at both sites; neither has an SVI/IP. The two core links form one LACP EtherChannel; STP stays enabled. Port numbers above are a planned map, not yet verified against selected models.

## Logical topology

~~~mermaid
flowchart TB
  ISP["ISP-RTR: .1 and .5 WAN addresses"]
  subgraph HQ["Headquarters"]
    FW["HQ-FW1: outside .2; inside 10.10.1.254; DMZ 10.10.50.1"]
    C1["HQ-CORE-SW1: SVI .2; preferred HSRP active"]
    C2["HQ-CORE-SW2: SVI .3; HSRP standby"]
    A1["HQ-ACC-SW1"]
    A2["HQ-ACC-SW2"]
    DSW["DMZ-SW1: 10.10.50.2"]
    WEB["WEB-SRV1: 10.10.50.20"]
    AAA["AAA-SRV1: 10.10.30.10"]
    C1 ===|"2-link LACP EtherChannel; VLANs 10, 20, 30, 40, 901; native 999"| C2
    C1 ---|"Trunk"| A1
    C1 ---|"Trunk"| A2
    C2 ---|"Trunk"| A1
    C2 ---|"Trunk"| A2
    C1 ---|"VLAN 901 transit"| FW
    FW --- DSW --- WEB
    C2 ---|"VLAN 30"| AAA
  end
  subgraph BRANCH["Branch"]
    BRR["BR-RTR1: WAN 203.0.113.6; subinterfaces .1 gateways"]
    BSW["BR-SW1: VLAN 110 management .2"]
    BPC["BR-PC1: VLAN 120, 10.20.20.10"]
    BRR ---|"802.1Q trunk VLANs 110–140"| BSW
    BSW --- BPC
  end
  ISP ---|"203.0.113.0/30"| FW
  ISP ---|"203.0.113.4/30"| BRR
  FW -. "Planned IPsec VPN" .- BRR
~~~

## Routing and security intent

- HQ cores route HQ VLANs and use HQ-FW1 10.10.1.254 as the default-route next hop.
- HQ-FW1 routes internal HQ subnets through transit HSRP VIP 10.10.1.1; DMZ is directly connected.
- ISP-RTR routes between the two separate documentation-only WAN links.
- Guest VLAN 40 is denied access to HQ internal networks; only explicitly approved external services are allowed.
- VLAN 10 is the approved network-device administration source.
- DMZ-to-internal access is denied by default. Outside access to WEB-SRV1 is limited to explicitly published web ports.
- Branch VLANs are separated with router ACLs. Branch-to-HQ application traffic is allowed only by explicit policy and should use IPsec when implemented.

## Availability limitations

Dual HQ cores and dual-homed access switches provide planned switching redundancy. The single HQ-FW1-to-CORE-SW1 physical link and the single firewall remain failure points. Do not describe the topology as fully redundant end-to-end.

## Build order

1. Select device models and verify their interface names/features.
2. Place devices, cable them, and label them.
3. Create VLANs, trunks, native VLAN 999, parking VLAN 998, EtherChannel, and STP.
4. Configure HQ core SVIs/HSRP and VLAN 901 transit.
5. Configure firewall interfaces, routes, NAT, and DMZ policy.
6. Configure branch subinterfaces, branch switch management, and ISP routing.
7. Configure IPsec, ACLs, SSH, and AAA where supported.
8. Execute and document the tests in testing/test-results.md.
