# Canonical Design Baseline

**Status:** Working design baseline for the planned Packet Tracer build. The simulation has not yet been built or tested.

This is the single source of truth for design decisions. If another file disagrees with this baseline, correct that file; do not maintain a competing interpretation. Future changes must be recorded here first and propagated to the IP plan, network design, diagram, configurations, and tests.

## Architecture decisions

1. HQ uses two Layer-3 core switches with HSRP for VLANs 10, 20, 30, 40, and 901. CORE-SW1 is preferred active; CORE-SW2 is standby.
2. HQ access switches are dual-homed to both cores. Two core-to-core Ethernet links form one LACP EtherChannel. STP remains enabled.
3. HQ-FW1 has outside, inside/transit, and DMZ interfaces. Inside transit uses VLAN 901, subnet 10.10.1.0/24. The firewall physically connects to CORE-SW1; the core EtherChannel trunk carries VLANs 10, 20, 30, 40, and 901 (native VLAN 999) so HSRP and transit connectivity span both cores. VLAN 50 is excluded from core trunks. The single physical firewall-to-CORE-SW1 link is a single point of failure; do not claim full end-to-end redundancy.
4. The DMZ is behind the firewall, not routed by a core SVI. VLAN 50 exists on the DMZ switch segment only. HQ-FW1 is the DMZ gateway.
5. The branch uses router-on-a-stick. BR-RTR1 subinterfaces are the gateways for VLANs 110, 120, 130, and 140. BR-SW1 has only a management SVI in VLAN 110.
6. ISP-RTR connects separately to HQ-FW1 and BR-RTR1 using distinct documentation-only public /30 links. Planned IPsec peers are HQ-FW1 outside and BR-RTR1 WAN; VPN operation is unverified until tested.
7. VLAN 10=HQ management, 20=HQ users (staff and finance endpoints), 30=HQ servers, 40=HQ guest, 50=HQ DMZ, 901=HQ firewall transit, 999=native/unused, 998=unused-port parking. VLANs 999 and 998 have no SVI or IP.
8. There is no separate Finance VLAN in this baseline. PC-FIN1 belongs to VLAN 20. Do not refer to a Finance VLAN unless a new VLAN is deliberately designed and allocated everywhere.
9. Trunk policy: core-to-core Port-channel allows VLANs 10, 20, 30, 40, and 901 with native VLAN 999; HQ core-to-access trunks allow VLANs 10, 20, 30, and 40 with native VLAN 999; branch router-to-switch trunk allows VLANs 110, 120, 130, and 140 with native VLAN 999. VLAN 50 is never carried on HQ core trunks. Use the port map in docs/network-design.md. Port labels must be checked against selected Packet Tracer device models before cabling; update the port map everywhere if a model uses different ports.

## Canonical VLAN/subnet map

| Site | VLAN | Name | Subnet | Gateway | Gateway owner |
|---|---:|---|---|---|---|
| HQ | 10 | HQ-MGMT | 10.10.10.0/24 | 10.10.10.1 | Core HSRP |
| HQ | 20 | HQ-USERS | 10.10.20.0/24 | 10.10.20.1 | Core HSRP |
| HQ | 30 | HQ-SERVERS | 10.10.30.0/24 | 10.10.30.1 | Core HSRP |
| HQ | 40 | HQ-GUEST | 10.10.40.0/24 | 10.10.40.1 | Core HSRP |
| HQ | 50 | HQ-DMZ | 10.10.50.0/24 | 10.10.50.1 | HQ-FW1 DMZ |
| HQ | 901 | HQ-FW-TRANSIT | 10.10.1.0/24 | 10.10.1.1 | Core HSRP |
| HQ | 999 | NATIVE-UNUSED | No IP subnet | None | No SVI |
| HQ | 998 | PARKING-UNUSED | No IP subnet | None | No SVI |
| Branch | 110 | BR-MGMT | 10.20.10.0/24 | 10.20.10.1 | BR-RTR1 subinterface |
| Branch | 120 | BR-USERS | 10.20.20.0/24 | 10.20.20.1 | BR-RTR1 subinterface |
| Branch | 130 | BR-SERVICES | 10.20.30.0/24 | 10.20.30.1 | BR-RTR1 subinterface |
| Branch | 140 | BR-GUEST | 10.20.40.0/24 | 10.20.40.1 | BR-RTR1 subinterface |
| Branch | 999 | NATIVE-UNUSED | No IP subnet | None | No SVI; native VLAN on branch trunk |\n| Branch | 998 | PARKING-UNUSED | No IP subnet | None | No SVI; unused branch access ports |

## Addressing rules

- HQ HSRP virtual IPs end in .1; CORE-SW1 SVI addresses end in .2; CORE-SW2 SVI addresses end in .3.
- HQ-FW1 inside: 10.10.1.254/24; CORE-SW1 VLAN 901: 10.10.1.2/24; CORE-SW2 VLAN 901: 10.10.1.3/24; transit HSRP VIP: 10.10.1.1.
- HQ-FW1 outside: 203.0.113.2/30; ISP-RTR Gi0/0: 203.0.113.1/30.
- Branch WAN: BR-RTR1 Gi0/0 203.0.113.6/30; ISP-RTR Gi0/1 203.0.113.5/30.
- DMZ-SW1 management: 10.10.50.2/24; WEB-SRV1: 10.10.50.20/24; gateway 10.10.50.1.
- AAA-SRV1: 10.10.30.10/24; gateway 10.10.30.1.
- BR-SW1 management: 10.20.10.2/24; BR-PC1: 10.20.20.10/24; gateway 10.20.20.1.
- BR-RTR1 subinterface gateways: VLAN 110=10.20.10.1, VLAN 120=10.20.20.1, VLAN 130=10.20.30.1, VLAN 140=10.20.40.1.
- Loopbacks, if supported and needed: HQ-CORE-SW1 10.10.255.1/32; HQ-CORE-SW2 10.10.255.2/32; BR-RTR1 10.20.255.1/32.

203.0.113.0/24 is documentation-only space, not a real public allocation. The two WAN /30s must remain distinct.

## Routing and security intent

- HQ core default route points to HQ-FW1 at 10.10.1.254 via VLAN 901; HQ-FW1 routes internal HQ subnets through transit VIP 10.10.1.1. DMZ is directly connected to HQ-FW1.
- VLAN 50 must not have an HQ core SVI or be carried on HQ user trunks.
- VLAN 10 is the authorized source for network-device administration. HQ users VLAN 20 must not have unrestricted management access.
- Guest VLAN 40 is denied access to internal HQ subnets and allowed only explicitly approved external services.
- DMZ-to-internal access is denied by default. Outside access to WEB-SRV1 is limited to explicitly published web ports.
- Branch VLANs remain separated by router ACLs. Branch-to-HQ access is permitted only for explicitly approved services and should use the IPsec tunnel when implemented.
- PAT/NAT, ACLs, AAA, HSRP, EtherChannel, VPN, and logging are planned controls, not claims of completed configuration.

## Change-control checklist

Any change to a device, VLAN, IP, interface, link, or policy must be reflected in this file, docs/ip-addressing.md, docs/network-design.md, docs/security-controls.md, diagrams, configurations, and relevant tests. Never mark a feature implemented or verified before building and testing it.
